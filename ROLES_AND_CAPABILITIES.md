# Distrac — Who Does What?

**A plain-language guide to user roles and what the platform can do today.**

---

## What is Distrac?

Distrac is our **distribution operating system**. It connects everything from the factory or supplier, through warehouses (depots), to the van or truck, and finally to the shop on the street.

The goal is simple: **see the business in real time, automate repetitive decisions, and hold the right person accountable at each step** — especially for stock, deliveries, and money.

We built the platform around **six standard job roles**. Each person logs in with one role. What they see and what they are allowed to do follows that role. Management can also fine-tune access over time (for example, which menu items appear in the office dashboard).

---

## Two places people work

| Where | Who uses it | Purpose |
|--------|-------------|---------|
| **Office dashboard (web)** | Management, supervisors, depot managers | Plan routes, approve missions, watch the map, manage users, review debt and exceptions |
| **Mobile app (field)** | Drivers, vendors, prevendeurs | Execute the day: deliver, collect cash, sell from the van, visit clients, scan QR codes |

Not every role uses both. Office roles live mostly on the web. Field roles live mostly on the phone.

---

## The six roles at a glance

| Role | Plain name | Works mainly on | In one sentence |
|------|------------|-----------------|-----------------|
| **General Management** | Company admin | Office dashboard | Full visibility and control over the whole network |
| **Supervisor** | Regional supervisor | Office + oversight | Approves risky decisions (credit, returns) and watches debt in their territory |
| **Depot Supervisor** | Depot / warehouse manager | Office + depot | Runs the warehouse, approves missions and stock proposals for their depot |
| **Seller** | Prevendeur (pre-sales) | Mobile | Visits shops, opens new clients, places orders for later delivery |
| **Vendor** | Van seller | Mobile | Sells directly from the vehicle stock and manages mobile inventory |
| **Livreur** | Driver / delivery agent | Mobile | Executes delivery routes, pickups, and cash collection missions |

---

## 1. General Management (Company admin)

**Who:** Director, operations head, or IT owner.

**Business job:** Run Distrac as a company-wide tool — structure the network, configure people, and see the full picture.

### What they can do today

**Command Center (live map)**  
- Open an interactive map of regions, depots, clients, vehicles, and industries.  
- Click any location for a profile-style detail panel (for example, depot stock, assigned drivers, missions).  
- See an **operations dashboard**: today’s sales, mission progress, depot capacity warnings, drivers checked in, and items waiting for approval.

**Network setup**  
- Create and edit **industries** (suppliers), **depots** (warehouses), **regions**, and **sectors**.  
- Manage **clients** and their locations on the map.  
- Manage **vehicles** and link them to tracking when configured.

**Operations**  
- View and manage **orders** across the company.  
- Plan and **approve missions** (tomorrow’s routes: deliveries, pickups, collections).  
- Review **exceptions** (payment changes, return requests from the field).  
- Handle **stock proposals** from the automated replenishment engine.

**Catalog & inventory**  
- Maintain **brands, categories, and products**.  
- Monitor **stock levels** and replenishment across depots.

**People & governance**  
- Create users, assign roles, and define **who can see which menus**.  
- Configure **system settings** (company name, dispatch time, global rules).

**Finance**  
- View **debt summaries** — who is holding responsibility for cash or credit in the chain.

### What makes this role special

General Management is the **only role designed for unrestricted company-wide access**. Everyone else is limited to their depots, sectors, or their own field work.

---

## 2. Supervisor (Regional supervisor)

**Who:** Area manager responsible for a group of sectors / depots.

**Business job:** **Approve exceptions and carry financial responsibility** when the field asks for credit, checks, or special payment terms. Keep debt and client issues under control.

### What they can do today

