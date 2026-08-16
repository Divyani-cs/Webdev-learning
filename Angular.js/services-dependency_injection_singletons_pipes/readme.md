# Services, Dependency Injection, Singletons & Pipes (DAY-6)

# 1. Angular Services

## What is an Angular Service?

An **Angular Service** is a normal TypeScript class used to store **shared logic or shared data** that multiple components may need.

For example:

- Fetching data from an API
- Storing logged-in user information
- Storing a company name
- Sharing data between components
- Keeping reusable logic in one place

### Simple Definition

> **Components are mainly used for the view/UI, while Services are used for shared logic and data.**

Instead of writing the same logic again and again inside different components, we put that logic inside a service.

### Without a Service

```text
                 Application
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Header       Footer      Profile
          |           |           |
          v           v           v
       Same        Same        Same
       Logic       Logic       Logic

Problem:
Same logic/data is repeated in multiple components.
```

### With a Service

```text
                    LogoService
                        |
              +---------+---------+
              |         |         |
              v         v         v
           Header     Footer    Profile
              |         |         |
              +---------+---------+
                        |
                   Shared Data
```

Now the shared logic exists in one place.

---

# 2. Creating an Angular Service

Angular CLI can create a service.

### Full Command

```bash
ng generate service logo
```

### Short Command

```bash
ng g s logo
```

Angular creates a file such as:

```text
logo.service.ts
```

---

## Basic Service Example

```typescript
import { Injectable } from "@angular/core";

@Injectable({ providedIn: "root" })
export class LogoService {

  companyName = "Resume Loop";

  getCompanyName() {
    return this.companyName;
  }

  setCompanyName(name: string) {
    this.companyName = name;
  }

}
```

---

# 3. Understanding the Service Code

## `@Injectable`

```typescript
@Injectable(...)
```

`@Injectable` tells Angular that this class can participate in Angular's **Dependency Injection system**.

```text
@Injectable
     |
     v
Angular knows this class
can participate in DI
     |
     v
Components can request it
```

---

## `providedIn: "root"`

```typescript
@Injectable({
  providedIn: "root"
})
```

`providedIn: "root"` makes the service available throughout the application.

It also gives the service the important singleton behavior discussed later.

```text
                 Angular Application
                         |
                         v
                    LogoService
                         |
                  providedIn: root
                         |
                         v
              Available throughout app
```

---

# 4. Understanding the Service Class

Our service contains:

```typescript
export class LogoService {

  companyName = "Resume Loop";

  getCompanyName() {
    return this.companyName;
  }

  setCompanyName(name: string) {
    this.companyName = name;
  }

}
```

It can be understood as:

```text
                    LogoService
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
    companyName   getCompanyName()  setCompanyName()
          |             |             |
          |             |             |
       Stores       Reads data      Changes data
        data
```

### `companyName`

```typescript
companyName = "Resume Loop";
```

This stores the company name.

### `getCompanyName()`

```typescript
getCompanyName() {
  return this.companyName;
}
```

This method returns the current company name.

### `setCompanyName()`

```typescript
setCompanyName(name: string) {
  this.companyName = name;
}
```

This method changes the company name.

---

# 5. Why Do We Need Services?

Suppose multiple components need the same data.

Without a service:

```text
Header Component
      |
      +---- companyName

Footer Component
      |
      +---- companyName

Profile Component
      |
      +---- companyName
```

Each component has its own copy of the logic/data.

This causes:

- Repeated code
- More maintenance
- Difficult updates
- Less reusable code

With a service:

```text
                    LogoService
                        |
                Shared companyName
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
       Header        Footer        Profile
```

Now the shared information is maintained in one place.

---

# 6. Dependency Injection

## What is Dependency Injection?

**Dependency Injection (DI)** is the mechanism through which Angular provides a required service to a component or class.

A component does not need to manually create the service.

Instead, it says:

> "I need this service."

Angular provides it.

```text
Component
    |
    | "I need LogoService"
    v
Angular Dependency Injection
    |
    | provides service
    v
Component
```

---

# 7. What is a Dependency?

A **dependency** is something that a class needs to perform its work.

