# The Pragmatic Single Responsibility Principle: Beyond Myths to Metrics

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_blog_post_min.png)

> The pragmatic Single Responsibility Principle: From subjective debates to objective metrics

The [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) (SRP) is the first of the five [SOLID principles](https://en.wikipedia.org/wiki/SOLID), established by Robert C. Martin in the early 2000s. Despite its conceptual simplicity, it is in practice one of the most difficult design principles to operationalize in object-oriented programming.

The purpose of the principle is to keep software modular, maintainable, and understandable. A class that has a clear area of responsibility is easier to test, simpler to change, and clearer in its intent. SRP is therefore not just a design principle - it is a quality characteristic that significantly influences long-term maintainability and minimizes the accumulation of technical debt.

## 1. Subject and Objectives

The focus of this discussion is the **Single Responsibility Principle** and the challenge of **objectively assessing its compliance at the class level**. Through an objective examination of SRP, a deeper understanding of its application in everyday development is intended to be conveyed. The approach taken here is based on the formalization by Robert Bräutigam, who defines the [Single Responsibility Principle](https://speakerdeck.com/robertbraeutigam/single-responsibility-principle) via the conjunction (logical AND) of two measurable [software metrics](https://en.wikipedia.org/wiki/Software_metric). The resulting formula is:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

The operationalization of the formula is carried out using two central key figures. Cohesion is determined using *[Lack of Cohesion of Methods Version 4](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4)* (LCOM4) via graph analysis, with an ideal target value of 1. Coupling is additionally measured using *[Coupling Between Objects](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo)* (CBO) by counting external dependencies, with the goal of a minimal value. Both metrics are first explicitly derived from example classes and then evaluated in a final comparison. Two widespread design approaches serve as comparison objects: the widely-used service pattern of the data-centric approach and the decorator pattern of the object-oriented approach. These concepts are implemented for analysis within an order management domain in Java.

## 2. The Dilemma with Single Responsibility Principle

The **problem** with SRP lies in its **subjective formulations**, which can be interpreted ambiguously in practice. As a result, they offer no concrete basis for evaluation or action in class design. A common definition states that a class should have only one single reason to change ("only one reason to change"). However, what specifically counts as a reason for change usually only becomes apparent at the moment of the actual modification. A forward-looking separation is therefore often based on pure speculation, since theoretically any minimal change in requirements could be interpreted as a new, independent reason.

Due to the ambiguities surrounding the term *"reason for change"*, Robert C. Martin later clarified his statements, which over time led to various levels of definition. These range from the requirement of exactly one task per module, to the identification of a specific reason for change, to the recommendation of grouping elements with identical causes of change together. In concrete interpretation, such a reason is ultimately defined as a requirement originating from a single actor or business person.

> "Each software module should have one and only one responsibility"
> - Robert C. Martin (2003)

> "Each software module should have one and only one reason to change"
> - Robert C. Martin (2007)

> "Gather together the things that change for the same reasons. Separate those things that change for different reasons"
> - Robert C. Martin (2014)

> "'Same reason' means it originates from the same business person"
> - Robert C. Martin (2014)

In practical application, however, these definitions raise new questions:

* For example, it is unclear whether a class `Order` that both validates and persists an order already violates the constraint of ***a single task***.
* The practical rule of thumb to ***group related things based on future reasons for change*** is also hardly verifiable at the time of implementation, as it requires advance planning of future product development.
* Likewise, the reference to a ***business person*** may be helpful for strategic domain modeling, but remains useless as a concrete programming criterion in everyday coding.

These context-dependent interpretations frequently lead to time-consuming fundamental debates during code reviews. Without objective evaluation criteria, such discussions can deteriorate into subjective disagreements, which prevents the efficiency and consistency of software development.

## 3. The Formalization of Cohesion and Coupling

### 3.1 Formal Definition of Single Responsibility Principle

The rather philosophical and sociologically influenced definitions of SRP offer little clear basis for action in software development due to their subjectivity. Terms like responsibility, reason for change, or actor orientation prove too vague for practical implementation and can consequently often lead to unnecessary code fragmentation. Robert Bräutigam instead proposes a pragmatic definition:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

By formally equating SRP with the conjunction of maximum [cohesion](https://en.wikipedia.org/wiki/Cohesion_(computer_science)) and minimum [coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)), the principle transforms from a purely abstract design philosophy into a precise structural metric. This mathematical definition makes it possible to objectively evaluate the formerly vague responsibilities of a class through measurable structural properties.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_coupling_cohesion.png)

The internal quality of a class is measured by its **cohesion** - the **degree of connection** in which methods and fields form a functional unit and thereby fulfill the promise of doing only one thing.

External quality, on the other hand, is determined by **coupling**, which describes the **dependencies on external objects**. The lower this external interconnectedness, the more isolated and maintenance-friendly the class remains in the face of global changes in the system.

Within coupling, a distinction is made between **physical** and **semantic** dependencies. While physical connections can be statically identified through field types, semantic coupling hides behind an implicit knowledge of external object structures. Since these dependencies are not accessible to the compiler, they often lead to difficult-to-trace error propagation during code changes. As soon as a class accesses deeper-level data via chains like `user.getAddress().getCity()`, a structural dependency arises that goes beyond mere type knowledge. Consequently, every getter method serves as a potential channel for increased semantic coupling.

### 3.2 Measures for Optimizing Cohesion and Coupling

In principle, coupling between classes is unavoidable for the functionality of a system. An excessive degree of dependencies, however, considerably hampers the modification, testing, and reusability of classes. To systematically optimize the external and internal coupling of classes, the application of fundamental OOP design guidelines, additional SOLID principles, and proven design patterns is essential.

The [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) and the [Tell, Don't Ask](https://martinfowler.com/bliki/TellDontAsk.html) principle serve as central measures to avoid unnecessary dependencies. While the **Law of Demeter** strictly restricts the access path to immediate neighbors, **Tell, Don't Ask** calls for giving commands to objects rather than querying their internal state. The consistent application of both principles preserves encapsulation and protects against semantic instability.

Since both principles isolate responsibilities and keep logic directly with the data, cohesion increases immediately. A class thus manages its information autonomously and offers only domain-relevant operations. It focuses on a coherent range of tasks, which is an essential prerequisite for compliance with the Single Responsibility Principle.

The principle [Encapsulate what varies](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)) acts supportively here and isolates change-prone logic behind interfaces. The [Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle) reinforces this effect by decoupling high-level modules from concrete implementations through stable abstractions. Additionally, the [Interface Segregation Principle](https://en.wikipedia.org/wiki/Interface_segregation_principle) ensures that classes are only bound to the specific partial interfaces they actually need. Correct segregation is largely guaranteed by the [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). Accordingly, subclasses must be able to fully replace their base classes so that the program behavior is never falsified.

Furthermore, various design patterns (such as Adapter, Bridge, Decorator, Strategy, or Visitor…) help implement the [Open-Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle). Accordingly, functional extensions should ideally be realized without the creation of new, direct couplings within the existing class. This decoupling also significantly supports cohesion by preventing a class from being expanded by ever-new responsibilities (variants or special cases). Instead of complicating the internal logic, new responsibilities can be outsourced to specialized, independent classes. The original code thus remains focused on its core task, while extensions are added as highly cohesive, interchangeable building blocks.

## 4. Measurement Methods for Cohesion (LCOM4) and Coupling (CBO)

### 4.1 Lack of Cohesion of Methods

One of the best-known metrics for measuring the internal class structure is the Lack of Cohesion of Methods (LCOM). Since various versions of this cohesion metric exist in the literature, this article specifically uses the fourth iteration (LCOM4) according to [Hitz and Montazeri (1995)](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4). This is characterized by high practical relevance, as its simple application and clear interpretability enable an objective assessment of SRP compliance.

The **LCOM4 metric** determines the **number** of connected element groups within a class, where cohesion is visualized by means of a graph analysis. A connected subgraph arises whenever **methods** either access the same **instance variables** or directly influence each other through mutual calls.

The following key figures provide concrete statements for evaluating the class structure:

* An **LCOM4 value of 1** means that all methods and fields are directly or indirectly connected to each other, which corresponds to a highly cohesive class in ideal condition.
* An **LCOM4 value above 1** means that the class breaks down into several independent subgraphs, which is treated as an objective indication of a violation of the Single Responsibility Principle.
* An **LCOM4 value of 0**, on the other hand, identifies a class without methods, such as a pure data container, which is evaluated neutrally within this analysis.

The derivation of the LCOM4 metric is illustrated by the following case studies using graph analysis.

**Case Study 1: Low Cohesion (LCOM4 = 2)**

In this scenario, the class `Order` combines two logical responsibilities: displaying `display()` an order and processing a payment `pay(int amount)`.

```java
// The class breaks down into two isolated subgraphs (LCOM4 = 2)
public class Order {

    private Cart cart;                   // Field 1
    private Customer customer;           // Field 2
    private String status = "PENDING";   // Field 3
    private int amount;                  // Field 4

    // M1: Display logic (uses Field 1 and Field 2)
    public String display() {
        return customer.getName() + ": " + cart.itemCount() + " items";
    }

    // M2: Payment logic (uses Field 3 and Field 4)
    public void pay(int amount) {
        this.amount = amount;
        this.status = "PAID";
    }
}
```

The graph analysis shows that no connection exists between these two groups, since neither a shared field is used nor a mutual method call is made.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case1.png)

The class has two independent responsibilities, which is reflected in two isolated subgraphs and an LCOM4 value of 2.

**Case Study 2: Ideal Cohesion through Splitting (LCOM4 = 1)**

To achieve genuine domain cohesion, we decompose the class along its responsibilities into two specialized units.

```java
// Focus on content/display
public class OrderView {

    private Cart cart;                 // Field 1
    private Customer customer;         // Field 2

    public String display() {
        // Uses Fields 1 and 2
        return customer.getName() + ": " + cart.itemCount();
    }
}

// Focus on payment/transaction
public class OrderPayment {

    private String status = "OPEN";   // Field 3
    private int amount;               // Field 4

    public void pay(int amount) {
        // Uses Fields 3 and 4
        this.amount = amount;
        this.status = "PAID";
    }
}
```

The split creates two independent graphs, each forming a self-contained functional unit.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case2.png)

Within the class `OrderView`, the method `display()` creates a direct connection between the customer object and the shopping cart. In the class `OrderPayment`, the method `pay()` modifies both instance variables simultaneously, creating a strong internal binding. In both cases, the analysis yields a single, fully connected subgraph with an LCOM4 value of 1, confirming the domain isolation.

**Case Study 3: Cohesion through Field Connection (LCOM4 = 1)**

In this scenario, the field `status` (Field 3) acts as a hub, as it is now used by both methods.
Purely structurally, cohesion increases, causing the LCOM4 value to drop to 1.

```java
public class Order {

    private Cart cart;                   // Field 1
    private Customer customer;           // Field 2
    private String status = "OPEN";      // Field 3
    private int amount;                  // Field 4

    public String display() {
        // Uses Fields 1, 2, and 3
        return customer.getName() + ": " + cart.itemCount() + ", Status: " + status;
    }

    public void pay(int amount) {
        // Uses Fields 3 and 4
        this.amount = amount;
        this.status = "PAID";
    }
}
```

The graph analysis of the class `Order` reveals the following picture.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case3.png)

Since both methods access the `status` field, the originally isolated logic areas are connected to each other via this instance variable. In terms of graph theory, a single connected graph is created, as a path from M1 via Field 3 to M2 exists. The result is an LCOM4 value of 1.

**Case Study 4: Cohesion through Method Dependencies (LCOM4 = 1)**

In this scenario, the class `Order` is functionally linked by the central method `isPaid()` (M3). This encapsulates the logic of the payment status and ensures autonomous management of the object state. Changes to the definition of "paid" thus only need to be made in this one central place, which optimizes maintainability.

```java
public class Order {

    private Cart cart;           // Field 1
    private Customer customer;   // Field 2
    private int amount = 0;      // Field 3

    // M1: Display logic
    public String display() {
        // Uses Fields 1, 2, and M3 (indirectly Field 3)
        String prefix = isPaid() ? "[PAID] " : "[OPEN] ";
        return prefix + customer.getName() + ": " + cart.itemCount();
    }

    // M2: Payment logic
    public void pay(int amount) {
        // Uses Field 3 and M3
        if (isPaid()) throw new IllegalStateException("Already paid.");
        this.amount = amount;
    }

    // M3: Central hub
    private boolean isPaid() {
        // Accesses Field 3
        return amount > 0;
    }
}
```

Through this structure, the graph shows that **M3** acts as the central connecting node.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case4.png)

