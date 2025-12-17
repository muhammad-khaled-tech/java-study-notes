
# 🥋 Set 1 — Final Boss Stage (OCP / Senior Java)

> [!danger]  
> **Level:** Final Boss ☠️  
> **Scope:** Core Libraries + Modern Java (11 / 17)  
> **Focus:** JVM Behavior — Parallelism — Modules — NIO.2 — Records — Localization
> 
> الأسئلة دي معمولة عشان تكشف أي فجوة صغيرة في الفهم. مفيش حفظ هنا، كله **Reasoning**.

---

## 🧪 Question 1: Parallel Stream Reduction — Identity Trap

```java
var data = List.of(1, 2, 3);
int result = data.parallelStream()
    .reduce(5, (a, b) -> a + b, (a, b) -> a + b);
System.out.println(result);
```

**What is the result?**

- A) 11
    
- B) 6
    
- C) 21
    
- D) 16
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: C — 21**
> 
> **🧠 Surgical Analysis:**
> 
> - `identity = 5` ❌ مش Neutral للجمع
>     
> - في Parallel Stream، الـ identity **بيتكرر مع كل Partition**
>     
> 
> **تقسيم افتراضي:**
> 
> - Thread 1 → `5 + 1 = 6`
>     
> - Thread 2 → `5 + 2 = 7`
>     
> - Thread 3 → `5 + 3 = 8`
>     
> 
> **Combiner:** `6 + 7 + 8 = 21`
> 
> ⚠️ **Rule:**  
> Parallel reduce = identity لازم يكون Neutral  
> (`0` للجمع – `1` للضرب)

---

## 🧪 Question 2: `ReentrantLock` — Double Unlock Trap

```java
Lock lock = new ReentrantLock();
try {
    lock.lock();
    System.out.print("Locked ");
    throw new RuntimeException();
} catch (Exception e) {
    System.out.print("Error ");
    lock.unlock(); // Line X
} finally {
    System.out.print("Finally ");
    lock.unlock(); // Line Y
}
```

**What is the result?**

- A) Locked Error Finally
    
- B) Locked Error Finally + Exception
    
- C) Locked Error Finally + IllegalMonitorStateException
    
- D) Locked Error + IllegalMonitorStateException
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: C**
> 
> **🧠 Surgical Analysis:**
> 
> - `lock()` → Hold Count = 1
>     
> - `unlock()` في catch → Hold Count = 0 ✔
>     
> - `unlock()` في finally → ❌ مش ماسك اللوك
>     
> 
> **Crash:** `IllegalMonitorStateException`
> 
> ⚠️ **Golden Rule:**  
> `lock()` مرة → `unlock()` مرة واحدة فقط

---

## 🧪 Question 3: Modules — `provides` vs `uses`

```java
module com.zoo.service {
    exports com.zoo.api;
    // Line X
}
```

**Correct directive to provide `Cat` for `Pet`:**

- A) `uses com.zoo.api.Pet;`
    
- B) `provides com.zoo.impl.Cat with com.zoo.api.Pet;`
    
- C) `provides com.zoo.api.Pet with com.zoo.impl.Cat;`
    
- D) `requires com.zoo.impl.Cat;`
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: C**
> 
> **🧠 Rule:**
> 
> ```java
> provides <Interface> with <Implementation>;
> ```
> 
> - `uses` → Consumer
>     
> - `provides` → Provider ✔
>     

---

## 🧪 Question 4: Generics — `? super` & PECS Rule

```java
List<? super Integer> list = new ArrayList<Number>();
list.add(10);        // Line 1
list.add(3.5);       // Line 2
Object o = list.get(0); // Line 3
Number n = list.get(0); // Line 4
```

**Which lines fail compilation?**

- A) Line 1 & 4
    
- B) Line 2 & 4
    
- C) Line 2 & 3
    
- D) Line 4 only
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: B**
> 
> **🧠 PECS:**
> 
> - **Super → Consumer** (write safe)
>     
> - **Read → Object only**
>     
> 
> ❌ `add(Double)`  
> ❌ `get()` إلى `Number`

---

## 🧪 Question 5: NIO.2 — `normalize()` vs `equals()`

```java
var p1 = Path.of("/a/./b/../c");
var p2 = Path.of("/a/c");
System.out.println(p1.equals(p2) + " " + p1.normalize().equals(p2));
```

**Output?**

- A) true true
    
- B) false true
    
- C) false false
    
- D) true false
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: B**
> 
> **🧠 Key Insight:**
> 
> - `equals()` → literal comparison
>     
> - `normalize()` → resolves `.` & `..`
>     

---

## 🧪 Question 6: Switch Expression Scope

```java
int y = switch (x) {
    case 2 -> {
        int z = 20;
        yield z;
    }
    default -> 0;
};
// Line Z
```

**Insert `System.out.print(z);` at Line Z → ?**

- A) 2020
    
- B) 20
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: C**
> 
> **🧠 Rule:**  
> Block scope ends at `}`
> 
> `z` خارج النطاق ❌

---

## 🧪 Question 7: JDBC — Scrollable ResultSet

```java
rs.next();
rs.next();
rs.previous();
System.out.println(rs.getRow());
```

**Assuming 5 rows exist:**

- A) 1
    
- B) 2
    
- C) 3
    
- D) Compilation Error
    
- E) SQLException
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: A**
> 
> **🧠 Cursor Movement:**  
> Row1 → Row2 → back to Row1

---

## 🧪 Question 8: Records — Compact Constructor Rule

```java
public record User(String name, int age) {
    public User {
        this.name = name.toUpperCase(); // Line X
    }
}
```

**Compilation result?**

- A) Compiles
    
- B) Error: cannot assign to final field
    
- C) Recursive constructor
    
- D) Missing params
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: B**
> 
> **🧠 Rule:**
> 
> - Compact constructor ❌ no `this.field =`
>     
> - ✔ modify parameter only
>     

---

## 🧪 Question 9: ResourceBundle Fallback

```java
Locale loc = new Locale("fr", "CH");
ResourceBundle.getBundle("Msg", loc);
```

**Files exist:**

- Msg_fr_CA
    
- Msg_fr
    
- Msg_en
    
- Msg
    

**Loaded file?**

- A) Msg_fr
    
- B) Msg_en
    
- C) Msg
    
- D) Exception
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: A**
> 
> **🧠 Fallback Order:**  
> `fr_CH → fr → default locale → base`

---

## 🧪 Question 10: `removeIf` on Fixed-Size List

```java
List<String> list = Arrays.asList("A","B","C");
list.removeIf(s -> s.equals("B"));
```

**Result?**

- A) [A, C]
    
- B) [A, B, C]
    
- C) Compilation Error
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتشريح العميق  
> **الإجابة: D — UnsupportedOperationException**
> 
> **🧠 Rule:**  
> `Arrays.asList` → Fixed Size
> 
> ❌ structural modification

---

