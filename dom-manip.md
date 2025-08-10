# DOM Manipulation

DOM Manipulation involves dynamically modifying the structure, content, and styling of web pages using JavaScript. This is fundamental to creating interactive web applications.

## Selecting Elements

### 1. Document Query Methods

#### `getElementById()`

```javascript
let element = document.getElementById("myId");
console.log(element); // Returns single element or null
```

#### `getElementsByClassName()`

```javascript
let elements = document.getElementsByClassName("myClass");
console.log(elements); // Returns HTMLCollection (live)
console.log(elements[0]); // Access first element
```

#### `getElementsByTagName()`

```javascript
let elements = document.getElementsByTagName("div");
console.log(elements); // Returns HTMLCollection of all div elements
```

#### `querySelector()` (Modern, Recommended)

```javascript
// Select first matching element
let element = document.querySelector("#myId"); // By ID
let element = document.querySelector(".myClass"); // By class
let element = document.querySelector("div"); // By tag
let element = document.querySelector('[data-id="1"]'); // By attribute
```

#### `querySelectorAll()` (Modern, Recommended)

```javascript
// Select all matching elements
let elements = document.querySelectorAll(".myClass");
console.log(elements); // Returns NodeList (static)

// Convert to array for array methods
let elementsArray = Array.from(elements);
```

### 2. Element Traversal

```javascript
let element = document.querySelector("#myElement");

// Parent
console.log(element.parentElement);
console.log(element.parentNode);

// Children
console.log(element.children); // HTMLCollection (elements only)
console.log(element.childNodes); // NodeList (includes text nodes)
console.log(element.firstElementChild);
console.log(element.lastElementChild);

// Siblings
console.log(element.nextElementSibling);
console.log(element.previousElementSibling);
```

## Creating and Modifying Elements

### Creating New Elements

```javascript
// Create element
let div = document.createElement("div");
let p = document.createElement("p");
let img = document.createElement("img");

// Create text node
let textNode = document.createTextNode("Hello World");
```

### Setting Content

#### `textContent` vs `innerHTML`

```javascript
let element = document.querySelector("#myElement");

// Set text content (safe, escaped)
element.textContent = "Hello <strong>World</strong>"; // Shows literal text

// Set HTML content (can be dangerous with user input)
element.innerHTML = "Hello <strong>World</strong>"; // Renders as HTML

// Get content
console.log(element.textContent); // Text only
console.log(element.innerHTML); // HTML markup
```

### Modifying Attributes

```javascript
let img = document.querySelector("img");

// Set attributes
img.setAttribute("src", "image.jpg");
img.setAttribute("alt", "Description");
img.setAttribute("data-id", "123");

// Get attributes
console.log(img.getAttribute("src"));
console.log(img.getAttribute("data-id"));

// Remove attributes
img.removeAttribute("data-id");

// Check if attribute exists
if (img.hasAttribute("alt")) {
  console.log("Alt attribute exists");
}

// Direct property access (for standard attributes)
img.src = "newimage.jpg";
img.id = "myImage";
img.className = "responsive";
```

### Working with Classes

```javascript
let element = document.querySelector(".myElement");

// Add classes
element.classList.add("newClass");
element.classList.add("class1", "class2", "class3");

// Remove classes
element.classList.remove("oldClass");
element.classList.remove("class1", "class2");

// Toggle class
element.classList.toggle("active"); // Adds if not present, removes if present

// Check if class exists
if (element.classList.contains("active")) {
  console.log("Element has active class");
}

// Replace class
element.classList.replace("oldClass", "newClass");

// Get all classes
console.log(element.classList); // DOMTokenList
```

### Styling Elements

```javascript
let element = document.querySelector("#myElement");

// Set individual styles
element.style.color = "red";
element.style.backgroundColor = "blue";
element.style.fontSize = "16px";
element.style.margin = "10px";

// Set multiple styles at once
element.style.cssText = "color: red; background-color: blue; font-size: 16px;";

// Get computed styles
let styles = window.getComputedStyle(element);
console.log(styles.color);
console.log(styles.fontSize);
```

## Adding and Removing Elements

### Inserting Elements

```javascript
let parent = document.querySelector("#parent");
let newElement = document.createElement("div");
newElement.textContent = "New Element";

// Append as last child
parent.appendChild(newElement);

// Prepend as first child (modern)
parent.prepend(newElement);

// Insert before specific element
let referenceElement = document.querySelector("#reference");
parent.insertBefore(newElement, referenceElement);

// Modern insertion methods
parent.append("Text", newElement); // Can insert text and elements
parent.prepend("First text");
referenceElement.before(newElement); // Insert before reference
referenceElement.after(newElement); // Insert after reference
```

### Moving Elements

```javascript
let element = document.querySelector("#moveMe");
let newParent = document.querySelector("#newParent");

// Moving (not copying) - removes from old location
newParent.appendChild(element);
```

### Cloning Elements

```javascript
let original = document.querySelector("#original");

// Shallow clone (element only, no children)
let shallowClone = original.cloneNode(false);

// Deep clone (element and all children)
let deepClone = original.cloneNode(true);

document.body.appendChild(deepClone);
```

### Removing Elements

