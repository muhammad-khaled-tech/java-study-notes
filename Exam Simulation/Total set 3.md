Here is **Exam Set #1**. I have designed these questions based on the scope defined in your **Slides** (Lessons 1-10 + Appendix) and excavated the deep technical logic from the **Boyarsky/Selikoff Textbook**.

This set covers: **Control Flow, Inner Classes, Generics, Lambdas, Streams, Concurrency, I/O (Serialization), and Exceptions.**

---

# Part 1: The Questions

### Question 1: Switch Expressions & Control Flow

**Topic:** Control Statements (Lesson 3 / Textbook Ch 2)

```
public class SwitchTest {
    public static void main(String[] args) {
        final int score = 4;
        var result = switch (score) {
            case 1, 2, 3 -> "Low";
            case 4 -> {
                String s = "High";
                return s;
            }
            default -> "Unknown";
        };
        System.out.println(result);
    }
}
```

**What is the result?** A. High B. The code does not compile because of `var`. C. The code does not compile because of `return`. D. The code compiles but throws an exception at runtime.

---

### Question 2: Static Nested Classes & Access Modifiers

**Topic:** Inner Classes (Lesson 5 / Textbook Ch 3)

```
class Outer {
    private int x = 10;
    static int y = 20;

    static class Inner {
        public void modify() {
            y = 30;  // Line 1
            x = 40;  // Line 2
        }
    }
}
```

**What is the result?** A. The code compiles successfully. B. Compilation error at Line 1 only. C. Compilation error at Line 2 only. D. Compilation error at both Line 1 and Line 2.

---

### Question 3: Generics & Wildcards

**Topic:** Generics (Lesson 7 / Textbook Ch 5)

```
import java.util.*;

public class WildcardTest {
    public static void main(String[] args) {
        List<? extends Number> list = new ArrayList<Integer>();
        list.add(null);      // Line 1
        list.add(10);        // Line 2
        Number n = list.get(0); // Line 3
    }
}
```

**What is the result?** A. Compilation error at Line 1. B. Compilation error at Line 2. C. Compilation error at Line 3. D. The code compiles and runs successfully.

---

### Question 4: Exception Handling & Finally Block

**Topic:** Exception Handling (Lesson 6 / Textbook Ch 4)

```
public class ExceptionFlow {
    public static String method() {
        try {
            throw new RuntimeException("A");
        } catch (Exception e) {
            return "B";
        } finally {
            return "C";
        }
    }

    public static void main(String[] args) {
        System.out.print(method());
    }
}
```

**What is the result?** A. A B. B C. C D. BC

---

### Question 5: Serialization & Modifiers

**Topic:** I/O & Modifiers (Appendix 1 / Textbook Ch 9)

```
import java.io.*;

class State implements Serializable {
    private static int staticVar = 10;
    private transient int transVar = 20;

    public static void main(String[] args) throws Exception {
        State s = new State();
        s.staticVar = 50;
        s.transVar = 60;

        // Serialize
        var out = new ObjectOutputStream(new FileOutputStream("state.ser"));
        out.writeObject(s);
        out.close();

        s.staticVar = 90;

        // Deserialize
        var in = new ObjectInputStream(new FileInputStream("state.ser"));
        State s2 = (State) in.readObject();
        System.out.println(s2.staticVar + " " + s2.transVar);
    }
}
```

**What is the result?** A. 50 60 B. 90 0 C. 50 0 D. 90 20

---

### Question 6: Concurrency & Thread Pools

**Topic:** Multi-Threading (Lesson 10 / Textbook Ch 8)

```
import java.util.concurrent.*;

public class PoolTest {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService service = Executors.newSingleThreadExecutor();
        service.submit(() -> System.out.print("A"));
        service.shutdown();
        service.submit(() -> System.out.print("B"));
    }
}
```

**What is the result?** A. AB B. A C. A followed by a `RejectedExecutionException`. D. The code does not compile.

