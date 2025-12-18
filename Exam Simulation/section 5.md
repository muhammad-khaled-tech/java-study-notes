
---
# 🧵 Multi-Threading — *مسك الختام* 🏁🎩

---

### 🟢 Question 1: `start()` vs `run()` — Context Switch Trap
**Topic:** Thread Life Cycle

```java
public class Runner {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.print("Run"));
        System.out.print("Start");
        t.run(); // الفخ هنا
        System.out.print("End");
    }
}
````

**What is the result?**

- A) StartRunEnd
    
- B) StartEndRun
    
- C) RunStartEnd
    
- D) Output order is not guaranteed
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: A — StartRunEnd
> 
> 🧠 تحليل السينيور:
> 
> استدعاء t.run() مباشرة بيعتبر Normal Method Call.
> 
> - **مفيش** Thread جديد اتخلق.
>     
> - **مفيش** Context Switch حصل.
>     
> - الكود اتنفذ سطر بسطر (Sequential) على الـ **Main Thread**.
>     
> 
> **💡 تكة:** لو كانت `t.start()`، كان الترتيب هيبقى غير مضمون (الإجابة D).

---

### 🟢 Question 2: Implementing `Runnable` — Access Modifier Trap

**Topic:** Interfaces & Overriding



```Java
public class MyTask implements Runnable {
    void run() { // الفخ هنا (Package-Private)
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B — Compilation Error
> 
> 🧠 تحليل السينيور:
> 
> الميثود run() في الإنترفيس Runnable معرفة إنها public abstract.
> 
> لما تيجي تعمل Override، ممنوع تقلل الـ Visibility.
> 
> - الإنترفيس: `public`
>     
> - الكلاس بتاعك: `package-private` (default) ❌
>     
> - **الحل:** لازم تكتب `public void run()`.
>     

---

### 🟢 Question 3: Restarting a Thread — Zombie Thread

**Topic:** Thread State



```Java
public class Restart {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.println("Go"));
        t.start();
        t.start(); // بيحاول يصحيه تاني
    }
}
```

**What happens?**

- A) Prints twice
    
- B) Prints once and ignores
    
- C) Prints once then throws exception
    
- D) Compilation Error
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: C
> 
> 🧠 تحليل السينيور:
> 
> دورة حياة الـ Thread في اتجاه واحد: New → Runnable → Running → Dead.
> 
> مفيش رجوع من الموت! 💀
> 
> أول start() بتشتغل تمام. التانية بترمي IllegalThreadStateException لأن الـ Thread حالته اتغيرت خلاص ومينفعش يتعمل له start مرتين.

---

### 🟢 Question 4: Checked Exception inside `run()`

**Topic:** Exception Handling



