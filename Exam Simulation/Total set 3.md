I apologize sincerely. The standard Markdown line breaks were likely interpreted by your viewer as a single line.

I will now force **double line breaks** (blank lines) between every single option to guarantee they render on separate lines in Obsidian.

---

### Question 1: Switch Expressions & Control Flow

> [!info] Source
> 
> Chapter 2 - Controlling Program Flow / Lesson 3

Java

```
public class SwitchMagic {
    public static void main(String[] args) {
        int k = 10;
        var result = switch(k) {
            case 10 -> {
                System.out.print("A");
                yield 100;
            }
            default -> {
                System.out.print("B");
                return 0;
            }
        };
        System.out.print(result);
    }
}
```

**What is the result?**

**A.** A100

**B.** B0

**C.** Compilation Error: `return` not allowed.

**D.** Compilation Error: `yield` not allowed.

> [!success] Answer: C (Compilation Error: `return` not allowed)

> [!info] Deep Dive
> 
> In a Switch Expression (a switch that returns a value), you act within a specific scope to compute a result for that variable.
> 
> 1. **`yield` Statement:** This is the correct way to return a value from a block (`{...}`) inside a switch expression. It terminates the switch expression, not the method.
>     
> 2. **`return` Statement:** This attempts to terminate the **entire method** (`main`). However, because the switch expression must resolve to a value to be assigned to `result`, putting a `return` statement creates a conflict. The compiler expects a value for `result` in all branches (via `yield` or a direct arrow `-> value`). A `return` leaves `result` undefined.
>     
> 3. **Correction:** The `default` block should use `yield 0;` instead of `return 0;`.
>     

---

### Question 2: Parallel Stream Reduction & Ordering

> [!info] Source
> 
> Chapter 6 - Streams / Lesson 9

Java

```
import java.util.List;

public class OrderChaos {
    public static void main(String[] args) {
        List<Integer> data = List.of(1, 2, 3, 4, 5);
        int val = data.parallelStream()
            .reduce(0, (a, b) -> a - b);
        System.out.println(val);
    }
}
```

**What is the output?**

**A.** -15

**B.** 15

**C.** -5

**D.** The output is unpredictable.

> [!success] Answer: D (The output is unpredictable)

> [!info] Deep Dive
> 
> The reduce operation requires the accumulator function to be Associative.
> 
> 1. **Associativity Rule:** `(a op b) op c` must equal `a op (b op c)`.
>     
> 2. **Subtraction:** Subtraction is **NOT** associative. `(1 - 2) - 3 = -4`, but `1 - (2 - 3) = 2`.
>     
> 3. **Parallel Execution:** The framework splits the data into chunks. Thread A might compute `1-2`, Thread B might compute `3-4`. The order in which these partial results are combined is non-deterministic in parallel streams.
>     
> 4. **Result:** You might get `-15`, `-5`, or other values depending on how the Fork/Join pool splits the task.
>     

---

### Question 3: Records & Compact Constructors

> [!info] Source
> 
> Chapter 3 - Object-Oriented Approach / Lesson 5

Java

```
public record User(String name) {
    public User {
        if (name == null) throw new IllegalArgumentException();
        this.name = name.toUpperCase(); // Line X
    }
}
```

**What occurs when compiling this record?**

**A.** Compiles successfully.

**B.** Compilation Error at Line X: Cannot assign to final field.

**C.** Compilation Error at Line X: Name clash.

**D.** Runtime Exception.

> [!success] Answer: B (Compilation Error at Line X)

> [!info] Deep Dive
> 
> This is a strict rule regarding Compact Constructors in Records.
> 
> 1. **Implicit Assignment:** The compact constructor (which has no parameters `()`) runs **before** the implicit assignment of fields happens.
>     
> 2. **The Error:** By writing `this.name = ...`, you are trying to assign the final field manually. However, Java will automatically assign the field _after_ your constructor block finishes. This would result in a "double assignment" of a final variable, which is illegal.
>     
> 3. **The Fix:** Modify the parameter directly: `name = name.toUpperCase();`. Java will then use this modified parameter to assign the field automatically.
>     

---

### Question 4: Module Service Directives

> [!info] Source
> 
> Chapter 7 - Modules / Lesson 4

Java

```
module com.provider {
    provides com.api.Service with com.impl.ServiceImpl;
    // Line X
}
```

**Which directive is required at Line X to allow consumers to use the `ServiceImpl` class via Reflection?**

**A.** `exports com.impl;`

