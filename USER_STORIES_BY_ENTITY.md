# Distrac Backend — User Stories Rapport (by Entity)

This document maps **every major backend capability** to **user stories per role**. Stories list explicit actions: **Read (R)**, **Create (C)**, **Update (U)**, **Delete (D)**, **Approve (A)**, **Reject (X)**, **Confirm (CONF)**, **Cancel**, etc.

**Source of truth:** route files under `Distrac_Backend/src/routes/`.

---

## Roles in the system

| Code | Common name |
|------|-------------|
| `GENERAL_MANAGEMENT` | Admin / General Management |
| `SUPERVISOR` | Regional Supervisor |
| `DEPOT_SUPERVISOR` | Depot Manager |
| `SELLER` | Prevendeur |
| `VENDOR` | Vendor |
| `LIVREUR` | Driver / Livreur |

---

## Scope & permission rules

**Territory scope:** Except **General Management**, most list/read/mutate operations are limited to the user's **assigned territory** (sectors, depots, industries). GM sees the full network.

**Permissions:** Some web features also require a **permission** (e.g. `manage_users`, `view_reports_tab`). GM has all permissions by default.

**Action legend:**

| Symbol | Meaning |
|--------|---------|
| R | Read / List |
| C | Create |
| U | Update |
| D | Delete |
| A | Approve |
| X | Reject |
| CONF | Confirm |
| CANCEL | Cancel |

---

## 1. Authentication & Session

### General Management

- As GM, I can **login (R session)** via `POST /auth/login`.
- As GM, I can **refresh my token (R)** via `POST /auth/refresh-token`.
- As GM, I can **read my profile (R)** via `GET /auth/profile`.
- As GM, I can **update my password (U)** via `POST /auth/change-password`.
- As GM, I can **logout (D session)** via `POST /auth/logout`.
- As GM, I can **read my permission list (R)** via `GET /auth/permissions`.
- As GM, I can **create staff accounts (C)** via `POST /auth/create-user`.
- As GM, I can **request password reset email (C)** via `POST /auth/forgot-password` (public).
- As GM, I can **reset password with token (U)** via `POST /auth/reset-password` (public).
- Public self-registration is **disabled** — `POST /auth/register` returns 403.

### Supervisor

- Same auth stories as GM **except** create-user is limited to GM + Supervisor (`POST /auth/create-user`).

### Depot Supervisor, Seller, Vendor, Livreur

- **Login, refresh, profile (R), change password (U), logout, permissions (R), forgot/reset password** — same as any authenticated user.
- **Cannot** create users via `/auth/create-user`.

---

## 2. Users & Profile Management

### General Management (`manage_users`)

- As GM, I can **list all users (R)** — `GET /users`.
- As GM, I can **create a user (C)** — `POST /users`.
- As GM, I can **read one user with role & assignments (R)** — `GET /users/:id`.
- As GM, I can **update vendor load value limits (U)** — `PATCH /users/:id/vendor-limits`.
- As GM, I can **update my own profile (U)** — `PUT /users/profile`.

### Supervisor (`manage_users` if granted)

- Same user-management stories as GM when the role has `manage_users`.

### Any authenticated user

- As any user, I can **update my own profile (U)** — `PUT /users/profile` (no `manage_users` needed).

### Depot Supervisor, Seller, Vendor, Livreur

- **Own profile only (U)** — `PUT /users/profile`.
- **No** list/create/read-other-users unless given `manage_users`.

---

## 3. Roles, Permissions & Territory Assignments

### General Management (`manage_roles`)

- As GM, I can **list roles with permissions (R)** — `GET /roles`.
- As GM, I can **create a role and link permissions (C)** — `POST /roles`.
- As GM, I can **list all available permissions (R)** — `GET /permissions`.
- As GM, I can **assign a user to sectors/depots/industries (C/U)** — `POST /assignments`.
- As GM, I can **read assignments for any user (R)** — `GET /assignments/:userId`.
- As GM, I can **read my own assignments (R)** — `GET /assignments/my-assignments`.