**Office dashboard**  
- See **orders and clients** in their assigned territory (not the whole country unless scoped that way).  
- Access **reports** relevant to their area.  
- Work with **missions** — review and approve proposed routes with depot management.  
- Open the **Command Center map** for logistics visibility (when granted logistics access).  
- Handle **exception inbox**: approve or reject driver requests (e.g. client wants to pay by check, return refused goods).

**Financial oversight**  
- Appear on **debt reports** as the person responsible for approved credit / delayed payments.  
- Resolve or reschedule collections when clients cannot pay on the original date.

**Team oversight (direction of travel)**  
- Monitor activity of **vendors and prevendeurs** in assigned sectors (sales visits, new clients).

### What they typically do *not* do

- Day-to-day warehouse picking (that is the depot manager).  
- Driving routes (that is the livreur).  
- Full system configuration (that is General Management).

---

## 3. Depot Supervisor (Depot / warehouse manager)

**Who:** Manager of a single warehouse or hub.

**Business job:** **Keep the depot full but not overloaded**, prepare tomorrow’s work, and control fleet assignment for that location.

### What they can do today

**Warehouse view**  
- See **stock in the depot** — quantities, value, and how full the warehouse is (price and volume capacity).  
- Review **replenishment proposals** generated by the system and accept or reject them.  
- Track **industry fulfillment** orders coming in or going out.

**Missions & fleet**  
- Review **proposed missions** for the next day (deliveries, industry pickups, collections).  
- **Edit, approve, or reassign** missions and drivers for their depot.  
- See which **livreurs** are assigned to the depot and their vehicles.

**Vendor coordination**  
- Review **vendor load requests** — when a van seller asks to load product from the depot for the next day.

**Delivery preparation**  
- Access **delivery-related** views tied to order preparation.

### What makes this role special

The depot manager is the **operational gate** between automated planning and execution. Nothing leaves the warehouse on a mission without depot-level approval in the normal workflow.

---

## 4. Seller — Prevendeur (Pre-sales)

**Who:** Field agent focused on **visits and order capture**, not necessarily carrying full van stock.

**Business job:** Grow the network — **visit shops, register clients, place orders** that the depot will fulfill later via a driver.

### What they can do today (mobile)

- **Visit clients** and view client information in their area.  
- **Create orders** for delivery on a future date.  
- **Register new clients** with location and identification (including QR codes for later delivery verification).  
- Perform **vehicle check-in (pointage)** when required before starting the day.  
- Follow a **daily mission** of client visits (seller route).

### What they typically do *not* do

- Approve credit or exceptions (supervisor).  
- Manage warehouse stock (depot manager).  
- Company-wide reporting (management).

---

## 5. Vendor (Van seller)

**Who:** Salesperson who **sells from the vehicle** with stock on board.

**Business job:** **Sell on the spot**, manage mobile stock, and bring revenue directly from the route.

### What they can do today (mobile)

- **Sell products** from van stock to clients.  
- **Create orders / invoices** during the visit.  
- **Manage mobile inventory** — load requests from depot, stock movements on the van.  
- **Visit clients** and update client information.  
- **Onboard new shops** and generate client QR codes.  
- **Vehicle check-in** and daily **vendor route** missions (deliveries + visits).  
- Submit **load requests** to the depot for approval before a heavy stock day.

### Difference from Prevendeur

| | Prevendeur | Vendor |
|---|------------|--------|
| Main action | Order for later delivery | Sell now from the van |
| Stock | Usually no full van warehouse | Carries and tracks van stock |
| Depot interaction | Indirect (order fulfillment) | Direct (load requests, replenishment) |

---

## 6. Livreur (Driver / delivery agent)

**Who:** Driver executing **planned routes**.

**Business job:** **Deliver goods, pick up from suppliers, collect cash** — with proof of presence at each stop.

### What they can do today (mobile)

- **Check in vehicle** at start of day (required before seeing missions).  
- View **today’s mission** — ordered list of stops (deliveries, pickups, collections).  
- **Scan client QR codes** to confirm arrival at the correct shop.  
- **Complete deliveries** and record outcomes.  
- **Collect payments** on collection stops.  
- **Request supervisor approval** when something unusual happens (credit, check, refused order, partial payment).  
- Report **exceptions** that appear in the supervisor’s inbox on the web dashboard.

