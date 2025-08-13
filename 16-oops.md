# Object-Oriented Programming in JavaScript

Object-Oriented Programming (OOP) is a programming paradigm that organizes code around **objects** rather than functions. JavaScript supports OOP through multiple approaches: constructor functions, prototypes, and ES6 classes. Understanding these concepts is crucial for building scalable and maintainable applications.

## 📚 Table of Contents

1. [Constructor Functions](#constructor-functions)
2. [Prototypes and Prototype Chain](#prototypes-and-prototype-chain)
3. [ES6 Classes](#es6-classes)
4. [Inheritance](#inheritance)
5. [Prototypal vs Classical Inheritance](#prototypal-vs-classical-inheritance)
6. [Best Practices](#best-practices)

## Constructor Functions

A **constructor function** is a regular function that serves as a blueprint for creating objects. It defines the structure and initial values for objects of a particular type.

### Basic Constructor Function

```javascript
// Constructor function (capitalize first letter by convention)
function PrintingMachine(name, times) {
    this.name = name;
    this.times = times;
    
    // ❌ Avoid: methods defined inside constructor (memory inefficient)
    this.write = function(text = "Empty input") {
        console.log(`${this.name}: ${text}`);
    };
}

// Creating instances using 'new' keyword
const printer1 = new PrintingMachine("John", 3);
const printer2 = new PrintingMachine("Sydney", 2);

console.log(printer1.name); // "John"
console.log(printer2.name); // "Sydney"

printer1.write("Hello World"); // "John: Hello World"
printer2.write(); // "Sydney: Empty input"
```

### What happens with `new` keyword?

When you use `new` with a constructor function:

1. **Creates a new empty object**: `{}`
2. **Sets `this`** to point to the new object
3. **Executes the constructor function** with the provided arguments
4. **Returns the new object** (unless you explicitly return something else)

```javascript
// Without 'new' (incorrect usage)
const wrongPrinter = PrintingMachine("Test", 1); // undefined, 'this' refers to window

// With 'new' (correct usage)
const correctPrinter = new PrintingMachine("Test", 1); // Returns new object
```

## Prototypes and Prototype Chain

**Prototype** is JavaScript's mechanism for inheritance. Every function in JavaScript has a `prototype` property that points to an object. This object becomes the prototype for all instances created by that constructor function.

### Why Use Prototypes?

When methods are defined inside constructor functions, **each instance gets its own copy** of the method, wasting memory:

```javascript
function BadExample(name) {
    this.name = name;
    
    // ❌ Each instance gets its own copy of this function
    this.greet = function() {
        console.log(`Hello, I'm ${this.name}`);
    };
}

const obj1 = new BadExample("Alice");
const obj2 = new BadExample("Bob");

console.log(obj1.greet === obj2.greet); // false (different function objects)
```

**Solution: Use prototype methods**

```javascript
function PrintingMachine(name, times) {
    this.name = name;
    this.times = times;
}

// ✅ Define methods on prototype (shared across all instances)
PrintingMachine.prototype.write = function(text = "Empty input") {
    console.log(`${this.name}: ${text}`);
};

PrintingMachine.prototype.getInfo = function() {
    return `Printer: ${this.name}, Times: ${this.times}`;
};

const printer1 = new PrintingMachine("Office", 5);
const printer2 = new PrintingMachine("Home", 2);

console.log(printer1.write === printer2.write); // true (same function object)

printer1.write("Document 1"); // "Office: Document 1"
printer2.write("Document 2"); // "Home: Document 2"
```

### Adding Properties to Prototype

```javascript
// Adding shared properties
PrintingMachine.prototype.manufacturer = "TechCorp";
PrintingMachine.prototype.warranty = "2 years";

// Adding shared methods
PrintingMachine.prototype.maintenance = function() {
    console.log(`Performing maintenance on ${this.name}`);
};

// All instances now have access to these
console.log(printer1.manufacturer); // "TechCorp"
printer2.maintenance(); // "Performing maintenance on Home"
```

### Prototype Chain

When you access a property or method on an object, JavaScript follows the **prototype chain**:

```javascript
function Vehicle(type) {
    this.type = type;
}

Vehicle.prototype.start = function() {
    console.log(`${this.type} is starting...`);
};

const car = new Vehicle("Car");

// Property lookup order:
console.log(car.type);    // 1. Found on car object directly
car.start();              // 2. Found on Vehicle.prototype
console.log(car.toString); // 3. Found on Object.prototype (inherited)
// car.nonExistent;       // 4. undefined (not found anywhere)
```

## ES6 Classes

ES6 introduced a more familiar **class syntax** that provides a cleaner way to create constructor functions and handle inheritance.

### Basic Class Syntax

```javascript
class PrintingMachine {
    // Constructor method (called when creating new instances)
    constructor(name, times) {
        this.name = name;
        this.times = times;
    }
    
    // Methods are automatically added to prototype
    write(text = "Empty input") {
        console.log(`${this.name}: ${text}`);
    }
    
    getInfo() {
        return `Printer: ${this.name}, Times: ${this.times}`;
    }
    
    // Static method (called on class, not instances)
    static getManufacturer() {
        return "TechCorp Industries";
    }
}

// Creating instances
const printer1 = new PrintingMachine("Office Pro", 10);
const printer2 = new PrintingMachine("Home Basic", 3);

printer1.write("Important Document"); // "Office Pro: Important Document"
console.log(printer2.getInfo()); // "Printer: Home Basic, Times: 3"

// Static method usage
console.log(PrintingMachine.getManufacturer()); // "TechCorp Industries"
```

### Class vs Constructor Function

```javascript
// Using Constructor Function
function PersonFunction(name, age) {
    this.name = name;
    this.age = age;
}

PersonFunction.prototype.introduce = function() {
    return `Hi, I'm ${this.name}, ${this.age} years old`;
};

// Using ES6 Class (equivalent functionality)
class PersonClass {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    introduce() {
        return `Hi, I'm ${this.name}, ${this.age} years old`;
    }
}

// Both create similar objects
const person1 = new PersonFunction("Alice", 25);
const person2 = new PersonClass("Bob", 30);
```

**Key Advantages of Classes:**
- **Cleaner syntax** and easier to read
- **Methods automatically go to prototype**
- **Better tooling support** (IDEs, linters)
- **Familiar to developers** from other OOP languages

## Inheritance

Inheritance allows objects to **acquire properties and methods** from other objects, promoting code reuse and creating hierarchical relationships.

### Inheritance with Constructor Functions

```javascript
// Parent constructor
function Vehicle(brand, year) {
    this.brand = brand;
    this.year = year;
}

Vehicle.prototype.start = function() {
    console.log(`${this.brand} vehicle is starting...`);
};

Vehicle.prototype.getAge = function() {
    return new Date().getFullYear() - this.year;
};

// Child constructor
function Car(brand, year, doors) {
    // Call parent constructor
    Vehicle.call(this, brand, year);
    this.doors = doors;
}

// Set up inheritance
Car.prototype = Object.create(Vehicle.prototype);
Car.prototype.constructor = Car;

// Add child-specific methods
Car.prototype.honk = function() {
    console.log(`${this.brand} car is honking!`);
};

const myCar = new Car("Toyota", 2020, 4);
myCar.start(); // Inherited method: "Toyota vehicle is starting..."
myCar.honk();  // Own method: "Toyota car is honking!"
console.log(myCar.getAge()); // Inherited method: 5 (assuming current year is 2025)
```

### Inheritance with ES6 Classes (extends)

```javascript
// Parent class
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
    
    login() {
        console.log(`${this.name} logged in`);
    }
    
    getProfile() {
        return `User: ${this.name} (${this.email})`;
    }
}

// Child class extending parent
class Admin extends User {
    constructor(name, email, permissions) {
        // Call parent constructor with super()
        super(name, email);
        this.permissions = permissions;
        this.role = "admin";
    }
    
    // Override parent method
    getProfile() {
        return `Admin: ${this.name} (${this.email}) - Role: ${this.role}`;
    }
    
    // Add new method
    manageUsers() {
        console.log(`${this.name} is managing users with permissions: ${this.permissions.join(', ')}`);
    }
    
    // Access parent method using super
    adminLogin() {
        super.login(); // Call parent's login method
        console.log("Admin privileges activated");
    }
}

const admin = new Admin("Alice", "alice@company.com", ["read", "write", "delete"]);

admin.login(); // Inherited: "Alice logged in"
admin.adminLogin(); // Uses super: "Alice logged in" + "Admin privileges activated"
console.log(admin.getProfile()); // Overridden: "Admin: Alice (alice@company.com) - Role: admin"
admin.manageUsers(); // New method: "Alice is managing users with permissions: read, write, delete"
```

### Multiple Levels of Inheritance

```javascript
class Vehicle {
    constructor(brand) {
        this.brand = brand;
    }
    
    start() {
        console.log(`${this.brand} is starting`);
    }
}

class Car extends Vehicle {
    constructor(brand, doors) {
        super(brand);
        this.doors = doors;
    }
    
    drive() {
        console.log(`Driving the ${this.brand} car`);
    }
}

class ElectricCar extends Car {
    constructor(brand, doors, batteryCapacity) {
        super(brand, doors);
        this.batteryCapacity = batteryCapacity;
    }
    
    charge() {
        console.log(`Charging ${this.brand} with ${this.batteryCapacity}kWh battery`);
    }
    
    // Override parent method
    start() {
        console.log(`${this.brand} electric motor is starting silently`);
    }
}

const tesla = new ElectricCar("Tesla", 4, 75);
tesla.start(); // Overridden: "Tesla electric motor is starting silently"
tesla.drive(); // Inherited from Car: "Driving the Tesla car"
tesla.charge(); // Own method: "Charging Tesla with 75kWh battery"
```

## Prototypal vs Classical Inheritance

### Prototypal Inheritance (JavaScript's Native Approach)

**Objects inherit directly from other objects** through the prototype chain.

```javascript
// Create a base object
const animal = {
    species: "Unknown",
    speak() {
        console.log(`${this.species} makes a sound`);
    },
    eat() {
        console.log(`${this.species} is eating`);
    }
};

// Create objects that inherit from animal
const dog = Object.create(animal);
dog.species = "Dog";
dog.bark = function() {
    console.log("Woof! Woof!");
};

const cat = Object.create(animal);
cat.species = "Cat";
cat.meow = function() {
    console.log("Meow!");
};

// Both inherit from animal
dog.speak(); // "Dog makes a sound"
dog.bark();  // "Woof! Woof!"

cat.speak(); // "Cat makes a sound"
cat.meow();  // "Meow!"

// Prototype chain: dog → animal → Object.prototype → null
console.log(Object.getPrototypeOf(dog) === animal); // true
```

### Classical Inheritance (Class-based)

**Classes serve as blueprints** for creating objects, with inheritance through class extension.

```javascript
// Base class (blueprint)
class Animal {
    constructor(species) {
        this.species = species;
    }
    
    speak() {
        console.log(`${this.species} makes a sound`);
    }
    
    eat() {
        console.log(`${this.species} is eating`);
    }
}

// Derived classes
class Dog extends Animal {
    constructor() {
        super("Dog");
    }
    
    bark() {
        console.log("Woof! Woof!");
    }
}

class Cat extends Animal {
    constructor() {
        super("Cat");
    }
    
    meow() {
        console.log("Meow!");
    }
}

// Creating instances
const dog = new Dog();
const cat = new Cat();

dog.speak(); // "Dog makes a sound"
dog.bark();  // "Woof! Woof!"

cat.speak(); // "Cat makes a sound"
cat.meow();  // "Meow!"
```

### Comparison

| Aspect | Prototypal Inheritance | Classical Inheritance |
|--------|----------------------|---------------------|
| **Approach** | Objects inherit from objects | Classes serve as blueprints |
| **Flexibility** | More flexible, dynamic | More structured |
| **Syntax** | `Object.create()`, prototypes | `class`, `extends` keywords |
| **Performance** | Slightly faster | Slightly slower (class overhead) |
| **Familiarity** | JavaScript-specific | Familiar to OOP developers |

## Best Practices

### 1. Use ES6 Classes for New Code

```javascript
// ✅ Preferred: ES6 Class
class User {
    constructor(name) {
        this.name = name;
    }
    
    greet() {
        return `Hello, ${this.name}`;
    }
}

// ❌ Avoid: Constructor function (unless maintaining legacy code)
function User(name) {
    this.name = name;
}
User.prototype.greet = function() {
    return `Hello, ${this.name}`;
};
```

### 2. Capitalize Constructor Functions and Classes

```javascript
// ✅ Correct: Capitalized
class UserAccount { }
function UserAccount() { }

// ❌ Incorrect: Not capitalized
class userAccount { }
function userAccount() { }
```

### 3. Use Private Fields (ES2022)

```javascript
class BankAccount {
    #balance = 0; // Private field
    
    constructor(initialBalance) {
        this.#balance = initialBalance;
    }
    
    deposit(amount) {
        this.#balance += amount;
    }
    
    getBalance() {
        return this.#balance; // Controlled access
    }
}

const account = new BankAccount(100);
account.deposit(50);
console.log(account.getBalance()); // 150
// console.log(account.#balance); // SyntaxError: Private field '#balance'
```

### 4. Prefer Composition over Inheritance

```javascript
// ✅ Good: Composition (has-a relationship)
class Engine {
    start() {
        console.log("Engine started");
    }
}

class Car {
    constructor() {
        this.engine = new Engine(); // Car has an engine
    }
    
    start() {
        this.engine.start();
        console.log("Car is ready to drive");
    }
}

// ❌ Questionable: Deep inheritance hierarchy
class Vehicle extends Machine extends Object // Too many levels
```

### 5. Override Methods Thoughtfully

```javascript
class Animal {
    speak() {
        console.log("Animal makes a sound");
    }
}

class Dog extends Animal {
    speak() {
        // Call parent method if needed
        super.speak();
        console.log("Dog barks");
    }
}
```

## 🔑 Key Takeaways

1. **Constructor Functions**: Use `new` keyword, capitalize function names
2. **Prototypes**: Share methods across instances to save memory
3. **ES6 Classes**: Modern, clean syntax for OOP in JavaScript
4. **Inheritance**: `extends` for classes, `Object.create()` for prototypal
5. **`super()`**: Call parent constructor and methods in child classes
6. **Two inheritance models**: Prototypal (objects from objects) vs Classical (classes)
7. **Best practices**: Use classes, capitalize names, prefer composition
8. **Memory efficiency**: Methods on prototype vs instance methods

## 🎯 Next Steps

- Learn about **Modules** and how they relate to OOP
- Explore **Design Patterns** (Factory, Observer, etc.)
- Study **SOLID Principles** for better OOP design
- Practice with **Real-world projects** using OOP concepts