### Supervisor (`manage_roles` if granted)

- Same stories as GM for roles, permissions, and assignments.

### Others

- No access unless `manage_roles` is explicitly granted on the role.

---

## 4. Clients (Shops / Customers)

### General Management

- As GM, I can **list clients in scope (R)** — `GET /clients` (needs `view_clients_tab` or logistics permission).
- As GM, I can **read one client (R)** — `GET /clients/:id`.
- As GM, I can **update any client (U)** — `PUT /clients/:id`.
- As GM, I can **delete a client (D)** — `DELETE /clients/:id`.
- As GM, I can **update client credit settings (U)** — `PUT /clients/:id/credit-settings`.
- As GM, I can **record a visit (C)** — `POST /clients/:id/visits`.
- As GM, I can **read visit history (R)** — `GET /clients/:id/visits`.
- As GM, I can **read client order history (R)** — `GET /clients/:id/orders`.
- As GM, I can **read client statistics (R)** — `GET /clients/:id/statistics`.
- As GM, I can **read client coupons (R)** — `GET /clients/:id/coupons`.
- As GM, I can **lookup client by QR (R)** — `GET /clients/qr`.

### Supervisor

- **R** list, detail, visits, orders, statistics, coupons, QR.
- **C** create client — `POST /clients`.
- **U** update client — `PUT /clients/:id`.
- **U** credit settings — `PUT /clients/:id/credit-settings`.
- **C** record visit — `POST /clients/:id/visits`.
- **D** delete client — `DELETE /clients/:id`.

### Depot Supervisor

- **R** list, detail, visits, orders, stats, coupons, QR (with permission).
- **U** update client — `PUT /clients/:id`.
- **C** record visit — `POST /clients/:id/visits`.
- **No** create, delete, or credit-settings.

### Seller (Prevendeur)

- **R** client data (scoped).
- **U** update client — `PUT /clients/:id`.
- **C** record visit — `POST /clients/:id/visits`.
- **C** arrival scan at shop — `POST /clients/arrival-scan`.

### Vendor

- **R** client data (scoped).
- **C** create client — `POST /clients`.
- **U** update client — `PUT /clients/:id`.
- **C** record visit — `POST /clients/:id/visits`.
- **C** arrival scan — `POST /clients/arrival-scan`.

### Livreur

- **R** client data (scoped).
- **C** create client — `POST /clients`.
- **R** order details for delivery — `GET /clients/:clientId/orders/:orderId/details`.
- **No** update/delete/credit-settings by default route.

---

## 5. Orders

### General Management

- As GM, I can **list orders (R)** — `GET /orders`.
- As GM, I can **read one order (R)** — `GET /orders/:id`, `GET /orders/:id/details`.
- As GM, I can **read order product summary (R)** — `GET /orders/:id/products`.
- As GM, I can **create an order (C)** — `POST /orders`.
- As GM, I can **update an order (U)** — `PUT /orders/:id`.
- As GM, I can **confirm an order (CONF)** — `POST /orders/:id/confirm`.
- As GM, I can **cancel an order (CANCEL)** — `POST /orders/:id/cancel`.
- As GM, I can **route escalated fulfillment (U)** — `POST /orders/:id/route-fulfillment`.
- As GM, I can **re-check escalations (U)** — `POST /orders/recheck-escalations`, `POST /orders/:id/recheck-escalation`.
- As GM, I can **read order statistics (R)** — `GET /orders/statistics`.
- As GM, I can **read orders for a date (R)** — `GET /orders/today`.
- As GM, I can **print receipt (R)** — `GET /orders/:id/receipt`.

### Supervisor

- Same as GM for orders in assigned territory.

### Depot Supervisor

