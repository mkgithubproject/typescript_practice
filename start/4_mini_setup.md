Got it! Let’s set up a **small TypeScript mini project** from scratch. I’ll make it simple but structured, so you can expand it later. I’ll guide you step by step. We’ll make a **CLI-based Todo App** as an example mini project.

---

## **1. Create Project Folder**

```bash
mkdir ts-mini-project
cd ts-mini-project
```

---

## **2. Initialize Node.js Project**

```bash
npm init -y
```

This creates a `package.json`.

---

## **3. Install TypeScript and Dev Dependencies**

```bash
npm install typescript ts-node @types/node --save-dev
```

* `typescript` → TypeScript compiler
* `ts-node` → Run TypeScript directly without compiling
* `@types/node` → Node.js type definitions

---

## **4. Initialize TypeScript Config**

```bash
npx tsc --init
```

This creates `tsconfig.json`. You can edit it to make it simpler for Node projects:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

---

## **5. Create Folder Structure**

```
ts-mini-project/
├── src/
│   ├── index.ts
│   └── todo.ts
├── package.json
├── tsconfig.json
```

---

## **6. Write TypeScript Code**

**`src/todo.ts`** – Todo logic:

```ts
export interface Todo {
    id: number;
    title: string;
    completed: boolean;
}

export class TodoList {
    private todos: Todo[] = [];
    private idCounter = 1;

    add(title: string): Todo {
        const todo: Todo = { id: this.idCounter++, title, completed: false };
        this.todos.push(todo);
        return todo;
    }

    list(): Todo[] {
        return this.todos;
    }

    complete(id: number): boolean {
        const todo = this.todos.find(t => t.id === id);
        if (todo) {
            todo.completed = true;
            return true;
        }
        return false;
    }
}
```

**`src/index.ts`** – CLI interface:

```ts
import readline from "readline";
import { TodoList } from "./todo";

const todoList = new TodoList();

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

function showMenu() {
    console.log("\n1. Add Todo\n2. List Todos\n3. Complete Todo\n4. Exit");
    rl.question("Choose option: ", handleInput);
}

function handleInput(option: string) {
    switch (option) {
        case "1":
            rl.question("Enter todo title: ", title => {
                todoList.add(title);
                console.log("Todo added!");
                showMenu();
            });
            break;
        case "2":
            console.log("\nTodos:");
            todoList.list().forEach(t => {
                console.log(`${t.id}. [${t.completed ? "x" : " "}] ${t.title}`);
            });
            showMenu();
            break;
        case "3":
            rl.question("Enter todo id to complete: ", idStr => {
                const id = parseInt(idStr);
                if (todoList.complete(id)) console.log("Todo completed!");
                else console.log("Todo not found.");
                showMenu();
            });
            break;
        case "4":
            rl.close();
            break;
        default:
            console.log("Invalid option.");
            showMenu();
    }
}

showMenu();
```

---

## **7. Add Scripts in `package.json`**

```json
"scripts": {
  "start": "ts-node src/index.ts",
  "build": "tsc"
}
```

---

## **8. Run the Project**

```bash
npm start
```

You now have a simple **TypeScript CLI Todo app**! 🎉

---

Ah, this is a common point of confusion! Let me break it down clearly.

---

### **Normal TypeScript Workflow**

1. You write `.ts` files (TypeScript source code).
2. TypeScript **cannot run directly in Node.js**, because Node only understands JavaScript.
3. Normally, you **compile TypeScript to JavaScript** first:

```bash
npx tsc
```

* This generates `.js` files in the `outDir` (e.g., `dist/`).
* Then you run Node on the compiled JS:

```bash
node dist/index.js
```

So the workflow is: **TypeScript → Compile → JavaScript → Run**.

---

### **What `ts-node` Does**

`ts-node` **combines these two steps**:

* You can **run `.ts` files directly**, without explicitly compiling them first.
* Behind the scenes, `ts-node` **compiles TypeScript in memory** and immediately executes the result with Node.js.

Example:

```bash
npx ts-node src/index.ts
```

* Node never sees the `.ts` file as a file on disk—it sees the compiled JS in memory.
* Saves time in small projects or scripts because you don’t have to run `tsc` every time.

---

### **Key Points**

* ✅ Great for development, quick testing, and small projects.
* ❌ Not ideal for production; in production, you usually **pre-compile TypeScript** to JS for performance.

---


