### Section 2: Interfaces & Access Modifiers

#### Q1: The "Protected" Interface Trap

**(Source: Lesson 4 - Part 3)**

```java
interface Secrets {
    int CODE = 123;
    protected void reveal();
    default void log() { System.out.println("Logging"); }
}
```

**What is the result of compiling this interface?** 
**A.** Compiles successfully. 
**B.** Compilation Error at `int CODE`: fields must be private. 
**C.** Compilation Error at `reveal()`: interface methods cannot be protected. 
**D.** Compilation Error at `log()`: default methods must be static.

**الإجابة: C (Compilation Error at `reveal()`)**

**🧠 تحليل السينيور:** في الـ Interface، كل الميثودز (ماعدا `private` في Java 9+) لازم تكون `public`. الـ Modifier `protected` ممنوع تماماً في الإنترفيس، لأن الهدف منه هو الـ Contract العام. المتغيرات `public static final` والـ default methods لازم تكون `public` (وهي كذلك ضمنياً لو مكتبتش حاجة).

---

#### Q2: Static Interface Methods Inheritance

**(Source: Lesson 4 - Part 5)**

```java
interface Printer {
    static void print() { System.out.print("Interface"); }
}
class ConsolePrinter implements Printer {
    public static void main(String[] args) {
        print();
        Printer.print();
        new ConsolePrinter().print();
    }
}
```

**Which lines cause a Compilation Error?** 
**A.** Only `Printer.print()` 
B.Only `print()` and `new ConsolePrinter().print()` 
**C.** All three lines. 
**D.** None, it prints "InterfaceInterfaceInterface".

**الإجابة: B (Only `print()` and `new ConsolePrinter().print()`)**

**🧠 تحليل السينيور:** الـ Static Methods في الإنترفيس **لا تورث** (Not Inherited) ولا يمكن الوصول إليها عبر Instance من الكلاس الذي ينفذها. لازم تناديها باسم الإنترفيس صراحة `Printer.print()`. محاولة مناداتها كأنها دالة عادية (`print()`) أو عن طريق أوبجكت (`new ConsolePrinter().print()`) هيديك خطأ كومبايلر.

---

#### Q3: Abstract Class Visibility Reduction

**(Source: Lesson 4 - Part 3)**

```java
interface Runner {
    void run();
}
abstract class Athlete implements Runner {
    protected void run() { System.out.println("Running"); }
}
```

**What happens when compiling this code?**
**A.** Compiles successfully. 
**B.** Compilation Error: Abstract classes cannot implement interfaces. 
**C.** Compilation Error: `run()` reduces visibility from public to protected. 
**D.** Compilation Error: Abstract class must declare `run()` as abstract.

**الإجابة: C (Compilation Error: `run()` reduces visibility)**

**🧠 تحليل السينيور:** الميثود `run()` في الإنترفيس هي `public` ضمنياً (Implicitly Public). لما `Athlete` يجي يعملها Implementation (حتى لو هو abstract)، مينفعش يقلل الـ Visibility لـ `protected`. لازم تكون `public` زي الأصل. قاعدة الـ Polymorphism: "Never reduce visibility".

---

#### Q4: Default Method Conflict (The Diamond Problem)

**(Source: Lesson 4 - Part 4)**

```java
interface A { default void hello() { System.out.print("A"); } }
interface B { default void hello() { System.out.print("B"); } }
class C implements A, B { }
```

**What happens when compiling class C?** 
**A.** Compiles and `new C().hello()` prints "A". 
**B.** Compiles and `new C().hello()` prints "B". 
**C.** Compilation Error: C inherits unrelated defaults for hello(). 
**D.** Runtime Exception: Ambiguous method call.

**الإجابة: C (Compilation Error)**

**🧠 تحليل السينيور:** لما كلاس يورث اتنين Interfaces فيهم نفس الـ Default Method Signature، الكومبايلر بيحتار ومش بيعرف يختار مين فيهم. الحل الوحيد إن الكلاس `C` يعمل `Override` للميثود دي ويحدد هو عايز مين (أو يكتب Implementation جديد).

---

#### Q5: Private Interface Methods Access

**(Source: Lesson 4 - Part 5)**

```
interface Calculator {
    private static int helper() { return 5; }
    default int add(int x) { return x + helper(); }
}
class MyCalc implements Calculator {
    public void test() { System.out.println(Calculator.helper()); }
}
```

**What is the result?** 
**A.** Compiles and prints 5. 
**B.** Compilation Error: Private methods cannot be static.
**C.** Compilation Error: `helper()` has private access in Calculator. 
**D.** Runtime Exception.

**الإجابة: C (Compilation Error)**

