# Business Context-Driven Package Structure: Der fehlende Link zwischen Business und Code

*Warum Ihre Package-Struktur das Business Context-Diagramm Ihres Systems sein sollte*

## Das Problem: Die Übersetzungslücke

Stellen Sie sich vor: Ein Business Analyst zeigt Ihnen ein System Context Diagramm von einem **Test-Catalog**, das mit **Jira**, **Codebeamer** und **DOORS** integriert ist:

```
                     Business Analyst
                            │
                            ▼
     ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
     │         │    │                 │    │             │
     │  Jira   │────│   TestCatalog   │────│ Codebeamer  │
     │         │    │                 │    │             │
     └─────────┘    └─────────────────┘    └─────────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │               │
                    │     DOORS     │
                    │               │
                    └───────────────┘
```

Dann öffnen Sie den Code und finden:

**Beispiel 1: Klassische Layered Architecture**

```
com.test.catalog
├── controller/
├── service/
├── repository/
├── dto/
├── entity/
└── config/
```

**Beispiel 2: Clean Architecture (nach Robert C. Martin)**

```
com.test.catalog
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

**Beispiel 3: DDD Structure (nach Eric Evans)**

```
com.test.catalog
├── aggregate/
├── entity/
├── valueobject/
├── repository/
├── service/
├── factory/
└── specification/
```

**Beispiel 4: Hexagonal Architecture**
*Quelle: [Alistair Cockburn's Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)*

```
com.test.catalog
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


