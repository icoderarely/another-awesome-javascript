# JavaScript Events and Event Handling

Events are signals that something has happened in the browser. All DOM nodes can generate these signals, allowing you to create interactive web applications.

## Understanding Events

An **event** is a signal that something has happened - a user clicked a button, moved the mouse, pressed a key, or the page finished loading. JavaScript can listen for these events and respond accordingly.

### Common Event Categories

#### 1. Mouse Events

- **`click`** - User clicks on an element
- **`contextmenu`** - User right-clicks on an element
- **`mouseover`** - Cursor enters an element
- **`mouseout`** - Cursor leaves an element
- **`mousedown`** - Mouse button is pressed down
- **`mouseup`** - Mouse button is released
- **`mousemove`** - Mouse is moved over an element
- **`dblclick`** - User double-clicks an element

#### 2. Keyboard Events

- **`keydown`** - Key is pressed down
- **`keyup`** - Key is released
- **`keypress`** - Key is pressed (deprecated, use `keydown`)

#### 3. Form Events

- **`submit`** - Form is submitted
- **`focus`** - Element receives focus (e.g., user clicks on input)
- **`blur`** - Element loses focus
- **`change`** - Element value changes (after losing focus)
- **`input`** - Element value changes (real-time)

#### 4. Document Events

- **`DOMContentLoaded`** - HTML is fully loaded and parsed
- **`load`** - All resources (images, stylesheets) are loaded
- **`beforeunload`** - User is about to leave the page

#### 5. CSS Events

- **`transitionend`** - CSS transition completes
- **`animationend`** - CSS animation completes

## Event Handlers

An **event handler** is a function that runs when an event occurs. There are multiple ways to assign event handlers.

### 1. HTML Attribute Method

Set handlers directly in HTML using `on<event>` attributes.

```html
<!-- Simple inline handler -->
<button onclick="alert('Button clicked!')">Click me</button>

<!-- Call a JavaScript function -->
<script>
  function handleClick() {
    alert("Button was clicked!");
  }

  function countItems() {
    for (let i = 1; i <= 3; i++) {
      console.log(`Item ${i}`);
    }
  }
</script>

<button onclick="handleClick()">Handle Click</button>
<button onclick="countItems()">Count Items</button>
```

**Characteristics:**

- ✅ Simple and direct
- ❌ Mixes HTML and JavaScript
- ❌ Limited to short code snippets
- ❌ HTML attribute names are case-insensitive (`onclick`, not `onClick`)

### 2. DOM Property Method

Assign handlers using JavaScript DOM properties.

```javascript
let button = document.querySelector("#myButton");

// Assign handler function
button.onclick = function () {
  alert("Button clicked via DOM property!");
};

// Or using arrow function
button.onclick = () => {
  console.log("Arrow function handler");
};

// Or reference existing function
function handleClick() {
  console.log("Function reference handler");
}
button.onclick = handleClick; // Note: no parentheses
```

**Important Notes:**

- DOM properties are **case-sensitive** (`onclick`, not `OnClick`)
- HTML attributes and DOM properties are equivalent - setting one overwrites the other
- You can only assign **one handler** per event type
- The value of `this` inside the handler refers to the element

```javascript
let button = document.querySelector("#myButton");

button.onclick = function () {
  console.log(this); // The button element
  console.log(this.textContent); // Button's text content
  this.style.backgroundColor = "red"; // Change button color
};
```

### 3. Event Listeners (Recommended)

The modern, flexible approach using `addEventListener`.

```javascript
let button = document.querySelector("#myButton");

// Basic event listener
button.addEventListener("click", function () {
  console.log("Button clicked!");
});

// Arrow function
button.addEventListener("click", () => {
  console.log("Arrow function listener");
});

// Reference to existing function
function handleClick() {
  console.log("Function reference listener");
}
button.addEventListener("click", handleClick);
```

#### Advantages of Event Listeners

1. **Multiple handlers**: Can attach multiple handlers to the same event
2. **Options support**: Advanced configuration with options object
3. **Better control**: Easy to remove specific handlers
4. **Modern standard**: Best practice in modern JavaScript

```javascript
let button = document.querySelector("#myButton");

// Multiple handlers for the same event
button.addEventListener("click", () => console.log("First handler"));
button.addEventListener("click", () => console.log("Second handler"));
button.addEventListener("click", () => console.log("Third handler"));

// All three will execute when button is clicked
```

#### Event Listener Options

```javascript
let button = document.querySelector("#myButton");

// Options object
button.addEventListener("click", handleClick, {
  once: true, // Execute only once, then remove
  passive: true, // Never calls preventDefault()
  capture: true, // Execute during capture phase
});

// Shorthand for capture
button.addEventListener("click", handleClick, true); // capture = true
```

#### Removing Event Listeners

```javascript
let button = document.querySelector("#myButton");

function handleClick() {
  console.log("Button clicked!");
}

// Add listener
button.addEventListener("click", handleClick);

// Remove listener (must use same function reference)
button.removeEventListener("click", handleClick);

// This won't work (different function, even if identical code):
button.addEventListener("click", function () {
  console.log("test");
});
button.removeEventListener("click", function () {
  console.log("test");
});
```