```javascript
let element = document.querySelector("#removeMe");

// Modern way
element.remove();

// Traditional way
element.parentNode.removeChild(element);

// Remove all children
let parent = document.querySelector("#parent");
parent.innerHTML = ""; // Quick but can cause memory leaks

// Better way to remove all children
while (parent.firstChild) {
  parent.removeChild(parent.firstChild);
}
```

## Event Handling

### Adding Event Listeners

```javascript
let button = document.querySelector("#myButton");

// Basic event listener
button.addEventListener("click", function () {
  console.log("Button clicked!");
});

// Arrow function
button.addEventListener("click", () => {
  console.log("Button clicked!");
});

// Named function
function handleClick(event) {
  console.log("Button clicked!", event);
}
button.addEventListener("click", handleClick);

// Event with options
button.addEventListener("click", handleClick, {
  once: true, // Execute only once
  passive: true, // Never calls preventDefault
  capture: true, // Execute during capture phase
});
```

### Common Events

```javascript
let input = document.querySelector("#myInput");
let form = document.querySelector("#myForm");

// Form events
form.addEventListener("submit", (e) => {
  e.preventDefault(); // Prevent default form submission
  console.log("Form submitted");
});

// Input events
input.addEventListener("input", (e) => {
  console.log("Input value:", e.target.value);
});

input.addEventListener("focus", () => {
  console.log("Input focused");
});

input.addEventListener("blur", () => {
  console.log("Input lost focus");
});

// Mouse events
button.addEventListener("mouseover", () => {
  console.log("Mouse over button");
});

button.addEventListener("mouseout", () => {
  console.log("Mouse left button");
});

// Keyboard events
document.addEventListener("keydown", (e) => {
  console.log("Key pressed:", e.key);
  if (e.key === "Escape") {
    console.log("Escape key pressed");
  }
});
```

### Event Delegation

```javascript
// Handle clicks on dynamically added elements
let container = document.querySelector("#container");

container.addEventListener("click", (e) => {
  if (e.target.classList.contains("button")) {
    console.log("Button clicked:", e.target.textContent);
  }

  if (e.target.matches(".delete-btn")) {
    e.target.parentElement.remove();
  }
});
```

## Practical Examples

### Dynamic List Management

```javascript
// Add items to list
function addItem(text) {
  let list = document.querySelector("#todoList");
  let item = document.createElement("li");

  item.innerHTML = `
        <span>${text}</span>
        <button class="delete-btn">Delete</button>
    `;

  list.appendChild(item);
}

// Event delegation for delete buttons
document.querySelector("#todoList").addEventListener("click", (e) => {
  if (e.target.classList.contains("delete-btn")) {
    e.target.parentElement.remove();
  }
});

// Add new item
document.querySelector("#addBtn").addEventListener("click", () => {
  let input = document.querySelector("#todoInput");
  if (input.value.trim()) {
    addItem(input.value.trim());
    input.value = "";
  }
});
```

### Form Validation

```javascript
let form = document.querySelector("#myForm");
let emailInput = document.querySelector("#email");
let errorDiv = document.querySelector("#error");

function showError(message) {
  errorDiv.textContent = message;
  errorDiv.style.color = "red";
}

function clearError() {
  errorDiv.textContent = "";
}

emailInput.addEventListener("input", () => {
  let email = emailInput.value;
  let emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

  if (email && !emailRegex.test(email)) {
    showError("Please enter a valid email address");
  } else {
    clearError();
  }
});

form.addEventListener("submit", (e) => {
  let email = emailInput.value;

  if (!email) {
    e.preventDefault();
    showError("Email is required");
  }
});
```

### Toggle Visibility

```javascript
function createToggle(buttonSelector, targetSelector) {
  let button = document.querySelector(buttonSelector);
  let target = document.querySelector(targetSelector);

  button.addEventListener("click", () => {
    if (target.style.display === "none") {
      target.style.display = "block";
      button.textContent = "Hide";
    } else {
      target.style.display = "none";
      button.textContent = "Show";
    }
  });
}

createToggle("#toggleBtn", "#content");
```

## Performance Tips

1. **Cache DOM queries**: Store frequently accessed elements in variables
2. **Use event delegation**: Instead of adding listeners to many elements
3. **Batch DOM operations**: Make multiple changes at once
4. **Use DocumentFragment**: For inserting multiple elements

```javascript
// Good: Cache elements
let container = document.querySelector("#container");
for (let i = 0; i < 1000; i++) {
  let div = document.createElement("div");
  container.appendChild(div);
}

// Better: Use DocumentFragment
let fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  let div = document.createElement("div");
  fragment.appendChild(div);
}
container.appendChild(fragment); // Single DOM operation
```

## Best Practices

1. **Always check if elements exist** before manipulating them
2. **Use semantic HTML** and enhance with JavaScript
3. **Separate concerns**: Keep styling in CSS, behavior in JavaScript
4. **Use modern methods**: `querySelector` over `getElementById`
5. **Handle errors gracefully**: Check for null elements
6. **Use event delegation** for dynamic content
7. **Avoid `innerHTML` with user input** (XSS vulnerability)
8. **Prefer `textContent` over `innerText`** for better performance
