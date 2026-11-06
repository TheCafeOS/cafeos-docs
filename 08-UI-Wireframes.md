# CafeOS

# UI Wireframes

Version: 1.0

Status: Active

---

> **Implementation note (2026-11-06):** The sketches below are retained as
> design concepts. The **Verified Current Workflows** section at the end records
> the existing frontend routes and controls; where a sketch differs, current
> code is authoritative.

# Purpose

This document defines the UI layout of every major screen in CafeOS MVP.

These are not final designs.

They are functional wireframes to help development.

---

# Navigation

Restaurant

```

Dashboard

Tables

Categories

Menu

Orders

Settings

Logout

```

Customer

```

Menu

Cart

Order Tracking

```

---

# Landing Page

```

+----------------------------------------------------+

CafeOS Logo

Modern Restaurant Operating System

[ Register Restaurant ]

[ Login ]

Features

Why CafeOS?

Footer

+----------------------------------------------------+

```

Purpose

Landing page for new restaurants.

---

# Register Page

```

+------------------------------------------+

Register Restaurant

Restaurant Name

Restaurant Email

Restaurant Phone

Restaurant Address

Owner Name

Owner Email

Password

[ Create Restaurant ]

Already have an account?

Login

+------------------------------------------+

```

---

# Login Page

```

+--------------------------------+

Restaurant Login

Email

Password

[ Login ]

+--------------------------------+

```

---

# Dashboard

```

+--------------------------------------------------------------+

Sidebar

Dashboard

Tables

Categories

Menu

Orders

Settings

---------------------------------------------------------------

Today's Orders

Revenue

Active Tables

Menu Items

---------------------------------------------------------------

Recent Orders

---------------------------------------------------------------

```

---

# Tables Page

```

+------------------------------------------------------------+

Tables

[ Add Table ]

------------------------------------------------------------

Table 1

QR Code

Download QR

Edit

Delete

------------------------------------------------------------

Table 2

QR Code

Download QR

Edit

Delete

------------------------------------------------------------

```

Restaurant owner manages tables.

---

# Categories Page

```

+--------------------------------------------+

Categories

[ Add Category ]

---------------------------------------------

Coffee

Edit

Delete

---------------------------------------------

Pizza

Edit

Delete

---------------------------------------------

Desserts

Edit

Delete

---------------------------------------------

```

---

# Menu Page

```

+---------------------------------------------------------------+

Menu

[ Add Item ]

---------------------------------------------------------------

Image

Cold Coffee

Coffee

₹180

Available

Edit

Delete

---------------------------------------------------------------

```

Modal

```

Add Menu Item

Image

Name

Description

Category

Price

Available

[ Save ]

```

---

# Customer Menu

```

+----------------------------------------------------------+

Restaurant Name

Table 1

Search

----------------------------------------------------------

Coffee

----------------------------------------------------------

Cold Coffee

₹180

Add

----------------------------------------------------------

Hot Coffee

₹160

Add

----------------------------------------------------------

Pizza

----------------------------------------------------------

Veg Pizza

₹299

Add

----------------------------------------------------------

```

No login required.

---

# Cart

```

+---------------------------------------------+

Cart

---------------------------------------------

Cold Coffee

Qty +

Qty -

₹180

---------------------------------------------

Veg Pizza

Qty +

Qty -

₹299

---------------------------------------------

Customer Name

(Optional) Phone Number

Total

₹659

[ Place Order ]

+---------------------------------------------+

```

---

# Order Success

```

+-----------------------------------------+

Order Placed Successfully

Order Number

ORD-1001

Status

PENDING

[ Track Order ]

+-----------------------------------------+

```

---

# Order Tracking

```

+-------------------------------------------+

Order

ORD-1001

-------------------------------------------

Status

Preparing

-------------------------------------------

Timeline

✓ Order Received

✓ Accepted

✓ Preparing

Ready

COMPLETED

-------------------------------------------

```

Updates happen automatically using Socket.IO.

---

# Orders Dashboard

