# Asycnchronous JS
# Asynchronous JavaScript

## Table of Contents
- [Understanding Synchronous vs Asynchronous](#understanding-synchronous-vs-asynchronous)
- [The Event Loop](#the-event-loop)
- [Blocking vs Non-Blocking Code](#blocking-vs-non-blocking-code)
- [Callbacks](#callbacks)
- [Callback Hell](#callback-hell)
- [Promises](#promises)
- [Async/Await](#asyncawait)
- [XMLHttpRequest (XHR)](#xmlhttprequest-xhr)
- [Fetch API](#fetch-api)
- [Error Handling in Async Code](#error-handling-in-async-code)
- [Advanced Async Patterns](#advanced-async-patterns)
- [Best Practices](#best-practices)
- [Key Takeaways](#key-takeaways)

## Understanding Synchronous vs Asynchronous

### Synchronous JavaScript
JavaScript is **synchronous by default** - code executes line by line, and each operation must complete before the next one starts.

```javascript
console.log("First");
console.log("Second");
console.log("Third");

// Output (always in this order):
// First
// Second  
// Third
```

### Asynchronous JavaScript
**Asynchronous operations** allow certain tasks to start now but finish later, without blocking the main thread.

```javascript
console.log("First");

setTimeout(() => {
  console.log("Second (delayed)");
}, 1000);

console.log("Third");

// Output:
// First
// Third
// Second (delayed) - appears after 1 second
```

### Why Asynchronous Programming?
```javascript
// Problem: Blocking code
function blockingOperation() {
  const start = Date.now();
  while (Date.now() - start < 3000) {
    // Blocks for 3 seconds - page becomes unresponsive
  }
  return "Done!";
}

console.log("Start");
const result = blockingOperation(); // Everything stops here
console.log(result);
console.log("End");
```

```javascript
// Solution: Non-blocking code
console.log("Start");
setTimeout(() => {
  console.log("Done!"); // Executes after 3 seconds
}, 3000);
console.log("End"); // Executes immediately

// Page remains responsive!
```

## The Event Loop

### How JavaScript Handles Async Operations
JavaScript uses the **Event Loop** to manage asynchronous operations:

1. **Call Stack**: Where function calls are executed
2. **Web APIs**: Browser-provided APIs (setTimeout, fetch, DOM events)
3. **Callback Queue**: Where completed async operations wait
4. **Event Loop**: Moves completed operations from queue to stack

```javascript
// Event Loop Example
console.log("1"); // Call stack

setTimeout(() => {
  console.log("2"); // Web API → Callback Queue → Call Stack
}, 0);

Promise.resolve().then(() => {
  console.log("3"); // Microtask Queue (higher priority)
});

console.log("4"); // Call stack

// Output: 1, 4, 3, 2
// Microtasks (Promises) run before macrotasks (setTimeout)
```

### Microtasks vs Macrotasks
```javascript
console.log("Start");

// Macrotask
setTimeout(() => console.log("Timeout 1"), 0);
setTimeout(() => console.log("Timeout 2"), 0);

// Microtasks
Promise.resolve().then(() => console.log("Promise 1"));
Promise.resolve().then(() => console.log("Promise 2"));

console.log("End");

// Output:
// Start
// End
// Promise 1
// Promise 2
// Timeout 1
// Timeout 2
```

## Blocking vs Non-Blocking Code

### Blocking Code
Code that stops (blocks) further execution until the current task completes.

```javascript
// Blocking example (bad)
function processLargeDataset(data) {
  let result = [];
  for (let i = 0; i < 1000000; i++) {
    // CPU-intensive operation
    result.push(data[i] * 2);
  }
  return result; // Nothing else can run during this time
}

const data = new Array(1000000).fill(1);
console.log("Processing...");
const processed = processLargeDataset(data); // Page freezes
console.log("Done!");
```

### Non-Blocking Code
Code that doesn't stop the execution of other operations.

```javascript
// Non-blocking example (good)
function processLargeDatasetAsync(data, callback) {
  let result = [];
  let index = 0;
  
  function processChunk() {
    let count = 0;
    while (count < 1000 && index < data.length) {
      result.push(data[index] * 2);
      index++;
      count++;
    }
    
    if (index < data.length) {
      setTimeout(processChunk, 0); // Give other code a chance to run
    } else {
      callback(result);
    }
  }
  
  processChunk();
}

const data = new Array(1000000).fill(1);
console.log("Processing...");
processLargeDatasetAsync(data, (result) => {
  console.log("Done!", result.length);
});
console.log("This runs immediately!"); // Doesn't wait
```

## Callbacks

### What are Callbacks?
A **callback** is a function passed as an argument to another function, to be executed later.

### Synchronous Callbacks
```javascript
function greet(name, callback) {
  console.log(`Hello ${name}`);
  callback();
}

function sayGoodbye() {
  console.log("Goodbye!");
}

greet("John", sayGoodbye);
// Output:
// Hello John
// Goodbye!
```

### Asynchronous Callbacks
```javascript
function fetchData(callback) {
  console.log("Fetching data...");
  
  setTimeout(() => {
    const data = { id: 1, name: "John Doe" };
    callback(data);
  }, 2000);
}

function displayData(data) {
  console.log("Data received:", data);
}

fetchData(displayData);
console.log("This runs immediately");

// Output:
// Fetching data...
// This runs immediately
// Data received: { id: 1, name: "John Doe" } (after 2 seconds)
```

### Error-First Callbacks
A common Node.js pattern where the first parameter is reserved for errors:

```javascript
function readFileAsync(filename, callback) {
  setTimeout(() => {
    if (filename === "nonexistent.txt") {
      callback(new Error("File not found"), null);
    } else {
      callback(null, "File content here");
    }
  }, 1000);
}

readFileAsync("data.txt", (err, data) => {
  if (err) {
    console.error("Error:", err.message);
    return;
  }
  console.log("File data:", data);
});
```

## Callback Hell

### The Problem
**Callback Hell** occurs when you have multiple nested callbacks, making code hard to read and maintain.

```javascript
// Callback Hell Example
function getUserData(userId, callback) {
  setTimeout(() => {
    console.log("Getting user data...");
    callback({ id: userId, name: "John" });
  }, 1000);
}

function getUserPosts(userId, callback) {
  setTimeout(() => {
    console.log("Getting user posts...");
    callback([{ id: 1, title: "Post 1" }, { id: 2, title: "Post 2" }]);
  }, 1000);
}

function getPostComments(postId, callback) {
  setTimeout(() => {
    console.log("Getting post comments...");
    callback([{ id: 1, text: "Great post!" }]);
  }, 1000);
}

// Nested callbacks (Pyramid of Doom)
getUserData(1, (user) => {
  console.log("User:", user);
  getUserPosts(user.id, (posts) => {
    console.log("Posts:", posts);
    getPostComments(posts[0].id, (comments) => {
      console.log("Comments:", comments);
      // Imagine more nesting... 😱
    });
  });
});
```

### Solutions to Callback Hell

#### 1. Named Functions
```javascript
function handleComments(comments) {
  console.log("Comments:", comments);
}

function handlePosts(posts) {
  console.log("Posts:", posts);
  getPostComments(posts[0].id, handleComments);
}

function handleUser(user) {
  console.log("User:", user);
  getUserPosts(user.id, handlePosts);
}

getUserData(1, handleUser);
```

#### 2. Modularization
```javascript
const userService = {
  async getUser(id) {
    return new Promise(resolve => {
      setTimeout(() => resolve({ id, name: "John" }), 1000);
    });
  },
  
  async getPosts(userId) {
    return new Promise(resolve => {
      setTimeout(() => resolve([{ id: 1, title: "Post 1" }]), 1000);
    });
  },
  
  async getComments(postId) {
    return new Promise(resolve => {
      setTimeout(() => resolve([{ id: 1, text: "Great!" }]), 1000);
    });
  }
};
```

## Promises

### What are Promises?
A **Promise** is an object representing the eventual completion or failure of an asynchronous operation.

### Promise States
1. **Pending**: Initial state, neither fulfilled nor rejected
2. **Fulfilled**: Operation completed successfully
3. **Rejected**: Operation failed

```javascript
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  const success = Math.random() > 0.5;
  
  setTimeout(() => {
    if (success) {
      resolve("Operation successful!");
    } else {
      reject(new Error("Operation failed!"));
    }
  }, 2000);
});

// Using the Promise
myPromise
  .then(result => {
    console.log("Success:", result);
  })
  .catch(error => {
    console.error("Error:", error.message);
  });
```

### Promise Methods

#### Promise.resolve() and Promise.reject()
```javascript
// Immediately resolved promise
Promise.resolve("Success!")
  .then(value => console.log(value)); // "Success!"

// Immediately rejected promise
Promise.reject(new Error("Failed!"))
  .catch(error => console.error(error.message)); // "Failed!"
```

#### Promise Chaining
```javascript
function fetchUser(id) {
  return new Promise(resolve => {
    setTimeout(() => resolve({ id, name: "John" }), 1000);
  });
}

function fetchPosts(userId) {
  return new Promise(resolve => {
    setTimeout(() => resolve([{ id: 1, title: "Post 1" }]), 1000);
  });
}

function fetchComments(postId) {
  return new Promise(resolve => {
    setTimeout(() => resolve([{ id: 1, text: "Great!" }]), 1000);
  });
}

// Promise chaining (solving callback hell)
fetchUser(1)
  .then(user => {
    console.log("User:", user);
    return fetchPosts(user.id);
  })
  .then(posts => {
    console.log("Posts:", posts);
    return fetchComments(posts[0].id);
  })
  .then(comments => {
    console.log("Comments:", comments);
  })
  .catch(error => {
    console.error("Error:", error);
  });
```

#### Promise.all()
Runs multiple promises concurrently and waits for all to complete:

```javascript
const promise1 = fetch('/api/users');
const promise2 = fetch('/api/posts');
const promise3 = fetch('/api/comments');

Promise.all([promise1, promise2, promise3])
  .then(responses => {
    console.log("All requests completed");
    return Promise.all(responses.map(r => r.json()));
  })
  .then(data => {
    const [users, posts, comments] = data;
    console.log({ users, posts, comments });
  })
  .catch(error => {
    console.error("One or more requests failed:", error);
  });
```

#### Promise.race()
Returns the first promise to settle (either resolve or reject):

```javascript
const fastAPI = new Promise(resolve => 
  setTimeout(() => resolve("Fast API"), 1000)
);

const slowAPI = new Promise(resolve => 
  setTimeout(() => resolve("Slow API"), 3000)
);

Promise.race([fastAPI, slowAPI])
  .then(result => console.log(result)); // "Fast API"
```

#### Promise.allSettled()
Waits for all promises to settle, regardless of outcome:

```javascript
const promises = [
  Promise.resolve("Success 1"),
  Promise.reject("Error 1"),
  Promise.resolve("Success 2")
];

Promise.allSettled(promises)
  .then(results => {
    results.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        console.log(`Promise ${index + 1} succeeded:`, result.value);
      } else {
        console.log(`Promise ${index + 1} failed:`, result.reason);
      }
    });
  });
```

## Async/Await

### What is Async/Await?
**Async/await** provides a cleaner, more readable way to work with promises by making asynchronous code look synchronous.

### Basic Syntax
```javascript
// Function declaration
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Arrow function
const fetchDataArrow = async () => {
  const response = await fetch('/api/data');
  return await response.json();
};

// Using the async function
fetchData()
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Converting Promises to Async/Await
```javascript
// Promise version
function getUserDataPromise(id) {
  return fetchUser(id)
    .then(user => {
      console.log("User:", user);
      return fetchPosts(user.id);
    })
    .then(posts => {
      console.log("Posts:", posts);
      return fetchComments(posts[0].id);
    })
    .then(comments => {
      console.log("Comments:", comments);
      return comments;
    });
}

// Async/await version
async function getUserDataAsync(id) {
  try {
    const user = await fetchUser(id);
    console.log("User:", user);
    
    const posts = await fetchPosts(user.id);
    console.log("Posts:", posts);
    
    const comments = await fetchComments(posts[0].id);
    console.log("Comments:", comments);
    
    return comments;
  } catch (error) {
    console.error("Error:", error);
    throw error;
  }
}
```

### Parallel Execution with Async/Await
```javascript
// Sequential (slower)
async function fetchDataSequential() {
  const user = await fetchUser(1);      // Wait 1 second
  const posts = await fetchPosts(1);    // Wait another 1 second
  const comments = await fetchComments(1); // Wait another 1 second
  // Total: ~3 seconds
  return { user, posts, comments };
}

// Parallel (faster)
async function fetchDataParallel() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(1),
    fetchPosts(1),
    fetchComments(1)
  ]);
  // Total: ~1 second (all run concurrently)
  return { user, posts, comments };
}
```

### Error Handling in Async/Await
```javascript
async function robustAsyncFunction() {
  try {
    const result1 = await riskyOperation1();
    const result2 = await riskyOperation2(result1);
    return result2;
  } catch (error) {
    if (error.name === 'NetworkError') {
      console.log('Network issue, retrying...');
      return await retryOperation();
    } else {
      console.error('Unexpected error:', error);
      throw error;
    }
  } finally {
    console.log('Cleanup operations');
  }
}
```

## XMLHttpRequest (XHR)

### What is XMLHttpRequest?
**XMLHttpRequest** is the older way to make HTTP requests in browsers before fetch() was introduced.

### Basic XHR Usage
```javascript
function makeXHRRequest(url, method = 'GET', data = null) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    
    // Configure the request
    xhr.open(method, url, true);
    xhr.setRequestHeader('Content-Type', 'application/json');
    
    // Handle the response
    xhr.onload = function() {
      if (xhr.status >= 200 && xhr.status < 300) {
        try {
          const response = JSON.parse(xhr.responseText);
          resolve(response);
        } catch (error) {
          resolve(xhr.responseText);
        }
      } else {
        reject(new Error(`HTTP ${xhr.status}: ${xhr.statusText}`));
      }
    };
    
    // Handle network errors
    xhr.onerror = function() {
      reject(new Error('Network error'));
    };
    
    // Handle timeout
    xhr.timeout = 10000;
    xhr.ontimeout = function() {
      reject(new Error('Request timeout'));
    };
    
    // Send the request
    xhr.send(data ? JSON.stringify(data) : null);
  });
}

// Usage examples
makeXHRRequest('https://jsonplaceholder.typicode.com/users')
  .then(users => console.log('Users:', users))
  .catch(error => console.error('Error:', error));
```

### XHR with Different Patterns

#### XHR + Callbacks
```javascript
function getUsersXHR_cb(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url, true);
  xhr.responseType = "json";
  
  xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) {
      callback(null, xhr.response);
    } else {
      callback(new Error(`HTTP ${xhr.status}`));
    }
  };
  
  xhr.onerror = () => callback(new Error("Network error"));
  xhr.send();
}

// Usage
getUsersXHR_cb("https://jsonplaceholder.typicode.com/users", (err, data) => {
  if (err) return console.error(err);
  console.log("XHR+Callback:", data);
});
```

#### XHR + Promises
```javascript
function getJSON_XHR(url, options = {}) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open(options.method || "GET", url, true);
    xhr.responseType = "json";
    
    if (options.headers) {
      for (const [key, value] of Object.entries(options.headers)) {
        xhr.setRequestHeader(key, value);
      }
    }
    
    xhr.onload = () => (xhr.status >= 200 && xhr.status < 300)
      ? resolve(xhr.response)
      : reject(new Error(`HTTP ${xhr.status}`));
      
    xhr.onerror = () => reject(new Error("Network error"));
    xhr.send(options.body || null);
  });
}

// Usage
getJSON_XHR("https://jsonplaceholder.typicode.com/users")
  .then(data => console.log("XHR+Promise:", data))
  .catch(console.error);
```

#### XHR + Async/Await
```javascript
(async () => {
  try {
    const users = await getJSON_XHR("https://jsonplaceholder.typicode.com/users");
    console.log("XHR+Await:", users);
  } catch (error) {
    console.error(error);
  }
})();
```

## Fetch API

### What is Fetch?
**Fetch API** is the modern, promise-based way to make HTTP requests in browsers.

### Basic Fetch Usage
```javascript
// Simple GET request
fetch('https://jsonplaceholder.typicode.com/users')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

### Fetch with Different HTTP Methods
```javascript
// GET request
const getUsers = async () => {
  const response = await fetch('/api/users');
  return await response.json();
};

// POST request
const createUser = async (userData) => {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(userData)
  });
  return await response.json();
};

// PUT request
const updateUser = async (id, userData) => {
  const response = await fetch(`/api/users/${id}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(userData)
  });
  return await response.json();
};

