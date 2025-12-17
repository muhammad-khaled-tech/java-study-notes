

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

#### Q9: flatMap Signature

```
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class FlatMapTest {
    public static void main(String[] args) {
        List<List<String>> list = Arrays.asList(
            Arrays.asList("a"),
            Arrays.asList("b")
        );

        System.out.println(
            list.stream()
                .flatMap(l -> l.stream())
                .count()
        );
    }
}
```

**What is the output?** 
A. 1 B. 2 C. Compilation Error. D. Stream<List>

---

#### Q10: Collectors.toMap Duplicate Key

```
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class MapDuplicate {
    public static void main(String[] args) {
        Stream.of("A", "A")
              .collect(Collectors.toMap(k -> k, v -> v));
    }
}
```

**What is the result?** A. Map containing {"A"="A"} B. Map containing {"A"="A", "A"="A"} C. Compilation Error. D. Throws `IllegalStateException` at runtime.

---

#### Q11: Map Mutable Key Trap

```
import java.util.HashMap;

class Key {
    int id;
    Key(int id) { this.id = id; }
    public int hashCode() { return id; }
}

public class MapKey {
    public static void main(String[] args) {
        HashMap<Key, String> map = new HashMap<>();
        Key k = new Key(1);
        map.put(k, "Value");
        k.id = 2; // Mutating key
        System.out.println(map.get(k));
    }
}
```

**What is the output?** A. Value B. null C. Compilation Error. D. Runtime Exception.

---

#### Q12: Map.merge Function

```
import java.util.HashMap;
import java.util.Map;

public class MapMerge {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("key", "value");
        map.merge("key", "new", (v1, v2) -> null);
        System.out.println(map);
    }
}
```

**What is the output?** A. {key=null} B. {key=value} C. {key=new} D. {} (Empty Map)

---

#### Q13: Stream.iterate Logic

```
import java.util.stream.Stream;

public class IterateTest {
    public static void main(String[] args) {
        Stream.iterate(1, x -> x + 1)
              .limit(3)
              .filter(x -> x > 1)
              .forEach(System.out::print);
    }
}
```

**What is the output?** A. 123 B. 23 C. 12 D. 234

---

#### Q14: Set Duplicates with Mutable Objects

```
import java.util.HashSet;
import java.util.ArrayList;

public class SetMutable {
    public static void main(String[] args) {
        HashSet<ArrayList<Integer>> set = new HashSet<>();
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        set.add(list);
        list.add(2); // Mutating list inside set
        set.add(list);
        System.out.println(set.size());
    }
}
```

**What is the output?** A. 1 B. 2 C. Compilation Error. D. Throws Exception.

---

#### Q15: Comparator Implementation Syntax

```
import java.util.Comparator;

public class CompSyntax {
    public static void main(String[] args) {
        // Does this compile?
        Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
        System.out.println(c.compare("A", "B"));
    }
}
```

**What is the result?** A. -1 B. 1 C. Compilation Error. D. 0

---

#### Q16: Parallel Stream & Stateful Lambda (Race Condition)

```
import java.util.ArrayList;
import java.util.List;
import java.util.stream.IntStream;

public class RaceCond {
    public static void main(String[] args) {
        List<Integer> data = new ArrayList<>();
        IntStream.range(0, 100).parallel().forEach(s -> data.add(s));
        System.out.println(data.size());
    }
}
```

**What is the result?** A. 100 B. Compilation Error. C. Unpredictable (less than 100 or Exception). D. Always throws IndexOutOfBoundsException.

---

#### Q17: Stream.allMatch on Empty Stream

```
import java.util.stream.Stream;

public class VacuousTruth {
    public static void main(String[] args) {
        boolean result = Stream.empty().allMatch(s -> false);
        System.out.println(result);
    }
}
```

**What is the output?** A. true B. false C. null D. Throws Exception.

---

#### Q18: Optional.ofNullable vs of

```
import java.util.Optional;

public class OptionalTest {
    public static void main(String[] args) {
        Optional<String> o = Optional.of(null);
        System.out.println(o.isPresent());
    }
}
```

**What is the result?** A. false B. true C. Compilation Error. D. Throws `NullPointerException`.

---

#### Q19: Stream.sorted() without Comparable

```
import java.util.stream.Stream;

class Item {}

public class SortTest {
    public static void main(String[] args) {
        Stream.of(new Item(), new Item())
              .sorted()
              .count();
    }
}
```

**What is the result?** A. 2 B. 0 C. Compilation Error. D. Throws `ClassCastException`.

---

#### Q20: List vs Stream Remove Logic

```
import java.util.ArrayList;
import java.util.List;

public class RemoveLogic {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A"); list.add("B");

        list.stream().filter(s -> s.equals("A"));

        System.out.println(list.size());
    }
}
```

**What is the output?** A. 1 B. 2 C. 0 D. Compilation Error.