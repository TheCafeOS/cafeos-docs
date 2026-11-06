# CafeOS

# API Documentation

Version: 1.0

Status: Active

Base URL

/api/v1

Content-Type

application/json

---

> **Current contract note (2026-11-06):** The MVP endpoint outline below is
> retained for history. The **Verified Current HTTP API Reference** at the end
> was cross-checked against the mounted Express routes, middleware,
> controllers, validation schemas, services, and Swagger definitions. It is
> authoritative where the two sections differ.

# Purpose

This document is the API contract between the Frontend and Backend.

Frontend should build according to these endpoints.

Backend should never change response structures without discussion.

---

# Standard Response Format

Every API returns the following structure.

Success

```json
{
  "success": true,
  "message": "Operation successful.",
  "data": {}
}
```

Error

```json
{
  "success": false,
  "message": "Something went wrong."
}
```

---

# Authentication

## Register Restaurant

POST

```
/auth/register
```

Request

```json
{
  "restaurantName": "<restaurant-name>",
  "restaurantEmail": "<restaurant-email>",
  "restaurantPhone": "<restaurant-phone>",
  "address": "<restaurant-address>",

  "ownerName": "<owner-name>",
  "ownerEmail": "<owner-email>",
  "password": "<password-meeting-policy>"
}
```

Response

```json
{
  "success": true,
  "message": "Restaurant registered successfully.",
  "data": {
    "token": "<access-token>",
    "accessToken": "<access-token>",
    "refreshToken": "<opaque-refresh-token>",
    "employee": {},
    "restaurant": {}
  }
}
```

Status Codes

```
201
400
409
```

---

## Login

POST

```
/auth/login
```

Request

```json
{
  "email": "<employee-email>",
  "password": "<employee-password>"
}
```

Response

```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "token": "<access-token>",
    "accessToken": "<access-token>",
    "refreshToken": "<opaque-refresh-token>",
    "employee": {}
  }
}
```

Status Codes

```
200
401
```

---

# Restaurant Tables

## Get Tables

GET

```
/tables
```

Response

```json
{
  "success": true,
  "data": [
    {
      "id": "<table-id>",
      "tableName": "Table 1",
      "isActive": true
    }
  ]
}
```

---

## Create Table

POST

```
/tables
```

Request

```json
{
  "tableName": "Table 5"
}
```

Response

```json
{
  "success": true,
  "message": "Table created successfully."
}
```

---

## Update Table

PUT

```
/tables/:id
```

Request

```json
{
  "tableName": "Outdoor A"
}
```

---

## Delete Table

DELETE

```
/tables/:id
```

Response

```json
{
  "success": true,
  "message": "Table deleted successfully."
}
```

---

# Categories

## Get Categories

GET

```
/categories
```

Response

```json
{
  "success": true,
  "data": [
    {
      "id": "<category-id>",
      "name": "<category-name>"
    },
    {
      "id": "<category-id>",
      "name": "<category-name>"
    }
  ]
}
```

---

## Create Category

POST

```
/categories
```

Request

```json
{
  "name": "Desserts"
}
```

---

## Update Category

PUT

```
/categories/:id
```

Request

```json
{
  "name": "Beverages"
}
```

---

## Delete Category

DELETE

```
/categories/:id
```

---

# Menu

## Get Menu (Customer)

GET

```
/public/menu/:qrToken
```

Description

Returns the menu for the restaurant associated with the scanned table.

Response

```json
{
  "success": true,
  "data": [
    {
      "id": "<menu-item-id>",
      "name": "<menu-item-name>",
      "description": "<menu-item-description>",
      "price": 1.00,
      "image": "<image-url>",
      "category": "<category-name>",
      "isAvailable": true
    }
  ]
}
```

---

## Create Menu Item

POST

```
/menu
```

Request

```json
{
  "name": "<menu-item-name>",
  "description": "<menu-item-description>",
  "price": 1.00,
  "categoryId": "<category-id>",
  "image": "<image-url>"
}
```

---

## Update Menu Item

PUT

```
/menu/:id
```

Request

```json
{
  "name": "<menu-item-name>",
  "price": 1.00,
  "isAvailable": true
}
```

---

## Delete Menu Item

DELETE

```
/menu/:id
```

---

# Orders

## Place Order

POST

```
/public/orders/:qrToken
```

Request

