# JavaScript Functions

Functions are reusable blocks of code that perform specific tasks. They are first-class objects in JavaScript.

## Function Declaration Types

### 1. Function Declaration

- **Hoisting**: ✅ Works (can be called before declaration)
- **Syntax**: Traditional function syntax

```javascript
// Can be called before declaration due to hoisting
greet(); // Works!

function greet() {
  console.log("Hello World!");
}
```

### 2. Function Expression

- **Hoisting**: ❌ Doesn't work (cannot be called before declaration)
- **Syntax**: Function assigned to a variable

```javascript
// Cannot be called before declaration
// sayHello(); // ❌ Error!

let sayHello = function () {
  console.log("Hello!");
};

sayHello(); // ✅ Works
```

### 3. Arrow Function

- **Hoisting**: ❌ Doesn't work
- **Syntax**: ES6+ concise syntax
- **Special**: No `this` binding, cannot be constructors

```javascript
// Cannot be called before declaration
// add(); // ❌ Error!

let add = (a, b) => {
  return a + b;
};

// Shorter syntax for single expressions
let multiply = (a, b) => a * b;

// Single parameter doesn't need parentheses
let square = (x) => x * x;
```

## Parameters and Arguments

### Basic Parameters

```javascript
function greet(name, age) {
  // Parameters
  console.log(`Hello ${name}, you are ${age} years old`);
}

greet("John", 25); // Arguments
```

### Default Parameters

```javascript
function greet(name = "Guest", age = 0) {
  console.log(`Hello ${name}, you are ${age} years old`);
}

greet(); // "Hello Guest, you are 0 years old"
greet("Alice"); // "Hello Alice, you are 0 years old"
greet("Bob", 30); // "Hello Bob, you are 30 years old"
```

## Rest Parameters (`...`)

Collect multiple arguments into an array:

```javascript
function sum(...numbers) {
  // Rest parameter
  let total = 0;
  for (let num of numbers) {
    total += num;
  }
  return total;
}

console.log(sum(1, 2, 3)); // 6
console.log(sum(1, 2, 3, 4, 5)); // 15

// Mixed parameters
function greetAll(greeting, ...names) {
  names.forEach((name) => {
    console.log(`${greeting} ${name}`);
  });
}

greetAll("Hello", "Alice", "Bob", "Charlie");
// Hello Alice
// Hello Bob
// Hello Charlie
```

**Note**: Rest parameter must be the last parameter in the function definition.

## Spread Operator (`...`)

Spread elements of an array/object:

```javascript
// In function calls
function add(a, b, c) {
  return a + b + c;
}

let numbers = [1, 2, 3];
console.log(add(...numbers)); // 6 (spreads array elements)

// In arrays
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// In objects
let obj1 = { a: 1, b: 2 };
let obj2 = { c: 3, d: 4 };
let merged = { ...obj1, ...obj2 }; // { a: 1, b: 2, c: 3, d: 4 }
```

## Function Concepts

### First-Class Functions

Functions can be treated as values:

```javascript
// Store in variables
let myFunc = function () {
  console.log("Hello");
};

// Pass as arguments
function runFunction(fn) {
  fn(); // Execute the passed function
}

runFunction(myFunc); // "Hello"

// Return from functions
function createGreeter() {
  return function (name) {
    console.log(`Hello ${name}`);
  };
}

let greeter = createGreeter();
greeter("Alice"); // "Hello Alice"
```

### Higher-Order Functions

Functions that either:

1. Accept other functions as parameters, or
2. Return functions

```javascript
// 1. Accept function as parameter
function processData(data, callback) {
  let result = data * 2;
  callback(result);
}

processData(5, function (result) {
  console.log(result); // 10
});

// 2. Return a function
function multiplier(factor) {
  return function (number) {
    return number * factor;
  };
}

let double = multiplier(2);
console.log(double(5)); // 10

let triple = multiplier(3);
console.log(triple(4)); // 12
```

### Pure vs Impure Functions

#### Pure Functions

- Don't change values outside their scope
- Same input always produces same output
- No side effects

```javascript
// Pure function
function add(a, b) {
  return a + b; // Only depends on parameters
}

function calculateArea(radius) {
  return Math.PI * radius * radius; // No external state modified
}
```

#### Impure Functions

- Modify external state
- May produce different outputs for same input

```javascript
let counter = 0;

// Impure function (modifies external variable)
function incrementCounter() {
  counter++; // Modifies external state
  return counter;
}

// Impure function (depends on external state)
function getRandomGreeting(name) {
  let greetings = ["Hello", "Hi", "Hey"];
  let random = Math.floor(Math.random() * greetings.length);
  return `${greetings[random]} ${name}`;
}
```

## Lexical Scope

Variables are accessible based on where they are defined in the source code:

### Scope Levels:

1. **Global scope**: Variables defined outside any function/block
2. **Function scope**: Variables defined inside a function
3. **Block scope**: Variables defined inside a block (`let`/`const`)
4. **Nested scope**: Inner functions access outer function variables

```javascript
let globalVar = "I'm global";

function outerFunction() {
  let outerVar = "I'm in outer function";

  function innerFunction() {
    let innerVar = "I'm in inner function";

    console.log(globalVar); // ✅ Can access
    console.log(outerVar); // ✅ Can access
    console.log(innerVar); // ✅ Can access
  }

  innerFunction();
  // console.log(innerVar); // ❌ Cannot access
}

outerFunction();
```

## Closures

A closure is created when a function returns another function that uses variables from the parent function:

```javascript
function counter() {
  let count = 0; // Private variable

  return function () {
    count += 1;
    return count;
  };
}

let myCounter = counter(); // Closure is created
console.log(myCounter()); // 1
console.log(myCounter()); // 2
console.log(myCounter()); // 3

// Each closure has its own copy of variables
let anotherCounter = counter();
console.log(anotherCounter()); // 1 (independent counter)
```

### Practical Use of Closures:

```javascript
// Creating private variables
function createBankAccount(initialBalance) {
  let balance = initialBalance;

  return {
    deposit: function (amount) {
      balance += amount;
      return balance;
    },
    withdraw: function (amount) {
      if (amount <= balance) {
        balance -= amount;
        return balance;
      } else {
        return "Insufficient funds";
      }
    },
    getBalance: function () {
      return balance;
    },
  };
}

let account = createBankAccount(100);
console.log(account.deposit(50)); // 150
console.log(account.withdraw(30)); // 120
console.log(account.getBalance()); // 120
// balance variable is private and cannot be accessed directly
```

## IIFE (Immediately Invoked Function Expression)

Functions that run immediately when defined:

```javascript
// Basic IIFE
(function () {
  console.log("I run immediately!");
})();

// IIFE with parameters
(function (name) {
  console.log(`Hello ${name}!`);
})("World");

// Arrow function IIFE
(() => {
  console.log("Arrow IIFE!");
})();

// IIFE returning value
let result = (function (a, b) {
  return a + b;
})(5, 3);
console.log(result); // 8
```

### Use Cases for IIFE:

- Avoid polluting global scope
- Create private scope for initialization code
- Module pattern (before ES6 modules)

## Best Practices

1. **Use descriptive function names** that explain what the function does
2. **Keep functions small** and focused on a single task
3. **Use arrow functions** for short, simple functions
4. **Prefer pure functions** when possible for easier testing and debugging
5. **Use default parameters** instead of checking for undefined
6. **Use rest parameters** instead of the `arguments` object
7. **Understand closure implications** for memory usage
8. **Use IIFE** to avoid global scope pollution when needed
