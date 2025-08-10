# JavaScript Objects

Objects are collections of key-value pairs that store structured data. They represent real-world entities and are fundamental to JavaScript programming.

## Object Creation

### 1. Object Literal (Most Common)

```javascript
let obj = {
  name: "John",
  age: 26,
  isMale: true,
  city: "New York",
};
```

### 2. Object Constructor

```javascript
let obj = new Object();
obj.name = "John";
obj.age = 26;
```

### 3. Object.create()

```javascript
let obj = Object.create(null); // Object with no prototype
obj.name = "John";
```

## Object Keys and Values

### Key Types

- Keys are always converted to strings internally
- Can use numbers, booleans, but they become strings
- Can use variables as keys with computed properties

```javascript
let obj = {
    "1": "number key",
    "true": "boolean key",
    "name": "string key"
};

// All of these access the same property:
console.log(obj[1]);        // "number key"
console.log(obj["1"]);      // "number key"
console.log(obj.1);         // ❌ SyntaxError

console.log(obj[true]);     // "boolean key"
console.log(obj["true"]);   // "boolean key"
console.log(obj.true);      // "boolean key"
```

## Accessing Object Properties

### 1. Dot Notation

- For fixed key names (literal keys)
- Only works with valid identifier names

```javascript
let user = {
  name: "John",
  age: 30,
};

console.log(user.name); // "John"
console.log(user.age); // 30
```

### 2. Bracket Notation

- For dynamic or multi-word keys
- When key is stored in a variable

```javascript
let user = {
  name: "John",
  "full name": "John Doe",
  age: 30,
};

// Multi-word keys
console.log(user["full name"]); // "John Doe"

// Dynamic keys
let property = "name";
console.log(user[property]); // "John"

// This won't work with dot notation:
// console.log(user.property); // undefined (looks for literal "property" key)
```

## Computed Properties

Use variables as keys when creating objects:

```javascript
let role = "admin";
let level = "senior";

let user = {
  name: "John",
  [role]: "value", // "admin": "value"
  [level + "Developer"]: true, // "seniorDeveloper": true
};

console.log(user.admin); // "value"
console.log(user.seniorDeveloper); // true
```

## Object Destructuring

Extract properties into variables:

### Basic Destructuring

```javascript
let user = { name: "John", age: 25, city: "NYC" };

let { name, age } = user;
console.log(name); // "John"
console.log(age); // 25
```

### Custom Variable Names

```javascript
let user = { name: "Navneet", age: 21 };
let { name: userName, age: userAge } = user;
console.log(userName); // "Navneet"
console.log(userAge); // 21
```

### Default Values

```javascript
let user = { name: "John" };
let { name, age = 25, city = "Unknown" } = user;
console.log(name); // "John"
console.log(age); // 25 (default)
console.log(city); // "Unknown" (default)
```

### Nested Destructuring

```javascript
let user = {
  name: "Amit",
  address: {
    city: "Delhi",
    pincode: 110001,
    location: {
      lat: 28.6139,
      lng: 77.209,
    },
  },
};

// Access nested properties
console.log(user.address.city); // "Delhi"
console.log(user.address.location.lat); // 28.6139

// Destructure nested objects
let {
  address: { city, pincode },
} = user;
console.log(city); // "Delhi"
console.log(pincode); // 110001

let {
  address: {
    location: { lat, lng },
  },
} = user;
console.log(lat, lng); // 28.6139 77.2090
```

## Object Iteration

### 1. `for...in` Loop

```javascript
let user = { name: "John", age: 30, city: "NYC" };

for (let key in user) {
  console.log(`${key}: ${user[key]}`);
  // Note: Use bracket notation because key is a variable
}
// name: John
// age: 30
// city: NYC
```

### 2. Object Static Methods

```javascript
let user = { name: "John", age: 30, city: "NYC" };

// Get all keys
let keys = Object.keys(user);
console.log(keys); // ["name", "age", "city"]

// Get all values
let values = Object.values(user);
console.log(values); // ["John", 30, "NYC"]

// Get key-value pairs
let entries = Object.entries(user);
console.log(entries); // [["name", "John"], ["age", 30], ["city", "NYC"]]

// Iterate over entries
Object.entries(user).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});
```

## Object Copying

### Shallow Copying

#### 1. Spread Operator

```javascript
let original = { name: "John", age: 30 };
let copy = { ...original };

copy.name = "Jane";
console.log(original.name); // "John" (unchanged)
console.log(copy.name); // "Jane"
```

#### 2. Object.assign()

```javascript
let original = { name: "John", age: 30 };
let copy = Object.assign({}, original);

// Can also add new properties
let copyWithExtras = Object.assign({ role: "admin" }, original);
console.log(copyWithExtras); // { role: "admin", name: "John", age: 30 }
```

