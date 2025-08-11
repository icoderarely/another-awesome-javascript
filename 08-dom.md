# DOM – Document Object Model

When JS runs in a web browser, we have:

**Root object:** `window`  
- It is a [global](global.md) object.  
- It represents the **browser window** and provides methods to control it.

---

## `window` consists of three parts

### 1. DOM
Represents all page content as objects that can be modified.  
Describes document structure, manipulations, and events.  
The `document` object is the main entry point to the page – you can change or create anything on the page using it.

```javascript
document.body.style.background = "#222";
```

---

### 2. BOM
Represents additional objects provided by the browser (host environment) for working with everything except the document.

Examples:  
- **navigator** – Provides information about the browser and OS  
  - `navigator.userAgent`: About the current browser  
  - `navigator.platform`: About the OS (`win` / `mac` / `linux`)
- **location** – Allows reading the current URL and redirecting the browser to a new one

```javascript
alert(location.href); // shows current URL
if (confirm("Go to Wikipedia?")) {
  location.href = "https://wikipedia.org"; // redirect
}
```

Other BOM features:  
- `alert`, `confirm`, `prompt` – Browser methods for communicating with the user (not directly related to the document)  
- `navigator`, `screen`, `location`, `frames`, `history`, `XMLHttpRequest`

---

### 3. JavaScript Core
Includes built-in objects and features like:  
`Object`, `Array`, `Function`, etc.

---

## DOM Tree

The backbone of an HTML document is **tags**.  
According to the DOM, every HTML tag is an **object**.  
Nested tags are called **children** of the enclosing tag, and the **text** inside a tag is also an object.  
Every tree node in the DOM is an object.

For example, `document.body` is an object representing the `<body>` tag.

---

### Spaces and Newlines in the DOM
Spaces and newlines are valid characters — they form **text nodes** and become part of the DOM.

```html
<head>
    <title>Google</title>
</head>
```
In this snippet:  
- `<head>` contains whitespace before `<title>`, which becomes a `#text` node containing a newline and spaces.  
- These whitespace text nodes are usually hidden in browser developer tools to save screen space.

---

### Types of DOM Nodes
There are 12 node types, but in practice, we mostly work with **4**:

1. **Document** – Entry point into the DOM.
2. **Element** – HTML tags, the building blocks of the DOM tree.
3. **Text** – `#text` nodes containing text (including whitespace).
4. **Comment** – `#comment` nodes containing HTML comments.

**Why comments?**  
Rule: *If something exists in HTML, it must also exist in the DOM Tree.*

---

### DOM Collections
List like object returned by certain DOM methods or properties that contains nodes from the document. 
We can access using index (`collection[0]`) and they have `.length` property but they are not arrays -> We cannot use array methods unless we convert them into one, they are special objects maintained by browser to represetn groups of nodes. 

#### Examples of DOM Collections
1. **HTMLCollection** - (^live) Returned by methods 
    a. `document.getElementsByTagName()`
    b. `document.getElementsByClassName()`
    c. `element.children`
2. **NodeList** - (live except `querySelectorAll()`) Returned by
    a. `document.querySelectorAll()` (static NodeList)
    b. `element.childNodes` (live NodeList in some cases)
    c. `getElementsByName()` (HTMLCollection-like)

[1] A live DOM collection automatically updates when the underlying DOM changes without you needing to requery.
---

**Interactive DOM Viewer:**  
[Live DOM Viewer – See and edit the DOM in real time](https://software.hixie.ch/utilities/js/live-dom-viewer/)

