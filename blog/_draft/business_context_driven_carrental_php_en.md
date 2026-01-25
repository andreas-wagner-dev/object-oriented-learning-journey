# CarRental - PHP Implementation

## Project Structure (PSR-4)

### Phase 1: Monolithic Package Structure

```
src/
├── CarRental/
│   ├── App/
│   │   ├── WebCarRentalApp.php      ← Composition Root
│   │   └── KafkaQueueConfig.php     ← Kafka Configuration
│   ├── CarPool/
│   │   ├── DbCar.php                ← Database Decorator
│   │   ├── DbCarPool.php            ← Database Decorator (Collection)
│   │   ├── CachedCarPool.php        ← Cache Decorator
│   │   ├── LoggedCar.php            ← Logging Decorator
│   │   ├── ValidCar.php             ← Validation Decorator
│   │   ├── PublishedCar.php         ← Kafka Producer (sends Messages)
│   │   └── ReceivedCar.php          ← Kafka Consumer (receives Events)
│   ├── Customer/
│   │   ├── DbCustomer.php           ← Database Decorator
│   │   ├── DbCustomers.php          ← Database Decorator (Collection)
│   │   └── NotifiedCustomer.php     ← Email/Notification Decorator
│   ├── Exchange/                    ← External Communication Layer
│   │   ├── Resource/                ← REST API DTOs
│   │   │   ├── CarResource.php      ← DTO of Entity
│   │   │   ├── CarResources.php     ← Entry Point class
│   │   │   ├── CarRequest.php       ← Request DTO
│   │   │   └── CarResponse.php      ← Response DTO
│   │   ├── Storage/                 ← ORM at first level!
│   │   │   ├── CarEntity.php        ← Data Object / Entity
│   │   │   ├── CarDbContext.php     ← DB Connection Handler
│   │   │   └── ...
│   │   ├── Messaging/               ← AVRO Schema / Message Contracts
│   │   │   ├── CarRentEvent.php     ← DTO Object
│   │   │   └── ...
│   ├── User/                        ← Server side UI (Pages/Layouts)
│   │   ├── Control/                 ← UI specific controls
│   │   ├── Layout/                  ← UI Layout/Style components
│   │   ├── Page/                    ← UI pages
│   │   ├── DbUser.php               ← Db Decorator
│   │   └── WebUser.php              ← Session/Web Decorator
│   ├── CarNumber.php                ← Shared/Util Helper
│   ├── Car.php                      ← Domain Interface
│   ├── CarPool.php                  ← Collection Interface
│   ├── Customer.php                 ← Domain Interface
│   ├── Customers.php                ← Collection Interface
│   ├── User.php                     ← Domain Interface
│   ├── Users.php                    ← Collection Interface
│   ├── Payment.php                  ← Domain Interface
│   ├── CarRentalApp.php             ← Composition Root Interface
│   └── ...
```

---

## Root Level - Domain Interfaces

```php
<?php
// src/CarRental/CarRentalApp.php
namespace CarRental;

interface CarRentalApp
{
    public function carPool(): CarPool;
    public function customers(): Customers;
    public function payments(): Payments;
    public function users(): Users;
}

// src/CarRental/Car.php
namespace CarRental;

interface Car
{
    public function rent(Customer $customer, \DateTime $from, \DateTime $to): void;
    public function returnCar(): void;
    public function isAvailable(): bool;
    public function calculatePrice(\DateTime $from, \DateTime $to): float;
}
```

---

## Storage / ORM Layer (Within Exchange!)

```php
<?php
// src/CarRental/Exchange/Storage/CarEntity.php
namespace CarRental\Exchange\Storage;

class CarEntity
{
    public string $id;
    public string $model;
    public float $dailyRate;
    public bool $isRented;
    public ?string $currentCustomerId = null;
    public ?\DateTime $rentedFrom = null;
    public ?\DateTime $rentedTo = null;
}
```

---

## Car Implementation & Decorators

