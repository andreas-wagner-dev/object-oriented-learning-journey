# Business Context-Driven Code Structure 
**State: Draft!!!**
> From Monolith over Modulith to Microservice - *The Missing Link Between Business Model and Code*


## 1. **The Problem:** The Translation Gap

*Why our code structure should be our Business-Context Diagram?*

**Imagine:** A Business Analyst shows you a System Context Diagram of a **Car Rental System**, integrated with **Payment** providers (`PayPal`/`Stripe`), a **Customer** database and **User** booking interface.

```
                       User
                    (Booking)
                        │
                        ▼
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
 │ Payment │    │                 │    │  Customer   │
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │
 │         │    │  (Application)  │    │             │
 └─────────┘    └─────────────────┘    └─────────────┘
                        │
                        ▼
                ┌───────────────┐
                │               │
                │   Car Pool    │
                │  (Inventory)  │
                └───────────────┘
```

Then we open the code and find often one of the following structures:

**Example 1:** Classic Layered Architecture
```
com.company.carrental
├── controller/
├── service/
├── repository/
├── dto/
├── entity/
└── config/
```
**Example 2:** Clean Architecture (Robert C. Martin)
```
com.company.carrental
├── domain/
│   ├── entity/
│   ├── usecase/
│   └── port/
├── application/
├── infrastructure/
│   ├── adapter/
│   ├── database/
│   └── external/
└── presentation/
```
**Example 3:** DDD Structure (Eric Evans)
```
com.company.carrental
├── aggregate/
├── entity/
├── valueobject/
├── repository/
├── service/
├── factory/
└── specification/
```
**Example 4:** Hexagonal Architecture (Alistair Cockburn's)
```
com.company.carrental
├── domain/
├── port/
│   ├── in/
│   └── out/
├── adapter/
│   ├── web/
│   ├── database/
│   └── messaging/
└── application/
```
**Example 5:** Vertical Slices Architecture (Jimmy Bogard's)
```
com.company.carrental
├── features/
│   ├── rent-car/
│   ├── return-car/
│   └── manage-customers/
├── shared/
└── infrastructure/
```

**Where is Car?** - **Where is Customer?** - **Where is Payment?** - **Where is Booking from the Context Diagram?**

**We must mentally translate:**

* **Car** entity is probably hidden in the `service` package? - **Layered**
* Or maybe in `infrastructure`? - **Clean Architecture**
* Could also be in `domain` or `application`? - **DDD**
* Probably in `adapter` or `port`? - **Hexagonal Architecture**
* Maybe in `shared`? or `features/rent-car`? - **Vertical Slices**

Even modern patterns like Vertical Slices often focus primarily on technical functionalities and process logic (**verbs** like `rent-car`, `return-car`), instead of directly mapping domain objects (**things** like `Car`, `Customer`) and bounded contexts as central components to the package structure.

## 2. The Solution: Context-Driven Packaging

**The solution is surprisingly simple:** Our package structure should primarily reflect the Context Diagram.

```
carrental/
├── booking/
├── carpool/
├── customer/
└── payment/
```

**This is a crucial strategic aspect of software architecture:** By directly mapping bounded contexts in code, we consistently apply the Ubiquitous Language at every level of system organization.

Structuring code based on business context diagrams aligns software organization directly with the Ubiquitous Language, eliminating the need for mental mapping. This approach offers significant advantages in developer onboarding and communication by directly mapping domain concepts like "payment" or "booking" to specific packages, allowing for rapid code navigation.

When someone asks: 
*	"Where is the car logic?" → `carpool/`
*	"Where is payment processing?" → `payment/`
*	"Where is customer data?" → `customer/`
*	"Where is the user booking?" → `booking/`

**Benefits: No *mental* translation or mapping needed.** 

**1. Code Navigation**
- *Before:* 1-2 minutes to locate feature
- *After:* 10-30 seconds
- Improvement: ~80-90% less time

**2. Onboarding New Developers**
- *Before:* "Where is the car logic?" → 5 minutes explanation
- *After:* "Look in the `carpool/` package" → 5-10 seconds
- Improvement: ~90% less explanation effort

**3. Business-Developer Communication**
- *Before:* Business says "PayPal problem" → Developer mentally translates to technical packages
- *After:* Business says "PayPal problem" → Developer goes directly to `payment/`
- Improvement: No translation overhead

---

## 3. Object-Oriented Domain-Driven Analysis and Design

### 3.1 Business Capability Mapping

The primary decomposition method (*Business Capability Mapping*) in Domain-Driven Design is based on business capabilities. It focuses exclusively on the **"what"** of the enterprise. These capabilities form the **top-level** packages, ensuring that the software architecture mirrors the business domain one-to-one.

Thanks to this analytical starting point, responsibilities are demarcated with sharp precision. The physical structure of the code follows this division just like the chapters of a book:

**Booking (`booking/`):** Encapsulates the interaction logic for users, reservation workflows, and the state management of bookings.

**Payment (`payment/`):** Exclusively encapsulates the processing of financial transactions and their associated security requirements.

**Customer (`customer/`):** Focuses on the management of renter profiles and customer history.

**Car Pool (`carpool/`):** Manages the entire lifecycle of the physical vehicles and their availability.

### 3.2. Project Package Structure

The packages of an object-oriented system are based on clear OO-Design principles. There are **no layers** in the traditional sense of Clean Architecture or DDD. Instead, **packages are hierarchically organized** according to domain concepts. Classes are also organized hierarchically according to domain concepts and functionally extended using the **Design-Patterns** like *Adapter*, *Decorator*, *Bridge*, etc..

```
carrental/
├── application/                 → Impl. Composition Root
│   ├── CarRentalApp.cs          ← ASP.NET Core main() + DI
│   ├── CarRentalAuth.cs         ← (optional) Auth Configuration
│   ├── CarRentalConfig.cs       ← (optional) Configuration
│   ├── CarRentalDependencies.cs ← (optional) DI or Dependencies
│   └── KafkaQueueConfig.cs      ← Kafka Configuration
├── carpool/
│   ├── CachedCar.cs             ← Cache Decorator
│   ├── CachedCarPool.cs         ← Cache Decorator
│   ├── LoggedCar.cs             ← Logging Decorator
│   ├── SimpleCar.cs             ← Default Decorator (Factory of cars in DDD)
│   ├── StoredCar.cs             ← Database Decorator (use DTO's from exchange/storage/)
│   ├── StoredCarPool.cs         ← Database Decorator (use DTO's from exchange/storage/)
│   ├── ServedCarPool.cs         ← REST service for cars (use DTO's from exchange/resource/)
│   ├── PublishedCar.cs          ← Kafka Producer of Events (use DTO's from exchange/messaging/)
│   ├── ReceivedCar.cs           ← Kafka Consumer of Message (use DTO's from exchange/messaging/)
│   └── ValidCar.cs              ← Validation Decorator
├── customer/
│   ├── StoredCustomer.cs        ← Database Decorator
│   ├── StoredCustomers.cs       ← Database Decorator
│   ├── NotifiedCustomer.cs      ← Email Decorator (use SmtpsEmail from exchange/mailing/)
│   └── ...cs
├── exchange/                    → Anti-Corruption Layer
│   ├── paypal/                  → e.g. Paypal REST library, HTTP endpoint classes JSON/XML DTOs
│   │   ├── Paypal.cs            ← API client
│   │   ├── PaypalRequest.cs     ← Request DTO
│   │   └── PaypalResponse.cs    ← Response DTO
│   ├── resource/                → JSON/XML DTOs for REST service classes
│   │   ├── CarRequest.cs        ← Request DTO as JSON-Object  
│   │   ├── CarResoure.cs        ← Response DTO as JSON-Object 
│   │   └── CarResoures.cs       ← Response DTO as JSON-Array
│   ├── storage/                 ← EF Core           
│   │   ├── CarEntity.cs         ← EF Core DTO
│   │   ├── CarDbContext.cs      ← EF Core DAO
│   │   └── ...cs
│   ├── mailing/                 → Email: SMTPS, IMAPS or POP3S Protocol  
│   │    └── SmtpsEmail.cs       ← SMTPS Email
│   ├── messaging/               ← Queues like Kafka
│   │    └── CarRentedEvent.cs   ← AVRO DTOs
│   │   
│   └── .../
├── payment/                     ← (uses exchange/paypal/)
│   ├── PaypalPayment.cs         ← Paypal API as Decorator
│   ├── CardPayment.cs           ← Credit card Decorator
│   └── ...cs
├── booking/    
│   ├── user/                        ← user contepts and server side UI rendering
│   │   ├── control/                 ← UI common controls (used in page/)
│   │   ├── layout/                  ← UI Layout/Style components (used in page/)
│   │   ├── page/                    ← UI pages of the application
│   │   ├── Control.cs               ← abstract Control
│   │   ├── Layout.cs                ← abstract Layout (Helper/Util)
│   │   ├── Page.cs                  ← abstract Page extends Control
│   │   ├── StoredUser.cs            ← Db Decorator
│   │   └── WebUser.cs               ← Web/Session Decorator
│   ├── IUser.cs                     ← Domain Interface
│   ├── IUsers.cs                    ← Domain Collection Interface
│   ├── StoredReservation.cs         ← Database Decorator
│   ...
├── CarNumber.cs                 ← Shared/Util/Helper for all packages
├── ICar.cs                      ← Domain Interface
├── ICarPool.cs                  ← Collection Interface
├── ICustomer.cs
├── ICustomers.cs
├── IReservation.cs              ← Domain Interface
├── IReservations.cs             ← Domain Collection Interface
├── ICarRentalApp.cs             ← Composition Root Interface
└── ...
```

### 3.3. Key Principles of Packaging and Naming Conventions

### 1) Packages Should Never Depend on Sub-Packages

Root package = Domain core, independent of everything.
- e.g., `ICar`, `ICustomer` as interfaces or abstract classes

Sub-packages = Implementations (adapters), dependent on core.
- e.g., `carpool/ValidCar.cs` implements `ICar`

### 2) Sub-Packages Don't Introduce New Concepts, Only Details

`car/StoredCar.cs` = detail of car persistence.

No new business concepts in sub-packages that don't exist as interfaces in root.

The `application/` package provide main method + (DI) injections of technical infrastructure.

### 3) Packages and Classes Reflect Business Concepts, Not Technical Roles

✅ **Recommended: Package names from Context Diagram**
* `payment/`, `inventory/`, `shipping/` (business concepts or external systems)
* `user/` (GUI interfaces or REST interfaces for GUI e.g. React)
* `exchange/` (everything that requires data exchange with external systems or libraries HTTP / REST / DB / PDF /...)
* `application/` (The application itself - package provide startup class with main method + (DI) injections of technical infrastructure)

Each package forms a logical unit that encapsulates a domain concept and hide implementation details.

✅ **Recommended: Classes names are Nouns (things) with descriptive prefixes (RESULT oriented)**

The naming follows a natural language logic that separates the **"what"** (things) from the **"how"** (context).

**Interfaces are Nouns:**

* `ICar`, `ICustomer`, `IReservation`, also the app it self `ICarRentalApp` as *Composition Root*

They describe the essence of things and represent the **"what."**

**Classes describe the context (prefix-based):** Class names function like adjectives, describing the current state or the result of an action.

* `CachedCar`, `StoredCar`, `ValidCar`
* `PayPalPayment`, `StripePayment`, `PayPal` (use HttpClient), `Stripe` (...Http)
* `InMemoryCar`, `StoredCar`, `CachedCar`, `LoggedCar`, `ValidCar` (prefixes describe WHAT)
* `PublishedCar` (send Kafka messages/events), `ReceivedCar` (receive Kafka messages/events)
* `CarRentalApp` in `application/` (implementation of `ICarRentalApp` interface)

Only what the business customer says - with result oriented prefixes.

❌ **Avoid: Technical package names — very Bad (it is a SHAME)**
* `service/`, `repository/`, `controller/`, `presentation/`, `persistence/`
* `common/`, `shared/`, `util/`, `helper/`
* `adapter/`, `client/`, `wrapper/`, `facade/`, `usecases/`, `interactors/`

Avoid technical package names for grouping by architecture patterns.

❌ **Avoid: Classes names - Verbs or technical suffixes — very Bad (it is a SHAME)**
* `CarProcessor`, `CarManager`, `CarClient`
* `PaymentRepository`, `PaymentService` (architecture pattern)
* `CustomerHandler`, `CustomerValidator`
* `CarService`, `CarManager`, `CarHandler` (verbs/technical suffixes of architecture pattern)
* `CarRepository`, `CarValidator` (technical roles)
* `CarDTO`, `CarModel` (technical classification of architecture pattern)
* `MailHelper`, `CarNumberUtil` (technical waste)
* `CarConsumer`, `CarProducer` (use Received/Published prefix instead)

Avoid meaning of technical things and suffixes of architecture patterns.

## 4. Implementing Object-Oriented Domain-Driven Design

### 4.1 Domain Interfaces in Root Package

The **essential domain concepts** – focusing on the Ubiquitous Language – belong directly in the software's **top-level package**. This ensures *conceptual integrity* and preserves the system's *abstract identity* before technical details distort the domain logic.

```
carrental/             ← Never knows about technical details
├── .../
├── ...
├── ICar.cs           ← The rented asset (Immutable / Read-Only)
├── ICustomer.cs      ← The primary actor of the domain (Root)
├── ICustomers.cs     ← Domain collection abstraction (Repository)
├── ICarRentalApp.cs  ← The system entry point (Composition Root Interface)
└── ...
```

**The Vehicle Interface (ICar.cs)** – Rigid Immutability

To eliminate side effects and guarantee thread safety, the `ICar` interface is strictly immutable. It features no property setters or mutating state methods. Every state transition (such as a reservation) yields a new, updated instance of the vehicle.

```csharp
namespace CarRental;

public interface ICar
{
    string Id();
    bool IsAvailable();
    decimal Price(DateTime from, DateTime to);
    ICar Reserve(string customerId); // Returns a new, reserved vehicle instance
}
```

**The Domain Interface (ICustomer.cs)** – Functional Data Flow

Following the Ubiquitous Language, the customer actively drives the business process. The customer accepts an immutable vehicle and triggers the action. To stay aligned with the immutable paradigm, the Rent method directly returns the newly transformed ICar instance back to the caller.

```csharp
namespace CarRental;

public interface ICustomer
{
    string Id();
    string Name();
    ICar Rent(ICar car, DateTime from, DateTime to); // Returns the transformed vehicle
}
```

### 4.2 The Composition Root Pattern

Placing the main application interface at level "0" of the project structure clearly indicates the starting point of the story to the reader. The `ICarRentalApp.cs` interface serves as the structural umbrella, offering controlled entry to the domain via collection interfaces.

```csharp
namespace CarRental;

public interface ICarRentalApp
{
    ICustomers Customers();
    ICarPool CarPool();
   // other methods ...
}
```
The class `ICarRentalApp.cs` enables access via the collection classes e.g. `ICarPool.cs` to single **domain interfaces** like `ICar.cs`. 

### 4.3 Detail Implementations with Decorators (customer/ & carpool/)

The business process of renting is executed through a chain of decorators. Each business requirement maps to exactly one decorator class, keeping responsibilities strictly separated by their Bounded Contexts.

#### 4.3.1. The Default Vehicle Implementation (carpool/SimpleCar.cs)

The core implementation focuses solely on in-memory data representations and mathematical domain logic. Calling Reserve yields a fresh instance without mutating the existing state.

```csharp
namespace CarRental.CarPool;

public class SimpleCar : ICar
{
    private readonly string _id;
    private readonly string _model;
    private readonly decimal _dailyRate;
    private readonly bool _isRented;
    private readonly string? _customerId;

    public SimpleCar(string id, string model, decimal dailyRate) 
        : this(id, model, dailyRate, false, null) {}

    // Internal constructor for immutable state transitions
    private SimpleCar(string id, string model, decimal dailyRate, bool isRented, string? customerId)
    {
        _id = id;
        _model = model;
        _dailyRate = dailyRate;
        _isRented = isRented;
        _customerId = customerId;
    }

    public string Id() => _id;
    public bool IsAvailable() => !_isRented;
    public decimal Price(DateTime from, DateTime to) => (to - from).Days * _dailyRate;

    public ICar Reserve(string customerId)
    {
        return new SimpleCar(_id, _model, _dailyRate, true, customerId);
    }
}

```

#### 4.3.2. The Vehicle Persistence Decorator (carpool/StoredCar.cs)

This decorator intercepts the Reserve call, triggers the logical state transformation on the underlying instance, and immediately synchronizes the result with the Entity Framework database model. It then wraps the newly created state as a StoredCar.

```csharp
using CarRental.Exchange.Storage;

namespace CarRental.CarPool;

public class StoredCar : ICar
{
    private readonly ICar _origin;
    private readonly CarDbContext _dbContext;

    public StoredCar(ICar origin, CarDbContext dbContext)
    {
        _origin = origin;
        _dbContext = dbContext;
    }

    public string Id() => _origin.Id();
    public bool IsAvailable() => _origin.IsAvailable();
    public decimal Price(DateTime from, DateTime to) => _origin.Price(from, to);

    public ICar Reserve(string customerId)
    {
        // 1. Logical in-memory transformation
        ICar nextCarState = _origin.Reserve(customerId);

        // 2. Persist state changes to the database
        var carEntity = _dbContext.Cars.Find(this.Id());
        if (carEntity != null)
        {
            carEntity.IsRented = true;
            carEntity.CurrentCustomerId = customerId;
            _dbContext.SaveChanges();
        }

        // 3. Decorate the fresh state instance with persistence capabilities again
        return new StoredCar(nextCarState, _dbContext);
    }
}
```

#### 4.3.3. The Default Customer Implementation (`customer/SimpleCustomer.cs`)
The customer's core logic handles process-specific business rules. It validates the request and passes the vehicle transformation directly back up the stack. No dead code, no unutilized return states.

```csharp
namespace CarRental.Customer;

public class SimpleCustomer : ICustomer
{
    private readonly string _id;
    private readonly string _name;

    public SimpleCustomer(string id, string name)
    {
        _id = id;
        _name = name;
    }

    public string Id() => _id;
    public string Name() => _name;

    public ICar Rent(ICar car, DateTime from, DateTime to)
    {
        // Business rule validation
        if (!car.IsAvailable())
        {
            throw new InvalidOperationException($"The vehicle {car.Id()} is already rented.");
        }

        // Execute transformation and return the resulting object immediately
        return car.Reserve(this.Id());
    }
}
```

#### 4.3.4. The Customer Persistence Decorator (`customer/StoredCustomer.cs`)

Because vehicle persistence is completely handled by `StoredCar`, the `StoredCustomer` simply routes the invocation down the pipeline. It remains isolated, focusing purely on customer-specific updates if required by future business features.

```csharp
namespace CarRental.Customer;

public class StoredCustomer : ICustomer
{
    private readonly ICustomer _origin;

    public StoredCustomer(ICustomer origin) => _origin = origin;

    public string Id() => _origin.Id();
    public string Name() => _origin.Name();

    public ICar Rent(ICar car, DateTime from, DateTime to)
    {
        // Cascades through the pipeline (validated by SimpleCustomer, saved by StoredCar)
        ICar reservedCar = _origin.Rent(car, from, to);

        // (Optional: Perform customer-specific DB modifications here)
        return reservedCar;
    }
}
```

### 4.4 The Decorator Composition (The Executable Pipeline)

When executing this flow within an API Controller or a Use Case endpoint, the harmony of this functional design becomes fully apparent:

```csharp
// Inside an API Endpoint / Use Case action:
ICustomer customer = _app.Customers().WithId(customerId); // e.g., a StoredCustomer chain
ICar car = _app.CarPool().CarOf(carId);                   // e.g., a StoredCar chain

// The entire process runs side-effect free across all decorators.
// Databases update, validation guards execute, and clean states flow.
ICar rentedCar = customer.Rent(car, from, to);
```

### 4.5 Implementation of the Composition Root in `application/`

The Composition Root is a unique location in an application where the entire dependency graph is composed.

>"A Composition Root is a unique location in an application where modules are composed together... Only applications should have Composition Roots. Libraries and frameworks shouldn't." — Mark Seemann, Dependency Injection in .NET

In this architecture, the `application/` package acts as the infrastructure shell. It is the only place allowed to reference framework-specific DI mechanisms or an `IServiceProvider`. The rest of the domain remains entirely ignorant of how it is constructed.

```csharp
carrental/
├── application/
│   └── CarRentalApp.cs    ← Application entry point & DI Bootstrap
├── ...
├── ICarPool.cs
├── ICustomer.cs       
├── ICustomers.cs        
└── ICarRentalApp.cs       ← Composition Root Interface (Root level)
```

The `CarRentalAp`p class implements the root-level interface. It uses the `IServiceProvider` as a factory manager to resolve flat infrastructure components (like database contexts or caches) and manually nests decorators to enforce the desired runtime behavior pipeline.

```csharp
using CarRental.CarPool;
using CarRental.Customer;
using CarRental.Exchange.Storage;
using Microsoft.Extensions.Caching.Memory;
using Microsoft.Extensions.DependencyInjection;

namespace CarRental.Application;

public class CarRentalApp : ICarRentalApp
{
    private readonly IServiceProvider _services;

    // Framework Integration: The container is encapsulated exclusively here.
    public CarRentalApp(IServiceProvider services)
    {
        _services = services;
    }

    public static void Main(string[] args) 
    {
        // ASP.NET Core Setup & Host Bootstrapping would live here
    }
    
    public ICarPool CarPool() 
    {
        // Composition Logic: CachedCarPool -> StoredCarPool
        // Infrastructure dependencies are resolved, then decorated with cross-cutting concerns.
        return new CachedCarPool(
            new StoredCarPool(
                _services.GetRequiredService<CarDbContext>()
            ),
            _services.GetRequiredService<IMemoryCache>()
        );
    }

    public ICustomers Customers() 
    {   
        // Composition Logic: StoredCustomers wrapping the database engine
        // optional: CachedCustomers(StoredCustomers(_services.GetRequiredService<CarDbContext>()))
        return new StoredCustomers(
            _services.GetRequiredService<CarDbContext>()
        );
    }
}
```

**Architectural Benefits of this Approach:**

**No Service Locator Anti-Pattern in Domain:** Because `_services.GetRequiredService` is confined inside the Composition Root (`CarRentalApp`), the domain packages (`carpool/`, `customer/`) remain completely clean. They use standard Constructor Injection and have no compile-time dependency on framework DI containers.

**Compile-Time Traceable Pipelines:** The nesting order of the pipelines (e.g., `CachedCarPool` wrapping `StoredCarPoo`l) is explicitly readable as raw code. If a decorator composition needs to change (for instance, removing a cache or adding an audit log), it happens strictly inside these factory methods.

**Decoupled Application Shells:** If we need to switch from an ASP.NET Core Web API to a console-based CLI or a serverless AWS Lambda function, we only swap out or add a new entry point class inside `application/`. The business context, domain interfaces, and decorator pipelines remain untouched.


## 5. Isolation of Frameworks and Libraries: The Anti-Corruption Layer (ACL)

Implementing an  **Anti-Corruption Layer (ACL)** ensures strict decoupling of the core domain logic from third-party Libraries or frameworks like object-relational mappers (ORMs), or external APIs, thereby preventing system contamination.
Depending on codebase size, team organization, and deployment scale, this isolation can be achieved through two distinct structural strategies.

### 5.1 Option 1: Multi-Project Physical Isolation (Enterprise Scale)

For large-scale systems or multi-team environments, third-party libraries and infrastructure tools should be moved out of the main domain assembly entirely. They live in dedicated, compile-time **isolated sub-projects** integrated as flat dependencies.

```
carrental                     → depends on: -endpoint, -resource, -storage, -... 

carrental-endpoint            → HTTP classes JSON/XML DTOs
carrental-resource            → REST classes JSON/XML DTOs
carrental-storage             ← EF Core ...DbContexts/Entities
carrental-paypal              ← Paypal REST client JSON/XML DTOs
carrental-mailing             ← EMAIL sending by SMTPS/IMAPS/POP3S 
carrental-messaging           ← QUEUES integration like Kafka with AVRO classes
carrental-pdf                 ← PDF library with DTOs/helper classes
carrental-text                ← Textformatting library with DTOs/helper classes
carrental-...                 ← other framework or library
```

The classes in these technical projects can then be used in the business packages of **carrental** project - starting at the first level. For example, if we are using ORMs like EF Core, isolate them in a **separate project** `storage` and then use EF classes in the package `carpool/` behind a class like `StoredCar.cs`, which is designed as a `Decorator`, `Bridge` or `Adapter` pattern.


### 5.2 Option 2: Single-Project Logical Isolation (Small to Mid-Sized Codebases)

For more compact applications or single-team projects, spinning up dozens of physical projects adds unnecessary DevOps overhead. Instead, we can achieve the exact same logical isolation by grouping all framework-facing structures into a dedicated root-level `exchange/` package.

```
carrental/
├── application/ 
├── carpool/
│   ├── ...Car.cs       
│   └── StoredCar.cs             ← Uses exchange/storage/ for persistence
├── exchange/
│   ├── resource/                → REST classes JSON/XML DTOs
│   ├── storage/                 ← EF Core Entity       
│   │   ├── CarEntity.cs         ← EF Core Entity
│   │   ├── CarDbContext.cs      ← EF Core DbContext
│   │   ├── CustomerEntity.cs    
│   │   ├── CustomerDbContext.cs
│   │   ├── Db....cs             ← EF Core common Utils or Helper only for this package
│   │   └── ...
│   ├── paypal/                  → Paypal: REST library
│   ├── mailing/                 → Email SMTPS, IMAPS or POP3S Protocol   
│   ├── messaging/               → Queues Apache Avro or Protocol Buffers DTOs
│   └── ...  
├── .../    
└── ICar.cs                      ← Never knows about EF Core
```

**Strict Rules of the Exchange Architectural Boundary:**

* **Unidirectional Dependency Flow:** Domain adapters (such as `StoredCar` in `carpool/`) are allowed to import and look down into the data schemas of the `exchange/` package.
* **Absolute Root Ignorance:** Interfaces and value objects sitting at level "0" (the root package directory like `ICar.cs`) must never import or mention data objects, entities, or libraries originating from `exchange/`.
* **No Cross-Border Contamination:** The `exchange/storage/` package must exclusively deal with persistence mechanisms. It has no say over business decisions.

**E.g. Integration of ORM Layer** -  `exchange/storage/ ` 

```csharp
// exchange/storage/CarEntity.cs
namespace CarRental.Exchange.Storage;

public class CarEntity
{
    public string Id { get; set; } = string.Empty;
    public string Model { get; set; } = string.Empty;
    public decimal DailyRate { get; set; }
    public bool IsRented { get; set; }
    public string? CurrentCustomerId { get; set; }
    public DateTime? RentedFrom { get; set; }
    public DateTime? RentedTo { get; set; }
    public DateTime CreatedAt { get; set; }
}
```

```csharp
// exchange/storage/CarDbContext.cs
using Microsoft.EntityFrameworkCore;

namespace CarRental.Exchange.Storage;

public class CarDbContext : DbContext
{
    public CarDbContext(DbContextOptions<CarDbContext> options) 
        : base(options)
    {
    }

    public DbSet<CarEntity> Cars { get; set; } = null!;

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<CarEntity>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Model).IsRequired().HasMaxLength(200);
            entity.Property(e => e.DailyRate).HasPrecision(18, 2);
        });
    }
}
```

**The Architectural Payoff**

Because these EF Core configurations are completely boxed into the `exchange/` layer, our domain core remains highly malleable. 
* If the team decides tomorrow to drop Entity Framework Core and rewrite the data layer using raw `Dapper SQL` queries or migrate entirely to a document store like *MongoDB*, not a single line of business logic or domain interface needs to change.
* We simply update the contents of the `exchange/storage/` folder and adjust the construction steps inside the `StoredCar` and `CarRentalApp` factories.


## 6. Architectural Evolution Path

A software system's project structure must dynamically adapt to changing organizational scales and business needs. Rather than guessing future scaling patterns on day one, this framework establishes a predictable three-phase evolution model.

```
[ Phase 1: Monolith ] → [ Phase 2: Modular Monolith ] → [ Phase 3: Microservices ]
  (Single Assembly)         (Logical/Project Cut)          (Physical Deployment Cut)
```

### 6.1 Phase 1: The Structured Monolith

Every system should begin here. All Bounded Contexts live within a single deployable assembly, safely partitioned using the strict packaging rules defined in previous chapters.

```
carrental/              ← Single .NET Assembly (.dll / executable)
├── application/        → Composition Root & Framework Setup
├── carpool/            → Bounded Context: Fleet & Assets
├── customer/           → Bounded Context: CRM & Identity
├── exchange/           → Shared Anti-Corruption Layer (ACL)
├── payment/            → Bounded Context: Billing
└── booking/            → Bounded Context: Reservations & UI
```

**Context Indicators:**

* **When to use:** Greenfield projects, team sizes under 10 developers, or scenarios where business domain boundaries are highly volatile.
* **The Advantage:** Ultra-fast refactoring, instant build times, and zero operational deployment complexity.

### 6.2 Phase 2: The Modular Monolith (Modulith)

Migrating to a modular assembly model is a deliberate tactical choice to halt architectural decay as a codebase grows.
A modular structurce of code is NOT an obvious next step, but a conscious decision to combat increasing entropy. It makes sense when:

* **The team is growing:** With around 4-5 developers, natural areas of responsibility begin to emerge. Modules allow these boundaries to be defined in the code, so developers are less likely to "poach" on each other's code.
* **Cognitive overload:** If a developer has to understand half the system to make a small change to the payment system, encapsulation has failed. The modular architecture restores the mental map.
* **Exploding test times:** If the entire test suite runs for every minor change and takes more than 5-10 minutes, modularization helps create test slices that can be validated independently.
* **Preparing for microservices:** A modular architecture is the best insurance against the "distributed monolith." Only when the functional interfaces within the modular architecture are stable is the physical transition to microservices safe.

#### 6.2.1. Strategic Decoupling

Moving to Phase 2 means breaking the single assembly into separate, decoupled projects.

> “Better duplication than the wrong abstraction.” — Sandi Metz

To maximize system autonomy, the **shared kernel** project or `Common/` module must be entirely eliminated. Cross-context structural models - such as `CarId`, `CustomerId`, and `PaymentId` value objects - are intentionally duplicated directly inside the contexts that consume them.

```
carrental-service              ← Deployable Unit
├── [carrental]                ← RECOMMENDED: To be eliminated (Shared Kernel)
├── carrental-application      ← ASP.NET Core Entry Point & DI Configuration (Composition Root)
├── carrental-carpool          ← Bounded Context: Fleet Management
├── carrental-customer         ← Bounded Context: CRM / Identity
├── carrental-payment          ← Bounded Context: Billing & Transactions
└── carrental-booking-ui       ← Frontend / API Gateway Logic
```

**Why avoid the Shared Kernel or Common module?**

* **Total Autonomy:** Context teams can evolve internal primitive behaviors without risk of breaking downward dependencies.
* **Anti-Bloat Protection:** It blocks generic folders from becoming unchecked junk drawers for unrelated code fragments.
* **Semantic Drift Resolution:** A CustomerId model parsing behavior in the billing module may require completely different validations than a CustomerId record utilized by marketing analytics. Depending on repository size and IDE preference, we can organize these decoupled boundaries using a Flat Layout or a Hierarchical Layout.


#### 6.2.2. Project Layouts - Revised Structure

**Strategy A:** Flat Project Layout (Explicit Compilations)

Every single architectural facet compiles to its own specialized project file (`.csproj`). Great for strict architectural validation.

```
carrental-carpool             ← project of customer bounded context 
├── carpool/                  → depends on: carpool-endpoint, *-resource, *-storage, *-mailing
│   ├── CachedCarPool.cs      ← Cache Decorator
│   ├── LoggedCar.cs          ← Logging Decorator
├   ...
├── CustomerId.cs             ← Value Object
├── PaymentId.cs              ← Value Object
├── ICar.cs                   ← Domain Interface
├── ICarPool.cs               ← Collection Interface
...
carrental-carpool-resource    ← REST Services with JSON DTOs
carrental-carpool-storage     ← ORM Entity DTOs with Repositories
carrental-carpool-messaging   ← AVRO Schema generation of DTOs
```

```
carrental-customer             ← project of customer bounded context 
├── customer/                  → depends on: customer-endpoint, *-resource, *-storage, -*-mailing
│   ├── StoredCustomer.cs      ← Database Decorator (use EF-Core and -Entities from ...-*-storage project)
│   ├── StoredCustomers.cs     ← Database Decorator (use EF-Core and -Entities from ...-*-storage project)
│   ├── NotifiedCustomer.cs    ← Email Decorator (use SmtpsEmail from ...-customer-mailing project)
│   └── ...cs
├── CarId.cs                   ← Value Object
├── PaymentId.cs               ← Value Object
├── ICustomer.cs               ← Domain Interface
├── ICustomers.cs              ← Collection Interface
...

carrental-customer-resource    ← REST Services with JSON DTOs
carrental-customer-storage     ← ORM Entity DTOs with Repositories
carrental-customer-mailing     ← Email: SMTPS, IMAPS or POP3S Protocol
```

```
carrental-payment
├── paypal/                    
│   └── ...cs                  ← Payment Decorator (uses Paypal endpoint)
├── Payment.cs                 ← Domain Interface
...

carrental-payment-paypal       ← endpoint of Paypal: REST library
```

```
carrental-booking
...

```

```
carrental                     ← deployable module-composition of all projects, main setup & DI
├── application/              → depends on: carrental-carpool, *-customer, *-payment, *-booking  
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs   ← Kafka Configuration
...                              
```


**Strategy B:** Hierarchical Project Layout (Encapsulated Folders)

If managing dozens of root-level project files creates developer friction, bundle the infrastructure layers as child project directories contained inside the main context group.

```
carrental                     ← Root Module-Composition of all Projects
├── application/              → depends on: carrental-carpool, *-customer, *-payment, *-booking  
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs   ← Kafka Configuration
...

carrental-carpool             → Module Context Parent Directory
├── carpool                   → Bounded Context
├── carpool-resource
├── carpool-storage
└── carpool-messaging

carrental-customer            ← Module Context Parent Directory
├── customer                  → Bounded Context
├── customer-resource
├── customer-storage
└── customer-mailing

carrental-payment            ← Module Context Parent Directory
├── payment                  → Bounded Context  
└── payment-paypal


carrental-booking            ← Module Context Parent Directory
├── user                     → Bounded Context
└── ...

```

### 6.3 Phase 3: Distributed Microservices

Microservices are NOT an automatic next step, they introduce extensive operational overhead (distributed transactions, partial network failures, deployment orchestration). This shift must be driven by strict technical necessity, never by design trends.

The following division is the logical consequence of Business Context-Driven structure. It separates  Bounded Contexts along with their technical infrastructure dependencies to individual microservices. Each service is structured like the **Mono or Modulith** artifact (in Phase 1 or 2).

```
carrental-gateway-service   ← artifact (build as deployable .dll)

carrental-carpool-service   ← artifact (build as deployable  .dll)
carrental-customer-service  ← artifact (build as deployable .dll)
carrental-payment-service   ← artifact (build as deployable .dll)

carrental-booking-service   ← artifact frontend (build as deployable  .dll)
```

**Note:** Each service maintains the SAME internal structure as the monolith/modulith. Only deployment boundaries change.


## 7. Frontend as a Standalone Microservice (BFF Pattern)

In modern cloud-native architectures, it is often beneficial to treat the frontend not merely as a static asset, a collection of files (such as .js, .css, .html), but as a standalone "Backend-for-Frontend" (BFF) service. The BFF functions as a customized user interface precisely tailored to the needs of a specific client (e.g., web, mobile, or smart device). This enables a strict separation of presentation logic and business interfaces.

```
carrental-booking-client             → Frontend Project / BFF Service Project 
├── src/
│   ├── application/                  → startup: composition root pure JS / Node.js
│   │   ├── NativeCarRentalApp.js     → decorator pure JS: main entry point
│   │   ├── ServedCarRentalApp.js     → decorator Node.js: main entry point
│   │   ...
│   ├── exchange/                     → access of external resources
│   │   ├── auth/                     → authentication
│   │   │   ├── AuthApi.js            → HTTP client / ext. auth api
│   │   │   ├── JwtAuthApi.js         → JWT-Token based auth api
│   │   │   ...
│   │   ├── database/                 → database / schema generation
│   │   │   ├── UserDb.js
│   │   │   ...
│   │   ├── endpoint/                 → HTTP clients for business services
│   │   │   ├── CarPoolApi.js
│   │   │   ├── CustomerApi.js
│   │   │   ├── PaymentApi.js
│   │   ...
│   ├── control/                 → custom UI elements / external plugins (jquery ui)
│   │   ├── InputGroup.js        → composite container
│   │   ├── TextInput.js         → input field
│   │   ├── TextLabel.js         → output field
│   │   ├── Form.js              → abstract Form extends Control  
│   │   ├── Page.js              → abstract Page extends Control  
│   │   ├── List.js              → abstract List extends Control 
│   │   ├── Table.js             → abstract Table extends Control  
│   │   ├── Menu.js              → abstract Menu extends Control  
│   │   ...
│   ├── layout/                  → CSS styles / pictures / layouts 
│   │   ├── icon/                → icons of application
│   │   ├── image/               → images of application
│   │   │   ├── background.png
│   │   │   ...
│   │   ├── layout.css           → CSS common rules for layouts
│   │   ├── Layout.js            → layout as helper class
│   │ ...
│   ├── page/                    → HTML sites or JS page components
│   │   ├── admin-form.js
│   │   ├── auth-page.js         → page of Login
│   │   ├── car-details.js    
│   │   ├── carpool-list.js  
│   │   ├── payment-form.js  
│   │   ├── user-profile.js
│   │   ├── main-form.js  
│   │   ├── main-menu.js
│   │   ├── page-navigation.js   → impl. of page router
│   │  ...
│   ├── customer/                → implementation of domain logic of Person
│   │   ├── ValidAddress.js      → decorator for validation
│   │   ├── ValidCustomer.js     → decorator for validation
│   │   ...                      → other decorators (Logged*, Cashed*)
│   ├── Address.js               → abstract domain class/interface
│   ├── Auth.js                  → abstract authentication
│   ├── Control.js               → abstract UI component class (composite-pattern)
│   ├── Navigation.js            → abstract router/navigation
│   ├── Car.js                   → abstract domain class/interface
│   ├── CarPool.js               → abstract domain class/interface
│   ├── Customer.js              → abstract domain class/interface
│   ├── CarRentalApp.js          → abstract main class for composition root
│   ├── UserProfile.js           → domain class extends Person
│   ...
├── test/                        → Unit and integration tests
├── .gitignore                   → Environment variables
├── .environment
├── readme.md
├── package.json
│   ...
```

By retaining identical structural patterns across both front-end and back-end repositories, engineers can navigate the entire system with zero cognitive friction. A feature update to the payment workflow targets a `payment/` package on both sides of the network boundary.

### 8. Conclusion: Screaming Architecture

When *Uncle Bob* (Robert C. Martin) coined the term **"Screaming Architecture"**, he pointed out a major flaw in modern software development: when we look at a software system's codebase, the directory layout usually screams the framework choices (e.g., `controllers/`, `views/`, `models/`) rather than telling us what the application actually does. By anchoring our code organization directly to our Business Context Diagram, our structure stops screaming technical plumbing and starts screaming its actual business purpose.

```
DOGMATIC STRUCTURE                    SCREAMING STRUCTURE
(Technical Plumbing)                  (Business Intent)

com.company.carrental                  com.company.carrental
├── controllers/                       ├── booking/
├── services/             ──►          ├── carpool/
├── repositories/                      ├── customer/
└── dtos/                              └── payment/
```

To elevate our project structures to this next level of maintainability, readability, and evolutionary capability, adhere to these [Three Golden Rules](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/):
1. **Packages Never Depend on Sub-Packages:** The root package folder defines our absolute domain core. It remains pure and completely independent. Sub-packages depend on the core to implement its details, never the other way around.
2. **Sub-Packages Introduce Details, Not new Concepts:** A sub-package file (like `carpool/StoredCar.cs`) must only provide a technological refinement of an existing domain abstraction. It is forbidden from inventing unmapped business capabilities.
3. **Represent Business Concepts, Not Technical Patterns:** Abolish prozedural suffixes and organizational patterns like `*Service`, `*Repository`, *Handler, and `*DTO`. Name the components as real-world nouns prefixed with their direct functional outcome (e.g., `StoredCar`, `CachedCarPool`, `ValidCustomer`).

Through the implementation of these rules - supplemented by "Rigid Immutability," "Pipeline Decorators," and an "Anti-Corruption Layer" our code ceases to be an unreadable jumble of framework instructions. It transforms into an executable narrative that directly reflects the business domain, scales cleanly across product milestones, and permanently bridges the translation gap.

**Screaming Architecture:** —> means the structure screams the only business domain at you. No translation layer. No mental mapping. Just direct, obvious correspondence between business concepts and code structure.

---
## 9. References and Further Reading

* Robert Bräutingam, [Happy-Packaging (2017)](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
* Robert C. Martin,[Screaming-Architecture (2011)](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
* Philipp Hauer, [Package by Feature (2020)](https://phauer.com/2020/package-by-feature/)
* Mark Seemann, [Composition Root Pattern (2011)](https://blog.ploeh.dk/2011/07/28/CompositionRoot/)
* Mark Seemann, [Poor Man's DI Pattern (2012)](https://blog.ploeh.dk/2012/11/06/WhentouseaDIContainer/)
* Mihai A. RODEGBFR, [My Take On Object Naming (2018)](https://amihaiemil.com/2018/01/07/my-take-on-object-naming.html)
* Yegor Bugayenko, [Composable Decorators (2015)](https://www.yegor256.com/2015/02/26/composable-decorators.html)
* Yegor Bugayenko, [Seven Virtues of a Good Object](https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html)
* Yegor Bugayenko, [Prefixed Naming](https://www.yegor256.com/2020/03/03/prefixed-naming.html)
* Yegor Bugayenko, [Don't Create Objects That End With -ER](https://www.yegor256.com/2015/03/09/objects-end-with-er.html)
* Yegor Bugayenko, [A Compound Name Is a Code Smell](https://www.yegor256.com/2015/01/12/compound-name-is-code-smell.html)
* Yegor Bugayenko, [Evil Suffix For Object Names](https://www.yegor256.com/2017/09/12/evil-object-name-suffix-client.html)

