# **Self-Explaining Software Structure: Code That Tells a Story**

The best architecture is the one that requires little to no explanation. When developers open a new project and immediately see folders like `components/`, `controllers/`, or `services/`, they learn a lot about the framework being used, but absolutely nothing about the customer's core business.

The following philosophy breaks away from this framework-centric mindset. It radically shifts focus toward a domain- and customer-oriented structure where the folder hierarchy itself tells the **Customer Story**.

## **The Foundation: The Pristine "Level 0"**

The core of this approach is **Level 0**—the top-level hierarchy of the project directory. Here, all classes and interfaces representing the fundamental business concepts of the application reside. Level 0 remains strictly free from technical baggage and framework code.

* **Focus on Core Entities:** In a movie streaming application (*Movie App*), Level 0 contains exclusively domain-level files such as `Movie.ts`, `Cast.ts`, or `Comment.ts`.  
* **Value Objects Over Technical Helpers:** Suffixes like `PaymentUtil.ts` or `MovieHelper.ts` are completely banned. Logic and data belong together. Instead of placing loose formatting functions inside a utility class, a dedicated Value Object such as `MovieDuration` or `PaymentNumber` is created, encapsulating its own validations and methods.  
* **Resolving Circular Dependencies Through Business Concepts:** When two packages (e.g., `payment` and `order`) depend circularly on each other, it signals an undiscovered business concept. The solution is not a generic `common/` folder, but the evolution of a new domain-specific package like `checkout/`.

## **The 3 Golden Rules of Readability**

To elevate code to the next level of clarity, three fundamental structural rules apply:

1. **Packages Must Never Depend on Sub-Packages:** The dependency flow is a strict one-way top-down street. A parent package orchestrates, but remains unaware of how its sub-package details are implemented.  
2. **Sub-Packages Introduce No New Concepts:** They do not bring new business domain logic into play; they merely provide further details and technical implementations for concepts already established on Level 0.  
3. **Packages Must Reflect Business Concepts, Not Technical Ones:** Framework terminology is eliminated. For example, the user interface is organized inside a package named `user/`. The UI exists solely because a human (*User*) interacts with the system—it is an interaction concept, not a purely technical routing detail.

## **Handling Cross-Cutting Concerns: The exchange/ Package**

Requirements like authentication, logging, and caching touch almost every part of an application. However, placing them naively on Level 0 destroys the purity of the business story. The solution lies in a strict separation through the Decorator pattern and the core entry mechanism of the system: the `exchange/` package in combination with the **Composition Root**.

The word exchange describes the purely domain-level concept of interacting with the outside world (databases, APIs, third-party services). This interaction works in three steps:

* **The Contract on Level 0:** A purely domain-level contract is defined, such as MoviePool.ts (a much more business-focused term than the technical word *Repository*).  
* **The Channels in exchange/:** Inside exchange/ sit isolated sub-packages. The `database/` sub-package houses MoviePoolDb.ts (for SQL/NoSQL). The `provider/` sub-package houses `MoviePoolApi.ts` (for HTTP/REST exchange).  
* **The Invisible Aspect (Decorator):** Caching does not alter business logic. Therefore, a `CachedMoviePoolApi.ts` is implemented as a Decorator wrapping the concrete API class. It intercepts requests, serves cached data, or delegates to the provider. Level 0 remains entirely unaware.

### **Cross-Cutting Concerns in Domain Sub-Packages (movie/, user/)**

Cross-cutting topics also appear within specific domain sub-packages—for instance, age restriction checks in `movie/` or feature toggles and visibility rules in `user/`. The core principle remains: **decoupling through domain rules and interceptors.**

#### **1. In Domain Sub-Packages (e.g., movie/)**

* **Domain Decorating:** If access to movies needs to be audited or restricted (e.g., parental controls), this logic is not cluttered with nested if `(user.age < 18)` conditions deep inside the entity. Instead, the movie/ package provides a domain decorator like `AgeRestrictedMoviePool.ts` that implements `MoviePool` and encapsulates the check.  
* **Domain Events Over Direct Cross-Calls:** When playing a movie triggers telemetric logging or a recommendation engine, `movie/` does not call analytics services directly. It simply emits an event (`MoviePlaybackStartedEvent.ts`). Everything else is processed asynchronously in the background.

