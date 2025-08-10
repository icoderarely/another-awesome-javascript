# JavaScript Arrays

Arrays are ordered collections that can store multiple values of any type. They are a special type of object in JavaScript.

## Array Creation

### 1. Array Literal (Recommended)

```javascript
let arr = [1, 2, 3, 4, 5];
let mixed = [1, "hello", true, null, undefined];
let empty = [];
```

### 2. Array Constructor

```javascript
let arr = new Array(); // Empty array
let arr2 = new Array(5); // Array with 5 empty slots
let arr3 = new Array(1, 2, 3); // [1, 2, 3]
```

## Accessing Array Elements

Arrays use zero-based indexing:

```javascript
let fruits = ["apple", "banana", "orange"];

console.log(fruits[0]); // "apple"
console.log(fruits[1]); // "banana"
console.log(fruits[2]); // "orange"
console.log(fruits[3]); // undefined (index doesn't exist)

// Array length
console.log(fruits.length); // 3
```

## Array Methods

### Modifier Methods (Change Original Array)

#### 1. `push()` - Add to End

```javascript
let arr = [1, 2, 3];
arr.push(4); // Returns new length (4)
console.log(arr); // [1, 2, 3, 4]

arr.push(5, 6); // Add multiple elements
console.log(arr); // [1, 2, 3, 4, 5, 6]
```

#### 2. `pop()` - Remove from End

```javascript
let arr = [1, 2, 3, 4];
let lastElement = arr.pop(); // Returns removed element
console.log(lastElement); // 4
console.log(arr); // [1, 2, 3]
```

#### 3. `unshift()` - Add to Beginning

```javascript
let arr = [2, 3, 4];
arr.unshift(1); // Returns new length (4)
console.log(arr); // [1, 2, 3, 4]

arr.unshift(-1, 0); // Add multiple elements
console.log(arr); // [-1, 0, 1, 2, 3, 4]
```

#### 4. `shift()` - Remove from Beginning

```javascript
let arr = [1, 2, 3, 4];
let firstElement = arr.shift(); // Returns removed element
console.log(firstElement); // 1
console.log(arr); // [2, 3, 4]
```

#### 5. `splice()` - Add/Remove at Any Position

```javascript
// Syntax: array.splice(start, deleteCount, item1, item2, ...)
let arr = [1, 2, 3, 4, 5];

// Remove elements
let removed = arr.splice(1, 2); // Remove 2 elements starting from index 1
console.log(removed); // [2, 3]
console.log(arr); // [1, 4, 5]

// Add elements
arr.splice(1, 0, "a", "b"); // Add 'a' and 'b' at index 1
console.log(arr); // [1, 'a', 'b', 4, 5]

// Replace elements
arr.splice(1, 2, "x", "y", "z"); // Replace 2 elements with 3 new ones
console.log(arr); // [1, 'x', 'y', 'z', 4, 5]
```

#### 6. `reverse()` - Reverse Array

```javascript
let arr = [1, 2, 3, 4];
arr.reverse();
console.log(arr); // [4, 3, 2, 1]
```

#### 7. `sort()` - Sort Array

```javascript
let arr = [3, 1, 4, 1, 5];

// Default sort (lexicographical)
arr.sort();
console.log(arr); // [1, 1, 3, 4, 5]

// Numeric sort
let numbers = [10, 2, 30, 4];
numbers.sort((a, b) => a - b); // Ascending
console.log(numbers); // [2, 4, 10, 30]

numbers.sort((a, b) => b - a); // Descending
console.log(numbers); // [30, 10, 4, 2]
```

### Non-Modifier Methods (Don't Change Original Array)

#### 1. `slice()` - Extract Portion

```javascript
let arr = [1, 2, 3, 4, 5];
let sliced = arr.slice(1, 4); // From index 1 to 3 (4 is excluded)
console.log(sliced); // [2, 3, 4]
console.log(arr); // [1, 2, 3, 4, 5] (unchanged)

let lastTwo = arr.slice(-2); // Last 2 elements
console.log(lastTwo); // [4, 5]
```

## Iterator Methods

### 1. `forEach()` - Execute Function for Each Element

```javascript
let numbers = [1, 2, 3, 4];

numbers.forEach((value, index, array) => {
  console.log(`Index ${index}: ${value}`);
});
// Index 0: 1
// Index 1: 2
// Index 2: 3
// Index 3: 4
```

### 2. `map()` - Transform Each Element

```javascript
let numbers = [1, 2, 3, 4];
let doubled = numbers.map((num) => num * 2);
console.log(doubled); // [2, 4, 6, 8]
console.log(numbers); // [1, 2, 3, 4] (unchanged)

// Always returns an array of the same length
let mixed = numbers.map((num) => (num > 2 ? num : "small"));
console.log(mixed); // ['small', 'small', 3, 4]
```

### 3. `filter()` - Filter Elements by Condition

```javascript
let numbers = [1, 2, 3, 4, 5, 6];
let evens = numbers.filter((num) => num % 2 === 0);
console.log(evens); // [2, 4, 6]

let ages = [12, 18, 25, 16, 30];
let adults = ages.filter((age) => age >= 18);
console.log(adults); // [18, 25, 30]
```

### 4. `reduce()` - Reduce to Single Value