For example:

```text
HeaderComponent
       |
       | needs
       v
LogoService
```

Here:

- `HeaderComponent` is the class that needs something.
- `LogoService` is its dependency.
- Angular provides that dependency.

---

# 8. Without Dependency Injection

Normally, we can manually create an object:

```typescript
const service = new LogoService();
```

Here the developer is directly responsible for creating the service.

```text
Component
    |
    | creates manually
    v
new LogoService()
    |
    v
Service Object
```

But Angular provides a system for managing these dependencies.

---

# 9. With Dependency Injection

Instead of:

```typescript
const service = new LogoService();
```

we write:

```typescript
constructor(public logoService: LogoService) {}
```

Angular sees that the component requires `LogoService`.

Then Angular provides it.

```text
             HeaderComponent
                    |
                    |
             constructor()
                    |
                    v
            "I need LogoService"
                    |
                    v
            Angular DI System
                    |
                    v
              LogoService
```

### Important Rule

> **When using Angular Dependency Injection, you normally do not write `new LogoService()` yourself.**

Angular handles the creation/provision of the dependency.

---

# 10. Injecting a Service into a Component

Example:

```typescript
import { Component } from "@angular/core";
import { LogoService } from "./logo.service";

@Component({
  selector: "app-header",

  template: `
    <h1>{{ logoService.getCompanyName() }}</h1>

    <button
      (click)="logoService.setCompanyName('Snapied')">
      Change
    </button>
  `,
})
export class HeaderComponent {

  constructor(public logoService: LogoService) {}

}
```

The important line is:

```typescript
constructor(public logoService: LogoService) {}
```

This means:

```text
HeaderComponent
       |
       | needs
       v
LogoService
```

Angular provides the `LogoService`.

---

# 11. Dependency Injection Flow

```text
+--------------------------------+
| Angular creates the component  |
+---------------+----------------+
                |
                v
+--------------------------------+
| Angular checks the constructor |
|                                |
| LogoService is required        |
+---------------+----------------+
                |
                v
+--------------------------------+
| Angular DI system looks for    |
| the required service           |
+---------------+----------------+
                |
                v
+--------------------------------+
| Angular creates or reuses      |
| the service instance           |
+---------------+----------------+
                |
                v
+--------------------------------+
| Angular provides the service   |
| to the component               |
+---------------+----------------+
                |
                v
+--------------------------------+
| Component uses the service     |
+--------------------------------+
```

---

# 12. Real-Life Example of Dependency Injection

Think about electricity.

You do not build an electricity generator every time you want to use electricity.

You simply plug your device into a socket.

```text
          You
           |
           | plug in
           v
    Electricity System
           |
           | provides electricity
           v
        Your Device
```

Angular DI works similarly:

```text
       Component
           |
           | asks for service
           v
      Angular DI
           |
           | provides service
           v
       Component
```

The component **asks**, and Angular **provides**.

---

# 13. Singleton

## What is a Singleton?

When a service is provided using:

```typescript
providedIn: "root"
```

Angular creates one shared instance of that service for the application.

That one shared instance is called a **Singleton**.

### Main Idea

```text
                  LogoService
                      |
                      v
               ONE INSTANCE
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Header       Footer      Profile
```

All these components can use the same service instance.

---

# 14. Why is Singleton Important?

The main advantage is **shared state/data**.

Because multiple components use the same service instance:

```text
                SAME SERVICE
                     |
        +------------+------------+
        |            |            |
        v            v            v
      Header       Footer       Profile
        |            |            |
        +------------+------------+
                     |
                     v
                Shared Data
```

If one component changes a value in the service, another component using the same service can access the changed value.

---

# 15. Singleton Example 1 — TV Remote

Imagine a family has **one TV remote**.

```text
                  ONE TV REMOTE
                        |
             +----------+----------+
             |          |          |
             v          v          v
           Papa       Sister     Brother
```

Papa changes the channel to cricket.

Everyone sees cricket.

Then the sister takes the same remote and changes the channel to cartoons.

Everyone sees cartoons.

There is not a separate remote for every person.

### Angular Connection

