# CafeOS

# Frontend Guide

Version: 1.0

Owner: Anshul

Tech Stack

- Next.js
- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- Axios
- Socket.IO Client

---

> **Current implementation note (2026-11-06):** The sprint plan below is
> historical. The **Current Frontend Implementation** reference at the end
> describes existing pages and behavior and supersedes any conflicting planned
> route or workflow.

# Purpose

This document is the roadmap for frontend development.

Whenever you're confused about what page to build next,
open this document.

Build pages in the given order.

---

# Frontend Responsibilities

Frontend is responsible for

- User Interface
- API Integration
- Authentication
- State Management
- Real-time Updates
- Responsive Design

Frontend NEVER talks directly to PostgreSQL.

Everything goes through Backend APIs.

---

# Folder Structure

```

src/

app/

components/

hooks/

services/

types/

utils/

styles/

```

---

# Sprint 1

Goal

Setup project.

Tasks

- Setup Next.js
- Setup Tailwind CSS
- Setup shadcn/ui
- Global Layout
- Navbar
- Landing Page

Deliverable

Landing Page running successfully.

---

# Sprint 2

## Restaurant Registration & Login

Pages

```

/register

/login

```

Restaurant Registration Form

Restaurant Details

- Restaurant Name
- Email
- Phone
- Address

Owner Details

- Owner Name
- Owner Email
- Password

After successful registration

↓

Redirect to Login

After Login

↓

Store JWT

↓

Redirect Dashboard

---

# Sprint 3

## Dashboard

Pages

```

/dashboard

```

Dashboard Cards

- Total Orders
- Today's Revenue
- Active Tables
- Menu Items

Navigation

Dashboard

Tables

Categories

Menu

Orders

Settings

---

# Sprint 4

## Restaurant Tables

Page

```

/dashboard/tables

```

Restaurant should be able to

- Create Table
- Rename Table
- Delete Table
- Generate QR Code
- Download QR

Each Table Card shows

- Table Name
- QR Code
- Active Status

---

# Sprint 5

## Categories

Page

```

/dashboard/categories

```

Restaurant should be able to

- Add Category
- Edit Category
- Delete Category

Example

Coffee

Pizza

Desserts

---

# Sprint 6

## Menu Management

Page

```

/dashboard/menu

```

Restaurant can

- Add Menu Item
- Edit Menu Item
- Delete Menu Item

Fields

- Image
- Name
- Description
- Price
- Category
- Available

---

# Sprint 7

## Customer Menu

Customer scans QR.

QR contains

```

tableId

```

Frontend extracts

```

tableId

```

Calls

```

GET /tables/:tableId/menu

```

Display

- Categories
- Menu Items
- Images
- Prices

Customer never selects restaurant.

Everything comes from QR.

---

# Sprint 8

## Shopping Cart

Features

- Add Item
- Remove Item
- Increase Quantity
- Decrease Quantity
- Total Price

Button

```

Place Order

```

Calls

```

POST /orders

```

Request

```

Customer Name

(Optional) Phone Number

tableId

Items

```

---

# Sprint 9

## Restaurant Orders

Page

```

/dashboard/orders

```

Restaurant sees

- Order Number
- Customer Name
- Customer Phone (if provided)
- Table Name
- Items
- Total
- Status

Buttons

```

Accept

Preparing

Ready

Completed

```

Calls

```

PATCH /orders/:id/status

```

---

# Sprint 10

## Live Updates

Connect Socket.IO

Restaurant listens

```

ORDER_CREATED

```

Customer listens

```

ORDER_UPDATED

```

No refresh.

Everything updates automatically.

---

# Pages

Public

```

/

/register

/login

/menu

```

Restaurant

```

/dashboard

/dashboard/tables

/dashboard/categories

/dashboard/menu

/dashboard/orders

```

Customer

```

/menu?table=TABLE_ID

/cart

/order/:id

```

---

# Components

Create reusable components.

Buttons

Inputs

Cards

Navbar

Sidebar

Dialogs

Tables

Food Cards

Category Cards

Menu Cards

Order Cards

Status Badge

Cart Item

Loader

Empty State

Error State

QR Card

---

# API Integration

Every API goes inside

```

services/

```

Example

```

auth.service.ts

restaurant.service.ts

table.service.ts

category.service.ts

menu.service.ts

order.service.ts

```

Never call Axios directly inside components.

---

# Authentication Flow

Restaurant

↓

Register

↓

Login

↓

JWT Stored

↓

Dashboard

Customer

↓

No Login

↓

Scan QR

↓

Menu Opens

---

# QR Flow

Customer

↓

Scan QR

↓

tableId detected

↓

Frontend calls

```

GET /tables/:tableId/menu

```

↓

Display Menu

↓

Customer Orders

---

# Loading States

Every page should handle

- Loading
- Success
- Error
- Empty Data

Never leave blank screens.

---

# Error Handling

Show friendly messages.

Examples

- Failed to load menu.
- Failed to place order.
- Login failed.
- Something went wrong.

Never display backend errors directly.

---

# Images

Restaurant uploads image

↓

Backend uploads to Cloudinary

↓

Frontend receives URL

↓

Display Image

---

# Design Principles

Simple

Modern

Fast

Responsive

Minimal Clicks

Restaurant staff should learn the interface within 10 minutes.

---

# Definition of Done

A frontend feature is complete when

