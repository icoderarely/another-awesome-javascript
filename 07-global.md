# Global Object in JavaScript

The **Global Object** provides variables and functions that are available anywhere in your JavaScript program. It's built into the language or the environment where JavaScript runs.

## Environment-Specific Global Objects

### Browser Environment

- **Global object**: `window`
- Represents the browser window
- Provides access to DOM, BOM, and global variables
- Implicit access: you don't need to write `window.` to call `alert()`.

```javascript
console.log(window); // Window object
window.alert("Hello World!");
```

### Node.js Environment

- **Global object**: `global`
- Provides server-side specific functionality

```javascript
console.log(global); // Global object in Node.js
```

### Universal Access: `globalThis`

- **Standardized name**: `globalThis`
- Works across all environments (modern browsers and Node.js)
- Recommended for cross-platform compatibility

```javascript
console.log(globalThis); // Works everywhere
```

## Accessing Global Properties

Properties of the global object can be accessed directly:

```javascript
// In browser
window.alert("Hello"); // Direct access
alert("Hello"); // Implicit access (same as above)

// Cross-platform
globalThis.console.log("Hello");
console.log("Hello"); // Implicit access
```

## Global Variables and the Global Object

### Variables Declared with `var`

Global variables declared with `var` become properties of the global object:

```javascript
var globalVar = 5;
console.log(window.globalVar); // 5 (in browser)
console.log(globalVar); // 5 (same variable)

// Proof they're the same
window.globalVar = 10;
console.log(globalVar); // 10
```

### Variables Declared with `let` and `const`

Variables declared with `let` and `const` do **not** become properties of the global object:

```javascript
let letVar = 5;
const constVar = 10;

console.log(window.letVar); // undefined
console.log(window.constVar); // undefined
console.log(letVar); // 5 (still accessible globally)
console.log(constVar); // 10 (still accessible globally)
```

## Creating Global Properties

If you need to make a value globally accessible, add it as a property:

```javascript
// Method 1: Direct assignment
window.currentUser = {
  name: "John",
  role: "admin",
};

console.log(window.currentUser.name); // "John"
console.log(currentUser.name); // "John" (implicit access)

// Method 2: Using globalThis (cross-platform)
globalThis.appConfig = {
  version: "1.0.0",
  apiUrl: "https://api.example.com",
};
```

## Functions in Global Scope

Functions declared in global scope also become properties of the global object:

```javascript
function globalFunction() {
  return "I'm global!";
}

console.log(window.globalFunction()); // "I'm global!"
console.log(globalFunction()); // "I'm global!" (same function)
```

## Common Global Objects and Functions

### Browser Globals

```javascript
// DOM related
document; // Document object
window; // Window object

// Timing functions
setTimeout(); // Execute after delay
setInterval(); // Execute repeatedly
clearTimeout(); // Cancel timeout
clearInterval(); // Cancel interval

// User interaction
alert(); // Show alert dialog
confirm(); // Show confirmation dialog
prompt(); // Show input dialog

// Network
fetch(); // Make HTTP requests
XMLHttpRequest; // Legacy HTTP requests
```

### JavaScript Core Globals

```javascript
// Built-in objects
Object; // Object constructor
Array; // Array constructor
Date; // Date constructor
Math; // Math object
JSON; // JSON utilities

// Global functions
parseInt(); // Parse integer
parseFloat(); // Parse float
isNaN(); // Check if NaN
encodeURIComponent(); // URL encoding
decodeURIComponent(); // URL decoding
```

## Best Practices

### ❌ Avoid Global Variables

```javascript
// Bad: Pollutes global scope
var userName = "John";
var userAge = 30;
var userEmail = "john@example.com";

function processUser() {
  // Uses global variables
}
```

### ✅ Use Modules or Namespacing

```javascript
// Good: Use objects to namespace
const User = {
  name: "John",
  age: 30,
  email: "john@example.com",

  process() {
    // Encapsulated logic
  },
};

// Or use modules (ES6)
export const userData = {
  name: "John",
  age: 30,
};
```

### ✅ Use IIFE to Avoid Global Pollution

```javascript
(function () {
  // Private scope
  let privateVar = "I'm not global";

  // Only expose what's necessary
  window.MyApp = {
    init() {
      console.log("App initialized");
    },
  };
})();
```

## Checking for Global Variables

```javascript
// Check if a global variable exists
if (typeof someGlobalVar !== "undefined") {
  console.log("someGlobalVar exists");
}

// Or using 'in' operator
if ("someGlobalVar" in window) {
  console.log("someGlobalVar exists in window");
}

// Using optional chaining (modern)
if (globalThis.someGlobalVar) {
  console.log("someGlobalVar exists");
}
```

## Why Minimize Global Usage?

1. **Name collisions**: Different scripts might use the same global names
2. **Maintenance issues**: Hard to track where global variables are modified
3. **Testing difficulties**: Global state makes unit testing harder
4. **Memory leaks**: Global variables persist for the entire application lifetime
5. **Code coupling**: Creates dependencies between unrelated parts of code

## Conclusion

While the global object is essential for JavaScript's operation, direct manipulation should be minimal. Use modules, namespacing, and proper scoping to create maintainable and conflict-free code.
