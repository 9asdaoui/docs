# Distrac Web Dashboard — Roles, Features & Command Center Guide


This document describes **only the office web application** — what exists today, what each role can see, what they must approve or reject, what happens automatically, and what alerts appear. It is written so you can decide **what the CEO should see on the map** without reading technical documentation.

---

## 1. Who actually uses the web?

| Role | Uses the web? | Primary job on the web |
|------|----------------|----------------------|
| **General Management** | Yes — full access | Run the company view: map, settings, users, finance |
| **Supervisor** | Yes — regional access | Approve exceptions, watch debt, support missions in their territory |
| **Depot Manager** (Depot Supervisor) | Yes — depot access | Approve tomorrow’s missions, stock proposals, vendor loads for their warehouse |
| **Prevendeur, Vendor, Livreur** | **No (mobile app)** | They do not use this web dashboard in normal operations |

> **Important:** Even on the web, people usually **only see their territory** (assigned regions, sectors, or depots). General Management sees the whole network.

---

## 2. Full list of web screens (what we built)

These are the main areas in the sidebar today:

### System
| Screen | What it is |
|--------|------------|
| **Command Center** | Live map + operational dashboard (default home page) |
| ↳ Industries | List of suppliers / factories |
| ↳ Depots | List of warehouses |
| ↳ Regions | Geographic regions on the map |
| ↳ Sectors | Sales territories (polygons) |
| ↳ Clients | Shop / customer list |
| ↳ Vehicles | Fleet list and GPS linking |
| **Users** | Create and manage staff accounts |
| **Roles** | Define roles and which menus each role gets |

### Catalog
| Screen | What it is |
|--------|------------|
| **Brands** | Product brands |
| **Categories** | Product categories |
| **Products** | Full product catalog (under Inventory group) |

### Operations
| Screen | What it is |
|--------|------------|
| **Orders** | Customer orders — list and detail |
| **Missions** | Tomorrow’s routes — propose, edit, approve |
| **Exceptions** | Supervisor inbox for field problems |

### Finance
| Screen | What it is |
|--------|------------|
| **Debt Summary** | Who holds financial responsibility (supervisors + drivers with cash) |

### Inventory
| Screen | What it is |
|--------|------------|
| **Stock** | Depot stock levels + manual stock requests |
| **Proposal Inbox** | System-generated replenishment suggestions |
| **Fulfillment Tracking** | Industry orders being prepared and shipped |

### Personal (every logged-in user)
| Screen | What it is |
|--------|------------|
| **Profile** | Own account |
| **Settings** | Company-wide rules (management only can edit) |

---

## 3. Command Center — what it is and what’s on it

The **Command Center** opens when users log in. It is split into **two thirds map / one third panel**.

### 3.1 The map (left side)

**What appears on the map**

| Layer | What you see |
|-------|----------------|
| **Regions** | Large orange zone outlines |
| **Sectors** | Coloured territory polygons (colour = linked depot) |
| **Depots** | Warehouse pins (gold = central depot) |
| **Industries** | Factory / supplier pins |
| **Clients** | Shop locations (dots) |
| **Vehicles** | Trucks (at depot or live GPS if linked) |
| **Mission routes** | Dashed lines for today’s approved / in-progress routes |

**What you can do on the map** (management / logistics permission only)

- Click any pin or zone → opens **detail panel** on the right  
- **Create** new industry, depot, sector, client, or vehicle from the panel  
- **Edit** location (drag marker) or draw/edit region and sector boundaries  
- **Search** within the current layer  
- **Filter** one layer at a time (All, Regions, Sectors, Depots, etc.)  
- **Follow a vehicle** when GPS tracking is connected  
- **Refresh** data manually  

**What read-only users can do**

- View the map and open details  
- They **cannot** create, move, or draw on the map  

### 3.2 The right panel — two modes

**Mode A — “All” filter (no entity selected)**  
Shows the **Operational HUD** (executive pulse):

| HUD block | What it tells the manager |
|-----------|---------------------------|
| **Today’s Pulse** | % of today’s delivery stops completed + today’s sales (vs yesterday trend) |
| **Action Inbox** | Clickable counts → Exceptions page, Proposal Inbox |
| **Critical Inventory** | Up to 3 depots at ≥80% full or over capacity (red bar) |
| **Fleet Status** | Drivers checked in today vs total + warning if tomorrow’s orders are not on a mission yet |
| **System Healthy** | Green check when nothing needs attention |

**Mode B — Entity selected**  
Shows a **profile + tabs** for that entity, for example:

- **Depot profile:** photo-style header, name, address, then tabs: Stock | Livreurs | Missions | Vendor Loads (each with its own KPIs + table)  
- **Client profile:** contact, credit settings, order history  
- **Industry, sector, region, vehicle:** respective operational details  

