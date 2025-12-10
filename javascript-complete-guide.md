# The Complete JavaScript Guide - Feynman Technique

**A beginner-friendly, comprehensive guide to mastering JavaScript fundamentals**

> "If you can't explain it simply, you don't understand it well enough." - Richard Feynman

---

## How to Use This Guide

This guide is organized into 13 progressive parts, from absolute basics to professional-level concepts. Each topic uses:

- **Simple analogies** - Relating programming to everyday experiences
- **Clear explanations** - No jargon without context
- **Code examples** - Working code you can try immediately
- **Common mistakes** - What to avoid and why
- **Best practices** - Professional standards

**Recommended approach:**
1. Read sections in order (they build on each other)
2. Type out every code example yourself
3. Break things on purpose to see what happens
4. Explain concepts out loud in your own words
5. Build small projects after each part

---

# Part 1: Getting Started

## 1.1 Introduction: What is JavaScript?

**The concept:** JavaScript is the programming language that makes websites interactive and dynamic.

**The analogy:** If a website were a car:
- **HTML** = The frame and body (structure)
- **CSS** = The paint and interior (appearance)
- **JavaScript** = The engine and electronics (behavior)

Without JavaScript, websites would be like magazines - you can look at them but can't interact with them. JavaScript makes buttons clickable, forms submittable, animations possible, and content dynamic.

**Where JavaScript runs:**
1. **Browser (Front-end)** - Makes web pages interactive
2. **Server (Back-end with Node.js)** - Handles databases, files, authentication
3. **Desktop apps** - Via frameworks like Electron (VS Code uses this!)
4. **Mobile apps** - Via frameworks like React Native or Ionic

**Why JavaScript is popular:**
- Only language that runs in all browsers
- Can build full applications (front-end + back-end)
- Huge ecosystem of libraries and tools
- Relatively easy to learn

---

## 1.2 Tools Setup

Before writing JavaScript, you need some tools. Think of these as your workshop equipment.

### Essential Tools

**1. Web Browser (Chrome, Firefox, or Edge)**
- Already have one!
- Built-in developer tools for debugging
- Chrome DevTools is most popular

**2. Node.js**
- Lets you run JavaScript outside the browser
- Download from nodejs.org
- Includes npm (package manager for installing tools)

**3. Code Editor - Visual Studio Code (VS Code)**
- Free, powerful code editor
- Download from code.visualstudio.com
- Syntax highlighting, autocomplete, debugging

### Helpful Extensions for VS Code

**Live Server**
- Automatically refreshes browser when you save code
- Like having an assistant who hits "refresh" for you

**Prettier**
- Auto-formats your code to look clean
- Like spell-check, but for code formatting

**How to install extensions:**
1. Open VS Code
2. Click Extensions icon (left sidebar)
3. Search for extension name
4. Click "Install"

---

## 1.3 Running JavaScript

JavaScript can run in two environments. Understanding both is important.

### Running JavaScript in the Browser

**Method 1: Using HTML file**
```html
<!DOCTYPE html>
<html>
<head>
    <title>My First JavaScript</title>
</head>
<body>
    <h1>Hello, World!</h1>
    
    <script>
        console.log("JavaScript is running!")
    </script>
</body>
</html>
```

Save as `index.html`, open in browser, press `F12` to see console output.

**Method 2: Using Browser Console**
1. Open any website
2. Press `F12` (opens Developer Tools)
3. Click "Console" tab
4. Type JavaScript directly: `console.log("Hello!")`
5. Press Enter

**The analogy:** The browser console is like a calculator - you can type commands and get instant results.

### Running JavaScript in Node.js

**Method 1: REPL (Read-Eval-Print Loop)**
```bash
# In terminal/command prompt:
node

# Now you're in Node.js REPL, type:
> console.log("Hello from Node!")
> 5 + 3
> .exit  // to quit
```

**Method 2: Running a file**
```bash
# Create file: script.js
# Inside script.js:
console.log("Hello from a file!")

# Run it:
node script.js
```

**When to use which:**
- **Browser**: Building websites, working with HTML/DOM
- **Node.js**: Learning basics, server-side code, automation scripts

### Loading JavaScript in the Browser

When a browser loads a web page, here's what happens:

```html
<html>
<head>
    <script src="script.js"></script>  <!-- Loads HERE -->
</head>
<body>
    <h1>My Page</h1>
</body>
</html>
```

**The problem:** Browser STOPS building the page to download and run JavaScript!

**The flow:**
1. Browser downloads HTML
2. Starts building the page (parsing HTML)
3. Finds `<script>` tag
4. **PAUSES everything**
5. Downloads JavaScript
6. Runs JavaScript
7. Continues building page

**Why this is slow:** Large JavaScript files block page rendering.

### Solutions: async and defer

**defer (RECOMMENDED)**
```html
<script src="script.js" defer></script>
```
- Downloads JavaScript while building page (parallel)
- Waits until page is fully built before running
- Scripts run in order
- Most predictable and efficient

**async**
```html
<script src="script.js" async></script>
```
- Downloads JavaScript while building page
- Runs immediately when ready (might interrupt page building)
- Scripts may run out of order
- Good for independent scripts (analytics, ads)

**The analogy:**
- **No attribute**: Cook can't do anything else while waiting for delivery
- **defer**: Cook continues working, uses delivery when everything else is ready
- **async**: Cook continues working, drops everything to deal with delivery when it arrives

**Best practice:** Put scripts at bottom of `<body>` with `defer`, or in `<head>` with `defer`.

---

## 1.4 Comments in Code

Comments are notes to yourself (and other programmers) that JavaScript ignores.

**Single-line comment:**
```javascript
// This is a comment
console.log("Hello")  // This runs, the comment is ignored
```

**Multi-line comment:**
```javascript
/*
This is a longer comment
that spans multiple lines
Very useful for documentation
*/
console.log("Hello")
```

**When to use comments:**
- Explain WHY you're doing something (not what - code shows that)
- Warn about tricky parts
- Document complex logic
- Temporarily disable code while testing

**Good comment:**
```javascript
// Using setTimeout to avoid blocking the UI
setTimeout(processData, 0)
```

**Bad comment:**
```javascript
// Add 1 to x
x = x + 1  // Comment just repeats what code says
```

**The principle:** Good code explains WHAT it does. Good comments explain WHY.

---

# Part 2: Core Language Basics

## 2.1 Primitive Types & Operations

Think of primitive types as the basic building blocks, like LEGO pieces. Everything else is built from these.

**JavaScript has 7 primitive types:**
1. Number
2. String  
3. Boolean
4. Undefined
5. Null
6. Symbol (rare, advanced)
7. BigInt (for huge numbers, rare)

We'll focus on the first 5 - they cover 99% of daily use.

### Numbers

**The concept:** Numbers are... numbers! JavaScript doesn't distinguish between integers and decimals.

```javascript
let age = 25              // Integer
let price = 19.99         // Decimal (float)
let negative = -5         // Negative
let billion = 1_000_000   // Underscores for readability (new in ES2021)
```

**Math operations:**
```javascript
5 + 3      // 8 (addition)
5 - 3      // 2 (subtraction)
5 * 3      // 15 (multiplication)
5 / 3      // 1.6666... (division)
5 % 3      // 2 (modulo - remainder)
5 ** 3     // 125 (exponentiation - 5 to the power of 3)
```

**Special numbers:**
```javascript
Infinity       // Larger than any number
-Infinity      // Smaller than any number
NaN            // Not a Number (we'll cover this later)
```

### Strings

**The concept:** Strings are text wrapped in quotes.

```javascript
let name = "Cedric"              // Double quotes
let city = 'Manila'              // Single quotes
let message = `Hello, world!`    // Backticks (template literals)
```

**All three work the same for simple strings. Backticks have special powers we'll cover later.**

**String operations:**
```javascript
"Hello" + " " + "World"    // "Hello World" (concatenation)
"Hello".length             // 5 (length property)
"Hello"[0]                 // "H" (access character by index)
"Hello".toUpperCase()      // "HELLO"
"Hello".toLowerCase()      // "hello"
```

**Important:** Strings use zero-based indexing:
```javascript
let word = "Hello"
word[0]  // "H" - first character
word[1]  // "e" - second character
word[4]  // "o" - fifth character
```

**Escape characters:**
```javascript
"He said \"Hello\""    // He said "Hello"
"Line 1\nLine 2"       // Line break
"Tab\there"            // Tab character
```

### Booleans

**The concept:** Boolean is a yes/no, true/false value. Like a light switch - it's either on or off.

```javascript
let isLoggedIn = true
let hasPermission = false
```

**Boolean operations:**
```javascript
true && true     // true (AND - both must be true)
true && false    // false
true || false    // true (OR - at least one must be true)
false || false   // false
!true            // false (NOT - flips the value)
!false           // true
```

**Comparisons return booleans:**
```javascript
5 > 3       // true
5 < 3       // false
5 >= 5      // true (greater than or equal)
5 <= 3      // false (less than or equal)
5 === 5     // true (equal to)
5 !== 3     // true (not equal to)
```

### Undefined

**The concept:** Undefined means "no value has been assigned yet."

```javascript
let x
console.log(x)  // undefined

let person = {
    name: "Cedric"
}
console.log(person.age)  // undefined (property doesn't exist)
```

**The analogy:** Undefined is like an empty box that was never filled. The box exists, but there's nothing in it.

### Null

**The concept:** Null means "intentionally empty" or "no value."

```javascript
let user = null  // Deliberately set to "no user"
```

**The difference:**
- **Undefined**: "I forgot to put something here" or "This doesn't exist"
- **Null**: "I specifically set this to nothing"

```javascript
let uninitializedVariable      // undefined (never assigned)
let deliberatelyEmpty = null   // null (assigned to nothing)
```

**The analogy:**
- **Undefined**: An empty lot where a house was never built
- **Null**: A vacant lot where the house was demolished

---

## 2.2 The typeof Operator

**The concept:** `typeof` tells you what type a value is.

```javascript
typeof 42              // "number"
typeof "hello"         // "string"
typeof true            // "boolean"
typeof undefined       // "undefined"
typeof null            // "object" (historical bug in JavaScript!)
typeof { name: "Cedric" }  // "object"
typeof [1, 2, 3]       // "object" (arrays are objects)
typeof function() {}   // "function"
```

**Practical use:**
```javascript
let value = getUserInput()

if (typeof value === "string") {
    console.log("It's text!")
} else if (typeof value === "number") {
    console.log("It's a number!")
}
```

**The weird one:** `typeof null` returns `"object"` - this is a famous JavaScript bug that can't be fixed without breaking millions of websites.

---

## 2.3 Truthy and Falsy Values ⭐

**The concept:** In JavaScript, every value has an inherent "truthiness" - it's considered either true or false in boolean contexts (like if statements).

### The Falsy Values (Only 8!)

**Memorize these - everything else is truthy:**

```javascript
false        // obviously false
0            // zero
-0           // negative zero
0n           // BigInt zero
""           // empty string
''           // empty string (single quotes)
``           // empty string (template literal)
null         // null
undefined    // undefined
NaN          // Not a Number
```

**Everything else is truthy!** Including:

```javascript
true         // obviously true
42           // any non-zero number
-42          // negative numbers
"0"          // string containing zero (not the number 0)
"false"      // string containing false (not the boolean false)
[]           // empty array (surprising!)
{}           // empty object (surprising!)
function(){} // functions
```

### Why This Matters

**In conditions:**
```javascript
let username = ""

if (username) {
    console.log("Welcome, " + username)
} else {
    console.log("Please enter a username")  // This runs! "" is falsy
}
```

**Common pattern - checking if something exists:**
```javascript
let user = getUser()

if (user) {
    // user exists and is truthy
    console.log(user.name)
} else {
    // user is null, undefined, false, 0, "", etc.
    console.log("No user found")
}
```

**The gotcha with zero:**
```javascript
let itemCount = 0

if (itemCount) {
    console.log("You have items")  // Doesn't run! 0 is falsy
} else {
    console.log("Your cart is empty")  // Runs even though count is defined
}

// Better way:
if (itemCount !== undefined) {
    console.log(`You have ${itemCount} items`)
}
```

**The gotcha with empty arrays/objects:**
```javascript
let emptyArray = []

if (emptyArray) {
    console.log("Array exists!")  // This RUNS! Arrays are always truthy
}

// To check if array is empty:
if (emptyArray.length > 0) {
    console.log("Array has items")
}
```

**The analogy:** Truthy/falsy is like asking "Is there something here?" Most things count as "something" (truthy), but a few specific cases count as "nothing" (falsy).

---

## 2.4 Variables

**The concept:** Variables are labeled containers that store values. You can put things in them, take things out, and change what's inside.

**The analogy:** Variables are like labeled boxes in a warehouse. The label is the variable name, the contents is the value.

### Creating Variables with let

```javascript
let age = 25
let city = "Manila"
let isStudent = true
```

**Breaking it down:**
- `let` - "I want to create a variable"
- `age` - The name (label for the box)
- `=` - Assignment operator (put something in the box)
- `25` - The value (what goes in the box)

**Updating variables:**
```javascript
let score = 0
console.log(score)  // 0

score = 10
console.log(score)  // 10

score = score + 5
console.log(score)  // 15

// Shorthand:
score += 5  // Same as: score = score + 5
console.log(score)  // 20
```

**Other shorthand operators:**
```javascript
let x = 10

x += 5   // x = x + 5  → 15
x -= 3   // x = x - 3  → 12
x *= 2   // x = x * 2  → 24
x /= 4   // x = x / 4  → 6
x++      // x = x + 1  → 7 (increment)
x--      // x = x - 1  → 6 (decrement)
```

### Creating Variables with const

```javascript
const birthYear = 1990
const pi = 3.14159
```

**The rule:** Once you set a `const`, you **cannot change it**.

```javascript
const name = "Cedric"
name = "John"  // ERROR! Cannot reassign const
```

**Why use const?** It prevents accidental changes to values that shouldn't change.

### Variable Naming Rules

**Must follow these rules:**
- Start with letter, underscore (_), or dollar sign ($)
- Can contain letters, numbers, underscores, dollar signs
- Cannot use reserved keywords (let, const, if, function, etc.)
- Case-sensitive (age and Age are different)

**Legal names:**
```javascript
let firstName
let first_name
let firstName1
let $element
let _privateVar
```

**Illegal names:**
```javascript
let 1stPlace      // Can't start with number
let first-name    // Can't use hyphens
let let           // Can't use reserved word
```

**Naming conventions (not rules, but standards):**
```javascript
// camelCase for variables and functions (standard in JavaScript)
let firstName = "Cedric"
let userAge = 25

// PascalCase for classes
class UserAccount { }

// SCREAMING_SNAKE_CASE for constants that never change
const MAX_USERS = 100
const API_KEY = "abc123"

// Descriptive names (good)
let userAge = 25
let isLoggedIn = true

// Unclear names (bad)
let x = 25
let flag = true
```

### let vs const: Which to Use?

**Best practice: Use `const` by default**

```javascript
const name = "Cedric"      // Won't change
const birthYear = 1990     // Won't change
let age = 25               // Will change every year
```

**Why?**
- Makes code more predictable
- Prevents accidental changes
- Shows intent: "This value is fixed"

**Use let only when you know the value will change:**
- Counter in a loop
- Score in a game
- Any accumulator

---

## 2.5 The var Keyword (Avoid It!)

Before `let` and `const` existed (pre-2015), JavaScript only had `var`.

**Why learn about var?** You'll see it in old code or tutorials.

### Problems with var

**Problem 1: No Block Scope**
```javascript
if (true) {
    var x = 10
}
console.log(x)  // 10 (leaked out of the if block!)

// With let:
if (true) {
    let y = 10
}
console.log(y)  // ERROR! y is not defined (properly scoped)
```

**Problem 2: Weird Hoisting**
```javascript
console.log(x)  // undefined (not an error!)
var x = 5

// With let:
console.log(y)  // ERROR! Cannot access before initialization
let y = 5
```

**Problem 3: Can Redeclare**
```javascript
var name = "Cedric"
var name = "John"  // No error! Confusing!

// With let:
let age = 25
let age = 30  // ERROR! Already declared (good!)
```

**The bottom line:** Use `let` and `const` in modern code. Understand `var` only for reading old code.

---

## 2.6 Type Coercion

**The concept:** Type coercion is when JavaScript automatically converts a value from one type to another.

**The analogy:** It's like having a translator who automatically switches between languages, sometimes helpfully, sometimes creating confusion.

### Explicit Coercion (You Control It)

```javascript
// String to Number
let str = "123"
let num = Number(str)        // 123
let num2 = parseInt(str)     // 123 (integer only)
let num3 = parseFloat("3.14") // 3.14 (with decimals)

// Number to String
let age = 25
let str1 = String(age)       // "25"
let str2 = age.toString()    // "25"

// To Boolean
Boolean(1)           // true
Boolean(0)           // false
Boolean("hello")     // true
Boolean("")          // false
```

### Implicit Coercion (JavaScript Does It)