**B.** `opens com.impl;`

**C.** `requires transitive com.impl;`

**D.** `uses com.impl.ServiceImpl;`

> [!success] Answer: B (`opens com.impl;`)

> [!info] Deep Dive
> 
> 1. **`exports`:** Makes the public API of a package visible to other modules for compile-time and run-time use. It does **not** allow "Deep Reflection" (accessing private members via `setAccessible(true)`).
>     
> 2. **`opens`:** This is the specific directive for **Reflection**. It allows other modules (like Spring or Hibernate) to reflectively access classes in the package, including private members, even if the package is not exported for general use.
>     
> 3. **`provides`:** Declares that this module offers a service implementation, but doesn't open it for reflection.
>     

---

### Question 5: NIO.2 Path Relativization

> [!info] Source
> 
> Chapter 9 - I/O / Lesson Appendix

Java

```
import java.nio.file.*;

public class PathTest {
    public static void main(String[] args) {
        Path p1 = Path.of("/a/b");
        Path p2 = Path.of("/a/b/c/d");
        System.out.println(p1.relativize(p2));
    }
}
```

**What is the output?**

**A.** `../..`

**B.** `c/d`

**C.** `/c/d`

**D.** `d/c`

> [!success] Answer: B (`c/d`)

> [!info] Deep Dive
> 
> relativize(p2) answers the question: "How do I get to p2 from p1?"
> 
> 1. **Common Root:** Both start at `/a/b`.
>     
> 2. **The Difference:** To go from `/a/b` to `/a/b/c/d`, you simply need to go down into `c` and then `d`.
>     
> 3. **Result:** `c/d`.
>     
> 4. _Note:_ If the question was `p2.relativize(p1)`, the answer would be `../..` (go up two levels).
>     

---

### Question 6: JDBC ResultSet Type Scroll Sensitivity

> [!info] Source
> 
> Chapter 10 - JDBC / Lesson Appendix

Java

```
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_SENSITIVE,
    ResultSet.CONCUR_UPDATABLE);
ResultSet rs = stmt.executeQuery("SELECT * FROM Inventory");
rs.next();
// External update happens here modifying the current row in DB
System.out.println(rs.getString("qty"));
```

**If an external process modifies the 'qty' of the current row while the ResultSet is open, what happens?**

**A.** The new value is printed.

**B.** The old value is printed.

**C.** A `SQLException` is thrown.

**D.** The behavior is undefined.

> [!success] Answer: A (The new value is printed)

> [!info] Deep Dive
> 
> The key lies in ResultSet.TYPE_SCROLL_SENSITIVE.
> 
> 1. **`INSENSITIVE`:** The ResultSet takes a snapshot of the data. Changes made by others are **not** visible.
>     
> 2. **`SENSITIVE`:** The ResultSet maintains a live link (or refreshes frequently). Changes made to the database by other transactions **are visible** to the current ResultSet cursor.
>     
> 3. Since it is `SENSITIVE`, Java fetches the latest data, reflecting the external update.
>     

---

### Question 7: Map.merge Functionality

> [!info] Source
> 
> Chapter 5 - Collections / Lesson 9

Java

```
import java.util.*;
public class MergeMap {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("msg", "Hello");
        map.merge("msg", "World", (oldVal, newVal) -> null);
        System.out.println(map.get("msg"));
    }
}
```

**What is the output?**

**A.** `Hello`

**B.** `World`

**C.** `HelloWorld`

**D.** `null`

> [!success] Answer: D (`null`)

> [!info] Deep Dive
> 
> The merge method has specific logic for removal:
> 
> 1. It checks if the key exists. Here, "msg" exists with "Hello".
>     
> 2. It applies the remapping function: `(old, new) -> null`.
>     
> 3. **Crucial Rule:** If the remapping function returns `null`, the `merge` method **removes** the key from the map entirely.
>     
> 4. `map.get("msg")` returns `null` because the key is no longer in the map.
>     

---

### Question 8: Atomic Operations & Race Conditions

> [!info] Source
> 
> Chapter 8 - Concurrency / Lesson 10

Java

```
AtomicInteger count = new AtomicInteger(0);
// In concurrent threads:
if (count.get() < 10) {
    count.incrementAndGet();
}
```

**Is this code thread-safe?**

**A.** Yes, because `AtomicInteger` methods are atomic.

**B.** No, because there is a "Check-Then-Act" race condition.

**C.** Yes, because `get()` creates a memory barrier.

