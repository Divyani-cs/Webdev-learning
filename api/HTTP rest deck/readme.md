# HTTP Standards & REST APIs

## 1. What is HTTP?

**HTTP** = **H**yper**T**ext **T**ransfer **P**rotocol — the language browsers and servers use to talk to each other.

A Mac on Chrome and a Linux server can understand each other because both "speak" HTTP.
- A **protocol** is just a rule book. It decides:
  - how a message is formatted
  - which methods are allowed (GET, POST, etc.)
  - how the result is reported (status codes)

## 2. Client and Server

- **Client** = customer (browser / app / Postman) — **always asks first**.
- **Server** = shopkeeper (Node.js, awake 24×7) — **always answers**.
- The cycle: ask → think → answer → **forget**.

★ The server never starts the conversation — it only replies.

### HTTP is Stateless ★

The server **forgets you** after every single request — like a goldfish's memory.

- Request 1: "Hi, I'm Riya, log me in" → "Welcome!"
- Request 2: "It's me again!" → "Who are you??"

**So how do apps stay logged in?**
Every request carries its own identity — a **cookie** or a **token (JWT)** rides along in the headers. The server checks the pass, not its memory.


## 3. HTTP vs HTTPS ↯

| | HTTP | HTTPS (Secure) |
|---|---|---|
| Security | none — plain text (like a postcard) | TLS encrypted (like a sealed, locked letter) |
| Port | 80 | 443 |
| Use for logins? | Never ✗ | Always ✓ |

A **port** is a numbered door — one computer, many doors, each service has its own (`:80` http, `:443` https, `:3000` for dev).

## 4. What is an API?

**API** = Application Programming Interface — a middleman that lets two programs talk. You never touch the database directly.

**Restaurant analogy:**
- **You** = customer, order "1 paneer pizza"
- **Waiter = API** — carries the order both ways
- **Kitchen = Server** — does the work
- **Pantry = Database** — stores everything
- Food comes back = the **response** (JSON!)

★ You never enter the kitchen yourself — the waiter (API) protects it.

**The round trip:** Client → API → Server → Database, and data flows back up as JSON. ✎ This same trip happens for every tap you make in any app.

### APIs Around You
- **Google Maps** — asks "where am I? fastest route?"
- **Instagram** — feed API fetches posts; like button = `POST /likes`
- **WhatsApp** — message API delivers texts + ✓✓ receipts
- **Paytm/UPI** — bank APIs settle payments
- **Netflix** — recommendation API decides your homepage

## 5. Resources — the "Things"

A **resource** is any **noun** your app cares about: Student, Order, Book, Payment, Course...

✎ Trick: if you can put "a / an / the" before it, it can be a resource.

- All students → `/students`
- One student (roll 99) → `/students/99`
- Their courses → `/students/99/courses`

↯ Nouns go in the **URL**, verbs go in the **method** — never `/getStudents`!

## 6. CRUD — Only 4 Things Ever Happen to Data

| CRUD | HTTP | SQL | Example |
|------|------|-----|---------|
| **C**reate | POST | INSERT | `POST /students` — new admission |
| **R**ead | GET | SELECT | `GET /students/99` |
| **U**pdate | PUT / PATCH | UPDATE | `PATCH /students/99` — fix marks |
| **D**elete | DELETE | DELETE | `DELETE /students/99` — TC issued |

Memory trick: **C**reate **P**osts — **R**ead **G**ets — **U**pdate **P**uts — **D**elete **D**eletes

## 7. The Five HTTP Methods (in detail)

### GET — "give me data" (trick: GET = GIVE)
- Fetches data, never changes anything.
- Like reading a library book — look, don't write in it.
```
GET /students/99 HTTP/1.1
Host: college.com
```
✗ Mistakes: sending a body with GET; using GET to change data.

### POST — "push something new" (trick: POST = new letter in a POSTbox)
- Creates a NEW resource. Server assigns the id.
```
POST /students HTTP/1.1
Content-Type: application/json

{ "name":"Aman", "course":"BCA" }
```
✗ Mistakes: posting to `/students/99` to create; forgetting `Content-Type`; expecting POST to be repeat-safe (2 clicks = 2 orders!).
Success code is **201 Created**, not 200.