Through the call dependencies from **M1** and **M2** to **M3**, a fully connected graph is created. The resulting **LCOM4 value of 1** is here the result of a functional dependency (encapsulation of the lifecycle) and not a purely technical auxiliary bridge.

**Domain and Technical Analysis of Cohesion**

As shown in **Case Study 2**, an ideal metric value can be artificially created by technical cross-cutting concerns (such as an `id`, a `status` field, or UI-specific data) without actually solving the underlying mixing of responsibilities. From a data-centric design perspective, the strict separation of payment and presentation logic, as shown in **Case Study 3**, may seem sensible. However, this contradicts an object-oriented view because it breaks encapsulation in the sense of OOP.

From the object-oriented perspective, embedding a `display()` method in the `Order` object is not an artificial connection, but an expression of genuine encapsulation. Since the representation of an order is inseparably linked to its domain state (e.g., the `status`), this knowledge belongs to the core of the entity itself. Splitting this unit would force the use of getter methods, which violates the **Tell, Don't Ask** principle and thus causes semantic coupling.

**Case Study 4** brings these opposing positions together. By introducing the central helper method `isPaid()`, a genuine functional dependency is created. This method logically evaluates the amount field and is used by both main methods. Thus, cohesion is no longer maintained only by a single field (technical bridge), but is anchored in the graph by a shared business rule (domain encapsulation).

These case studies highlight a central insight for practice: An **LCOM4 value of 1 is a necessary but not sufficient condition** for SRP compliance. The LCOM4 metric only confirms the structural connectedness, **but does not replace the qualitative check** of whether the linked elements actually form a domain unit.

The following recommendation can be derived from this: If there is no domain connection between the methods of a class, a split is required. An artificial connection through purely technical infrastructure concerns should be avoided so as not to distort the transparency of actual cohesion. A robust design following **Case Study 4** shows, for example, how internal helper methods strengthen domain cohesion while simultaneously improving maintainability through centralization of logic.

### 4.2 Coupling Between Objects

Complementing cohesion, the **Coupling Between Objects** (CBO) metric by Chidamber & Kemerer (1994) measures the number of external types to which a class maintains a direct dependency. The CBO value is determined by counting all coupled classes within the following structures:
* **Class extensions** (inheritance and implementation),
* **Field types** (instance variables of the class),
* **Method calls** (references to external logic),
* **Method signatures** (types of arguments and return values),
* **Local variables** (instantiations or references within methods).

*Primitive* data types and *standard wrappers* such as `int` or `String` are not considered here, as they belong to the base elements of a programming language.

The CBO metric serves as an indicator of maintainability and testability, as a high CBO value means that the class must be adapted disproportionately often when its partner classes change. The evaluation of measurement results follows a clear scale:

* A **CBO value of 0** indicates that a class is isolated and therefore does not effectively participate in the system.
* **CBO values between 1 and 5** are considered the ideal range, as this signals loose coupling and supports maintainability.
* A **CBO value greater than 5** indicates excessive entanglement with other classes.

