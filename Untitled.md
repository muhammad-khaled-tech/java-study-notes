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

