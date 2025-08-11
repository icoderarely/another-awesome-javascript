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

Understanding the different ways to manipulate element content is crucial for effective DOM manipulation. There are several properties available, each with specific behaviors and use cases.

#### Content Properties Overview

**innerHTML**: Gets or sets the HTML markup inside an element, including both tags and text content. This property can change the structure of the DOM tree.

**outerHTML**: Gets or sets the entire element itself, including the element's own tags. When you set outerHTML, you can completely change the element from one type to another (e.g., from `<p>` to `<div>`).

**textContent**: Gets or sets the raw text content of an element and all its descendants. It ignores CSS rules and layout, giving you the actual text nodes regardless of whether they are hidden or displayed.

**innerText**: Gets or sets the visible text content only, considering CSS styling. If an element is hidden with CSS, innerText won't include its content.

**outerText**: Gets or sets the text content and replaces the entire element when set. When you assign a value to outerText, the original element is replaced with a text node.

#### Detailed Comparison and Examples

| Property      | Includes HTML | Considers CSS | Scope          | Security    |
| ------------- | ------------- | ------------- | -------------- | ----------- |
| `innerHTML`   | ✅ Yes        | ❌ No         | Inside element | ⚠️ XSS risk |
| `outerHTML`   | ✅ Yes        | ❌ No         | Entire element | ⚠️ XSS risk |
| `textContent` | ❌ No         | ❌ No         | Inside element | ✅ Safe     |
| `innerText`   | ❌ No         | ✅ Yes        | Inside element | ✅ Safe     |
| `outerText`   | ❌ No         | ✅ Yes        | Entire element | ✅ Safe     |

```javascript
let element = document.querySelector("#myElement");

// innerHTML - HTML content inside element (includes tags + text)
element.innerHTML = "Hello <strong>World</strong>"; // Renders as HTML
console.log(element.innerHTML); // "Hello <strong>World</strong>"

// outerHTML - The element itself (can change element type)
console.log(element.outerHTML); // "<div id='myElement'>Hello <strong>World</strong></div>"
element.outerHTML = '<p id="newElement">New content</p>'; // Changes div to p

// textContent - Raw content ignoring CSS (gives text node regardless of display)
element.textContent = "Hello <strong>World</strong>"; // Shows literal text
console.log(element.textContent); // "Hello <strong>World</strong>" (literal, even if hidden)

// innerText - Visible text content (considers CSS style)
element.innerText = "Visible text only";
console.log(element.innerText); // Only visible text (excludes hidden elements)

// outerText - Text + element replacement
element.outerText = "Just text"; // Element is completely replaced with text node
```

#### Practical Differences in Action

```html
<!-- HTML structure for demonstration -->
<div id="example">
  <p>Visible paragraph</p>
  <p style="display: none;">Hidden paragraph</p>
  <script>
    console.log("script content");
  </script>
</div>
```

```javascript
let div = document.querySelector("#example");

// textContent - Gets ALL text content, ignores CSS rules and layout
console.log(div.textContent);
// Output: "Visible paragraph Hidden paragraph console.log('script content')"

// innerText - Gets only VISIBLE text, considers CSS styling
console.log(div.innerText);
// Output: "Visible paragraph" (hidden content excluded)

// innerHTML - Gets HTML markup with tags
console.log(div.innerHTML);
// Output: "<p>Visible paragraph</p><p style="display: none;">Hidden paragraph</p><script>console.log('script content')</script>"
```

#### Best Practices for Content Manipulation

```javascript
// ✅ Good: Use textContent for plain text (prevents XSS attacks)
element.textContent = userInput; // Safe even with malicious input

// ❌ Dangerous: Using innerHTML with user input
element.innerHTML = userInput; // Can execute malicious scripts

// ✅ Good: Use innerHTML only for trusted content
element.innerHTML = '<span class="highlight">Trusted content</span>';

// ✅ Good: Use innerText when you need only visible text
let visibleText = element.innerText; // Respects CSS display rules

// ✅ Good: Use outerHTML to completely replace elements
oldElement.outerHTML = '<div class="new-class">Replacement content</div>';
```

### Attribute Manipulation