---

### Question 7: Lambda Scope & Effective Finality

**Topic:** Lambda Expressions (Lesson 8 / Textbook Ch 3)

```
import java.util.function.Supplier;

public class LambdaScope {
    public static void main(String[] args) {
        int value = 10;
        Supplier<Integer> s = () -> value; // Line 1
        value++; // Line 2
        System.out.println(s.get()); // Line 3
    }
}
```

**What is the result?** A. 10 B. 11 C. Compilation error at Line 1. D. Compilation error at Line 2.

---

### Question 8: Stream Reduction

**Topic:** Streams (Lesson 9 / Textbook Ch 6)

```
import java.util.stream.Stream;

public class Reducer {
    public static void main(String[] args) {
        Integer val = Stream.of(1, 2, 3)
            .reduce(10, (a, b) -> a + b, (a, b) -> a - b);
        System.out.println(val);
    }
}
```

**What is the result?** A. 16 B. 6 C. 10 D. The result is unpredictable because of the combiner.

---

### Question 9: Interface Methods & Inheritance

**Topic:** Modifiers & Interfaces (Lesson 4 / Textbook Ch 3)

```
interface A {
    default void run() { System.out.print("A"); }
}
interface B {
    default void run() { System.out.print("B"); }
}
class C implements A, B {
    public void run() { System.out.print("C"); }

    public static void main(String[] args) {
        new C().run();
    }
}
```

**What is the result?** A. C B. Compilation error: C inherits unrelated defaults for run(). C. Compilation error: C cannot implement both interfaces. D. A

---

### Question 10: Array Initialization

**Topic:** Data Types & Arrays (Lesson 2 / Textbook Ch 5)

```
public class ArrayInit {
    public static void main(String[] args) {
        int[][] matrix = new int[];
        matrix = new int;
        matrix = new int;

        System.out.println(matrix + " " + matrix);
    }
}
```

**What is the result?** A. 0 0 B. 0 followed by an ArrayIndexOutOfBoundsException. C. Compilation error. D. NullPointerException.

---

# Part 2: Detailed Answer Key

### Answer 1

- **Correct Answer:** C
- **Concept:** Switch Expressions (`yield` vs `return`)
- **Deep Explanation:** In a Switch Expression (introduced in Java 14), you must return a value to the variable being assigned (`result`). If you use a code block `{...}`, you must use the keyword `yield` to return the value. The keyword `return` attempts to exit the _method_ (`main`), not the switch. Since the switch expression expects a value to be assigned to `result`, checking out of the method entirely creates an incomplete expression logic or, more specifically, `return` is simply forbidden inside a switch expression block.
- **Why it's Right:** Textbook Chapter 2 confirms that `yield` is required for blocks in switch expressions.
- **Why Others are Wrong:**
    - A is incorrect because the code fails to compile.
    - B is incorrect because `var` is valid here (inferred as String).
    - D is incorrect because this is a compile-time error.

### Answer 2

- **Correct Answer:** C
- **Concept:** Static Nested Classes
- **Deep Explanation:** A `static` nested class is behaviorally a top-level class that happens to be nested for packaging convenience. It does _not_ have an implicit reference to an instance of the `Outer` class. Therefore, it cannot access non-static instance variables (like `x`) directly. It can only access static members (like `y`).
- **Why it's Right:** Line 2 attempts to access `x` (instance variable) from a static context (Static Inner Class) without an object reference.
- **Why Others are Wrong:**
    - A is incorrect because Line 2 fails.
    - B is incorrect because accessing `y` (static) is perfectly valid.
    - D is incorrect because Line 1 is valid.

### Answer 3

