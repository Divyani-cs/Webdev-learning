# Introduction to Node.js

## 1) What is Node.js?

Node.js is a **JavaScript runtime** built on Chrome's **V8 Engine**. It lets you run JavaScript **outside the browser** — on a server — so you can build fast, scalable, real-time applications.

> **Analogy:** Think of JavaScript as a language that used to only be spoken *inside* one room (the browser). Node.js built a translator that lets that same language be spoken *anywhere* — on servers, on your laptop, even in scripts. Same language, brand new stage.

### Key Idea
Use JavaScript **everywhere** — Frontend + Backend = **Full JavaScript Stack**. One language, both sides of the app.

---

## 2) Why Was It Created?

Node.js was created by **Ryan Dahl in 2009** to solve one specific problem: handling a **huge number of concurrent connections** efficiently — especially for I/O-heavy apps like chats, streaming, and APIs.

> **Analogy:** Imagine a restaurant with only ONE waiter (single thread). The old way: the waiter takes an order, walks to the kitchen, and just **stands there waiting** until the food is ready — no other table gets served in the meantime. That's blocking.
>
> Node.js's way: the waiter takes the order, **hands it to the kitchen, and immediately moves to the next table.** When the food is ready, the kitchen calls the waiter back. One waiter, but nobody waits around doing nothing. That's **non-blocking I/O** — the core idea behind Node.js.

---

## 3) Features of Node.js

| Feature | What it Means |
|---|---|
| **Built on V8 Engine** | Same engine that powers Chrome — very fast JS execution |
| **Non-blocking, Asynchronous I/O** | Doesn't wait around for slow tasks (file read, DB call) |
| **Event-driven Architecture** | Code reacts to events (a request coming in, a file finishing) |
| **Single-threaded (with Event Loop)** | One main thread, but handles many tasks using the Event Loop |
| **Cross-platform** | Runs on Windows, macOS, Linux |
| **NPM Ecosystem** | Massive library of ready-made packages |
| **Highly Scalable & Lightweight** | Handles thousands of connections without heavy memory use |

---

## 4) Advantages of Node.js

- ✔️ High Performance
- ✔️ Scalable
- ✔️ Full-Stack JavaScript (same language, frontend + backend)
- ✔️ Large Ecosystem (NPM)
- ✔️ Perfect for Real-time Applications
- ✔️ Great for Microservices & APIs
- ✔️ Active Community & Frequent Updates

---

## 5) Real-World Use Cases

| Use Case | Examples |
|---|---|
| Real-time Chat Apps | WhatsApp Web, Slack |
| Streaming Services | Netflix, YouTube |
| E-commerce Platforms | Amazon, eBay |
| REST APIs & Web Services | Almost every modern backend |
| Data-intensive Applications | Live dashboards, analytics |
| IoT & Microservices | Smart devices, distributed systems |

---

## 6) Node.js Architecture — How a Request Actually Flows

```
   CLIENT                NODE.JS APPLICATION           NODE.JS RUNTIME
┌──────────┐   HTTP     ┌──────────────────┐    ┌───────────────────────────┐
│ Browser/ │  Request   │                  │    │   V8 Engine   Event  libuv │
│ Mobile/  │ ─────────► │   Node.js App    │───►│  (JS runs)  ◄─ Loop ─►(I/O)│
│ API      │            │       (JS)       │    │                            │
│ Client   │ ◄───────── │                  │◄───│                            │
└──────────┘   HTTP     └──────────────────┘    └─────────────┬──────────────┘
              Response                                          │ Non-blocking I/O
                                                                  ▼
                                                        ┌───────────────────┐
                                                        │   OS / System      │
                                                        │ (File System,      │
                                                        │  Network, Database)│
                                                        └─────────┬──────────┘
                                                                  │
                                                                  ▼
                                                        ┌───────────────────┐
                                                        │  Callback Queue    │
                                                        │ (Completed Tasks)  │
                                                        └───────────────────┘
```

> **Analogy:** The **Event Loop** is like a single traffic controller at a busy junction. It doesn't drive any car itself — it just keeps checking "is this lane clear? send the next car through." Cars (tasks) that need a long detour (slow I/O like a DB call) get sent off to a side road (libuv/OS), and rejoin the main road only when they're done and the junction is clear.

---

## 7) Remember / Interview Facts / Common Mistakes

