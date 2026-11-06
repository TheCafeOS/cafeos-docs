# CafeOS

# Backend Guide

Version: 1.0

Owner: Mayank

Tech Stack

- Node.js
- Express.js
- TypeScript
- Prisma ORM
- PostgreSQL
- Socket.IO
- JWT
- Cloudinary

---

> **Current implementation note (2026-11-06):** This guide retains the
> original sprint plan for historical context. The **Current Backend
> Implementation** reference at the end describes the code currently in
> `cafeos-backend` and supersedes any conflicting planned routes or behavior
> above.

# Purpose

This document is your daily roadmap while building the CafeOS backend.

Whenever you're confused about what to build next,
open this document.

---

# Backend Responsibilities

Backend is responsible for

- Authentication
- Database
- Business Logic
- APIs
- File Uploads
- Socket.IO
- Security

The backend is the single source of truth.

Never trust frontend data.

---

# Backend Folder Structure

```

backend/

src/

config/

controllers/

middleware/

routes/

services/

prisma/

socket/

utils/

types/

app.ts

server.ts

```

---

# Backend Modules

Build modules in this order.

```

Authentication

Restaurant

Restaurant Table

Category

Menu

Order

Socket.IO

```

Do NOT build AI yet.

---

# Sprint 1

Goal

Backend starts successfully.

Tasks

- Setup Express
- Setup TypeScript
- Setup Prisma
- Connect PostgreSQL
- Configure Environment Variables
- Create Folder Structure

Deliverable

```

GET /

```

returns

```

CafeOS API Running

```

---

# Sprint 2

## Authentication

Authentication is done using **Employee**.

Restaurant never logs in.

Employee logs in.

Current MVP

Restaurant

↓

Owner Employee

↓

JWT

Future

Restaurant

↓

Owner

Manager

Chef

Cashier

Waiter

No architecture changes required.

---

Build

- Employee Login
- JWT
- Password Hashing
- Auth Middleware

API

```

POST /auth/login

```

---

# Sprint 3

## Restaurant Tables

Every physical table should exist in the database.

Example

```

Table 1

Table 2

Outdoor A

VIP 1

```

Each table has

- Name
- QR Code
- Status

Routes

```

GET /tables

POST /tables

PUT /tables/:id

DELETE /tables/:id

```

Restaurant owner should be able to

- Create Table
- Rename Table
- Delete Table
- Generate QR

---

# Sprint 4

## Categories

Routes

```

GET /categories

POST /categories

PUT /categories/:id

DELETE /categories/:id

```

---

# Sprint 5

## Menu

Routes

```

GET /tables/:tableId/menu

POST /menu

PUT /menu/:id

DELETE /menu/:id

```

Customer never sends restaurantId.

Customer only scans QR.

Backend finds

Table

↓

Restaurant

↓

Menu

Automatically.

---

# Sprint 6

## Orders

Models

- Order
- OrderItem

Routes

```

POST /orders

GET /orders

GET /orders/:id

PATCH /orders/:id/status

```

Order Request

```

Customer Name

(Optional) Phone

Table ID

Items

```

Backend should

- Validate menu items
- Calculate total
- Generate Order Number
- Save Order

---

Status

```

PENDING

ACCEPTED

PREPARING

READY

COMPLETED

CANCELLED

```

---

# Sprint 7

## Socket.IO

Events

```

ORDER_CREATED

ORDER_UPDATED

```

Flow

Customer places order

↓

Restaurant Dashboard receives event

↓

Restaurant updates status

↓

Customer receives event

No page refresh.

---

# Controllers

Controllers should only

- Read Request
- Call Service
- Return Response

No business logic.

---

# Services

Services contain

- Business Rules
- Validation
- Calculations

Examples

- Calculate Order Total
- Validate Table
- Validate Menu Item
- Update Status

---

# Prisma

Prisma is only responsible for

- Create
- Read
- Update
- Delete

Never put business logic inside Prisma queries.

---

# Response Format

Every API returns

Success

```json
{
  "success": true,
  "message": "Operation successful.",
  "data": {}
}
```

Failure

```json
{
  "success": false,
  "message": "Something went wrong."
}
```

---

# Middleware

Create

```

auth.middleware.ts

```

Responsibilities

- Verify JWT
- Identify Employee
- Identify Restaurant
- Reject Unauthorized Requests

---

# Environment Variables

```

PORT=

DATABASE_URL=

JWT_SECRET=

CLOUDINARY_CLOUD_NAME=

CLOUDINARY_API_KEY=

CLOUDINARY_API_SECRET=

```

Never commit `.env`.

---

# Validation Rules

Always validate

Menu

- Name required
- Price > 0