### 3.3 Why this matters for UI decisions

When the CEO says *“I want to see X on the Command Center”*, X must be one of:

1. Something already in the **HUD** (sales, missions %, alerts, depots, fleet)  
2. Something on the **map** (new layer, colour rule, badge on pin)  
3. Something in an **entity detail panel** (depot tabs, client credit, etc.)  
4. A **new screen** linked from the Action Inbox (Exceptions, Proposals, Missions, Orders)  

---

## 4. Decisions: what must be approved or rejected on the web

These are the **human decision points** — everything else is either automatic or happens on mobile.

### 4.1 Exceptions (Supervisor + General Management)

**Where:** Operations → **Exceptions** (also linked from Command Center HUD)

**Who raises them:** Drivers on mobile (credit request, check payment, client refusal, partial payment)

| Exception type | Supervisor must… |
|----------------|------------------|
| **Credit request** | Call client, then **Approve** (accepts debt responsibility) or **Reject** |
| **Check approval** | Verify, then Approve / Reject |
| **Client refusal** | Confirm with client, then Approve (authorizes return) or Reject |
| **Partial payment** | Decide, Approve / Reject |

**Rule:** Resolution requires a **written note** (confirms the phone call happened).

**Statuses shown:** Pending | Approved | Rejected

---

### 4.2 Missions (Depot Manager + Supervisor + General Management)

**Where:** Operations → **Missions** + depot detail on map + Mission detail page

**How missions are born**

1. **System runs** (usually evening, configurable time) → creates **Proposed** missions for tomorrow  
2. **Human approves** → mission becomes **Approved** → driver sees it on mobile next morning  

| Action | Who | Effect |
|--------|-----|--------|
| **Run / plan missions** | Management / depot | Triggers system to build proposals |
| **Edit stops / livreur** | Depot manager / management | Adjust route before approval |
| **Approve mission** | Depot manager / management | Driver can execute next day |
| **Reassign livreur** | Depot manager / management | Move mission to another driver |
| **Create custom mission** | Depot manager / management | Manual route |

**Mission statuses:** Proposed → Approved → In progress → Completed (or Cancelled)

**Command Center alert:** If orders are due **tomorrow** but not yet on any mission → shown in **Fleet Status** warning.

---

### 4.3 Stock replenishment proposals (Depot Manager + General Management)

**Where:** Inventory → **Proposal Inbox** (also linked from HUD)

**Who creates them:** **The system** when depot stock falls below minimum (or needs transfer from another depot)

| Proposal type | Meaning |
|---------------|---------|
| **Transfer** | Move stock from another depot |
| **Industry request** | Order from supplier / factory |

| Action | Effect |
|--------|--------|
| **Accept** | Converts proposal into an active **stock request** |
| **Dismiss / reject** | Hides from inbox (local dismiss — does not delete system record) |

**After accept:** Stock request may still need **management approval** (see 4.4) unless auto-approve applies.

---

### 4.4 Stock requests (General Management with “approve stock” permission)

**Where:** Inventory → **Stock** → request detail

| Status | Meaning | Who acts |
|--------|---------|----------|
| **Pending management** | Waiting for company approval | General Management approves → sent to industry |
| **Approved for industry** | Supplier must prepare | Industry user (separate flow) |
| **Shipped / Received** | In transit / in depot | Operational tracking |

---

### 4.5 Vendor load requests (Depot Manager)

**Where:** Depot detail panel → **Vendor Loads** tab (on map) or depot operations

**Who raises them:** **Vendor** on mobile — wants to load the van from depot tomorrow

| Action | Who | Effect |
|--------|-----|--------|
| **Approve** | Depot manager | Stock allocated to vendor route |
| **Reject** | Depot manager | Vendor must adjust request |

---

### 4.6 Client credit (Supervisor / Management on client profile)

**Where:** Clients → client detail (map or list) → Credit section

| Setting | Effect |
|---------|--------|
| **Auto-approve credit OFF** | Order on credit → **pending supervisor** (mobile exception flow) |
| **Auto-approve credit ON** | Credit orders pass without supervisor call |
| **Credit limit / days** | Business rules for that shop |

This is **per client**, not global.

---

### 4.7 Orders (web)

**Where:** Operations → **Orders**

- View order list and detail  
- Create orders (roles with permission)  
- Order **confirmation** and escalation rules tie into stock availability (system may hold orders as “escalated” if stock insufficient)

---

## 5. What happens automatically (no human click)

Understanding auto-rules helps decide **what should appear as an alert** vs what should silently run.

### 5.1 Mission dispatch (evening)

