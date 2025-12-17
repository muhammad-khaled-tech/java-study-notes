

### Section 3: Exception Handling (Hierarchy & Flow)

#### Q1: The Unreachable Catch Trap

**(Source: Lesson 6 - Slide 275, Slide 295)**

```
import java.io.*;
public class ExceptionTrap {
    public void readFile() {
        try {
            throw new FileNotFoundException("File missing");
        } catch (IOException e) {
            System.out.print("IO");
        } catch (FileNotFoundException e) {
            System.out.print("File");
        }
    }
}
```

**What is the result?** 
A. Prints `IO`
B. Prints `File`
C. Prints `IOFile`
D. Compilation Error

**الإجابة: D (Compilation Error)**

**🧠 تحليل السينيور:** الـ `FileNotFoundException` هو ابن (Subclass) للـ `IOException`. بما إنك اصطدت الأب (`IOException`) في الـ catch block الأول، فالابن مستحيل يوصله الدور (Unreachable Code). الكومبايلر بيعتبر دي Dead Code وبيرفض الكود. لازم تعكس الترتيب: الابن الأول، ثم الأب.

---

#### Q2: The Multi-Catch Redundancy

**(Source: Lesson 6 - Slide 281)**

```
public class MultiCatchTrap {
    public static void main(String[] args) {
        try {
            if (true) throw new RuntimeException();
        } catch (ArithmeticException | RuntimeException e) {
            System.out.println("Caught");
        }
    }
}
```

**What is the result?** 
A. Prints `Caught` 
B. Runtime Exception 
C. Compilation Error 
D. Ignores the exception

**الإجابة: C (Compilation Error)**

**🧠 تحليل السينيور:** في الـ Multi-Catch (`|`)، ممنوع تحط كلاسين ليهم علاقة وراثة (Parent/Child) في نفس السطر. الـ `ArithmeticException` هو ابن للـ `RuntimeException`. الجافا بتقولك: "ما كفاية تكتب `RuntimeException` وهي هتشيل كله!". وجودهم مع بعض يعتبر Redundant (زائد عن الحاجة) وممنوع.

---

#### Q3: Finally vs. Return (The Swallow Trap)

**(Source: Lesson 6 - Slide 287)**

```
public class FinallyTrap {
    public static int test() {
        try {
            throw new RuntimeException("Boom");
        } finally {
            return 10;
        }
    }
    public static void main(String[] args) {
        System.out.println(test());
    }
}
```

**What is the result?** 
A. Prints `10` 
B. Throws `RuntimeException` 
C. Compilation Error (Unreachable code) 
D. Compilation Error (Missing catch)

**الإجابة: A (Prints 10)**

**🧠 تحليل السينيور:** دي واحدة من أخطر الـ Anti-patterns. الـ `finally` block بيشتغل دايماً. لو الـ `finally` احتوى على `return`، هو بيعمل "Swallow" (بيبلع) أي Exception حصل في الـ `try` وبيمسحه تماماً كأنه محصلش. النتيجة هتكون 10 والـ Exception اختفى.

---

#### Q4: Override & Checked Exceptions

**(Source: Lesson 6 - Slide 263, Slide 296)**

```
import java.io.*;
class Parent {
    void doWork() throws IOException {}
}
class Child extends Parent {
    @Override
    void doWork() throws Exception {}
}
```

**What is the result?** 
A. Compiles successfully 
B. Compilation Error in `Parent` 
C. Compilation Error in `Child` 
D. Runtime Error upon instantiation

**الإجابة: C (Compilation Error in Child)**

**🧠 تحليل السينيور:** قاعدة الـ Overriding مع الـ Exceptions: الابن يقدر يرمي نفس الـ Checked Exception، أو ابنه (Subclass)، أو مايرميش حاجة خالص. لكن **ممنوع** يرمي Exception أوسع (Broader/Parent) من اللي الأب أعلن عنه. `Exception` أعم من `IOException`، فده كسر للعقد (Contract Violation).

---

#### Q5: Try-with-Resources Scope

**(Source: Lesson 6 - Slide 289, 290)**

```
import java.util.Scanner;
public class ResourceTrap {
    public static void main(String[] args) {
        try (Scanner s = new Scanner("123")) {
            s.nextInt();
        } catch (Exception e) {
            s.nextInt();
        }
    }
}
```

**What is the result?** 
A. Compiles and runs
B. Compilation Error at line `try` 
C. Compilation Error inside `catch` block 
D. Throws `IllegalStateException`

**الإجابة: C (Compilation Error inside catch block)**

