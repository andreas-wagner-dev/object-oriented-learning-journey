# Rethinking Vertical Slice: Architektur neu gedacht

Die von Jimmy Bogard populär gemachte Vertical Slice Architecture (VSA) gilt als konsequente Evolution der Clean Architecture. Während die Clean Architecture Abhängigkeiten über konzentrische Ringe hinweg nach innen auf eine isolierte Domäne ausrichtet, schneidet VSA diese horizontalen Strukturen stattdessen vertikal nach Features. Jedes Feature kontrolliert somit seine Logik, Validierung und Datenquelle selbst. Das Ziel ist eine minimale Kopplung zwischen den sogenannten Slices bei maximaler Kohäsion im Inneren.

Obwohl diese Struktur als sinnvolle Verfeinerung die Kopplung effektiv minimiert, hält sie weiterhin an einer rein technischen Denkweise fest. Damit verfehlt sie das fundamentale Ziel einer konsequent fachlichen Ausrichtung. Im Projektalltag kann die Flut an unterschiedlichen Slices das Chaos und die Unverständlichkeit sogar noch verschlimmern. Denn das Business denkt in Substantiven (Kunde, Auto, Buchung) und Verben (registrieren, warten, bezahlen) – niemals in technischen Werkzeugen wie Handlern, Controllern oder DTOs. So bleiben die altbekannten Probleme der Clean Architecture bestehen; sie werden lediglich in den einzelnen Slice verlagert. 

Anstatt die technischen Schichten wirklich aufzulösen, baut die klassische VSA-Praxis in jedem einzelnen Feature-Ordner eine isolierte Mini-Schichtenarchitektur aus CQRS- und MediatR-Mustern auf. Das technische Denken dominiert weiterhin die Struktur – und das, obwohl strategische DDD-Konzepte wie die „Allgegenwärtige Sprache“ oder das Ideal der Screaming Architecture genau das Gegenteil fordern.


## Das Autovermietungs-System

Um das Problem dieser technischen Fehlausrichtung in der klassischen VSA greifbar zu machen, nutzen wir ein klassisches Autovermietungs-System (Car Rental). Das System interagiert mit verschiedenen externen Systemen, Zahlungsanbietern und Benutzeroberflächen:

```
                       User
                    (Booking)
                        │
                        ▼
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
 │ Payment │    │                 │    │  Customer   │
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │
 │/Stripe  │    │  (Application)  │    │             │
 └─────────┘    └─────────────────┘    └─────────────┘
                        │
                        ▼
                ┌───────────────┐
                │               │
                │   Car Pool    │
                │  (Inventory)  │
                └───────────────┘
```

### Die klassische Vertical Slice Struktur

Bei der traditionellen VSA-Praxis gruppiert ein allumfassender Ordner namens Features/ sämtliche Geschäftsfälle. Technische Ordner wie Infrastruktur/ und Shared/ existieren daneben als globale, querschneidende Schichten.Wirft man einen Blick auf die oberste Verzeichnisebene eines solchen Projekts, bricht das Versprechen einer fachlich orientierten Architektur sofort in sich zusammen:

```
src/
├─ Features/
├─ Infrastruktur/
└─ Shared/
```

Wenn ein neuer Entwickler diese Struktur öffnet, stellt er sich unweigerlich die Fragen: 
Wo ist das Auto (Car)? Wo ist der Kunde (Customer)? Wo ist die Zahlung (Payment)? Wo ist die Buchung (Booking) aus unserem Kontext-Diagramm abgeblieben?

Nichts davon ist sichtbar. Die von Eric Evans im Domain-Driven Design (DDD) geforderte „Allgegenwärtige Sprache“ (Ubiquitous Language) existiert auf der Makro-Ebene schlichtweg nicht. Anstatt die echten fachlichen Säulen des Unternehmens abzubilden, ist das System nach rein technischen Aspekten organisiert.

Die Kernarchitektur „schreit“ nicht nach Autovermietung, sondern nach Software-Infrastruktur. Sie verfehlt das Ideal der Screaming Architecture auf der Root-Ebene komplett. Diese technische Schubladen-Sortierung zwingt den Betrachter zu einer mentalen Dauer-Übersetzung: Um zu verstehen, was das System geschäftlich überhaupt tut, muss man erst tiefer in den Features/-Ordner abtauchen.

## Anatomie der klassischen Struktur: Drei ungleiche Säulen

In der Praxis sieht die vollständige, klassische Vertical Slice Struktur für unser System meistens so aus:

