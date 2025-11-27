# **Decorate it All...**

*This article is based on the findings and concepts of Yegor Bugayenko on object-oriented programming and the Elegant Objects approach.*

## **1\. Introduction and Definition**

The Decorator Pattern is one of the most elegant and powerful design patterns in object-oriented programming. It allows adding functionality to objects without changing their interface. Unlike many other patterns that often seem overly complex, the Decorator Pattern stands out for its simplicity and versatility.

### **What are Decorators?**

A Decorator is a class that implements an interface while encapsulating an instance of the same interface. Through this composition, Decorators can add supplementary behavior without modifying the original implementation. The biggest advantage of Decorators lies in their composability – they make our code composable.

The following diagram visualizes the structure and roles in the Decorator Pattern.

classDiagram  
    class Component {  
        \<\<interface\>\>  
        \+operation()  
    }  
    class ConcreteComponent {  
        \+operation()  
    }  
    class Decorator {  
        \<\<abstract\>\>  
        \-Component component  
        \+operation()  
    }  
    class ConcreteDecoratorA {  
        \+operation()  
    }  
    class ConcreteDecoratorB {  
        \+operation()  
    }

    Component \<|.. ConcreteComponent  
    Component \<|.. Decorator  
    Decorator \<|-- ConcreteDecoratorA  
    Decorator \<|-- ConcreteDecoratorB  
    Decorator o-- Component

**Explanation of the Roles:**

Component

* Interface or base class that defines the behavior.

ConcreteComponent

* Base class that implements the standard behavior.

Decorator

* Abstract class that implements the same interface as Component and holds a reference to a Component object (Delegation).

ConcreteDecoratorA and ConcreteDecoratorB

* Concrete Decorators that extend the behavior by overriding methods and delegating to the "inner" object.

## **2\. Practical Examples**

### **2.1 Vertical and Horizontal Decorating**

There are two fundamental approaches to Decorating: **Vertical** and **Horizontal**.

**Vertical Decorating:**

Numbers numbers \= new Sorted(  
    new Unique(  
        new Odds(  
            new Positive(  
                new ArrayNumbers(1, \-2, 3, 0, 2\)  
            )  
        )  
    )  
);

Each Decorator implements the Numbers interface and adds a piece of functionality.

**Horizontal Decorating:**

interface Diff {  
    Iterable\<Integer\> apply(Iterable\<Integer\> origin);  
}

Numbers numbers \= new Modified(  
    new ArrayNumbers(1, \-2, 3, 0, 2),  
    new Positive(),  
    new Odds(),  
    new Unique(),  
    new Sorted()  
);

Vertical Decorating is simpler to implement and suitable for smaller objects with few methods. As the number of Decorators grows, switching to Horizontal Decorating is recommended.

### **2.2 Defensive Programming with Validating Decorators**

Instead of embedding validation logic directly into a class, we should use Validating Decorators:

interface Report {  
    void export(File file);  
}

class NoNullReport implements Report {  
    private final Report origin;

    NoNullReport(Report rep) {  
        this.origin \= rep;  
    }

    @Override  
    void export(File file) {  
        if (file \== null) {  
            throw new IllegalArgumentException("File is NULL");  
        }  
        this.origin.export(file);  
    }  
}

class NoWriteOverReport implements Report {  
    private final Report origin;

    NoWriteOverReport(Report rep) {  
        this.origin \= rep;  
    }

    @Override  
    void export(File file) {  
        if (file.exists()) {  
            throw new IllegalArgumentException("File already exists");  
        }  
        this.origin.export(file);  
    }  
}

Usage:

Report report \= new NoNullReport(  
    new NoWriteOverReport(  
        new DefaultReport()  
    )  
);  
report.export(file);

This approach results in smaller objects, and smaller objects mean higher maintainability. The DefaultReport class remains small, regardless of how many validations are added in the future.

### **2.3 Synchronized Decorators for Thread-Safety**

No class should need to be thread-safe; instead, all classes should provide synchronized Decorators.

interface Position {  
    void increment();  
}

class SimplePosition implements Position {  
    private int number \= 0;

    @Override  
    public void increment() {  
        int before \= this.number;  
        int after \= before \+ 1;  
        this.number \= after;  
    }  
}

class SyncPosition implements Position {  
    private final Position origin;

    SyncPosition(Position pos) {  
        this.origin \= pos;  
    }

    @Override  
    public synchronized void increment() {  
        this.origin.increment();  
    }  
}

If thread-safety is needed:

