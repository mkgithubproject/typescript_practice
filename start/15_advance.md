Perfect 👌 you’re now entering **advanced TypeScript** territory. Let’s cover each of these step by step with **simple explanations + examples**.

---

# 1. **Declaration files (.d.ts)**

👉 These files contain **only type information**, not actual code.
Useful when:

* Writing **type definitions for JS libraries**.
* Adding custom types (like `global` variables).

Example: `math.d.ts`

```ts
// Declare a function that exists in some JS library
declare function add(a: number, b: number): number;
```

Now in your code:

```ts
let result = add(2, 3); // ✅ TS knows it's number
```

👉 `.d.ts` files tell TypeScript **what types exist**, even if the real code is JS.

---

# 2. **Working with 3rd Party JS libraries (@types/...)**

👉 Many JS libraries don’t ship with TS types.
You install them separately:

```bash
npm install lodash
npm install -D @types/lodash
```

Now you get autocomplete + type safety:

```ts
import _ from "lodash";

const arr = [1, 2, 3];
const doubled = _.map(arr, x => x * 2);
// doubled: number[]
```

👉 `@types/...` packages are just `.d.ts` files.

---

# 3. **Namespaces vs Modules**

* **Namespaces** → Old, internal TS way (all in one file/app).
* **Modules** → Modern, ES standard (with `import/export`).

### Namespace (❌ outdated):

```ts
namespace Shapes {
  export interface Circle { radius: number; }
}

let c: Shapes.Circle = { radius: 10 };
```

### Module (✅ modern):

```ts
// circle.ts
export interface Circle { radius: number; }

// main.ts
import { Circle } from "./circle";
const c: Circle = { radius: 10 };
```

👉 Always prefer **modules** in Node.js/modern apps.

---

# 4. **Advanced Generics Patterns**

Generics can be very powerful when combined with constraints.

### Constraining with `extends`

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Alice", age: 30 };

let name = getProperty(user, "name"); // string
let age = getProperty(user, "age");   // number
```

👉 `K extends keyof T` ensures only valid keys can be used.

---

# 5. **Utility Types (In-depth)**

✅ Already saw `Partial`, `Pick`, `Omit`, `Record`. Let’s add more:

### `Required`

Make all properties required.

```ts
type User = { name?: string; age?: number };
type StrictUser = Required<User>; 
// { name: string; age: number }
```

### `Readonly`

Make all props immutable.

```ts
type User = { name: string; age: number };
type ReadonlyUser = Readonly<User>;
// { readonly name: string; readonly age: number }
```

### `Exclude`

Remove certain types from a union.

```ts
type Fruit = "apple" | "banana" | "mango";
type NoBanana = Exclude<Fruit, "banana">;
// "apple" | "mango"
```

### `Extract`

Keep only certain types from a union.

```ts
type Letters = "a" | "b" | "c";
type OnlyA = Extract<Letters, "a">;
// "a"
```

### `NonNullable`

Remove `null` and `undefined`.

```ts
type T = string | null | undefined;
type Clean = NonNullable<T>;
// string
```

---

# 6. **Conditional & Mapped Types**

### Conditional

```ts
type IsArray<T> = T extends any[] ? "Yes" : "No";

type A = IsArray<string[]>; // "Yes"
type B = IsArray<number>;   // "No"
```

### Mapped

Loop over keys of a type:

```ts
type User = { name: string; age: number };

type Optional<T> = {
  [K in keyof T]?: T[K];
};

type OptionalUser = Optional<User>;
// { name?: string; age?: number }
```

👉 Combine conditional + mapped:

```ts
type Nullable<T> = {
  [K in keyof T]: T[K] | null;
};

type User = { name: string; age: number };
type NullableUser = Nullable<User>;
// { name: string | null; age: number | null }
```

---

✅ **Summary**

* **`.d.ts`** → declare types for JS code.
* **`@types/...`** → type packages for JS libs.
* **Namespaces vs Modules** → use modules (`import/export`).
* **Advanced Generics** → constraints like `<T, K extends keyof T>`.
* **Utility types in depth** → Required, Readonly, Exclude, Extract, NonNullable, etc.
* **Conditional & Mapped types** → super flexible type transformations.

---

