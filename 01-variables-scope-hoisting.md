# Variables, Scope, and Hoisting in JavaScript

## Variable Declarations

JavaScript provides three ways to declare variables, each with different characteristics:

### 1. `var` Declaration

- **Scope**: Function scoped
- **Hoisting**: Hoisted to top with `undefined` value
- **Window object**: Added to window object (problematic)
- **Redeclaration**: Can be redeclared without error

```javascript
var a = 12; // Accessible throughout the program
console.log(a); // 12

// Ways to declare and initialize
var a;
a = 12;

// Or in one line
var a = 12;
```

### 2. `let` Declaration

- **Scope**: Block scoped
- **Hoisting**: Hoisted but stays in Temporal Dead Zone (TDZ)
- **Redeclaration**: Cannot be redeclared (throws error)

```javascript
let a = 12;
// let a = 15; // ❌ Error: 'a' has already been declared
```

### 3. `const` Declaration

- **Scope**: Block scoped
- **Hoisting**: Hoisted but stays in TDZ
- **Reassignment**: Cannot be reassigned
- **Use case**: For values that won't change (like π)

```javascript
const PI = 3.14159;
// PI = 3.14; // ❌ Error: Assignment to constant variable

// Object properties can be updated (not reassignment)
const user = { name: "John" };
user.name = "Bob"; // ✅ This works (updating property)
console.log(user); // { name: 'Bob' }

// To prevent object mutation, use Object.freeze()
const frozenUser = Object.freeze({ name: "John" });
```

## Scope Types

### 1. Global Scope

- Variables declared outside any function or block
- Can be used throughout the entire program

### 2. Block Scope `{}`

- Applies to `let` and `const`
- Variables are only accessible within the block

```javascript
{
  let blockScoped = "I'm in a block";
  const alsoBlockScoped = "Me too";
}
// console.log(blockScoped); // ❌ ReferenceError
```

### 3. Function Scope

- Applies to `var`
- Variables are accessible throughout the function

```javascript
function example() {
  var functionScoped = "I'm function scoped";
  if (true) {
    var stillFunctionScoped = "Me too";
  }
  console.log(stillFunctionScoped); // ✅ Works
}
```

## Lexical Scoping

**Lexical Scoping** (also called **Static Scoping**) is a fundamental concept in JavaScript that determines how variables are accessed based on where they are defined in the code structure, not where they are called.

### How Lexical Scoping Works

JavaScript determines variable access by looking at the **physical placement** of variables and functions in the code when it's written, not when it's executed.

```javascript
let outerVariable = "I'm outer";

function outerFunction() {
  let outerFunctionVar = "I'm in outer function";

  function innerFunction() {
    let innerVar = "I'm inner";

    // Inner function can access:
    console.log(innerVar); // ✅ Own variable
    console.log(outerFunctionVar); // ✅ Outer function's variable
    console.log(outerVariable); // ✅ Global variable
  }

  innerFunction();
  // console.log(innerVar); // ❌ Error: Can't access inner variable
}

outerFunction();
```

### The Scope Chain

When JavaScript looks for a variable, it follows the **scope chain**:

1. **Current scope** - Look in the current function/block
2. **Parent scope** - Look in the containing function/block
3. **Grandparent scope** - Continue up the chain
4. **Global scope** - Finally check global scope
5. **ReferenceError** - If not found, throw an error

```javascript
let global = "Global";

function level1() {
  let level1Var = "Level 1";

  function level2() {
    let level2Var = "Level 2";

    function level3() {
      let level3Var = "Level 3";

      // Scope chain lookup order:
      console.log(level3Var); // 1. Found in current scope
      console.log(level2Var); // 2. Found in parent scope
      console.log(level1Var); // 3. Found in grandparent scope
      console.log(global); // 4. Found in global scope
      // console.log(notDefined); // 5. ReferenceError
    }

    level3();
  }

  level2();
}

level1();
```

### Lexical Scoping vs Dynamic Scoping