```

+--------------------------------------------------------------+

Orders

--------------------------------------------------------------

Order #1001

Table 2

Mayank

₹560

PENDING

[ Accept ]

--------------------------------------------------------------

Order #1002

Table 4

Rahul

₹820

PREPARING

[ Ready ]

--------------------------------------------------------------

```

---

# Order Details

```

+--------------------------------------------+

Order #1001

Customer

Mayank

Phone

9876543210

Table

Table 2

---------------------------------------------

Cold Coffee ×2

Veg Pizza ×1

---------------------------------------------

Total

₹659

---------------------------------------------

Accept

Preparing

Ready

Completed

+--------------------------------------------+

```

---

# Settings

MVP

```

Restaurant Name

Restaurant Phone

Restaurant Address

Restaurant Logo

Save Changes

```

---

# Mobile Layout

Customer pages must be designed mobile-first.

Restaurant dashboard should work on

- Laptop
- Tablet

Customer UI should work perfectly on phones.

---

# Color Palette

Primary

Orange (#F97316)

Secondary

White

Accent

Gray

Success

Green

Error

Red

Warning

Yellow

---

# Icons

Recommended

Lucide React

---

# Typography

Headings

Bold

Body

Regular

Buttons

Medium

---

# UI Principles

- Keep it simple.
- Avoid clutter.
- Large touch targets.
- Mobile-first.
- Fast loading.
- Consistent spacing.
- Reusable components.

---

# MVP Screens Checklist

Public

- Landing Page
- Register
- Login

Restaurant

- Dashboard
- Tables
- Categories
- Menu
- Orders
- Settings

Customer

- Menu
- Cart
- Order Success
- Order Tracking

---

---

# Verified Current Workflows

These are implementation-aligned summaries, not pixel-perfect screenshots.
Actual route and feature availability is described in the
[Frontend Guide](./05-Frontend-Guide.md#current-frontend-implementation).

## Registration and login

```text
Register: restaurant details + owner details + password
    → backend creates restaurant, owner, session and token pair
Login: email + password
    → backend creates a new session and returns access + refresh tokens
    → dashboard
```

Tokens are stored by the auth utility; they are not shown in the UI. Invalid or
expired sessions return the user to login.

## Restaurant dashboard and navigation

Owner navigation currently includes Dashboard, Orders, Menu, Tables,
Employees, Loyalty, Settings, and “Soon” entries for Inventory and Reports.
Managers and staff have reduced navigation based on role. The dashboard
includes order/revenue summaries and recent/active orders as provided by the
backend.

## Menu and categories

The Menu page is the category and menu-item workspace. It supports category
creation/edit/removal alongside menu item creation/edit/removal, item
availability, category assignment, food type, image upload and image framing.
There is no separately routed Categories page.

## Tables and QR ordering

The Tables page manages table names/status, QR codes and merge workflows. A
customer scans a table QR code and opens `/menu/[qrToken]`; the public page
shows restaurant branding, table identity, categories and available menu
items. The QR token identifies the table. The cart supports quantity
adjustments and checkout; current-order UI exposes active order state/tracking.

## Orders and notifications

The dashboard Orders page displays orders and supports valid status progression
(`PENDING` → `CONFIRMED` → `PREPARING` → `COMPLETED`, with cancellation where
allowed). Live events update dashboard/customer state. Notifications are
available from the dashboard notification controls and are loaded/read/cleared
through the authenticated API.

## Settings and sessions

```text
Settings
  ├─ Restaurant information and branding
  ├─ Account/password controls
  └─ Sessions
       ├─ Active session list: created, last used, expires
       ├─ Log out current device
       ├─ Revoke one other session
       └─ Log out other sessions (individual revocations; current device stays)
```

The current UI does not expose a “log out all including this device” button.
The backend API provides logout-all, and the frontend service has a method for
it, but the settings control for other sessions iterates over individual
revocation requests. A server revocation warning may be shown if local logout
completes but server confirmation fails.

## Planned or placeholder screens

Inventory and Reports are shown as “Soon” in navigation and should be treated
as planned/placeholder workflows rather than completed capabilities. Wireframe
sketches above that are not reflected in current routes or controls are design
ideas, not claims about implemented UI.

---

End of Document