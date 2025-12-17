# 🍋 Java OCP High-Yield Exam Cheat Sheet (The "Zatona")
**Tags:** #Java #OCP #CheatSheet #ExamPrep

---

## 1. 🚨 The "It Looks Right, But It's Wrong" List
*(Compilation Errors - Syntax Traps)*

> [!FAILURE] Trap: Uninitialized `var`
> **If you see:** `var x = null;` or `var x;` inside a method.
> **It Fails Because:** Local Variable Type Inference (LVTI) requires an initializer on the same line to infer the type. `null` has no type, and lack of initialization gives the compiler zero information.
> *Source: Textbook Ch 1*

> [!FAILURE] Trap: Generic Invariance
> **If you see:** `List<Number> list = new ArrayList<Integer>();`
> **It Fails Because:** Generics are **NOT covariant**. A `List<Integer>` is *not* a child of `List<Number>`. You must use wildcards: `List<? extends Number>`.
> *Source: Slide 309*

> [!FAILURE] Trap: Short/Byte Math Promotion
> **If you see:** `short x = 10; short y = 3; short z = x + y;`
> **It Fails Because:** **Type Promotion**. In Java, binary operations on anything smaller than an `int` (byte, short, char) automatically promote operands to `int`. The result is an `int`, which cannot fit into `short` without a cast.
> *Source: Slide 89*

> [!FAILURE] Trap: Switch Expression without Yield
> **If you see:** A switch expression using `{ ... }` without `yield`.
> **It Fails Because:** If you use a code block inside a switch expression (Java 14+), you **MUST** use `yield` to return the value, not `return` (which exits the method).
> *Source: Textbook Ch 2*

> [!FAILURE] Trap: Private Interface Methods Access
> **If you see:** `interface A { private void method(); }` being called by `class B implements A`.
> **It Fails Because:** Private Interface Methods (Java 9) are strictly for internal logic reuse **inside the interface file**. They are invisible to implementing classes.
> *Source: Slide 199-204*

> [!FAILURE] Trap: Lambda & Effectively Final
> **If you see:** `localVariable` defined in a method being accessed inside a Lambda (`x -> x + localVariable`) where `localVariable` is modified later.
> **It Fails Because:** Variables used in Lambdas must be **Effectively Final**. If you change it anywhere in the method, the Lambda rejects it.
> *Source: Slide 232*

> [!FAILURE] Trap: Multi-Catch Redundancy
> **If you see:** `catch (FileNotFoundException | IOException e)`
> **It Fails Because:** **Redundancy**. You cannot catch a Child and a Parent in the same pipe (`|`). `IOException` already covers `FileNotFoundException`.
> *Source: Slide 281 / Textbook Ch 4*

---

## 2. 💣 The Runtime Trap Detector
*(Code compiles but blows up at runtime)*

> [!DANGER] The "Autoboxing" NPE
> **Scenario:** `Integer x = null; int y = x;`
> **The Bomb:** Throws `NullPointerException`. Java tries to call `x.intValue()` automatically. If `x` is null, it crashes.
> *Source: Slide 230 / Textbook Ch 1*

> [!DANGER] The "Unsupported Operation" Trap
> **Scenario:** `List<String> list = Arrays.asList("A", "B"); list.add("C");`
> **The Bomb:** Throws `UnsupportedOperationException`. `Arrays.asList` (and `List.of`) creates a **Fixed-Size List**. You can change elements (`set`), but you cannot add/remove.
> *Source: Textbook Ch 5 / Slide 97*

> [!DANGER] The "Concurrent Modification" Crash
> **Scenario:** Removing from a List inside a for-each loop.
> **The Bomb:** Throws `ConcurrentModificationException`. You must use `Iterator.remove()` or `Collection.removeIf()`.
> *Source: Slide 430*

> [!DANGER] The "Stream Reuse" Error
> **Scenario:** Calling a terminal operation on a Stream, then trying to call another method on the same Stream variable.
> **The Bomb:** Throws `IllegalStateException`: stream has already been operated upon or closed. Streams are **single-use**.
> *Source: Slide 355*

> [!DANGER] The "Cast" Trap
> **Scenario:** `Object o = "String"; Integer i = (Integer) o;`
> **The Bomb:** Throws `ClassCastException`. Even with explicit casting, the object in the Heap must actually be that type (or a subclass).
> *Source: Slide 305*

---

## 3. 🧠 "Rule of Thumb" & Mnemonics
*(Golden Rules for Speed)*

> [!TIP] PECS Rule (Generics)
> **P**roducer **E**xtends, **C**onsumer **S**uper.
> * Reading data? Use `? extends T`.
> * Writing data? Use `? super T`.
> *Source: Slide 309*

> [!TIP] Overriding vs. Overloading
> * **Overloading:** Same method name, different parameters. Return type/Exceptions don't matter.
> * **Overriding:** Same signature. Access must be broader/same. Return type must be covariant (same/subclass). Exceptions must be narrower/same (or none).
> *Source: Slide 263 / Textbook Ch 3*

> [!TIP] The "SAM" Rule
> A Functional Interface must have **S**ingle **A**bstract **M**ethod.
> *(Object methods like `toString` do NOT count).*
> *Source: Slide 215*

> [!TIP] Initialization Order
> Static Blocks (once) -> Instance Blocks -> Constructor.
> *Source: Slide 189*

> [!TIP] Stream Flow
> * **Intermediate Operations:** Lazy (Wait for terminal).
> * **Terminal Operations:** Eager (Run immediately).
> *Source: Slide 357*

> [!TIP] Comparable vs Comparator
> * **Comparable:** "I compare myself" (`compareTo` inside the class).
> * **Comparator:** "I judge two others" (`compare` external class/lambda).
> *Source: Textbook Ch 5*

---

## 4. 🔍 The "Fine Print" Details
*(Obscure facts explicitly mentioned in source)*

> [!NOTE] The "Object" Loophole in Interfaces
> If an interface declares an abstract method that matches a public method from `java.lang.Object` (like `toString` or `equals`), it does **NOT** count towards the limit for Functional Interfaces. The interface remains functional.
> *Source: Slide 215*

> [!NOTE] Console Passwords
> `System.console().readPassword()` returns a `char[]`, **NOT** a `String`. This is for security (so you can wipe the array from memory immediately, whereas Strings linger in the pool).
> *Source: Textbook Ch 9 / Slide 993*

> [!NOTE] JDBC Cursor Position
> When a ResultSet is first created, the cursor points **before the first row**. You must call `rs.next()` at least once to get data. Failing to do so throws `SQLException`.
> *Source: Textbook Ch 10 / Slide 1030*

> [!NOTE] Effectively Final in Inner Classes
> Local Inner Classes can access local variables only if they are **final or effectively final**.
> **Reason:** The variable lives on the Stack, but the object lives on the Heap. Java copies the value, so it forbids changes to ensure consistency.
> *Source: Slide 242*

> [!NOTE] Module Directives
> * `requires transitive`: If Module A requires transitive B, and Module C requires A, then C automatically can read B.
> * `opens`: Allows access via **Reflection only**.
> *Source: Textbook Ch 7*
