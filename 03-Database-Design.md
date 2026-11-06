# CafeOS

# Database Design

Version: 1.0

Status: Active

Database: PostgreSQL

ORM: Prisma

---

> **Current implementation note (2026-11-06):** The MVP inventory below is an
> earlier planning snapshot. The complete **Current Prisma Schema** reference
> at the end of this document is authoritative for the implemented schema,
> relationships, constraints, enums, and migrations.

# Purpose

This document defines the database structure for the CafeOS MVP.

The database is designed to be:

- Simple
- Scalable
- Multi-tenant
- Easy to extend

Only the tables required for MVP are included.

---

# Database Overview

```

Restaurant
│
├── Employee
│
├── RestaurantTable
│
├── Category
│ │
│ └── MenuItem
│
└── Order
│
└── OrderItem

```

---

# Historical MVP Tables (superseded by Current Prisma Schema below)

The MVP contains **7 tables**.

1. Restaurant
2. Employee
3. RestaurantTable
4. Category
5. MenuItem
6. Order
7. OrderItem

---

# 1. Restaurant

Stores restaurant information.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| name | String | Restaurant Name |
| email | String | Login Email |
| phone | String | Contact Number |
| address | String | Restaurant Address |
| logo | String | Cloudinary URL |
| createdAt | DateTime | Created Time |
| updatedAt | DateTime | Updated Time |

---

Relationship

Restaurant

↓

Employees

Restaurant Tables

Categories

Menu Items

Orders

---

# 2. Employee

Currently only one employee (Owner) is required.

Future versions will support multiple employees.

| Field | Type |
|--------|------|
| id | UUID |
| restaurantId | UUID |
| name | String |
| email | String |
| password | String |
| role | Enum |
| createdAt | DateTime |

---

Role Enum

```

OWNER

MANAGER

CHEF

WAITER

```

Only OWNER is required for MVP.

---

# 3. RestaurantTable

Represents a physical table inside the restaurant.

Examples

- Table 1
- Table 2
- Table 5
- Outdoor A
- VIP 1

Each table has its own QR Code.

| Field | Type |
|--------|------|
| id | UUID |
| restaurantId | UUID |
| tableName | String |
| qrCode | String |
| isActive | Boolean |
| createdAt | DateTime |

---

Relationship

Restaurant

↓

Restaurant Tables

---

QR Flow

Customer

↓

Scans QR

↓

QR contains tableId

↓

Backend identifies

Restaurant

+

Table

↓

Returns correct menu

---

# 4. Category

Examples

```

Pizza

Coffee

Desserts

Burgers

```

| Field | Type |
|--------|------|
| id | UUID |
| restaurantId | UUID |
| name | String |
| createdAt | DateTime |

---

Relationship

Restaurant

↓

Category

↓

Menu Items

---

# 5. MenuItem

Stores restaurant menu.

| Field | Type |
|--------|------|
| id | UUID |
| restaurantId | UUID |
| categoryId | UUID |
| name | String |
| description | String |
| image | String |
| price | Decimal |
| isAvailable | Boolean |
| createdAt | DateTime |
| updatedAt | DateTime |

---

Example

```

Name

Cold Coffee

Price

180

Category

Coffee

Available

Yes

```

---

Relationship

Category

↓

Menu Items

---

# 6. Order

Stores customer orders.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| restaurantId | UUID | Restaurant Reference |
| tableId | UUID | Restaurant Table |
| orderNumber | String | Human-readable order number (e.g. ORD-1001) |
| customerName | String | Customer Name |
| customerPhone | String (Optional) | Customer Phone Number |
| totalAmount | Decimal | Total Order Amount |
| status | Enum | Current Order Status |
| createdAt | DateTime | Created Time |
| updatedAt | DateTime | Updated Time |

---

Status Enum

```
PENDING

ACCEPTED

PREPARING

READY

COMPLETED

CANCELLED
```

---

Relationship