```text
One TV Remote
      |
      v
One Singleton Service

Family Members
      |
      v
Angular Components
```

The TV remote represents the shared service instance.

---

# 16. Singleton Example 2 — Washing Machine

Imagine a small family has one washing machine.

```text
             ONE WASHING MACHINE
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Mother       Son       Father
```

Mother starts a washing cycle.

Later, the son uses the same machine in whatever state it was left.

There are not separate machines automatically created for each person.

### Angular Connection

```text
One Washing Machine
        |
        v
One Service Instance

Different People
        |
        v
Different Components
```

Everyone uses the same shared thing.

---

# 17. Singleton Example 3 — Shopping Cart

Imagine you walk through a mall with one shopping cart.

You add:

```text
Shirt
  |
  v
Shoes
  |
  v
Toy
```

Everything remains inside the same cart.

```text
Shop 1 ----> Shirt ----+
                       |
Shop 2 ----> Shoes ----+----> ONE CART ----> Billing
                       |
Shop 3 ----> Toy ------+
```

The same idea applies to a singleton service.

Different components can add or read information from the same service.

---

# 18. What Happens Without a Singleton?

Suppose every component had a separate service instance.

```text
Header
  |
  +----> Service Copy A

Footer
  |
  +----> Service Copy B

Profile
  |
  +----> Service Copy C
```

If Header changes its service:

```text
Header
   |
   v
Service Copy A
   |
   v
companyName = "Snapied"
```

Footer still has:

```text
Service Copy B
   |
   v
companyName = "Resume Loop"
```

So Footer would not automatically see Header's change.

With a singleton:

```text
Header ----+
           |
Footer ----+----> SAME SERVICE INSTANCE
           |
Profile ---+
```

Everyone works with the same shared service.

---

# 19. Sharing Data Through a Singleton

One of the main benefits of a service is sharing data between components.

The components do not have to be parent and child.

For example:

```text
Header Component
       |
       | changes company name
       v
+------------------+
|   LogoService    |
|                  |
| companyName      |
+--------+---------+
         |
         | same instance
         v
Footer Component
       |
       | reads company name
       v
   "Snapied"
```

### Header changes the value

```typescript
this.logoService.setCompanyName("Snapied");
```

### Footer reads the value

```html
<p>{{ logoService.getCompanyName() }}</p>
```

The Footer can show:

```text
Snapied
```

because both components are using the same service instance.

---

# 20. Important Mistake When Sharing Data

Suppose a component copies the service value only once inside `ngOnInit()`:

```typescript
this.companyName = this.logoService.getCompanyName();
```

At that moment:

```text
LogoService
     |
     v
"Resume Loop"
     |
     | copied once
     v
Component.companyName
     |
     v
"Resume Loop"
```

Later, the service changes:

```text
LogoService
     |
     v
"Snapied"
```

But the component's copied property may still contain:

```text
"Resume Loop"
```

because it took the value only once.

### Better Approach

Read the service value directly in the template:

```html
{{ logoService.getCompanyName() }}
```

Conceptually:

```text
             LogoService
                  |
                  | current value
                  v
               Template
                  |
                  v
            Latest Display
```

Think of it like a whiteboard.

If you take a photograph of the whiteboard once, you only have the old information.

If you keep looking at the actual whiteboard, you can see the latest information.

---

# 21. Why Angular 13 Instead of Angular 22?

A common question is:

> "If newer Angular versions exist, why are we learning Angular 13?"

The lesson gives several reasons.

---

## Reason 1 — Core Ideas Are the Same

Important Angular concepts include:

- Components
- Services
- Dependency Injection
- Pipes
- Modules

The fundamental concepts remain transferable across Angular versions.

```text
Angular 13
    |
    +---- Components
    |
    +---- Services
    |
    +---- Dependency Injection
    |
    +---- Pipes
    |
    +---- Modules
              |
              v
       Strong Fundamentals
              |
              v
       Newer Angular Versions
```

What you learn about the core concepts can be carried into newer versions.

---

## Reason 2 — Real Applications Can Use Older Versions

Companies do not necessarily upgrade every application every year.