### PUT — "replace the whole thing" (trick: PUT a new one in its place)
- Replaces the resource **entirely**. Sending only 1 field wipes the rest!
```
PUT /students/99 HTTP/1.1
{ "name":"Riya S", "course":"MCA", "year": 2 }
```
✓ **Idempotent** — doing it 10 times gives the same result. Safe to retry.

### PATCH — "fix just this part" (trick: a patch on jeans)
- Updates SOME fields, leaves the rest untouched.
```
PATCH /students/99 HTTP/1.1
{ "course":"MCA" }
```
↯ PUT = repaint the whole wall. PATCH = touch up one spot.

### DELETE — "destroy it" (trick: DELETE = DESTROY)
- Removes the resource.
```
DELETE /students/99 HTTP/1.1
Authorization: Bearer <token>
```
✗ Mistakes: `DELETE /students` (deletes the whole collection!); no auth check.
✓ Idempotent — deleting twice still leaves it gone (2nd call returns 404).

## 8. URL Anatomy

```
https :// college.com :443 /students /99 ?course=bca #marks
protocol    host         port  path      id   query      fragment
```

| Part | Meaning |
|------|---------|
| Protocol | how to travel (secure!) |
| Host | which building (DNS finds its IP) |
| Port | which door (hidden by default) |
| Path → Resource | which shelf — the noun! |
| ID | exactly which one |
| Query Params | extra filters, `?a=1&b=2` style |
| Fragment | scroll position — never sent to the server! |

House trick: protocol = vehicle, host = building, port = gate, path = room, query = "knock twice!"
↯ Path picks the thing, query filters it.

## 9. Anatomy of a Request (the envelope)

1. **Start line** — `POST /students HTTP/1.1` (method + URL + version)
2. **Headers** — labels on the envelope:
   ```
   Host: college.com
   Content-Type: application/json
   Authorization: Bearer eyJhbG...
   Cookie: session=abc123
   ```
3. **Blank line** — separates headers from body
4. **Body** — the actual parcel: `{ "name": "Aman", "course": "BCA" }`

★ GET & DELETE usually have NO body — just the envelope.


## 10. Anatomy of a Response

1. **Status line** — `HTTP/1.1 200 OK`
2. **Headers** — `Content-Type`, `Content-Length`, `Set-Cookie`, `Cache-Control`
3. **Body** — the actual data (JSON)

**Why JSON?** ★
- JavaScript Object Notation — Node speaks it natively
- Human-readable, machine-parseable
- Lighter than XML
- Every language can parse it



## 11. Status Codes — the server's mood ★↯

Rhyme: **1** wait • **2** great • **3** relocate • **4** YOUR mistake • **5** server's fate

### 1xx — "hold on…" (informational)
- **100 Continue** — "go ahead, send the rest" (used before big uploads)
- **101 Switching Protocols** — upgrading HTTP to WebSocket (used by chat apps)

### 2xx — "here you go ✓" (success)
- **200 OK** — done, here it is
- **201 Created** — new thing made (↯ POST should return 201, not 200!)
- **202 Accepted** — got your order, cooking it later (queued jobs)
- **204 No Content** — done, nothing to show (perfect after DELETE)

### 3xx — "go there →" (redirection)
- **301 Moved Permanently** — shop shifted forever
- **302 Found** — temporarily somewhere else
- **304 Not Modified** — your saved copy is still fresh, use it
- **307 / 308** — like 302/301 but the method must stay the same

### 4xx — "YOU messed up" (client error)
- **400 Bad Request** — gibberish form / broken JSON
- **401 Unauthorized** — no ID card, not logged in
- **403 Forbidden** — logged in, but still not allowed
- **404 Not Found** — no such page (typo in URL)
- **405 Method Not Allowed** — right door, wrong action
- **408 Request Timeout** — you took too long
- **409 Conflict** — duplicate data (e.g. same email twice)
- **410 Gone** — deleted forever (stronger than 404)
- **415 Unsupported Media Type** — a file type the server can't handle
- **422 Unprocessable Entity** — valid JSON, but nonsense values
- **429 Too Many Requests** — slow down!

Trick: 401 = who are you? 403 = I know you… still no! 404 = forgot to check the URL.