**🧠 تحليل السينيور:** الـ Private Interface Methods (Feature جديدة في Java 9) معمولة عشان الـ internal reuse جوه الإنترفيس نفسه بس. مينفعش أي كلاس من بره (حتى لو بيعمل `implements` أو بيناديها كـ Static) يشوفها. هي مخفية تماماً (`private access`).

---

### Section 2: Wrapper Classes & Autoboxing

#### Q6: Unboxing Null (The Silent Killer)

**(Source: Lesson 5 - Part 2)**

```
public class UnboxCheck {
    public static void main(String[] args) {
        Integer val = null;
        if (val < 0) System.out.println("Negative");
        else System.out.println("Positive/Zero");
    }
}
```

**What happens at runtime?** 
**A.** Prints "Positive/Zero" 
**B.** Prints "Negative" 
**C.** Throws `NullPointerException` 
**D.** Compilation Error: Incompatible types.

**الإجابة: C (Throws `NullPointerException`)**

**🧠 تحليل السينيور:** عشان الجافا تقارن `val < 0`، لازم تعمل Unboxing للـ `Integer` وتحوله لـ `int` (Primitive) لأن العمليات الحسابية والمقارنات بتتم على الـ Primitives. بما إن `val` بـ `null`، عملية الـ Unboxing (`val.intValue()`) هتضرب NPE فوراً. دي أشهر مصيبة Autoboxing.

---

#### Q7: Integer Caching Trap

**(Source: Lesson 5 - Part 1)**

```
public class CacheTrap {
    public static void main(String[] args) {
        Integer a = 127; Integer b = 127;
        Integer c = 128; Integer d = 128;
        System.out.println((a == b) + " " + (c == d));
    }
}
```

**What is the output?** 
**A.** true true 
**B.** false false 
**C.** true false 
**D.** false true

**الإجابة: C (true false)**

**🧠 تحليل السينيور:** الجافا بتعمل Caching للـ Integers في المدى من `-128` لـ `127`.

- `127` في المدى، فـ `a` و `b` بيشاوروا على نفس الأوبجكت في الكاش (True).
- `128` بره المدى، فـ `c` و `d` كل واحد فيهم أوبجكت جديد في الـ Heap (False).

---

#### Q8: Overloading: Widening vs. Boxing

**(Source: Lesson 2 & Lesson 5)**

```
public class OverloadTrick {
    static void go(Long x) { System.out.print("Long"); }
    static void go(Integer x) { System.out.print("Integer"); }
    public static void main(String[] args) {
        int i = 5;
        go(i);
    }
}
```

**What is the output?**
**A.** Long 
**B.** Integer
**C.** Compilation Error
**D.** Runtime Exception

**الإجابة: B (Integer)**

**🧠 تحليل السينيور:** الجافا بتفضل الـ Autoboxing (من `int` لـ `Integer`) على الـ Widening + Boxing.

- لو مفيش `go(Integer)`، الكومبايلر **مش** هيقدر يحول `int` -> `long` -> `Long` (خطوتين تحويل ممنوع).
- لكن `int` -> `Integer` خطوة واحدة (Autoboxing) وهي المتاحة هنا.

---

#### Q9: Wrapper Constructors & String Parsing

**(Source: Lesson 5 - Part 1)**

```
public class ParseTrick {
    public static void main(String[] args) {
        Boolean b1 = new Boolean("TruE");
        Boolean b2 = Boolean.valueOf("love");
        System.out.println(b1 + " " + b2);
    }
}
```

**What is the output?** 
**A.** true false 
**B.** false false
**C.** true true 
**D.** Throws Exception

**الإجابة: A (true false)**

**🧠 تحليل السينيور:**

- `Boolean` كونستركتور (أو `valueOf`) ذكي جداً (Case Insensitive). كلمة "TruE" بيعتبرها `true`.
- أي سترينج تاني غير "true" (زي "love") بيرجع `false` (مش بيضرب Exception زي `Integer.valueOf` اللي بتضرب `NumberFormatException`). دي حالة خاصة بالـ Boolean.

---

#### Q10: Equality Mix-up

**(Source: Lesson 5)**

```
public class EqualityTest {
    public static void main(String[] args) {
        Long l = 100L;
        Integer i = 100;
        System.out.println(l.equals(i));
    }
}
```

**What is the output?**
**A.** true 
**B.** false 
**C.** Compilation Error 
**D.** Runtime Exception

**الإجابة: B (false)**

**🧠 تحليل السينيور:** دالة `equals()` في الـ Wrapper Classes أول حاجة بتعملها بتتشك على الـ Type.

