## 🧠 امتحان Java – Stream API & Collections Framework

### مستوى **Senior / Advanced**

_(منسّق بالكامل وجاهز للاستخدام في **Obsidian**)_

---

> [!note]  
> الامتحان ده مركز على **التفاصيل الدقيقة (Edge Cases)**  
> وبيختبر فهمك الحقيقي لـ:
> 
> - **Stream API**
>     
> - **Collections Framework**
>     
> - **Lazy Evaluation**
>     
> - **Collectors**
>     
> - **Lambda Restrictions**
>     
> 
> **الإجابات البديهية غالبًا غلط** 👀  
> فكّر دايمًا: _Compile Time ولا Runtime؟_ و _JVM behavior هيبقى إيه؟_

---

## 🧪 Q1: Stream Lazy Evaluation Trap

```java
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

- A) ABC
    
- B) A
    
- C) C
    
- D) Nothing is printed
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — Nothing is printed**
> 
> **🧠 تحليل السينيور:**  
> أي **Intermediate Operation** زي `filter` هي **Lazy**.  
> من غير **Terminal Operation** (`forEach`, `count`, `collect`)  
> الـ Stream Pipeline **مش بيبدأ أصلاً**.

---

## 🧪 Q2: Stream Reuse (Illegal State)

```java
Stream<String> s = Stream.of("Java", "Streams");
s.count();
long count = s.count();
System.out.println(count);
```

- A) 2
    
- B) 0
    
- C) Compilation Error
    
- D) Throws `IllegalStateException`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — IllegalStateException**
> 
> **🧠 تحليل السينيور:**  
> الـ Stream **One-time use**.  
> أول `count()` قفل الـ Stream.  
> أي محاولة إعادة استخدام = Runtime Exception.

---

## 🧪 Q3: Collectors.groupingBy Return Type

```java
var result = Stream.of("Apple", "Banana", "Cherry")
        .collect(Collectors.groupingBy(String::length));
```

- A) `Map<String, Integer>`
    
- B) `Map<Integer, String>`
    
- C) `Map<Integer, List<String>>`
    
- D) `Map<String, List<Integer>>`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: C**
> 
> **🧠 تحليل السينيور:**
> 
> - Key = نتيجة الـ classifier → `Integer`
>     
> - Value = `List<T>` افتراضيًا
>     
> 
> ✔️ `Map<Integer, List<String>>`

---

## 🧪 Q4: Collectors.partitioningBy Logic

```java
var map = Stream.of("A", "BB", "CCC")
        .collect(Collectors.partitioningBy(s -> s.length() > 3));
System.out.println(map.get(true));
```

- A) null
    
- B) `[]`
    
- C) `[CCC]`
    
- D) Compilation Error
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — Empty List**
> 
> **🧠 تحليل السينيور:**  
> `partitioningBy` دايمًا بيرجع  
> `Map<Boolean, List<T>>` بمفتاحين `true` و `false`  
> حتى لو القائمة فاضية.

---

## 🧪 Q5: Stream.of() with Primitive Array Trap

```java
int[] arr = {1, 2, 3};
System.out.println(Stream.of(arr).count());
```

- A) 1
    
- B) 3
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: A — 1**
> 
> **🧠 تحليل السينيور:**  
> `Stream.of(arr)`  
> → Stream فيه **عنصر واحد** (الـ array نفسها).
> 
> الحل الصح:
> 
> - `Arrays.stream(arr)`
>     
> - `IntStream.of(arr)`
>     

---

## 🧪 Q6: Lambda Variable Scope (Effectively Final)

```java
int x = 10;
x++;
Supplier<Integer> s = () -> x;
```

- A) 10
    
- B) 11
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: C — Compilation Error**
> 
> **🧠 تحليل السينيور:**  
> أي Local Variable داخل Lambda  
> لازم يكون **Effectively Final**.  
> `x++` كسر القاعدة → Compile-time failure.

---

## 🧪 Q7: TreeSet without Comparable

```java
TreeSet<Dog> tree = new TreeSet<>();
tree.add(new Dog(1));
tree.add(new Dog(2));
```

- A) 2
    
- B) 1
    
- C) Compilation Error
    
- D) `ClassCastException`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D**
> 
> **🧠 تحليل السينيور:**  
> `TreeSet` لازم:
> 
> - `Comparable`
>     
> - أو `Comparator`
>     
> 
> غير كده → Runtime crash.

---

## 🧪 Q8: Reduce Identity Trap

```java
Integer val = Stream.of(1, 2, 3)
        .reduce(0, (a, b) -> a * b);