Therefore, developers can encounter older Angular applications in real projects.

```text
        New Angular Versions
                 |
                 | coexist with
                 v
       Older Production Apps
                 |
                 v
      Developers may need
      older-version knowledge
```

Therefore, learning an older version is not automatically useless.

---

## Reason 3 — Angular 13 Is Stable and Well Documented

Angular 13 has many existing:

- Tutorials
- Examples
- Answers
- Learning resources

This makes it easier to find information when learning.

---

## Reason 4 — Fewer Moving Parts for Beginners

Newer Angular versions introduce additional features and concepts.

The lesson specifically mentions:

- Standalone components
- Signals

These features are useful, but they can add complexity when someone is still learning the basic Angular concepts.

A simple learning path is:

```text
Learn Fundamentals
        |
        v
Understand Angular Concepts
        |
        v
Learn New Features
        |
        v
Move to Newer Angular
```

### Main Takeaway

> Learn the strong fundamentals first. Once the basics are clear, moving to newer Angular versions becomes easier.

---

# 22. Pipes

## What is a Pipe?

A **Pipe** changes how a value looks when it is displayed in an Angular template.

It changes the **display format** without changing the actual original data.

Pipes use the pipe symbol:

```text
|
```

### Basic Syntax

```html
{{ value | pipeName }}
```

### Basic Flow

```text
Original Value
      |
      v
    PIPE
      |
      v
Formatted Display Value
```

---

# 23. Built-in Pipes

Angular provides several built-in pipes.

Common examples:

| Pipe | Purpose |
|------|---------|
| `uppercase` | Converts text to uppercase |
| `lowercase` | Converts text to lowercase |
| `titlecase` | Formats text as title case |
| `date` | Formats a date |
| `currency` | Formats currency |
| `number` | Formats a number |
| `percent` | Formats a percentage |
| `json` | Displays data as JSON |
| `slice` | Takes a part of a string/array |

---

# 24. uppercase Pipe

Example:

```html
<p>{{ name | uppercase }}</p>
```

Suppose:

```text
name = "Resume Loop"
```

The displayed result becomes:

```text
RESUME LOOP
```

### Flow

```text
"Resume Loop"
      |
      | uppercase
      v
"RESUME LOOP"
```

---

# 25. lowercase Pipe

Example:

```html
<p>{{ name | lowercase }}</p>
```

Flow:

```text
"Resume Loop"
      |
      | lowercase
      v
"resume loop"
```

---

# 26. titlecase Pipe

The `titlecase` pipe formats text into title case.

Example:

```html
<p>{{ name | titlecase }}</p>
```

It is useful when text needs to be displayed in a title-like format.

---

# 27. currency Pipe

Example:

```html
<p>{{ price | currency:'INR' }}</p>
```

If:

```text
price = 500
```

The value can be displayed in Indian currency formatting:

```text
₹500.00
```

The important part is:

```text
currency:'INR'
```

Here `INR` is an argument given to the currency pipe.

### Flow

```text
500
 |
 | currency
 | argument = INR
 v
₹500.00
```

---

# 28. date Pipe

Example:

```html
<p>{{ today | date:'longDate' }}</p>
```

The pipe formats the date for display.

For example:

```text
August 7, 2026
```

The exact output depends on the actual date value.

### Flow

```text
Date Value
    |
    | date:'longDate'
    v
Long Date Format
```

---

# 29. Pipe Arguments

A pipe can receive an argument after a colon.

### General Syntax

```html
{{ value | pipeName:argument }}
```

Example:

```html
{{ price | currency:'INR' }}
```

Flow:

```text
price
  |
  v
currency pipe
  |
  | argument = INR
  v
Formatted Currency
```

---

# 30. Chaining Pipes

Angular allows multiple pipes to be used together.

Example:

```html
{{ name | slice:0:5 | uppercase }}
```

The output of the first pipe becomes the input of the next pipe.

### Flow

```text
Original Value
      |
      v
slice:0:5
      |
      v
First 5 Characters
      |
      v
uppercase
      |
      v
Final Result
```

### General Pipe Chain

