# Single Responsibility Principle
## A Pragmatic and Objective Definition

**Autor:** Robert Bräutigam  
**Organisation:** MATHEMA Software GmbH  
**Kontakt:** robert@mathema.de  
**Website:** https://javadevguy.wordpress.com/  
**Twitter:** @robertbrautigam

---

## Goal

**TO DEFINE THE SRP IN A PRAGMATICAL AND OBJECTIVE WAY, ALLOWING IT TO BE USED DIRECTLY IN OUR DAY-TO-DAY PROGRAMMING.**

---

## Traditional Definitions

### Definition 1
> "Each software module should have one and only one responsibility."

### Definition 2
> "Each software module should have one and only one reason to change."

### Definition 3
> "Gather together the things that change for the same reasons. Separate those things that change for different reasons."

### Definition 4
> "'Same reason' means it originates from the same business person."

**Really???** These definitions are subjective and hard to apply!

---

## Rationale

**We want maintainability!**

To achieve this, we need to:
- Minimize work for a given amount of change
- Minimize amount of code to read/write

Therefore:
- ✅ Small classes
- ✅ High probability of localized change
- ✅ Low probability of change propagating to other classes

---

## Single Responsibility Principle

### The Pragmatic Definition

```
SRP ≡ MAXIMIZE COHESION ∧ MINIMIZE COUPLING
```

**That's it!** SRP is about **Cohesion** and **Coupling**.

---

## Cohesion & Coupling

**It's about dependencies.**

### Cohesion
- Dependencies **inside** an object make it more **cohesive**
- Methods and fields refer to each other

### Coupling
- Dependencies **between** objects make them more **coupled**
- Objects depend on other objects

### What are dependencies?

1. **Physical relationships**: A method calling another method, or referencing a variable
2. **Semantic relationships**: Implicit knowledge about structure

---

## Physical Dependencies

### This class is cohesive

```java
public final class Amount {
    private final int cents;
    private final Currency currency;
    
    public Amount(int cents, Currency currency) {
        this.cents = cents;
        this.currency = currency;
    }
    
    public Amount subtract(Amount other) {
        if (currency != other.currency) { 
            throw new IllegalArgumentException("Currency mismatch");
        }
        return new Amount(cents - other.cents, currency);
    }
    
    public boolean isEqualTo(Amount other) {
        return subtract(other).cents == 0;
    }
}
```

**Why cohesive?**
- Methods use the fields (`cents`, `currency`)
- Methods call each other (`isEqualTo` uses `subtract`)
- Constructor initializes fields used by methods

---

## Physical Dependencies (Coupling)

### Class has coupling to Amount

```java
public final class Account {
    private Amount balance;
    ...
    
    public void debit(Amount amount) {
        ...
        balance = balance.subtract(amount);
    }
}
```

**Physical coupling:** `Account` depends on `Amount` through the `subtract` method.

This is **acceptable** - visible to the compiler.

---

## Semantic Dependencies

### Class knows too much about Amount

```java
public final class CurrencyConverter {
    public Amount convert(Amount amount, Currency currency) {
        return new Amount(
            amount.getValue() *                    // ← Privacy violation!
            exchange.getDailyRate(
                amount.getCurrency(), currency),  // ← Privacy violation!
            currency
        );
    }
}
```

**Semantic coupling:** `CurrencyConverter` knows about Amount's internal structure (value, currency).

**High probability they change together!**

---

## Semantic Coupling

### The Real Problem

- It is **always a code smell**
- It is also a **design smell**
- Much worse than physical coupling, since it is **invisible** (to the compiler)
- Changes are very likely to propagate through semantic couplings
- Sometimes in **subtle and unexpected ways**
- **Very often facilitated by getters**

---

## Designing a UI: Meters

### Real-World Example

---

## Meter

**Context:** Water or Gas Meters, part of an IoT network

**Requirements:**
- A meter may receive requests for making a readout of current values
- A meter may at any time update its capabilities
- These capabilities have to be displayed on the web interface

