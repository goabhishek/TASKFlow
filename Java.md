# Core Java Basics

## 1. What is Java?
**Interview answer:** Java is a statically typed, class-based language. Java source is compiled to bytecode, which runs on a Java Virtual Machine (JVM). The JVM enables portability across compatible platforms.

## 2. JDK vs JRE vs JVM
- **JVM:** executes bytecode.
- **JRE:** runtime components needed to run Java applications; terminology varies across modern distributions.
- **JDK:** development tools such as the compiler plus runtime components.

## 3. Primitive types and reference types
Primitive types include `byte`, `short`, `int`, `long`, `float`, `double`, `char`, and `boolean`. Reference variables refer to objects or arrays and can be `null`.

```java
public class Basics {
    public static void main(String[] args) {
        int age = 26;
        double salary = 81000.0;
        boolean employed = true;
        String name = "Abhi"; // String is a reference type

        System.out.println(name + ": " + age + ", " + salary + ", " + employed);
    }
}
```

## 4. == vs equals()
For primitives, `==` compares values. For references, `==` compares whether both references identify the same object. `equals()` compares logical equality when the class implements it appropriately.

```java
String first = new String("java");
String second = new String("java");

System.out.println(first == second);      // false
System.out.println(first.equals(second)); // true
```

## 5. String immutability
A `String` cannot be changed after creation. Operations that appear to modify it create another string. Use `StringBuilder` for repeated modifications in a single thread.

```java
StringBuilder result = new StringBuilder();
for (int i = 1; i <= 3; i++) {
    result.append(i).append(' ');
}
System.out.println(result.toString()); // 1 2 3
```

## 6. Pass-by-value
Java always passes arguments by value. For object arguments, the copied value is a reference; a method can mutate the referenced object, but reassigning its local reference does not reassign the caller's variable.

## 7. static, final, finally, finalize
- `static`: belongs to the class rather than an instance.
- `final`: prevents reassignment of a variable, overriding of a method, or extension of a class depending on usage.
- `finally`: cleanup block commonly used with `try`/`catch`; try-with-resources is preferred for closeable resources.
- `finalize()`: deprecated and unreliable; do not use for resource cleanup.

## 8. Compile and run
```bash
javac Basics.java
java Basics
```

## Common questions
**Q: Is Java purely object-oriented?** No. It has primitive types and static members, among other features.

**Q: Why is String immutable?** It supports safe sharing, predictable hashing, and security-sensitive use cases; immutability also simplifies concurrency.

**Q: What is the difference between checked and unchecked exceptions?** Checked exceptions must be caught or declared; unchecked exceptions are subclasses of `RuntimeException` (and `Error` is a separate unchecked category).

## Quick revision
- JVM executes bytecode; JDK includes development tools.
- Java is pass-by-value.
- `==` and `equals()` are not interchangeable for objects.
- Strings are immutable.
- Prefer try-with-resources for closeable resources.
"""

files["02-Java/02-oop-concepts.md"] = r"""
# Object-Oriented Programming (OOP) in Java

## 1. Four pillars of OOP
1. **Encapsulation:** keep data and the operations on it together; control access through methods.
2. **Abstraction:** expose essential behavior while hiding implementation details.
3. **Inheritance:** create a new class based on an existing class.
4. **Polymorphism:** the same interface or parent type can represent different implementations.

## 2. Working example
```java
interface Payable {
    double calculatePay();
}

abstract class Employee implements Payable {
    private final String name; // encapsulated field

    protected Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

class FullTimeEmployee extends Employee {
    private final double monthlySalary;

    FullTimeEmployee(String name, double monthlySalary) {
        super(name);
        this.monthlySalary = monthlySalary;
    }

    @Override
    public double calculatePay() {
        return monthlySalary;
    }
}

public class OopDemo {
    public static void main(String[] args) {
        Payable employee = new FullTimeEmployee("Abhi", 81000);
        System.out.println(employee.calculatePay()); // polymorphism
    }
}
```

## 3. Class vs object
A class defines fields and behavior; an object is an instance of a class.

## 4. Overloading vs overriding
- **Overloading:** same method name with different parameter lists; generally resolved at compile time.
- **Overriding:** a subclass provides a compatible implementation of an inherited instance method; dynamic dispatch chooses the implementation at runtime.

