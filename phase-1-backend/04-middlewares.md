# Middleware in FastAPI

## What this concept is about
This note explains what middleware is in FastAPI, why it exists, and how it allows code to run before a request reaches the route handler and after the response is generated.

---

## What problem middleware solves
In real backend applications, some logic needs to run for every request:
- logging
- measuring response time
- adding security headers
- handling CORS
- rate limiting
- global exception handling

Without middleware, this logic would be:
- repeated across multiple routes
- inconsistently applied
- mixed with business logic

Middleware solves this by centralizing cross-cutting concerns in one place.

---

## What middleware is in FastAPI
Middleware is code that wraps the entire request–response lifecycle.

It:
- runs before the request reaches the router
- runs again after the route handler finishes
- can modify, observe, or block requests and responses

Middleware does not know business context and should not contain business logic.

---

## Basic middleware example

	from fastapi import FastAPI, Request
	import time

	app = FastAPI()

	@app.middleware("http")
	async def timing_middleware(request: Request, call_next):
		start = time.time()
		response = await call_next(request)
		response.headers["X-Process-Time"] = str(time.time() - start)
		return response

### What happens internally
- A request arrives at the application
- The middleware starts executing
- call_next forwards the request to the router
- The route handler processes the request
- A response is returned to the middleware
- The middleware modifies the response
- The final response is sent to the client

Execution order:
middleware (before) → route handler → middleware (after)

---

## Middleware vs Dependency Injection

Middleware:
- runs for every request
- executes outside business logic
- does not know which route is called
- used for infrastructure-level concerns

Dependency Injection:
- runs only when explicitly declared
- is route-aware
- used for business-related shared logic

They solve different problems and are often used together.

---

## Middleware for logging (real use case)

Example logging middleware:

	from fastapi import Request
	import logging
	import time

	@app.middleware("http")
	async def logging_middleware(request: Request, call_next):
		start = time.time()
		response = await call_next(request)
		duration = time.time() - start
		logging.info(
			f"{request.method} {request.url.path} {response.status_code} {duration}"
		)
		return response

### Why this is useful
- logging is applied consistently
- no duplication across routes
- performance metrics are easy to collect

---

## Middleware execution order
Middleware order matters.

Rules:
- request execution flows from top to bottom
- response execution flows from bottom to top
- incorrect ordering can cause security issues

Example:
- authentication or rate limiting middleware should run before logging sensitive data

---

## Common mistakes with middleware
- performing database queries
- implementing business logic
- doing per-user authorization
- running heavy computations
- blocking I/O operations

Middleware should always be lightweight and fast.

---

## Where middleware is commonly used
Middleware is typically used for:
- request and response logging
- CORS handling
- rate limiting
- security headers
- correlation IDs
- global exception wrapping

Most production APIs rely on middleware for observability and safety.

---

## Key takeaway
Middleware in FastAPI provides a clean way to execute global logic around every request and response, keeping infrastructure concerns separate from business logic and making APIs production-ready.
"""