# CafeOS

# Development Roadmap

Version: 1.0

Status: Active

Authors

- Mayank Sharma (Backend)
- Anshul (Frontend)

---

# Purpose

This document defines the development roadmap for CafeOS.

Whenever you're confused about what to build next, open this file.

Complete one sprint before moving to the next.

**Do NOT skip ahead.**

---

# MVP Goal

By the end of the MVP, a restaurant owner should be able to:

Restaurant Login

↓

Create Menu

↓

Customer Scans QR

↓

Customer Orders Food

↓

Restaurant Receives Order

↓

Restaurant Updates Order Status

↓

Customer Sees Live Updates

If this workflow works, MVP is complete.

---

# Sprint 1 — Foundation

## Goal

Create the project foundation.

Nothing fancy.

Just make everything run.

---

## Backend (Mayank)

- Setup Express
- Setup TypeScript
- Setup Prisma
- Connect PostgreSQL
- Create folder structure
- Create environment configuration
- Create health check endpoint
- Setup Git repository

Deliverable:

Backend runs successfully with PostgreSQL connected.

---

## Frontend (Anshul)

- Setup Next.js
- Setup Tailwind CSS
- Setup shadcn/ui
- Create folder structure
- Create global layout
- Create theme
- Create Navbar
- Create basic Landing Page

Deliverable:

Frontend runs successfully.

---

# Sprint 2 — Authentication

## Goal

Restaurant owner should be able to login.

---

## Backend

Build:

- Restaurant Model
- JWT Authentication
- Login API
- Protected Routes
- Password Hashing
- Authentication Middleware

APIs

POST /auth/login

Deliverable

Restaurant receives JWT after login.

---

## Frontend

Build

- Login Page
- Login Form
- Authentication Context
- Protected Routes
- Logout

Deliverable

Restaurant owner can login successfully.

---

# Sprint 3 — Menu Management

## Goal

Restaurant owner should manage menu items.

---

## Backend

Create Models

- Category
- MenuItem

Build APIs

GET /menu

POST /menu

PUT /menu/:id

DELETE /menu/:id

GET /categories

POST /categories

Deliverable

Restaurant can manage complete menu.

---

## Frontend

Pages

Dashboard

↓

Categories

↓

Menu

Features

- Add Item
- Edit Item
- Delete Item
- Upload Image
- Categories

Deliverable

Restaurant dashboard fully manages menu.

---

# Sprint 4 — Customer Menu

## Goal

Customer should browse the menu.

---

## Backend

Public APIs

GET /menu

GET /categories

Deliverable

Customer can fetch restaurant menu.

---

## Frontend

Pages

QR Menu

Category Filter

Food Cards

Search

Cart

Deliverable

Customer can browse the menu.

---

# Sprint 5 — Ordering

## Goal

Customer places order.

Restaurant receives order.

---

## Backend

Models

Order

OrderItem

APIs

POST /orders

GET /orders

GET /orders/:id

PATCH /orders/:id/status

Business Logic

- Validate Items
- Calculate Total
- Save Order

Deliverable

Orders stored successfully.

---

## Frontend

Customer

- Cart
- Checkout
- Place Order

Restaurant

- Orders Page

Deliverable

Restaurant receives orders.

---

# Sprint 6 — Live Order Updates

## Goal

Customer sees live updates.

---

## Backend

Setup Socket.IO

Events

ORDER_CREATED

ORDER_UPDATED

ORDER_READY

ORDER_SERVED

Deliverable

Backend sends events.

---

## Frontend

Customer

Live Status

Restaurant

Update Status

Deliverable

Order status updates automatically.

---

# Sprint 7 — Restaurant Dashboard

## Goal

Restaurant dashboard becomes usable.

---

## Backend

Dashboard APIs

Today's Orders

Revenue

Recent Orders

Order Count

Deliverable

Dashboard APIs completed.

---

## Frontend

Dashboard

Cards

Charts

Recent Orders

Today's Revenue

Order Summary

Deliverable

Restaurant owner sees dashboard.

---

# MVP Completion Checklist

Restaurant Login

Menu Management

Customer Menu

Cart

Order Placement

Restaurant Dashboard

Kitchen Orders

Socket.IO

Order Tracking

Basic Dashboard

When every item above is complete,

CafeOS MVP Version 1.0 is complete.

---

# Phase 2

Build after MVP.

Features

- Customer Accounts
- Loyalty Program
- Coupons
- Google Review Booster
- Reservations
- WhatsApp Notifications
- Razorpay Payments
- Analytics

---

# Phase 3

AI Features

- AI Waiter
- AI Recommendations
- AI Marketing
- AI Dashboard
- AI Review Analysis

---

# Phase 4

Business Features

- Inventory
- Suppliers
- Purchase Orders
- Multi Branch
- Employee Management
- Payroll
- CRM

---

# Working Rules

While developing,

Backend should NEVER wait for frontend.

Frontend should NEVER wait for backend.

Frontend should initially use mock JSON.

Backend should build APIs independently.

Integration happens only after APIs are complete.

---

# Daily Workflow

Every day:

1. Pick one task.
2. Complete it.
3. Commit code.
4. Push to GitHub.
5. Update the other developer.

Never start multiple unfinished features.

---

# Definition of Done

A feature is complete only if:

- Code is written.
- API works.
- Frontend works.
- No console errors.
- No backend errors.
- Feature tested manually.

---

# Current Sprint

Sprint 1

Backend

- [ ] Setup Express
- [ ] Setup TypeScript
- [ ] Setup Prisma
- [ ] Connect PostgreSQL
- [ ] Folder Structure

Frontend

- [ ] Setup Next.js
- [ ] Setup Tailwind
- [ ] Setup shadcn/ui
- [ ] Create Landing Page
- [ ] Create Dashboard Layout

Do not move to Sprint 2 until Sprint 1 is complete.

---

End of Document