# JavaScript Data Types

JavaScript has two main categories of data types: **Primitives** and **Reference Types**.

## Primitive Data Types

Primitives can be copied directly and are stored by value.

### 1. Number

- Represents both integers and floating-point numbers
- Special numeric values: `Infinity`, `-Infinity`, `NaN`

```javascript
let age = 25;
let price = 99.99;
let infinity = 1 / 0; // Infinity
let notANumber = "hello" / 2; // NaN

console.log(typeof NaN); // "number" (failed mathematical operation)
```

### 2. String

- Represents text data
- Can use single quotes, double quotes, or backticks (template literals)

```javascript
let name = "John";
let message = "Hello World";
let template = `Hello, ${name}!`; // Template literal
```

### 3. Boolean

- Represents logical values: `true` or `false`

```javascript
let isActive = true;
let isComplete = false;
```

### 4. Null

- Represents an intentional empty value
- It's a primitive but `typeof null` returns `"object"` (known JavaScript quirk)

```javascript
let data = null; // Intentionally empty
console.log(typeof null); // "object" (this is a bug in JavaScript)
```

### 5. Undefined

- Variable declared but not assigned a value
- Default value for uninitialized variables

```javascript
let x;
console.log(x); // undefined
console.log(typeof x); // "undefined"
```

## Reference Data Types

Reference types cannot be copied directly and are stored as memory references.

### 1. Object

- Collection of key-value pairs
- Stores structured data

```javascript
let user = {
  name: "John",
  age: 30,
  isActive: true,
};
```

### 2. Array

- Ordered collection of values
- Special type of object

```javascript
let numbers = [1, 2, 3, 4, 5];
let mixed = [1, "hello", true, null];
```

### 3. Function

- First-class objects in JavaScript
- Can be stored in variables, passed as arguments

```javascript
function greet() {
  console.log("Hello!");
}

let sayHello = function () {
  console.log("Hi there!");
};
```

## Type Checking

Use the `typeof` operator to check data types:

```javascript
console.log(typeof 42); // "number"
console.log(typeof "hello"); // "string"
console.log(typeof true); // "boolean"
console.log(typeof undefined); // "undefined"
console.log(typeof null); // "object" (quirk!)
console.log(typeof {}); // "object"
console.log(typeof []); // "object"
console.log(typeof function () {}); // "function"
```

## Falsy Values

Values that evaluate to `false` in boolean context:

```javascript
// All falsy values in JavaScript:
false;
0;
(""); // empty string
null;
undefined;
NaN;
document.all; // (legacy, rarely encountered)
```

## Boolean Conversion

### Using the `!` (NOT) Operator

```javascript
let value = "hello";

console.log(value); // "hello"
console.log(!value); // false (negation)
console.log(!!value); // true (double negation = actual boolean)

// Double negation (!!) is equivalent to Boolean()
console.log(Boolean(value)); // true
console.log(typeof !!value); // "boolean"
```

### Truthy vs Falsy Examples

```javascript
// Truthy values (everything not in falsy list)
console.log(!!"hello"); // true
console.log(!!42); // true
console.log(!![]); // true (empty array is truthy)
console.log(!!{}); // true (empty object is truthy)

// Falsy values
console.log(!!0); // false
console.log(!!""); // false
console.log(!!null); // false
console.log(!!undefined); // false
console.log(!!NaN); // false
```

## Type Coercion

JavaScript automatically converts types in certain situations:

```javascript
// String concatenation
console.log("5" + 3); // "53" (number to string)
console.log(5 + "3"); // "53" (number to string)

// Arithmetic operations
console.log("5" - 3); // 2 (string to number)
console.log("5" * 2); // 10 (string to number)

// Comparison
console.log(5 == "5"); // true (loose equality, type coercion)
console.log(5 === "5"); // false (strict equality, no coercion)
```

## Key Differences: Primitives vs References

### Primitives (Pass by Value)

```javascript
let a = 5;
let b = a; // b gets a copy of a's value
a = 10;
console.log(b); // 5 (unchanged)
```

### References (Pass by Reference)

```javascript
let obj1 = { name: "John" };
let obj2 = obj1; // obj2 points to the same object
obj1.name = "Jane";
console.log(obj2.name); // "Jane" (changed because same reference)
```

## Best Practices

1. **Use strict equality (`===`)** instead of loose equality (`==`) to avoid unexpected type coercion
2. **Check for `null` explicitly** since `typeof null === "object"`
3. **Use `Array.isArray()`** to check if something is an array
4. **Be aware of falsy values** when writing conditional statements
5. **Understand the difference** between primitives and reference types for proper data handling