**With the + operator:**
```javascript
"5" + 3      // "53" (number becomes string)
"5" + "3"    // "53" (concatenation)
5 + 3        // 8 (normal addition)
```

**With other operators:**
```javascript
"5" - 3      // 2 (string becomes number)
"10" * "2"   // 20 (both become numbers)
"10" / "2"   // 5 (both become numbers)
```

**The confusing rule:** 
- `+` with any string → concatenation (joins strings)
- `-`, `*`, `/` → always math (converts to numbers)

**Why this is confusing:**
```javascript
"5" + 2      // "52" (concatenation)
"5" - 2      // 3 (subtraction)
```

**Best practice:** Be explicit about conversions to avoid confusion:
```javascript
// Instead of relying on implicit coercion:
"5" + 2      // "52" (confusing)

// Be explicit:
"5" + String(2)     // "52" (clear intent: concatenation)
Number("5") + 2     // 7 (clear intent: math)
```

---

## 2.7 NaN: Not a Number

**The concept:** NaN is a special value representing "this was supposed to be a number but something went wrong."

```javascript
parseInt("hello")    // NaN (can't convert to number)
Number("abc")        // NaN
0 / 0                // NaN (undefined in math)
Math.sqrt(-1)        // NaN (imaginary number in JavaScript)
```

### The Confusing Parts

**1. typeof NaN is "number"**
```javascript
typeof NaN   // "number" (makes no sense, but true!)
```

**Why?** Historical JavaScript quirk. NaN is technically a numeric value representing "invalid number."

**2. NaN doesn't equal anything, not even itself**
```javascript
NaN === NaN  // false (only value that doesn't equal itself!)
NaN == NaN   // false

let x = NaN
x === x      // false (weird!)
```

**3. How to check for NaN properly**
```javascript
// WRONG way:
let result = parseInt("hello")
if (result === NaN) {  // Never true!
    console.log("Invalid number")
}

// RIGHT way:
if (isNaN(result)) {  // Use isNaN() function
    console.log("Invalid number")
}

// Modern way (ES6+):
if (Number.isNaN(result)) {  // More reliable
    console.log("Invalid number")
}
```

**Difference between isNaN() and Number.isNaN():**
```javascript
isNaN("hello")         // true (converts to number first, gets NaN)
Number.isNaN("hello")  // false (doesn't convert, "hello" is not NaN)

isNaN(NaN)             // true
Number.isNaN(NaN)      // true

// Recommendation: Use Number.isNaN() for strict checking
```

**The analogy:** NaN is like an error message disguised as a number. It's JavaScript's way of saying "I tried to do math but something went wrong."

---

## 2.8 Equality Comparisons

### Double Equals (==) vs Triple Equals (===)

**Triple equals (===) - Strict Equality (RECOMMENDED)**
```javascript
5 === 5        // true
5 === "5"      // false (different types)
true === 1     // false (different types)
null === undefined  // false (different types)
```

**Checks both value AND type.** No conversions happen.

**Double equals (==) - Loose Equality (AVOID)**
```javascript
5 == 5         // true
5 == "5"       // true (converts string to number!)
true == 1      // true (converts boolean to number!)
null == undefined  // true (special rule)
0 == false     // true (false becomes 0)
"" == false    // true (both become 0)
```

**Performs type coercion before comparing.** Very unpredictable!

### The Problems with ==

**Unexpected results:**
```javascript
"" == 0        // true (why?)
[] == false    // true (why?)
[] == ![]      // true (this makes no sense!)
```

**The rule to remember:** Use `===` (and `!==`) 99% of the time.

**The ONE exception:**
```javascript
// Checking for null OR undefined:
if (value == null) {  // Catches both null and undefined
    console.log("Value is null or undefined")
}

// Equivalent to:
if (value === null || value === undefined) {
    console.log("Value is null or undefined")
}
```

**Comparison summary:**
```javascript
// Triple equals (===) - Use this!
===  // Equal value and equal type
!==  // Not equal value or not equal type

// Double equals (==) - Avoid!
==   // Equal value (after type coercion)
!=   // Not equal value (after type coercion)

// Other comparisons:
>    // Greater than
<    // Less than
>=   // Greater than or equal
<=   // Less than or equal
```

---

# Part 3: Functions & Scope

## 3.1 Functions Basics

**The concept:** Functions are reusable blocks of code that perform a specific task.

**The analogy:** Functions are like recipes. Instead of writing out all the steps every time you want to make cookies, you write the recipe once and follow it whenever needed.

### Why Functions?

**Without functions (repetitive):**
```javascript
console.log("Welcome, Cedric!")
console.log("Your score is 100")
console.log("---")

console.log("Welcome, Maria!")
console.log("Your score is 95")
console.log("---")

console.log("Welcome, Juan!")
console.log("Your score is 88")
console.log("---")
```

**With functions (reusable):**
```javascript
function greetUser(name, score) {
    console.log(`Welcome, ${name}!`)
    console.log(`Your score is ${score}`)
    console.log("---")
}

greetUser("Cedric", 100)
greetUser("Maria", 95)
greetUser("Juan", 88)
```

### Creating Functions

**Function declaration:**
```javascript
function greet(name) {
    return "Hello, " + name
}

let message = greet("Cedric")
console.log(message)  // "Hello, Cedric"
```

**Breaking it down:**
1. `function` - keyword saying "I'm making a function"
2. `greet` - function name (use camelCase)
3. `(name)` - parameter (input the function receives)
4. `{...}` - function body (code that runs)
5. `return` - sends a value back to whoever called the function

### Parameters vs Arguments

**Parameters** = Variables in the function definition
**Arguments** = Actual values passed when calling

```javascript
function add(a, b) {     // a and b are parameters
    return a + b
}

let result = add(5, 3)   // 5 and 3 are arguments
```

### Multiple Parameters

```javascript
function introduce(name, age, city) {
    return `Hi, I'm ${name}, ${age} years old, from ${city}`
}

console.log(introduce("Cedric", 25, "Manila"))
// "Hi, I'm Cedric, 25 years old, from Manila"
```

### Default Parameters

```javascript
function greet(name = "Guest") {
    return `Hello, ${name}!`
}

console.log(greet("Cedric"))  // "Hello, Cedric!"
console.log(greet())          // "Hello, Guest!" (uses default)
```

### Return Statement

**The return statement:**
- Sends a value back to the caller
- Immediately exits the function (nothing after return runs)

```javascript
function isAdult(age) {
    if (age >= 18) {
        return true
    }
    return false
}

// Simpler version:
function isAdult(age) {
    return age >= 18
}
```

**Functions without return:**
```javascript
function logMessage(message) {
    console.log(message)
    // No return statement
}

let result = logMessage("Hello")
console.log(result)  // undefined (functions without return give undefined)
```

### Common Mistakes

**Mistake 1: Forgetting to return**
```javascript
function add(a, b) {
    a + b  // Result is calculated but not returned!
}

let sum = add(5, 3)
console.log(sum)  // undefined
```

**Mistake 2: Forgetting parentheses when calling**
```javascript
function sayHello() {
    return "Hello!"
}

console.log(sayHello)    // [Function: sayHello] (the function itself)
console.log(sayHello())  // "Hello!" (the function's return value)
```

**Mistake 3: Using return in wrong place**
```javascript
function processNumbers() {
    return "Starting"  // Function exits here!
    console.log("This never runs")
    return "Done"      // Never reached
}
```

---

## 3.2 Arrow Functions

**The concept:** Arrow functions are a shorter way to write functions, introduced in ES6 (2015).

**Function declaration vs Arrow function:**
```javascript
// Traditional function
function greet(name) {
    return "Hello, " + name
}

// Arrow function
const greet = (name) => {
    return "Hello, " + name
}
```

### Arrow Function Syntax

**Full syntax:**
```javascript
const functionName = (parameters) => {
    // function body
    return value
}
```

**Shortcuts:**

**1. Single parameter - skip parentheses:**
```javascript
const double = num => {
    return num * 2
}

// Not:
const double = (num) => {
    return num * 2
}
```

**2. Single expression - skip braces and return:**
```javascript
const double = num => num * 2

// Automatically returns num * 2
```

**3. No parameters - empty parentheses:**
```javascript
const sayHello = () => {
    return "Hello!"
}

// Or shorter:
const sayHello = () => "Hello!"
```

**4. Multiple parameters - keep parentheses:**
```javascript
const add = (a, b) => a + b

// Not:
const add = a, b => a + b  // Syntax error!
```

### Examples

```javascript
// No parameters
const getRandom = () => Math.random()

// One parameter (parentheses optional)
const square = x => x * x
const squareAlt = (x) => x * x  // Also valid

// Two parameters (parentheses required)
const add = (a, b) => a + b

// Multi-line body (braces and return required)
const greet = name => {
    let greeting = "Hello, " + name
    return greeting.toUpperCase()
}

// Returning an object (wrap in parentheses)
const makePerson = (name, age) => ({
    name: name,
    age: age
})

// Without parentheses, it thinks {} is function body!
const makePerson = (name, age) => { name: name, age: age }  // WRONG!
```

### When to Use Arrow Functions

**Use arrow functions for:**
- Short, simple functions
- Functions passed as callbacks
- Functions that don't need their own `this`

**Use regular functions for:**
- Functions that need their own `this` context
- Object methods
- When clarity is more important than brevity

```javascript
// Good use of arrow function (callback):
numbers.map(num => num * 2)

// Regular function might be clearer for complex logic:
function calculateTax(income, rate, deductions) {
    let taxableIncome = income - deductions
    let tax = taxableIncome * rate
    return tax > 0 ? tax : 0
}
```

---

## 3.3 Callbacks

**The concept:** A callback is a function passed as an argument to another function.

**The mind-blowing part:** In JavaScript, functions are values! You can pass them around like any other value.

```javascript
function sayHello() {
    console.log("Hello!")
}

// Pass the function itself (no parentheses!)
let myFunction = sayHello
myFunction()  // "Hello!"
```

### Basic Callback Example

```javascript
function greet(name) {
    console.log(`Hello, ${name}!`)
}

function goodbye(name) {
    console.log(`Goodbye, ${name}!`)
}

function processUserName(name, callback) {
    console.log("Processing...")
    callback(name)  // Call the function that was passed in
}

processUserName("Cedric", greet)    // Hello, Cedric!
processUserName("Cedric", goodbye)  // Goodbye, Cedric!
```

### Why Callbacks Are Powerful

Same function, different behaviors:

```javascript
function calculate(a, b, operation) {
    return operation(a, b)
}

function add(x, y) {
    return x + y
}

function multiply(x, y) {
    return x * y
}

console.log(calculate(5, 3, add))       // 8
console.log(calculate(5, 3, multiply))  // 15
```

### Callbacks with Arrow Functions

```javascript
function calculate(a, b, operation) {
    return operation(a, b)
}

// Instead of defining separate functions:
console.log(calculate(5, 3, (x, y) => x + y))      // 8
console.log(calculate(5, 3, (x, y) => x * y))      // 15
console.log(calculate(5, 3, (x, y) => x - y))      // 2
```

### Common Callback Pattern

```javascript
// Array methods use callbacks extensively:
let numbers = [1, 2, 3, 4, 5]

// forEach - run function for each item
numbers.forEach(num => {
    console.log(num * 2)
})

// map - transform each item
let doubled = numbers.map(num => num * 2)
console.log(doubled)  // [2, 4, 6, 8, 10]

// filter - keep items that pass a test
let evens = numbers.filter(num => num % 2 === 0)
console.log(evens)  // [2, 4]
```

### The Critical Mistake

**WRONG - Calling the function immediately:**
```javascript
function processUserName(name, callback) {
    callback(name)
}

// This calls greet immediately and passes its return value (undefined)!
processUserName("Cedric", greet("Cedric"))
```

**RIGHT - Passing the function itself:**
```javascript
// No parentheses! Pass the function, don't call it
processUserName("Cedric", greet)
```

**The analogy:** 
- `greet` = "Here's a recipe"
- `greet()` = "Here's the cookie I just baked"

You want to give the recipe, not the cookie!

---

## 3.4 Scope

**The concept:** Scope determines where variables can be accessed in your code.

**The analogy:** Scope is like rooms in a house. Things in the living room can't automatically be seen from the bedroom.

### Types of Scope

**1. Global Scope** (Outside all functions and blocks)
```javascript
let globalVar = "I'm global"

function myFunction() {
    console.log(globalVar)  // Can access global variable
}

console.log(globalVar)  // Can access from anywhere
```

**2. Function Scope** (Inside a function)
```javascript
function myFunction() {
    let functionVar = "I'm in the function"
    console.log(functionVar)  // Works
}

console.log(functionVar)  // ERROR! Not defined outside function
```

**3. Block Scope** (Inside {} braces)
```javascript
if (true) {
    let blockVar = "I'm in the block"
    console.log(blockVar)  // Works
}

console.log(blockVar)  // ERROR! Not defined outside block
```

### Scope Rules

**Rule 1: Inner scopes can see outer scopes**
```javascript
let global = "global"

function outer() {
    let outerVar = "outer"
    
    function inner() {
        let innerVar = "inner"
        console.log(global)    // ✓ Can access
        console.log(outerVar)  // ✓ Can access
        console.log(innerVar)  // ✓ Can access
    }
    
    inner()
}
```

**Rule 2: Outer scopes cannot see inner scopes**
```javascript
function myFunction() {
    let inside = "inside"
}

console.log(inside)  // ERROR! Can't access function variables from outside
```

**Rule 3: Same-level scopes cannot see each other**
```javascript
function func1() {
    let var1 = "one"
}

function func2() {
    console.log(var1)  // ERROR! Can't access var1 from func1
}
```

### Scope Chain

```javascript
let level1 = "Global"

function outer() {
    let level2 = "Outer"
    
    function inner() {
        let level3 = "Inner"
        console.log(level3)  // Looks here first
        console.log(level2)  // Then looks in outer
        console.log(level1)  // Finally looks in global
    }
    
    inner()
}
```

**The analogy:** It's like looking for your keys:
1. Check your pocket (current scope)
2. Check the room you're in (parent scope)
3. Check the whole house (global scope)

### Variable Shadowing

```javascript
let name = "Global Cedric"

function greet() {
    let name = "Local Cedric"  // Shadows the global variable
    console.log(name)  // "Local Cedric" (uses closest scope)
}

greet()
console.log(name)  // "Global Cedric" (global unchanged)
```

### Block Scope with let and const

```javascript
// let and const are block-scoped:
if (true) {
    let x = 10
    const y = 20
}
console.log(x)  // ERROR
console.log(y)  // ERROR

// var is NOT block-scoped (another reason to avoid it):
if (true) {
    var z = 30
}
console.log(z)  // 30 (leaked out!)
```

### Best Practices

**1. Minimize global variables**
```javascript
// BAD - Everything global
let user = "Cedric"
let age = 25
let score = 100

// GOOD - Encapsulated in objects or functions
function createUser() {
    let user = "Cedric"
    let age = 25
    let score = 100
    
    return { user, age, score }
}
```

**2. Use const by default (most restrictive scope)**
```javascript
const name = "Cedric"  // Can't be reassigned
let score = 0          // Can be reassigned
```

**3. Declare variables in the smallest scope needed**
```javascript
// BAD - declared globally when only needed in loop
let i = 0
for (i = 0; i < 10; i++) {
    console.log(i)
}

// GOOD - declared in smallest scope
for (let i = 0; i < 10; i++) {
    console.log(i)
}
```

---

## 3.5 Hoisting

**The concept:** JavaScript moves function and variable declarations to the top of their scope before executing code.

**The weird part:** You can use functions before they're defined in your code!

### Function Hoisting

**This works:**
```javascript
sayHello()  // "Hello!" - works even though function is defined below!

function sayHello() {
    console.log("Hello!")
}
```

**What JavaScript does internally:**
```javascript
// JavaScript moves function declarations to the top:
function sayHello() {
    console.log("Hello!")
}

sayHello()  // Now it makes sense
```

### Why Function Hoisting Exists

**Benefit: Better code organization**
```javascript
// Important functions at the top (easy to find)
startApp()
processData()
cleanup()

// Implementation details at the bottom
function startApp() {
    console.log("App started")
}

function processData() {
    console.log("Processing...")
}

function cleanup() {
    console.log("Cleanup done")
}
```

### What Does NOT Get Hoisted

**Arrow functions and function expressions:**
```javascript
greet()  // ERROR! Cannot access before initialization

const greet = () => {
    console.log("Hello!")
}
```

**Why?** Arrow functions use `const` or `let`, which are hoisted differently.

### Variable Hoisting (with var)

**With var (confusing):**
```javascript
console.log(x)  // undefined (not an error!)
var x = 5
console.log(x)  // 5

// What JavaScript does:
var x            // Declaration hoisted
console.log(x)   // undefined (declared but not assigned)
x = 5           // Assignment stays in place
console.log(x)  // 5
```

**With let and const (better behavior):**
```javascript
console.log(x)  // ERROR! Cannot access before initialization
let x = 5

