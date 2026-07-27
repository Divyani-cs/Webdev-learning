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

## 4. How the Tables Connect: the ER Diagram

This is the whole database in one picture. An arrow points from a table to the one it depends on. For example, a section belongs to a document, so `sections` points to `documents`.

**Resume Forge — Database Schema** · 9 tables, 10 foreign keys · arrow points from child to the parent it references

```
users                          sections                items
─────────                      ─────────                ─────────
id PK                          id PK                     id PK
name                           heading                   content
email UNIQUE                   position                  position
password                       documentId FK ──┐          sectionId FK ──┐
tier ENUM                                       │                        │
aiCredits                                       │                        │
                                                 │                        │
                    documents ◄─────────────────┘                        │
                    ─────────                                            │
templates           id PK                                                │
─────────           title                                                │
id PK       ◄─────  type ENUM                                            │
name                userId FK  ──────► users (CASCADE)                   │
config TEXT         templateId FK ──► templates (SET NULL)               │
                        ▲   ▲   ▲                                        │
                        │   │   │                                        │
              ┌─────────┘   │   └──────────┐                             │
              │              │              │                            │
          versions      applications      shares                        │
          ─────────     ─────────         ─────────                     │
          id PK          id PK             id PK                        │
          snapshot       company           slug UNIQUE                  │
          label          role              documentId FK (CASCADE)      │
          documentId FK  status ENUM                                    │
          (SET NULL)     userId FK (CASCADE)                            │
                          documentId FK (CASCADE)                       │
                                                                         │
                                                          items.sectionId FK
                                                          (CASCADE) ─────┘

exports
─────────
id PK
format ENUM
fileUrl
documentId FK (CASCADE)
userId FK (CASCADE)
```

*PK = primary key · FK = foreign key · red arrow = ON DELETE CASCADE · grey arrow = ON DELETE SET NULL*

---

## 5. Where the Connections Actually Live

Every foreign key column, like `userId:integer`, becomes a real constraint in the migration:

```js
userId: {
  type: Sequelize.INTEGER,
  references: { model: 'Users', key: 'id' },
  onUpdate: 'CASCADE',
  onDelete: 'CASCADE'
}
```

And the model side of the relationship is written in each model's `associate()` function:

```js
Document.belongsTo(models.User, { foreignKey: 'userId' });
Document.hasMany(models.Section, { foreignKey: 'documentId', onDelete: 'CASCADE' });
```

So `documents` → `users` (a document belongs to a user), and `documents` → `sections` (a document has many sections) — the same "arrow points to what it depends on" idea from the ER diagram, just written in code.

---

## 6. Running the Migrations

```bash
npx sequelize-cli db:migrate            # build every table
npx sequelize-cli db:migrate:undo       # undo the last table
npx sequelize-cli db:migrate:undo:all   # undo everything
npx sequelize-cli db:migrate:status     # see what has run
```

---

## 🎯 What We Did Today

- Ran **9 `model:generate` commands**, each producing a matching **model** (`models/`) and **migration** (`migrations/`) pair.
- Learned the difference: **migration builds the shelf, model puts things on and off the shelf.**
- Mapped out all **9 tables** — `users`, `templates`, `documents`, `sections`, `items`, `versions`, `applications`, `shares`, `exports` — and what each one points to.
- Saw how foreign keys are declared twice: once in the migration (the real database constraint) and once in each model's `associate()` function (how Sequelize understands the relationship in code).
- Learned the `db:migrate` commands used to actually build (and undo) the tables.

