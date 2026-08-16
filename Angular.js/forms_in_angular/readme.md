## Forms in Angular (DAY-7)

## 1. What Are Forms in Angular?

Forms are used whenever an Angular application needs to collect information from a user.

Common examples include:

- Login forms
- Signup forms
- Registration forms
- Search forms
- Contact forms
- Profile forms
- Data-entry forms

Angular provides two main approaches for creating forms:

```text
                         ANGULAR FORMS
                              |
                +-------------+-------------+
                |                           |
                v                           v
       TEMPLATE-DRIVEN                  REACTIVE
            FORMS                        FORMS
                |                           |
                v                           v
          Logic mostly                Logic mostly
            in HTML                   in TypeScript
                |                           |
                v                           v
             ngModel              FormGroup + FormControl
                |                           |
                v                           v
          FormsModule            ReactiveFormsModule
```

Both approaches are valid. The main difference is **where the form logic is written and how much control we have over the form**.

---

# 2. Two Types of Angular Forms

Angular provides:

1. Template-Driven Forms
2. Reactive Forms

## Template-Driven Forms

The form is mainly created and controlled from the HTML template.

Main concept:

```text
ngModel
```

Required module:

```text
FormsModule
```

Best suited for:

```text
Simple and small forms
```

---

## Reactive Forms

The form is mainly created and controlled from the TypeScript class.

Main concepts:

```text
FormGroup
FormControl
Validators
```

Required module:

```text
ReactiveFormsModule
```

Best suited for:

```text
Bigger forms
Complex forms
Forms with more validation
```

---

# 3. Template-Driven vs Reactive Forms

| Feature | Template-Driven | Reactive |
|---|---|---|
| Main logic | HTML template | TypeScript class |
| Main concept | `ngModel` | `FormGroup`, `FormControl` |
| Required module | `FormsModule` | `ReactiveFormsModule` |
| Best for | Small/simple forms | Bigger/complex forms |
| Validation | Simpler | More control |
| Amount of code | Less | More |
| Form definition | Mostly HTML | Mostly TypeScript |

### Simple Difference

```text
                 ANGULAR FORMS
                       |
          +------------+------------+
          |                         |
          v                         v
   TEMPLATE-DRIVEN              REACTIVE
          |                         |
          v                         v
       HTML                    TypeScript
          |                         |
          v                         v
      ngModel                  FormGroup
          |                         |
          v                         v
 Component Properties          FormControl
                                    |
                                    v
                                Validators
```

Template-driven forms are simpler to start with, while reactive forms provide more control and are useful for larger forms and validation.

---

# 4. Template-Driven Forms

In a template-driven form, most of the form structure and behavior is written in the HTML template.

The component class usually contains only the properties that store the form values and the method that handles submission.

The main Angular feature is:

```text
[(ngModel)]
```

Template-driven forms require:

```text
FormsModule
```

### Basic Structure

```text
                  TEMPLATE-DRIVEN FORM
                           |
                           v
                          HTML
                           |
                           v
                         Input
                           |
                           v
                       ngModel
                           |
                           v
                  Component Property
                           |
                           v
                        Submit
```

---

# 5. FormsModule

Template-driven forms use:

```text
FormsModule
```

This provides Angular's template-driven form functionality.

The relationship is:

```text
Template-Driven Form
        |
        v
      ngModel
        |
        v
   FormsModule
        |
        v
Angular Form Support
```

---

# 6. Basic Template-Driven Form

Example:

```html
<form (ngSubmit)="onSubmit()">

  <input
    name="username"
    [(ngModel)]="username"
    placeholder="Name"
  />

  <input
    name="email"
    [(ngModel)]="email"
    placeholder="Email"
  />

  <button type="submit">
    Submit
  </button>

</form>
```

The TypeScript class can contain:

```typescript
username: string = "";
email: string = "";

onSubmit() {
  console.log(this.username, this.email);
}
```

