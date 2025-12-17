
# 🎓 Java OCP Practice Exam - Set 2

**Tags:** #Java #OCP #PracticeExam #Coding

---

### Q1: StringBuilder & Immutability

**Topic:** String Handling



```Java
public class StringTwister {
    public static void main(String[] args) {
        var sb = new StringBuilder("Java");
        String s = sb.append("Se").substring(4);
        sb.insert(0, "C");
        System.out.println(sb + " " + s);
    }
}
```

**What is the result?**

- **A.** CJavaSe Se
    
- **B.** JavaSe Se
    
- **C.** CJavaSe aSe
    
- **D.** JavaSe aSe
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: StringBuilder API (Mutability vs Return Types).
> 
> Deep Explanation:
> 
> 1. `var sb` starts as `"Java"`.
>     
> 2. `sb.append("Se")` modifies `sb` to `"JavaSe"`.
>     
> 3. `.substring(4)` is called. **Critical Rule:** `substring` on `StringBuilder` returns a **new String** but does **NOT** modify the StringBuilder itself. Index 4 corresponds to `'S'`. So `s` becomes `"Se"`.
>     
> 4. `sb` is still `"JavaSe"`.
>     
> 5. `sb.insert(0, "C")` modifies `sb` in place. `sb` becomes `"CJavaSe"`.
>     
> 6. Output: `sb` ("CJavaSe") + `s` ("Se").
>     

---

### Q2: Protected Access & Inheritance

**Topic:** Modifiers & Packages

Java

```Java
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

**What is the result of compiling Child.java?**

- **A.** The code compiles successfully.
    
- **B.** Compilation error at Line 1 only.
    
- **C.** Compilation error at Line 1 and Line 2.
    
- **D.** Compilation error at Line 2 and Line 3.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: The "Protected Trap" in Inheritance.
> 
> Deep Explanation:
> 
> - **Rule:** `protected` members are accessible in different packages **only via inheritance** (using `this` or a subclass instance).
>     
> - **Line 1:** `p` is a reference of type `Parent`. Even though we are inside `Child`, accessing `p.x` treats `x` as a member of `Parent`. Since `Parent` is in a different package, you cannot access its protected members via a parent reference.
>     
> - **Line 2:** `c` is a reference of type `Child`. This is allowed because `Child` inherits `x`.
>     
> - **Line 3:** `x` is implicitly `this.x`. Allowed via inheritance.
>     

---

### Q3: Local Inner Classes & Effective Finality

**Topic:** Inner Classes

Java

```Java
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

**What is the result?**

- **A.** 15
    
- **B.** 16
    
- **C.** Compilation error at Line X.
    
- **D.** Compilation error at Line Y.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Local Inner Classes & "Effectively Final" variables.
> 
> Deep Explanation:
> 
> - Local inner classes can only access local variables if they are **final** or **effectively final** (never modified).
>     
> - **Line Y:** The variable `seed` is modified (`seed++`). This strips it of its "effectively final" status.
>     
> - **Line X:** When the inner class tries to access `seed`, the compiler notices `seed` is modified elsewhere, resulting in a compilation error **at the point of usage inside the inner class**.
>     

---

### Q4: Try-with-Resources & Suppressed Exceptions

**Topic:** Exception Handling

Java

```Java
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

**What is the result?**

- **A.** Animals Out
    
- **B.** Cage Closed
    
- **C.** Animals Out + Cage Closed
    
- **D.** Cage Closed + Animals Out
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Suppressed Exceptions.
> 
> Deep Explanation:
> 
> 1. The `try` block executes and throws `RuntimeException("Animals Out")`. This is the **Primary Exception**.
>     
> 2. Java automatically calls `close()` on the resource `d`.
>     
> 3. The `close()` method throws `IOException("Cage Closed")`.
>     
> 4. **Rule:** Since an exception is already "in flight" (Animals Out), the exception from `close()` is **suppressed** and added to the primary exception's suppressed list.
>     
> 5. `e.getMessage()` prints "Animals Out".
>     
> 6. `e.getSuppressed()` retrieves the "Cage Closed" exception.
>     

---

### Q5: Generics & Type Erasure

**Topic:** Generics

Java

```Java
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

**What is the result?**

- **A.** Text
    
- **B.** Compilation error at Line 1.
    
- **C.** Compilation error at Line 2.
    