Attributes control element behavior and provide additional information about elements.

#### Core Attribute Methods

```javascript
let img = document.querySelector("img");

// setAttribute - Set attribute value
img.setAttribute("src", "image.jpg");
img.setAttribute("alt", "Description");
img.setAttribute("data-id", "123");

// getAttribute - Get attribute value
console.log(img.getAttribute("src")); // "image.jpg"
console.log(img.getAttribute("data-id")); // "123"

// removeAttribute - Remove attribute completely
img.removeAttribute("data-id");

// hasAttribute - Check if attribute exists
if (img.hasAttribute("alt")) {
  console.log("Alt attribute exists");
}
```

#### Direct Property Access vs Attributes

```javascript
let img = document.querySelector("img");

// Direct property access (recommended for standard attributes)
img.src = "newimage.jpg"; // Sets src attribute
img.id = "myImage"; // Sets id attribute
img.className = "responsive"; // Sets class attribute (note: className, not class)

// Property access vs getAttribute
img.src = "image.jpg";
console.log(img.src); // "http://domain.com/image.jpg" (absolute URL)
console.log(img.getAttribute("src")); // "image.jpg" (as written in HTML)

// Custom attributes (data-*) - use getAttribute/setAttribute
img.setAttribute("data-category", "nature");
console.log(img.dataset.category); // "nature" (using dataset API)
console.log(img.getAttribute("data-category")); // "nature"
```

#### Working with Data Attributes

```javascript
let element = document.querySelector("#myElement");

// Using dataset API (recommended for data-* attributes)
element.dataset.userId = "123"; // Sets data-user-id="123"
element.dataset.itemCount = "5"; // Sets data-item-count="5"

console.log(element.dataset.userId); // "123"
console.log(element.dataset.itemCount); // "5"

// Converts kebab-case to camelCase automatically
element.dataset.fullName = "John Doe"; // Sets data-full-name="John Doe"

// Get all data attributes
console.log(element.dataset); // DOMStringMap object with all data-* attributes
```

#### Boolean Attributes

```javascript
let input = document.querySelector('input[type="checkbox"]');
let button = document.querySelector("button");

// Boolean attributes (present = true, absent = false)
input.disabled = true; // Adds disabled attribute
input.disabled = false; // Removes disabled attribute

// Using setAttribute for boolean attributes
button.setAttribute("disabled", ""); // Disables button
button.removeAttribute("disabled"); // Enables button

// Check boolean attributes
if (input.hasAttribute("disabled")) {
  console.log("Input is disabled");
}
```

### Working with Classes and Styles

#### Understanding classList

`classList` is a powerful DOM property that provides a clean and efficient way to add, remove, or toggle CSS classes on DOM elements. It's much better than manipulating the `className` property directly because it treats classes as individual items rather than a single string.

**What classList does:**

- Provides methods to add, remove, or toggle classes into an element
- Treats each class as a separate entity
- Automatically handles spacing and duplicates
- Safer and more reliable than string manipulation

```javascript
// Basic classList operations
let element = document.querySelector(".myElement");

// Add classes - classList is a way to add classes
element.classList.add("newClass"); // Add single class
element.classList.add("class1", "class2", "class3"); // Add multiple classes

// Remove classes - classList is a way to remove classes
element.classList.remove("oldClass"); // Remove single class
element.classList.remove("class1", "class2"); // Remove multiple classes

// Toggle class - classList is a way to toggle classes
element.classList.toggle("active"); // Adds if absent, removes if present
// Returns true if added, false if removed

// Check if class exists
if (element.classList.contains("active")) {
  console.log("Element has active class");
}

// Replace class
element.classList.replace("oldClass", "newClass"); // Replace oldClass with newClass
```

#### Quick classList Examples (Following Your Pattern)

```javascript
// Following the pattern: document.body.classList.method('className')
document.body.classList.add("dark-theme"); // Add dark theme
document.body.classList.remove("light-theme"); // Remove light theme
document.body.classList.toggle("mobile-view"); // Toggle mobile view
```

#### Advanced Class Manipulation