- **R** list, detail, products, today, receipt.
- **C** create — `POST /orders`.
- **U** update — `PUT /orders/:id`.
- **CONF** confirm — `POST /orders/:id/confirm`.
- **CANCEL** cancel — `POST /orders/:id/cancel`.
- **U** route fulfillment, recheck escalations.
- **No** order statistics endpoint (GM/Supervisor only).

### Seller

- **R** list, detail, products, today, receipt (scoped).
- **C** create order — `POST /orders`.
- **U** update order — `PUT /orders/:id`.
- **No** confirm/cancel/route-fulfillment.

### Vendor

- **R** list, detail, products, today, receipt (scoped).
- **C** create — `POST /orders`.
- **U** update — `PUT /orders/:id`.
- **CONF** confirm — `POST /orders/:id/confirm`.

### Livreur

- **R** list, detail, products, receipt (scoped).
- **No** create/update/confirm/cancel.

---

## 6. Missions (Daily Routes)

### General Management

- As GM, I can **read today's mission stops (R)** — `GET /missions/today`.
- As GM, I can **read mission dashboard (R)** — `GET /missions/dashboard`.
- As GM, I can **read mission map overlay (R)** — `GET /missions/map-overlay`.
- As GM, I can **list proposed missions (R)** — `GET /missions/proposed`.
- As GM, I can **list approved missions (R)** — `GET /missions/approved`.
- As GM, I can **read mission builder options (R)** — `GET /missions/builder-options`.
- As GM, I can **create custom mission (C)** — `POST /missions/custom`.
- As GM, I can **read one mission (R)** — `GET /missions/:id`.
- As GM, I can **update proposed mission (U)** — `PATCH /missions/:id`.
- As GM, I can **delete mission (D)** — `DELETE /missions/:id`.
- As GM, I can **reorder/update stops (U)** — `PUT /missions/:id/stops`.
- As GM, I can **approve mission (A)** — `POST /missions/:id/approve`.
- As GM, I can **reassign mission to another livreur (U)** — `POST /missions/:id/reassign`.
- As GM, I can **run mission dispatcher manually (C)** — `POST /missions/dispatcher/run`.
- As GM, I can **add custom stop on depot mission (C)** — `POST /depots/:id/custom-stop`.
- As GM, I can **complete a stop (CONF)** — `PATCH /missions/stop/:id/complete`.
- As GM, I can **arrive at stop (CONF)** — `POST /missions/stop/:id/arrive`.
- As GM, I can **vehicle check-in (C)** — `POST /missions/vehicle-checkin`.

### Supervisor

- Same mission management stories as GM (proposed/approved/builder/custom/approve/reassign/dispatcher/dashboard).

### Depot Supervisor

- Same mission management stories as Supervisor for **their depot scope**.

### Seller

- **R** today's missions — `GET /missions/today`.
- **C** vehicle check-in — `POST /missions/vehicle-checkin`.
- **CONF** arrive/complete stops assigned to them — arrive, complete.

### Vendor

- **R** today's missions.
- **C** vehicle check-in.
- **CONF** arrive/complete stops.
- **CONF** fulfill vendor stop (sale at stop) — `POST /missions/stop/:id/fulfill`.

### Livreur

- **R** today's missions — `GET /missions/today`.
- **R** pickup product list for the day — `GET /missions/today/pickup-products`.
- **C** vehicle check-in.
- **CONF** arrive at stop — `POST /missions/stop/:id/arrive`.
- **CONF** complete stop (with installment evidence if needed) — `PATCH /missions/stop/:id/complete`.

---

## 7. Deliveries

### General Management

- As GM, I can **list deliveries (R)** — `GET /deliveries`.
- As GM, I can **read one delivery (R)** — `GET /deliveries/:id`.
- As GM, I can **create a delivery (C)** — `POST /deliveries`.
- As GM, I can **assign orders + optimize route (C/U)** — `POST /deliveries/assign-optimize`.
- As GM, I can **read optimized route (R)** — `GET /deliveries/optimized-route`.
- As GM, I can **update delivery status (U)** — `PUT /deliveries/:id/status`.

