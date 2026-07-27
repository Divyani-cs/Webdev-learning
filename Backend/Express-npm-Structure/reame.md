# Express, npm & Structure 

## 1. Express:

Node can make a web server on its own. It is just painful. You have to read the URL yourself, check the method yourself, set headers yourself.

> **Express** — A framework. It sits on top of Node and does the boring parts for you: reading the URL, matching the method, parsing the body, sending JSON. You write the interesting part.

The whole of Express is basically this one idea:

```js
app.METHOD(PATH, HANDLER)

app.get('/hello', (req, res) => {
  res.json({ message: 'hi' });
});
```

*"When a GET comes to `/hello`, run this function."*

### The Two Things Every Handler Gets

| Name | What it is |
|------|------------|
| `req` | The **request**. What the client sent you. |
| `res` | The **response**. What you send back. |

**Useful bits of `req`:**
- `req.params` — the `:id` part of the URL
- `req.query` — the `?limit=5` part
- `req.body` — the JSON they sent (only if `express.json()` is on)
- `req.method` and `req.url` — GET, POST, and the path

**Useful bits of `res`:**
- `res.json({...})`
- `res.status(404)`
- `res.send()`

---

## 2. Middleware

**Flow:** `request → express.json() → logger → your controller → response`

```js
function myMiddleware(req, res, next) {
  // do something
  next(); // done, carry on
}
```

### Example: `middleware/logger.js`

```js
// Our own middleware. Prints every request that comes in.
function logger(req, res, next) {
  console.log(req.method, req.url);
  next(); // done, carry on to the next thing
}

module.exports = logger;
```

Other common middleware:
- `express.json()` → fills in `req.body`
- `cors()` → allows other origins to call your API
- `express.static()` → serves static files

⚠️ **Order matters.** If `app.use(express.json())` isn't set up before your routes, `req.body` will be `undefined`.

---

## 3. npm

- `npm init -y` → makes `package.json`
- `npm install` → installs everything listed in `package.json`
- `npm install express` → adds a package, puts it in `./node_modules/`
- `npm install -g nodemon` → installs a tool globally
- `require()` → loads a module into your file
- Add `node_modules/` to `.gitignore` — never commit it

### Dependencies vs DevDependencies

```bash
npm install express             # -> dependencies
npm install nodemon --save-dev  # -> devDependencies
npm install --production        # skips devDependencies
```

### Semantic Versioning (SemVer)

```
4  .  22  .  2
MAJOR . MINOR . PATCH
```

| Symbol | Meaning |
|--------|---------|
| `^4.18.0` | Compatible updates — newer minor/patch versions within 4.x.x |
| `~4.18.0` | Only patch updates |
| `4.18.0` | Exact version, no updates |

Example:
```bash
$ npm install express@^4.18.0
```
`package.json` says: `^4.22.2` → actually installed: `4.22.2`

### Scoped Packages

```
express        <- normal package
@types/node    <- scoped package
@babel/core    <- scoped package
```

The part before `/` is the **scope**, e.g. `@types`, `@shorterloop/utils`. Scoped packages can be **public** or **private**.

### Alternatives to npm

- Yarn
- pnpm
- Bun

### Useful npm Commands

| Command | What it does |
|---------|----------------|
| `npm init -y` | make `package.json` |
| `npm install` | install everything in `package.json` |
| `npm install express` | add a package |
| `npm install -g nodemon` | install a tool globally |
| `npm uninstall express` | remove it |
| `npm view express versions` | see every published version |
| `npm outdated` | what is out of date |
| `npm root -g` | where global packages live |

*(Also handy shell commands from class: `ls`, `pwd`, `cat`, `touch`.)*

---

## 4. Project Structure

```
resume-api/
├── app.js           # starts the server
├── routes/          # which URL goes where
├── controllers/     # the function that answers
├── models/          # reading and writing data
└── middleware/      # runs before controllers
```

### `app.js`

```js
const express = require('express');
const routes = require('./routes');

const app = express();

// Middleware: runs before every route
app.use(express.json());

// All our routes live under /api
app.use('/api', routes);

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

### `routes/index.js`

```js
const express = require('express');
const router = express.Router();

router.use('/documents', require('./documentRoutes'));

module.exports = router;
```

### `routes/documentRoutes.js`

```js
const express = require('express');
const router = express.Router();
const controller = require('../controllers/documentController');

router.get('/hello', controller.hello);

module.exports = router;
```

### `controllers/documentController.js`

```js
// GET /api/documents/hello
function hello(req, res) {
  res.json({ message: 'Hello from documents' });
}

module.exports = { hello };
```

### Try It

```
GET http://localhost:3000/api/documents/hello
```

```json
{ "message": "Hello from documents" }
```

### How the Request Travels

```
app.js  →  /api  →  routes/index.js  →  /documents  →
documentRoutes.js  →  /hello  →  controller.hello  →  response
```

---

## 5. Models

Models read and write data — here, from a `data.json` file acting as a fake database.

### `models/documentModel.js`

```js
function findById(id) {
  const data = db.read();
  return data.documents.find(doc => doc.id === id);
}
```

A model never touches `req` or `res` — it only knows about the data (`db.read()`, `fs`, etc.).

### Controller Using the Model

```js
function getOne(req, res) {
  const doc = documentModel.findById(req.params.id);

  if (!doc) {
    return res.status(404).json({ error: 'Document not found' });
  }

  res.status(200).json(doc);
}
```

The **controller** knows `req` and `res`, and calls `res.status()` to reply. The **model** just fetches the data and hands it back.

### Testing a POST Request

```
POST http://localhost:3000/api/documents
```
```json
{ "title": "My CV" }
```

⚠️ This only works if `express.json()` is set up — that's what turns the raw request into `req.body`.

---

## 🎯 Summary

- **routes** — decide which URL calls which controller
- **controllers** — handle `req`/`res`, call the model, send the response
- **models** — only handle data, never touch `req`/`res`
- **middleware** — runs before controllers (logging, parsing JSON, auth, etc.)
- **package.json** — tracks your project's dependencies and scripts
