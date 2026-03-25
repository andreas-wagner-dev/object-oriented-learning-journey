# Object-Oriented Domain-Driven Design

**Autor:** Robert Bräutigam  
**Organisation:** MATHEMA Software GmbH  
**Kontakt:** robert@mathema.de  
**Website:** https://javadevguy.wordpress.com/  
**Twitter:** @robertbrautigam

---

## Goal

**TO INTRODUCE A NEW DESIGN PARADIGM WHICH INCREASES MAINTAINABILITY OF SOFTWARE.**

---

## OO and DDD?

**ISN'T THAT REDUNDANT?**

---

## What We Learn...

```java
public interface Animal {
    void speak();
}

public final class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Meow...");
    }
}

public final class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Woof...");
    }
}
```

---

## "Enterprise" Code...

```java
public interface Animal {
    String getSpeech();
}

public final class Cat implements Animal {
    private final String speech;
    ...
    
    @Override
    public String getSpeech() {
        return speech;
    }
}

public final class SpeechService {
    public void speak(Animal animal) {
        System.out.println(animal.getSpeech());
    }
}
```

---

## Wait a Second...

**What's wrong here?**

---

## What is Encapsulation Then?

- It means having **public and private parts**.
- It means having **secrets**!
- Having secrets means to have an **effective abstraction**
- Effective abstraction means to solve a problem we **don't have to think about ever again**.

---

## Where is My Encapsulation?

```java
public interface Animal {
    String getSpeech();
}

public final class Cat implements Animal {
    private final String speech;
    ...
    
    @Override
    public String getSpeech() {
        return speech;
    }
}

public final class SpeechService {
    public void speak(Animal animal) {
        System.out.println(animal.getSpeech());
    }
}
```

**Problem:** The object `Cat` has no responsibility! It's just a data container. The `SpeechService` does everything.

---

## These Things Too?

- **Cohesion**
- **Coupling**
- **Tell, don't ask**
- **Law of Demeter**
- etc...

### GO HOME AND RETHINK YOUR LIFE

---

## OK, So Now What?

- Objects must have **hidden state**
- Objects should **do stuff**, instead of giving out data
- Objects' instance variables should **not be given out**

### ⇒ SOUNDS LIKE WE SHOULD AVOID GETTERS

---

## How Do We Do DDD Without Getters?

Let's examine the traditional DDD Building Blocks...

---

## Building Blocks of DDD

Traditional DDD defines several building blocks:
- Value Objects
- Entity Objects
- Services
- Repositories
- Aggregate Roots

**Let's see how they're commonly (mis)implemented...**

---

## Value Objects

**Definition:** Things that do not have an identity. Objects representing the same value are interchangeable.

---

## Commonly Implemented As

```java
public final class Amount {
    private final BigDecimal value;
    private final Currency currency;
    
    public Amount(BigDecimal value, Currency currency) {
        this.value = value;
        this.currency = currency;
    }
    
    public BigDecimal getValue() {
        return value;
    }
    
    public Currency getCurrency() {
        return currency;
    }
    
    // equals(), hashCode(), toString()...
}
```

---

## That's Not Cool!

- There is **nothing hidden**.
- Therefore there is **no problem solved here**.
- This thing has **no reason to exist**!

### HOW CAN WE FIX THIS?

The business people talk a lot about "Amounts", so let's assume it's something we need to have.

**What business problem could the "Amount" solve?**

---

## OO Value Object

```java
public final class Amount {
    private final BigDecimal value;
    private final Currency currency;
    
    public Amount(BigDecimal value, Currency currency) {
        this.value = value;
        this.currency = currency;
    }
    
    public Amount add(Amount other) { 
        // Implementation that handles currency conversion
        ...
    }
    
    public boolean largerThan(Amount other) { 
        // Implementation that compares amounts
        ...
    }
    
    // More behavior, not getters!
}
```

**Now the object actually solves problems!**

---

## Entity Objects

**Definition:** Things that have an identity. Objects are not interchangeable. Objects may represent the same conceptual thing even if some attributes differ.

---

## Commonly Seen As

