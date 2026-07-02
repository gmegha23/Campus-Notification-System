# Stage 1

# Campus Notification System - REST API Design

## Base URL

http://localhost:5000/api

---

## Common Headers

### Request Header

Content-Type: application/json
Accept: application/json

### Response Header

Content-Type: application/json

---

## 1. Get All Notifications

**Endpoint**

GET /notifications

**Response**

```json
{
  "success": true,
  "notifications": [
    {
      "id": 1,
      "title": "Placement Drive",
      "message": "TCS placement on Monday",
      "type": "Placement",
      "read": false
    }
  ]
}
```

---

## 2. Get Notification by ID

GET /notifications/1

---

## 3. Add Notification

POST /notifications

```json
{
  "title": "Results",
  "message": "Semester results published",
  "type": "Result"
}
```

---

## 4. Update Notification

PUT /notifications/1

---

## 5. Delete Notification

DELETE /notifications/1

---

## 6. Mark Notification as Read

PATCH /notifications/1/read

---

## Real-Time Notification

WebSocket is used to send notifications instantly to connected users.

Example:

```json
{
  "event": "New Notification",
  "title": "Amazon Placement Drive",
  "message": "Registration closes today"
}
```

---

## HTTP Status Codes

- 200 - Success
- 201 - Created
- 400 - Bad Request
- 404 - Not Found
- 500 - Server Error

# Stage 2

## Database Choice

I suggest using **MongoDB (NoSQL Database)** because:

- It stores data in JSON format.
- It is easy to use with Node.js and Express.
- It is flexible to store notification data.
- It can handle a large number of notifications efficiently.

---

## Database Schema

Collection Name: **notifications**

Example Document

```json
{
  "_id": "1",
  "title": "Placement Drive",
  "message": "TCS placement on Monday",
  "type": "Placement",
  "read": false,
  "createdAt": "2026-07-02T10:30:00Z"
}
```

Fields:

| Field | Type |
|--------|------|
| _id | ObjectId |
| title | String |
| message | String |
| type | String |
| read | Boolean |
| createdAt | Date |

---

## Problems When Data Increases

As the number of notifications increases,

- Searching becomes slower.
- Database size becomes larger.
- Reading all notifications takes more time.

---

## Solution

These problems can be solved by

- Creating indexes on frequently searched fields.
- Using pagination to display notifications.
- Archiving old notifications.
- Using database sharding for very large data.

---

## MongoDB Queries

### 1. Get All Notifications

```javascript
db.notifications.find()
```

---

### 2. Get Notification by ID

```javascript
db.notifications.findOne({
    _id: ObjectId("1")
})
```

---

### 3. Add Notification

```javascript
db.notifications.insertOne({
    title: "Placement Drive",
    message: "TCS placement on Monday",
    type: "Placement",
    read: false,
    createdAt: new Date()
})
```

---

### 4. Update Notification

```javascript
db.notifications.updateOne(
    {
        _id: ObjectId("1")
    },
    {
        $set: {
            message: "Interview timing updated"
        }
    }
)
```

---

### 5. Delete Notification

```javascript
db.notifications.deleteOne({
    _id: ObjectId("1")
})
```

---

### 6. Mark Notification as Read

```javascript
db.notifications.updateOne(
    {
        _id: ObjectId("1")
    },
    {
        $set: {
            read: true
        }
    }
)
```

---

### 7. Get Notifications by Type

```javascript
db.notifications.find({
    type: "Placement"
})
```