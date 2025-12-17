

### Question 1: Inner Classes & Variable Capture

**Topic:** Inner Classes (Lesson 5 / Textbook Ch 3)

```java
public class Outer {
    private int x = 24;

    public int getX() {
        String message = "x is ";
        class Inner {
            private int x = 55;
            public void printX() {
                System.out.println(message + x);
            }
        }
        message = "X is "; // Line A
        var in = new Inner();
        in.printX();
        return x;
    }

    public static void main(String[] args) {
        new Outer().getX();
    }
}
```

**What is the result?** 
A. x is 55 
B. X is 55
C. x is 24 
D. Compilation error at Line A.

**Correct Answer: D**

- **Concept:** Local Inner Classes & "Effectively Final" Variables.
- **Deep Explanation:** Local inner classes (classes defined inside a method) can access local variables of the enclosing method only if those variables are **final** or **effectively final**. "Effectively final" means the variable is assigned a value once and never modified.
- **Why it's Right:** Inside the `Inner` class, the code references the local variable `message`. However, at **Line A**, `message` is reassigned (`message = "X is ";`). This reassignment breaks the "effectively final" status of the variable. Therefore, the compiler throws an error at the point where `message` is referenced inside the inner class (or at the assignment, depending on the compiler), stating that variables accessed from an inner class must be final or effectively final.
- **Why Others are Wrong:** Options A, B, and C assume the code compiles. It does not because the local variable `message` is modified after initialization, violating the capture rule for local inner classes.

---

### Question 2: Stream Collectors (Partitioning vs Grouping)

**Topic:** Streams (Lesson 8 / Textbook Ch 6)

```java
import java.util.*;
import java.util.stream.*;

public class StreamPartition {
    public static void main(String[] args) {
        Stream<String> s = Stream.of("Speak", "Bark", "Meow", "Growl");
        Map<Boolean, List<String>> map = s.collect(
            Collectors.partitioningBy(b -> b.length() > 4)
        );
        System.out.println(map.get("true")); // Line X
    }
}
```

**What is the result?**
A. Speak, Growl
B. Bark, Meow
C. null 
D. Compilation error at Line X.

**Correct Answer: C**

- **Concept:** `Collectors.partitioningBy` Return Type.
- **Deep Explanation:** The `partitioningBy` collector always returns a `Map<Boolean, List<T>>`. The keys in this map are strictly of type `Boolean` (`true` and `false`).
- **Why it's Right:** On **Line X**, the code calls `map.get("true")`. The argument `"true"` is a **String**. While `Map.get(Object key)` accepts any object type, the map contains `Boolean` keys, not `String` keys. Therefore, looking up the _String_ `"true"` will not match the _Boolean_ `true`. The map returns `null`.
- **Why Others are Wrong:**
    - A would be correct if `map.get(true)` (boolean) was called.
    - D is incorrect because `Map.get` accepts `Object`, so passing a String is syntactically legal, just logically incorrect for this specific map.

---

### Question 3: Concurrency & Atomic Operations

**Topic:** Multi-Threading (Lesson 10 / Textbook Ch 8)

```java
import java.util.concurrent.atomic.*;

public class AtomicTest {
    private static AtomicInteger count = new AtomicInteger(0);

    public static void main(String[] args) {
        Runnable r = () -> {
            count.getAndIncrement(); // Op 1
            int c = count.get();     // Op 2
            System.out.print(c + " ");
        };
        new Thread(r).start();
        new Thread(r).start();
    }
}
```

**Which statement is true regarding the output?** A. It will always be "1 2 ". B. It will always be "1 1 ". C. It could be "1 2 ", "2 1 ", or "2 2 ". D. The code causes a deadlock.

**Correct Answer: C**

- **Concept:** Thread Safety & Atomicity.
- **Deep Explanation:** While `AtomicInteger` methods like `getAndIncrement()` are atomic individually, the **sequence of operations** inside the `run()` method is NOT atomic.
    - Thread A could execute `getAndIncrement()` (count becomes 1).
    - Thread B could execute `getAndIncrement()` (count becomes 2).
    - Thread A resumes and executes `count.get()`, reading 2.
    - Thread B executes `count.get()`, reading 2.
    - Output: "2 2 ".
    - Other interleavings allow "1 2 " or "2 1 ".