```
src/
├─ Features/                          # VERTICAL SLICES (Nach Use Cases)
│   ├─ Customer/                      # Kontext: Kundenstamm
│   │   └─ RegisterCustomer/          # Slice: Registrierung
│   │       ├─ RegisterCustomerController.cs
│   │       ├─ RegisterCustomerCommand.cs
│   │       ├─ RegisterCustomerHandler.cs
│   │       ├─ RegisterCustomerValidator.cs
│   │       └─ CustomerResponseDto.cs
│   │
│   ├─ Booking/                       # Kontext: Reservierungen
│   │   ├─ CreateBooking/             # Slice: Fahrzeug buchen
│   │   │   ├─ CreateBookingController.cs
│   │   │   ├─ CreateBookingCommand.cs
│   │   │   ├─ CreateBookingHandler.cs
│   │   │   └─ BookingDto.cs
│   │   │
│   │   └─ CancelBooking/             # Slice: Stornierung
│   │       ├─ CancelBookingController.cs
│   │       ├─ CancelBookingCommand.cs
│   │       └─ CancelBookingHandler.cs
│   │
│   ├─ Payment/                       # Kontext: Zahlungen
│   │   └─ ProcessPayment/            # Slice: Zahlung abwickeln
│   │       ├─ ProcessPaymentController.cs
│   │       ├─ ProcessPaymentCommand.cs
│   │       ├─ ProcessPaymentHandler.cs
│   │       ├─ PayPalClient.cs        # Technische API-Anbindung direkt im Slice
│   │       └─ StripeClient.cs        # Technische API-Anbindung direkt im Slice
│   │
│   └─ CarPool/                       # Kontext: Fuhrpark
│       ├─ SearchAvailableCars/       # Slice: Fahrzeugsuche
│       │   ├─ SearchCarsController.cs
│       │   ├─ CarQuery.cs
│       │   └─ CarQueryResultDto.cs
│       │
│       └─ MaintainCar/               # Slice: Werkstatt-Steuerung
│           ├─ MaintainCarController.cs
│           ├─ MoveToMaintenanceCommand.cs
│           └─ MoveToMaintenanceHandler.cs
│
├─ Infrastruktur/                     # TECHNISCHE SCHICHT (Datenbank & Host)
│   ├─ Data/
│   │   ├─ CarRentalDbContext.cs      # Gemeinsamer, fetter DbContext
│   │   ├─ CustomerConfiguration.cs   # EF Core Mapping
│   │   ├─ BookingConfiguration.cs    # EF Core Mapping
│   │   └─ CarConfiguration.cs        # EF Core Mapping
│   ├─ Messaging/
│   │   └─ EventBus.cs                # Nachrichten-Infrastruktur (RabbitMQ/ServiceBus)
│   └─ Migrations/                    # EF Core Migrations-Dateien
│
└─ Shared/                            # GEMEINSAME KOMPONENTEN (Querschnitt)
    ├─ Domain/
    │   ├─ CustomerEntity.cs          # Anämisches Datenmodell (von allen Slices genutzt)
    │   ├─ BookingEntity.cs           # Anämisches Datenmodell (von allen Slices genutzt)
    │   └─ CarEntity.cs               # Anämisches Datenmodell (von allen Slices genutzt)
    ├─ Exceptions/
    │   └─ DomainException.cs         # Globale Fehlerklassen
    └─ Kernel/
        └─ BaseEntity.cs              # Gemeinsame ID- und Audit-Logik

```

Beim tieferen Eintauchen in diese Verzeichnisstruktur wird das eigentliche Kernproblem – die dezentralisierte Verlagerung der technischen Denkmuster – im Detail sichtbar:

1. Das technische Pattern-Rauschen im Slice

Erinnern wir uns daran, dass das Business in Substantiven und Verben denkt. Ein Blick in den Slice RegisterCustomer/ offenbart jedoch das genaue Gegenteil. Die Struktur wird von rein technischen CQRS- und MediatR-Mustern überschwemmt. Statt der fachlichen Logik des Verbs „registrieren“ springen dem Entwickler sofort Begriffe wie Command, Handler, Validator und Dto ins Auge.

Das Business wird visuell von der Technologie erdrückt. Anstatt die Schichten der Clean Architecture aufzulösen, wurde hier lediglich eine isolierte Mini-Schichtenarchitektur innerhalb des Ordners aufgebaut. Zudem bricht die Kapselung bei externen Integrationen auf: Technische HTTP-Clients wie PayPalClient.cs werden pragmatisch tief im einzelnen Feature-Slice vergraben, anstatt sie sauber vom Kern zu isolieren.

