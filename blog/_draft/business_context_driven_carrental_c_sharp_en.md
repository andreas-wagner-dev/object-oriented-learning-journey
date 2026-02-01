# Business Context-Driven Package Structure: The Missing Link Between Business and Code

> Why Your Package Structure Should Be Your Business Context Diagram?

**The Problem:** The Translation Gap

**Imagine:** A Business Analyst shows you a System Context Diagram of a **Car Rental System**, integrated with `Payment` providers (`PayPal`/`Stripe`), a `Customer` database, and `User` interfaces.

```
                      User
                        │
                        ▼
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
 │ Payment │    │                 │    │  Customer   │
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │
 │         │    │                 │    │             │
 └─────────┘    └─────────────────┘    └─────────────┘
                        │
                        ▼
                ┌───────────────┐
                │               │
                │   Car Pool    │
                │  (Inventory)  │
                └───────────────┘
```
Then you open the code and find:

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

* **Where is Car?**
* **Where is Customer?**
* **Where is Payment?**
* **Where is User from the Context Diagram?**

**You must mentally translate:**

* **Car** entity is probably hidden in the `service` package? - **Layered**
* Or maybe in `infrastructure`? - **Clean Architecture**
* Could also be in `domain` or `application`? - **DDD**
* Probably in `adapter` or `port`? - **Hexagonal Architecture**
* Maybe in `shared`? or `features/rent-car`? - **Vertical Slices**

You have to guess whether the `Car` logic is in `repository`, `service`, or some `domain` package.  
Where is the `PayPal` integration?  
Is `Customer` in *entity* or *aggregate*?

Even modern patterns like Vertical Slices often focus primarily on technical functionalities and process logic (**verbs** like `rent-car`, `return-car`), instead of directly mapping domain objects (**things** like `Car`, `Customer`) and external contexts as central components of the package structure.

## The Solution: Context-Driven Packaging
**The solution is surprisingly simple:** Your package structure should primarily reflect the Context Diagram.

```
carrental/
├── carpool/
├── customer/
├── payment/
└── user/
```

**This is a crucial strategic aspect of software architecture:** By directly mapping business contexts in code, we consistently apply the *Ubiquitous Language* at every level of system organization.

---

## Benefits

**1. Code Navigation**
- Before: 1-2 minutes to locate feature
- After: 10-30 seconds
- Improvement: ~80-90% less time

**2. Onboarding New Developers**
- Before: "Where is the car logic?" → 5 minutes explanation
- After: "Look in the carpool/ package" → 30 seconds
- Improvement: ~90% less explanation effort

**3. Business-Developer Communication**
- Before: Business says "carpool problem" → Developer mentally translates to technical packages
- After: Business says "carpool problem" → Developer goes directly to `carpool/`
- Improvement: No translation overhead


## Business Context-Driven - Project Structure

```
carrental/
├── application/
│   ├── CarRentalApp.cs          ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs      ← Kafka Configuration
├── carpool/
│   ├── CachedCarPool.cs         ← Cache Decorator
│   ├── LoggedCar.cs             ← Logging Decorator
│   ├── StoredCar.cs             ← Database Decorator
│   ├── StoredCarPool.cs         ← Database Decorator
│   ├── ServedCarPool.cs         ← REST service for cars (use DTO's from exchange/resource/)
│   ├── PublishedCar.cs          ← Kafka Producer of Events (use DTO's from exchange/messaging/)
│   ├── ReceivedCar.cs           ← Kafka Consumer of Message (use DTO's from exchange/messaging/)
│   └── ValidCar.cs              ← Validation Decorator
├── customer/
│   ├── StoredCustomer.cs        ← Database Decorator
│   ├── StoredCustomers.cs       ← Database Decorator
│   ├── NotifiedCustomer.cs      ← Email Decorator
│   └── ...cs
├── exchange/
│   ├── endpoint/                → HTTP client classes JSON/XML DTOs
│   ├── resource/                → JSON/XML DTOs for REST service classes
│   │   ├── CarResoure.cs        ← DTO of Entity
│   │   ├── CarResoures.cs       ← Entry Point class (REST-Service)
│   │   ├── CarRequest.cs        ← Request DTO
│   │   └── CarResponse.cs       ← Response DTO
│   ├── storage/                 ← EF Core Entity           
│   │   ├── CarEntity.cs         ← EF Core Entity DTO
│   │   ├── CarDbContext.cs      ← EF Core Entity DTO
│   │   └── ...cs
│   ├── messaging/               ← Queues like Kafka
│   │    └── CarRentedEvent.cs   ← AVRO DTOs
│   │   
│   └── .../            
├── user/                        ← user contepts and server side UI rendering
│   ├── control/                 ← UI common controls (used in page/)
│   ├── layout/                  ← UI Layout/Style components (used in page/)
│   ├── page/                    ← UI pages of the application
│   ├── Control.cs               ← abstract Control
│   ├── Layout.cs                ← abstract Layout (Helper/Util)
│   ├── Page.cs                  ← abstract Page extends Control
│   ├── DbUser.cs                ← Db Decorator
│   └── WebUser.cs               ← Web/Session Decorator
├── CarNumber.cs                 ← Shared/Util/Helper for all packages
├── ICar.cs                      ← Domain Interface
├── ICarPool.cs                  ← Collection Interface
├── ICustomer.cs
├── ICustomers.cs
├── IUser.cs                     ← Domain Interface
├── IUsers.cs                    ← Collection Interface
├── ICarRentalApp.cs             ← Composition Root Interface
└── ...
```