**D.** No, because `incrementAndGet` is not synchronized.

> [!success] Answer: B (No, "Check-Then-Act" race condition)

> [!info] Deep Dive
> 
> Although count.get() and count.incrementAndGet() are individually atomic, the block of logic is not.
> 
> 1. **Thread A** reads `count` as 9 (Logic: 9 < 10 is True).
>     
> 2. **Context Switch** happens.
>     
> 3. **Thread B** reads `count` as 9 (Logic: 9 < 10 is True).
>     
> 4. **Thread B** increments `count` to 10.
>     
> 5. **Thread A** resumes. It already decided to enter the block. It increments `count` to 11.
>     
> 6. **Result:** The limit of 10 was breached. To fix this, you would need a loop with `compareAndSet`.
>     

---

### Question 9: Generics & Array Creation

> [!info] Source
> 
> Lesson 7 - Generics

Java

```
public class GenericArray<T> {
    public void create() {
        T[] array = new T; // Line X
    }
}
```

**What happens at Line X?**

**A.** Compiles successfully.

**B.** Compilation Error: Generic array creation.

**C.** Runtime ClassCastException.

**D.** Creates an array of Objects.

> [!success] Answer: B (Compilation Error: Generic array creation)

> [!info] Deep Dive
> 
> Java strictly forbids creating arrays of a generic type parameter (new T[]).
> 
> 1. **Type Erasure:** At runtime, `T` is erased to `Object`.
>     
> 2. **Heap Pollution:** If Java allowed `new T`, it would effectively create `new Object`. If you passed this array to code expecting `String[]`, it would crash at runtime only when accessed, breaking the type-safety guarantee of Generics.
>     
> 3. **Workaround:** You must create `new Object` and cast it `(T[])`, or pass a `Class<T>` literal to perform reflection-based creation (`Array.newInstance`).
>     

---

### Question 10: try-with-resources & Suppression

> [!info] Source
> 
> Chapter 4 - Exceptions / Lesson 6

Java

```
class Door implements AutoCloseable {
    public void close() { throw new RuntimeException("Shell"); }
}
public class House {
    public static void main(String[] args) {
        try (Door d = new Door()) {
            throw new RuntimeException("Room");
        } catch (Exception e) {
            System.out.print(e.getMessage() + " " + e.getSuppressed().getMessage());
        }
    }
}
```

**What is the output?**

**A.** `Shell Room`

**B.** `Room Shell`

**C.** `Room`

**D.** `Shell`

> [!success] Answer: B (`Room Shell`)

> [!info] Deep Dive
> 
> 1. **Primary Exception:** The logic inside the `try` block throws "Room". This is the main exception.
>     
> 2. **Automatic Closing:** Java attempts to close the resource `Door`.
>     
> 3. **Secondary Exception:** The `close()` method throws "Shell".
>     
> 4. **Suppression:** Since an exception ("Room") is already flying, Java **suppresses** the "Shell" exception and attaches it to the "Room" exception.
>     
> 5. **Output:** `e.getMessage()` is "Room". `e.getSuppressed().getMessage()` is "Shell".
>     

---

### Question 11: DateTimeFormatter & Literals

> [!info] Source
> 
> Chapter 1 - Date/Time / Lesson Appendix

Java

```
LocalDateTime dt = LocalDateTime.of(2022, 10, 20, 15, 30);
var f = DateTimeFormatter.ofPattern("MMMM' at 'h' o''clock'");
System.out.println(f.format(dt));
```

**What is the output?**

**A.** October at 3 o'clock

**B.** October at 3 o''clock

**C.** October at 3 o clock

**D.** Runtime Exception: Invalid pattern.