## The Event Object

When an event occurs, the browser creates an **event object** containing details about what happened and passes it to the handler.

```javascript
button.addEventListener("click", function (event) {
  // Event object contains information about the event
  console.log("Event type:", event.type); // "click"
  console.log("Target element:", event.target); // Element that triggered event
  console.log("Current target:", event.currentTarget); // Element handling event
  console.log("Mouse coordinates:", event.clientX, event.clientY);
  console.log("Timestamp:", event.timeStamp);
});
```

### Key Event Object Properties

```javascript
document.addEventListener("click", function (event) {
  // Common properties
  console.log("event.type:", event.type); // Event type ("click")
  console.log("event.target:", event.target); // Element that triggered event
  console.log("event.currentTarget:", event.currentTarget); // Element handling event
  console.log("event.timeStamp:", event.timeStamp); // When event occurred

  // Mouse events
  if (event.type.startsWith("mouse") || event.type === "click") {
    console.log("Mouse X:", event.clientX); // X coordinate in viewport
    console.log("Mouse Y:", event.clientY); // Y coordinate in viewport
    console.log("Button pressed:", event.button); // Which mouse button
  }

  // Keyboard events
  if (event.type.startsWith("key")) {
    console.log("Key pressed:", event.key); // Key value ("a", "Enter", "Shift")
    console.log("Key code:", event.code); // Physical key ("KeyA", "Enter")
    console.log("Ctrl key:", event.ctrlKey); // Was Ctrl pressed?
    console.log("Shift key:", event.shiftKey); // Was Shift pressed?
    console.log("Alt key:", event.altKey); // Was Alt pressed?
  }
});
```

### Event Object in Different Event Types

```javascript
// Mouse events
button.addEventListener("click", (e) => {
  console.log(`Clicked at (${e.clientX}, ${e.clientY})`);
  console.log("Button:", e.button); // 0=left, 1=middle, 2=right
});

// Keyboard events
input.addEventListener("keydown", (e) => {
  console.log(`Key: ${e.key}, Code: ${e.code}`);

  if (e.key === "Enter") {
    console.log("Enter key pressed!");
  }

  if (e.ctrlKey && e.key === "s") {
    e.preventDefault(); // Prevent browser save dialog
    console.log("Ctrl+S pressed - custom save action");
  }
});

// Form events
form.addEventListener("submit", (e) => {
  e.preventDefault(); // Prevent default form submission
  console.log("Form submitted with custom handler");
});
```

## Event Propagation

Event propagation describes how events travel through the DOM tree. Understanding this is crucial for effective event handling.

### Key Concepts

- **`event.target`** - The element where the event actually occurred (where it was fired)
- **`event.currentTarget`** - The element that is currently handling the event
- **`this`** - Same as `event.currentTarget` in regular functions

```html
<div id="outer">
  <div id="middle">
    <button id="inner">Click me</button>
  </div>
</div>
```

```javascript
// When button is clicked:
// event.target = button element (where click happened)
// event.currentTarget = depends on which element's handler is running
```

### Event Bubbling

Events "bubble up" from the target element to its ancestors (bottom to top).

```javascript
// HTML: <div id="parent"><button id="child">Click</button></div>

document.getElementById("parent").addEventListener("click", (e) => {
  console.log("Parent clicked");
  console.log("Target:", e.target.id); // "child" (button)
  console.log("Current target:", e.currentTarget.id); // "parent" (div)
});

document.getElementById("child").addEventListener("click", (e) => {
  console.log("Child clicked");
  console.log("Target:", e.target.id); // "child" (button)
  console.log("Current target:", e.currentTarget.id); // "child" (button)
});

// When button is clicked:
// 1. "Child clicked" (target phase)
// 2. "Parent clicked" (bubbling phase)
```

### Event Capturing (Trickling)

Events can also be captured during the "capture phase" (top to bottom), before reaching the target.

```javascript
// Add capture: true to listen during capture phase
document.getElementById("parent").addEventListener(
  "click",
  (e) => {
    console.log("Parent captured (going down)");
  },
  { capture: true }
);

document.getElementById("child").addEventListener("click", (e) => {
  console.log("Child clicked (target phase)");
});

document.getElementById("parent").addEventListener("click", (e) => {
  console.log("Parent bubbled (going up)");
});

// When button is clicked:
// 1. "Parent captured (going down)" (capture phase)
// 2. "Child clicked (target phase)"
// 3. "Parent bubbled (going up)" (bubble phase)
```

### Stopping Propagation

Use `stopPropagation()` to prevent the event from continuing its journey.

```javascript
document.getElementById("child").addEventListener("click", (e) => {
  console.log("Child clicked");
  e.stopPropagation(); // Stop bubbling to parent
});

document.getElementById("parent").addEventListener("click", (e) => {
  console.log("This will not run"); // Won't execute due to stopPropagation
});
```

