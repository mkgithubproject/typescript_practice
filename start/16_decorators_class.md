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

Perfect! Let’s really **demystify how a class decorator can replace a constructor** in TypeScript by building a **custom, minimal example** step by step. We’ll simulate what happens “behind the scenes” so you can **see it clearly**.

---

## **1️⃣ Basic idea**

A **class decorator** can:

1. Receive the original constructor (`User`) as an argument.
2. Return a **new constructor** (a new class) that replaces the original one.
3. The returned constructor can:

   * Call the original constructor using `super(...)`.
   * Add new properties or methods.
   * Override or change behavior.

---

## **2️⃣ Build a custom constructor replacer**

Let’s create a **very simple example** to visualize:

```ts
// Step 1: Original class
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
        console.log("Original constructor:", this.name);
    }
}

// Step 2: Decorator that replaces constructor
function ReplaceCtor<T extends { new (...args: any[]): {} }>(OriginalConstructor: T) {
    // Return a new class
    return class extends OriginalConstructor {
        newProp: string;

        constructor(...args: any[]) {
            console.log("Before original constructor");
            super(...args); // call original constructor
            console.log("After original constructor");
            this.newProp = "Added by decorator";
        }
    };
}

// Step 3: Apply decorator manually
const DecoratedPerson = ReplaceCtor(Person);

// Step 4: Create instance
const p = new DecoratedPerson("Alice");

console.log(p);
```

---

### **Step-by-step timeline**

1. **Original class is defined**:

```ts
class Person { ... }
```

2. **Decorator runs**:

```ts
const DecoratedPerson = ReplaceCtor(Person);
```

* `ReplaceCtor` receives `Person` as `OriginalConstructor`.
* It returns a **new class** that extends `Person`.
* The returned class is assigned to `DecoratedPerson`.
* ✅ At this point, `Person` is unchanged, but `DecoratedPerson` has the **replaced constructor**.

3. **Instance creation**:

```ts
const p = new DecoratedPerson("Alice");
```

* Calls the **new constructor** inside the decorator.
* Executes:

```ts
console.log("Before original constructor");
super(...args); // calls Person constructor
console.log("After original constructor");
this.newProp = "Added by decorator";
```

* Result:

```
Before original constructor
Original constructor: Alice
After original constructor
```

4. **Inspecting instance**:

```ts
console.log(p);
```

Output:

```
DecoratedPerson { name: 'Alice', newProp: 'Added by decorator' }
```

* Instance has **original properties** (`name`) + **new property** (`newProp`).

---

## **3️⃣ Key insights**

* **`class extends OriginalConstructor`**: allows us to **wrap the original constructor**.
* **`super(...args)`**: ensures the original constructor logic runs.
* **Returning a class from decorator**: **replaces the original constructor** at runtime.
* TypeScript type system doesn’t automatically know about `newProp`; you need:

  * Type assertion (`as any`)
  * Or generic return type with extra properties.

---

## **4️⃣ Visual analogy**

```
OriginalClass (Person)  ---> Decorator ---> NewClass extends OriginalClass
                     ^                               |
                     |                               |
                 Called when instance created    Adds newProp
```

* When TS sees `@ReplaceCtor`, it **replaces the class binding**.
* At runtime, `new DecoratedPerson()` calls the **decorator’s returned constructor**.

---

Exactly! Let me clarify this carefully.

---

## **1️⃣ What happens when you use a class decorator**

In TypeScript:

```ts
@ReplaceCtor
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}
```

is essentially **transpiled to something like this** behind the scenes:

```ts
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}

// Apply decorator
Person = ReplaceCtor(Person);
```

---

### ✅ Key points

1. **The decorator function runs immediately when the class is defined.**
2. **Whatever the decorator returns becomes the “new constructor” for that class.**
3. So after the decorator runs:

```ts
const p = new Person("Alice");
```

* TS (and JS at runtime) now calls the **returned class from the decorator**, not the original constructor.
* You don’t have to change any code where you instantiate `Person`; TS **automatically uses the replaced constructor**.

---

### **Step-by-step “replacement” logic**

1. Original class defined:

```ts
class Person { ... }
```

2. Decorator executes:

```ts
Person = ReplaceCtor(Person);
```

* `ReplaceCtor` returns a **new class**:

```ts
class extends OriginalConstructor { ... }
```

* This new class **replaces the original `Person`** in the current scope.

3. Instance creation:

```ts
const p = new Person("Alice");
```

* Actually calls the **decorator-returned class constructor**.
* The original constructor still runs **inside `super(...)`**, if you call it.

---

### **Analogy**

Think of it like:

```ts
let OriginalClass = class { ... };
OriginalClass = decorate(OriginalClass);
const obj = new OriginalClass(); // Calls decorated version
```

* `@Decorator` just **rewrites the class binding**.
* All existing `new ClassName()` calls now automatically point to the new constructor.

---

### ✅ TL;DR

* TypeScript doesn’t literally rewrite `new Person` to `new DecoratedPerson`.
* Instead, it **reassigns the class name**:

```ts
Person = ReplaceCtor(Person);
```

* After that, `new Person()` calls the **decorator-replaced class** automatically.

---

