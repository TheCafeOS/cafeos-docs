# CafeOS

# API Documentation (MVP)

Version: 1.0

Status: Active

Base URL

/api/v1

Content-Type

application/json

---

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
  "restaurantName": "Cafe Bliss",
  "restaurantEmail": "contact@cafebliss.com",
  "restaurantPhone": "9876543210",
  "address": "Rajpur Road, Dehradun",

  "ownerName": "Mayank Sharma",
  "ownerEmail": "owner@cafebliss.com",
  "password": "Password@123"
}
```

Response

```json
{
  "success": true,
  "message": "Restaurant registered successfully."
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
  "email": "owner@cafebliss.com",
  "password": "Password@123"
}
```

Response

```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "token": "jwt_token"
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
      "id": "uuid",
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
      "id": "uuid",
      "name": "Coffee"
    },
    {
      "id": "uuid",
      "name": "Pizza"
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
/tables/:tableId/menu
```

Description

Returns the menu for the restaurant associated with the scanned table.

Response

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Cold Coffee",
      "description": "Creamy cold coffee",
      "price": 180,
      "image": "https://...",
      "category": "Coffee",
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
  "name": "Cold Coffee",
  "description": "Creamy cold coffee",
  "price": 180,
  "categoryId": "uuid",
  "image": "cloudinary_url"
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
  "name": "Cold Coffee",
  "price": 200,
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
/orders
```

Request

```json
{
  "customerName": "Mayank",
  "customerPhone": "9876543210",
  "tableId": "uuid",
  "items": [
    {
      "menuItemId": "uuid",
      "quantity": 2
    },
    {
      "menuItemId": "uuid",
      "quantity": 1
    }
  ]
}
```

`customerPhone` is optional.

---

Response

```json
{
  "success": true,
  "message": "Order placed successfully.",
  "data": {
    "orderId": "uuid",
    "orderNumber": "ORD-1001",
    "status": "PENDING"
  }
}
```

Status Codes

```
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
      "id": "uuid",
      "orderNumber": "ORD-1001",
      "customerName": "Mayank",
      "customerPhone": "9876543210",
      "tableName": "Table 1",
      "totalAmount": 560,
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
    "id": "uuid",
    "orderNumber": "ORD-1001",
    "customerName": "Mayank",
    "customerPhone": "9876543210",
    "tableName": "Table 1",
    "status": "PREPARING",
    "items": [
      {
        "name": "Cold Coffee",
        "quantity": 2,
        "price": 180
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
  "status": "READY"
}
```

Allowed Values

```
PENDING
ACCEPTED
PREPARING
READY
SERVED
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
  "orderId": "uuid",
  "orderNumber": "ORD-1001",
  "customerName": "Mayank",
  "tableName": "Table 1"
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
  "orderId": "uuid",
  "status": "READY"
}
```

---

# Protected APIs

Authentication Required

```
POST /tables
PUT /tables/:id
DELETE /tables/:id

POST /categories
PUT /categories/:id
DELETE /categories/:id

POST /menu
PUT /menu/:id
DELETE /menu/:id

GET /orders
PATCH /orders/:id/status
```

---

# Public APIs

```
POST /auth/register
POST /auth/login

GET /tables/:tableId/menu

POST /orders

GET /orders/:id
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

GET /tables/:tableId/menu

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

End of Document