// DELETE request
const deleteUser = async (id) => {
  const response = await fetch(`/api/users/${id}`, {
    method: 'DELETE'
  });
  return response.ok;
};
```

### Advanced Fetch Features
```javascript
// Fetch with timeout
function fetchWithTimeout(url, options = {}, timeout = 5000) {
  return Promise.race([
    fetch(url, options),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Timeout')), timeout)
    )
  ]);
}

// Fetch with retry logic
async function fetchWithRetry(url, options = {}, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url, options);
      if (response.ok) return response;
      throw new Error(`HTTP ${response.status}`);
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}

// Usage
fetchWithTimeout('https://api.example.com/data', {}, 3000)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Request failed or timed out:', error));
```

### Fetch vs XHR Comparison
```javascript
// Modern fetch approach
async function getJSON(url, init) {
  const response = await fetch(url, init);
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}

// Usage
(async () => {
  try {
    const users = await getJSON("https://jsonplaceholder.typicode.com/users");
    console.log("Fetch+Await:", users);
  } catch (error) {
    console.error(error);
  }
})();
```

### Converting Fetch to Callback Pattern
```javascript
function fetch_cb(url, callback) {
  fetch(url)
    .then(response => {
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return response.json();
    })
    .then(data => callback(null, data))
    .catch(error => callback(error));
}