```text
Value
  |
  v
Pipe 1
  |
  v
Pipe 2
  |
  v
Pipe 3
  |
  v
Final Display
```

Pipes are processed from left to right.

---

# 31. Custom Pipes

Sometimes the built-in Angular pipes do not do exactly what you need.

In that case, you can create your own **Custom Pipe**.

A custom pipe is a class that uses:

```typescript
@Pipe
```

and implements:

```typescript
PipeTransform
```

The main method is:

```typescript
transform()
```

---

# 32. Creating a Custom Pipe

Angular CLI can create a custom pipe:

```bash
ng generate pipe double
```

Short form:

```bash
ng g pipe double
```

Example custom pipe:

```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({ name: "double" })
export class DoublePipe implements PipeTransform {

  transform(value: number): number {
    return value * 2;
  }

}
```

---

# 33. Understanding `@Pipe`

This code:

```typescript
@Pipe({ name: "double" })
```

tells Angular that this class is a pipe.

The pipe will be used in the template with the name:

```text
double
```

Therefore:

```html
{{ 10 | double }}
```

means:

```text
Use the "double" pipe on 10.
```

---

# 34. Understanding `PipeTransform`

The class uses:

```typescript
implements PipeTransform
```

This means the pipe follows Angular's pipe transformation structure.

The main method is:

```typescript
transform()
```

---

# 35. Understanding `transform()`

Example:

```typescript
transform(value: number): number {
  return value * 2;
}
```

The `transform()` method:

1. Receives the input value.
2. Performs some operation.
3. Returns the transformed value.

### Flow

```text
Input
  |
  v
transform()
  |
  | Perform operation
  v
Output
```

For the `double` pipe:

```text
10
 |
 v
transform(10)
 |
 | 10 × 2
 v
20
```

---

# 36. Using a Custom Pipe

After creating the pipe:

```html
<p>{{ 10 | double }}</p>
```

The result is:

```text
20
```

### Complete Flow

```text
Template
   |
   | 10 | double
   v
DoublePipe
   |
   v
transform(10)
   |
   | 10 × 2
   v
20
   |
   v
Displayed in Template
```

---

# 37. Custom Pipe with an Argument

A custom pipe can also accept arguments.

Example:

```typescript
@Pipe({ name: "multiply" })
export class MultiplyPipe implements PipeTransform {

  transform(value: number, times: number): number {
    return value * times;
  }

}
```

Here:

```typescript
value
```

is the original value.

And:

```typescript
times
```

is the argument supplied to the pipe.

### Using It

```html
<p>{{ 10 | multiply:3 }}</p>
```

### Flow

```text
10
 |
 | multiply
 | argument = 3
 v
transform(10, 3)
 |
 | 10 × 3
 v
30
```

Result:

```text
30
```

---

# 38. Angular 13 Module Declaration for Custom Pipes

In Angular 13, a custom pipe needs to be included in the appropriate module's `declarations`.

Conceptually:

```text
Custom Pipe
     |
     v
Angular Module
     |
     v
declarations
     |
     v
Pipe can be used by
templates in that module
```

Example:

```typescript
@NgModule({
  declarations: [
    DoublePipe
  ]
})
export class AppModule {}
```

---

# 39. Built-in Pipe vs Custom Pipe

| Built-in Pipe | Custom Pipe |
|---|---|
| Already provided by Angular | Created by the developer |
| Ready to use | Must be written |
| Examples: `uppercase`, `date`, `currency` | Examples: `double`, `multiply` |
| Used for common formatting | Used for application-specific transformation |

### Decision Flow

```text
Need to format/transform a value
              |
              v
     Does Angular already
     provide this pipe?
          /        \
        YES         NO
         |           |
         v           v
   Built-in Pipe  Custom Pipe
```

---

# 40. Service vs Pipe

Services and pipes have completely different purposes.

| Service | Pipe |
|---|---|
| Stores shared logic/data | Formats/transforms displayed values |
| Used by components/classes | Mainly used inside templates |
| Can store shared application data | Mainly performs transformation |
| Uses Dependency Injection | Uses `|` syntax |
| Example: `LogoService` | Example: `uppercase`, `DoublePipe` |