```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; } // overloading
}

class Animal {
    String sound() { return "unknown"; }
}
class Dog extends Animal {
    @Override
    String sound() { return "bark"; } // overriding
}
```

## 5. Abstract class vs interface
- Abstract class can contain state, constructors, and abstract/concrete methods.
- Interface defines a contract and can include abstract, default, static, and private methods.
- A class can extend one class but implement multiple interfaces.
- Choose based on whether you need a shared base implementation/state or a capability contract.

## 6. Composition vs inheritance
Composition means one object contains/uses another object. Prefer composition when the relationship is “has-a”; use inheritance for a genuine “is-a” relationship and substitutability.

## 7. SOLID overview
- **S — Single Responsibility:** one main reason to change.
- **O — Open/Closed:** open to extension, closed to modification where practical.
- **L — Liskov Substitution:** subtypes should be usable where their base type is expected.
- **I — Interface Segregation:** prefer focused interfaces.
- **D — Dependency Inversion:** high-level policy depends on abstractions, not concrete details.

## Common interview questions
**Q: Can constructors be overridden?** No. Constructors are not inherited; they can be overloaded.

**Q: Can a static method be overridden?** Static methods are hidden, not overridden through runtime instance dispatch.

**Q: Why encapsulate fields?** To preserve invariants and control how an object's state changes.

**Q: Is multiple inheritance supported in Java?** Classes cannot extend multiple classes, but they can implement multiple interfaces.

## Quick revision
- Encapsulation protects invariants.
- Abstraction exposes contracts.
- Inheritance models “is-a”; composition models “has-a”.
- Overloading changes parameters; overriding changes inherited behavior.
- Use interfaces to enable loose coupling and testability.
"""

files["02-Java/03-strings-arrays.md"] = r"""
# Java Strings, Arrays, and Common Algorithms

## 1. Reverse a string
```java
public class ReverseString {
    static String reverse(String input) {
        return new StringBuilder(input).reverse().toString();
    }

    public static void main(String[] args) {
        System.out.println(reverse("interview")); // weivretni
    }
}
```
Time complexity: O(n); space complexity: O(n) for the resulting string.

## 2. Find the largest array element
```java
static int max(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        throw new IllegalArgumentException("Array must not be empty");
    }

    int largest = numbers[0];
    for (int number : numbers) {
        if (number > largest) largest = number;
    }
    return largest;
}
```
Time: O(n); extra space: O(1).

## 3. Frequency map
```java
import java.util.HashMap;
import java.util.Map;

static Map<Character, Integer> frequencies(String input) {
    Map<Character, Integer> counts = new HashMap<>();
    for (char ch : input.toCharArray()) {
        counts.put(ch, counts.getOrDefault(ch, 0) + 1);
    }
    return counts;
}
```

## 4. Two Sum
Given an array and a target, return indexes of two values whose sum equals the target. Assume exactly one solution exists.

```java
import java.util.HashMap;
import java.util.Map;

static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int needed = target - nums[i];
        if (seen.containsKey(needed)) {
            return new int[] { seen.get(needed), i };
        }
        seen.put(nums[i], i);
    }
    return new int[0];
}
```
Average time: O(n); extra space: O(n). Explain why checking before inserting avoids using the same element twice.

## Common questions
- Why are arrays fixed length? Their length is set at creation; use collections when dynamic size is needed.
- `StringBuilder` vs `StringBuffer`? `StringBuilder` is generally preferred in single-threaded code; `StringBuffer` synchronizes its methods.
- Array vs ArrayList? Arrays have fixed length and can store primitives; `ArrayList` is resizable and stores objects (primitives use wrapper types).

## Practice
- Palindrome check
- Remove duplicates
- First non-repeating character
- Move zeroes to the end
- Merge two sorted arrays
- Binary search
"""

files["02-Java/04-collections-framework.md"] = r"""
# Java Collections Framework

## 1. Main interfaces
- `List`: ordered collection; duplicates allowed.
- `Set`: unique elements.
- `Queue` / `Deque`: queue and double-ended queue operations.
- `Map`: key-value pairs; `Map` is not a subtype of `Collection`.