```javascript
let numbers = [1, 2, 3, 4, 5];

// Sum all numbers
let sum = numbers.reduce((accumulator, current) => {
  return accumulator + current;
}, 0); // 0 is initial value
console.log(sum); // 15

// Without initial value (starts with first element)
let sum2 = numbers.reduce((acc, curr) => acc + curr);
console.log(sum2); // 15

// Find maximum
let max = numbers.reduce((acc, curr) => (curr > acc ? curr : acc));
console.log(max); // 5

// Complex example: count occurrences
let fruits = ["apple", "banana", "apple", "orange", "banana"];
let count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});
console.log(count); // {apple: 2, banana: 2, orange: 1}
```

### 5. `find()` - Find First Matching Element

```javascript
let numbers = [1, 2, 3, 4, 5];
let found = numbers.find((num) => num > 3);
console.log(found); // 4 (first element that satisfies condition)

let notFound = numbers.find((num) => num > 10);
console.log(notFound); // undefined
```

### 6. `some()` - Test if Any Element Matches

```javascript
let numbers = [1, 2, 3, 4, 5];
let hasEven = numbers.some((num) => num % 2 === 0);
console.log(hasEven); // true (at least one even number exists)

let hasLarge = numbers.some((num) => num > 10);
console.log(hasLarge); // false
```

### 7. `every()` - Test if All Elements Match

```javascript
let numbers = [2, 4, 6, 8];
let allEven = numbers.every((num) => num % 2 === 0);
console.log(allEven); // true (all numbers are even)

let allSmall = numbers.every((num) => num < 10);
console.log(allSmall); // true
```

## Array Destructuring

Extract values from arrays into variables:

```javascript
let arr = [1, 2, 3, 4, 5];

// Basic destructuring
let [a, b, c] = arr;
console.log(a, b, c); // 1 2 3

// Skip elements
let [first, , third] = arr;
console.log(first, third); // 1 3

// Default values
let [x, y, z = 10] = [1, 2];
console.log(x, y, z); // 1 2 10

// Specific positions
let { [0]: firstEl, [3]: fourthEl } = arr;
console.log(firstEl, fourthEl); // 1 4

// Swapping variables
let [p, q] = [5, 10];
[p, q] = [q, p];
console.log(p, q); // 10 5
```

## Rest and Spread with Arrays

### Rest Pattern - Collect Remaining Elements

```javascript
let arr = [1, 2, 3, 4, 5];
let [first, second, ...rest] = arr;
console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3, 4, 5]
```

### Spread Operator - Expand Array Elements

```javascript
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

// Copy array
let arrCopy = [...arr1]; // Creates new array, not reference
console.log(arrCopy); // [1, 2, 3]

// Combine arrays
let combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Add elements
let extended = [...arr1, 99, ...arr2, 100];
console.log(extended); // [1, 2, 3, 99, 4, 5, 6, 100]
```

## Array Copying

### Shallow Copy vs Reference

```javascript
// Reference (not a copy)
let original = [1, 2, 3];
let reference = original;
reference.push(4);
console.log(original); // [1, 2, 3, 4] - original is changed!

// Shallow copy methods
let arr = [1, 2, 3];

// Method 1: Spread operator
let copy1 = [...arr];

// Method 2: Array.from()
let copy2 = Array.from(arr);

// Method 3: slice()
let copy3 = arr.slice();

copy1.push(4);
console.log(arr); // [1, 2, 3] - unchanged
console.log(copy1); // [1, 2, 3, 4]
```

### Deep Copy for Nested Arrays

```javascript
let nested = [
  [1, 2],
  [3, 4],
];

// Shallow copy - inner arrays are still references
let shallowCopy = [...nested];
shallowCopy[0].push(3);
console.log(nested); // [[1, 2, 3], [3, 4]] - original changed!

// Deep copy using JSON (limitations: no functions, undefined, etc.)
let deepCopy = JSON.parse(JSON.stringify(nested));
deepCopy[0].push(5);
console.log(nested); // [[1, 2, 3], [3, 4]] - unchanged
console.log(deepCopy); // [[1, 2, 3, 5], [3, 4]]
```

## Common Array Patterns

### Checking if Value is Array

```javascript
let arr = [1, 2, 3];
let notArr = "hello";

console.log(Array.isArray(arr)); // true
console.log(Array.isArray(notArr)); // false
```

### Removing Duplicates

```javascript
let arr = [1, 2, 2, 3, 3, 4];
let unique = [...new Set(arr)];
console.log(unique); // [1, 2, 3, 4]
```

### Flattening Arrays

```javascript
let nested = [1, [2, 3], [4, [5, 6]]];

// Flat one level
let flat1 = nested.flat();
console.log(flat1); // [1, 2, 3, 4, [5, 6]]

// Flat all levels
let flatAll = nested.flat(Infinity);
console.log(flatAll); // [1, 2, 3, 4, 5, 6]
```

## Best Practices

1. **Use `const`** for arrays that won't be reassigned (you can still modify contents)
2. **Use `Array.isArray()`** to check if something is an array
3. **Prefer `forEach`, `map`, `filter`, `reduce`** over traditional for loops
4. **Use spread operator** for array copying and combining
5. **Understand the difference** between modifier and non-modifier methods
6. **Use meaningful variable names** in callback functions
7. **Consider performance** - `push`/`pop` are faster than `shift`/`unshift`
8. **Chain array methods** for readable functional programming style
