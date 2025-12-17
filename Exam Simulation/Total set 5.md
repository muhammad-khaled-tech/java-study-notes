
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
>