### Easy Way to Remember

```text
SERVICE
   |
   v
Shared Logic + Shared Data
```

```text
PIPE
   |
   v
Display Formatting + Transformation
```

---

# 41. Complete Angular Day 3 Concept Map

```text
                         ANGULAR
                            |
             +--------------+--------------+
             |                             |
             v                             v
         SERVICES                         PIPES
             |                             |
             v                             v
     Shared Logic/Data              Format/Transform
             |                             |
             v                             v
       @Injectable                   Built-in Pipes
             |                             |
             v                  +----------+----------+
    Dependency Injection        |          |          |
             |              uppercase    date     currency
             v
    providedIn: "root"
             |
             v
         Singleton
             |
             v
     One Shared Instance
             |
      +------+------+
      |      |      |
      v      v      v
   Header  Footer Profile

                                  Custom Pipes
                                       |
                                       v
                                     @Pipe
                                       |
                                       v
                                  transform()
```

---

# 42. Complete Service + DI + Singleton Flow

```text
                 Angular Application
                         |
                         v
                    LogoService
                         |
                         v
                    @Injectable
                         |
                         v
                 providedIn: "root"
                         |
                         v
                Singleton Instance
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
          Header       Footer      Profile
             |           |           |
             +-----------+-----------+
                         |
                         v
                    Shared Data
                    companyName
                         |
              +----------+----------+
              |                     |
              v                     v
      setCompanyName()       getCompanyName()
              |                     |
              v                     v
       Change shared data     Read shared data
```

---

# 43. Complete Pipe Processing Flow

```text
                 Template Value
                       |
                       v
                  Pipe Symbol |
                       |
                       v
              Built-in / Custom Pipe
                       |
                       v
                 Transformation
                       |
                       v
                Displayed Result
```

Example:

```text
"Resume Loop"
      |
      v
uppercase
      |
      v
"RESUME LOOP"
```

Another example:

```text
10
 |
 v
multiply:3
 |
 v
30
```

---
# 44. Complete Angular Day 3 Flow

```text
                         ANGULAR
                            |
             +--------------+--------------+
             |                             |
             v                             v
         COMPONENTS                       PIPES
             |                             |
             | need shared                 |
             | work/data                  |
             v                             |
          SERVICES                         |
             |                             |
             v                             |
        @Injectable                        |
             |                             |
             v                             |
    Dependency Injection                   |
             |                             |
             v                             |
    providedIn: "root"                     |
             |                             |
             v                             |
         Singleton                         |
             |                             |
             v                             |
     One Shared Instance                   |
             |                             |
       +-----+-----+                       |
       |     |     |                       |
       v     v     v                       v
    Header Footer Profile          Built-in / Custom
       |     |     |                     |
       +-----+-----+                     |
             |                            |
             v                            v
        Shared Data                 transform()
                                          |
                                          v
                                   Displayed Result
```

---

# 45. Service Creation Flow

```text
Developer
    |
    | ng g s logo
    v
Angular CLI
    |
    v
logo.service.ts
    |
    v
@Injectable
    |
    v
providedIn: "root"
    |
    v
Service available
throughout application
```

---

# 46. Component + Service Flow

```text
                Component
                    |
                    | needs shared work/data
                    v
                 Service
                    |
                    | provided through
                    v
           Dependency Injection
                    |
                    v
              Angular provides
              service instance
                    |
                    v
                Component
                    |
                    v
              Uses the service
```

---

# 47. Shared Data Flow

```text
                  LogoService
                      |
                  companyName
                      |
          +-----------+-----------+
          |                       |
          v                       v
      Header                   Footer
          |                       |
          |                       |
   setCompanyName()        getCompanyName()
          |                       |
          v                       v
      "Snapied"              "Snapied"
```

Both components are reading/writing the same service data.

---

# 48. Pipe Transformation Flow

```text
             Original Data
                  |
                  v
            Angular Template
                  |
                  v
             Pipe Operator |
                  |
                  v
          Pipe Transformation
                  |
                  v
            Displayed Value
```