- **Correct Answer:** B
- **Concept:** Generics Upper Bounded Wildcards (PECS)
- **Deep Explanation:** `List<? extends Number>` means "A list of _some_ type that extends Number". It could be `List<Integer>`, `List<Double>`, or `List<BigDecimal>`. Because the compiler cannot guarantee the underlying type, it effectively makes the list **read-only** (except for `null`, which matches all reference types). You cannot add an `Integer` because the list might actually point to a `List<Double>`.
- **Why it's Right:** Line 2 tries to add `10`, violating the upper-bound safety rule found in Lesson 7 and Textbook Chapter 5.
- **Why Others are Wrong:**
    - A is incorrect because adding `null` is always allowed.
    - C is incorrect because reading _from_ an `extends` list is allowed (you get back the upper bound, `Number`).
    - D is incorrect due to the error at Line 2.

### Answer 4

- **Correct Answer:** C
- **Concept:** Finally Block Dominance
- **Deep Explanation:** The `try` block throws an exception. The `catch` block catches it and attempts to `return "B"`. However, before the method can actually return, the `finally` block **must** execute. If a `finally` block contains a `return` statement, it overrides any previous return value or thrown exception. The method abruptly completes with the value from the `finally` block.
- **Why it's Right:** As detailed in Lesson 6 (Slide 287 "The finally Clause"), a return in `finally` masks the original return.
- **Why Others are Wrong:**
    - A is incorrect because the exception is caught.
    - B is incorrect because the `catch` return is superseded.
    - D is incorrect because a method can only return one value.

### Answer 5

- **Correct Answer:** B
- **Concept:** Serialization (`transient` & `static`)
- **Deep Explanation:**
    1. **static:** Static variables belong to the _class_, not the object. They are **not** serialized. When deserializing, the JVM looks at the _current_ value of the static variable in the classloader. Since we set `s.staticVar = 90` _after_ serialization but _before_ the print statement, the deserialized object sees the current class value: 90.
    2. **transient:** Transient variables are explicitly ignored during serialization. When `s2` is deserialized, `transVar` is initialized to the default value for `int`, which is 0.