Restaurant

↓

Orders

↓

Order Items


---

# 7. OrderItem

Stores every item inside an order.

| Field | Type |
|--------|------|
| id | UUID |
| orderId | UUID |
| menuItemId | UUID |
| quantity | Int |
| price | Decimal |

---

Example

Order

↓

Cold Coffee ×2

Pizza ×1

Burger ×3

---

# Entity Relationships

Restaurant

↓

Employee

Restaurant

↓

RestaurantTable

Restaurant

↓

Category

↓

MenuItem

Restaurant

↓

Order

↓

OrderItem

RestaurantTable

↓

Order

Category

↓

MenuItem

MenuItem

↓

OrderItem

---

# ER Diagram

```

Restaurant

│

├──────────────┐

│              │

Employee    RestaurantTable

│

Category

│

MenuItem

│

OrderItem

▲

│

Order

```

---

# Images

Images are stored in Cloudinary.

Database stores only image URLs.

Example

```

https://res.cloudinary.com/...

```

---

# UUID

Every table uses UUID.

Never use auto increment IDs.

---

# Future Tables

Not part of MVP.

```

Customer

Coupon

Reservation

Review

Payment

Inventory

Supplier

Notification

Analytics

Loyalty

```

---

# Complete Order Flow

Restaurant

↓

Creates Categories

↓

Creates Menu

↓

Creates Restaurant Tables

↓

Prints QR Codes

↓

Customer Scans QR

↓

Backend identifies Restaurant

↓

Returns Menu

↓

Customer Enters Name

↓

(Optional) Customer Enters Phone Number

↓

Customer Places Order

↓

Order Saved

↓

Restaurant Dashboard Receives Order

↓

Restaurant Dashboard Receives Order

↓

Restaurant Updates Status

↓

Customer Receives Live Updates


# Why Customer Phone is Optional

The phone number is optional in the MVP.

If provided, CafeOS can later support:

- Returning customer recognition
- Loyalty Program
- WhatsApp order updates
- Google Review reminders
- Birthday offers
- Customer Analytics
- AI personalized recommendations

No database migration will be required.

---

# Database Rules

- Every table belongs to one restaurant.
- Every menu item belongs to one category.
- Every order belongs to one table.
- Every order contains one or more order items.
- Restaurant tables are identified using QR codes.
- Images are stored in Cloudinary.
- UUID is mandatory.
- Every table contains `createdAt`.
- Tables with editable content also contain `updatedAt`.

---

# Future Scalability

The current schema allows future support for:

- Multi-branch restaurants
- Reservations
- Split bills
- Multiple customers on one table
- AI Waiter
- Customer accounts
- Loyalty program
- Payments
- Inventory
- Analytics

without major database redesign.

---

# MVP Checklist

- Restaurant
- Employee
- RestaurantTable
- Category
- MenuItem
- Order
- OrderItem

If all seven tables are implemented, the CafeOS MVP database is complete.

---

---

# Current Prisma Schema

Source of truth: `cafeos-backend/prisma/schema.prisma`. IDs use Prisma `cuid()`
by default; `AuthSession` and `RefreshToken` IDs use UUIDs. PostgreSQL stores
money in `DECIMAL(10,2)`. Dates are timestamp columns; timestamps with
`@default(now())` are database-generated, while `updatedAt` uses Prisma
`@updatedAt`. Optional fields are nullable. The current schema contains these
19 models:

| Model | Implemented data and relations |
|---|---|
| `Restaurant` | Fields: `id`, `name`, `slug`, `phone`, `address`, `createdAt`, `updatedAt`, `restaurantEmail`, `coverImageUrl`, `cuisineType`, `customLink`, `description`, `facebook`, `instagram`, `logoUrl`, `tagline`, `themeColor`, `website`, `coverPublicId`, `logoPublicId`, `openingHours`. Parent of employees, tables, categories, menu items, orders, offers, loyalty data, merges, audit logs, and public-order idempotency records. |
| `Employee` | Fields: `id`, `restaurantId`, `name`, `email`, `passwordHash`, `createdAt`, `updatedAt`, `deletedAt`, `isActive`, `lastLoginAt`, `role`. Belongs to a restaurant; owns notifications, audit entries, and authentication sessions. |
| `AuthSession` | Fields: `id`, `employeeId`, `createdAt`, `lastUsedAt`, `expiresAt`, `revokedAt`. Belongs to employee and has many refresh-token records. |
| `RefreshToken` | Fields: `id`, `sessionId`, `tokenHash`, `createdAt`, `expiresAt`, `consumedAt`, `replacedByTokenId`. Belongs to a session; raw refresh tokens are not stored. |
| `Notification` | Fields: `id`, `employeeId`, `type`, `title`, `message`, `isRead`, `readAt`, `data`, `createdAt`. Belongs to an employee. |
| `AuditLog` | Fields: `id`, `restaurantId`, `employeeId`, `action`, `entity`, `entityId`, `metadata`, `createdAt`. Belongs to a restaurant and optionally an employee. |
| `PublicOrderIdempotency` | Fields: `id`, `restaurantId`, `tableId`, `customerSessionId`, `idempotencyKey`, `requestHash`, `orderId`, `responseSnapshot`, `createdAt`, `updatedAt`. Used to deduplicate retried public orders. |
| `RestaurantTable` | Fields: `id`, `restaurantId`, `name`, `qrCode`, `createdAt`, `updatedAt`, `status`. Belongs to a restaurant; related to orders, merges, and public-order idempotency records. |
| `TableMerge` | Fields: `id`, `restaurantId`, `isActive`, `createdAt`, `updatedAt`. Belongs to a restaurant and has merge/table association rows and related orders. |
| `TableMergeTable` | Fields: `id`, `restaurantId`, `mergeId`, `tableId`, `createdAt`. Association row linking a restaurant, table merge, and restaurant table. |
| `Category` | Fields: `id`, `restaurantId`, `name`, `createdAt`, `updatedAt`. Belongs to a restaurant and has menu items. |
| `MenuItem` | Fields: `id`, `restaurantId`, `categoryId`, `name`, `description`, `price`, `isAvailable`, `imageUrl`, `createdAt`, `updatedAt`, `imagePublicId`, `foodType`, `imagePositionX`, `imagePositionY`, `imageScale`. Belongs to a restaurant, optionally a category, and may be referenced by order items. |
| `Order` | Fields: `id`, `restaurantId`, `tableId`, `customerPhone`, `total`, `createdAt`, `updatedAt`, `customerId`, `status`, `loyaltyProcessed`, `customerSessionId`, `tableMergeId`, `appliedOfferId`, `discountAmount`, `subtotal`. Belongs to restaurant/table; optionally customer, merge and offer; has items, rewards and optional public idempotency record. |
| `OrderItem` | Fields: `id`, `orderId`, `menuItemId`, `quantity`, `price`, `createdAt`, `updatedAt`. Belongs to an order and menu item. |
| `Offer` | Fields: `id`, `restaurantId`, `name`, `description`, `discountType`, `discountValue`, `minimumOrderValue`, `maximumDiscount`, `isActive`, `startsAt`, `endsAt`, `createdAt`, `updatedAt`. Belongs to restaurant; may be referenced by orders. |
| `LoyaltyProgram` | Fields: `id`, `restaurantId`, `rewardName`, `purchaseThreshold`, `rewardQuantity`, `minimumOrderValue`, `isActive`, `createdAt`, `updatedAt`. Belongs to restaurant; has customer-program progress and rewards. |
| `LoyaltyCustomer` | Fields: `id`, `restaurantId`, `phone`, `name`, `visitCount`, `totalSpend`, `lastOrderAt`, `createdAt`, `updatedAt`. Belongs to restaurant; related to orders, progress and rewards. |
| `LoyaltyCustomerProgram` | Fields: `id`, `customerId`, `programId`, `progressCount`, `createdAt`, `updatedAt`. Joins a loyalty customer to a program. |
| `LoyaltyReward` | Fields: `id`, `restaurantId`, `customerId`, `programId`, `status`, `orderId`, `createdAt`, `redeemedAt`. Belongs to restaurant/customer; program and order are optional. |

