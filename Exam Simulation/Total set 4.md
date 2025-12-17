Here is **Exam Set #2**. This set focuses on **Strings/StringBuilder, Access Modifiers, Inner Classes, Collections, Streams, Exceptions, Generics, and Concurrency**.

These questions are designed to test edge cases found in the **Boyarsky/Selikoff Textbook** while adhering to the syllabus structure outlined in your **Slides**.

---

# Part 1: The Questions

### Question 1: StringBuilder & Immutability

**Topic:** String Handling (Lesson 3 / Textbook Ch 1)

```
public class StringTwister {
    public static void main(String[] args) {
        var sb = new StringBuilder("Java");
        String s = sb.append("Se").substring(4);
        sb.insert(0, "C");
        System.out.println(sb + " " + s);
    }
}
```

**What is the result?** A. CJavaSe Se B. JavaSe Se C. CJavaSe aSe D. JavaSe aSe

---

### Question 2: Protected Access & Inheritance

**Topic:** Modifiers & Packages (Lesson 4 / Textbook Ch 3)

```
// File: com/parent/Parent.java
package com.parent;
public class Parent {
    protected int x = 10;
}

// File: com/child/Child.java
package com.child;
import com.parent.Parent;

public class Child extends Parent {
    public void test() {
        Parent p = new Parent();
        System.out.print(p.x); // Line 1
        Child c = new Child();
        System.out.print(c.x); // Line 2
        System.out.print(x);   // Line 3
    }
}
```

**What is the result of compiling `Child.java`?** A. The code compiles successfully. B. Compilation error at Line 1 only. C. Compilation error at Line 1 and Line 2. D. Compilation error at Line 2 and Line 3.

---

### Question 3: Local Inner Classes & Effective Finality

**Topic:** Inner Classes (Lesson 5 / Textbook Ch 3)

```
public class InnerSanctum {
    public void calculate() {
        int seed = 10;
        class Calculator {
            public void add() {
                System.out.println(seed + 5); // Line X
            }
        }
        seed++; // Line Y
        new Calculator().add();
    }
    public static void main(String[] args) {
        new InnerSanctum().calculate();
    }
}
```

**What is the result?** A. 15 B. 16 C. Compilation error at Line X. D. Compilation error at Line Y.

---

### Question 4: Try-with-Resources & Suppressed Exceptions

**Topic:** Exception Handling (Lesson 6 / Textbook Ch 4)

```
import java.io.*;

class Door implements AutoCloseable {
    public void close() throws IOException {
        throw new IOException("Cage Closed");
    }
}

public class Zoo {
    public static void main(String[] args) {
        try (Door d = new Door()) {
            throw new RuntimeException("Animals Out");
        } catch (Exception e) {
            System.out.print(e.getMessage());
            for (Throwable t : e.getSuppressed()) {
                System.out.print(" + " + t.getMessage());
            }
        }
    }
}
```

**What is the result?** A. Animals Out B. Cage Closed C. Animals Out + Cage Closed D. Cage Closed + Animals Out

---

### Question 5: Generics & Type Erasure

**Topic:** Generics (Lesson 7 / Textbook Ch 5)

```
import java.util.*;

public class GenericList {
    public static void main(String[] args) {
        List<Integer> numbers = new ArrayList<>();
        numbers.add(1);
        List objects = numbers; // Line 1
        objects.add("Text");    // Line 2
        System.out.print(numbers.get(1)); // Line 3
    }
}
```

**What is the result?** A. Text B. Compilation error at Line 1. C. Compilation error at Line 2. D. Runtime ClassCastException at Line 3.

---

### Question 6: Map Merge Logic

**Topic:** Collections (Lesson 9/10 / Textbook Ch 5)

```
import java.util.*;

public class MapMerge {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("key", "value");

        map.merge("key", "new", (v1, v2) -> null);

        System.out.println(map.get("key"));
    }
}
```

**What is the result?** A. value B. new C. null D. valuenew

---

### Question 7: Stream Operations (All Match)

**Topic:** Streams (Lesson 9 / Textbook Ch 6)

```
import java.util.stream.Stream;

public class StreamLogic {
    public static void main(String[] args) {
        boolean result = Stream.iterate(1, x -> x + 1)
            .limit(5)
            .allMatch(x -> x < 10);

        System.out.println(result);
    }
}
```

**What is the result?** A. true B. false C. Compilation error. D. Throws an exception at runtime (Infinite Stream).

---

### Question 8: Interface Static Methods

**Topic:** Interfaces (Lesson 4 / Textbook Ch 3)

```
interface Machine {
    static void start() {
        System.out.print("Machine");
    }
}

class Robot implements Machine {
    public static void main(String[] args) {
        Machine.start(); // Line 1
        Robot.start();   // Line 2
        start();         // Line 3
    }
}
```

**What is the result?** A. MachineMachineMachine B. Machine C. Compilation error at Line 2 and Line 3. D. Compilation error at Line 3 only.

---

### Question 9: Concurrency & Synchronization

**Topic:** Multi-Threading (Lesson 11 / Textbook Ch 8)

