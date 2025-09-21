Perfect! Let’s start **from TypeScript basic types** step by step. I’ll explain each type clearly with examples you can try.

---

## **1️⃣ Basic Types**

### **1. Number**

```ts
let age: number = 25;
let price: number = 99.99;
```

* TS ensures `age` and `price` can only hold numbers.
* Assigning a string will throw an error:

```ts
// Error
age = "twenty-five";
```

---

### **2. String**

```ts
let name: string = "Mohit";
let greeting: string = `Hello, ${name}!`; // Template literals
```

---

### **3. Boolean**

```ts
let isLoggedIn: boolean = true;
let hasAccess: boolean = false;
```

---

### **4. Array**

```ts
// Number array
let numbers: number[] = [1, 2, 3, 4];

// String array
let fruits: string[] = ["apple", "banana"];

// Generic array
let booleans: Array<boolean> = [true, false];
```

---

### **5. Tuple**

* Fixed-length array with specific types at each position:

```ts
let user: [string, number] = ["Mohit", 25];
```

* You **cannot** swap types or add extra elements beyond defined types.

---

### **6. Enum**

* Named constants for a set of values:

```ts
enum Color {
  Red,
  Green,
  Blue,
}

let favoriteColor: Color = Color.Green; // 1
```

* You can also assign custom numbers or strings:

```ts
enum Status {
  Success = "SUCCESS",
  Failure = "FAILURE",
}

let apiStatus: Status = Status.Success;
```

---

### **7. Any**

* Can hold **any type** (not recommended unless necessary):

```ts
let data: any = 42;
data = "Hello"; // No error
data = true;
```

---

### **8. Unknown**

* Safer version of `any`. You must **check type before using it**:

```ts
let value: unknown = 10;

if (typeof value === "number") {
  let doubled = value * 2; // ✅ Safe
}
```

---

### **9. Void**

* Usually for **functions that don’t return anything**:

```ts
function logMessage(msg: string): void {
  console.log(msg);
}
```

---

### **🔟 Never**

* For functions that **never return** (throw error or infinite loop):

```ts
function throwError(): never {
  throw new Error("Something went wrong!");
}
```

---

### **1️⃣1️⃣ Null & Undefined**

```ts
let nothing: null = null;
let notAssigned: undefined = undefined;

// Optional chaining
let maybeNumber: number | null = null;
maybeNumber = 42;
```

---

### **1️⃣2️⃣ Union Types**

* A variable can hold **more than one type**:

```ts
let id: string | number;
id = "abc123";
id = 123; // ✅ Both valid
```

---

### **1️⃣3️⃣ Literal Types**

* Variable can hold **exact value(s)**:

```ts
let direction: "up" | "down" | "left" | "right";
direction = "up"; // ✅
```

---

If you want, we can **move next to functions and how to type them** in TypeScript.

Do you want to continue with **typed functions** next?