Category

- Name required

Order

- At least one item
- Valid table
- Valid menu item

Authentication

- Email required
- Password required

Never trust frontend.

---

# Image Upload Flow

Restaurant

↓

Uploads Image

↓

Backend uploads to Cloudinary

↓

Cloudinary returns URL

↓

Save URL in PostgreSQL

---

# Complete Backend Flow

Employee Login

↓

Restaurant Dashboard

↓

Create Tables

↓

Generate QR

↓

Create Categories

↓

Create Menu

↓

Customer Scans QR

↓

Backend Finds Table

↓

Returns Menu

↓

Customer Places Order

↓

Restaurant Receives Order

↓

Updates Status

↓

Socket Event

↓

Customer Gets Live Update

---

# Things NOT to Build

Do NOT build

- AI
- Loyalty
- Payments
- Reviews
- Coupons
- Reservations
- Analytics
- Inventory

Focus only on MVP.

---

# Backend Checklist

- Express Setup
- TypeScript
- Prisma
- PostgreSQL
- JWT Authentication
- Restaurant Tables
- Categories
- Menu CRUD
- Orders
- Socket.IO
- Cloudinary Upload
- Deployment

---

# Definition of Done

A backend feature is complete when

- API works
- Validation added
- Errors handled
- Database updated
- Tested in Postman
- Frontend successfully integrated

---

# Daily Rule

Finish ONE feature before starting another.

Example

✅ Restaurant Tables

then

✅ Categories

then

✅ Menu

Do not work on multiple unfinished features.

---

---

# Current Backend Implementation

## Architecture and source layout

The runtime is Express + TypeScript with the simple route → controller →
service → Prisma flow. Controllers adapt HTTP requests/responses; business
rules and database operations live in services. Current source modules:

| Directory/file | Responsibility |
|---|---|
| `src/app.ts`, `src/server.ts` | Express setup, route mounting, Socket.IO/server lifecycle and health endpoint. |
| `src/config/` | Validated environment configuration and Cloudinary client. |
| `src/constants/` | Audit and Socket.IO event/room names. |
| `src/routes/` | Auth, tables, categories, menu, orders, public, dashboard, settings, employees, audit logs, loyalty, notifications, and offers. |
| `src/controllers/` | HTTP handlers for those route groups. |
| `src/services/` | Authentication/session, order, table/merge, menu, category, settings, employee, dashboard, public, notification, offer, loyalty, audit, and Cloudinary operations. |
| `src/middleware/` | Session-aware authentication, role authorization, validation, upload, request ID, rate limiting, and error handling. |
| `src/validations/` | Zod request schemas for route inputs. |
| `src/docs/` | Swagger/OpenAPI schemas, common responses, and generated specification. |
| `src/lib/` | Prisma client, structured logger, Socket.IO session-revocation event bus. |
| `src/utils/` | JWT/opaque-token utilities, API response helpers, errors, pagination, request parameters, customer sessions, and order mapping/status rules. |

`app.ts` mounts API routers below `/api/v1`; `/health` is outside that prefix
and `/api-docs` serves Swagger UI. The route layer uses `validate`,
`asyncHandler`, `requireAuth`, and `requireRole` according to each operation.

## Authentication and authorization

- Login and registration validate their body, verify or create the employee,
  then transactionally create an `AuthSession` and its initial refresh-token
  record. Registration returns the newly created owner and restaurant.
- Access JWTs expire after 15 minutes and carry employee (`sub`), restaurant,
  role, and session (`sid`) claims. `requireAuth` verifies the JWT, finds the
  matching unrevoked, unexpired server session for that employee, and rejects
  deleted/inactive employees. Role checks run afterward with `requireRole`.
- Refresh tokens are opaque 32-byte cryptographic random values. Only their
  SHA-256 hash is persisted. Their lifetime is seven days; successful rotation
  consumes the old token and creates a replacement in one transaction while
  extending the session expiry. Presenting a consumed token triggers session
  revocation after the detection transaction completes and returns 401.
- Refresh uses a conditional update for the unused token. Concurrent requests
  cannot both rotate it successfully; the losing request is classified as
  reuse and revokes that session. Clients must coordinate refreshes rather
  than rely on simultaneous refresh requests being harmless.
- `POST /api/v1/auth/logout` revokes the current session. `logout-all` revokes
  every employee session. Session listing exposes only ID and timestamps plus
  a `current` flag. Individual revocation is employee-scoped. Password changes
  update the hash and revoke all sessions transactionally.
- There is no legacy stateless-refresh or access-token bypass. Existing access
  tokens without `sid` and old refresh JWTs without server-side token records
  are rejected; users must authenticate again after a rollout to this design.

