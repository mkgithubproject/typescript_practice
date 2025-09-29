Perfect! Let’s move to **Interfaces and Type Aliases**—these are essential for defining the **shape of objects, reusable types, and contracts** in TypeScript.

---

# **4️⃣ Interfaces and Type Aliases**

---

## **A. Interfaces**

An **interface** defines the **structure of an object** (or class), including types for its properties.

```ts
interface User {
  id: number;
  name: string;
  email?: string; // optional property
}

const user1: User = {
  id: 1,
  name: "Mohit",
};

const user2: User = {
  id: 2,
  name: "Rohit",
  email: "rohit@example.com",
};
```

✅ Key Points:

* Optional properties are marked with `?`.
* Interfaces are **extensible**: one interface can extend another.

```ts
interface Employee extends User {
  employeeId: number;
}

const emp: Employee = {
  id: 1,
  name: "Mohit",
  employeeId: 101,
};
```

* Interfaces can also describe **functions**:

```ts
interface GreetFunc {
  (name: string, title?: string): string;
}

const greet: GreetFunc = (name, title) => {
  return title ? `Hello ${title} ${name}` : `Hello ${name}`;
};
```

---

## **B. Type Aliases**

A **type alias** lets you **create a custom type**, which can be a primitive, union, intersection, tuple, or object.

### **1. Basic Type Alias**

```ts
type ID = string | number;

let userId: ID;
userId = 101;       // ✅
userId = "abc123";  // ✅
```

### **2. Object Type Alias**

```ts
type UserType = {
  id: number;
  name: string;
  email?: string;
};

const user: UserType = { id: 1, name: "Mohit" };
```

### **3. Intersection Types**

Combine multiple types:

```ts
type Person = { name: string };
type Employee = { employeeId: number };

type Staff = Person & Employee;

const staff: Staff = { name: "Mohit", employeeId: 101 };
```

### **4. Function Type Alias**

```ts
type Greet = (name: string, title?: string) => string;

const greet: Greet = (name, title) =>
  title ? `Hello ${title} ${name}` : `Hello ${name}`;
```

---

## **C. Interface vs Type Alias**

| Feature                | Interface   | Type Alias         |
| ---------------------- | ----------- | ------------------ |
| Object Shapes          | ✅           | ✅                  |
| Extendable             | ✅ `extends` | ✅ `&` intersection |
| Union Types            | ❌           | ✅                  |
| Primitives / Functions | ❌           | ✅                  |
| Declaration Merging    | ✅           | ❌                  |

✅ **Rule of Thumb**:

* Use **interface** for **objects & classes**, especially when planning to extend.
* Use **type** for **unions, primitives, tuples, or complex types**.

---

## **D. Practical Examples**

### **1. Typed Array of Objects**

```ts
const users: User[] = [
  { id: 1, name: "Mohit" },
  { id: 2, name: "Rohit", email: "rohit@example.com" },
];
```

### **2. Function with Typed Object**

```ts
function printUser(user: User): void {
  console.log(`${user.name} (${user.id})`);
}

printUser(users[0]); // Mohit (1)
```

### **3. Union with Type Alias**

```ts
type Response = "success" | "failure";

function handleResponse(res: Response) {
  console.log(`Status: ${res}`);
}

handleResponse("success"); // ✅
handleResponse("error");   // ❌ Error
```

---

Interfaces and Type Aliases are **core to TypeScript**, and they become extremely useful in **large projects** where objects, functions, and APIs need consistent typing.

---

Perfect! Let’s focus on **Interfaces in TypeScript** exactly according to your roadmap:

# **4️⃣ Interfaces in TypeScript**

Interfaces are **contracts** that define the **shape of an object**, including properties, methods, and types.

---

## **A. Defining Object Types**

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