```json
{
  "customerPhone": "<customer-phone>",
  "items": [
    {
      "menuItemId": "<menu-item-id>",
      "quantity": 1
    },
    {
      "menuItemId": "<menu-item-id>",
      "quantity": 1
    }
  ]
}
```

The table is derived from `qrToken`; `customerPhone` is optional. An
`Idempotency-Key` header may be supplied for retry deduplication.
When that header is supplied, the response is a stable creation snapshot; the
same key and payload return that snapshot with HTTP 200. Without the header,
the new-order response uses the full order mapper shape (including numeric
totals, a `{id, name}` table object, and item/menu-item details).

---

Response

```json
{
  "success": true,
  "message": "Order created successfully",
  "data": {
    "id": "<order-id>",
    "status": "PENDING",
    "total": "<decimal-string>",
    "table": "<table-name>",
    "createdAt": "<ISO-8601-timestamp>"
  }
}
```

Status Codes

```
200 (idempotent replay)
201
400
404
```

---

## Get Restaurant Orders

GET

```
/orders
```

Response

```json
{
  "success": true,
  "data": [
    {
      "id": "<order-id>",
      "customerPhone": "<customer-phone>",
      "table": {
        "id": "<table-id>",
        "name": "<table-name>"
      },
      "subtotal": 1.00,
      "discountAmount": 0.00,
      "total": 1.00,
      "status": "PREPARING"
    }
  ]
}
```

---

## Get Single Order

GET

```
/orders/:id
```

Response

```json
{
  "success": true,
  "data": {
    "id": "<order-id>",
    "customerPhone": "<customer-phone>",
    "subtotal": 1.00,
    "discountAmount": 0.00,
    "total": 1.00,
    "table": {
      "id": "<table-id>",
      "name": "<table-name>"
    },
    "status": "PREPARING",
    "items": [
      {
        "id": "<order-item-id>",
        "quantity": 1,
        "price": 1.00,
        "menuItem": {
          "id": "<menu-item-id>",
          "name": "<menu-item-name>",
          "imageUrl": "<image-url-or-null>"
        }
      }
    ]
  }
}
```

---

## Update Order Status

PATCH

```
/orders/:id/status
```

Request

```json
{
  "status": "COMPLETED"
}
```

Allowed Values

```
PENDING
CONFIRMED
PREPARING
COMPLETED
CANCELLED
```

Response

```json
{
  "success": true,
  "message": "Order status updated successfully."
}
```

---

# Socket.IO Events

## Restaurant Dashboard

Listens for

```
ORDER_CREATED
```

Payload

```json
{
  "orderId": "<order-id>",
  "tableId": "<table-id>",
  "tableName": "<table-name>",
  "status": "<order-status>"
}
```

---

## Customer

Listens for

```
ORDER_UPDATED
```

Payload

```json
{
  "orderId": "<order-id>",
  "status": "<order-status>"
}
```

---

# Protected APIs

Authentication Required

```
POST /tables
PATCH /tables/:id
DELETE /tables/:id

POST /categories
PATCH /categories/:id
DELETE /categories/:id

POST /menu
PATCH /menu/:id
DELETE /menu/:id

POST /orders
GET /orders
GET /orders/:id
PATCH /orders/:id/status
```

---

# Public APIs

```
POST /auth/register
POST /auth/login
POST /auth/refresh
GET /public/menu/:qrToken
POST /public/orders/:qrToken
GET /public/orders/:qrToken/active
GET /public/orders/:qrToken/:orderId
```

---

# HTTP Status Codes

```
200 OK

201 Created

400 Bad Request

401 Unauthorized

403 Forbidden

404 Not Found

409 Conflict

429 Too Many Requests

500 Internal Server Error
```

---

# API Development Order

Implement APIs in this sequence.

1.

POST /auth/register

↓

2.

POST /auth/login

↓

3.

Restaurant Tables CRUD

↓

4.

Category CRUD

↓

5.

Menu CRUD

↓

6.

GET /public/menu/:qrToken

↓

7.

POST /orders

↓

8.

GET /orders

↓

9.

PATCH /orders/:id/status

↓

10.

Socket.IO

---

# API Rules

- Use consistent response format.
- Always validate input.
- Never expose database schema directly.
- Return appropriate HTTP status codes.
- Do not change field names without discussion.
- Backend owns business logic.
- Frontend only consumes APIs.

---

---