---

## Root Level - Domain Interfaces

```csharp
// ICarRentalApp.cs - Composition Root Interface (ROOT!)
namespace CarRental;

public interface ICarRentalApp
{
    ICarPool CarPool();
    ICustomers Customers();
}
```

```csharp
// ICar.cs - Domain Interface (ROOT!)
namespace CarRental;

public interface ICar
{
    void Rent(ICustomer customer, DateTime from, DateTime to);
    void Return();
    bool IsAvailable();
    decimal CalculatePrice(DateTime from, DateTime to);
}
```

---

## exchange/storage/ - ORM Layer (Integration)

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

---

## carpool/ - Implementations with Decorators

```csharp
// carpool/InMemoryCar.cs - Core Implementation
namespace CarRental.CarPool;

public sealed class InMemoryCar : ICar
{
    private readonly string _id;
    private readonly string _model;
    private readonly decimal _dailyRate;
    private bool _isRented;

    public InMemoryCar(string id, string model, decimal dailyRate)
    {
        _id = id;
        _model = model;
        _dailyRate = dailyRate;
    }

    public void Rent(ICustomer customer, DateTime from, DateTime to) => _isRented = true;
    public void Return() => _isRented = false;
    public bool IsAvailable() => !_isRented;
    public decimal CalculatePrice(DateTime from, DateTime to) => (to - from).Days * _dailyRate;
}
```

```csharp
// carpool/StoredCar.cs - Database Decorator (using exchange/storage/)
using CarRental.Exchange.Storage;

namespace CarRental.CarPool;

public sealed class StoredCar : ICar
{
    private readonly ICar _origin;
    private readonly CarDbContext _dbContext;
    private readonly string _carId;

    public StoredCar(ICar origin, CarDbContext dbContext, string carId)
    {
        _origin = origin;
        _dbContext = dbContext;
        _carId = carId;
    }

    public void Rent(ICustomer customer, DateTime from, DateTime to)
    {
        _origin.Rent(customer, from, to);
        var entity = _dbContext.Cars.Find(_carId);
        if (entity != null)
        {
            entity.IsRented = true;
            entity.CurrentCustomerId = customer.Id();
            _dbContext.SaveChanges();
        }
    }

    public void Return()
    {
        _origin.Return();
        var entity = _dbContext.Cars.Find(_carId);
        if (entity != null)
        {
            entity.IsRented = false;
            _dbContext.SaveChanges();
        }
    }

    public bool IsAvailable() => _origin.IsAvailable();
    public decimal CalculatePrice(DateTime from, DateTime to) => _origin.CalculatePrice(from, to);
}
```

**Decorator Composition:**

```
  → ValidCar (Validation)
    → StoredCar (Persistence via exchange/storage/)
      → CachedCar (Caching)
        → LoggedCar (Logging)
          → PublishedCar (Events)
```

**The Core Message: Decorators ARE Your Business Logic**

Here's the paradigm shift:

**Traditional thinking:**

"Decorators are a technical pattern for adding functionality."

**Business-driven thinking:**

"Decorators ARE the business process, made executable."

Look at this decorator chain again:

```
Validation → Events → Logging → Caching → Persistence
    ↓          ↓        ↓         ↓          ↓
 Business   Domain    Audit   Performance   Data
  Rules     Events    Trail   Optimization  Storage
This isn't technical plumbing. This is your business.
```

