# Our Database with Sequelize —

## 1. What We Actually Did Today

We ran nine commands. Each one looks like this:

```bash
npx sequelize-cli model:generate --name User --attributes name:string,email:string
```

And each one created **two** files at once:

- a **model** in `models/`, the JavaScript object your code will talk to
- a **migration** in `migrations/`, the instructions that build the real table

> **Model vs migration, the difference that confuses everyone at first**
> The **migration** is a set of instructions: "make a table called users with these columns." You run it once and the table exists. The **model** is how your running app reads and writes rows in that table every day.
> Migration builds the shelf; model puts things on and off the shelf. One command wrote both, already matching each other.

---
```mermaid
flowchart TD
    A[npx sequelize-cli model generate] --> B[Model file in models folder]
    A --> C[Migration file in migrations folder]
    B --> D[How the app reads and writes rows]
    C --> E[One-time instruction that builds the table]
```


## 2. The Nine Commands, in Order

Run them top to bottom. The order is not random — it follows what depends on what (more on that in section 5).

```bash
npx sequelize-cli model:generate --name User \
  --attributes name:string,email:string,password:string,tier:enum:'{free,pro}',aiCredits:integer

npx sequelize-cli model:generate --name Template \
  --attributes name:string,config:text

npx sequelize-cli model:generate --name Document \
  --attributes title:string,type:enum:'{resume,cover_letter}',userId:integer,templateId:integer

npx sequelize-cli model:generate --name Section \
  --attributes heading:string,position:integer,documentId:integer

npx sequelize-cli model:generate --name Item \
  --attributes content:text,position:integer,sectionId:integer

npx sequelize-cli model:generate --name Version \
  --attributes snapshot:text,label:string,documentId:integer

npx sequelize-cli model:generate --name Application \
  --attributes company:string,role:string,\
status:enum:'{saved,applied,interview,offer,rejected}',userId:integer,documentId:integer

npx sequelize-cli model:generate --name Share \
  --attributes slug:string,documentId:integer

npx sequelize-cli model:generate --name Export \
  --attributes format:enum:'{pdf,docx}',fileUrl:string,documentId:integer,userId:integer
```

### Reading the Command Itself

- `npx sequelize-cli model:generate` — the command that creates a model + migration pair
- `--name User` — the name of the table/model
- `--attributes` — the list of columns, `columnName:type`
- `name:string` — a text column
- `config:text` — a long-text column (like a resume summary)
- `aiCredits:integer` — a whole-number column
- `tier:enum:'{free,pro}'` — a column restricted to a fixed list of values

---

## 3. The Nine Tables, and What Each Holds

| Table | What it stores | Points to |
|-------|------------------|-----------|
| `users` | the account: name, email, password, tier, AI credits | nothing (top of the tree) |
| `templates` | a design a resume can use | nothing |
| `documents` | one resume or cover letter, the central table | users, templates |
| `sections` | a block in a document (Experience, Skills) | documents |
| `items` | one line inside a section (a single job) | sections |
| `versions` | a saved snapshot of a document | documents |
| `applications` | a tracked job application | users, documents |
| `shares` | a public link to a document | documents |
| `exports` | a generated PDF or DOCX record | documents, users |

---

How the Tables Connect — The Relationship Structure

The whole database can be thought of as one connected structure, where an arrow points from a table to the table it depends on. For example, a `section` belongs to a `document`, so `sections` points to `documents`.

```mermaid
flowchart TD
    users --> documents
    templates --> documents
    documents --> sections
    sections --> items
    documents --> versions
    users --> applications
    documents --> applications
    documents --> shares
    documents --> exports
    users --> exports
```

`documents` sits at the center of the schema — almost every other table either points to it directly, or points to something that points to it. `users` sits at the top, since accounts don't depend on anything else in the system.

**Foreign key behavior inside migrations:** a migration for a column like `userId` includes a reference back to its parent table, along with what should happen on update or delete:

```javascript
userId: {
  type: Sequelize.INTEGER,
  references: { model: 'Users', key: 'id' },
  onUpdate: 'CASCADE',
  onDelete: 'CASCADE'
}
```

This is the same `CASCADE` behavior introduced on Day 1 — if a user is deleted, everything referencing that user through `CASCADE` is automatically deleted too, instead of being blocked or left orphaned.

The model files declare the same relationship in JavaScript, using an `associate()` function, for example:

```javascript
Document.belongsTo(models.User, { foreignKey: 'userId' });
Document.hasMany(models.Section, { foreignKey: 'documentId', onDelete: 'CASCADE' });
```

---

### 4. Running Migrations

