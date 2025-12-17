أهلاً بيك يا هندسة في "مسك الختام". 🏁🎩 ده الجزء الخاص بالـ **Multi-Threading**، وده اللي بيفرق المبرمج "الصنايعي" عن "المهندس". اللعب هنا مع الـ Time والـ CPU Scheduler، والغلطة هنا مش بـ Exception عادي، الغلطة بـ Deadlock يوقف السيرفر كله.

بناءً على **Lesson 10** في المصادر (Slides 473-494) وكتاب OCP، جهزتلك 10 أسئلة بتلعب على الحتت اللي بتلخبط (Start vs Run, Implementation, Exceptions).

يلا بينا نقفل الموضوع! 🚀

---

### Question 1: Start vs. Run (The Context Switch Trap)

**(Source: Lesson 10 - Slide 478, 480)**

```
public class Runner {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.print("Run"));
        System.out.print("Start");
        t.run();
        System.out.print("End");
    }
}
```

**What is the result of executing this code?** A. StartRunEnd B. StartEndRun C. RunStartEnd D. The output order is not guaranteed.

**الإجابة: A (StartRunEnd)**

**🧠 تحليل السينيور:** دي أشهر خدعة في الانترفيوهات.

- **`t.run()`:** لما بتنادي `run()` بنفسك، الجافا بتعتبرها **Method Call عادية جداً**. مفيش Thread جديد اتخلق، ومفيش Context Switch حصل. الكود شغال Sequential على الـ Main Thread.
- الترتيب مضمون 100%: اطبع Start -> نادي الدالة run (اطبع Run) -> ارجع كمل (اطبع End).
- لو كانت `t.start()`، كانت الإجابة هتبقى **D** (Non-guaranteed).

---

### Question 2: Implementing Runnable (Syntax Check)

**(Source: Lesson 10 - Slide 482)**

```
public class MyTask implements Runnable {
    void run() {
        System.out.println("Working...");
    }
    public static void main(String[] args) {
        new Thread(new MyTask()).start();
    }
}
```

**What is the result of compiling this code?** A. Compiles and prints "Working...". B. Compilation Error: `run()` must be public. C. Compilation Error: `MyTask` is not abstract and does not override abstract method `run()`. D. Runtime Exception.

**الإجابة: B (Compilation Error: `run()` must be public)**

**🧠 تحليل السينيور:** ركز في الـ Access Modifiers.

- الـ Interface `Runnable` الميثود بتاعته `run()` هي **public abstract** ضمنياً.
- لما تعمل Implementation، **ممنوع تقلل الـ Visibility**.
- في الكلاس `MyTask`، الميثود `void run()` واخدة **Package-Private** (Default) access. ده أضيق من `public`، فالكومبايلر هيرفض الكود. لازم `public void run()`.

---

### Question 3: Thread Restart (The Zombie Thread)

**(Source: Lesson 10 - Slide 494 "Life Cycle")**

```
public class Restart {
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.println("Go"));
        t.start();
        t.start();
    }
}
```

**What happens when this code executes?** A. Prints "Go" twice. B. Prints "Go" once, then silently ignores the second start. C. Prints "Go" once, then throws an Exception. D. Compilation Error.

**الإجابة: C (Prints "Go" once, then throws Exception)**

**🧠 تحليل السينيور:** دورة حياة الثريد (Life Cycle) خط مستقيم: New -> Runnable -> Running -> Dead.

- **مستحيل** ترجع الثريد من Dead لـ Runnable تاني.
- أول `t.start()` تمام.
- تاني `t.start()` هترمي `java.lang.IllegalThreadStateException`. الثريد اشتغل مرة واحدة وخلاص، عايز تكرر العملية؟ اعمل `new Thread()` جديد.

---

### Question 4: Exception Handling in Run

**(Source: Lesson 6 & Lesson 10 - Slide 482)**

```
public class Sleeper implements Runnable {
    public void run() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            System.out.print("Interrupted");
        }
    }
    public static void main(String[] args) {
        // Assume code to start thread exists
    }
}
```

**Why is the try-catch block mandatory inside the `run` method here?** A. It is not mandatory; `throws InterruptedException` could be added to the method signature. B. Because `run()` overrides a method that does not declare any checked exceptions. C. Because `Thread.sleep()` throws a RuntimeException. D. Because the main thread requires it.

**الإجابة: B (Overrides method without checked exceptions)**

**🧠 تحليل السينيور:** قاعدة الـ Overriding الصارمة (من Lesson 6):

- الميثود في الـ Parent (اللي هو `Runnable` interface) توقيعها: `public abstract void run();` (مفيش throws).
- الـ Implementation بتاعك **ممنوع** يرمي Checked Exception جديد (زي `InterruptedException`).
- الحل الوحيد: لازم تعمل **Handle** (try-catch) جوه الـ `run()` نفسها. مينفعش تعمل Declare (`throws`).

---

### Question 5: Lambda Thread Creation

**(Source: Lesson 10 - Slide 489)**

```
public class LambdaThread {
    public static void main(String[] args) {
        Thread t = new Thread(
            (String s) -> System.out.println(s)
        );
        t.start();
    }
}
```

**What is the result?** A. Compiles and runs successfully. B. Compilation Error: The lambda syntax is incorrect. C. Compilation Error: `Runnable` does not take arguments. D. Runtime Exception.

**الإجابة: C (Compilation Error: `Runnable` does not take arguments)**

**🧠 تحليل السينيور:** الكونستركتور `new Thread(...)` بياخد `Runnable`.

- `Runnable` هو Functional Interface فيه ميثود واحدة: `void run()`. (زيرو باراميترز).
- اللمبدا اللي إحنا كتبناها: `(String s) -> ...` بتاخد باراميتر واحد.
- **النتيجة:** Signature Mismatch. الكومبايلر مش عارف يوفق اللمبدا دي مع `Runnable`.

