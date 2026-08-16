## Component Lifecycle Hooks (DAY-4)

## 1. The Big Idea (in one line)

> A **lifecycle hook** is a method that Angular calls **automatically**, at a fixed moment in a component's life. You just write the method — Angular decides *when* to call it.

Think of it like a **delivery tracking app**:
- You don't manually check "has my order been packed yet?"
- The app **notifies you automatically** at each stage: Order Placed → Packed → Shipped → Delivered.

Angular does the same thing with components. It notifies your code automatically at each stage of a component's life — you just have to "listen" by writing the right method.

**Golden rule:** Every lifecycle hook method name starts with `ng` (e.g. `ngOnInit`, `ngOnDestroy`). This is Angular's way of saying "this method belongs to my internal system, not to you."

---

## 2. Why Does a Component Even Have a "Life"?

A component in Angular is not just static HTML. It goes through real stages, just like a living thing:

```
   BORN            LIVING             UPDATING           DYING
(created)     (shown on screen)    (data changes)     (removed)
    │                 │                   │                │
    ▼                 ▼                   ▼                ▼
constructor()   ngAfterViewInit()   ngOnChanges()      ngOnDestroy()
ngOnChanges()                       ngDoCheck()
ngOnInit()
```

At **each stage**, Angular gives you a "hook" — a chance to plug in your own code.

---

## 3. The Full Lifecycle — Order Matters!

Here is the **official order** Angular follows, from the moment a component is created to the moment it dies:

```
┌─────────────────────────────────────────────────────────────────┐
│                     COMPONENT LIFECYCLE FLOW                     │
└─────────────────────────────────────────────────────────────────┘

   START
     │
     ▼
┌───────────────┐
│  constructor() │   ← plain JS/TS class setup (NOT a real hook)
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  ngOnChanges()  │   ← runs FIRST TIME too (if component has @Input)
└───────┬─────────┘
        │
        ▼
┌────────────────┐
│   ngOnInit()    │   ← runs ONCE. Best place for setup / API calls
└───────┬─────────┘
        │
        ▼
┌─────────────────┐
│   ngDoCheck()    │   ← runs on EVERY change detection cycle
└────────┬─────────┘
         │
         ▼
┌──────────────────────┐
│  ngAfterViewInit()     │   ← runs ONCE, after the HTML view is ready
└──────────┬─────────────┘
           │
           ▼
   ┌───────────────────────────────┐
   │   COMPONENT IS ALIVE ON PAGE    │
   │   (user interacts, data changes)│
   └───────────────┬─────────────────┘
                    │
      Whenever @Input changes:
                    │
                    ▼
         ┌────────────────────┐
         │   ngOnChanges()      │  (runs again)
         └──────────┬───────────┘
                    │
      Whenever ANYTHING changes:
                    │
                    ▼
         ┌────────────────────┐
         │    ngDoCheck()       │  (runs again)
         └──────────┬───────────┘
                    │
        ... this loop continues while
          the component is on screen ...
                    │
                    ▼
      When component is about to be removed
      (e.g. user navigates to another page):
                    │
                    ▼
         ┌────────────────────┐
         │   ngOnDestroy()      │  ← runs ONCE. Clean-up time!
         └──────────┬───────────┘
                    │
                    ▼
                  END
```

---

## 4. Quick Reference Table

| Hook | Runs When? | How Many Times? | What You Do Here |
|---|---|---|---|
| `constructor()` | Component class is created | Once | Simple variable setup (not real Angular data yet) |
| `ngOnChanges()` | An `@Input` value changes | Every time input changes (including the first time) | React to new data from parent |
| `ngOnInit()` | Right after component is created & inputs are set | Once | Load data, call API, set starting values |
| `ngDoCheck()` | Every single change detection run | Very frequently | Custom manual checks (rarely used) |
| `ngAfterViewInit()` | Once the template/HTML is fully rendered | Once | Access DOM elements, work with child components |
| `ngOnDestroy()` | Just before component is removed | Once | Clean up: stop timers, unsubscribe, remove listeners |

**Most used in real projects:** `ngOnInit()` and `ngOnDestroy()`. These two alone solve 90% of use cases.

---

## 5. Deep Dive — `ngOnInit()`

### What it means
`ngOnInit` runs **once**, right after Angular has finished creating the component **and** has set its `@Input` values. This is your "component is ready, start doing real work now" signal.

### Real-life comparison
Imagine you just moved into a new house (component created). `ngOnInit` is like **unpacking your bags and setting up furniture** — the setup work you do right after arriving, before you start "living" there.

### Code Example
```typescript
import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-hello",
  template: `<h3>{{ message }}</h3>`,
})
export class HelloComponent implements OnInit {
  message: string = "";

  ngOnInit() {
    this.message = "Component is ready!";
    console.log("ngOnInit ran");
  }
}
```

**What happens:** When `<app-hello>` appears on the page, Angular calls `ngOnInit()` automatically, sets `message`, and the browser shows **"Component is ready!"**

### Why not just use the constructor?

This is the **most common confusion** for beginners. Here's the clear difference:

```
┌─────────────────────┐         ┌─────────────────────┐
│     constructor()     │         │      ngOnInit()       │
├─────────────────────┤         ├─────────────────────┤
│ Runs FIRST            │         │ Runs AFTER constructor │
│ Plain TypeScript class │         │ Angular-specific        │
│ @Input NOT ready yet   │         │ @Input IS ready          │
│ Use for: simple wiring,│         │ Use for: real setup,     │
│ dependency injection   │         │ API calls, data loading   │
└─────────────────────┘         └─────────────────────┘
```

**Rule of thumb:** Constructor = simple wiring (like injecting a service). `ngOnInit` = real setup work (like calling an API).

---

## 6. Deep Dive — `ngOnChanges()`

### What it means
`ngOnChanges` runs **every time** a parent component changes a value passed via `@Input`. It also runs **once at the start**, when the input first gets its value.

### Real-life comparison
Think of a **food delivery tracker widget** on your phone. Every time the restaurant updates the order status (parent sends new data), the widget **reacts and updates itself** — that reaction is `ngOnChanges`.

### Code Example
```typescript
import { Component, Input, OnChanges } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Name is: {{ name }}</p>`,
})
export class ChildComponent implements OnChanges {
  @Input() name: string = "";

  ngOnChanges() {
    console.log("Input changed, name is now:", this.name);
  }
}
```

### Visual: Parent → Child Communication

```
   PARENT COMPONENT                    CHILD COMPONENT
┌─────────────────────┐            ┌──────────────────────┐
│  name = "Unishka"     │  @Input   │   ngOnChanges()         │
│                        │ ────────► │   fires automatically   │
│  (changes name later)  │           │   whenever "name"        │
│  name = "Rawat"        │ ────────► │   value changes here    │
└─────────────────────┘            └──────────────────────┘
```

**Key point:** `ngOnChanges` only fires for `@Input` properties — not for internal variables that change on their own.

---

## 7. Deep Dive — `ngOnDestroy()`

### What it means
`ngOnDestroy` runs **once**, right before Angular removes the component from the page. This is your **last chance to clean up** anything that would otherwise keep running in the background.

### Real-life comparison
Imagine leaving a hotel room (component being destroyed). Before you leave, you **turn off the lights, switch off the AC, and check out** — that's cleanup. If you forget, the AC keeps running and wasting electricity even though nobody's there — that's a **memory leak**.

### Code Example
```typescript
import { Component, OnInit, OnDestroy } from "@angular/core";

@Component({
  selector: "app-timer",
  template: `<p>Seconds: {{ count }}</p>`,
})
export class TimerComponent implements OnInit, OnDestroy {
  count: number = 0;
  timerId: any;

  ngOnInit() {
    // start a timer when the component appears
    this.timerId = setInterval(() => this.count++, 1000);
  }

  ngOnDestroy() {
    // stop the timer before the component is removed
    clearInterval(this.timerId);
    console.log("Timer cleaned up");
  }
}
```

### Visual: Why Cleanup Matters

```
WITHOUT ngOnDestroy cleanup:
┌───────────────┐     component removed     ┌───────────────┐
│  Timer running   │  ─────────────────────►  │  Timer STILL     │
│  on screen        │      (user navigates)     │  running in       │
│                    │                            │  background! ❌  │
└───────────────┘                            └───────────────┘
                                              → MEMORY LEAK

WITH ngOnDestroy cleanup:
┌───────────────┐     component removed     ┌───────────────┐
│  Timer running   │  ─────────────────────►  │  Timer STOPPED    │
│  on screen        │   ngOnDestroy() called    │  cleanly ✅        │
│                    │   → clearInterval()        │                    │
└───────────────┘                            └───────────────┘
```

**Things you should ALWAYS clean up in `ngOnDestroy`:**
- `setInterval` / `setTimeout` timers
- RxJS subscriptions (`.subscribe()`)
- Event listeners added manually (e.g. `window.addEventListener`)
- WebSocket connections

---

## 8. Simple Way to Remember Everything

```
BORN     →  constructor()  →  ngOnChanges() (first time)  →  ngOnInit()
LIVING   →  ngOnChanges() runs again on every input change
VIEW READY →  ngAfterViewInit() (once, when HTML is fully rendered)
DYING    →  ngOnDestroy() (once, just before removal)
```

## 9. Common Mistakes Beginners Make

| Mistake | Why It's Wrong | Fix |
|---|---|---|
| Doing API calls inside `constructor()` | `@Input` and view aren't ready yet | Move API calls to `ngOnInit()` |
| Forgetting `ngOnDestroy()` for timers/subscriptions | Causes memory leaks, app slows down over time | Always clean up in `ngOnDestroy()` |
| Expecting `ngOnChanges()` to fire for non-`@Input` variables | It only tracks `@Input` properties | Use `ngDoCheck()` if you need to track other changes (rarely needed) |
| Confusing `ngOnInit()` with constructor | They run at different times with different data readiness | Constructor = wiring, `ngOnInit` = setup |

---

## 10. Summary — Remember This

- Lifecycle hooks = methods Angular calls **automatically** at set moments in a component's life.
- **`ngOnInit()`** → runs once at the start. Do your setup here (load data, set values, call APIs).
- **`ngOnChanges()`** → runs when an `@Input` changes. React to new data from parent.
- **`ngOnDestroy()`** → runs once at the end. Clean up here (timers, subscriptions, listeners).

> **Rule of thumb:** Set up in `ngOnInit`, clean up in `ngOnDestroy`. These two cover most real-world needs.

