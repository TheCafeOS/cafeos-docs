# CafeOS

# UI Wireframes

Version: 1.0

Status: Active

---

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

Served

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

Served

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

End of Document