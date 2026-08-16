#  CSS — Cascading Style Sheets

> **"HTML builds the skeleton. CSS gives it life."**
>
> Without CSS, every website on the internet would look the same — plain black text on a white background. No colors, no fonts, no layouts. Just raw content. CSS is what separates a webpage from a *website*.

---

##  Table of Contents

1. [Why CSS Exists](#1-why-css-exists)
2. [Where You Put CSS — and Why It Matters](#2-where-you-put-css--and-why-it-matters)
3. [The Three Ways to Write CSS](#3-the-three-ways-to-write-css)
4. [CSS Syntax — How a Rule Is Written](#4-css-syntax--how-a-rule-is-written)
5. [Selectors — How CSS Targets Elements](#5-selectors--how-css-targets-elements)
6. [The DOM — How CSS Finds Elements](#6-the-dom--how-css-finds-elements)
7. [Developer Tools — Your Best Friend](#7-developer-tools--your-best-friend)
8. [File Paths — Linking CSS Correctly](#8-file-paths--linking-css-correctly)

---

## 1. Why CSS Exists

HTML was never designed to handle styling. In the early days of the web, people used to write things like this directly in HTML:

```html
<font color="red" size="5">Hello World</font>
<body bgcolor="yellow">
```

This was a mess. Every single element needed its own styling attributes. If you wanted to change the color of all headings, you had to find every single `<h1>` and change it manually. For a 100-page website, that's a nightmare.

CSS was introduced to **separate content from presentation**. Your HTML file only describes *what* is on the page. Your CSS file describes *how it looks*. They stay in separate files, and CSS can style hundreds of HTML pages at once from a single file.

```
HTML  →  What is on the page    (structure)
CSS   →  How it looks           (presentation)
JS    →  How it behaves         (interactivity)
```

> Think of building a house. The **bricks and concrete** (HTML) form the structure. The **paint, tiles, and furniture** (CSS) make it liveable and beautiful. You wouldn't mix concrete and paint — you keep them separate.

---

## 2. Where You Put CSS — and Why It Matters

This is something most beginners don't think about, but it directly affects what the user *sees* when your page first loads.

### The FOUC Problem

**FOUC** stands for **Flash of Unstyled Content.** It's that ugly moment when a page first loads as plain black-and-white HTML, and *then* the styles kick in a split second later. It looks broken and unprofessional.

This happens when CSS is placed at the **bottom** of the HTML file. The browser reads HTML top-to-bottom. If it reaches the content before it reaches the CSS, it renders the unstyled content first, then goes back and applies styles.

**Always put your CSS in the `<head>` tag.** The browser reads the `<head>` before rendering anything on screen. If CSS is loaded there, the page is fully styled *before* the user ever sees it.

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- CSS here = user always sees styled page -->
    <link rel="stylesheet" href="./style.css">
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

> **The Painter Analogy 🎨**
>
> Imagine a painter is hired to paint a house while it's being built.
>
> - **If the painter works *during* construction** — by the time you walk in, everything is already painted. You only ever see the finished house.
> - **If the painter shows up *after* the house is fully built** — you first see a grey, bare house. Then you stand there watching it get painted.
>
> CSS in `<head>` = painter during construction. The user never sees the ugly unfinished version.

---

## 3. The Three Ways to Write CSS

### Method 1 — Inline CSS ❌

Inline CSS is written directly inside an HTML tag using the `style` attribute.

```html
<p style="color: red; font-size: 18px;">This is red text</p>
<h1 style="color: blue; text-align: center;">Hello</h1>
```

**Why it's bad:**
- If you have 50 paragraphs and want to change their color, you have to edit 50 separate tags.
- Your HTML file becomes cluttered and hard to read.
- No reusability — you can't share these styles with other elements.
- Hardest to maintain over time.

**When it's acceptable:** Quick one-off tests, or when you're overriding styles in HTML emails (which have limited CSS support).

---

### Method 2 — Internal CSS ⚠️

Internal CSS is written inside a `<style>` tag, placed in the `<head>` of the HTML file.

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      p {
        color: red;
        font-size: 18px;
      }
      h1 {
        color: blue;
      }
    </style>
  </head>
  <body>
    <h1>Hello</h1>
    <p>This is red text</p>
  </body>
</html>
```

**Why it's okay but not great:**
- Better than inline — at least your styles are in one place.
- But styles are stuck to *this one HTML file* — you can't reuse them on other pages.
- If you have 10 HTML pages, you'd have to copy-paste and maintain the same `<style>` block in all 10 files.

**When to use it:** Single-page projects, quick prototypes, or when you genuinely have only one HTML file.

---

### Method 3 — External CSS ✅ (The Correct Way)

External CSS lives in a completely separate `.css` file. You link it to your HTML using a `<link>` tag in the `<head>`.

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="./style.css">
  </head>
  <body>
    <h1>Hello</h1>
    <p>This paragraph is styled from an external file</p>
  </body>
</html>
```

```css
/* style.css */
p {
  color: red;
  font-size: 18px;
}

h1 {
  color: blue;
}
```

**Why it's the best:**
- One CSS file can style *hundreds* of HTML pages simultaneously.
- Change one line in `style.css` → every page on the site updates instantly.
- HTML stays clean and readable — it only contains content, not styling.
- Easy to find and fix styling issues since everything is in one place.
- Browsers *cache* external CSS files, which makes your website load faster on repeat visits.

### Quick Comparison

|                   | Inline       | Internal                    | External             |
| ----------------- | ------------ | ---------------------------- | --------------------- |
| Written in        | HTML tag     | `<style>` in `<head>`       | Separate `.css` file |
| Reusable?         | ❌ No         | ❌ Only on same page          | ✅ Across all pages   |
| Easy to maintain? | ❌ Very messy | ⚠️ Okay                      | ✅ Yes                |
| Recommended?      | ❌ Avoid      | ⚠️ Only for 1-page projects | ✅ Always              |

---

## 4. CSS Syntax — How a Rule Is Written

Every CSS rule follows the same structure:

```css
selector {
  property: value;
  property: value;
}
```

- **Selector** — which HTML element you want to style
- **Property** — what you want to change (color, size, spacing, etc.)
- **Value** — what you want to change it to
- **Declaration** — one `property: value` pair
- **Rule** — the full block including selector and all declarations

```css
/* This entire thing is called a "rule" */
h1 {
  color: blue;        /* declaration 1 */
  font-size: 32px;    /* declaration 2 */
  text-align: center; /* declaration 3 */
}
```

**Important syntax rules:**
- Each declaration ends with a semicolon `;`
- Declarations live inside curly braces `{ }`
- CSS ignores whitespace and line breaks — formatting is for your readability, not the browser's
- Comments are written as `/* your comment here */`

---

## 5. Selectors — How CSS Targets Elements

Selectors are the way CSS finds and identifies which HTML elements to style. Getting selectors right is one of the most important skills in CSS.

### Element Selector

Targets **every** element of that tag type on the page.

```css
p {
  color: grey;
  line-height: 1.6;
}
```

Every single `<p>` tag on the entire page will get this style.

### Class Selector

Targets elements that have a specific `class` attribute. Uses a **dot** `.` before the name.

```html
<div class="card">Card 1</div>
<div class="card">Card 2</div>
<p class="card">Even paragraphs can use this class</p>
```

```css
.card {
  background: white;
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 8px;
}
```

All three elements above get the same styling. A class can be used on **any number of elements**, and **any type of element**.

> **Key rule:** Use classes when you want to style *multiple* elements the same way.

### ID Selector

Targets **one specific** element with a matching `id` attribute. Uses a **hash** `#` before the name.

```html
<header id="main-header">This is the header</header>
```

```css
#main-header {
  background: #1a1a2e;
  color: white;
  padding: 24px;
}
```

> **Key rule:** An `id` must be **unique** — only one element per page can have a given id. If you find yourself using the same id on multiple elements, switch to a class.

### ID vs Class — The Most Common Confusion

```
Class (.name)  →  Use on MANY elements  →  "these cards all look the same"
ID (#name)     →  Use on ONE element    →  "this specific header is unique"
```

Think of it like names vs labels. Everyone in a room might wear a "student" label (class), but only one person is named "Unishka" (id).

### Grouping Selector

Apply the exact same styles to multiple different elements in one rule. Use a **comma** to separate them.

```css
/* Without grouping — repetitive */
h1 { font-family: Arial; color: #333; }
h2 { font-family: Arial; color: #333; }
h3 { font-family: Arial; color: #333; }

/* With grouping — clean and efficient */
h1, h2, h3 {
  font-family: Arial;
  color: #333;
}
```

### Descendant Selector

Targets elements that are **nested inside** a specific parent. Uses a **space** between selectors.

```html
<div class="article">
  <p>This paragraph IS inside .article</p>
</div>

<p>This paragraph is NOT inside .article</p>
```

```css
/* Only targets <p> tags that are INSIDE .article */
.article p {
  font-size: 16px;
  line-height: 1.8;
}
```

This is incredibly useful when you want different styles for the same element type in different parts of the page.

---

## 6. The DOM — How CSS Finds Elements

When a browser reads your HTML file, it doesn't just display it directly. It first builds a **tree-like structure** in memory called the **DOM** — Document Object Model.

Think of the DOM as a family tree of your HTML elements. Every element is a "node" in this tree, with parent-child-sibling relationships.

```
Document
└── html
    ├── head
    │   ├── title
    │   └── link  (your CSS file)
    └── body
        ├── header
        │   └── h1       ← "header h1" targets this
        ├── main
        │   ├── div.card
        │   │   └── p    ← ".card p" targets this
        │   └── p        ← ".card p" does NOT target this
        └── footer
```

CSS uses this tree to decide which elements match a selector. When you write `div p`, CSS walks down the DOM tree and finds every `<p>` that has a `<div>` somewhere above it as an ancestor.

This is also why the word **"Cascading"** is in CSS — styles flow *down* through the tree from parent to child, just like water cascading downward. A `color` set on `body` will cascade down to every element inside it, unless overridden.

---

## 7. Developer Tools — Your Best Friend

Every browser has built-in DevTools that are essential for CSS work. They let you inspect, debug, and experiment with styles live — without touching your actual files.

**Open DevTools:**
- Press `F12` on Windows/Linux
- Press `Cmd + Option + I` on Mac
- Or right-click any element on the page → click **"Inspect"**

### Elements Tab

Shows you the full DOM tree — the browser's internal representation of your HTML. You can click on any element and see exactly what it is, what class or id it has, and where it lives in the hierarchy.

### Styles Panel

Shows **every CSS rule** currently applied to the selected element — including which file it came from and which line number.

You can:
- **Click on any value** and edit it live to test changes instantly
- **Uncheck the checkbox** next to a property to temporarily disable it
- **See crossed-out styles** — those are being overridden by a more specific rule

### The Computed Tab

Shows the *final* calculated values for every CSS property on an element, after all rules and inheritance have been resolved. Useful when you can't figure out why a style isn't applying.

> ⚠️ **Important:** Any changes you make in DevTools are **temporary**. They live only in the browser's memory and reset the moment you refresh the page. Always copy your changes back into your actual CSS file.

---

## 8. File Paths — Linking CSS Correctly

When you link an external CSS file, you need to tell the browser exactly where to find it. Even one wrong character means the CSS file silently fails to load, with no error message shown on screen.

```html
<link rel="stylesheet" href="./style.css">
```

### Path Types

**CSS file is in the same folder as HTML:**
```
project/
├── index.html
└── style.css
```
```html
<link rel="stylesheet" href="./style.css">
<!-- ./  means "start from the current folder" -->
```

**CSS is inside a subfolder:**
```
project/
├── index.html
└── css/
    └── style.css
```
```html
<link rel="stylesheet" href="./css/style.css">
```

**HTML is in a subfolder, CSS is one level up:**
```
project/
├── style.css
└── pages/
    └── about.html
```
```html
<link rel="stylesheet" href="../style.css">
<!-- ../  means "go one folder up, then find the file" -->
```

### Common Mistakes

| Mistake | What Happens |
|---|---|
| Wrong filename (`Style.css` vs `style.css`) | File not found — CSS doesn't load |
| Wrong path (`/style.css` vs `./style.css`) | May work locally, breaks on server |
| Forgetting `rel="stylesheet"` | File loads but browser ignores it as CSS |

> **Quick debug tip:** If your CSS isn't working at all, open DevTools → go to the **Network tab** → refresh the page → find your CSS file in the list. If it shows a **404 status**, your path is wrong.

---

*Notes by Unishka Bisht — BCA 3rd year, Amrapali University, Haldwani*