```php
<?php
// src/CarRental/CarPool/DbCar.php
namespace CarRental\CarPool;

use CarRental\Car;
use CarRental\Customer;
use CarRental\Exchange\Storage\CarDbContext;

final class DbCar implements Car
{
    public function __construct(
        private Car $origin,
        private CarDbContext $db,
        private string $carId
    ) {}

    public function rent(Customer $customer, \DateTime $from, \DateTime $to): void
    {
        $this->origin->rent($customer, $from, $to);
        
        $entity = $this->db->find($this->carId);
        if ($entity) {
            $entity->isRented = true;
            $entity->currentCustomerId = $customer->id();
            $this->db->save($entity);
        }
    }

    public function returnCar(): void 
    {
        $this->origin->returnCar();
        // Database logic here...
    }

    public function isAvailable(): bool 
    { 
        return $this->origin->isAvailable(); 
    }
    
    public function calculatePrice(\DateTime $from, \DateTime $to): float 
    { 
        return $this->origin->calculatePrice($from, $to); 
    }
}
```

---

## App Layer - Composition Root

```php
<?php
// src/CarRental/App/WebCarRentalApp.php
namespace CarRental\App;

use CarRental\CarRentalApp;
use CarRental\CarPool;
use CarRental\Customers;
use CarRental\CarPool\DbCarPool;
use CarRental\CarPool\CachedCarPool;
use CarRental\Exchange\Storage\CarDbContext;

class WebCarRentalApp implements CarRentalApp
{
    public function __construct(private $container) {}

    public function carPool(): CarPool
    {
        // Composition logic: CachedCarPool(DbCarPool(InMemoryCars))
        return new CachedCarPool(
            new DbCarPool(
                $this->container->get(CarDbContext::class)
            ),
            $this->container->get('cache_service')
        );
    }

    public function customers(): Customers
    {
        return $this->container->get(Customers::class);
    }
    
    public function payments(): Payments
    {
        return $this->container->get(Payments::class);
    }
    
    public function users(): Users
    {
        return $this->container->get(Users::class);
    }
}
```

---

## Summary of Rules

### Key Structure Rules

#### 1. Packages Should Never Depend on Sub-Packages

The Root package (`CarRental`) is the domain core and is independent. Sub-packages (like `CarPool/`) depend on the core.

#### 2. Sub-Packages Don't Introduce New Concepts

`CarPool/DbCar.php` is a detail of persistence. There are no business concepts in sub-packages that don't exist as interfaces in the root.

#### 3. Business Context over Technical Roles

Packages and classes reflect the business context (Nouns with prefixes) instead of technical layers (Service/Repository).

#### 4. Exchange Package for External Communication

All external interfaces (REST via `Resource/`, DB via `Storage/`, Kafka via `Messaging/`) are encapsulated in the `Exchange/` package.

---

### Object Naming Rules

#### ✅ Prefer:

- `InMemoryCar`, `DbCar`, `CachedCar` (Prefixes describe the WHAT)
- `Exchange/Storage/` for technical persistence (ORM Isolation)
- `CarRentalApp` interface in root, `WebCarRentalApp` in App package
- `Exchange/Resource/` for REST API DTOs

#### ❌ Avoid:

- `CarService`, `CarManager`, `CarRepository` (Technical suffixes/verbs)
- `CarDTO`, `CarModel` (Technical classification)
- `common/`, `shared/`, `util/` (Technical package names)

---

## Implementation Patterns

The architecture relies heavily on structural design patterns to decouple business logic from infrastructure concerns. By using the **Decorator Pattern**, we enhance core domain objects with technical capabilities (like persistence, caching, or logging) without polluting the original business logic. This results in highly cohesive and testable classes where each decorator has exactly one responsibility.

A central role is played by the **Composition Root** within the `App/` package. Instead of objects managing their own dependencies or using service locators, the Composition Root (e.g., `WebCarRentalApp`) assembles the complex object graphs. This allows for flexible configurations; for instance, a `CarPool` can be easily swapped from a database-backed implementation to a mock implementation for integration testing simply by changing the assembly logic in one central place. This pattern ensures that the domain logic remains "pure" and unaware of the framework or delivery mechanism being used.

---

## Evolution Path (PHP Implementation)