* When Product Owner says "we need to validate rental dates," you add **ValidCar**. 
* When Compliance says "we need audit trails," you add **LoggedCar**. 
* When Operations says "we need event-driven architecture," you add **PublishedCar**.

Each business requirement = one decorator. Clear. Traceable. Maintainable.

---

## application/ - Framework Integration & Root Composition with DI

```csharp
using CarRental.CarPool;
using CarRental.Exchange.Storage;
using Microsoft.Extensions.Caching.Memory;
using Microsoft.Extensions.DependencyInjection;

namespace CarRental.Application;

public class CarRentalApp : ICarRentalApp
{
    private readonly IServiceProvider _services;
    public CarRentalApp(IServiceProvider services) => _services = services;

    public static void Main(string[] args) 
    {
        // ASP.NET Core Setup & DI Configuration
    }
    
    public ICarPool CarPool() 
    {
        // Implementation of ICarPool using collection decorators
        // Composition logic: CachedCarPool(StoredCarPool(InMemoryCars))
        return new CachedCarPool(
            new StoredCarPool(
                _services.GetRequiredService<CarDbContext>()
            ),
            _services.GetRequiredService<IMemoryCache>()
        );
    }

    public ICustomers Customers() 
    {   // Composition logic: CachedCustomers(StoredCustomers(_services.GetRequiredService<CustomerDbContext>()))
        // Returns the collection implementation for customers
        return ...;
    }
}
```


---

## Key Principles and Conventions

### 1. Packages Should Never Depend on Sub-Packages

Root package = Domain core, independent of everything.
- e.g., `ICar`, `ICustomer` as interfaces or abstract classes

Sub-packages = Implementations (adapters), dependent on core.
- e.g., `carpool/ValidCar.cs` implements `ICar`

### 2. Sub-Packages Don't Introduce New Concepts, Only Details

`car/StoredCar.cs` = detail of car persistence.

No new business concepts in sub-packages that don't exist as interfaces in root.

The `application/` package provide main method + (DI) injections of technical infrastructure.

### 3. Packages and Classes Reflect Business Concepts, Not Technical Roles

✅ **Correct: Classes names are Nouns (things) with descriptive prefixes (RESULT oriented)**
- `CachedCar`, `StoredCar`, `ValidCar`
- `PayPalPayment`, `StripePayment`, `PayPal` (use HttpClient), `Stripe` (...Http)
- `customer/StoredCustomer`, `customer/ValidCustomer`
- `InMemoryCar`, `StoredCar`, `CachedCar`, `LoggedCar`, `ValidCar` (prefixes describe WHAT)
- `PublishedCar` (send Kafka messages/events), `ReceivedCar` (receive Kafka messages/events)
- `ICarRentalApp` interface in root, `CarRentalApp` in `application/`

Only what the business/customer says - with result oriented prefixes.

✅ **Correct: Package names from Context Diagram**
- `payment/`, `inventory/`, `shipping/` (business concepts or external systems)
- `user/` (GUI interfaces or REST interfaces for GUI e.g. React)
- `exchange/` (everything that requires data exchange with external systems HTTP / REST / DB /...)
- `exchange/storage/` or `exchange/database/`

❌ **Wrong: Classes names - Verbs or technical suffixes — very Bad (it is a SHAME)**
- `carpool/CarProcessor`, `carpool/CarManager`, `carpool/CarClient`
- `payment/PaymentRepository`, `payment/PaymentService`
- `customer/CustomerHandler`, `customer/CustomerValidator`
- `CarService`, `CarManager`, `CarHandler` (verbs/technical suffixes)
- `CarRepository`, `CarValidator` (technical roles)
- `CarDTO`, `CarModel` (technical classification)
- `CarConsumer`, `CarProducer` (use Kafka prefix instead)

❌ **Wrong: Technical package names — very Bad (it is a SHAME)**
- `service/`, `repository/`, `controller/`, `presentation/`, `persistence/`
- `common/`, `shared/`, `util/`, `helper/`
- `adapter/`, `client/`, `wrapper/`, `facade/`, `usecases/`, `interactors/`

Avoid meaning of technical things and suffixes of architecture pattern.

### 4. Isolation of Frameworks and Libraries 

Ideally, such technical aspects of frameworks should be outsourced to separate projects and integrated into the main project as dependencies.

```
carrental                     → depends on: -endpoint, -resource, -storage, -... 

carrental-endpoint            → HTTP classes JSON/XML DTOs
carrental-resource            → REST classes JSON/XML DTOs
carrental-storage             ← EF Core Entity   
carrental-...                 ← other framework or library  
```

