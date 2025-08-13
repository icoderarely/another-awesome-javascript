# Client-Side Storage: Local Storage, Session Storage, and Cookies

When visiting websites, browsers need to store data on your computer to remember user preferences, login states, and other information. JavaScript provides three main client-side storage mechanisms.

## 📚 Overview of Storage Options

| Storage Type | Size Limit | Persistence | Sent to Server | Use Case |
|-------------|------------|-------------|----------------|----------|
| **Cookies** | 4KB | Configurable | Yes (automatically) | Authentication, tracking |
| **localStorage** | 5-10MB | Until manually cleared | No | User preferences, caching |
| **sessionStorage** | 5-10MB | Until tab closes | No | Temporary form data, session state |

## Local Storage

**Persistent storage** that survives browser restarts and only expires when manually cleared.

### Basic Operations

```javascript
// Save data
localStorage.setItem('theme', 'dark');
localStorage.setItem('language', 'en');

// Read data
const theme = localStorage.getItem('theme'); // "dark"
const language = localStorage.getItem('language'); // "en"

// Remove specific item
localStorage.removeItem('theme');

// Clear all localStorage
localStorage.clear();

// Check if item exists
if (localStorage.getItem('theme') !== null) {
    console.log('Theme preference exists');
}
```

### Storing Complex Data

localStorage only stores **strings**. For objects and arrays, use JSON methods:

```javascript
// Storing objects
const user = { name: 'John', age: 25, preferences: ['dark', 'notifications'] };
localStorage.setItem('user', JSON.stringify(user));

// Retrieving objects
const storedUser = JSON.parse(localStorage.getItem('user'));
console.log(storedUser.name); // "John"

// Storing arrays
const favorites = ['item1', 'item2', 'item3'];
localStorage.setItem('favorites', JSON.stringify(favorites));

// Retrieving arrays
const storedFavorites = JSON.parse(localStorage.getItem('favorites'));
```

### Common Use Cases
- **UI theme preferences** (dark/light mode)
- **Caching API responses** to reduce network calls
- **Storing offline data** for Progressive Web Apps (PWAs)
- **User settings** and customizations

## Session Storage

**Temporary storage** that persists only for the current tab session. Data is lost when the tab is closed.

### Basic Operations

```javascript
// Save temporary data
sessionStorage.setItem('formDraft', 'Hello there!');
sessionStorage.setItem('currentStep', '3');

// Read temporary data
const draft = sessionStorage.getItem('formDraft'); // "Hello there!"

// Remove and clear work the same as localStorage
sessionStorage.removeItem('formDraft');
sessionStorage.clear();
```

### Important Characteristics
- **Tab-specific**: Each tab has its own sessionStorage
- **Temporary**: Data disappears when tab closes
- **Page refresh**: Data survives page refreshes within the same tab

### Common Use Cases
- **Form drafts** and temporary user input
- **Checkout process state** in e-commerce
- **Temporary filters** and search states
- **Multi-step form progress**

## Cookies

**Lightweight storage** that automatically gets sent to the server with every HTTP request. Designed for server-client communication.

### Basic Cookie Operations

```javascript
// Create a simple cookie (session cookie - expires when browser closes)
document.cookie = "username=John";

// Create a persistent cookie with expiry date
document.cookie = "theme=dark; expires=Fri, 31 Dec 2025 23:59:59 GMT; path=/";

// Read all cookies (returns a string)
console.log(document.cookie); // "username=John; theme=dark; otherCookie=value"

// Delete a cookie (set expiry to past date)
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/";
```

### Cookie Attributes

```javascript
// Complete cookie with all attributes
document.cookie = "sessionId=abc123; expires=Fri, 31 Dec 2025 23:59:59 GMT; path=/; domain=.example.com; Secure; HttpOnly; SameSite=Strict";
```

**Cookie Attributes Explained:**
- **Name=Value**: The actual data (`sessionId=abc123`)
- **expires**: When the cookie expires (persistent cookie)
- **path**: Which URLs can access the cookie (`/` = entire site)
- **domain**: Which domains can access the cookie
- **Secure**: Only send over HTTPS connections
- **HttpOnly**: Cannot be accessed by JavaScript (prevents XSS)
- **SameSite**: CSRF protection (`Strict`, `Lax`, or `None`)

### Cookie Types

```javascript
// Session Cookie (no expiry - deleted when browser closes)
document.cookie = "tempData=value123";

// Persistent Cookie (with expiry date)
document.cookie = "userPrefs=darkMode; expires=Wed, 01 Jan 2026 12:00:00 UTC";

// Path-specific Cookie
document.cookie = "adminToken=xyz789; path=/admin";
```

### Security Best Practices

```javascript
// Secure cookie for authentication
document.cookie = "authToken=abc123; Secure; HttpOnly; SameSite=Strict; path=/";

// Avoid storing sensitive data in cookies accessible to JavaScript
// ❌ Bad
document.cookie = "password=mypassword123"; // Never do this!

// ✅ Good
document.cookie = "sessionId=randomToken; HttpOnly; Secure";
```

### Common Use Cases
- **Authentication tokens** (JWT, session IDs)
- **User tracking** and analytics
- **Server session management** (PHPSESSID, JSESSIONID)
- **Cross-page communication** with server

## Storage Comparison and When to Use What

### Use localStorage when:
- Data needs to persist across browser sessions
- Storing user preferences and settings
- Caching large amounts of data
- Building offline-capable applications

### Use sessionStorage when:
- Data is only needed for current session
- Storing temporary form data
- Managing multi-step processes
- Keeping tab-specific state

### Use cookies when:
- Server needs access to the data
- Implementing authentication
- Tracking user behavior
- Small amounts of data (under 4KB)

## Security Considerations

### 🚨 Important Security Rules

1. **Never store sensitive data** in any client-side storage
2. **XSS vulnerability**: All three storage types are vulnerable to XSS attacks
3. **CSRF vulnerability**: Cookies are vulnerable to CSRF attacks
4. **Use HTTPS**: Always use secure connections for sensitive data

```javascript
// ❌ Never store these client-side:
localStorage.setItem('password', 'mypassword');
localStorage.setItem('creditCard', '1234-5678-9012-3456');
document.cookie = "socialSecurity=123-45-6789";

// ✅ Safe to store:
localStorage.setItem('theme', 'dark');
localStorage.setItem('language', 'en');
sessionStorage.setItem('formDraft', 'user input');
```

## Practical Example: Theme Preference

```javascript
// Save theme preference
function setTheme(theme) {
    localStorage.setItem('userTheme', theme);
    document.body.className = theme;
}

// Load theme on page load
function loadTheme() {
    const savedTheme = localStorage.getItem('userTheme') || 'light';
    document.body.className = savedTheme;
    return savedTheme;
}

// Usage
loadTheme(); // Apply saved theme on page load
setTheme('dark'); // User changes to dark theme
```

## 🔑 Key Takeaways

1. **localStorage**: Persistent, large capacity, no server communication
2. **sessionStorage**: Temporary, tab-specific, large capacity
3. **Cookies**: Small, automatically sent to server, configurable expiry
4. **Security**: Never store sensitive data client-side
5. **JSON methods**: Required for storing objects/arrays in Web Storage
6. **Path and domain**: Important for cookie accessibility 

Which one to use?
Authentication/session tracking → Cookies (with HttpOnly + Secure).
Long-term client-only settings → Local Storage.
Short-term tab-only state → Session Storage.
