# Callbacks in Node.js

A beginner-friendly guide to callbacks, error-first callbacks, callback hell, and how Promises/async-await solve it — with simple, real-world style examples.

## Table of Contents

- [What is a Callback?](#what-is-a-callback)
- [Basic Callback Examples](#basic-callback-examples)
- [Error-First Callbacks](#error-first-callbacks)
- [Nested Callbacks (Callback Hell)](#nested-callbacks-callback-hell)
- [Sequential Function Chaining](#sequential-function-chaining)
- [Promises](#promises)
- [Async/Await](#asyncawait)

## What is a Callback?

A **callback** is simply a function passed into another function as an argument, which is then invoked ("called back") inside that outer function once some work is done.

```js
function orderFood(callback) {
  console.log("Ordered food");
  callback();
}

orderFood(function () { // <-- calling it
  console.log("Eat!");
});
```

## Basic Callback Examples

**Setting an alarm:**

```js
function setAlarm(callback) {
  console.log("Alarm ringing");
  callback();
}

setAlarm(function () { // <-- calling it
  console.log("Wake up!");
});
```

**Sending a message:**

```js
function sendMsg(callback) {
  console.log("Message sent");
  callback();
}

sendMsg(function () { // <-- calling it
  console.log("Got a reply!");
});
```

## Error-First Callbacks

Node.js convention: the first argument to a callback is reserved for an error (or `null` if there isn't one), and the second argument is the actual result.

```js
function readFile(name, callback) {
  if (!name) {
    return callback("No file name given"); // error first
  }
  callback(null, "File contents here"); // null = no error, then result
}

readFile("notes.txt", function (error, data) { // <-- calling it
  if (error) {
    console.log("Error:", error);
  } else {
    console.log("Got:", data);
  }
});
```

**Dividing two numbers:**

```js
function divide(a, b, callback) {
  if (b === 0) {
    return callback("Cannot divide by zero");
  }
  callback(null, a / b);
}

divide(10, 2, function (error, result) { // <-- calling it
  if (error) {
    console.log("Error:", error);
  } else {
    console.log("Answer:", result);
  }
});
```

**Finding a user:**

```js
function findUser(id, callback) {
  if (!id) {
    return callback("No id given");
  }
  callback(null, { name: "Himanshu" });
}

findUser(5, function (error, user) { // <-- calling it
  if (error) {
    console.log("Error:", error);
  } else {
    console.log("User:", user.name);
  }
});
```

## Nested Callbacks (Callback Hell)

When callbacks are nested inside callbacks to run steps in order, the code starts to form a "pyramid" shape. This pattern is commonly known as **callback hell**.

**Morning routine:**

```js
function wake(cb) { console.log("Wake"); cb(); }
function brush(cb) { console.log("Brush"); cb(); }
function bath(cb) { console.log("Bath"); cb(); }

wake(function () { // <-- calling, nested
  brush(function () {
    bath(function () {
      console.log("Ready!");
    });
  });
});
```

**Making tea:**

```js
function boil(cb) { console.log("Boil"); cb(); }
function addTea(cb) { console.log("Add tea"); cb(); }
function pour(cb) { console.log("Pour"); cb(); }

boil(function () { // <-- calling, nested
  addTea(function () {
    pour(function () {
      console.log("Tea done!");
    });
  });
});
```

**Placing an order:**

```js
function pick(cb) { console.log("Pick"); cb(); }
function pay(cb) { console.log("Pay"); cb(); }
function ship(cb) { console.log("Ship"); cb(); }

pick(function () { // <-- calling, nested
  pay(function () {
    ship(function () {
      console.log("Delivered!");
    });
  });
});
```

## Sequential Function Chaining

Instead of nesting, each function can simply call the next one directly. This flattens the pyramid but tightly couples each function to the next.

**Morning routine:**

```js
function wake() { console.log("Wake"); brush(); }
function brush() { console.log("Brush"); bath(); }
function bath() { console.log("Bath"); done(); }
function done() { console.log("Ready!"); }

wake(); // <-- one call starts the chain
```

**Making tea:**

```js
function boil() { console.log("Boil"); addTea(); }
function addTea() { console.log("Add tea"); pour(); }
function pour() { console.log("Pour"); ready(); }
function ready() { console.log("Tea done!"); }

boil(); // <-- one call starts the chain
```

**Placing an order:**

```js
function pick() { console.log("Pick"); pay(); }
function pay() { console.log("Pay"); ship(); }
function ship() { console.log("Ship"); done(); }
function done() { console.log("Delivered!"); }

pick(); // <-- one call starts the chain
```

## Promises

A **Promise** represents a value that will be available now, later, or never. It's a cleaner alternative to nested callbacks, using `.then()` to handle the resolved value.

```js
function cook() {
  return new Promise(function (resolve) {
    resolve("Food ready");
  });
}

cook().then(function (result) { // <-- calling it
  console.log(result);
});
```

```js
function download() {
  return new Promise(function (resolve) {
    resolve("File downloaded");
  });
}

download().then(function (result) { // <-- calling it
  console.log(result);
});
```

```js
function login() {
  return new Promise(function (resolve) {
    resolve("Logged in");
  });
}

login().then(function (result) { // <-- calling it
  console.log(result);
});
```

> `resolve("...")` marks the promise as fulfilled with a value, and `.then(...)` runs once that value is available.

## Async/Await

`async`/`await` is syntax built on top of Promises that lets asynchronous code read like synchronous code — no `.then()` chains needed.

```js
function makeTea() {
  return new Promise(function (resolve) {
    resolve("Tea ready");
  });
}

async function serveTea() {
  const result = await makeTea();
  console.log(result);
}

serveTea(); // <-- calling it
```

```js
function getData() {
  return new Promise(function (resolve) {
    resolve("Data loaded");
  });
}

async function show() {
  const result = await getData();
  console.log(result);
}

show(); // <-- calling it
```

```js
function pay() {
  return new Promise(function (resolve) {
    resolve("Payment done");
  });
}

async function checkout() {
  const result = await pay();
  console.log(result);
}

checkout(); // <-- calling it
```

## Summary

| Pattern | Description |
|---|---|
| Callbacks | Pass a function to run after a task finishes |
| Error-first callbacks | `callback(error, result)` — Node.js convention |
| Nested callbacks | "Callback hell" — deeply nested pyramid of functions |
| Sequential chaining | Each function calls the next directly |
| Promises | `.then()` to handle resolved/rejected values |
| Async/Await | Write async code that reads like sync code |

---

*Examples inspired by everyday scenarios: ordering food, setting alarms, making tea, and morning routines.*
