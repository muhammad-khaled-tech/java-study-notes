

## Question 1: Enum Abstract Methods & Semicolons

**(Source: Chapter 3 - Questions / Lesson 4)**

```java
enum Status {
    ON {
        public String getCode() { return "1"; }
    },
    OFF {
        public String getCode() { return "0"; }
    }; 
    abstract String getCode();
}

public class EnumTest {
    public static void main(String[] args) {
        System.out.println(Status.ON.getCode());
    }
}
```

### What is the result?

- A. Prints `1`
    
- B. Compilation Error: Missing semicolon after OFF.
    
- C. Compilation Error: `getCode` cannot be abstract in an enum.
    
- D. Compilation Error: Access modifier mismatch.
    

> [!success] 👀 الإجابة والتحليل المفصل  
> **الإجابة: A (Prints 1)**
> 
> **🧠 التحليل المبسط (Simple Analysis):**
> 
> 1. الـ `enum` في Java هو `class` عادي جداً.
>     
> 2. وجود `abstract method` داخل enum مسموح بشرط كل constant يعمل override.
>     
> 3. الميثود `getCode()` معرفة `package-private`.
>     
> 4. الـ implementations داخل `ON` و `OFF` `public` → **زيادة Visibility مسموحة**.
>     
> 5. الـ semicolon بعد `OFF` موجودة وصحيحة.
>     
> 6. الكود **Compiles ويطبع 1**.
>     

---

## Question 2: JDBC Transactions & Savepoints

**(Source: Chapter 10 - Accessing Databases / Lesson Appendix)**

```java
Connection conn = DriverManager.getConnection(url);
conn.setAutoCommit(false);
Statement stmt = conn.createStatement();
stmt.executeUpdate("INSERT INTO Log VALUES (1)"); // Step 1
Savepoint sp1 = conn.setSavepoint();
stmt.executeUpdate("INSERT INTO Log VALUES (2)"); // Step 2
Savepoint sp2 = conn.setSavepoint();
stmt.executeUpdate("INSERT INTO Log VALUES (3)"); // Step 3
conn.rollback(sp1); // Rollback to SP1
conn.rollback(sp2); // Rollback to SP2
conn.commit();
```

### What happens at runtime?

- A. Rows 1, 2, 3 are saved.
    
- B. Only Row 1 is saved.
    
- C. Rows 1 and 2 are saved.
    
- D. Throws `SQLException` at the second rollback.
    

> [!warning] 👀 الإجابة والتحليل المفصل  
> **الإجابة: D (Throws SQLException)**
> 
> **🧠 التحليل المبسط:**
> 
> - `rollback(sp1)` يمسح كل ما بعده + يلغي `sp2`
>     
> - `rollback(sp2)` بعدها = **Invalid Savepoint**
>     
> - النتيجة: `SQLException`
>     

---

## Question 3: Serialization & Inheritance Constructors

**(Source: Chapter 9 - I/O / Lesson Appendix)**

```java
class Parent { // Not Serializable
    Parent() { System.out.print("P"); }
}
class Child extends Parent implements Serializable {
    Child() { System.out.print("C"); }
}
```

### What is printed during Deserialization?

- A. PC
    
- B. P
    
- C. C
    
- D. Nothing
    

> [!info] 👀 الإجابة والتحليل المفصل  
> **الإجابة: B (Prints P)**
> 
> **🧠 التحليل المبسط:**
> 
> - Constructor بتاع `Serializable` class لا يُستدعى
>     
> - Constructor بتاع الـ Parent غير Serializable **يُستدعى**
>     
> - يطبع `P` فقط
>     

---

## Question 4: Generics & The "Put" Limitation

**(Source: Lesson 7 - Slide 309)**

```java
List<? extends Number> list = new ArrayList<Integer>();
list.add(10);      // Line X
list.add(null);    // Line Y
Number n = list.get(0); // Line Z
```

### Which lines cause a compilation error?

- A. Line X only.
    
- B. Line X and Y.
    
- C. Line X, Y, and Z.
    
- D. None.
    