---

## Meter Design (First Attempt)

```java
public final class Meter {
    private boolean gzipSupported;
    private Encryption encryptionSupported;
    private X509Certificate encryptionCertificate;
    ...
    
    public void receiveReadoutRequest() {
        // ... using capabilities ...
    }
    
    public void updateCapabilities(...) {
        // ... update capabilities ...
    }
    
    public String displayHtml() {
        return " ... html code ... ";
    }
}
```

**Is this good?** Let's see what "traditional SRP" would say...

---

## Common Interpretation of SRP

### "Violation of SRP!"

```java
public final class Meter {
    private boolean gzipSupported;
    private Encryption encryptionSupported;
    private X509Certificate encryptionCertificate;
    ...
    
    // Getters and Setters
    public boolean getGzipSupported() { ... }
    public Encryption getEncryptionSupported() { ... }
    // ...
}

public final class MeterView {
    public String displayMeter(Meter meter) {
        return "...html with " +
               "meter.getGzipSupported(), " +
               "meter.getEncryptionSupported(), " +
               "meter.getX()...";
    }
}
```

**Result:** Heavy semantic and physical coupling, very unmaintainable!

---

## More Pragmatic Interpretation

### Better Design

```java
public final class Meter {
    private boolean gzipSupported;
    private Encryption encryptionSupported;
    private X509Certificate encryptionCertificate;
    ...
    
    public Component display() {
        return new Tags(
            gzipSupported ? new Tag("GZIP") : null,
            encryptionSupported ? new Tag("ENC") : null,
            encryptionSupported != null ? encryptionSupported.display() : null,
            ...
        );
    }
}
```

**Benefits:**
- No more HTML details in the method
- No details of design exposed
- Tags don't know about Meter internals
- **The meter displays itself!**

---

## "UGH, UI IN THE DOMAIN, I FEEL DIRTY!"

**Maybe that is just a culture of discrimination.**

**Why shouldn't the UI be part of the business?**

---

## UI in the Domain: Cons

Common arguments against:
- ❌ "It's just wrong"
- ❌ "UI is not important!"
- ❌ "I don't want to change business logic because of colors"
- ❌ "What if I want to change the Web UI to Swing?"

---

## UI in the Domain: Pros

Arguments for:
- ✅ UI is by its nature **tightly coupled/cohesive** to the domain
- ✅ UI is usually an **important part** of an application
- ✅ UI is actually **part of the requirements**!
- ✅ Business people actually **talk about the UI**
- ✅ It is part of the **common understanding and vocabulary**!
- ✅ **Details of the UI don't have to be in the Domain!**

---

## Composition and SRP: User Registration

### Real-World Example

---

## User Registration

**Requirements:**
- User may register with username and password
- User may authenticate herself with given password
- At the registration, an email should be sent as confirmation

---

## "Traditional" Design

### "Don't mix SQL with SMTP"

```java
public class UserManager {
    public boolean authenticate(String username, String password) {
        String passwordHash = sql.select("from user ...", ...);
        return HashUtils.match(password, passwordHash);
    }
    
    public void register(String username, String password, 
                        String emailAddress) {
        sql.insert("into user ...", ...);
        new SmtpClient().send(emailAddress,
            "Hello " + username + ", welcome to Application");
    }
}
```

**Problem:** Everything mixed together!

---

## Adding "Traditional" SRP

### Step 1: Extract User class

```java
public class User {
    private String username;
    private String passwordHash;
    private String emailAddress;
    
    // Getters and Setters
}

public class EmailNotificationService {
    private SmtpClient smtpClient;
    ...
    
    public void sendNotification(User user) {
        smtpClient.send(user.getEmailAddress(),
            "Hello " + user.getUsername() + ", welcome to Application");
    }
}
```

---

## Adding "Traditional" SRP

### Step 2: Extract Repository