```javascript
let element = document.querySelector("#myElement");

// Get all classes as array
let classArray = Array.from(element.classList);
console.log(classArray); // ['class1', 'class2', 'class3']

// Get number of classes
console.log(element.classList.length); // 3

// Access class by index
console.log(element.classList[0]); // 'class1'

// Iterate through classes
element.classList.forEach((className) => {
  console.log(`Class: ${className}`);
});

// Toggle with condition
let isActive = true;
element.classList.toggle("active", isActive); // Adds 'active' if isActive is true
```

#### Understanding the style Attribute

The `style` attribute can be used to change the CSS properties of a selected DOM node element directly. This allows you to modify the appearance and layout of elements dynamically using JavaScript.

**What the style attribute does:**

- Provides direct access to inline CSS styles
- Changes are applied immediately
- Higher specificity than external stylesheets
- Each CSS property becomes a JavaScript property (camelCase)

```javascript
let element = document.querySelector("#myElement");

// Set individual CSS properties using the style attribute
element.style.color = "red";
element.style.backgroundColor = "blue"; // Note: camelCase for CSS properties
element.style.fontSize = "16px";
element.style.margin = "10px";
element.style.borderRadius = "5px";

// CSS properties with hyphens become camelCase
element.style.borderTopWidth = "2px"; // border-top-width
element.style.textAlign = "center"; // text-align
element.style.boxShadow = "0 2px 4px rgba(0,0,0,0.1)"; // box-shadow
```

#### Setting Multiple Styles at Once

```javascript
let element = document.querySelector("#myElement");

// Method 1: Using cssText (overwrites existing styles)
element.style.cssText = "color: red; background-color: blue; font-size: 16px;";

// Method 2: Using Object.assign
Object.assign(element.style, {
  color: "red",
  backgroundColor: "blue",
  fontSize: "16px",
  padding: "10px",
});

// Method 3: Custom function for multiple styles
function setStyles(element, styles) {
  Object.assign(element.style, styles);
}

setStyles(element, {
  width: "200px",
  height: "100px",
  border: "1px solid #ccc",
  borderRadius: "8px",
});
```

#### Getting Computed Styles

```javascript
let element = document.querySelector("#myElement");

// Get computed styles (actual values as rendered by the browser)
let computedStyles = window.getComputedStyle(element);

console.log(computedStyles.color); // rgb(255, 0, 0)
console.log(computedStyles.fontSize); // 16px
console.log(computedStyles.display); // block
console.log(computedStyles.width); // 200px

// Get specific computed style
let fontSize = getComputedStyle(element).fontSize;

// Get computed style with pseudo-elements
let beforeStyles = getComputedStyle(element, "::before");
console.log(beforeStyles.content);
```

#### Style vs classList: When to Use What

```javascript
// ✅ Good: Use classList for predefined styles
element.classList.add("highlighted-element");

// ❌ Avoid: Multiple individual style changes
element.style.color = "red";
element.style.backgroundColor = "yellow";
element.style.border = "2px solid black";

// ✅ Good: Use style for dynamic values
element.style.width = `${calculatedWidth}px`;
element.style.transform = `translateX(${position}px)`;

// ✅ Good: Batch style changes when needed
Object.assign(element.style, {
  color: "red",
  backgroundColor: "yellow",
  border: "2px solid black",
});
```

#### Practical Examples

```javascript
// Theme switcher using classList
function toggleTheme() {
  document.body.classList.toggle("dark-theme");
  document.body.classList.toggle("light-theme");
}

// Loading state management
function showLoading(element) {
  element.classList.add("loading", "disabled");
  element.textContent = "Loading...";
}

function hideLoading(element, originalText) {
  element.classList.remove("loading", "disabled");
  element.textContent = originalText;
}

// Dynamic positioning using style
function moveElement(element, x, y) {
  element.style.transform = `translate(${x}px, ${y}px)`;
}

// Conditional styling
function updateStatus(element, isOnline) {
  element.classList.remove("online", "offline");
  element.classList.add(isOnline ? "online" : "offline");

  // Dynamic color based on status
  element.style.color = isOnline ? "green" : "red";
}
```

### Styling Elements

Direct style manipulation allows you to change CSS properties dynamically using JavaScript.