### What makes this role special

The livreur is the **last mile**. The system is strict on purpose: many actions are blocked until the driver proves they are at the client (QR scan). That protects the company from false deliveries and unclear cash handover.

---

## How the roles work together (typical day)

```
Evening (system + depot)
  → System proposes tomorrow’s missions and stock needs
  → Depot manager reviews and approves missions
  → Management sees Command Center: unassigned orders, depot capacity, progress

Morning (field)
  → Livreurs / vendors / prevendeurs check in their vehicle
  → They open their mission for the day
  → They execute stops and scan QR codes at clients

During the day (exceptions)
  → Driver hits a problem (credit, return, check)
  → Request goes to Supervisor inbox
  → Supervisor approves or rejects on the web dashboard

End of day (oversight)
  → Management sees sales pulse, mission completion %, debt, full depots
  → Depot manager handles vendor load requests and stock proposals
```

---

## What we have built so far (summary for leadership)

### Office platform (web)

- **Command Center** — live map + operational HUD (sales, mission progress, alerts, depot health, fleet status).  
- **Logistics network** — industries, depots, regions, sectors, clients, vehicles on a map.  
- **Mission planning** — propose, edit, approve tomorrow’s routes.  
- **Orders** — create and track customer orders.  
- **Exceptions** — supervisor workflow for field problems.  
- **Inventory brain** — automated replenishment proposals, fulfillment tracking, stock views.  
- **Product catalog** — brands, categories, products.  
- **Users & roles** — who can log in and what menus they see.  
- **Debt & finance views** — responsibility chain for cash and credit.  
- **Depot profiles** — rich detail per warehouse (stock, drivers, missions, vendor loads).

### Field platform (mobile)

- **Vehicle check-in (pointage)**  
- **Daily missions** with ordered stops  
- **QR-verified visits** at clients  
- **Delivery, pickup, and collection** flows  
- **Exception requests** to supervisors  
- **Vendor van stock** and load requests  
- **Prevendeur visits** and order capture  

### Automated “system” logic (runs in the background)

- Suggests **when depots need stock** and from where (depot transfer vs industry order).  
- Builds **mission proposals** from tomorrow’s orders, pickups, and collections.  
- Tracks **depot capacity** (how full a warehouse is in value and volume).  
- Maintains a **responsibility ledger** for who holds money or debt at each step.

---

## Access in practice

- **Not everyone sees everything.** A depot manager sees their depot; a supervisor sees their sectors; management sees the whole network.  
- **The sidebar in the office app adapts** to each person — if a menu is missing, that role was not given that responsibility.  
- **Roles can be adjusted** by General Management without changing the product code (permissions and assignments).

---

## Glossary (quick reference)

| Term | Meaning |
|------|---------|
| **Mission** | A driver’s plan for one day — a sequence of stops (deliver, pick up, collect cash). |
| **Stop** | One task on a mission (one client, one industry pickup, etc.). |
| **Exception** | Something that went outside normal rules — needs supervisor decision. |
| **Proposal** | System suggestion (stock replenishment or mission) waiting for human approval. |
| **Pointage** | Morning vehicle check-in — proves the driver started the day. |
| **Depot** | Warehouse / distribution hub. |
| **Sector** | Sales territory, usually linked to a depot and region. |
| **Industry** | Supplier or factory that ships product into the network. |
| **Responsibility / debt** | Who officially holds the risk for money or goods at a given moment. |

---

## Document status

This document reflects the **intended design and what is implemented in the current Distrac codebase** (office dashboard + backend + field workflows). Some advanced items from the long-term master plan may still be partial or evolving; the role boundaries above describe the **target operating model** we are building toward.

*Last updated: June 2026*