console.log(y)  // ERROR! Cannot access before initialization
const y = 10
```

### The Temporal Dead Zone

Variables declared with `let` and `const` are hoisted, but you can't access them before their declaration:

```javascript
// Temporal Dead Zone for x starts here
console.log(x)  // ERROR!
let x = 5      // TDZ ends here
console.log(x)  // 5
```

### Hoisting Summary

**What gets hoisted:**
- Function declarations (fully hoisted - can use before declaration)
- Variable declarations with `var` (hoisted as `undefined`)
- Variable declarations with `let`/`const` (hoisted but in temporal dead zone)

**What doesn't get hoisted:**
- Function expressions and arrow functions
- Variable assignments

**Best practice:** Don't rely on hoisting for variables. Always declare variables at the top of their scope.

---

## 3.6 Closures

**The concept:** A closure is when a function "remembers" variables from where it was created, even after that place is gone.

**This is one of JavaScript's most powerful and most confusing features!**

### Basic Closure Example

```javascript
function outer() {
    let count = 0  // Variable in outer function
    
    function inner() {
        count++
        console.log(count)
    }
    
    return inner
}

let counter = outer()
counter()  // 1
counter()  // 2
counter()  // 3
```

**What's happening:**
1. `outer()` runs and creates `count`
2. `inner()` is created inside `outer()`
3. `outer()` finishes and returns `inner`
4. `outer()` is done, but `inner` still remembers `count`!

**The analogy:** It's like a person who moves away from their hometown but still remembers their childhood home. The home (outer function) is done and gone, but the memory (closure) remains.

### Why Closures Exist

Closures let you create **private variables** - data that only certain functions can access:

```javascript
function createBankAccount(initialBalance) {
    let balance = initialBalance  // Private variable
    
    return {
        deposit: function(amount) {
            balance += amount
            return balance
        },
        withdraw: function(amount) {
            if (amount <= balance) {
                balance -= amount
                return balance
            }
            return "Insufficient funds"
        },
        getBalance: function() {
            return balance
        }
    }
}

let myAccount = createBankAccount(100)
console.log(myAccount.getBalance())  // 100
myAccount.deposit(50)                // 150
myAccount.withdraw(30)               // 120
console.log(myAccount.balance)       // undefined (can't access directly!)
```

**The power:** `balance` is truly private. The only way to access it is through the methods we provide.

### More Closure Examples

**Example 1: Creating specialized functions**
```javascript
function multiplyBy(multiplier) {
    return function(number) {
        return number * multiplier
    }
}

let double = multiplyBy(2)
let triple = multiplyBy(3)

console.log(double(5))  // 10
console.log(triple(5))  // 15
```

**Example 2: Event handlers remembering state**
```javascript
function setupButton(buttonId) {
    let clickCount = 0  // Each button gets its own count
    
    document.getElementById(buttonId).addEventListener('click', function() {
        clickCount++
        console.log(`${buttonId} clicked ${clickCount} times`)
    })
}

setupButton('btn1')
setupButton('btn2')
// Each button remembers its own click count!
```

### Common Closure Gotcha (The Loop Problem)

**The problem:**
```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i)  // Prints: 3, 3, 3 (not 0, 1, 2!)
    }, 1000)
}
```

**Why?** All three functions share the same `i`, and by the time they run, `i` is 3.

**Solution 1: Use let (block scoped)**
```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i)  // Prints: 0, 1, 2
    }, 1000)
}
```

**Solution 2: Create closure with IIFE**
```javascript
for (var i = 0; i < 3; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(j)  // Prints: 0, 1, 2
        }, 1000)
    })(i)
}
```

### Key Insights

1. **Closures happen automatically** - you don't need special syntax
2. **Inner functions always have access to outer function variables**
3. **Each function call creates new variables** - closures don't share
4. **Closures can create private data** - only accessible through specific functions

---

# Part 4: Data Structures

## 4.1 Arrays

**The concept:** Arrays are ordered lists of values. Think of them as numbered containers that hold multiple items.

**The analogy:** An array is like a row of lockers at school, each with a number. You can store different things in each locker and access them by their number.

### Creating Arrays

```javascript
// Empty array
let emptyArray = []

// Array with values
let fruits = ["apple", "banana", "cherry"]
let numbers = [1, 2, 3, 4, 5]
let mixed = [1, "hello", true, null]  // Can mix types!

// Using Array constructor (less common)
let arr = new Array(3)  // Creates array with 3 empty slots
```

### Accessing Elements (Zero-Based Indexing)

**CRITICAL:** Arrays start counting at 0, not 1!

```javascript
let fruits = ["apple", "banana", "cherry"]

fruits[0]  // "apple" (first item)
fruits[1]  // "banana" (second item)
fruits[2]  // "cherry" (third item)
fruits[3]  // undefined (doesn't exist)
```

**Why zero-based?** Historical computer science reasons. Think of the index as "how many steps from the start."

### Array Length

```javascript
let fruits = ["apple", "banana", "cherry"]
console.log(fruits.length)  // 3

// Get last item:
let last = fruits[fruits.length - 1]  // "cherry"
// Why -1? If length is 3, last index is 2 (0, 1, 2)
```

### Modifying Arrays

```javascript
let fruits = ["apple", "banana", "cherry"]

// Change an item
fruits[1] = "blueberry"
console.log(fruits)  // ["apple", "blueberry", "cherry"]

// Add to end
fruits.push("date")
console.log(fruits)  // ["apple", "blueberry", "cherry", "date"]

// Remove from end
let removed = fruits.pop()
console.log(removed)  // "date"
console.log(fruits)   // ["apple", "blueberry", "cherry"]

// Add to beginning
fruits.unshift("apricot")
console.log(fruits)  // ["apricot", "apple", "blueberry", "cherry"]

// Remove from beginning
let first = fruits.shift()
console.log(first)   // "apricot"
console.log(fruits)  // ["apple", "blueberry", "cherry"]
```

### Nested Arrays (Arrays Inside Arrays)

```javascript
let matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

console.log(matrix[0])     // [1, 2, 3] (first row)
console.log(matrix[0][0])  // 1 (first row, first column)
console.log(matrix[1][2])  // 6 (second row, third column)
```

### Checking if Something is an Array

```javascript
let arr = [1, 2, 3]
let notArr = "not an array"

Array.isArray(arr)     // true
Array.isArray(notArr)  // false

typeof arr    // "object" (not helpful!)
```

---

## 4.2 Array Methods - Basic

These methods help you work with arrays effectively.

### push() and pop() - End of Array

```javascript
let stack = []

stack.push(1)     // [1]
stack.push(2)     // [1, 2]
stack.push(3)     // [1, 2, 3]

let last = stack.pop()  // 3
console.log(stack)      // [1, 2]
```

**The analogy:** Like a stack of plates - add to top (push), remove from top (pop).

### unshift() and shift() - Beginning of Array

```javascript
let queue = []

queue.push(1)     // [1]
queue.push(2)     // [1, 2]
queue.push(3)     // [1, 2, 3]

let first = queue.shift()  // 1
console.log(queue)         // [2, 3]
```

**The analogy:** Like a line at the bank - enter at back (push), leave from front (shift).

### indexOf() and includes()

```javascript
let fruits = ["apple", "banana", "cherry"]

// Find position
fruits.indexOf("banana")      // 1
fruits.indexOf("grape")       // -1 (not found)

// Check if exists
fruits.includes("banana")     // true
fruits.includes("grape")      // false
```

### slice() - Copy Part of Array

```javascript
let numbers = [1, 2, 3, 4, 5]

let slice1 = numbers.slice(1, 4)  // [2, 3, 4] (from index 1 to 4, not including 4)
let slice2 = numbers.slice(2)     // [3, 4, 5] (from index 2 to end)
let slice3 = numbers.slice(-2)    // [4, 5] (last 2 items)

console.log(numbers)  // [1, 2, 3, 4, 5] (original unchanged!)
```

### splice() - Add/Remove Items

**CAUTION:** This MODIFIES the original array!

```javascript
let fruits = ["apple", "banana", "cherry", "date"]

// Remove items
// splice(start, deleteCount)
fruits.splice(1, 2)  // Remove 2 items starting at index 1
console.log(fruits)  // ["apple", "date"]

// Add items
fruits = ["apple", "banana", "cherry"]
fruits.splice(1, 0, "blueberry")  // At index 1, remove 0, add "blueberry"
console.log(fruits)  // ["apple", "blueberry", "banana", "cherry"]

// Replace items
fruits = ["apple", "banana", "cherry"]
fruits.splice(1, 1, "blueberry")  // At index 1, remove 1, add "blueberry"
console.log(fruits)  // ["apple", "blueberry", "cherry"]
```

### join() and split()

```javascript
// join - Array to String
let words = ["Hello", "World"]
let sentence = words.join(" ")  // "Hello World"
let csv = words.join(",")       // "Hello,World"

// split - String to Array (on String, not Array!)
let text = "apple,banana,cherry"
let fruits = text.split(",")    // ["apple", "banana", "cherry"]

let sentence2 = "Hello World"
let words2 = sentence2.split(" ")  // ["Hello", "World"]
```

### concat() - Combine Arrays

```javascript
let arr1 = [1, 2, 3]
let arr2 = [4, 5, 6]
let combined = arr1.concat(arr2)  // [1, 2, 3, 4, 5, 6]

console.log(arr1)  // [1, 2, 3] (originals unchanged)
console.log(arr2)  // [4, 5, 6]
```

### reverse() - Reverse Order

```javascript
let numbers = [1, 2, 3, 4, 5]
numbers.reverse()
console.log(numbers)  // [5, 4, 3, 2, 1]

// CAUTION: Modifies original array!
```

---

## 4.3 Array Methods - Advanced

These are the power tools of JavaScript. Master these!

### forEach() - Do Something With Each Item

```javascript
let numbers = [1, 2, 3, 4, 5]

numbers.forEach(num => {
    console.log(num * 2)
})
// Prints: 2, 4, 6, 8, 10

// With index:
numbers.forEach((num, index) => {
    console.log(`Index ${index}: ${num}`)
})
```

**When to use:** When you want to DO something with each item, but don't need a new array.

### map() - Transform Each Item

```javascript
let numbers = [1, 2, 3, 4, 5]

let doubled = numbers.map(num => num * 2)
console.log(doubled)  // [2, 4, 6, 8, 10]
console.log(numbers)  // [1, 2, 3, 4, 5] (original unchanged)

// More complex transformation:
let users = [
    { name: "Cedric", age: 25 },
    { name: "Maria", age: 30 }
]

let names = users.map(user => user.name)
console.log(names)  // ["Cedric", "Maria"]
```

**When to use:** When you want to transform each item and get a new array back.

**The analogy:** Like a factory assembly line - each item goes in, gets transformed, comes out changed.

### filter() - Keep Items That Pass a Test

```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let evens = numbers.filter(num => num % 2 === 0)
console.log(evens)  // [2, 4, 6, 8, 10]

let odds = numbers.filter(num => num % 2 !== 0)
console.log(odds)   // [1, 3, 5, 7, 9]

// More complex filtering:
let users = [
    { name: "Cedric", age: 25, active: true },
    { name: "Maria", age: 17, active: true },
    { name: "Juan", age: 30, active: false }
]

let activeAdults = users.filter(user => user.active && user.age >= 18)
console.log(activeAdults)  // [{ name: "Cedric", age: 25, active: true }]
```

**When to use:** When you want to keep only items that meet certain criteria.

### reduce() - Combine All Items Into One Value

**This is the most complex but most powerful array method!**

```javascript
let numbers = [1, 2, 3, 4, 5]

// Sum all numbers
let sum = numbers.reduce((total, num) => {
    return total + num
}, 0)  // 0 is the starting value

console.log(sum)  // 15

// Shorter version:
let sum2 = numbers.reduce((total, num) => total + num, 0)
```

**Breaking down reduce:**
1. `total` - The accumulator (running total)
2. `num` - Current item
3. `0` - Starting value for total
4. Function returns new total for next iteration

**The flow:**
```javascript
// First iteration:  total = 0, num = 1, returns 0 + 1 = 1
// Second iteration: total = 1, num = 2, returns 1 + 2 = 3
// Third iteration:  total = 3, num = 3, returns 3 + 3 = 6
// Fourth iteration: total = 6, num = 4, returns 6 + 4 = 10
// Fifth iteration:  total = 10, num = 5, returns 10 + 5 = 15
```

**More reduce examples:**

```javascript
// Find maximum
let max = numbers.reduce((max, num) => num > max ? num : max, numbers[0])

// Count occurrences
let letters = ["a", "b", "a", "c", "b", "a"]
let count = letters.reduce((acc, letter) => {
    acc[letter] = (acc[letter] || 0) + 1
    return acc
}, {})
console.log(count)  // { a: 3, b: 2, c: 1 }

// Flatten array of arrays
let nested = [[1, 2], [3, 4], [5, 6]]
let flat = nested.reduce((acc, arr) => acc.concat(arr), [])
console.log(flat)  // [1, 2, 3, 4, 5, 6]
```

**The analogy:** Like a snowball rolling down a hill, picking up more snow (accumulating) as it goes.

### find() - Get First Item That Passes Test

```javascript
let users = [
    { id: 1, name: "Cedric" },
    { id: 2, name: "Maria" },
    { id: 3, name: "Juan" }
]

let user = users.find(user => user.id === 2)
console.log(user)  // { id: 2, name: "Maria" }

let notFound = users.find(user => user.id === 99)
console.log(notFound)  // undefined
```

**Difference from filter:**
- `filter()` returns array of all matches (or empty array)
- `find()` returns first match (or undefined)

### findIndex() - Get Position of First Match

```javascript
let users = [
    { id: 1, name: "Cedric" },
    { id: 2, name: "Maria" },
    { id: 3, name: "Juan" }
]

let index = users.findIndex(user => user.name === "Maria")
console.log(index)  // 1

let notFound = users.findIndex(user => user.name === "Pedro")
console.log(notFound)  // -1
```

### some() - Check if ANY Item Passes Test

```javascript
let numbers = [1, 2, 3, 4, 5]

let hasEven = numbers.some(num => num % 2 === 0)
console.log(hasEven)  // true (at least one even number)

let hasNegative = numbers.some(num => num < 0)
console.log(hasNegative)  // false (no negative numbers)
```

**Returns:** `true` if at least one item passes, `false` otherwise.

### every() - Check if ALL Items Pass Test

```javascript
let numbers = [2, 4, 6, 8, 10]

let allEven = numbers.every(num => num % 2 === 0)
console.log(allEven)  // true (all are even)

let allPositive = numbers.every(num => num > 0)
console.log(allPositive)  // true

let allLarge = numbers.every(num => num > 5)
console.log(allLarge)  // false (2 and 4 are not > 5)
```

**Returns:** `true` if all items pass, `false` if any fail.

### sort() - Sort Array

**CAUTION:** Modifies original array!

```javascript
let numbers = [3, 1, 4, 1, 5, 9, 2, 6]

// Sort numbers (WRONG way):
numbers.sort()
console.log(numbers)  // [1, 1, 2, 3, 4, 5, 6, 9] - Looks right, but...

let numbers2 = [1, 5, 10, 25, 100]
numbers2.sort()
console.log(numbers2)  // [1, 10, 100, 25, 5] - WRONG!

// Why? sort() converts to strings: "1", "10", "100", "25", "5"
```

**RIGHT way for numbers:**
```javascript
let numbers = [1, 5, 10, 25, 100]

// Ascending
numbers.sort((a, b) => a - b)
console.log(numbers)  // [1, 5, 10, 25, 100]

// Descending
numbers.sort((a, b) => b - a)
console.log(numbers)  // [100, 25, 10, 5, 1]
```

**How the comparison function works:**
- If return < 0: `a` comes before `b`
- If return > 0: `b` comes before `a`
- If return === 0: order unchanged

**Sorting strings:**
```javascript
let fruits = ["banana", "apple", "cherry"]
fruits.sort()
console.log(fruits)  // ["apple", "banana", "cherry"] - Works fine!
```

**Sorting objects:**
```javascript
let users = [
    { name: "Cedric", age: 25 },
    { name: "Maria", age: 30 },
    { name: "Juan", age: 20 }
]

// Sort by age
users.sort((a, b) => a.age - b.age)
console.log(users)
// [{ name: "Juan", age: 20 }, { name: "Cedric", age: 25 }, { name: "Maria", age: 30 }]

// Sort by name
users.sort((a, b) => a.name.localeCompare(b.name))
```

### Method Chaining

You can chain array methods together!

```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let result = numbers
    .filter(num => num % 2 === 0)  // Keep evens: [2, 4, 6, 8, 10]
    .map(num => num * 2)           // Double them: [4, 8, 12, 16, 20]
    .reduce((sum, num) => sum + num, 0)  // Sum them: 60

console.log(result)  // 60

// Real-world example:
let users = [
    { name: "Cedric", age: 25, active: true },
    { name: "Maria", age: 17, active: true },
    { name: "Juan", age: 30, active: false },
    { name: "Ana", age: 22, active: true }
]

