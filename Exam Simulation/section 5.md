

---

## 🧵 Multi-Threading — _مسك الختام_ 🏁🎩

> [!note]  
> الجزء ده هو اللي بيفرق **المهندس الصنايعي** عن اللي حافظ API وخلاص.  
> الغلط هنا مش Exception… الغلط هنا **Deadlock** يوقف Production 😈
> 
> **Sources:**
> 
> - Lesson 10 (Slides 473–494)
>     
> - OCP Java Book
>     

---

## 🧪 Question 1: `start()` vs `run()` — Context Switch Trap

```java
public class Runner {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.print("Run"));
        System.out.print("Start");
        t.run();
        System.out.print("End");
    }
}
```

**What is the result?**

- A) StartRunEnd
    
- B) StartEndRun
    
- C) RunStartEnd
    
- D) Output order is not guaranteed
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: A — StartRunEnd**
> 
> **🧠 تحليل السينيور:**  
> `run()` = **Normal method call**
> 
> - مفيش Thread جديد
>     
> - مفيش Context Switch
>     
> - كله شغال على **Main Thread**
>     
> 
> لو كانت `t.start()` → الإجابة كانت هتبقى **D**

---

## 🧪 Question 2: Implementing `Runnable` — Access Modifier Trap

```java
public class MyTask implements Runnable {
    void run() {
        System.out.println("Working...");
    }
    public static void main(String[] args) {
        new Thread(new MyTask()).start();
    }
}
```

**What happens at compile time?**

- A) Compiles and runs
    
- B) Compilation Error: `run()` must be public
    
- C) Must declare class as abstract
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B**
> 
> **🧠 تحليل السينيور:**
> 
> - `Runnable.run()` = `public abstract`
>     
> - ممنوع تقلل الـ visibility
>     
> 
> ❌ `void run()`  
> ✅ `public void run()`

---

## 🧪 Question 3: Restarting a Thread — Zombie Thread

```java
public class Restart {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.println("Go"));
        t.start();
        t.start();
    }
}
```

**What happens?**

- A) Prints twice
    
- B) Prints once and ignores
    
- C) Prints once then throws exception
    
- D) Compilation Error
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: C**
> 
> **🧠 Thread Life Cycle:**  
> `New → Runnable → Running → Dead`
> 
> ❌ مفيش رجوع من Dead
> 
> Exception: `IllegalThreadStateException`

---

## 🧪 Question 4: Checked Exception inside `run()`

```java
public class Sleeper implements Runnable {
    public void run() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            System.out.print("Interrupted");
        }
    }
}
```

**Why is try-catch mandatory here?**

- A) Could add `throws`
    
- B) Overriding method has no checked exceptions
    
- C) `sleep()` throws RuntimeException
    
- D) Main thread requires it
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B**
> 
> **🧠 Overriding Rule:**
> 
> - Parent method (`run`) → no checked exceptions
>     
> - Child method → ممنوع يضيف
>     
> 
> ✔ الحل الوحيد: Handle جوه الميثود

---

## 🧪 Question 5: Lambda Thread Creation Trap

```java
Thread t = new Thread(
    (String s) -> System.out.println(s)
);
t.start();
```

**What is the result?**

- A) Works fine
    
- B) Lambda syntax error
    
- C) `Runnable` takes no arguments
    
- D) Runtime Exception
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: C**
> 
> **🧠 Runnable Signature:**
> 
> ```java
> void run();
> ```
> 
> ❌ `(String s) -> ...`  
> ✔ `() -> ...`

---

## 🧪 Question 6: Execution Order — OS Scheduler

```java
new Thread(() -> System.out.print("T1 ")).start();
new Thread(() -> System.out.print("T2 ")).start();
System.out.print("Main ");
```

**Which output is NOT possible?**

- A) Main T1 T2
    
- B) T1 Main T2
    
- C) T2 T1 Main
    
- D) None (all possible)
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D**
> 
> **🧠 Rule:**  
> `.start()` = hand over control to **OS Scheduler**
> 
> مفيش أي ترتيب مضمون بدون `join()` / sync.

---

## 🧪 Question 7: `Thread` vs `Runnable` — Design Question

```java
class Vehicle {}
class Car extends Vehicle implements Runnable {
    public void run() {
        System.out.println("Vroom");
    }
}
```

**Why is this better than `extends Thread`?**

- A) Faster
    
- B) Java has single inheritance
    
- C) Runnable has more methods
    
- D) Thread is deprecated
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B**
> 
> **🧠 Architecture:**
> 
> - Java = single inheritance
>     
> - Runnable = **Composition over Inheritance**
>     

---

## 🧪 Question 8: `Thread.sleep()` — Static Method Trap

```java
Thread t = new Thread(() -> System.out.println("Run"));
t.start();
t.sleep(1000);
System.out.println("End");
```

**Which thread sleeps?**

- A) `t`
    
- B) `main`
    
- C) Both
    
- D) Compilation Error
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B**
> 
> **🧠 Important:**  
> `sleep()` **static**
> 
> بتنيم **Current Thread**
> 
> IDE Warning: calling static via instance ⚠️

---

## 🧪 Question 9: `synchronized` Block — Primitive Trap (✔️ مصححة)

```java
public class Counter {
    private int count = 0;

    public void increment() {
        synchronized (______) {
            count++;
        }
    }
}
```

**Which causes a compilation error?**

- A) `this`
    
- B) `Counter.class`
    
- C) `new Object()`
    
- D) `count`
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: D — `count`**
> 
> **🧠 Rule:**  
> `synchronized` لازم **Reference type**
> 
> ❌ primitives
> 
> ```java
> synchronized(count) // compile error
> ```

---

## 🧪 Question 10: Anonymous Inner Class Thread

```java
new Thread() {
    public void run() {
        System.out.println("A");
    }
}.start();
```

**This is an example of:**

- A) Runnable
    
- B) Extending Thread (Anonymous Class)
    
- C) Lambda
    
- D) Method Reference
    

> [!success] 👀 الإجابة والتحليل  
> **الإجابة: B**
> 
> **🧠 Explanation:**
> 
> ```java
> new Thread() { ... }
> ```
> 
> = anonymous subclass of `Thread`
> 
> Pre-Java-8 style

---

> [!summary]  
> ✔ منسق لـ **Obsidian**  
> ✔ تصحيح خدعة `synchronized`  
> ✔ Senior-level explanations  
> ✔ جاهز Exam / Interview / Notes
> 
> لو حابب:
> 
> - 🧪 MCQ امتحان كامل
>     
> - 📘 Threading Cheat Sheet
>     
> - ⚠️ Deadlock & Race Patterns
>     
> 
> قولّي وانت أظبطهولك 💪