**🧠 تحليل السينيور:** المتغيرات المعرفة داخل أقواس الـ `try(...)` (اللي هي الـ Resources) الـ Scope بتاعها بينتهي بنهاية بلوك الـ `try`. هي غير مرئية (Invisible) داخل الـ `catch` أو الـ `finally`. المتغير `s` مش متشاف جوه الـ `catch`.

---

### Section 2: Generics (Erasure & Wildcards)

#### Q6: Invariance of Generics

**(Source: Lesson 7 - Slide 305, 306)**

```
import java.util.*;
public class GenericTrap {
    public static void main(String[] args) {
        List<Object> list = new ArrayList<String>();
        list.add("Hello");
    }
}
```

**What is the result?** 
A. Compiles and runs
B. Compilation Error: Incompatible types 
C. Runtime `ClassCastException`
D. Runtime `ArrayStoreException`

**الإجابة: B (Compilation Error: Incompatible types)**

**🧠 تحليل السينيور:** الـ Generics في الجافا **Invariant**. يعني `List<String>` ليست ابناً لـ `List<Object>` (رغم إن `String` ابن لـ `Object`). لو الجافا سمحت بده، كان ممكن تضيف `Integer` في الليستة عن طريق الـ Reference بتاع `Object`، وده هيكسر الـ Type Safety للـ ArrayList الأصلية.

---

#### Q7: The Wildcard Add Block

**(Source: Lesson 7 - Slide 309)**

```
import java.util.*;
public class WildcardTrap {
    public static void main(String[] args) {
        List<?> list = new ArrayList<String>();
        list.add("Hello");
    }
}
```

**What is the result?** 
A. Compiles and adds "Hello"
B. Compilation Error at initialization 
C. Compilation Error at `list.add` 
D. Runtime Error

**الإجابة: C (Compilation Error at list.add)**

**🧠 تحليل السينيور:** الـ Unbounded Wildcard `List<?>` معناها: "ليستة فيها حاجة، بس أنا معرفش إيه هي". بما إن الكومبايلر مش عارف النوع، هو بيمنعك تضيف **أي حاجة** (ماعدا `null`) عشان يضمن الـ Type Safety. هو مش عارف هل الليستة دي مستنية String ولا Integer ولا Dog.

---

#### Q8: Type Erasure Clash

**(Source: Lesson 7 - Slide 307)**

```
import java.util.*;
public class ErasureTrap {
    public void sort(List<String> list) {}
    public void sort(List<Integer> list) {}
}
```

**What happens when compiling this class?** 
A. Compiles successfully (Overloading works)
B. Compilation Error: Name clash 
C. Compilation Error: Duplicate method signature
D. Runtime Error

**الإجابة: C (Compilation Error: Duplicate method signature)**

**🧠 تحليل السينيور:** بسبب الـ **Type Erasure**، الجافا بتمسح الجينيريكس وقت الكومبايليشن. بعد المسح، الاتنين بيتحولوا لـ: `public void sort(List list)`. الكومبايلر هيشوف ميثودين بنفس الاسم ونفس الباراميتر (الـ Raw Type)، وده ممنوع (Duplicate Signature).

---

#### Q9: PECS Rule (Producer Extends)

**(Source: Lesson 7 - Slide 309, 310)**

```
import java.util.*;
public class PECSTrap {
    public static void main(String[] args) {
        List<? extends Number> list = new ArrayList<Integer>();
        list.add(10);
    }
}
```

**What is the result?** 
A. Compiles and runs 
B. Compilation Error at initialization C. Compilation Error at `list.add` D. Runtime Exception

**الإجابة: C (Compilation Error at list.add)**

**🧠 تحليل السينيور:** القاعدة: **Producer Extends, Consumer Super (PECS)**. لما تستخدم `? extends Number`، الليستة بتبقى **Read-only** (ممكن تقرأ منها Number)، لكن ممنوع تكتب فيها. الكومبايلر مش ضامن إن الـ `ArrayList` الحقيقية هي `Integer` (ممكن تكون `Double`)، فبيمنع الإضافة تماماً.

---

#### Q10: Generic Method Syntax

**(Source: Lesson 7 - Slide 307 - Implicit)**

```
public class SyntaxTrap {
    public static T void print(T t) {
        System.out.println(t);
    }
}
```

**What is the result?** A. Compiles successfully B. Compilation Error: Cannot resolve symbol 'T' C. Compilation Error: Invalid return type D. Warnings only

**الإجابة: B (Compilation Error: Cannot resolve symbol 'T')**

**🧠 تحليل السينيور:** عشان تعرف Generic Method، لازم تعلن عن الـ Type Parameter `<T>` **قبل** الـ Return Type. الكود الصح: `public static <T> void print(T t)`. من غير الـ `<T>`، الجافا بتفتكر إن `T` ده اسم كلاس حقيقي، ومش بتلاقيه فبتضرب Error.

