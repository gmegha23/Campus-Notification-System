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