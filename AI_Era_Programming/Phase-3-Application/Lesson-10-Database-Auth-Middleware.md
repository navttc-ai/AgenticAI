# Lesson 24: Database, Auth, and Middleware

## Purpose

Your API needs to persist real data, verify who's making requests, and let the right callers reach it safely across the network. This lesson covers a real Postgres backend, storing passwords safely, verifying identity with JWTs, and middleware that runs on every request.

## Guiding Questions

1. What's the critical gotcha when connecting a FastAPI app to Neon Postgres through a pooled endpoint?
2. Why is "never trust unverified identity" the same principle behind both password storage and request authentication?
3. What does a JWT actually prove, and what four things does verifying one check?
4. What does middleware let you do that an individual route handler can't do as cleanly?

---

## SQLModel + Neon Setup

### The Idea
Neon is a serverless Postgres provider — real Postgres, not an approximation, that scales down to near-zero cost when idle. Connecting to it correctly requires using the *pooled* endpoint for your running app and the *direct* (non-pooled) endpoint for migrations — mixing them up causes a specific, easy-to-miss failure.

### In Simple Words
Think of the pooled endpoint as a shared taxi service — efficient for lots of short trips, but it doesn't remember which specific driver you had last time. The direct endpoint is your own reserved car — needed for a longer job (like restructuring the whole garage) where continuity matters. Using the shared taxi for the garage restructuring job doesn't fail loudly — it just silently loses track of details you needed.

### Real Example
The specific, documented gotcha: the pooled endpoint silently drops `search_path` — which is why every SQL statement needs to be schema-qualified (`public.notes`, not just `notes`) when running through it:
```python
# Correct: schema-qualified, works safely through the pooled endpoint
async def get_note(db, note_id: int):
    return await db.fetchrow("SELECT * FROM public.notes WHERE id = $1", note_id)
```
The rule in practice: use the pooled connection string for your live application traffic, and the direct connection string specifically for schema migrations. One more thing that looks like a footgun but isn't — Neon's copy-paste connection string includes `channel_binding=require`, which `asyncpg` (not being libpq-based) simply ignores; it connects fine with that parameter left in, no special handling needed.