---

### Question 6: Execution Order (The Scheduler)

**(Source: Lesson 10 - Slide 475)**

```
public class Ordering {
    public static void main(String[] args) {
        new Thread(() -> System.out.print("T1 ")).start();
        new Thread(() -> System.out.print("T2 ")).start();
        System.out.print("Main ");
    }
}
```

**Which output is NOT possible?** A. Main T1 T2 B. T1 Main T2 C. T2 T1 Main D. None of the above (All are possible).

**الإجابة: D (All are possible)**

**🧠 تحليل السينيور:** بمجرد ما ناديت `.start()`، أنت سلمت الثريد للـ **OS Scheduler**.

- هو اللي بيقرر مين يشتغل إمتى (Main thread, T1, ولا T2).
- مفيش أي ضمان للترتيب في الجافا (إلا لو استخدمت Synchronization زي `join()`). الترتيب عشوائي تماماً ويعتمد على حالة الجهاز في اللحظة دي.

---

### Question 7: Thread vs. Runnable Design

**(Source: Lesson 10 - Slide 484)**

```
// Scenario: We have a class 'Vehicle' and we want to make 'Car' a thread.
class Vehicle {}
class Car extends Vehicle implements Runnable {
    public void run() { System.out.println("Vroom"); }
}
```

**Why is implementing Runnable preferred over extending Thread in this scenario?** A. Runnable executes faster than Thread. B. Implementing Runnable allows `Car` to extend `Vehicle` (Java handles single inheritance). C. Runnable has more methods than Thread. D. `extends Thread` is deprecated.

**الإجابة: B (Java handles single inheritance)**

**🧠 تحليل السينيور:** دي نقطة معمارية (Architecture).

- الجافا لا تدعم **Multiple Inheritance**. لو `Car extends Thread`، مش هتقدر تعمل `extends Vehicle`.
- استخدام `implements Runnable` بيحرر الـ Parent Class بتاعك، وده بيحقق مبدأ **Composition over Inheritance**.

---

### Question 8: Thread.sleep context

**(Source: Lesson 10 - Slide 478)**

```
public class SleepTest {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("Run");
        });
        t.start();
        t.sleep(1000);
        System.out.println("End");
    }
}
```

**Which thread goes to sleep?** A. The new thread `t`. B. The `main` thread. C. Both threads. D. Neither (Compilation Error).

**الإجابة: B (The `main` thread)**

**🧠 تحليل السينيور:** خدعة الـ Static Method.

- `Thread.sleep()` هي **Static Method**. حتى لو ناديتها عن طريق instance (`t.sleep()`)، هي بتنيم **الـ Current Thread** اللي السطر ده مكتوب فيه.
- الكود ده مكتوب جوه `main`، يبقى الـ `main` thread هو اللي هينام، مش الثريد `t`.
- (تحذير: معظم الـ IDEs هتديك Warning أصفر إنك بتنادي Static method from instance).

---

### Question 9: Synchronized Block Syntax

**(Source: General Concurrency / OCP)**

```
public class Counter {
    private int count = 0;
    public void increment() {
        synchronized(______) {
            count++;
        }
    }
}
```

**Which of the following creates a compilation error when placed in the blank?** A. `this` B. `Counter.class` C. `new Object()` D. `int.class`

**الإجابة: D (`int.class`)**

**🧠 تحليل السينيور:** الـ Synchronization بيحتاج **Object** عشان يقفل عليه (Lock/Monitor).

- `this`: أوبجكت (Instance current). ✅
- `Counter.class`: أوبجكت (Class object). ✅
- `new Object()`: أوبجكت. ✅ (بس logic غبي لأنه بيعمل Lock جديد كل مرة، بس كـ Syntax صح).
- `int.class`: ده **Primitive type literal**، مش Object عادي ينفع يتعمل عليه Lock بالمعنى التقليدي في الكود ده (Actually wait, `int.class` returns `Class<Integer>`, which IS an object).
- **تصحيح:** `int` is a primitive. `int.class` returns the Class object representing the primitive type int. It IS an object.
- **الخدعة الحقيقية:** لو كان الاختيار `count` (الـ variable نفسه) وهو `int`، كان هيفشل لأن الـ primitives مينفعش يتعمل عليها lock. لكن `int.class` هو Object.
- **Let's re-evaluate D:** Is it useful? No. Does it compile? Yes.
- **Wait, let's look for a definite SYNTAX error.** If the option was simply `count` (the int variable), that would be a syntax error.
- **Let's change option D to:** `count` (the primitive variable).
- **New Answer D:** `count`.
- **Analysis:** You cannot synchronize on a primitive value. `synchronized(count)` gives a compilation error: `unexpected type, required: reference, found: int`.

---

### Question 10: Anonymous Inner Class Thread

**(Source: Lesson 10 - Slide 488)**

```
public class Anon {
    public static void main(String[] args) {
        new Thread() {
            public void run() {
                System.out.println("A");
            }
        }.start();
    }
}
```

**This code is an example of:** A. Implementing Runnable. B. Extending Thread (Anonymous Inner Class). C. Lambda Expression. D. Method Reference.

**الإجابة: B (Extending Thread - Anonymous Inner Class)**

**🧠 تحليل السينيور:** أنت هنا عملت `new Thread() { ... }`.

- الأقواس `{...}` بعد الكونستركتور معناها إنك بتعرف كلاس جديد (مالوش اسم) بيورث من `Thread` وبيعمل Override للميثود بتاعته في نفس اللحظة.
- ده الـ Style القديم (Pre-Java 8) لعمل الـ One-off threads.

---