- UI finished
- Responsive
- API connected
- Loading state
- Error state
- Manual testing completed
- No console errors

---

# MVP Checklist

Landing Page

Restaurant Registration

Restaurant Login

Dashboard

Restaurant Tables

QR Generation

Categories

Menu

Customer Menu

Shopping Cart

Orders

Live Updates

Responsive UI

If every item above is complete,

Frontend MVP is complete.

---

# Future Features

Build later

- AI Waiter
- Payments
- Loyalty
- Coupons
- Reviews
- Reservations
- Analytics
- Customer Accounts
- Dark Mode
- Multi-language

Focus only on MVP.

---

---

# Current Frontend Implementation

## Application structure and implemented screens

The frontend is a Next.js App Router application. The current top-level routes
include `/`, `/register`, `/login`, `/scan`, `/menu`, `/menu/[qrToken]`, and
dashboard routes `/dashboard`, `/dashboard/orders`, `/dashboard/menu`,
`/dashboard/tables`, `/dashboard/employees`, `/dashboard/loyalty`,
`/dashboard/settings`, `/dashboard/inventory`, and `/dashboard/reports`.
Inventory and reports are labeled “Soon” in the owner/manager navigation;
their presence as route folders does not mean those workflows are complete.

Reusable UI is under `components/`; API access is organized in `services/`;
API/domain contracts are in `types/`; client state helpers are in `utils/` and
`lib/`; shared providers are in `providers/`; validation is in `schemas/`.
Categories are created/edited/deleted from the Menu screen rather than from a
separate `/dashboard/categories` route.

Implemented workflows include restaurant registration/login; dashboard
summary and orders; menu items and categories; restaurant tables, QR codes and
table merging; employee administration; restaurant settings/branding;
notifications; loyalty screens; public QR menu, cart, order placement and
active-order tracking. Do not treat reports/inventory placeholders or the
older sprint checklist as completed functionality.

## API and server state

Feature API calls generally live in `services/` and use the shared Axios
instance in `services/api.ts`. Its request interceptor attaches the current
access token and records the auth generation/employee identity associated
with the request. On a 401 it permits a single retry, refreshes through
`POST /api/v1/auth/refresh`, saves both returned tokens, and retries only while
the original identity still matches. Authentication cleanup redirects to
`/login`; ambiguous refresh failure clears the affected auth state only when
that same auth snapshot is still current.

The app has a React Query provider with retry/stale/garbage-collection
defaults. No `useQuery`/`useMutation` feature usage was found in the current
source scan; most data fetches are explicit service calls with component
loading/error state. Zustand is used for the customer cart and selected order
dialog; the cart is in-memory client state, not a server-of-record.

## Authentication persistence and cross-tab coordination

The frontend stores `accessToken`, `refreshToken`, and employee data in
`localStorage`; `authGeneration` and `logoutPending` coordinate concurrent
state changes. Both login and registration persist the access and refresh
tokens. The backend remains authoritative for session status and token
validity.

Refresh requests use Web Locks (`cafeos-auth-refresh`) across tabs. A tab
acquires the lock, re-reads shared auth state, and uses the latest access token
if another tab already rotated the pair. Otherwise, it makes one refresh call
and persists both tokens only if the expected identity, generation, and
refresh token still match. This prevents a delayed refresh response from
overwriting a logout or a subsequent account login. The same lock name
serializes frontend auth-state mutations.

If Web Locks are unavailable, the refresh coordinator fails closed; the Axios
refresh failure path clears the still-current credentials and redirects rather
than sending duplicate rotations that could trigger backend replay detection.
Non-refresh auth-state operations use their own helper fallback when locks are
unavailable; this does not weaken the refresh path.

Logout invalidates the current auth generation, sends the captured access
token to `POST /api/v1/auth/logout`, then clears local auth state even if the
server revocation cannot be confirmed. The UI distinguishes confirmed server
revocation from local-only cleanup. Storage events plus a same-tab custom
auth-state event synchronize the Socket.IO client in other tabs.

Session settings list active sessions and allow revoking one or all *other*
sessions (the current UI performs individual revoke requests in sequence).
The backend and auth service also expose `POST /api/v1/auth/logout-all`;
the current settings UI does not call that endpoint. Changing a password
revokes sessions server-side; the user may need to authenticate again.

## Socket.IO client

The socket client is created disconnected and provides the current access
token through Socket.IO's `auth.token` callback on connection/reconnection.
After refresh, auth synchronization updates the socket and reconnects it when
the token changes; unchanged tokens do not force a reconnect. Logout or
cross-tab credential removal disconnects the local client. Server-side
handshake/session checks and token-expiry disconnection are documented in
[the Backend Guide](./04-Backend-Guide.md#current-backend-implementation).

## UI workflow notes

- The menu-management page contains both category controls and menu item
  controls. Category management is not a separate navigation destination.
- The tables page supports management and QR download; merge/unmerge actions
  are available through table services/routes where exposed by the UI.
- Public ordering begins from `/menu/[qrToken]`; the QR token, not a client
  supplied restaurant ID, selects the table/restaurant menu. Cart and current
  order UI submit and track orders through public APIs.
- Settings include restaurant information/branding, account/password actions,
  and active-session controls. The “Log out other sessions” button individually
  revokes non-current sessions; it is not the backend bulk logout endpoint.
- Employees, loyalty, and notifications are implemented separately from the
  original MVP-only plan.

---

End of Document