**Beispiel 5: Vertical Slices Architecture**
*Quelle: [Jimmy Bogard's Vertical Slices](https://jimmybogard.com/vertical-slice-architecture/)*

```
com.test.catalog
├── features/
│   ├── create-item/
│   │   ├── CreateItemController.java
│   │   ├── CreateItemService.java
│   │   └── CreateItemRepository.java
│   ├── sync-requirements/
│   │   ├── SyncController.java
│   │   ├── SyncService.java
│   │   └── SyncRepository.java
│   └── generate-report/
│       ├── ReportController.java
│       ├── ReportService.java
│       └── ReportRepository.java
├── shared/
└── infrastructure/
```


**Wo ist Jira? Wo ist Codebeamer? Wo ist DOORS aus dem Context-Diagramm?**

**Wo ist der TestCatalog und die externen Systeme, welche im Context-Diagramm so klar sichtbar waren?**

Sie müssen mental übersetzen: 
- "Jira ist vermutlich irgendwo im `service` Package versteckt..." (Layered)
- "Oder vielleicht in `infrastructure/adapter/external`?" (Clean Architecture)
- "Könnte auch in `domain/service` oder `application` liegen?" (DDD)
- "Wahrscheinlich in `adapter/messaging` oder `port/out`?" (Hexagonal)
- "Vielleicht in `features/sync-requirements`? Aber was ist mit anderen Jira-Features?" (Vertical Slices)

**Das Problem:** Selbst moderne Architektur-Patterns wie Vertical Slices fokussieren sich auf **technische Features** statt auf **externe Kontexte**. Die Jira-Integration wird auf verschiedene Feature-Slices verteilt, statt als einheitlicher externer Kontext behandelt zu werden.

## Die Lösung: Context-Driven Packaging

Basierend auf den [drei goldenen Regeln](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/) des leserfreundlichen Paketierens plus einer entscheidenden Erkenntnis: **Ihre Package-Struktur sollte das Context-Diagramm widerspiegeln**.

### Template: Context-Driven Package Structure

**Jetzt spiegeln wir das Context-Diagramm direkt im Code wider:**

```
com.test.catalog                       ← Hauptkontext (Domain Core)
├── app/                               → Startup, DI, Config
│   └── CatalogApplication/            → main method for Startup, DI, Config
├── exchange/                          ← Externer Kontext
│   ├── resource/                      → JAX-RS Resources
│   ├── service/                       → JAX-WS ervices
│   └── database/                      → JPA Entities
├── jira/                              ← Externer Kontext
│   ├── JiraClient.java
│   ├── JiraTickeItem.java
│   └── JiraConsumedMessage.java
├── codebeamer/                       ← Externer Kontext
│   ├── CodebeamerClient.java
│   └── RequirementSync.java
├── doors/                            ← Externer Kontext
│   ├── DoorsClient.java
│   └── DocumentImport.java
├── item/                              ← Item Kontext (used in user/page/)
│   ├── XmlItem.java
│   ├── JsonItem.java                   ← used in exchange/resource/)
│   └── DbItem.java
├── user/                              ← UI Kontext
│   ├── control/                       → common Forms, Panel, Table, List,... (used in user/page/)
│   ├── layout/                        → common Layout, Font, Color,... (used in user/page/)
│   ├── page/                          
│   │     ├── ItemForm.java
│   │     ├── ItemTable.java
│   │     ├── ...
│   │     └── ...
│   └── WebUser.java                  ← impl. of User.java Interface
├── Catalog.java                      ← Domain Interface
├── Item.java                         ← Domain Interface
├── Synchronization.java              ← Domain Interface
└── User.java                         ← Domain Interface
```

## Vorher/Nachher: Echtes Projekt-Beispiel

### Vorher (Technische Pakete)
**Aufgabe**: "Behebe den Bug in der Jira-Integration"
- ⏱️ **15 Minuten** Code-Navigation
- 🔍 Suche in: `service/`, `integration/`, `client/`, `external/`
- 🤔 "Welche Klasse macht was mit Jira?"

### Nachher (Context-Driven)
**Aufgabe**: "Behebe den Bug in der Jira-Integration"  
- ⏱️ **30 Sekunden** Code-Navigation
- 🎯 Direkt zu: `com.test.catalog.jira/`
- ✅ Sofort klar: Alle Jira-bezogenen Klassen

## Messbare Vorteile

### 1. Code-Navigation
- **Vorher**: 3-5 Minuten für Feature-Lokalisierung
- **Nachher**: 30 Sekunden bis 2 Minuten
- **Verbesserung**: 80-90% weniger Zeit

### 2. Onboarding neuer Entwickler
- **Vorher**: "Wo finde ich die Jira Anbindung?" → 30 Minuten Erklärung
- **Nachher**: "Schau ins `jira` Package" → 30 Sekunden
- **Verbesserung**: 99% weniger Erklärungsaufwand

### 3. Business-Developer Kommunikation
- **Vorher**: BA sagt "Jira-Problem" → Developer übersetzt mental zu technischen Packages
- **Nachher**: BA sagt "Jira-Problem" → Developer geht direkt zu `com.test.catalog.jira/`
- **Verbesserung**: Null Übersetzungsaufwand

## Die drei goldenen Regeln

1. **Pakete sollen niemals von Sub-Paketen abhängen**
   - Root Package = Domain Core, unabhängig von allem
   - Externe Kontexte = Adapter, abhängig vom Core

2. **Sub-Pakete führen keine neuen Konzepte ein, nur Details**
   - `jira/JiraClient` = Detail der Jira-Integration
   - Keine neuen fachlichen Konzepte in Sub-Packages

3. **Pakete spiegeln Business-Kontexte wider, nicht technische**
   - ✅ `jira`, `codebeamer`, `doors` (aus Context-Diagramm)
   - ❌ `service`, `repository`, `controller` (technische Details)

## Sofort verwendbare Templates

### Template 1: E-Commerce System
```
com.company.shop
├── app/                     → Startup, DI, Config
│   ├── ShopApp.java         → main Composition of the app
│   └── ShopApi.java         → JAX-RS Resources
├── payment/                 → PayPal, Stripe Integration
├── inventory/               → Warehouse, Stock Management  
├── shipping/                → DHL, UPS Integration
├── user/                    → Frontend, APIs
├── Product.java
└── Order.java
```

### Template 2: Monitoring System  
```
com.company.monitoring
├── app/                           → Startup, DI, Config
│   ├── MonitoringApp.java         → main Composition of the app
│   └── MonitoringApi.java         → JAX-RS Resources
├── alert                          → Alert Processing
├── grafana/                       → Dashboard Integration
├── prometheus/                    → Metrics Collection
├── user/                          → Admin UI
├── Alert.java
├── Metric.java
└── User.java
```

### Template 3: Document Management
```
com.company.docmgmt
├── app/                             → Startup, DI, Config
│   ├── DocManagementApp.java        → main Composition of the app
│   ├── DocManagementApp.java        → JAX-RS Configuration
│   └── DocManagementConfig.java     → DI Configuration
├── confluence/                      → Confluence Integration
│   ├── http/
│   │   └── HttpConfluence.java      → implements Confluence interface
│   ├── Confluence.java              → Confluence interface
│   ├── ConfluenceDocument.java      → implements Document from root
│   └── ConfluenceDocuments.java     → implements Documents from root
├── sharepoint/                      → SharePoint Integration
│   ├── kafka/
│   │   └── KafkaSharepoint.java
│   ├── SharePoint.java
│   ├── SharePointDocument.java
│   └── SharePointDocuments.java
├── mail/                            → Email Integration
│   ├── smtp/
│   │   └── SmtpMail.java            → implements Mail interface
│   ├── Mail.java                    → mail interface
│   ├── MailNotification.java        → implements Notification from root
│   └── MailNotifications.java       → implements Notifications from root
├── user/                            → User/Frontend/UI
│   ├── admin/                       → implementins of User Forms
│   ├── document/                    → implementins of Document Forms
│   ├── .../                         → implementins of other Forms
│   ├── Form.java                    → shered abstract Form Class
│   ├── Menu.java                    → shered Frontend Class
│   └── ...java                      → other shered Class
├── Document.java
├── Documents.java
├── ChangedDocument.java
├── NewDocument.java
├── Notification.java
├── Notifications.java
├── User.java
└── Users.java
```

## Implementierung: Schritt für Schritt

1. **Analysieren Sie Ihr Context-Diagramm**
   - Welche externen Systeme gibt es?
   - Was ist der Hauptkontext/Domain?

2. **Erstellen Sie die Package-Struktur**
   - Root = Domain Name
   - Erste Ebene = Externe Kontexte + UI
   - Domain Entities direkt im Root

3. **Migrieren Sie schrittweise**
   - Beginnen Sie mit einem Kontext (z.B. `jira/`)
   - Verschieben Sie alle Jira-bezogenen Klassen dorthin
   - Wiederholen Sie für jeden Kontext

## Die Synthese: Kombination bewährter Erkenntnisse

### Was die Experten bereits erkannt haben:

**javadevguy (2017):** Formulierte die drei goldenen Regeln für leserfreundliche Packages:
- Pakete sollen niemals von Sub-Paketen abhängen
- Sub-Pakete führen keine neuen Konzepte ein, nur Details  
- Pakete sollen fachliche Konzepte widerspiegeln, nicht technische

**Philipp Hauer (2020):** Betonte "Package by Feature" und das Prinzip "KISS > DRY":
- Features sollten selbstständig und unabhängig sein
- Duplication ist besser als falsche Abstraktionen
- Fachliche Gruppierung über technische Schichten

**Simon Brown (2015):** Propagierte "Modular Monoliths":
- Klare Modul-Grenzen sind entscheidend
- Modularität verhindert den "Big Ball of Mud"
- Module sollten eigenständige, zusammengehörige Einheiten sein

### Die fehlende Verbindung: Context-Driven Packaging

**Das Problem:** Alle Experten fokussieren sich auf Code-Organisation, aber **keiner** macht die Verbindung zu **System Context-Diagrammen** explizit.

**Die Erkenntnis:** Package-Struktur = ausführbares Context-Diagramm

**Die Synthese kombiniert:**
- **javadevguy's Regeln** für lesbare Business Struktur
- **Brown's Modularität** für klare Grenzen
- **Hauer's Feature-Orientierung** für fachliche Kohäsion  
- **Context-Diagramm-Mapping** für Business-Developer-Alignment

**Das Ergebnis:** Eine Package-Struktur, die sowohl technisch sauber als auch fachlich erkennbar ist und die mentale Übersetzungslücke zwischen Business Analysten und Entwicklern schließt.

## Fazit

Business Context-Driven Packaging macht Ihr **Context-Diagramm im Code sichtbar**. Business Analysten und Entwickler sprechen endlich dieselbe Sprache. Der Code wird zum **lebenden Architektur-Dokument**.

**Probieren Sie es aus**: Nehmen Sie Ihr aktuelles Projekt, zeichnen Sie das Context-Diagramm und spiegeln Sie es in Ihren Packages wider. Sie werden überrascht sein, wie viel klarer Ihr Code wird.

---

*Haben Sie Context-Driven Packaging ausprobiert? Teilen Sie Ihre Erfahrungen in den Kommentaren!*

## Weiterführende Links

### Grundlegende Artikel
- [Happy Packaging! (javadevguy)](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
- [Modular Monoliths - Simon Brown (Video)](https://www.youtube.com/watch?v=9imyW_WAYP4)
- [Modular Monoliths - Simon Brown (Slides)](https://static.codingthearchitecture.com/presentations/softwarecircus2015-modular-monoliths.pdf)

### Architektur-Pattern Quellen
- [Clean Architecture Blog (Robert C. Martin)](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Clean Architecture Package Structure Discussion](https://stackoverflow.com/questions/46884449/uncle-bobs-clean-architecture-approach-what-is-recommended-package-structure)
- [Eric Evans DDD Sample Application](https://dddsample.sourceforge.net/)
- [NDDDSample - DDD Implementation](https://github.com/aivascu/ndddsample)
- [Hexagonal Architecture (Alistair Cockburn)](https://alistair.cockburn.us/hexagonal-architecture/)
- [Vertical Slice Architecture (Jimmy Bogard)](https://jimmybogard.com/vertical-slice-architecture/)
- [Package by Feature (Philipp Hauer)](https://phauer.com/2020/package-by-feature/)

### Beispiel-Projekte
- [Spring Framework Documentation](https://spring.io/guides/gs/serving-web-content/)
- [Spring Petclinic Sample](https://github.com/spring-projects/spring-petclinic)