- `if (obj instanceof Long)` -> لو لأ، بترجع `false` فوراً.
- بما إن `i` هو `Integer`، المقارنة بتفشل حتى لو القيمة الرقمية (100) متساوية.

---

### Section 3: Inner Classes

#### Q11: Member Inner Class Instantiation

**(Source: Lesson 5 - Part 2)**

```
class Outer {
    class Inner {}
}
public class Test {
    public static void main(String[] args) {
        // INSERT CODE HERE
    }
}
```

**Which line correctly instantiates the Inner class?**
**A.** `Outer.Inner i = new Outer.Inner();` 
**B.** `Outer.Inner i = new Outer().new Inner();` 
**C.** `Outer.Inner i = new Outer().Inner();` 
**D.** `Outer.Inner i = Outer.new Inner();`

**الإجابة: B (`new Outer().new Inner();`)**

**🧠 تحليل السينيور:** الـ Member Inner Class محتاج **Instance** من الـ Outer Class عشان يعيش جواه. الصيغة الغريبة `new Outer().new Inner()` هي الطريقة الوحيدة الصح من بره الكلاس. الاختيار A غلط لأنه بيعاملها كأنها Static Nested Class.

---

#### Q12: Static Nested Class Instantiation

**(Source: Lesson 5 - Part 2)**

```
class Outer {
    static class Nested {}
}
public class Test {
    public static void main(String[] args) {
        // INSERT CODE HERE
    }
}
```

**Which line correctly instantiates the Nested class?**
**A.** `Outer.Nested n = new Outer().new Nested();` 
**B.** `Outer.Nested n = new Outer.Nested();` 
**C.** `Outer.Nested n = Outer.new Nested();` 
**D.** `Outer.Nested n = new Nested();`

**الإجابة: B (`new Outer.Nested();`)**

**🧠 تحليل السينيور:** الـ Static Nested Class بتتصرف كأنها Top-level class عادية، بس اسمها مركب `Outer.Nested`. مش محتاجة أوبجكت من `Outer` عشان تتخلق، فبنستخدم `new Outer.Nested()`.

---

#### Q13: Local Inner Class & Variables

**(Source: Lesson 5 - Part 2)**

```
class Outer {
    void myMethod() {
        int x = 10;
        x++;
        class Local {
            void print() { System.out.println(x); }
        }
        new Local().print();
    }
}
```

**What is the result?** 
**A.** Prints 10 
**B.** Prints 11 **C.** Compilation Error: variable x is accessed from within inner class. 
**D.** Runtime Exception.

**الإجابة: C (Compilation Error)**

**🧠 تحليل السينيور:** الـ Local Inner Class تقدر تشوف المتغيرات المحلية (Local Variables) **فقط** لو كانت `final` أو `effectively final`. بما إننا عملنا `x++`، المتغير `x` مبقاش `effectively final`، وبالتالي ممنوع استخدامه جوه الكلاس الداخلي.

---

#### Q14: Accessing Private Members

**(Source: Lesson 5 - Part 2)**

```
class Outer {
    private String msg = "Secret";
    class Inner {
        void reveal() { System.out.println(Outer.this.msg); }
    }
}
```

**Is this code valid?** 
**A.** No, Inner cannot access private members of Outer. 
**B.** Yes, compiles and runs fine. **C.** No, syntax `Outer.this.msg` is incorrect. 
**D.** No, msg must be final.

**الإجابة: B (Yes, compiles and runs fine)**

**🧠 تحليل السينيور:** دي قوة الـ Inner Classes! هي موجودة جوه "بطن" الـ Outer Class، فبتقدر تشوف كل أعضائه (حتى الـ `private`). والوصول لـ `this` بتاع الـ Outer بيتم عن طريق `OuterClassName.this`.

---

#### Q15: Anonymous Inner Class Syntax

**(Source: Lesson 5 - Part 2)**

```
interface Action { void doIt(); }
public class Test {
    public static void main(String[] args) {
        Action a = new Action() {
            public void doIt() { System.out.println("Done"); }
        }
    }
}
```

**What is the result?** **A.** Prints "Done"
**B.** Compilation Error: Missing semicolon. 
**C.** Compilation Error: Cannot instantiate interface. 
**D.** Compiles but does nothing.

**الإجابة: B (Compilation Error: Missing semicolon)**

**🧠 تحليل السينيور:** الـ Anonymous Inner Class هي في الحقيقة جملة تعريف متغير `Action a = ... ;`. زي أي جملة في الجافا، لازم تنتهي بـ `;`. القوس المقفول `}` لوحده مش كفاية، لازم `};` في الآخر. دي غلطة مشهورة جداً.