### Supervisor

- Same delivery stories as GM.

### Depot Supervisor

- Same delivery stories as GM in depot scope.

### Livreur

- **R** list deliveries (own/assigned).
- **R** optimized route — `GET /deliveries/optimized-route`.
- **U** update status on own deliveries — `PUT /deliveries/:id/status`.

### Seller, Vendor

- **No** dedicated delivery management routes (missions handle field execution).

---

## 8. Exceptions (Field Problems)

### General Management

- As GM, I can **list all exceptions (R)** — `GET /exceptions`.
- As GM, I can **read one exception (R)** — `GET /exceptions/:id`.
- As GM, I can **resolve an exception (A/CONF)** — `POST /exceptions/:id/resolve`.

### Supervisor

- Same exception inbox stories as GM (scoped).

### Livreur

- As Livreur, I can **submit an exception request (C)** — `POST /exceptions/request` (return, payment change, etc.).
- As Livreur, I can **poll my exception status (R)** — `GET /exceptions/:id`.

### Depot Supervisor, Seller, Vendor

- **R** single exception if they know the ID (for polling).
- **No** list or resolve.

---

## 9. Returns (Product Returns)

### General Management

- As GM, I can **declare a return (C)** — `POST /returns/declare`.
- As GM, I can **list returns (R)** — `GET /returns`.
- As GM, I can **supervisor-approve return (A)** — `POST /returns/:id/approve-supervisor`.
- As GM, I can **depot-approve return (A)** — `POST /returns/:id/approve-depot`.

### Supervisor

- **C** declare, **R** list, **A** supervisor approve, **A** depot approve.

### Depot Supervisor

- **C** declare, **R** list, **A** depot approve.

### Vendor

- **C** declare return — `POST /returns/declare`.
- **R** list returns — `GET /returns`.
- **R** poll updates — `GET /returns/polling`.
- **CONF** vendor exchange/collection — `POST /returns/vendor-exchange`.

### Seller

- Same as Vendor for declare, list, polling, vendor-exchange.

### Livreur

- **C** declare, **R** list, **R** polling.

---

## 10. Products, Brands & Categories

### General Management

- As GM, I can **list products (R)** — `GET /products`.
- As GM, I can **read product (R)** — `GET /products/:id`.
- As GM, I can **create product (C)** — `POST /products`.
- As GM, I can **update product (U)** — `PUT /products/:id`.
- As GM, I can **delete product (D)** — `DELETE /products/:id`.
- As GM, I can **list brands (R)** — `GET /brands`.
- As GM, I can **create brand (C)** — `POST /brands`.
- As GM, I can **list categories (R)** — `GET /categories`.
- As GM, I can **create category (C)** — `POST /categories`.

### Supervisor

- **R** products, brands, categories.
- **C/U/D** products — create, update, delete.
- **No** create brand/category (GM only).

### Depot Supervisor, Seller, Vendor, Livreur

- **R** products, brands, categories (catalog read for orders/missions).
- **No** catalog mutations.

---

## 11. Stock (Depot Inventory)

### General Management

- As GM, I can **read stock levels (R)** — `GET /stock`.
- As GM, I can **read stock by product & location (R)** — `GET /stock/by-product`.
- As GM, I can **adjust stock (U)** — `POST /stock/adjust`.
- As GM, I can **transfer stock between locations (U)** — `POST /stock/transfer`.
- As GM, I can **read movement history (R)** — `GET /stock/movements`.
- As GM, I can **list stock requests (R)** — `GET /stock/requests`.
- As GM, I can **read one stock request (R)** — `GET /stock/requests/:id`.
- As GM, I can **create stock request (C)** — `POST /stock/requests`.
- As GM, I can **approve stock request (A)** — `PATCH /stock/requests/:id/approve` (`approve_stock`).
- As GM, I can **confirm receipt at depot (CONF)** — `PATCH /stock/requests/:id/receive`.

### Supervisor

