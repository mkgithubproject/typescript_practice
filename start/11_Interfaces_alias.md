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

Next, we can move to **Generics in TypeScript**, which is the **next step in your roadmap** after mastering interfaces and type aliases.

Do you want to go to **Generics** next?