# Verified Current HTTP API Reference

Base path: `/api/v1`. Unless an operation returns binary content or HTTP 204,
successful JSON responses use:

```json
{
  "success": true,
  "message": "Operation-specific message",
  "data": {},
  "pagination": {}
}
```

`data` is omitted when the controller does not provide it. `pagination` is
present only for paginated list endpoints and contains `page`, `limit`,
`totalItems`, `totalPages`, `hasNextPage`, and `hasPreviousPage`. Errors use
`{ "success": false, "message": "...", "requestId": "..." }`.

Protected endpoints require `Authorization: Bearer <access-token>`. Tokens
must carry a valid session ID; the server checks employee/session status on
each protected request. Resource queries are scoped to the authenticated
employee’s restaurant or employee account. Unless specified, common errors
are 400 (validation), 401 (missing/invalid/expired/revoked session), 403
(role/ownership denied), 404 (resource not found), 409 (conflict), 429 (rate
limit), and 500 (unexpected error); exact statuses vary by operation.

## Authentication

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `POST /auth/register` | Public; registration may be disabled | JSON: `restaurantName`, `restaurantEmail`, `restaurantPhone`, `address`, `ownerName`, `ownerEmail`, `password`. Password: 12–128 chars, upper/lower/number/special. | 201; `data` contains `token` (access-token compatibility alias), `accessToken`, `refreshToken`, `employee`, and `restaurant` (including the filtered owner employee relation). Errors include 400, 403, 409, 500. |
| `POST /auth/login` | Public; rate-limited | JSON: `email`, `password`. | 200; `data` contains `token`, `accessToken`, `refreshToken`, and employee `{id, restaurantId, email, role}`. Invalid credentials 401; deactivated account 403. |
| `POST /auth/refresh` | Public; rate-limited | JSON: `{ "refreshToken": "<opaque-refresh-token>" }`. | 200; `data` contains compatibility `token`, new `accessToken`, and replacement `refreshToken`. The presented token is single-use; replay revokes its session and returns 401. |
| `POST /auth/logout` | Authenticated employee; current session only | Empty JSON body | 200; success envelope, no token data. Idempotently revokes current session. |
| `POST /auth/logout-all` | Authenticated employee; all sessions of that employee | Empty JSON body | 200; `data: { "revokedCount": <number> }`. |
| `GET /auth/sessions` | Authenticated employee; own sessions only | None | 200; `data` array of `{id, createdAt, lastUsedAt, expiresAt, current}`. Never includes raw tokens or token hashes. |
| `DELETE /auth/sessions/:sessionId` | Authenticated employee; own session only | UUID path parameter | 200; success envelope. 404 if the session does not belong to that employee or does not exist. |
| `PATCH /auth/change-password` | Authenticated employee | JSON: `currentPassword`, `newPassword` (same password policy as registration). | 200; success envelope. Current password mismatch 401. On success all employee sessions are revoked. |