// Usage
fetch_cb("https://jsonplaceholder.typicode.com/users", (err, data) => {
  if (err) return console.error(err);
  console.log("Fetch+Callback:", data);
});
```

## Error Handling in Async Code

### Try-Catch with Async/Await
```javascript
async function handleAsyncErrors() {
  try {
    const user = await fetchUser(1);
    const posts = await fetchUserPosts(user.id);
    return posts;
  } catch (error) {
    if (error.name === 'NetworkError') {
      console.log('Network issue, showing cached data');
      return getCachedPosts();
    } else if (error.status === 404) {
      console.log('User not found');
      return [];
    } else {
      console.error('Unexpected error:', error);
      throw error; // Re-throw if we can't handle it
    }
  }
}
```

### Promise Error Handling
```javascript
fetchUser(1)
  .then(user => fetchUserPosts(user.id))
  .then(posts => {
    console.log('Posts:', posts);
    return posts;
  })
  .catch(error => {
    if (error.status === 404) {
      console.log('User not found, creating new user');
      return createUser({ name: 'New User' });
    }
    throw error; // Re-throw unhandled errors
  })
  .catch(error => {
    console.error('Final error handler:', error);
  });
```

### Global Error Handling
```javascript
// Catch unhandled promise rejections
window.addEventListener('unhandledrejection', event => {
  console.error('Unhandled promise rejection:', event.reason);
  event.preventDefault(); // Prevent browser console error
});

