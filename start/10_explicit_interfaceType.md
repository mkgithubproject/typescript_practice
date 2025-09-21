Got it! Let’s dive into **TypeScript explicit types vs type inference** in a clear and structured way. This is a core concept that helps you write safe, maintainable TypeScript code.

---

## 1. **Explicit Types**

**Definition:** Explicit types are when you **manually declare the type** of a variable, function parameter, or return value.

**Syntax:**

```ts
let name: string = "Mohit";       // explicit type string
let age: number = 25;             // explicit type number
let isActive: boolean = true;     // explicit type boolean

function add(a: number, b: number): number {
  return a + b;
}
```

**Key Points:**

* Helps **catch errors early**.
* Improves **readability** for other developers.
* Useful when the type cannot be inferred clearly or could be ambiguous.

---

## 2. **Type Inference**

**Definition:** TypeScript can **automatically infer the type** of a variable based on its initial value.

**Example:**

```ts
let username = "Mohit";   // inferred as string
let count = 10;           // inferred as number
let isAdmin = false;      // inferred as boolean

function multiply(a: number, b: number) {
  return a * b;           // return type inferred as number
}
```

**Key Points:**

* You **don’t have to explicitly type everything**.
* TypeScript infers the type from the **assigned value**.
* Makes code **less verbose** but still type-safe.

---

## 3. **Combining Explicit Types and Inference**

You can use **explicit types** when you want more clarity or flexibility, and rely on **inference** when it’s obvious:

```ts
let score: number;  // explicit type, initially undefined
score = 100;

let city = "Delhi"; // inferred type string
```

**When to prefer explicit types:**

1. Variables initialized with `null` or `undefined`.
2. Function return types that are complex or not obvious.
3. For public APIs or libraries to improve clarity.

**Example with `null`:**

```ts
let data: string | null = null;  // explicit type needed
data = "Loaded";
```

---

## 4. **Inference Rules**

1. **Based on initialization value**:

   ```ts
   let x = 10;  // number
   let y = "hello"; // string
   ```
2. **Contextual typing** (like function parameters):

   ```ts
   window.addEventListener("click", (event) => {
     console.log(event.button); // event is inferred as MouseEvent
   });
   ```
3. **Best common type for arrays**:

   ```ts
   let arr = [1, 2, 3];  // number[]
   let mixed = [1, "two", 3]; // (string | number)[]
   ```

---

✅ **Summary Table**

| Feature              | Example                                                        | Type           |                |
| -------------------- | -------------------------------------------------------------- | -------------- | -------------- |
| Explicit Type        | `let age: number = 30;`                                        | number         |                |
| Type Inference       | `let name = "Mohit";`                                          | string         |                |
| When to Use Explicit | \`let data: string                                             | null = null;\` | string \| null |
| Function Return      | `function add(a: number, b: number): number { return a + b; }` | number         |                |

---

If you want, I can also make a **small diagram comparing Explicit Types vs Type Inference** which makes it super easy to remember.

Do you want me to make that diagram?
