Got it 👍 Let’s go step by step — from **basic decorators in TypeScript** to **advanced real-world usage**.

---

# 🚀 Decorators in TypeScript (Basic → Advanced)

Decorators in TypeScript are **special functions** that can be attached to classes, methods, properties, or parameters to **add extra behavior** at runtime.
They’re heavily used in frameworks like **NestJS** and **Angular**.

To enable decorators, you must add this in **`tsconfig.json`**:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

---

## 1. **Basic Class Decorator**

A **class decorator** gets the constructor of the class.

```ts
function Logger(constructor: Function) {
  console.log("Class created:", constructor.name);
}

@Logger
class User {
  constructor(public name: string) {}
}
```

📌 When `User` is defined, the decorator runs and logs the class name.

---

## 2. **Decorator Factory (Configurable Decorator)**

You can make decorators **accept arguments**.

```ts
function Logger(prefix: string) {
  return function (constructor: Function) {
    console.log(`${prefix} - ${constructor.name}`);
  };
}

@Logger("INFO")
class Product {}
```

✅ Output: `INFO - Product`

---

## 3. **Method Decorator**

A method decorator gets:

* `target` → class prototype
* `propertyKey` → method name
* `descriptor` → method details

```ts
function LogMethod(
  target: any,
  propertyKey: string,
  descriptor: PropertyDescriptor
) {
  const original = descriptor.value;

  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${propertyKey} with`, args);
    const result = original.apply(this, args);
    console.log(`Result:`, result);
    return result;
  };
}

class Calculator {
  @LogMethod
  add(a: number, b: number) {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(5, 3);
```

✅ Logs inputs and output of method calls.

---

## 4. **Property Decorator**

A property decorator runs when the property is **defined**, not when the object is instantiated.

```ts
function Readonly(target: any, propertyKey: string) {
  Object.defineProperty(target, propertyKey, {
    writable: false,
  });
}

class Car {
  @Readonly
  brand: string = "Tesla";
}

const car = new Car();
car.brand = "BMW"; // ❌ Error (brand is read-only)
```

---

## 5. **Accessor Decorator (Getter/Setter)**

```ts
function Capitalize(
  target: any,
  propertyKey: string,
  descriptor: PropertyDescriptor
) {
  const original = descriptor.set!;
  descriptor.set = function (value: string) {
    original.call(this, value.toUpperCase());
  };
}

class Person {
  private _name: string = "";

  @Capitalize
  set name(value: string) {
    this._name = value;
  }

  get name() {
    return this._name;
  }
}

const p = new Person();
p.name = "mohit";
console.log(p.name); // "MOHIT"
```

---

## 6. **Parameter Decorator**

Used to store metadata about parameters.

```ts
function LogParameter(target: any, propertyKey: string, parameterIndex: number) {
  console.log(`Parameter at index ${parameterIndex} in method ${propertyKey}`);
}

class Example {
  greet(@LogParameter message: string) {
    console.log(message);
  }
}
```

---

## 7. **Advanced Example: Validation Decorators**

We can build something like a mini **validation system**.

```ts
const validators: any = {};

function Required(target: any, propertyKey: string) {
  validators[propertyKey] = "required";
}

class UserForm {
  @Required
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}

function validate(obj: any): boolean {
  for (const key in validators) {
    if (validators[key] === "required" && !obj[key]) {
      console.log(`${key} is required`);
      return false;
    }
  }
  return true;
}

const form1 = new UserForm("");
validate(form1); // ❌ name is required
```

---

## 8. **Real-World Example (like NestJS)**

In **NestJS**, decorators like `@Controller`, `@Get`, etc., work similarly.

```ts
function Controller(prefix: string) {
  return function (constructor: Function) {
    constructor.prototype.prefix = prefix;
  };
}

function Get(path: string) {
  return function (target: any, propertyKey: string) {
    if (!target.routes) target.routes = [];
    target.routes.push({ path, method: propertyKey });
  };
}

@Controller("/users")
class UserController {
  @Get("/")
  getUsers() {
    return ["User1", "User2"];
  }
}

const ctrl = new UserController();
console.log(ctrl.prefix); // "/users"
console.log(ctrl.routes); // [{ path: "/", method: "getUsers" }]
```

---

# 🎯 Summary

* **Class Decorator** → adds behavior to classes.
* **Decorator Factory** → allows arguments.
* **Method Decorator** → wraps method execution.
* **Property Decorator** → modifies property behavior.
* **Accessor Decorator** → modifies getters/setters.
* **Parameter Decorator** → adds metadata about parameters.
* **Advanced Use** → validation, metadata storage, frameworks.

---

### use cases

```
interface User {
	log(): void;
}
  
  
  @Logger
  class User {
	name: string;
	rollNo : number
	constructor( name: string) {
		this.name = name;
		this.rollNo = 101;
	}
  }

  const u = new User("John");
  console.log("Instance properties:",u.log()); // will have to tell type to ts of log
```
Ah, yes 👍
That’s TypeScript being strict about **types**.

Even though at runtime your decorator adds `log()` to the prototype, **TypeScript’s type system doesn’t know about it**, so it complains:

```
Property 'log' does not exist on type 'User'.ts(2339)
```

---

### ✅ Fix 1: Tell TypeScript that `User` has `log`

You can extend the type inside your decorator by **returning a new class** that extends the original one and declares `log`:

```ts
function Logger<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    log() {
      console.log("log function added to class");
      return { name: (this as any).name, rollNo: (this as any).rollNo };
    }
  };
}

