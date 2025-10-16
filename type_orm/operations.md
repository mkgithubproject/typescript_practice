Good question 👍

Let’s compare **TypeORM functions** like `findOne`, `update`, etc., with their **Mongoose equivalents** — so you can quickly translate between the two.

---

## 🧩 1. `findOne` (TypeORM → Mongoose)

### 🟦 TypeORM

```ts
const user = await userRepository.findOne({ where: { email: 'test@example.com' } });
```

### 🟩 Mongoose

```js
const user = await User.findOne({ email: 'test@example.com' });
```

✅ **Explanation:**

* In TypeORM, you pass `where: { ... }`
* In Mongoose, you directly pass the filter object

---

## 🧩 2. `find` (TypeORM → Mongoose)

### 🟦 TypeORM

```ts
const users = await userRepository.find({ where: { isActive: true } });
```

### 🟩 Mongoose

```js
const users = await User.find({ isActive: true });
```

✅ **Explanation:**

* Works the same — returns an array of documents/entities.

---

## 🧩 3. `findBy` or `findById` (TypeORM → Mongoose)

### 🟦 TypeORM

```ts
const user = await userRepository.findOneBy({ id: 1 });
```

### 🟩 Mongoose

```js
const user = await User.findById('670ccbb13f1f8f1b6e0e76f2');
```

✅ **Tip:** `findById` is shorthand for `findOne({ _id: id })`.

---

## 🧩 4. `save` (TypeORM → Mongoose)

### 🟦 TypeORM

```ts
const user = new User();
user.name = 'John';
await userRepository.save(user);
```

### 🟩 Mongoose

```js
const user = new User({ name: 'John' });
await user.save();
```

✅ **Explanation:**
Both create or update depending on whether `_id` exists.

---

## 🧩 5. `update` (TypeORM → Mongoose)

### 🟦 TypeORM

```ts
await userRepository.update({ id: 1 }, { name: 'Updated' });
```

### 🟩 Mongoose

```js
await User.updateOne({ _id: '670ccbb13f1f8f1b6e0e76f2' }, { name: 'Updated' });
```

✅ **Other Mongoose options:**

```js
await User.findByIdAndUpdate(id, { name: 'Updated' }, { new: true }); // returns updated doc
```

---

## 🧩 6. `delete` or `remove`

### 🟦 TypeORM

```ts
await userRepository.delete({ id: 1 });
```

### 🟩 Mongoose

```js
await User.deleteOne({ _id: id });
```

✅ **Other variants:**

```js
await User.findByIdAndDelete(id);
await User.deleteMany({ isActive: false });
```

---

## 🧩 7. `count`

### 🟦 TypeORM

```ts
const count = await userRepository.count({ where: { isActive: true } });
```

### 🟩 Mongoose

```js
const count = await User.countDocuments({ isActive: true });
```

---

## 🧩 8. `findAndUpdate` equivalent (no direct in TypeORM)

### 🟦 TypeORM

Usually done in two steps:

```ts
const user = await userRepository.findOneBy({ id });
user.name = 'Updated';
await userRepository.save(user);
```

### 🟩 Mongoose

One step:

```js
const updatedUser = await User.findOneAndUpdate(
  { _id: id },
  { name: 'Updated' },
  { new: true }
);
```

---

## 🧩 9. `findOrFail` (TypeORM) → custom check in Mongoose

### 🟦 TypeORM

```ts
const user = await userRepository.findOneOrFail({ where: { id: 1 } });
```

### 🟩 Mongoose

```js
const user = await User.findById(id);
if (!user) throw new Error('User not found');
```

---

## ✅ Quick Summary Table

| Operation  | TypeORM                                | Mongoose                                           |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Find One   | `findOne({ where: { field: value } })` | `findOne({ field: value })`                        |
| Find Many  | `find({ where: { ... } })`             | `find({ ... })`                                    |
| Find by ID | `findOneBy({ id })`                    | `findById(id)`                                     |
| Save       | `save(entity)`                         | `save()`                                           |
| Update     | `update(where, data)`                  | `updateOne(filter, data)` or `findByIdAndUpdate()` |
| Delete     | `delete(where)`                        | `deleteOne(filter)` or `findByIdAndDelete()`       |
| Count      | `count({ where: { ... } })`            | `countDocuments(filter)`                           |