const user1: User = {
  id: 1,
  name: "Mohit",
  email: "mohit@example.com"
};
```

✅ Key points:

* `interface` defines the **structure** of an object.
* Helps **type-check objects** at compile time.

---

## **B. Optional Properties**

* Use `?` to mark properties as optional.

```ts
interface User {
  id: number;
  name: string;
  email?: string; // optional
}

const user2: User = {
  id: 2,
  name: "Rohit"
  // email is optional
};
```

* Optional properties **may or may not be present**.

---

## **C. Readonly Properties**

* Use `readonly` to make properties **immutable** after initialization.

```ts
interface User {
  readonly id: number;
  name: string;
}

const user3: User = { id: 101, name: "Mohit" };
user3.name = "Rohit"; // ✅ allowed
// user3.id = 102; // ❌ Error: Cannot assign to 'id' because it is readonly
```

---

## **D. Function Types in Interfaces**

Interfaces can define **function signatures** inside objects.

```ts
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

const calc: Calculator = {
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  }
};

console.log(calc.add(5, 3));      // 8
console.log(calc.subtract(5, 3)); // 2
```

* Also, interfaces can define **standalone function types**:

```ts
interface GreetFunc {
  (name: string, title?: string): string;
}

const greet: GreetFunc = (name, title) =>
  title ? `Hello ${title} ${name}` : `Hello ${name}`;

console.log(greet("Mohit"));       // Hello Mohit
console.log(greet("Mohit", "Mr.")); // Hello Mr. Mohit
```

---

### ✅ **Summary Table**

| Feature                  | Syntax / Example                                 | Notes                            |
| ------------------------ | ------------------------------------------------ | -------------------------------- |
| Object Type              | `interface User { id: number; name: string }`    | Defines shape of object          |
| Optional Property        | `email?: string`                                 | May or may not exist             |
| Readonly Property        | `readonly id: number`                            | Cannot be reassigned             |
| Function Types           | `add(a: number, b: number): number`              | Methods in interface             |
| Standalone Function Type | `interface GreetFunc { (name: string): string }` | Function signature for variables |

---

Ah! That’s an important distinction. In TypeScript:

* **Classes cannot extend interfaces** like they do with classes in Java.
* **Classes implement interfaces** instead.

---

## **A. Implementing an Interface (Not Extending)**

```ts
interface Person {
  id: number;
  name: string;
  display(): void;
}

// Class implements the interface
class User implements Person {
  constructor(public id: number, public name: string) {}

  display(): void {
    console.log(`User: ${this.name} (ID: ${this.id})`);
  }
}

const user = new User(1, "Mohit");
user.display(); // User: Mohit (ID: 1)
```

✅ Key points:

* `implements` enforces that the **class must have all properties and methods defined in the interface**.
* A class **cannot use `extends` with an interface**; `extends` is only for **class inheritance**.

---

## **B. Implementing Multiple Interfaces**

```ts
interface Contact {
  email: string;
  phone: string;
}

interface Employee extends Person {
  department: string;
}

// Class can implement multiple interfaces
class Staff implements Employee, Contact {
  constructor(
    public id: number,
    public name: string,
    public department: string,
    public email: string,
    public phone: string
  ) {}

  display(): void {
    console.log(`${this.name} works in ${this.department}`);
  }
}

const staff = new Staff(1, "Rohit", "IT", "rohit@example.com", "1234567890");
staff.display(); // Rohit works in IT
```

✅ Key points:

* Use `implements` to **enforce multiple interfaces**.
* All properties/methods in the interfaces must exist in the class.

---

### **Summary**

| Concept                  | Java          | TypeScript                             |
| ------------------------ | ------------- | -------------------------------------- |
| Class inherits class     | `extends`     | `extends`                              |
| Class inherits interface | `implements`  | `implements` (cannot use `extends`)    |
| Multiple inheritance     | ❌ not allowed | ✅ via interfaces (implements multiple) |

---
Yes, **interfaces in TypeScript are conceptually very similar to interfaces in Java**, but there are some differences in syntax and flexibility. Let me break it down clearly:

---

## **1️⃣ Similarities**

| Feature                | Java Interface                                     | TypeScript Interface                                        |
| ---------------------- | -------------------------------------------------- | ----------------------------------------------------------- |
| Contract definition    | `interface` defines methods a class must implement | Same, defines properties & methods a class must implement   |
| Implemented by classes | `class MyClass implements MyInterface`             | `class MyClass implements MyInterface`                      |
| Method signatures      | Only signatures, no method body                    | Only signatures (optional: can include optional properties) |
| Multiple inheritance   | A class can implement multiple interfaces          | Same, a class can implement multiple interfaces             |

### Example Comparison

**Java:**

```java
interface Person {
    void display();
}