- **D.** Runtime ClassCastException at Line 3.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: D
> 
> Concept: Heap Pollution and Type Erasure.
> 
> Deep Explanation:
> 
> - **Line 1:** Assigning `List<Integer>` to a raw `List` is legal (backward compatibility).
>     
> - **Line 2:** The variable `objects` is raw, so the compiler checks against `Object`. Adding `"Text"` is allowed. The heap now contains `[1, "Text"]` inside the ArrayList.
>     
> - **Line 3:** We access `numbers.get(1)`. The variable `numbers` is typed as `List<Integer>`. The compiler inserts an implicit cast: `(Integer) numbers.get(1)`.
>     
> - **Runtime:** Casting the String "Text" to Integer causes a `ClassCastException`.
>     

---

### Q6: Map Merge Logic

**Topic:** Collections

Java

```Java
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

**What is the result?**

- **A.** value
    
- **B.** new
    
- **C.** null
    
- **D.** valuenew
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Map.merge() behavior.
> 
> Deep Explanation:
> 
> - `merge` checks if the key exists. It does ("value").
>     
> - It applies the remapping function `(old, new) -> result`. Here, the function explicitly returns `null`.
>     
> - **Rule:** If the remapping function returns `null`, the key is **removed** from the map.
>     
> - Calling `map.get("key")` afterwards returns `null` because the key is gone.
>     

---

### Q7: Stream Operations (Short-Circuiting)

**Topic:** Streams

Java

```Java
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

**What is the result?**

- **A.** true
    
- **B.** false
    
- **C.** Compilation error.
    
- **D.** Throws an exception at runtime (Infinite Stream).
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: Infinite Streams & Short-Circuiting.
> 
> Deep Explanation:
> 
> - `Stream.iterate` creates an infinite stream (1, 2, 3...).
>     
> - `.limit(5)` truncates it to finite elements: `1, 2, 3, 4, 5`.
>     
> - `.allMatch(x -> x < 10)` checks every element.
>     
> - Since 1 through 5 are all less than 10, it returns `true`. It does not loop infinitely because of `limit`.
>     

---

### Q8: Interface Static Methods

**Topic:** Interfaces

Java

```Java
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

**What is the result?**

- **A.** MachineMachineMachine
    
- **B.** Machine
    
- **C.** Compilation error at Line 2 and Line 3.
    
- **D.** Compilation error at Line 3 only.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Static Methods in Interfaces.
> 
> Deep Explanation:
> 
> - **Rule:** Static methods in interfaces are **NOT inherited** by implementing classes.
>     
> - **Line 1:** `Machine.start()` is the only legal way to call it.
>     
> - **Line 2:** `Robot.start()` is illegal because `Robot` did not inherit the static method.
>     
> - **Line 3:** `start()` looks for a method in the current scope, which doesn't exist.
>     

---

### Q9: Concurrency & Synchronization

**Topic:** Multi-Threading

Java

```Java
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

**Assuming threads run concurrently, which statement is true regarding blocking?**

- **A.** Compilation error: printA cannot be called on instance c.
    
- **B.** The threads block each other; output is guaranteed ordered.
    
- **C.** The threads do NOT block each other.
    
- **D.** Runtime Exception.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Class Lock vs Object Lock.
> 
> Deep Explanation:
> 
> - `printA` is **static synchronized**. It acquires the lock on the **Class object** (`Counter.class`).
>     
> - `printB` is **synchronized** (instance). It acquires the lock on the **Instance object** (`c`).
>     
> - Since the locks are on **different objects** (Class vs Instance), the threads do NOT block each other and can run simultaneously.
>     

---

### Q10: Serialization & Inheritance

**Topic:** I/O Streams

Java