Perfect! Let’s explore **Dependency Injection (DI) using a class decorator** in TypeScript. I’ll make it **simple, clear, and fully working**, so you can see how decorators can inject dependencies into a class.

---

## **1️⃣ Concept**

* **Dependency Injection** = providing a class with the instances it needs **from the outside** rather than creating them inside the class.
* With decorators, we can **automatically inject dependencies** at runtime.

---

## **2️⃣ Example: Logger Service**

```ts
// Step 1: Dependency class
class LoggerService {
    log(message: string) {
        console.log("LoggerService:", message);
    }
}

// Step 2: DI decorator
function InjectLogger<T extends { new(...args: any[]): {} }>(constructor: T) {
    return class extends constructor {
        logger = new LoggerService(); // inject dependency
    };
}

// Step 3: Consumer class
@InjectLogger
class UserService {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    printName() {
        // @ts-ignore
        this.logger.log(`User name is ${this.name}`);
    }
}

// Step 4: Use it
const userService = new UserService("John");
userService.printName();
```

### ✅ Output:

```
LoggerService: User name is John
```

---

## **3️⃣ How it works behind the scenes**

1. Original class:

```ts
class UserService { ... }
```

2. Decorator executes:

```ts
UserService = InjectLogger(UserService);
```

* Returns a **new class extending the original**.
* Adds a `logger` property with an instance of `LoggerService`.

3. Instance creation:

```ts
const userService = new UserService("John");
```

* Calls the **decorator-replaced constructor**.
* `logger` is automatically injected.
* Methods in the class can now use `this.logger`.

---

## **4️⃣ Making it type-safe**

TS doesn’t know that `logger` exists unless we tell it:

```ts
interface UserService {
    logger: LoggerService;
}
```

Now, TS knows `logger` exists and you don’t need `@ts-ignore`.

---

## **5️⃣ Advanced: Injecting arbitrary dependencies**

We can make a **generic DI decorator**:

```ts
function Inject<T>(Dependency: new () => T) {
    return function <U extends { new(...args: any[]): {} }>(constructor: U) {
        return class extends constructor {
            dep = new Dependency();
        };
    };
}

// Usage
@Inject(LoggerService)
class ProductService {
    name: string;
    constructor(name: string) { this.name = name; }
}

const productService = new ProductService("Book");
// @ts-ignore
productService.dep.log("Product created!");
```

* Now the decorator can **inject any dependency class**.
* This is essentially how frameworks like **NestJS** do DI behind the scenes.

---

💡 **Key points**:

1. **Class decorators run once at class definition**, not per instance.
2. Returning a **new class** allows you to add injected dependencies.
3. You can combine this with **interface merging** to make TS types safe.

---

Absolutely! Adding **metadata** in a class decorator is a very common pattern in TypeScript, especially for frameworks like **NestJS** or **Angular**. Metadata allows you to **attach extra information to a class**, which can be read later at runtime.

Let’s go step by step.

---

## **1️⃣ Concept**

* **Metadata** = additional information about a class (or property/method) stored in a standardized way.
* TypeScript supports this via the **`reflect-metadata`** library.
* Example use case: marking a class as injectable, tagging roles, versioning, etc.

---

## **2️⃣ Setup**

Install `reflect-metadata` (needed for TypeScript decorators to store metadata):

```bash
npm install reflect-metadata
```

And enable these TS compiler options in `tsconfig.json`:

```json
{
  "experimentalDecorators": true,
  "emitDecoratorMetadata": true
}
```

Then import at the entry point:

```ts
import "reflect-metadata";
```

---

## **3️⃣ Example: Attaching metadata to a class**

```ts
import "reflect-metadata";

// Step 1: Class decorator to add metadata
function Role(roleName: string) {
    return function (constructor: Function) {
        // Attach metadata using Reflect API
        Reflect.defineMetadata("role", roleName, constructor);
    };
}

// Step 2: Apply decorator
@Role("Admin")
class UserService {
    constructor(public name: string) {}
}

// Step 3: Read metadata later
const role = Reflect.getMetadata("role", UserService);
console.log("UserService role:", role);
```

### ✅ Output:

```
UserService role: Admin
```

---

## **4️⃣ How it works behind the scenes**

1. Class decorator executes **at class definition**.
2. `Reflect.defineMetadata("role", roleName, constructor)` attaches metadata **to the class constructor function**.
3. Later, you can read it with `Reflect.getMetadata(key, target)` from **anywhere in your app**.

---

## **5️⃣ Advanced: Multiple metadata**

You can attach multiple metadata entries:

```ts
function Version(ver: string) {
    return function (constructor: Function) {
        Reflect.defineMetadata("version", ver, constructor);
    };
}

@Role("Admin")
@Version("1.0.0")
class UserService {}

console.log("Role:", Reflect.getMetadata("role", UserService));
console.log("Version:", Reflect.getMetadata("version", UserService));
```

### Output:

```
Role: Admin
Version: 1.0.0
```

* The order of decorators **matters** if multiple decorators modify metadata.

---

### **6️⃣ Use Case**

* Frameworks use metadata to implement **dependency injection**, **serialization**, **ORM mapping**, and **authorization**.
* For example, NestJS reads metadata to know which classes are injectable and which roles are required for routes.

---











  
