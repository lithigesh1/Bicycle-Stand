#  API Doumentation
**Base URL**

```

[https://bicycle-stand.onrender.com/api](https://bicycle-stand.onrender.com/api)

````

---

# Endpoints

## 1. Command API

### **POST** `/command`

Send a lock/unlock command for a specific bicycle.

- **Request Body:**

```json
{
  "cycleId": "string",
  "command": "lock" | "unlock"
}
````

* **Response:**

```json
{
  "message": "Command saved successfully"
}
```

* **Errors:**

| Status Code | Description                   |
| ----------- | ----------------------------- |
| 400         | Missing or invalid fields     |
| 500         | Server failed to save command |

---

### **GET** `/command/:cycleId`

ESP32 device polls this endpoint to fetch the latest command.

* **URL Parameters:**

| Parameter | Type   | Description              |
| --------- | ------ | ------------------------ |
| cycleId   | String | Unique ID of the bicycle |

* **Response:**

```json
{
  "_id": "commandId",
  "cycleId": "string",
  "command": "lock" | "unlock",
  "createdAt": "timestamp"
}
```

* **Errors:**

| Status Code | Description            |
| ----------- | ---------------------- |
| 404         | No command found       |
| 500         | Error fetching command |

---

## 2. Status API

### **POST** `/status`

ESP32 sends battery status, GPS location, and tamper alerts.

* **Request Body:**

```json
{
  "cycleId": "string",
  "battery": 0-100,
  "location": {
    "lat": Number,
    "lng": Number
  },
  "tamper": true | false
}
```

* **Response:**

```json
{
  "message": "Status updated"
}
```

* **Errors:**

| Status Code | Description               |
| ----------- | ------------------------- |
| 400         | Missing or invalid fields |
| 500         | Failed to update status   |

---

### **GET** `/status/:cycleId`

Fetch the current status of a bicycle (for mobile app or monitoring).

* **URL Parameters:**

| Parameter | Type   | Description              |
| --------- | ------ | ------------------------ |
| cycleId   | String | Unique ID of the bicycle |

* **Response:**

```json
{
  "_id": "cycleId",
  "cycleId": "string",
  "status": "locked" | "unlocked",
  "battery": Number,
  "location": {
    "lat": Number,
    "lng": Number
  },
  "lastSeen": "timestamp"
}
```

* **Errors:**

| Status Code | Description           |
| ----------- | --------------------- |
| 404         | Cycle not found       |
| 500         | Error fetching status |

---

## 3. Logs API (Tamper & Disconnect Events)

Logs are created internally when tamper or disconnect events happen.

* Logs are stored in the `logs` collection.
* Each log entry contains:

```json
{
  "cycleId": "string",
  "type": "tamper" | "disconnect",
  "message": "string",
  "timestamp": "date"
}
```

---

### **GET** `/logs/:cycleId`

Fetch all logs related to a specific cycle.

* **URL Parameters:**

| Parameter | Type   | Description              |
| --------- | ------ | ------------------------ |
| cycleId   | String | Unique ID of the bicycle |

* **Response:**

```json
[
  {
    "_id": "logId",
    "cycleId": "string",
    "type": "tamper" | "disconnect",
    "message": "string",
    "timestamp": "date"
  },
  ...
]
```

* **Errors:**

| Status Code | Description         |
| ----------- | ------------------- |
| 404         | No logs found       |
| 500         | Error fetching logs |

---

### **POST** `/logs`

Add a new log entry (useful for manual/testing purposes).

* **Request Body:**

```json
{
  "cycleId": "string",
  "type": "tamper" | "disconnect",
  "message": "string",
  "timestamp": "ISO 8601 date string"
}
```

* **Response:**

```json
{
  "message": "Log entry created",
  "log": {
    "_id": "logId",
    "cycleId": "string",
    "type": "tamper" | "disconnect",
    "message": "string",
    "timestamp": "date"
  }
}
```

* **Errors:**

| Status Code | Description               |
| ----------- | ------------------------- |
| 400         | Missing or invalid fields |
| 500         | Server error creating log |

```
