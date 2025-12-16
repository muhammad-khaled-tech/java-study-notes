

**قواعد الاشتباك:**

1. **ركز في كل سطر:** الجافا لغة "دقيقة" (Strict)، والخدعة غالباً بتكون في التفاصيل الصغيرة.
2. **Compile Time vs Runtime:** اسأل نفسك دايماً: "هل الكومبايلر هيعديها؟" ولو عدت، "إيه اللي هيحصل في الميموري؟".

يلا بينا! 🚀

---

### Section 1: Interfaces & Access Modifiers

#### Q1: The "Protected" Interface Trap

**(Source: Lesson 4 - Part 3)**

```
interface Secrets {
    int CODE = 123;
    protected void reveal();
    default void log() { System.out.println("Logging"); }
}
```

**What is the result of compiling this interface?** **A.** Compiles successfully. **B.** Compilation Error at `int CODE`: fields must be private. **C.** Compilation Error at `reveal()`: interface methods cannot be protected. **D.** Compilation Error at `log()`: default methods must be static.

---

#### Q2: Static Interface Methods Inheritance

**(Source: Lesson 4 - Part 5)**

```
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

**Which lines cause a Compilation Error?** **A.** Only `Printer.print()` **B.** Only `print()` and `new ConsolePrinter().print()` **C.** All three lines. **D.** None, it prints "InterfaceInterfaceInterface".

---

#### Q3: Abstract Class Visibility Reduction

**(Source: Lesson 4 - Part 3)**

```
interface Runner {
    void run();
}
abstract class Athlete implements Runner {
    protected void run() { System.out.println("Running"); }
}
```

**What happens when compiling this code?** **A.** Compiles successfully. **B.** Compilation Error: Abstract classes cannot implement interfaces. **C.** Compilation Error: `run()` reduces visibility from public to protected. **D.** Compilation Error: Abstract class must declare `run()` as abstract.

---

#### Q4: Default Method Conflict (The Diamond Problem)

**(Source: Lesson 4 - Part 4)**

```
interface A { default void hello() { System.out.print("A"); } }
interface B { default void hello() { System.out.print("B"); } }
class C implements A, B { }
```

**What happens when compiling class C?** **A.** Compiles and `new C().hello()` prints "A". **B.** Compiles and `new C().hello()` prints "B". **C.** Compilation Error: C inherits unrelated defaults for hello(). **D.** Runtime Exception: Ambiguous method call.

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

**What is the result?** **A.** Compiles and prints 5. **B.** Compilation Error: Private methods cannot be static. **C.** Compilation Error: `helper()` has private access in Calculator. **D.** Runtime Exception.

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

**What happens at runtime?** **A.** Prints "Positive/Zero" **B.** Prints "Negative" **C.** Throws `NullPointerException` **D.** Compilation Error: Incompatible types.

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

**What is the output?** **A.** true true **B.** false false **C.** true false **D.** false true

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

**What is the output?** **A.** Long **B.** Integer **C.** Compilation Error **D.** Runtime Exception

---

#### Q9: Wrapper Constructors & String Parsing

**(Source: Lesson 5 - Part 1)**

```
public class ParseTrick {
    public static void main(String[] args) {
        Boolean b1 = new Boolean("TruE");
        Boolean b2 = Boolean.valueOf(null);
        System.out.println(b1 + " " + b2);
    }
}
```

**What is the output?** **A.** true false **B.** false false **C.** true true **D.** Throws Exception

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

**What is the output?** **A.** true **B.** false **C.** Compilation Error **D.** Runtime Exception

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

**Which line correctly instantiates the Inner class?** **A.** `Outer.Inner i = new Outer.Inner();` **B.** `Outer.Inner i = new Outer().new Inner();` **C.** `Outer.Inner i = new Outer().Inner();` **D.** `Outer.Inner i = Outer.new Inner();`

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

**Which line correctly instantiates the Nested class?** **A.** `Outer.Nested n = new Outer().new Nested();` **B.** `Outer.Nested n = new Outer.Nested();` **C.** `Outer.Nested n = Outer.new Nested();` **D.** `Outer.Nested n = new Nested();`

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

**What is the result?** **A.** Prints 10 **B.** Prints 11 **C.** Compilation Error: variable x is accessed from within inner class. **D.** Runtime Exception.

---

#### Q14: Accessing Private Members

**(Source: Lesson 5 - Part 2)**

```
class Outer {
    private String msg = "Secret";
    class Inner {
        void reveal() { System.out.println(msg); }
    }
}
```

**Is this code valid?** **A.** No, Inner cannot access private members of Outer. **B.** Yes, compiles and runs fine. **C.** No, msg must be static to be accessed. **D.** No, msg must be final.

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

**What is the result?** **A.** Prints "Done" **B.** Compilation Error: Missing semicolon. **C.** Compilation Error: Cannot instantiate interface. **D.** Compiles but does nothing.