```Java
public class Sleeper implements Runnable {
    public void run() {
        try {
            Thread.sleep(1000); // دي بترمي InterruptedException
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B
> 
> 🧠 تحليل السينيور:
> 
> قاعدة الـ Overriding المقدسة: ممنوع الابن يرمي Checked Exception الأب مش راميه.
> 
> ميثود run() في Runnable مش بترمي أي Exceptions.
> 
> عشان كده، أي Checked Exception (زي InterruptedException) بيحصل جواها، لازم يتعالج جواها (try-catch)، ومينفعش تستخدم throws في التوقيع بتاعها.

---

### 🟢 Question 5: Lambda Thread Creation Trap

**Topic:** Functional Interfaces



```Java
Thread t = new Thread(
    (String s) -> System.out.println(s) // الفخ هنا
);
t.start();
```

**What is the result?**

- A) Works fine
    
- B) Lambda syntax error
    
- C) `Runnable` takes no arguments
    
- D) Runtime Exception
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: C
> 
> 🧠 تحليل السينيور:
> 
> الـ Constructor بتاع Thread مستني Runnable.
> 
> الـ Runnable ده Functional Interface فيه ميثود يتيمة: void run().
> 
> الميثود دي مبتاخدش باراميترز (no-args).
> 
> اللامبدا اللي أنت باعتها بتاخد String، وده ميمشيش مع التوقيع بتاع Runnable. الكومبايلر هيصرخ في وشك.

---

### 🟢 Question 6: Execution Order — OS Scheduler

**Topic:** Scheduling



```Java
new Thread(() -> System.out.print("T1 ")).start();
new Thread(() -> System.out.print("T2 ")).start();
System.out.print("Main ");
```

**Which output is NOT possible?**

- A) Main T1 T2
    
- B) T1 Main T2
    
- C) T2 T1 Main
    
- D) None (all possible)
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: D — None (all possible)
> 
> 🧠 تحليل السينيور:
> 
> بمجرد ما ناديت .start()، أنت سلمت الدفة لـ OS Scheduler.
> 
> هو بيقرر مين يشتغل إمتى وبأي ترتيب بناءً على خوارزميات معقدة وحمل الجهاز.
> 
> الكود ده حرفياً سباق خيول (Race)، أي حصان ممكن يوصل الأول، وممكن الـ Main يخلص قبلهم كلهم.

---

### 🟢 Question 7: `Thread` vs `Runnable` — Design Question

**Topic:** Design Principles



```Java
class Vehicle {}
class Car extends Vehicle implements Runnable {
    public void run() { System.out.println("Vroom"); }
}
```

**Why is this better than `extends Thread`?**

- A) Faster
    
- B) Java has single inheritance
    
- C) Runnable has more methods
    
- D) Thread is deprecated
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B
> 
> 🧠 تحليل السينيور:
> 
> الجافا بتدعم Single Class Inheritance.
> 
> لو خليت Car extends Thread، ضيعت فرصتك إنك تورث من Vehicle.
> 
> استخدام implements Runnable بيخليك حر تورث من أي كلاس تاني (Composition over Inheritance). ده شغل Archtiect فاهم مش حافظ.

---

### 🟢 Question 8: `Thread.sleep()` — Static Method Trap

**Topic:** Static Methods



```Java
Thread t = new Thread(() -> System.out.println("Run"));
t.start();
t.sleep(1000); // الفخ هنا
System.out.println("End");
```

**Which thread sleeps?**

- A) `t`
    
- B) `main`
    
- C) Both
    
- D) Compilation Error
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B — main
> 
> 🧠 تحليل السينيور:
> 
> خدعة كلاسيكية! Thread.sleep() دي Static Method.
> 
> حتى لو ناديتها عن طريق instance (t.sleep())، الجافا بتترجمها لـ Thread.sleep().
> 
> وهي بتنيم الـ Current Thread اللي السطر ده اتنفذ فيه (اللي هو هنا الـ main). الـ Thread t شغال ولا همه حاجة.

---

### 🟢 Question 9: `synchronized` Block — Primitive Trap

**Topic:** Synchronization



```Java
public class Counter {
    private int count = 0;

    public void increment() {
        synchronized (______) { // الفراغ ده
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: D — count
> 
> 🧠 تحليل السينيور:
> 
> الـ Lock في الجافا بيتحط على Object Header (Monitor).
> 
> الـ Primitives (زي int) معندهاش Object Header، وبالتالي مينفعش تستخدمها كـ Lock.
> 
> الكومبايلر هيرفض synchronized(count) فوراً.

---

### 🟢 Question 10: Anonymous Inner Class Thread

**Topic:** Syntax Variants

Java

```Java
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
    

> [!SUCCESS]- اضغط هنا لرؤية الحل والتحليل
> 
> الإجابة الصحيحة: B
> 
> 🧠 تحليل السينيور:
> 
> الشكل ده new Thread() { ... } بيعمل كلاس "مجهول الهوية" (Anonymous Inner Class) بيورث (extends) من Thread وبيعمل Override للـ run.
> 
> ده كان الاستايل القديم قبل ما ربنا يكرمنا بالـ Lamdbas في Java 8.