### The Pitfall
Running schema migrations through the pooled endpoint, or running live app queries against the direct endpoint. Either mismatch works *most* of the time, which makes the failure mode worse — it surfaces as an intermittent, confusing bug (a query that "sometimes" can't find a table it should see) rather than a clear, immediate error.

### Guiding Question (Answer This From Memory Later)
What's the critical gotcha when connecting a FastAPI app to Neon Postgres through a pooled endpoint?

---

## User Management & Password Hashing

### The Idea
A password must never be stored as plain text — it's hashed (run through a one-way function) before storage, so that even if the database is compromised, the actual passwords aren't recoverable. This is the same underlying principle as Lesson 23's "never trust unverified identity," extended from *who's asking* to *what they know*.

### In Simple Words
A hash is like a wax seal pressed once and never un-pressed — you can check whether a fresh press matches an old one, but you can't work backward from the seal to recover the original stamp. Storing a password hash means even someone who steals your entire database can't read anyone's actual password — they'd only have a one-way fingerprint of it.

### Real Example
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(plain_password: str) -> str:
    return pwd_context.hash(plain_password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

# At signup:
stored_hash = hash_password("correct horse battery staple")
# stored_hash looks nothing like the original password — one-way, by design

# At login:
is_valid = verify_password("correct horse battery staple", stored_hash)  # True
```
`verify_password` never reverses the hash to recover the original — it hashes the *login attempt* fresh and compares the two hashes. That's the entire mechanism: comparison without ever needing to store or retrieve the real password.

### The Pitfall
Writing your own hashing scheme, or using a fast general-purpose hash (like plain SHA-256) instead of a password-specific one (like bcrypt). Password hashing needs to be *deliberately slow* to resist brute-force guessing — a fast hash makes it cheap for an attacker to try billions of guesses per second against a stolen database.

### Guiding Question (Answer This From Memory Later)
Why is "never trust unverified identity" the same principle behind both password storage and request authentication?

---

## JWT Authentication

### The Idea
A JWT (JSON Web Token) is a signed claim of who someone is — three parts joined by dots (`header.payload.signature`). Verifying one checks four specific things: the signature is genuine, the token is for *this* server (audience), it came from a trusted issuer, and it hasn't expired.

### In Simple Words
A JWT is a wristband at a venue: the signature is the booth's wax seal pressed into it. The booth keeps its stamp locked away and never lets it leave, so nobody else can forge that exact seal — but it tapes a clear photo of the seal by the door so anyone can *check* a wristband is genuine without ever being able to *create* a fake one. That's the whole trick: verification without the power to forge.

### Real Example
```python
from jose import jwt
from jose.exceptions import JWTError

def verified_claims(token: str) -> dict:
    key = get_signing_key(token)
    try:
        claims = jwt.decode(
            token,
            key,
            algorithms=["RS256"],
            audience=RESOURCE_URL,                          # for THIS server specifically
            issuer=AUTH_ISSUER,                              # from a trusted issuer
            options={"require": ["exp", "sub", "aud", "iss"]},  # must include these facts
        )
    except JWTError as e:
        raise AuthError(f"token rejected: {e}") from e
    return claims   # claims["sub"] is the verified user — nothing came from an unverified source
```
The four checks map directly to the token's core facts: `sub` (subject — who), `aud` (audience — for whom), `iss` (issuer — from whom), `exp` (expiry — until when). A token missing any of these, signed by the wrong issuer, meant for a different audience, or simply expired gets rejected before `claims["sub"]` is ever trusted as real identity.

### The Pitfall
Checking the token's validity somewhere *inside* a route's business logic rather than at the authentication layer itself. If the check only happens deep inside a handler, an unauthenticated request can still reach code paths before the check runs, returning a misleading success status instead of a clean `401` at the door.

### Guiding Question (Answer This From Memory Later)
What does a JWT actually prove, and what four things does verifying one check?

---

## Middleware & CORS

### The Idea
Middleware runs on *every* request before it reaches any specific route handler — the right place for cross-cutting concerns like checking headers, logging, or enforcing CORS (Cross-Origin Resource Sharing) policy, rather than repeating that logic in every individual route.

### In Simple Words
A route handler is one specific employee doing one specific job. Middleware is the security checkpoint everyone passes through before reaching *any* employee — it doesn't care which employee you're headed to, it applies the same check to everyone, once, in one place.

### Real Example
```python
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://myfrontend.com"],   # only this origin may call the API from a browser
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
)
```
Without CORS configuration, a browser blocks a frontend hosted at one domain from calling an API hosted at another — a security default browsers enforce to stop malicious sites from silently calling APIs on a user's behalf. `CORSMiddleware` is how you explicitly and narrowly permit your *own* frontend to do exactly that, applied once for the whole app rather than configured per route.

The same middleware pattern extends beyond CORS — a payment-gating middleware from Lesson 18's territory works identically: it runs before the actual route logic, checking a header and enforcing a rule (like requiring proof of payment) uniformly across every route it's applied to, rather than each route re-implementing that check.

### The Pitfall
Setting `allow_origins=["*"]` "to make CORS errors go away" during development and forgetting to narrow it before shipping. That configuration permits *any* website to call your API from a user's browser — turning a security boundary meant to protect users into one that protects nothing.

### Guiding Question (Answer This From Memory Later)
What does middleware let you do that an individual route handler can't do as cleanly?

---

## Summary

- **SQLModel + Neon**: use the pooled endpoint for app traffic (schema-qualify every query) and the direct endpoint for migrations — mixing them up causes silent, intermittent failures.
- **Password Hashing**: hash passwords one-way with a slow, password-specific algorithm (bcrypt); verify by hashing the login attempt fresh and comparing — never store or reverse the original.
- **JWT Authentication**: a signed token proving identity, verified on four facts — signature, audience, issuer, expiry — checked at the authentication layer, not buried inside route logic.
- **Middleware & CORS**: cross-cutting checks (CORS, payment gating, logging) belong in middleware, applied once to every request, not duplicated inside every individual route.
- **Why Together**: these four give your API a real backend, safely-stored credentials, verified identity per request, and consistent cross-cutting policy — the full production security and data stack.

## Active Recall Questions (Answer Without Looking Back)

**Question 1**: What specifically goes wrong if you run a schema migration through Neon's pooled endpoint instead of the direct one?
**Question 2**: Why does `verify_password` never need to "un-hash" the stored password?
**Question 3**: Name the four facts a JWT verification checks, and what each one confirms.

---

**Sources**: Agent Factory System of Record (via SoR MCP connector), Deploying Agent Harness crash course — Concept 6 and Decision 4 (Neon Postgres, pooled vs. direct endpoint); Connector-Native Apps crash course — Concept 7–8 (identity from verified subject, JWT verification code); AI Identity crash course (JWT wristband/wax-seal analogy, tokens and sessions); Building Agent Factories — Chapter 70: FastAPI for Agents (SQLModel + Neon Setup, User Management & Password Hashing, JWT Authentication, Middleware & CORS).