```
public class Counter {
    public static synchronized void printA() {
        System.out.print("A");
    }
    public synchronized void printB() {
        System.out.print("B");
    }
    public static void main(String[] args) {
        Counter c = new Counter();
        new Thread(() -> c.printA()).start();
        new Thread(() -> c.printB()).start();
    }
}
```

**Assuming threads run concurrently, which statement is true?** A. Compilation error: `printA` cannot be called on instance `c`. B. The output is guaranteed to be AB. C. The output is guaranteed to be BA. D. The output could be AB or BA (No synchronization guarantee between static and instance).

---

### Question 10: Serialization & Inheritance

**Topic:** I/O Streams (Appendix 1 / Textbook Ch 9)

```
import java.io.*;

class Parent {
    Parent() { System.out.print("P"); }
}

class Child extends Parent implements Serializable {
    Child() { System.out.print("C"); }

    public static void main(String[] args) throws Exception {
        var obj = new Child(); // Line 1

        var out = new ObjectOutputStream(new FileOutputStream("file.ser"));
        out.writeObject(obj);
        out.close();

        var in = new ObjectInputStream(new FileInputStream("file.ser"));
        var obj2 = (Child) in.readObject(); // Line 2
    }
}
```

**What is the output?** A. PCPC B. PC P C. PC D. P P

---

# Part 2: Detailed Answer Key

### Answer 1

- **Correct Answer:** A
- **Concept:** `StringBuilder` API (Mutability vs Return Types)
- **Deep Explanation:**
    1. `var sb = new StringBuilder("Java");` -> `sb` is "Java".
    2. `sb.append("Se")` modifies `sb` to "JavaSe" and returns the reference to `sb`.
    3. `.substring(4)` is called on `sb`. **Crucial Rule:** `StringBuilder.substring` returns a new `String`. It does **not** modify the `StringBuilder` itself. So, `s` becomes "Se", but `sb` remains "JavaSe".
    4. `sb.insert(0, "C")` modifies `sb` to "CJavaSe".
    5. Print `sb` ("CJavaSe") + space + `s` ("Se").
- **Why it's Right:** Slide 150/158 confirm `append` and `insert` modify the builder, while `substring` returns a String.
- **Why Others are Wrong:**
    - B is incorrect because `insert` modified the builder.
    - C/D are incorrect because `substring(4)` starts at index 4 ('S'), not 'a'.

### Answer 2

- **Correct Answer:** B
- **Concept:** Protected Access Modifier (The "Protected Trap")
- **Deep Explanation:** `protected` allows access to subclasses in different packages, _but only through inheritance_.
    - **Line 1:** `p` is a reference of type `Parent`. Even though `Child` extends `Parent`, inside `Child`, you cannot access `p.x` because `p` is not guaranteed to be a `Child` instance. It's a standard `Parent` instance, and protected members are not visible to unrelated instances in different packages.
    - **Line 2:** `c` is a `Child`. A subclass can access its _own_ inherited protected members. Valid.
    - **Line 3:** Accessing `x` (implicit `this.x`) is valid for the same reason as Line 2.
- **Why it's Right:** Textbook Chapter 3 explicitly covers this edge case: "A subclass may access a protected member... only if the member involves the implementation of its own parent class."
- **Why Others are Wrong:**
    - A is incorrect because Line 1 fails.
    - C/D are incorrect because Lines 2 and 3 are valid.

### Answer 3

- **Correct Answer:** C
- **Concept:** Local Inner Classes & Effectively Final
- **Deep Explanation:** Local classes (classes defined inside a method) can access local variables of the enclosing method _only if_ those variables are final or **effectively final**.
    - Variable `seed` is initialized to 10.
    - Line Y performs `seed++`, modifying the variable.
    - This modification makes `seed` **not** effectively final.
    - Line X attempts to access `seed` inside the local class. Since `seed` is not effectively final, the compiler throws an error _at the point of usage_ (Line X).
- **Why it's Right:** Slide 248 notes: "It can also access the local variables of the enclosing method if they are declared final" (and Java 8+ extends this to effectively final).
- **Why Others are Wrong:**
    - A and B are incorrect because it doesn't compile.
    - D is incorrect because the error is flagged where the variable is _accessed_ inside the inner class, not where it is modified (though the modification causes the issue).

### Answer 4

- **Correct Answer:** C
- **Concept:** Try-with-Resources & Suppressed Exceptions
- **Deep Explanation:**
    1. The `try` block executes and throws `RuntimeException("Animals Out")`. This is the _primary_ exception.
    2. Java attempts to close the resource `d`.
    3. `d.close()` throws `IOException("Cage Closed")`.
    4. Because an exception was already thrown in the `try` block, the exception from `close()` is **suppressed** and added to the primary exception.
    5. The `catch` block catches the primary exception ("Animals Out").
    6. `e.getMessage()` prints "Animals Out".
    7. `e.getSuppressed()` retrieves the "Cage Closed" exception.
- **Why it's Right:** Textbook Chapter 4 confirms that exceptions thrown during auto-close are suppressed if the try block also throws.
- **Why Others are Wrong:**
    - A misses the suppressed exception.
    - B/D implies the `close` exception takes precedence (it only does if the try block finishes normally).