The threshold value of **CBO > 5** used in this article is not a universal standard. Depending on the source, the recommended limit varies considerably:
While [Sahraoui, Godin & Miceli](https://www.iro.umontreal.ca/~sahraouh/papers/ICSM00.pdf) set a maximum value of **14** in their article, [ObjectScript Quality](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo) recommends a significantly lower threshold of **4**.

The following case studies illustrate the determination of CBO values and the nuances of coupling types.

**Case Study 1: Direct Dependency**

In the first scenario, `Report` is directly dependent on concrete implementations. Every change to the participating classes can have an immediate impact on the class.

```java
// CBO = 5 - High coupling to concrete types
public class Report {

    private ReportRepository repository; // Class (CBO +1)
    private PdfExporter pdfExporter;     // Class (CBO +1)

    public ReportData generate(DataQuery q) { // DataQuery (CBO +1), ReportData (CBO +1)
        List<DataRow> rows = repository.fetch(q); // DataRow (CBO +1) as return type of fetch
        return pdfExporter.export(rows);
    }
}
// Counted types: ReportRepository, PdfExporter, DataQuery, ReportData, DataRow
```
The `Report` must "know" the type `DataRow` here in order to process or return the result of `repository.fetch(q)`. In the CBO metric, types in method signatures (parameters and return types) always count as coupling.

**Case Study 2: Dependency Inversion**

To reduce the CBO value, the *Dependency Inversion Principle* is applied. Instead of referencing concrete implementations, `Report` binds to two stable interfaces `Repository` and `Exporter`.

```java
// Coupling to stable interfaces (CBO = 4)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public ReportData generate(Query q) { // Query (CBO +1), ReportData (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        return exporter.export(rows);
    }
}
// Counted types: Repository, Exporter, ReportData, Query
```

**Case Study 3: Signature Optimization**

If a specific return type (`void` instead of `ReportData`) can be dispensed with, coupling can be further reduced. A decisive factor here is the distinction between signature coupling and local coupling.

```java
// Coupling to interfaces without return type (CBO = 3)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generate(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        exporter.export(rows);
    }
}
// Counted types: Repository, Exporter, Query
```

The type `DataRow` now only appears as a local "pass-through value". Since it is neither part of the fields nor of the method signature, it is not counted as a direct coupling in the metric. The class `Report` merely passes the `DataRow` object between `Repository` and `Exporter` without having a functional dependency on the internal structure of `DataRow` (pass-through effect).

**Case Study 4: Semantic Coupling**

The CBO value increases again to 4 as soon as an explicit dependency on `DataRow` arises. This is the case when `Report` actively calls methods of the type (e.g., a validation via `rows.get(0).validate()`).

```java
// Coupling to semantics (CBO = 4)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generate(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        rows.get(0).validate(); // Active call to DataRow (CBO +1)
        exporter.export(rows);
    }
}
// Counted types: Repository, Exporter, Query, DataRow
```

As soon as the class calls methods like `validate()`, a semantic coupling arises. The class `Report` now needs "knowledge" about the internal behavior and business rules of `DataRow` (violation of the Law of Demeter). The class thereby leaves its role as a pure coordinator and speaks with a "stranger" that it should actually only pass through. As a result, the class is no longer just technically coupled (knowledge of the type) but also logically (knowledge of the process), which makes maintenance more difficult.

### 4.3 The Synergy of LCOM4 and CBO

As the preceding case studies show, an LCOM4 value of 1 can be misleading. As soon as a class uses technically necessary cross-cutting fields such as an id, a status field, or a logger, bridges are created in the graph between domain responsibilities that are otherwise foreign to each other. The structural analysis rates this as cohesion, even though the Single Responsibility Principle remains factually violated.

This is where the combination with the CBO metric unfolds its full diagnostic power. While LCOM4 in `OrderService` (see Section 5.1) suggests ideal internal binding, a measurement of coupling (CBO) in a "fat service" would immediately raise the alarm. A high CBO value reveals that the class, despite its internal connection via a status field, must serve an excessive number of external dependencies. A design is truly SRP-compliant only when it simultaneously meets both criteria:

* **LCOM4 = 1**, where the connection is based on **domain logic** and not on purely technical infrastructure.
* **CBO ≤ 5**, which ensures that the class does not combine too many external "areas of knowledge".

Only in the overall view of both metrics can it be objectively determined whether a class forms a genuine domain unit or merely represents a collection of loosely coupled tasks held together by technical auxiliary variables.

A "clean" design according to Robert Bräutigam's formalization therefore strives for a class characterized by maximum cohesion with minimal coupling, reflected in a target of an **LCOM4 value of 1** and a **CBO range of 0 to 5**.

> `SRP ≡ max(COHESION) ∧ min(COUPLING)` ≡> `SRP ≡ (LCOM4 = 1) ∧ (CBO ≤ 5)`

The diagram below visualizes the **assessment of a class** using the **LCOM4 and CBO metrics** with regard to the **Single Responsibility Principle**. The **X-axis** measures the number of external dependencies via the **Coupling Between Objects** value. A critical threshold is marked by the **CBO threshold** at a **value of 5**. The **Y-axis** represents the **Lack of Cohesion Method** value and determines the number of isolated subgraphs within a class. While a **value of 1** stands for **maximum cohesion**, values of 2 or higher indicate independent logic islands and therefore insufficient cohesion.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_lcom4_cbo_diagram.png)

The diagram divides classes into four specific quality areas based on their metrics.

The **bottom-left** quadrant marks the **SRP ideal**, where the class exhibits both maximum cohesion (LCOM4 = 1) and loose coupling (CBO < 5). In this state, the Single Responsibility Principle is fully satisfied, as the class handles a clearly defined task with minimal external dependencies.

In contrast, the **top-left** quadrant describes fragmentation. Here, coupling is also low, but the class exhibits low cohesion (LCOM4 = 2). This means that it internally breaks down into two or more isolated logic areas and should consequently be split into separate classes.

The **bottom-right** area identifies the so-called Fat Service. Classes in this sector are internally cohesive and handle a domain unit, but are connected to too many external classes (CBO > 5). This indicates that the class combines too many infrastructure dependencies within itself and, despite domain consistency, becomes too complex for easy maintenance.

The most critical state is formed by the **top-right** quadrant. In this critical area, the class is neither internally cohesive nor externally decoupled. Such a combination of low cohesion and high coupling characterizes a classic anti-pattern, often referred to as a "God Object", which due to its complexity is hardly testable and can only be modified with high risk.

The **choice of threshold** affects at least two conflicting quality goals. A low threshold promotes small, focused classes, but simultaneously increases system fragmentation and therefore the number of abstractions to be managed. A higher threshold allows more compact classes, but makes testing more difficult, as more dependencies must be provided as mocks per class. The optimal threshold therefore results from a trade-off between these two poles and could be determined empirically on an organization-specific basis. Throughout this article, a uniform threshold of **a maximum of 5** is used.

### 4.4 The Conflict Between LCOM4 and CBO

The isolated optimization of either of the two metrics inevitably leads to an architectural dead end. In practice, LCOM4 and CBO values have an inverse relationship.

* **Extreme Cohesion (LCOM4 = 1 through Atomization):** If one tries to perfect the LCOM value by splitting a class into its smallest units, coupling (CBO) in the overall system increases drastically. Many highly specialized classes now need to communicate with each other through complex interfaces to solve a domain task. Complexity shifts from the internal logic of a class to the interaction between the many new smaller classes.
* **Minimal Coupling (CBO → 0 through Centralization):** If, on the other hand, external coupling is radically reduced, one ends up with massive "god classes". These require hardly any external partners, but combine so many different responsibilities within themselves that internal cohesion (LCOM) collapses.