2. Der "fette" Infrastruktur-Monolith

Im Infrastruktur/-Ordner landen alle technischen Querschnittsbelange, die das System am Laufen halten. Hier lebt der CarRentalDbContext – ein typischer, „fetter“ Entity Framework Kontext, der die Tabellen aller Domänen (Kunden, Autos, Zahlungen) in einer einzigen Klasse vereint. Obwohl wir vorgespielt haben, die Anwendung in vertikale Features zu schneiden, kettet dieser zentrale DB-Kontext alle Slices auf Datenbankebene wieder untrennbar aneinander.


3. Die Auszehrung der Domäne im Shared-Ordner

Alles, was von mehr als einem Feature benötigt wird, wandert unweigerlich in den globalen Shared/-Bereich. Das betrifft vor allem die Domänen-Klassen wie CustomerEntity.cs oder CarEntity.cs – also die wichtigsten Substantive unseres Business. Da die Slices selbst keine eigenen Datenmodelle besitzen dürfen (um Duplikate zu vermeiden), greifen alle Slices quer durch das Projekt auf diese zentralen Entitäten zu. Meistens verkümmern diese Klassen dadurch zu reinen, anämischen Datenbehältern ohne echte Geschäftslogik, während die eigentlichen Fachregeln über Dutzende Handler-Dateien verstreut werden.

## Was Entwickler wirklich sehen wollen

Wenn ein Softwareentwickler oder Architekt ein neues Projekt öffnet, möchte er nicht raten müssen. Er möchte, dass die Codebasis sofort zu ihm spricht. Onboarding, Wartung und das Finden von Fehlern werden massiv beschleunigt, wenn das Verzeichnis die reale Geschäftswelt widerspiegelt. Ein Entwickler wünscht sich beim Blick auf die Root-Ebene drei Kernaspekte:

1. Sofortige fachliche Orientierung

Die Ordnerstruktur sollte das System-Kontext-Diagramm 1:1 abbilden. Wenn das Business aus Buchungen, Kunden und Zahlungen besteht, müssen genau diese Substantive die oberste Ebene dominieren. Der Entwickler will sofort sehen: „Ah, das ist eine Autovermietung.“

2. Funktionale Navigation ohne Such-Odyssee

Wenn ein Fehler im PayPal-Prozess auftritt, will der Entwickler nicht erst überlegen müssen, welches globale Shared-Modul oder welche Querverlinkung im Features-Ordner dafür zuständig ist. Die Struktur muss intuitiv sein: Payment öffnen → PayPal öffnen → Fehler beheben. Alles, was fachlich zu PayPal gehört, muss genau dort gekapselt sein.

3. Technische Details im HintergrundFrameworks, Controller, HTTP-Schnittstellen und Datenbank-Treiber sind austauschbare Werkzeuge. Sie sind Mittel zum Zweck, nicht der Zweck selbst. Ein Entwickler möchte, dass diese technischen Details visuell in den Hintergrund treten. Sie gehören als Implementierungsdetails in das jeweilige Fachgebiet, anstatt das Projekt von außen zu umklammern.

Kurz gesagt: Im Sinne der "Screeming Architektur" muss ein Codebasis nach dem Business schreien, nicht nach der eingesetzten Mustern oder  Technologie.

## Die Brücke zur Realität: Vom Wunschbild zur konkreten Struktur

Um dieses fachliche Wunschbild in die Realität umzusetzen, müssen wir die gewohnten Trampelpfade der klassischen Vertical Slices verlassen. Wir dürfen Slices nicht mehr als flache, isolierte Datei-Schubladen betrachten, sondern als lebendige, hierarchische Domänen-Module.

Hierfür wenden wir drei eiserne Paketierungsregeln konsequent an:

* **Regel 1: Keine Aufwärts-Abhängigkeiten**  
Klassen in übergeordneten Paketen dürfen niemals von Klassen in Unterpaketen abhängen. Die Abhängigkeitsrichtung fließt immer nur von außen nach innen, um den Kern stabil zu halten.
* **Regel 2: Unterpakete als Detail-Verfeinerung**  
Klassen in Unterpaketen sollten keine komplett neuen Konzepte einführen. Sie liefern lediglich weitere Details zu den Konzepten der übergeordneten Pakete (z. B. eine spezifische PayPal-Implementierung unterhalb des allgemeinen Payment-Moduls).
* **Regel 3: Fachlichkeit vor Technik**  
Klassen und Pakete spiegeln ausschließlich fachliche Konzepte wider – also die Substantive und Verben des Business. Technische Muster (Handler, Validator, Configuration) ordnen sich diesen Strukturen unter und treten visuell in den Hintergrund.

