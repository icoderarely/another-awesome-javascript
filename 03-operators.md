# JavaScript Operators

Operators are symbols that perform operations on operands (values and variables).

## Arithmetic Operators

Perform mathematical calculations:

| Operator | Description                          | Example      |
| -------- | ------------------------------------ | ------------ |
| `+`      | Addition (also string concatenation) | `5 + 3 = 8`  |
| `-`      | Subtraction                          | `5 - 3 = 2`  |
| `*`      | Multiplication                       | `5 * 3 = 15` |
| `/`      | Division                             | `6 / 3 = 2`  |
| `%`      | Modulus (remainder)                  | `7 % 3 = 1`  |
| `**`     | Exponentiation (power)               | `2 ** 3 = 8` |

### Special Cases with Addition (`+`)

```javascript
// Numeric addition
console.log(5 + 3); // 8

// String concatenation
console.log("5" + "3"); // "53"
console.log("Hello" + " World"); // "Hello World"

// Mixed types (coercion to string)
console.log("5" + 3); // "53"
console.log(5 + "3"); // "53"
```

## Comparison Operators

Compare values and return boolean results:

| Operator | Description                         | Example               |
| -------- | ----------------------------------- | --------------------- |
| `==`     | Loose equality (with type coercion) | `5 == "5"` → `true`   |
| `===`    | Strict equality (no type coercion)  | `5 === "5"` → `false` |
| `!=`     | Loose inequality                    | `5 != "3"` → `true`   |
| `!==`    | Strict inequality                   | `5 !== "5"` → `true`  |
| `>`      | Greater than                        | `5 > 3` → `true`      |
| `<`      | Less than                           | `3 < 5` → `true`      |
| `>=`     | Greater than or equal               | `5 >= 5` → `true`     |
| `<=`     | Less than or equal                  | `3 <= 5` → `true`     |

### Loose vs Strict Equality

```javascript
// Loose equality (==) - performs type coercion
console.log(5 == "5"); // true
console.log(0 == false); // true
console.log(null == undefined); // true

// Strict equality (===) - no type coercion
console.log(5 === "5"); // false
console.log(0 === false); // false
console.log(null === undefined); // false
```

## Logical Operators

Perform logical operations and return boolean values:

| Operator | Description | Behavior                                |
| -------- | ----------- | --------------------------------------- |
| `&&`     | Logical AND | Returns `false` if any operand is falsy |
| `\|\|`   | Logical OR  | Returns `true` if any operand is truthy |
| `!`      | Logical NOT | Inverts the boolean value               |

### AND Operator (`&&`)

```javascript
console.log(true && true); // true
console.log(true && false); // false
console.log(false && true); // false

// Short-circuit evaluation
console.log("hello" && "world"); // "world" (last truthy value)
console.log(0 && "hello"); // 0 (first falsy value)
```

### OR Operator (`||`)

```javascript
console.log(true || false); // true
console.log(false || false); // false

// Short-circuit evaluation
console.log("hello" || "world"); // "hello" (first truthy value)
console.log(0 || "hello"); // "hello" (first truthy value)
```

### NOT Operator (`!`)

```javascript
console.log(!true); // false
console.log(!false); // true
console.log(!"hello"); // false
console.log(!0); // true
```

## Assignment Operators

Assign values to variables:

| Operator | Description               | Example   | Equivalent   |
| -------- | ------------------------- | --------- | ------------ |
| `=`      | Basic assignment          | `a = 5`   | -            |
| `+=`     | Addition assignment       | `a += 3`  | `a = a + 3`  |
| `-=`     | Subtraction assignment    | `a -= 3`  | `a = a - 3`  |
| `*=`     | Multiplication assignment | `a *= 3`  | `a = a * 3`  |
| `/=`     | Division assignment       | `a /= 3`  | `a = a / 3`  |
| `%=`     | Modulus assignment        | `a %= 3`  | `a = a % 3`  |
| `**=`    | Exponentiation assignment | `a **= 3` | `a = a ** 3` |

### Examples:

```javascript
let a = 10;

a += 5; // a = 15
a -= 3; // a = 12
a *= 2; // a = 24
a /= 4; // a = 6
a %= 4; // a = 2
a **= 3; // a = 8
```

## Unary Operators

Operate on a single operand:

| Operator | Description                     | Example                       |
| -------- | ------------------------------- | ----------------------------- |
| `+`      | Unary plus (converts to number) | `+"5"` → `5`                  |
| `-`      | Unary minus (negates)           | `-5` → `-5`                   |
| `!`      | Logical NOT                     | `!true` → `false`             |
| `typeof` | Returns type of operand         | `typeof "hello"` → `"string"` |
| `++`     | Increment                       | `a++` or `++a`                |
| `--`     | Decrement                       | `a--` or `--a`                |

### Increment and Decrement

#### Pre-increment (`++a`)

```javascript
let a = 5;
console.log(++a); // 6 (increment first, then return)
console.log(a); // 6
```

#### Post-increment (`a++`)

```javascript
let a = 5;
console.log(a++); // 5 (return first, then increment)
console.log(a); // 6
```

#### Pre-decrement (`--a`)

```javascript
let a = 5;
console.log(--a); // 4 (decrement first, then return)
console.log(a); // 4
```

#### Post-decrement (`a--`)

```javascript
let a = 5;
console.log(a--); // 5 (return first, then decrement)
console.log(a); // 4
```

## Ternary Operator

Conditional operator that returns one of two values based on a condition:

### Syntax:

```javascript
condition ? valueIfTrue : valueIfFalse;
```

### Examples:

```javascript
let age = 18;
let status = age >= 18 ? "adult" : "minor";
console.log(status); // "adult"

let score = 85;
let grade = score >= 90 ? "A" : score >= 80 ? "B" : "C";
console.log(grade); // "B"

// Can be used for function calls
let message = isLoggedIn ? showDashboard() : showLogin();
```

## Operator Precedence

Some operators have higher precedence than others:

1. **Unary operators**: `!`, `+`, `-`, `++`, `--`
2. **Arithmetic**: `**`, `*`, `/`, `%`, `+`, `-`
3. **Comparison**: `<`, `>`, `<=`, `>=`, `==`, `===`, `!=`, `!==`
4. **Logical**: `&&`, `||`
5. **Ternary**: `? :`
6. **Assignment**: `=`, `+=`, `-=`, etc.

### Example:

```javascript
let result = 2 + 3 * 4; // 14, not 20 (multiplication first)
let result2 = (2 + 3) * 4; // 20 (parentheses override precedence)
```

## Best Practices

1. **Use strict equality (`===`)** instead of loose equality (`==`)
2. **Use parentheses** to make complex expressions clearer
3. **Be careful with type coercion** in arithmetic operations
4. **Understand operator precedence** or use parentheses when in doubt
5. **Use meaningful variable names** with assignment operators
6. **Consider readability** when using ternary operators (avoid nesting too deeply)