---

Perfect 👏 — this is **one of the most powerful and advanced features** of TypeORM, and mastering the **QueryBuilder** will make you feel like a database magician 🪄.

Let’s go from **zero → expert**, step by step, with **clear explanations**, **rules**, and **examples**.

---

# 🧱 1. What is QueryBuilder?

### 🧩 Definition:

> `QueryBuilder` in TypeORM is a fluent, chainable API that lets you **build SQL queries dynamically** — while staying inside TypeScript/JavaScript.

Instead of writing raw SQL strings like this:

```sql
SELECT * FROM user WHERE name = 'John';
```

You can write:

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.name = :name', { name: 'John' })
  .getMany();
```

✅ **Result:** Same SQL, but with cleaner, safer, reusable code — and automatic parameter binding to prevent SQL injection.

---

# 🧩 2. Basic Syntax and Structure

```ts
dataSource
  .getRepository(Entity)
  .createQueryBuilder(alias)
  .<operation>()  // select, insert, update, delete
  .<conditions>()
  .<ordering>()
  .<pagination>()
  .getMany() | .getOne() | .execute()
```

Let’s decode that 👇

| Step                                     | Purpose                    | Example                      |
| ---------------------------------------- | -------------------------- | ---------------------------- |
| `.createQueryBuilder('user')`            | Creates builder with alias | `'user'` acts like SQL alias |
| `.select(['user.id', 'user.name'])`      | Select columns             |                              |
| `.where('user.age > :age', { age: 18 })` | Add condition              | Parameter binding            |
| `.orderBy('user.createdAt', 'DESC')`     | Add ordering               |                              |
| `.getMany()`                             | Execute query              | Returns array                |

---

# 🧠 3. QueryBuilder Return Types

| Method          | Returns                                                  |
| --------------- | -------------------------------------------------------- |
| `.getOne()`     | One entity                                               |
| `.getMany()`    | Array of entities                                        |
| `.getCount()`   | Count only                                               |
| `.getRawOne()`  | Raw DB row (no mapping to entity)                        |
| `.getRawMany()` | Array of raw rows                                        |
| `.execute()`    | Executes query (useful for `INSERT`, `UPDATE`, `DELETE`) |

---

# 🔍 4. SELECT Queries (Read operations)

### ✅ Example 1 — Simple SELECT

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.isActive = :isActive', { isActive: true })
  .getMany();
```

Equivalent SQL:

```sql
SELECT * FROM user WHERE isActive = true;
```

---

### ✅ Example 2 — Selecting specific columns

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .select(['user.id', 'user.name'])
  .getMany();
```

---

### ✅ Example 3 — Using multiple conditions

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.age > :age', { age: 18 })
  .andWhere('user.isActive = :isActive', { isActive: true })
  .getMany();
```

---

### ✅ Example 4 — Ordering and Pagination

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .orderBy('user.createdAt', 'DESC')
  .skip(10)   // offset
  .take(5)    // limit
  .getMany();
```

Equivalent SQL:

```sql
SELECT * FROM user ORDER BY createdAt DESC LIMIT 5 OFFSET 10;
```

---

# 🧩 5. INSERT Queries

```ts
const result = await dataSource
  .createQueryBuilder()
  .insert()
  .into(User)
  .values({ name: 'John', email: 'john@example.com' })
  .execute();
```

**Result:**

```js
{
  identifiers: [ { id: 1 } ],
  generatedMaps: [ { id: 1 } ],
  raw: [ ... ]
}
```

✅ Works in all DBs.

---

### PostgreSQL bonus — return inserted row:

```ts
const result = await dataSource
  .createQueryBuilder()
  .insert()
  .into(User)
  .values({ name: 'John', email: 'john@example.com' })
  .returning('*')
  .execute();

console.log(result.raw[0]);
```

---

# 🧩 6. UPDATE Queries

```ts
const result = await dataSource
  .createQueryBuilder()
  .update(User)
  .set({ name: 'Updated Name' })
  .where('id = :id', { id: 1 })
  .execute();