@Logger
class User {
  name: string;
  rollNo: number;

  constructor(name: string) {
    this.name = name;
    this.rollNo = 101;
  }
}

const u = new User("John");
console.log("Instance properties:", u.log());
```

✅ Now TypeScript **knows that `log` exists**.

---

### ✅ Fix 2: Use an `interface` merge

You can also **tell TypeScript separately** that `User` has `log`:

```ts
interface User {
  log(): void;
}
```

Now TS won’t complain:

```ts
function Logger(constructor: Function) {
  constructor.prototype.log = function() {
    console.log("log function added to class");
  };
}

@Logger
class User {
  name: string;
  rollNo: number;

  constructor(name: string) {
    this.name = name;
    this.rollNo = 101;
  }
}

const u = new User("John");
u.log(); // ✅ no error
```

---

### ✅ Fix 3: Use type assertion (`as any`)

Quick-and-dirty fix (not recommended, but useful sometimes):

```ts
(u as any).log();
```

This tells TypeScript “trust me, I know this property exists.”

---

⚡ Best practice: use **Fix 1** (returning a new typed class) when building decorators that add new methods, because it’s type-safe.

---

Ah! This is a **really important distinction** in TypeScript, and understanding how TS handles `implements` vs. **interface merging** will clarify why decorators behave differently. Let’s go **step by step**, including what happens behind the scenes.

---

## **1️⃣ `implements` an interface**

Example:

```ts
interface IUser {
    name: string;
    log(): void;
}

class User implements IUser {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    log() {
        console.log("Logging...");
    }
}
```

### How TypeScript handles this:

1. TypeScript **checks the class against the interface at compile time**.

   * It verifies that the class has all properties and methods declared in the interface (`name` and `log` in this case).
2. If any required property/method is missing, TS throws an error **during compilation**.
3. After compilation:

   * **Nothing special happens at runtime**.
   * The compiled JS does **not include interface information**, because interfaces are **type-only** constructs in TS.

💡 Behind the scenes:

* TS uses `implements` **only for static type checking**.
* It doesn’t generate runtime code.
* If a method is missing, TS stops you at compile time; otherwise, nothing changes in JS.

---

## **2️⃣ Interface merge with a class**

Example:

```ts
class User {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}

// Merge interface with class
interface User {
    log(): void;
}
```

### How TypeScript handles this:

1. TypeScript sees an `interface User` with `log()`.
2. It **merges** this interface with the class `User` **at compile time**.

   * After merging, TS “thinks” the class has a `log()` method, even if it doesn’t exist yet.
3. No runtime code is generated.
4. If you **later add `log` via a decorator**, TypeScript will now allow it without errors because the merged interface says `log` exists.

💡 Behind the scenes:

* Interface merge is purely **a type-level operation**.
* TS combines the class type with the interface type into a single **compile-time type**.
* It doesn’t enforce that the method exists at runtime — it just trusts you.

---

## **3️⃣ Key Differences**

| Feature                    | `implements`                              | Interface Merge                                    |
| -------------------------- | ----------------------------------------- | -------------------------------------------------- |
| Compile-time type checking | ✅ TS ensures class implements all members | ⚠️ TS assumes members exist; doesn’t check runtime |
| Runtime impact             | None (type-only)                          | None (type-only)                                   |
| When useful                | ✅ Enforcing class conforms to a contract  | ✅ Letting TS know about decorator-added members    |
| Safety                     | High — compiler enforces methods          | Medium — compiler trusts you                       |

---

### **Summary / Behind the Scene Understanding**

* `implements` → TS **verifies the class** matches the interface **at compile-time**.
* Interface merge → TS **merges types** so the class “looks like” it has those members **at compile-time**.
* Both disappear in the compiled JS; decorators are runtime operations, so interface merge is often used to satisfy TS when adding things at runtime.

---
Ah, now we’re getting to the **core of TypeScript’s type system magic**. Let’s carefully dissect **how interface merging happens behind the scenes**.

---

## **1️⃣ What interface merging actually is**

When you write something like:

```ts
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