#### Setting Individual Styles

```javascript
let element = document.querySelector("#myElement");

// Set individual CSS properties
element.style.color = "red";
element.style.backgroundColor = "blue"; // Note: camelCase for CSS properties
element.style.fontSize = "16px";
element.style.margin = "10px";
element.style.borderRadius = "5px";

// CSS properties with hyphens become camelCase
element.style.borderTopWidth = "2px"; // border-top-width
element.style.textAlign = "center"; // text-align
element.style.boxShadow = "0 2px 4px rgba(0,0,0,0.1)"; // box-shadow
```

#### Setting Multiple Styles

```javascript
let element = document.querySelector("#myElement");

// Method 1: Using cssText (overwrites existing styles)
element.style.cssText = "color: red; background-color: blue; font-size: 16px;";

// Method 2: Using Object.assign
Object.assign(element.style, {
  color: "red",
  backgroundColor: "blue",
  fontSize: "16px",
  padding: "10px",
});

// Method 3: Custom function for multiple styles
function setStyles(element, styles) {
  Object.assign(element.style, styles);
}

setStyles(element, {
  width: "200px",
  height: "100px",
  border: "1px solid #ccc",
  borderRadius: "8px",
});
```

#### Getting Computed Styles

```javascript
let element = document.querySelector("#myElement");

// Get computed styles (actual values as rendered)
let computedStyles = window.getComputedStyle(element);

console.log(computedStyles.color); // rgb(255, 0, 0)
console.log(computedStyles.fontSize); // 16px
console.log(computedStyles.display); // block
console.log(computedStyles.width); // 200px

// Get specific computed style
let fontSize = getComputedStyle(element).fontSize;

// Get computed style with pseudo-elements
let beforeStyles = getComputedStyle(element, "::before");
console.log(beforeStyles.content);
```

#### Style Property vs Computed Styles

```javascript
let element = document.querySelector("#myElement");

// Direct style (only inline styles)
element.style.color = "red";
console.log(element.style.color); // "red"

// Computed style (includes CSS from stylesheets)
let computed = getComputedStyle(element);
console.log(computed.color); // "rgb(255, 0, 0)"

// If no inline style is set
console.log(element.style.fontSize); // "" (empty string)
console.log(computed.fontSize); // "16px" (from CSS or browser default)
```

#### Removing Styles

```javascript
let element = document.querySelector("#myElement");

// Remove specific style property
element.style.color = ""; // Removes inline color style
element.style.removeProperty("background-color");

// Remove all inline styles
element.style.cssText = "";
element.removeAttribute("style");
```

#### Style Manipulation Best Practices

```javascript
// ✅ Good: Use classes for multiple style changes
element.classList.add("highlighted-element");

// ❌ Avoid: Multiple individual style changes
element.style.color = "red";
element.style.backgroundColor = "yellow";
element.style.border = "2px solid black";

// ✅ Good: Batch style changes
Object.assign(element.style, {
  color: "red",
  backgroundColor: "yellow",
  border: "2px solid black",
});

// ✅ Good: Toggle styles with data attributes or classes
function toggleHighlight(element) {
  element.classList.toggle("highlight");
}

// ✅ Good: Responsive styles with CSS classes
function setResponsiveLayout(element, screenSize) {
  element.className = `layout layout--${screenSize}`;
}
```

## Dynamic DOM Manipulation

Dynamic DOM manipulation is the process of creating, adding, and removing elements from the DOM tree using JavaScript. This is essential for creating interactive web applications that respond to user actions.

### The Process Overview

Before you can add elements to the DOM, you need to follow these steps:

1. **First, select a parent node/element** where you want to add or remove an element
2. **Create the child element** that you want to add to the parent
3. **Configure the element** (set content, attributes, styles)
4. **Insert the element** into the DOM using appropriate methods

### Creating New Elements

The foundation of dynamic manipulation starts with creating elements that you can later insert into the DOM.

