# Timers and Intervals

JavaScript provides powerful timer functions that allow you to execute code after a delay or repeatedly at specified intervals. Understanding these functions is crucial for creating interactive web applications, animations, and handling time-based operations.

## 📚 Table of Contents

1. [setTimeout - Delayed Execution](#settimeout---delayed-execution)
2. [setInterval - Repeated Execution](#setinterval---repeated-execution)
3. [Clearing Timers](#clearing-timers)
4. [Advanced Timer Concepts](#advanced-timer-concepts)
5. [Common Use Cases](#common-use-cases)
6. [Best Practices](#best-practices)
7. [Browser vs Node.js](#browser-vs-nodejs)

## setTimeout - Delayed Execution

The `setTimeout()` function executes code once after a specified delay.

### Basic Syntax

```javascript
setTimeout(function, delay, arg1, arg2, ...argN)
```

- **function**: The function to execute
- **delay**: Time in milliseconds (1000ms = 1 second)
- **arg1, arg2, ...argN**: Optional arguments to pass to the function

### Basic Examples

```javascript
// Simple timeout
setTimeout(function () {
  console.log("This runs after 2 seconds");
}, 2000);

// Using arrow function
setTimeout(() => {
  console.log("Arrow function executed after 1 second");
}, 1000);

// Passing arguments to the function
function greetUser(name, age) {
  console.log(`Hello ${name}, you are ${age} years old!`);
}

setTimeout(greetUser, 1500, "Alice", 25);
// Output after 1.5 seconds: "Hello Alice, you are 25 years old!"
```

### Storing Timer ID

```javascript
// Store the timer ID to potentially cancel it later
const timerId = setTimeout(() => {
  console.log("This might not execute if cancelled");
}, 3000);

console.log("Timer ID:", timerId); // Returns a unique identifier
```

### Real-world Example: Delayed Message

```javascript
function showDelayedMessage(message, delay = 2000) {
  console.log("Message will appear in " + delay / 1000 + " seconds...");

  setTimeout(() => {
    const messageElement = document.getElementById("message");
    if (messageElement) {
      messageElement.textContent = message;
      messageElement.style.display = "block";
    }
  }, delay);
}

// Usage
showDelayedMessage("Welcome to our website!", 1000);
```

## setInterval - Repeated Execution

The `setInterval()` function executes code repeatedly at specified intervals.

### Basic Syntax

```javascript
setInterval(function, delay, arg1, arg2, ...argN)
```

### Basic Examples

```javascript
// Simple interval
setInterval(function () {
  console.log("This runs every 2 seconds");
}, 2000);

// Using arrow function
setInterval(() => {
  console.log("Current time:", new Date().toLocaleTimeString());
}, 1000);

// Passing arguments
function updateCounter(increment) {
  window.counter = (window.counter || 0) + increment;
  console.log("Counter:", window.counter);
}

setInterval(updateCounter, 1000, 5);
// Increments counter by 5 every second
```

### Real-world Example: Digital Clock

```javascript
function createDigitalClock() {
  const clockElement = document.getElementById("clock");

  function updateClock() {
    const now = new Date();
    const timeString = now.toLocaleTimeString();

    if (clockElement) {
      clockElement.textContent = timeString;
    }
    console.log("Current time:", timeString);
  }

  // Update immediately
  updateClock();

  // Then update every second
  const intervalId = setInterval(updateClock, 1000);

  return intervalId; // Return ID so it can be cleared later
}

// Usage
const clockIntervalId = createDigitalClock();
```

### Auto-incrementing Counter

```javascript
let counter = 0;

const counterId = setInterval(() => {
  counter++;
  console.log(`Count: ${counter}`);

  // Stop after reaching 10
  if (counter >= 10) {
    clearInterval(counterId);
    console.log("Counter stopped!");
  }
}, 500);
```

## Clearing Timers

Both `setTimeout` and `setInterval` return a unique identifier that can be used to cancel them.

### Clearing setTimeout

```javascript
// Set a timeout
const timeoutId = setTimeout(() => {
  console.log("This will not execute if cleared");
}, 5000);

// Clear the timeout before it executes
clearTimeout(timeoutId);
console.log("Timeout cleared!");
```

### Clearing setInterval

```javascript
// Set an interval
const intervalId = setInterval(() => {
  console.log("Repeating message");
}, 1000);

// Clear the interval after 5 seconds
setTimeout(() => {
  clearInterval(intervalId);
  console.log("Interval cleared!");
}, 5000);
```

### Practical Example: Start/Stop Timer

```javascript
class Timer {
  constructor() {
    this.intervalId = null;
    this.seconds = 0;
  }

  start() {
    if (this.intervalId) {
      console.log("Timer is already running!");
      return;
    }

    this.intervalId = setInterval(() => {
      this.seconds++;
      console.log(`Timer: ${this.seconds} seconds`);
    }, 1000);

    console.log("Timer started!");
  }

  stop() {
    if (this.intervalId) {
      clearInterval(this.intervalId);
      this.intervalId = null;
      console.log("Timer stopped!");
    } else {
      console.log("Timer is not running!");
    }
  }

  reset() {
    this.stop();
    this.seconds = 0;
    console.log("Timer reset!");
  }
}

// Usage
const timer = new Timer();
timer.start(); // Start timer
// timer.stop();   // Stop timer
// timer.reset();  // Reset timer
```

## Advanced Timer Concepts

### Nested Timeouts vs setInterval

```javascript
// Using setInterval (may accumulate if execution takes longer than interval)
let intervalId = setInterval(() => {
  // Some potentially slow operation
  console.log("Interval execution");
}, 1000);

// Using nested setTimeout (ensures gap between executions)
function recursiveTimeout() {
  setTimeout(() => {
    // Some potentially slow operation
    console.log("Recursive timeout execution");
    recursiveTimeout(); // Call itself again
  }, 1000);
}

recursiveTimeout();
```

### Dynamic Intervals

```javascript
function createDynamicInterval(initialDelay = 1000) {
  let delay = initialDelay;
  let iteration = 0;

  function scheduleNext() {
    setTimeout(() => {
      iteration++;
      console.log(`Iteration ${iteration} with delay ${delay}ms`);

      // Increase delay each time
      delay += 500;

      // Continue for 5 iterations
      if (iteration < 5) {
        scheduleNext();
      }
    }, delay);
  }

  scheduleNext();
}

createDynamicInterval(500);
```

### Timer with Callback

```javascript
function createTimer(duration, onTick, onComplete) {
  let remaining = duration;

  const intervalId = setInterval(() => {
    remaining--;

    if (onTick) {
      onTick(remaining);
    }

    if (remaining <= 0) {
      clearInterval(intervalId);
      if (onComplete) {
        onComplete();
      }
    }
  }, 1000);

  return intervalId;
}

// Usage: 5-second countdown
createTimer(
  5,
  (remaining) => console.log(`${remaining} seconds remaining`),
  () => console.log("Time's up!")
);
```

## Common Use Cases

### 1. Auto-save Feature

```javascript
class AutoSave {
  constructor(saveFunction, interval = 30000) {
    this.saveFunction = saveFunction;
    this.interval = interval;
    this.intervalId = null;
    this.isDirty = false;
  }

  start() {
    this.intervalId = setInterval(() => {
      if (this.isDirty) {
        this.saveFunction();
        this.isDirty = false;
        console.log("Auto-saved!");
      }
    }, this.interval);
  }

  markDirty() {
    this.isDirty = true;
  }

  stop() {
    if (this.intervalId) {
      clearInterval(this.intervalId);
      this.intervalId = null;
    }
  }
}

// Usage
const autoSave = new AutoSave(() => {
  // Your save logic here
  console.log("Saving data...");
}, 10000); // Save every 10 seconds

autoSave.start();

// Call this when data changes
// autoSave.markDirty();
```

### 2. Slideshow/Carousel

```javascript
class Slideshow {
  constructor(slides, interval = 3000) {
    this.slides = slides;
    this.currentSlide = 0;
    this.interval = interval;
    this.intervalId = null;
  }

  start() {
    this.intervalId = setInterval(() => {
      this.nextSlide();
    }, this.interval);
  }

  stop() {
    if (this.intervalId) {
      clearInterval(this.intervalId);
      this.intervalId = null;
    }
  }

  nextSlide() {
    this.currentSlide = (this.currentSlide + 1) % this.slides.length;
    this.displaySlide();
  }

  displaySlide() {
    console.log(
      `Showing slide ${this.currentSlide + 1}: ${
        this.slides[this.currentSlide]
      }`
    );
    // Update DOM here
  }
}

// Usage
const slideshow = new Slideshow(
  ["Slide 1: Welcome", "Slide 2: Features", "Slide 3: Contact"],
  2000
);

slideshow.start();
```

### 3. Polling for Updates

```javascript
function createPoller(checkFunction, onUpdate, interval = 5000) {
  let lastResult = null;

  const intervalId = setInterval(async () => {
    try {
      const currentResult = await checkFunction();

      if (JSON.stringify(currentResult) !== JSON.stringify(lastResult)) {
        onUpdate(currentResult, lastResult);
        lastResult = currentResult;
      }
    } catch (error) {
      console.error("Polling error:", error);
    }
  }, interval);

  return intervalId;
}

// Usage: Poll for new messages
const pollerId = createPoller(
  async () => {
    // Simulate API call
    const response = await fetch("/api/messages");
    return response.json();
  },
  (newData, oldData) => {
    console.log("New messages detected!", newData);
    // Update UI with new messages
  },
  3000 // Check every 3 seconds
);
```

### 4. Debouncing with setTimeout

```javascript
function debounce(func, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);

    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// Usage: Search input debouncing
const searchInput = document.getElementById("search");
const debouncedSearch = debounce((query) => {
  console.log("Searching for:", query);
  // Perform search
}, 300);

searchInput?.addEventListener("input", (e) => {
  debouncedSearch(e.target.value);
});
```

## Best Practices

### 1. Always Store Timer IDs

```javascript
// Good: Store ID for later cleanup
const timerId = setTimeout(() => {
  console.log("Timer executed");
}, 1000);

// Bad: Can't cancel this timer
setTimeout(() => {
  console.log("Timer executed");
}, 1000);
```

### 2. Clean Up Timers

```javascript
class Component {
  constructor() {
    this.timers = [];
  }

  addTimer(callback, delay, isInterval = false) {
    const timerId = isInterval
      ? setInterval(callback, delay)
      : setTimeout(callback, delay);

    this.timers.push({ id: timerId, isInterval });
    return timerId;
  }

  destroy() {
    this.timers.forEach((timer) => {
      if (timer.isInterval) {
        clearInterval(timer.id);
      } else {
        clearTimeout(timer.id);
      }
    });
    this.timers = [];
  }
}
```

### 3. Handle Edge Cases

```javascript
function safeSetTimeout(callback, delay) {
  // Ensure delay is a positive number
  const safeDelay = Math.max(0, parseInt(delay) || 0);

  // Ensure callback is a function
  if (typeof callback !== "function") {
    console.error("Callback must be a function");
    return null;
  }

  return setTimeout(callback, safeDelay);
}
```

### 4. Use Appropriate Timing

```javascript
// Good: Reasonable intervals
setInterval(() => {
  updateClock();
}, 1000); // 1 second for clock updates

// Avoid: Too frequent intervals (can impact performance)
setInterval(() => {
  heavyOperation();
}, 10); // 10ms might be too frequent
```

## Browser vs Node.js

### Browser Environment

```javascript
// In browsers, timer IDs are numbers
const timerId = setTimeout(() => {
  console.log("Browser timer");
}, 1000);

console.log(typeof timerId); // "number"
console.log(timerId); // e.g., 123
```

### Node.js Environment

```javascript
// In Node.js, timers are objects with methods
const timerId = setTimeout(() => {
  console.log("Node.js timer");
}, 1000);

console.log(typeof timerId); // "object"
console.log(timerId); // Timeout object with internal methods

// Node.js specific methods
timerId.unref(); // Allow process to exit even if timer is pending
timerId.ref(); // Require process to stay alive for timer
```

### Cross-platform Timer Utility

```javascript
function createTimer(callback, delay, isInterval = false) {
  const timerId = isInterval
    ? setInterval(callback, delay)
    : setTimeout(callback, delay);

  return {
    id: timerId,
    clear: () => {
      if (isInterval) {
        clearInterval(timerId);
      } else {
        clearTimeout(timerId);
      }
    },
    // Node.js specific (will be ignored in browsers)
    unref: () => timerId.unref && timerId.unref(),
    ref: () => timerId.ref && timerId.ref(),
  };
}

// Usage
const timer = createTimer(() => console.log("Cross-platform timer"), 1000);
// timer.clear(); // Works in both environments
```

## 🔑 Key Takeaways

1. **setTimeout**: Executes code once after a delay
2. **setInterval**: Executes code repeatedly at intervals
3. **Always store timer IDs**: Needed for clearing timers
4. **Clean up timers**: Prevent memory leaks by clearing unused timers
5. **Use appropriate intervals**: Don't make them too frequent
6. **Consider nested setTimeout**: For guaranteed gaps between executions
7. **Handle edge cases**: Validate inputs and handle errors
8. **Environment differences**: Browser vs Node.js timer implementations

## 🎯 Next Steps

- Learn about **requestAnimationFrame** for smooth animations
- Explore **Web Workers** for background timer operations
- Study **Promise-based timers** and async/await patterns
- Understand **Event Loop** and how timers fit into JavaScript's concurrency model
