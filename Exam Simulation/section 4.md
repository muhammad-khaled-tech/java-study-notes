

---

#### Q1: Stream Lazy Evaluation Trap

```
import java.util.stream.Stream;

public class LazyStream {
    public static void main(String[] args) {
        Stream.of("A", "B", "C")
              .filter(s -> {
                  System.out.print(s);
                  return true;
              });
    }
}
```

**What is the result of executing this code?**
A. ABC
B. A 
C. C 
D. Nothing is printed.

---

#### Q2: Stream Reuse (Illegal State)

```
import java.util.stream.Stream;

public class StreamReuse {
    public static void main(String[] args) {
        Stream<String> s = Stream.of("Java", "Streams");
        s.count();
        long count = s.count();
        System.out.println(count);
    }
}
```

**What is the result?** 
A. 2 
B. 0 
C. Compilation Error. 
D. Throws `IllegalStateException` at runtime.

---

#### Q3: Collectors.groupingBy Return Type

```
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Grouping {
    public static void main(String[] args) {
        // What is the return type of 'result'?
        var result = Stream.of("Apple", "Banana", "Cherry")
                .collect(Collectors.groupingBy(String::length));
    }
}
```

**What is the inferred type of the variable `result`?** 
A. `Map<String, Integer>` 
B. `Map<Integer, String>` 
C. `Map<Integer, List<String>>` 
D. `Map<String, List<Integer>>`

---

#### Q4: Collectors.partitioningBy Logic

```
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Partitioning {
    public static void main(String[] args) {
        var map = Stream.of("A", "BB", "CCC")
                .collect(Collectors.partitioningBy(s -> s.length() > 3));
        System.out.println(map.get(true));
    }
}
```

**What is the output?** 
A. `null` 
B. `[]` (Empty List) 
C. `[CCC]` 
D. The code does not compile.

---

#### Q5: Stream.of() with Arrays Trap

```
import java.util.stream.Stream;

public class StreamTrap {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};
        System.out.println(Stream.of(arr).count());
    }
}
```

**What is the output?** 
A. 1 
B. 3 
C. Compilation Error. 
D. Throws Exception at runtime.

---

#### Q6: Lambda Variable Scope (Effectively Final)

```
import java.util.function.Supplier;

public class LambdaScope {
    public static void main(String[] args) {
        int x = 10;
        x++;
        Supplier<Integer> s = () -> x;
        System.out.println(s.get());
    }
}
```

**What is the result?** 
A. 10 
B. 11 
C. Compilation Error. 
D. Runtime Exception.

---

#### Q7: TreeSet without Comparable

```
import java.util.TreeSet;

class Dog {
    int size;
    Dog(int s) { size = s; }
}

public class SetTest {
    public static void main(String[] args) {
        TreeSet<Dog> tree = new TreeSet<>();
        tree.add(new Dog(1));
        tree.add(new Dog(2));
        System.out.println(tree.size());
    }
}
```

**What is the result?** 
A. 2 
B. 1 
C. Compilation Error. 
D. Throws `ClassCastException` at runtime.

---

#### Q8: Reduce Identity Trap

```
import java.util.stream.Stream;

public class ReduceMath {
    public static void main(String[] args) {
        Integer val = Stream.of(1, 2, 3)
                            .reduce(0, (a, b) -> a * b);
        System.out.println(val);
    }
}
```

**What is the output?** 
A. 6 '
B. 0 
C. 1 
D. 5

---