### Problem with Shallow Copying

```javascript
let user = {
  name: "John",
  address: {
    city: "NYC",
    zipcode: "10001",
  },
};

let shallowCopy = { ...user };
shallowCopy.address.city = "LA";

console.log(user.address.city); // "LA" (original changed!)
// The nested object is still a reference
```

### Deep Copying

```javascript
let user = {
  name: "John",
  address: {
    city: "NYC",
    zipcode: "10001",
  },
};

// Method 1: JSON (limitations: no functions, undefined, dates become strings)
let deepCopy = JSON.parse(JSON.stringify(user));
deepCopy.address.city = "LA";
console.log(user.address.city); // "NYC" (unchanged)

// Method 2: structuredClone (modern browsers)
let deepCopy2 = structuredClone(user);
```

## Optional Chaining

Safely access nested properties without errors:

```javascript
let user = {
  name: "John",
  address: {
    city: "NYC",
  },
};

// Without optional chaining (throws error if address doesn't exist)
// console.log(user.address.street.name); // ❌ Error if street is undefined

// With optional chaining
console.log(user?.address?.street?.name); // undefined (no error)
console.log(user?.address?.city); // "NYC"
console.log(user?.phone?.number); // undefined

// Works with methods too
user?.someMethod?.(); // Only calls if someMethod exists
```

## Object Methods and `this`

```javascript
let calculator = {
  a: 10,
  b: 5,

  add: function () {
    return this.a + this.b;
  },

  // Arrow functions don't have their own 'this'
  multiply: () => {
    return this.a * this.b; // 'this' refers to global object
  },

  // Method shorthand (ES6)
  subtract() {
    return this.a - this.b;
  },
};

console.log(calculator.add()); // 15
console.log(calculator.subtract()); // 5
console.log(calculator.multiply()); // NaN (this is not calculator)
```

## Object Property Descriptors

Control how properties behave:

```javascript
let user = {};

Object.defineProperty(user, "name", {
  value: "John",
  writable: false, // Cannot be changed
  enumerable: true, // Shows in for...in
  configurable: false, // Cannot be deleted or reconfigured
});

user.name = "Jane"; // Silently fails (or throws in strict mode)
console.log(user.name); // "John"

// Get property descriptor
let descriptor = Object.getOwnPropertyDescriptor(user, "name");
console.log(descriptor);
// { value: "John", writable: false, enumerable: true, configurable: false }
```

## Preventing Object Modifications

### Object.freeze()

```javascript
let user = { name: "John", age: 30 };
Object.freeze(user);

user.name = "Jane"; // Silently fails
user.city = "NYC"; // Silently fails
delete user.age; // Silently fails

console.log(user); // { name: "John", age: 30 } (unchanged)
```

### Object.seal()

```javascript
let user = { name: "John", age: 30 };
Object.seal(user);

user.name = "Jane"; // ✅ Works (can modify existing)
user.city = "NYC"; // ❌ Fails (cannot add new)
delete user.age; // ❌ Fails (cannot delete)

console.log(user); // { name: "Jane", age: 30 }
```

### Object.preventExtensions()

```javascript
let user = { name: "John", age: 30 };
Object.preventExtensions(user);

user.name = "Jane"; // ✅ Works
user.city = "NYC"; // ❌ Fails (cannot add new)
delete user.age; // ✅ Works

console.log(user); // { name: "Jane" }
```

## Common Object Patterns

### Factory Function

```javascript
function createUser(name, age) {
  return {
    name: name,
    age: age,
    greet() {
      return `Hello, I'm ${this.name}`;
    },
  };
}

let user1 = createUser("John", 30);
let user2 = createUser("Jane", 25);
```

### Object as a Map

```javascript
let userRoles = {
  john: "admin",
  jane: "user",
  bob: "moderator",
};

// Check if key exists
if ("john" in userRoles) {
  console.log("John has a role");
}

// Better to use Map for this purpose in modern JS
let roleMap = new Map([
  ["john", "admin"],
  ["jane", "user"],
]);
```

## Best Practices

1. **Use object literal syntax** for creating objects
2. **Use destructuring** to extract multiple properties
3. **Use meaningful property names** that describe the data
4. **Use optional chaining** to safely access nested properties
5. **Understand shallow vs deep copying** implications
6. **Use `const`** for objects that won't be reassigned
7. **Prefer dot notation** when possible for better readability
8. **Use Object.freeze()** for truly immutable objects
9. **Consider using Map** for key-value collections with dynamic keys
10. **Use method shorthand** for cleaner object method definitions
