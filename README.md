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

---

# TypeScript for React
> TypeScript = JavaScript + Types. Since you know C++, types will feel natural.

---

## Table of Contents (TS)
17. [Why TypeScript](#17-why-typescript)
18. [Basic Types](#18-basic-types)
19. [Interfaces & Type Aliases](#19-interfaces--type-aliases)
20. [Functions in TS](#20-functions-in-ts)
21. [Arrays & Tuples](#21-arrays--tuples)
22. [Union & Intersection Types](#22-union--intersection-types)
23. [Optional & Readonly](#23-optional--readonly)
24. [Generics](#24-generics)
25. [Enums](#25-enums)
26. [Type Assertions & Type Guards](#26-type-assertions--type-guards)
27. [Typing React Components](#27-typing-react-components)
28. [Typing Props](#28-typing-props)
29. [Typing useState](#29-typing-usestate)
30. [Typing useRef](#30-typing-useref)
31. [Typing Events](#31-typing-events)
32. [Typing API Responses](#32-typing-api-responses)
33. [Utility Types](#33-utility-types)
34. [TS + React Cheatsheet](#34-ts--react-cheatsheet)

---

## 17. Why TypeScript

```cpp
// In C++, this is a compile error:
int x = "hello";  // ❌ caught at compile time
```

```js
// In JS, this is silent:
let x = 5;
x = "hello";  // ✓ no error — bugs hide until runtime
```

```ts
// In TS, this is caught immediately:
let x: number = 5;
x = "hello";  // ❌ Type 'string' is not assignable to type 'number'
```

> TypeScript gives you C++-like type safety in JavaScript. Since you already think in types, TS will feel comfortable.

---

## 18. Basic Types

```ts
// Primitives
let name: string = "Rahul";
let age: number = 25;          // covers int, float, double
let isAdmin: boolean = true;

// Any (avoid — defeats the purpose of TS)
let data: any = "hello";
data = 42;  // no error, but bad practice

// Unknown (safer alternative to any)
let input: unknown = getUserInput();
if (typeof input === "string") {
  input.toUpperCase();  // TS now knows it's a string
}

// Void (function returns nothing)
function logMessage(msg: string): void {
  console.log(msg);
}

// Never (function never returns — throws or infinite loops)
function throwError(msg: string): never {
  throw new Error(msg);
}

// Null & Undefined
let val: null = null;
let missing: undefined = undefined;

// Literal types
let direction: "left" | "right" | "up" | "down" = "left";
let statusCode: 200 | 404 | 500 = 200;
```

---

## 19. Interfaces & Type Aliases

These are the most used TS features in React.

### Interface
```ts
interface User {
  id: number;
  name: string;
  email: string;
}

const user: User = { id: 1, name: "Rahul", email: "r@gmail.com" };
```

### Type Alias
```ts
type User = {
  id: number;
  name: string;
  email: string;
};
```

### Interface vs Type — when to use which?
| | `interface` | `type` |
|---|---|---|
| Objects/shapes | ✅ preferred | ✅ works |
| Can extend | ✅ `extends` | ✅ `&` intersection |
| Union types | ❌ | ✅ only type can |
| Primitives | ❌ | ✅ `type ID = string` |
| React props | ✅ convention | ✅ both fine |

> **Rule of thumb:** Use `interface` for object shapes (props, API responses). Use `type` for unions, primitives, and complex combos.

### Extending Interface
```ts
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const dog: Dog = { name: "Bruno", breed: "Labrador" };
```

### Extending Type (intersection)
```ts
type Animal = { name: string };
type Dog = Animal & { breed: string };
```

---

## 20. Functions in TS

```ts
// Parameter types + return type
function add(a: number, b: number): number {
  return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Optional parameter
function greet(name: string, greeting?: string): string {
  return `${greeting ?? "Hello"}, ${name}`;
}

// Default parameter
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}`;
}

// Function type
type MathFn = (a: number, b: number) => number;
const add: MathFn = (a, b) => a + b;
```

---

## 21. Arrays & Tuples

```ts
// Arrays — two syntaxes (both identical)
const nums: number[] = [1, 2, 3];
const strs: Array<string> = ["a", "b", "c"];

// Array of objects
const users: User[] = [{ id: 1, name: "Rahul", email: "r@g.com" }];

// Tuple — fixed length, fixed types (like std::pair in C++)
const point: [number, number] = [10, 20];
const entry: [string, number] = ["age", 25];

// Named tuple
const point: [x: number, y: number] = [10, 20];

// useState returns a tuple — that's why you destructure it!
const [count, setCount] = useState<number>(0);
// count: number, setCount: (value: number) => void
```

---

## 22. Union & Intersection Types

### Union `|` — can be one of these types
```ts
// Like a type-safe version of a value that can vary
type ID = string | number;
let userId: ID = "abc123";
userId = 42;  // also valid

type Status = "loading" | "success" | "error";
let apiStatus: Status = "loading";
```

### Intersection `&` — must satisfy ALL types
```ts
type HasName = { name: string };
type HasAge  = { age: number };
type Person  = HasName & HasAge;

const p: Person = { name: "Rahul", age: 25 };  // must have both
```

---

## 23. Optional & Readonly

```ts
interface User {
  id: number;
  name: string;
  email?: string;          // optional — string | undefined
  readonly createdAt: Date; // cannot be changed after creation
}

const user: User = { id: 1, name: "Rahul", createdAt: new Date() };
user.email = "r@g.com";    // ✅ fine
user.createdAt = new Date(); // ❌ Error: cannot assign to readonly
```

---

## 24. Generics

Like templates in C++. Write once, work for any type.

```cpp
// C++ template
template<typename T>
T getFirst(vector<T> arr) { return arr[0]; }
```

```ts
// TS generic
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

getFirst<number>([1, 2, 3]);   // returns number
getFirst<string>(["a", "b"]);  // returns string
getFirst([true, false]);        // TS infers boolean automatically
```

### Generic interface
```ts
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// Use for different API responses
type UsersResponse = ApiResponse<User[]>;
type ProductResponse = ApiResponse<Product>;
```

### Generic with constraint
```ts
// T must have a `length` property
function logLength<T extends { length: number }>(val: T): void {
  console.log(val.length);
}

logLength("hello");   // ✅
logLength([1, 2, 3]); // ✅
logLength(42);        // ❌ number has no length
```

---

## 25. Enums

```ts
// Numeric enum (like C++)
enum Direction {
  Up,     // 0
  Down,   // 1
  Left,   // 2
  Right   // 3
}
let dir: Direction = Direction.Up;

// String enum ← preferred in React (readable in DevTools/logs)
enum Status {
  Loading = "LOADING",
  Success = "SUCCESS",
  Error   = "ERROR"
}
let status: Status = Status.Loading; // "LOADING"
```

> **Tip:** Many TS devs prefer `type Status = "loading" | "success" | "error"` over enums in React — it's simpler and works great.

---

## 26. Type Assertions & Type Guards

### Type Assertion (like C++ cast)
```ts
// Tell TS "I know better"
const input = document.getElementById("name") as HTMLInputElement;
input.value;  // ✅ TS knows it's an input now

// Alternative syntax (not usable in .tsx files!)
const input = <HTMLInputElement>document.getElementById("name");
```

### Type Guards — narrow a union type
```ts
type Dog = { bark: () => void };
type Cat = { meow: () => void };
type Pet = Dog | Cat;

// typeof guard
function process(val: string | number) {
  if (typeof val === "string") {
    val.toUpperCase(); // TS knows it's string here
  } else {
    val.toFixed(2);    // TS knows it's number here
  }
}

// instanceof guard
if (error instanceof Error) {
  console.log(error.message);
}

// Custom type guard
function isDog(pet: Pet): pet is Dog {
  return (pet as Dog).bark !== undefined;
}

if (isDog(myPet)) {
  myPet.bark(); // ✅
}
```

---

## 27. Typing React Components

```tsx
// Functional Component — two ways

// 1. Return type inferred (most common, recommended)
const Button = ({ label }: { label: string }) => {
  return <button>{label}</button>;
};

// 2. Explicit FC type (older style — some teams still use it)
import { FC } from "react";
const Button: FC<{ label: string }> = ({ label }) => {
  return <button>{label}</button>;
};
```

> **Tip:** Prefer letting TS infer the return type. Only add `: JSX.Element` or `: ReactNode` if you have a specific reason.

---

## 28. Typing Props

```tsx
// Define props interface separately (cleaner)
interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: "primary" | "secondary" | "danger";
  disabled?: boolean;
  children?: React.ReactNode;  // anything renderable
}

const Button = ({ label, onClick, variant = "primary", disabled = false }: ButtonProps) => {
  return (
    <button onClick={onClick} disabled={disabled} className={variant}>
      {label}
    </button>
  );
};

// Usage
<Button label="Save" onClick={() => handleSave()} variant="primary" />
```

### Props with children
```tsx
interface CardProps {
  title: string;
  children: React.ReactNode;  // string, JSX, array, null — anything
}

const Card = ({ title, children }: CardProps) => (
  <div>
    <h2>{title}</h2>
    {children}
  </div>
);
```

---

## 29. Typing useState

```tsx
import { useState } from "react";

// TS can infer simple types
const [count, setCount] = useState(0);          // number
const [name, setName] = useState("");           // string
const [isOpen, setIsOpen] = useState(false);   // boolean

// Be explicit for objects, arrays, unions
const [user, setUser] = useState<User | null>(null);
const [users, setUsers] = useState<User[]>([]);
const [status, setStatus] = useState<"idle" | "loading" | "error">("idle");

// Update object state (always spread!)
setUser(prev => ({ ...prev!, role: "admin" }));
```

---

## 30. Typing useRef

```tsx
import { useRef } from "react";

// DOM ref — type is the HTML element
const inputRef = useRef<HTMLInputElement>(null);
const divRef = useRef<HTMLDivElement>(null);

// Access in JSX
<input ref={inputRef} />

// Access in handler
const focusInput = () => {
  inputRef.current?.focus();  // optional chaining — current can be null
};

// Mutable ref (store value without re-render)
const timerRef = useRef<number | null>(null);
timerRef.current = setTimeout(() => {}, 1000);
```

---

## 31. Typing Events

This trips up most people. Here are the ones you'll use daily:

```tsx
// Input change
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};

// Textarea change
const handleChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {};

// Select change
const handleChange = (e: React.ChangeEvent<HTMLSelectElement>) => {};

// Button click
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {};

// Div click
const handleClick = (e: React.MouseEvent<HTMLDivElement>) => {};

// Form submit
const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault();
};

// Keyboard
const handleKey = (e: React.KeyboardEvent<HTMLInputElement>) => {
  if (e.key === "Enter") { }
};

// Usage in JSX
<input onChange={handleChange} onClick={handleClick} />
```

---

## 32. Typing API Responses

```ts
// Define the shape of your API data
interface Post {
  id: number;
  title: string;
  body: string;
  userId: number;
}

// Async function with typed return
const fetchPost = async (id: number): Promise<Post> => {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
  if (!res.ok) throw new Error("Failed to fetch");
  return res.json();
};

// In component
const [post, setPost] = useState<Post | null>(null);

useEffect(() => {
  fetchPost(1).then(setPost).catch(console.error);
}, []);
```

---

## 33. Utility Types

TS ships with powerful built-in type transformers. Use these instead of rewriting types.

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

// Partial — all fields optional (great for update payloads)
type UserUpdate = Partial<User>;
// { id?: number; name?: string; email?: string; password?: string }

// Required — all fields required
type FullUser = Required<UserUpdate>;

// Pick — keep only selected fields
type PublicUser = Pick<User, "id" | "name">;
// { id: number; name: string }

// Omit — remove selected fields
type SafeUser = Omit<User, "password">;
// { id: number; name: string; email: string }

// Readonly — freeze all fields
type FrozenUser = Readonly<User>;

// Record — object with specific key/value types (like a map)
type UserMap = Record<string, User>;
const users: UserMap = { "u1": { id: 1, name: "Rahul", ... } };

// ReturnType — extract return type of a function
const getUser = () => ({ id: 1, name: "Rahul" });
type UserFromFn = ReturnType<typeof getUser>;
// { id: number; name: string }

// Parameters — extract parameter types
type AddParams = Parameters<typeof add>;  // [number, number]
```

---

## 34. TS + React Cheatsheet

```tsx
// Props interface
interface MyProps {
  title: string;
  count?: number;
  onClick: () => void;
  children: React.ReactNode;
}

// Component
const MyComponent = ({ title, count = 0, onClick, children }: MyProps) => {
  // State
  const [value, setValue] = useState<string>("");
  const [data, setData] = useState<User | null>(null);

  // Ref
  const ref = useRef<HTMLInputElement>(null);

  // Event handler
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={ref} value={value} onChange={handleChange} />
      <button onClick={onClick}>{title}</button>
      {children}
    </form>
  );
};

export default MyComponent;
```

### Common HTML Element Types
| Element | Type |
|---|---|
| `<input>` | `HTMLInputElement` |
| `<button>` | `HTMLButtonElement` |
| `<div>` | `HTMLDivElement` |
| `<form>` | `HTMLFormElement` |
| `<select>` | `HTMLSelectElement` |
| `<textarea>` | `HTMLTextAreaElement` |
| `<img>` | `HTMLImageElement` |
| `<a>` | `HTMLAnchorElement` |

---

*With JS + TS mastered, you're fully equipped for React. The type system will save you hours of debugging.*
