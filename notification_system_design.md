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
# Stage 3

## Query Given

```sql
SELECT * FROM notifications
WHERE studentID = 1042 AND isRead = false
ORDER BY createdAt ASC;
```

### Is the Query Accurate?

Yes. The query correctly returns all unread notifications for student **1042** and sorts them by the notification creation time in ascending order.

---

## Why is the Query Slow?

The database contains:

- 50,000 students
- 5,000,000 notifications

The query becomes slow because:

- It scans a large number of records.
- There may be no index on `studentID`, `isRead`, or `createdAt`.
- `SELECT *` retrieves all columns, even if only a few are needed.

---

## Improvements

Use only the required columns instead of `SELECT *`.

```sql
SELECT notificationID, title, message, createdAt
FROM notifications
WHERE studentID = 1042
AND isRead = false
ORDER BY createdAt ASC;
```

Create a composite index on the columns used in the query.

```sql
CREATE INDEX idx_student_read_created
ON notifications(studentID, isRead, createdAt);
```

---

## Computation Cost

- Without an index: **O(n)** (Full Table Scan)
- With a composite index: **O(log n)**

The indexed query is much faster because the database can directly locate the required records.

---

## Should We Add Indexes on Every Column?

**No.**

Adding indexes on every column is not a good idea because:

- It increases storage space.
- INSERT, UPDATE, and DELETE operations become slower.
- Many indexes are never used.

Indexes should be created only on columns that are frequently used in:

- WHERE
- ORDER BY
- JOIN

---

## Query to Find Students Who Received Placement Notifications in the Last 7 Days

```sql
SELECT DISTINCT studentID
FROM notifications
WHERE notificationType = 'Placement'
AND createdAt >= NOW() - INTERVAL 7 DAY;
```

This query returns all unique students who received a placement notification during the last seven days.