Position position \= new SyncPosition(new SimplePosition());

If not:

Position position \= new SimplePosition();

Making class functionality both extensive and thread-safe violates the Single Responsibility Principle.

### **2.4 Composable Decorators vs. Imperative Utility Methods**

In many codebases, imperative Utility methods are found instead of Decorators. Classes like StringUtils, FileUtils, or IOUtils are typical examples of this approach. Utility classes only possess static methods and encapsulate no state. They lead to procedural code that is difficult to test and less maintainable.

Consider a Text interface:

interface Text {  
    String read();  
}

**Imperative with Utility Methods (Bad Design):**

Text text \= new TextInFile(new File("/tmp/a.txt"));  
String content \= text.read();  
content \= StringUtils.trim(content);  
content \= StringUtils.toUpperCase(content);  
content \= removeUnprintable(content);

**Declarative with Decorators (Good Design):**

Text text \= new AllCapsText(  
    new TrimmedText(  
        new PrintableText(  
            new TextInFile(new File("/tmp/a.txt"))  
        )  
    )  
);  
String content \= text.read();

In the declarative variant, a Text instance is first created through the composition of multiple Decorators, without anything being executed. Processing only begins when read() is called. This is a crucial difference from the imperative style.

### **2.5 Decorating Envelopes**

Sometimes we need Decorators that merely aggregate other Decorators – so-called "Decorating Envelopes":

class RsHtml implements Response {  
    RsHtml(final String text) {  
        this(  
            new RsWithType(  
                new RsWithStatus(text, 200),  
                "text/html"  
            )  
        )  
    }

    // All Response methods are forwarded  
}

Instead of repeating the composition of Decorators everywhere, you use RsHtml. It is very practical, but the implementation is verbose, as all method calls must be forwarded.

### **2.6 Decorating MVC: OOP instead of Classic**

In classic MVC architectures (Model-View-Controller), presentation logic is separated from domain logic. With the Decorator Pattern, however, presentation logic can be implemented as an extension of the model through composition.

### **2.6.1 Classic MVC**

Separation of Model and View:  
The Model contains only the data and business logic. The View is a separate layer that "observes" the Model and renders its data.  
Controller:  
Mediates between Model and View, receives user input, and updates the Model and/or View.  
Presentation Logic:  
Resides in the View or the Controller. The Model knows nothing about its presentation.  
Typical:  
View asks the Model for data (e.g., user.getName()) and renders it.  
Example (classic):

// Model  
class User {

    String name;  
    String getName() { return name; }  
}

// View  
class UserView {

    void render(User user) {  
        System.out.println("\<span\>" \+ user.getName() \+ "\</span\>");  
    }

}

### **2.6.2 MVC with Decorator (OOP)**

Example: Presentation Decorator for a Model

public interface User {  
    String name();  
}

public class SimpleUser implements User {

    private final String name;

    public SimpleUser(String name) {  
        this.name \= name;  
    }

    @Override  
    public String name() {  
        return name;  
    }  
}

// Decorator for HTML presentation  
public class HtmlUser implements User {

    private final User origin;

    public HtmlUser(User origin) {  
        this.origin \= origin;  
    }

    @Override  
    public String name() {  
        return "\<span class='user'\>" \+ origin.name() \+ "\</span\>";  
    }  
}

// Decorator for JSON presentation  
public class JsonUser implements User {

    private final User origin;

    public JsonUser(User origin) {  
        this.origin \= origin;  
    }

    @Override  
    public String name() {  
        return "{\\"user\\": \\"" \+ origin.name() \+ "\\"}";  
    }  
}

**Usage**

// view  
User user \= new HtmlUser(  
    // controller  
    new JsonUser(  
        // model  
        new SimpleUser("Alice")  
    )  
);

System.out.println(user.name());  // \<span class='user'\>{"user": "Alice"}\</span\>

**Impact on Maintainability**

* Modularity: Presentation logic is cleanly encapsulated. New presentation forms (e.g., Markdown, XML) can be added as additional Decorators without changing existing classes.  
* Open for Extension: The Model remains unchanged; Decorators can be combined arbitrarily.  
* No Inheritance Hierarchy: No deep inheritance trees, but flat, independent Decorator classes.

**Impact on Testing Strategy**

* Isolated Tests: Every Decorator can be tested independently. For example, HtmlUser can be tested with a Mock-User without knowing the implementation of SimpleUser.  
* Testable Combinatorics: The combination of different Decorators can be specifically tested to ensure they work together correctly.  
* Mocking Unnecessary: Since Decorators implement the interface, they can be used directly as Test Doubles.