**JavaScript uses Lexical Scoping** - the scope is determined by where variables are declared in the code.

```javascript
let x = "global";

function outer() {
  let x = "outer";
  inner();
}

function inner() {
  console.log(x); // Prints "global" (lexical scoping)
  // NOT "outer" (which would be dynamic scoping)
}

outer(); // Output: "global"
```

**Why "global"?** Because `inner()` function was **defined** in global scope, so it looks for `x` in global scope, regardless of where it was **called** from.

### Practical Example: Function Factory

Lexical scoping enables powerful patterns like function factories:

```javascript
function createCounter(initialValue = 0) {
  let count = initialValue;

  return function () {
    count++; // Accesses 'count' from outer scope
    return count;
  };
}

const counter1 = createCounter(0);
const counter2 = createCounter(100);

console.log(counter1()); // 1
console.log(counter1()); // 2
console.log(counter2()); // 101
console.log(counter1()); // 3
```

Each returned function "remembers" its own `count` variable due to lexical scoping.

### Lexical Scoping with var vs let/const

```javascript
function demonstrateScoping() {
  if (true) {
    var varVariable = "I'm var";
    let letVariable = "I'm let";
  }

  console.log(varVariable); // ✅ "I'm var" (function scoped)
  // console.log(letVariable); // ❌ Error (block scoped)
}
```

### Key Points About Lexical Scoping

1. **Determined at compile time**, not runtime
2. **Inner functions have access to outer variables**
3. **Outer functions cannot access inner variables**
4. **Creates the foundation for closures**
5. **Scope chain follows the nesting structure of code**

```javascript
// Visual representation of scope chain
let a = "global a";

function outer() {
  // Outer scope
  let b = "outer b";

  function middle() {
    // Middle scope
    let c = "middle c";

    function inner() {
      // Inner scope
      let d = "inner d";

      // Scope chain: inner → middle → outer → global
      console.log(d); // Found in inner scope
      console.log(c); // Found in middle scope
      console.log(b); // Found in outer scope
      console.log(a); // Found in global scope
    }

    inner();
  }

  middle();
}

outer();
```

## Hoisting

JavaScript prepares memory before running code and moves all declarations to the top - this is called **hoisting**.

### How Different Declarations are Hoisted:

1. **`var`**: Hoisted and initialized with `undefined`
2. **`let`/`const`**: Hoisted but **not initialized** (stays in TDZ)

```javascript
console.log(a); // undefined (not an error)
var a = 12;

console.log(b); // ❌ ReferenceError: Cannot access 'b' before initialization
let b = 15;
```

## Temporal Dead Zone (TDZ)

The area between the hoisting of a `let`/`const` variable and its initialization is called the **Temporal Dead Zone**.

```javascript
// TDZ starts here for variable 'a'
console.log(a); // ❌ ReferenceError: Cannot access 'a' before initialization

let a = 12; // TDZ ends here
console.log(a); // ✅ 12
```

### What Actually Happens:

```javascript
// JavaScript internally does something like:
let a; // Hoisted but not accessible
console.log(a); // ❌ Reference error
a = 12; // Initialization happens here
```

## Key Differences Summary

| Feature       | `var`           | `let`      | `const`   |
| ------------- | --------------- | ---------- | --------- |
| Scope         | Function        | Block      | Block     |
| Hoisting      | Yes (undefined) | Yes (TDZ)  | Yes (TDZ) |
| Redeclaration | ✅ Allowed      | ❌ Error   | ❌ Error  |
| Reassignment  | ✅ Allowed      | ✅ Allowed | ❌ Error  |
| TDZ           | ❌ No           | ✅ Yes     | ✅ Yes    |

## Best Practices

1. **Use `const` by default** for values that won't be reassigned
2. **Use `let`** when you need to reassign the variable
3. **Avoid `var`** in modern JavaScript due to its confusing scoping rules
4. **Declare variables at the top** of their scope to avoid TDZ issues