### OH NO, NOT AGAIN!

```java
public final class Customer {
    private final CustomerId customerId;
    private Name name;
    ...
    
    public Customer(CustomerId customerId, Name name, ...) {
        this.customerId = customerId;
        this.name = name;
        ...
    }
    
    // ...getters, some setters...
}
```

**Another data container!**

---

## How It Should Look

```java
public final class Customer {
    // ...data doesn't matter...
    
    public void renameTo(Name newName) { ... }
    
    public void freezeCreditCards() { ... }
    
    public void unfreezeCreditCards() { ... }
    
    public CreditStatement createCreditStatement() { ... }
    
    // More behavior!
}
```

**The object DOES things, not just holds data!**

---

## Services

**Eric Evans:**  
> "Sometimes, it just isn't a thing."

**Object-Orientation:**  
> Everything is an object.

**David West:**  
> "...any decomposition, however complicated the domain, will result in the identification of a relatively few kinds of objects and only objects. There will be nothing 'left over' that is not an object."

---

## "Fixing" Services

**Eric Evans:**  
> "There are important domain operations that can't find a natural home in an ENTITY or VALUE OBJECT."

**Aha!** It's not OO's fault, the building blocks are incomplete!

---

## PasswordService (Vaughn Vernon)

### WHY?

```java
public class PasswordService {
    // ...no hidden state...
    
    public String generateStrongPassword();
    
    public boolean isStrong(String password);
    
    public boolean isWeak(String password);
    
    // ...
}
```

**This is just a collection of functions!**

---

## Password

**It's actually a Value Object!**

```java
public final class Password {
    private final String password;
    
    public Password(String password) {
        this.password = password;
    }
    
    public boolean isStrong() { 
        // Check password strength
        ...
    }
    
    public boolean isWeak() { 
        // Check if password is weak
        ...
    }
    
    public static Password generateStrongPassword() { 
        // Generate a strong password
        ...
    }
}
```

**Now it's a real object with behavior!**

---

## GroupMemberService.isMemberGroup()

```java
public boolean isMemberGroup(Group aGroup, GroupMember aMemberGroup) {
    boolean isMember = false;
    Iterator<GroupMember> iter = aGroup.groupMembers().iterator();
    
    while (!isMember && iter.hasNext()) {
        GroupMember member = iter.next();
        
        if (member.isGroup()) {
            if (aMemberGroup.equals(member)) {
                isMember = true;
            } else {
                Group group = this.groupRepository()
                    .groupNamed(member.tenantId(), ...);
                
                if (group != null) {
                    isMember = this.isMemberGroup(group, aMemberGroup);
                }
            }
        }
    }
    
    return isMember;
}
```

**Complex logic outside the object!**

---

## Why not Group.contains()?

```java
public final class Group implements GroupMember {
    private final Set<GroupMember> members;
    ...
    
    @Override
    public boolean contains(GroupMember potentialMember) {
        if (equals(potentialMember)) {
            return true;
        }
        
        return members.stream()
            .filter(member -> member.contains(potentialMember))
            .findFirst()
            .isPresent();
    }
}

public final class User implements GroupMember {
    ...
    
    @Override
    public boolean contains(GroupMember potentialMember) {
        return equals(potentialMember);
    }
}
```

**Much cleaner! The object knows how to check membership!**

---

## Repositories

**Definition:** A means to get an initial reference to an object.

**Eric Evans:**  
> "Provide methods to add and remove objects... [and] methods that select objects..."

**In other words:** A CRUD service.

---

## Problems with Repositories

- Reinforces **data-based thinking**
- Not part of the Domain! **Repositories are technical.**
- Often implemented by **violating encapsulation**...

---

## Repository vs. Encapsulation

### NOT OK!

```java
// Simplified from Vaughn Vernon's example
public class LevelDBTeamRepository {
    ...
    
    public void save(Team team) {
        String id = team.getTeamId().getId(); // LoD violation
        String name = team.getName();         // Privacy violation
        
        // ...persist team to id + name...
    }
}
```

**The repository pulls data out of the object!**

---