```java
public class UserRepository {
    ...
    
    public void insert(User user) {
        sql.insert("into user ...",
            user.getUsername(),
            HashUtils.hash(user.getPassword()),
            user.getEmailAddress());
    }
    
    public User select(String username) {
        return sql.select(...);
    }
}
```

**This is still too much!**

---

## Adding "Traditional" SRP

### Step 3: Extract Commands

```java
public class InsertUserCommand {
    public void execute(User user) {
        sql.insert("into user ...",
            user.getUsername(),
            HashUtils.hash(user.getPassword()),
            user.getEmailAddress());
    }
}

public class SelectUserCommand {
    public User execute(String username) {
        return sql.select(...);
    }
}
```

---

## Adding "Traditional" SRP: Result

```java
public class UserManager {
    private InsertUserCommand insertUserCommand;
    private SelectUserCommand selectUserCommand;
    private EmailNotificationService notificationService;
    
    public boolean authenticate(String username, String password) {
        User user = selectUserCommand.execute(username);
        return HashUtils.match(password, user.getPasswordHash());
    }
    
    public void register(String username, String password, 
                        String emailAddress) {
        User user = new User(username, password, emailAddress);
        insertUserCommand.execute(user);
        notificationService.sendNotification(user);
    }
}
```

**Result:**
- ✅ Familiar to most people
- ✅ Seems clean enough
- ❌ **It is the beginning of the end!**

---

## Adding "Traditional" SRP: Analysis

### Problems

- Leads to **high fragmentation** (one method per class designs)
- Leads to **injection/dependency/testing hell**
- Instead of decoupling, we actually have **very tight coupling** (semantic as well as physical)
- **No cohesion**, or very little cohesion
- Has **very little to do with Object-Orientation**

**Look at the vocabulary:**
- User
- UserManager
- InsertUserCommand
- SelectUserCommand
- EmailNotificationService

**This is procedural code with OOP syntax!**

---

## Sidenote: Don't Do Utils

### Instead of static utility classes...

```java
public final class PasswordHash {
    private final byte[] hash;
    private final byte[] salt;
    
    // Static factory methods
    public static PasswordHash compute(String clearText) {
        byte[] randomSalt = ...;
        return compute(randomSalt, clearText);
    }
    
    public static PasswordHash compute(byte[] salt, String clearText) {
        byte[] calculatedHash = ...;
        return new PasswordHash(salt, calculatedHash);
    }
    
    // Instance methods
    public boolean matches(String clearText) {
        return this.equals(new PasswordHash(salt, clearText));
    }
    
    @Override
    public boolean equals(Object o) {
        ...
        return Arrays.equals(this.hash, o.hash);
    }
}
```

**Make it an object with behavior!**

---

## Alternative Design with SRP

### The "core" is an interface

```java
public interface User {
    boolean authenticate(String password);
    void register();
}
```

**All functionalities are implemented on this interface.**

---

## Alternative Design with SRP

### Only SQL code, cleanly separated

```java
public final class SqlUser implements User {
    private final String username;
    private final String passwordHash;
    ...
    
    @Override
    public boolean authenticate(String password) {
        return new PasswordHash(
            sql.select("passwordhash from user...", ...)
        ).matches(password);
    }
    
    @Override
    public void register() {
        sql.insert("into user...", ...);
    }
}
```

**The object knows how to persist itself!**

---

## Alternative Design with SRP

### Delegation pattern

> "Unfortunately delegation is not part of the language (like inheritance). Yet."

```java
public class DelegatingUser implements User {
    private final User delegate;
    
    public DelegatingUser(User delegate) {
        this.delegate = delegate;
    }
    
    @Override
    public boolean authenticate(String password) {
        return delegate.authenticate(password);
    }
    
    @Override
    public void register() {
        delegate.register();
    }
}
```

**Base class for decorators!**

---

## Alternative Design with SRP

### Decorator for email notification