### Remember
Node.js uses an **Event Loop** to handle thousands of connections using a **single thread**.

### Interview Fact
Node.js is **NOT a language**. It is a **runtime environment** for executing JavaScript outside the browser — on the server.

###  Common Mistake
**Blocking operations** (like `fs.readFileSync`, heavy CPU-bound loops) can block the Event Loop and hurt performance for *everyone* using the app — not just the current request.

> **Analogy:** One blocking task is like that same single waiter deciding to personally cook one customer's meal from scratch while every other table just sits there, ignored. In Node.js, one bad blocking line of code can freeze the whole server for all users, not just one.

---

# Modules in Node.js

## 1) What Are Modules?

A module is a **reusable piece of code** that encapsulates related functionality. In Node.js, **every file is automatically a module** — it has its own private scope.

Modules help with:
- Organizing code
- Reusability
- Maintainability
- Avoiding polluting the global scope

> **Analogy:** Think of modules like **Lego blocks**. Each block (file) is self-contained and does one job well. You don't rebuild the whole car every time — you just snap in the block you need, wherever you need it.

---

## 2) CommonJS — Node's Default Module System

Node.js uses **CommonJS** by default. Every file is treated as its own module with its own private scope — variables in one file don't leak into another.

```
   a.js  ◄─── require() ───  b.js
   (module)  ─── exports ──► (module)
```

> **Analogy:** Imagine two neighbors living in separate houses (files). By default, nothing inside one house is visible from the street. If a neighbor wants to share something (a function, a value), they have to deliberately put it **outside their door** (`module.exports`). Only then can the other neighbor **come and pick it up** (`require()`).

---

## 3) `require()` — Importing a Module

The `require()` function is used to import a module — built-in, third-party, or your own local file.

```js
const fs = require('fs')       // built-in module
const path = require('path')   // built-in module
const myModule = require('./myModule')  // local module
```

⭐ `require()` is **synchronous** — it returns the exported object immediately, blocking until the file is loaded.

---

## 4) `module.exports` — Exporting From a Module

Used to export values (functions, objects, variables) from a file so other files can use them.

```js
// math.js
function add(a, b) {
  return a + b
}
function subtract(a, b) {
  return a - b
}

module.exports = { add, subtract }
```

✅ `module.exports` can export one object — but that object can hold multiple functions, classes, or values.

---

## 5) Importing Local Modules

Use `require()` with a relative or absolute path to bring in your own files.

```js
// app.js
const math = require('./math')          // ./ means current folder
const { add, subtract } = require('./math')

console.log(math.add(5, 3))       // 8
console.log(subtract(5, 3))       // 2
```

> **Tip:** Use `./` for the same folder and `../` to go one folder back.
> ```
> ../utils/helper.js   -> same folder level
> ../config/db.js      -> one level up
> ```

---

## 6) Built-in Modules (Core Modules)

Node.js ships with many modules out of the box — **no installation needed**.

| Module | Purpose | Module | Purpose |
|---|---|---|---|
| `fs` | File system operations | `url` | Parse & format URLs |
| `path` | Work with file & directory paths | `events` | Work with EventEmitter |
| `os` | Operating system info | `buffer` | Handle binary data |
| `http` | Create HTTP server & client | `stream` | Work with readable/writable streams |
| `https` | Secure HTTP (SSL/TLS) | `util` | Utility functions |

> **Analogy:** Built-in modules are like the tools that come pre-installed in a new house — plumbing, electricity, a kitchen. You don't have to build these from scratch; you just `require()` them and start using them.

### Example: Using a Built-in Module (`fs`)

```js
const fs = require('fs')

fs.readFile('hello.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('Error:', err)
    return
  }
  console.log('File Content:', data)
})
```

✅ Always handle errors while working with modules.

---

## Key Takeaways

- ✔️ Every file in Node.js is a module
- ✔️ Use `require()` to import modules
- ✔️ Use `module.exports` to export
- ✔️ Built-in modules save time & effort — no installation needed

## Interview Fact

Node.js uses **CommonJS** by default. **ES Modules** (`import`/`export`) are also supported in modern Node.js — using the `.mjs` extension or `"type": "module"` in `package.json`.

## Common Mistakes

- Forgetting to export using `module.exports`
- Wrong path in `require()` leading to a `"Cannot find module"` error
