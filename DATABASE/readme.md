# MySQL, Tables & Joins —
## 1. Why a File Is Not Enough

Our resume data has lived in `data.json`. It works in class but fails in the real world for two reasons:

- **Data gets lost.** Two users save at the same moment. Both read the file, both write it back, and the second write erases the first user's change. No error — the data is just gone. A file cannot stop this. A database can.
- **Search is slow.** To find one resume in a file, you load all of them and loop through every one. With ten thousand records, that's slow every single time. A database finds it instantly, because it was built for exactly this.

> **MySQL** — A real database server. A separate program whose only job is to store data safely and find it fast, even with many people using it at once. It is one of the most widely used databases in the world, and what you learn here is what companies actually run.

## 2. Install MySQL and MySQL Workbench

Two things to install. The **MySQL Server** is the database itself. **MySQL Workbench** is a visual app to talk to it: run queries, see your tables, click around your data.

| Install | What it is |
|---------|------------|
| MySQL Server | The database engine that stores everything. Runs in the background. |
| MySQL Workbench | A window into the database. You type SQL here and see results. |

During install, MySQL asks you to set a **root password** — write it down. `root` is the main admin user, and you'll need that password to connect from Workbench and later from your Node app. When install finishes, open Workbench and connect to `localhost` with user `root` and your password. You now have a live database in front of you.

> **Remember the root password.** Almost every "cannot connect" problem this week is a wrong root password. If you forget it, the simplest fix is to reinstall MySQL and set a new one. Save it somewhere the moment you set it.

## 3. Create a Database, by Hand, in Workbench

A MySQL server can hold many databases, one per project. First we make ours. In Workbench, open a query tab and run this:

```sql
CREATE DATABASE resume_db;
```

That's it. You now have an empty database called `resume_db`. To work inside it, tell MySQL to use it:

```sql
USE resume_db;
```

> **Server vs database vs table vs row**
> The **server** is the whole MySQL program. It holds many **databases**, one per project (`resume_db`, `shop_db`, ...). Each database holds many **tables** (users, resumes, ...). Each table holds many **rows**.
> Think folders inside folders: server, then database, then table, then row.

## 4. Create a Table, by Hand

Now a table to hold users. Run this in Workbench, inside `resume_db`:

```sql
CREATE TABLE users (
  id    INT AUTO_INCREMENT PRIMARY KEY,
  name  VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL UNIQUE
);
```

Read each line, because you are declaring the shape of your data:

| Part | Meaning |
|------|---------|
| `id INT AUTO_INCREMENT` | A number that goes up automatically for each new row |
| `PRIMARY KEY` | The unique identifier for each row |
| `VARCHAR(255)` | Text, up to 255 characters |
| `NOT NULL` | This field can never be left empty |
| `UNIQUE` | No two rows can have the same value here |

## 5. Add Records (`INSERT`)

```sql
INSERT INTO users (name, email) VALUES ('Himanshu', 'himanshu@example.com');
INSERT INTO users (name, email) VALUES ('Ayush', 'ayush@example.com');
INSERT INTO users (name, email) VALUES ('Tanushree','tanushree@example.com');
```

Notice we never set `id` — MySQL fills it in automatically because of `AUTO_INCREMENT`.

## 6. Read Records (`SELECT`)

```sql
SELECT * FROM users;
```

```
+----+-----------+-------------------------+
| id | name      | email                   |
+----+-----------+-------------------------+
| 1  | Himanshu  | himanshu@example.com    |
| 2  | Ayush     | ayush@example.com       |
| 3  | Tanushree | tanushree@example.com   |
+----+-----------+-------------------------+
```

The `*` means all columns. To find one person, filter with `WHERE`:

```sql
SELECT * FROM users WHERE email = 'ayush@example.com';
```

⚠️ **`WHERE` is not optional on `UPDATE` and `DELETE`.**
- `DELETE FROM users;` with no `WHERE` deletes **every** user.
- `DELETE FROM users WHERE id = 2;` deletes exactly one.

The `WHERE` is what saves you.

## 7. A Second Table: `resumes`

A resume belongs to a user. We make a second table and connect it to `users` with a **foreign key** — a column that holds the id of the owning user.

```sql
CREATE TABLE resumes (
  id      INT AUTO_INCREMENT PRIMARY KEY,
  title   VARCHAR(255) NOT NULL,
  summary TEXT,
  userId  INT,
  FOREIGN KEY (userId) REFERENCES users(id)
);
```

```sql
INSERT INTO resumes (title, summary, userId) VALUES ('Full Stack Intern', 'Node, Express, MySQL', 1);
INSERT INTO resumes (title, summary, userId) VALUES ('QA Intern', 'Manual + API testing', 2);
```

> **Foreign key** — A column in one table that points to a row in another. `resumes.userId` holds a `users.id`. A resume with `userId = 1` belongs to Himanshu. The `FOREIGN KEY ... REFERENCES` line tells MySQL to enforce that link.

Notice `summary` is `TEXT`, not `VARCHAR`. `VARCHAR` caps at 255 characters; a real summary is longer. `TEXT` holds long text. Choosing the right type is part of designing a table.

## 8. Joins — Reading Two Tables Together

You have users in one table and resumes in another, linked by `userId`. A **join** reads them together in one query, matching each resume to its owner.

> **Join** — A query that combines rows from two tables using a matching column. Here we match `resumes.userId` to `users.id`, so each resume is paired with the user it belongs to.

```sql
SELECT resumes.title, users.name
FROM resumes
JOIN users ON resumes.userId = users.id;
```

```
+--------------------+----------+
| title              | name     |
+--------------------+----------+
| Full Stack Intern  | Himanshu |
| QA Intern          | Ayush    |
+--------------------+----------+
```

One query, two tables, joined on the matching id. This is the heart of a relational database: data lives in separate tables, and joins bring it back together on demand.

## 9. Why Split Into Two Tables? — Normalization

Why not one big table with the user's name repeated on every resume row? That question is what **normalization** answers.

> **Normalization** — Organizing data so each fact is stored in exactly one place. A user's name lives once, in the `users` table. Resumes point to it by id. You do not copy the name onto every resume row.

Imagine the bad, un-normalized version — one table where every resume row also stores the user's name and email:

| Title | Name | Email |
|-------|------|-------|
| Full Stack Intern | Himanshu | himanshu@example.com |
| QA Intern | Himanshu | himanshu@example.com |

Himanshu's name and email get copied on every resume he has. Two problems follow:
1. **Waste** — the same data stored again and again.
2. **Update anomalies** — change his email in one row and forget the others, and now his records disagree with each other.

The normalized version stores the email once in `users`. Change it in one place, and every resume, through the join, sees the new value instantly. Nothing to hunt down, nothing to miss.

**The rule in one line:** store each fact once, and link to it by id. Joins put the data back together when you need it. That's why relational databases use many small, connected tables instead of one giant one.

---

## 🎯 What We Did Today

1. **Installed MySQL and Workbench** and connected as root. This is the one real setup step — get it working tonight.
2. **Created `resume_db`, the `users` table**, and added three users by hand in Workbench. Then `SELECT * FROM users` and saw the rows.
3. **Created the `resumes` table**, added two resumes, and ran the join from section 8. Seeing your own data joined across two tables is the goal of today.



---

*Everything here ran live in class. If a part does not click, message me the file name and the line, and read it again tonight while it is fresh.*