- **Why it's Right:** Explained in Appendix 1/Textbook Chapter 9 regarding `Serializable`.
- **Why Others are Wrong:**
    - A and C are incorrect regarding the static variable (it doesn't revert to 50).
    - D is incorrect regarding the transient variable (it doesn't keep the value 20).

### Answer 6

- **Correct Answer:** C
- **Concept:** ExecutorService Lifecycle
- **Deep Explanation:** Once `service.shutdown()` is called, the ExecutorService stops accepting new tasks. It continues to execute submitted tasks, but any _new_ calls to `submit()` will trigger a `RejectedExecutionException`.
- **Why it's Right:** Lesson 10 and Textbook Chapter 8 define the behavior of `shutdown()`. "A" prints successfully, but the second submit fails.
- **Why Others are Wrong:**
    - A assumes the second task is accepted (incorrect).
    - B implies the second task is ignored silently (incorrect, it throws an exception).
    - D is incorrect; the code syntax is valid.

### Answer 7

- **Correct Answer:** D
- **Concept:** Effectively Final variables in Lambdas
- **Deep Explanation:** Any local variable used inside a lambda expression (or anonymous inner class) must be **final** or **effectively final**. This means the variable cannot be modified anywhere in its scope (before or after the lambda). By performing `value++` on Line 2, `value` is no longer effectively final.
- **Why it's Right:** The compiler detects that `value` is modified, invalidating its usage inside the lambda at Line 1. However, the error usually flags the _usage_ (Line 1) or the _modification_ (Line 2) depending on the compiler, but logically the modification at Line 2 destroys the "effectively final" status required by Line 1. (Note: Most compilers flag the usage inside the lambda, but the _cause_ is line 2. If forced to choose the line causing the logic break, it is the modification). _Correction on Standard Exam Logic:_ The compile error usually points to the variable usage inside the lambda saying "local variables referenced from a lambda expression must be final or effectively final". However, option D is acceptable if interpreted as "Line 2 makes it illegal". But strictly speaking, the red underline appears at Line 1 usage. Let's look closely at standard behavior: The compilation error occurs at Line 1 because `value` is not effectively final. _However_, usually exam questions flag the _act of modification_ if the question asks "Which line causes the error?". Actually, usually removing Line 2 fixes it. Let's stick to the rule: The variable is modified, thus not effectively final. The error is reported at the lambda usage. **WAIT**, I selected "D" in the key initially? Let me re-evaluate. If I comment out line 2, line 1 works. If I comment out line 1, line 2 works. The conflict is mutual. However, typically the error is "Usage at Line 1 is invalid because Line 2 exists". Let's stick with **Line 2 (Option D)** being the "cause" in the context of the question asking what breaks the effectively final rule, or **Line 1 (Option C)** as the location of the error message. _Self-Correction:_ In OCP exams, if you modify the variable, the error is usually cited as "variable used in lambda expression should be final or effectively final". This is an error _at the lambda_. So **C** is technically more precise regarding where the compiler complains.
- _Re-evaluating Answer Key:_ I will change the correct answer to **C** (Line 1) to be safe with compiler output, or ensure the explanation clarifies. Actually, looking at OpenJDK `javac`: `error: local variables referenced from a lambda expression must be final or effectively final`. This error points to Line 1.
- **REVISED Correct Answer:** **C** (Compilation error at Line 1).
- **Why Others are Wrong:**
    - A and B are incorrect because it doesn't compile.

### Answer 8

- **Correct Answer:** A
- **Concept:** Stream Reduction (Sequential)
- **Deep Explanation:** The `reduce` method here takes three arguments: `identity` (10), `accumulator`, and `combiner`. However, this is a **sequential** stream (created via `Stream.of`).
    - In a sequential stream, the **combiner is NOT used**.
    - Execution:
        1. Start with identity `10`.
        2. Accumulate 1: `10 + 1 = 11`.
        3. Accumulate 2: `11 + 2 = 13`.
        4. Accumulate 3: `13 + 3 = 16`.
- **Why it's Right:** Textbook Chapter 6 confirms the combiner is only used in parallel streams to merge partial results.
- **Why Others are Wrong:**
    - B would be the result if the identity was 0.
    - C is just the identity.
    - D is incorrect because the stream is sequential; the output is deterministic.

### Answer 9

- **Correct Answer:** A
- **Concept:** Interface Default Method Conflicts
- **Deep Explanation:** Java allows a class to implement multiple interfaces. If both interfaces define a `default` method with the same signature (`void run()`), there is a conflict. The implementing class **MUST** override the method to resolve the ambiguity.
- **Why it's Right:** Class `C` _does_ override `run()`. It provides its own implementation (`System.out.print("C")`). This resolves the conflict perfectly.
- **Why Others are Wrong:**
    - B would be correct _only if_ Class C did not provide its own implementation of `run()`.
    - C is incorrect; implementing multiple interfaces is standard Java.
    - D is incorrect because the overridden method in C takes precedence.

### Answer 10

- **Correct Answer:** B
- **Concept:** Multidimensional Arrays & Initialization
- **Deep Explanation:**
    - `new int[]` creates an array of arrays of length 2. `matrix` and `matrix` are initially null.
    - `matrix = new int` initializes the first row with 3 columns (indices 0, 1, 2).
    - `matrix = new int` initializes the second row with 2 columns (indices 0, 1).
    - `matrix` exists (default int 0).
    - `matrix` accesses index 2 of the second row. The second row has length 2 (indices 0 and 1). Index 2 is out of bounds.
- **Why it's Right:** Accessing index 2 on an array of length 2 throws `ArrayIndexOutOfBoundsException`.
- **Why Others are Wrong:**
    - A is incorrect because the second access fails.
    - C is incorrect; syntax is valid (jagged arrays).
    - D is incorrect because `matrix` was initialized; it's not null, just too short.