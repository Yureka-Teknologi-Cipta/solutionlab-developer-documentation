---
sidebar_position: 3
---

# Default Response

> Notes: data JSON with standard `camelCase`

## Success Response

```
{
    "status": 200,
    "message": "success",
    "validation": {},
    "results": {
        "data": {}
    }
}
```

## Success Response with Pagination

```
{
    "status": 200,
    "message": "success",
    "validation": {},
    "results": {
        "data": [],
        "pagination": {
            "page": 1, // current page
            "limit": 10,
            "totalData": 100,
            "totalPage": 10
        }
    }
}
```

## Failed Response

```
{
    "status": 400,
    "message": "email harus diisi",
    "validation": {
        "user.first_name": ["field x harus berupa email"]
    },
    "results":{}
}
```