// Catch regular errors
window.addEventListener('error', event => {
  console.error('Script error:', event.error);
});
```

## Advanced Async Patterns

### Promise Pool (Controlling Concurrency)
```javascript
class PromisePool {
  constructor(concurrency = 3) {
    this.concurrency = concurrency;
    this.running = [];
    this.queue = [];
  }

  async add(promiseFunction) {
    return new Promise((resolve, reject) => {
      this.queue.push({
        promiseFunction,
        resolve,
        reject
      });
      this.process();
    });
  }

  async process() {
    if (this.running.length >= this.concurrency || this.queue.length === 0) {
      return;
    }

    const { promiseFunction, resolve, reject } = this.queue.shift();
    const promise = promiseFunction()
      .then(resolve)
      .catch(reject)
      .finally(() => {
        this.running.splice(this.running.indexOf(promise), 1);
        this.process();
      });

    this.running.push(promise);
  }
}

// Usage
const pool = new PromisePool(2); // Max 2 concurrent requests

const urls = [
  'https://api.example1.com/data',
  'https://api.example2.com/data',
  'https://api.example3.com/data',
  'https://api.example4.com/data'
];

Promise.all(
  urls.map(url => pool.add(() => fetch(url).then(r => r.json())))
).then(results => {
  console.log('All results:', results);
});
```

### Async Iterator Pattern
```javascript
class AsyncDataProcessor {
  constructor(dataSource) {
    this.dataSource = dataSource;
  }

