# Execution Context

An **execution context** is an environment created by the JavaScript engine that handles the transformation and execution of JavaScript code. Since browsers cannot natively understand high-level JavaScript code, it must be converted to machine code for execution.

## 📚 What is Execution Context?

When a JavaScript file is loaded, the **Global Execution Context** is created. This is the foundation where all JavaScript code execution begins.

```javascript
const firstName = "john";
let lastName = "doe";

function greet(nameToGreet) {
  const fullName = nameToGreet + " " + lastName;
  return "Hello, " + fullName;
}

greet(firstName); // Function call creates new execution context
```

## Types of Execution Context

### 1. Global Execution Context (GEC)

- **Default context** when JavaScript engine receives a script file
- **JavaScript code not inside any function** gets executed here
- **Only one GEC** can exist per JavaScript file
- **Created automatically** when script loads

### 2. Function Execution Context (FEC)

- **Created whenever a function is called**
- **Separate context** within the Global Execution Context
- **Evaluates and executes** code within that specific function
- **Multiple FECs** can exist (one per function call)

## Phases of Execution Context

Every execution context goes through **two main phases**:

### Phase 1: Creation Phase (Memory Creation)

The execution context is associated with an **Execution Context Object (ECO)** that stores important data used during runtime. This phase has three stages:

#### a) Creation of Variable Object (VO)

A container that stores variable and function declarations within the execution context.

```javascript
// During creation phase, memory allocation happens:
var x = 10; // x → undefined (hoisted)
let y = 20; // y → uninitialized (TDZ)
const z = 30; // z → uninitialized (TDZ)

function myFunc() {
  // myFunc → function reference
  return "Hello";
}
```

**Hoisting Process:**

- **`var` declarations**: Stored as `undefined`
- **Function declarations**: Stored with complete function reference
- **`let`/`const`**: Hoisted but remain uninitialized ([see Variables, Scope, and Hoisting](01-variables-scope-hoisting.md))

#### b) Creation of Scope Chain

**Scope** defines how accessible code is to other parts of the codebase. The scope chain determines variable access through nested functions.

```javascript
let globalVar = "I'm global";

function outer() {
  let outerVar = "I'm outer";

  function inner() {
    let innerVar = "I'm inner";

    // Scope chain: inner → outer → global
    console.log(innerVar); // ✅ Found in inner scope
    console.log(outerVar); // ✅ Found in outer scope
    console.log(globalVar); // ✅ Found in global scope
  }

  inner();
}
```

**Lexical Scoping**: Inner functions have access to outer function variables, but outer functions cannot access inner variables. This enables **closures**.

#### c) Setting the `this` Keyword

The `this` keyword refers to the context where an execution context belongs.

```javascript
// Global context
console.log(this); // Window object (in browsers)

// Object method context
const user = {
  name: "John",
  greet() {
    console.log(this.name); // "John" - `this` refers to user object
  },
};

// Function context
function regularFunction() {
  console.log(this); // Window object (non-strict mode)
}
```

### Phase 2: Execution Phase

**Actual code execution** begins. The Variable Object reads the code and updates variables with their actual values.

```javascript
// Creation Phase: x = undefined, myFunc = function reference
// Execution Phase: x = 10, then function executes

var x = 10; // x gets actual value: 10
console.log(x); // Outputs: 10

function myFunc() {
  return "Hello World";
}

const result = myFunc(); // Function executes, returns "Hello World"
```

## JavaScript Execution Stack (Call Stack)

The **Call Stack** keeps track of execution contexts created during script execution. JavaScript is **single-threaded**, so it can only execute one task at a time.

### How Call Stack Works

```javascript
function firstFunction() {
  console.log("First function start");
  secondFunction();
  console.log("First function end");
}

function secondFunction() {
  console.log("Second function start");
  thirdFunction();
  console.log("Second function end");
}

function thirdFunction() {
  console.log("Third function executed");
}

firstFunction();
```

**Call Stack Execution Order:**

1. **Global Execution Context** pushed to stack
2. `firstFunction()` called → **FEC created** and pushed to top
3. `secondFunction()` called → **FEC created** and pushed to top
4. `thirdFunction()` called → **FEC created** and pushed to top
5. `thirdFunction()` completes → **popped from stack**
6. `secondFunction()` completes → **popped from stack**
7. `firstFunction()` completes → **popped from stack**
8. **Global context** remains

### Stack Visualization

```
|                    |
|  thirdFunction()   | ← Active (top of stack)
|  secondFunction()  |
|  firstFunction()   |
|  Global Context    |
|____________________|
```

## Practical Example: Complete Execution

```javascript
var globalVar = "Global";

function outerFunction(param) {
  var outerVar = "Outer";

  function innerFunction() {
    var innerVar = "Inner";
    console.log(globalVar + " " + outerVar + " " + innerVar + " " + param);
  }

  innerFunction();
}

outerFunction("Parameter");
```

**Execution Steps:**

1. **Global EC Created**

   - Creation: `globalVar = undefined`, `outerFunction = function reference`
   - Execution: `globalVar = "Global"`

2. **outerFunction EC Created** (when called)

   - Creation: `outerVar = undefined`, `innerFunction = function reference`, `param = "Parameter"`
   - Execution: `outerVar = "Outer"`

3. **innerFunction EC Created** (when called)

   - Creation: `innerVar = undefined`
   - Execution: `innerVar = "Inner"`, access scope chain for other variables
   - Output: "Global Outer Inner Parameter"

4. **Stack Cleanup**: innerFunction → outerFunction → Global Context

## 🔑 Key Takeaways

1. **Two Types**: Global Execution Context and Function Execution Context
2. **Two Phases**: Creation (memory allocation) and Execution (code running)
3. **Hoisting**: Variables and functions are allocated memory before execution
4. **Scope Chain**: Determines variable accessibility through nested scopes
5. **Call Stack**: Manages execution order in LIFO (Last In, First Out) manner
6. **Single-threaded**: JavaScript executes one thing at a time
7. **`this` Binding**: Determined during execution context creation

## 🎯 Related Concepts

- **[Variables, Scope, and Hoisting](01-variables-scope-hoisting.md)**: Detailed explanation of hoisting and scope
- **[Functions](04-functions.md)**: How function calls create execution contexts
- **Closures**: Enabled by lexical scoping and execution context preservation

---

**Reference**: [Execution Context - FreeCodeCamp](https://www.freecodecamp.org/news/execution-context-how-javascript-works-behind-the-scenes/)