let activeAdultNames = users
    .filter(user => user.active)
    .filter(user => user.age >= 18)
    .map(user => user.name)
    .sort()

console.log(activeAdultNames)  // ["Ana", "Cedric"]
```

---

## 4.4 Spread Operator (...)

**The concept:** The spread operator "unpacks" arrays (or objects) into individual elements.

**The analogy:** Like unzipping a bag and spreading out its contents.

### Spread with Arrays

**Copying arrays:**
```javascript
let original = [1, 2, 3]
let copy = [...original]

copy.push(4)
console.log(original)  // [1, 2, 3] (unchanged)
console.log(copy)      // [1, 2, 3, 4]
```

**Combining arrays:**
```javascript
let arr1 = [1, 2, 3]
let arr2 = [4, 5, 6]
let combined = [...arr1, ...arr2]
console.log(combined)  // [1, 2, 3, 4, 5, 6]

// You can add items too:
let withExtra = [...arr1, "middle", ...arr2]
console.log(withExtra)  // [1, 2, 3, "middle", 4, 5, 6]
```

**Passing array items as separate arguments:**
```javascript
let numbers = [1, 5, 3, 9, 2]

// Without spread:
console.log(Math.max(numbers))  // NaN (Math.max doesn't accept arrays)

// With spread:
console.log(Math.max(...numbers))  // 9
// Same as: Math.max(1, 5, 3, 9, 2)
```

### Spread with Objects

```javascript
let person = {
    name: "Cedric",
    age: 25
}

// Copy object
let copy = { ...person }
copy.age = 30
console.log(person.age)  // 25 (unchanged)
console.log(copy.age)    // 30

// Combine objects
let location = {
    city: "Manila",
    country: "Philippines"
}

let fullProfile = { ...person, ...location }
console.log(fullProfile)
// { name: "Cedric", age: 25, city: "Manila", country: "Philippines" }

// Override properties
let updated = {
    ...person,
    age: 26,           // Overrides person.age
    email: "c@example.com"  // Adds new property
}
console.log(updated)
// { name: "Cedric", age: 26, email: "c@example.com" }
```

### Rest Parameters (Opposite of Spread)

**Collect multiple arguments into an array:**
```javascript
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0)
}

console.log(sum(1, 2, 3))        // 6
console.log(sum(1, 2, 3, 4, 5))  // 15

// Mix regular parameters with rest:
function introduce(greeting, ...names) {
    console.log(`${greeting} ${names.join(", ")}!`)
}

introduce("Hello", "Cedric", "Maria", "Juan")
// "Hello Cedric, Maria, Juan!"
```

**In destructuring:**
```javascript
let [first, second, ...rest] = [1, 2, 3, 4, 5]

console.log(first)   // 1
console.log(second)  // 2
console.log(rest)    // [3, 4, 5]
```

---

## 4.5 Destructuring

**The concept:** Destructuring is a shorthand for unpacking values from arrays or objects into separate variables.

**The analogy:** Like unpacking a suitcase and putting each item in its own drawer.

### Array Destructuring

**Basic example:**
```javascript
// Without destructuring:
let colors = ["red", "green", "blue"]
let first = colors[0]
let second = colors[1]
let third = colors[2]

// With destructuring:
let [first, second, third] = ["red", "green", "blue"]
console.log(first)   // "red"
console.log(second)  // "green"
console.log(third)   // "blue"
```

**Skip items:**
```javascript
let [first, , third] = ["red", "green", "blue"]
console.log(first)   // "red"
console.log(third)   // "blue"
```

**Default values:**
```javascript
let [a, b, c = "default"] = [1, 2]
console.log(a)  // 1
console.log(b)  // 2
console.log(c)  // "default" (wasn't in array)
```

**Swapping variables:**
```javascript
let a = 1
let b = 2

[a, b] = [b, a]  // Swap!
console.log(a)  // 2
console.log(b)  // 1
```

**With rest operator:**
```javascript
let [first, ...rest] = [1, 2, 3, 4, 5]
console.log(first)  // 1
console.log(rest)   // [2, 3, 4, 5]
```

### Object Destructuring

**Basic example:**
```javascript
// Without destructuring:
let person = { name: "Cedric", age: 25, city: "Manila" }
let name = person.name
let age = person.age
let city = person.city

// With destructuring:
let { name, age, city } = person
console.log(name)  // "Cedric"
console.log(age)   // 25
console.log(city)  // "Manila"
```

**Different variable names:**
```javascript
let person = { name: "Cedric", age: 25 }

let { name: personName, age: personAge } = person
console.log(personName)  // "Cedric"
console.log(personAge)   // 25
```

**Default values:**
```javascript
let person = { name: "Cedric" }

let { name, age = 30, city = "Unknown" } = person
console.log(name)  // "Cedric"
console.log(age)   // 30 (default)
console.log(city)  // "Unknown" (default)
```

**Nested destructuring:**
```javascript
let user = {
    name: "Cedric",
    address: {
        city: "Manila",
        country: "Philippines"
    }
}

let { name, address: { city, country } } = user
console.log(name)     // "Cedric"
console.log(city)     // "Manila"
console.log(country)  // "Philippines"
```

**In function parameters:**
```javascript
// Instead of:
function greet(user) {
    console.log(`Hello, ${user.name}! You are ${user.age} years old.`)
}

// Use destructuring:
function greet({ name, age }) {
    console.log(`Hello, ${name}! You are ${age} years old.`)
}

greet({ name: "Cedric", age: 25 })
```

**With default values in functions:**
```javascript
function createUser({ name, age = 18, role = "user" }) {
    return { name, age, role }
}

console.log(createUser({ name: "Cedric" }))
// { name: "Cedric", age: 18, role: "user" }
```

---

## 4.6 Objects

**The concept:** Objects store related data using key-value pairs (properties).

**The analogy:** If arrays are numbered lockers, objects are lockers with name labels instead of numbers.

### Creating Objects

```javascript
// Object literal (most common):
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

// Empty object:
let emptyObj = {}

// Using new Object (rare):
let obj = new Object()
obj.name = "Cedric"
```

### Accessing Properties

**Dot notation:**
```javascript
let person = {
    name: "Cedric",
    age: 25
}

console.log(person.name)  // "Cedric"
console.log(person.age)   // 25
```

**Bracket notation:**
```javascript
console.log(person["name"])  // "Cedric"
console.log(person["age"])   // 25

// Useful when property name is in a variable:
let property = "name"
console.log(person[property])  // "Cedric"

// Or when property name has spaces/special characters:
let obj = {
    "first name": "Cedric",
    "favorite-color": "blue"
}
console.log(obj["first name"])      // "Cedric"
console.log(obj["favorite-color"])  // "blue"
```

### Adding/Modifying Properties

```javascript
let person = {
    name: "Cedric"
}

// Add new property:
person.age = 25
person["city"] = "Manila"

// Modify existing:
person.name = "Cedric Santos"

console.log(person)
// { name: "Cedric Santos", age: 25, city: "Manila" }
```

### Deleting Properties

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

delete person.age
console.log(person)  // { name: "Cedric", city: "Manila" }
```

### Checking if Property Exists

```javascript
let person = {
    name: "Cedric",
    age: 25
}

// in operator:
console.log("name" in person)     // true
console.log("email" in person)    // false

// hasOwnProperty:
console.log(person.hasOwnProperty("name"))   // true
console.log(person.hasOwnProperty("email"))  // false

// Simply check if undefined:
if (person.email) {
    console.log(person.email)
} else {
    console.log("No email")
}
```

### Nested Objects

```javascript
let user = {
    name: "Cedric",
    age: 25,
    address: {
        street: "123 Main St",
        city: "Manila",
        country: "Philippines"
    },
    hobbies: ["coding", "reading", "gaming"]
}

console.log(user.address.city)     // "Manila"
console.log(user.hobbies[0])       // "coding"
```

### Methods (Functions in Objects)

```javascript
let person = {
    name: "Cedric",
    age: 25,
    greet: function() {
        console.log("Hello, I'm " + this.name)
    },
    // Shorthand syntax:
    introduce() {
        console.log(`I'm ${this.name}, ${this.age} years old`)
    }
}

person.greet()       // "Hello, I'm Cedric"
person.introduce()   // "I'm Cedric, 25 years old"
```

### Property Shorthand

```javascript
let name = "Cedric"
let age = 25

// Old way:
let person = {
    name: name,
    age: age
}

// Shorthand (when variable name matches property name):
let person2 = {
    name,
    age
}
console.log(person2)  // { name: "Cedric", age: 25 }
```

### Computed Property Names

```javascript
let propName = "favoriteColor"

let person = {
    name: "Cedric",
    [propName]: "blue"  // Property name from variable
}

console.log(person.favoriteColor)  // "blue"
```

---

## 4.7 Object Methods

JavaScript provides useful methods for working with objects.

### Object.keys() - Get All Keys

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

let keys = Object.keys(person)
console.log(keys)  // ["name", "age", "city"]

// Loop through keys:
Object.keys(person).forEach(key => {
    console.log(`${key}: ${person[key]}`)
})
// name: Cedric
// age: 25
// city: Manila
```

### Object.values() - Get All Values

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

let values = Object.values(person)
console.log(values)  // ["Cedric", 25, "Manila"]
```

### Object.entries() - Get Key-Value Pairs

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

let entries = Object.entries(person)
console.log(entries)
// [["name", "Cedric"], ["age", 25], ["city", "Manila"]]

// Great for looping:
Object.entries(person).forEach(([key, value]) => {
    console.log(`${key}: ${value}`)
})
```

### Object.assign() - Copy/Merge Objects

```javascript
let person = { name: "Cedric", age: 25 }
let location = { city: "Manila", country: "Philippines" }

// Merge into new object:
let combined = Object.assign({}, person, location)
console.log(combined)
// { name: "Cedric", age: 25, city: "Manila", country: "Philippines" }

// Copy object:
let copy = Object.assign({}, person)
```

**Modern alternative - spread operator:**
```javascript
let combined = { ...person, ...location }
```

### Object.freeze() - Make Immutable

```javascript
let person = {
    name: "Cedric",
    age: 25
}

Object.freeze(person)

person.age = 30       // Doesn't work (fails silently)
person.city = "Manila"  // Doesn't work
delete person.name    // Doesn't work

console.log(person)  // { name: "Cedric", age: 25 } (unchanged)
```

### Object.seal() - Prevent Adding/Removing Properties

```javascript
let person = {
    name: "Cedric",
    age: 25
}

Object.seal(person)

person.age = 30        // Works (can modify existing)
person.city = "Manila"  // Doesn't work (can't add new)
delete person.name     // Doesn't work (can't delete)

console.log(person)  // { name: "Cedric", age: 30 }
```

---

## 4.8 Reference vs Value

**This is CRITICAL for understanding how JavaScript handles data!**

### Value Types (Primitives)

Primitives are copied by value:

```javascript
let a = 5
let b = a  // b gets a COPY of the value
b = 10

console.log(a)  // 5 (unchanged)
console.log(b)  // 10

// They're completely independent
```

**The analogy:** Like photocopying a document. You have two separate pieces of paper.

### Reference Types (Objects and Arrays)

Objects and arrays are copied by reference:

```javascript
let arr1 = [1, 2, 3]
let arr2 = arr1  // arr2 gets a REFERENCE to the same array
arr2.push(4)

console.log(arr1)  // [1, 2, 3, 4] (changed!)
console.log(arr2)  // [1, 2, 3, 4]

// They both point to the same array in memory!
```

**The analogy:** Like giving someone your hotel room number. You both go to the same room.

### More Examples

**With objects:**
```javascript
let person1 = { name: "Cedric", age: 25 }
let person2 = person1  // Reference to same object
person2.age = 30

console.log(person1.age)  // 30 (changed!)
console.log(person2.age)  // 30
```

**With function parameters:**
```javascript
function changeNumber(num) {
    num = 100
}

function changeArray(arr) {
    arr.push(4)
}

let x = 5
changeNumber(x)
console.log(x)  // 5 (unchanged - primitive)

let arr = [1, 2, 3]
changeArray(arr)
console.log(arr)  // [1, 2, 3, 4] (changed - reference!)
```

### Why const Doesn't Prevent Object Changes

```javascript
const arr = [1, 2, 3]
arr.push(4)  // Works! Changing what's IN the array
console.log(arr)  // [1, 2, 3, 4]

arr = [5, 6, 7]  // ERROR! Can't change which array it points to

const person = { name: "Cedric" }
person.age = 25  // Works! Changing what's IN the object
person = { name: "Maria" }  // ERROR! Can't reassign
```