```javascript
// Step 1: Create different types of elements
let elem = document.createElement("div"); // Create a div element
elem.textContent = "your_text"; // Set the text content
// So this is the child element that we want to add

// Create other elements
let p = document.createElement("p");
let img = document.createElement("img");
let button = document.createElement("button");
let ul = document.createElement("ul");

// Create text nodes directly
let textNode = document.createTextNode("Hello World");

// Configure the created elements
div.className = "container";
div.id = "myDiv";

img.src = "image.jpg";
img.alt = "Description";

button.textContent = "Click me";
button.addEventListener("click", () => alert("Clicked!"));
```

### Ways to Add Elements to the DOM

Once you have created and configured your elements, there are several methods to insert them into the DOM tree. Each method has specific characteristics and use cases.

#### Modern Insertion Methods (Recommended)

**1. `append()` - Add as last child**

`append()` is the modern way to insert elements at the end of a parent element.

- **Purpose**: Inserts one or more nodes or strings as the last child
- **Return value**: `undefined`
- **Advantages**: Can append multiple things at once and supports text without `createTextNode()`
- **Flexibility**: Can append multiple things at once

```javascript
let parent = document.querySelector("#parent");
let newElement = document.createElement("div");
newElement.textContent = "New Element";

// append() - Insert at the end (supports multiple nodes and text)
parent.append(newElement); // Add element
parent.append("Text content"); // Add text directly
parent.append(newElement, "More text"); // Add multiple items at once
```

**2. `appendChild()` - Add as last child (Traditional)**

`appendChild()` is the traditional method for adding elements.

- **Purpose**: Inserts a single node at the end
- **Limitation**: Cannot pass plain strings (must wrap them in `document.createTextNode()`)
- **Return value**: Returns the node you appended
- **Restriction**: Can only append one node at a time

```javascript
let parent = document.querySelector("#parent");
let newElement = document.createElement("div");

// appendChild() - Traditional method (single node only)
let appendedNode = parent.appendChild(newElement); // Returns the appended node

// For text content with appendChild, you need createTextNode
let textNode = document.createTextNode("Text content");
parent.appendChild(textNode);
```

**3. `prepend()` - Add at the start as first child**

`prepend()` allows you to insert elements at the beginning of a parent element.

```javascript
// prepend() - Insert at the beginning
parent.prepend(newElement); // Add element as first child
parent.prepend("First text"); // Add text as first child
```

#### Comparison: append() vs appendChild()

| Feature             | `append()`              | `appendChild()`              |
| ------------------- | ----------------------- | ---------------------------- |
| **Browser Support** | Modern (ES2017)         | All browsers                 |
| **Return Value**    | `undefined`             | Appended node                |
| **Multiple Items**  | ✅ Yes                  | ❌ No                        |
| **Text Support**    | ✅ Yes                  | ❌ No (needs createTextNode) |
| **Node Types**      | Elements, text, strings | Elements only                |

```javascript
let parent = document.querySelector("#parent");
let elem1 = document.createElement("div");
let elem2 = document.createElement("span");

// append() - Modern and flexible
parent.append(elem1, "Some text", elem2); // ✅ Works

// appendChild() - Traditional, more limited
parent.appendChild(elem1); // ✅ Works
// parent.appendChild('text');                // ❌ Error - strings not allowed
// parent.appendChild(elem1, elem2);          // ❌ Error - only one argument
```

### Removing Elements from the DOM

There are several approaches to remove elements, each with different use cases.

#### Modern Element Removal

**`elem.remove()` - Simple and Clean**

The modern `remove()` method is the simplest way to remove an element from the DOM.

```javascript
let element = document.querySelector("#removeMe");
element.remove(); // Removes the element completely

// Always check if element exists
if (element) {
  element.remove();
}
```

#### Traditional Element Removal

**For lists and specific scenarios:**

When working with lists or when you need more control, you might need to use the traditional approach.

```javascript
// For list -> select ul and li (by default it will select the first one)
let ul = document.querySelector("#myList");
let li = ul.querySelector("li"); // Selects the first li

ul.removeChild(li); // Remove the specific li from ul
```

#### Removing Multiple Children

```javascript
// Remove all children from a parent
let parent = document.querySelector("#parent");

// Method 1: Using remove() on each child
Array.from(parent.children).forEach((child) => child.remove());

// Method 2: Traditional approach
while (parent.firstChild) {
  parent.removeChild(parent.firstChild);
}
```