- Same stock stories as GM (adjust, transfer, requests, approve, receive).

### Depot Supervisor

- **R** levels, by-product, movements, requests.
- **U** adjust, transfer.
- **C** stock request.
- **CONF** receive at depot.
- **No** approve (needs `approve_stock` — typically GM/Supervisor).

### Seller, Vendor, Livreur

- **R** stock levels and movements (scoped) where needed for field work.
- **No** adjust/transfer/approve by default.

### Industry user (permission `fulfill_stock`)

- **R** stock requests in industry scope.
- **CONF** mark as shipped — `PATCH /stock/requests/:id/fulfill`.

---

## 12. Depot Proposals (Replenishment Brain)

### Depot Supervisor

- As Depot Manager, I can **read AI replenishment proposals (R)** — `GET /depot/proposals`.
- As Depot Manager, I can **accept a proposal → creates stock request (C/CONF)** — `POST /depot/proposals/:id/accept`.

### General Management & Supervisor

- Same if they have depot assignments / scope in service layer.

---

## 13. Industry Fulfillment Orders

### User with industry assignment + `fulfill_stock`

- As Industry user, I can **list fulfillment orders (R)** — `GET /industry/orders`.
- As Industry user, I can **mark order ready (CONF)** — `PATCH /industry/orders/:id/ready`.

### General Management

- Full visibility via web; backend routes are scoped to industry assignments.

---

## 14. Vendor Mobile Stock & Load-Out

### Vendor

- As Vendor, I can **read my van stock (R)** — `GET /vendor/stock`.
- As Vendor, I can **read pick list (R)** — `GET /vendor/stock/pick-list`.
- As Vendor, I can **list my load requests (R)** — `GET /vendor/stock/load-requests`.
- As Vendor, I can **submit load request (C)** — `POST /vendor/stock/load-requests`.
- As Vendor, I can **physically confirm load received (CONF)** — `POST /vendor/stock/confirm-load` (moves stock depot → van, status `LOADED`).

### Depot Supervisor

- As Depot Manager, I can **list vendor load requests for my depot (R)** — `GET /depot/vendor-load-requests`.
- As Depot Manager, I can **approve load request (A)** — `PATCH /depot/vendor-load-requests/:id/approve` (approval only, no stock move).
- As Depot Manager, I can **reject load request (X)** — `PATCH /depot/vendor-load-requests/:id/reject`.

### General Management & Supervisor

- Same depot load-request stories in scope.

---

## 15. Depots, Regions, Sectors, Industries & Vehicles (Logistics)

### General Management (`view_logistics_tab` / `manage_logistics`)

- **Depots — R** list/detail `GET /depots`, `GET /depots/:id`; **C** `POST /depots`; **U** `PUT /depots/:id`.
- **Regions — R** list/detail/topology/trace-candidates; **C** `POST /regions`; **U** `PUT /regions/:id`; **U** validate boundary.
- **Sectors — R** list/detail; **C** `POST /sectors`; **U** `PUT /sectors/:id`; **D** `DELETE /sectors/:id`.
- **Industries — R** list/detail; **C** `POST /industries`; **U** `PUT /industries/:id`.
- **Vehicles — R** list, positions, history, trips, map viewport/stream; **C** `POST /logistics/vehicles`; **U** `PATCH /logistics/vehicles/:id`.
- **R** logistics summary — `GET /logistics/summary`.
- **R/U** reverse geocode — `POST /logistics/geocode/reverse`.
- **Wialon GPS — R** units, status, suggestions; **C** link vehicle; **D** unlink; link-all-suggested.

### Supervisor (with logistics permissions)

- **R** all logistics entities in territory.
- **C/U/D** only if `manage_logistics` is granted.

### Depot Supervisor

- Typically **R** depots, vehicles, map in assigned scope.
- **No** create region/sector unless `manage_logistics`.

### Seller, Vendor, Livreur

- **No** logistics admin routes; they use missions/clients/orders.