**The analogy:** `const` is like having a permanent hotel room number. You can rearrange the furniture in the room (modify contents), but you can't change which room you're in (can't reassign).

### How to Actually Copy Arrays/Objects

**Arrays:**
```javascript
let original = [1, 2, 3]

// Method 1: Spread operator
let copy1 = [...original]

// Method 2: slice()
let copy2 = original.slice()

// Method 3: Array.from()
let copy3 = Array.from(original)

// Now they're independent:
copy1.push(4)
console.log(original)  // [1, 2, 3] (unchanged)
console.log(copy1)     // [1, 2, 3, 4]
```

**Objects:**
```javascript
let original = { name: "Cedric", age: 25 }

// Method 1: Spread operator
let copy1 = { ...original }

// Method 2: Object.assign()
let copy2 = Object.assign({}, original)

copy1.age = 30
console.log(original.age)  // 25 (unchanged)
console.log(copy1.age)     // 30
```

**CAUTION - Shallow vs Deep Copy:**
```javascript
let person = {
    name: "Cedric",
    address: {
        city: "Manila"
    }
}

let copy = { ...person }  // Shallow copy
copy.address.city = "Quezon"

console.log(person.address.city)  // "Quezon" (nested object still shared!)

// For deep copy, use:
let deepCopy = JSON.parse(JSON.stringify(person))
// Or use a library like lodash's _.cloneDeep()
```

---

## 4.9 JSON

**The concept:** JSON (JavaScript Object Notation) is a text format for storing and transporting data.

**Why it matters:** JSON is the standard for sending data between browsers and servers, and between different programs.

### What is JSON?

**JSON looks like JavaScript objects, but:**
- Property names MUST be in double quotes
- Only supports: strings, numbers, booleans, null, arrays, objects
- Cannot have: functions, undefined, dates (must convert to strings)

**Valid JSON:**
```json
{
    "name": "Cedric",
    "age": 25,
    "active": true,
    "hobbies": ["coding", "reading"],
    "address": {
        "city": "Manila"
    }
}
```

### JSON.stringify() - Object to JSON String

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

let jsonString = JSON.stringify(person)
console.log(jsonString)
// '{"name":"Cedric","age":25,"city":"Manila"}'

console.log(typeof jsonString)  // "string"
```

**Use cases:**
- Storing data in localStorage
- Sending data to a server
- Saving to a file

**Pretty printing:**
```javascript
let jsonString = JSON.stringify(person, null, 2)
console.log(jsonString)
/*
{
  "name": "Cedric",
  "age": 25,
  "city": "Manila"
}
*/
```

### JSON.parse() - JSON String to Object

```javascript
let jsonString = '{"name":"Cedric","age":25,"city":"Manila"}'

let person = JSON.parse(jsonString)
console.log(person.name)  // "Cedric"
console.log(typeof person)  // "object"
```

**Use cases:**
- Reading data from localStorage
- Receiving data from a server
- Loading data from a file

### Common Use Case - localStorage

```javascript
// Save object to localStorage:
let user = { name: "Cedric", score: 100 }
localStorage.setItem("user", JSON.stringify(user))

// Load object from localStorage:
let savedUser = JSON.parse(localStorage.getItem("user"))
console.log(savedUser.name)  // "Cedric"
```

### What JSON Can't Handle

```javascript
let obj = {
    name: "Cedric",
    greet: function() {  // Functions lost
        console.log("Hello")
    },
    date: new Date(),    // Converted to string
    special: undefined   // Removed entirely
}

let json = JSON.stringify(obj)
console.log(json)
// '{"name":"Cedric","date":"2024-01-15T...Z"}'
// Function and undefined are gone!
```

### Error Handling with JSON.parse()

```javascript
let invalidJSON = '{ invalid json }'

try {
    let obj = JSON.parse(invalidJSON)
} catch (error) {
    console.log("Invalid JSON:", error.message)
}
```

---

# Part 5: Control Flow & Logic

## 5.1 If Statements

**The concept:** If statements let you run code conditionally - only when certain conditions are true.

**The analogy:** Like a choose-your-own-adventure book. Depending on the condition, you follow different paths.

### Basic If Statement

```javascript
let age = 18

if (age >= 18) {
    console.log("You can vote!")
}
```

**Breaking it down:**
- `if` - keyword starting the conditional
- `(age >= 18)` - condition to test (must be true or false)
- `{...}` - code to run if condition is true

### If-Else

```javascript
let age = 15

if (age >= 18) {
    console.log("You can vote!")
} else {
    console.log("You cannot vote yet")
}
```

### Else If (Multiple Conditions)

```javascript
let grade = 85

if (grade >= 90) {
    console.log("A")
} else if (grade >= 80) {
    console.log("B")
} else if (grade >= 70) {
    console.log("C")
} else if (grade >= 60) {
    console.log("D")
} else {
    console.log("F")
}
```

### Nested If Statements

```javascript
let age = 20
let hasLicense = true

if (age >= 18) {
    if (hasLicense) {
        console.log("You can drive!")
    } else {
        console.log("You need a license")
    }
} else {
    console.log("You're too young to drive")
}
```

### Combining Conditions

**AND (&&) - Both must be true:**
```javascript
let age = 20
let hasLicense = true

if (age >= 18 && hasLicense) {
    console.log("You can drive!")
}
```

**OR (||) - At least one must be true:**
```javascript
let isWeekend = true
let isHoliday = false

if (isWeekend || isHoliday) {
    console.log("No work today!")
}
```

**NOT (!) - Flips the condition:**
```javascript
let isRaining = false

if (!isRaining) {
    console.log("Let's go outside!")
}
```

**Complex conditions:**
```javascript
let age = 25
let hasTicket = true
let isVIP = false

if ((age >= 18 && hasTicket) || isVIP) {
    console.log("You can enter the concert!")
}
```

### Guard Clauses (Early Returns)

Instead of deep nesting, return early:

**Without guard clauses (hard to read):**
```javascript
function processOrder(order) {
    if (order) {
        if (order.items.length > 0) {
            if (order.paymentReceived) {
                // Process order
                console.log("Processing order...")
            } else {
                console.log("Payment not received")
            }
        } else {
            console.log("No items in order")
        }
    } else {
        console.log("No order found")
    }
}
```

**With guard clauses (much clearer):**
```javascript
function processOrder(order) {
    if (!order) {
        console.log("No order found")
        return
    }
    
    if (order.items.length === 0) {
        console.log("No items in order")
        return
    }
    
    if (!order.paymentReceived) {
        console.log("Payment not received")
        return
    }
    
    // Process order
    console.log("Processing order...")
}
```

### Single-Line If Statements

```javascript
if (age >= 18) console.log("Adult")

// Can omit braces for single line, but not recommended for readability
```

---

## 5.2 Ternary Operator

**The concept:** Ternary operator is a shorthand for simple if-else statements.

**Syntax:** `condition ? valueIfTrue : valueIfFalse`

### Basic Ternary

```javascript
let age = 20
let status = age >= 18 ? "adult" : "minor"
console.log(status)  // "adult"

// Equivalent to:
let status
if (age >= 18) {
    status = "adult"
} else {
    status = "minor"
}
```

### In Expressions

```javascript
let age = 15
console.log(`You are ${age >= 18 ? "an adult" : "a minor"}`)

let price = isPremium ? 99.99 : 49.99

let discount = isMember ? totalPrice * 0.1 : 0
```

### Nested Ternary (Use Sparingly!)

```javascript
let grade = 85
let letter = grade >= 90 ? "A" :
             grade >= 80 ? "B" :
             grade >= 70 ? "C" :
             grade >= 60 ? "D" : "F"

// This works, but an if-else chain is usually clearer!
```

**When to use ternary:**
- Simple conditions with two outcomes
- Assigning values based on condition
- Inline in JSX (React) or template strings

**When NOT to use:**
- Complex conditions
- More than two outcomes
- When readability suffers

---

## 5.3 Switch Statements

**The concept:** Switch statements check one value against multiple possible cases.

**When to use:** When you have ONE value to check against MANY possible values.

### Basic Switch

```javascript
let day = "Monday"

switch (day) {
    case "Monday":
        console.log("Start of work week")
        break
    case "Friday":
        console.log("Almost weekend!")
        break
    case "Saturday":
    case "Sunday":
        console.log("Weekend!")
        break
    default:
        console.log("Midweek day")
}
```

**Breaking it down:**
- `switch (day)` - Value to check
- `case "Monday":` - If day equals "Monday"
- `break` - Exit the switch (IMPORTANT!)
- `default` - Runs if no cases match (like `else`)

### The CRITICAL Importance of break

**Without break (fall-through behavior):**
```javascript
let x = 1

switch (x) {
    case 1:
        console.log("One")
        // No break! Falls through
    case 2:
        console.log("Two")
        break
}

// Output: "One" then "Two" (both run!)
```

**With break:**
```javascript
let x = 1

switch (x) {
    case 1:
        console.log("One")
        break
    case 2:
        console.log("Two")
        break
}

// Output: "One" (stops after break)
```

### When Fall-Through is Useful

```javascript
let month = "February"

switch (month) {
    case "December":
    case "January":
    case "February":
        console.log("Winter")
        break
    case "March":
    case "April":
    case "May":
        console.log("Spring")
        break
    case "June":
    case "July":
    case "August":
        console.log("Summer")
        break
    case "September":
    case "October":
    case "November":
        console.log("Fall")
        break
}
```

### Switch vs If-Else

**Use switch when:**
```javascript
// Checking ONE variable against MANY exact values
switch (color) {
    case "red":
    case "blue":
    case "green":
        // ...
}
```

**Use if-else when:**
```javascript
// Complex conditions or ranges
if (age < 13) {
    // child
} else if (age < 20) {
    // teen
} else {
    // adult
}

// Multiple different conditions
if (temperature > 30 && humidity > 70) {
    // ...
}
```

---

## 5.4 Logical Operators

### AND Operator (&&)

**Both conditions must be true:**
```javascript
let age = 25
let hasLicense = true

if (age >= 18 && hasLicense) {
    console.log("Can drive")  // Runs only if BOTH are true
}
```

**Truth table:**
```javascript
true && true    // true
true && false   // false
false && true   // false
false && false  // false
```

### OR Operator (||)

**At least one condition must be true:**
```javascript
let isWeekend = false
let isHoliday = true

if (isWeekend || isHoliday) {
    console.log("Day off!")  // Runs if EITHER is true
}
```

**Truth table:**
```javascript
true || true    // true
true || false   // true
false || true   // true
false || false  // false
```

### NOT Operator (!)

**Flips the boolean:**
```javascript
let isRaining = false

if (!isRaining) {
    console.log("Nice weather!")  // Runs because NOT false = true
}
```

**Examples:**
```javascript
!true   // false
!false  // true
!0      // true (0 is falsy)
!""     // true (empty string is falsy)
!"hello" // false ("hello" is truthy)
```

### Operator Precedence

**Order of operations:**
1. `!` (NOT) - Highest
2. `&&` (AND)
3. `||` (OR) - Lowest

```javascript
let a = true
let b = false
let c = true

// Evaluated as: a && b, then result || c
let result = a && b || c  // true

// NOT is evaluated first:
let result2 = !a && b || c  // true
// Step 1: !a = false
// Step 2: false && b = false
// Step 3: false || c = true
```

**Use parentheses for clarity:**
```javascript
let result = (a && b) || c  // Clearer intent
```

---

## 5.5 Short Circuit Evaluation

**The concept:** JavaScript stops evaluating logical operations as soon as it knows the result.

### AND (&&) Short Circuit

**Stops at first falsy value:**
```javascript
false && console.log("This never runs")
// false is falsy, so second part is skipped

true && console.log("This runs!")
// true is truthy, so must check second part
```

**Practical use - check before accessing:**
```javascript
let user = null

// Without short circuit (crashes):
// if (user.name === "Cedric") { }  // ERROR!

// With short circuit (safe):
if (user && user.name === "Cedric") {
    console.log("Hi Cedric!")
}
// user is null (falsy), so user.name is never accessed
```

### OR (||) Short Circuit

**Stops at first truthy value:**
```javascript
true || console.log("This never runs")
// true is truthy, so no need to check further

false || console.log("This runs!")
// false is falsy, so must check second part
```

**Practical use - default values:**
```javascript
function greet(name) {
    name = name || "Guest"
    console.log(`Hello, ${name}!`)
}

greet("Cedric")  // "Hello, Cedric!"
greet()          // "Hello, Guest!"
```

**The pattern:**
```javascript
let result = value1 || value2 || value3 || "default"
// Returns first truthy value, or "default" if all falsy
```

### Return Values (Not Just Booleans!)

```javascript
let a = "hello"
let b = "world"

let result = a || b
console.log(result)  // "hello" (not true!)

let result2 = false || "default"
console.log(result2)  // "default"

let result3 = 0 || 42
console.log(result3)  // 42 (0 is falsy)
```

---

## 5.6 Nullish Coalescing (??)

**The concept:** Like `||`, but only treats `null` and `undefined` as "no value."

**The problem with ||:**
```javascript
let count = 0
let result = count || 10
console.log(result)  // 10 (0 is falsy, so uses default!)
// But 0 might be a valid value!
```

**Solution with ??:**
```javascript
let count = 0
let result = count ?? 10
console.log(result)  // 0 (only null/undefined trigger default)

let count2 = null
let result2 = count2 ?? 10
console.log(result2)  // 10
```

**Comparison:**
```javascript
0 || 10        // 10 (0 is falsy)
0 ?? 10        // 0 (0 is not null/undefined)

"" || "default"    // "default" ("" is falsy)
"" ?? "default"    // "" ("" is not null/undefined)

false || true      // true (false is falsy)
false ?? true      // false (false is not null/undefined)

null || "default"  // "default"
null ?? "default"  // "default" (both work for null)
```

**When to use which:**
- Use `??` when 0, false, or "" are valid values
- Use `||` for general "any falsy value" defaults

---

## 5.7 Optional Chaining (?.)

**The concept:** Safely access nested properties without crashing if something is null/undefined.

**The problem:**
```javascript
let user = {
    name: "Cedric"
    // No address property
}

console.log(user.address.city)  // ERROR! Cannot read 'city' of undefined
```

**Old solution (verbose):**
```javascript
let city = user && user.address && user.address.city
// or
let city = user.address ? user.address.city : undefined
```

**New solution with ?. (clean):**
```javascript
let city = user.address?.city
console.log(city)  // undefined (no error!)
```

**How it works:**
- If `user.address` is null or undefined, stop and return undefined
- Otherwise, access `.city`

### Multiple Levels

```javascript
let user = {
    profile: {
        address: {
            city: "Manila"
        }
    }
}

// Check each level:
let city = user?.profile?.address?.city  // "Manila"

// If any level is missing:
let user2 = {
    profile: null
}
let city2 = user2?.profile?.address?.city  // undefined (no error!)
```

### With Arrays

```javascript
let users = null

let firstUser = users?.[0]  // undefined (no error!)

let users2 = [{ name: "Cedric" }]
let firstName = users2?.[0]?.name  // "Cedric"
```

### With Functions

```javascript
let obj = {
    greet: function() {
        return "Hello!"
    }
}

console.log(obj.greet?.())  // "Hello!"

let obj2 = {}
console.log(obj2.greet?.())  // undefined (no error!)
```

---

# Part 6: Loops & Iteration

## 6.1 For Loops

**The concept:** For loops repeat code a specific number of times.

**The analogy:** Like doing laps around a track. You know you need to do 10 laps, so you count them as you go.

### Basic For Loop

```javascript
for (let i = 0; i < 5; i++) {
    console.log(i)
}
// Output: 0, 1, 2, 3, 4
```

**Breaking it down:**
1. `let i = 0` - **Initialization** (start at 0)
2. `i < 5` - **Condition** (keep going while true)
3. `i++` - **Update** (add 1 after each iteration)

**The flow:**
```javascript
// Iteration 1: i = 0, check 0 < 5 (true), run code, i becomes 1
// Iteration 2: i = 1, check 1 < 5 (true), run code, i becomes 2
// Iteration 3: i = 2, check 2 < 5 (true), run code, i becomes 3
// Iteration 4: i = 3, check 3 < 5 (true), run code, i becomes 4
// Iteration 5: i = 4, check 4 < 5 (true), run code, i becomes 5
// Check: 5 < 5 (false), STOP
```

### Looping Through Arrays

```javascript
let fruits = ["apple", "banana", "cherry"]

for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i])
}
// Output: apple, banana, cherry
```

**With index and value:**
```javascript
for (let i = 0; i < fruits.length; i++) {
    console.log(`${i}: ${fruits[i]}`)
}
// 0: apple
// 1: banana
// 2: cherry
```

### Different Starting Points and Steps

**Start at different number:**
```javascript
for (let i = 1; i <= 5; i++) {
    console.log(i)
}
// Output: 1, 2, 3, 4, 5
```

**Count backwards:**
```javascript
for (let i = 5; i > 0; i--) {
    console.log(i)
}
// Output: 5, 4, 3, 2, 1
```

**Count by 2:**
```javascript
for (let i = 0; i < 10; i += 2) {
    console.log(i)
}
// Output: 0, 2, 4, 6, 8
```

### Nested For Loops

```javascript
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 3; j++) {
        console.log(`i: ${i}, j: ${j}`)
    }
}
// i: 1, j: 1
// i: 1, j: 2
// i: 1, j: 3
// i: 2, j: 1
// i: 2, j: 2
// ...
```

**Practical example - multiplication table:**
```javascript
for (let i = 1; i <= 5; i++) {
    for (let j = 1; j <= 5; j++) {
        console.log(`${i} x ${j} = ${i * j}`)
    }
}
```

### break - Exit Loop Early

```javascript
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break  // Exit loop completely
    }
    console.log(i)
}
// Output: 0, 1, 2, 3, 4 (stops at 5)
```

**Finding item in array:**
```javascript
let numbers = [1, 5, 8, 3, 9, 2]
let target = 8
let found = false

for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] === target) {
        console.log(`Found ${target} at index ${i}`)
        found = true
        break  // No need to keep looking
    }
}
```

### continue - Skip Current Iteration

```javascript
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) {
        continue  // Skip even numbers
    }
    console.log(i)
}
// Output: 1, 3, 5, 7, 9 (odd numbers only)
```

### Common For Loop Mistakes

**Mistake 1: Off-by-one error**
```javascript
let arr = [1, 2, 3]

// WRONG - goes one too far:
for (let i = 0; i <= arr.length; i++) {
    console.log(arr[i])  // arr[3] is undefined!
}

// RIGHT:
for (let i = 0; i < arr.length; i++) {
    console.log(arr[i])
}
```

**Mistake 2: Infinite loop (condition never becomes false)**
```javascript
// WRONG - i never changes:
for (let i = 0; i < 5;) {  // Missing i++
    console.log(i)  // Prints 0 forever!
}

// WRONG - condition always true:
for (let i = 0; i > -1; i++) {  // i will always be > -1
    console.log(i)  // Never stops!
}
```

**Mistake 3: Modifying array while looping**
```javascript
let numbers = [1, 2, 3, 4, 5]

// WRONG - array length changes during loop:
for (let i = 0; i < numbers.length; i++) {
    numbers.pop()  // Removes items while looping!
}

// RIGHT - store length first:
let length = numbers.length
for (let i = 0; i < length; i++) {
    // ...
}
```

---

## 6.2 While Loops

**The concept:** While loops repeat code as long as a condition is true.

**When to use:** When you don't know how many times to loop (unlike for loops where you usually do).

### Basic While Loop

```javascript
let i = 0

while (i < 5) {
    console.log(i)
    i++
}
// Output: 0, 1, 2, 3, 4
```

**Comparison to for loop:**
```javascript
// For loop:
for (let i = 0; i < 5; i++) {
    console.log(i)
}

// While loop (equivalent):
let i = 0
while (i < 5) {
    console.log(i)
    i++
}
```

### When While Loops Shine

**User input (unknown iterations):**
```javascript
let password = ""

while (password !== "secret") {
    password = prompt("Enter password:")
}

console.log("Access granted!")
```

**Processing until condition met:**
```javascript
let balance = 1000

while (balance > 0) {
    let withdrawal = Math.random() * 200
    balance -= withdrawal
    console.log(`Withdrew $${withdrawal.toFixed(2)}, Balance: $${balance.toFixed(2)}`)
}
```

**Reading data with unknown length:**
```javascript
let line = readNextLine()  // Hypothetical function

while (line !== null) {
    processLine(line)
    line = readNextLine()
}
```

### Do-While Loop

**The concept:** Runs code at least once, then checks condition.

```javascript
let i = 0

do {
    console.log(i)
    i++
} while (i < 5)
// Output: 0, 1, 2, 3, 4
```

**Key difference:**
```javascript
// While loop - might not run at all:
let x = 10
while (x < 5) {
    console.log("Never runs")
}

// Do-while - always runs at least once:
let y = 10
do {
    console.log("Runs once!")  // Executes
} while (y < 5)
```

**Practical use - input validation:**
```javascript
let age

do {
    age = prompt("Enter your age (1-120):")
} while (age < 1 || age > 120)

console.log("Valid age entered!")
```

### While Loop Warnings

**Infinite loops:**
```javascript
// WRONG - never stops:
while (true) {
    console.log("Forever!")
}

