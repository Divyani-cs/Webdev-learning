# Storing Data, Step by Step — 

## 0. The Setup Before Any Step

Three model files describe the tables, and one connection points at your MySQL. You wrote versions of these already. Here they are together so every step below has its foundation.

### `config/database.js` — the MySQL connection

```js
// config/database.js
// The single connection to our MySQL database.
// Every other file talks to the database through this one object.

const { Sequelize } = require('sequelize');

const sequelize = new Sequelize(
  'resume_db',      // the database name (we create this in MySQL Workbench first)
  'root',            // your MySQL username
  'your_password',   // your MySQL password (set when you installed MySQL)
  {
    host: 'localhost',   // MySQL runs on your own machine
    dialect: 'mysql',    // tells Sequelize to speak MySQL
    logging: false,      // set to console.log to see every SQL query it sends
  }
);

module.exports = sequelize;
```

### `models/user.js`

```js
// models/user.js
const { DataTypes } = require('sequelize');
const bcrypt = require('bcryptjs');
const sequelize = require('../config/database');

const User = sequelize.define('User', {
  name: { type: DataTypes.STRING, allowNull: false },
  email: {
    type: DataTypes.STRING, allowNull: false, unique: true,
    validate: { isEmail: true },
  },
  password: { type: DataTypes.STRING, allowNull: false },
});

// hash the password before every save
User.beforeCreate(async (user) => {
  const salt = await bcrypt.genSalt(10);
  user.password = await bcrypt.hash(user.password, salt);
});

User.prototype.checkPassword = function (plainText) {
  return bcrypt.compare(plainText, this.password);
};

// ---- THIS model's relationships live in THIS file ----
// It receives all the models, so it never has to require Resume itself.
// That is what avoids the circular require. Called once, after all
// models are loaded (see models/index.js).
User.associate = (models) => {
  User.hasMany(models.Resume, { foreignKey: 'userId', onDelete: 'CASCADE' });
};

module.exports = User;
```

### `models/resume.js`

```js
// models/resume.js
const { DataTypes } = require('sequelize');
const sequelize = require('../config/database');

const Resume = sequelize.define('Resume', {
  title: { type: DataTypes.STRING, allowNull: false },
  summary: { type: DataTypes.TEXT },
});

// ---- THIS model's relationship lives in THIS file ----
Resume.associate = (models) => {
  Resume.belongsTo(models.User, { foreignKey: 'userId' });
};

module.exports = Resume;
```

### `models/index.js`

```js
// models/index.js
// This file no longer defines relationships. Each model does that itself,
// in its own associate() function. This file just loads the models and
// then calls each associate() once everything is loaded.

const sequelize = require('../config/database');
const User = require('./user');
const Resume = require('./resume');

const models = { User, Resume };

// Now that all models are loaded, wire up each one's relationships.
Object.values(models).forEach((model) => {
  if (model.associate) model.associate(models);
});

module.exports = { sequelize, ...models };
```

**Why `associate` lives inside each model, not in `index.js`:** `User.hasMany` and `Resume.belongsTo` are each defined inside their own model file, in an `associate()` function. `user.js` never has to `require('./resume')` directly — it just receives all the models as an argument. That's what avoids a **circular require**. `index.js` loads every model first, then calls each one's `associate()` once everything is ready.

---

## 1. Connect and Sync

```js
const { sequelize, User, Resume } = require('./models');

await sequelize.sync(); // no force, so existing data is kept
```

`sync()` checks that the `users` and `resumes` tables match your models, creating them if they don't exist yet. Without `force: true`, existing data is kept. With `force: true`, the tables are dropped and recreated — useful while developing, dangerous once you have real data.

## 2. Create a User

```js
const user = await User.create({
  name: 'Divyani',
  email: 'Divyani@example.com',
  password: 'secret123',
});

console.log('Saved user #' + user.id);
```

**SQL Sequelize sends:**
```sql
INSERT INTO `Users`
(`name`, `email`, `password`, `createdAt`, `updatedAt`)
VALUES ('Divyani', 'Divyani@example.com', ..., NOW(), NOW());
```

**Result:**
```
Saved user #1
```

