# APIs and Client-Server Architecture

## Table of Contents

- [Client-Server Architecture](#client-server-architecture)
- [Understanding JSON](#understanding-json)
- [What are APIs?](#what-are-apis)
- [Types of APIs](#types-of-apis)
- [HTTP Methods](#http-methods)
- [API Endpoints](#api-endpoints)
- [Working with APIs in JavaScript](#working-with-apis-in-javascript)
- [Best Practices](#best-practices)
- [Common API Patterns](#common-api-patterns)
- [Error Handling](#error-handling)
- [Key Takeaways](#key-takeaways)

## Client-Server Architecture

The client-server model is a fundamental architecture pattern in web development where:

### Components

- **Client**: The application that requests services (browser, mobile app, desktop app)
- **Server**: The system that provides services and resources (web server, database server, API server)

### Communication Flow

```
Client → Request → Server
Server → Response → Client
```

### Real-World Example

```javascript
// Client side (your browser)
fetch("https://api.github.com/users/octocat")
  .then((response) => response.json())
  .then((data) => console.log(data));

// This sends a request to GitHub's server
// Server processes the request and sends back user data
```

### Types of Requests

- **With Data**: Sending information to create/update resources
- **Without Data**: Retrieving existing information

## Understanding JSON

### What is JSON?

**JSON (JavaScript Object Notation)** is a lightweight, text-based data interchange format that's easy for humans to read and write, and easy for machines to parse and generate.

### Key Characteristics

- **Text Format**: JSON is always a string, not an object
- **Language Independent**: Despite its name, JSON is used across all programming languages
- **Structured**: Organizes data in key-value pairs
- **Modern Standard**: Has largely replaced XML for web APIs

### JSON Syntax Rules

```javascript
// Valid JSON data types
{
  "string": "Hello World",           // String (double quotes required)
  "number": 42,                      // Number (integer or float)
  "boolean": true,                   // Boolean (true/false)
  "null": null,                      // Null value
  "array": [1, 2, 3],               // Array
  "object": {                        // Nested object
    "nested": "value"
  }
}

// Invalid in JSON (but valid in JavaScript)
{
  function: function() {},           // Functions not allowed
  undefined: undefined,              // Undefined not allowed
  'singleQuotes': 'not allowed',     // Must use double quotes
  variableName: "value"              // Keys must be strings
}
```

### JSON Conversion Methods

```javascript
// JavaScript Object
const user = {
  name: "John Doe",
  age: 30,
  isActive: true,
};

// Converting TO JSON (stringify)
const jsonString = JSON.stringify(user);
console.log(jsonString); // '{"name":"John Doe","age":30,"isActive":true}'
console.log(typeof jsonString); // "string"

// Converting FROM JSON (parse)
const parsedData = JSON.parse(jsonString);
console.log(parsedData); // {name: "John Doe", age: 30, isActive: true}
console.log(typeof parsedData); // "object"

// Real-world usage
localStorage.setItem("user", JSON.stringify(user)); // Store
const storedUser = JSON.parse(localStorage.getItem("user")); // Retrieve
```

## What are APIs?

### Definition

**API (Application Programming Interface)** is a set of rules, protocols, and tools that allows different software applications to communicate and interact with each other.

### Real-World Analogy

Think of an API like a waiter in a restaurant:

- **You (Client)**: Order food from the menu
- **Waiter (API)**: Takes your order to the kitchen and brings back your food
- **Kitchen (Server)**: Prepares the food based on your order

### Types of APIs

#### 1. Web APIs (REST APIs)

```javascript
// Getting user data from a REST API
fetch("https://jsonplaceholder.typicode.com/users/1")
  .then((response) => response.json())
  .then((user) => {
    console.log(`User: ${user.name}, Email: ${user.email}`);
  });
```

#### 2. Browser APIs

```javascript
// Geolocation API
navigator.geolocation.getCurrentPosition((position) => {
  console.log(
    `Lat: ${position.coords.latitude}, Lng: ${position.coords.longitude}`
  );
});

// Local Storage API
localStorage.setItem("theme", "dark");
const theme = localStorage.getItem("theme");
```

#### 3. Third-Party APIs

```javascript
// Weather API example
async function getWeather(city) {
  const apiKey = "your-api-key";
  const response = await fetch(
    `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}`
  );
  const data = await response.json();
  return data;
}
```

## HTTP Methods

### Common HTTP Methods

```javascript
// GET - Retrieve data
fetch("https://api.example.com/users").then((response) => response.json());

// POST - Create new data
fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    name: "John Doe",
    email: "john@example.com",
  }),
});

// PUT - Update existing data (complete update)
fetch("https://api.example.com/users/1", {
  method: "PUT",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    name: "John Smith",
    email: "johnsmith@example.com",
  }),
});

// PATCH - Partial update
fetch("https://api.example.com/users/1", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    email: "newemail@example.com",
  }),
});

// DELETE - Remove data
fetch("https://api.example.com/users/1", {
  method: "DELETE",
});
```

## API Endpoints

### Understanding Endpoints

An endpoint is a specific URL where an API can be accessed by a client application.

```javascript
// Base URL
const baseURL = "https://jsonplaceholder.typicode.com";

// Different endpoints
const endpoints = {
  users: `${baseURL}/users`, // Get all users
  user: `${baseURL}/users/1`, // Get specific user
  posts: `${baseURL}/posts`, // Get all posts
  userPosts: `${baseURL}/users/1/posts`, // Get posts by specific user
};
```

### RESTful API Structure

```javascript
// RESTful endpoint patterns
const apiEndpoints = {
  // Collection endpoints
  getAllUsers: "GET /users",
  createUser: "POST /users",

  // Resource endpoints
  getUser: "GET /users/:id",
  updateUser: "PUT /users/:id",
  patchUser: "PATCH /users/:id",
  deleteUser: "DELETE /users/:id",

  // Nested resources
  getUserPosts: "GET /users/:id/posts",
  createUserPost: "POST /users/:id/posts",
};
```

## Working with APIs in JavaScript

### Complete API Class Example

```javascript
class APIClient {
  constructor(baseURL) {
    this.baseURL = baseURL;
    this.defaultHeaders = {
      "Content-Type": "application/json",
    };
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const config = {
      headers: { ...this.defaultHeaders, ...options.headers },
      ...options,
    };

    try {
      const response = await fetch(url, config);

      if (!response.ok) {
        throw new Error(
          `HTTP Error: ${response.status} ${response.statusText}`
        );
      }

      const contentType = response.headers.get("content-type");
      if (contentType && contentType.includes("application/json")) {
        return await response.json();
      }

      return await response.text();
    } catch (error) {
      console.error("API Request failed:", error);
      throw error;
    }
  }

  // GET request
  async get(endpoint) {
    return this.request(endpoint, { method: "GET" });
  }

  // POST request
  async post(endpoint, data) {
    return this.request(endpoint, {
      method: "POST",
      body: JSON.stringify(data),
    });
  }

  // PUT request
  async put(endpoint, data) {
    return this.request(endpoint, {
      method: "PUT",
      body: JSON.stringify(data),
    });
  }

  // DELETE request
  async delete(endpoint) {
    return this.request(endpoint, { method: "DELETE" });
  }
}

// Usage
const api = new APIClient("https://jsonplaceholder.typicode.com");

// Get all users
const users = await api.get("/users");

// Create a new post
const newPost = await api.post("/posts", {
  title: "My New Post",
  body: "This is the content of my post",
  userId: 1,
});
```

## Best Practices

### 1. Error Handling

```javascript
async function fetchUserSafely(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`);

    if (!response.ok) {
      throw new Error(`Failed to fetch user: ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    console.error("Error fetching user:", error);
    return null; // or throw error based on your needs
  }
}
```

### 2. API Response Validation

```javascript
function validateUserData(userData) {
  const requiredFields = ["id", "name", "email"];

  for (const field of requiredFields) {
    if (!userData[field]) {
      throw new Error(`Missing required field: ${field}`);
    }
  }

  if (!isValidEmail(userData.email)) {
    throw new Error("Invalid email format");
  }

  return true;
}

function isValidEmail(email) {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}
```

### 3. Rate Limiting and Throttling

```javascript
class RateLimitedAPI {
  constructor(baseURL, requestsPerSecond = 10) {
    this.baseURL = baseURL;
    this.requestQueue = [];
    this.isProcessing = false;
    this.interval = 1000 / requestsPerSecond;
  }

  async makeRequest(endpoint, options) {
    return new Promise((resolve, reject) => {
      this.requestQueue.push({ endpoint, options, resolve, reject });
      this.processQueue();
    });
  }

  async processQueue() {
    if (this.isProcessing || this.requestQueue.length === 0) return;

    this.isProcessing = true;

    while (this.requestQueue.length > 0) {
      const { endpoint, options, resolve, reject } = this.requestQueue.shift();

      try {
        const response = await fetch(`${this.baseURL}${endpoint}`, options);
        const data = await response.json();
        resolve(data);
      } catch (error) {
        reject(error);
      }

      await new Promise((resolve) => setTimeout(resolve, this.interval));
    }

    this.isProcessing = false;
  }
}
```

## Common API Patterns

### 1. Pagination

```javascript
async function fetchAllUsers(baseUrl) {
  let allUsers = [];
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const response = await fetch(`${baseUrl}/users?page=${page}&limit=10`);
    const data = await response.json();

    allUsers.push(...data.users);
    hasMore = data.hasMore;
    page++;
  }

  return allUsers;
}
```

### 2. Search and Filtering

```javascript
class UserAPI {
  constructor(baseURL) {
    this.baseURL = baseURL;
  }

  async searchUsers(query, filters = {}) {
    const params = new URLSearchParams({
      q: query,
      ...filters,
    });

    const response = await fetch(`${this.baseURL}/users/search?${params}`);
    return response.json();
  }

  async getUsers(options = {}) {
    const {
      page = 1,
      limit = 10,
      sortBy = "name",
      order = "asc",
      active = null,
    } = options;

    const params = new URLSearchParams({
      page,
      limit,
      sortBy,
      order,
    });

    if (active !== null) {
      params.append("active", active);
    }

    const response = await fetch(`${this.baseURL}/users?${params}`);
    return response.json();
  }
}
```

## Error Handling

### HTTP Status Codes

```javascript
async function handleAPIResponse(response) {
  switch (response.status) {
    case 200:
      return await response.json();
    case 201:
      console.log("Resource created successfully");
      return await response.json();
    case 400:
      throw new Error("Bad Request - Check your data");
    case 401:
      throw new Error("Unauthorized - Check your authentication");
    case 403:
      throw new Error("Forbidden - You don't have permission");
    case 404:
      throw new Error("Not Found - Resource doesn't exist");
    case 500:
      throw new Error("Server Error - Try again later");
    default:
      throw new Error(`Unexpected status code: ${response.status}`);
  }
}
```

### Retry Logic

```javascript
async function fetchWithRetry(url, options, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);

      if (response.ok) {
        return await response.json();
      }

      if (response.status >= 400 && response.status < 500) {
        // Client error - don't retry
        throw new Error(`Client error: ${response.status}`);
      }

      // Server error - might be temporary, retry
      if (attempt === maxRetries) {
        throw new Error(`Failed after ${maxRetries} attempts`);
      }
    } catch (error) {
      if (attempt === maxRetries) {
        throw error;
      }

      // Wait before retry (exponential backoff)
      await new Promise((resolve) =>
        setTimeout(resolve, Math.pow(2, attempt) * 1000)
      );
    }
  }
}
```

## Key Takeaways

### Essential Concepts

1. **Client-Server Model**: Understanding how applications communicate
2. **JSON Format**: The standard for data exchange in modern web APIs
3. **HTTP Methods**: GET, POST, PUT, PATCH, DELETE for different operations
4. **API Endpoints**: Specific URLs for accessing different resources
5. **Error Handling**: Properly managing failed requests and edge cases

### Best Practices Summary

- Always validate API responses
- Implement proper error handling
- Use async/await for cleaner code
- Consider rate limiting for high-frequency requests
- Cache responses when appropriate
- Secure API keys and sensitive data

### Next Steps

- Practice with public APIs (JSONPlaceholder, GitHub API, etc.)
- Learn about authentication methods (API keys, OAuth, JWT)
- Explore GraphQL as an alternative to REST
- Study API security best practices
- Learn about API testing and documentation

---

**File Navigation:**

- **Previous:** [16. Object-Oriented Programming](16-oops.md)
- **Next:** [18. Asynchronous JavaScript](18-async.md)