```

✅ Returns metadata only by default.

---

### PostgreSQL bonus:

```ts
const result = await dataSource
  .createQueryBuilder()
  .update(User)
  .set({ name: 'Updated Name' })
  .where('id = :id', { id: 1 })
  .returning('*')
  .execute();

console.log(result.raw[0]);
```

---

# 🧩 7. DELETE Queries

```ts
const result = await dataSource
  .createQueryBuilder()
  .delete()
  .from(User)
  .where('id = :id', { id: 5 })
  .execute();
```

Equivalent SQL:

```sql
DELETE FROM user WHERE id = 5;
```

---

# 🧩 8. Joins

### ✅ Inner Join

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .innerJoinAndSelect('user.profile', 'profile')
  .getMany();
```

Equivalent SQL:

```sql
SELECT * FROM user
INNER JOIN profile ON profile.userId = user.id;
```

---

### ✅ Left Join with condition

```ts
const users = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .leftJoinAndSelect('user.posts', 'post', 'post.isPublished = :status', { status: true })
  .getMany();
```

---

# 🧩 9. Aggregations and Grouping

```ts
const stats = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .select('user.role')
  .addSelect('COUNT(user.id)', 'count')
  .groupBy('user.role')
  .getRawMany();

console.log(stats);
```

Example output:

```js
[
  { role: 'admin', count: 5 },
  { role: 'user', count: 50 }
]
```

---

# 🧩 10. Subqueries

```ts
const qb = dataSource.getRepository(User).createQueryBuilder('user');

const subQuery = qb.subQuery()
  .select('COUNT(post.id)')
  .from(Post, 'post')
  .where('post.userId = user.id')
  .getQuery();

const users = await qb
  .addSelect(`(${subQuery})`, 'postCount')
  .getRawMany();
```

---

# 🧩 11. Parameters & Safety Rules

### ✅ Always use parameter binding:

```ts
.where('user.id = :id', { id: 1 });
```

❌ Never do this:

```ts
.where(`user.id = ${id}`);
```

➡️ That’s vulnerable to SQL injection.

---

# 🧩 12. Useful Rules to Remember

| Rule                                             | Explanation                              |
| ------------------------------------------------ | ---------------------------------------- |
| Always use parameter binding                     | Prevents SQL injection                   |
| Alias your tables                                | Helps avoid ambiguous columns            |
| Use `.getRawMany()` if you need SQL-like objects | Skips ORM mapping                        |
| Use `.getMany()` for entities                    | Maps results to classes                  |
| `.returning('*')` works only in PostgreSQL       | Not in MySQL or SQLite                   |
| `.execute()` returns metadata                    | Use `.getMany()` for data                |
| Chain methods logically                          | Select → Where → Order → Limit → Execute |

---

# ⚙️ 13. Example: Full CRUD with QueryBuilder

```ts
// CREATE
await dataSource.createQueryBuilder().insert().into(User)
  .values({ name: 'Alice' }).execute();

// READ
const users = await dataSource.getRepository(User)
  .createQueryBuilder('u').getMany();

// UPDATE
await dataSource.createQueryBuilder().update(User)
  .set({ name: 'Updated Alice' }).where('id = :id', { id: 1 }).execute();

// DELETE
await dataSource.createQueryBuilder().delete().from(User)
  .where('id = :id', { id: 1 }).execute();
```

---

# 🎯 14. Expert Tip: Combine ORM + Raw Power

You can freely mix **QueryBuilder** and **Repository** methods.
Example:

```ts
const userQb = dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.email = :email', { email: 'test@example.com' });

const user = await userQb.getOne();
```

Or even use `.getQuery()` to inspect generated SQL:

```ts
console.log(userQb.getQuery());
```

---

## 🚀 Summary

| Task      | Method                                | Example            |
| --------- | ------------------------------------- | ------------------ |
| Select    | `.select().where().getMany()`         | Read               |
| Insert    | `.insert().into().values().execute()` | Create             |
| Update    | `.update().set().where().execute()`   | Update             |
| Delete    | `.delete().from().where().execute()`  | Delete             |
| Join      | `.innerJoinAndSelect()`               | Combine tables     |
| Aggregate | `.groupBy().addSelect()`              | Count, sum, avg    |
| Subquery  | `.subQuery()`                         | Complex conditions |

