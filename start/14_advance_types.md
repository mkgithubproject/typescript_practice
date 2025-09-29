Perfect 👍 Let’s go through **advanced TypeScript types** step by step with **simple explanations and examples** so you can get them quickly.

---

# 1. **Generics**

> Generics let you write reusable code that works with different types.

```ts
function identity<T>(value: T): T {
  return value;
}

// Usage
let num = identity<number>(42);   // num: number
let str = identity<string>("Hi"); // str: string
```

👉 Here, `T` is a placeholder for a type.
Instead of writing separate functions for `number`, `string`, etc., we use one generic.

---

# 2. **Conditional Types**

> A type that depends on a condition (`extends ? :`).

```ts
type IsString<T> = T extends string ? "Yes" : "No";

type A = IsString<string>; // "Yes"
type B = IsString<number>; // "No"
```

👉 If the type matches, we return one type, else another.

---

# 3. **Mapped Types**

> Create new types by looping (`map`) over keys of another type.

```ts
type User = {
  name: string;
  age: number;
};

// Make all properties optional
type OptionalUser = {
  [K in keyof User]?: User[K];
};

// Same as { name?: string; age?: number }
```

👉 It’s like running a `for...in` over the keys of a type.

---

# 4. **Utility Types**

> Pre-built helpers in TypeScript.

### ✅ `Partial`

Makes all properties optional.

```ts
type User = { name: string; age: number; };
type PartialUser = Partial<User>;
// { name?: string; age?: number }
```

### ✅ `Pick`

Choose only some properties.

```ts
type UserName = Pick<User, "name">;
// { name: string }
```

### ✅ `Omit`

Remove some properties.

```ts
type UserWithoutAge = Omit<User, "age">;
// { name: string }
```

### ✅ `Record`

Create an object type with specific keys and values.

```ts
type Roles = Record<"admin" | "user", boolean>;
// { admin: boolean; user: boolean }
```

---

# 5. **`keyof` Operator**

> Gets all keys of a type.

```ts
type User = { name: string; age: number; };

type Keys = keyof User;  
// "name" | "age"
```

👉 Useful for type-safe property access.

---

# 6. **Type Inference**

> TypeScript often figures out the type automatically.

```ts
let x = 10;    // inferred as number
let y = "Hi";  // inferred as string

function add(a: number, b: number) {
  return a + b;  // return type inferred as number
}
```

👉 You don’t always need to manually specify types.

---

✅ **Summary in one line each:**

* **Generics** → Reusable with any type.
* **Conditional types** → Type depends on condition.
* **Mapped types** → Loop over keys to build new types.
* **Utility types** → Predefined helpers (`Partial`, `Pick`, etc.).
* **`keyof`** → Get keys of a type.
* **Inference** → TS guesses the type automatically.

---

Good question 👍 Let’s slow it down.

---

We have this type:

```ts
type User = { 
  name: string; 
  age: number; 
};
```

The **`keyof` operator** means:
👉 “Give me a union of all property names (keys) of the type.”

So:

```ts
type Keys = keyof User;
```

becomes:

```ts
type Keys = "name" | "age";
```

---

### Think of it like this:

* `User` has two properties → `name`, `age`.
* `keyof User` = “all possible keys of `User`”.
* So `Keys` is `"name" | "age"`.

---

### Example usage:

```ts
function getValue(obj: User, key: keyof User) {
  return obj[key];
}

const user: User = { name: "Alice", age: 25 };

let v1 = getValue(user, "name"); // OK → "Alice"
let v2 = getValue(user, "age");  // OK → 25
let v3 = getValue(user, "email"); // ❌ Error → "email" is not a key in User
```

👉 Because of `keyof User`, TypeScript only lets you pass `"name"` or `"age"` as the key.
It prevents mistakes like `"email"`.

---

Ah I see — you’re asking:

👉 *Why does `keyof User` give `"name" | "age"` (a **union** of keys), and not something else?*
👉 And what is this "union without key"?

Let’s break it down 👇

---

### 1. What is a **union type** in TypeScript?

A **union** means "this OR that".

```ts
type Fruit = "apple" | "banana" | "mango";

let f: Fruit;
f = "apple";  // ✅ allowed
f = "banana"; // ✅ allowed
f = "orange"; // ❌ not allowed
```

So `"apple" | "banana" | "mango"` is a **union of string literal types**.

---

### 2. How does `keyof` work?

When you write:

```ts
type User = { name: string; age: number };
type Keys = keyof User;
```

TypeScript looks at the **keys** of `User` → `"name"` and `"age"`.

So it makes a **union**:

```ts
type Keys = "name" | "age";
```

That’s why it’s a **union of property names**, not an object, not an array.

---

### 3. Example usage

```ts
function printKey(key: keyof User) {
  console.log("The key is:", key);
}

printKey("name"); // ✅ works
printKey("age");  // ✅ works
printKey("email"); // ❌ Error (not part of union)
```

👉 Because `keyof User` is `"name" | "age"`, only those are allowed.

---

✅ **Simple way to think about it:**

* `keyof` takes an object type.
* Returns a **union of all its keys**.
* You can use it to ensure you only work with valid keys.

---