```java
public final class NotifiedUser extends DelegatingUser {
    private final String username;
    private final String emailAddress;
    private final SmtpClient smtpClient;
    
    public NotifiedUser(String username, String emailAddress,
                       User delegate) {
        super(delegate);
        this.username = username;
        this.emailAddress = emailAddress;
        ...
    }
    
    @Override
    public void register() {
        super.register();  // Delegate to wrapped user
        
        smtpClient.send(emailAddress,
            "Hello " + username + ", welcome to Application");
    }
}
```

**Email functionality is composed on top!**

---

## Alternative Design with SRP: Result

### Clean composition

```java
public User createUser(String username, String password, 
                      String emailAddress) {
    return new NotifiedUser(
        username, 
        emailAddress,
        new SqlUser(username, password, emailAddress)
    );
}
```

**Beautiful!**
- ✅ Composition over inheritance
- ✅ Each class has one responsibility
- ✅ Easy to test
- ✅ Easy to extend

---

## Alternative Design with SRP: Analysis

### Benefits

- Instead of separating per **technology**, it separates based on **vertical features**
  - Slight but crucial difference!
- Objects are **cohesive** and truly **decoupled**
  - I.e. **SRP**
- Each feature is **testable**
- It is **OO** - No data is pulled out of objects

---

## Preliminary Summary

> "I don't think it means what I was taught it means. (If it is, it shouldn't...)"

### The Real Definition

```
Single Responsibility Principle ≡ Cohesion & Coupling
```

---

## What You Should Do For Your Code

### 4 Practical Rules

1. **Make sure your class is physically cohesive**
   - Methods and fields refer to each other
   - Don't forget the Constructor!

2. **Make sure the physical coupling is not stronger than the physical cohesion**
   - Dependencies inside > Dependencies outside

3. **Make sure you have your data, and don't need to get your data!**
   - This avoids semantic coupling
   - **Avoid getters!**

4. **Don't be dogmatic**
   - UI (HTML, JSON/HTTP, SOAP) is functionality too!

---

## Some (Unexpected?) Consequences

### MVC

Let's rethink Model-View-Controller...

---

## "Traditional" MVC

### The problematic pattern

```java
// Model - Just data
public class Person {
    private String name;
    private int age;
    private String address;
    
    // Setters, getters...
}

// Controller
public class PersonController {
    public void greet(Person person) { ... }
    public void add(Person person) { ... }
}
```

**View:** JSF or plain HTML with substitutions

---

## "Traditional" MVC Problems

**Despite claims that this is for decoupling, it actually increases coupling:**

- Everything is strongly coupled to the **data object**
  - Both controller and view
- **The whole thing must change** if Person changes
- Only allows **limited "view" changes** to be localized
- Makes code **unmaintainable**

---

## Alternative MVC

### A better interpretation

- **Model** = The "business" object itself
- **Controller** = Abstract UI Component
- **View** = HTML the Component reads

```java
public final class Person { // Model
    private final String name;
    private final int age;
    private final String address;
    ...
    
    public Component displaySummary() {
        return new InfoPanel()
            .addInfo("Name", name)
            .addInfo("Age", age)
            .addInfo("Address", address);
    }
}
```

**The object displays itself!**

---

## Alternative MVC with Input

### This is what we should mean by cohesion!

```java
public final class Person {
    private final String name;
    private final int age;
    private final String address;
    
    public Person(String name, int age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
    
    public InputComponent<Person> displayInput() {
        return new InputGroup()
            .add(new TextInput("Name", name))
            .add(new NumberInput("Age", age, ...))
            .add(new TextInput("Address", address))
            .map(Person::new);
    }
}
```

**The object knows how to edit itself!**

---

## Alternative MVC

### Benefits

**Responsibilities cleanly separated:**
- Person doesn't know **details** of View
- View doesn't know **details** of Person
- **Composable!**

---

## Alternative MVC: Composability

### Nested objects compose naturally

```java
public final class Person {
    private final String name;
    private final int age;
    private final Address address;  // ← Nested object
    
    public Person(String name, int age, Address address) {
        ...
    }
    
    public InputComponent<Person> displayInput() {
        return new InputGroup()
            .add(new TextInput("Name", name))
            .add(new NumberInput("Age", age, ...))
            .add(address.displayInput())  // ← Composition!
            .map(Person::new);
    }
}
```

