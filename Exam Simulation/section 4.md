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


---



 