- **Why it's Right:** There is a race condition between `Op 1` and `Op 2`. Reading the value after incrementing it, without synchronization block, allows other threads to intervene.
- **Why Others are Wrong:** A assumes sequential execution. D is incorrect as there are no locks causing a cyclic wait.

---

### Question 4: Exceptions & Overriding

**Topic:** Exception Handling (Lesson 6 / Textbook Ch 4)

```
import java.io.*;

class Parent {
    public void read() throws IOException {
        throw new IOException();
    }
}

class Child extends Parent {
    // Insert method here
}
```

**Which method, if inserted into `Child`, will cause a compilation error?** A. `public void read() throws FileNotFoundException {}` B. `public void read() throws Exception {}` C. `public void read() throws RuntimeException {}` D. `public void read() {}`

**Correct Answer: B**

- **Concept:** Overriding Methods & Exception Rules.
- **Deep Explanation:** When overriding a method, the subclass method **cannot** throw a checked exception that is new or broader than the checked exception declared in the parent class.
    - Parent throws `IOException`.
    - `Exception` is a **superclass** (broader) of `IOException`. This is forbidden.
- **Why it's Right:** Option B declares `throws Exception`. Since `Exception` is checked and broader than `IOException`, it violates the overriding rule.
- **Why Others are Wrong:**
    - A is valid because `FileNotFoundException` is a _subclass_ of `IOException`.
    - C is valid because `RuntimeException` is unchecked and can be thrown by any method regardless of overrides.
    - D is valid because an overriding method can choose to throw _no_ exceptions.

---

### Question 5: NIO.2 Path Relativize

**Topic:** Java I/O API (Lesson Appendix 1 / Textbook Ch 9)

```
import java.nio.file.*;

public class PathMath {
    public static void main(String[] args) {
        Path p1 = Paths.get("/home/user/music");
        Path p2 = Paths.get("docs/resume.txt");

        try {
            System.out.println(p1.relativize(p2));
        } catch (IllegalArgumentException e) {
            System.out.println("Error");
        }
    }
}
```

**What is the result?** A. `../docs/resume.txt` B. `../../docs/resume.txt` C. Error D. The code does not compile.

**Correct Answer: C**

- **Concept:** Path Relativization Constraints.
- **Deep Explanation:** The `relativize()` method constructs a path from one location to another. However, **both paths must be of the same type** (both absolute or both relative).
    - `p1` starts with `/`, so it is **absolute**.
    - `p2` does not start with `/`, so it is **relative**.
- **Why it's Right:** Attempting to relativize a relative path against an absolute path (or vice versa) throws an `IllegalArgumentException` at runtime. The `catch` block catches this and prints "Error".
- **Why Others are Wrong:** A and B assume the method works; D is incorrect because the code is syntactically valid.

---

### Question 6: Generics & Lower Bounds

**Topic:** Generics (Lesson 7 / Textbook Ch 5)

```
import java.util.*;

public class GenericBound {
    public static void addNumbers(List<? super Integer> list) {
        list.add(10);
        list.add(new Integer(20)); // Deprecated but valid syntax
        list.add(new Object());    // Line X
    }
}
```

**What happens when compiling Line X?** A. Compiles successfully. B. Compilation error. C. Compiles but throws ClassCastException at runtime. D. Compiles only if the list passed is `List<Object>`.

**Correct Answer: B**

- **Concept:** Lower Bounded Wildcards (`? super T`).
- **Deep Explanation:** `List<? super Integer>` means "A list of Integer or any of its superclasses (Number, Object)".
    - You **can** add `Integer` (or subclasses of Integer) because the list is guaranteed to be capable of holding Integers.
    - You **cannot** add `Object` because the actual list instance might be a `List<Integer>` or `List<Number>`. An `Object` is not necessarily an `Integer`.
- **Why it's Right:** The compiler ensures type safety. Adding an `Object` to what might be a `List<Integer>` would violate type safety. Therefore, Line X generates a compilation error.
- **Why Others are Wrong:** A is wrong because `add` with lower bounds only accepts the lower bound type (`Integer`) or its subclasses.

---

### Question 7: JDBC ResultSet Cursor

**Topic:** JDBC (Lesson Appendix 1 / Textbook Ch 10)

```
try (var conn = DriverManager.getConnection("jdbc:derby:zoo");
     var stmt = conn.createStatement();
     var rs = stmt.executeQuery("SELECT count(*) FROM animals")) { // Returns 1 row

    System.out.println(rs.getInt(1)); // Line X
}
```

