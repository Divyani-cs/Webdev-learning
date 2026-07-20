# JavaScript Notes 

## 1. Objects & Array Methods

### 1.1 What is an object?

An object is a collection of related information stored as key-value pairs — like a student's admission form: name, age, city, marks, all kept together.

```js
const student = {
  name: "Rahul",
  age: 16,
  city: "Delhi",
  marks: 92
};
```

Each line is a **key** (`name`, `age`...) paired with a **value** (`"Rahul"`, `16`...).

### 1.2 Why objects?

- Group related data in one place
- Readable code — the keys explain what each value is
- Reusable and easy to maintain
- Used everywhere: users, products, orders, employees

### 1.3 Accessing properties

```js
student.name;      // dot notation
student["age"];    // bracket notation
```

**When to use which:** dot notation is the everyday choice. Use brackets when the key is stored in a variable, or has a space/special character in it.

### 1.4 Add, update & delete

```js
student.school = "ABC School";  // add
student.marks = 95;             // update
delete student.city;            // delete
```

### 1.5 Methods and `this`

A **method** is a function stored inside an object. Inside it, `this` refers to the object itself, so it can reach its own data.

```js
const student = {
  name: "Rahul",
  greet() {
    console.log("Hello " + this.name);
  }
};
student.greet(); // Hello Rahul
```

### 1.6 Looping through an object

```js
for (const key in student) {
  console.log(key, student[key]);
}
```

Three helpers that turn an object into arrays you can work with:

| Method              | Gives you                    |
|----------------------|-------------------------------|
| `Object.keys(obj)`   | an array of the keys          |
| `Object.values(obj)` | an array of the values        |
| `Object.entries(obj)`| an array of `[key, value]` pairs |

### 1.7 Nested objects

```js
const student = {
  name: "Rahul",
  address: { city: "Delhi", pin: 110001 }
};
student.address.city; // "Delhi"
```

### 1.8 Arrays (and arrays of objects)

An array of objects is the shape real apps use most — a list of products, users, or orders.

```js
const products = [
  { id:1, name:"Laptop", price:65000, stock:true },
  { id:2, name:"Mouse", price:500, stock:true },
  { id:3, name:"Keyboard", price:1200, stock:false },
  { id:4, name:"Monitor", price:18000, stock:true }
];
```

> **Where this data comes from:** in a real app you rarely type this list — it arrives from a network call to a server (an API). We watched a network request on shorterloop.com in DevTools and saw the response come back exactly in this shape: an array of objects.

**`forEach()`** — do something for every item, returns nothing.
```js
products.forEach(p => console.log(p.name));
```

**`map()`** — build a NEW array.
```js
const names = products.map(p => p.name);
```

**`filter()`** — keep the matching items.
```js
const expensive = products.filter(p => p.price > 10000);
```

**`find()`** — the first match only; returns the item, not an array.
```js
const laptop = products.find(p => p.name === "Laptop");
```

**`some()`** — is at least one true?
```js
products.some(p => p.stock); // true if any in stock
```

**`every()`** — are they all true?
```js
products.every(p => p.stock); // true only if ALL in stock
```

**`sort()`** — put them in order.
```js
products.sort((a, b) => a.price - b.price); // cheapest first
```

**`reduce()`** — boil it down to ONE value; `0` is the starting value.
```js
const total = products.reduce((sum, p) => sum + p.price, 0);
```

**The big split:** `map`/`filter` give a new array. `find` gives one item. `some`/`every` give `true`/`false`. `reduce` gives one value. `forEach` gives nothing — it just runs.

#### Quick reference

| Method    | Returns          | Use it for                  |
|-----------|-------------------|------------------------------|
| `forEach` | nothing           | run code on each item        |
| `map`     | a new array       | transform every item         |
| `filter`  | a new array       | keep items that match        |
| `find`    | one item          | the first match              |
| `some`    | true / false      | does any match?               |
| `every`   | true / false      | do all match?                 |
| `sort`    | the sorted array  | put items in order            |
| `reduce`  | one value         | combine into a total          |

### 1.9 Mini project: Student Management System

```js
const students = [
  { id:1, name:"Rahul", marks:92 },
  { id:2, name:"Aman", marks:81 },
  { id:3, name:"Riya", marks:96 }
];

// map    -> all the names
// filter -> marks above 90
// find   -> the student with id 2
// reduce -> total marks of everyone
```

## 2. Functions & Events

### 2.1 What is a function?

A reusable block of code that does one job. Write it once, run it whenever you need it.

```js
function greet() {
  console.log("Hello!");
}
greet(); // calling it
greet();
```

### 2.2 Why do we use functions?

- Reusability — write once, use many times
- Maintainability — fix a bug in one place, not ten
- Organised code — each job lives in its own box
- Easier debugging — problems are easy to locate
- Better readability — a good name explains what it does
- Unit testing — small pieces are easy to test on their own