## Relationships and deletion behavior

- Deleting a restaurant cascades through its employees, tables, categories,
  menu items, orders, offers, loyalty records, table merges, idempotency
  records, and audit logs where the schema declares `onDelete: Cascade`.
- Deleting an employee cascades to its authentication sessions and
  notifications. Deleting a session cascades to its refresh-token history.
- Deleting a table cascades to merge membership and public idempotency records;
  orders reference tables without an explicit cascade and therefore are not
  silently deleted by that relation.
- Deleting a table merge cascades to its association rows. Deleting a category
  does not delete menu items; `MenuItem.categoryId` is optional and follows
  Prisma’s optional-relation behavior.
- `OrderItem.order` cascades when its order is deleted. Other relations without
  an explicit cascade use the referential action generated by Prisma/PostgreSQL
  for that required or optional relation; inspect schema and migration DDL
  before changing retention or deletion behavior.
- `PublicOrderIdempotency.orderId` is unique and its order relation uses
  `onDelete: Restrict` to preserve the idempotency record’s referential
  integrity.

## Uniqueness and indexes

Every model has a primary key. Additional uniqueness constraints:

- `Restaurant.slug`
- `Employee.email`
- `RefreshToken.tokenHash`
- `PublicOrderIdempotency.orderId` when non-null, and
  `(restaurantId, customerSessionId, idempotencyKey)`
- `RestaurantTable.qrCode` and `(restaurantId, name)`
- `TableMergeTable(mergeId, tableId)`
- `Category(restaurantId, name)`
- `LoyaltyCustomer(restaurantId, phone)`
- `LoyaltyCustomerProgram(customerId, programId)`

The non-unique indexes declared in the Prisma schema are:

| Model | Indexes |
|---|---|
| `Employee` | `(restaurantId, createdAt)`, `(restaurantId, role)`, `(restaurantId, isActive)` |
| `AuthSession` | `(employeeId, revokedAt, expiresAt)`, `(expiresAt)` |
| `RefreshToken` | `(sessionId, consumedAt, expiresAt)`, `(expiresAt)` |
| `Notification` | `(employeeId, createdAt DESC)`, `(employeeId, isRead)`, `(type)` |
| `AuditLog` | `(restaurantId, createdAt)`, `(restaurantId, action)`, `(restaurantId, employeeId)`, `(restaurantId)` |
| `PublicOrderIdempotency` | `(restaurantId, createdAt)` |
| `RestaurantTable` | `(restaurantId, status)`, `(restaurantId, createdAt)` |
| `TableMerge` | `(restaurantId, isActive)`, `(restaurantId, createdAt)` |
| `TableMergeTable` | `(restaurantId, tableId)`, `(restaurantId, mergeId)`, `(tableId)` |
| `Category` | `(restaurantId, createdAt)` |
| `MenuItem` | `(restaurantId)`, `(categoryId)`, `(restaurantId, createdAt)`, `(restaurantId, isAvailable)`, `(restaurantId, categoryId)` |
| `LoyaltyProgram` | `(restaurantId)`, `(restaurantId, isActive)` |
| `LoyaltyCustomer` | `(restaurantId, phone)` |
| `LoyaltyCustomerProgram` | `(customerId)`, `(programId)` |
| `LoyaltyReward` | `(restaurantId, customerId, status)`, `(programId, status)` |
| `Order` | `(restaurantId)`, `(tableId)`, `(restaurantId, createdAt)`, `(restaurantId, status)`, `(restaurantId, tableId)`, `(tableId, customerSessionId, status)`, `(restaurantId, appliedOfferId)`, `(tableMergeId)` |
| `OrderItem` | `(orderId)`, `(menuItemId)` |
| `Offer` | `(restaurantId)`, `(restaurantId, isActive)`, `(restaurantId, startsAt, endsAt)` |

