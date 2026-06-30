# CafeOS

# System Flow

Version: 1.0

Status: Active

---

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

End of Document