### 2.3 What makes a good function?

- One job (single responsibility) — it does one thing well
- Keep it short — around 20 to 30 lines is a good target
- If it grows too big, break it into smaller functions

> **Rule of thumb:** if you cannot describe what a function does in one short sentence, it is probably doing too much. Split it.

### 2.4 Syntax: parts of a function

```js
function add(a, b) {   // a, b = parameters
  return a + b;         // return = send a value back
}
let total = add(2, 3);  // 2, 3 = arguments -> total is 5
```

**Parameters vs arguments**
- **Parameters** — the names in the definition (`a, b`). The empty slots.
- **Arguments** — the real values you pass in when calling (`2, 3`).

> Easy way to remember: Parameters are the **P**laceholders. Arguments are the **A**ctual values.

**Calling (invoking) a function:** writing the function does nothing on its own — you have to call it with its name and brackets, e.g. `add(2, 3)`.

### 2.5 The `return` statement

`return` sends a value back out of the function so you can use it. Once `return` runs, the function stops right there.

```js
function square(n) {
  return n * n;
  console.log("never runs"); // dead code after return
}
```

Functions without `return` just do something (like print to the screen) and hand back `undefined`.

### 2.6 Function vs procedure

- A **function** takes input and returns a value (like `add(2,3)` giving back `5`).
- A **procedure** just does a job and returns nothing (like printing to the screen).

In JavaScript, we call both of them "functions" — there is no separate procedure keyword.

### 2.7 Pure vs impure functions

**Pure**
```js
function add(a, b) {
  return a + b;
}
```
- Same input always gives the same output
- Changes nothing outside itself (no side effects)
- Easy to test and trust

**Impure**
```js
let count = 0;
function tick() {
  count++; // changes outside
}
```
- Output can change even with the same input
- Touches the outside world (DOM, a global, random, time)

> Aim for pure where you can. Pure functions are predictable, which makes bugs rare and testing simple.

### 2.8 Three ways to write a function

```js
// 1. function declaration
function greet(name) {
  return "Hi " + name;
}

// 2. function expression
const greet = function(name) {
  return "Hi " + name;
};

// 3. arrow function
const greet = (name) => {
  return "Hi " + name;
};
```

### 2.9 Arrow function variations

**Implicit return** (one line, no braces):
```js
const add = (a, b) => a + b; // returns a+b
```

**Single parameter** (brackets optional):
```js
const square = n => n * n;
```

**Multiple parameters** (brackets needed):
```js
const add = (a, b) => a + b;
```

**Same function, written 5 ways (all equal):**

| Style              | Looks like                                 |
|---------------------|---------------------------------------------|
| declaration         | `function add(a,b){ return a+b; }`          |
| expression          | `const add = function(a,b){...};`           |
| arrow (full)        | `const add = (a,b) => { return a+b; };`     |
| arrow (short)       | `const add = (a,b) => a+b;`                 |

> The `_` trick: `_` is just a parameter name people use to mean "I am not using this argument." It's not special syntax — any name would work, but `_` signals "ignore me."

> **Quick guide:** one line of logic → use implicit return. One parameter → you can drop the `()`. Zero or 2+ parameters → keep the `()`.

---

### 2.10 What is an event?

An event is something that happens on the page: a click, a key press, the mouse moving, a form being submitted. You attach a function that runs when that thing happens — this is what makes a page interactive.

### 2.11 `addEventListener`

The main tool. Give it two things: the event name to listen for, and the function to run when it fires.

```js
btn.addEventListener("click", () => {
  console.log("clicked!");
});
```

The function you pass in is called a **callback** — the browser calls it back for you when the event happens. You can also pass a named function:

```js
function changeColor() { ... }
btn.addEventListener("mouseout", changeColor);
```

> **Watch out:** pass the function name without brackets (`changeColor`), not `changeColor()`. Brackets would run it immediately instead of waiting for the event.

### 2.12 The event object

```js
btn.addEventListener("click", (event) => {
  console.log(event.target); // the element clicked
  console.log(event.type);   // "click"
});
```

### 2.13 Common events

| Event       | Fires when...                              |
|-------------|----------------------------------------------|
| `click`     | an element is clicked                        |
| `dblclick`  | an element is double-clicked                 |
| `mousemove` | the mouse moves over an element              |
| `mouseover` | the mouse enters an element                  |
| `mouseout`  | the mouse leaves an element                  |
| `mousedown` | a mouse button is pressed down               |
| `keydown`   | a key is pressed down                        |
| `keyup`     | a key is released                            |
| `input`     | the value of a field changes as you type     |
| `change`    | a field changes and loses focus              |
| `submit`    | a form is submitted                          |
| `blur`      | an element loses focus                       |
| `scroll`    | the user scrolls                             |
| `resize`    | the window is resized                        |
| `load`      | the page (or an image) finishes loading      |

