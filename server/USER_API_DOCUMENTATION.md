# User Controller API Documentation

This document describes the user controller endpoints for the restaurant QR order system.

## Base URL
```
/api/user
```

## Authentication
All endpoints require authentication via JWT token in the Authorization header:
```
Authorization: Bearer <jwt_token>
```

## Endpoints

### 1. Get All Sessions
**GET** `/sessions`

Returns all sessions for the authenticated user, sorted by creation date (latest first).

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "_id": "session_id",
      "tableNo": 5,
      "isActive": true,
      "isPaid": false,
      "paymentStatus": "Unpaid",
      "paymentMethod": "",
      "finalAmount": 1250,
      "startedAt": "2024-01-15T10:30:00.000Z",
      "endedAt": null,
      "createdAt": "2024-01-15T10:30:00.000Z",
      "updatedAt": "2024-01-15T10:30:00.000Z",
      "totalOrders": 3,
      "totalAmount": 1250,
      "orders": [...]
    }
  ]
}
```

### 2. Get Orders by Session
**GET** `/session/:sessionId/orders`

Returns detailed order information for a specific session.

**Parameters:**
- `sessionId` (string, required): Valid MongoDB ObjectId

**Response:**
```json
{
  "success": true,
  "data": {
    "session": {
      "_id": "session_id",
      "tableNo": 5,
      "isActive": true,
      "isPaid": false,
      "paymentStatus": "Unpaid",
      "paymentMethod": "",
      "finalAmount": 1250,
      "startedAt": "2024-01-15T10:30:00.000Z",
      "endedAt": null
    },
    "orders": [
      {
        "id": "order_id",
        "sessionId": "session_id",
        "tableNo": 5,
        "items": [
          {
            "id": "item_id",
            "name": "Butter Chicken",
            "description": "Creamy and rich chicken curry",
            "price": 450,
            "quantity": 2,
            "category": "Main Course",
            "image": "butter-chicken.jpg",
            "vegetarian": false,
            "spicy": false,
            "status": "Processing",
            "totalPrice": 900
          }
        ],
        "amount": 900,
        "status": "Processing",
        "paymentStatus": "Pending",
        "paymentMethod": "Cash",
        "createdAt": "2024-01-15T10:35:00.000Z",
        "updatedAt": "2024-01-15T10:35:00.000Z",
        "totalItems": 1,
        "estimatedTime": "5-10 minutes"
      }
    ],
    "summary": {
      "totalAmount": 1250,
      "totalOrders": 3,
      "pendingOrders": 2,
      "deliveredOrders": 1
    }
  }
}
```

### 3. Get Active Session
**GET** `/active-session`

Returns the currently active session for the user with detailed order information.

**Response:**
```json
{
  "success": true,
  "data": {
    "session": {
      "_id": "session_id",
      "tableNo": 5,
      "isActive": true,
      "isPaid": false,
      "paymentStatus": "Unpaid",
      "finalAmount": 1250
    },
    "orders": [...],
    "totalAmount": 1250,
    "totalOrders": 3
  }
}
```

### 4. Get User Profile
**GET** `/profile`

Returns the authenticated user's profile information.

**Response:**
```json
{
  "success": true,
  "data": {
    "_id": "user_id",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "1234567890",
    "isGoogleAccount": false,
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### 5. Update User Profile
**PUT** `/profile`

Updates the user's profile information.

**Request Body:**
```json
{
  "name": "John Doe",     // optional, min 3 chars, max 255 chars
  "phone": "1234567890"   // optional, min 10 chars, max 20 chars
}
```

**Response:**
```json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "_id": "user_id",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "1234567890",
    "isGoogleAccount": false,
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-15T12:00:00.000Z"
  }
}
```

### 6. Get Order History
**GET** `/orders`

Returns paginated order history for the user.

**Query Parameters:**
- `page` (number, optional): Page number (default: 1)
- `limit` (number, optional): Items per page, 1-100 (default: 10)
- `status` (string, optional): Filter by order status ("Not Process", "Processing", "Delivered", "Cancelled")

**Response:**
```json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "order_id",
        "tableNo": 5,
        "items": [...],
        "amount": 900,
        "status": "Delivered",
        "paymentStatus": "Paid",
        "paymentMethod": "Cash",
        "createdAt": "2024-01-15T10:35:00.000Z",
        "session": {
          "_id": "session_id",
          "tableNo": 5,
          "isActive": false,
          "startedAt": "2024-01-15T10:30:00.000Z"
        },
        "totalItems": 1
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalOrders": 50,
      "hasNextPage": true,
      "hasPrevPage": false
    }
  }
}
```

## Error Responses

All endpoints return consistent error responses:

```json
{
  "success": false,
  "message": "Error description",
  "extraDetails": "Additional error details"
}
```

Common HTTP Status Codes:
- `200`: Success
- `404`: Resource not found
- `422`: Validation error
- `500`: Internal server error

## Data Models

### Session Model
- `_id`: MongoDB ObjectId
- `tableNo`: Number (required)
- `isActive`: Boolean (default: true)
- `isPaid`: Boolean (default: false)
- `users`: Array of User ObjectIds
- `orders`: Array of Order ObjectIds
- `startedAt`: Date (default: now)
- `endedAt`: Date (optional)
- `paymentMethod`: String enum ["Cash", "Online", "Razorpay", ""]
- `paymentStatus`: String enum ["Unpaid", "Paid"]
- `finalAmount`: Number (default: 0)

### Order Model
- `_id`: MongoDB ObjectId
- `sessionId`: Session ObjectId (required)
- `buyer`: User ObjectId (required)
- `tableNo`: Number (required)
- `products`: Array of order products
- `amount`: Number (required)
- `status`: String enum ["Not Process", "Processing", "Delivered", "Cancelled"]
- `paymentStatus`: String enum ["Pending", "Paid", "Unpaid"]
- `paymentMethod`: String enum ["Cash", "Online", "Counter", "Pending", "Razorpay"]

### Service Model (Menu Items)
- `_id`: MongoDB ObjectId
- `name`: String (required)
- `description`: String (required)
- `price`: Number (required)
- `category`: String enum ["Appetizer", "Main Course", "Dessert", "Beverage"]
- `image`: String (optional)
- `vegetarian`: Boolean (default: false)
- `spicy`: Boolean (default: false)
- `available`: Boolean (default: true) 