Wenn wir diese drei Regeln konsequent anwenden, verschwinden die künstlichen Mauern zwischen Features, Infrastruktur und Shared. Sie verschmelzen zu einer Einheit, die sich exakt mit unserem System-Kontext-Diagramm deckt.

Schauen wir uns nun an, wie radikal sich das Gesicht unseres Projekts verändert, wenn wir die technische Brille absetzen und die fachliche Struktur sprechen lassen:

```
src/
├─ CarRental.Application/             # ➡️ APPLICATION (Composition Root)
│   ├─ CarRentalApp.cs
│   ├─ CarRentalDI.cs
│   ├─ CarRentalSettings.json
│   └─ CarRentalQueueConfig.cs
│
├─ CarRental.Booking/                 # ➡️ USER (Booking)
│   ├─ Reservation/
│   │   ├─ ServedBooking.cs
│   │   ├─ BookingRequest.cs
│   │   ├─ BookingResponse.cs
│   │   ├─ PersistentReservation.cs
│   │   └─ PersistentReservations.cs
│   ├─ Cancellation/
│   │   └─ ServedCancellation.cs
│   ├─ IBookingSession.cs
│   ├─ IReservation.cs
│   └─ IReservations.cs
│
├─ CarRental.CarPool/                 # ➡️ CAR POOL (Inventory)
│   ├─ CarFleet/
│   │   ├─ ServedCarPool.cs
│   │   ├─ CarRequest.cs
│   │   └─ CarResponse.cs
│   ├─ Maintenance/
│   │   ├─ LoggedCar.cs
│   │   ├─ ValidCar.cs
│   │   ├─ PublishedCar.cs
│   │   ├─ ReceivedCar.cs
│   │   └─ CarInMaintenance.cs
│   ├─ ICar.cs
│   ├─ ICarPool.cs
│   ├─ Car.cs
│   ├─ CarStatus.cs
│   ├─ PersistentCar.cs               # Im Root (von Subpaketen genutzt)
│   ├─ PersistentCarPool.cs           # Im Root (von Subpaketen genutzt)
│   ├─ CachedCar.cs                   # Im Root (von Subpaketen genutzt)
│   └─ CachedCarPool.cs               # Im Root (von Subpaketen genutzt)
│
├─ CarRental.Customer/                # ➡️ CUSTOMER (Database)
│   ├─ Registration/                  # 🛠️ Unterpaket: Nur noch Logik & API
│   │  ├─ ServedRegistration.cs
│   │  └─ NotifiedCustomer.cs         # Exklusiver Decorator für Neukunden
│   ├─ ICustomer.cs                   # Abstraktion im Root
│   ├─ ICustomers.cs                  # Abstraktion im Root
│   ├─ Customer.cs                    # Domänen-Basisklasse im Root
│   ├─ PersistentCustomer.cs          # 🔄 IM ROOT (für alle Subpakete)
│   └─ PersistentCustomers.cs         # 🔄 IM ROOT (für alle Subpakete)
│
└─ CarRental.Payment/                # ➡️ PAYMENT (Zahlungsabwicklung)
    ├─ PayPal/
    │   ├─ PayPal.cs
    │   ├─ PayPalRequest.cs
    │   ├─ PayPalResponse.cs
    │   └─ PayPalPayment.cs
    ├─ Stripe/
    │   ├─ Stripe.cs
    │   └─ StripePayment.cs
    ├─ IPayment.cs
    └─ Transaction.cs

```

## Die Anatomie der neuen Struktur: Echte Kapselung im Detail

Der Blick auf das neue Verzeichnis zeigt: Das technische Rauschen ist komplett von der Oberfläche verschwunden. Jedes Domänen-Modul agiert nun als autonome, fachliche Einheit, die ihre eigenen Datenmodelle, Validierungen und Infrastruktur-Details streng intern verwaltet.

Um zu verstehen, warum dieses Design so stabil ist, betrachten wir die Implementierung der Kern-Konzepte:

### 1. Das Customer-Modul: Datenmodelle im Modul-Root

Ein zentraler Webfehler klassischer Architekturen sind anämische Datenmodelle im globalen Shared/-Ordner. In der modernisierten VSA liegen die langlebigen Kern-Strukturen und Abstraktionen direkt auf der obersten Ebene des jeweiligen Fachmoduls (CarRental.Customer/) und hängt nicht von Klassen des Subpakets Registration/ ab (konsequente Umsetzung von Regel 1).

