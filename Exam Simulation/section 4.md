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
    

**الإجابة الصحيحة هي: B. 0**

### 💡 ليه؟ (Deep Explanation)

السؤال ده فخ مشهور جداً بيعتمد على فهمك لحاجة اسمها **"Identity Value"** (القيمة المحايدة) في دالة `reduce`.

تعال نمشي ورا الكود خطوة بخطوة كأننا "Debugger":

1. البداية (The Identity):
    
    أنت بدأت الـ reduce بقيمة ابتدائية 0. دي أول قيمة بتمسكها في إيدك قبل ما تضرب أي حاجة.
    
2. التنفيذ (Execution Trace):
    
    الدالة بتاعتك هي الضرب (a * b).
    
    - **الخطوة 1:** اضرب القيمة الابتدائية (`0`) في أول رقم (`1`).
        
        - `0 * 1 = 0` (النتيجة المتراكمة بقت 0).
            
    - **الخطوة 2:** خد النتيجة (`0`) واضربها في الرقم التاني (`2`).
        
        - `0 * 2 = 0` (لسة 0).
            
    - **الخطوة 3:** خد النتيجة (`0`) واضربها في الرقم التالت (`3`).
        
        - `0 * 3 = 0`.
            

### 🧠 الزتونة (The Concept):

عشان عملية الـ `reduce` تطلع نتيجة صح، لازم تختار **Identity** مناسب للعملية الحسابية:

- **في الجمع (+):** المحايد هو **`0`** (لأن `x + 0 = x`).
    
- **في الضرب (*):** المحايد هو **`1`** (لأن `x * 1 = x`).
    

**الغلطة هنا:** إنك استخدمت المحايد بتاع الجمع (`0`) مع عملية ضرب. وأي حاجة تضرب في صفر بتبقى صفر.

✅ **التصحيح:** لو عايز النتيجة تطلع **6**، لازم تغير الكود لـ:

Java

```
.reduce(1, (a, b) -> a * b); // ابدأ بـ 1 مش 0
``` 

الغرض الأساسي من `reduce` في كلمتين هو: **"التجميع"** أو **"التقليص"**.

هي عملية بتاخد **مجموعة عناصر** (Stream) وبتفضل تطبق عليهم معادلة معينة لحد ما **"تصفصفهم"** في الآخر على **قيمة واحدة بس**.

عشان تفهمها صح، تخيلها زي **"الحصالة"**:

### 1. إزاي بتشتغل؟ (The Mechanism)

الـ `reduce` محتاجة حاجتين أساسيتين عشان تشتغل:

1. **قيمة البداية (Identity):** دي الحاجة اللي بتبدأ بيها (زي ما تكون الحصالة فيها 0 جنيه، أو 1 لو ضرب).
    
2. **المعادلة (Accumulator):** دي الطريقة اللي "بتجمع" بيها القديم على الجديد.
    

### 2. أمثلة عملية (عشان تثبت):

#### أ) تجميع أرقام (Sum)

عايز تجمع شوية أرقام `1, 2, 3`.

- **البداية:** `0`
    
- **المعادلة:** `(القديم + الجديد)`
    

Java

```
int sum = Stream.of(1, 2, 3)
                .reduce(0, (acc, num) -> acc + num);
// النتيجة: 6
```

_(هنا `acc` هو الحصالة، و `num` هو الرقم اللي عليه الدور)._

#### ب) تجميع نص (Concatenation)

عايز تلزق حروف في بعض `A, B, C`.

- **البداية:** `""` (نص فاضي)
    
- **المعادلة:** `(النص القديم + الحرف الجديد)`
    

Java

```
String word = Stream.of("A", "B", "C")
                    .reduce("", (s1, s2) -> s1 + s2);
// النتيجة: "ABC"
```

#### ج) أكبر رقم (Max)

عايز تطلع أكبر رقم فيهم.

- **المعادلة:** "قارن القديم بالجديد، وخد الكبير".
    

Java

```
int max = Stream.of(5, 1, 9, 3)
                .reduce(0, (a, b) -> a > b ? a : b);
// النتيجة: 9
```

---

### ⚠️ تكة للامتحان (The Exam Tip):

الـ `reduce` ليها شكلين في الكتابة:

1. **شكل بـ Identity (زي الأمثلة فوق):**
    
    - بترجع القيمة علطول (`int`, `String`...).
        
    - مستحيل ترجع `null` (لأن لو الـ Stream فاضي، هترجع الـ Identity).
        
2. **شكل من غير Identity:**
    
    - زي كده: `.reduce((a, b) -> a + b)`
        
    - دي بترجع **`Optional`**.
        
    - **ليه؟** عشان لو الـ Stream فاضي، هي مش عارفة تبدأ بإيه، فممكن ترجع "ولا حاجة" (Empty Optional).
        

الخلاصة:

reduce = Many elements ➡️ One result.

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
    

**الإجابة الصحيحة هي: D. Throws `IllegalStateException`**

### 💡 الشرح التفصيلي (Deep Explanation)

ده فخ كلاسيكي في الـ Streams API.

1. القاعدة الصارمة (The Strict Rule):
    
    دالة Collectors.toMap(keyMapper, valueMapper) في شكلها البسيط (اللي بتاخد 2 parameters بس) بتفترض افتراض حاسم: "ممنوع تكرار المفاتيح (Duplicate Keys)".
    
2. **سيناريو الكارثة:**
    
    - الستريم بيبدأ بالعنصر الأول `"A"`. الـ Map بتسجله: `{Key="A", Value="A"}`.
        
    - الستريم بيوصل للعنصر التاني `"A"`. الـ Mapper بيطلع Key هو `"A"`.
        
    - الـ Map بتكتشف إن المفتاح ده موجود بالفعل! 😱
        
    - بما إنك محددتش "طريقة لحل النزاع" (Merge Function)، الجافا بتعتبر ده خطأ في البيانات وبترمي `IllegalStateException: Duplicate key A`.
        
3. ✅ الحل (The Fix):
    
    لو عايز الكود يشتغل، لازم تستخدم الـ Overload الثالث لـ toMap اللي بياخد BinaryOperator لحل النزاع:
    
    Java
    
    ```
    .collect(Collectors.toMap(
        k -> k, 
        v -> v, 
        (existingValue, newValue) -> existingValue // لو لقيت تكرار، احتفظ بالقديم
    ));
    ```
    
    في الحالة دي، النتيجة هتكون `{A=A}`.
    


---

### 🟢 Q11: Mutable Key Trap in `HashMap`
**Topic:** Collections (Hashing)

```java
import java.util.HashMap;

class Key {
    int id;
    Key(int id) { this.id = id; }
    public int hashCode() { return id; } // يعتمد على id المتغير
}

public class MapKey {
    public static void main(String[] args) {
        HashMap<Key, String> map = new HashMap<>();
        Key k = new Key(1);

        map.put(k, "Value"); // (1) اتحطت في Bucket بناءً على id=1
        k.id = 2;            // (2) غيرنا الـ id، فالـ HashCode اتغير!

        System.out.println(map.get(k)); // (3) بنبحث بالمفتاح المعدل
    }
}
````

**What is the output?**

- A) Value
    
- B) null
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B — null
> 
> 🧠 تحليل السينيور:
> 
> دي غلطة "المبتدئين" القاتلة في الـ HashMaps.
> 
> 1. لما عملت `put`، الجافا حسبت الـ HashCode (كان 1) وحطت القيمة في الـ Bucket المناسب للرقم ده.
>     
> 2. لما أنت غيرت `k.id = 2`، أنت غيرت الـ HashCode بتاع المفتاح وهو جوه الـ Map!
>     
> 3. لما جيت تعمل `get`، الجافا حسبت الـ HashCode الجديد (بقى 2)، وراحت تدور في Bucket تاني خالص غير اللي البيانات متخزنة فيه.
>     
> 
> **💡 الزتونة:** الـ Keys في الـ HashMap لازم تكون **Immutable** (غير قابلة للتغيير)، عشان كده بنحب نستخدم `String` او `Integer` كمفاتيح.

---

### 🟢 Q12: `Map.merge()` Returning `null`

**Topic:** Collections (Map API)



```Java
import java.util.HashMap;
import java.util.Map;

public class MapMerge {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("key", "value");