---

Excellent 👏 — this is one of those **“aha!”** topics that truly makes QueryBuilder click once you understand it.

Let’s break it down **visually and practically** so you’ll never forget what `getRawOne()` (and `getRawMany()`) actually do.

---

# 🧩 The core idea

TypeORM has two “worlds”:

| Concept          | Description                                              |
| ---------------- | -------------------------------------------------------- |
| **Entity world** | Returns actual entity instances (mapped to your classes) |
| **Raw world**    | Returns plain database rows (just like SQL query output) |

---

# 🟦 Example Setup

Let’s say you have this entity:

```ts
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  email: string;
}
```

Now you run this:

```ts
const user = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.id = :id', { id: 1 })
  .getOne();
```

---

# ✅ 1️⃣ `getOne()` → returns **Entity object**

```js
User {
  id: 1,
  name: 'Alice',
  email: 'alice@example.com'
}
```

➡️ This is a **User instance** mapped by TypeORM to your entity class.
You can call methods on it, and TypeORM understands it’s a `User`.

---

# ❗2️⃣ `getRawOne()` → returns **Raw Database Row**

```ts
const rawUser = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.id = :id', { id: 1 })
  .getRawOne();

console.log(rawUser);
```

**Output:**

```js
{
  user_id: 1,
  user_name: 'Alice',
  user_email: 'alice@example.com'
}
```

🔍 Notice the difference:

* Keys have **aliases** like `user_id` instead of `id`.
* It’s just a **plain JavaScript object**, not an instance of your `User` class.
* You can’t call `user.save()` on it — it’s *not* a managed entity.

---

# 🧠 Why the difference?

✅ `getOne()`:

* Runs your query
* Converts the raw SQL row into a **User entity instance**
* Applies **relations**, **transformers**, and **decorators**

✅ `getRawOne()`:

* Runs the query
* Returns exactly what the **database** returned — no mapping, no transformation

---

# 🧩 3️⃣ Real-life Example — when you need `getRawOne()`

### Example: Aggregation or custom columns

```ts
const result = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .select('user.name', 'name')
  .addSelect('COUNT(order.id)', 'orderCount')
  .leftJoin('user.orders', 'order')
  .where('user.id = :id', { id: 1 })
  .groupBy('user.name')
  .getRawOne();

console.log(result);
```

**Output:**

```js
{
  name: 'Alice',
  orderCount: '5'
}
```

✅ Here, you can’t use `getOne()` because there’s **no entity field** for `orderCount`.
That’s why you use `getRawOne()` to get raw computed data.

---

# ⚖️ 4️⃣ Quick Comparison Table

| Method         | Returns                  | Example Output                       | Use When                        |
| -------------- | ------------------------ | ------------------------------------ | ------------------------------- |
| `getOne()`     | Entity instance (mapped) | `{ id: 1, name: 'Alice' }`           | You need a full entity          |
| `getMany()`    | Array of entities        | `[User, User, ...]`                  | Fetch multiple entities         |
| `getRawOne()`  | Raw DB row               | `{ user_id: 1, user_name: 'Alice' }` | Custom columns, computed values |
| `getRawMany()` | Array of raw rows        | `[ { user_id: 1 }, { user_id: 2 } ]` | Multiple raw results            |

---

# 🧩 5️⃣ Bonus: Mixed usage — `getRawAndEntities()`

Sometimes you want both:

* The **mapped entities**
* The **raw database rows**

```ts
const result = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .leftJoinAndSelect('user.profile', 'profile')
  .getRawAndEntities();

console.log(result.raw);      // raw SQL rows
console.log(result.entities); // mapped entity instances
```

✅ Very useful when debugging or working with complex joins.

---

# 🚀 Summary

