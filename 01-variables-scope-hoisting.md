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