```Java
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

**What is the output?**

- **A.** PCPC
    
- **B.** PC P
    
- **C.** PC
    
- **D.** P P
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Serialization & Constructor Execution.
> 
> Deep Explanation:
> 
> - **Line 1 (Construction):** `new Child()` runs normally. Parent constructor runs (`P`), then Child (`C`). Output so far: **PC**.
>     
> - **Line 2 (Deserialization):**
>     
>     - `Child` is `Serializable`, so its constructor is **skipped**.
>         
>     - `Parent` is **NOT** `Serializable`. To restore the state of the parent fields, Java **MUST run the no-arg constructor** of the first non-serializable superclass. Output: **P**.
>         
> - **Total:** **PC P**.

---
### Q11: Thread Life Cycle & yield()

**Topic:** Concurrency

Java

```
public class Processor extends Thread {
    public void run() {
        for (int i = 0; i < 3; i++) {
            System.out.print(i);
            Thread.yield();
        }
    }
    public static void main(String[] args) {
        new Processor().start();
        new Processor().start();
    }
}
```

**What is the guaranteed output?**

- **A.** 001122
    
- **B.** 012012
    
- **C.** The output is unpredictable (e.g., 012012, 001122, 010122).
    
- **D.** Compilation Error.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Thread Scheduling Hints (yield()).
> 
> Deep Explanation:
> 
> - `Thread.yield()`: This method is a "hint" to the thread scheduler that the current thread is willing to yield its current use of a processor. The scheduler is free to **ignore** this hint.
>     
> - **Race Condition:** There is no synchronization here. The OS scheduler determines which thread runs and for how long.
>     
> - **Outcome:** One thread might print 0 and yield, allowing the other to run. Or, the first thread might ignore the yield and print 012 before the second thread even starts. The output depends entirely on the OS and JVM implementation.
>     

---

### Q12: Files.lines vs readAllLines (Memory Impact)

**Topic:** I/O & NIO.2

Java

```
Path path = Paths.get("huge_log_file.txt"); // Assume 5GB file
// Option A
List<String> lines = Files.readAllLines(path);

// Option B
try (Stream<String> s = Files.lines(path)) {
    s.forEach(System.out::println);
}
```

**Which statement accurately describes the behavior for a 5GB file on a standard 2GB Heap?**

- **A.** Option A is faster and uses less memory.
    
- **B.** Option B causes an OutOfMemoryError.
    
- **C.** Option A causes an OutOfMemoryError; Option B works efficiently.
    
- **D.** Both options work identically.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Stream Lazy Loading vs Eager Loading.
> 
> Deep Explanation:
> 
> - `readAllLines` (**Eager**): Loads the **entire file** into a `List<String>` in heap memory. A 5GB file will immediately exhaust a 2GB heap, causing `java.lang.OutOfMemoryError`.
>     
> - `Files.lines` (**Lazy**): Returns a `Stream`. Streams pull data lazily (one line at a time). It reads a line, processes it (prints it), and discards it for Garbage Collection. It has a constant, low memory footprint regardless of file size.
>     

---

### Q13: Parallel Stream Reduction (Associativity)

**Topic:** Streams

Java

```
List<Integer> data = List.of(1, 2, 3, 4, 5);
int result = data.parallelStream()
    .reduce(0, (a, b) -> (a - b)); // Subtraction
System.out.println(result);
```

**What is the result?**

- **A.** -15
    
- **B.** -5
    
- **C.** 15
    
- **D.** Unpredictable (Non-deterministic).
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: D
> 
> Concept: Parallel Reduction Requirements (Associativity).
> 
> Deep Explanation:
> 
> - **The Rule:** For `reduce()` to work correctly in parallel, the accumulator function must be **associative**. That means `(a op b) op c` must equal `a op (b op c)`.
>     
> - **Subtraction:** Subtraction is **NOT** associative.
>     
>     - `(1 - 2) - 3 = -4`
>         
>     - `1 - (2 - 3) = 2`
>         
> - **Parallel Execution:** The Framework splits the data into chunks. Depending on how it splits and recombines them (e.g., Thread 1 handles 1,2; Thread 2 handles 3,4,5), the result will change every time you run it.
>     

---

### Q14: Multi-Catch Inheritance Rule

**Topic:** Exceptions

Java

```
try {
    throw new IOException("Disk Error");
} catch (FileNotFoundException | IOException e) {
    System.out.println(e.getMessage());
}
```

**What is the result?**

- **A.** Prints "Disk Error".
    
- **B.** Compilation Error.
    
- **C.** Throws RuntimeException.
    
- **D.** Prints "Disk Error" twice.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Multi-Catch Redundancy.
> 
> Deep Explanation:
> 
> - **The Rule:** In a multi-catch block (`|`), you cannot list two exceptions if one is a subclass of the other.
>     
> - **The Hierarchy:** `FileNotFoundException` extends `IOException`.
>     
> - **The Conflict:** Catching `IOException` automatically catches `FileNotFoundException`. Listing the child separately is redundant code. The compiler rejects this to prevent unreachable code logic within the same catch block configuration.
>     

---

### Q15: Enums & Abstract Methods

**Topic:** Enums

Java

```
enum Operations {
    ADD,
    SUBTRACT {
        public int apply(int x, int y) { return x - y; }
    };
    public abstract int apply(int x, int y);
}
```

**What happens when compiling this Enum?**

- **A.** Compiles successfully.
    
- **B.** Compilation Error at ADD.
    
- **C.** Compilation Error at SUBTRACT.
    
- **D.** Compilation Error at the abstract method declaration.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Enum Constant Specific Class Bodies.
> 
> Deep Explanation:
> 
> - **The Contract:** If an enum declares an abstract method, **every single constant** must implement that method.
>     
> - **The Bug:** `SUBTRACT` provides an implementation (anonymous inner class body). `ADD` does not.
>     
> - **Result:** The compiler treats `ADD` as an incomplete implementation of the abstract class `Operations`.
>     

---

### Q16: Interface Private Methods Access

**Topic:** Interfaces

Java

```
interface Sensor {
    private void calibrate() { System.out.print("C"); }

