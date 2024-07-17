---
sidebar_position: 1
---

# Api Response

API responses provide feedback to client requests and play a crucial role in communicating the outcome of those requests. They typically include a status code indicating the success or failure of the operation, along with relevant data or error messages.

Successful responses (status codes 2xx) convey the requested data or confirm the completion of an operation, while error responses (status codes 4xx and 5xx) indicate issues encountered during processing.

Our API responses follow a consistent structure, including the status code, a descriptive message, optional validation details, and relevant data payloads. By adhering to this structure, we ensure clarity, consistency, and ease of interpretation for both developers and end-users interacting with our APIs.

### Base Response
```ts
{
    "status": number;
    "message": string;
    "validation": object;
    "data": object;
}
```

- status: A numerical HTTP status code indicating the result of the API request. Common values include 200 for success, 400 for client errors, and 500 for server errors.

- message: A descriptive string providing additional information about the status of the request. This message helps clarify the outcome, such as "unauthorized" for a 401 status or "operation successful" for a 200 status.

- validation: An object containing validation errors, if any. This is typically used for 4xx responses to provide detailed feedback on why the request was invalid. For example, it may list fields that failed validation and the specific validation messages.

- data: An object containing the response payload. For successful requests, this includes the requested data. For example, a list of users or details of a specific resource. For error responses, this object may be empty or contain relevant error information.


### Success Response

```js
{
    "status": 200,
    "message": "success",
    "validation": {},
    "data": {}
}
```

### Success Response with Pagination

```js
{
    "status": 200,
    "message": "success",
    "validation": {},
    "data": {
        "list": [],
        "limit": 10,
        "page": 1,
        "total": 0 // total data
    }
}
```

### Error Basic Response

```js
{
    "status": 400,
    "message": "email has already taken",
    "validation": {},
    "data": {}
}
```

### Error Validation Response

```js
{
    "status": 400,
    "message": "email harus diisi",
    "validation": {
        "user.first_name": ["field x harus berupa email"]
    },
    "data": {}
}
```

> Notes: data JSON with standard `camelCase`