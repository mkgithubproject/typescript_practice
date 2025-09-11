Perfect 👌 Practicing with a **real small project** is the best way to learn TypeScript.
Let’s start with a **simple CLI To-Do App** (command-line app).

---

# 📝 Project: Simple To-Do List (TypeScript)

### 📂 Folder structure

```
ts-todo/
│── src/
│    └── index.ts
│── tsconfig.json
│── package.json
```

---

## 1. **Setup**

```bash
mkdir ts-todo && cd ts-todo
npm init -y
npm install --save-dev typescript @types/node
npx tsc --init // or npm run tsRun ( add a script in package.json if you want to run  that package using npm)
```

Update **`tsconfig.json`** (minimal for now):

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true
  }
}
```

---

## 2. **Write Code**

Create `src/index.ts`:

```ts
// A simple To-Do List app in TypeScript

interface Todo {
  id: number;
  task: string;
  completed: boolean;
}

class TodoList {
  private todos: Todo[] = [];
  private idCounter: number = 1;

  addTask(task: string): void {
    const newTodo: Todo = {
      id: this.idCounter++,
      task,
      completed: false,
    };
    this.todos.push(newTodo);
    console.log(`✅ Task added: "${task}"`);
  }

  listTasks(): void {
    console.log("\n📋 To-Do List:");
    this.todos.forEach((todo) => {
      console.log(
        `${todo.id}. [${todo.completed ? "✔" : " "}] ${todo.task}`
      );
    });
  }

  completeTask(id: number): void {
    const todo = this.todos.find((t) => t.id === id);
    if (todo) {
      todo.completed = true;
      console.log(`🎉 Task "${todo.task}" marked as completed!`);
    } else {
      console.log("⚠️ Task not found.");
    }
  }
}

// ------------------ Run Demo ------------------
const myTodos = new TodoList();
myTodos.addTask("Learn TypeScript basics");
myTodos.addTask("Build a small project");
myTodos.listTasks();
myTodos.completeTask(1);
myTodos.listTasks();
```

---

## 3. **Compile and Run**

Compile:

```bash
npx tsc
```

Run:

```bash
node dist/index.js
```

---

## 4. ✅ Output

```
✅ Task added: "Learn TypeScript basics"
✅ Task added: "Build a small project"

📋 To-Do List:
1. [ ] Learn TypeScript basics
2. [ ] Build a small project
🎉 Task "Learn TypeScript basics" marked as completed!

📋 To-Do List:
1. [✔] Learn TypeScript basics
2. [ ] Build a small project
```

---

👉 This project covers:

* **Interfaces** (for Todo type)
* **Classes** (for TodoList logic)
* **Strict typing** (task: string, id: number, etc.)

---
Great catch 🚀 This is where the difference between **local install** and **npx on-the-fly** comes in.

---

## 🔎 Case 1: You **installed TypeScript locally**

```bash
npm install --save-dev typescript @types/node
npx tsc --init
```

* `typescript` is now in your project’s `node_modules/`.
* `npx` finds it in `./node_modules/.bin/tsc` and runs it.
* ✅ Stable, works even without internet.
* ✅ Same version across the whole team (locked in `package.json`).

---

## 🔎 Case 2: You run `npx tsc` **without installing TypeScript**

```bash
npx tsc --init
```

* `npx` checks:

  1. Is `tsc` in local `node_modules/.bin`? ❌ (not installed)
  2. Then it **downloads a temporary copy** of `typescript` from the npm registry into a cache folder (not `node_modules`).
  3. Runs it, then discards it later.
* ⚠️ Works, but:

  * Requires internet.
  * You won’t have `typescript` in your `package.json`, so teammates won’t know what version you used.
  * Each new run might fetch a different version if `latest` changed.

---

## 📌 Example

Try this:

```bash
npx tsc -v
```

👉 If you didn’t install TypeScript, it will still show a version (downloaded temporarily).

But check your project:

```bash
ls node_modules
```

👉 You won’t see `typescript` there unless you installed it with `npm install`.

---

# ✅ Best Practice

* For **projects**: install locally (`npm install --save-dev typescript`) → run with `npx tsc`.
* For **one-off experiments**: you can just `npx tsc` without installing.

---
Excellent question 👌 — let’s break down **why we use `--save-dev` when installing TypeScript**.

---

# 📦 `dependencies` vs `devDependencies`

In your `package.json`, there are **two sections**:

```json
{
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0"
  }
}
```

---

## 1. **`dependencies`**

* These are packages needed **at runtime** (when your app is running in production).
* Example:

  * `express`, `mongoose`, `socket.io` → your app won’t work without them.

When you run:

```bash
npm install express
```

➡️ It goes into `dependencies`.

---

## 2. **`devDependencies`**

* These are packages needed **only during development** (not in production).
* Example:

  * `typescript` (you only need it to compile TS → JS, not to run the app)
  * `jest` (for testing)
  * `nodemon` (auto-restarts server in dev mode)

When you run:

```bash
npm install --save-dev typescript
```

➡️ It goes into `devDependencies`.

---

## 🔎 Why `typescript` is `--save-dev`

* Your **production server** runs JavaScript (`node dist/index.js`).
* It does **not need TypeScript installed**.
* So we install `typescript` only for development → `devDependencies`.

If you put it in `dependencies`, it would still work, but:

* Production would install unnecessary packages → slower deploys, bigger `node_modules`.
* It’s not the intended usage.

---

# ✅ Rule of Thumb

* **`--save-dev` (`devDependencies`)** → tools for development (TypeScript, Jest, ESLint, Nodemon).
* **normal install (`dependencies`)** → libraries required at runtime (Express, Mongoose, React).

---