// WRONG - condition never becomes false:
let i = 0
while (i < 5) {
    console.log(i)
    // Forgot i++!
}
```

**Always ensure:**
1. Condition will eventually become false
2. Variables in condition are updated inside loop

---

## 6.3 For...Of Loop

**The concept:** For...of loops iterate through values in an array (or other iterables).

**Modern, clean syntax for arrays!**

### Basic For...Of

```javascript
let fruits = ["apple", "banana", "cherry"]

for (let fruit of fruits) {
    console.log(fruit)
}
// Output: apple, banana, cherry
```

**Comparison to regular for loop:**
```javascript
// Old way:
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i])
}

// New way (cleaner):
for (let fruit of fruits) {
    console.log(fruit)
}
```

### When to Use For...Of

**When you need:** Values only (not indexes)

```javascript
let numbers = [1, 2, 3, 4, 5]
let sum = 0

for (let num of numbers) {
    sum += num
}
console.log(sum)  // 15
```

**When you need indexes:** Use regular for or forEach

```javascript
// Need index? Use regular for:
for (let i = 0; i < fruits.length; i++) {
    console.log(`${i}: ${fruits[i]}`)
}

// Or entries():
for (let [index, fruit] of fruits.entries()) {
    console.log(`${index}: ${fruit}`)
}
```

### Works with Strings

```javascript
let word = "hello"

for (let char of word) {
    console.log(char)
}
// Output: h, e, l, l, o
```

### With break and continue

```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

for (let num of numbers) {
    if (num === 5) break       // Stop at 5
    if (num % 2 === 0) continue // Skip evens
    console.log(num)
}
// Output: 1, 3
```

---

## 6.4 For...In Loop

**The concept:** For...in loops iterate through keys/properties of an object.

**Use for objects, NOT arrays!**

### Basic For...In

```javascript
let person = {
    name: "Cedric",
    age: 25,
    city: "Manila"
}

for (let key in person) {
    console.log(`${key}: ${person[key]}`)
}
// name: Cedric
// age: 25
// city: Manila
```

**Breaking it down:**
- `key` gets each property name (string)
- `person[key]` accesses the value for that property

### Why Not Use For...In with Arrays?

```javascript
let fruits = ["apple", "banana", "cherry"]

// Works, but...
for (let index in fruits) {
    console.log(index, fruits[index])
}
// 0 apple
// 1 banana
// 2 cherry

// Problems:
// 1. index is a STRING ("0", "1", "2"), not a number
// 2. Picks up extra properties if they exist
// 3. Order not guaranteed
```

**For arrays, use:**
- For...of (values)
- Regular for (indexes)
- forEach (callback)

### For...In with Object Methods

```javascript
let user = {
    firstName: "Cedric",
    lastName: "Santos",
    age: 25,
    getFullName() {
        return `${this.firstName} ${this.lastName}`
    }
}

for (let key in user) {
    console.log(`${key}: ${user[key]}`)
}
// firstName: Cedric
// lastName: Santos
// age: 25
// getFullName: [Function: getFullName]
```

**Filter out functions:**
```javascript
for (let key in user) {
    if (typeof user[key] !== 'function') {
        console.log(`${key}: ${user[key]}`)
    }
}
```

---

## 6.5 Recursion

**The concept:** Recursion is when a function calls itself.

**The analogy:** Like Russian nesting dolls - each doll contains a smaller version of itself, until you reach the tiniest one.

### Basic Recursion Example

```javascript
function countdown(n) {
    if (n === 0) {
        console.log("Done!")
        return
    }
    
    console.log(n)
    countdown(n - 1)  // Function calls itself!
}

countdown(3)
// Output:
// 3
// 2
// 1
// Done!
```

**The flow:**
```javascript
countdown(3)
  → console.log(3)
  → countdown(2)
      → console.log(2)
      → countdown(1)
          → console.log(1)
          → countdown(0)
              → console.log("Done!")
              → return
```

### The CRITICAL Rule: Base Case

**You MUST have a stopping condition, or it runs forever!**

```javascript
// WRONG - infinite recursion:
function badRecursion(n) {
    console.log(n)
    badRecursion(n - 1)  // Never stops!
}

// RIGHT - has base case:
function goodRecursion(n) {
    if (n === 0) return  // BASE CASE - stops here!
    console.log(n)
    goodRecursion(n - 1)
}
```

### Practical Recursion Examples

**Factorial:**
```javascript
// 5! = 5 × 4 × 3 × 2 × 1 = 120

function factorial(n) {
    if (n === 1) return 1     // Base case
    return n * factorial(n - 1)
}

console.log(factorial(5))  // 120

// How it works:
// factorial(5) = 5 * factorial(4)
// factorial(4) = 4 * factorial(3)
// factorial(3) = 3 * factorial(2)
// factorial(2) = 2 * factorial(1)
// factorial(1) = 1
// Then multiply back: 2*1=2, 3*2=6, 4*6=24, 5*24=120
```

**Sum array:**
```javascript
function sumArray(arr) {
    if (arr.length === 0) return 0  // Base case
    return arr[0] + sumArray(arr.slice(1))
}

console.log(sumArray([1, 2, 3, 4, 5]))  // 15

// How it works:
// sumArray([1,2,3,4,5]) = 1 + sumArray([2,3,4,5])
// sumArray([2,3,4,5]) = 2 + sumArray([3,4,5])
// sumArray([3,4,5]) = 3 + sumArray([4,5])
// sumArray([4,5]) = 4 + sumArray([5])
// sumArray([5]) = 5 + sumArray([])
// sumArray([]) = 0
// Then add back: 5+0=5, 4+5=9, 3+9=12, 2+12=14, 1+14=15
```

**Fibonacci sequence:**
```javascript
// 0, 1, 1, 2, 3, 5, 8, 13...
// Each number is sum of previous two

function fibonacci(n) {
    if (n <= 1) return n  // Base cases
    return fibonacci(n - 1) + fibonacci(n - 2)
}

console.log(fibonacci(7))  // 13
// 0, 1, 1, 2, 3, 5, 8, 13
```

### When to Use Recursion

**Good for:**
- Tree/nested structures (file systems, DOM, etc.)
- Mathematical sequences
- Divide-and-conquer algorithms
- Problems naturally defined recursively

**Not ideal for:**
- Simple iterations (use loops instead)
- Very deep recursion (can cause stack overflow)
- When performance matters (recursion is slower)

### Recursion vs Loops

**Most recursive functions can be written as loops:**

```javascript
// Recursive:
function sumRecursive(n) {
    if (n === 0) return 0
    return n + sumRecursive(n - 1)
}

// Loop (more efficient):
function sumLoop(n) {
    let sum = 0
    for (let i = 1; i <= n; i++) {
        sum += i
    }
    return sum
}

console.log(sumRecursive(100))  // 5050
console.log(sumLoop(100))       // 5050
```

**Use loops when possible for:**
- Better performance
- Clearer code
- Avoid stack overflow

**Use recursion for:**
- Naturally recursive problems
- Tree/nested data structures
- Elegant solutions to complex problems

---

## 6.6 Loop Comparison Summary

```javascript
let fruits = ["apple", "banana", "cherry"]

// 1. REGULAR FOR - Most control, best for complex logic
for (let i = 0; i < fruits.length; i++) {
    console.log(i, fruits[i])
}

// 2. FOR...OF - Clean, modern, best for simple iteration
for (let fruit of fruits) {
    console.log(fruit)
}

// 3. FOREACH - Functional style, can't break/continue
fruits.forEach((fruit, index) => {
    console.log(index, fruit)
})

// 4. WHILE - Best when iterations unknown
let i = 0
while (i < fruits.length) {
    console.log(fruits[i])
    i++
}

// 5. FOR...IN - For objects only!
let person = { name: "Cedric", age: 25 }
for (let key in person) {
    console.log(key, person[key])
}
```

**Quick decision guide:**

| Need | Use |
|------|-----|
| Values only from array | for...of |
| Index and value from array | regular for or forEach |
| Object properties | for...in or Object.keys() |
| Unknown iterations | while |
| Array transformation | map/filter/reduce |
| Must break/continue | for or while (not forEach) |

---

# Part 7: Advanced Concepts

## 7.1 Error Handling

**The concept:** Error handling lets you catch and deal with errors gracefully instead of crashing.

**The analogy:** Like having a safety net under a tightrope walker. If something goes wrong, you can catch it and recover.

### Try-Catch-Finally

```javascript
try {
    // Code that might error
    let result = riskyOperation()
    console.log(result)
} catch (error) {
    // Code that runs if error occurs
    console.log("An error occurred:", error.message)
} finally {
    // Code that ALWAYS runs (optional)
    console.log("Cleanup code here")
}
```

**Breaking it down:**
- `try` - Attempt this code
- `catch` - If error happens, run this
- `finally` - Always run this (even if no error)

### Basic Example

```javascript
try {
    let data = JSON.parse('{ invalid json }')
} catch (error) {
    console.log("Failed to parse JSON:", error.message)
}

console.log("Program continues...")
// Output:
// Failed to parse JSON: Unexpected token i in JSON at position 2
// Program continues...
```

**Without try-catch (crashes):**
```javascript
let data = JSON.parse('{ invalid json }')  // ERROR! Program stops
console.log("This never runs")
```

### The Error Object

```javascript
try {
    throw new Error("Something went wrong!")
} catch (error) {
    console.log(error.name)     // "Error"
    console.log(error.message)  // "Something went wrong!"
    console.log(error.stack)    // Full stack trace
}
```

### Throwing Your Own Errors

```javascript
function divide(a, b) {
    if (b === 0) {
        throw new Error("Cannot divide by zero")
    }
    return a / b
}

try {
    let result = divide(10, 0)
} catch (error) {
    console.log("Error:", error.message)
}
// Output: Error: Cannot divide by zero
```

### Different Error Types

```javascript
// ReferenceError
try {
    console.log(nonExistentVariable)
} catch (error) {
    console.log(error.name)  // "ReferenceError"
}

// TypeError
try {
    null.toString()
} catch (error) {
    console.log(error.name)  // "TypeError"
}

// SyntaxError
try {
    eval('{ invalid syntax')
} catch (error) {
    console.log(error.name)  // "SyntaxError"
}
```

### Finally Block

**Runs no matter what:**
```javascript
function readFile() {
    let file
    try {
        file = openFile("data.txt")
        let content = file.read()
        return content
    } catch (error) {
        console.log("Error reading file:", error.message)
        return null
    } finally {
        if (file) {
            file.close()  // Always close, even if error
        }
        console.log("Cleanup complete")
    }
}
```

### Best Practices

**1. Catch specific errors you can handle:**
```javascript
try {
    let data = await fetchUserData()
    processData(data)
} catch (error) {
    if (error.message.includes("network")) {
        console.log("Network error, retrying...")
        retry()
    } else {
        throw error  // Re-throw if we can't handle it
    }
}
```

**2. Don't catch errors you can't fix:**
```javascript
// BAD - swallows all errors:
try {
    doComplexOperation()
} catch (error) {
    // Silently fail - bad!
}

// GOOD - log and inform user:
try {
    doComplexOperation()
} catch (error) {
    console.error("Operation failed:", error)
    showUserError("Sorry, something went wrong")
}
```

**3. Use custom error messages:**
```javascript
function processUser(user) {
    if (!user) {
        throw new Error("User object is required")
    }
    if (!user.email) {
        throw new Error("User email is required")
    }
    // ...
}
```

---

## 7.2 Math Object

**The concept:** Math is a built-in object with mathematical constants and functions.

### Common Math Methods

**Math.random() - Random numbers:**
```javascript
Math.random()  // Random number between 0 (inclusive) and 1 (exclusive)
// Examples: 0.437, 0.891, 0.023

// Random integer between 0 and 9:
Math.floor(Math.random() * 10)

// Random integer between 1 and 10:
Math.floor(Math.random() * 10) + 1

// Random integer between min and max (inclusive):
function randomInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min
}

console.log(randomInt(1, 100))  // Random number 1-100
```

**Rounding numbers:**
```javascript
Math.floor(4.7)   // 4 (rounds down)
Math.ceil(4.2)    // 5 (rounds up)
Math.round(4.5)   // 5 (rounds to nearest)
Math.round(4.4)   // 4

Math.trunc(4.7)   // 4 (removes decimal part)
Math.trunc(-4.7)  // -4
```

**Min and Max:**
```javascript
Math.max(1, 5, 3, 9, 2)     // 9
Math.min(1, 5, 3, 9, 2)     // 1

// With array:
let numbers = [1, 5, 3, 9, 2]
Math.max(...numbers)  // 9 (using spread operator)
```

**Power and Roots:**
```javascript
Math.pow(2, 3)    // 8 (2³)
Math.sqrt(16)     // 4 (square root)
Math.cbrt(27)     // 3 (cube root)

// Or use ** operator:
2 ** 3  // 8
```

**Absolute value:**
```javascript
Math.abs(-5)   // 5
Math.abs(5)    // 5
```

### Math Constants

```javascript
Math.PI      // 3.141592653589793
Math.E       // 2.718281828459045 (Euler's number)
```

**Practical examples:**
```javascript
// Circle area
function circleArea(radius) {
    return Math.PI * radius ** 2
}

// Distance between two points
function distance(x1, y1, x2, y2) {
    return Math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)
}

// Random array element
function randomElement(arr) {
    let index = Math.floor(Math.random() * arr.length)
    return arr[index]
}
```

---

# Part 8: Asynchronous JavaScript ⭐

**This is where JavaScript gets REALLY powerful and REALLY confusing!**

## 8.1 Understanding Asynchronous Code

**The concept:** Asynchronous code allows JavaScript to do multiple things without waiting for each to finish.

**The analogy:** Imagine a restaurant:
- **Synchronous**: Chef makes one dish completely before starting the next (slow!)
- **Asynchronous**: Chef starts multiple dishes, works on others while things cook (fast!)

### Synchronous vs Asynchronous

**Synchronous (blocking):**
```javascript
console.log("1. Start")

// This takes 3 seconds and BLOCKS everything:
let result = longRunningTask()  // Waits here...

console.log("2. After task")
console.log("3. End")

// Output (after 3 seconds):
// 1. Start
// 2. After task
// 3. End
```

**Asynchronous (non-blocking):**
```javascript
console.log("1. Start")

// This takes 3 seconds but DOESN'T block:
setTimeout(() => {
    console.log("2. After task")
}, 3000)

console.log("3. End")

// Output (immediately):
// 1. Start
// 3. End
// ... 3 seconds later ...
// 2. After task
```

**Why JavaScript needs async:**
- Fetching data from servers (slow!)
- Reading files (slow!)
- Waiting for user input
- Timers and animations
- Any operation that takes time

Without async, the entire page would freeze while waiting!

---

## 8.2 The Call Stack ⭐

**The concept:** The call stack is where JavaScript keeps track of which function is currently running.

**The analogy:** Like a stack of plates. You can only add to the top (push) or remove from the top (pop).

### How the Call Stack Works

```javascript
function first() {
    console.log("First")
    second()
    console.log("First again")
}

function second() {
    console.log("Second")
    third()
    console.log("Second again")
}

function third() {
    console.log("Third")
}

first()
```

**Call stack visualization:**
```
Start:
[]

first() called:
[first]

second() called from first:
[first, second]

third() called from second:
[first, second, third]

third() completes:
[first, second]  ← third removed

second() completes:
[first]  ← second removed

first() completes:
[]  ← first removed
```

**Output:**
```
First
Second
Third
Second again
First again
```

### Stack Overflow

**What happens if stack gets too deep:**
```javascript
function recursive() {
    recursive()  // Calls itself forever
}

recursive()
// ERROR: Maximum call stack size exceeded
```

**The stack literally fills up and crashes!**

---

## 8.3 The Event Loop ⭐⭐⭐

**THE MOST IMPORTANT CONCEPT FOR UNDERSTANDING JAVASCRIPT!**

**The concept:** The event loop is how JavaScript handles asynchronous code using a single thread.

### JavaScript's Components

```
┌─────────────────────┐
│    Call Stack       │  ← Where code runs
└─────────────────────┘
           ↑
           │
┌─────────────────────┐
│    Event Loop       │  ← Coordinator
└─────────────────────┘
           ↑
           │
┌─────────────────────┐
│   Callback Queue    │  ← Waiting callbacks
│   (Task Queue)      │
└─────────────────────┘
           ↑
           │
┌─────────────────────┐
│  Web APIs / Node    │  ← Async operations
│  (setTimeout, etc)  │
└─────────────────────┘
```

### How It Works

**The Event Loop checks:**
1. Is the call stack empty?
2. If yes, take first callback from queue and push to stack
3. Repeat forever

**Example:**

```javascript
console.log("1")

setTimeout(() => {
    console.log("2")
}, 0)

console.log("3")

// Output:
// 1
// 3
// 2  (even though timeout is 0!)
```

**Step-by-step:**

```
1. console.log("1") → Call Stack → "1" printed → Removed from stack

2. setTimeout(() => console.log("2"), 0)
   → Sent to Web APIs
   → setTimeout removed from stack
   → After 0ms, callback added to Callback Queue

3. console.log("3") → Call Stack → "3" printed → Removed from stack