> [!danger] 👀 الإجابة والتحليل المفصل  
> **الإجابة: A (Line X only)**
> 
> **🧠 التحليل المبسط:**
> 
> - `extends` = Read only
>     
> - `add(Integer)` ممنوع
>     
> - `add(null)` مسموح
>     
> - `get()` بيرجع `Number`
>     

---

## Question 5: Record Constructors & Accessors

**(Source: Chapter 3 / Lesson 5)**

```java
public record Player(String name, int score) {
    public Player {
        if (score < 0) score = 0;
        this.name = name.toUpperCase(); // Line 1
    }
    public int getScore() { return score; } // Line 2
}
```

### What is the result of compiling?

- A. Compiles successfully.
    
- B. Error at Line 1 only.
    
- C. Error at Line 2 only.
    
- D. Error at Line 1 and Line 2.
    

> [!warning] 👀 الإجابة والتحليل المفصل  
> **الإجابة: D (Error at Line 1 and Line 2)**
> 
> **🧠 التحليل المبسط:**
> 
> - Compact Constructor: ممنوع `this.field`
>     
> - Accessor الصحيح: `score()` مش `getScore()`
>     

---

## Question 6: Locale & Bundle Fallback

**(Source: Chapter 11 - Localization)**

```java
Locale.setDefault(new Locale("fr", "FR"));
Locale target = new Locale("en", "UK");
ResourceBundle rb = ResourceBundle.getBundle("Train", target);
```

### Which file is loaded?

- A. `Train.properties`
    
- B. `Train_en.properties`
    
- C. `Train_en_US.properties`
    
- D. Exception
    

> [!success] 👀 الإجابة والتحليل المفصل  
> **الإجابة: B (`Train_en.properties`)**
> 
> **🧠 التحليل:**
> 
> - `en_UK` غير موجود
>     
> - يسقط الـ country → `en`
>     
> - يتوقف عند أول Match
>     

---

## Question 7: Path.subpath() Indexing

**(Source: Chapter 9 - IO)**

```java
Path p = Path.of("/zoo/animals/bear/koala.txt");
System.out.println(p.subpath(1, 3));
```

### Output?

- A. `animals/bear`
    
- B. `zoo/animals`
    
- C. `animals/bear/koala.txt`
    
- D. `zoo/animals/bear`
    

> [!info] 👀 الإجابة والتحليل المفصل  
> **الإجابة: A (`animals/bear`)**
> 
> **🧠 التحليل:**
> 
> - Root `/` لا يُحسب
>     
> - `subpath(start, end)` → end exclusive
>     

---

## Question 8: Modules & ServiceLoader

**(Source: Chapter 7 - Modules)**

### Which directive allows lookup using `ServiceLoader`?

- A. `provides`
    
- B. `uses`
    
- C. `requires`
    
- D. `exports`
    

> [!success] 👀 الإجابة والتحليل المفصل  
> **الإجابة: B (`uses`)**

---

## Question 9: List.remove(int) vs remove(Object)

**(Source: Lesson 9 / Chapter 5)**

```java
List<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.remove(1);
System.out.println(list);
```

### Output?

- A. `[1]`
    
- B. `[2]`
    
- C. `[]`
    
- D. Exception
    

> [!warning] 👀 الإجابة والتحليل المفصل  
> **الإجابة: A (`[1]`)**
> 
> **🧠 التحليل:**
> 
> - `remove(int)` → index
>     
> - index 1 = القيمة 2
>     

---

## Question 10: AtomicInteger Thread Safety

**(Source: Chapter 8 - Concurrency)**

```java
count.set(count.get() + 1);
```

### Is it thread-safe?

- A. Yes
    
- B. No, race condition
    
- C. Yes, synchronized
    
- D. Needs volatile
    

> [!danger] 👀 الإجابة والتحليل المفصل  
> **الإجابة: B (Race Condition)**
> 
> **🧠 التحليل:**
> 
> - `get()` + `set()` عمليتين منفصلتين
>     
> - الحل: `incrementAndGet()`
>     

---

لو حابب:

- تنسيق أدق (icons / colors)
    
- تحويله لـ **Note per Question**
    
- أو دمجه مع Sets قبل كده
    

ابعت بس 👍