### 1. Monolithic (Phase 1): Package Structure

In this initial phase, the entire source code is located in a single application. Logical separation is done exclusively via namespaces within the `src/` directory.

```
src/CarRental/
├── App/          ← Framework/DI Integration
├── CarPool/      ← Business Logic (Nouns)
├── Customer/     ← Business Logic (Nouns)
├── Exchange/     ← External Communication Layer
├── Payment/      ← Business Logic (Nouns)
├── User/         ← UI Logic (Nouns)
└── ...
```

**PHP Realization:** A single `composer.json` file. Autoloading via `"CarRental\\": "src/CarRental/"`.

---

### 2. Modular Monolith (Phase 2): Deployable Artifacts

Specialization of packages into independent, versionable units. To ensure future scalability and team autonomy, each module encapsulates its own logic and its own Exchange (infrastructure) layer.

```
carrental-monolith/                 
├── carrental/                      ← Module: Shared Interfaces (Core contracts)
│   ├── Car.php
│   ├── CarPool.php
│   ├── Customer.php
│   ├── Payment.php
│   ├── CarRentalApp.php
│   └── ...
│
├── carrental-app/                  ← Module: Orchestrator/Composite Artifact
│   └── src/
│       ├── WebCarRentalApp.php
│       └── ...
│
├── carrental-carpool/              ← Module: Car logic
│   ├── src/
│   │   ├── CarPool/
│   │   │   ├── DbCar.php
│   │   │   ├── ValidCar.php
│   │   │   ├── CachedCar.php
│   │   │   ├── LoggedCar.php
│   │   │   ├── PublishedCar.php
│   │   │   ├── ReceivedCar.php
│   │   │   ├── DbCarPool.php
│   │   │   └── CachedCarPool.php
│   │   └── Exchange/
│   │       ├── Storage/ 
│   │       │   ├── CarEntity.php
│   │       │   ├── CarDbContext.php
│   │       │   └── CarRepository.php
│   │       ├── Resource/
│   │       │   ├── CarResource.php
│   │       │   ├── CarResources.php
│   │       │   ├── CarRequest.php
│   │       │   └── CarResponse.php
│   │       └── Messaging/
│   │           ├── CarRentedEvent.php
│   │           ├── CarReturnedEvent.php
│   │           └── KafkaCarProducer.php
│   └── composer.json
│
├── carrental-payment/              ← Module: Payment
│   ├── src/
│   │   ├── Payment/
│   │   │   ├── DbPayment.php
│   │   │   ├── ValidPayment.php
│   │   │   ├── LoggedPayment.php
│   │   │   ├── PayPalPayment.php
│   │   │   ├── StripePayment.php
│   │   │   └── DbPayments.php
│   │   └── Exchange/
│   │       ├── Storage/
│   │       │   ├── PaymentEntity.php
│   │       │   └── PaymentDbContext.php
│   │       ├── Gateway/
│   │       │   ├── StripeClient.php
│   │       │   ├── PayPalClient.php
│   │       │   └── PaymentGatewayAdapter.php
│   │       ├── Resource/
│   │       │   ├── PaymentResource.php
│   │       │   └── PaymentRequest.php
│   │       └── Messaging/
│   │           ├── PaymentConfirmedEvent.php
│   │           └── PaymentFailedEvent.php
│   └── composer.json
│
├── carrental-customer/             ← Module: Customer
│   ├── src/
│   │   ├── Customer/
│   │   │   ├── DbCustomer.php
│   │   │   ├── DbCustomers.php
│   │   │   ├── ValidCustomer.php
│   │   │   ├── NotifiedCustomer.php
│   │   │   └── CachedCustomer.php
│   │   └── Exchange/
│   │       ├── Storage/ 
│   │       │   ├── CustomerEntity.php
│   │       │   ├── CustomerDbContext.php
│   │       │   └── CustomerRepository.php
│   │       ├── Resource/
│   │       │   ├── CustomerResource.php
│   │       │   └── CustomerRequest.php
│   │       ├── Notification/
│   │       │   ├── MailchimpClient.php
│   │       │   ├── SendGridClient.php
│   │       │   └── EmailNotifier.php
│   │       └── Messaging/
│   │           ├── CustomerRegisteredEvent.php
│   │           └── CustomerUpdatedEvent.php
│   └── composer.json
│
├── carrental-user/                 ← Module: UI
│   ├── src/
│   │   ├── User/
│   │   │   ├── DbUser.php
│   │   │   ├── DbUsers.php
│   │   │   ├── WebUser.php
│   │   │   ├── ValidUser.php
│   │   │   └── AuthenticatedUser.php
│   │   ├── Exchange/
│   │   │   ├── Storage/ 
│   │   │   │   ├── UserEntity.php
│   │   │   │   ├── UserDbContext.php
│   │   │   │   └── SessionEntity.php
│   │   │   └── Resource/
│   │   │       ├── UserResource.php
│   │   │       └── LoginRequest.php
│   │   ├── Control/
│   │   │   ├── CarListControl.php
│   │   │   ├── RentalFormControl.php
│   │   │   └── PaymentControl.php
│   │   ├── Layout/
│   │   │   ├── MainLayout.php
│   │   │   ├── HeaderLayout.php
│   │   │   └── FooterLayout.php
│   │   └── Page/ 
│   │       ├── DashboardPage.php
│   │       ├── RentCarPage.php
│   │       ├── LoginPage.php
│   │       ├── ProfilePage.php
│   │       └── PaymentPage.php
│   └── composer.json
│
└── composer.json                   ← Bridges modules via path repositories
```