### Answer 5

- **Correct Answer:** D
- **Concept:** Generics, Type Erasure, and Heap Pollution
- **Deep Explanation:**
    - Line 1: `List objects = numbers;` creates a raw type reference to the generic list. This generates a warning but compiles. This is "Heap Pollution".
    - Line 2: `objects.add("Text");` works because the raw `List` treats everything as `Object`. The underlying `ArrayList` now contains `[1, "Text"]`.
    - Line 3: `numbers.get(1)` tries to retrieve the item. The compiler inserts an implicit cast to `Integer` because `numbers` is of type `List<Integer>`.
    - At runtime, Java tries to cast the String "Text" to `Integer`. This fails.
- **Why it's Right:** Slide 305/306 discusses Type Erasure. The cast happens at retrieval time.
- **Why Others are Wrong:**
    - A is incorrect because of the ClassCastException.
    - B/C are incorrect because raw type interoperability allows compilation (backward compatibility).

### Answer 6

- **Correct Answer:** C
- **Concept:** Map `merge()` behavior
- **Deep Explanation:** The `map.merge(key, value, remappingFunction)` method works as follows:
    1. If the key exists (it does, "value"), run the remapping function.
    2. Function: `(v1, v2) -> null`. The inputs are "value" (old) and "new" (new). It returns `null`.
    3. **Rule:** If the remapping function returns `null`, the key is **removed** from the map.
    4. `map.get("key")` returns `null` because the key is gone.
- **Why it's Right:** Textbook Chapter 5 (Maps) details `merge` logic. Returning null signals removal.
- **Why Others are Wrong:**
    - A/B/D assume the key stays. It does not.

### Answer 7

- **Correct Answer:** A
- **Concept:** Streams & Short-Circuiting
- **Deep Explanation:**
    1. `Stream.iterate(1, x -> x + 1)` creates an infinite stream: 1, 2, 3...
    2. `.limit(5)` restricts it to: 1, 2, 3, 4, 5.
    3. `.allMatch(x -> x < 10)` checks if _all_ elements are less than 10.
    4. 1 < 10 (true), ... 5 < 10 (true).
    5. Since all 5 elements pass, it returns `true`.
- **Why it's Right:** Slide 364 lists `allMatch` as a terminal operation. It stops processing when the limit is reached or a false result occurs.
- **Why Others are Wrong:**
    - B is incorrect because the logic holds true.
    - D is incorrect because `limit(5)` makes the stream finite before `allMatch` processes it.

### Answer 8

- **Correct Answer:** C
- **Concept:** Static Methods in Interfaces
- **Deep Explanation:** Static methods in interfaces (introduced in Java 8) are **not inherited** by implementing classes.
    - **Line 1:** `Machine.start()` is the only valid way to call an interface static method.
    - **Line 2:** `Robot.start()` implies inheritance/static access via subclass. This is illegal for interface static methods.
    - **Line 3:** `start()` implies it's locally available or inherited. It is not.
- **Why it's Right:** Slide 198 (Static Methods in Interface) and Textbook Chapter 3 confirm static interface methods must be called using the Interface name.
- **Why Others are Wrong:**
    - A/B are incorrect because the code fails to compile.
    - D is incorrect because Line 2 is also an error.

### Answer 9

- **Correct Answer:** D
- **Concept:** Synchronization (Class Lock vs Object Lock)
- **Deep Explanation:**
    - `printA` is `static synchronized`. It locks on the **Class** object (`Counter.class`).
    - `printB` is `synchronized`. It locks on the **Instance** object (`c`).
    - Since they use **different locks**, the two threads can enter their respective methods **simultaneously**.
    - Thread 1 might print A, then Thread 2 prints B, OR Thread 2 prints B, then Thread 1 prints A.
- **Why it's Right:** Slide 478/Textbook Chapter 8. Different locks do not block each other.
- **Why Others are Wrong:**
    - A is incorrect; static methods can be called via instance references (though discouraged, it compiles).
    - B/C imply synchronization enforced an order, which it didn't here.

### Answer 10

- **Correct Answer:** B
- **Concept:** Serialization & Constructor Execution
- **Deep Explanation:**
    1. **Line 1 (Serialization):** `new Child()` is called.
        - `Parent` constructor runs -> Prints "P".
        - `Child` constructor runs -> Prints "C".
        - Output so far: "PC".
    2. **Line 2 (Deserialization):**
        - `Child` implements `Serializable`.
        - **Rule:** Constructors for serializable classes are **not** run during deserialization.
        - **Rule:** Constructors for the **first non-serializable parent** ARE run.
        - `Parent` is NOT serializable. So `Parent()` is called. -> Prints "P".
        - `Child()` is skipped.
- **Why it's Right:** Appendix 1 / Textbook Chapter 9. Inheritance in serialization requires the non-serializable parent constructor to run to initialize inherited state.
- **Why Others are Wrong:**
    - A assumes `Child` constructor runs during deserialization (incorrect).
    - C assumes `Parent` constructor doesn't run during deserialization (incorrect).
    - D is nonsense.