```
npx sequelize-cli db:migrate            # build every table
npx sequelize-cli db:migrate:undo       # undo the last table
npx sequelize-cli db:migrate:undo:all   # undo everything
npx sequelize-cli db:migrate:status     # see what has run
```

Migrations must run in a specific order, not a random one, because tables depending on other tables (like `documents` depending on `users`) need their parent table to already exist first.

---

## Register API, Password Security, and Async JavaScript

###  The Register API

The register endpoint takes a name, email, and password from the request. It checks that the email is not already in use, saves the new user to the database, and returns a token, so the user is immediately logged in right after signing up — without needing a separate login step.

```mermaid
flowchart TD
    A[Request - name, email, password] --> B{Email already exists}
    B -- Yes --> C[Reject - send error]
    B -- No --> D[Hash the password]
    D --> E[Save new user to database]
    E --> F[Generate token]
    F --> G[Respond - user is now logged in]
```

---

###  Password Encryption

A password is never stored exactly as it was typed. It is hashed first, so what sits in the database is a scrambled string, not the real password.

**Why one-way encryption specifically:** hashing is deliberately one-way. A password can be turned into its scrambled form, but the scrambled form can never be turned back into the original password. Even someone looking directly at the database cannot read anyone's real password.

At login, the newly typed attempt is hashed the same way, and the two scrambled strings are compared — not the two plain passwords. This means that even if a database is ever leaked, it still does not hand over anyone's real password.

> **Analogy — A One-Way Paper Shredder**
> Hashing a password is like feeding a piece of paper into a shredder that always produces the exact same confetti pattern for the exact same original page. Given the confetti, there's no way to reconstruct the original page. But if a new page is shredded and its confetti pattern matches an old one exactly, it proves it was the same original page — without ever needing to see the page itself again.

```mermaid
flowchart LR
    A[Plain password typed at signup] --> B[Hash function - one way only]
    B --> C[Scrambled string stored in database]
    D[Plain password typed at login] --> E[Hash function - same process]
    E --> F{Scrambled strings match}
    F -- Yes --> G[Login allowed]
    F -- No --> H[Login rejected]
```

---

### Promises

**Promise — Definition:** a way to handle an operation that takes time, such as a database call or a request over the internet, without freezing the rest of the code while waiting. Instead of waiting, the code receives a Promise immediately, and the real result arrives later.

A Promise has three states:
- **Pending** — still waiting for the result
- **Resolved** — the operation succeeded
- **Rejected** — the operation failed

`.then` runs when a Promise resolves successfully. `.catch` runs when a Promise is rejected.

```mermaid
flowchart TD
    A[Promise created] --> B[Pending]
    B --> C{Operation finishes}
    C -- Success --> D[Resolved - .then runs]
    C -- Failure --> E[Rejected - .catch runs]
```

---

###  Callback Hell

**Callback hell — Definition:** what happens when one slow task depends on the result of another, which depends on another, and each step is handled using nested callback functions. The code keeps nesting deeper and drifting to the right of the screen with every added step, becoming hard to read and even harder to handle errors in.

> **Analogy — A Chain of Nested Boxes**
> Callback hell is like opening a gift box, only to find another wrapped box inside, then another inside that one, and so on. Each layer has to be fully opened before the next one is even visible, and if something goes wrong in the middle, it's unclear which box actually caused the problem.

---

###  Solving Callback Hell — Promises and Async/Await

**Promises flatten the nesting.** Instead of callbacks nested inside callbacks, a chain of `.then()` calls can be lined up one after another, at the same indentation level.

**Async/await goes a step further.** It lets the slow steps be written as if they run line by line, top to bottom — which reads like ordinary, sequential code, while error handling stays in one single place (a `try/catch` block), rather than being scattered across every nested callback.

```mermaid
flowchart TD
    A[Callback hell - deeply nested, hard to read] --> B[Promises - flat .then chain]
    B --> C[Async and await - reads like normal top to bottom code]
    C --> D[Error handling centralized in one try catch block]
```


## 🎯 What We Did Today

- Ran **9 `model:generate` commands**, each producing a matching **model** (`models/`) and **migration** (`migrations/`) pair.
- Learned the difference: **migration builds the shelf, model puts things on and off the shelf.**
- Mapped out all **9 tables** — `users`, `templates`, `documents`, `sections`, `items`, `versions`, `applications`, `shares`, `exports` — and what each one points to.
- Saw how foreign keys are declared twice: once in the migration (the real database constraint) and once in each model's `associate()` function (how Sequelize understands the relationship in code).
- Learned the `db:migrate` commands used to actually build (and undo) the tables.

