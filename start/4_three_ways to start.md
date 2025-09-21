Perfect! Let's go **step by step** and cover **all 3 ways** to watch and run TypeScript files in a Node.js project, including the **folder structure, scripts, and config files**. I’ll make it clear and ready-to-use.

---

## **Folder Structure Example**

```
ts-watch-example/
├─ src/
│  ├─ index.ts
│  └─ utils.ts
├─ dist/
├─ package.json
├─ tsconfig.json
└─ nodemon.json
```

* `src/` → TypeScript source files.
* `dist/` → Compiled JavaScript files (optional, if using `tsc`).
* `package.json` → Node scripts.
* `tsconfig.json` → TypeScript configuration.
* `nodemon.json` → Nodemon configuration.

---

## **1️⃣ Using TypeScript Built-in Watch Mode**

### **Step 1: Install TypeScript**

```bash
npm install --save-dev typescript
```

### **Step 2: Create `tsconfig.json`**

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src"]
}
```

### **Step 3: Add a script in `package.json`**

```json
"scripts": {
  "build": "tsc",
  "watch": "tsc --watch"
}
```

### **Step 4: Run watch**

```bash
npm run watch
```

✅ Now, every time you save a `.ts` file in `src/`, it will automatically **compile to `dist/`**.

---

## **2️⃣ Using `nodemon` + `ts-node`**

This allows you to **run the TypeScript files directly** without compiling manually.

### **Step 1: Install dependencies**

```bash
npm install --save-dev nodemon ts-node typescript
```

### **Step 2: Create `nodemon.json`**

```json
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts"],
  "exec": "ts-node ./src/index.ts"
}
```

* `watch` → directories to watch
* `ext` → file extensions to watch
* `exec` → command to run on file change

### **Step 3: Add script in `package.json`**

```json
"scripts": {
  "dev": "nodemon"
}
```

### **Step 4: Run**

```bash
npm run dev
```

✅ Now, your app **restarts automatically** whenever a `.ts` file changes.

---

## **3️⃣ Using `tsc-watch`**

`tsc-watch` allows running scripts after TypeScript compilation, useful for **Node apps using compiled JS**.

### **Step 1: Install `tsc-watch`**

```bash
npm install --save-dev tsc-watch
```

### **Step 2: Add script in `package.json`**

```json
"scripts": {
  "start:watch": "tsc-watch --onSuccess \"node ./dist/index.js\""
}
```

* `--onSuccess` → Runs command after successful compilation
* `dist/` → compiled JS files location

### **Step 3: Run**

```bash
npm run start:watch
```

✅ Every time you save a `.ts` file:

1. TypeScript compiles it to `dist/`.
2. Node automatically runs the new `index.js`.

---

## **Sample `src/index.ts`**

```ts
import { greet } from './utils';

console.log(greet('World'));
```

### **Sample `src/utils.ts`**

```ts
export function greet(name: string): string {
  return `Hello, ${name}!`;
}
```

---

### ✅ **Comparison**

| Method              | Compile? | Run? | Best Use Case                              |
| ------------------- | -------- | ---- | ------------------------------------------ |
| `tsc --watch`       | Yes      | No   | When you just want compiled JS files       |
| `nodemon + ts-node` | No       | Yes  | When you want to run TS directly in dev    |
| `tsc-watch`         | Yes      | Yes  | When you want compiled JS **and auto-run** |

---

If you want, I can make a **ready-to-clone project folder** with all three setups working at once, so you can just `npm install` and `npm run dev/watch/start:watch`.

Do you want me to do that?
