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

SERVED

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

End of Document