    default void start() {
        calibrate();
    }

    static void reset() {
        calibrate(); // Line X
    }
}
```

**What happens at Line X?**

- **A.** Compiles successfully.
    
- **B.** Compilation Error: Cannot make a static reference to the non-static method.
    
- **C.** Runtime Exception.
    
- **D.** Prints "C".
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Static vs Instance Context in Interfaces.
> 
> Deep Explanation:
> 
> - **Context:** `reset()` is `static`. It belongs to the interface type, not an instance.
>     
> - **Target:** `calibrate()` is `private` (instance method). It requires an instance (`this`) to run.
>     
> - **The Violation:** You cannot call an instance method from a static context. To fix this, `calibrate` must be declared as `private static void`.
>     

---

### Q17: Autoboxing & NullPointerException

**Topic:** Primitives & Wrappers

Java

```
public class Numbers {
    public static void main(String[] args) {
        Integer val = null;
        if (val < 10) { // Line X
            System.out.println("Less");
        }
    }
}
```

**What is the result?**

- **A.** Prints "Less".
    
- **B.** Nothing happens (Condition is false).
    
- **C.** Runtime NullPointerException.
    
- **D.** Compilation Error.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Unboxing null.
> 
> Deep Explanation:
> 
> - **The Logic:** The operator `<` works on primitives. Java attempts to **Auto-unbox** `val` to compare it with `10`.
>     
> - **The Execution:** The compiler generates `val.intValue() < 10`.
>     
> - **The Crash:** Since `val` is `null`, calling `intValue()` on it throws `NullPointerException`. This is the most dangerous aspect of Autoboxing.
>     

---

### Q18: Stream Execution Order

**Topic:** Streams

Java

```
Stream.of("d2", "a2", "b1", "b3", "c")
    .map(s -> {
        System.out.print("map: " + s + " ");
        return s.toUpperCase();
    })
    .filter(s -> {
        System.out.print("filter: " + s + " ");
        return s.startsWith("A");
    })
    .forEach(s -> System.out.print("forEach: " + s));