### 2.14 Handling events: the pattern

Three steps, every time:

```js
// 1. select
const btn = document.querySelector(".btn");

// 2. write the handler
function changeBackgroundColor() {
  document.body.style.background = "lightblue";
}

// 3. listen, and pass the handler as a callback
btn.addEventListener("mouseout", changeBackgroundColor);
```

**Select → Listen → Do.** Almost every interactive feature on the web is just this loop with a different event and a different action.

### 2.15 Removing an event listener

```js
btn.addEventListener("click", changeColor);
// later, stop listening
btn.removeEventListener("click", changeColor);
```

**Why remove one?**
- One-time actions — a handler that should run once, then stop (e.g. a "claim reward" button)
- Cleanup — when an element goes away, remove its listeners so they don't pile up and waste memory
- Turning behaviour off — stop reacting once something is done or disabled

> **Important:** it only works if you pass the *same named function*. An inline arrow like `() => {...}` is a brand new function each time, so the browser can't find it to remove it. Use a named function when you plan to remove it.

### 2.16 What if JavaScript is off?

JavaScript can be switched off, or fail to load — so a page should still make sense without it. This idea is called **graceful degradation**: the core content and links work with plain HTML, and JS only adds extra polish on top.

- Write real HTML content and real links first
- Use JS to enhance, not to hold the whole page together
- If JS is the only thing making the page usable, users with it off see a broken page

**Turning JS off yourself (a testing skill):** open DevTools (F12), go to Settings or the command menu, and toggle "Disable JavaScript." Reload and see what still works.

### 2.17 Key takeaways

- A function should do one thing, and its name should tell you what.
- Parameters are placeholders, arguments are the real values.
- Pass a callback by name (no brackets) to `addEventListener`.
- To remove a listener you need the same named function.
- Every interactive feature = select, listen, do.

---

## 3. JavaScript & the DOM

### 3.1 What is JavaScript?

Three technologies work as a team to build a web page:

- **HTML** → the structure (skeleton)
- **CSS** → the looks (clothes)
- **JavaScript** → the behaviour (the brain)

> HTML builds it, CSS styles it, JS makes it do things. Today the page stops being a poster and starts responding to people.

### 3.2 What is the DOM?

**DOM = Document Object Model.** When the browser loads your HTML, it turns it into a tree of objects. Every tag becomes a node your JS can read & change.

```
document
  ↓
 body
  ↓
div#card
 ╱ | ╲
h1 p button
```

Every HTML element = one node (object) in this tree.

> **Key idea:** the DOM is an API the browser gives you. It is *not* part of the JavaScript language itself. The browser builds the tree and hands you methods (`document.querySelector`, `.appendChild`...) to read and change the live page.

### 3.3 Nodes = a family tree

Every node has a parent, children, and siblings — just like a family.

```
    parent
   ╱      ╲
 you     sibling
```

```
h1
 ↓
 p
 ↓
button
 ↓  next ↑ previous
```

### 3.4 Selecting elements (the DOM API)

Before you can change anything, you have to grab it.

```js
// by id -> one element
document.getElementById("card");

// by class name -> HTMLCollection
document.getElementsByClassName("card");

// by tag name -> HTMLCollection
document.getElementsByTagName("li");

// first match of a CSS selector
document.querySelector(".card");

// ALL matches -> NodeList
document.querySelectorAll(".card");
```

> **Note:** `querySelector` uses the exact same selectors you learned in CSS. The `getElementsBy...` methods are older; `querySelector(All)` is the modern go-to.

### 3.5 CSS selectors you can use