  async* processData() {
    for (const item of this.dataSource) {
      // Simulate async processing
      await new Promise(resolve => setTimeout(resolve, 100));
      yield item * 2;
    }
  }
}

// Usage
async function processLargeDataset() {
  const processor = new AsyncDataProcessor([1, 2, 3, 4, 5]);
  
  for await (const result of processor.processData()) {
    console.log('Processed:', result);
    // Can handle each result as it becomes available
  }
}

processLargeDataset();
```

## Best Practices

### 1. Always Handle Errors
```javascript
// Bad
async function badExample() {
  const data = await fetch('/api/data');
  return data.json(); // No error handling
}

// Good
async function goodExample() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    return await response.json();
  } catch (error) {
    console.error('Failed to fetch data:', error);
    return null; // or throw, depending on requirements
  }
}
```

### 2. Avoid Mixing Promises and Async/Await
```javascript
// Bad (mixing patterns)
async function mixedExample() {
  const user = await fetchUser(1);
  return fetchPosts(user.id).then(posts => posts.filter(p => p.active));
}

// Good (consistent async/await)
async function consistentExample() {
  const user = await fetchUser(1);
  const posts = await fetchPosts(user.id);
  return posts.filter(p => p.active);
}
```

### 3. Use Promise.all() for Independent Operations
```javascript
// Bad (sequential when parallel is possible)
async function sequentialExample() {
  const user = await fetchUser(1);
  const settings = await fetchSettings(1);
  const notifications = await fetchNotifications(1);
  return { user, settings, notifications };
}