### Preventing Default Behavior

Use `preventDefault()` to stop the browser's default action.

```javascript
// Prevent form submission
form.addEventListener("submit", (e) => {
  e.preventDefault();
  console.log("Form submission prevented");
});

// Prevent link navigation
link.addEventListener("click", (e) => {
  e.preventDefault();
  console.log("Link click prevented");
});

// Prevent context menu
element.addEventListener("contextmenu", (e) => {
  e.preventDefault();
  console.log("Right-click context menu prevented");
});
```

## Event Delegation

Event delegation leverages event bubbling to handle events efficiently, especially for dynamically created elements.

### Basic Event Delegation

Instead of adding listeners to many child elements, add one listener to a parent element.

```javascript
// HTML: <ul id="list"><li>Item 1</li><li>Item 2</li></ul>

// ❌ Bad: Add listener to each li
document.querySelectorAll("#list li").forEach((li) => {
  li.addEventListener("click", (e) => {
    console.log("Item clicked:", e.target.textContent);
  });
});

// ✅ Good: Use event delegation on parent
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    console.log("Item clicked:", e.target.textContent);
  }
});
```

### Advanced Event Delegation

Use more sophisticated targeting with classes or data attributes.

```javascript
// HTML with different button types
/*
<div id="toolbar">
    <button class="btn-save" data-action="save">Save</button>
    <button class="btn-delete" data-action="delete">Delete</button>
    <button class="btn-edit" data-action="edit">Edit</button>
</div>
*/

document.getElementById("toolbar").addEventListener("click", (e) => {
  // Check if clicked element is a button
  if (e.target.matches("button")) {
    let action = e.target.dataset.action;

    switch (action) {
      case "save":
        console.log("Saving...");
        break;
      case "delete":
        console.log("Deleting...");
        break;
      case "edit":
        console.log("Editing...");
        break;
    }
  }

  // Alternative: Check by class
  if (e.target.classList.contains("btn-save")) {
    console.log("Save button clicked");
  }
});
```

### Event Delegation for Dynamic Content

Perfect for handling events on elements added after page load.

```javascript
// Parent container
let container = document.getElementById("dynamic-container");

// Delegated event handler
container.addEventListener("click", (e) => {
  // Handle button clicks
  if (e.target.matches(".dynamic-button")) {
    console.log("Dynamic button clicked:", e.target.textContent);
  }

  // Handle delete buttons
  if (e.target.matches(".delete-btn")) {
    e.target.parentElement.remove();
  }
});

// Function to add dynamic content
function addDynamicContent() {
  let div = document.createElement("div");
  div.innerHTML = `
        <button class="dynamic-button">Dynamic Button</button>
        <button class="delete-btn">Delete</button>
    `;
  container.appendChild(div);
}

// Event delegation automatically handles new elements!
addDynamicContent();
```

### Practical Event Delegation Examples

```javascript
// Table row selection
document.getElementById("data-table").addEventListener("click", (e) => {
  let row = e.target.closest("tr");
  if (row) {
    // Toggle selected class
    row.classList.toggle("selected");
  }
});

// Modal close buttons
document.body.addEventListener("click", (e) => {
  if (e.target.matches(".modal-close") || e.target.closest(".modal-close")) {
    let modal = e.target.closest(".modal");
    if (modal) {
      modal.style.display = "none";
    }
  }
});

// Form validation
document.addEventListener("input", (e) => {
  if (e.target.matches('input[type="email"]')) {
    validateEmail(e.target);
  }

  if (e.target.matches("input[required]")) {
    validateRequired(e.target);
  }
});
```

## Best Practices

### 1. Use Event Listeners

```javascript
// ✅ Good: Modern and flexible
element.addEventListener("click", handler);

// ❌ Avoid: Limited to one handler
element.onclick = handler;
```

### 2. Use Event Delegation for Dynamic Content

```javascript
// ✅ Good: Handles current and future elements
parent.addEventListener("click", (e) => {
  if (e.target.matches(".button")) {
    // Handle click
  }
});

// ❌ Avoid: Only handles existing elements
document.querySelectorAll(".button").forEach((btn) => {
  btn.addEventListener("click", handler);
});
```

### 3. Remove Event Listeners When Needed

```javascript
// Store function reference for removal
function handleClick() {
  console.log("Clicked");
}

element.addEventListener("click", handleClick);

// Remove when no longer needed
element.removeEventListener("click", handleClick);
```

### 4. Use preventDefault() and stopPropagation() Wisely

```javascript
link.addEventListener("click", (e) => {
  e.preventDefault(); // Stop default behavior
  e.stopPropagation(); // Stop event propagation

  // Custom logic here
});
```

### 5. Passive Event Listeners for Performance

```javascript
// For scroll/touch events that don't need preventDefault()
element.addEventListener("scroll", handler, { passive: true });
```

This comprehensive guide covers all aspects of JavaScript events and event handling, from basic concepts to advanced techniques like event delegation and propagation control.