**Each object displays itself, composition works automatically!**

---

## Some (Unexpected?) Consequences

### Layered Architectures

Let's rethink the traditional layers...

---

## Layered Architecture

Traditional approach:
- **Presentation Layer**
- **Application Layer**
- **Domain Layer**
- **Infrastructure Layer**

---

## Problems with Layers

1. The "Domain" is only **1/4 of the Application**
2. Is a **technical design**, business-agnostic
3. Layers usually **leak data upwards** and create coupling (DTOs)
4. Layers therefore **tightly coupled** to layers below
5. **⇒ Changes escalate and expand upwards!**

---

## Layered Architecture Question

### The Critical Question

**How many classes would you have to change if a domain object changed slightly?**

**Is it more than 1?**

If yes:
- ❌ Almost automatically **violates SRP**
- ❌ Changes are **not localized**

---

## Domain-Driven Design + SRP

### The Combination

---

## Building Blocks of DDD

Traditional DDD Building Blocks:
- Value Objects
- Entities
- Services
- Repositories
- Aggregates
- Factories

**But how do these relate to SRP?**

---

## DDD + SRP

### The Key Insight

**DDD makes the case for the ubiquitous language:**
- The same language in **code** as between **people**

**⇒ Responsibilities cannot be arbitrary**
- They have to come from the **business** as well

**Therefore:**
- Persistence
- Json/XML Formatting
- Validation
- Rules
- Commands
- etc.

**...are usually NOT valid responsibilities!**

They are **technical concerns**, not **business responsibilities**.

---

## Summary

### What We Learned

1. **We got a useful definition of SRP**, based on Cohesion and Coupling

2. **DDD implies responsibilities are not arbitrary**
   - Have to come from the requirements

3. **Using SRP to increase Maintainability** leads to a different design than most are familiar with

4. **Among others:** UI, MVC and Layered Architectures have superior alternative interpretations for most cases

---

## Key Takeaways

### The Essentials

1. **SRP = Maximize Cohesion + Minimize Coupling**
   - Not "one reason to change"
   - Not "one responsibility" (too vague)

2. **Semantic coupling is worse than physical coupling**
   - Invisible to compiler
   - Facilitated by getters
   - Avoid it!

3. **UI can be part of the domain**
   - Objects can display themselves
   - Increases cohesion

4. **Composition > Fragmentation**
   - Don't split everything into micro-classes
   - Use decorators for features

5. **Technical concerns ≠ Business responsibilities**
   - Persistence, JSON, etc. are implementation details
   - Not separate "responsibilities"

---

## Practical Guidelines

### How to Apply SRP

✅ **DO:**
- Keep related things together (cohesion)
- Make methods and fields refer to each other
- Let objects do things, not expose data
- Use composition and decorators
- Let objects display themselves
- Let objects persist themselves

❌ **DON'T:**
- Create getters/setters for everything
- Split by technical layers
- Create Manager/Service/Helper classes
- Pull data out of objects
- Separate UI from domain arbitrarily
- Fragment into one-method classes

---

## Thanks!

**Questions?**

📧 robert@mathema.de  
🌐 https://javadevguy.wordpress.com/  
🐦 @robertbrautigam

---

## Additional Resources

**Recommended Reading:**
- Robert C. Martin - "Agile Software Development, Principles, Patterns, and Practices"
- Eric Evans - "Domain-Driven Design"
- Yegor Bugayenko - "Elegant Objects"
- Allen Holub - "Why getter and setter methods are evil"

**Online:**
- Robert Bräutigam's Blog: https://javadevguy.wordpress.com/
- Object-Oriented Design Principles
- Domain-Driven Design Community

---

*This document is a markdown conversion of Robert Bräutigam's presentation on the Single Responsibility Principle.*