### 5xx — "MY fault, sorry" (server error)
- **500 Internal Server Error** — kitchen's on fire (crash in your code)
- **501 Not Implemented** — we don't serve that yet
- **502 Bad Gateway** — the middleman got garbage back
- **503 Service Unavailable** — closed for maintenance / overloaded
- **504 Gateway Timeout** — the kitchen never answered in time

**5-line summary:** 1xx hold on → 2xx here you go → 3xx go there → 4xx you messed up → 5xx I messed up.

## 12. REST Naming Rules — write URLs like a pro

| ✗ Wrong | ✓ Right |
|---------|---------|
| `GET /getStudents` | `GET /students` |
| `POST /createNewStudent` | `POST /students` |
| `GET /student` | `GET /students` (always plural) |
| `GET /Students_List` | `GET /students` (lowercase, kebab-case) |
| `POST /students/delete/99` | `DELETE /students/99` |
| `GET /students/99/get-courses` | `GET /students/99/courses` |

★ **Golden rule:** URL = noun (plural). Method = verb.

## 13. Versioning & Consistency

- **Versioning** keeps old apps working: `/api/v1/students` stays as-is, `/api/v2/students` ships the new shape (like textbook editions).
- **Safe methods:** GET (+ HEAD, OPTIONS) — window shopping, look don't touch.
- **Idempotent methods:** GET, PUT, DELETE.
- **NOT idempotent:** POST — every press counts, like a doorbell. (Networks retry failed requests, so retrying POST can double-charge a payment!)

**Consistency checklist ✎**
- [ ] Plural nouns everywhere: `/students`, `/courses`, `/orders`
- [ ] Lowercase + kebab-case: `/course-modules`
- [ ] Filters in query: `/students?year=2&sort=name`
- [ ] Version prefix: `/api/v1/…`
- [ ] Right status codes: 201, 204, 404, 422…
- [ ] Same JSON shape for every error

## 14. HTTP Facts — History & Plumbing

- Tim Berners-Lee invented HTTP (+ HTML + the first browser!) in 1989–91 at CERN, to share physics papers.
- **HTTP/1.1 (1997)** — added keep-alive (reuse one connection for many requests).
- **HTTP/2 (2015)** — multiplexing (many requests in one pipe at once).
- **HTTP/3 + QUIC** — runs on UDP, faster on flaky mobile networks (YouTube uses it).
- **DNS comes first** — the internet's phonebook (`college.com` → an IP address). No DNS answer = no HTTP at all.
- **TCP handshake + TLS** happen before any HTTP byte moves (SYN → SYN-ACK → ACK, then TLS key exchange).

## 15. HTTP Facts — Speed & Identity

- **Caching** — `Cache-Control: max-age=3600` means "keep this for 1 hour, don't ask again."
- **ETag** — a fingerprint for data; if unchanged, server replies `304 Not Modified` and resends nothing.
- **Cookie vs Session vs JWT** ↯
  - Cookie = a note in your pocket (browser)
  - Session = register at reception (server remembers)
  - JWT = a tamper-proof wristband (server checks the seal, remembers nothing) — this is what cures statelessness
- **Compression** — `Accept-Encoding: gzip, br` shrinks the response (free speed).
- **CDN** — copies of files stored near you (like a local shop) so content loads faster from a nearby city instead of far away.

## 16. One Click, Full Journey ↯

```
browser → DNS → internet (TCP+TLS) → API gate (auth/rate limit)
→ Node server (routing) → controller (validation) → service (logic)
→ database → JSON response → browser paints the page
```

**Where errors show up at each stop:**
- DNS fails → "server not found"
- Gate says no → 401 / 403 / 429
- Controller rejects → 400 / 422
- Service crashes → 500
- DB is slow → 504

---

## 🎯 Key Takeaways

1. **HTTP is stateless** — the server forgets you; tokens/cookies carry your identity.
2. **HTTPS always for logins** — HTTP is a postcard, HTTPS is a sealed letter.
3. **URL = noun, method = verb** — never put a verb in your URL.
4. **Read the status code first** — 2xx good, 4xx your mistake, 5xx server's mistake.
5. **GET & DELETE are idempotent, POST is not** — never assume repeating POST is safe.
6. **PUT replaces everything, PATCH updates only what you send.**
7. **JSON is the universal data format** for requests and responses.