The important idea is that each input is connected to a component property using `[(ngModel)]`.

---

# 7. Understanding the `<form>` Element

The form starts with:

```html
<form (ngSubmit)="onSubmit()">
```

The important part is:

```text
(ngSubmit)
```

`ngSubmit` is used to handle form submission.

This:

```html
(ngSubmit)="onSubmit()"
```

means:

> When the form is submitted, call the `onSubmit()` method.

### Flow

```text
User
  |
  v
Fills Form
  |
  v
Clicks Submit
  |
  v
Form Submitted
  |
  v
ngSubmit
  |
  v
onSubmit()
```

---

# 8. Understanding the `name` Attribute

Each input has a `name` attribute.

Example:

```html
<input
  name="username"
  [(ngModel)]="username"
/>
```

Another input:

```html
<input
  name="email"
  [(ngModel)]="email"
/>
```

The form therefore contains two named fields:

```text
Form
 |
 +---- username
 |
 +---- email
```

The `name` attribute identifies the input field within the form.

---

# 9. What Is `ngModel`?

`ngModel` connects a form input with a component property.

Example:

```html
<input
  name="username"
  [(ngModel)]="username"
/>
```

Here:

```text
HTML Input
    |
    v
[(ngModel)]
    |
    v
username property
```

The input and component property remain connected.

---

# 10. What Is Two-Way Data Binding?

The syntax:

```html
[(ngModel)]
```

provides **two-way data binding**.

Two-way means data can move in both directions.

### Component → Input

```text
Component Property
       |
       v
     Input
```

### Input → Component

```text
Input
  |
  v
Component Property
```

Together:

```text
          Component Property
                  ↕
              [(ngModel)]
                  ↕
                Input
```

This is why `[(ngModel)]` is called two-way binding.

---

# 11. Example of Two-Way Binding

Suppose the component has:

```typescript
username: string = "";
```

And the template has:

```html
<input
  name="username"
  [(ngModel)]="username"
/>
```

Initially:

```text
username = ""
```

Now the user types:

```text
Divyani
```

Angular updates the component property:

```text
username = "Divyani"
```

### Flow

```text
User types "DIVYANI"
        |
        v
      Input
        |
        v
    [(ngModel)]
        |
        v
    username
        |
        v
"DIVYANI"
```
# 12. Component-to-Input Direction

Suppose the component contains:

```typescript
username: string = "Divyani";
```

and the template contains:

```html
<input
  name="username"
  [(ngModel)]="username"
/>
```

The input will initially show:

```text
Divyani
```

The flow is:

```text
username = "Divyani"
        |
        v
    [(ngModel)]
        |
        v
      Input
        |
        v
    Displays:
    Divyani
```

---

# 13. Input-to-Component Direction

Now suppose the user changes:

```text
Divyani
```

to:

```text
Divyani
```

The component property also changes:

```text
username = "Divyani"
```

The flow is:

```text
User changes input
        |
        v
      Input
        |
        v
    [(ngModel)]
        |
        v
 username property
        |
        v
username = "Divyani"
```

---

# 14. Understanding the Username Input

Example:

```html
<input
  name="username"
  [(ngModel)]="username"
  placeholder="Name"
/>
```

There are three important parts.

### `name`

```html
name="username"
```

Identifies the field.

### `[(ngModel)]`

```html
[(ngModel)]="username"
```

Connects the input with the component property.

### `placeholder`

```html
placeholder="Name"
```

Displays hint text inside the input.

---

# 15. Understanding the Email Input

Example:

```html
<input
  name="email"
  [(ngModel)]="email"
  placeholder="Email"
/>
```

This connects the input with:

```typescript
email: string = "";
```

The relationship is:

```text
Email Input
     |
     v
[(ngModel)]
     |
     v
email property
```

---

# 16. Template-Driven Form Class

The component class can remain very simple:

```typescript
username: string = "";
email: string = "";

onSubmit() {
  console.log(this.username, this.email);
}
```

The HTML controls most of the form behavior.

The component mainly stores the values and handles submission.

### Structure

```text
HTML
 |
 +---- Username Input
 |
 +---- Email Input
 |
 +---- ngModel
 |
 +---- Submit
 |
 v
Component
 |
 +---- username
 |
 +---- email
 |
 +---- onSubmit()
```

---

# 17. What Happens When the Form Is Submitted?

Suppose the user enters:

```text
Name:
Divyani

Email:
divyani@gmail.com
```

Because of `[(ngModel)]`, the component contains:

```typescript
username = "Divyani";
email = "divyani@example.com";
```

When the user clicks Submit:

```text
User
  |
  v
Clicks Submit
  |
  v
ngSubmit
  |
  v
onSubmit()
  |
  v
this.username
this.email
  |
  v
Form values are available
```

---

# 18. Complete Template-Driven Form Flow

```text
                         USER
                          |
                          v
                     HTML FORM
                          |
              +-----------+-----------+
              |                       |
              v                       v
        Username Input            Email Input
              |                       |
              v                       v
         [(ngModel)]              [(ngModel)]
              |                       |
              v                       v
         username                 email
         property                 property
              |                       |
              +-----------+-----------+
                          |
                          v
                    User clicks Submit
                          |
                          v
                       ngSubmit
                          |
                          v
                       onSubmit()
                          |
                          v
                    Form values ready
```

---

# 19. Why Template-Driven Forms Are Simple

Most of the form configuration exists inside the HTML.

```text
HTML
 |
 +---- Form
 |
 +---- Input
 |
 +---- ngModel
 |
 +---- Submit
 |
 v
Component Properties
```

There is less TypeScript code.

Therefore, template-driven forms are suitable for:

- Small login forms
- Simple search forms
- Small registration forms
- Basic forms with limited validation

---

# 20. Reactive Forms

Reactive Forms work differently.

The form is created mainly inside the TypeScript class.

The HTML template connects to that form.

The three important concepts are:

```text
FormGroup
FormControl
Validators
```

Reactive forms require:

```text
ReactiveFormsModule
```

### Basic Structure

```text
                  REACTIVE FORM
                       |
                       v
                  TypeScript
                       |
                       v
                   FormGroup
                       |
             +---------+---------+
             |                   |
             v                   v
        FormControl         FormControl
             |                   |
             v                   v
         username              email
             |                   |
             +---------+---------+
                       |
                       v
                    Template
```

---
# 21. ReactiveFormsModule

Reactive forms require:

```text
ReactiveFormsModule
```

The basic relationship is:

```text
Reactive Form
      |
      v
  FormGroup
      |
      v
 FormControl
      |
      v
ReactiveFormsModule
```

---

# 22. FormGroup

`FormGroup` represents the **complete form**.

Example:

```typescript
signupForm = new FormGroup({
  username: ...,
  email: ...
});
```

Think of `FormGroup` as a container that holds the controls of the form.

```text
                 signupForm
                 FormGroup
                     |
          +----------+----------+
          |                     |
          v                     v
      username                email
```

---

# 23. FormControl

A `FormControl` represents **one individual form field**.

Example:

```typescript
username: new FormControl("")
```

represents the username field.

And:

```typescript
email: new FormControl("")
```

represents the email field.

### Simple Rule

```text
FormGroup
   ↓
Whole Form

FormControl
   ↓
One Field
```

---

# 24. FormGroup + FormControl Relationship

```text
                  FormGroup
                     |
          +----------+----------+
          |                     |
          v                     v
    FormControl           FormControl
          |                     |
          v                     v
      username                email
```

So if a form has five fields, it can have five `FormControl`s inside one `FormGroup`.

---

# 25. Creating a Reactive Form

Example:

```typescript
import { Component } from "@angular/core";
import {
  FormGroup,
  FormControl,
  Validators
} from "@angular/forms";

@Component({
  /* ... */
})
export class SignupComponent {

  signupForm = new FormGroup({

    username: new FormControl(
      "",
      Validators.required
    ),

    email: new FormControl(
      "",
      [
        Validators.required,
        Validators.email
      ]
    )

  });

  onSubmit() {
    console.log(this.signupForm.value);
  }

}
```

The form is created inside the TypeScript class using `FormGroup` and `FormControl`.

---

# 26. Understanding the Imports

The reactive form uses:

```typescript
import {
  FormGroup,
  FormControl,
  Validators
} from "@angular/forms";
```

These have different purposes:

```text
FormGroup
    ↓
Represents the complete form

FormControl
    ↓
Represents one form field

Validators
    ↓
Checks whether form values are valid
```

---

# 27. Creating the FormGroup

The form starts with:

```typescript
signupForm = new FormGroup({
```

This creates the overall form object.

```text
signupForm
    |
    v
FormGroup
```

Inside the `FormGroup`, individual fields are defined.

```text
signupForm
    |
    +---- username
    |
    +---- email
```

---

# 28. Creating the Username FormControl

The username control is:

```typescript
username: new FormControl(
  "",
  Validators.required
)
```

It has two important parts.

### Initial Value

```text
""
```

The field initially contains an empty string.

### Validator

```text
Validators.required
```

The field cannot be empty.

### Structure

```text
Username
    |
    v
FormControl
    |
    +---- Initial value: ""
    |
    +---- Validator: required
```

---

# 29. Creating the Email FormControl

The email control is:

```typescript
email: new FormControl(
  "",
  [
    Validators.required,
    Validators.email
  ]
)
```

It contains:

```text
Initial Value
      ↓
""

Validators
      ↓
required
email
```

Therefore:

1. The email field cannot be empty.
2. The value must have a valid email format.

---

# 30. Reactive Form Template

The template connects to the `FormGroup`:

```html
<form
  [formGroup]="signupForm"
  (ngSubmit)="onSubmit()"
>

  <input
    formControlName="username"
    placeholder="Name"
  />

  <input
    formControlName="email"
    placeholder="Email"
  />

  <button
    type="submit"
    [disabled]="signupForm.invalid"
  >
    Submit
  </button>

</form>
```

---
# 31. Understanding `[formGroup]`

The HTML form contains:

```html
[formGroup]="signupForm"
```

This connects the HTML form to the `signupForm` object created in TypeScript.

```text
TypeScript
    |
    v
signupForm
    |
    | [formGroup]
    v
HTML Form
```

So the template knows which `FormGroup` it belongs to.

---

# 32. Understanding `formControlName`

The username input contains:

```html
formControlName="username"
```

The email input contains:

```html
formControlName="email"
```

These connect the inputs to the corresponding `FormControl`s.

```text
                    FormGroup
                        |
            +-----------+-----------+
            |                       |
            v                       v
    username FormControl     email FormControl
            |                       |
            v                       v
formControlName="username" formControlName="email"
            |                       |
            v                       v
      Username Input            Email Input
```

---

# 33. Reactive Form Data Flow

```text
                TypeScript
                    |
                    v
                FormGroup
                    |
          +---------+---------+
          |                   |
          v                   v
     FormControl         FormControl
      username               email
          |                   |
          v                   v
 formControlName       formControlName
          |                   |
          v                   v
     HTML Input           HTML Input
          |                   |
          +---------+---------+
                    |
                    v
                User Input
```

---

# 34. Understanding `[disabled]`

The submit button contains:

```html
[disabled]="signupForm.invalid"
```

This means:

> Disable the submit button when the form is invalid.

The flow is:

```text
                 signupForm
                      |
                      v
                 Is invalid?
                 /        \
               YES         NO
                |           |
                v           v
            Disabled      Enabled
              Button        Button
```