**Example of a Unit Test (JUnit):**

@Test  
void rendersHtmlUser() {  
    User user \= new HtmlUser(() \-\> "Bob");  
    assertEquals("\<span class='user'\>Bob\</span\>", user.name());  
}

Conclusion:  
With MVC Decoration, presentation logic becomes modular, testable, and arbitrarily extensible. The strict separation of Model and View is replaced by composition – this significantly increases maintainability and simplifies the testing strategy.

## **2.7 Efficient Delegation: Reducing Boilerplate**

Delegation is the technical foundation of the Decorator Pattern. With large interfaces, the pure forwarding of many methods can lead to boilerplate. The following techniques reduce this effort without compromising the principles of **True Encapsulation**, **Immutability**, and **Composition over Inheritance**.

### **2.7.1 Abstract Envelope Base Class (Centralizing Delegation)**

A central **Envelope** base class implements the interface completely and forwards all calls to origin. Concrete Decorators only override the methods they actually modify.

public interface Document {  
    String read();  
    void write(String content);  
    int pageCount();  
    void print();  
}

// Central Delegation Base Class  
public abstract class DocumentEnvelope implements Document {

    protected final Document origin;

    protected DocumentEnvelope(final Document origin) {  
        this.origin \= origin;  
    }

    @Override public String read() {  
        return origin.read();  
    }

    @Override public void write(final String content) {  
        origin.write(content);  
    }  
    @Override public int pageCount() {  
        return origin.pageCount();  
    }

    @Override public void print() {  
        origin.print();  
    }  
}

// Small, focused Decorator  
public final class LoggingDocument extends DocumentEnvelope {

    public LoggingDocument(final Document origin) {  
        super(origin);  
    }

    @Override public void print() {  
        System.out.println("print() called");  
        super.print(); // delegates to the Envelope  
    }  
}

**Advantage:** One-time boilerplate in the Envelope, minimal work per Decorator afterward. Fits directly with *Decorating Envelopes*.

### **2.7.2 Default Methods in the Interface (Java 8+)**

If standard behavior is semantically meaningful, **Default Methods** can encapsulate delegation for common variations. The Decorator (or the object) implements only the methods that truly differ.

@FunctionalInterface  
public interface Text {  
    String read();

    default Text trimmed() {  
        return () \-\> read().trim();  
    }

    default Text upperCased() {  
        return () \-\> read().toUpperCase();  
    }  
}

// Usage without new classes:  
Text t \= new TextInFile(Path.of("/tmp/a.txt"))  
                        .trimmed()  
                        .upperCased();

String content \= t.read();

**Note:** Default methods are part of the contract. Use them sparingly, only when they are functionally correct.

### **2.7.3 Mini‑Factory (Decorating Envelope) for Recurring Chains**

Common Decorator combinations are encapsulated in a **Mini‑Factory**/Envelope so that the wiring is not repeated everywhere.

import java.util.Objects;  
import java.util.function.Supplier;

public class PermissionCheckedDocument extends DocumentEnvelope {

    private final Supplier\<Boolean\> allowed;

    public PermissionCheckedDocument(Document origin, Supplier\<Boolean\> allowed) {  
        super(origin);  
        this.allowed \= Objects.requireNonNull(allowed);  
    }

    @Override  
    public void write(final String content) {  
        if (\!allowed.get()) {  
            throw new SecurityException("Write not allowed");  
        }  
        super.write(content);  
    }  
}

public class SecureLoggedDocument extends DocumentEnvelope {

    public SecureLoggedDocument(Document origin, Supplier\<Boolean\> allowed) {  
        // Combines Permission-Check \+ Logging  
        super(new LoggingDocument(new PermissionCheckedDocument(origin, allowed)));  
    }  
}

// Usage:  
Document doc \= new SecureLoggedDocument(  
                new FileDocument(  
                    Path.of("doc.pdf")  
                ),  
                () \-\> currentUser().canWrite()  
);

doc.write("…");

**Advantage:** One class encapsulates the common combination, production code remains lean.

### **2.7.4 Single‑Method‑Interfaces (SAM) without Reflection and Lombok**

With **Single‑Abstract‑Method** (SAM) Interfaces, delegation can be extremely lightweight.

@FunctionalInterface  
public interface Text {  
    String read();  
}

// Universal, composable Decorator  
public class MappedText implements Text {

    private Text origin;  
    private Function\<String, String\> mapping;

