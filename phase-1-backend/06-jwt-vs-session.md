# Authentication & Security — JWT vs Sessions

These notes cover session-based authentication, JWT internals, common security attacks (CSRF, XSS), token design, and HS256 vs RS256 — written from a backend / production perspective.

---

## 1. Why Authentication Exists

HTTP is stateless — every request is independent.

Authentication answers:
**“Who is making this request?”**

Two common approaches:
- Sessions → server remembers the user
- JWT → client carries proof of identity

---

## 2. Session-Based Authentication

### How Sessions Work

1. User logs in  
2. Server creates a session record (memory / Redis / DB)  
3. Server sends a session ID to client (cookie)  
4. Browser automatically sends cookies on every request  
5. Server looks up the session → user identified  

### Key Characteristics

- Login state stored on the server
- Easy logout (delete session)
- Requires shared storage (Redis) to scale
- Browser auto-sends cookies

---

## 3. JWT-Based Authentication

### What is JWT?

JWT = JSON Web Token

A JWT is:
- Signed (prevents tampering)
- Stateless
- Not encrypted

JWT represents a verifiable snapshot of identity, not a session.

---

## 4. JWT Structure

A JWT has three Base64URL-encoded parts:

header.payload.signature

---

### 4.1 Header

Stores metadata:
- typ → JWT
- alg → signing algorithm (HS256 / RS256)

Header is readable by anyone.

---

### 4.2 Payload

Stores claims:
- User identity (sub, user_id)
- Roles / scopes
- Timestamps (iat, exp)

Important notes:
- Payload is NOT secret
- Anyone can decode it
- Never store sensitive data (passwords, secrets)

---

### 4.3 Signature

The signature does NOT store data.

It is cryptographic proof created by signing:

base64(header) + "." + base64(payload)

Using:
- A secret key (HS256), or
- A private key (RS256)

The result is Base64URL-encoded and becomes the third part of the JWT.

What the signature guarantees:
- Token was issued by our server
- Header and payload were not modified

Signature provides integrity and authenticity, not secrecy.

---

## 5. Why JWT Is Not Secret

- Header and payload are Base64 encoded
- Base64 is NOT encryption
- Anyone can decode JWT contents

JWT is signed, not encrypted.

---

## 6. CSRF vs XSS

### CSRF — Cross-Site Request Forgery

How it works:
- Browser automatically sends cookies
- Attacker tricks user into triggering a request
- Server thinks request is legitimate

Why sessions are vulnerable:
- Cookies are auto-sent by browser

JWT in Authorization header is CSRF-safe because:
- Headers are NOT auto-sent by browser

---

### XSS — Cross-Site Scripting

How it works:
- Attacker injects malicious JavaScript into a site
- JavaScript runs in the user’s browser

Impact:
- JavaScript can read localStorage / sessionStorage
- JWT stored in localStorage can be stolen

Sessions with httpOnly cookies are safer because:
- JavaScript cannot read httpOnly cookies

---

## 7. Storage vs Attacks Summary

| Storage Method | CSRF Risk | XSS Risk |
|---------------|----------|----------|
| Session cookie (httpOnly) | Yes | No |
| JWT in localStorage | No | Yes |
| JWT in httpOnly cookie | Yes | No |

---

## 8. Access Token vs Refresh Token

### Access Token
- Short-lived (minutes)
- Sent on every request
- Limits damage if stolen

### Refresh Token
- Long-lived (days / weeks)
- Used only to obtain new access tokens
- Stored securely
- Rotated to prevent reuse

---

## 9. HS256 vs RS256

### HS256 (Symmetric)
- Same secret signs and verifies
- Simple and fast
- Secret must be shared across services

Used in:
- Single backend apps
- Monoliths

---

### RS256 (Asymmetric)
- Private key signs
- Public key verifies
- Private key never leaves auth server

Used in:
- Microservices
- OAuth providers
- Large distributed systems

---

## 10. Final Mental Models

- Sessions store login state on the server
- JWT carries signed identity on the client
- CSRF abuses browser auto-sending behavior
- XSS abuses injected JavaScript
- JWT signature protects integrity, not secrecy
- Private key signs, public key verifies (RS256)