```

- A) 6
    
- B) 0
    
- C) 1
    
- D) 5
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — 0**
> 
> **🧠 تحليل السينيور:**  
> Identity value غلط.  
> في الضرب:
> 
> - Identity = `1`
>     
> 
> 0 × أي حاجة = 0 دائمًا.

---
تمام 👌  
معاك حق، من **سؤال 9** كان في **لخبطة تنسيق + اختصار في الشرح**.  
دي **إعادة تنسيق وتصحيح كاملة من Q9 → Q20** بأسلوب **Obsidian نظيف 100%** ومن غير ما أغير فكرة الأسئلة.

---

## 🧪 Q9: `flatMap` Signature & Behavior

```java
import java.util.Arrays;
import java.util.List;

public class FlatMapTest {
    public static void main(String[] args) {
        List<List<String>> list = Arrays.asList(
            Arrays.asList("a"),
            Arrays.asList("b")
        );

        long count = list.stream()
                .flatMap(l -> l.stream())
                .count();

        System.out.println(count);
    }
}
```

**What is the output?**

- A) 1
    
- B) 2
    
- C) Compilation Error
    
- D) `Stream<List<String>>`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — 2**
> 
> **🧠 تحليل السينيور:**  
> `flatMap` بتحول:
> 
> - `Stream<List<String>>`
>     
> - إلى `Stream<String>`
>     
> 
> العناصر: `"a"`, `"b"` → العدد = 2.

---

## 🧪 Q10: `Collectors.toMap` Duplicate Key Trap

```java
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class MapDuplicate {
    public static void main(String[] args) {
        Stream.of("A", "A")
              .collect(Collectors.toMap(k -> k, v -> v));
    }
}
```

**What is the result?**

- A) `{A=A}`
    
- B) `{A=A, A=A}`
    
- C) Compilation Error
    
- D) Throws `IllegalStateException`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — IllegalStateException**
> 
> **🧠 تحليل السينيور:**  
> `toMap` بدون **merge function**
> 
> - duplicate keys  
>     = runtime crash.
>     

---

## 🧪 Q11: Mutable Key Trap in `HashMap`

```java
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
        k.id = 2;

        System.out.println(map.get(k));
    }
}
```

**What is the output?**

- A) Value
    
- B) null
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — null**
> 
> **🧠 تحليل السينيور:**  
> تغيير قيمة الـ key → تغيير `hashCode()`
> 
> الـ Map تدور في bucket غلط  
> → القيمة تضيع
> 
> ❗ **Keys must be immutable**

---

## 🧪 Q12: `Map.merge()` Returning `null`

```java
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

**What is the output?**

- A) `{key=null}`
    
- B) `{key=value}`
    
- C) `{key=new}`
    
- D) `{}`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — Empty Map**
> 
> **🧠 تحليل السينيور:**  
> في `merge`:
> 
> - لو الـ remapping function رجعت `null`
>     
> - الـ entry بيتشال بالكامل.
>     

---

## 🧪 Q13: `Stream.iterate` Execution Order

```java
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

**What is the output?**

- A) 123
    
- B) 23
    
- C) 12
    
- D) 234
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — 23**
> 
> **🧠 تحليل السينيور:**
> 
> - `iterate` → 1, 2, 3
>     
> - `limit(3)` → 1, 2, 3
>     
> - `filter(x > 1)` → 2, 3
>     

---

## 🧪 Q14: Mutable Object inside `Set`

```java
import java.util.HashSet;
import java.util.ArrayList;