So:

```text
Valid Form
    ↓
Submit Enabled
```

and:

```text
Invalid Form
    ↓
Submit Disabled
```

---

# 35. Reactive Form Submission

The component contains:

```typescript
onSubmit() {
  console.log(this.signupForm.value);
}
```

When the user submits:

```text
User clicks Submit
       |
       v
(ngSubmit)
       |
       v
onSubmit()
       |
       v
signupForm.value
       |
       v
All form values
```

For example:

```text
{
  username: "Divyani",
  email: "divyani@example.com"
}
```

---

# 36. Validation

Validation means checking whether the data entered by the user is acceptable.

For example:

```text
Username
    |
    v
Required
    |
    v
Cannot be empty
```

And:

```text
Email
    |
    +----> Required
    |
    +----> Valid Email Format
```

Reactive forms make validation easier because validators can be directly attached to `FormControl`s.

---

# 37. Validators.required

```typescript
Validators.required
```

means:

> The field cannot be empty.

Example:

```typescript
username: new FormControl(
  "",
  Validators.required
)
```

### Flow

```text
             Username Input
                    |
                    v
           Validators.required
                    |
                    v
              Is it empty?
                /       \
              YES        NO
               |          |
               v          v
            Invalid      Valid
```

---

# 38. Validators.email

```typescript
Validators.email
```

means:

> The value must have a valid email format.

Example:

```typescript
email: new FormControl(
  "",
  Validators.email
)
```

### Flow

```text
              Email Input
                   |
                   v
           Validators.email
                   |
                   v
        Valid Email Format?
             /          \
           YES           NO
            |             |
            v             v
          Valid         Invalid
```

---

# 39. Validators.minLength()

Example:

```typescript
Validators.minLength(6)
```

Meaning:

> The value must contain at least 6 characters.

### Flow

```text
Input
  |
  v
minLength(6)
  |
  v
Count Characters
  |
  +------> 6 or more ------> Valid
  |
  +------> Less than 6 ----> Invalid
```

Example:

```text
"abcdef"
   ↓
6 characters
   ↓
Valid
```

But:

```text
"abc"
  ↓
3 characters
  ↓
Invalid
```

---

# 40. Validators.maxLength()

Example:

```typescript
Validators.maxLength(20)
```

Meaning:

> The value can contain at most 20 characters.

### Flow

```text
Input
  |
  v
maxLength(20)
  |
  v
Count Characters
  |
  +------> 20 or less ------> Valid
  |
  +------> More than 20 ----> Invalid
```

---

# 41. Multiple Validators

A field can have multiple validators.

Example:

```typescript
email: new FormControl(
  "",
  [
    Validators.required,
    Validators.email
  ]
)
```

This means the email must satisfy both conditions.

```text
                       Email
                         |
             +-----------+-----------+
             |                       |
             v                       v
      Validators.required     Validators.email
             |                       |
             v                       v
       Cannot be empty        Valid email format
             |                       |
             +-----------+-----------+
                         |
                         v
                    Valid Field
```

If any required condition fails, the control becomes invalid.

---

# 42. Validation Flow

```text
                    User Input
                        |
                        v
                    FormControl
                        |
                        v
                     Validators
                        |
             +----------+----------+
             |                     |
             v                     v
           Valid                Invalid
             |                     |
             v                     v
       Form is valid         Form is invalid
             |                     |
             v                     v
      Submit possible       Submit disabled
```

---

# 43. Form State

Angular allows us to check whether a form is valid or invalid.

### Check if the complete form is valid

```typescript
signupForm.valid
```

### Check if the complete form is invalid

```typescript
signupForm.invalid
```

### Check a specific field

```typescript
signupForm.get('email')?.invalid
```

This checks the validation state of the email control.

---

# 44. Form State Flow

