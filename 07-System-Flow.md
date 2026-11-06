# CafeOS

# System Flow

Version: 1.0

Status: Active

---

> **Current flow note (2026-11-06):** The original MVP arrows below are
> historical sketches. The **Verified Current Flows** section at the end is
> authoritative for the implemented routes, tokens, events, and deployment
> behavior.

# Purpose

This document explains how every major part of CafeOS works together.

Read this before implementing any feature.

---

# System Overview

CafeOS has two users.

1. Restaurant Staff
2. Customer

Restaurant Staff manages the restaurant.

Customer places orders.

Both communicate through the backend.

---

# High Level Flow

Restaurant

↓

Creates Tables

↓

Creates Categories

↓

Creates Menu

↓

Prints QR Codes

↓

Customer Scans QR

↓

Menu Opens

↓

Customer Places Order

↓

Restaurant Receives Order

↓

Restaurant Updates Status

↓

Customer Receives Live Updates

---

# Restaurant Registration Flow

Restaurant visits

/register

↓

Fills Restaurant Details

↓

Fills Owner Details

↓

Backend creates

Restaurant

↓

Employee

↓

JWT not generated yet

↓

Redirect to Login

---

# Login Flow

Owner enters

Email

Password

↓

Backend verifies credentials

↓

JWT generated

↓

Frontend stores JWT

↓

Dashboard opens

---

# Dashboard Flow

Dashboard

│

├── Tables

├── Categories

├── Menu

└── Orders

Everything is managed from here.

---

# Restaurant Table Flow

Owner

↓

Creates Table

↓

Backend saves table

↓

QR generated

↓

Restaurant prints QR

↓

Places QR on table

---

# Customer Flow

Customer sits at table

↓

Scans QR

↓

Frontend extracts tableId

↓

Calls

GET /tables/:tableId/menu

↓

Backend finds restaurant

↓

Returns menu

↓

Customer starts ordering

---

# Menu Flow

Restaurant

↓

Creates Category

↓

Creates Menu Item

↓

Backend stores menu

↓

Customer automatically sees updated menu

No frontend changes required.

---

# Order Flow

Customer

↓

Adds Items

↓

Cart

↓

Enters Name

↓

(Optional) Phone Number

↓

Place Order

↓

POST /orders

↓

Backend validates order

↓

Calculates total

↓

Creates order

↓

ORDER_CREATED event

↓

Restaurant Dashboard receives order

---

# Kitchen Flow

Restaurant receives order

↓

Accept

↓

Preparing

↓

Ready

↓

Served

Every status update triggers

ORDER_UPDATED

Socket event

---

# Live Update Flow

Restaurant changes status

↓

Backend updates database

↓

Socket.IO emits ORDER_UPDATED

↓

Customer screen updates instantly

No refresh required.

---

# Authentication Flow

Employee Login

↓

JWT

↓

Frontend stores token

↓

Protected APIs

↓

Backend verifies JWT

↓

Request allowed

---

# Image Upload Flow

Restaurant

↓

Uploads image

↓

Backend uploads to Cloudinary

↓

Cloudinary returns URL

↓

Database stores URL

↓

Frontend displays image

---

# Error Flow

User sends request

↓

Backend validates request

↓

Success

↓

Return data

OR

Validation fails

↓

Return error response

Frontend displays friendly message

---

# Customer Journey

Restaurant

↓

Sit at Table

↓

Scan QR

↓

Browse Menu

↓

Add to Cart

↓

Place Order

↓

Track Order

↓

Receive Food

Journey complete.

---

# Restaurant Journey

Register

↓

Login

↓

Create Tables

↓

Generate QR

↓

Create Categories

↓

Create Menu

↓

Receive Orders

↓

Update Status

↓

Serve Customer

Journey complete.

---

# Backend Responsibilities

- Authentication
- Database
- Business Logic
- Validation
- Socket.IO
- Cloudinary

Backend never renders UI.

---

# Frontend Responsibilities

- UI
- Forms
- API Calls
- Socket.IO Client
- Loading States
- Error Messages

Frontend never talks directly to PostgreSQL.

---

# Socket Events

Restaurant receives

ORDER_CREATED

Customer receives

ORDER_UPDATED

Only these two events are required for MVP.

---

# Complete MVP Flow

Restaurant Registers

↓

Restaurant Logs In

↓

Creates Tables

↓

Prints QR

↓

Creates Categories

↓

Creates Menu

↓

Customer Scans QR

↓

Menu Opens

↓

Customer Orders

↓

Restaurant Receives Order

↓

Restaurant Updates Status

↓

Customer Receives Live Updates

↓

Order Served

MVP Complete.

---

# Development Rule

Whenever implementing a feature,

first identify where it belongs.

Restaurant Flow?

Customer Flow?

Backend?

Frontend?

Never build a feature without knowing its place in the overall system.

---

---

# Verified Current Flows

