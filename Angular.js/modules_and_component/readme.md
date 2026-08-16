
# Angular Modules and Components (DAY-3)

**Topic:** Angular Modules (NgModule) + Component Communication

## Part 1: What is an Angular Module (NgModule)?

An **NgModule** is a container. It groups related pieces of an Angular app together — components, services, directives, and pipes — so Angular knows how everything fits and works together.

Think of it like a **folder that organizes your app's parts** so Angular can compile and run them correctly.

### The 4 Main Things a Module Holds

| Property | What it means | Simple explanation |
|---|---|---|
| `declarations` | List of components, directives, pipes that belong to THIS module | "These are mine, I own them" |
| `imports` | Other modules this module needs to use | "I am borrowing features from these modules" |
| `exports` | Declarations from this module that other modules are allowed to use | "I am sharing these with others" |
| `providers` | Services available for dependency injection in this module | "These services can be used/injected here" |

### Example Structure

```typescript
@NgModule({
  declarations: [AppComponent, HelloComponent],
  imports: [BrowserModule, FormsModule],
  exports: [HelloComponent],
  providers: [UserService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Flowchart: Inside a Module

```
                ┌─────────────────────────────┐
                │        NgModule (Box)        │
                │                              │
   declarations │  → HelloComponent            │
                │  → AppComponent              │
                │                              │
   imports      │  → BrowserModule             │
                │  → FormsModule                │
                │                              │
   exports      │  → HelloComponent (shared)   │
                │                              │
   providers    │  → UserService                │
                └─────────────────────────────┘
```

---

## Part 2: Why Do Modules Exist?

Modules exist to solve real problems in large applications. Four main reasons:

```
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ ORGANISATION │   │ REUSABILITY  │   │  SEPARATION  │   │ LAZY LOADING │
│              │   │              │   │              │   │              │
│ Groups       │   │ Share one    │   │ Keeps        │   │ Load only    │
│ related code │   │ module's     │   │ features      │   │ needed       │
│ together     │   │ features     │   │ independent  │   │ modules,     │
│              │   │ across app   │   │ of each other│   │ not all at   │
│              │   │              │   │              │   │ once         │
└──────────────┘   └──────────────┘   └──────────────┘   └──────────────┘
```

1. **Organisation** — Instead of one giant messy file, code is split into logical groups (e.g., `UserModule`, `AdminModule`).
2. **Reusability** — A module built once (like a `SharedModule` with common buttons/cards) can be reused in many places.
3. **Separation** — Different features (e.g., Login, Dashboard, Reports) stay independent, making bugs easier to isolate.
4. **Lazy Loading** — App loads faster because Angular only loads a module when the user actually navigates to that feature, not all at the start.

---

## Part 3: Important Point — Modules Are Now Optional

> ⚠️ **This is a key modern-Angular update.**

- Older Angular (before v14) — **Every component had to belong to a module.** No exceptions.
- Modern Angular (v14+) — **Standalone Components** were introduced. A component can now work on its own, without being declared inside an `NgModule`.

### Why This Matters

```
   OLD ANGULAR WAY                    MODERN ANGULAR WAY
┌───────────────────┐             ┌───────────────────┐
│     NgModule        │             │  Standalone         │
│  ┌──────────────┐   │             │  Component            │
│  │  Component    │   │    VS      │  (works alone,       │
│  │  (needs module)│   │             │   imports its own    │
│  └──────────────┘   │             │   dependencies        │
└───────────────────┘             │   directly)            │
                                     └───────────────────┘
```

**Rule of thumb:**
- Learn NgModules → to **read and understand old Angular projects** (still very common in real jobs).
- Use Standalone Components → for **new Angular projects going forward** (this is the modern default).

Good question to confirm with Dinesh Sir: check whether your `resume-forge` project has an `app.module.ts` file. If it does NOT, your project is already using standalone components.

---

## Part 4: Component Communication (Theory)

In Angular, components are often nested — a **parent** component contains a **child** component. They need ways to talk to each other.

### The Two Directions

```
              PARENT COMPONENT
                     │
        @Input()     │     @Output()
       (data goes    │    (event goes
          DOWN)       │        UP)
                     │
                     ▼
              CHILD COMPONENT
