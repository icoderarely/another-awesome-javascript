# JavaScript Modules

## Table of Contents

- [What are Modules?](#what-are-modules)
- [Why Use Modules?](#why-use-modules)
- [CommonJS Modules (Node.js)](#commonjs-modules-nodejs)
- [ES6 Modules (ESM)](#es6-modules-esm)
- [Named Exports and Imports](#named-exports-and-imports)
- [Default Exports and Imports](#default-exports-and-imports)
- [Mixed Exports](#mixed-exports)
- [Module Loading and Resolution](#module-loading-and-resolution)
- [Dynamic Imports](#dynamic-imports)
- [Module Patterns and Best Practices](#module-patterns-and-best-practices)
- [Browser vs Node.js Modules](#browser-vs-nodejs-modules)
- [Module Bundlers](#module-bundlers)
- [Common Issues and Solutions](#common-issues-and-solutions)
- [Key Takeaways](#key-takeaways)

## What are Modules?

**Modules** are a way to organize code into reusable, maintainable files. They help split code into smaller, manageable parts that can be easily maintained, reused, and shared across different parts of an application.

### Core Concepts

- **Encapsulation**: Each module has its own scope
- **Reusability**: Code can be imported and used in multiple places
- **Maintainability**: Easier to debug and update isolated functionality
- **Namespace Management**: Avoids naming conflicts and global pollution

### Simple Example

```javascript
// Before modules (global scope pollution)
var userName = "John";
var userAge = 25;

function getUserInfo() {
  return userName + " is " + userAge + " years old";
}

// All variables and functions are global - risk of conflicts!
```

```javascript
// With modules (clean separation)
// user.js
export const userName = "John";
export const userAge = 25;

export function getUserInfo() {
  return `${userName} is ${userAge} years old`;
}

// main.js
import { getUserInfo } from "./user.js";
console.log(getUserInfo());
```

## Why Use Modules?

### 1. **Code Organization**

```javascript
// Without modules - everything in one file
// app.js (hundreds of lines)
function calculateTax() {
  /* ... */
}
function formatCurrency() {
  /* ... */
}
function validateEmail() {
  /* ... */
}
function sendEmail() {
  /* ... */
}
// ... more functions

// With modules - organized by functionality
// math/tax.js
export function calculateTax() {
  /* ... */
}

// utils/currency.js
export function formatCurrency() {
  /* ... */
}

// validation/email.js
export function validateEmail() {
  /* ... */
}

// services/email.js
export function sendEmail() {
  /* ... */
}
```

### 2. **Reusability**

```javascript
// math.js - can be used in multiple projects
export function add(a, b) {
  return a + b;
}
export function multiply(a, b) {
  return a * b;
}
export function calculateCompoundInterest(principal, rate, time) {
  return principal * Math.pow(1 + rate, time);
}

// Can be imported in any file that needs math functions
import { add, calculateCompoundInterest } from "./math.js";
```

### 3. **Avoiding Name Collisions**

```javascript
// Without modules - naming conflicts
var calculate = function () {
  return "math calculation";
};
var calculate = function () {
  return "finance calculation";
}; // Overwrites!

// With modules - no conflicts
// math.js
export function calculate() {
  return "math calculation";
}

// finance.js
export function calculate() {
  return "finance calculation";
}

// main.js
import { calculate as mathCalc } from "./math.js";
import { calculate as financeCalc } from "./finance.js";
```

## CommonJS Modules (Node.js)

### Overview

**CommonJS** is the module system used by Node.js by default. It uses `require()` to import and `module.exports` or `exports` to export.

### Characteristics

- **Synchronous**: Modules are loaded synchronously at runtime
- **Runtime Loading**: Modules are loaded when the code executes
- **Node.js Native**: Works in Node.js by default
- **Browser Support**: Requires bundlers (Webpack, Browserify) for browser use

### Basic Syntax

#### Exporting with module.exports

```javascript
// math.js
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

function multiply(a, b) {
  return a * b;
}

// Export multiple functions
module.exports = {
  add,
  subtract,
  multiply,
};

// Alternative syntax
module.exports = { add, subtract, multiply };
```

#### Importing with require()

```javascript
// app.js
const math = require("./math");

console.log(math.add(5, 3)); // 8
console.log(math.subtract(5, 3)); // 2
console.log(math.multiply(5, 3)); // 15

// Destructuring import
const { add, multiply } = require("./math");
console.log(add(10, 5)); // 15
console.log(multiply(4, 3)); // 12
```

### Different Export Patterns

#### 1. Object Export

```javascript
// config.js
module.exports = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3,
};

// usage
const config = require("./config");
console.log(config.apiUrl);
```

#### 2. Function Export

```javascript
// logger.js
module.exports = function (message) {
  console.log(`[${new Date().toISOString()}] ${message}`);
};

// usage
const logger = require("./logger");
logger("Application started");
```

#### 3. Class Export

```javascript
// User.js
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  getInfo() {
    return `${this.name} (${this.email})`;
  }
}

module.exports = User;

// usage
const User = require("./User");
const user = new User("John", "john@example.com");
console.log(user.getInfo());
```

#### 4. Mixed Exports

```javascript
// utils.js
function formatDate(date) {
  return date.toLocaleDateString();
}

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

const API_BASE_URL = "https://api.example.com";

module.exports = {
  formatDate,
  isValidEmail,
  API_BASE_URL,
  version: "1.0.0",
};
```

## ES6 Modules (ESM)

### Overview

**ES6 Modules** (also called ESM - ECMAScript Modules) are the native module system for JavaScript, supported in modern browsers and Node.js.

### Characteristics

- **Static Structure**: Imports and exports are determined at compile time
- **Asynchronous Loading**: Modules can be loaded asynchronously
- **Tree Shaking**: Unused code can be eliminated by bundlers
- **Browser Native**: Works directly in modern browsers
- **Strict Mode**: Always runs in strict mode

### Browser Setup

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>ES6 Modules</title>
  </head>
  <body>
    <script type="module" src="main.js"></script>
  </body>
</html>
```

### Node.js Setup

```json
// package.json
{
  "type": "module"
}
```

Or use `.mjs` file extension:

```javascript
// math.mjs
export function add(a, b) {
  return a + b;
}
```

## Named Exports and Imports

### Named Exports

Named exports allow you to export multiple values from a module by name.

#### Export at Declaration

```javascript
// math.js
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export const PI = 3.14159;

export class Calculator {
  constructor() {
    this.result = 0;
  }

  add(num) {
    this.result += num;
    return this;
  }

  getResult() {
    return this.result;
  }
}
```

#### Export After Declaration

```javascript
// utils.js
function formatCurrency(amount) {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  }).format(amount);
}

function formatDate(date) {
  return new Intl.DateTimeFormat("en-US").format(date);
}

const API_KEY = "your-api-key";

// Export at the end
export { formatCurrency, formatDate, API_KEY };
```

### Named Imports

#### Basic Import

```javascript
// main.js
import { add, subtract, PI } from "./math.js";

console.log(add(10, 5)); // 15
console.log(subtract(10, 5)); // 5
console.log(PI); // 3.14159
```

#### Import with Renaming

```javascript
// main.js
import { add as sum, subtract as minus, PI as piValue } from "./math.js";

console.log(sum(10, 5)); // 15
console.log(minus(10, 5)); // 5
console.log(piValue); // 3.14159
```

#### Import All as Namespace

```javascript
// main.js
import * as math from "./math.js";

console.log(math.add(10, 5)); // 15
console.log(math.PI); // 3.14159

const calc = new math.Calculator();
console.log(calc.add(5).add(3).getResult()); // 8
```

#### Selective Import

```javascript
// main.js - only import what you need
import { formatCurrency } from "./utils.js";

console.log(formatCurrency(1234.56)); // $1,234.56
```

## Default Exports and Imports

### Default Exports

Each module can have **only one** default export. The importing file can name it anything.

#### Function Default Export

```javascript
// logger.js
export default function log(message, level = "info") {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${level.toUpperCase()}: ${message}`);
}
```

#### Class Default Export

```javascript
// User.js
export default class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.createdAt = new Date();
  }

  getProfile() {
    return {
      name: this.name,
      email: this.email,
      memberSince: this.createdAt.getFullYear(),
    };
  }

  static fromJSON(json) {
    return new User(json.name, json.email);
  }
}
```

#### Object Default Export

```javascript
// config.js
export default {
  api: {
    baseUrl: "https://api.example.com",
    timeout: 5000,
    retries: 3,
  },
  features: {
    darkMode: true,
    notifications: true,
  },
  version: "2.1.0",
};
```

### Default Imports

#### Basic Default Import

```javascript
// main.js
import logger from "./logger.js";
import User from "./User.js";
import config from "./config.js";

logger("Application started");

const user = new User("Alice", "alice@example.com");
console.log(user.getProfile());

console.log("API URL:", config.api.baseUrl);
```

#### Default Import with Custom Name

```javascript
// main.js
import writeLog from "./logger.js"; // Can name it anything
import Person from "./User.js"; // Can name it anything
import appConfig from "./config.js"; // Can name it anything

writeLog("Custom logger name");
const person = new Person("Bob", "bob@example.com");
```

## Mixed Exports

You can combine default and named exports in the same module.

### Mixed Export Example

```javascript
// math.js
// Default export (main functionality)
export default function multiply(a, b) {
  return a * b;
}

// Named exports (utility functions)
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export const PI = 3.14159;
export const E = 2.71828;
```

### Mixed Import Example

```javascript
// main.js
import multiply, { add, subtract, PI } from "./math.js";

console.log(multiply(3, 4)); // 12 (default export)
console.log(add(3, 4)); // 7 (named export)
console.log(subtract(10, 4)); // 6 (named export)
console.log(PI); // 3.14159 (named export)
```

### Advanced Mixed Pattern

```javascript
// api.js
class APIClient {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
  }

  async get(endpoint) {
    const response = await fetch(`${this.baseUrl}${endpoint}`);
    return response.json();
  }
}

// Default export (main class)
export default APIClient;

// Named exports (utility functions)
export function createAuthHeader(token) {
  return { Authorization: `Bearer ${token}` };
}

export function handleAPIError(error) {
  console.error("API Error:", error.message);
  return null;
}

export const endpoints = {
  users: "/users",
  posts: "/posts",
  comments: "/comments",
};
```

```javascript
// main.js
import APIClient, { createAuthHeader, endpoints } from "./api.js";

const client = new APIClient("https://api.example.com");
const headers = createAuthHeader("your-token");
const users = await client.get(endpoints.users);
```

## Module Loading and Resolution

### Module Resolution

How JavaScript finds and loads modules:

#### Relative Paths

```javascript
// Current directory
import { utils } from "./utils.js";

// Parent directory
import { config } from "../config.js";

// Nested directory
import { validator } from "./helpers/validator.js";
```

#### Absolute Paths (Node.js)

```javascript
// From node_modules
import express from "express";
import { v4 as uuidv4 } from "uuid";

// From project root (with proper setup)
import { database } from "/src/database/connection.js";
```

### File Extensions

```javascript
// Required in browsers and Node.js with type: "module"
import { math } from "./math.js";

// Optional in CommonJS
const math = require("./math"); // .js assumed
```

### Index Files

```javascript
// utils/index.js
export { formatDate } from "./date.js";
export { validateEmail } from "./validation.js";
export { apiClient } from "./api.js";

// main.js - imports from index.js automatically
import { formatDate, validateEmail } from "./utils/";
// or
import { formatDate, validateEmail } from "./utils/index.js";
```

### Module Caching

```javascript
// counter.js
let count = 0;

export function increment() {
  return ++count;
}

export function getCount() {
  return count;
}

// main.js
import { increment, getCount } from "./counter.js";
import { increment as inc2 } from "./counter.js"; // Same module instance

console.log(increment()); // 1
console.log(inc2()); // 2 (shares state)
console.log(getCount()); // 2
```

## Dynamic Imports

### What are Dynamic Imports?

Dynamic imports allow you to load modules at runtime, conditionally, or on-demand.

### Basic Dynamic Import

```javascript
// Static import (loaded at compile time)
import { heavyFunction } from "./heavy-module.js";

// Dynamic import (loaded at runtime)
async function loadHeavyModule() {
  const module = await import("./heavy-module.js");
  return module.heavyFunction();
}

// Or with .then()
import("./heavy-module.js")
  .then((module) => {
    module.heavyFunction();
  })
  .catch((error) => {
    console.error("Failed to load module:", error);
  });
```

### Conditional Loading

```javascript
// feature-flag.js
export const FEATURES = {
  ADVANCED_ANALYTICS: true,
  BETA_FEATURES: false,
};

// main.js
import { FEATURES } from "./feature-flag.js";

async function initializeApp() {
  // Always load core functionality
  const { initCore } = await import("./core.js");
  initCore();

  // Conditionally load advanced features
  if (FEATURES.ADVANCED_ANALYTICS) {
    const { initAnalytics } = await import("./analytics.js");
    initAnalytics();
  }

  if (FEATURES.BETA_FEATURES) {
    const { initBetaFeatures } = await import("./beta.js");
    initBetaFeatures();
  }
}
```

### Lazy Loading

```javascript
// app.js
class App {
  async loadDashboard() {
    // Load dashboard module only when needed
    const { Dashboard } = await import("./components/Dashboard.js");
    const dashboard = new Dashboard();
    dashboard.render();
  }

  async loadReports() {
    // Load reports module only when user accesses reports
    const { ReportGenerator } = await import("./reports/ReportGenerator.js");
    const generator = new ReportGenerator();
    return generator.generateReport();
  }
}

// Usage
const app = new App();

// Load dashboard when user clicks dashboard tab
document.getElementById("dashboard-tab").addEventListener("click", () => {
  app.loadDashboard();
});
```

### Dynamic Import with Error Handling

```javascript
async function loadModule(modulePath) {
  try {
    const module = await import(modulePath);
    return module;
  } catch (error) {
    console.error(`Failed to load module ${modulePath}:`, error);

    // Fallback to a default module
    const fallback = await import("./fallback.js");
    return fallback;
  }
}

// Usage
const userModule = await loadModule("./user-specific-module.js");
```

## Module Patterns and Best Practices

### 1. **Barrel Exports**

Consolidate exports from multiple modules:

```javascript
// components/index.js (barrel file)
export { Button } from "./Button.js";
export { Modal } from "./Modal.js";
export { Form } from "./Form.js";
export { Card } from "./Card.js";

// main.js - clean imports
import { Button, Modal, Form } from "./components/";
```

### 2. **Facade Pattern**

Provide a simplified interface to complex subsystems:

```javascript
// payment/index.js
import { CreditCardProcessor } from "./credit-card.js";
import { PayPalProcessor } from "./paypal.js";
import { BankTransferProcessor } from "./bank-transfer.js";

export class PaymentFacade {
  static async processPayment(method, amount, details) {
    switch (method) {
      case "credit-card":
        return CreditCardProcessor.process(amount, details);
      case "paypal":
        return PayPalProcessor.process(amount, details);
      case "bank-transfer":
        return BankTransferProcessor.process(amount, details);
      default:
        throw new Error("Unsupported payment method");
    }
  }
}

// main.js
import { PaymentFacade } from "./payment/";
await PaymentFacade.processPayment("credit-card", 100, cardDetails);
```

### 3. **Factory Pattern**

Create objects without specifying exact classes:

```javascript
// logger/factory.js
import { ConsoleLogger } from "./console-logger.js";
import { FileLogger } from "./file-logger.js";
import { DatabaseLogger } from "./database-logger.js";

export class LoggerFactory {
  static create(type) {
    switch (type) {
      case "console":
        return new ConsoleLogger();
      case "file":
        return new FileLogger();
      case "database":
        return new DatabaseLogger();
      default:
        return new ConsoleLogger(); // default fallback
    }
  }
}

// main.js
import { LoggerFactory } from "./logger/factory.js";
const logger = LoggerFactory.create("file");
```

### 4. **Singleton Pattern**

Ensure only one instance of a class exists:

```javascript
// database.js
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }

    this.connection = null;
    Database.instance = this;
  }

  connect() {
    if (!this.connection) {
      this.connection = "Connected to database";
    }
    return this.connection;
  }
}

export default new Database(); // Export instance, not class

// main.js
import database from "./database.js";
console.log(database.connect());
```

### 5. **Module Organization Best Practices**

#### Feature-Based Structure

```
src/
├── auth/
│   ├── index.js
│   ├── login.js
│   ├── register.js
│   └── password-reset.js
├── user/
│   ├── index.js
│   ├── profile.js
│   ├── settings.js
│   └── avatar.js
├── utils/
│   ├── index.js
│   ├── date.js
│   ├── validation.js
│   └── api.js
└── main.js
```

#### Clear Naming Conventions

```javascript
// ✅ Good - descriptive names
import { validateEmailAddress } from "./validation/email.js";
import { formatCurrencyUSD } from "./formatting/currency.js";
import { fetchUserProfile } from "./api/user.js";

// ❌ Bad - unclear names
import { validate } from "./val.js";
import { format } from "./fmt.js";
import { fetch } from "./api.js";
```

## Browser vs Node.js Modules

### Browser Modules

#### Basic HTML Setup

```html
<!DOCTYPE html>
<html>
  <head>
    <title>ES6 Modules in Browser</title>
  </head>
  <body>
    <div id="app"></div>

    <!-- Main module -->
    <script type="module" src="js/main.js"></script>

    <!-- Fallback for older browsers -->
    <script nomodule src="js/legacy.js"></script>
  </body>
</html>
```

#### Browser Module Example

```javascript
// js/dom-utils.js
export function createElement(tag, className, textContent) {
  const element = document.createElement(tag);
  if (className) element.className = className;
  if (textContent) element.textContent = textContent;
  return element;
}

export function appendToBody(element) {
  document.body.appendChild(element);
}

// js/main.js
import { createElement, appendToBody } from "./dom-utils.js";

const heading = createElement("h1", "title", "Hello Modules!");
appendToBody(heading);
```

### Node.js Modules

#### Package.json Configuration

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  }
}
```

#### Node.js Module Example

```javascript
// file-utils.js
import { promises as fs } from "fs";
import path from "path";

export async function readJSONFile(filePath) {
  try {
    const data = await fs.readFile(filePath, "utf8");
    return JSON.parse(data);
  } catch (error) {
    console.error("Error reading file:", error);
    return null;
  }
}

export async function writeJSONFile(filePath, data) {
  try {
    await fs.writeFile(filePath, JSON.stringify(data, null, 2));
    return true;
  } catch (error) {
    console.error("Error writing file:", error);
    return false;
  }
}

// index.js
import { readJSONFile, writeJSONFile } from "./file-utils.js";

const config = await readJSONFile("./config.json");
console.log("Config loaded:", config);
```

## Module Bundlers

### Why Use Bundlers?

- **Compatibility**: Enable modules in older browsers
- **Optimization**: Bundle and minify code
- **Development Features**: Hot reloading, source maps
- **Asset Management**: Handle CSS, images, etc.

### Popular Bundlers

#### Webpack

```javascript
// webpack.config.js
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: __dirname + "/dist",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: "babel-loader",
      },
    ],
  },
};
```

#### Vite

```javascript
// vite.config.js
import { defineConfig } from "vite";

export default defineConfig({
  build: {
    rollupOptions: {
      input: "./src/main.js",
    },
  },
});
```

#### Rollup

```javascript
// rollup.config.js
export default {
  input: "src/main.js",
  output: {
    file: "dist/bundle.js",
    format: "iife",
  },
};
```

## Common Issues and Solutions

### 1. **File Extension Issues**

```javascript
// ❌ Error: Missing file extension
import { utils } from "./utils";

// ✅ Correct: Include .js extension
import { utils } from "./utils.js";
```

### 2. **Circular Dependencies**

```javascript
// ❌ Circular dependency
// a.js
import { b } from "./b.js";
export const a = "a";

// b.js
import { a } from "./a.js";
export const b = "b";

// ✅ Solution: Use dynamic imports or restructure
// a.js
export const a = "a";

// b.js
export const b = "b";

// main.js
import { a } from "./a.js";
import { b } from "./b.js";
```

### 3. **Default vs Named Export Confusion**

```javascript
// utils.js
export default function log(message) {
  console.log(message);
}

// ❌ Wrong import
import { log } from "./utils.js"; // Looking for named export

// ✅ Correct import
import log from "./utils.js"; // Default import
```

### 4. **Module Not Found Errors**

```javascript
// ❌ Incorrect path
import { helper } from "../helper.js"; // Wrong directory

// ✅ Correct path
import { helper } from "./helpers/helper.js";

// ✅ Alternative: Use absolute imports (with proper setup)
import { helper } from "/src/helpers/helper.js";
```

## Key Takeaways

### Essential Concepts

1. **Module Types**: CommonJS (Node.js) vs ES6 Modules (modern standard)
2. **Export Types**: Named exports (multiple) vs Default exports (one per module)
3. **Import Patterns**: Static imports (compile-time) vs Dynamic imports (runtime)
4. **Module Resolution**: How JavaScript finds and loads modules
5. **Best Practices**: Code organization, naming conventions, and patterns

### CommonJS vs ES6 Modules Comparison

| Feature         | CommonJS                       | ES6 Modules         |
| --------------- | ------------------------------ | ------------------- |
| Syntax          | `require()` / `module.exports` | `import` / `export` |
| Loading         | Synchronous                    | Asynchronous        |
| Browser Support | Requires bundler               | Native support      |
| Tree Shaking    | No                             | Yes                 |
| Static Analysis | No                             | Yes                 |
| Top-level await | No                             | Yes                 |

### Best Practices Summary

- Use ES6 modules for new projects
- Keep modules focused and single-purpose
- Use descriptive names for exports
- Organize code by features, not file types
- Leverage dynamic imports for code splitting
- Avoid circular dependencies
- Use barrel exports for cleaner imports
- Include file extensions in import paths

### When to Use What

- **Named Exports**: When exporting multiple related functions/classes
- **Default Exports**: When exporting a single main functionality
- **Dynamic Imports**: For code splitting and conditional loading
- **CommonJS**: When working with older Node.js projects
- **ES6 Modules**: For modern applications and libraries

### Next Steps

- Practice with module bundlers (Webpack, Vite, Rollup)
- Learn about tree shaking and code splitting
- Explore micro-frontends and module federation
- Study package management and npm/yarn
- Practice with real-world project organization

---

**File Navigation:**

- **Previous:** [18. Asynchronous JavaScript](18-async.md)
- **Next:** [Advanced JavaScript Patterns](20-advanced-patterns.md)