        // الـ Remapping Function بترجع null
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: D — Empty Map {}
> 
> 🧠 تحليل السينيور:
> 
> دالة merge في الجافا ليها سلوك سحري مع الـ null.
> 
> القاعدة بتقول: لو الـ Function اللي أنت باعتها عشان تدمج القيمتين (القديمة والجديدة) قررت ترجع null، دي إشارة للـ Map إنها تحذف الـ Entry ده تماماً، مش تحط قيمته بـ null.
> 
> **💡 الزتونة:** `return null` inside merge = **Delete Key**.

---

### 🟢 Q13: `Stream.iterate` Execution Order

**Topic:** Streams



```Java
import java.util.stream.Stream;

public class IterateTest {
    public static void main(String[] args) {
        Stream.iterate(1, x -> x + 1) // 1, 2, 3, 4...
              .limit(3)               // 1, 2, 3 (وقفنا هنا)
              .filter(x -> x > 1)     // 1 طارت، اتبقى 2, 3
              .forEach(System.out::print);
    }
}
```

**What is the output?**

- A) 123
    
- B) 23
    
- C) 12
    
- D) 234
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B — 23
> 
> 🧠 تحليل السينيور:
> 
> لازم تمشي ورا الـ Pipeline خطوة بخطوة:
> 
> 1. `iterate`: بدأ يعد 1, 2, 3...
>     
> 2. `limit(3)`: دي "قصت" الشريط، وأخدت أول 3 أرقام بس (1, 2, 3).
>     
> 3. `filter(x > 1)`: دي "مصفاة"، عدت الـ 2 والـ 3، ومنعت الـ 1.
>     
> 4. `forEach`: طبعت اللي اتبقى.
>     

---

### 🟢 Q14: Mutable Object inside `Set`

**Topic:** Collections (Set)

Java

```Java
import java.util.HashSet;
import java.util.ArrayList;

public class SetMutable {
    public static void main(String[] args) {
        HashSet<ArrayList<Integer>> set = new HashSet<>();
        ArrayList<Integer> list = new ArrayList<>();

        list.add(1);
        set.add(list); // ضفنا الليستة وهي فيها [1]

        list.add(2);   // عدلنا نفس الليستة، بقت [1, 2]
        set.add(list); // بنحاول نضيف نفس الأوبجيكت تاني

        System.out.println(set.size());
    }
}
```

**What is the output?**

- A) 1
    
- B) 2
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: A — 1
> 
> 🧠 تحليل السينيور:
> 
> الـ Set بتمنع التكرار. هنا أنت بتحاول تضيف نفس الـ Object Reference مرتين.
> 
> صحيح محتوى الليستة اتغير، لكن هو في الآخر نفس "الكائن" (Pointer) في الميموري. الـ Set لما جيت تضيفه المرة التانية، بصت لقت إن الـ Reference ده عندها أصلاً، فرفضت الإضافة.
> 
> **💡 تنويه:** اللعب في الـ HashCode بتاع أوبجيكت جوه Set (زي ما عملنا بتغيير محتوى الليستة) ده خطر جداً وممكن يخلي الـ Set تتصرف بغرابة، بس في الحالة دي الحجم هيفضل 1.

---

### 🟢 Q15: Comparator Lambda Syntax

**Topic:** Lambda & Comparator

```Java
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: A — -1
> 
> 🧠 تحليل السينيور:
> 
> دالة compareTo في الـ String بتمشي بترتيب القاموس (Lexicographical).
> 
> - لو الأول (`A`) **أصغر** من التاني (`B`) ← النتيجة **سالبة**.
>     
> - لو بيساويه ← صفر.
>     
> - لو أكبر ← موجبة.
>     
>     بما إن A بتيجي قبل B، فالنتيجة -1.
>     

---

### 🟢 Q16: Parallel Stream + Stateful Lambda

**Topic:** Concurrency & Streams

Java

```Java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.IntStream;

