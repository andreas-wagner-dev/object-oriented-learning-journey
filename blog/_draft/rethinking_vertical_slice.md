# Rethinking Vertical Slice: Architektur neu gedacht

Die Vertical Slice Architecture (VSA) hat die Softwareentwicklung revolutioniert, indem sie technische Schichten (Controller, Service, Repository) aufbrach und Features in den Fokus rückte. Doch in der Praxis mutieren Slices oft zu unübersichtlichen Datei-Wüsten. Zeit für ein grundlegendes Umdenken.


## Das Autovermietungs-System

Um das Konzept der modernisierten Vertical Slices greifbar zu machen, nutzen wir ein klassisches Autovermietungs-System (Car Rental). Das System interagiert mit verschiedenen externen Systemen, Zahlungsanbietern und Benutzeroberflächen:

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

## Die klassische Vertical Slice Struktur

Bei dem traditionellen Ansatz gruppiert der Features/ Ordner die Geschäftsfälle, während technische Ordner wie Infrastruktur/ und Shared/ als globale, querschneidende Schichten daneben existieren:

Wirft man einen Blick auf die oberste Verzeichnisebene eines klassischen Vertical Slice Projekts, bricht das Versprechen einer fachlich orientierten Architektur sofort in sich zusammen:
```
src/
├─ Features/
├─ Infrastruktur/
└─ Shared/
```

Wenn ein neuer Entwickler oder ein Software-Architekt diese Struktur öffnet, stellt er sich unweigerlich die Fragen:
* Wo ist das Auto (`Car`)?
* Wo ist der Kunde (`Customer`)?
* Wo ist die Zahlung (`Payment`)?
* Wo ist die Buchung (`Booking`) aus dem Kontext-Diagramm abgeblieben?

**Nichts davon ist sichtbar.** Anstatt die echten fachlichen Säulen des Unternehmens abzubilden, ist das System nach technischen Aspekten (`Infrastruktur` vs. `Shared` vs. `Features`) organisiert. Die Kernarchitektur „schreit“ nicht nach **Autovermietung**, sondern nach Software-Infrastruktur. Die erste Ebene wirft Fragen auf, weil sie eine rein technische Schubladen anbietet und zwingt den Betrachter zu einer mentalen Übersetzung: Um zu verstehen, was das System geschäftlich überhaupt tut, muss man erst tiefer in den `Features/`-Ordner abtauchen. 

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

Wenn wir tiefer in diese klassische Verzeichnisstruktur eintauchen, sehen wir, dass der gesamte Code in drei starre, technische Hauptbereiche aufgeteilt wird:

1. Der `Features/`-Ordner (Die Slices): Hier wird versucht, Anwendungsfälle (Use Cases) zu kapseln. Jedes Feature (wie `RegisterCustomer` oder `ProcessPayment`) erhält einen eigenen Unterordner. Innerhalb dieses Ordners liegen alle Klassen, die für diesen spezifischen HTTP-Request benötigt werden. Das Problem: Die Ordner werden sofort von rein technischen **CQRS- und MediatR-Mustern** überschwemmt. Statt fachlicher Logik springen dem Entwickler sofort Begriffe wie `Command`, `Handler`, `Validator` und `Dto` ins Auge. Zudem bricht die Kapselung bei externen Integrationen auf: Technische HTTP-Clients für Drittanbieter (wie `PayPalClient.cs` und `StripeClient.cs`) werden pragmatisch tief im einzelnen Feature-Slice vergraben, anstatt sie sauber vom Kern zu isolieren.

2. Der `Infrastruktur/`-Ordner (Das Fundament): In dieser Schublade landen alle technischen Querschnittsbelange, die das System am Laufen halten. Hier lebt der `CarRentalDbContext` – ein typischer, „fetter“ Entity Framework Kontext, der die Tabellen aller Domänen (Kunden, Autos, Zahlungen) in einer einzigen Klasse vereint. Auch die Konfigurationen für den Datenbank-Mapper sowie die Verbindung zum Message Broker (`EventBus.cs`) liegen hier isoliert vom restlichen Code.

3. Der `Shared/`-Ordner (Der gemeinsame Nenner): Alles, was von mehr als einem Feature benötigt wird, wandert unweigerlich in den `Shared/`-Bereich. Das betrifft vor allem die Domänen-Klassen wie `CustomerEntity.cs` oder `CarEntity.cs`. Da die Slices selbst keine eigenen Datenmodelle besitzen dürfen (um Duplikate zu vermeiden), greifen alle Slices quer durch das Projekt auf diese zentralen Entitäten zu. Meistens verkümmern diese Klassen dadurch zu reinen Datenbehältern (anämische Datenmodelle) ohne echte Geschäftslogik.