Access JWT lifetime is 15 minutes. Opaque refresh tokens have a seven-day
rolling lifetime. See [Authentication lifecycle](./07-System-Flow.md#authentication-lifecycle)
for rotation and replay behavior.

## Tables and table merges

All table-management routes require an authenticated employee scoped to their
restaurant. Create/update/merge require OWNER or MANAGER; delete requires
OWNER. Listing, QR download, and merge operations are guarded by auth; merge
routes require OWNER or MANAGER.

| Method and path | Request | Success response |
|---|---|---|
| `GET /tables` | None | 200; `data` array of restaurant tables. |
| `POST /tables` | JSON `{ "name": "<table-name>" }` (1–50 chars). | 201; `data` created table. |
| `PATCH /tables/:id` | CUID path; JSON optional `name` and/or `status` (`AVAILABLE`, `OCCUPIED`, `RESERVED`, `INACTIVE`). | 200; `data` updated table. |
| `DELETE /tables/:id` | CUID path | 204; no body. |
| `GET /tables/:id/qr` | CUID path | 200 `image/png` attachment, not a JSON envelope. |
| `GET /tables/merges` | None | 200; `data` array of merges in the employee’s restaurant. |
| `POST /tables/merges` | JSON `{ "tableIds": ["<cuid>", "<cuid>"] }`; 2–50 IDs. | 201; `data` created merge. 409 for conflicting active merge. |
| `GET /tables/merges/:mergeId` | CUID path | 200; `data` merge details; 404 if not found in restaurant. |
| `POST /tables/merges/:mergeId/unmerge` | CUID path; empty body | 200; `data` updated merge. |

## Categories and menu

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `GET /categories` | Authenticated; restaurant-scoped | Optional query: `page`, `limit`, `search`, `sort` (`name`, `createdAt`), `order` (`asc`, `desc`). | 200; `data` categories array and `pagination`. |
| `POST /categories` | OWNER or MANAGER | JSON `{ "name": "<name>" }` (1–50 chars). | 201; `data` category. |
| `PATCH /categories/:id` | OWNER or MANAGER | CUID path; JSON `{ "name": "<name>" }`. | 200; `data` category. |
| `DELETE /categories/:id` | OWNER | CUID path | 204; no body. |
| `GET /menu` | Authenticated; restaurant-scoped | Optional query: `page`, `limit`, `search`, `categoryId`, `isAvailable`, `sort` (`name`, `price`, `createdAt`), `order`. | 200; `data` menu-item array and `pagination`. |
| `POST /menu` | OWNER or MANAGER | JSON `name` (1–100), optional `description` (≤500), positive `price`, optional `categoryId`, `isAvailable`, `foodType` (`VEG`, `NON_VEG`, `EGG`), and image framing (`imageScale`, `imagePositionX`, `imagePositionY`). Image bytes are uploaded separately. | 201; `data` created menu item. |
| `PATCH /menu/:id` | OWNER or MANAGER | CUID path; any supported menu fields above may be updated. | 200; `data` menu item. |
| `POST /menu/:id/image` | OWNER or MANAGER | CUID path; multipart form field `image`. | 200; `data` updated menu item. |
| `DELETE /menu/:id` | OWNER | CUID path | 200; success envelope and `data: null` if deleted, or item data if retained as unavailable because prior orders reference it. |

## Public menu, ordering, and customer loyalty

These endpoints do not require employee bearer authentication. Public order
creation is rate-limited. Requests use the table’s QR token. A successful
public order creates/uses the HttpOnly `cafeos_customer_session` cookie;
`Idempotency-Key` is optional, but retry deduplication is guaranteed only when
the client supplies a stable key. The token/session values in these examples
are placeholders only.

| Method and path | Request | Success response |
|---|---|---|
| `GET /public/menu/:qrToken` | QR token path | 200; `data` contains `table: {id, name, status}`, restaurant display/contact fields, `categories: [{id, name}]`, available `menuItems`, and public `offers`. Inactive table 403; invalid QR 404. |
| `POST /public/orders/:qrToken` | QR token; optional `Idempotency-Key` header (1–255 chars); JSON optional nullable `customerPhone` and required nonempty `items: [{menuItemId, quantity}]` (positive integer quantity). | 201 for a new order or 200 for an idempotent replay. With a key, `data: {id, status, total: <decimal-string>, table: <table-name-or-null>, createdAt}` is the stable snapshot; without a key, `data` is the full order mapper response. Invalid/conflicting payload 400/409; inactive table 403; invalid QR 404. |
| `GET /public/orders/:qrToken/active` | QR token path; customer session cookie is used to scope active orders | 200; `data: {table: {id, name}, type: "NONE" \| "COMBINED" \| "SEPARATE", orderCount, combinedTotal, orders}`. `orders` include status, totals, offers, timestamps, and items. |
| `GET /public/orders/:qrToken/:orderId` | QR token and CUID order ID; customer session cookie | 200; `data` contains `id`, `status`, numeric subtotal/discount/total, applied offer or null, customer phone, timestamps, and item/menu-item details, scoped to the table/session; not found 404. |
| `GET /public/loyalty/program/:qrToken` | QR token path | 200; `data` active public loyalty program; unavailable/invalid QR 404. |
| `GET /public/loyalty/customer/:qrToken/:phone` | QR token and phone path | 200; `data` customer loyalty profile; missing/invalid profile 404. |

The authenticated staff order-creation API is distinct from public QR ordering:

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `POST /orders` | OWNER, MANAGER, STAFF; restaurant-scoped | JSON `tableId`, optional `customerPhone`, nonempty `items: [{menuItemId, quantity}]`. | 201; `data` created order. |
| `GET /orders` | OWNER, MANAGER, STAFF; restaurant-scoped | Optional query: `page` (≥1), `limit` (1–100), `status` (`PENDING`, `CONFIRMED`, `PREPARING`, `COMPLETED`, `CANCELLED`), `search`, `tableId`, `from`, `to`, `sort` (`createdAt`, `status`, `total`), `order` (`asc`, `desc`). `from` must not exceed `to`. | 200; `data` order array and `pagination`. |
| `GET /orders/:id` | OWNER, MANAGER, STAFF; restaurant-scoped | CUID order path | 200; `data` order details; 404 if not found. |
| `PATCH /orders/:id/status` | OWNER, MANAGER, STAFF; restaurant-scoped | CUID path; JSON `{ "status": "<OrderStatus>" }`. | 200; `data` updated order. Invalid transition 409. |

Current order statuses are `PENDING`, `CONFIRMED`, `PREPARING`, `COMPLETED`,
and `CANCELLED`. Valid transitions are PENDING → CONFIRMED/CANCELLED,
CONFIRMED → PREPARING/CANCELLED, and PREPARING → COMPLETED/CANCELLED.
`READY` and `SERVED` are not current API or database statuses.

## Dashboard and restaurant settings

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `GET /dashboard/today` | OWNER | None | 200; `data: {date, totalOrders, completedOrders, totalRevenue, avgOrderValue}`. |
| `GET /dashboard/orders/status` | OWNER | None | 200; `data` array of `{status, count}`. |
| `GET /dashboard/orders/active` | OWNER, MANAGER, STAFF | None | 200; `data` active order sessions. |
| `GET /dashboard/orders/recent` | OWNER | Optional `limit` (default 10, max 50). | 200; `data` array of `{id, tableId, tableName, status, total, itemCount, customerPhone, createdAt}`. |
| `GET /dashboard/summary` | OWNER, MANAGER | None | 200; `data: {today: {totalOrders, totalRevenue, date}, statusBreakdown: [{status, count}], recentOrders: [{id, tableName, status, total, createdAt}]}`. |
| `GET /settings` | Authenticated employee; restaurant-scoped | None | 200; `data` restaurant settings/branding. |
| `PATCH /settings` | OWNER | JSON restaurant name/email, optional contact/address, tagline, description, cuisine, links, theme color, and weekly opening hours. | 200; `data` updated settings. |
| `PATCH /settings/logo` | OWNER | Multipart `image` | 200; `data` updated settings. |
| `PATCH /settings/cover` | OWNER | Multipart `image` | 200; `data` updated settings. |

There is no standalone restaurant CRUD router. Registration creates a
restaurant; subsequent restaurant settings are served through `/settings`.

## Employees and audit logs

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `POST /employees` | OWNER; own restaurant | JSON `name` (2–100), `email`, `password` (12–128 with complexity), `role` (`MANAGER` or `STAFF`). | 201; `data` created employee (password hash is not returned). |
| `GET /employees` | OWNER; own restaurant | Query supports pagination, search, role, active state, sort (`name`, `createdAt`, `lastLoginAt`) and order. | 200; `data` array and `pagination`. |
| `GET /employees/:id` | OWNER; own restaurant | CUID path | 200; `data` employee; 404 if not found. |
| `PATCH /employees/:id` | OWNER; own restaurant | CUID path; optional `name` and/or `role` (`MANAGER`, `STAFF`). | 200; `data` employee. |
| `PATCH /employees/:id/status` | OWNER; own restaurant | CUID path; JSON `{ "isActive": true }` | 200; `data` employee. |
| `DELETE /employees/:id` | OWNER; own restaurant | CUID path | 200; success envelope and `data: null`; employee is soft-deleted. |
| `GET /audit-logs` | OWNER; own restaurant | Query `page` (default 1) and `limit` (default 20, max 100). | 200; `data` audit-log array and `pagination`. |

## Notifications

All notification routes require authentication and operate on the current
employee’s notifications. OWNER, MANAGER, and STAFF are accepted.

| Method and path | Request | Success response |
|---|---|---|
| `GET /notifications` | Query `page` (default 1), `limit` (default 20, max 100), optional `isRead` and `type` (`NEW_ORDER`, `ORDER_STATUS`, `LOYALTY_REWARD`, `EMPLOYEE_ACTIVITY`). | 200; `data` array and `pagination`. |
| `GET /notifications/unread-count` | None | 200; `data: {count: <integer>}`. |
| `PATCH /notifications/read-all` | Empty body | 200; `data: {count: <integer>}`. |
| `PATCH /notifications/:id/read` | CUID path | 200; `data` updated notification. |
| `DELETE /notifications/:id` | CUID path | 204; no body. |

## Offers

| Method and path | Access | Request | Success response |
|---|---|---|---|
| `GET /offers` | OWNER, MANAGER, STAFF; own restaurant | None | 200; `data` offers array. |
| `POST /offers` | OWNER or MANAGER | JSON `name`, optional `description`, `discountType` (`PERCENTAGE`/`FIXED`), positive `discountValue`, nonnegative `minimumOrderValue`, optional positive `maximumDiscount`, `isActive`, optional ISO `startsAt`/`endsAt`. | 201; `data` offer. |
| `GET /offers/:offerId` | OWNER, MANAGER, STAFF; own restaurant | CUID path | 200; `data` offer. |
| `PATCH /offers/:offerId` | OWNER or MANAGER | CUID path; offer body fields | 200; `data` offer. |
| `PATCH /offers/:offerId/status` | OWNER or MANAGER | CUID path; JSON `{ "isActive": true }` | 200; `data` offer. |
| `DELETE /offers/:offerId` | OWNER or MANAGER | CUID path | 200; success envelope and `data: null`. |

## Loyalty

Management endpoints are restricted to OWNER/MANAGER; read and redemption
operations accept OWNER/MANAGER/STAFF. All employee-facing loyalty data is
scoped to the authenticated restaurant.

| Method and path | Request | Success response |
|---|---|---|
| `PUT /loyalty/program` | JSON `rewardName`, positive integer `purchaseThreshold`/`rewardQuantity`, nonnegative `minimumOrderValue`, `isActive`. | 200; saved program in `data`. |
| `GET /loyalty/programs` | None | 200; `data` program list. |
| `POST /loyalty/programs` | Same program body | 201; `data` program. |
| `GET /loyalty/programs/:programId` | CUID path | 200; `data` program. |
| `PATCH /loyalty/programs/:programId` | CUID path; program body | 200; `data` program. |
| `PATCH /loyalty/programs/:programId/status` | CUID path; JSON `{ "isActive": true }` | 200; `data` program. |
| `DELETE /loyalty/programs/:programId` | CUID path | 200; success envelope with `data: null`. |
| `GET /loyalty/customers` | Query pagination, search, sort (`lastOrderAt`, `visitCount`, `totalSpend`, `createdAt`) and order. | 200; `data` customer list and `pagination`. |
| `GET /loyalty/customers/:phone` | Phone path | 200; `data` customer profile. |
| `POST /loyalty/customers/:customerId/rewards/:rewardId/redeem` | CUID customer and reward path parameters | 200; redeemed reward/service result in `data`; conflict/not-available errors are reported by service. |

## Socket.IO (not HTTP)

The frontend supplies an access token in the Socket.IO handshake `auth.token`.
Authenticated sockets join server-assigned session, employee, and restaurant
rooms; a customer may join a table room only by presenting its QR token through
`join_qr`. Current server event names are:

| Event | Direction/use |
|---|---|
| `ORDER_CREATED` | Server-to-dashboard order creation notification. |
| `ORDER_UPDATED` | Server-to-order subscribers after an order change. |
| `NOTIFICATION_CREATED` | Server-to-employee notification update. |
| `TABLES_MERGED`, `TABLES_UNMERGED` | Server-to-restaurant table state changes. |
| `join_qr`, `leave_table` | Client-to-server room membership requests; table membership is validated. |

Authenticated sockets are revalidated and disconnected when their access
token expires. Session revocation disconnects the corresponding session room;
employee-wide revocation disconnects employee-room sockets. Cross-instance
disconnect requires the optional Redis adapter to be configured and available
on every backend instance; without it, revocation broadcasts only within the
current process. See the [Backend Guide](./04-Backend-Guide.md#socketio-authentication-rooms-and-revocation).

## Swagger/OpenAPI

Swagger UI is served at `/api-docs`. Auth, core resources, and selected public
routes have route-level OpenAPI comments and shared schemas in the backend.
Some newer route families do not yet have equally detailed operation
annotations; this reference was checked against their Express route and
validation code and should not be mistaken for a claim that generated
OpenAPI fully describes every row above.

---

End of Document