| Concept     | `getOne()`              | `getRawOne()`                     |
| ----------- | ----------------------- | --------------------------------- |
| Returns     | Entity instance         | Raw DB object                     |
| Field names | Entity property names   | Aliased SQL column names          |
| Works with  | Normal entity queries   | Custom selects, aggregates, joins |
| Example use | `getOne()` after `find` | Count, sum, subquery results      |

---

### ✅ Quick Mental Model

> 🧠 **`getOne()` → ORM world**
> 🧠 **`getRawOne()` → SQL world**

---

Excellent catch 👏 — and this is one of those **subtle but important** QueryBuilder behaviors that trips up even experienced TypeORM developers.

Let’s unpack this step by step so you understand **exactly** why it happens and how to control it like an expert 🔍

---

## 🧩 1️⃣ Why do you see `user_` prefix in raw output?

Because **you defined an alias** (`'user'`) in your QueryBuilder:

```ts
const rawUser = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .where('user.id = :id', { id: 1 })
  .getRawOne();
```

👉 When TypeORM builds the query, it generates SQL like this:

```sql
SELECT "user"."id" AS "user_id",
       "user"."name" AS "user_name",
       "user"."email" AS "user_email"
FROM "user" "user"
WHERE "user"."id" = 1;
```

### 🔍 Then, in the raw output:

The column **aliases** (`AS "user_id"`, `AS "user_name"`) become your **object keys**:

```js
{
  user_id: 1,
  user_name: 'Alice',
  user_email: 'alice@example.com'
}
```

---

## 🧠 Why TypeORM does this:

To **avoid naming conflicts** when you use joins.

Example:

```ts
.createQueryBuilder('user')
.leftJoin('user.profile', 'profile')
```

Without prefixes, both tables could have a column called `id`, and your output would become ambiguous.
So TypeORM adds `user_` and `profile_` prefixes automatically to make the raw data unique.

---

## 🧩 2️⃣ How to get *custom key names* (without `user_`)

You can **manually set aliases** for columns using the second parameter in `.select()` or `.addSelect()`.

Example:

```ts
const rawUser = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .select('user.id', 'id')
  .addSelect('user.name', 'name')
  .addSelect('user.email', 'email')
  .where('user.id = :id', { id: 1 })
  .getRawOne();

console.log(rawUser);
```

✅ Output:

```js
{
  id: 1,
  name: 'Alice',
  email: 'alice@example.com'
}
```

Now you control the field names using the **alias** in the second argument.

---

## 🧩 3️⃣ With multiple tables (why alias prefix is useful)

Example:

```ts
const result = await dataSource
  .getRepository(User)
  .createQueryBuilder('user')
  .leftJoin('user.profile', 'profile')
  .select([
    'user.id AS user_id',
    'user.name AS user_name',
    'profile.id AS profile_id',
    'profile.bio AS profile_bio'
  ])
  .getRawOne();

console.log(result);
```

Output:

```js
{
  user_id: 1,
  user_name: 'Alice',
  profile_id: 10,
  profile_bio: 'Engineer'
}
```

✅ Here the prefixes help you distinguish columns from different tables easily.

---

## 🧩 4️⃣ Summary

| Behavior                                                         | Reason                             | Control Method                                      |
| ---------------------------------------------------------------- | ---------------------------------- | --------------------------------------------------- |
| Prefixes like `user_id`                                          | Alias added to avoid conflicts     | Set custom aliases via `.select('column', 'alias')` |
| Raw output matches SQL column aliases                            | SQL uses `AS` internally           | Use custom aliases or `getOne()` for entity mapping |
| Default aliasing only appears in `getRawOne()` or `getRawMany()` | Because they return plain SQL data |                                                     |

---

## 🧾 Quick Example Recap

### Default (automatic aliasing)

```ts
.select(['user.id', 'user.name'])
```

→ `{ user_id: 1, user_name: 'Alice' }`

### Custom alias (clean keys)

```ts
.select('user.id', 'id')
.addSelect('user.name', 'name')
```

→ `{ id: 1, name: 'Alice' }`

---

✅ **Rule of Thumb:**

> * `getOne()` → returns mapped entity with clean keys
> * `getRawOne()` → returns raw SQL result, keys = SQL aliases
> * You control key names via `.select('column', 'alias')`

---




