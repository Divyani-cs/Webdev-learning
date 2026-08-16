## Introduction to Angular (Day 1)

## 📖 Table of Contents
1. [Why Use Angular?](#1-why-use-angular)
2. [Performance & Architecture](#2-performance--architecture)
3. [Mobile Support](#3-mobile-support)
4. [Language Choices (ECMAScript vs. TypeScript)](#4-language-choices)
5. [Installation & Setup](#5-installation--setup)
6. [Angular CLI Reference](#6-angular-cli-reference)
7. [Homework Assignment](#7-homework-assignment)

---

## 1. Why Use Angular?
Angular is one of the world's most popular JavaScript frameworks for building modern web applications. It is engineered, built, and actively maintained by **Google**. 

---

## 2. Performance & Architecture
Angular focuses on building smaller, faster, and highly scalable applications. Key architectural features include:
* **Speed:** Delivers faster initial page loads and rapid screen updates.
* **Efficient Change Detection:** Optimizes how data changes update the view layer.
* **Modularity:** Splits application code cleanly into separate, manageable parts.
* **Dependency Injection:** Provides a tidy, structured way to share services and code across components.
* **Testability:** Built from the ground up to ensure your application code is easily testable.

> 💡 *Note: Modern Angular is continuously optimized. The current standard version used in this course is **Angular 22**.*

---

## 3. Mobile Support
Angular is designed with mobile environments in mind from the ground up:
* Accounts for mobile realities like touch interfaces, small screens, and limited hardware.
* Allows you to build a **single application** that works seamlessly across both mobile and desktop.
* Eliminates the need to bolt on separate third-party tools just to make your app function on phones.

---

## 4. Language Choices

### What is ECMAScript?
* ECMAScript is the official, standardized name for the JavaScript language.
* New versions introducing modern features (like classes, modules, and arrow functions) are released roughly every year.
* *Naming tip:* **ECMAScript 6** was officially renamed **ES2015**; both terms refer to the exact same standard.

### What is TypeScript?
* Developed by **Microsoft**, TypeScript is a free, open-source **superset of JavaScript**.
* Every valid line of JavaScript is automatically valid TypeScript.
* It adds strict **types**, interfaces, inheritance, and advanced object-oriented programming concepts.
* A compilation step automatically translates TypeScript code into plain JavaScript that any standard web browser can execute.

---

## 5. Installation & Setup

Follow these three steps exactly to set up your development environment.

### Step 1: Install Node.js
Angular runs on top of Node.js. 
1. Go to [nodejs.org](https://nodejs.org).
2. Download and install the **LTS (Stable)** version. 
3. *Requirement:* Angular requires **Node version 20.19 or newer**.

Verify the installation by running the following commands in your terminal:
```bash
node -v
npm -v
```
*(Both commands should print a version number. Installing Node also installs `npm`, the node package manager tool).*

### Step 2: Install the Angular CLI
Install the command-line interface globally on your machine so it can be accessed from any directory:
```bash
npm install -g @angular/cli
```

Verify that the CLI works properly:
```bash
ng version
```

### Step 3: Create and Run Your First Application
Generate your application, navigate into its root directory, and launch the local development server:
```bash
ng new my-first-app
cd my-first-app
ng serve
```

Once compilation completes, open your web browser and navigate to:
👉 **`http://localhost:4200`**

---

## 6. Angular CLI Reference
The Angular CLI (`ng` tool) handles project creation, code generation, local execution, and production builds. 

| Command | Description |
| :--- | :--- |
| `ng new <name>` | Creates a brand new, fully configured Angular project. |
| `ng serve` | Runs the app locally with live auto-reloads upon saving files. |
| `ng generate component <name>` | Creates a new component piece with all its necessary files. (Short version: `ng g c <name>`) |
| `ng build` | Packages the application into optimized final files ready for a production server. |
| `ng test` | Runs the unit testing suite for your application. |
| `ng version` | Displays the installed versions of Angular and its dependencies. |

---