public class RaceCond {
    public static void main(String[] args) {
        List<Integer> data = new ArrayList<>();

        IntStream.range(0, 100)
                 .parallel() // شغلنا التيربو (Multi-threading)
                 .forEach(i -> data.add(i)); // كارثة هنا!

        System.out.println(data.size());
    }
}
```

**What is the result?**

- A) 100
    
- B) Compilation Error
    
- C) Unpredictable
    
- D) Always throws Exception
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: C — Unpredictable
> 
> 🧠 تحليل السينيور:
> 
> أنت هنا بتعمل جريمة برمجية! 😅
> 
> 1. `ArrayList` كلاس **Not Thread-Safe**. يعني مبيعرفش يحمي نفسه لما كذا Thread يدخلوا عليه في نفس الوقت.
>     
> 2. لما شغلت `.parallel()`، فيه كذا Thread بيحاولوا يعملوا `add` في نفس اللحظة.
>     
> 3. النتيجة: Race Condition. ممكن عنصرين يتكتبوا فوق بعض، ممكن الـ Index يضرب، ممكن يرمي Exception، وممكن (بالحظ) تطلع 100. عشان كده الإجابة "غير متوقعة".
>     

---

### 🟢 Q17: `allMatch` on Empty Stream

**Topic:** Streams

Java

```Java
boolean result = Stream.empty().allMatch(s -> false);
System.out.println(result);
```

**What is the output?**

- A) true
    
- B) false
    
- C) null
    
- D) Runtime Exception
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: A — true
> 
> 🧠 تحليل السينيور:
> 
> دي قاعدة منطقية في علم الرياضيات اسمها Vacuous Truth (الحقيقة الفراغية).
> 
> دالة allMatch بتسأل: "هل فيه أي عنصر كسر القاعدة؟".
> 
> بما إن الـ Stream فاضي، مفيش ولا عنصر كسر القاعدة، يبقى الإجابة "نعم، الكل موافق" (لأنه مفيش حد أصلاً).
> 
> **💡 خد بالك:** `anyMatch` على ستريم فاضي بترجع `false`.

---

### 🟢 Q18: `Optional.of(null)` Trap

**Topic:** Optionals



```Java
import java.util.Optional;

public class OptionalTest {
    public static void main(String[] args) {
        Optional<String> o = Optional.of(null); // الفخ هنا
        System.out.println(o.isPresent());
    }
}
```

**What is the result?**

- A) false
    
- B) true
    
- C) Compilation Error
    
- D) `NullPointerException`
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: D — NullPointerException
> 
> 🧠 تحليل السينيور:
> 
> الفرق بين of و ofNullable بيوقع ناس كتير:
> 
> - `Optional.of(val)`: بتقول للجافا "أنا متأكد إن القيمة دي مش null". فلو طلعت null، الجافا بتعاقبك بـ NPE في وشك فوراً.
>     
> - `Optional.ofNullable(val)`: دي "الدبلوماسية"، لو null هتعمل Optional فاضي، ولو مش null هتحط القيمة.
>     

---

### 🟢 Q19: `Stream.sorted()` without `Comparable`

**Topic:** Streams & Sorting



```Java
import java.util.stream.Stream;

class Item {} // ولا هو Comparable ولا نيلة

public class SortTest {
    public static void main(String[] args) {
        Stream.of(new Item(), new Item())
              .sorted() // رتب يا باشا
              .count();
    }
}
```

**What is the result?**

- A) 2
    
- B) 0
    
- C) Compilation Error
    
- D) `ClassCastException`
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: D — ClassCastException
> 
> 🧠 تحليل السينيور:
> 
> دالة sorted() اللي من غير باراميترز بتفترض افتراض بريء: إن الأوبجيكتس اللي جاية لها "بتعرف تتقارن ببعض" (يعني بتعمل implement لـ Comparable).
> 
> كلاس Item ده كلاس "أبيض"، مفيهوش أي منطق للمقارنة. فالجافا بتيجي ترتب، مش عارفة مين قبل مين، فبترمي ClassCastException في الر runtime.

---

### 🟢 Q20: Stream Does NOT Modify Source Collection

**Topic:** Streams Concept



```Java
import java.util.ArrayList;
import java.util.List;

public class RemoveLogic {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");

        // بنفلتر الـ Stream، بس مش بنعمل حاجة بالنتيجة
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B — 2
> 
> 🧠 تحليل السينيور:
> 
> القاعدة الذهبية للـ Streams: Streams do NOT modify the source.
> 
> الـ Stream عامل زي "النظارة" أو "خط إنتاج"، بيوريك البيانات بشكل معين أو بيطلع بيانات جديدة، لكنه مبيلمسش الليستة الأصلية list.
> 
> عشان تحذف من الليستة، لازم تستخدم list.removeIf(...) أو تجمع النتيجة في ليستة جديدة.

---



 