```text
                    signupForm
                         |
             +-----------+-----------+
             |                       |
             v                       v
           valid                   invalid
             |                       |
             v                       v
          true                    true
```

For a single field:

```text
signupForm
    |
    v
get('email')
    |
    v
Email FormControl
    |
    v
invalid
    |
    v
true / false
```

---

# 45. Complete Reactive Validation Flow

```text
                     USER
                      |
                      v
                  Form Input
                      |
                      v
                 FormControl
                      |
                      v
                  Validators
                      |
          +-----------+-----------+
          |                       |
          v                       v
        Valid                  Invalid
          |                       |
          v                       v
    Form is valid           Form is invalid
          |                       |
          v                       v
 Submit can be enabled      Submit is disabled
```

---

# 46. Complete Template-Driven Form Flow

```text
                         USER
                          |
                          v
                     HTML FORM
                          |
              +-----------+-----------+
              |                       |
              v                       v
        Username Input            Email Input
              |                       |
              v                       v
         [(ngModel)]              [(ngModel)]
              |                       |
              v                       v
         username                 email
         property                 property
              |                       |
              +-----------+-----------+
                          |
                          v
                    User clicks Submit
                          |
                          v
                       ngSubmit
                          |
                          v
                       onSubmit()
                          |
                          v
                    Form Values Ready
```

---

# 47. Complete Reactive Form Flow

```text
                      USER
                       |
                       v
                  HTML FORM
                       |
                       v
                [formGroup]
                       |
                       v
                  FormGroup
                       |
             +---------+---------+
             |                   |
             v                   v
        FormControl         FormControl
         username               email
             |                   |
             v                   v
      formControlName      formControlName
             |                   |
             v                   v
      Username Input         Email Input
             |                   |
             +---------+---------+
                       |
                       v
                  User enters data
                       |
                       v
                   Validators
                       |
             +---------+---------+
             |                   |
             v                   v
           Valid              Invalid
             |                   |
             v                   v
      Submit Enabled       Submit Disabled
```

---

# 48. Template-Driven Form Architecture

```text
                  TEMPLATE-DRIVEN
                         |
                         v
                        HTML
                         |
          +--------------+--------------+
          |                             |
          v                             v
     Username Input                Email Input
          |                             |
          v                             v
     [(ngModel)]                   [(ngModel)]
          |                             |
          v                             v
       username                      email
          |                             |
          +--------------+--------------+
                         |
                         v
                     Component
                         |
                         v
                      Submit
                         |
                         v
                     ngSubmit
                         |
                         v
                     onSubmit()
```

---

# 49. Reactive Form Architecture

```text
                     REACTIVE FORM
                           |
                           v
                       TypeScript
                           |
                           v
                       FormGroup
                           |
              +------------+------------+
              |                         |
              v                         v
        FormControl               FormControl
         username                    email
              |                         |
              +------------+------------+
                           |
                           v
                        Template
                           |
              +------------+------------+
              |                         |
              v                         v
        formControlName          formControlName
              |                         |
              v                         v
        Username Input             Email Input
                           |
                           v
                       Validators
                           |
                   +-------+-------+
                   |               |
                   v               v
                 Valid          Invalid
```

---

# 50. When to Use Template-Driven Forms

Template-driven forms are suitable when the form is:

- Small
- Simple
- Quick to build
- Not heavily dependent on complex validation

Examples:

```text
Simple Login Form
       |
       v
Template-Driven


Simple Search Form
       |
       v
Template-Driven


Small Registration Form
       |
       v
Template-Driven
```

---

# 51. When to Use Reactive Forms

Reactive forms are suitable when the form:

- Has many fields
- Requires multiple validators
- Needs complex validation
- Requires more programmatic control
- Has complicated form state

Example:

```text
Large Signup Form
       |
       +---- Many Fields
       |
       +---- Complex Validation
       |
       +---- Form State
       |
       v
Reactive Form
```

---

# 52. Simple Decision Flow

