# HTTP Lifecycle

## What this concept is about
This note explains how an HTTP request travels from a client to a server and how the server processes it and sends a response back.

---

## What HTTP solves
HTTP defines a standard way for a **client** (browser, app, script) to communicate with a **server** over a network.

It answers one simple question:
> How does one machine ask another machine for data and get a response?

---

## HTTP Request (Client → Server)

An HTTP request is a **structured message** sent by the client to the server.

A request always contains:

### 1. Method (what action to perform)
Examples:
- `GET` → fetch data
- `POST` → send data
- `PUT` / `PATCH` → update data
- `DELETE` → remove data

The method tells the server **what kind of operation** is expected.

---

### 2. Path (what resource)
Example:


The path tells the server **which resource** is being requested.

---

### 3. Headers (extra information)
Headers provide **metadata** about the request.

Examples:
- authentication information
- content type
- client details

Headers help the server understand **how to process the request**.

---

### 4. Body (optional data)
- Present mainly in `POST`, `PUT`, `PATCH`
- Contains the actual data sent by the client
- Example: user details, form data, JSON payload

---

## HTTP Response (Server → Client)

An HTTP response is the server’s **answer** to a request.

A response contains:

### 1. Status code (what happened)
Examples:
`/users/123`

- `200` → success
- `400` → client error
- `404` → resource not found
- `500` → server error

Status codes tell the client **whether the request succeeded or failed**.

---

### 2. Headers
Response headers describe:
- response type
- caching rules
- server metadata

---

### 3. Body (result or error)
- Contains requested data on success
- Contains error details on failure

---

## HTTP Lifecycle (step by step)

1. Client creates and sends an HTTP request.
2. Server receives the request.
3. Server parses method, path, headers, and body.
4. Server performs required work (validation, data fetching, logic).
5. Server creates an HTTP response.
6. Server sends the response back to the client.
7. Client receives and processes the response.

The lifecycle ends after the response is sent.

---

## Important rules

- The **client always initiates** the HTTP lifecycle.
- One request always results in **exactly one response**.
- The server cannot send a response without a request.
- HTTP is **stateless by default** — each request is independent.

---

## Final takeaway

> HTTP is a strict request–response protocol where the client sends a structured request and the server replies with exactly one structured response.
