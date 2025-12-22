# Dependency Injection (DI) in FastAPI

## What this concept is about
This note explains how Dependency Injection works in FastAPI, why it is used, and how shared logic is provided to endpoints in a controlled and reusable way.

---

## What problem Dependency Injection solves
In real backend applications, many endpoints need the same things:
- database connections
- authentication checks
- configuration values
- shared validation logic

Without DI, this logic would be:
- repeated in multiple endpoints
- tightly coupled to endpoint code
- hard to change and test

Dependency Injection solves this by centralizing shared logic and letting FastAPI manage its execution.

---

## What a dependency is in FastAPI
A dependency is a **function whose execution is controlled by FastAPI**, and whose return value is **injected into an endpoint**.

The endpoint does not call the dependency directly.

---

## Basic dependency example

Dependency function:

    def get_common_data():
        return {"source": "dependency"}

Endpoint using the dependency:

    from fastapi import FastAPI, Depends

    app = FastAPI()

    @app.get("/example")
    def example(data = Depends(get_common_data)):
        return data

### What happens internally
- A request arrives at `/example`
- FastAPI runs `get_common_data()`
- The return value is assigned to `data`
- FastAPI then calls `example(data=...)`
- The response is returned to the client

Execution order:
dependency → endpoint

---

## Dependency vs normal function call

Normal function call:
- the function is called manually
- execution order is controlled by the developer
- inputs are passed explicitly

Dependency:
- FastAPI decides when it runs
- FastAPI resolves its inputs
- FastAPI injects its output
- endpoint code stays clean

---

## Dependency for database access (real use case)

Dependency:

    def get_db():
        db = create_db_connection()
        try:
            return db
        finally:
            db.close()

Endpoint:

    @app.get("/users/{user_id}")
    def get_user(user_id: int, db = Depends(get_db)):
        return db.fetch_user(user_id)

### Why this is useful
- DB creation logic is not repeated
- Connection lifecycle is centralized
- Endpoint focuses only on business logic

---

## Dependencies can block request execution

Dependencies can stop an endpoint from running.

Example: authentication dependency

    from fastapi import HTTPException

    def get_current_user(token: str = Depends(get_token)):
        if not is_valid(token):
            raise HTTPException(status_code=401)
        return {"user": "authenticated"}

Endpoint:

    @app.get("/profile")
    def profile(user = Depends(get_current_user)):
        return user

### What happens
- FastAPI runs `get_current_user`
- If authentication fails:
  - endpoint function is never called
  - error response is returned immediately

This is how authentication and permission checks are implemented.

---

## Dependencies can depend on other dependencies

Dependencies can be chained.

Example:

    def get_settings():
        return settings

    def get_db(settings = Depends(get_settings)):
        return create_db(settings)

FastAPI resolves dependencies in the correct order automatically.

---

## Where Dependency Injection is commonly used
DI is typically used for:
- database sessions
- authentication and authorization
- request-scoped data
- shared validations
- configuration access

Most production FastAPI applications rely heavily on DI.

---

## Key takeaway
Dependency Injection in FastAPI allows shared logic to be declared as dependencies, executed by the framework, and injected into endpoints, keeping endpoint code clean, reusable, and easy to maintain.
