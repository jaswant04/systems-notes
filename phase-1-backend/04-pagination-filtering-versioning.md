# Pagination, Filtering, and Versioning in FastAPI

## What this concept is about
This note explains how pagination, filtering, and versioning are used in backend APIs to control data size, data selection, and API evolution without breaking clients.

---

## Pagination

### Why pagination exists
Endpoints that return large datasets can become slow and inefficient.
Pagination is used to:
- keep responses small
- reduce database load
- improve response time
- avoid returning unnecessary data

Instead of returning all records, data is returned in chunks.

---

### Core idea of pagination
Pagination means returning only a **subset of the total data** per request.
The client decides which subset it wants.

---

### Limit–Offset pagination

This is the most common pagination style.

Example request:

    /users?limit=10&offset=20

Meaning:
- return 10 results
- skip the first 20 results

---

### FastAPI example (limit–offset)

    @app.get("/users")
    def list_users(limit: int = 10, offset: int = 0):
        return {
            "limit": limit,
            "offset": offset,
            "data": fetch_users(limit=limit, offset=offset)
        }

What this does:
- `limit` and `offset` are query parameters
- default values are used if not provided
- FastAPI validates input types before execution

---

### Page-based pagination

Clients think in pages instead of offsets.

Example request:

    /users?page=3&page_size=10

Meaning:
- page number 3
- 10 items per page

---

### FastAPI example (page-based)

    @app.get("/users")
    def list_users(page: int = 1, page_size: int = 10):
        offset = (page - 1) * page_size
        limit = page_size

        return {
            "page": page,
            "page_size": page_size,
            "data": fetch_users(limit=limit, offset=offset)
        }

Internally, page-based pagination is converted to limit–offset.

---

### Important pagination rules
- Pagination should be applied at the database/query level
- Avoid allowing very large limits
- Pagination controls **how much data** is returned, not which data

---

## Filtering

### Why filtering exists
Filtering allows clients to request **only the data they need**.
Without filtering, clients would have to fetch extra data and discard it, which is inefficient.

Filtering answers:
> Which data should be returned?

---

### Core idea of filtering
Filtering uses **query parameters** to narrow down results based on conditions.

Example request:

    /users?role=admin&active=true

---

### FastAPI filtering example

    @app.get("/users")
    def list_users(
        role: str | None = None,
        active: bool | None = None,
        limit: int = 10,
        offset: int = 0
    ):
        return fetch_users(
            role=role,
            active=active,
            limit=limit,
            offset=offset
        )

What this does:
- `role` and `active` are optional filters
- if a filter is not provided, it is ignored
- one endpoint supports many use cases

---

### Path parameters vs filtering
- Path parameters identify a specific resource
- Filtering narrows down collections

Filtering is typically used with list endpoints.

---

### Important filtering rules
- Filters should be optional where possible
- Filtering should happen at the database level
- One flexible endpoint is better than many rigid endpoints

---

## Pagination + Filtering together

In real systems, pagination and filtering are almost always combined.

Example request:

    /users?role=admin&active=true&limit=10&offset=0

Meaning:
- fetch only admin users
- only active ones
- return 10 at a time

Filtering decides **what data**, pagination decides **how much data**.

---

## Versioning

### Why API versioning exists
APIs evolve over time:
- fields change
- response structure changes
- validation rules change

Breaking existing clients is not acceptable.
Versioning allows multiple API contracts to exist at the same time.

---

### What versioning means
Versioning means serving **different versions of the same API** simultaneously.

Old clients continue using older versions.
New clients opt into newer versions.

---

### URL-based versioning (recommended)

Most common and practical approach:

    /api/v1/users
    /api/v2/users

Advantages:
- explicit and clear
- easy to route
- easy to deprecate

---

### FastAPI versioning example

    @app.get("/api/v1/users")
    def list_users_v1():
        return {"version": "v1"}

    @app.get("/api/v2/users")
    def list_users_v2():
        return {"version": "v2"}

Each version:
- has its own contract
- evolves independently
- does not affect other versions

---

### When to introduce a new version
A new version should be created only for:
- breaking response changes
- breaking request changes
- behavior changes visible to clients

Minor internal changes do not require versioning.

---

## Common mistakes to avoid

- Creating new endpoints instead of using filters
- Paginating data after fetching everything
- Versioning too early or too often
- Mixing logic of different API versions

---

## Key takeaway
Pagination controls **how much data** is returned, filtering controls **which data** is returned, and versioning allows APIs to evolve safely without breaking existing clients.