| Selector             | Meaning                          | Example                  |
|-----------------------|-----------------------------------|----------------------------|
| `*`                   | everything (universal)            | `"*"`                      |
| `#id`                 | by id                              | `"#header"`                |
| `.class`              | by class                          | `".card"`                  |
| `tag`                 | by tag name                       | `"button"`                 |
| `.a.b`                | has BOTH classes                  | `".card.active"`           |
| `A, B`                | A or B (group)                    | `"h1, h2"`                 |
| `A B`                 | descendant (any depth)            | `".card button"`           |
| `A > B`               | direct child only                 | `".card > button"`         |
| `A + B`               | next sibling right after A        | `"h2 + p"`                 |
| `A ~ B`               | any sibling after A               | `"h2 ~ p"`                 |
| `:first-child`        | first in its parent               | `"li:first-child"`         |
| `:last-child`         | last in its parent                | `"li:last-child"`          |
| `:nth-child(n)`       | the nth, counts from 1            | `"li:nth-child(2)"`        |
| `:nth-child(even)`    | even ones (2,4,6...)              | `"tr:nth-child(even)"`     |
| `:nth-child(odd)`     | odd ones (1,3,5...)               | `"tr:nth-child(odd)"`      |
| `:nth-child(3n)`      | every 3rd (a formula)             | `"li:nth-child(3n)"`       |
| `:nth-of-type(n)`     | nth of that tag only              | `"p:nth-of-type(1)"`       |
| `:only-child`         | the sole child                    | `"span:only-child"`        |
| `:not(x)`             | everything except x               | `"li:not(.done)"`          |
| `:hover` / `:focus`   | state based                       | `"button:hover"`           |
| `:checked` / `:disabled` | form states                    | `"input:checked"`          |
| `[attr]`              | has the attribute                 | `"a[target]"`              |
| `[attr="x"]`          | attribute equals x                | `input[type="password"]`   |
| `[attr^="x"]`         | attribute starts with x           | `a[href^="https"]`         |
| `[attr$="x"]`         | attribute ends with x             | `img[src$=".png"]`         |
| `[attr*="x"]`         | attribute contains x              | `a[href*="youtube"]`       |

> **Even/odd:** `nth-child(even)` and `nth-child(odd)` are the classic way to stripe table rows. `3n`, `2n+1` etc. are formulas — `n` counts 0,1,2,3...

### 3.6 Traversing the tree

Once you hold one element, you can walk around it:

- `.parentElement` — go up to the parent
- `.children` — all direct children
- `.firstElementChild` / `.lastElementChild`
- `.nextElementSibling` / `.previousElementSibling` — the neighbours
- `.closest(".card")` — walk UP till a match
- `card.querySelector("button")` — search inside that element only

### 3.7 Reading & changing an element

```js
// text (textContent = raw text)
button.textContent = "Submit";
button.innerText = "Submit";

// html inside the element
div.innerHTML = "<h1>Hi</h1>";

// inline style
button.style.color = "red";
button.style.display = "none";

// classes (let CSS hold the look!)
el.classList.add("active");
el.classList.remove("active");
el.classList.toggle("active");
el.classList.contains("active"); // T/F

// attributes
img.setAttribute("src", "dog.png");
img.getAttribute("src");
img.removeAttribute("alt");

// data-* attrs + form value
el.dataset.userId;
input.value;
```

### 3.8 Creating, adding & removing

```js
const li = document.createElement("li");
li.textContent = "Apple";

// add it to the page
ul.appendChild(li);
ul.prepend(li);
node.before(li);
node.after(li);
ul.insertAdjacentHTML("beforeend", "<li>Hi</li>");

// copy / replace / remove
li.cloneNode(true);
old.replaceWith(li);
button.remove(); // take off page
```

> **Rule:** prefer `classList.toggle` over setting styles one-by-one. Keep the logic in JS, the look in CSS. And `textContent` is safer than `innerHTML` for plain text.

### 3.9 HTMLCollection vs NodeList

- `.children` → an **HTMLCollection** — it's *live* (updates if the DOM changes)
- `querySelectorAll()` → a **NodeList** — a static snapshot you loop with `forEach()`

### 3.10 One picture to remember it all

```
document
  ↓
 body
  ↓
div#container
 ╱  ╱  ╲  ╲
h1  p1  p2  button
```

- `parentElement` ↑ up
- `children` ↓ down
- `first/lastElementChild`
- `next/previousElementSibling`
- `closest()` ↑ walks up to match
- `querySelector(All)` = find

> **Interview rule:** only 2 jobs → (1) FIND an element, then (2) move / read / change it. Everything today fits in those two buckets.

### 3.11 Cheat sheet — the DOM API

| Method / property                          | What it does                     |
|----------------------------------------------|-------------------------------------|
| `getElementById()`                           | find by id                          |
| `getElementsByClassName` / `TagName()`       | find by class / tag (older)         |
| `querySelector()` / `querySelectorAll()`     | find by CSS selector (modern)       |
| `parentElement` / `children`                 | move up / down                      |
| `first` / `lastElementChild`                 | first / last child                  |
| `next` / `prevElementSibling`                | the neighbours                      |
| `closest()`                                  | nearest matching ancestor           |
| `textContent` / `innerText` / `innerHTML`    | read / change content               |
| `style`                                      | change inline CSS                   |
| `classList.add/remove/toggle/contains`       | manage classes                      |
| `get` / `set` / `removeAttribute()`          | manage attributes                   |
| `dataset` / `value`                          | data-* attrs / form value           |
| `createElement()`                            | make a new element                  |
| `appendChild` / `prepend` / `before` / `after` | add to the page                   |
| `insertAdjacentHTML()`                       | quick html insert                   |
| `cloneNode()` / `replaceWith()` / `remove()` | copy / replace / delete             |