> [!success] Answer: A (October at 3 o'clock)

> [!info] Deep Dive
> 
> In DateTimeFormatter patterns:
> 
> 1. **Single Quotes (`'`):** Used to escape text. `' at '` outputs the literal string " at ".
>     
> 2. **Double Single Quote (`''`):** This is the escape sequence for a single quote itself. `o''clock` results in the literal text "o'clock".
>     
> 3. **Result:** "MMMM" (Full Month) + " at " + "h" (12-hour format) + " o'clock" = October at 3 o'clock.
>     

---

### Question 12: Stream API - groupingBy & Mapping

> [!info] Source
> 
> Chapter 6 - Streams / Lesson 9

Java

```
Stream<String> s = Stream.of("Apple", "Banana", "Apricot");
var map = s.collect(Collectors.groupingBy(
    str -> str.charAt(0),
    Collectors.mapping(String::toUpperCase, Collectors.toList())
));
System.out.println(map);
```

**What is the content of the map?**

**A.** `{A=[Apple, Apricot], B=[Banana]}`

**B.** `{A=[APPLE, APRICOT], B=[BANANA]}`

**C.** `{65=[APPLE, APRICOT], 66=[BANANA]}`

**D.** Compilation Error.

> [!success] Answer: B (`{A=[APPLE, APRICOT], B=[BANANA]}`)

> [!info] Deep Dive
> 
> This uses a Downstream Collector.
> 
> 1. **First Argument (Classifier):** Groups by the first char (`'A'`, `'B'`).
>     
> 2. **Second Argument (Downstream):** `mapping` applies a transformation (`toUpperCase`) _before_ collecting into a List.
>     
> 3. **Flow:** "Apple" -> Group 'A' -> Transform to "APPLE" -> Add to List.
>     
> 4. **Result:** The keys are Characters, and values are Lists of uppercase Strings.
>     

---

### Question 13: Local Variable Type Inference (var)

> [!info] Source
> 
> Chapter 1 - Variables / Lesson 2

Java

```
public class VarCheck {
    // var x = 10; // Line 1
    public void test() {
        var a = 10;      // Line 2
        var b = (String) null; // Line 3
        var c = {1, 2};  // Line 4
    }
}
```

**Which lines cause a compilation error?**

**A.** Line 1 and Line 4

**B.** Line 3 and Line 4

**C.** Line 1 and Line 3

**D.** All lines.

> [!success] Answer: A (Line 1 and Line 4)

> [!info] Deep Dive
> 
> 1. **Line 1 (`var x = 10`):** **Error.** `var` is not allowed for instance variables (fields). It is only for local variables.
>     
> 2. **Line 2 (`var a = 10`):** **Valid.** Infers `int`.
>     
> 3. **Line 3 (`var b = (String) null`):** **Valid.** `null` alone is illegal, but casting it provides a type (`String`), so inference works.
>     
> 4. **Line 4 (`var c = {1, 2}`):** **Error.** Array Initializers (`{}`) require an explicit type on the left or `new int[]{}` on the right. Inference cannot guess the array type from standalone brackets.
>     

---

### Question 14: Serialization & Inheritance

> [!info] Source
> 
> Chapter 9 - I/O / Lesson Appendix

Java

```
class Person {
    String name = "Unknown";
    Person() { name = "Default"; } // No-arg constructor
}
class Employee extends Person implements Serializable {
    String role;
    Employee(String r) { role = r; }
}
// Assume serialization and deserialization occurs here
```

**What is the value of `name` after `Employee` is deserialized?**

**A.** `Unknown`

**B.** `Default`

**C.** `null`

**D.** The value it had during serialization.

> [!success] Answer: B (`Default`)

> [!info] Deep Dive
> 
> When deserializing an object:
> 
> 1. **Serializable Class (`Employee`):** Its constructor is **skipped**. Fields are populated from the byte stream.
>     
> 2. **Non-Serializable Parent (`Person`):** Java **MUST** run the no-argument constructor of the first non-serializable parent to initialize inherited fields.
>     
> 3. **Execution:** `Person()` constructor runs, setting `name = "Default"`.
>     
> 4. _Note:_ If `Person` did not have a no-arg constructor, serialization would fail at runtime with `InvalidClassException`.
>     

---

### Question 15: Pattern Matching Scope

> [!info] Source
> 
> Chapter 2 - Flow Control / Lesson 3

Java

```
Object o = 5;
if (!(o instanceof Integer i)) {
    return;
}
System.out.println(i);
```

**What happens?**

**A.** Prints 5.

**B.** Compilation Error: `i` not in scope.

**C.** Runtime Error.

**D.** Prints `null`.

> [!success] Answer: A (Prints 5)

> [!info] Deep Dive
> 
> This is Flow Scoping in Pattern Matching (Java 16+).
> 
> 1. **Logic:** The condition is inverted `!`.
>     
> 2. **If Block:** If `o` is **NOT** an Integer, we enter the block and `return`.
>     
> 3. **After the If:** The compiler knows that if execution reaches `System.out.println(i)`, the condition `!(o instanceof Integer)` must have been false. Therefore, `o instanceof Integer` must be **true**.
>     
> 4. **Scope:** Because the compiler guarantees `i` is initialized in this path, `i` is legally in scope and usable.
>