Example:

```text
"resume loop"
      |
      v
uppercase
      |
      v
"RESUME LOOP"
```

---

# 49. Multiple Pipes Flow

```text
        Original Value
              |
              v
          Pipe 1
              |
              v
          Pipe 2
              |
              v
          Pipe 3
              |
              v
       Final Display
```

Example:

```html
{{ name | slice:0:5 | uppercase }}
```

Flow:

```text
Name
 |
 v
slice:0:5
 |
 v
First 5 Characters
 |
 v
uppercase
 |
 v
Final uppercase result
```

---

# 50. One-Line Memory Rules

```text
Service
→ A class used for shared logic and shared data.

@Injectable
→ Marks a class as injectable for Angular's DI system.

Dependency Injection
→ Angular provides the required dependency to a component/class.

providedIn: "root"
→ Makes the service available across the application and supports singleton behavior.

Singleton
→ One shared service instance.

Shared Data
→ Multiple components can use the same service instance to access shared data.

Pipe
→ Formats or transforms a value for display.

Built-in Pipe
→ A pipe already provided by Angular.

Custom Pipe
→ A pipe created by the developer.

@Pipe
→ Decorator used to define a custom pipe.

PipeTransform
→ Interface used by a pipe class.

transform()
→ Method that receives the value and returns the transformed value.

Pipe Argument
→ Extra value passed after ":".

Pipe Chaining
→ Applying multiple pipes one after another.

|
→ Pipe operator used in Angular templates.
```

---

# 51. Final Quick Revision

## Service

```text
Service
   ↓
Shared Logic + Shared Data
```

Example:

```typescript
@Injectable({ providedIn: "root" })
export class LogoService {

  companyName = "Resume Loop";

}
```

---

## Dependency Injection

```text
Component
    ↓
Requests Service
    ↓
Angular DI
    ↓
Provides Service
    ↓
Component Uses Service
```

---

## Singleton

```text
             ONE SERVICE INSTANCE
                      |
          +-----------+-----------+
          |           |           |
        Header      Footer      Profile
```

---

## Pipe

```text
Original Value
      ↓
     Pipe
      ↓
Formatted / Transformed Value
```

Example:

```html
{{ name | uppercase }}
```

---

## Custom Pipe

```text
@Pipe
   ↓
Pipe Class
   ↓
PipeTransform
   ↓
transform()
   ↓
Transformed Value
```

---

# 52. Final Mental Model

```text
                         ANGULAR
                            |
             +--------------+--------------+
             |                             |
             v                             v
         COMPONENTS                       PIPES
             |                             |
             | need shared work            |
             | or data                     |
             v                             |
          SERVICES                         |
             |                             |
             v                             |
        @Injectable                        |
             |                             |
             v                             |
    Dependency Injection                   |
             |                             |
             v                             |
    providedIn: "root"                     |
             |                             |
             v                             |
         SINGLETON                         |
             |                             |
             v                             |
     One Shared Instance                   |
             |                             |
       +-----+-----+                       |
       |     |     |                       |
       v     v     v                       v
    Header Footer Profile          Built-in / Custom
       |     |     |                       |
       +-----+-----+                       v
             |                         transform()
             v                            |
        Shared Data                       v
                                    Displayed Result
```

# Final Summary

- **Service** → Used for shared logic and shared data.
- **`@Injectable`** → Makes a class available for Angular Dependency Injection.
- **Dependency Injection** → Angular provides the required service to a component.
- **`providedIn: "root"`** → Makes the service available throughout the application and supports singleton behavior.
- **Singleton** → One shared service instance.
- **Shared Data** → Multiple components can access the same service data.
- **Pipe** → Formats or transforms a value for display.
- **Built-in Pipe** → Already provided by Angular.
- **Custom Pipe** → Created by the developer.
- **`@Pipe`** → Defines a custom pipe.
- **`PipeTransform`** → Interface used by custom pipes.
- **`transform()`** → Receives a value, transforms it, and returns the result.
- **Pipe Argument** → Extra value passed using `:`.
- **Pipe Chaining** → Multiple pipes can be applied one after another.