| Setting | Where | Default |
|---------|-------|---------|
| **Mission dispatch time** | Settings → Logistics | **19:00** |

At this time the system can build **proposed missions** for the next day (deliveries, industry pickups, collections).

### 5.2 Stock replenishment auto-approve

**Three levels** (any one can auto-approve):

| Level | Setting | Where to configure |
|-------|---------|-------------------|
| **Global** | “Auto-approve replenishment” | Settings → Inventory (General Management only) |
| **Per depot** | “Auto-approve replenishment” on depot | Edit depot on map or depot settings |
| **When both off** | Proposal appears in **Proposal Inbox** → human must **Accept** |

If auto-approve is ON: system creates the stock request **without** showing in Proposal Inbox.

### 5.3 Client credit auto-approve

| Setting | Effect |
|---------|--------|
| Per-client **Auto-approve credit** | Credit sales skip supervisor exception |

### 5.4 Low stock detection

| Setting | Default | Effect |
|---------|---------|--------|
| **Low stock threshold** | 10 (units) | Used in inventory alerting logic |

System scans depots and generates **proposals** when products fall below minimum levels defined per product in depot.

### 5.5 Collection missions

When a client owes money on a **due date**, the system can **inject a collection stop** into a future mission automatically (no manual mission building for that debt).

### 5.6 Responsibility / debt ledger

When supervisors **approve** credit or collections happen in the field, the system **transfers financial responsibility** in the background (visible on **Debt Summary**).

---

## 6. Alerts & warnings (what managers should notice)

### 6.1 Command Center HUD alerts

| Signal | When it appears | Suggested manager action |
|--------|-----------------|---------------------------|
| **Yellow — Payment/Return requests** | Pending exceptions > 0 | Open Exceptions → approve/reject |
| **Blue — Replenishment proposals** | Pending proposals > 0 | Open Proposal Inbox → accept |
| **Red depot bar — OVER CAPACITY** | Depot stock value > 100% of price capacity | Slow replenishment or redistribute stock |
| **Amber depot bar** | Depot ≥ 80% full | Plan transfer or industry order |
| **Fleet — unassigned tomorrow orders** | Orders due tomorrow not on any mission | Open Missions → approve/build routes |
| **Fleet — drivers on road** | X / Y drivers checked in today | Operational follow-up if X is low |
| **Green — System Healthy** | No pending actions, no critical depots, no fleet warnings | No action needed |

### 6.2 Missions page KPIs

| KPI | Meaning |
|-----|---------|
| **Awaiting approval** | Proposed missions not yet approved for selected date/depot |
| **Active missions** | Approved or in progress |
| **Successful deliveries** | Completed delivery stops (stats) |

### 6.3 Stock page

| Signal | Meaning |
|--------|---------|
| **Pending management requests** | Stock requests waiting for General Management approval |
| **Low stock lines** | Products at or below minimum in depot view |

### 6.4 Debt page

| Section | Meaning |
|---------|---------|
| **Supervisor debt** | Total amount supervisors are responsible for (approved credit, etc.) |
| **Livreur carried cash** | Cash collected but not yet deposited |

---

## 7. Role-by-role: web access in detail

### 7.1 General Management (Company admin)

**Typical sidebar:** Everything.

| Area | Can see | Can do | Must approve/reject |
|------|---------|--------|---------------------|
| Command Center | Full map + HUD + all entities | Create/edit network on map | — |
| Users & Roles | All | Create users, assign roles & territories | — |
| Settings | All | Change dispatch time, global auto-approve, low stock threshold | — |
| Orders | All (scoped if configured) | Create, view | — |
| Missions | All depots | Plan, edit, approve, reassign | **Approve missions** |
| Exceptions | All | Resolve any exception | **Approve / reject** |
| Proposal Inbox | All | Accept proposals | **Accept / dismiss** |
| Stock | All depots | View, submit requests | **Approve stock requests** (if permission granted) |
| Debt Summary | Company-wide | View only | — |
| Catalog | All | Manage brands, categories, products | — |
| Clients | All | Edit credit settings, locations | Credit policy per client |

**CEO lens:** This is the role whose Command Center view defines the “company control tower.”

---

### 7.2 Supervisor (Regional)

**Typical sidebar (if permissions granted):** Orders, Clients, Missions, Exceptions, Debt, sometimes Command Center (read-only map).

| Area | Can see | Can do | Must approve/reject |
|------|---------|--------|---------------------|
| Command Center | Map + HUD for **assigned territory** | Usually view only (no map editing) | Use HUD links to Exceptions |
| Orders | Their sectors | View, sometimes create | — |
| Clients | Their sectors | View, edit credit | Set auto-credit per client |
| Missions | Their depots | View, often **approve** with depot | **Approve missions** (shared with depot) |
| Exceptions | Their region | **Primary owner** | **Approve / reject** all types |
| Debt Summary | Supervisors + livreurs in scope | View | — |
| Proposals / Stock | Often limited | May accept depot proposals if given logistics access | Depends on permissions |

