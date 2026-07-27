# Backend Development 

## Topics Covered

- Express.js Basics
- Request (`req`) and Response (`res`)
- Routing
- Middleware
- npm Fundamentals
- PowerShell
- Project Structure
- First Route in Express
- Models
- Controllers
- Postman

---

## 1. What is Express.js?

Node.js can create a web server on its own, but it takes a lot of manual code.

**Express.js** is a framework built on top of Node.js that makes backend development easier and faster. Instead of writing everything from scratch, Express gives you ready-made functions for:

- Handling URLs
- Managing requests
- Sending responses
- Creating routes
- Using middleware

**Without Express**, Node.js has to manually: read the URL, check the HTTP method, parse incoming data, and build the response — more code, more work.

**With Express**, all these common tasks are handled automatically, so you can focus only on your app's actual logic.

## 2. What is a Framework?

A framework is a ready-made toolkit that helps you build apps faster, instead of starting from zero.

**Examples:** Express.js, Fastify, Koa, NestJS

Express is one of the most popular frameworks for learning backend development.

## 3. Basic Route in Express

```javascript
app.get("/hello", (req, res) => {
    res.json({
        message: "Hello"
    });
});
```

- `app.get()` → creates a GET route
- `/hello` → the URL
- `req` → information the client sends
- `res` → used to send data back

## 4. Understanding the Request (`req`)

The **request object** carries everything the client sends to the server.

| Property | What it's for | Example |
|----------|-----------------|---------|
| `req.params` | Dynamic values inside the URL | `/users/5` → `req.params.id` → `5` |
| `req.query` | Query parameters | `/users?limit=10` → `req.query.limit` → `10` |
| `req.body` | Data sent in a POST/PUT request | `{ "name": "John" }` |
| `req.method` | The HTTP method used | `GET`, `POST`, `PUT`, `DELETE` |
| `req.url` | The requested URL | `/documents` |

⚠️ **Important:** `req.body` only works after you add:
```javascript
app.use(express.json());
```

## 5. Understanding the Response (`res`)

The response object sends data back to the client.

| Method | What it does |
|--------|----------------|
| `res.send()` | Sends plain text or HTML |
| `res.json()` | Sends JSON data |
| `res.status(404)` | Sets the HTTP status code |

Example:
```javascript
res.status(404).json({
    error: "Not Found"
});
```

## 6. Routing

Routing decides which function should run for a specific URL.

```
GET /hello → Route matches → Controller runs → Response is returned
```

**Flow:** Client → Route → Controller → Response

## 7. Middleware

Middleware is a function that runs **before** the request reaches the controller. It can:

- Check authentication
- Validate data
- Log requests
- Modify request data
- Stop invalid requests

**Structure:**
```javascript
function myMiddleware(req, res, next) {
    // work here
    next();
}
```

`next()` passes control to the next middleware or controller. If `next()` is not called, the request just stops there.

**Flow:** Client → `express.json()` → Logger Middleware → Controller → Response

### Why Middleware Order Matters

✅ **Correct order:**
```javascript
app.use(express.json());
app.use(logger);
app.use(routes);
```

❌ **Wrong order:**
```javascript
app.use(routes);
app.use(express.json());
```

Result: `req.body` becomes `undefined`.

✎ Always place `express.json()` **before** your routes.

## 8. Logger Middleware

A logger prints information about every request — helpful for debugging.

```javascript
function logger(req, res, next) {
    console.log(req.method);
    console.log(req.url);
    next();
}
```

Example output:
```
GET
/api/documents
```

## 9. npm (Node Package Manager)

**npm** has two meanings:
1. The package registry
2. The command line tool

It's used to install and manage packages.

### package.json

Every Node project has a `package.json` file storing: project name, version, scripts, dependencies, and project info.

Create it with:
```bash
npm init -y
```

### Installing Packages

```bash
npm install express                # install Express
npm install --save-dev nodemon     # install Nodemon (dev only)
npm install -g nodemon             # install globally
npm uninstall express              # remove a package
```

### Local vs Global Installation

- **Local** — available only inside the current project (`npm install express`)
- **Global** — available across the whole computer (`npm install -g nodemon`)

### Dependencies vs DevDependencies