4. Call Stack now empty!
   → Event Loop checks Callback Queue
   → Finds setTimeout callback
   → Moves to Call Stack
   → console.log("2") runs → "2" printed
```

**Visual:**
```
Synchronous code ALWAYS runs first:
Call Stack: [console.log("1")] → Run
Call Stack: [console.log("3")] → Run
Call Stack: [] (empty!)

Then async callbacks from queue:
Callback Queue: [() => console.log("2")]
Event Loop: "Stack empty? Yes! Move callback to stack"
Call Stack: [() => console.log("2")] → Run
```

### Another Example

```javascript
console.log("Start")

setTimeout(() => {
    console.log("Timeout 1")
}, 1000)

setTimeout(() => {
    console.log("Timeout 2")
}, 0)

console.log("End")

// Output:
// Start
// End
// Timeout 2 (after 0ms)
// Timeout 1 (after 1000ms)
```

**Why this order:**
1. All synchronous code runs first (Start, End)
2. Call stack empties
3. Event loop checks queue
4. Timeout 2 callback runs first (shorter timeout)
5. Timeout 1 callback runs after 1 second

### Microtask Queue (Advanced)

**There are actually TWO queues:**

1. **Microtask Queue** (higher priority)
   - Promises
   - queueMicrotask()

2. **Callback Queue / Task Queue** (lower priority)
   - setTimeout
   - setInterval
   - DOM events

**Microtasks run BEFORE callbacks:**
```javascript
console.log("1")

setTimeout(() => console.log("2"), 0)  // Callback Queue

Promise.resolve().then(() => console.log("3"))  // Microtask Queue

console.log("4")

// Output:
// 1
// 4
// 3  (Promise microtask runs first!)
// 2  (Then setTimeout callback)
```

**The rule:**
1. Run all synchronous code
2. Run ALL microtasks
3. Run ONE callback
4. Repeat steps 2-3

---

## 8.4 setTimeout and setInterval

### setTimeout - Delay Code Execution

**Run code once after delay:**
```javascript
console.log("Start")

setTimeout(() => {
    console.log("Delayed message")
}, 2000)  // Wait 2000 milliseconds (2 seconds)

console.log("End")

// Output immediately:
// Start
// End
// ... 2 seconds later ...
// Delayed message
```

**Storing timeout ID:**
```javascript
let timeoutId = setTimeout(() => {
    console.log("This might not run")
}, 3000)

// Cancel the timeout:
clearTimeout(timeoutId)
```

**With parameters:**
```javascript
function greet(name, greeting) {
    console.log(`${greeting}, ${name}!`)
}

setTimeout(greet, 1000, "Cedric", "Hello")
// After 1 second: "Hello, Cedric!"
```

### setInterval - Repeat Code

**Run code repeatedly at intervals:**
```javascript
let count = 0

let intervalId = setInterval(() => {
    count++
    console.log(`Count: ${count}`)
    
    if (count === 5) {
        clearInterval(intervalId)  // Stop after 5
    }
}, 1000)

// Output every second:
// Count: 1
// Count: 2
// Count: 3
// Count: 4
// Count: 5
```

**Countdown timer:**
```javascript
let seconds = 10

let timer = setInterval(() => {
    console.log(seconds)
    seconds--
    
    if (seconds < 0) {
        clearInterval(timer)
        console.log("Time's up!")
    }
}, 1000)
```

### Common Gotchas

**1. setTimeout is not precise:**
```javascript
setTimeout(() => {
    console.log("Not exactly after 1000ms")
}, 1000)

// Might run after 1001ms, 1005ms, etc.
// Depends on call stack and event loop
```

**2. setInterval can stack up:**
```javascript
// BAD - if callback takes longer than interval:
setInterval(() => {
    longRunningTask()  // Takes 2 seconds
}, 1000)  // Runs every 1 second
// Multiple calls stack up!

// GOOD - use setTimeout recursively:
function repeat() {
    longRunningTask()
    setTimeout(repeat, 1000)
}
repeat()
```

**3. this binding in setTimeout:**
```javascript
let obj = {
    name: "Cedric",
    greet() {
        setTimeout(function() {
            console.log(this.name)  // undefined! 'this' is wrong
        }, 1000)
    }
}

// Fix 1: Arrow function
greet() {
    setTimeout(() => {
        console.log(this.name)  // Works!
    }, 1000)
}

// Fix 2: Bind
greet() {
    setTimeout(function() {
        console.log(this.name)
    }.bind(this), 1000)
}
```

---

## 8.5 Callbacks for Async Operations

**The concept:** Callbacks can handle asynchronous results.

### Async Callback Pattern

```javascript
function fetchData(callback) {
    setTimeout(() => {
        let data = { name: "Cedric", age: 25 }
        callback(data)
    }, 1000)
}

fetchData((data) => {
    console.log("Received:", data)
})

console.log("Request sent")

// Output:
// Request sent
// ... 1 second later ...
// Received: { name: "Cedric", age: 25 }
```

### Error-First Callbacks

**Node.js convention:**
```javascript
function fetchData(callback) {
    setTimeout(() => {
        let error = null
        let data = { name: "Cedric" }
        
        // Call callback with (error, data)
        callback(error, data)
    }, 1000)
}

fetchData((error, data) => {
    if (error) {
        console.log("Error:", error)
        return
    }
    console.log("Success:", data)
})
```

### Callback Hell (The Problem!)

**When callbacks nest deeply:**
```javascript
getData(function(a) {
    getMoreData(a, function(b) {
        getEvenMoreData(b, function(c) {
            getYetMoreData(c, function(d) {
                getFinalData(d, function(e) {
                    console.log("Finally done!", e)
                })
            })
        })
    })
})

// This is hard to read, maintain, and handle errors!
```

**This is why Promises and async/await were invented!**

---

## 8.6 Promises ⭐

**The concept:** Promises represent a value that will be available in the future.

**The analogy:** Like ordering food at a restaurant:
- You get a receipt (promise) immediately
- Food is being prepared (pending)
- Eventually: you get food (resolved) or they're out (rejected)

### Promise States

```
┌─────────────┐
│   Pending   │  ← Initial state
└─────────────┘
       │
       ├─────────────────┐
       │                 │
       ↓                 ↓
┌─────────────┐   ┌─────────────┐
│  Fulfilled  │   │  Rejected   │
│ (Resolved)  │   │   (Error)   │
└─────────────┘   └─────────────┘
```

### Creating a Promise

```javascript
let promise = new Promise((resolve, reject) => {
    // Async operation here
    let success = true
    
    if (success) {
        resolve("Success!")  // Promise fulfilled
    } else {
        reject("Error!")     // Promise rejected
    }
})
```

### Using Promises - then/catch

```javascript
promise
    .then((result) => {
        console.log("Success:", result)
    })
    .catch((error) => {
        console.log("Error:", error)
    })
```

**Complete example:**
```javascript
function fetchUser(id) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (id > 0) {
                resolve({ id: id, name: "Cedric" })
            } else {
                reject("Invalid ID")
            }
        }, 1000)
    })
}

fetchUser(1)
    .then(user => {
        console.log("User:", user)
    })
    .catch(error => {
        console.log("Error:", error)
    })
```

### Promise Chaining

**Return promises to chain:**
```javascript
fetchUser(1)
    .then(user => {
        console.log("Got user:", user.name)
        return fetchPosts(user.id)  // Returns another promise
    })
    .then(posts => {
        console.log("Got posts:", posts)
        return fetchComments(posts[0].id)
    })
    .then(comments => {
        console.log("Got comments:", comments)
    })
    .catch(error => {
        console.log("Error anywhere in chain:", error)
    })

// Much cleaner than callback hell!
```

### Promise Methods

**Promise.all() - Wait for all:**
```javascript
let promise1 = fetchUser(1)
let promise2 = fetchUser(2)
let promise3 = fetchUser(3)

Promise.all([promise1, promise2, promise3])
    .then(results => {
        console.log("All users:", results)
        // results is an array: [user1, user2, user3]
    })
    .catch(error => {
        console.log("One failed:", error)
    })

// If ANY promise fails, entire Promise.all fails
```

**Promise.race() - First to finish:**
```javascript
let slow = new Promise(resolve => setTimeout(() => resolve("slow"), 2000))
let fast = new Promise(resolve => setTimeout(() => resolve("fast"), 1000))

Promise.race([slow, fast])
    .then(result => {
        console.log("Winner:", result)  // "fast"
    })
```

**Promise.allSettled() - Wait for all, regardless of success:**
```javascript
let promises = [
    Promise.resolve("Success"),
    Promise.reject("Error"),
    Promise.resolve("Another success")
]

Promise.allSettled(promises)
    .then(results => {
        results.forEach(result => {
            if (result.status === "fulfilled") {
                console.log("Success:", result.value)
            } else {
                console.log("Failed:", result.reason)
            }
        })
    })
```

---

## 8.7 Async/Await ⭐⭐

**The concept:** Async/await makes asynchronous code look and behave more like synchronous code.

**This is the MODERN way to handle async operations!**

### Basic Async Function

```javascript
async function fetchData() {
    return "Hello"
}

// Async functions always return a promise
fetchData().then(data => console.log(data))  // "Hello"
```

### The await Keyword

**Pause until promise resolves:**
```javascript
async function getUser() {
    console.log("Fetching user...")
    
    let user = await fetchUser(1)  // Wait for promise
    console.log("Got user:", user)
    
    let posts = await fetchPosts(user.id)  // Wait for next promise
    console.log("Got posts:", posts)
    
    return posts
}

getUser()
```

**Compare to promises:**
```javascript
// With promises (harder to read):
function getUser() {
    console.log("Fetching user...")
    
    return fetchUser(1)
        .then(user => {
            console.log("Got user:", user)
            return fetchPosts(user.id)
        })
        .then(posts => {
            console.log("Got posts:", posts)
            return posts
        })
}

// With async/await (much clearer!):
async function getUser() {
    console.log("Fetching user...")
    
    let user = await fetchUser(1)
    console.log("Got user:", user)
    
    let posts = await fetchPosts(user.id)
    console.log("Got posts:", posts)
    
    return posts
}
```

### Error Handling with Try-Catch

```javascript
async function getUser() {
    try {
        let user = await fetchUser(1)
        let posts = await fetchPosts(user.id)
        return posts
    } catch (error) {
        console.log("Error:", error)
        return null
    }
}
```

### Waiting for Multiple Promises

**Sequential (slow - waits for each):**
```javascript
async function getUsers() {
    let user1 = await fetchUser(1)  // Wait 1 second
    let user2 = await fetchUser(2)  // Wait 1 second
    let user3 = await fetchUser(3)  // Wait 1 second
    // Total: 3 seconds
    
    return [user1, user2, user3]
}
```

**Parallel (fast - all at once):**
```javascript
async function getUsers() {
    let promises = [
        fetchUser(1),
        fetchUser(2),
        fetchUser(3)
    ]
    
    let users = await Promise.all(promises)  // Wait 1 second total
    return users
}

// Or more concise:
async function getUsers() {
    return await Promise.all([
        fetchUser(1),
        fetchUser(2),
        fetchUser(3)
    ])
}
```

### Async/Await Rules

**1. await only works inside async functions:**
```javascript
// WRONG:
function getData() {
    let result = await fetchData()  // ERROR!
}

// RIGHT:
async function getData() {
    let result = await fetchData()  // Works!
}
```

**2. Top-level await (modern JavaScript):**
```javascript
// In modules, you can use await at top level:
let data = await fetchData()
console.log(data)
```

**3. Async functions always return promises:**
```javascript
async function getValue() {
    return 42
}

getValue().then(val => console.log(val))  // 42

// Even if you return a regular value,
// it's wrapped in a promise automatically
```

### Real-World Example

```javascript
async function displayUserPosts() {
    try {
        // Show loading
        showLoading(true)
        
        // Fetch data
        let user = await fetchUser(1)
        console.log("User:", user.name)
        
        let posts = await fetchPosts(user.id)
        console.log(`Found ${posts.length} posts`)
        
        // Display posts
        posts.forEach(post => displayPost(post))
        
    } catch (error) {
        console.error("Failed to load posts:", error)
        showError("Could not load posts")
    } finally {
        showLoading(false)
    }
}

displayUserPosts()
```

### Converting Callbacks to Async/Await

**Wrap callback-based code in promises:**
```javascript
// Old callback API:
function readFile(path, callback) {
    // Read file...
    callback(error, data)
}

// Convert to promise:
function readFilePromise(path) {
    return new Promise((resolve, reject) => {
        readFile(path, (error, data) => {
            if (error) reject(error)
            else resolve(data)
        })
    })
}

// Now use with async/await:
async function processFile() {
    try {
        let data = await readFilePromise("file.txt")
        console.log(data)
    } catch (error) {
        console.error("Error reading file:", error)
    }
}
```

---

# Part 9: The DOM (Browser JavaScript)

## 9.1 Window and Document Objects

**The concept:** When JavaScript runs in a browser, it gets two global objects: `window` and `document`.

### The Window Object

**The global object in browsers:**
```javascript
console.log(window)  // The entire browser window object

// All global variables are on window:
let myVar = "test"
console.log(window.myVar)  // "test"

// Global functions too:
function myFunc() {}
console.log(window.myFunc)  // [Function: myFunc]
```

**Window properties and methods:**
```javascript
window.innerWidth   // Browser viewport width
window.innerHeight  // Browser viewport height
window.location.href  // Current URL
window.alert("Hello!")  // Alert dialog
window.confirm("Are you sure?")  // Confirmation dialog
window.prompt("Enter name:")  // Input dialog
```

### The Document Object

**Represents the HTML page:**
```javascript
document.title  // Page title
document.URL    // Current URL
document.body   // <body> element
document.head   // <head> element

// You usually just write:
console.log(document.title)
// Instead of:
console.log(window.document.title)
```

---

## 9.2 Selecting Elements

**The concept:** Before you can manipulate HTML, you need to select elements.

### querySelector (Modern, Recommended)

**Select first matching element:**
```javascript
// By ID
let element = document.querySelector("#myId")

// By class
let element = document.querySelector(".myClass")

// By tag
let element = document.querySelector("div")

// Complex selectors
let element = document.querySelector("div.container > p.intro")
let element = document.querySelector("input[type='text']")
```

### querySelectorAll (Get Multiple Elements)

```javascript
let paragraphs = document.querySelectorAll("p")
// Returns NodeList (array-like)

// Loop through:
paragraphs.forEach(p => {
    console.log(p.textContent)
})

// Convert to array:
let pArray = Array.from(paragraphs)
```

### Old Methods (Still Work)

```javascript
// By ID
let element = document.getElementById("myId")

// By class (returns HTMLCollection)
let elements = document.getElementsByClassName("myClass")

// By tag
let elements = document.getElementsByTagName("p")
```

**querySelector vs getElement methods:**
- `querySelector`: More flexible (any CSS selector), returns first match
- `getElementById`: Faster for simple ID lookup
- **Recommendation**: Use `querySelector` for consistency

---

## 9.3 Manipulating Elements

### Changing Content

```javascript
let element = document.querySelector("#myDiv")

// Text only (safe, no HTML):
element.textContent = "New text content"

// HTML (careful - can be XSS risk):
element.innerHTML = "<strong>Bold text</strong>"

// For form inputs:
let input = document.querySelector("#name")
input.value = "Cedric"
```

### Changing Attributes

```javascript
let img = document.querySelector("img")

// Get attribute:
let src = img.getAttribute("src")

// Set attribute:
img.setAttribute("src", "new-image.jpg")
img.setAttribute("alt", "New description")

// Remove attribute:
img.removeAttribute("alt")

// Direct property access (common attributes):
img.src = "new-image.jpg"
img.alt = "New description"
```

### Changing Styles

```javascript
let element = document.querySelector("#myDiv")

// Inline styles (use camelCase):
element.style.color = "red"
element.style.backgroundColor = "yellow"
element.style.fontSize = "20px"
element.style.display = "none"  // Hide
element.style.display = "block"  // Show

// Multiple styles:
Object.assign(element.style, {
    color: "red",
    fontSize: "20px",
    padding: "10px"
})
```

### Working with Classes

```javascript
let element = document.querySelector("#myDiv")

// Add class:
element.classList.add("active")
element.classList.add("highlight", "important")  // Multiple

// Remove class:
element.classList.remove("active")

// Toggle class (add if not there, remove if there):
element.classList.toggle("hidden")

// Check if has class:
if (element.classList.contains("active")) {
    console.log("Element is active")
}

// Replace class:
element.classList.replace("old-class", "new-class")
```

---

## 9.4 Creating and Removing Elements

### Creating Elements

```javascript
// Create element:
let newDiv = document.createElement("div")
newDiv.textContent = "Hello, I'm new!"
newDiv.className = "my-class"
newDiv.id = "my-id"

// Create text node:
let text = document.createTextNode("Some text")

// Append to page:
document.body.appendChild(newDiv)