```

**What is the behavior?**

- **A.** Maps all elements, then filters all elements.
    
- **B.** Filters first, then maps.
    
- **C.** Processes elements vertically (one by one through the chain).
    
- **D.** Compilation Error.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Vertical Execution (Loop Fusion).
> 
> Deep Explanation:
> 
> - **Horizontal vs Vertical:** Streams do not run horizontally (process all Map, then all Filter). They run **vertically**.
>     
> - **Flow:**
>     
>     - Take "d2" -> Map ("map: d2") -> Filter ("filter: D2") -> Fail.
>         
>     - Take "a2" -> Map ("map: a2") -> Filter ("filter: A2") -> Pass -> ForEach ("forEach: A2").
>         
> - **Optimization:** This vertical execution allows "Short-Circuiting" (stopping early) if needed.
>     

---

### Q19: JDBC ResultSet Types

**Topic:** JDBC

Java

```
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY);
ResultSet rs = stmt.executeQuery("SELECT * FROM Logs");
rs.next();
// .. Another user updates the current row in the DB ..
System.out.println(rs.getString("message"));
```

**What value does rs.getString retrieve?**

- **A.** The new updated value.
    
- **B.** The old original value.
    
- **C.** Throws SQLException.
    
- **D.** It depends on the database driver.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Scroll Insensitive ResultSets.
> 
> Deep Explanation:
> 
> - `TYPE_SCROLL_INSENSITIVE`: This constant tells the DB driver to create a static view (**Snapshot**) of the data at the moment the query was executed.
>     
> - **Behavior:** It is "Insensitive" to changes made by others. The ResultSet holds the original data to ensure consistency during traversal.
>     

---

### Q20: Method References Types

**Topic:** Functional Programming

Java

```
String str = "Hello";
Supplier<String> s = str::toUpperCase; // Line 1
Function<String, String> f = String::toUpperCase; // Line 2
```

**Which statement describes the method references correctly?**

- **A.** Line 1 is "Bound" (Specific Object), Line 2 is "Unbound" (Arbitrary Object).
    
- **B.** Both are Static method references.
    
- **C.** Line 1 compiles, Line 2 does not.
    
- **D.** Line 2 is Bound, Line 1 is Unbound.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: Bound vs Unbound Method References.
> 
> Deep Explanation:
> 
> - **Line 1** (`str::toUpperCase`): This is **Bound**. The method is tied to the specific instance `str`. When `s.get()` is called, it effectively calls `str.toUpperCase()`. No argument is needed.
>     
> - **Line 2** (`String::toUpperCase`): This is **Unbound**. It refers to an instance method via the Class name. It expects the instance to be passed as an argument. That is why it is a `Function<String, String>` (Input String -> Output String), effectively `(x) -> x.toUpperCase()`.
>     

---

### Q21: Module Directive provides

**Topic:** Modules

Java

```
module com.service {
    // Line X
}
```

**Which line correctly declares that this module implements the PayApi interface with the PayPal class?**

- **A.** uses com.api.PayApi;
    
- **B.** provides com.api.PayApi with com.impl.PayPal;
    
- **C.** exports com.impl.PayPal;
    
- **D.** requires com.api.PayApi;
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Service Provider Definition.
> 
> Deep Explanation:
> 
> - **Consumer:** Uses `uses`.
>     
> - **Provider:** Uses `provides [Interface] with [Implementation]`.
>     
> - **API:** Uses `exports`.
>     
> - Option B is the correct syntax for registering a service implementation in `module-info.java`.
>     

---

### Q22: AtomicInteger vs volatile

**Topic:** Concurrency

Java

```
private volatile int counter = 0;

public void increment() {
    counter++; // Line X
}
```

**Is Line X thread-safe?**

- **A.** Yes, because volatile guarantees visibility.
    
- **B.** Yes, ++ is atomic in Java.
    
- **C.** No, volatile does not guarantee atomicity for compound actions.
    
- **D.** No, unless the variable is static.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Volatile limitations.
> 
> Deep Explanation:
> 
> - **Volatile:** Guarantees **Visibility** (memory writes are immediately flushed to main RAM). It does **not** guarantee Atomicity for compound operations.
>     
> - **The Trap:** `counter++` is actually 3 steps: Read, Modify, Write.
>     
> - **Race Condition:** Two threads can read the same value (e.g., 5) simultaneously due to the visibility guarantee, increment it to 6 locally, and write back 6. One update is lost. You need `AtomicInteger` or `synchronized`.
>     

---

### Q23: Inner Class Instantiation

**Topic:** Inner Classes

Java

```
public class Outer {
    class Inner { }