    public MappedText(Text origin, Function\<String, String\> mapping) {  
        this.origin \= origin;  
        this.mapping \= mapping;  
    }

    @Override  
    public String read() {  
        return mapping.apply(origin.read());  
    }  
}

// Usage  
Text original \= new TextInFile(Path.of("/tmp/a.txt"));  
Text upper \= new MappedText(original, String::toUpperCase);  
Text trimmedUpper \= new MappedText(upper, String::trim);  
String content \= trimmedUpper.read();

// Or inline with Lambdas  
Text t \= () \-\> original.read().trim().toUpperCase();

**Advantage:** Minimal code, maximum composition. Ideal for *Printers instead of Getters* and behavior-oriented APIs.

### **2.8 Fluent Interfaces vs. Decorators**

Fluent Interfaces, coined by Martin Fowler, are a very convenient way to communicate with objects. They make Facades easier to use but ruin the internal design and significantly complicate maintainability.

**Example of a Fluent Interface:**

String html \= new JdkRequest("\[https://www.google.com\](https://www.google.com)")  
    .method("GET")  
    .fetch()  
    .as(RestResponse.class)  
    .assertStatus(200)  
    .body();

This looks elegant and reads almost like natural language. But the internal design suffers massively:

**The Problems with Fluent Interfaces:**