Das Subpaket Registration/ führt keine neuen, globalen Entitäten ein. Es nutzt die Typen des übergeordneten Moduls und fügt lediglich spezifische Use-Case-Details hinzu (konsequente Umsetzung von Regel 2). Auch das Interface nutzt kein technisches SaveAsync und verzichtet auf das technokratische Suffix -Async. Es spricht die reine Sprache des Business: Register (konsequente Umsetzung von Regel 3).

Das Subpaket Registration/ führt keine neuen, globalen Entitäten ein. Es nutzt die Typen des übergeordneten Moduls und fügt lediglich spezifische Use-Case-Details hinzu (konsequente Umsetzung von Regel 2). Auch das Interface nutzt kein technisches SaveAsync, sondern spricht die Sprache des Business: RegisterAsync (konsequente Umsetzung von Regel 3).

```csharp
namespace CarRental.Customer;

// Das langlebige Fachkonzept (Substantiv) lebt im Root des Moduls
public class Customer
{
    public Guid Id { get; private set; }
    public string Email { get; private set; }
    public bool IsVerified { get; private set; }

    public Customer(Guid id, string email)
    {
        Id = id;
        Email = email;
        IsVerified = false;
    }

    public void VerifyEmail() => IsVerified = true;
}

// Die Infrastruktur-Implementierung liegt direkt daneben, nicht global in "Infrastruktur/"
public class PersistentCustomers : ICustomers
{
    private readonly CarRentalDbContext _dbContext;

    public PersistentCustomers(CarRentalDbContext dbContext)
    {
        _dbContext = dbContext;
    }

    // Der fachliche Vertrag verlangt "Register". Dass die Datenbank 
    // asynchron arbeitet, ist ein reines Implementierungsdetail dieser Klasse.
    public async Task Register(Customer customer)
    {
        await _dbContext.Customers.AddAsync(customer);
        await _dbContext.SaveChangesAsync();
    }
}
```

### 2. Das Payment-Modul: Kapselung des Anti-Corruption Layers (ACL)

Der technische HTTP-Client für PayPal ist kein Fremdkörper mehr, der ungeschützt in einem Feature-Slice liegt. Er ist ein internes Implementierungsdetail des Moduls CarRental.Payment und nach außen hin unsichtbar. Das Interface IPayment verzichtet auf ein generisches ProcessAsync und nutzt das ausdrucksstarke Execute, um die geschäftliche Aktion zu beschreiben.

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

namespace CarRental.Payment.PayPal;

internal class PayPal : IPayment
{
    private readonly HttpClient _httpClient;