### Practical Element Creation Examples

```javascript
// Create a complete card element
function createUserCard(user) {
  let card = document.createElement("div");
  card.className = "user-card";

  let name = document.createElement("h3");
  name.textContent = user.name;

  let email = document.createElement("p");
  email.textContent = user.email;
  email.className = "email";

  let avatar = document.createElement("img");
  avatar.src = user.avatar;
  avatar.alt = `${user.name}'s avatar`;
  avatar.className = "avatar";

  // Assemble the card using append (can add multiple at once)
  card.append(avatar, name, email);

  return card;
}

// Usage
let user = {
  name: "John Doe",
  email: "john@example.com",
  avatar: "avatar.jpg",
};
let userCard = createUserCard(user);
document.querySelector("#users").appendChild(userCard);
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

There are several ways to remove elements from the DOM, each with different use cases and considerations.

#### Modern Element Removal

```javascript
let element = document.querySelector("#removeMe");

// remove() - Modern, clean, and simple
element.remove(); // Removes the element completely

// Check if element exists before removing
if (element) {
  element.remove();
}
```

#### Traditional Element Removal

```javascript
let element = document.querySelector("#removeMe");

// removeChild() - Traditional method (requires parent)
if (element && element.parentNode) {
  element.parentNode.removeChild(element); // Returns the removed element
}

// Store reference to removed element
let removedElement = element.parentNode.removeChild(element);
```

#### Removing Multiple Elements

```javascript
// Remove all elements with a specific class
let elements = document.querySelectorAll(".remove-me");
elements.forEach((element) => element.remove());

// Remove all children from a parent
let parent = document.querySelector("#parent");

// Method 1: Using innerHTML (quick but can cause memory leaks)
parent.innerHTML = ""; // ⚠️ Can cause memory leaks with event listeners

// Method 2: Remove children one by one (safer)
while (parent.firstChild) {
  parent.removeChild(parent.firstChild);
}

// Method 3: Using modern approach
while (parent.firstElementChild) {
  parent.firstElementChild.remove();
}

// Method 4: Convert to array and remove all
Array.from(parent.children).forEach((child) => child.remove());
```

#### Removing Specific Child Elements

```javascript
let ul = document.querySelector("#myList");
let listItems = ul.querySelectorAll("li");

// Remove specific list item by index
if (listItems[2]) {
  listItems[2].remove(); // Remove third item
}

// Remove items based on content
listItems.forEach((item) => {
  if (item.textContent.includes("delete")) {
    item.remove();
  }
});

// Using removeChild for lists
let firstItem = ul.querySelector("li");
if (firstItem) {
  ul.removeChild(firstItem); // Remove first li element
}
```

#### Safe Removal Patterns

```javascript
// Safe removal function
function safeRemove(selector) {
  let element = document.querySelector(selector);
  if (element) {
    element.remove();
    return true;
  }
  return false;
}

// Usage
if (safeRemove("#temporary-element")) {
  console.log("Element removed successfully");
}

// Remove with cleanup (for elements with event listeners)
function removeWithCleanup(element) {
  if (element) {
    // Remove event listeners if needed
    element.removeEventListener("click", someHandler);

    // Clear any timers or intervals
    if (element.dataset.timerId) {
      clearTimeout(element.dataset.timerId);
    }

    // Remove the element
    element.remove();
  }
}
```

#### Conditional Removal

```javascript
// Remove elements based on conditions
function removeOldItems() {
  let items = document.querySelectorAll(".item");

  items.forEach((item) => {
    let timestamp = parseInt(item.dataset.timestamp);
    let now = Date.now();
    let dayInMs = 24 * 60 * 60 * 1000;

    // Remove items older than 7 days
    if (now - timestamp > 7 * dayInMs) {
      item.remove();
    }
  });
}

// Remove empty elements
function removeEmptyElements(selector) {
  let elements = document.querySelectorAll(selector);

  elements.forEach((element) => {
    if (!element.textContent.trim() && !element.children.length) {
      element.remove();
    }
  });
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