#### Module Responsibilities:

- **carrental**: The "Core" library. Contains only contracts and value objects. 0 dependencies.
- **carrental-carpool**: Implements `carrental`. It has its own private `Exchange/` package. This isolates its specific database schemas and API DTOs from the rest of the monolith.
- **carrental-payment**: Manages payment logic and its own `Exchange/Gateway` for external API interactions (Stripe/PayPal), preventing leaking vendor-specific details into other modules.
- **carrental-customer**: Uses its internal `Exchange/Storage` to manage customer data independently. It also has an `Exchange/Notification` layer to talk to email providers.
- **carrental-user**: Manages UI components, user authentication, and session handling with its own `Exchange/Storage` for user data.
- **carrental-app**: The entry point. Uses `composer.json` to link these packages using path repositories (symlinks) during development and tagged versions in production.

#### Teams:

Each module can be developed in a separate repository. Because each module has its own Exchange layer, a team can change their DB schema (e.g., from MySQL to MongoDB) by only modifying their own module's internal Exchange package without breaking any global core interfaces.

---

### 3. Microservices (Phase 3): Deployment Boundaries

Modules become independent services with hard physical boundaries. Each module is now deployed as an independent process with its own database.

```
carrental-infrastructure/
│
├── carrental-gateway/              ← API Gateway / BFF
│   ├── src/
│   │   ├── ApiGateway.php
│   │   ├── RouteRegistry.php
│   │   ├── AuthMiddleware.php
│   │   ├── CarPoolProxy.php
│   │   ├── CustomerProxy.php
│   │   ├── PaymentProxy.php
│   │   ├── UserProxy.php
│   │   ├── AggregatedRentalResource.php
│   │   └── GraphQLSchema.php
│   ├── docker-compose.yml
│   ├── Dockerfile
│   └── composer.json
│
├── carrental-ui/                   ← Decoupled Web App (Physical Artifact)
│   ├── src/
│   │   ├── Component/
│   │   │   ├── CarListComponent.tsx
│   │   │   ├── RentalFormComponent.tsx
│   │   │   ├── PaymentComponent.tsx
│   │   │   └── UserProfileComponent.tsx
│   │   ├── Page/
│   │   │   ├── DashboardPage.tsx
│   │   │   ├── RentCarPage.tsx
│   │   │   ├── LoginPage.tsx
│   │   │   └── ProfilePage.tsx
│   │   ├── Service/
│   │   │   ├── ApiClient.ts
│   │   │   ├── CarPoolService.ts
│   │   │   ├── CustomerService.ts
│   │   │   └── PaymentService.ts
│   │   └── Store/
│   │       ├── AppStore.ts
│   │       ├── CarStore.ts
│   │       └── UserStore.ts
│   ├── package.json
│   ├── Dockerfile
│   └── nginx.conf
│
├── carpool-service/                ← Physical Service (Car Module + DB)
│   ├── src/
│   │   ├── CarPool/
│   │   │   ├── DbCar.php
│   │   │   ├── ValidCar.php
│   │   │   ├── CachedCar.php
│   │   │   ├── LoggedCar.php
│   │   │   ├── PublishedCar.php
│   │   │   ├── ReceivedCar.php
│   │   │   ├── DbCarPool.php
│   │   │   ├── CachedCarPool.php
│   │   │   └── InMemoryCar.php
│   │   ├── Exchange/
│   │   │   ├── Storage/
│   │   │   │   ├── CarEntity.php
│   │   │   │   ├── CarDbContext.php
│   │   │   │   └── CarRepository.php
│   │   │   ├── Resource/
│   │   │   │   ├── CarResource.php
│   │   │   │   ├── CarResources.php
│   │   │   │   ├── CarRequest.php
│   │   │   │   ├── CarResponse.php
│   │   │   │   └── CarController.php
│   │   │   ├── Messaging/
│   │   │   │   ├── CarRentedEvent.php
│   │   │   │   ├── CarReturnedEvent.php
│   │   │   │   ├── CarAvailableEvent.php
│   │   │   │   ├── KafkaCarProducer.php
│   │   │   │   └── KafkaCarConsumer.php
│   │   │   └── Cache/
│   │   │       ├── RedisCarCache.php
│   │   │       └── CacheInvalidator.php
│   │   ├── App/
│   │   │   ├── CarPoolServiceApp.php
│   │       ├── Database/
│   │       │   ├── migrations/
│   │       │   └── seeds/
│   │       └── Config/
│   │           ├── database.php
│   │           └── kafka.php
│   ├── docker-compose.yml
│   ├── Dockerfile
│   ├── composer.json
│   └── .env
│
├── customer-service/               ← Physical Service (Customer Module + DB)
│   ├── src/
│   │   ├── Customer/
│   │   │   ├── DbCustomer.php
│   │   │   ├── DbCustomers.php
│   │   │   ├── ValidCustomer.php
│   │   │   ├── NotifiedCustomer.php
│   │   │   ├── CachedCustomer.php
│   │   │   └── InMemoryCustomer.php
│   │   ├── Exchange/
│   │   │   ├── Storage/
│   │   │   │   ├── CustomerEntity.php
│   │   │   │   ├── CustomerDbContext.php
│   │   │   │   ├── CustomerRepository.php
│   │   │   │   └── RentalHistoryEntity.php
│   │   │   ├── Resource/
│   │   │   │   ├── CustomerResource.php
│   │   │   │   ├── CustomerRequest.php
│   │   │   │   ├── CustomerResponse.php
│   │   │   │   └── CustomerController.php
│   │   │   ├── Notification/
│   │   │   │   ├── MailchimpClient.php
│   │   │   │   ├── SendGridClient.php
│   │   │   │   ├── EmailNotifier.php
│   │   │   │   └── NotificationTemplate.php
│   │   │   ├── Messaging/
│   │   │   │   ├── CustomerRegisteredEvent.php
│   │   │   │   ├── CustomerUpdatedEvent.php
│   │   │   │   ├── RentalCompletedEvent.php
│   │   │   │   ├── KafkaCustomerProducer.php
│   │   │   │   └── KafkaCustomerConsumer.php
│   │   │   └── Cache/
│   │   │       └── RedisCustomerCache.php
│   │   ├── App/
│   │   │   └── CustomerServiceApp.php
│   │   └── Infrastructure/
│   │       ├── Database/
│   │       │   ├── migrations/
│   │       │   └── seeds/
│   │       └── Config/
│   │           ├── database.php
│   │           └── notification.php
│   ├── docker-compose.yml
│   ├── Dockerfile
│   ├── composer.json
│   └── .env