#### **2. In Interaction & UI Sub-Packages (e.g., user/)**

* **UI Decorators & Higher-Order Components:** Requirements like *"Show this section only to admins"* or *"Hide this feature via feature toggle"* do not belong in the presentation component itself (e.g., `user/playback/PlayerView.ts`). The UI component remains purely visual.  
* **Visibility Wrappers:** Checks are performed via UI decorators or wrapper components (e.g., `WithFeatureToggle` or `WithPermission`), wrapped around `user/` components by the Composition Root or router layer.

## **Event-Driven Architecture & Domain Events**

Asynchronous processes and reactive workflows fit seamlessly into this architecture when responsibilities are cleanly decoupled:

1. **Domain Events on Level 0:** Domain events (e.g., `MovieRentedEvent.ts` or `UserRegisteredEvent.ts`) are pure data structures and contracts defined on Level 0. They describe *what happened* in the business domain.  
2. **Event Delivery in `exchange/`:** The technical messaging infrastructure (e.g., RabbitMQ, Apache Kafka, AWS EventBridge) is isolated as an adapter inside `exchange/messaging/`.

Level 0 simply emits the domain event—how and where it is published is managed by the Composition Root using components from exchange/events/.

## **The Brain of the Application: The Composition Root as Story Orchestrator**

If the entire application consists of highly isolated domain building blocks and technical exchange channels (exchange/), what ties them all together? What turns these separate parts into a living application?

This is where the **Composition Root** (often implemented in MovieApp.ts on Level 0) comes into play. It is far more than a simple startup script; it is the director and orchestrator of the Customer Story.

Instead of business classes instantiating their own databases or APIs via direct import or new calls (which would destroy independence), the Composition Root holds the exclusive responsibility to assemble the system at runtime (*Dependency Injection*).

### **How the Composition Root Brings the Story to Life:**

1. **Technical Creation:** At startup, the Composition Root reaches to the outer edges of the application, instantiating raw exchange channels under exchange/ (e.g., the HTTP client `MoviePoolApi`).  
2. **Functional Refinement (Decoration):** It enriches these channels with implicit business requirements. It takes `MoviePoolApi`, wraps it inside the `CachedMoviePoolApi` decorator, and adds security checks from `exchange/auth/`. The beauty: neither the API nor the core app is aware of this layering.  
3. **Delivering the Story:** Finally, it passes this optimized, secure construct to the Level 0 core classes (such as Movie.ts or workflow drivers in application/).

Through this orchestration, the core business logic remains entirely untouched by details of data sourcing or caching. The Composition Root reads like a blueprint of the Customer Story:

*"Take the movie pool, secure it with authentication, accelerate it with a cache, and present it to the user in the catalog."*

## **The Schema for a Structure**

Applying these principles consistently yields a clean, self-explaining project structure:

```
├── [movie-app]/  
│   ├── application/             # Core orchestrator of the Customer Story  
│   │   └── NodeJsMovieApp.ts    # Implementation & Composition Root   
│   │  
│   ├── exchange/                # Outer exchange with resources & third parties  
│   │   ├── auth/                # Sub-package: Authentication (e.g., JwtAuthApi.ts)  
│   │   ├── database/            # Sub-package: Direct read/write DB operations  
│   │   │   ├── PersonDb.ts      # Database operations for people/cast  
│   │   │   └── MoviePoolDb.ts   # Database operations for movies  
│   │   ├── messaging/           # Sub-package: Event infrastructure (RabbitMQ / Kafka)  
│   │   │   └── KafkaQueue.ts    # Message Broker / Listener & Publisher  
│   │   ├── paypal/              # Sub-package: Payment provider integration  
│   │   └── provider/            # Sub-package: Data exchange with external movie APIs  
│   │       ├── CachedMoviePoolApi.ts # Decorator: Serves cached movie data  
│   │       └── MoviePoolApi.ts   # Fetches raw movie data via REST/HTTP  
│   ├── control/                 → UI elements / generic controls  
│   │   ├── InputGroup.ts        → Composite container  
│   │   ├── Dashboard.ts         → Composite container  
│   │   ├── Table.ts             → Composite container  
│   │   └── TextInput.ts         → Text input component  
│   │  
│   ├── layout/                  → Layouts, CSS & graphics  
│   │   ├── icon/                → Application icons  
│   │   ├── image/               → Application graphics  
│   │   └── layout.css           → Global layout styles  
│   │  
│   ├── page/                    → Page views & segments  
│   │   ├── AuthPage.js          → Authentication page  
│   │   ├── MovieDashboard.js    → Movie dashboard view  
│   │   └── PageNavigation.js    → Router implementation  
│   │  
│   ├── movie/                   # Sub-package: Business concept details for movies  
│   │   ├── LocalMoviePool.ts    # Implements MoviePool using MoviePoolDb for local data  
│   │   ├── RemoteMoviePool.ts   # Implements MoviePool using MoviePoolApi for remote data  
│   │   └── AgeRestrictedMoviePool.ts # Domain decorator: Age restriction rules  
│   │  
│   ├── user/                    # Interaction package for humans (UI code)  
│   │   ├── playback/            # Business detail: Player view  
│   │   └── catalog/             # Business detail: Browsing view  
│   │  
│   /* --- LEVEL 0: Pure Business Concepts & Interfaces --- */  
│   ├── Cast.ts                  # Domain data structure for cast members  
│   ├── Comment.ts               # Domain data structure for user comments  
│   ├── Movie.ts                 # Core entity (The Movie object itself)  
│   ├── MovieDuration.ts         # Value Object representing movie length  
│   ├── MovieRentedEvent.ts      # Domain Event representing movie rental  
│   ├── MoviePool.ts             # Domain contract (Interface) for the movie dataset  
│   └── MovieApp.ts              # Application entry contract & Composition Root
```

## **Conclusion**

This architecture degrades frameworks, libraries, and communication protocols to interchangeable details at the boundaries of the system. Switching from a REST API to GraphQL requires changes exclusively inside the `exchange/` directory. Because the Composition Root orchestrates all dependencies, the application core—the Customer Story—remains completely shielded from technical noise and reads like a book.

## **Exemplary Implementation: application/NodeJsMovieApp.ts**

To illustrate how the Composition Root functions as a story orchestrator in practice, the following TypeScript example demonstrates concrete runtime wiring inside `application/`:

```ts
// application/NodeJsMovieApp.ts  
import { MovieApp } from '../MovieApp';  
import { MoviePool } from '../MoviePool';

// Exchange channels from exchange/  
import { MoviePoolApi } from '../exchange/provider/MoviePoolApi';  
import { CachedMoviePoolApi } from '../exchange/provider/CachedMoviePoolApi';  
import { LoggingMoviePool } from '../exchange/logging/LoggingMoviePool';  
import { JwtAuthApi } from '../exchange/auth/JwtAuthApi';

// Concrete business implementation details from movie/  
import { RemoteMoviePool } from '../movie/RemoteMoviePool';  
import { AgeRestrictedMoviePool } from '../movie/AgeRestrictedMoviePool';

/**  
 * NodeJsMovieApp is the concrete Composition Root for a Node.js runtime environment.  
 * It implements the foundational Level 0 MovieApp interface.  
 */  
export class NodeJsMovieApp implements MovieApp {  
  private moviePool: MoviePool;

  constructor() {  
    // 1. Instantiate raw exchange channels (Infrastructure / Exchange)  
    const rawApi = new MoviePoolApi(process.env.MOVIE_API_URL || 'https://api.movies.com');  
    const authService = new JwtAuthApi(process.env.JWT_SECRET || 'super-secret-key');

    // 2. Enhance behavior using the Decorator Pattern (Cross-Cutting Concerns)  
      
    // a) Wrap API with caching  
    const cachedApi = new CachedMoviePoolApi(rawApi);

    // b) Connect business detail (RemoteMoviePool implements Level 0 'MoviePool')  
    const remotePool = new RemoteMoviePool(cachedApi, authService);

    // c) Apply domain age restriction decorator from movie/  
    const ageRestrictedPool = new AgeRestrictedMoviePool(remotePool);

    // d) Apply logging & observability decorator wrapping the MoviePool interface  
    this.moviePool = new LoggingMoviePool(ageRestrictedPool);  
  }

  /**  
   * Starts the application and executes the Customer Story setup.  
   */  
  public async start(): Promise<void> {  
    console.log('🚀 MovieApp (Node.js Environment) started.');  
    console.log('Orchestrating Customer Story...');

    // Consume pure Level 0 core contract  
    const movies = await this.moviePool.getFeaturedMovies();  
    console.log(`[Story Log]: Successfully loaded ${movies.length} featured movies.`);  
  }

  /**  
   * Exposes the fully decorated MoviePool instance to other application parts.  
   */  
  public getMoviePool(): MoviePool {  
    return this.moviePool;  
  }  
}

// Direct entry point execution  
if (require.main === module) {  
  const app = new NodeJsMovieApp();  
  app.start().catch((error) => {  
    console.error('Critical failure during MovieApp startup:', error);  
    process.exit(1);  
  });  
}
```