| Dependencies | DevDependencies |
|---------------|-------------------|
| Needed when the app runs in production | Only needed while developing |
| e.g. Express, Mongoose | e.g. Nodemon, testing tools |

### Semantic Versioning (SemVer)

```
MAJOR . MINOR . PATCH
  4   .  22   .  2
```

- **Major** — breaking changes, old code may stop working
- **Minor** — new features added, old code usually keeps working
- **Patch** — bug fixes, nothing major changes

### Version Symbols

| Symbol | Meaning |
|--------|---------|
| `^4.18.0` | Any newer version inside version 4 (e.g. 4.22.2 is fine) |
| `~4.18.0` | Only patch updates allowed |
| `4.18.0` | Exactly this version, no updates |

### package-lock.json

Stores the exact package versions installed on your computer.

**Benefits:** same versions for everyone, fewer bugs, consistent project setup.
✎ Always commit this file to Git.

### Scoped Packages

Packages that belong to an organization, e.g. `@types/node`, `@babel/core`. The part before `/` is the scope.

### Package Visibility

- **Public** — anyone can install it
- **Private** — only authorized users can access it

### Alternatives to npm

- Yarn
- pnpm
- Bun

### Useful npm Commands

```bash
npm init -y            # create package.json
npm install            # install all dependencies
npm install express    # install a package
npm install -g nodemon # install globally
npm uninstall express  # remove a package
npm outdated           # check outdated packages
```

## 10. Why PowerShell?

PowerShell is a modern command-line tool. It offers better commands and scripting support, and is commonly used in backend development.

## 11. Standard Backend Project Structure

```
project/
│
├── app.js
├── routes/
├── controllers/
├── models/
├── middleware/
├── package.json
├── package-lock.json
└── node_modules/
```

### `app.js`
The main entry point of the application.
- Creates the Express app
- Registers middleware
- Registers routes
- Starts the server

### `routes/`
Decides **which URL calls which controller.**
```
GET /documents → documentController
```

### `controllers/`
Handle requests.
- Receive `req`
- Call the model
- Send the response

Controllers know `req` and `res`, but should **not** directly read or write files.

### `models/`
Manage data.
- Read data
- Write data
- Update data
- Delete data

Models should **never** use `req`, use `res`, or send responses.

### `middleware/`
Runs before controllers. Examples: Logger, Authentication, Validation.

### Backend Request Flow

```
Client → Route → Middleware → Controller → Model → Database
       → Controller → Response
```

## 12. First "Hello" Route

URL:
```
GET /api/documents/hello
```

**Project flow:**
```
app.js → routes/index.js → documentRoutes.js → documentController.js → Response
```

**Output:**
```json
{
    "message": "Hello from documents"
}
```

## 13. Models (recap)

Models are responsible **only** for data.

Should:
- Read data
- Write data
- Find records
- Delete records

Should never:
- Use `req`
- Use `res`
- Return HTTP status codes

## 14. Controllers (recap)

Controllers connect routes with models.

Responsibilities:
- Receive request
- Call the model
- Check the returned data
- Send the response

Controllers know `req` and `res`, but should not directly access files or database logic.

### Model vs Controller

| Model | Controller |
|-------|------------|
| Handles data | Handles request |
| Reads/Writes data | Sends response |
| Doesn't know `req` or `res` | Knows `req` and `res` |
| No status codes | Returns status codes |

## 15. Postman

Postman is used to test backend APIs. With it, you can:

- Send GET requests
- Send POST requests
- Send JSON data
- Check responses
- Save API collections

⚠️ The browser address bar can only send **GET** requests. To test **POST**, **PUT**, and **DELETE**, you need Postman.

---

## Important Points 

- Express makes backend development much easier.
- `req` contains client data.
- `res` sends data back.
- Middleware runs before controllers.
- Middleware order is very important.
- Always call `next()` inside middleware.
- `express.json()` must be above your routes.
- `package.json` stores project information.
- `package-lock.json` stores exact installed versions.
- Controllers handle requests.
- Models handle data.
- Controllers and models should not do each other's work.
- Postman is essential for testing APIs.

## Quick Revision

```
Client → Express → Middleware → Route → Controller
       → Model → Database → Controller → Response
```

---

