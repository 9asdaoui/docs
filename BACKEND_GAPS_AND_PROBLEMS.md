# Distrac Backend — Gaps & Problems Report

**Date:** June 2026  
**Scope:** `Distrac_Backend` API (Express + Supabase)  
**Purpose:** Honest assessment of what still blocks production hardening, security, and scale.

---

## Executive summary

The backend is **functionally rich** and follows a clear pattern (routes → controllers → services → database). Core business flows — orders, missions, stock brain, returns, logistics — are implemented and used by the web and mobile apps.

However, several **structural risks** remain:

1. **Security holes** — public registration can assign admin roles; password reset is fake; many read endpoints lack territory checks (IDOR).
2. **Authorization is inconsistent** — mix of RBAC permissions, hardcoded roles, and “authenticate only” routes; product docs promise territory scoping that code does not always enforce.
3. **Database lifecycle is manual** — no canonical schema file; 32+ SQL migrations applied by hand; easy to drift between environments.
4. **All data access uses the Supabase service role** — Row Level Security is bypassed; every missing check in app code is a full data leak.
5. **Test coverage is thin** — 9 test files vs 24 services; almost no integration or security regression tests.
6. **Performance and scale debt** — N+1 queries on order lists, per-request permission DB lookups, in-process schedulers that duplicate on multiple servers.
7. **Legacy parallel domains** — `deliveries` API vs `missions`/`mission_stops`; old SQL and column fallbacks still in services.

**Bottom line:** The backend works for development and demos, but **must be hardened before treating it as production-grade** for multi-tenant territory isolation and public internet exposure.

---

## 1. Critical problems (fix before production exposure)

### 1.1 Open registration with any role

`POST /auth/register` is **public** (no login required). The validator accepts `GENERAL_MANAGEMENT`, `SUPERVISOR`, and all operational roles.

**Risk:** Anyone who discovers the endpoint can create a company-admin account.

**Expected fix:** Remove public register, or restrict to invite-only / admin-only (`create-user` path already exists for supervisors and GM).

---

### 1.2 Password reset is not implemented

`forgot-password` and `reset-password` return success messages but contain explicit `TODO` comments — no email, no token verification, no password change.

**Risk:** Users believe reset worked; security auditors will flag this immediately.

**Expected fix:** Integrate Supabase Auth reset flow or implement token + email; until then, **disable endpoints** or return `501 Not Implemented`.

---

### 1.3 IDOR — read any resource by UUID

Several endpoints only require **authentication**, not **ownership or territory**:

| Area | Endpoint pattern | Problem |
|------|------------------|---------|
| Orders | `GET /orders/:id` | Fetches order by ID with no scope check |
| Deliveries | `GET /deliveries/:id` | Same |
| Stock | `GET /stock`, `GET /stock/by-product` | Any logged-in user can query stock levels |
| Clients | `GET /clients/:id` | Permission on route, but service does not verify sector scope |
| Exceptions | `GET /exceptions/:id` | Livreur scoped to own; supervisor can read any |

**Risk:** A user in depot A can read orders, stock, or clients from depot B if they guess or obtain UUIDs.

**Expected fix:** Central `assertResourceInScope(user, entity)` used on every single-resource read and update.

---

### 1.4 Delivery status update ignores driver ownership

The delivery controller passes `userId` to `updateDeliveryStatus`, but the service method **does not accept or use it** — any `LIVREUR` can update any delivery.

**Risk:** Status manipulation across the fleet.

**Expected fix:** Verify `delivery.livreur_id === userId` (or mission assignment) before update.

---

### 1.5 Supabase service-role = no database safety net

The app connects with the **service role key**, which bypasses all Supabase RLS policies.

**Implication:** Authorization must be **100% correct in application code**. There is no second line of defense.

**Expected fix (long term):** User-scoped JWT + RLS for reads; or rigorous audit of every query path.

---

### 1.6 No reliable schema bootstrap

`npm run migrate` references `src/database/MASTER_SCHEMA.sql`, which **does not exist**. Real schema changes live in `scripts/migrations/V3` through `V31` (32 files, some duplicate version numbers like two `V31_*` files).

**Risk:** New developer or new environment cannot reproduce the database; code may assume columns that were never migrated.

**Expected fix:** Single ordered migration manifest + `schema_migrations` tracking table; or one generated baseline dump.

---

## 2. High priority gaps

### 2.1 Territory scoping is incomplete

Product documentation (`WEB_ROLES_AND_COMMAND_CENTER.md`) states supervisors and depot managers only see their territory. **Implementation is partial:**