## 2. Common implementations
| Type | Typical use | Notes |
|---|---|---|
| ArrayList | Indexed reads, general list | Fast random access; middle insert/remove can shift elements |
| LinkedList | Deque operations in some cases | Indexed access is slow; often not the default choice |
| HashSet | Uniqueness and membership | No guaranteed iteration order |
| TreeSet | Sorted unique values | O(log n) typical operations |
| HashMap | Key-value lookup | No guaranteed iteration order |
| TreeMap | Sorted keys | O(log n) typical operations |
| ArrayDeque | Stack/queue | Usually preferable to legacy Stack |

## 3. HashMap example
```java
import java.util.HashMap;
import java.util.Map;

public class MapDemo {
    public static void main(String[] args) {
        Map<String, Integer> scores = new HashMap<>();
        scores.put("Asha", 90);
        scores.put("Ravi", 82);
        scores.put("Asha", 95); // replaces the value for the same key

        System.out.println(scores.get("Asha")); // 95
        System.out.println(scores.containsKey("Ravi")); // true
    }
}
```

## 4. equals() and hashCode()
If two objects are equal according to `equals()`, they must return the same `hashCode()`. Unequal objects may share a hash code. This contract is important for hash-based collections.

## 5. Comparable vs Comparator
- `Comparable<T>` defines a class's natural ordering using `compareTo`.
- `Comparator<T>` defines an external or alternative ordering.

```java
import java.util.Comparator;
import java.util.List;

List<String> names = List.of("Ravi", "Abhi", "Neha");
names.stream()
     .sorted(Comparator.comparingInt(String::length))
     .forEach(System.out::println);
```

## Common questions
**Q: HashMap vs TreeMap?** HashMap offers average constant-time basic operations under good hashing; TreeMap keeps keys sorted and typically uses logarithmic operations.

**Q: ArrayList vs LinkedList?** ArrayList is usually the default for lists because indexed access and iteration are efficient; LinkedList can be useful for certain deque workloads but has node overhead.

**Q: Is HashMap thread-safe?** No, not for concurrent mutation without coordination. Consider `ConcurrentHashMap` for concurrent access patterns.

**Q: Can HashMap have null?** Standard HashMap permits one null key and null values; other Map implementations may differ.

## Quick revision
- Select a collection based on operations and ordering requirements.
- Keep equals/hashCode consistent.
- Use Comparator for custom sorting.
- Don't assume HashMap iteration order.
"""

files["02-Java/05-exceptions-generics-streams.md"] = r"""
# Java Exceptions, Generics, and Streams

## 1. Exception hierarchy
`Throwable` has two major branches: `Error` and `Exception`. `RuntimeException` is an unchecked subclass of `Exception`.

- Checked exceptions: must be caught or declared.
- Unchecked exceptions: `RuntimeException` subclasses; often indicate programming errors or invalid runtime conditions.
- Do not catch and silently ignore exceptions.

## 2. try-with-resources
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

class ReadFile {
    static String firstLine(Path path) throws IOException {
        try (BufferedReader reader = Files.newBufferedReader(path)) {
            return reader.readLine();
        }
    }
}
```
Resources implementing `AutoCloseable` are closed automatically.

## 3. Generics
Generics provide compile-time type safety and reduce casts.

```java
import java.util.ArrayList;
import java.util.List;

List<String> languages = new ArrayList<>();
languages.add("Java");
// languages.add(42); // compile-time error
```

## 4. Streams
Streams process data through operations such as `filter`, `map`, and `reduce`. Intermediate operations are lazy; a terminal operation triggers processing.

```java
import java.util.List;
import java.util.stream.Collectors;

List<String> names = List.of("Abhi", "Asha", "Ravi", "Ananya");

List<String> longNames = names.stream()
    .filter(name -> name.length() >= 5)
    .map(String::toUpperCase)
    .sorted()
    .collect(Collectors.toList());

System.out.println(longNames);
```

## 5. Optional
`Optional<T>` represents a value that may be absent. It is useful as a return type in suitable APIs, but should not be used indiscriminately for every field or parameter.

```java
import java.util.Optional;

