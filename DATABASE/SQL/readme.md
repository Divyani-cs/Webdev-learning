# SQL, NoSQL & How Data Is Stored 

## 1. What Is a Database, and What Is a DBMS

A **database** is an organised collection of data. That is all the word means: data, kept in order so you can find it. But the data does not manage itself. Something has to store it, protect it, and answer questions about it. That something is the **DBMS**.

> **DBMS — Database Management System**
> The software that stores and manages a database for you. It handles saving data, reading it back, keeping it safe, and letting many people use it at once. MySQL is a DBMS. So are PostgreSQL, Oracle, and MongoDB. When you "install MySQL," you are installing a DBMS.

So the **database** is the data. The **DBMS** is the program that looks after it. People often say "database" for both, but now you know the difference.

## 2. What Is a Language, and What Is a Query

You talk to a DBMS in a **language**. Just as you write JavaScript to talk to a browser, you write database commands in a database language. The most common one is SQL.

> **Query**
> A single question or command you send to the database. "Give me all users from Haldwani" is a query. "Add this new user" is a query. Every time your app reads or writes data, it sends a query. The word comes from "to ask": a query asks the database to do something.

So a **language** is how you speak to the database, and a **query** is one thing you say in that language.

## 3. SQL vs NoSQL — the Two Families

*(This section covers the two big families of databases — the topic we didn't finish covering live today. Come prepared with questions tomorrow.)*

---

## 6. CHAR vs VARCHAR (and Where STRING Fits)

> **Where STRING fits.** `STRING` is not a MySQL word, it is a **Sequelize** word. When you write `DataTypes.STRING` in a model, Sequelize turns it into `VARCHAR(255)` in MySQL. So STRING is the JavaScript name, VARCHAR is the real MySQL column it becomes. Same thing, two layers. Rule of thumb: **VARCHAR** for text that varies in length, **CHAR** only when every value is exactly the same size.

### How Each One Stores the Name "Deepesh"

**Storing "Deepesh" (7 letters) in a size-10 text column:**

**`CHAR(10)`** — always reserves 10 cells

```
D  e  e  p  e  s  h  ·  ·  ·     = 10 used
                     ↑
          3 blank spaces added (padding)
```

**`VARCHAR(10)`** — stores only what you use

```
D  e  e  p  e  s  h  [nothing stored here]     = 7 used
```

> **Same name, less space.**
> CHAR wastes 3 cells on padding every time. VARCHAR keeps only the 7 it needs.

Deepesh has 7 letters. In a size-10 column, `CHAR` fills all 10 cells and pads the 3 extra with blank spaces, every time. `VARCHAR` keeps only the 7 the name uses. Store a million names and `CHAR` is storing a million rows of padding you never needed.

## 7. More MySQL Types: ENUM, BOOLEAN, TINYINT

Text is not the only kind of column. A few more types came up, each for a specific job.

> **ENUM**
> A column that can only hold one value from a fixed list you decide in advance. A `status` column might be `ENUM('active', 'pending', 'deleted')`. Try to save anything else and MySQL refuses. It keeps a column honest when there are only a few valid options.

> **BOOLEAN**
> A true or false column. `isVerified`, `termsAccepted`. It answers a yes-or-no question about the row.

> **TINYINT**
> *(Covered alongside BOOLEAN and ENUM as another compact MySQL data type.)*

---

## 🎯 Recap

- A **database** is the data; a **DBMS** is the software that manages it (MySQL, PostgreSQL, Oracle, MongoDB).
- A **language** (like SQL) is how you talk to a DBMS; a **query** is a single command in that language.
- **VARCHAR** stores only what you use; **CHAR** always reserves the full fixed size, padding the rest.
- Sequelize's `DataTypes.STRING` becomes MySQL's `VARCHAR(255)` under the hood.
- **ENUM** restricts a column to a fixed set of values; **BOOLEAN** is a true/false column.

---