    public static void main(String[] args) {
        // Line X
    }
}
```

**Which line correctly instantiates the Inner class from the static method?**

- **A.** new Inner();
    
- **B.** new Outer.Inner();
    
- **C.** new Outer().new Inner();
    
- **D.** Outer.new Inner();
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Member Inner Class Instantiation.
> 
> Deep Explanation:
> 
> - **Member Inner Class:** It is tied to an instance of the Outer class. It effectively contains a hidden pointer `Outer.this`.
>     
> - **Static Context:** `main` is static. It has no `this`.
>     
> - **The Fix:** You must manually create the Outer instance first (`new Outer()`), and then use that instance to create the Inner instance (`.new Inner()`).
>     

---

### Q24: Functional Interface Object Methods

**Topic:** Functional Interfaces

Java

```
@FunctionalInterface
interface Validator {
    boolean validate(String s);
    String toString();
    boolean equals(Object o);
}
```

**Does this compile?**

- **A.** Yes.
    
- **B.** No, too many abstract methods.
    
- **C.** No, toString cannot be abstract.
    
- **D.** No, @FunctionalInterface is missing parameters.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: The "Object Method" Loophole.
> 
> Deep Explanation:
> 
> - **SAM Rule:** A Functional Interface must have exactly one unique abstract method.
>     
> - **The Loophole:** Methods that match `java.lang.Object` public methods (like `toString`, `equals`, `hashCode`) do **not** count towards this limit.
>     
> - **Why?** Because every implementation will inherit these from `Object` anyway, so they are never truly "unimplemented."
>     
> - **Result:** Only `validate` counts. 1 Method = Valid Functional Interface.
>     

---

### Q25: Serialization Inheritance

**Topic:** I/O & Serialization

Java

```
class A {
    int x = 10;
    A() { x = 20; System.out.print("A"); }
}
class B extends A implements Serializable {
    int y = 30;
    B() { y = 40; System.out.print("B"); }
}
// Code to deserialize an instance of B...
```

**What is printed during deserialization and what is the value of x?**

- **A.** Prints "AB", x=40.
    
- **B.** Prints "A", x=20.
    
- **C.** Prints nothing, x=0.
    
- **D.** Prints "B", x=10.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Serialization & Constructor Execution.
> 
> Deep Explanation:
> 
> - **Serializable Class (B):** Constructors are skipped during deserialization. Data is pulled from the byte stream. "B" is not printed.
>     
> - **Non-Serializable Parent (A):** The JVM has no saved data for A. To initialize A's fields, the JVM **MUST run the no-args constructor** of the first non-serializable parent.
>     
> - **Execution:** `A()` runs. It prints "A" and sets `x = 20`.
>     

---

### Q26: `Double` Check Locking (Singleton)

**Topic:** Design Patterns & Concurrency

Java

```
public class Database {
    private static Database instance;

    private Database() {}