// Good (parallel execution)
async function parallelExample() {
  const [user, settings, notifications] = await Promise.all([
    fetchUser(1),
    fetchSettings(1),
    fetchNotifications(1)
  ]);
  return { user, settings, notifications };
}
```

### 4. Implement Proper Timeout Handling
```javascript
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Operation timed out')), ms)
  );
  
  return Promise.race([promise, timeout]);
}

// Usage
const userData = await withTimeout(
  fetch('/api/user/1').then(r => r.json()),
  5000
);
```

## Key Takeaways

### Essential Concepts
1. **Synchronous vs Asynchronous**: Understanding blocking vs non-blocking code
2. **Event Loop**: How JavaScript manages async operations
3. **Callbacks**: The foundation of async programming
4. **Promises**: Modern solution to callback hell
5. **Async/Await**: Syntactic sugar for cleaner async code
6. **Error Handling**: Proper error management in async operations

### Evolution of Async JavaScript
```javascript
// 1. Callbacks (ES5)
getData(function(err, data) {
  if (err) throw err;
  console.log(data);
});

// 2. Promises (ES6)
getData()
  .then(data => console.log(data))
  .catch(err => console.error(err));

// 3. Async/Await (ES2017)
try {
  const data = await getData();
  console.log(data);
} catch (err) {
  console.error(err);
}
```

### When to Use What
- **Callbacks**: Legacy code, Node.js APIs, simple async operations
- **Promises**: Multiple async operations, complex chaining, error handling
- **Async/Await**: Modern applications, cleaner code, better debugging
- **XHR**: Legacy browser support, fine-grained control
- **Fetch**: Modern browsers, simple API, promise-based

### Best Practices Summary
- Always handle errors appropriately
- Use async/await for cleaner, more readable code
- Implement timeouts for network requests
- Use Promise.all() for parallel operations
- Avoid callback hell with proper patterns
- Consider performance implications of sequential vs parallel execution

### Next Steps
- Learn about Web Workers for CPU-intensive tasks
- Explore Service Workers for offline functionality
- Study advanced patterns like observables and reactive programming
- Practice with real-world APIs and async scenarios
- Learn about testing async code

---

**File Navigation:**
- **Previous:** [17. APIs and Client-Server Architecture](17-apis.md)
- **Next:** [Advanced JavaScript Patterns](19-advanced-patterns.md)

## Blocking and Non-Blocking code
Blocking: Code that stops (blocks) further execution until the current task completes.
Because JavaScript runs on a single thread, if one task takes time, everything else waits.

Non-Blocking: Code that doesn’t stop the execution of other operations — instead, it delegates the task and continues running the rest.
Achieved using asynchronous operations like timers, network requests, file I/O, event listeners.
Uses the Event Loop and callback queue to handle results later.

## Callback

A callback function is just a function you pass as an argument to another function, so that it can be executed later — either immediately, after some work, or after an event/async operation completes.

1. synchronous
```javascript
function fun1(callback) {
    callback()
}

