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

Served

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

End of Document