1. **Large, Bloated Classes**: The JdkRequest class must contain the methods method(), fetch(), and many others. If new functionality is needed, the class must continually grow.  
2. **Difficult Extensibility**: Every new function means new methods in already large classes. This heavily violates the Single Responsibility Principle.  
3. **Maintenance Problems**: With 43 methods (as in Java's Stream API), the class becomes a nightmare for maintainers.  
4. **Violation of OOP Principles**: Fluent Interfaces lead to poor cohesion and tight coupling.

**The Better Alternative with Decorators:**

String html \= new BodyOfResponse(  
    new ResponseAssertStatus(  
        new FetchedResponse(  
            new RequestWithMethod(  
                new JdkRequest("\[https://www.google.com\](https://www.google.com)"),  
                "GET"  
            )  
        ),  
        200  
    )  
).toString();

**Advantages of this Solution:**

1. **Small, Focused Classes**: Each class has a single responsibility.  
2. **Easy Extensibility**: New functionality \= new Decorator class, no change to existing classes.  
3. **Simplified Testing**: Small classes are easier to test.  
4. **Immutability**: All classes can be immutable.  
5. **Loose Coupling**: Decorators are independent of each other.

The disadvantage is that IDE auto-completion does not work as well, and the syntax looks less like a DSL. But this is a small price for the significantly better maintainability.

**The Conflict:**

There appears to be a conflict between usability and maintainability:

* Fluent Interfaces are good for users but bad for developers.  
* Small objects are good for developers but harder to use.

However, this conflict only exists if one is accustomed to large classes and procedural programming. For true OOP developers, a large number of small classes is an advantage, not a disadvantage.

**Pragmatic Checklist**

* **Apply ISP:** Functionally decompose large interfaces (Readable/Writable/Printable…).  
* **Envelope Base Class:** Delegate centrally once, only override the delta per Decorator.  
* **Default Methods:** Only for stable, semantically correct standard logic.  
* **Mini-Factory/Envelopes:** Encapsulate recurring chains, no copy‑paste.  
* **SAM Interfaces:** Prefer for behavior-oriented, small contracts.

## **3\. Limits and Drawbacks of the Decorator Pattern**

Despite all its advantages, the Decorator Pattern also has limitations and drawbacks that one should be aware of:

### **3.1 Complexity and "Decorator Soup"**

The biggest problem is the potential complexity resulting from many nested Decorators. Excessive use can create a confusing hierarchy that is difficult to understand. This is sometimes referred to as "Decorator Soup" – a convoluted class hierarchy.

String result \= new UpperCaseText(  
    new TrimmedText(  
        new EncodedText(  
            new ValidatedText(  
                new CachedText(  
                    new LoggedText(  
                        new TextInFile("data.txt")  
                    )  
                )  
            )  
        )  
    )  
).read();

With too many Decorators, it becomes difficult to maintain an overview.

### **3.2 Object Identity and Type Checking**

A Decorator and its wrapped component are not identical, so type checks fail. This can lead to unexpected behavior in code that relies on object identity, such as equality checks with \===.

Text original \= new SimpleText("Hello");  
Text decorated \= new UpperCaseText(original);

// This fails:  
if (decorated instanceof SimpleText) {  
    // Will never be executed  
}

### **3.3 Increased Number of Classes**

Implementing Decorators for multiple features leads to many classes. This can make the codebase more extensive and harder to manage, especially with numerous Decorator combinations.

### **3.4 Performance Overhead**

Each Decorator introduces one layer of method delegation, which can lead to a slight performance overhead. With deep Decorator chains, this can become measurable, although it is rarely a real problem in practice.

### **3.5 Instantiation Complexity**

Decorators can complicate the component instantiation process, as one must not only instantiate the component but also wrap it in several Decorators.

### **3.6 Limitations in Interface Inheritance**

The Decorator Pattern is based on interface inheritance and can therefore only extend the behavior of objects through interfaces or abstract classes. It cannot add new data fields to the original object.

### **3.7 The Problem with Test Data**

An interesting aspect from the testing perspective: While the Builder Pattern (related to Decorators) works well for test data, the Object Mother Pattern can become problematic when there are many variations of the test data.

**Object Mother vs. Test Data Builder:**

The Object Mother Pattern is an attempt to provide complex test objects:

// Object Mother approach  
Invoice invoice \= TestInvoices.newDeerstalkerAndCapeInvoice();

The problem: Over time, the Object Mother becomes bloated, confusing, and difficult to maintain. A new factory method must be added for every new variation.

The better approach is Test Data Builders (a form of Decorators):

Invoice invoice \= new InvoiceBuilder()  
    .withRecipient("Sherlock Holmes")  
    .withAddress("221b Baker Street")  
    .withItem(new ItemBuilder().withName("Deerstalker").build())  
    .build();

Test Data Builders make it easier to create test data inline without making tests brittle or creating much duplication. Tests are isolated from the aspects of the object structure that are irrelevant to the test.

### **3.8 When NOT to Use Decorators**

* **For Simple Functionality**: If the functionality to be added is very simple, it may be easier to add it directly to the class rather than creating a new Decorator.  
* **For Third-Party Classes**: It is often difficult to apply the Decorator Pattern to third-party component classes.  
* **For Final Classes**: Many programming languages have the final keyword, which prevents further extension of a class.

### **3.9 Pragmatic Approach**

The solution lies in pragmatic use:

1. **For small interfaces** (1-3 methods): Use Vertical Decorating.  
2. **For large interfaces**: Consider Horizontal Decorating or a Facade.  
3. **For tests**: Combine Object Mother with Test Data Builders.  
4. **In production code**: Use Decorating Envelopes to encapsulate common combinations.

The Decorator Pattern is powerful, but like all patterns, it should be used with caution. The pattern can lead to a system of "many small objects" that all look alike to the maintainer – this can be a maintenance nightmare.

## **4\. References**

### **Primary Sources (Yegor Bugayenko)**

1. Vertical and Horizontal Decorating (2015)  
   https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html  
2. Composable Decorators vs. Imperative Utility Methods (2015)  
   https://www.yegor256.com/2015/02/26/composable-decorators.html  
3. Defensive Programming via Validating Decorators (2016)  
   https://www.yegor256.com/2016/01/26/defensive-programming.html  
4. Synchronized Decorators to Replace Thread-Safe Classes (2017)  
   https://www.yegor256.com/2017/01/17/synchronized-decorators.html  
5. Decorating Envelopes (2017)  
   https://www.yegor256.com/2017/01/31/decorating-envelopes.html  
6. OOP Alternative to Utility Classes (2014)  
   https://www.yegor256.com/2014/05/05/oop-alternative-to-utility-classes.html  
7. MVC vs OOP (2016)  
   https://www.yegor256.com/2016/12/13/mvc-vs-oop.html  
8. Fluent Interfaces Are Bad for Maintainability (2018)  
   https://www.yegor256.com/2018/03/13/fluent-interfaces.html  
9. Test Data Builders: an alternative to the Object Mother pattern (2007) \- Nat Pryce  
   http://www.natpryce.com/articles/000714.html

### **Technical References**

* Design Patterns: Elements of Reusable Object-Oriented Software (Gang of Four)  
* Effective Java by Joshua Bloch  
* Growing Object-Oriented Software, Guided by Tests by Steve Freeman, Nat Pryce

### **Further Reading**

* Bugayenko, Y. (2016). *Elegant Objects, Volume 1*  
* Bugayenko, Y. (2017). *Elegant Objects, Volume 2*  
* Gang of Four: *Design Patterns: Elements of Reusable Object-Oriented Software*