---

## 16. Reports & Analytics

### General Management (`view_reports_tab`)

- As GM, I can **read debt summary (R)** — `GET /reports/debt/summary`.
- As GM, I can **read livreur carried cash/debt (R)** — `GET /reports/debt/livreurs`.
- As GM, I can **read depot capacity status (R)** — `GET /reports/depot/status`.
- As GM, I can **read fleet check-in & unassigned-order alerts (R)** — `GET /reports/fleet/status`.

### Supervisor (`view_reports_tab`)

- Same report stories as GM (territory-scoped in service where implemented).

### Depot Supervisor, Seller, Vendor, Livreur

- **No** access to `/reports/*` by default.

---

## 17. Operational Statistics

### Any authenticated user (route has no role gate)

- As any user, I can **read daily operational stats (R)** — `GET /statistics/operations`.
- As any user, I can **read driver/vehicle time stats (R)** — `GET /statistics/driver`.

*(Data may still be filtered in the service by user context.)*

---

## 18. System Settings

### General Management (`manage_settings`)

- As GM, I can **read system settings (R)** — `GET /settings` (thresholds, company rules, etc.).
- As GM, I can **update system settings (U)** — `PUT /settings`.

### Others

- **No** settings access unless `manage_settings` is explicitly granted.

---

## 19. Depot Finance (Cash Deposit)

### General Management & Depot Supervisor

- As Depot Manager / GM, I can **deposit end-of-day cash (C)** — `POST /depot/deposit-cash`.

### Supervisor, Seller, Vendor, Livreur

- **No** cash deposit route.

---

## 20. File Upload (Images)

### Any authenticated user

- As any user, I can **upload images (C)** — `POST /upload` (product photos, check images, evidence; max 10MB, images only).

---

## Quick reference — Admin (GM) superpowers

| Entity | R | C | U | D | Other |
|--------|---|---|---|---|-------|
| Users | ✓ | ✓ | ✓ limits | — | own profile |
| Roles/Permissions | ✓ | ✓ roles | ✓ assignments | — | |
| Clients | ✓ | via roles | ✓ | ✓ | credit, visits |
| Orders | ✓ | ✓ | ✓ | — | confirm, cancel, route |
| Missions | ✓ | ✓ | ✓ | ✓ | approve, reassign, dispatcher |
| Deliveries | ✓ | ✓ | ✓ | — | assign-optimize |
| Exceptions | ✓ | — | — | — | resolve |
| Returns | ✓ | ✓ | — | — | approve both steps |
| Products | ✓ | ✓ | ✓ | ✓ | |
| Brands/Categories | ✓ | ✓ | — | — | |
| Stock | ✓ | ✓ requests | ✓ adjust/transfer | — | approve, receive |
| Vendor loads | ✓ | — | — | — | approve/reject |
| Logistics map | ✓ | ✓ | ✓ | ✓ sectors | Wialon link |
| Reports | ✓ | — | — | — | |
| Settings | ✓ | — | ✓ | — | |
| Depot cash | — | ✓ deposit | — | — | |

---

## Notes for product / QA

1. **Duplication is intentional** — the same story (e.g. "create order") appears under Vendor, Seller, and Supervisor because each role uses it in a different context.
2. **Permissions vs roles** — web tabs use permissions; mobile routes often use `restrictTo(role)`. A custom role can mix both.
3. **Territory** — always test GM (all data) vs Supervisor (region) vs Depot Manager (one warehouse).
4. **Two-step vendor load** — Depot **approves** (`APPROVED`); Vendor **confirms physically** (`LOADED` + stock move).

---

## Related docs

- [WEB_ROLES_AND_COMMAND_CENTER.md](./WEB_ROLES_AND_COMMAND_CENTER.md) — web dashboard view by role
- [BACKEND_GAPS_AND_PROBLEMS.md](./BACKEND_GAPS_AND_PROBLEMS.md) — known gaps and production readiness