The classes in these technical projects can then be used in the business packages of **carrental** project - starting at the first level.  
E. g. when using ORMs like EF Core, isolate them in a `storage` project and then use EF-Classes in the `carpool/` package.  

**Important:** The domain interfaces and classes in the root package of **carrental** project should never use classes technical of projects.

**Alternative:** suitable for projects with small codebases.

Isolate all technical aspects (everything that requires data exchange with external systems) into a dedicated package `exchange/` followed by further subpackages for each aspect, such as:
* `endpoint/`   → HTTP classes for WEB access with helper classes
* `resource/`   → HTTP classes for REST and JSON/XML DTOs with helper classes
* `storage/`    → ORM classes for DB access with helper classes
* `mailing/`    → SMTPS/IMAPS/POP3S for EMAIL sending and server integration with helper classes
* `messaging/`  → AVRO classes for Kafka integration with helper classes
* `text/`       → Textformatting Library with helper classes
* `pdf/`        → PDF Library with helper classes
* `other.../`   → ... Library/classes ...

The classes in these packages can then be used in the business packages starting at the first level.  
E. g. when using ORMs like EF Core, isolate them in the `exchange/storage/` package.

```
carrental/
├── application/ 
├── carpool/            
│   └── StoredCar.cs             ← Uses exchange/storage/ for persistence
├── exchange/
│   ├── endpoint/            → HTTP classes JSON/XML DTOs
│   ├── resource/            → REST classes JSON/XML DTOs
│   ├── storage/             ← EF Core Entity       
│   │   ├── CarEntity.cs     ← EF Core Entity
│   │   ├── CarDbContext.cs  ← EF Core DbContext
│   │   ├── Db....cs         ← EF Core common Utils or Helper only for this package
│   │   └── ...
│   ├── mailing/             → Email: SMTPS, IMAPS or POP3S Protocol   
│   ├── messaging/           → Queues Apache Avro or Protocol Buffers DTOs
│   └── ...  
├── .../    
└── ICar.cs              ← Never knows about EF Core
```

**Important:**
The domain interfaces and classes in the root package should never contain such technical DTO classes.
* All ORM classes (Entity, DbContext) live in `exchange/storage/`
* The package `exchange/storage/` can be used by `carpool/`, `payment/` not otherwise
* Domain adapters (like `StoredCar` in `carpool/`) access `exchange/storage/`

### 5. Composition Root Pattern

**Interface in Root:**
```csharp
// ICarRentalApp.cs (ROOT!)
public interface ICarRentalApp
{
    ICarPool CarPool();
    ICustomers Customers();
}
```

**Implementation in application/:**
```csharp
// application/CarRentalApp.cs
public class CarRentalApp : ICarRentalApp
{
    public static void Main(string[] args) { ... }
    
    public ICarPool CarPool() 
    {
        // Compose decorators here
    }
}
```

This ensures framework independence and clean dependency flow.



---

## Evolution Path

### Mono Artifact (Phase 1)
```
carrental/
├── application/
├── carpool/
├── customer/
├── payment/
└── user/
```

### Modulith Artifacts (Phase 2)
```
carrental-service/      ← service is something deployable or an artifact
├── carrental/          ← Core module (all common or shared intefaces/classes)
├── carrental-app/      ← ASP.NET Core Main + DI
├── carrental-carpool/
├── carrental-customer/
└── .../
```

### Microservices (Phase 3)

Split at package/module boundaries.
Each service is structured like the **Mono** artifact (in Phase 1 or 2)
```
carrental-gateway-service   ← artifact (build as deployable .dll)
carrental-client-service    ← artifact frontend (build as deployable  .dll)
carrental-carpool-service   ← artifact (build as deployable  .dll)
carrental-customer-service  ← artifact (build as deployable .dll)
carrental-payment-service   ← artifact (build as deployable .dll)
```

Each phase maintains the same principles - only deployment boundaries change.

**Screaming Architecture:** —> means your package structure IS your business context diagram. 

No *mental* translation or mapping needed.

---
# References

* Java Dev Guy, [Happy-Packaging (2017)](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
* Mark Seemann, [Composition Root Pattern (2011)](https://blog.ploeh.dk/2011/07/28/CompositionRoot/)
* Mark Seemann, [Poor Man's DI Pattern (2012)](https://blog.ploeh.dk/2012/11/06/WhentouseaDIContainer/)
* Wikipedia, [Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)
