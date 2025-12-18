### 1. Abstract Class vs. Interface (Java 8+ Rules)

**Source:** Slides 182-197, Textbook Chapter 3

|Feature|Abstract Class (`abstract class`)|Interface (`interface`)|
|:--|:--|:--|
|**Concept**|Represents an **"IS-A"** relationship (Identity). Used for sharing **code** among related classes.|Represents a **"CAN-DO"** relationship (Capability). Used for sharing **behavior** among unrelated classes.|
|**Methods**|Can have `abstract`, concrete, `protected`, `private`, `static`, and `final` methods.|All methods are implicitly `public abstract`. Can have `default`, `static` (Java 8), and `private` (Java 9) methods.|
|**Variables**|Can have any type (instance, static, final, non-final, private, protected).|All variables are implicitly **`public static final`** (Constants).|
|**Constructors**|**Yes.** Used to initialize state (called via `super()`).|**No.** Interfaces do not hold state.|
|**Inheritance**|Single Inheritance (`extends`).|Multiple Inheritance (`implements`).|
|**Syntax**|`abstract class Dog extends Animal { ... }`|`class Dog implements Pet, Runnable { ... }`|
|**🎓 Exam Tip**|**The "State" Trap:** If you need to store non-final instance variables (state), you **MUST** use an Abstract Class. Interfaces cannot hold instance state.|**The "Default" Trap:** If two interfaces define the same `default` method signature, the implementing class **MUST** override it to resolve the ambiguity (Diamond Problem).|

---

### 2. Comparable vs. Comparator

**Source:** Textbook Chapter 5 (Sorting & Collections), Slides 458-459

|Feature|`Comparable<T>`|`Comparator<T>`|
|:--|:--|:--|
|**Package**|`java.lang`|`java.util`|
|**Philosophy**|**"I compare myself."** Defines the _natural ordering_ of the object.|**"I judge two others."** Defines a _custom ordering_ (e.g., sort by length instead of alphabetical).|
|**Method**|`int compareTo(T other)`|`int compare(T o1, T o2)`|
|**Implementation**|Implemented **inside** the class itself (e.g., inside `Student`).|Implemented in a **separate** class or Lambda (e.g., `NameSorter`).|
|**Invocation**|`Collections.sort(list)` (Uses natural order).|`Collections.sort(list, comparator)` (Uses custom order).|
|**Syntax**|`return this.id - other.id;`|`return o1.id - o2.id;`|
|**🎓 Exam Tip**|**The Signature Trap:** `Comparable` takes **one** argument. `Comparator` takes **two**. Don't mix them up!|**Consistency:** Ideally, `compareTo` returning 0 should imply `equals` returns `true`.|

---

### 3. Array vs. ArrayList vs. LinkedList

**Source:** Slides 91-93 (Arrays), Slides 401-433 (Collections Framework)

|Feature|Array (`[]`)|ArrayList|LinkedList|
|:--|:--|:--|:--|
|**Size**|**Fixed** at creation. Cannot grow.|**Dynamic.** Grows automatically (backing array resizing).|**Dynamic.** Grows automatically (node linking).|
|**Performance (Read)**|**O(1)**. Instant access by index.|**O(1)**. Fast random access.|**O(n)**. Slow. Must traverse nodes from head/tail.|
|**Performance (Insert/Delete)**|N/A (Fixed size).|**Slow (O(n))** in middle (requires shifting elements).|**Fast (O(1))** if using Iterator (just changes pointers).|
|**Primitives**|Supports `int`, `double`, etc. directly.|**No.** Requires Wrappers (`Integer`, `Double`).|**No.** Requires Wrappers.|
|**Memory**|Low overhead.|Medium overhead (capacity > size).|High overhead (stores Node objects + pointers).|
|**🎓 Exam Tip**|**The "Remove" Trap:** Arrays do not have a `remove` method. You must create a new array to resize.|**The "Capacity" Trap:** `new ArrayList(10)` sets _capacity_, not _size_. The list is still empty!|**The Queue Tip:** `LinkedList` implements `Deque` and `Queue`. Use it when you need Stack/Queue behavior.|

---

### 4. Checked vs. Unchecked Exceptions

**Source:** Slides 257-260, Textbook Chapter 4

|Feature|Checked Exception|Unchecked Exception (Runtime)|
|:--|:--|:--|
|**Parent Class**|Extends `Exception` (but not `RuntimeException`).|Extends `RuntimeException`.|
|**Philosophy**|**External Failure** (Bad Luck). The program can anticipate and recover (e.g., File missing, Network down).|**Programming Error** (Your Fault). Logic bugs that should be fixed, not caught (e.g., Null pointer, Bad array index).|
|**Compiler Rule**|**Handle or Declare.** You MUST use `try-catch` OR `throws`.|**Ignored.** The compiler does not force you to handle them.|
|**Examples**|`IOException`, `SQLException`, `ClassNotFoundException`.|`NullPointerException`, `ClassCastException`, `ArithmeticException`.|
|**🎓 Exam Tip**|**The Override Rule:** An overriding method cannot throw a **broader** checked exception than the parent, but it CAN throw any **Unchecked** exception it wants.|**The "Error" Trap:** `Error` (like `OutOfMemoryError`) is unchecked, but you should rarely catch it. The JVM is dying.|

---

### 5. Overloading vs. Overriding

**Source:** Textbook Chapter 3, Slide 263

|Feature|Overloading|Overriding|
|:--|:--|:--|
|**Definition**|Same method name, **different** parameters (in same class).|Same method name, **same** parameters (in child class).|
|**Signatures**|Must be **Different** (Parameter list changes).|Must be **Identical** (Signature matches parent).|
|**Return Type**|Can be anything.|Must be **Covariant** (Same type or a subclass).|
|**Access Modifiers**|Can be anything.|Can be **Same or Broader** (cannot restrict access).|
|**Exceptions**|Can throw anything.|Can throw **Same or Narrower** checked exceptions (or none). Cannot throw broader checked exceptions.|
|**Binding**|**Static** (Compile-time). Decided by Reference Type.|**Dynamic** (Runtime). Decided by Actual Object Type.|
|**🎓 Exam Tip**|**The Primitive Trap:** Java picks the most specific overload. `f(int)` wins over `f(long)` which wins over `f(varargs)`.|**The "Private" Trap:** You cannot override a `private` method. If you define a method with the same name in a child, it is a **new** method, not an override.|

---

### 6. map() vs flatMap() in Streams

**Source:** Slide 360, Slide 434-436

|Feature|`map()`|`flatMap()`|
|:--|:--|:--|
|**Purpose**|**Transformation.** Converts one element into exactly one element.|**Flattening.** Converts one element into zero, one, or many elements (Stream of Streams).|
|**Input -> Output**|`T -> R`|`T -> Stream<R>`|
|**Structure**|Preserves structure. `[A, B]` -> `[A', B']`.|Flattens structure. `[[A, B], [C]]` -> `[A, B, C]`.|
|**Example**|`Stream.of("cat").map(s -> s.length())` Result: `Stream<Integer>` (3)|`Stream.of(list1, list2).flatMap(l -> l.stream())` Result: One big combined Stream.|
|**🎓 Exam Tip**|**One-to-One:** Use `map` when for every input item, you produce one output item.|**One-to-Many:** Use `flatMap` when each input item produces a List/Stream, and you want to merge them all into a single pipeline.|