| Domain | List endpoints | Single-resource / reports |
|--------|----------------|---------------------------|
| Orders | Scoped when assignments exist | **Not scoped** on `getOrderById` |
| Clients | List filtered by sector | **getClientById** not filtered |
| Missions | Depot scope on many paths | Generally better than orders |
| Exceptions | **No territory filter on list** | Supervisor sees all pending |
| Reporting (debt, fleet, depot status) | N/A | **No `getDataScope`** — network-wide for any supervisor |
| Stock reads | N/A | **Unscoped** |

**Additional edge case:** If a supervisor has **empty assignments**, order list queries apply **no depot/sector filter** — they may see all orders.

**Expected fix:** Define a single scoping contract per entity type; block or empty-result when scope is undefined for non-GM roles.

---

### 2.2 Dual authorization models (RBAC vs roles)

The codebase mixes:

- `authorize('view_logistics_tab')` — permission from `role_permissions` table  
- `restrictTo('SUPERVISOR', 'GENERAL_MANAGEMENT')` — hardcoded role names  
- `authenticate` only — relies on service-layer checks (often missing)

Migrations (`V27`, `V30`, `V31`) patch permissions ad hoc. **GM full access required a manual V31 script** — shows drift between “intended” and “seeded” permissions.

**Risk:** User has correct role but missing permission → 403; or has permission but wrong role → inconsistent access; docs and reality diverge.

**Expected fix:** One matrix: role → permissions → route policy; deprecate `restrictTo` where permissions exist.

---

### 2.3 Routes with weak gates

Examples of **authenticate-only** or overly open access:

- **Settings `GET /`** — any logged-in user reads global settings (dispatch time, auto-approve flags).
- **Upload `POST /upload`** — any authenticated user, up to 50 × 10MB images.
- **Inventory brain routes** (`/depot/proposals`, industry orders) — authenticate only, no inventory permission.
- **Statistics routes** — authenticate only; role check deferred to service runtime.

---

### 2.4 Refresh token fallback weakens security

If the `refresh_tokens` table is missing, refresh may fall back to JWT-only verification (documented in auth service). This weakens revocation and session control.

---

### 2.5 Incomplete business flows

| Feature | Status |
|---------|--------|
| Password reset | Stub only |
| `TRANSFER_TO_DEPOT` order escalation | Creates stock request stub; fulfillment path may be incomplete |
| `deliveries` module | Parallel to missions; unclear if still used by mobile; weaker auth |
| PostGIS `find_sector_for_coordinates` | Exists in DB layer, **not wired to HTTP** for auto sector assignment |
| Proposal “reject” on web | Frontend dismiss only; no formal backend rejection state |

---

## 3. Medium priority — technical debt

### 3.1 Performance

| Issue | Where | Impact |
|-------|-------|--------|
| N+1 order hydration | `getOrdersForDate` / `mapOrderRow` | Each order triggers multiple DB calls |
| Escalation recheck on every order read | `_getOrderDetail` | Expensive for list views |
| Permission lookup per request | `authorize()` middleware | Extra DB round-trip every API call |
| In-process mission dispatcher | `server.js` startup, ~1 min interval | Duplicate runs if multiple app instances |
| Wialon GPS poller | ~20s interval, in-process | Same duplication risk |
| Map viewport client cap | 500 clients per bbox | Documented scale limit |

---

### 3.2 API design inconsistencies

- **Triple mount** on `/depot` — proposals, finance, and vendor loads share prefix (routing works but confusing).
- **Swagger drift** — e.g. `REGIONAL_MANAGER` in OpenAPI vs `SUPERVISOR` in code; some documented paths do not match actual routes.
- **Mixed response patterns** — mostly `{ status, data }`; some endpoints return different error shapes.
- **Global rate limit** per IP only — no per-user throttling for authenticated abuse.

---

### 3.3 Schema and legacy code

- Fragmented SQL under `src/database/` plus `legacy/` copies (including typo filenames).
- Services contain **column fallbacks** (`owner_id` vs `depot_id`, legacy payment fields) — signs of evolving schema without cleanup.
- Duplicate migration version numbers (`V8` + `V8b`, two `V31_*`).

---

### 3.4 Error handling

- Global `errorHandler` and `catchAsync` are solid.
- **Exceptions:** `getPermissions` uses manual 500 response instead of `next(AppError)`.
- **Development mode** may return full stack traces to clients.

---