---

### Section 3: Functional Interfaces & Lambdas

#### Q11: Valid Functional Interface?

**(Source: Lesson 8 - Slide 215)**

```
@FunctionalInterface
interface SmartFunction {
    void execute();
    String toString();
    boolean equals(Object o);
}
```

**What is the result of compiling this interface?** A. Compilation Error: Multiple abstract methods B. Compilation Error: toString/equals cannot be abstract C. Compiles successfully D. Compilation Error: Missing default implementation

**الإجابة: C (Compiles successfully)**

**🧠 تحليل السينيور:** خدعة الـ **Object Methods Loophole**. أي ميثود `public` موجودة في كلاس `Object` (زي `toString`, `equals`) لا يتم احتسابها ضمن الـ Abstract Methods في الإنترفيس، لأن أي implementation للإنترفيس غصب عنه هيورثهم من `Object`. الإنترفيس ده فيه ميثود واحدة حقيقية (`execute`)، فهو Valid Functional Interface.

---

#### Q12: Predicate Type Mismatch

**(Source: Lesson 8 - Slide 220)**

```
import java.util.function.Predicate;
public class LambdaTrap {
    public static void main(String[] args) {
        Predicate<String> p = s -> s.length();
    }
}
```

**What is the result?** A. Compiles and runs B. Compilation Error: Incompatible types (int cannot be converted to boolean) C. Compilation Error: Missing return statement D. Runtime Error

**الإجابة: B (Compilation Error: Incompatible types)**

**🧠 تحليل السينيور:** الـ `Predicate` الميثود بتاعته `test` بترجع `boolean`. الكود `s -> s.length()` بيرجع `int`. الجافا مش بتحول `int` لـ `boolean` (زي C++). لازم اللمبدا ترجع `true/false` (مثلاً `s.length() > 5`).

---

#### Q13: Variables in Lambda (Effectively Final)

**(Source: Lesson 8 - Slide 232 Implicit, Slide 349)**

```
import java.util.function.Supplier;
public class ScopeTrap {
    public static void main(String[] args) {
        int x = 10;
        Supplier<Integer> s = () -> x;
        x++;
        System.out.println(s.get());
    }
}
```

**What is the result?** A. Prints `10` B. Prints `11` C. Compilation Error at `x++` D. Compilation Error inside lambda

**الإجابة: D (Compilation Error inside lambda)**

**🧠 تحليل السينيور:** المتغيرات المحلية (Local Variables) المستخدمة جوه اللمبدا لازم تكون **Effectively Final** (قيمتها مبتتغيرش بعد التعريف). بما إننا عملنا `x++`، المتغير `x` مبقاش final، وبالتالي ممنوع اللمبدا تشوفه. الكومبايلر هيشتكي عند سطر اللمبدا: "local variables referenced from a lambda expression must be final or effectively final".

---

#### Q14: Consumer Return Void

**(Source: Lesson 8 - Slide 219)**

```
import java.util.function.Consumer;
public class ConsumerTrap {
    public static void main(String[] args) {
        Consumer<String> c = s -> { return "Processed " + s; };
    }
}
```

**What is the result?** A. Compiles successfully B. Compilation Error: Unexpected return value C. Runtime Error D. Ignores the return value

**الإجابة: B (Compilation Error: Unexpected return value)**

**🧠 تحليل السينيور:** الـ `Consumer` الميثود بتاعته `accept` نوعها `void`. اللمبدا هنا بتحاول ترجع `String` ("Processed..."). ده تعارض في الأنواع (Void vs String). لو عايز ترجع قيمة، استخدم `Function` مش `Consumer`.

---

#### Q15: Primitive Functional Interface Mismatch

**(Source: Lesson 8 - Slide 216/220 - Implied)**

```
import java.util.function.IntPredicate;
public class PrimitiveTrap {
    public static void main(String[] args) {
        IntPredicate ip = (Integer i) -> i > 0;
    }
}
```

**What is the result?** A. Compiles successfully (Autoboxing works) B. Compilation Error: Incompatible parameter types C. Runtime ClassCastException D. Warning only

**الإجابة: B (Compilation Error: Incompatible parameter types)**

**🧠 تحليل السينيور:** الـ `IntPredicate` الميثود بتاعته `test(int value)` بتاخد **Primitive `int`**. أنت في اللمبدا عرفت الباراميتر صراحةً كـ **Wrapper `Integer`**. الجافا مش بتسمح بخلط الأنواع الصريحة في تعريف اللمبدا مع الانترفيس الأصلي لو مختلفين (حتى لو فيه Autoboxing). الحل يا إما تشيل النوع `(i) -> ...` أو تستخدم `(int i) -> ...`.