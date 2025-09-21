Perfect! Let’s go through **“3. Variables and Functions”** exactly according to your roadmap steps: **typed variables, function parameter & return types, optional/default parameters, and rest parameters**. I’ll explain each with examples.

---

# **3️⃣ Variables and Functions in TypeScript (Roadmap Version)**

---

## **A. Typed Variables**

TypeScript allows you to **assign types to variables** to ensure type safety.

```ts
// Number type
let age: number = 25;

// String type
let name: string = "Mohit";

// Boolean type
let isLoggedIn: boolean = true;

// Array type
let numbers: number[] = [1, 2, 3];
let fruits: string[] = ["apple", "banana"];

// Union type (multiple possible types)
let id: string | number;
id = "abc123";
id = 101;

// Type inference (TS can detect type automatically)
let score = 99; // inferred as number
// score = "high"; // ❌ Error
```

✅ Key points:

* TS ensures variables only hold values of the declared type.
* Use **union types** when multiple types are possible.
* Let TS infer types where obvious.

---

## **B. Function Parameter & Return Types**

TypeScript allows you to **type both function parameters and the return value**.

```ts
// Function with typed parameters and return
function add(a: number, b: number): number {
  return a + b;
}

let sum = add(5, 10); // 15
// add("5", 10); // ❌ Error
```

* Parameters `a` and `b` must be numbers.
* Function **must return a number**.

---

## **C. Optional and Default Parameters**

### **1. Optional Parameters**

Use `?` for parameters that **may not be provided**.

```ts
function greet(name: string, title?: string): string {
  return title ? `Hello ${title} ${name}` : `Hello ${name}`;
}

console.log(greet("Mohit"));       // Hello Mohit
console.log(greet("Mohit", "Mr.")); // Hello Mr. Mohit
```

### **2. Default Parameters**

Provide **default values** if the argument is not passed.

```ts
function multiply(a: number, b: number = 2): number {
  return a * b;
}

console.log(multiply(5));    // 10 (uses default b = 2)
console.log(multiply(5, 3)); // 15
```

✅ Key points:

* Optional parameters **cannot appear before required ones**.
* Default parameters **override undefined arguments**.

---

## **D. Rest Parameters**

* Accept a **variable number of arguments** as an array.

```ts
function sumAll(...numbers: number[]): number {
  return numbers.reduce((acc, curr) => acc + curr, 0);
}

console.log(sumAll(1, 2, 3, 4)); // 10
console.log(sumAll(5, 10));      // 15
```

* Use `...` syntax to gather remaining arguments.
* The type `number[]` ensures all arguments are numbers.

---

✅ **Summary Table**

| Feature                     | Example                                      | Notes                        |
| --------------------------- | -------------------------------------------- | ---------------------------- |
| Typed Variable              | `let age: number = 25;`                      | TS ensures type safety       |
| Function Parameter & Return | `function add(a: number, b: number): number` | Must match declared types    |
| Optional Parameter          | `title?: string`                             | May be omitted               |
| Default Parameter           | `b: number = 2`                              | Uses default if not provided |
| Rest Parameter              | `...numbers: number[]`                       | Accepts variable arguments   |

---