### 3.5 Observability and ops

- Logging exists (`logger`) but no structured tracing, metrics, or health beyond basic server start.
- No documented backup/restore or disaster recovery for Supabase.
- Scheduler and poller failures may be silent except in logs.

---

## 4. Low priority — nice to have

- Consolidate OpenAPI / inline Swagger into maintainable modules.
- Add `jest.config.js`, ESLint config, CI gate on tests.
- Remove or merge `deliveries` API with missions if unused.
- Permission caching (Redis or in-memory with TTL).
- Leader election for dispatcher and Wialon poller in multi-instance deploys.
- Standardize French/English error messages for mobile clients.

---

## 5. Test coverage gap

**Current:** 9 test files

| Covered | Not covered (examples) |
|---------|-------------------------|
| Auth middleware basics | Full `authService` (register, refresh, login) |
| Map viewport / clustering | `orderService` (credit, escalation, IDOR) |
| Region topology | `missionDispatcherService` |
| Orders-for-date date parsing | `inventoryBrainService` |
| Depot reporting metrics | `clientService`, `stockService` |
| Vendor stock | Controllers, route integration |
| Vehicle helpers | Exception scoping, delivery ownership |

**Risk:** Regressions in security and scoping will not be caught by CI.

---

## 6. Per-domain scorecard

| Domain | Maturity | Main gap |
|--------|----------|----------|
| **Auth** | Medium | Open register, fake reset, refresh fallback |
| **Users / roles** | Medium | Permission drift; create-user can set any role |
| **Orders** | Medium–High | List scope OK; single-order IDOR; N+1 performance |
| **Missions** | High | Good depot scoping for field ops; exception list unscoped |
| **Exceptions** | Medium | Approve/reject works; list not territory-filtered |
| **Deliveries** | Low | Legacy; weak auth; parallel to missions |
| **Clients** | Medium | List scoped; detail/coupons not always |
| **Stock / inventory brain** | Medium–High | Writes gated; reads unscoped; auto-approve logic solid |
| **Logistics / map** | High | Good permission gates; summary counts global |
| **Reporting** | Medium | Routes gated; **service not territory-scoped** |
| **Returns** | Medium–High | Workflow implemented; needs more tests |
| **Settings** | Medium | Read open to all auth users |
| **Wialon / fleet** | Medium | Works single-process; scale story missing |
| **Uploads** | Low | No role gate |

---

## 7. Recommended remediation order

| Phase | Actions | Effort |
|-------|---------|--------|
| **Phase 1 — Security** | Lock register; fix or remove password reset; add scope checks on order/delivery/client/stock/exception reads; fix delivery ownership | 1–2 weeks |
| **Phase 2 — Scoping** | Apply `getDataScope` to reporting, exception list, empty-assignment behavior; document GM vs scoped roles | 1 week |
| **Phase 3 — Schema** | Migration manifest + tracking; baseline schema export from production/staging | 3–5 days |
| **Phase 4 — Auth unify** | Route audit: every endpoint needs `authorize` or documented service check; align seeds with V31-style full GM | 1 week |
| **Phase 5 — Tests** | IDOR tests, exception scope, auth flows, critical order/mission paths | Ongoing |
| **Phase 6 — Scale** | External cron for dispatcher; single leader for Wialon; order list query optimization | 2+ weeks |

---

## 8. What is working well (context)

To balance the report — these areas are in good shape:

- **Layered architecture** — controllers stay thin; business logic in services.
- **Validation** — Joi schemas on most write endpoints.
- **Mission field workflow** — check-in, stop completion, propose/approve pipeline.
- **Order credit / debt validation** — detailed rules in `orderService`.
- **Inventory brain** — low-stock detection, proposals, auto-approve paths.
- **Logistics map APIs** — consistent `view_logistics_tab` / `manage_logistics` gates.
- **Returns workflow** — multi-step approval implemented.
- **Error primitives** — `AppError`, `catchAsync`, centralized handler.

---

## Related documents

- [WEB_ROLES_AND_COMMAND_CENTER.md](./WEB_ROLES_AND_COMMAND_CENTER.md) — product expectations for scoping and approvals (not fully enforced in backend).
- [ROLES_AND_CAPABILITIES.md](./ROLES_AND_CAPABILITIES.md) — full role overview.
- [GLOBAL_MAP.md](../Distrac_Backend/GLOBAL_MAP.md) — Command Center API composition.

---

*This report reflects codebase analysis as of June 2026. Re-validate after major migrations or auth changes.*