## Was Entwickler wirklich sehen wollen

Wenn ein Softwareentwickler oder Architekt ein neues Projekt öffnet, möchte er nicht raten müssen, worum es geht. Er möchte, dass die Codebasis sofort zu ihm spricht. Onboarding, Wartung und das Finden von Fehlern werden massiv beschleunigt, wenn das Verzeichnis die echte Geschäftswelt widerspiegelt. Ein Entwickler wünscht sich beim Blick auf die Root-Ebene drei Kernaspekte:

**1. Sofortige fachliche Orientierung**  
Die Ordnerstruktur sollte das System-Kontext-Diagramm 1:1 abbilden. Wenn das Business aus Buchungen, Kunden und Zahlungen besteht, müssen genau diese Begriffe die oberste Ebene dominieren. Der Entwickler will sofort sehen: „Ah, das ist ein Autovermietungssystem!“ und nicht „Das ist ein ASP.NET-Projekt mit MediatR.“

**3. Funktionale Navigation ohne Such-Odyssee**  
Wenn ein Fehler im PayPal-Prozess auftritt, will der Entwickler nicht erst überlegen müssen, welches globale Shared-Modul oder welche Querverlinkung im Features-Ordner dafür zuständig ist. Die Struktur muss intuitiv sein: Payment öffnen -> PayPal öffnen -> Fehler beheben. Alles, was fachlich zu PayPal gehört, muss genau dort gekapselt sein.

**5. Technische Details im Hintergrund**  
Frameworks, Controller, HTTP-Schnittstellen und Datenbank-Treiber sind austauschbare Werkzeuge. Sie sind Mittel zum Zweck, nicht der Zweck selbst. Ein Entwickler möchte, dass diese technischen Details visuell in den Hintergrund treten. Sie gehören als Implementierungsdetails in das jeweilige Fachgebiet, anstatt das Projekt von außen zu umklammern.

**Kurz gesagt:** *Die Architektur muss nach dem Business schreien, nicht nach der eingesetzten Technologie.*

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


```csharp
using Microsoft.Extensions.DependencyInjection;

using CarRental.Booking;
using CarRental.CarPool;
using CarRental.Customer;
using CarRental.Payment;

namespace CarRental.Application;

public static class CarRentalDI
{
    public static IServiceCollection 
        AddCarRentalServices(
            this IServiceCollection services)
    {
        // --- 1. BOOKING MODUL ---
        services.AddScoped<
            IReservations, 
            PersistentReservations>();

        // --- 2. CAR POOL MODUL ---
        services.AddScoped<
            PersistentCarPool>();
            
        services.AddScoped<ICarPool>(sp => 
            new CachedCarPool(
                sp.GetRequiredService<
                    PersistentCarPool>()
            ));

        // --- 3. CUSTOMER MODUL ---
        services.AddScoped<
            PersistentCustomers>();
            
        services.AddScoped<ICustomers>(sp => 
            new NotifiedCustomer(
                sp.GetRequiredService<
                    PersistentCustomers>()
            ));

        // --- 4. PAYMENT MODUL ---
        services.AddScoped<IPayment, 
            PayPalPayment>();

        return services;
    }
}


```
## Fazit

Wer Vertical Slices zu flach und rein technisch baut, erntet schnell Chaos. Indem wir hierarchische Domänen-Pakete, das Decorator-Muster und eine zentrale Composition Root nutzen, bleibt die Codebasis hochgradig modular. Technische Details verblassen – was bleibt, ist eine Softwarestruktur, die wie ein Buch über dein echtes Geschäft lesbar ist.


## Links & Literatur
* Thomas Bayer (2025), [Vertical Slice Architecture](https://software-architecture-summit.de/blog/software-architektur/vertical-slice-architecture-einfuhrung/)
* Bogard, Jimmy: [Vertical Slice Architecture](https://www.jimmybogard.com/vertical-slice-architecture/)
* [Spring-Boot-Starter für den Jmediator](https://github.com/membrane/jmediator-spring-boot-starter)
* [Spring-Boot-Beispiel zur Vertical Slice Architecture](https://github.com/membrane/spring-boot-vertical-slice-architecture)
