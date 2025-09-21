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

This fully covers **Interfaces** according to your roadmap.

Next in the roadmap, we can cover **Type Aliases**, which are often used for **custom types, unions, intersections, and tuples**.

Do you want me to move to **Type Aliases** next?