function fun2() {
    // some code
}

fun1(fun2)
```

fun2 here is the callback function. definition of a callback function doesn’t depend on when it’s called. If a function is passed as an argument to another function and invoked from inside that function, it’s a callback.

2. Asycnchronous
```javascript
setTimeout(function() {
    console.log("Timer finished!");
}, 2000);

console.log("This runs first");
```

here the call back function is the anonymous function is setTimeout. why? as it is passed to setTimeout and executed later. 

### Callback pattern
The callback pattern is a common JavaScript programming style where you:
1. Pass a function as an argument to another function.
2. The receiving function calls it at a certain point, often after some work is done (either immediately or later).

Error first callbacks
```javascript
function handeXyz(err, doAbc) {
    if(err) {
        console.log(err.msg);
        return;
    }
    log(doAbc);
}
```
we first handle errors and then the functionality of calllback if no errors.

### Callback Hell (pyramid of doom)
happens when you have too many nested callbacks, usually in asynchronous code, making it:
1. Hard to read
2. Hard to debug
3. Hard to maintain

why? Each async step depends on the result of the previous step
```javascript
function getData(data, callback) {
    setTimeout(() => {
        console.log(data);
        if (callback) callback();
    }, 1000);
}

getData('Step 1', () => {
    getData('Step 2', () => {
        getData('Step 3');
    });
});
```

## Promises
It’s used for handling asynchronous operations in a cleaner way than callbacks.
a special JavaScript object that represents a value that may be available now, later, or never.
like -> I promise I’ll give you a value. I’ll either fulfill it or tell you I failed

A Promise has three possible states:
1. Pending → initial state, neither fulfilled nor rejected.
2. Fulfilled → operation completed successfully, value available.
3. Rejected → operation failed, reason (error) available.
Once a Promise is fulfilled or rejected, it becomes settled and its state doesn’t change.

pending -> resolve -> fulfilled -> value
pending -> reject -> rejected -> reason

Built-in error handling via .catch()



```javascript
let pr = new Promise((res, rej) => {
    
})
```
We get a Promise instead of a Data...
because the data isn't ready yet to be served.
fetching data takes time -> 500ms to 2s or even more
js doesnt want to stp everything and wait
instead it gives a promise saying -> will give you data later, once it arrives

If we log directly the result of fetch() on an api, we will say it gives a promise and no data

```javascript
Promise.resolve('5').then((val) => console.log(val));