## Persistence Option #1

**SQL-Speaking Objects**

```java
public final class SqlCustomer implements Customer {
    private final Connection connection;
    private final String customerId;
    
    public SqlCustomer(String customerId, Connection connection) {
        this.customerId = customerId;
        this.connection = connection;
    }
    
    @Override
    public void freezeCreditCards() {
        connection.execute(
            "update card set valid = 0 where customerId = ?", 
            customerId
        );
    }
}
```

**The object knows how to persist itself!**

---

## Persistence Option #2

**Serialization Methods**

```java
public final class Customer {
    // ...private parts...
    
    public Json toJson() {
        // Serialize to JSON
        ...
    }
    
    public static Customer fromJson(Json json) {
        // Deserialize from JSON
        ...
    }
}
```

**The object controls its own serialization!**

---

## Aggregate Root

**Definition:** Entities that exclusively control a set of internal entities and value objects. Outside objects are not allowed to hold references to internals, and the aggregate root entity controls access, preserves invariants.

**Well, all objects must do this anyway...**

---

## Layered Architecture

Traditional layered architecture separates:
- **Presentation Layer**
- **Application Layer**
- **Domain Layer**
- **Infrastructure Layer**

---

## Problems with Layers

- The "Domain" is only **1/4 of the Application**
- Layers usually **leak data upwards** and create coupling (DTOs)
- UI usually **tightly coupled to Domain**
- UI (external interfaces) is usually **second rate citizen**

---

## UI of Objects

```java
import org.apache.wicket.Component;

public final class AccountNumber {
    private final String accountNumber;
    ...
    
    public Component display(String componentId) {
        return new Label(componentId, accountNumber);
    }
}
```

**The object knows how to display itself!**

---

## UI of Objects (Extended)

```java
import org.apache.wicket.Component;

public final class AccountNumber {
    private final String accountNumber;
    ...
    
    public Component display(String componentId) {
        return new Label(componentId, accountNumber);
    }
    
    public FormComponent<AccountNumber> displayEditable(String componentId) {
        return new TextField<>(componentId, ...);
    }
}
```

**The object provides both read-only and editable views!**

---

## Summary

Traditional DDD Building Blocks often lead to:
- ❌ Anemic Domain Models
- ❌ Data containers with getters/setters
- ❌ Services that do all the work
- ❌ Repositories that violate encapsulation

---

## Building Blocks of OO DDD

True Object-Oriented DDD:
- ✅ Objects with **behavior**, not just data
- ✅ **No getters** for internal state
- ✅ Objects **do things**
- ✅ Objects know how to **persist themselves**
- ✅ Objects know how to **display themselves**
- ✅ **Tell, don't ask**

---

## OO DDD

Ok, maybe we should not concentrate on the original building blocks!

But, things that DDD adds to OO:
- **Learn and think about the domain** (As opposed to technical stuff including building blocks)
- **Exercise and speak the design**
- **Ubiquitous language**
- **Bounded Context**

---

## Key Takeaways

1. **Encapsulation matters** - Hide your data!
2. **Behavior over data** - Objects should DO things
3. **Avoid getters** - Don't expose internal state
4. **Tell, don't ask** - Give commands, don't query state
5. **UI is part of the domain** - Objects can display themselves
6. **Self-persisting objects** - Objects know how to save themselves
7. **DDD + OO = Better software** - Combine domain focus with true OOP

---

## Thanks!

**Questions?**

📧 robert@mathema.de  
🌐 https://javadevguy.wordpress.com/  
🐦 @robertbrautigam

---

## Additional Resources

For more on Object-Oriented Programming and Domain-Driven Design:

- **Yegor Bugayenko** - Elegant Objects
- **Allen Holub** - "Why getter and setter methods are evil"
- **Eric Evans** - Domain-Driven Design (the book)
- **Vaughn Vernon** - Implementing Domain-Driven Design
- **Robert Bräutigam's Blog** - https://javadevguy.wordpress.com/

---

*This document is a markdown conversion of Robert Bräutigam's presentation on Object-Oriented Domain-Driven Design.*
