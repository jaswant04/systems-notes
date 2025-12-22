# Routing and Path Operations in FastAPI

## What this concept is about
This note explains how routing works in FastAPI, how endpoints are defined, and how data from HTTP requests is mapped to Python functions using paths, methods, and parameters.

---

## What routing means
Routing is the mechanism used by FastAPI to decide which function should handle an incoming HTTP request.

FastAPI determines the correct function based on:
- HTTP method (GET, POST, etc.)
- URL path

Only one matching function is executed per request.

---

## Basic endpoint (static route)

Example:

    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/health")
    def health_check():
        return {"status": "ok"}

### What this means
- HTTP method: GET  
- Path: /health  
- If a client sends GET /health  
- FastAPI matches the route  
- FastAPI calls health_check()  
- The return value becomes the HTTP response  

This endpoint:
- takes no input
- always returns the same response

---

## What an endpoint is
An endpoint is a Python function mapped to:
- one HTTP method
- one URL path

Each unique method + path combination is a separate endpoint.

Examples:
- GET /users
- POST /users
- GET /users/1

---

## Path parameters (dynamic routes)

Path parameters allow parts of the URL to be variable.

Example:

    @app.get("/users/{user_id}")
    def get_user(user_id: int):
        return {"user_id": user_id}

### What this means
- {user_id} is a path parameter
- /users/10 → user_id = 10
- /users/abc → invalid (not an integer)
- The value is extracted from the URL
- It is passed into the function as an argument

Path parameters:
- are always required
- identify a specific resource
- are validated before the function runs

If validation fails, FastAPI returns an error and does not execute the function.

---

## Query parameters

Query parameters come from the URL after ?.

Example request:

    /users?page=2&limit=5

Endpoint example:

    @app.get("/users")
    def list_users(page: int = 1, limit: int = 10):
        return {"page": page, "limit": limit}

### What this means
- page and limit are query parameters
- They are optional because default values exist
- If not provided, defaults are used
- Commonly used for pagination and filtering

Difference from path parameters:
- Path parameters identify which resource
- Query parameters modify how data is fetched

---

## Required vs optional parameters

- Path parameters are always required
- Query parameters can be required or optional

Example (required query parameter):

    @app.get("/search")
    def search(q: str):
        return {"query": q}

If q is missing, FastAPI returns an error.

---

## Parameter types and validation

FastAPI uses type annotations for validation.

Example:

    @app.get("/items/{item_id}")
    def get_item(item_id: int):
        return {"item_id": item_id}

Behavior:
- Input is converted to the declared type
- Invalid input is rejected
- Function is not executed on validation failure

Examples:
- /items/5 → valid
- /items/abc → validation error

---

## Same path with different HTTP methods

The same path can represent different actions using different HTTP methods.

Example:

    @app.get("/users")
    def get_users():
        return {"action": "get users"}

    @app.post("/users")
    def create_user():
        return {"action": "create user"}

These are treated as separate endpoints because:
- Path is the same
- HTTP method is different

Routing always uses:
method + path

---

## What happens when routing fails

FastAPI returns an error and does not call the endpoint function if:
- the path does not exist → 404
- the method is not allowed → 405
- parameters fail validation → 422

This prevents invalid requests from reaching business logic.

---

## Key takeaway
FastAPI routing maps HTTP method and path to exactly one function, extracts and validates inputs, and executes the function only when the request is valid.