`User.create` turns straight into an `INSERT`. Sequelize adds `createdAt` and `updatedAt` automatically, and fills in `id`. That's why `user.id` is available right after `create` — you don't have to look the user up again to know their id.

## 3. Create Resumes Linked to That User

```js
await Resume.create({
  title: 'Full Stack Intern',
  summary: 'Built REST APIs with Node, Express and MySQL.',
  userId: user.id,
});

await Resume.create({
  title: 'QA Intern',
  summary: 'Manual test cases and API tests with Postman.',
  userId: user.id,
});

console.log('Saved 2 resumes');
```

**SQL Sequelize sends:**
```sql
INSERT INTO `Resumes`
(`title`, `summary`, `userId`, `createdAt`, `updatedAt`)
VALUES ('Full Stack Intern', '...', 1, NOW(), NOW());
```

**Result:**
```
Saved 2 resumes
```

`userId: user.id` is what links the resume to its owner — it becomes `userId: 1` in the actual row, since that's the id we got back in step 2.

## 4. Read All Resumes for a User

```js
const resumes = await Resume.findAll({ where: { userId: user.id } });

console.log('This user has', resumes.length, 'resumes:');
resumes.forEach(r => console.log(' -', r.title));
```

**SQL Sequelize sends:**
```sql
SELECT id, title, summary, userId, createdAt, updatedAt
FROM `Resumes`
WHERE `userId` = 1;
```

**Result:**
```
This user has 2 resumes:
 - Full Stack Intern
 - QA Intern
```

`findAll` with a `where` clause is how you filter rows. `where: { userId: user.id }` becomes the `WHERE userId = 1` in the SQL. (Other common finders: `findByPk` for one row by its id, `findOne` for the first row matching a condition.)

## 5. Read a Resume Together With Its Owner (a Join)

```js
const first = await Resume.findByPk(resumes[0].id, { include: User });

console.log('Resume "' + first.title + '" belongs to ' + first.User.name);
```

**SQL Sequelize sends:**
```sql
SELECT Resume.*, User.name, User.email
FROM `Resumes` AS `Resume`
LEFT OUTER JOIN `Users` AS `User`
  ON `Resume`.`userId` = `User`.`id`
WHERE `Resume`.`id` = 1;
```

**Result:**
```
Resume "Full Stack Intern" belongs to Divyani
```

`include: User` is what tells Sequelize to run a `LEFT OUTER JOIN` instead of a plain `SELECT`, matching `userId` to `id` for you. The joined data comes back attached as `first.User` — so `first.User.name` reads the owner's name straight off the same result, no second query needed.

## 6. Update a Resume

```js
first.title = 'Senior Full Stack Intern';
await first.save();

console.log('Updated title:', first.title);
```

**SQL Sequelize sends:**
```sql
UPDATE `Resumes`
SET `title` = 'Senior Full Stack Intern', `updatedAt` = NOW()
WHERE `id` = 1;
```

**Result:**
```
Updated title: Senior Full Stack Intern
```

`save()` on a model instance turns into an `UPDATE`. Sequelize refreshes `updatedAt` automatically, and always scopes the update with `WHERE id = 1` so only that one row changes.

## 7. Verify in the Database

```sql
SELECT * FROM resumes;
```

```
+----+---------------------------+---------+--------+
| id | title                     | summary | userId |
+----+---------------------------+---------+--------+
| 1  | Senior Full Stack Intern  | ...     | 1      |
| 2  | QA Intern                 | ...     | 1      |
+----+---------------------------+---------+--------+
```

The row we updated in JavaScript shows the new title, right there in MySQL Workbench — proof that Sequelize's `save()` really did reach the database.

---

## 🎯 What We Did Today

- Set up the connection (`config/database.js`) and two models (`user.js`, `resume.js`), with each model's own `associate()` avoiding a circular require.
- **Created** a user and two resumes from JavaScript with `.create()`.
- **Read** resumes for a user with `.findAll({ where })`.
- **Joined** a resume to its owner with `.findByPk({ include: User })`.
- **Updated** a resume with `.save()`.
- Confirmed every change directly in MySQL with a plain `SELECT`.