As illustrated in the following diagram, an **SRP-compliant** design moves along the [Pareto front](https://en.wikipedia.org/wiki/Pareto_front). The goal is to find the point or area at which a class remains domain-focused without plunging the system into an impenetrable network of micro-dependencies. An improvement in cohesion must not be purchased through a disproportionate increase in coupling.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_coupling_cohesion_conflict.png)

Optimal design quality is shown where a class has a clear **domain identity** without needing too many external components to fulfill its requirements. This balance between **high cohesion** and **low coupling** prevents structural anti-patterns such as:

* **Spaghetti code**: Uncontrolled and tangled cross-connections between classes.
* **Lasagna code**: Too many fine-grained layers, which make the overall picture harder to grasp.

This balance improves the long-term maintainability and extensibility of the system.

## 5. Examples: Data-Centric Service vs. Object-Oriented Decorator

To demonstrate the practical application of LCOM4 and CBO, the same domain is examined below using different design approaches. The goal is to make the differences in SRP compliance objectively measurable.

The scenario used is the order process of an online shop with three core operations: creating, paying, and canceling.

* **Creating:** Reserves items in the warehouse (InventoryApi), saves the order (OrderRepository), sends a reservation confirmation (Email), and logs the process (Audit).
* **Paying:** Charges the amount (PaymentApi), marks the order as paid, sends a confirmation (Email), and logs the process (Audit).
* **Canceling:** Releases items in the warehouse (InventoryApi), marks the order as canceled, sends a notification (Email), and logs the process (Audit).

This results in seven involved components whose responsibilities are distributed differently across the following implementations: `Cart`, `Customer`, `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email`, and `Audit`.

### 5.1 Service Pattern (Data-Centric)

The classic, data-centric service pattern bundles all operations of an order into a central class that operates on an anemic domain model.

```java
// Anemic Domain Model
public class Order {
    public String id;
    public String status;
    // Getters and setters...
}
```

```java
public class OrderService {

    private OrderRepository orderRepository;   // Field 1 (CBO +1)
    private InventoryApi inventoryApi;         // Field 2 (CBO +1)
    private PaymentApi paymentApi;             // Field 3 (CBO +1)
    private Email email;                       // Field 4 (CBO +1)
    private Audit audit;                       // Field 5 (CBO +1)

    // Parameter: Cart (CBO +1), Customer (CBO +1), Return: Order (CBO +1)
    public Order reserve(Cart cart, Customer customer) {
        // Note: Types are counted across the class;
        // duplicates (e.g. Order in pay/release) are not counted multiple times.
        inventoryApi.reserve(cart);
        Order order = new Order(cart, customer);
        orderRepository.save(order);
        email.sendReservation(order);
        audit.log("Order reserved: " + order.getId());
        return order;
    }

    public void pay(Order order) {
        paymentApi.charge(order);
        order.markAsPaid();
        orderRepository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }

    public void release(Order order) {
        inventoryApi.release(order);
        order.markAsReleased();
        orderRepository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}
```

When used, all required components are injected into a single class. The `OrderService` acts as the central point of contact for all operations.

```java
OrderService service = new OrderService(
    orderRepository,
    inventoryApi,
    paymentApi,
    email,
    audit);

// Creating an order knows 5 dependencies
Order order = service.reserve(cart, customer);

// Paying knows 5 dependencies, even though only 3 are needed
service.pay(order);

// Canceling - service knows 5 dependencies, even though only 3 are needed
service.release(order);
```

An examination using LCOM4 shows that the method `reserve` accesses fields 1, 2, 4, and 5, while `pay` uses fields 1, 3, 4, and 5, and `release` uses fields 1, 2, 4, and 5. Consequently, the class has an **LCOM4 value of 1**, indicating high cohesion, as all methods are linked to each other via the `Repository` and `Audit` logging.

The metric determination yields a **CBO value of 8** for the class, which according to the strict definition of Chidamber & Kemerer (1994) corresponds to very high coupling. Here, all external types are counted, appearing either as fields such as `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email`, and `Audit`, or as parameter and return types such as `Order`, `Cart`, and `Customer`.

In the interpretation, the `OrderService` thus delivers an apparently ideal LCOM4 result. On closer inspection, however, this cohesion turns out to be artificially forced through technical cross-cutting concerns such as persistence and logging. The core domain tasks of inventory management, payment, and email delivery are actually independent of each other, but are merely held together in the graph by the shared use of infrastructure components.

The actual problem is made clear by the critical **CBO value of 8**, which far exceeds the recommended **threshold of 5** and objectively identifies the class as a "Fat Service". Since each method carries the entire burden of dependencies, all **5 fields** must be provided as mocks for a unit test of the `reserve` method, even though most of them are functionally uninvolved in that particular method.

The **Single Responsibility Principle is violated here**, as the class combines several domain-independently motivated reasons for change - such as logic changes in payment, inventory, or email delivery - within itself. This analysis clearly shows that LCOM4 alone is insufficient for diagnosing this problem. The structural deficiencies only become fully visible through the **CBO value of 8**.

### 5.2 Service Pattern (Data-Centric) – Split by Method

An obvious refactoring approach is to apply the subjective SRP definition from Section 2: *"Each software module should have one and only one reason to change"*. Since each of the three methods of `OrderService` represents an independent domain reason for change - reservation, payment, and cancellation - they are transferred to three separate services, with each class containing exactly one method.

```java
// Responsibility: Create order and reserve inventory
public class OrderReservationService {

    private OrderRepository repository; // Field 1 (CBO +1)
    private InventoryApi inventory;     // Field 2 (CBO +1)
    private Email email;                // Field 3 (CBO +1)
    private Audit audit;                // Field 4 (CBO +1)

    public Order reserve(Cart cart, Customer customer) {
        // Cart (CBO +1), Customer (CBO +1), Order (CBO +1)
        inventory.reserve(cart);
        Order order = new Order(cart, customer);
        repository.save(order);
        email.sendReservation(order);
        audit.log("Order reserved: " + order.getId());
        return order;
    }
}

// Responsibility: Process payment
public class OrderPaymentService {

    private OrderRepository repository; // Field 1 (CBO +1)
    private PaymentApi payment;         // Field 2 (CBO +1)
    private Email email;                // Field 3 (CBO +1)
    private Audit audit;                // Field 4 (CBO +1)

    public void process(Order order) {  // Order (CBO +1)
        payment.charge(order);
        order.markAsPaid();
        repository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }
}

// Responsibility: Cancel order and release inventory
public class OrderReleaseService {

    private OrderRepository repository; // Field 1 (CBO +1)
    private InventoryApi inventory;     // Field 2 (CBO +1)
    private Email email;                // Field 3 (CBO +1)
    private Audit audit;                // Field 4 (CBO +1)

    public void release(Order order) { // Order (CBO +1)
        inventory.release(order);
        order.markAsReleased();
        repository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}
```

In practical use, all three services are instantiated as independent components.

```java
OrderReservationService reservationSvc = new OrderReservationService(repository, inventoryApi, email, audit);
OrderPaymentService      paymentSvc    = new OrderPaymentService(repository, paymentApi, email, audit);
OrderReleaseService      releaseSvc    = new OrderReleaseService(repository, inventoryApi, email, audit);

Order order = reservationSvc.reserve(cart, customer);
paymentSvc.process(order);
releaseSvc.release(order);
```

The LCOM4 derivation yields a value of **1** by definition for all three classes, since a class with exactly one method cannot mathematically break down into multiple graphs. This cohesion is therefore mathematically trivial and provides no qualitative insight.

The CBO determination yields a **CBO value of 7** for `OrderReservationService` (4 fields + `Cart`, `Customer`, `Order` from the signature). `OrderPaymentService` and `OrderReleaseService` each result in a **CBO value of 5** (4 fields + `Order` from the signature).

The interpretation makes it clear that splitting by subjective reasons for change reduces class size, but fully retains the core problem. Technical cross-cutting concerns such as `Audit`, `Email`, and `OrderRepository` are now copied into all three services. A change in the logging strategy requires modifications in three different places. Furthermore, `OrderReservationService` and `OrderReleaseService` share the same `InventoryApi` dependency, indicating a domain connection between these operations - a signal picked up in the following section.

SRP is formally adhered to here in the sense of the subjective definition, as each class has exactly one method. The metric analysis shows, however, that this split brings no structural improvement over the monolith: coupling remains high, and cross-cutting concerns are merely replicated rather than isolated.

### 5.3 Service Pattern (Data-Centric) – Split by Domain Context

In contrast to the purely method-based split in Section 5.2, this approach is oriented towards the domain context of the operations. Since `reserve()` and `release()` both operate on the `InventoryApi` and thus concern the same domain of inventory management, they are combined in a shared `OrderStockService`. Payment forms an `OrderPaymentService` as an independent domain responsibility. This grouping more closely corresponds to the principle *"Gather together the things that change for the same reasons"* (cf. Section 2), since the two methods of `OrderStockService` share the same domain reason for change.

An obvious refactoring step is to split the original "Fat Service" into two specialized classes, each representing a domain area of responsibility.

```java

// Responsibility: Inventory management, reservation upon creation and release upon cancellation
public class OrderStockService {

    private OrderRepository repository; // Field 1 (CBO +1)
    private InventoryApi inventory;     // Field 2 (CBO +1)
    private Email email;                // Field 3 (CBO +1)
    private Audit audit;                // Field 4 (CBO +1)

    public Order reserve(Cart cart, Customer customer) {
        // Cart (CBO +1), Customer (CBO +1), Order (CBO +1)
        inventory.reserve(cart);
        Order order = new Order(cart, customer);
        repository.save(order);
        audit.log("Order reserved: " + order.getId());
        return order;
    }

    public void release(Order order) {
        inventory.release(order);
        order.markAsReleased();
        repository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}

// Responsibility: Process payment
public class OrderPaymentService {

    private OrderRepository repository; // Field 1 (CBO +1)
    private PaymentApi payment;         // Field 2 (CBO +1)
    private Email email;                // Field 3 (CBO +1)
    private Audit audit;                // Field 4 (CBO +1)

    public void process(Order order) { // Order (CBO +1)
        payment.charge(order);
        order.markAsPaid();
        repository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }
}
```

In practical use, the `OrderStockService` and `OrderPaymentService` are instantiated as independent components.

```java
OrderStockService stockSvc = new OrderStockService(orderRepository, inventoryApi, email, audit);
OrderPaymentService paymentSvc = new OrderPaymentService(orderRepository, paymentApi, email, audit);

// usage
Order order = stockSvc.reserve(cart, customer);
paymentSvc.process(order);
stockSvc.release(order);
```

Through this deconstruction, the CBO value per class noticeably decreases, as each unit only receives the dependencies it strictly requires for its dedicated task.

The LCOM4 derivation yields a value of 1 for `OrderPaymentService` by definition, since a class with a single method cannot mathematically break down into multiple graphs. `OrderStockService`, on the other hand, contains two methods that belong together domain-wise: both operate on inventory management, access the same fields, and therefore form a single connected graph with LCOM4 = 1.

The metric analysis shows a significant improvement in coupling values. `OrderStockService` achieves a **CBO value of 7**, as in addition to the four fields (`OrderRepository`, `InventoryApi`, `Email`, `Audit`), the signature types `Order`, `Cart`, and `Customer` also count. `OrderPaymentService` achieves a **CBO value of 5**, as its method `process()` only introduces `Order` as an additional type. Although the classes thereby become smaller and easier to handle in unit tests, a fundamental problem remains.

The interpretation reveals that technical cross-cutting concerns such as logging via `Audit` or persistence via `OrderRepository` are still distributed across all three classes. A change in the logging strategy or an adjustment of the `Repository` interface therefore still requires modifications at three different places in the system. The responsibilities are physically distributed across separate files, but remain logically "woven into" each individual operation.

SRP is better addressed here, but not fully satisfied, as the classes suffer from high conceptual redundancy despite their trivial cohesion.

### 5.3 Vertical Decorator Pattern (Object-Oriented)

In Object-Oriented Design, there are various structural patterns (such as Adapter, Bridge, or Decorator) and behavioral patterns (such as Strategy). The decorator pattern, especially in combination with other design patterns, proves particularly well-suited to ensuring strict compliance with the Single Responsibility Principle (SRP). Here, the core logic is isolated in a base class, while domain functional extensions and technical aspects such as logging or persistence are outsourced to separate wrappers. The pattern consistently separates responsibilities through object composition, so that each class takes on exactly one task. Cross-cutting concerns arise here through wrapping objects, not through accumulating fields within a class.

The base interface is simply defined as Order, representing a purely domain term without the technical suffix "Service".

```java
// Base interface
public interface Order {
    String id();
    void process();
    void release();
}
```

In the new design, methods transform into pure behavioral directives. Dependencies on APIs are injected directly into the responsible decorators. Each decorator creates a new instance of itself (or the chain) upon a state change, to satisfy the functional character of the interface. The class `StoredOrder` uses two public constructors for this: one constructor is used for the initial creation of an order with automatic assignment of a `UUID`, while the second constructor allows loading an existing order by its `id` or ensuring `id` preservation within the chain during state changes.

```java
// Core: Persistence - Repository encapsulated, ID in constructor
public class StoredOrder implements Order {

    private String id;             // Field 1
    private Cart cart;             // Field 2 (CBO +1)
    private OrderRepository repo;  // Field 3 (CBO +1)

    // Constructor 1: Initial creation of a new order
    public StoredOrder(Cart cart, OrderRepository repo) {
        // generates 'id' via 'UUID' -> (CBO +1)
        this(UUID.randomUUID().toString(), cart, repo);
    }

    // Constructor 2: Loading an existing order or preserving ID on state change
    public StoredOrder(String id, Cart cart, OrderRepository repo) {
        this.id = id; this.cart = cart; this.repo = repo;
    }

    @Override
    public String id() { return this.id; }           // → Field 1
                                    
    @Override
    public void process() {
        repo.updateStatus(this.id, "PROCESSING");    // → Field 3, 1
    }

    @Override
    public void release() {
        repo.updateStatus(this.id, "CANCELLED");     // → Field 3, 1
    }
}

// Vertical decorator: Payment - injects PaymentApi, uses it in process()
public class PaidOrder implements Order {

    private Order delegate;        // Field 1 (CBO +1)
    private PaymentApi gateway;    // Field 2 (CBO +1)

    public PaidOrder(Order delegate, PaymentApi gateway) {
        this.delegate = delegate; this.gateway = gateway;
    }

    @Override
    public String id() { return delegate.id(); }  // → Field 1

    @Override
    public void process() {
        gateway.charge(delegate);                 // → Field 2, 1
        delegate.process();                       // → Field 1
    }

    @Override
    public void release() { delegate.release(); }   // → Field 1

}

// Vertical decorator: Inventory management - injects InventoryApi, uses it in release()
public class StockedOrder implements Order {

    private Order delegate;        // Field 1 (CBO +1)
    private InventoryApi inv;      // Field 2 (CBO +1)

    public StockedOrder(Order delegate, InventoryApi inv) {
        this.delegate = delegate; this.inv = inv;
    }

    @Override
    public String id() { return delegate.id(); }   // → Field 1

    @Override
    public void process() { delegate.process(); }  // → Field 1
                              
    @Override
    public void release() {
        inv.release(delegate);                     // → Field 2, 1
        delegate.release();                        // → Field 1
    }
}

// Decorator: Audit logging - both methods affected
public class AuditingOrder implements Order {

    private Order delegate;        // Field 1 (CBO +1)
    private Audit audit;           // Field 2 (CBO +1)

    public AuditingOrder(Order delegate, Audit audit) {
        this.delegate = delegate; this.audit = audit;
    }

    @Override
    public String id() { return delegate.id(); }      // → Field 1

    @Override
    public void process() {
        delegate.process();                           // → Field 1
        audit.log("Processed: " + delegate.id());     // → Field 2, 1
    }

    @Override
    public void release() {
        delegate.release();                           // → Field 1
        audit.log("Cancelled: " + delegate.id());     // → Field 2, 1
    }
}

// NotifiedOrder: Same structure as AuditingOrder → LCOM4 = 1, CBO = 2
// Decorator: Email notification - both methods affected
public class NotifiedOrder implements Order {

    private Order delegate;          // Field 1 (CBO +1)
    private Email email;             // Field 2 (CBO +1)

    public NotifiedOrder(Order delegate, Email email) {
        this.delegate = delegate; this.email = email;
    }

    @Override
    public String id() { return delegate.id(); }      // → Field 1

    @Override
    public void process() {
        delegate.process();                           // → Field 1
        email.sendConfirmation(delegate.id());        // → Field 2, 1
    }

    @Override
    public void release() {
        delegate.release();                           // → Field 1
        email.sendCancellation(delegate.id());        // → Field 2, 1
    }
}
```

In practical use, the desired functionality is composed through deep nesting of objects like `new NotifiedOrder(new AuditingOrder(...))`. When `order.process()` is called, the request traverses the entire chain, with each layer contributing its specific part - from payment through persistence to logging and email delivery. Optionally, a new order is simply created via the overloaded constructor `new StoredOrder(cart, repo)`.

```java

// Processing an existing order via id
Order order = new NotifiedOrder(
    new AuditingOrder(
        new StockedOrder(
            new PaidOrder(
                // optional for new order: new StoredOrder(cart, repo) 
                new StoredOrder(id, cart, repo),
                paymentApi),
            inventoryApi),
        audit),
    email);

// Usage: reserve and process

order.process();
// Flow: NotifiedOrder → AuditingOrder → StockedOrder → PaidOrder → StoredOrder
// PaidOrder charges the payment, StoredOrder persists,
// AuditingOrder logs, NotifiedOrder sends the confirmation email.

order.release();
// Flow: NotifiedOrder → AuditingOrder → StockedOrder → PaidOrder → StoredOrder
// StockedOrder releases the inventory, StoredOrder persists,
// AuditingOrder logs, NotifiedOrder sends the cancellation email.
```

The measurement of the metrics yields an impressive picture of decoupling. Each participating class has a **CBO value of 3** and an **LCOM4 value of 1**. These results are not coincidental, but are grounded in domain design. For example, `StoredOrder` requires exactly two fields, as persistence necessarily requires the entity to be stored and the corresponding tool in the form of the repository. Analogously, `PaidOrder` combines the target object with the required infrastructure of the payment interface.

The same principle applies to every other class in this design. In contrast to the previously considered split services, components such as `Audit` or `OrderRepository` here each appear only in a single, specifically designated class. A change in the logging strategy therefore only requires adjusting `AuditingOrder`, while a change to the `Repository` interface exclusively affects `StoredOrder`. Since no other part of the code is affected by these adjustments, cross-cutting concerns are not distributed throughout the system, but are fully isolated within their respective responsibility.

This design simultaneously satisfies the *Open-Closed Principle*: if a new requirement is to be added - for example, an SMS notification after successful payment - a new decorator class `SmsOrder` is sufficient. The existing code remains untouched, as the new class is simply inserted into the composition chain. Every extension is achieved by addition, not by modification.

### 5.4 Horizontal Decorator Pattern (Object-Oriented)

Because the vertical decorator design loses clarity as the number of components grows, [Yegor Bugayenko (2015)](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html) proposes a horizontal approach. Here, a central `Wrapper` object named `Ordering` manages a flat list of instructions/transformations, defined in a separate interface as `OrderAction`. Instead of deep nesting, execution occurs through simple iteration over all registered process steps, with each `OrderAction` class isolating a specific technical or domain task.

```java
// The base interface for instructions of Order object
public interface OrderAction {
    void process(String id, Cart cart);
    void release(String id, Cart cart);
}

// The central wrapper of 'Order' instructions
public class Ordering implements Order {

    private String id;                  // Field 1
    private Cart cart;                  // Field 2 (CBO +1)
    private List<OrderAction> acts;     // Field 3 (CBO +1)

    public Ordering(String id, Cart cart, List<OrderAction> acts) {
        this.id = id; this.cart = cart; this.acts = acts;
    }

    @Override
    public String id() { return this.id; }

    @Override
    public void process() {
        acts.forEach(a -> a.process(this.id, this.cart)); // → Field 3, 1, 2
    }

    @Override
    public void release() {
        acts.forEach(a -> a.release(this.id, this.cart));  // → Field 3, 1, 2
    }
}

// The responsibility implementations
// Each OrderAction class isolates a specific technical or domain task:

// Persistence
public class Persist implements OrderAction {

    private OrderRepository repo;  // Field 1 (CBO +1)

    public Persist(OrderRepository repo) { this.repo = repo; }

    // Cart (CBO +1)
    @Override public void process(String id, Cart cart) { repo.updateStatus(id, "PAID"); }
    @Override public void release(String id, Cart cart) { repo.updateStatus(id, "CANCELLED"); }
}

// Payment (only process() relevant)
public class Pay implements OrderAction {

    private PaymentApi gateway;   // Field 1 (CBO +1)

    public Pay(PaymentApi gateway) { this.gateway = gateway; }

    // Cart (CBO +1)
    @Override public void process(String id, Cart cart) { gateway.charge(id); }
    @Override public void release(String id, Cart cart) { /* empty */ }
}

// Inventory management (only release() relevant)
public class Stock implements OrderAction {

    private InventoryApi inv;   // Field 1 (CBO +1)

    public Stock(InventoryApi inv) { this.inv = inv; }

    // Cart (CBO +1)
    @Override public void process(String id, Cart cart) { /* empty */ }
    @Override public void release(String id, Cart cart) { inv.release(cart); }
}

// Further classes like 'Audit' (logging) and 'Notify' (email) follow the same structure.
```

In practical use, the composition appears flat and equal-ranked compared to the vertical chain. While vertical decorating often represents the easier entry point, the horizontal variant proves to be the more scalable form for complex systems. A call to `order.process()` causes the wrapper to iterate sequentially over all implementations, with persistence setting the status, payment being charged, and finally logging and notification taking place.

```java
Order order = new Ordering(id, cart, List.of(
    new Persist(repo),
    new Pay(paymentApi),
    new Stock(inventoryApi),
    new Audit(audit),
    new Notify(email)
));

order.process(); 
// Ordering iterates: Persist sets status, Pay charges payment, 
// Audit logs, Notify sends confirmation.
```

The metric analysis highlights the structural advantages of this model. The `OrderAction` classes achieve a **CBO value of 2**, as in addition to their own tool (e.g., `PaymentApi`), the interface type `Cart` from the method signature also counts as an external type. This represents a further reduction compared to the vertical decorators, as no `delegate` field is needed for passing calls along, since the chain control is fully delegated to the `Ordering` wrapper with a **CBO value of 3**. Cross-cutting concerns such as Audit or persistence remain strictly isolated in a single class each, while new requirements such as an SMS notification can simply be added as a new implementation without touching existing code, which conforms to the **Open-Closed Principle**.

A structural side effect of this pattern are the empty methods in classes like `Pay` or `Stock`, as not every process step necessarily needs to respond to every action. Accepting these empty implementations is the necessary price for the clean horizontal cut, but simultaneously represents a violation of the [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). According to this principle, a subclass should be designed so that it can fully replace its base class without falsifying the program behavior through unexpected empty steps or restricted behavior.

Alternatively, the `OrderAction` interface could be split in the spirit of the **Interface Segregation Principle (ISP)** into specialized interfaces such as `OnProcess` and `OnRelease`.

```java
// Two segregated interfaces instead of a shared interface
public interface OnProcess {
    void process(String id, Cart cart);
}

public interface OnRelease {
    void release(String id, Cart cart);
}

// The wrapper accepts both types in separate lists
public class Ordering implements Order {

    private String id;
    private Cart cart;                   // Field 1 (CBO +1)
    private List<OnProcess> onProcess;   // Field 2 (CBO +1)
    private List<OnRelease> onRelease;   // Field 3 (CBO +1)

    public Ordering(String id, Cart cart,
                  List<OnProcess> onProcess, List<OnRelease> onRelease) {
        this.id = id; this.cart = cart;
        this.onProcess = onProcess; this.onRelease = onRelease;
    }

    @Override public String id() { return this.id; }

    @Override
    public void process() {
        onProcess.forEach(a -> a.process(this.id, this.cart));
    }

    @Override
    public void release() {
        onRelease.forEach(a -> a.release(this.id, this.cart));
    }
}

// Pay implements only OnProcess - release() is omitted entirely
public class Pay implements OnProcess {

    private PaymentApi gateway;  // Field 1 (CBO +1)

    public Pay(PaymentApi gateway) { this.gateway = gateway; }

    // Cart (CBO +1)
    @Override public void process(String id, Cart cart) { gateway.charge(id); }
}

// Stock implements only OnRelease - process() is omitted entirely
public class Stock implements OnRelease {

    private InventoryApi inv;  // Field 1 (CBO +1)

    public Stock(InventoryApi inv) { this.inv = inv; }

    // Cart (CBO +1)
    @Override public void release(String id, Cart cart) { inv.release(cart); }
}

// Persist responds to both actions and therefore implements both interfaces
public class Persist implements OnProcess, OnRelease {

    private OrderRepository repo;  // Field 1 (CBO +1)

    public Persist(OrderRepository repo) { this.repo = repo; }

    // Cart (CBO +1)
    @Override public void process(String id, Cart cart) { repo.updateStatus(id, "PAID"); }
    @Override public void release(String id, Cart cart) { repo.updateStatus(id, "CANCELLED"); }
}
```

The composition passes `Persist` into both lists, while `Pay` and `Stock` are added exclusively to the appropriate list:

```java
Persist persist = new Persist(repo);

Order order = new Ordering(id, cart,
    List.of(persist, new Pay(paymentApi)),   // OnProcess only
    List.of(persist, new Stock(inventoryApi)) // OnRelease only
);
```

This variant is LSP-compliant, as no class implements a method that is not relevant to its responsibility. The price for this is the increased complexity of the `Ordering` wrapper with a **CBO value of 3**, which now manages two separate lists, as well as the need to explicitly add classes like `Persist` to both lists. This reveals a classic trade-off (cf. Section 4.4) between a flat, scalable structure and strict adherence to all SOLID principles.

## 6. Comparison

The choice of a software design is always a trade-off between initial development speed and long-term maintenance costs (Total Cost of Ownership). A direct comparison of the five approaches shows the structural evolution from monolithic bundling to granular decoupling. The table illustrates the systematic shift of quality parameters across the various design stages. The CBO values are given per class and are based on a complete count of all external types from fields and method signatures (parameters and return types), as defined in Section 4.2. The test effort refers to the number of fields (injected dependencies) to be mocked per class.

| Feature | Service (Monolith) | Services (by Method) | Services (by Domain Context) | Vertical Decorator | Horizontal Decorator |
|---|---|---|---|---|---|
| **Domain Cohesion (LCOM4)** | ❌ LCOM4 = 1 (forced) | ⚠️ LCOM4 = 1 (trivial) | ✅ LCOM4 = 1 (domain-based) | ✅ LCOM4 = 1 (domain-based) | ✅ LCOM4 = 1 (domain-based) |
| **Coupling (CBO)** | ❌ CBO = 8 | ⚠️ CBO = 7 / 5 per class | ⚠️ CBO = 7 (`StockSvc`) / 5 (`PaySvc`) | ✅ CBO = 3 or 2 per class | ✅ CBO = 3 or 2 per class |
| **Change Propagation** | ✅ 1 class | ❌ all 3 classes | ❌ 2 classes | ✅ only 1 class | ✅ only 1 class |
| **Extensibility (OCP)** | ❌ modify methods | ❌ modify methods | ❌ modify methods | ✅ new decorator | ✅ new `OrderAction` + list entry |
| **Test Effort (Mocks)** | ❌ 5 mocks per method | ⚠️ 4 mocks per class | ⚠️ 4 mocks per class | ✅ 2 mocks per class | ✅ 1 mock per class |
| **Usage** | ✅ one class | ✅ three classes | ✅ two classes | ⚠️ deep composition chain | ⚠️ flat, complex list |
| **Structural Risks** | ⚠️ Fat Service, high coupling | ⚠️ maximum redundancy | ⚠️ redundancy, high spread | ⚠️ class fragility | ⚠️ LSP violation or interface fragility |

**Data-Centric Service Cut**

The classic `OrderService` bundles all concerns of an order within a single class. A key advantage of this approach is the central point of contact, as all business logic of a domain remains concentrated in one place as a reliable source of truth. This structure also enables linear debugging, where the control flow can be easily traced within a single file without jumping between many small classes. Since this pattern is considered an industry standard, it also offers a low cognitive barrier for new developers.

On the other hand, there are significant disadvantages, as high coupling can cause changes to technical components such as logging to have unpredictable side effects on business logic. Testability is also impaired, as each method requires a high number of mocks, making unit tests cumbersome and maintenance-intensive. Finally, changes create a high cognitive load, as the entire complexity of the class must always be penetrated to safely adjust even isolated parts.

The split based on the subjective reason-for-change logic (`OrderReservationService`, `OrderPaymentService`, `OrderReleaseService`) maximizes granularity, but further exacerbates the core problem. Each of the three services carries the same four cross-cutting dependencies (`OrderRepository`, `Email`, `Audit`, and the respective domain API), increasing redundancy over the monolith. `OrderReservationService` and `OrderReleaseService` also share `InventoryApi`, showing that the split ignores the domain connection between these operations. Coupling drops to CBO = 5 for two of the three classes, but remains structurally equally burdened.

The decomposition by domain context (`OrderStockService` and `OrderPaymentService`) is a step in the right direction. A key advantage lies in improved clarity, as the classes become smaller and focus on specific sub-processes. `OrderStockService` correctly groups the two inventory operations together, as they share the same domain reason for change. This approach also facilitates parallel development, as different teams can work on different services simultaneously.

However, there are disadvantages, as technical cross-cutting concerns such as `Audit` or `OrderRepository` must still be injected into every service. This leads to distributed maintenance, where global changes to the logging format must be applied simultaneously in multiple places. Ultimately, true isolation is absent, as the structural mixing of technical infrastructure and domain logic persists.

**Object-Oriented Decorator Cut**

This approach cuts the domain strictly by responsibility into classes such as `StoredOrder` or `PaidOrder`. In contrast to object-oriented programming (OOP), which describes the language framework, *Object-Oriented Design* refers to the design discipline in which responsibilities are structurally distributed across objects. The advantages lie in the strict adherence to **SRP** and the Open-Closed Principle, as new requirements are addressed through additional decorators without endangering stable code. This enables targeted debugging, as errors in logging are guaranteed to be found in `AuditingOrder`, while the cognitive load remains limited to the current responsibility. Additionally, minimal coupling is achieved, as each class only knows the interface and its specific dependency.

The disadvantages, however, include a class explosion through a significantly increasing number of files and constructors. The complex composition through deep nesting such as `new PaidOrder(new StoredOrder(...))` also takes some getting used to. The pattern does favor functional extensions (Open-Closed Principle), but makes the interface structure a critical point that causes high effort when changed, as any change to the central `Order` interface forces adjustments in all decorators.

The horizontal pattern optimizes readability with deep chains but enforces structural compromises in the form of empty implementations when the number of methods is large. Switching to this model is therefore only advisable when the design does not require empty method implementations, so that the *Liskov Substitution Principle* is not violated wherever possible.

**Object-Oriented vs. Data-Centric**

The object-oriented cut (**Decorator Pattern**) creates a high specialization of individual classes through its layering. However, this often leads to vertical complexity (**Lasagna code**). In contrast, the data-centric cut (**Service Pattern**) tends toward horizontal growth. Without clear boundaries, too many responsibilities quickly accumulate, increasing coupling and weakening cohesion (**Spaghetti code**).

A design decision must therefore weigh whether the structural depth of the Decorator or the functional breadth of the data-centric pattern better fits the respective domain. The latter offers more direct access to business logic, but is more prone to growing into an unmanageable collection of tasks.

## 7. Summary

The **Single Responsibility Principle** is considered one of the simplest and at the same time one of the most difficult design principles to implement. The cause of this contradiction lies in its subjective formulation. Terms like **reason for change**, **responsibility**, or **actor** are highly context-dependent. In code reviews, they can therefore lead to time-consuming fundamental debates without reaching an objective conclusion.

The formalization proposed by Robert Bräutigam transforms the principle into a measurable structural metric:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

This definition resolves the philosophical problem by linking SRP compliance to two simultaneously required, objectively measurable conditions: high internal cohesion and low external coupling. The former is assessed by LCOM4 via a graph analysis of method and field relationships, the latter by CBO via counting external type dependencies.

The comparison of the five design variants illustrates how these conditions behave in practice and how they condition each other.

The **monolithic structure** of `OrderService` illustrates that an LCOM4 value of 1 can be deceptive. Five domain-independently motivated responsibilities - namely inventory management, payment, email delivery, persistence, and logging - are connected in the graph merely via technical cross-cutting fields like `Audit` and `OrderRepository`. The apparently ideal cohesion is therefore not domain-grounded, but an artifact of shared infrastructure. The CBO value of 8 reveals the actual problem: each method drags along the full burden of all dependencies, making unit tests laborious and turning the class into a central change risk.

The **split by method** (`OrderReservationService`, `OrderPaymentService`, `OrderReleaseService`) demonstrates the limits of the subjective SRP definition. The principle *"one reason to change"* is formally adhered to, as each class has exactly one method. Metrically, however, there is no progress: all three classes carry the same cross-cutting dependencies, redundancy rises to its maximum, and `OrderReservationService` and `OrderReleaseService` share the same `InventoryApi` - an indication that the split ignores the domain connection. The LCOM4 values of 1 are mathematically forced and provide no insight.

The **split by domain context** (`OrderStockService` and `OrderPaymentService`) is a conceptual step forward, as the shared `InventoryApi` dependency of `reserve()` and `release()` is correctly recognized as a domain connection and grouped accordingly. Coupling drops to CBO = 7 (`OrderStockService`) and 5 (`OrderPaymentService`). The core problem remains, however: cross-cutting concerns such as logging and persistence are merely copied into each new class rather than structurally isolated. A change in the logging strategy still requires interventions at multiple places.

The **vertical decorator** achieves genuine domain isolation for the first time. Each class (`StoredOrder`, `PaidOrder`, `AuditingOrder`) knows exactly two dependencies: its delegate object and its specific tool. The CBO value of 2 or 3 and the LCOM4 value of 1 are not forced here, but grounded in domain design. A change to the persistence logic exclusively affects `StoredOrder`. This design is the first of the four patterns to fully satisfy the Bräutigam formula.

The **horizontal decorator** drives decoupling with a CBO value of 2 per process class to the structural extreme. The flat list composition via the `Ordering` class (CBO value of 3) is easier to understand than deep nesting and allows new requirements to be added through a single list entry. The price for this scalability, however, are empty methods in classes like `Pay` or `Stock`, which violate the Liskov Substitution Principle. This tension can be resolved by splitting the `OrderAction` interface according to the Interface Segregation Principle, which, however, further increases the number of abstractions.

## 8. Recommendations for Action

The insights from theory and design comparison can be summarized in four concrete guidelines.

**Consider metrics together, never in isolation.** An LCOM4 value of 1 is a necessary but not sufficient condition for SRP compliance. Only the combination of both metrics provides a reliable picture. The diagnostic guiding question is therefore always: "Why are these methods domain-connected: through genuine domain logic or through technical infrastructure?" A **low LCOM4 with high CBO** is a reliable **warning signal** for a "Fat Service". The **CBO threshold to be applied is not universally fixed** and varies by source between 4 and 14. Since a lower value increases system fragmentation while a higher value makes testing more difficult by requiring more mocks, the threshold should be explicitly defined by the organization or team based on this trade-off.

**Cohesion: Identify technical bridges and evaluate them domain-wise.** Fields such as a `Logger`, an `id`, or a `status field` can act as bridges in the cohesion graph between domain responsibilities that are otherwise foreign to each other. If such cross-cutting fields are the only connection between method groups, this indicates a hidden violation of SRP. The qualitative evaluation of this connection, however, depends significantly on the chosen design paradigm.

* From the perspective of a data-centric design, where the class acts as a data container, the separation by technical reasons for change - such as extracting a `display()` method into a UI-related class - appears to be the structurally appropriate solution.
* In contrast, object-oriented design understands an object as an autonomous unit that fully encapsulates its state. From this perspective, representation belongs inseparably to the core of the entity, as externalization would force the use of getter methods and violate the **Tell, Don't Ask** principle.

Since the LCOM4 metric only evaluates the existence of a connection in the graph and makes no domain classification, it remains the developer's task to decide whether a connection represents a technically artificial bridge or a legitimate domain encapsulation.

**Coupling: Separate physical and semantic dependencies.** A **CBO value greater than 5** signals excessive interconnection. As a first measure, the application of the **Dependency Inversion Principle** is recommended: dependencies on concrete implementations are replaced by stable interfaces, which immediately reduces CBO. Additionally, semantic coupling should be prevented through consistent adherence to the **Law of Demeter** and the **Tell-Don't-Ask principle**. Every method that accesses the internal state of a foreign object creates a critical dependency. A typical example is call chains like `order.getCustomer().getAddress()`. Such **semantic couplings** are often not detected by static analysis tools. During later changes, however, they can frequently lead to unexpected error propagation throughout the entire system.

**Choose design patterns, don't apply them dogmatically.** The **Decorator Pattern** resolves the problem of distributed cross-cutting concerns in a structurally clean manner and is clearly superior to the Service Pattern in terms of SRP compliance. However, it is **no universal cure-all**. Deep decorator chains increase the complexity of object composition and make the system overview harder to grasp, while horizontal variants put pressure on **LSP**. The **data-centric cut** is a **pragmatic option** for simple [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) applications or teams whose skills are not yet aligned with composition-based, object-oriented design patterns, as the low cognitive barrier and centralized overview outweigh in these contexts. The decision for a design approach should therefore be guided by the concrete **competence of the development team** and **maintenance costs**.

Keeping these guidelines in mind, **SRP** proves to be not a rigid dogma, but a **pragmatic tool** that only offers concrete options for action through measurable metrics. Nevertheless, the **readability and comprehensibility** of the overall system should always be weighted more heavily than the one-sided optimization of a single metric, as the overarching goal remains the **long-term maintainability** of the software.

## 9. Final Remarks

The metrics and design patterns presented here represent only a subset of **object-oriented design**. In the academic literature, more precise and mathematically deeper models exist for measuring cohesion and coupling, which capture further nuances of software complexity.

The focus of this article, however, was the **goal of presenting a simple and pragmatic approach**. Since complex formulas are often difficult to apply in **everyday practice**, a **simple-to-use instrument is crucial** that supports quick and well-founded design decisions. Through the combination of **LCOM4 and CBO metrics** with proven **design patterns**, a path has been shown that translates the abstract **Single Responsibility Principle** into manageable practice.

Ultimately, software development is a craft in which metrics serve merely as a compass. The experience and judgment of the development team form the decisive element for sustainable code quality.

## 10. Sources and References

* ACM Digital Library (2026) [Applying the Single Responsibility Principle in Industry: Modularity Benefits and Trade-offs](https://scispace.com/pdf/applying-the-single-responsibility-principle-in-industry-rewdq52at2.pdf)
* Aivosto Oy Helsinki, Finland (1994–2021) [Chidamber & Kemerer Object-Oriented Metrics Suite](https://www.aivosto.com/project/help/pm-oo-ck.html)
* European Southern Observatory (2026) [A METRICS SUITE FOR OBJECT ORIENTED DESIGN](https://www.eso.org/~tcsmgr/oowg-forum/TechMeetings/Articles/OOMetrics.pdf)
* Robert Martin (2007) [The Single Responsibility Principle](https://drive.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/view?resourcekey=0-AbuGpXQzwZcUGExkktKt0g)
* Robert Martin (2014) [The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)
* Robert Bräutigam (2014) [The Single Responsibility Principle](https://speakerdeck.com/robertbraeutigam/single-responsibility-principle)
* Robert Bräutigam (2017) [The Genius of the Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/)
* Sahraoui, Godin & Miceli (2000) [Can Metrics Help Bridging the Gap Between the Improvement of OO Design Quality and Its Automation?](https://www.iro.umontreal.ca/~sahraouh/papers/ICSM00.pdf)
* Martin Fowler (2013) [Tell, Don't Ask](https://martinfowler.com/bliki/TellDontAsk.html)
* Matthieu Cneude (2020) [The Single Responsibility Principle Revisited](https://thevaluable.dev/single-responsibility-principle-revisited/)
* ObjectScript_Q (2026) [Lack of Cohesion in Methods (LCOM4)](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4)
* ObjectScript_Q (2026) [Coupling Between Object classes (CBO)](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo)
* University of Turku (2016) [Design Principles And Patterns](https://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf)
* University of Vienna (1994) [Measuring Coupling and Cohesion In Object-Oriented Systems](http://www.isys.uni-klu.ac.at/PDF/1995-0043-MHBM.pdf)
* Yegor Bugayenko (2015) [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html)