    public static Database getInstance() {
        if (instance == null) {
            synchronized (Database.class) {
                if (instance == null) {
                    instance = new Database();
                }
            }
        }
        return instance;
    }
}
```

**What is the potential flaw in this Singleton implementation?**

- **A.** It compiles but is not thread-safe.
    
- **B.** It is thread-safe but `instance` should be `volatile` to prevent instruction reordering.
    
- **C.** It causes a Deadlock.
    
- **D.** The constructor must be `public`.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: The "Partially Constructed Object" Problem.
> 
> Deep Explanation:
> 
> - **The Code:** This is the famous "Double-Checked Locking" pattern.
>     
> - **The Flaw:** Without `volatile`, the Java Memory Model allows "instruction reordering."
>     
> - **The Scenario:** Thread A enters the `synchronized` block and executes `instance = new Database()`. This involves three steps:
>     
>     1. Allocate memory.
>         
>     2. Initialize the object (run constructor).
>         
>     3. Assign the memory address to `instance`.
>         
> - **The Bug:** The CPU might reorder steps 2 and 3. `instance` becomes non-null (pointing to raw memory) _before_ the constructor finishes. Thread B checks `if (instance == null)`, sees it's not null, and returns a broken, partially initialized object.
>     
> - **The Fix:** `private static volatile Database instance;`.
>     

---

### Q27: `Path.resolve()` Behavior

**Topic:** NIO.2

Java

```
Path p1 = Paths.get("/home/user");
Path p2 = Paths.get("/etc/config");
Path p3 = p1.resolve(p2);
System.out.println(p3);
```

**What is the output?**

- **A.** `/home/user/etc/config`
    
- **B.** `/etc/config`
    
- **C.** `/home/user/config`
    
- **D.** Throws `InvalidPathException`
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Absolute Path Resolution.
> 
> Deep Explanation:
> 
> - **The Method:** `p1.resolve(p2)` tries to join `p2` to the end of `p1`.
>     
> - **The Trap:** If the parameter (`p2`) is an **Absolute Path** (starts with `/` on Linux or `C:` on Windows), the method ignores `p1` entirely and just returns `p2`.
>     
> - **Logic:** You cannot append an absolute path to another path; it replaces the context.
>     

---

### Q28: Immutable Class Design

**Topic:** Encapsulation

Java

```
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = start;
        this.end = end;
    }

    public Date getStart() { return start; }
    public Date getEnd() { return end; }
}
```

**Is this class truly immutable?**

- **A.** Yes, because the class is `final` and fields are `final`.
    
- **B.** No, because `Date` is mutable.
    
- **C.** No, because the constructor is public.
    
- **D.** Yes, because there are no setters.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Mutable Object References in Immutable Classes.
> 
> Deep Explanation:
> 
> - **The Definition:** An immutable object's state cannot change after construction.
>     
> - **The Flaw:** `java.util.Date` is mutable.
>     
> - **The Exploit:**
>     
>     Java
>     
>     ```
>     Date d = new Date();
>     Period p = new Period(d, d);
>     d.setYear(100); // Modifies the internals of 'p' externally!
>     ```
>     
> - **The Fix:** You must perform a **Defensive Copy** in the constructor (`this.start = new Date(start.getTime())`) and in the getters (`return new Date(start.getTime())`).
>     

---

### Q29: Locale & ResourceBundle Fallback

**Topic:** Localization

Plaintext

```
We have the following property files:
1. Messages.properties
2. Messages_fr.properties
3. Messages_fr_CA.properties
The default Locale is Locale.US.
We run: ResourceBundle.getBundle("Messages", Locale.CANADA_FRENCH);
```

**Which file is loaded?**

- **A.** `Messages_fr_CA.properties`
    
- **B.** `Messages_fr.properties`
    
- **C.** `Messages.properties`
    
- **D.** Throws `MissingResourceException`
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: ResourceBundle Search Strategy.
> 
> Deep Explanation:
> 
> - **The Search Order:** Java looks for an exact match first.
>     
>     1. `Messages_fr_CA.properties` (Exact Match? Yes. Stop.)
>         
> - **If it wasn't there:** It would drop the country: `Messages_fr.properties`.
>     
> - **If that wasn't there:** It would try the Default Locale (`en_US` -> `Messages_en_US.properties` -> `Messages_en.properties`).
>     
> - **If all else fails:** It uses the base bundle `Messages.properties`.
>     

---

### Q30: `CopyOnWriteArrayList` Iterator

**Topic:** Concurrency & Collections

Java

```
List<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");