class User implements Person {
    public void display() {
        System.out.println("Hello");
    }
}
```

**TypeScript:**

```ts
interface Person {
    name: string;
    display(): void;
}

class User implements Person {
    constructor(public name: string) {}
    
    display(): void {
        console.log(`Hello, ${this.name}`);
    }
}
```

✅ In both languages:

* The interface acts as a **contract**.
* The class **must implement all properties/methods** defined in the interface.

---

## **2️⃣ Key Differences**

| Aspect                                 | Java                                | TypeScript                                                                 |
| -------------------------------------- | ----------------------------------- | -------------------------------------------------------------------------- |
| Properties in interfaces               | Cannot have fields (only constants) | Can define properties with types                                           |
| Optional properties                    | ❌ not supported                     | ✅ supported using `?`                                                      |
| Readonly properties                    | ❌ not supported                     | ✅ supported using `readonly`                                               |
| Can extend multiple interfaces         | ✅                                   | ✅ (with `extends`)                                                         |
| Can define function type outside class | ❌ only methods in interface         | ✅ standalone function types supported                                      |
| Default implementations                | Java 8+: `default` keyword          | TypeScript: cannot define body in interface (use abstract class if needed) |

---

### **3️⃣ Summary**

* **Interfaces in TypeScript ≈ Interfaces in Java** in purpose: a **contract for classes**.
* **Differences** mostly come from TypeScript being a superset of JavaScript:

  * Supports **optional & readonly properties**.
  * Can define **function types**.
  * Can extend multiple interfaces easily.

---

Ah! That’s an important clarification: **objects don’t “implement” interfaces” like classes do**. Let me explain carefully.

---

### **1️⃣ Class implements an interface**

* When a **class implements an interface**, TypeScript **enforces that the class contains all properties and methods** defined in the interface.
* You use the `implements` keyword:

```ts
interface Person {
    name: string;
    age: number;
    greet(): void;
}

class Employee implements Person {
    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(`Hi, I'm ${this.name}`);
    }
}
```

✅ Here, `Employee` **formally implements** the interface. If it misses any property/method, TS will throw an error.

---

### **2️⃣ Object “matches” an interface**

* Plain objects **don’t use `implements`**.
* Instead, you **assign the interface as a type** to the object. TypeScript then **checks that the object matches the interface structure**.

```ts
interface Person {
    name: string;
    age: number;
    greet(): void;
}

const personObj: Person = {
    name: "Mohit",
    age: 25,
    greet() {
        console.log(`Hi, I'm ${this.name}`);
    }
};
```

* Here, `personObj` **is typed as `Person`**, but we **don’t say it “implements” Person**.
* TypeScript checks the shape, but there’s **no formal implementation like in classes**.

---

### **3️⃣ Key difference**

| Aspect            | Class                    | Object                         |
| ----------------- | ------------------------ | ------------------------------ |
| Uses `implements` | ✅ Yes                    | ❌ No                           |
| Checked by TS     | Must include all members | Must match shape               |
| Can add methods   | ✅ Yes                    | ✅ Yes, but just part of object |
| Runtime effect    | Can create instances     | Just a literal object          |

---

So, the short answer:

> **Objects don’t implement interfaces. Classes implement interfaces. Objects are typed to match interfaces.**

---







