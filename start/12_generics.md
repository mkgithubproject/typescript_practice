Perfect! Following your roadmap after **“Variables and Functions”** and **“Interfaces & Type Aliases”**, the next topic is:

# **5️⃣ Generics in TypeScript**

Generics allow you to **create reusable components, functions, or classes** that work with **any type**, while still keeping **type safety**.

---

## **A. Generic Functions**

```ts
function identity<T>(value: T): T {
  return value;
}

let num = identity<number>(42);        // T = number
let str = identity<string>("Hello");   // T = string
```

* `<T>` is a **placeholder type**.
* TS infers type if not explicitly passed:

```ts
let inferred = identity("TypeScript"); // T inferred as string
```

---

## **B. Generic Arrays**

```ts
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

console.log(getFirst([1, 2, 3]));       // 1
console.log(getFirst(["a", "b", "c"])); // "a"
```

---

## **C. Generic Interfaces**

```ts
interface ApiResponse<T> {
  status: string;
  data: T;
}

const userResponse: ApiResponse<User> = {
  status: "success",
  data: { id: 1, name: "Mohit" },
};
```

---

## **D. Generic Type Aliases**

```ts
type Pair<T, U> = [T, U];

let userPair: Pair<string, number> = ["Mohit", 101];
```

---

## **E. Generic Classes**

```ts
class Box<T> {
  private content: T;
  constructor(value: T) {
    this.content = value;
  }
  getValue(): T {
    return this.content;
  }
}

const numberBox = new Box<number>(123);
console.log(numberBox.getValue()); // 123
```

---

## **F. Constraints in Generics**

* Limit types that a generic can accept using `extends`:

```ts
function printLength<T extends { length: number }>(value: T): number {
  return value.length;
}

console.log(printLength("Hello")); // 5
console.log(printLength([1,2,3])); // 3
// console.log(printLength(42)); // ❌ Error (number has no length)
```

---

### ✅ **Summary of Generics**

| Feature             | Example                                 | Notes                       |
| ------------------- | --------------------------------------- | --------------------------- |
| Generic Function    | `function identity<T>(value: T): T {}`  | Reusable for any type       |
| Generic Array       | `function getFirst<T>(arr: T[]): T {}`  | Works with any array type   |
| Generic Interface   | `interface ApiResponse<T> { data: T; }` | Type-safe API responses     |
| Generic Type Alias  | `type Pair<T, U> = [T, U];`             | Multiple generic types      |
| Generic Class       | `class Box<T> { ... }`                  | Encapsulates type-safe data |
| Generic Constraints | `<T extends { length: number }>`        | Limit acceptable types      |

---

If you want, I can **next cover “Advanced Types”** (Union, Intersection, Conditional, Mapped, Utility types), which comes **after Generics** in a TypeScript roadmap.

Do you want me to continue with **Advanced Types** next?
