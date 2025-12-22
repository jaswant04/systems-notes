# What is FastAPI

## What this concept is about
This note explains what FastAPI is, what problem it solves, how it runs, and where it fits in the HTTP request–response flow.

---

## What problem FastAPI solves
Building an HTTP server from scratch in Python requires handling:
- network connections
- HTTP parsing
- routing
- request/response formatting
- concurrency

Doing this manually is complex and error-prone. FastAPI provides a reusable framework so developers can focus on **application logic instead of low-level HTTP and server details**.

---

## What FastAPI is
FastAPI is a Python web framework used to build **HTTP APIs**.

Its primary responsibility is to:
- receive HTTP requests
- map them to user-defined functions
- execute application logic
- return HTTP responses

FastAPI acts as the **interface layer** between HTTP and Python code.

---

## What FastAPI does
FastAPI handles:
- receiving and parsing HTTP requests
- routing requests to the correct handler
- executing endpoint functions
- converting return values into HTTP responses
- integrating cleanly with asynchronous execution

Code runs **only when a request arrives**.

---

## What FastAPI does NOT do
FastAPI does NOT:
- store or manage data
- define business logic
- manage databases
- act as a frontend framework
- run code continuously in a loop

Those responsibilities belong to application code or other services.

---

## How a FastAPI app starts

When the server is started (for example via an ASGI server):
- the Python file is executed **once**
- the FastAPI app object is created
- routes and configuration are registered
- the application enters a waiting state

At this point, the app is **initialized but idle**.

---

## How a FastAPI app stays alive

After startup:
- the application remains in memory
- it does not exit after handling a request
- it waits for incoming HTTP requests

The lifecycle follows this pattern:

    wait → receive request → execute endpoint → send response → wait


The application runs indefinitely until the server process is stopped.

---

## How FastAPI handles requests

For each incoming HTTP request:
1. FastAPI receives the request from the server
2. It determines which endpoint should handle it
3. The corresponding function is executed
4. The function’s return value is converted into an HTTP response
5. The response is sent back to the client

Only the matched endpoint function is executed per request.

---

## Relationship with Starlette

- Starlette provides low-level ASGI and HTTP handling
- FastAPI uses Starlette internally
- FastAPI adds API-focused features on top

This relationship is based on **composition**, not classical inheritance.

---

## Key takeaway
> FastAPI is a framework built on top of Starlette that abstracts HTTP server complexity and allows developers to focus on writing clear API logic instead of low-level protocol handling.