```

### @Input() — Parent sends data DOWN to Child

- Used when a **parent** wants to pass data **into** a child component.
- The child receives it like a normal property.

```typescript
// child.component.ts
@Input() userName: string;
```

```html
<!-- parent.component.html -->
<app-child [userName]="parentUserName"></app-child>
```

### @Output() — Child sends events UP to Parent

- Used when a **child** wants to tell the **parent** that something happened (like a button click).
- Works together with `EventEmitter`.
- The parent listens using `$event`.

```typescript
// child.component.ts
@Output() itemSelected = new EventEmitter<string>();

selectItem() {
  this.itemSelected.emit('Item A');
}
```

```html
<!-- parent.component.html -->
<app-child (itemSelected)="onItemSelected($event)"></app-child>
```

```typescript
// parent.component.ts
onItemSelected(value: string) {
  console.log(value); // 'Item A'
}
```

### Flowchart: Full Communication Cycle

```
   PARENT                                      CHILD
┌─────────────┐                          ┌─────────────┐
│              │  [userName]="data"       │              │
│              │ ───────────────────────► │  @Input()    │
│              │      (data flows down)    │  userName    │
│              │                          │              │
│              │  (itemSelected)="fn()"    │              │
│  onItemSel-  │ ◄─────────────────────── │  @Output()   │
│  ected()     │   (event flows up via     │  itemSelected│
│              │    $event)                │  .emit()     │
└─────────────┘                          └─────────────┘
```

---

## Part 5: Understanding Brackets in Angular

This is one of the most confusing parts for beginners — here is the simple breakdown:

| Syntax | Name | Direction | What it does |
|---|---|---|---|
| `{{ }}` | Interpolation | Component → Template (display only) | Displays a value from TypeScript inside HTML text |
| `[ ]` | Property Binding | Parent → Child (data IN) | Passes data INTO an element or component property |
| `( )` | Event Binding | Child → Parent (event OUT) | Listens for an event and runs a function when it happens |
| `[( )]` | Two-Way Binding (Banana in a Box) | Both directions | Combines property binding + event binding together |

### 1. `{{ }}` — Interpolation

Used to **display** a value from your TypeScript class directly in the HTML.

```typescript
name = 'Unishka';
```
```html
<h1>Hello, {{ name }}!</h1>
<!-- Output: Hello, Unishka! -->
```

- It is **one-way**: data flows from component → template only, just to show text.
- Cannot be used to set attributes or listen to events — only to display data.

### 2. `[ ]` — Property Binding (Data IN)

Used to **pass data** into an HTML element's property or a child component's `@Input`.

```html
<img [src]="imageUrl">
<app-child [userName]="name"></app-child>
```

- One-way: component → element/child.
- Square brackets = "sending data in."

### 3. `( )` — Event Binding (Event OUT)

Used to **listen** for events like clicks, typing, or custom `@Output` events, and run a method in response.

```html
<button (click)="sayHello()">Click Me</button>
```

- One-way: element/child → component.
- Round brackets = "catching an event out."

### 4. `[( )]` — Two-Way Binding (Bonus, good to know)

Combines both — often used with form inputs via `ngModel`.

```html
<input [(ngModel)]="name">
```

- This is called **"banana in a box"** (funny nickname because `[( )]` looks like a banana inside a box).
- Data flows both ways: if `name` changes in TypeScript, the input updates; if user types in the input, `name` updates too.

### Quick Visual Summary

```
{{ }}   →  DISPLAY ONLY        (one-way, component → HTML text)
[ ]     →  DATA IN             (one-way, component → element/child)
( )     →  EVENT OUT           (one-way, element/child → component)
[( )]   →  DATA IN + EVENT OUT (two-way, both directions at once)
```

---

## Summary —  Key Takeaways

1. **NgModule** = container holding `declarations`, `imports`, `exports`, `providers`.
2. Modules exist for **organisation, reusability, separation, and lazy loading**.
3. Modern Angular (v14+) uses **standalone components by default** — modules are now optional. Learn modules to read old code; use standalone for new work.
4. **@Input()** sends data DOWN (parent → child). **@Output()** with `EventEmitter` sends events UP (child → parent), read using `$event`.
5. Bracket cheat sheet:
   - `{{ }}` = Interpolation (display, one-way)
   - `[ ]` = Property Binding (data in, one-way)
   - `( )` = Event Binding (event out, one-way)
   - `[( )]` = Two-Way Binding (both directions)


