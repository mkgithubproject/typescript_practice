Perfect! Let’s go through **Classes & Objects** in TypeScript according to your roadmap, covering **class properties, constructors, access modifiers, getters/setters, implementing interfaces, and inheritance**.

---

# **6️⃣ Classes & Objects in TypeScript**

TypeScript extends JavaScript classes with **type safety** and **access modifiers**.

---

## **A. Class Properties & Constructor**

```ts
class User {
  id: number;
  name: string;

  constructor(id: number, name: string) {
    this.id = id;
    this.name = name;
  }

  display(): void {
    console.log(`User: ${this.name} (ID: ${this.id})`);
  }
}

const user1 = new User(1, "Mohit");
user1.display(); // User: Mohit (ID: 1)
```

* `constructor` initializes **class properties**.
* Methods can have **typed parameters and return types**.

---

## **B. Access Modifiers**

TypeScript supports **three access levels**:

```ts
class User {
  public id: number;        // accessible everywhere
  private password: string; // accessible only inside class
  protected name: string;   // accessible in class & subclasses

  constructor(id: number, name: string, password: string) {
    this.id = id;
    this.name = name;
    this.password = password;
  }

  display(): void {
    console.log(`User: ${this.name}`);
  }

  private showPassword(): void {
    console.log(this.password);
  }
}

const user = new User(1, "Mohit", "12345");
console.log(user.id);    // ✅ 1
// console.log(user.password); // ❌ Error
```

---

## **C. Getters & Setters**

* Control **access to properties** with logic.

```ts
class Employee {
  private _salary: number;

  constructor(salary: number) {
    this._salary = salary;
  }

  get salary(): number {
    return this._salary;
  }

  set salary(value: number) {
    if (value > 0) this._salary = value;
    else console.error("Salary must be positive");
  }
}

const emp = new Employee(5000);
console.log(emp.salary); // 5000
emp.salary = -1000;       // Error: Salary must be positive
emp.salary = 7000;
console.log(emp.salary); // 7000
```

---

## **D. Implementing Interfaces**

* Classes can **implement interfaces**, enforcing structure.

```ts
interface Person {
  id: number;
  name: string;
  display(): void;
}

class User implements Person {
  constructor(public id: number, public name: string) {}

  display(): void {
    console.log(`User: ${this.name}`);
  }
}

const user2 = new User(2, "Rohit");
user2.display(); // User: Rohit
```

---

## **E. Inheritance**

* Classes can **extend other classes**.

```ts
class Employee extends User {
  constructor(id: number, name: string, public department: string) {
    super(id, name); // call parent constructor
  }

  display(): void {
    console.log(`Employee: ${this.name}, Dept: ${this.department}`);
  }
}

const emp1 = new Employee(3, "Mohit", "IT");
emp1.display(); // Employee: Mohit, Dept: IT
```

* `super()` calls the **parent class constructor**.
* Methods can be **overridden** in child classes.

---

### ✅ **Summary Table**

| Feature                      | Example / Syntax                     | Notes                                  |
| ---------------------------- | ------------------------------------ | -------------------------------------- |
| Class & Constructor          | `class User { constructor(...) {} }` | Initialize properties                  |
| Public / Private / Protected | `public`, `private`, `protected`     | Control access                         |
| Getter / Setter              | `get salary()`, `set salary(value)`  | Controlled property access             |
| Implement Interface          | `class User implements Person`       | Enforces object structure              |
| Inheritance                  | `class Employee extends User`        | Reuse & override parent class behavior |

---

This fully covers **Classes & Objects** according to your roadmap.

Next logical topics in a TypeScript roadmap would be **Advanced Types** (Conditional, Mapped, Utility Types) or **Generics** if we haven’t completed that yet.

Do you want to go to **Generics** next?