## Input, errors, and HTTP logging

Zod schemas validate request bodies, path parameters, and query parameters.
Validation failures and service errors are normalized by the error handler
into `{ success: false, message, requestId }`; unexpected failures return
HTTP 500 with a generic message. Success responses use
`{ success: true, message, data?, pagination? }`. Pagination metadata includes
page, limit, total items/pages, and next/previous-page flags.

The app applies Helmet, allow-listed credentialed CORS, API/auth/public-order
rate limiters, bounded JSON/urlencoded bodies, request IDs, and Pino HTTP
logging. The logger redacts authorization/cookie/password/token fields and
the request serializer omits request bodies and query strings. Route paths are
sanitized for logs. Never log access tokens, refresh tokens, token hashes, or
passwords.

Image middleware receives uploads in memory; `cloudinary.service.ts` uploads
buffers and returns a secure URL and public ID, while persisted records keep
the URL/public ID needed for later replacement or deletion. Do not store image
bytes in PostgreSQL.

## Socket.IO authentication, rooms, and revocation

Socket.IO is initialized by `server.ts` before the HTTP listener starts.
Authenticated handshakes pass the current access token through Socket.IO
`auth.token`; the server verifies signature, `sid`, JWT expiration, active
employee, active session, employee binding, and restaurant binding. A
connection rechecks the database session before joining authenticated rooms.
The access-token expiry timer disconnects authenticated sockets when that
15-minute token expires; reconnecting requires a current valid token.

Room names are generated server-side:

- `restaurant_<restaurantId>` for restaurant-scoped broadcasts
- `employee_<employeeId>` and `session_<sessionId>` for authenticated users
- `table_<tableId>` for a QR-validated table connection

The current event constants are `ORDER_CREATED`, `ORDER_UPDATED`,
`NOTIFICATION_CREATED`, `TABLES_MERGED`, and `TABLES_UNMERGED`. An anonymous
customer connection may join a table only through `join_qr` with a QR token
resolved by the backend; arbitrary table-ID joins are not accepted.

Session/password revocations publish an in-process event. With the official
Socket.IO Redis adapter installed, `io.in(room).disconnectSockets(true)` is
broadcast to adapter-connected instances. Redis configuration is optional:

- With `SOCKET_IO_REDIS_URL` unset, the server logs a single-instance-only
  warning. This is not a multi-instance-safe revocation configuration.
- To run multiple backend instances, configure a valid `redis:` or `rediss:`
  `SOCKET_IO_REDIS_URL` on **every** instance and verify connectivity and
  cross-instance disconnect behavior before scaling. The app does not prove
  that any deployment currently has Redis configured.
- If Redis is configured but cannot initialize, startup fails before the HTTP
  server begins listening; it does not silently fall back to local-only mode.
- Redis clients are closed during graceful shutdown. Runtime Redis outages
  should be monitored; sockets also have a bounded lifetime at access-token
  expiry, but that is not a substitute for configured shared revocation.

## Startup and shutdown

`server.ts` initializes Socket.IO and its configured adapter, then starts the
HTTP listener outside test mode. Outside test mode it also runs the expired
pending-order cancellation task immediately and every 60 seconds. Adapter
startup errors are logged without printing connection details and set a
failing process exit state. On SIGINT or SIGTERM, the server closes the HTTP
listener, Socket.IO, Redis clients, and Prisma. The health endpoint performs a
database query and reports a 503 if the database is unavailable.

The validated configuration reads these variable names: `NODE_ENV`, `PORT`,
`DATABASE_URL`, `JWT_SECRET`, `JWT_REFRESH_SECRET`, `CORS_ORIGINS`,
`PUBLIC_APP_URL`, `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`,
`CLOUDINARY_API_SECRET`, and `ALLOW_PUBLIC_REGISTRATION`. `SOCKET_IO_REDIS_URL`
is optional for single-instance mode and required on every instance intended
to participate in cross-instance socket revocation. Keep values and
credentials in deployment secret configuration; do not put them in
documentation or source control.

## Validation snapshot

This is a dated development snapshot, not a guarantee of production readiness:

- Focused backend auth/session/socket/environment tests: 37 passed.
- Full backend suite: 72 passed, 1 failed. The remaining known baseline failure
  is `loyalty-redemption-concurrency.test.ts`: expected `[200, 409]`, received
  `[200, 404]`; it is unrelated to authentication.
- Backend TypeScript build passed.
- The Redis dependency installation reported 25 npm audit findings (9
  moderate, 13 high, 3 critical); those findings require separate review.
- Deployment Redis configuration and real multi-instance behavior were not
  verified here.

---

End of Document