```
Promise.resolve().then(...) is basically a shortcut for:
Creating a resolved promise immediately.
Adding a .then() callback to it, which schedules that callback as a microtask.

```javascript
fetData1().then(data => { // api 1
    // do stuff
    fetchData2().then(data => {   // api 2
        // do stuff
        })
    })
```
instead we can do here 
```javascript
fetchData1()
    .then(data => { return fetchData2() })
    .then(data2 => {return something})
```
fetchData1 and fetchData2 are both returning promise 

## Async Await
cleaner code in more readable way
async -> makes a function always return a promise
await -> pauses inside an async function until the promise is fulfilled
code outside async funciton continues immediately
code inside the async function pauses at await

the above code can be even clearner
```JavaScript
(async () => {
    let data = await fetchData1();
    // do stuff with data
    let data2 = await fetchData2();
    // do stuff with data2
})();
```



If JS waited for data -> the page would stop responding(freeze) for seconds/mins -> you cant scroll/click or interact basically till that time
instead:
fetch starts req in background using browsers web api 
returns a promise
js continues the other code

## XMLHttpRequest
(agent talk some about xmlHttprequest)

## FetchAPI
(agent talk about this as well)

## XHR + Callbacks
```javascript
function getUsersXHR_cb(url, cb) {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url, true);
  xhr.responseType = "json";                  // lets you skip JSON.parse
  xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) cb(null, xhr.response);
    else cb(new Error(`HTTP ${xhr.status}`));
  };
  xhr.onerror = () => cb(new Error("Network error"));
  xhr.send();                                 // <-- the actual send
}

// use
getUsersXHR_cb("https://jsonplaceholder.typicode.com/users", (err, data) => {
  if (err) return console.error(err);
  console.log("XHR+CB:", data);
});

```

## XHR + Promises
```javascript
function getJSON_XHR(url, options = {}) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open(options.method || "GET", url, true);
    xhr.responseType = "json";
    if (options.headers) {
      for (const [k, v] of Object.entries(options.headers)) xhr.setRequestHeader(k, v);
    }
    xhr.onload = () => (xhr.status >= 200 && xhr.status < 300)
      ? resolve(xhr.response)
      : reject(new Error(`HTTP ${xhr.status}`));
    xhr.onerror = () => reject(new Error("Network error"));
    xhr.send(options.body || null);
  });
}

// .then/.catch
getJSON_XHR("https://jsonplaceholder.typicode.com/users")
  .then(data => console.log("XHR+Promise:", data))
.catch(console.error);

```

## XHR + async/await
```javascript
(async () => {
  try {
    const users = await getJSON_XHR("https://jsonplaceholder.typicode.com/users");
    console.log("XHR+await:", users);
  } catch (e) {
    console.error(e);
  }
})();

```

## Fetch + .then/.catch (native)
```javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then(res => {
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return res.json();
  })
  .then(data => console.log("fetch+.then:", data))
  .catch(console.error);

```

## Fetch + async/await
```javascript
async function getJSON(url, init) {
  const res = await fetch(url, init);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

(async () => {
  try {
    const users = await getJSON("https://jsonplaceholder.typicode.com/users");
    console.log("fetch+await:", users);
  } catch (e) {
    console.error(e);
  }
})();

```

## fetch + callbacks 
```javascript
function fetch_cb(url, cb) {
  fetch(url)
    .then(res => {
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return res.json();
    })
    .then(data => cb(null, data))
    .catch(err => cb(err));
}

// use
fetch_cb("https://jsonplaceholder.typicode.com/users", (err, data) => {
  if (err) return console.error(err);
  console.log("fetch+callback:", data);
});

```