    public PayPal(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    // Das Interface fordert "Execute". Die asynchrone HTTP-Kommunikation 
    // wird im Inneren der Methode weggekapselt.
    public async Task<Transaction> Execute(PayPalRequest request)
    {
        // Technische Kommunikation mit der PayPal-API bleibt intern gekapselt
        var response = await _httpClient.PostAsJsonAsync("/v2/checkout/orders", request);
        response.EnsureSuccessStatusCode();

        var result = await response.Content.ReadFromJsonAsync<PayPalResponse>();
        return new Transaction(result.Id, result.Amount, "PayPal", Status.Success);
    }
}
```

Die restliche Anwendung weiß nicht einmal, dass PayPal über HTTP kommuniziert, geschweige denn, wie Threads verwaltet werden. Sollte sich die API von PayPal ändern oder Stripe als Standard aktiv werden, betrifft dies ausschließlich das Innere dieses Moduls. Die eigentliche Business-Logik bleibt unberührt.

### Die Steuerung des Bootstrapping-Prozesses

In dieser Architektur rücken Infrastruktur-Entscheidungen an den äußersten Rand des Systems. Die CarRental.Application dient als reine Composition Root. Die CarRentalApp.cs enthält die Main-Methode, um den Startprozess der Anwendung explizit zu steuern. Die Web-Infrastruktur, die Domänen-Dienste und die HTTP-Pipeline werden zentral konfiguriert.

#### 1. CarRentalApp.cs (Der Einstiegspunkt)

Die Anwendung startet, lädt die technischen Umgebungsparameter und übergibt die Konfiguration an die Registrierungs-Logik:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;

namespace CarRental.Application;

public class CarRentalApp
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        // 1. Web-Infrastruktur hinzufügen
        builder.Services.AddControllers();

        // 2. Composition Root aufrufen und Konfiguration mitgeben
        builder.Services.AddCarRentalServices(builder.Configuration);

        var app = builder.Build();

        // 3. HTTP-Pipeline konfigurieren
        app.UseRouting();
        app.MapControllers();

        // 4. Anwendung starten
        app.Run();
    }
}
```


### 2. CarRentalSettings und Das Options-Modell (Im Modul CarRental.Payment/PayPal/)

Technische Parameter für die Infrastruktur und Drittanbieter-Anbindungen (wie den PayPal Anti-Corruption Layer) werden extern gepflegt:

```json
json{
  "PayPal": {
    "BaseUrl": "https://paypal.com",
    "TimeoutSeconds": 30
  }
}


Ein einfacher, unveränderlicher Datensatz (Record), der exakt der Struktur im JSON entspricht und als starke Typisierung für die Konfiguration dient:

```csharp
namespace CarRental.Payment.PayPal;

public record PayPalOptions
{
    public string BaseUrl { get; init; } = string.Empty;
    public int TimeoutSeconds { get; init; } = 15;
}

```

### 3. Integration in CarRentalDI.cs (Composition Root)

In der zentralen CarRentalDI.cs laufen alle Fäden zusammen. Selbst im DI-Container wird sichtbar, dass wir keine technischen Datenspeicher konfigurieren, sondern fachliche Schnittstellen bedienen. Beachte, dass die asynchrone Natur der Implementierungen ein reines Detail bleibt – die Verträge selbst (ICustomers, IPayment) fordern pure Fachlichkeit ohne technisches Rauschen wie -Async:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Options;
using CarRental.Booking;
using CarRental.CarPool;
using CarRental.Customer;
using CarRental.Payment;
using CarRental.Payment.PayPal;

namespace CarRental.Application;

public static class CarRentalDI
{
    public static IServiceCollection AddCarRentalServices(this IServiceCollection services, IConfiguration configuration)
    {
        // --- CONFIGURATION BINDING ---
        services.Configure<PayPalOptions>(configuration.GetSection("PayPal"));

        // --- 1. BOOKING MODUL ---
        services.AddScoped<IReservations, PersistentReservations>();

        // --- 2. CAR POOL MODUL ---
        services.AddScoped<PersistentCarPool>();
            
        services.AddScoped<ICarPool>(sp => 
            new CachedCarPool(
                sp.GetRequiredService<PersistentCarPool>()
            ));

        // --- 3. CUSTOMER MODUL ---
        services.AddScoped<PersistentCustomers>();
            
        services.AddScoped<ICustomers>(sp => 
            new NotifiedCustomer(
                sp.GetRequiredService<PersistentCustomers>()
            ));

        // --- 4. PAYMENT MODUL (Mit HttpClient & Options) ---
        // Registriert den technischen PayPal-HTTP-Client direkt mit den Optionen
        services.AddHttpClient<PayPal>((sp, client) =>
        {
            var options = sp.GetRequiredService<IOptions<PayPalOptions>>().Value;

            client.BaseAddress = new Uri(options.BaseUrl);
            client.Timeout = TimeSpan.FromSeconds(options.TimeoutSeconds);
        });

        // IPayment fordert "Execute" – wie es im Hintergrund läuft, entscheidet das Modul
        services.AddScoped<IPayment, PayPal>();

        return services;
    }
}
```


## Die drei Design-Säulen der modernisierten VSA

Das radikale Umdenken basiert auf drei fundamentalen Architektur-Paradigmen, die das Projekt resistent gegen Code-Fäulnis machen und das Business-Denken konsequent erzwingen:

### 1. Keine Aufwärts-Abhängigkeiten (Rule of Layering)

Subpakete wie PayPal/ oder Registration/ hängen von den Kern-Typen ihres übergeordneten Moduls ab – niemals umgekehrt. Dadurch bleibt die Domäne stabil. Technische Details können sich beliebig ändern, ohne die übergeordneten Kern-Strukturen des Zahlungsmoduls zu destabilisieren.

### 2. Verbannung technischer Implementierungsdetails aus Verträgen

Ein Rückgabetyp wie Task ist für die asynchrone Laufzeitumgebung von .NET unverzichtbar. Das Mitschleifen von Suffixen wie -Async oder datenbanknahen Begriffen wie Save in den Interfaces ist jedoch ein architektonischer Fehler. Die Domäne bestimmt die Sprache. Wenn die Verträge konsequent frei von technischem Ballast gehalten werden, bleibt die Architektur langlebig und entkoppelt von Frameworks und Paradigmen der Infrastruktur.


```csharp

```

### 3. Vertikale Dekoration statt Cross-Cutting-Pipelines

Anstatt Logging, Caching oder Benachrichtigungen in globale, technische Schichten oder MediatR-Pipelines zu zwingen, nutzen wir das Decorator-Pattern auf Basis unserer rein fachlichen Verträge.

* Ein CachedCarPool dekoriert den PersistentCarPool.
* Ein NotifiedCustomer erweitert das Register-Verhalten um eine E-Mail-Benachrichtigung.
* Die Komposition erfolgt sauber und zentral in der CarRentalDI.cs. Da die Methodennamen rein fachlich sind (Register, Execute), liest sich die Dekoration wie ein echter Geschäftsprozess.

## Vertikale Dekoration in der Praxis

Anstatt Querschnittsbelange (Cross-Cutting Concerns) wie Validierung, Logging oder Caching in globale MediatR-Pipelines oder technische Schichten zu verlagern, nutzen wir das Decorator-Pattern. Da unsere Schnittstellen in der Modul-Root reine Fachlichkeit ohne technisches Suffix wie -Async sprechen, bleibt die geschäftliche Absicht des Decorators glasklar lesbar.

Schauen wir uns das am Beispiel des Customer-Moduls an. Wenn ein Kunde registriert wird, soll im Erfolgsfall eine Willkommens-E-Mail versendet werden. Das Senden einer E-Mail ist ein rein technischer Nebeneffekt – er gehört nicht in den Kern der Registrierungslogik.

Hier ist die Implementierung des fachlichen Vertrags ICustomers und seiner Dekoration:

```csharp
using System.Threading.Tasks;

namespace CarRental.Customer;

// 1. Der reine fachliche Vertrag in der Modul-Root
public interface ICustomers
{
    Task Register(Customer customer);
}

// 2. Die Kern-Implementierung: Schreibt den Kunden exklusiv in die Datenbank
public class PersistentCustomers : ICustomers
{
    private readonly CarRentalDbContext _dbContext;

    public PersistentCustomers(CarRentalDbContext dbContext)
    {
        _dbContext = dbContext;
    }

    public async Task Register(Customer customer)
    {
        await _dbContext.Customers.AddAsync(customer);
        await _dbContext.SaveChangesAsync();
    }
}

// 3. Der vertikale Decorator: Erweitert das Verhalten um den E-Mail-Nebeneffekt
public class NotifiedCustomer : ICustomers
{
    private readonly ICustomers _inner;
    private readonly IEmailService _emailService; // Internes Detail des Customer-Moduls

    public NotifiedCustomer(ICustomers inner, IEmailService emailService)
    {
        _inner = inner;
        _emailService = emailService;
    }

    public async Task Register(Customer customer)
    {
        // Ruft zuerst die Kern-Logik (Speichern) auf
        await _inner.Register(customer);

        // Führt anschließend den fachlichen Nebeneffekt aus
        await _emailService.SendWelcomeEmail(customer.Email);
    }
}

```

Die Komposition dieser Klassen erfolgt, wie zuvor in der CarRentalDI.cs gezeigt, zentral am äußersten Rand der Anwendung (Composition Root):

```csharp
// In CarRentalDI.cs
services.AddScoped<PersistentCustomers>();
services.AddScoped<ICustomers>(sp => 
    new NotifiedCustomer(
        sp.GetRequiredService<PersistentCustomers>(),
        sp.GetRequiredService<IEmailService>()
    ));

```

Die Klasse PersistentCustomers bleibt voll fokussiert auf ihre Kernaufgabe (Datenpersistenz). Der Decorator NotifiedCustomer fügt das Benachrichtigungs-Feature hinzu, ohne dass bestehender Code angefasst oder ein schwerfälliges Framework-Plugin (wie MediatR-Behaviors) dazwischengeschaltet werden muss.

## Phänomenale Testbarkeit: Unit-Tests ohne Mocking-Frameworks

Ein oft übersehener, aber gravierender Nachteil des klassischen VSA-Ansatzes mit CQRS und MediatR ist die Testbarkeit. Um einen einzelnen Handler zu testen, müssen Entwickler komplexe Mock-Frameworks (wie Moq oder NSubstitute) bemühen, um MediatR-Pipelines, Validatoren und tief vergrabene HTTP-Clients zu simulieren. Das Ergebnis sind fragile Tests, die bei technischen Refactorings sofort brechen.

In unserer modernisierten VSA schreiben sich Unit-Tests fast von selbst. Da die Schnittstellen im Modul-Root pure Fachlichkeit ohne technisches Suffix wie -Async oder datenbankgetriebene CRUD-Begriffe definieren, können wir für den Test pure, leichtgewichtige Fakes statt komplexer Mocks verwenden.

Schauen wir uns an, wie elegant wir das Zusammenspiel aus der Domänen-Klasse Customer und dem Decorator NotifiedCustomer testen können – ganz ohne ein einziges Mock-Framework:

```csharp
using System;
using System.Threading.Tasks;
using Xunit;
using CarRental.Customer;

namespace CarRental.Tests.Customer;

// 1. Ein simpler, lesbarer Fake statt eines komplexen Mocks
public class FakeCustomers : ICustomers
{
    public Customer LastRegisteredCustomer { get; private set; }

    public Task Register(Customer customer)
    {
        LastRegisteredCustomer = customer;
        return Task.CompletedTask;
    }
}

public class FakeEmailService : IEmailService
{
    public string LastSentEmail { get; private set; }

    public Task SendWelcomeEmail(string email)
    {
        LastSentEmail = email;
        return Task.CompletedTask;
    }
}

// 2. Der eigentliche Unit-Test: Liest sich wie eine fachliche Spezifikation
public class CustomerRegistrationTests
{
    [Fact]
    public async Task Should_Register_Customer_And_Send_Welcome_Email()
    {
        // Arrange
        var fakeCustomers = new FakeCustomers();
        var fakeEmailService = new FakeEmailService();
        
        // Wir komponieren das Verhalten exakt wie in der Composition Root
        var sut = new NotifiedCustomer(fakeCustomers, fakeEmailService);
        var customer = new Customer(Guid.NewGuid(), "test@carrental.com");

        // Act
        await sut.Register(customer);

        // Assert
        Assert.NotNull(fakeCustomers.LastRegisteredCustomer);
        Assert.Equal("test@carrental.com", fakeCustomers.LastRegisteredCustomer.Email);
        
        // Prüft, ob der fachliche Nebeneffekt des Decorators korrekt ausgeführt wurde
        Assert.Equal("test@carrental.com", fakeEmailService.LastSentEmail);
    }
}

```
Dieser Test hängt von keinem Framework ab. Er testet pure Geschäftslogik. Da das Design die technischen Details (wie EF Core oder SMTP-Protokolle) über Interfaces komplett aus der Domäne verbannt hat, laufen hunderte dieser Tests in wenigen Millisekunden durch. Wenn du später Entity Framework gegen einen anderen OR-Mapper austauschst, bleibt dieser Test zu 100 % unberührt und schützt dich weiterhin vor Regressfehler.

## Fazit: Die Architektur schreit wieder nach Business

Die Evolution von der klassischen Schichtenarchitektur hin zur Vertical Slice Architecture war ein Meilenstein für die Softwareentwicklung. Doch erst wenn wir die technischen Schubladen wie Features/, Infrastruktur/ und Shared/ auf der Root-Ebene konsequent eliminieren und durch echte Geschäftsdomänen ersetzen, löst VSA ihr eigentliches Versprechen ein.

Indem wir die Technik komplett der Fachlichkeit unterordnen, schaffen wir eine Codebasis, die:

* das System-Kontext-Diagramm 1:1 im Datei-Explorer widerspiegelt, was das Onboarding neuer Entwickler massiv beschleunigt und Such-Odysseen beendet,
* bis in die C#-Methodensignatur hinein in Substantiven und Verben spricht, da rein technischer Ballast wie das Suffix -Async oder datenbankgetriebene CRUD-Begriffe konsequent aus den Verträgen verbannt werden,
* überlegene Testbarkeit ohne schwerfällige Mock-Frameworks bietet, weil die Domäne frei von Infrastruktur-Abhängigkeiten bleibt und sich über schlanke Fakes in Millisekunden validieren lässt.

Architektur ist kein Selbstzweck. Ihre wichtigste Aufgabe ist es, die Komplexität der realen Geschäftswelt im Code beherrschbar zu machen. Mit modernisierten, radikal fachfokussierten Vertical Slices hört dein System endlich auf, nach eingesetzten Frameworks zu klingen – und schreit stattdessen nach dem, was es wirklich ist: einem erfolgreichen Business.


## Links & Literatur
* Thomas Bayer (2025), [Vertical Slice Architecture](https://software-architecture-summit.de/blog/software-architektur/vertical-slice-architecture-einfuhrung/)
* Bogard, Jimmy: [Vertical Slice Architecture](https://www.jimmybogard.com/vertical-slice-architecture/)
* [Spring-Boot-Starter für den Jmediator](https://github.com/membrane/jmediator-spring-boot-starter)
* [Spring-Boot-Beispiel zur Vertical Slice Architecture](https://github.com/membrane/spring-boot-vertical-slice-architecture)