public class SetMutable {
    public static void main(String[] args) {
        HashSet<ArrayList<Integer>> set = new HashSet<>();
        ArrayList<Integer> list = new ArrayList<>();

        list.add(1);
        set.add(list);

        list.add(2);
        set.add(list);

        System.out.println(set.size());
    }
}
```

**What is the output?**

- A) 1
    
- B) 2
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: A — 1**
> 
> **🧠 تحليل السينيور:**  
> نفس الـ reference
> 
> الـ Set مش هتضيف نفس العنصر مرتين  
> حتى لو محتواه اتغير.

---

## 🧪 Q15: Comparator Lambda Syntax

```java
import java.util.Comparator;

public class CompSyntax {
    public static void main(String[] args) {
        Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
        System.out.println(c.compare("A", "B"));
    }
}
```

**What is the output?**

- A) -1
    
- B) 1
    
- C) Compilation Error
    
- D) 0
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: A — -1**
> 
> `"A"` أصغر من `"B"` lexicographically.

---

## 🧪 Q16: Parallel Stream + Stateful Lambda

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.IntStream;

public class RaceCond {
    public static void main(String[] args) {
        List<Integer> data = new ArrayList<>();

        IntStream.range(0, 100)
                 .parallel()
                 .forEach(i -> data.add(i));

        System.out.println(data.size());
    }
}
```

**What is the result?**

- A) 100
    
- B) Compilation Error
    
- C) Unpredictable
    
- D) Always throws Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: C — Unpredictable**
> 
> **🧠 تحليل السينيور:**  
> `ArrayList` مش Thread-safe
> 
> Parallel mutation = race condition  
> (data loss أو exception).

---

## 🧪 Q17: `allMatch` on Empty Stream

```java
boolean result = Stream.empty().allMatch(s -> false);
System.out.println(result);
```

**What is the output?**

- A) true
    
- B) false
    
- C) null
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: A — true**
> 
> **🧠 Vacuous Truth:**  
> مفيش عنصر بيكسر الشرط → الشرط متحقق.

---

## 🧪 Q18: `Optional.of(null)` Trap

```java
import java.util.Optional;

public class OptionalTest {
    public static void main(String[] args) {
        Optional<String> o = Optional.of(null);
        System.out.println(o.isPresent());
    }
}
```

**What is the result?**

- A) false
    
- B) true
    
- C) Compilation Error
    
- D) `NullPointerException`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — NPE**
> 
> استخدم `Optional.ofNullable()` لو فيه احتمال `null`.

---

## 🧪 Q19: `Stream.sorted()` without `Comparable`

```java
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

**What is the result?**

- A) 2
    
- B) 0
    
- C) Compilation Error
    
- D) `ClassCastException`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — ClassCastException**
> 
> `sorted()` يفترض `Comparable`  
> أو `Comparator`.

---

## 🧪 Q20: Stream Does NOT Modify Source Collection

```java
import java.util.ArrayList;
import java.util.List;

public class RemoveLogic {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");

        list.stream().filter(s -> s.equals("A"));

        System.out.println(list.size());
    }
}
```

**What is the output?**

- A) 1
    
- B) 2
    
- C) 0
    
- D) Compilation Error
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B — 2**
> 
> **🧠 تحليل السينيور:**  
> Streams **لا تعدل المصدر**
> 
> لو عايز حذف:
> 
> ```java
> list.removeIf(s -> s.equals("A"));
> ```

---

> [!summary]  
> كده من **Q9 → Q20**
> 
> - التنسيق مظبوط
>     
> - الترقيم سليم
>     
> - الشرح Senior-level
>     
> - جاهز 100% لـ **Obsidian**
>     
> 
> لو حابب أحوله:
> 
> - 🧪 Mock Exam
>     
> - 📘 Study Notes
>     
> - 🎯 Interview Traps Sheet
>     

قولّي 👍