Optional<String> username = Optional.ofNullable(System.getenv("APP_USER"));
String displayName = username.orElse("Guest");
```

## Common questions
**Q: throw vs throws?** `throw` throws an exception instance; `throws` declares possible exceptions in a method signature.

**Q: finally vs try-with-resources?** `finally` can run cleanup code; try-with-resources is designed to close resources and handles suppressed exceptions.

**Q: map vs flatMap in streams?** `map` transforms each element; `flatMap` transforms each element into a stream-like result and flattens the results.

**Q: What is a functional interface?** An interface with one abstract method, suitable for lambda expressions; it may still have default and static methods.
"""

files["02-Java/06-multithreading.md"] = r"""
# Java Multithreading and Concurrency Basics

## 1. Process vs thread
A process has its own memory space and resources; threads are execution paths within a process and share process memory.

## 2. Runnable and ExecutorService
For application code, an executor often provides better task management than manually creating a thread for every task.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class ExecutorDemo {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService pool = Executors.newFixedThreadPool(2);

        try {
            pool.submit(() -> System.out.println("Task on " +
                Thread.currentThread().getName()));
        } finally {
            pool.shutdown();
            if (!pool.awaitTermination(5, TimeUnit.SECONDS)) {
                pool.shutdownNow();
            }
        }
    }
}
```

## 3. Race condition and synchronization
A race condition occurs when the result depends on unpredictable timing between concurrent operations. Synchronization can protect shared mutable state.

```java
class SafeCounter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

For highly concurrent counters, consider atomic classes such as `AtomicInteger` when their semantics fit.

## 4. volatile
`volatile` provides visibility and ordering guarantees for a variable between threads, but it does not make compound operations such as `count++` atomic.

## 5. Deadlock
Deadlock can occur when threads wait indefinitely for locks held by one another. Reduce risk by using a consistent lock order, minimizing lock scope, and considering higher-level concurrency utilities.

## Common questions
- `synchronized` vs `volatile`: mutual exclusion/visibility for synchronized blocks vs visibility/order guarantees for volatile variables.
- `Runnable` vs `Callable`: Runnable does not return a result; Callable can return a value and throw checked exceptions.
- Why use a thread pool? It reuses threads and controls concurrent task execution.
- What is thread safety? Correct behavior when accessed concurrently under the specified usage conditions.
"""

files["02-Java/07-java-interview-questions.md"] = r"""
# Core Java and OOP Interview Questions

1. **JDK vs JVM?** JDK contains development tools; JVM executes bytecode.
2. **Why is Java platform-independent?** Bytecode can run on compatible JVM implementations.
3. **Is Java pass-by-reference?** No, Java passes all arguments by value.
4. **== vs equals()?** Primitive value/reference identity comparison vs logical equality as implemented.
5. **Why is String immutable?** Predictable sharing, hashing, security, and simpler concurrency.
6. **What are the four OOP pillars?** Encapsulation, abstraction, inheritance, polymorphism.
7. **Overloading vs overriding?** Different parameter lists vs redefining inherited instance behavior.
8. **Interface vs abstract class?** Contract/capabilities vs shared base state/implementation; a class can implement many interfaces but extend one class.
9. **What is polymorphism?** A parent/interface reference can invoke the implementation appropriate to the actual object.
10. **What is constructor chaining?** Calling another constructor using `this(...)` or a parent constructor using `super(...)`.
11. **What is the equals/hashCode contract?** Equal objects must have equal hash codes.
12. **ArrayList vs LinkedList?** Dynamic array vs linked nodes; choose based on access and mutation patterns.
13. **HashMap vs ConcurrentHashMap?** General map vs implementation designed for concurrent access.
14. **Checked vs unchecked exceptions?** Checked must be caught/declared; unchecked RuntimeException subclasses need not be.
15. **What is try-with-resources?** Automatically closes AutoCloseable resources.
16. **What are generics?** Compile-time type parameters that improve type safety.
17. **What is a Stream?** A pipeline for declarative data processing.
18. **What does volatile do?** Visibility and ordering guarantees, not atomic compound updates.
19. **What is deadlock?** Threads wait on resources in a cycle and cannot proceed.
20. **What are SOLID principles?** Five design principles for maintainable, extensible object-oriented code.
"""

