# JavaScript Notes — From C++ to React
> Written for developers who know C++ and want to master JS for React.

---

## Table of Contents
1. [Variables](#1-variables)
2. [Data Types](#2-data-types)
3. [Functions](#3-functions)
4. [Arrays](#4-arrays)
5. [Objects](#5-objects)
6. [Destructuring](#6-destructuring)
7. [Spread & Rest](#7-spread--rest)
8. [Loops & Iteration](#8-loops--iteration)
9. [Truthy & Falsy](#9-truthy--falsy)
10. [Nullish & Optional Chaining](#10-nullish--optional-chaining)
11. [Promises & Async/Await](#11-promises--asyncawait)
12. [Modules (Import/Export)](#12-modules-importexport)
13. [Array Methods for React](#13-array-methods-for-react)
14. [Closures](#14-closures)
15. [The `this` Keyword](#15-the-this-keyword)
16. [JS Concepts React Uses Heavily](#16-js-concepts-react-uses-heavily)

---

## 1. Variables

```cpp
// C++
int x = 5;
const int y = 10;
```

```js
// JavaScript
let x = 5;       // mutable, block-scoped (use this mostly)
const y = 10;    // immutable binding, block-scoped (use this by default)
var z = 15;      // OLD, function-scoped — AVOID in modern JS
```

### Rules of thumb
- Always use `const` by default.
- Use `let` only when you need to reassign.
- Never use `var`.

---

## 2. Data Types

```js
// Primitives
let name    = "Rahul";         // string
let age     = 25;              // number (no int/float split!)
let isAdmin = true;            // boolean
let nothing = null;            // intentional empty
let missing = undefined;       // variable declared but not assigned
let id      = Symbol("id");    // unique identifier

// Reference type
let obj = {};    // object
let arr = [];    // array (special object)
```

### typeof
```js
typeof "hello"     // "string"
typeof 42          // "number"
typeof true        // "boolean"
typeof undefined   // "undefined"
typeof null        // "object"  ← famous JS bug, null is NOT an object
typeof {}          // "object"
typeof []          // "object"  ← arrays are objects too!
typeof function(){} // "function"
```

---

## 3. Functions

### 3 ways to write functions

```js
// 1. Function Declaration (hoisted)
function greet(name) {
  return "Hello " + name;
}

// 2. Function Expression (not hoisted)
const greet = function(name) {
  return "Hello " + name;
};

// 3. Arrow Function ← YOU WILL USE THIS MOST IN REACT
const greet = (name) => {
  return "Hello " + name;
};

// Arrow shorthand (single expression — implicit return)
const greet = (name) => "Hello " + name;

// Single param — parentheses optional
const double = n => n * 2;

// No params
const sayHi = () => "Hi!";
```

### Default Parameters
```js
// C++: void greet(string name = "User") {}
const greet = (name = "User") => `Hello ${name}`;
greet();         // "Hello User"
greet("Rahul");  // "Hello Rahul"
```

### Template Literals (String interpolation)
```js
// C++: "Hello " + name
// JS:
const msg = `Hello ${name}, you are ${age} years old`;
```

---

## 4. Arrays

```js
const fruits = ["apple", "banana", "mango"];

fruits[0];            // "apple"
fruits.length;        // 3
fruits.push("grape"); // add to end
fruits.pop();         // remove from end
fruits.unshift("kiwi"); // add to start
fruits.shift();       // remove from start
fruits.includes("banana"); // true
fruits.indexOf("mango");   // 2
```

---

## 5. Objects

Think of JS objects like `structs` or `maps` in C++.

```js
const user = {
  name: "Rahul",
  age: 25,
  isAdmin: true,
  address: {
    city: "Delhi",
    pin: "110001"
  }
};

// Access
user.name;            // "Rahul"
user["name"];         // "Rahul" (bracket notation)
user.address.city;    // "Delhi"

// Add / update
user.email = "rahul@gmail.com";
user.age = 26;

// Delete
delete user.isAdmin;

// Check if key exists
"name" in user;  // true
```

### Shorthand property
```js
const name = "Rahul";
const age = 25;

// Old way
const user = { name: name, age: age };

// Shorthand ← used everywhere in React
const user = { name, age };
```

### Methods in objects
```js
const user = {
  name: "Rahul",
  greet() {
    return `Hi, I'm ${this.name}`;
  }
};
user.greet(); // "Hi, I'm Rahul"
```

---

## 6. Destructuring

This is **very heavily used in React**. Learn it well.

### Array Destructuring
```js
const [a, b, c] = [1, 2, 3];
// a=1, b=2, c=3

// Skip elements
const [first, , third] = [10, 20, 30];
// first=10, third=30

// With default values
const [x = 0, y = 0] = [5];
// x=5, y=0

// Swap variables (no temp needed!)
let p = 1, q = 2;
[p, q] = [q, p];
```

### Object Destructuring
```js
const user = { name: "Rahul", age: 25, city: "Delhi" };

const { name, age } = user;
// name="Rahul", age=25

// Rename while destructuring
const { name: userName, age: userAge } = user;
// userName="Rahul", userAge=25

// Default values
const { name, role = "user" } = user;
// role="user" (since it didn't exist)

// Nested destructuring
const { address: { city } } = { address: { city: "Delhi" } };
```

### In function parameters ← very common in React props
```js
// Instead of this:
function UserCard(props) {
  return props.name + " " + props.age;
}

// Do this:
function UserCard({ name, age }) {
  return name + " " + age;
}
```

---

## 7. Spread & Rest

### Spread `...` — expand
```js
// Arrays
const a = [1, 2, 3];
const b = [4, 5, 6];
const combined = [...a, ...b];  // [1,2,3,4,5,6]

// Copy array
const copy = [...a];  // NOT a = copy (different reference)

// Objects
const user = { name: "Rahul", age: 25 };
const updated = { ...user, age: 26 };  // { name: "Rahul", age: 26 }
// ← This is how you update state in React!
```

### Rest `...` — collect remaining
```js
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
sum(1, 2, 3, 4); // 10

// In destructuring
const [first, ...rest] = [1, 2, 3, 4];
// first=1, rest=[2,3,4]
```

---

## 8. Loops & Iteration

```js
const arr = [10, 20, 30];

// C-style (works but rarely used in modern JS)
for (let i = 0; i < arr.length; i++) { }

// for...of ← iterate values (like range-based for in C++)
for (const val of arr) {
  console.log(val);  // 10, 20, 30
}

// for...in ← iterate keys/indices (use for objects)
const obj = { a: 1, b: 2 };
for (const key in obj) {
  console.log(key, obj[key]);  // a 1, b 2
}

// while
let i = 0;
while (i < 3) { i++; }
```

---

## 9. Truthy & Falsy

In JS, conditions don't require booleans. These are **falsy**:

```js
false
0
""        // empty string
null
undefined
NaN
```

Everything else is **truthy** — including `[]`, `{}`, `"0"`.

```js
if (user) { }         // runs if user is not null/undefined
if (arr.length) { }   // runs if array is not empty
if (!name) { }        // runs if name is empty string, null, or undefined
```

---

## 10. Nullish & Optional Chaining

### Nullish Coalescing `??`
```js
// Returns right side only if left is null or undefined
const name = user.name ?? "Anonymous";

// vs OR ||  which also triggers on 0 and ""
const count = user.count || 10;   // if count is 0, gives 10 (bug!)
const count = user.count ?? 10;   // if count is 0, gives 0 (correct)
```

### Optional Chaining `?.`
```js
// Without it (C++ style thinking):
if (user && user.address && user.address.city) { }

// With optional chaining:
const city = user?.address?.city;   // undefined if any part is null
const len = arr?.length;
const result = obj?.method?.();     // call method if it exists
```

---

## 11. Promises & Async/Await

### The problem: JS is single-threaded and non-blocking
```js
// This does NOT wait — callback hell in old JS
fetchData(function(data) {
  processData(data, function(result) {
    saveResult(result, function() { });
  });
});
```

### Promise
```js
const promise = fetch("https://api.example.com/users");

promise
  .then(response => response.json())   // runs on success
  .then(data => console.log(data))
  .catch(error => console.error(error)); // runs on error
```

### Async/Await ← use this always
```js
// Mark function as async
const getUsers = async () => {
  try {
    const response = await fetch("https://api.example.com/users");
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
};

getUsers();
```

> `await` pauses execution inside the async function only — the rest of the program keeps running.

---

## 12. Modules (Import/Export)

### Named Export
```js
// utils.js
export const add = (a, b) => a + b;
export const multiply = (a, b) => a * b;

// main.js
import { add, multiply } from "./utils";
import { add as sum } from "./utils";  // rename
```

### Default Export
```js
// Button.js
const Button = () => <button>Click</button>;
export default Button;

// App.js
import Button from "./Button";       // any name works
import MyBtn from "./Button";        // also valid
```

### Mixed
```js
export default function App() {}
export const version = "1.0";

import App, { version } from "./App";
```

---

## 13. Array Methods for React

These are the **most important** methods for React. Master them.

### `.map()` — transform each element → returns new array
```js
// Used in React to render lists
const numbers = [1, 2, 3];
const doubled = numbers.map(n => n * 2);  // [2, 4, 6]

// React usage:
const users = [{ id: 1, name: "Rahul" }, { id: 2, name: "Priya" }];
return (
  <ul>
    {users.map(user => <li key={user.id}>{user.name}</li>)}
  </ul>
);
```

### `.filter()` — keep elements that pass test → returns new array
```js
const nums = [1, 2, 3, 4, 5];
const evens = nums.filter(n => n % 2 === 0);  // [2, 4]

// React: remove an item from list
const removeUser = (id) => {
  setUsers(users.filter(user => user.id !== id));
};
```

### `.reduce()` — reduce array to single value
```js
const nums = [1, 2, 3, 4];
const sum = nums.reduce((accumulator, current) => accumulator + current, 0);
// sum = 10

// Build an object from array
const users = [{ id: 1, name: "Rahul" }, { id: 2, name: "Priya" }];
const userMap = users.reduce((acc, user) => {
  acc[user.id] = user;
  return acc;
}, {});
// { 1: { id:1, name:"Rahul" }, 2: { id:2, name:"Priya" } }
```

### `.find()` — returns first match or undefined
```js
const user = users.find(u => u.id === 2);  // { id:2, name:"Priya" }
```

### `.findIndex()` — returns index of first match or -1
```js
const idx = users.findIndex(u => u.id === 2);  // 1
```

### `.some()` / `.every()`
```js
const nums = [1, 2, 3, 4];
nums.some(n => n > 3);   // true  (at least one)
nums.every(n => n > 0);  // true  (all pass)
```

### `.flat()` / `.flatMap()`
```js
[[1, 2], [3, 4]].flat();              // [1, 2, 3, 4]
[1, 2, 3].flatMap(n => [n, n * 2]);  // [1,2, 2,4, 3,6]
```

---

## 14. Closures

A function that **remembers** variables from its outer scope even after the outer function has finished.

```js
function makeCounter() {
  let count = 0;           // outer variable
  return function() {      // inner function closes over `count`
    count++;
    return count;
  };
}

const counter = makeCounter();
counter();  // 1
counter();  // 2
counter();  // 3
```

> React's `useState` and event handlers use closures heavily. When you write `onClick={() => setCount(count + 1)}`, that arrow function closes over `count`.

---

## 15. The `this` Keyword

Unlike C++, `this` in JS depends on **how** the function is called.

```js
const user = {
  name: "Rahul",
  greet: function() {
    console.log(this.name);  // "Rahul" ✓
  },
  greetArrow: () => {
    console.log(this.name);  // undefined ✗ — arrow functions don't have their own `this`
  }
};
```

> **In React components (functional):** You don't use `this` at all. That's one reason React moved to functional components + hooks.

---

## 16. JS Concepts React Uses Heavily

| Concept | Where in React |
|---|---|
| Arrow functions | Event handlers, callbacks |
| Destructuring | Props `({ name, age })`, useState `const [x, setX]` |
| Spread operator | Updating state objects `{ ...state, key: val }` |
| Template literals | Dynamic classNames, strings |
| Array `.map()` | Rendering lists |
| Array `.filter()` | Removing items from state |
| Ternary operator | Conditional rendering `{isLoading ? <Spinner/> : <Data/>}` |
| Short-circuit `&&` | Conditional rendering `{isAdmin && <AdminPanel/>}` |
| Optional chaining | Safely accessing nested data `user?.profile?.avatar` |
| Async/Await | API calls inside `useEffect` |
| Modules | Every component file uses import/export |
| Closures | useState setters, event handlers |

### Ternary & Short-circuit (bonus)
```js
// Ternary — inline if-else
const label = isAdmin ? "Admin" : "User";

// Short-circuit — render only if true
{isLoggedIn && <Dashboard />}

// Nullish for defaults
const name = user?.name ?? "Guest";
```

---

## Quick Reference Cheatsheet

```js
// Variables
const x = 5;  let y = 10;

// Arrow function
const fn = (a, b) => a + b;

// Template literal
`Hello ${name}`

// Destructuring
const { a, b } = obj;
const [x, y] = arr;

// Spread
const newObj = { ...obj, key: val };
const newArr = [...arr, newItem];

// Optional chaining + nullish
const val = obj?.key?.nested ?? "default";

// Async/Await
const data = await fetch(url).then(r => r.json());

// Array methods
arr.map(x => x * 2)
arr.filter(x => x > 0)
arr.reduce((acc, x) => acc + x, 0)
arr.find(x => x.id === id)
```

---

*These notes cover everything you need to write React comfortably. Practice each section with small examples and revisit when you hit confusion in React.*