### **Composition Root Key Takeaways**

1. **Pure Decoration:** Neither the domain core (MoviePool.ts) nor the API implementation (`MoviePoolApi.ts`) is aware of caching, auth tokens, or logging details.  
2. **Exclusive Control:** `NodeJsMovieApp` alone decides which concrete implementations are assembled at runtime.  
3. **Effortless Interchangeability:** Replacing `Node.js` with a serverless environment (e.g., `AwsLambdaMovieApp.ts`) requires zero changes to core domain logic.

### **Code Comparison: Util Class vs. Value Object**

The advantage of Level 0 Value Objects becomes immediately apparent when comparing traditional procedural code with domain-driven modeling:

```ts
// ❌ Before (Anti-Pattern): Procedural utility class with decoupled data structure  
class MovieUtil {  
  public static formatDuration(minutes: number): string {  
    if (minutes < 0) throw new Error("Invalid duration");  
    const h = Math.floor(minutes / 60);  
    const m = minutes % 60;  
    return `${h}h ${m}m`;  
  }  
}
```

```ts
// Usage scattered across codebase:  
const formatted = MovieUtil.formatDuration(movie.durationInMinutes);

// ✅ After (Level 0 Value Object): Self-validating, immutable unit  
export class MovieDuration {  
  private readonly minutes: number;

  constructor(minutes: number) {  
    if (minutes < 0) {  
      throw new InvalidMovieDurationException(minutes);  
    }  
    this.minutes = minutes;  
  }

  public toFormattedString(): string {  
    const h = Math.floor(this.minutes / 60);  
    const m = this.minutes % 60;  
    return `${h}h ${m}m`;  
  }  
}

// Usage: Domain entity directly holds the Value Object  
const duration = new MovieDuration(135);  
console.log(duration.toFormattedString()); // "2h 15m"
```
### **Error Handling: From Technical Exceptions to Domain Errors**

In many architectures, technical details like Http500Exception, SqlTimeoutError, or AxiosError leak into the core business logic.

The exchange/ package serves as an **Error Translator**: it catches infrastructure errors and translates them into precise Level 0 Domain Exceptions.

```ts
// exchange/provider/MoviePoolApi.ts  
try {  
  const response = await this.httpClient.get('/movies');  
  return response.data;  
} catch (error) {  
  if (error.response?.status === 503) {  
    // Translating a technical error into a domain exception!  
    throw new MovieProviderUnavailableException('Movie data provider is temporarily unreachable.');  
  }  
  throw new TechnicalExchangeException(error.message);  
}
```

### **Testing Strategy: How to Test a Story Architecture**

A clear directory structure unlocks its full potential when writing automated tests. Separating Level 0 from infrastructure yields a highly maintainable testing pyramid:
```
          /   
         / E2E        <- Composition Root (Acceptance Tests)  
        /-------  
       / Integr.      <- exchange/ (DB & Mock Server Tests)  
      /-----------  
     /  Unit Tests    <- Level 0 (100% Isolated Business Logic)  
    -----------------
```

* **Level 0 (Domain Core):** Pure unit tests without mocks, stubs, or in-memory databases. Because Level 0 is framework-free, thousands of assertions run in milliseconds.  
* **`exchange/` (Infrastructure):** Targeted integration tests against real databases (e.g., via Testcontainers) or HTTP mock servers (e.g., `WireMock`) to validate contracts and data mapping.  
* **Composition Root (`application/`):** End-to-end or acceptance tests that instantiate NodeJsMovieApp and execute complete business use cases.
