# Day 1 — Variables, Primitives, and Type Coercion

---
By the end of this lesson, a student should be able to:

- Explain the difference between var, let, and const, and choose the correct one for any situation — defaulting to const, using let when reassignment is needed, and recognizing var as legacy code to avoid.
- Distinguish between a binding being immutable (const) and a value being immutable — understanding why `const user = {}` still allows `user.name = "Alice"`.
- Name all seven JavaScript primitives (number, string, boolean, undefined, null, bigint, symbol) and recognize what each represents.
- Use `typeof` correctly — including knowing its one famous lie: typeof null `===` "object" — and know to use `===` null for explicit null checks.
- Predict the result of implicit type coercion expressions, particularly around the + operator's dual role as addition and string concatenation.
- Apply explicit coercion using `Number()`, `String()`, and `Boolean()` and predict their output for edge-case inputs like null, undefined, `""`, and `"0"`.
- Recall the six falsy values and correctly identify surprising truthy values like `"0"`, `[]`, and `{}`.
- Choose `===` over `==` by default, and explain why — understanding that `==` introduces silent type coercion that makes code harder to reason about.

---

Let's build the mental model first, then you'll test it live, then you'll do exercises.

---

## The Three Keywords: `var`, `let`, `const`

JavaScript has three ways to declare a variable, and one of them (`var`) should be considered a historical artifact you'll read in old code but never write yourself.

- `let` declares a variable that can be reassigned.
- `const` declares a binding that cannot be reassigned — it's your default choice for everything unless you know upfront you need to reassign.

The key insight is that `const` doesn't make a value immutable, it makes the **binding** immutable. An object declared with `const` can still have its properties changed.

```js
let score = 0;
score = 10;        // fine

const name = "Alice";
name = "Bob";      // TypeError: Assignment to constant variable

const user = { age: 25 };
user.age = 26;     // fine — the binding didn't change, the object did
```

`var` is function-scoped and hoisted, which produces bugs that are genuinely hard to reason about. You'll see it in legacy code — recognize it, don't write it.

---

## The Seven Primitives

JavaScript has exactly seven primitive types. Everything else is an object.

```js
let a = 42;           // number  (integers and floats share one type)
let b = "hello";      // string
let c = true;         // boolean
let d = undefined;    // undefined — variable declared, no value assigned
let e = null;         // null — intentional absence of value
let f = 42n;          // bigint — for integers beyond Number.MAX_SAFE_INTEGER
let g = Symbol("id"); // symbol — guaranteed unique value
```

`typeof` is your inspector:

```js
typeof 42         // "number"
typeof "hello"    // "string"
typeof true       // "boolean"
typeof undefined  // "undefined"
typeof null       // "object"  ← famous bug, frozen in the spec forever
typeof 42n        // "bigint"
typeof Symbol()   // "symbol"
```

> **Note:** `typeof null === "object"` is a 30-year-old bug that can never be fixed without breaking the web. Check for null explicitly: `value === null`.

---

## Type Coercion — Where JavaScript Earns Its Reputation

JavaScript will silently convert types when operators are applied to mismatched types. There are two kinds:

### Implicit Coercion — JavaScript decides for you, often surprisingly:

```js
"5" + 3       // "53"  — + sees a string and concatenates
"5" - 3       // 2     — - has no string meaning, so converts to number
"5" * "3"     // 15    — both coerced to numbers
true + 1      // 2     — true coerces to 1
false + 1     // 1     — false coerces to 0
null + 1      // 1     — null coerces to 0
undefined + 1 // NaN   — undefined coerces to NaN
[] + []       // ""    — both become empty strings
[] + {}       // "[object Object]"
{} + []       // 0     — parsed as block + unary +
```

### Explicit Coercion — you decide, and the code is honest about it:

```js
Number("42")      // 42
Number("")        // 0
Number("abc")     // NaN
Number(true)      // 1
Number(null)      // 0
Number(undefined) // NaN

String(42)        // "42"
String(null)      // "null"
String(undefined) // "undefined"

Boolean(0)        // false
Boolean("")       // false
Boolean(null)     // false
Boolean(undefined)// false
Boolean(NaN)      // false
Boolean("0")      // true  ← non-empty string is truthy, even "false"
Boolean([])       // true  ← empty array is truthy
```

### The Six Falsy Values

```
false, 0, "", null, undefined, NaN
```

Everything else is truthy.

---

## `==` vs `===`

- `==` performs **type coercion** before comparing.
- `===` compares **type and value** with no conversion.

**Always use `===`** unless you have a specific reason not to.

```js
0 == false         // true  — coercion
0 === false        // false — different types
"" == false        // true  — coercion
null == undefined  // true  — the one useful == case
null === undefined // false
```

---

## Key Takeaway

The reason `const` is the right default isn't dogma — it's that **mutation is one of the biggest sources of bugs in large codebases**. If a variable can't be reassigned, an entire class of errors becomes impossible. You'll feel this pay off progressively as your programs grow.

---