**What is the result assuming the table exists?** A. Prints the count. B. Prints 0. C. Throws SQLException at runtime. D. Compilation error.

**Correct Answer: C**

- **Concept:** ResultSet Cursor Position.
- **Deep Explanation:** When a `ResultSet` is returned, the cursor is positioned **before the first row**. You must call `rs.next()` at least once to move the cursor to the first row of data.
- **Why it's Right:** Attempting to access data (`rs.getInt()`) before calling `next()` results in a `SQLException` with a message indicating an "Invalid cursor state" or similar.
- **Why Others are Wrong:** A and B assume the cursor is automatically on the first row, which is false in JDBC.

---

### Question 8: Modules & Transitive Dependencies

**Topic:** Modules (Lesson 7 / Textbook Ch 7)

**File: module-info.java (Module A)**

```
module A {
    exports com.a;
}
```

**File: module-info.java (Module B)**

```
module B {
    requires transitive A;
}
```

**File: module-info.java (Module C)**

```
module C {
    requires B;
}
```

**Which statement is true regarding Module C?** A. Module C can access packages in Module A. B. Module C cannot access packages in Module A without explicitly requiring it. C. Module C creates a cyclic dependency. D. Module B fails to compile.

**Correct Answer: A**

- **Concept:** Implied Readability (`requires transitive`).
- **Deep Explanation:** When Module B `requires transitive A`, any module that reads B (like Module C) **automatically reads A** as well.
- **Why it's Right:** Because of the `transitive` keyword in B, Module C has "implied readability" to Module A, meaning it can access exported packages from A without adding `requires A` in its own descriptor.
- **Why Others are Wrong:** B is incorrect because `transitive` solves exactly this problem. C is incorrect as there is no loop (C->B->A).

---

### Question 9: Functional Interfaces & Ambiguity

**Topic:** Lambda Expressions (Lesson 8 / Textbook Ch 6)

```
import java.util.concurrent.*;

public class Ambiguity {
    public static void execute(Runnable r) { System.out.print("Run"); }
    public static void execute(Callable<String> c) { System.out.print("Call"); }

    public static void main(String[] args) {
        execute(() -> { return "Done"; }); // Line X
    }
}
```

**What is the result?** A. Run B. Call C. Compilation error due to ambiguity. D. Runtime Exception.

**Correct Answer: B**

- **Concept:** Functional Interface Matching.
- **Deep Explanation:** The compiler looks at the lambda body to determine which interface matches.
    - `Runnable`'s method is `void run()`. It returns nothing.
    - `Callable`'s method is `V call()`. It returns a value.
- **Why it's Right:** The lambda `{ return "Done"; }` returns a String. This is **incompatible** with `Runnable` (void) but **compatible** with `Callable<String>`. Therefore, the compiler selects the `Callable` overload, printing "Call".
- **Why Others are Wrong:** A is incorrect because the lambda returns a value. C is incorrect because the return value disambiguates the call.

---

### Question 10: Localization & ResourceBundle Fallback

**Topic:** Localization (Lesson 11 / Textbook Ch 11)

**Files:**

1. `Menu.properties`
2. `Menu_en.properties`
3. `Menu_fr_CA.properties`

```
Locale.setDefault(new Locale("en", "US"));
var rb = ResourceBundle.getBundle("Menu", new Locale("fr", "FR"));
```

**Which file is loaded?** A. `Menu_fr_CA.properties` B. `Menu_en.properties` C. `Menu.properties` D. Throws MissingResourceException

**Correct Answer: B**

- **Concept:** ResourceBundle Candidate Selection.
- **Deep Explanation:** Java searches for bundles in this order:
    1. Requested Locale: `fr_FR` (Not found)
    2. Requested Language: `fr` (Not found - `fr_CA` does not match `fr`)
    3. **Default Locale:** `en_US` (Not found)
    4. **Default Language:** `en` (**Found:** `Menu_en.properties`)
    5. Root: `Menu.properties`
- **Why it's Right:** Since the requested `fr_FR` and `fr` are missing, Java falls back to the system default locale (`en_US`). It finds `Menu_en.properties` (matching the language of the default locale).
- **Why Others are Wrong:** A is wrong because `fr_CA` is not a parent of `fr_FR` or `fr`. C is wrong because the Default Language match (`en`) takes precedence over the Root bundle.