# CafeOS

# Database Design

Version: 1.0

Status: Active

Database: PostgreSQL

ORM: Prisma

---

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

# MVP Tables

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

SERVED

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

End of Document