### CSS Syntax

```css
selector {
  property: value;
}
```

Example:

```css
h1 {
  color: pink;
}
```

### Selectors

| Type | Example | Targets |
|------|---------|---------|
| Element | p | every p tag |
| Class | .card | elements with class="card" |
| ID | #header | the element with id="header" |
| Universal | * | everything |

The Three Ways to Write CSS
Method 1 — Inline CSS ❌
Inline CSS is written directly inside an HTML tag using the style attribute.
<p style="color: red; font-size: 18px;">This is red text</p>
<h1 style="color: blue; text-align: center;">Hello</h1>

Method 2 — Internal CSS ⚠️
Internal CSS is written inside a <style> tag, placed in the <head> of the HTML file.
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

Method 3 — External CSS ✅ (The Correct Way)
External CSS lives in a completely separate .css file. You link it to your HTML using a <link> tag in the <head>.
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
/* style.css */
p {
  color: red;
  font-size: 18px;
}

h1 {
  color: blue;
}

Quick Comparison
Inline	Internal	External
Written in	HTML tag	<style> in <head>	Separate .css file
Reusable?	❌ No	❌ Only on same page	✅ Across all pages
Easy to maintain?	❌ Very messy	⚠️ Okay	✅ Yes
Recommended?	❌ Avoid	⚠️ Only for 1-page projects	✅ Always

CSS Syntax — How a Rule Is Written
Every CSS rule follows the same structure:
selector {
  property: value;
  property: value;
}
Selector — which HTML element you want to style
Property — what you want to change (color, size, spacing, etc.)
Value — what you want to change it to
Declaration — one property: value pair
Rule — the full block including selector and all declarations

The DOM — How CSS Finds Elements
When a browser reads your HTML file, it doesn't just display it directly. It first builds a tree-like structure in memory called the DOM — Document Object Model.

Think of the DOM as a family tree of your HTML elements. Every element is a "node" in this tree, with parent-child-sibling relationships.

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

        CSS uses this tree to decide which elements match a selector. When you write div p, CSS walks down the DOM tree and finds every <p> that has a <div> somewhere above it as an ancestor.

This is also why the word "Cascading" is in CSS — styles flow down through the tree from parent to child, just like water cascading downward. A color set on body will cascade down to every element inside it, unless overridden.

Developer Tools — 
Every browser has built-in DevTools that are essential for CSS work. They let you inspect, debug, and experiment with styles live — without touching your actual files.

Open DevTools:

Press F12 on Windows/Linux
Press Cmd + Option + I on Mac
Or right-click any element on the page → click "Inspect"
Elements Tab
Shows you the full DOM tree — the browser's internal representation of your HTML. You can click on any element and see exactly what it is, what class or id it has, and where it lives in the hierarchy.

Styles Panel
Shows every CSS rule currently applied to the selected element — including which file it came from and which line number.

You can:

Click on any value and edit it live to test changes instantly
Uncheck the checkbox next to a property to temporarily disable it
See crossed-out styles — those are being overridden by a more specific rule
The Computed Tab
Shows the final calculated values for every CSS property on an element, after all rules and inheritance have been resolved. Useful when you can't figure out why a style isn't applying.

⚠️ Important: Any changes you make in DevTools are temporary. They live only in the browser's memory and reset the moment you refresh the page. Always copy your changes back into your actual CSS file.