// Or append to specific element:
let container = document.querySelector("#container")
container.appendChild(newDiv)
```

**Building complex elements:**
```javascript
// Create a list item with content
let li = document.createElement("li")
li.className = "list-item"

let link = document.createElement("a")
link.href = "https://example.com"
link.textContent = "Click me"

li.appendChild(link)
document.querySelector("#myList").appendChild(li)
```

### Inserting Elements

```javascript
let parent = document.querySelector("#parent")
let newElement = document.createElement("div")

// Insert at end (same as appendChild):
parent.append(newElement)

// Insert at beginning:
parent.prepend(newElement)

// Insert before a specific element:
let reference = document.querySelector("#reference")
parent.insertBefore(newElement, reference)

// Modern: insert adjacent
element.insertAdjacentElement("beforebegin", newElement)  // Before element
element.insertAdjacentElement("afterbegin", newElement)   // First child
element.insertAdjacentElement("beforeend", newElement)    // Last child
element.insertAdjacentElement("afterend", newElement)     // After element
```

### Removing Elements

```javascript
// Remove element:
let element = document.querySelector("#myElement")
element.remove()

// Remove child:
let parent = document.querySelector("#parent")
let child = document.querySelector("#child")
parent.removeChild(child)

// Remove all children:
parent.innerHTML = ""  // Quick but crude
// Or:
while (parent.firstChild) {
    parent.removeChild(parent.firstChild)
}
```

### Cloning Elements

```javascript
let original = document.querySelector("#myDiv")

// Shallow clone (no children):
let clone = original.cloneNode()

// Deep clone (with children):
let deepClone = original.cloneNode(true)

document.body.appendChild(deepClone)
```

---

## 9.5 Event Listeners

**Already covered in original document, but here's a summary:**

```javascript
let button = document.querySelector("#myButton")

// Add event listener:
button.addEventListener("click", function() {
    console.log("Button clicked!")
})

// With arrow function:
button.addEventListener("click", () => {
    console.log("Clicked!")
})

// With event object:
button.addEventListener("click", (event) => {
    console.log("Target:", event.target)
    console.log("Mouse X:", event.clientX)
})

// Remove event listener (need reference to function):
function handleClick() {
    console.log("Clicked!")
}
button.addEventListener("click", handleClick)
button.removeEventListener("click", handleClick)
```

**Common events:**
- `click` - Mouse click
- `dblclick` - Double click
- `mouseover`, `mouseout` - Mouse hover
- `mouseenter`, `mouseleave` - Mouse enter/leave
- `keydown`, `keyup`, `keypress` - Keyboard
- `input` - Input value changes
- `change` - Input loses focus after change
- `submit` - Form submission
- `focus`, `blur` - Element focus
- `scroll` - Page scroll
- `load` - Page/image loaded

---

## 9.6 Forms and User Input

### Getting Input Values

```javascript
let input = document.querySelector("#name")
let value = input.value

// Different input types:
let text = document.querySelector("input[type='text']").value
let checkbox = document.querySelector("input[type='checkbox']").checked
let radio = document.querySelector("input[type='radio']:checked").value
let select = document.querySelector("select").value
```

### Form Submission

```javascript
let form = document.querySelector("#myForm")

form.addEventListener("submit", (event) => {
    event.preventDefault()  // Prevent page reload!
    
    // Get form data:
    let name = document.querySelector("#name").value
    let email = document.querySelector("#email").value
    
    // Validate:
    if (name === "" || email === "") {
        alert("Please fill all fields")
        return
    }
    
    // Process data:
    console.log("Form data:", { name, email })
    
    // Could send to server here
})
```

### Form Validation

```javascript
function validateEmail(email) {
    let re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return re.test(email)
}

form.addEventListener("submit", (event) => {
    event.preventDefault()
    
    let email = document.querySelector("#email").value
    
    if (!validateEmail(email)) {
        alert("Invalid email")
        return
    }
    
    // Submit form
})
```

### FormData API

```javascript
let form = document.querySelector("#myForm")

form.addEventListener("submit", (event) => {
    event.preventDefault()
    
    // Automatically collect all form fields:
    let formData = new FormData(form)
    
    // Access values:
    let name = formData.get("name")
    let email = formData.get("email")
    
    // Convert to object:
    let data = Object.fromEntries(formData)
    console.log(data)
    // { name: "Cedric", email: "c@example.com" }
})
```

---

# Part 10: Browser APIs & Storage

## 10.1 localStorage and sessionStorage

**The concept:** Store data in the browser that persists between page reloads.

### localStorage vs sessionStorage

**localStorage:**
- Persists forever (until manually cleared)
- Shared across all tabs/windows for same domain

**sessionStorage:**
- Persists only for the current tab/window
- Cleared when tab is closed

### Using localStorage

```javascript
// Store data (only strings!):
localStorage.setItem("name", "Cedric")
localStorage.setItem("age", "25")

// Get data:
let name = localStorage.getItem("name")  // "Cedric"

// Remove data:
localStorage.removeItem("name")

// Clear all:
localStorage.clear()

// Check if key exists:
if (localStorage.getItem("name") !== null) {
    console.log("Name exists")
}
```

### Storing Objects (Use JSON)

```javascript
// Store object:
let user = { name: "Cedric", age: 25, city: "Manila" }
localStorage.setItem("user", JSON.stringify(user))

// Get object:
let storedUser = JSON.parse(localStorage.getItem("user"))
console.log(storedUser.name)  // "Cedric"
```

### Practical Example - Save User Preferences

```javascript
// Save preferences:
function savePreferences() {
    let preferences = {
        theme: "dark",
        fontSize: "large",
        notifications: true
    }
    localStorage.setItem("preferences", JSON.stringify(preferences))
}

// Load preferences:
function loadPreferences() {
    let stored = localStorage.getItem("preferences")
    
    if (stored) {
        let preferences = JSON.parse(stored)
        applyTheme(preferences.theme)
        setFontSize(preferences.fontSize)
        // ...
    } else {
        // Use defaults
        setDefaults()
    }
}

// Load on page load:
window.addEventListener("load", loadPreferences)
```

### Storage Limits

- Usually 5-10 MB per domain
- Exceeding limit throws error

```javascript
try {
    localStorage.setItem("key", largeData)
} catch (error) {
    console.log("Storage full!", error)
}
```

---

## 10.2 Fetch API

**The concept:** Fetch data from servers (make HTTP requests).

### Basic GET Request

```javascript
fetch("https://api.example.com/users")
    .then(response => response.json())  // Parse JSON
    .then(data => {
        console.log(data)
    })
    .catch(error => {
        console.error("Error:", error)
    })
```

### With Async/Await (Better!)

```javascript
async function getUsers() {
    try {
        let response = await fetch("https://api.example.com/users")
        
        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`)
        }
        
        let data = await response.json()
        console.log(data)
        return data
        
    } catch (error) {
        console.error("Error fetching users:", error)
    }
}

getUsers()
```

### POST Request (Sending Data)

```javascript
async function createUser(userData) {
    try {
        let response = await fetch("https://api.example.com/users", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify(userData)
        })
        
        let data = await response.json()
        return data
        
    } catch (error) {
        console.error("Error:", error)
    }
}

// Use it:
createUser({ name: "Cedric", email: "c@example.com" })
```

### Other HTTP Methods

```javascript
// PUT (update):
fetch(url, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(updatedData)
})

// DELETE:
fetch(url, {
    method: "DELETE"
})

// PATCH (partial update):
fetch(url, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(partialUpdate)
})
```

### Handling Different Response Types

```javascript
// JSON:
let data = await response.json()

// Text:
let text = await response.text()

// Blob (for images, files):
let blob = await response.blob()
let url = URL.createObjectURL(blob)
img.src = url
```

### Real-World Example

```javascript
async function displayUsers() {
    try {
        // Show loading
        showLoading(true)
        
        // Fetch data
        let response = await fetch("https://jsonplaceholder.typicode.com/users")
        
        if (!response.ok) {
            throw new Error("Failed to fetch")
        }
        
        let users = await response.json()
        
        // Display users
        let userList = document.querySelector("#userList")
        userList.innerHTML = ""
        
        users.forEach(user => {
            let li = document.createElement("li")
            li.textContent = `${user.name} (${user.email})`
            userList.appendChild(li)
        })
        
    } catch (error) {
        console.error("Error:", error)
        showError("Failed to load users")
    } finally {
        showLoading(false)
    }
}
```

---

# Part 11: Modules

## 11.1 What Are Modules?

**The concept:** Modules let you split code into separate files and import/export between them.

**Why modules:**
- Organize code into logical pieces
- Reuse code across projects
- Avoid global namespace pollution
- Better maintenance

### ES6 Modules (Modern)

**Exporting:**
```javascript
// math.js

// Named exports:
export function add(a, b) {
    return a + b
}

export function multiply(a, b) {
    return a * b
}

export const PI = 3.14159

// Or export all at once:
function add(a, b) {
    return a + b
}

function multiply(a, b) {
    return a * b
}

const PI = 3.14159

export { add, multiply, PI }
```

**Importing:**
```javascript
// main.js

// Import specific functions:
import { add, multiply, PI } from "./math.js"

console.log(add(5, 3))      // 8
console.log(multiply(5, 3))  // 15
console.log(PI)             // 3.14159

// Import with different names:
import { add as sum } from "./math.js"
console.log(sum(5, 3))  // 8

// Import everything:
import * as math from "./math.js"
console.log(math.add(5, 3))
console.log(math.PI)
```

### Default Exports

**Only one default export per module:**
```javascript
// user.js

export default class User {
    constructor(name) {
        this.name = name
    }
    
    greet() {
        return `Hello, ${this.name}`
    }
}

// Or:
class User {
    // ...
}

export default User
```

**Importing default:**
```javascript
// main.js

import User from "./user.js"  // No braces!

let user = new User("Cedric")
console.log(user.greet())
```

### Mixing Default and Named Exports

```javascript
// utils.js

export default function main() {
    console.log("Main function")
}

export function helper1() {}
export function helper2() {}
```

```javascript
// Import:
import main, { helper1, helper2 } from "./utils.js"
```

### Using Modules in Browser

```html
<script type="module" src="main.js"></script>

<!-- Or inline: -->
<script type="module">
    import { add } from "./math.js"
    console.log(add(5, 3))
</script>
```

**Important notes:**
- Must use `type="module"`
- Must serve from a server (not file://)
- Modules are deferred by default
- Strict mode automatically enabled

---

# Part 12: Debugging

**(This section was covered extensively in the original document, here's a quick summary)**

## 12.1 Common Errors

- **Syntax Errors**: Typos, missing brackets
- **Runtime Errors**: Accessing undefined properties, type errors
- **Logical Errors**: Code runs but produces wrong results

## 12.2 Console Methods

```javascript
console.log("Normal message")
console.error("Error message")
console.warn("Warning message")
console.table([{name: "Cedric", age: 25}])
console.group("Group")
console.log("Inside group")
console.groupEnd()
console.assert(5 > 10, "5 is not greater than 10")
```

## 12.3 Breakpoints

- Click line number in DevTools Sources tab
- Or add `debugger` statement in code
- Step through code line by line
- Inspect variables at each step

## 12.4 Best Debugging Practices

1. Read error messages carefully
2. Use console.log strategically
3. Use breakpoints for complex issues
4. Test small pieces of code in isolation
5. Check assumptions with console.assert

---

# Part 13: Best Practices & Wrap-Up

## 13.1 JavaScript Best Practices

### 1. Use Strict Mode

```javascript
"use strict"

// Catches common mistakes:
x = 10  // Error: x is not defined (without strict mode, creates global)
```

### 2. Const by Default, Let When Needed

```javascript
// Good:
const maxUsers = 100
let currentCount = 0

// Avoid:
var anything  // Don't use var
```

### 3. Meaningful Variable Names

```javascript
// Bad:
let x = 5
let dt = new Date()

// Good:
let userAge = 5
let currentDate = new Date()
```

### 4. Functions Should Do One Thing

```javascript
// Bad - does too much:
function processUser(user) {
    validateUser(user)
    saveUser(user)
    sendEmail(user)
    logActivity(user)
}

// Good - separate concerns:
function processUser(user) {
    if (!isValid(user)) return false
    save(user)
    notify(user)
    return true
}
```

### 5. Avoid Global Variables

```javascript
// Bad:
let data = []
let currentUser = null

// Good - encapsulate:
const app = {
    data: [],
    currentUser: null,
    methods: {
        // ...
    }
}
```

### 6. Handle Errors

```javascript
// Bad:
let data = JSON.parse(userInput)

// Good:
try {
    let data = JSON.parse(userInput)
} catch (error) {
    console.error("Invalid JSON:", error)
    data = null
}
```

### 7. Use === Instead of ==

```javascript
// Avoid:
if (x == "5") {}

// Use:
if (x === 5) {}
```

### 8. Comment Why, Not What

```javascript
// Bad:
// Increment i
i++

// Good:
// Skip every other row for performance
i += 2
```

---

## 13.2 Common Pitfalls to Avoid

### 1. Modifying Arrays While Looping

```javascript
// Bad:
for (let i = 0; i < arr.length; i++) {
    arr.splice(i, 1)  // Array length changes!
}

// Good:
for (let i = arr.length - 1; i >= 0; i--) {
    arr.splice(i, 1)
}
// Or use filter:
arr = arr.filter(item => condition)
```

### 2. Forgetting to Return

```javascript
// Bad:
function add(a, b) {
    a + b  // Doesn't return!
}

// Good:
function add(a, b) {
    return a + b
}
```

### 3. Not Handling Async Errors

```javascript
// Bad:
async function getData() {
    let data = await fetch(url)
    // What if this fails?
}

// Good:
async function getData() {
    try {
        let data = await fetch(url)
        return data
    } catch (error) {
        console.error("Failed:", error)
        return null
    }
}
```

### 4. Reference vs Value Confusion

```javascript
// Arrays and objects are references:
let arr1 = [1, 2, 3]
let arr2 = arr1  // Reference, not copy!
arr2.push(4)
console.log(arr1)  // [1, 2, 3, 4] - arr1 changed too!

// Copy properly:
let arr3 = [...arr1]  // or arr1.slice()
```

---

## 13.3 Learning Path Forward

**You've learned the fundamentals! What's next?**

### Immediate Next Steps:
1. **Build projects** - Todo list, calculator, weather app
2. **Practice daily** - Coding challenges (LeetCode, Codewars)
3. **Read other people's code** - Learn from GitHub projects

### Intermediate Topics:
- **Frontend Framework** - React, Vue, or Angular
- **Node.js** - Backend JavaScript
- **TypeScript** - JavaScript with types
- **Build Tools** - Webpack, Vite
- **Testing** - Jest, Testing Library

### Advanced Topics:
- **Design Patterns** - Module, Observer, Factory, etc.
- **Performance Optimization**
- **Security** - XSS, CSRF, authentication
- **Advanced Async** - Streams, Workers
- **Algorithms & Data Structures**

---

## 13.4 Key Takeaways

**Core Concepts:**
1. **Variables** - const by default, let when changing
2. **Functions** - Reusable code blocks, arrow functions
3. **Scope** - Where variables are accessible
4. **Arrays & Objects** - Data structures
5. **Control Flow** - if/else, loops, switch
6. **Async** - Event loop, promises, async/await
7. **DOM** - Manipulating web pages
8. **Error Handling** - try/catch, validation

**Remember:**
- Practice consistently
- Break problems into small pieces
- Read error messages carefully
- Don't memorize - understand concepts
- Build real projects
- Ask questions and seek help
- Keep learning!

---

## 13.5 Quick Reference

### Common Patterns

```javascript
// Loop through array:
arr.forEach(item => console.log(item))

// Transform array:
let doubled = arr.map(x => x * 2)

// Filter array:
let evens = arr.filter(x => x % 2 === 0)

// Sum array:
let sum = arr.reduce((total, x) => total + x, 0)

// Async request:
async function getData() {
    try {
        let response = await fetch(url)
        let data = await response.json()
        return data
    } catch (error) {
        console.error(error)
    }
}

// Event listener:
element.addEventListener('click', () => {
    console.log('Clicked!')
})

// Create element:
let div = document.createElement('div')
div.textContent = 'Hello'
document.body.appendChild(div)

// LocalStorage:
localStorage.setItem('key', JSON.stringify(obj))
let obj = JSON.parse(localStorage.getItem('key'))
```

---

# Congratulations! 🎉

You've completed this comprehensive JavaScript guide! You now have a solid foundation in:

- ✅ Core language features
- ✅ Functions and scope
- ✅ Arrays, objects, and data manipulation
- ✅ Control flow and loops
- ✅ Asynchronous programming
- ✅ Event loop (critical!)
- ✅ DOM manipulation
- ✅ Modern JavaScript features
- ✅ Best practices

**Remember the Feynman way:**
> "If you can't explain it simply, you don't understand it well enough."

Keep practicing, keep building, and keep explaining concepts in your own words. You've got this!

---

**Final Note:** This guide covers fundamentals thoroughly. For reference on advanced topics or specific frameworks, consult official documentation. Happy coding! 🚀