for (String s : list) {
    list.add("C");
    System.out.print(s);
}
System.out.print(list.size());
```

**What is the output?**

- **A.** `AB3` (or `AB4` depending on loop count logic, correct concept is `AB` for output).
    
- **B.** `AB2`
    
- **C.** Throws `ConcurrentModificationException`
    
- **D.** Enters an infinite loop.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: Snapshot Iterators.
> 
> Deep Explanation:
> 
> - **The Class:** `CopyOnWriteArrayList` is designed for high-concurrency read scenarios.
>     
> - **The Magic:** Whenever you modify the list (`add`), it creates a fresh copy of the underlying array.
>     
> - **The Iterator:** The iterator created by the `for-loop` holds a reference to the **original** array (Snapshot) as it existed when the loop started (`["A", "B"]`).
>     
> - **Execution:**
>     
>     - The loop iterates over "A" and "B" only. It does not see "C".
>         
>     - However, the underlying list _is_ modified.
>         
>     - The final size is 3.
>         

---

### Q31: `Map.of` vs `new HashMap`

**Topic:** Collections

Java

```
Map<String, String> map = Map.of("key1", "val1", "key2", null);
```

**What is the result?**

- **A.** Creates a map with a null value.
    
- **B.** Throws `NullPointerException`.
    
- **C.** Throws `IllegalArgumentException`.
    
- **D.** Compiles but ignores the null entry.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Immutable Collection Factories Constraints.
> 
> Deep Explanation:
> 
> - **The Modern Factories:** `List.of`, `Set.of`, `Map.of` (introduced in Java 9).
>     
> - **The Constraints:**
>     
>     1. They are **Immutable**.
>         
>     2. They **do not allow nulls** (keys or values).
>         
> - **The Crash:** `Map.of` checks for nulls immediately and throws `NullPointerException` at runtime.
>     
> - **Contrast:** `new HashMap()` allows one null key and multiple null values.
>     

---

### Q32: Secure Randomness

**Topic:** Security

**Which class should be used to generate a secure token for a session ID?**

- **A.** `java.util.Random`
    
- **B.** `java.security.SecureRandom`
    
- **C.** `java.util.concurrent.ThreadLocalRandom`
    
- **D.** `java.lang.Math.random()`
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: B
> 
> Concept: Cryptographically Strong Randomness.
> 
> Deep Explanation:
> 
> - **Random / Math.random:** These use a "Linear Congruential Generator". It is deterministic. If an attacker knows the "seed" (often just the system time), they can predict the next numbers.
>     
> - **SecureRandom:** Uses entropy from the OS (mouse movements, network noise, etc.) to generate truly unpredictable numbers. It is required for security-sensitive operations like Session IDs, Passwords, and Cryptography keys.
>     

---

### Q33: Casting & `instanceof` Pattern Matching

**Topic:** Data Types

Java

```
Object o = 10; // Integer
if (o instanceof String s) {
    System.out.println(s.length());
} else {
    System.out.println("Not a String");
}
```

**What is the result?**

- **A.** Compilation Error (Incompatible types).
    
- **B.** Throws `ClassCastException`.
    
- **C.** Prints "Not a String".
    
- **D.** Prints `0`.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: instanceof Pattern Matching.
> 
> Deep Explanation:
> 
> - **The Syntax:** `o instanceof String s` checks if `o` is a String.
>     
> - **The Logic:** If true, it casts `o` to `String` and assigns it to `s`, then enters the `if` block.
>     
> - **The Failure:** `o` is an Integer. The check returns `false`.
>     
> - **The Safety:** Unlike a manual cast `(String) o` which would throw `ClassCastException` at runtime, `instanceof` simply returns false safely.
>     

---

### Q34: ForkJoinPool & RecursiveTask

**Topic:** Concurrency

Java

```
class SumTask extends RecursiveTask<Integer> {
    // ... setup code ...
    protected Integer compute() {
        if (workLoad < 10) return process();
        var t1 = new SumTask(part1);
        var t2 = new SumTask(part2);
        t1.fork();
        return t2.compute() + t1.join(); // Line X
    }
}
```

**Why is Line X written as `t2.compute() + t1.join()` instead of `t1.join() + t2.compute()`?**

- **A.** It is a syntax requirement.
    
- **B.** To avoid Deadlock.
    
- **C.** Optimization: It uses the current thread to do work (`t2`) while `t1` runs in the background.
    
- **D.** It makes no difference.
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: C
> 
> Concept: Work Stealing & Optimization.
> 
> Deep Explanation:
> 
> - **Fork:** `t1.fork()` pushes `t1` to the work queue (async).
>     
> - **Compute:** `t2.compute()` runs the second task **immediately on the current thread** (synchronous).
>     
> - **Join:** `t1.join()` waits for the result of `t1`.
>     
> - **The Strategy:** If you called `t1.join()` immediately after forking, the current thread would just sit there waiting (blocking). By calling `t2.compute()` first, the current thread stays busy doing useful work while `t1` is processed by another thread. This maximizes parallelism.
>     

---

### Q35: `Comparator` with `nullsFirst`

**Topic:** Collections & Lambdas

Java

```
Comparator<String> c = Comparator.nullsFirst(Comparator.naturalOrder());
List<String> list = Arrays.asList("B", null, "A");
list.sort(c);
System.out.println(list);
```

**What is the output?**

- **A.** `[null, A, B]`
    
- **B.** `[A, B, null]`
    
- **C.** Throws `NullPointerException`.
    
- **D.** `[B, null, A]` (Order unchanged).
    

> [!SUCCESS]- Click to reveal answer
> 
> Correct Answer: A
> 
> Concept: Sorting with Nulls.
> 
> Deep Explanation:
> 
> - **The Problem:** Standard `compareTo` throws NPE if it encounters null.
>     
> - **The Wrapper:** `Comparator.nullsFirst(...)` wraps another comparator.
>     
> - **The Logic:**
>     
>     1. It checks for nulls. If it sees a null, it puts it at the beginning (because `nullsFirst`).
>         
>     2. If both are non-null, it delegates to the wrapped comparator (`naturalOrder` -> alphabetical).
>         
> - **Result:** `null` comes first, then "A", then "B".
>