interface User {
  log(): void;
}
```

TypeScript **merges the interface with the class type** at compile time.

* **Key point:** This is **purely a type-level operation**.
* TypeScript does **not emit any JavaScript** for interfaces — they exist only in the type system.

---

### Step-by-step behind the scenes

1. **Class declaration is compiled**

TypeScript sees:

```ts
class User { name: string; constructor(name: string) { this.name = name; } }
```

* TS creates a **class type** for `User`:

  ```ts
  type UserClassType = {
    name: string;
    constructor(name: string): User;
  }
  ```

2. **Interface declaration is compiled**

```ts
interface User { log(): void; }
```

* TS creates an **interface type**:

  ```ts
  type UserInterfaceType = {
    log(): void;
  }
  ```

3. **Merge happens**

* TypeScript merges the class type and interface type **into a single type called `User`**.
* Effectively, TS sees:

```ts
type User = {
  name: string;
  log(): void;  // added from interface
  constructor(name: string): User;
}
```

* **Important:** This is **type-only**. No JS code is generated.

4. **Type system now knows**

* Anywhere you use `User` in TS, the compiler believes `log()` exists.
* At runtime, if you call `log()` **without adding it via decorator or manually**, it will throw an error.

---

### Step 2 vs Step 3 in short

* **Step 2 (interface declaration):** TS creates a type representing what should exist.
* **Step 3 (merge):** TS merges the interface type into the class type **so the class “pretends” to have those members at compile time**.

---

### Step 4: Interaction with decorators

```ts
@Logger
class User { ... }

interface User { log(): void }
```

1. `Logger` runs **at runtime**, adding `log()` to the prototype.
2. Interface merge runs **at compile-time**, telling TS: “Trust me, instances have `log()`.”
3. TS sees everything as safe, and you get no type errors.

---

### ✅ Key Takeaways

* Interface merging = **compile-time type-level merging**.
* No runtime effect. TS “fuses” class type and interface type.
* Decorators = runtime addition of properties/methods. Merge + decorators = a safe combo for TS + JS.

---
Alright! Let’s do a **deep dive into `type` in TypeScript**. I’ll explain from **basic to advanced**, including how it works behind the scenes.

---

## **1️⃣ What is `type` in TypeScript**

In TypeScript, `type` is a **way to define a type alias**.

```ts
type ID = number | string;

let userId: ID;
userId = 101;     // ✅ valid
userId = "abc123"; // ✅ valid
```

* `type` does **not create a new type at runtime**, it’s purely **compile-time**.
* You can use it for:

  * Primitive unions
  * Objects
  * Functions
  * Tuples
  * Intersections & mapped types

---

## **2️⃣ Type vs Interface**

| Feature        | `type`                          | `interface`                     |
| -------------- | ------------------------------- | ------------------------------- |
| Merge          | ❌ cannot merge                  | ✅ can merge                     |
| Extend         | ✅ via `&` (intersection)        | ✅ via `extends`                 |
| Complex types  | ✅ unions, intersections, tuples | ❌ unions, limited intersections |
| Runtime effect | None (type-level only)          | None (type-level only)          |

Example:

```ts
type A = { x: number };
type B = { y: number };
type C = A & B; // intersection
// C is { x: number, y: number }
```

---

## **3️⃣ Object types**

```ts
type User = {
  name: string;
  age: number;
  log?: () => void; // optional property
};

const u: User = { name: "John", age: 25 };
```

* Optional properties (`?`)
* Readonly properties:

```ts
type UserReadonly = {
  readonly id: number;
  name: string;
};
```

* `readonly` prevents reassignment:

```ts
const u1: UserReadonly = { id: 1, name: "John" };
u1.id = 2; // ❌ error
```

---

## **4️⃣ Function types**

```ts
type Greet = (name: string) => string;

const greet: Greet = (n) => `Hello ${n}`;
```

* This is a **type alias for a function signature**.

---

## **5️⃣ Union & Intersection types**

* **Union `|`** = one of multiple types:

```ts
type ID = string | number;
let userId: ID;
userId = 101;    // ✅
userId = "abc";  // ✅
userId = true;   // ❌
```

* **Intersection `&`** = combine multiple types:

```ts
type A = { x: number };
type B = { y: string };
type C = A & B;  // { x: number; y: string }
```

---

## **6️⃣ Literal types**

```ts
type Direction = "up" | "down" | "left" | "right";

let move: Direction;
move = "up";     // ✅
move = "forward"; // ❌
```

* Useful for **strict enums or constants**.

---

## **7️⃣ Advanced: Generics with `type`**

```ts
type ApiResponse<T> = {
  data: T;
  error?: string;
};

const res: ApiResponse<string> = { data: "Hello" };
```

* Makes `type` **reusable with different type arguments**.

---

## **8️⃣ Behind the scenes**

* `type` is **completely erased** when TS compiles to JS.
* Example:

```ts
type ID = number;
const userId: ID = 101;
```

* Compiles to:

```js
const userId = 101;
```

* TS **only uses it for type checking at compile time**.
* No runtime impact, unlike classes or functions.

---

## **9️⃣ When to use `type` vs `interface`**

* Use `interface`:

  * When you need **extendable/mergeable contracts** (like classes).
* Use `type`:

  * For **unions, intersections, tuples, literals**, or more complex type expressions.

---

If you want, I can make a **visual diagram showing `type` vs `interface` vs runtime objects** so you can see how TS handles them behind the scenes.

Do you want me to do that?





  