Primary-key and uniqueness indexes are described above. Exact names/types and
sort direction remain defined by `@@index`, `@@unique`, and native Prisma
types in `schema.prisma`.

## Enums

```text
EmployeeRole: OWNER | MANAGER | STAFF
NotificationType: NEW_ORDER | ORDER_STATUS | LOYALTY_REWARD | EMPLOYEE_ACTIVITY
TableStatus: AVAILABLE | OCCUPIED | RESERVED | INACTIVE
FoodType: VEG | NON_VEG | EGG
OrderStatus: PENDING | CONFIRMED | PREPARING | COMPLETED | CANCELLED
LoyaltyRewardStatus: AVAILABLE | REDEEMED
DiscountType: PERCENTAGE | FIXED
AuditAction: LOGIN | LOGOUT | PASSWORD_CHANGED | EMPLOYEE_CREATED |
  EMPLOYEE_UPDATED | EMPLOYEE_STATUS_CHANGED | EMPLOYEE_DELETED |
  CATEGORY_CREATED | CATEGORY_UPDATED | CATEGORY_DELETED | MENU_CREATED |
  MENU_UPDATED | MENU_DELETED | TABLE_CREATED | TABLE_UPDATED | TABLE_DELETED |
  ORDER_CREATED | ORDER_STATUS_CHANGED | ORDER_CANCELLED | SETTINGS_UPDATED |
  TABLES_MERGED | TABLES_UNMERGED
```

`OrderStatus` does not include `READY` or `SERVED`; the terminal successful
state is `COMPLETED`. Valid transitions are enforced by backend service logic.

## Migration history

The Prisma migration history is append-only. The authentication-session
migration is
`20261105230000_add_auth_sessions/migration.sql`; it creates `AuthSession` and
`RefreshToken`, the employee/session foreign keys with `ON DELETE CASCADE`,
the unique token-hash index, and the session/token lookup and expiry indexes.
It does not rewrite previous migrations.

The checked-in sequence, in order, is:

1. `20260702074142_init`
2. `20260702081715_add_restaurant_email`
3. `20260704100408_add_order_status_enum`
4. `20260705050631_add_menu_image_public_id`
5. `20260706083358_add_table_status_enum`
6. `20260718100404_add_restaurant_branding`
7. `20260718102314_add_restaurant_image_public_ids`
8. `20260719172933_add_employee_rbac_fields`
9. `20260720100805_add_audit_logs`
10. `20260722055919_add_performance_indexes`
11. `20260724095259_fix_category_unique`
12. `20260724102304_add_menu_image_transform`
13. `20260803080320_unique_table_per_restaurant`
14. `20260803164623_add_loyalty_system`
15. `20260803171313_use_order_status_enum`
16. `20260804093939_add_loyalty_processed_flag`
17. `20260804155532_add_notifications`
18. `20260805180832_remove_order_ready_status`
19. `20260809081616_add_restaurant_opening_hours`
20. `20260810092124_multiple_loyalty_programs`
21. `20260810095658_preserve_redeemed_loyalty_rewards`
22. `20260810172158_add_customer_ip_to_orders`
23. `20260812083209_replace_customer_ip_with_session`
24. `20260814091856_add_table_merges`
25. `20260814102745_add_offers`
26. `20261019150000_add_public_order_idempotency`
27. `20261105230000_add_auth_sessions`

In particular, `remove_order_ready_status` precedes the current order enum
contract; historical references to READY/SERVED are not current statuses.
Apply migrations only through the approved deployment process after checking
the target database identity.

---

End of Document