```text
                  Need an Angular Form
                           |
                           v
                    Is the form simple?
                     /            \
                   YES             NO
                    |               |
                    v               v
             Template-Driven     Reactive
                    |               |
                    v               v
                 ngModel       FormGroup
                                  +
                              FormControl
                                  +
                              Validators
```

---

# 53. Template-Driven Mental Model

```text
HTML
  |
  v
Form
  |
  v
Input
  |
  v
[(ngModel)]
  |
  v
Component Property
  |
  v
Submit
  |
  v
ngSubmit
  |
  v
onSubmit()
```

The main idea:

> **The template controls most of the form.**

---

# 54. Reactive Mental Model

```text
TypeScript
    |
    v
FormGroup
    |
    v
FormControl
    |
    v
Validators
    |
    v
HTML Template
    |
    v
User Input
    |
    v
Form State
    |
    +----> Valid
    |
    +----> Invalid
```

The main idea:

> **The TypeScript class controls most of the form.**

---

# 55. Important Syntax to Remember

## Template-Driven

```html
<form (ngSubmit)="onSubmit()">

  <input
    name="username"
    [(ngModel)]="username"
  />

  <input
    name="email"
    [(ngModel)]="email"
  />

  <button type="submit">
    Submit
  </button>

</form>
```

Remember:

```text
FormsModule
ngModel
[(ngModel)]
name
ngSubmit
```

---

## Reactive

```typescript
signupForm = new FormGroup({

  username: new FormControl(
    "",
    Validators.required
  ),

  email: new FormControl(
    "",
    [
      Validators.required,
      Validators.email
    ]
  )

});
```

Template:

```html
<form
  [formGroup]="signupForm"
  (ngSubmit)="onSubmit()"
>

  <input
    formControlName="username"
  />

  <input
    formControlName="email"
  />

  <button
    type="submit"
    [disabled]="signupForm.invalid"
  >
    Submit
  </button>

</form>
```

Remember:

```text
ReactiveFormsModule
FormGroup
FormControl
Validators
[formGroup]
formControlName
```

---

# 56. Quick Revision Table

| Concept | Meaning |
|---|---|
| Angular Form | Used to collect user input |
| Template-Driven Form | Form mainly controlled from HTML |
| Reactive Form | Form mainly controlled from TypeScript |
| `FormsModule` | Required for template-driven forms |
| `ReactiveFormsModule` | Required for reactive forms |
| `ngModel` | Connects an input with a component property |
| `[(ngModel)]` | Two-way data binding |
| `ngSubmit` | Handles form submission |
| `FormGroup` | Represents the complete reactive form |
| `FormControl` | Represents one individual field |
| `[formGroup]` | Connects HTML form to a `FormGroup` |
| `formControlName` | Connects an input to a `FormControl` |
| `Validators.required` | Field cannot be empty |
| `Validators.email` | Must be a valid email |
| `Validators.minLength(6)` | At least 6 characters |
| `Validators.maxLength(20)` | At most 20 characters |
| `.valid` | Checks whether form/control is valid |
| `.invalid` | Checks whether form/control is invalid |

---

# 57. Final Revision Flowchart

```text
                              ANGULAR FORMS
                                   |
                    +--------------+--------------+
                    |                             |
                    v                             v
             TEMPLATE-DRIVEN                  REACTIVE
                    |                             |
                    v                             v
                  HTML                       TypeScript
                    |                             |
                    v                             v
                ngModel                     FormGroup
                    |                             |
                    v                             v
            Two-Way Binding                 FormControl
                    |                             |
                    v                             v
          Component Property                Validators
                    |                             |
                    v                             v
                 Submit                    Valid / Invalid
                    |                             |
                    v                             v
                ngSubmit                    Form State
                    |                             |
                    v                             v
                onSubmit()               Submit Enabled/
                                         Submit Disabled
```

---




The value is now available inside the component.

---