**Cannot usually:** Manage users, change global settings, edit map boundaries.

**CEO lens:** Supervisor Command Center should emphasize **exception count, debt exposure, and mission approval backlog** in their zones — not global catalog or user admin.

---

### 7.3 Depot Manager (Depot Supervisor)

**Typical sidebar:** Stock-related views, Missions, Command Center (depot-focused), sometimes Industries.

| Area | Can see | Can do | Must approve/reject |
|------|---------|--------|---------------------|
| Command Center | Their depot(s) on map | May edit depot location if given logistics write access | — |
| Depot detail (map) | Stock, livreurs, missions, vendor loads tabs | Operational view | **Vendor load approve/reject** |
| Missions | **Their depot only** | Edit routes, reassign drivers | **Approve missions** |
| Proposal Inbox | Proposals for their depot | Accept replenishment suggestions | **Accept proposals** |
| Stock | Their depot | View levels, create requests | — |
| Fulfillment | Orders linked to their network | Track industry shipments | — |

**Cannot usually:** See other depots’ data, manage company users, resolve credit exceptions (supervisor job), view global debt.

**CEO lens:** Depot manager Command Center should feel like a **warehouse control room**: capacity gauge, tomorrow’s missions, pending vendor loads, stock proposals.

---

## 8. Territorial limits (why two people see different maps)

| Assignment type | Effect on web |
|-----------------|---------------|
| **Sector assignment** | Sees only clients / orders in those sectors |
| **Depot assignment** | Sees only that depot’s stock, missions, proposals |
| **Region assignment** | Broader map filter |

**General Management** has no assignment filter — sees entire country/network.

When scoping Command Center UI, ask: *“Is this KPI global, regional, or depot-only?”*

---

## 9. Command Center UI — decision checklist for the CEO

Use this when discussing what you want on screen:

### A. On the default HUD (no entity selected)

- [ ] Which **KPIs** must be visible at login? (sales, mission %, debt total, order count…)  
- [ ] Which **alerts** must never be hidden? (exceptions, full depots, unassigned orders…)  
- [ ] Should alerts be **clickable shortcuts** only, or also show preview detail inline?  
- [ ] Do we need **role-specific HUDs** (CEO vs depot manager see different widgets)?  

### B. On the map

- [ ] Which **layers** default ON? (all vs depots-only vs missions-only)  
- [ ] Do we need **colour rules**? (e.g. red depot pin = over capacity)  
- [ ] Should **live trucks** be more prominent than static depot pins?  

### C. On entity click (e.g. depot)

- [ ] Is the **profile header** (name, address, image) the right first impression?  
- [ ] Are the **four tabs** (Stock, Livreurs, Missions, Vendor Loads) the right split?  
- [ ] Should **approve actions** live on the map panel or only on full pages?  

### D. Approvals

- [ ] Should the HUD show **one combined inbox** or **separate counts** (exceptions vs proposals vs missions)?  
- [ ] Who should get **email/SMS** later when counts > 0? (not built yet — product decision)  

---

## 10. Quick reference — “Who approves what?”

| Item | Primary approver | Where on web |
|------|------------------|--------------|
| Driver credit / check / refusal | **Supervisor** | Exceptions |
| Tomorrow’s delivery route | **Depot manager** (+ management) | Missions |
| System stock proposal | **Depot manager** / management | Proposal Inbox |
| Stock request to industry | **General Management** | Stock → request detail |
| Vendor van load | **Depot manager** | Depot → Vendor Loads tab |
| Client credit policy | **Supervisor** / management | Client profile |
| Replenishment auto-approve | **System** (if toggles ON) | Settings / depot flag |
| Mission auto-build | **System** (at dispatch time) | Settings → dispatch time |

---

## 11. What is NOT on the web yet (expectations)

- Prevendeurs, vendors, and livreurs **do not** use this dashboard for daily work (mobile app).  
- **No push notifications** on web for new exceptions (must open Exceptions or HUD).  
- **Proposal “reject”** in inbox is dismiss-only on screen — not a formal backend rejection workflow.  
- Some inventory menu items are **open to all logged-in users** (no strict permission gate yet) — may change.  

---

## Related document

For mobile + full company overview (all channels): see [ROLES_AND_CAPABILITIES.md](./ROLES_AND_CAPABILITIES.md).

*Last updated: June 2026 — reflects current web app and Command Center as implemented.*