## Authentication lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Employee
    participant Web as Frontend
    participant API as Auth API
    participant DB as PostgreSQL
    Employee->>Web: Submit registration or login
    Web->>API: POST /auth/register or /auth/login
    API->>DB: Verify/create employee; create AuthSession
    API->>DB: Store SHA-256 hash of initial opaque refresh token
    API-->>Web: 15-minute access JWT (sid) + 7-day refresh token
    Web->>Web: Persist both tokens and employee identity
    Note over Web: On expiry/401, coordinate refresh across tabs
    Web->>API: POST /auth/refresh with current refresh token
    API->>DB: Transaction: conditionally consume old token; create replacement; extend session
    API-->>Web: New access JWT + replacement refresh token
    Note over API,DB: Reuse of a consumed token revokes its session after the detection transaction commits
    Employee->>API: POST /auth/logout or DELETE /auth/sessions/:sessionId
    API->>DB: Set revokedAt; publish session revocation
```

Registration creates the session immediately; it does not redirect to a
separate login requirement. Access-token middleware checks the session and
employee state on each protected HTTP request. Refresh is single-use; a
concurrent loser can be detected as reuse and revoke the session.

## Cross-tab refresh coordination

```mermaid
sequenceDiagram
    participant TabA as Tab A
    participant TabB as Tab B
    participant Lock as Web Locks API
    participant Store as Shared localStorage
    participant API as Backend
    TabA->>Lock: Request cafeos-auth-refresh
    TabB->>Lock: Request same lock; wait
    Lock-->>TabA: Grant
    TabA->>Store: Re-read identity, generation and refresh token
    TabA->>API: POST /auth/refresh (only if token still current)
    API-->>TabA: New access + refresh tokens
    TabA->>Store: Persist pair if captured auth snapshot still matches
    TabA-->>Lock: Release
    Lock-->>TabB: Grant
    TabB->>Store: Re-read token pair
    Note over TabB,Store: If the refresh token changed, reuse the new access token; do not call refresh again
```

The lock is also used to serialize auth-state mutations. If Web Locks are
unavailable, the refresh coordinator fails closed and current auth state is
cleared rather than issuing an uncoordinated refresh. Identity/generation
checks prevent late responses from overwriting logout or a subsequent login.

## Customer QR ordering and live order updates

```mermaid
sequenceDiagram
    actor Customer
    participant Web as Customer UI
    participant API as CafeOS API
    participant DB as PostgreSQL
    participant Socket as Socket.IO
    participant Dash as Restaurant dashboard
    Customer->>Web: Scan table QR (/menu/:qrToken)
    Web->>API: GET /api/v1/public/menu/:qrToken
    API->>DB: Resolve active table, restaurant and public menu
    API-->>Web: Table, restaurant, categories and available menu items
    Customer->>Web: Submit cart
    Web->>API: POST /api/v1/public/orders/:qrToken
    Note over Web,API: HttpOnly customer-session cookie; optional Idempotency-Key
    API->>DB: Validate items and persist order
    API-->>Web: 201 order summary (200 for idempotent replay)
    API->>Socket: Emit ORDER_CREATED to restaurant and table rooms
    Socket-->>Dash: ORDER_CREATED
    Dash->>API: PATCH /api/v1/orders/:id/status
    API->>DB: Validate status transition and update order
    API->>Socket: Emit ORDER_UPDATED to restaurant and table rooms
    Socket-->>Web: ORDER_UPDATED
    Web->>API: GET active/public order state as needed
```

The server derives restaurant/table ownership from the QR token; public
clients do not submit an authoritative restaurant ID. Order event payloads
omit `customerPhone`.

## Socket.IO authentication and revocation

```mermaid
sequenceDiagram
    participant Client as Browser socket
    participant InstanceA as Backend instance A
    participant DB as PostgreSQL
    participant Redis as Redis adapter (optional)
    participant InstanceB as Backend instance B
    Client->>InstanceA: Connect with auth.token access JWT
    InstanceA->>InstanceA: Verify signature, sid and exp
    InstanceA->>DB: Verify employee/session active and restaurant binding
    InstanceA-->>Client: Accept; join session, employee, restaurant rooms
    Note over InstanceA,Client: Disconnect at JWT expiry; reconnect needs a current token
    Client->>InstanceA: join_qr with QR token (customer/table room)
    InstanceA->>DB: Resolve token to active table
    InstanceA-->>Client: Join validated table room
    Note over InstanceA,DB: Logout, logout-all, password change or session revoke updates session state
    InstanceA->>Redis: Adapter broadcasts disconnectSockets for target room (when configured)
    Redis->>InstanceB: Propagate room disconnect to other instances
    InstanceB-->>Client: Disconnect any socket hosted on instance B
```

Without `SOCKET_IO_REDIS_URL`, only the local Socket.IO process receives the
revocation operation; multi-instance deployments require Redis configured and
verified on every instance. Redis is optional in code, and this documentation
does not assert that it is configured in any current deployment. If the URL is
configured but adapter initialization fails, server startup fails instead of
silently switching to local-only behavior.

---

End of Document