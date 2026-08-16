## Frontend Frameworks (DAY-5)

## Angular — Binding, Directives & Pipes (Deep Dive)

> Everything that happens inside the template.

The template is the HTML the user sees. Angular gives it superpowers: showing data, reacting to clicks, looping over lists, showing things conditionally, and formatting values. This note breaks each of those superpowers down — what it does, why it exists, and how data actually flows.

---

## Table of Contents

1. [Data Binding: The Four Kinds](#1-data-binding-the-four-kinds)
2. [Directives: `*ngIf`, `*ngFor`, `ngSwitch`](#2-directives-ngif-ngfor-ngswitch)
3. [Pipes: Formatting a Value for Display](#3-pipes-formatting-a-value-for-display)
4. [Custom Pipes: Building Your Own](#4-custom-pipes-building-your-own)
5. [How It All Fits Together](#5-how-it-all-fits-together)

---

## 1. Data Binding: The Four Kinds

Binding is the **connection** between your class (the TypeScript data) and your template (the HTML view). The direction of that connection is what changes, and the brackets tell you which direction.

### 1.1 Interpolation — `{{ }}`

One-way, **read-only**: class → view. Prints a value as text.

```html
<h1>Hello {{ name }}</h1>
```
```ts
name = "Angular";
```
Output: `Hello Angular`

### 1.2 Property Binding — `[ ]`

Sets an actual **property** of an HTML element (not just text).

```html
<img [src]="imageUrl" />
<button [disabled]="isBusy">Save</button>
```

| | `{{ }}` Interpolation | `[ ]` Property Binding |
|---|---|---|
| Used for | Displaying text between tags | Setting a property/attribute on a tag |
| Example | `<h1>{{ name }}</h1>` | `<img [src]="imageUrl" />` |
| Data type | Always becomes a string | Can be any type (string, boolean, number, object) |

### 1.3 Event Binding — `( )`

View → class. Catches an event (click, keypress, submit) and calls a method.

```html
<button (click)="save()">Save</button>
```
```ts
save() {
  console.log("saved!");
}
```

### 1.4 Two-Way Binding — `[( )]`

Both directions at once — nicknamed **"banana in a box"**.

```html
<input [(ngModel)]="username" />
<p>You typed: {{ username }}</p>
```

**Setup note:** `[(ngModel)]` requires `FormsModule`.
- NgModule-based project → add `FormsModule` to that module's `imports` array.
- Standalone component → add `FormsModule` directly to the component's own `imports` array.

### 1.5 Summary Table

| Syntax | Direction | What it does |
|---|---|---|
| `{{ value }}` | class → view | Shows a value as text |
| `[ property ]` | class → view | Sets an element's property |
| `( event )` | view → class | Runs a method on an event |
| `[( ngModel )]` | class ⇄ view | Keeps both in sync, always |

**Memory trick:** Square brackets = data going **in**. Round brackets = event coming **out**. Both together = **two-way**.

---

## 2. Directives: `*ngIf`, `*ngFor`, `ngSwitch`

A **directive** is an instruction placed on an HTML element to change how it behaves — whether it shows up, how many times it repeats, or which version appears.

### 2.1 `*ngIf` — Show or Hide

Decides whether an element exists on the page **at all**. If `false`, Angular **removes it completely** from the DOM (not just CSS-hidden).

```html
<p *ngIf="isLoggedIn">Welcome back!</p>
<p *ngIf="!isLoggedIn">Please log in.</p>
```

### 2.2 `*ngFor` — Loop Over a List

Repeats one element **once for every item** in an array.

```html
<ul>
  <li *ngFor="let fruit of fruits">{{ fruit }}</li>
</ul>
```
```ts
fruits = ["Apple", "Mango", "Banana"];
```

Three items → three `<li>` elements. Angular auto-updates the list when the array changes.

### 2.3 `ngSwitch` — Pick One of Many

Picks exactly **one block** to display, based on a matching value.

```html
<div [ngSwitch]="role">
  <p *ngSwitchCase="'admin'">You are an admin</p>
  <p *ngSwitchCase="'user'">You are a user</p>
  <p *ngSwitchDefault>Unknown role</p>
</div>
```

### 2.4 Summary Table

| Directive | What it decides |
|---|---|
| `*ngIf` | Should this element exist at all? (yes/no) |
| `*ngFor` | How many copies of this element are needed? |
| `ngSwitch` | Which ONE of several elements should show? |

---

## 3. Pipes: Formatting a Value for Display

A **pipe** transforms how a value **looks on screen**, without touching the underlying data. Applied with `|` inside interpolation.

```html
<p>{{ name | uppercase }}</p>            <!-- ANGULAR -->
<p>{{ price | currency:'INR' }}</p>      <!-- Rs 500.00 -->
<p>{{ today | date:'longDate' }}</p>     <!-- August 5, 2026 -->
```

### Commonly Used Built-in Pipes

| Pipe | Purpose |
|---|---|
| `uppercase` | Converts text to ALL CAPS |
| `lowercase` | Converts text to all lowercase |
| `titlecase` | Capitalizes The First Letter Of Each Word |
| `date` | Formats a date value |
| `currency` | Formats a number as currency (e.g. `₹500.00`) |
| `number` | Formats a number (decimals, separators) |
| `percent` | Formats a number as a percentage |
| `json` | Converts an object to a readable JSON string (great for debugging) |
| `slice` | Cuts a portion of a string or array |

### 3.1 Chaining Pipes

Output of one pipe becomes input of the next, read left to right.

```html
{{ name | slice:0:5 | uppercase }}
```

`"angular development"` → `slice:0:5` → `"angul"` → `uppercase` → `"ANGUL"`

---

## 4. Custom Pipes: Building Your Own

A pipe is a class marked with `@Pipe`, containing a required `transform` method.

```ts
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({ name: "double" })
export class DoublePipe implements PipeTransform {
  transform(value: number): number {
    return value * 2;
  }
}
```

```html
<p>{{ 10 | double }}</p> <!-- shows 20 -->
```

The `transform` method receives the value on the **left** of `|`, performs the calculation, and returns the result shown on the page. Generate a pipe via CLI: `ng generate pipe double`.

### 4.1 Pipes That Take Arguments

Extra arguments are written after a colon `:`.

```ts
@Pipe({ name: "multiply" })
export class MultiplyPipe implements PipeTransform {
  transform(value: number, times: number): number {
    return value * times;
  }
}
```

```html
<p>{{ 10 | multiply:3 }}</p> <!-- shows 30 -->
```

`transform(10, 3)` → `10 * 3` → `30`

The first value (before `|`) always maps to the pipe's main `value` parameter. Anything after the colon maps to additional `transform` parameters, in order.

---

## 5. How It All Fits Together

| Concept | Role |
|---|---|
| **Binding** (`{{ }} [ ] ( ) [( )]`) | Connects class ⇄ view |
| **Directives** (`*ngIf *ngFor ngSwitch`) | Reshapes **what** is on the page |
| **Pipes** (`\| uppercase \| date \| custom`) | Changes **how** data looks |

Binding moves data between the class and the template. Directives decide **which elements exist** and **how many** appear. Pipes decide **how a value is displayed**, without changing the underlying data. Together, these three tools make an Angular template dynamic instead of static HTML.
