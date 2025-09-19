# Kontextgetriebene Paketierung in Softwareprojekten

Fast jeder Entwickler beginnt damit, Packages lediglich als ein Werkzeug zur Organisation von Klassen zu nutzen – meist so, wie es in dem Moment logisch erscheint.  Doch eine *Packaging-Strategie* kann weitaus mächtiger sein: Sie kann *Wissen* enthalten, *Orientierung* bieten und die langfristige *Wartbarkeit* der Software erheblich verbessern.

Dieses Blogpost beleuchtet drei zentrale Regeln von Robert Bräutingm für eine Bisness-Contept getriebene Paketierung in Softwareprojekten und vertieft praktische Aspekt, welche beim Einsatz in realen Projekten auftreten können.

Dazu werden Varianten für Clean- und DDD-Architektur sowie für **Jakarta EE**-basierte Systeme vorgestellt.

Als Ausgangspunkt der Betrachtung statischer Code-Strukturen wie *Pakete" eines Softwaresystems, wird das **C4-Modell** für die grundlegende Terminologie und Abstraktion verwendet. 

---

## Die drei Regeln

### Regel 1: Packages sollten niemals von ihren Sub-Packages abhängen
Ein Package darf nicht von seinen eigenen Sub-Packages abhängig sein.

Beispiel: `com.example.customer` sollte nicht von `com.example.customer.bill` abhängen.

Damit bleibt die Hierarchie stabil und die Abhängigkeiten zeigen nach „unten“, nicht nach „oben“.  
Was **nicht** gemeint ist: Packages auf der gleichen Ebene („Siblings“) dürfen sich durchaus gegenseitig referenzieren, wenn sie fachlich verbunden sind.

---

### Regel 2: Sub-Packages führen keine neuen Konzepte ein
Sub-Packages dienen der **Detaillierung** eines bestehenden Konzepts, nicht der Einführung neuer.

Ein Beispiel:  
- `com.example.billing` (Business-Konzept „Billing“)  
- `com.example.billing.rule` (Details: Geschäftsregeln des Billings)

**Details** - sind Geschäftsregeln und beziehen sich immer auf Geschäftobjekte aus dem lokalen Kontext  

```
com.example.billing
├── bill/  <- fachliche Deteils - Geschäftsregeln eines Geschäftobjekts (Bill) aus dem Business-Kontext
│ ├── StoredBill.java  <- realisiert/erweitert/dekoriert/detailliert Bill
│ ├── TaxedBill.java  
│ └── PaidBill.java  
├── rule/ <- fachliche Deteils - Geschäftsregeln eines Geschäftobjekt (Rule) aus dem Business-Kontext
│ ├── unit/ <- fachliche Deteils - Geschäftsregeln eines Geschäftobjekts (Unit) aus dem Sub Business-Kontext
│ │   └──ISO.java <- realisiert/erweitert/dekoriert/detailliert Unit
│ ├── StoredRule.java
│ ├── TimedRule.java
│ ├── ConfirmedRule.java
│ └── Unit.java <- Geschäftobjekt im Sub Kontext
├── tax/  <- fachliche Deteils - Geschäftsregeln eines Geschäftobjekts (Tax) aus dem Business-Kontext
│ ├── UKTax.java  <- realisiert/erweitert/dekoriert/detailliert Tax
│ └── EUTax.java  
├── Rule.java <- Geschäftobjekt im Kontext
├── Tax.java <- Geschäftobjekt im Kontext
├── Bill.java <- Geschäftobjekt im Kontext
└── Billing.java  <- Geschäftobjekt im Kontext
```

Falsch wäre:  
- com.example.billing.rule` enthält plötzlich ein Konzept „Notification“, das nichts mit Billing zu tun hat bzw. zu keinem der Geschäftobjekte im "billing" Paket eine Beziehung hat.  
- oder ein Sub-Package `util`, das kein Business-Konzept darstellt, sondern nur nüzliche Klassen z. B. für ISO Einheiten sammelt.

---

### Regel 3: Packages spiegeln Business-Konzepte wider

Packages sollten **Business-Terminologie** und **fachliche Konzepte** reflektieren, und nicht die technischen.  
Dadurch wird die Software auch für Fachfremde nachvollziehbar.

Die Business-Konzepte hängen jedoch stark von der Perspektive des Betrachters ab.

Während vom Kunden in erster Linie seine Geschäftsprozesse als Business-Konzepte angesehen werden,
sieht meist der Endnutzer 'user' die Benuzteroberfläche als für Ihn relevantes Business-Konzept.
Beispiel 1:
- `com.example.billing.user` (Business-Konzept für Interaktion menschlicher Benutzer)
Beispiel 2:
- `com.example.billing.api.user`  (Business-Konzept für Interaktion menschlicher Benutzer)  
- `com.example.billing.api.resource` (Business-Konzept für externe Systeme, die Business-Daten konsumieren)  
- `com.example.billing.api.service` (Business-Konzept für Dienste, die Business-Logik ausführen)

Auch das Deployment sowie die Verwaltug von Abhängigkeiten und Konfigurationen können jenach Berachterperspektive ein Business-Konzpt sein. 
Ein Paket wie `app` oder 'config' ist meist in einem Bibliothekenartigen Codestruktur nicht notwendig, im Gegensatz zu Business-Applikationen ist es oft notwendig, um auch dem technischen *Übel* Herr zu werden.

---

## Praktische Aspekte

### Aspekt 0: Analyse der Business-Konzepte (C4)

Die 4 Sichten im C4 Modell können beiten Anhaltspunkte zur Identifikation von Paketen:
- Kontext-Diagramme – Ebene 1: ... Umfang eines Systems seine Beziehungen zu *Benutzern* und *anderen Systemen*.
- Container-Diagramme – Ebene 2: ... Laufzeitgrenzen um Komponenten als separat betreibbare Einheiten.
- Komponenten-Diagramme – Ebene 3: ... Aspekte wie Paketirung von Komponenten, deren Verantwortlichkeiten mit Technologie-/Implementierungsdetails.
- Quelltext-Diagramme – Ebene 4: ... Details über den Entwurf der Architekturelemente, die auf Code abgebildet werden können.

Zudem veranschaulichen Deployment Diagrame, wie Instanzen von Softwaresystemen und/oder Containern im statischen Modell in der Infrastruktur innerhalb einer gegebenen Bereitstellungsumgebung (z. B. Produktion, Staging, Entwicklung usw.) bereitgestellt werden. 

1. *Identifikation von Elenenten und Zielgruppen* [C4 System-Context Diagram (https://c4model.com/diagrams/system-context)]
2. *Identifikation von Applikation Schittellen* [C4 Container Diagram (https://c4model.com/diagrams/container)]
3. *Identifikation von Business-Konzepten* [C4 System-Context Diagram ((https://c4model.com/diagrams/component)]
4. *Identifikation von Abhängkeiten und Konfigurationen* [C4 Deployment Diagram (https://c4model.com/diagrams/deployment)]

---
### Aspekt 1: Abhängigkeiten zwischen Sub-Packages
Wenn zwei Sub-Packages derselben Ebene voneinander abhängen, ist das erlaubt, sofern es fachlich Sinn ergibt.  
Beispiel:  
- `com.example.customer.address` ↔ `com.example.customer.contact`

Beide gehören zum übergeordneten Konzept *Customer* und dürfen zusammenarbeiten, so lange sie keine zirkulär Abhängingkeiten aufweisen

**Valide Abhängigkeit:** customer.address und customer.contact sind Sub-Packages derselben Ebene unter customer. Beide Sub-Packages gehören zum selben Überkonzept *Customer*.

```mermaid
graph LR
    subgraph Customer
        Address[customer.address]
        Contact[customer.contact]
    end
    Contact --> Address
```

```mermaid
graph LR
    subgraph Customer
        Address[customer.address]
        Contact[customer.contact]
    end
    Address --> Contact
```

| Regel / Sonderfall                                        | Erfüllt? | Begründung                                                                                                                         |
| --------------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Regel 1: Kein Paket darf von Sub-Package abhängen**     | ✅        | `customer.address` hängt von `customer.contact` — aber kein übergeordnetes Paket hängt davon ab. Es ist innerhalb derselben Ebene. |
| **Regel 2: Sub-Packages führen keine neuen Konzepte ein** | ✅        | Beide sind Teil des Konzeptes „Customer“. Es werden keine völlig neuen Domänenbegriffe eingeführt.                                 |
| **Regel 3: Pakete spiegeln Business-Konzepte**            | ✅        | „Address“ und „Contact“ sind fachlich verständliche Begriffe im Kontext „Customer“, kein technisches Layer-Wording.                |


**Zirkuläre Abhängigkeit:** customer.address hängt von customer.contact ab und umgekehrt.

```mermaid
graph LR
    subgraph Customer
        Address[customer.address]
        Contact[customer.contact]
    end
    Address --> Contact
    Contact --> Address
```

*Warum ist diese Struktur problematisch?*

- Verletzung von Regel 1: Pakete sollten nicht von ihren eigenen Sub‑Paketen abhängen. Diese zirkuläre Abhängigkeit führt zu einer instabilen Hierarchie.
- Verletzung von Regel 2: Sub‑Pakete sollten keine neuen Konzepte einführen. Die gegenseitige Abhängigkeit zwischen address und contact kann zu einer Vermischung von Verantwortlichkeiten führen.
- Verletzung von Regel 3: Pakete sollten Business‑Konzepte widerspiegeln. Die zirkuläre Struktur erschwert das Verständnis der fachlichen Zusammenhänge.

**Mögliche Lösungen**

- Refactoring: Überprüfen, ob die Verantwortlichkeiten klar getrennt sind und ob eine der Abhängigkeiten entfernt oder umgestaltet werden kann.
- Verwendung von Schnittstellen: Einführen von Schnittstellen oder abstrakten Klassen, um die direkte Abhängigkeit zwischen den Paketen zu vermeiden.
- Neuorganisation der Pakete: Erstellen eines neuen Pakets, das die gemeinsamen Konzepte oder Schnittstellen enthält, um die Abhängigkeiten zu entflechten.

---
### Aspekt 2: Technische Hilfsklassen Utilities
Utilities und technische Hilfsklassen sollten **kein eigenes Business-Konzept** sein.  
Sie gehören entweder klar zu einem bestehenden Business-Konzept oder werden durch objektorientierte Patterns (z. B. **Decorator**) realisiert.  

Falsch wäre: ein generisches `com.example.util`-Paket.  
Richtig wäre: technische Helfer lokal in dem Business-Paket ablegen, wo sie fachlich Sinn ergeben.

### Aspekt 3: Applikation Schnittstellen - API als eigenes Business-Konzept

Eine Applikation, die Schnittstellen z. B. für Ressourcen oder Interaktionen anbietet, impliziert die (**API**) als ein Business-Konzept der Applikation.

Die Detailausprägungen bzw. die Sub-Pakete welche von einer 'api' ausgehen, können z. B. sein: 
- `api.user` (Interaktion durch Menschen, z. B. UI/Frontend)  
- `api.resource` (bereitgestellte Daten-Ressourcen, z. B. JSON via JAX-RS)  
- `api.service` (externe Services, die Business-Logik triggern)  

Hier ist die API also nicht nur ein technischer Layer, sondern ein **vollwertiges Business-Konzept**.

---

### Aspekt 4: Technishe Abhängkeiten und Konfiguration

Ein Paket wie `app` oder 'config' ist meist in einem Bibliothekenartigen Code nicht notwendig, im Gegensatz zu Business-Applikationen ist es meist notwendig um dem technischen *Übel* Herr zu werden.

Im Kontext des Deloyments sind technishen Abhängkeiten und Konfiguration der gesamten Applikation ein Business-Konzept/-Need.

Durch die Einführung eines `app`-Pakets wird das Deloyment und Konfiguration als Business-Konzept und  kein **technisches Hilfspaket**.  
Es dient ausschließlich für:
- **Application-Startup** (Bootstrapping)  
- **Dependency Injection** (z. B. CDI, Spring, Guice)  
- **Konfiguration** (Properties, technische Bindung an Frameworks)  

👉 Fachliche Logik gehört **niemals** in `app`.  
Das Paket ist vergleichbar mit der „Infrastruktur-Schicht“ in DDD und bleibt bewusst **technisch isoliert**.
---

### Aspekt 5: Tasks sind kein eigenes Business-Konzept
Hintergrundaufgaben (oder Cron Tasks) sind fachlich motivierte Prozesse bezogen auf die fachlichen Objekte, aber **kein eigenständiges Business-Konzept** wie z. B. `catalog` oder `item`.  
Sie befinden sich direkt im **Root** eines **Business-Kontext** und werden dort als fachliche Prozesse verankert:

```
com.test.catalog
├── ItemCleanupTask.java ← Fachlicher Task als direkt im Root vom fachlichen Haupkontext
├── ItemSyncTask.java ← Fachlicher Task als direkt im Root vom fachlichen Haupkontext 
├── Catalog.java
├── Item.java
└── Synchronization.java
```

Oder als Betrachtung fachlicher Deteils zu einem Bussines-Objekt 

```
com.test.catalog
├── item/ <- Fachliche Deteils zum ← Bussines-Objekt Item 
│ ├── ItemSyncTask.java ← Fachlicher Task als Detail direkt im Root vom fachlichen Subkontext
│ ├── ItemCleanupTask.java ← Fachlicher Task als Detail direkt im Root vom fachlichen Subkontext
├── Catalog.java
├── Item.java ← Bussines-Objekt/-Interface  
└── Synchronization.java
```

---
## Paketstruktur für Framework basierte Architektur (Jakarta EE oder Spring)

### Variante A: Mehrere Einstiegspunkte ohne zentrales `api`-Paket

Hier sind die Einstiegspunkte direkt im jeweiligen Sub-Kontext untergebracht:

```
com.test.catalog
├── app/ ← Startup, DI, Config
│ ├── CatalogApplication.java (handle or register hooks on Startup event, prepare all **jakarta.inject.*** CDI injections/producers)
│ ├── CatalogResourceApplication.java (enable JAX-RS @ApplicationPath)
│ └── CatalogFacesConfig.java (enable or configure JSF)
├── jira/  <- Fachliche Deteils zum Item bezüglich eines externen Business-Kontextes 
│ ├── Jira.java
│ ├── IssueItem.java
│ └── StorySync.java
├── codebeamer/  <- Fachliche Deteils zum Item bezüglich eines externen Business-Kontextes  
│ ├── Codebeamer.java
│ ├── RequirementItem.java
│ └── RequirementSync.java
├── doors/ <- Fachliche Deteils zum Item Konzept bezüglich eines externen Business-Kontextes 
│ ├── Doors.java
│ ├── DocumentItem.java
│ └── DocumentSync.java
├── catalog/ <- Fachliche Deteils zum Catalog Konzept 
│ ├── CatalogView.java
│ ├── CatalogApi.java ← JAX-RS Einstiegspunkt
│ ├── CatalogResource.java ← JSON-B Wrapper
│ ├── CatalogEdit.java
│ └── CatalogList.java
├── item/ <- Fachliche Deteils zum Item Konzept 
│ ├── ItemView.java
│ ├── IssueItemView.java
│ ├── RequirementItemView.java
│ ├── DocumentItemView.java
│ ├── ItemSyncTask.java ← Fachlicher Task direkt im Root
│ ├── ItemCleanupTask.java ← Fachlicher Task direkt im Root
│ ├── ItemApi.java
│ ├── ItemResource.java
│ └── ItemList.java
├── Catalog.java
├── Item.java
└── Synchronization.java
```

---
### Variante B: Einheitliches `api`-Paket für alle Einstiegspunkte

Hier werden alle Schnittstellen in einem dedizierten `api`-Paket gesammelt, das selbst ein Business-Konzept darstellt.  
Tasks bleiben jedoch im Root, da sie **fachliche Prozesse** sind und kein API-Subkonzept.

```
com.test.catalog
├── api/
│ ├── user/ 
│ │ ├── catalog/ <- Fachlicher Begriff im Menu
│ │ │ ├── CatalogView.java
│ │ │ ├── CatalogForm.java
│ │ │ └── CatalogTable.java
│ │ ├── item/ <- Fachlicher Begriff im Menu
│ │ │ ├── ItemView.java
│ │ │ ├── IssueItemView.java
│ │ │ ├── RequirementItemView.java
│ │ │ ├── DocumentItemView.java
│ │ │ ├── ItemForm.java
│ │ │ └── ItemTable.java
│ │ ├── DataTable.java
│ │ ├── DataForm.java
│ │ └── CatalogMenu.java
│ ├── resource/
│ │ ├── CatalogApi.java
│ │ ├── CatalogResource.java
│ │ ├── ItemApi.java
│ │ └── ItemResource.java
│ └── service/
│ └── ExternalServiceApi.java
├── app/ ← Startup, DI, Config
│ ├── CatalogApplication.java (handle or register hooks on Startup event, prepare all **jakarta.inject.*** CDI injections/producers)
│ ├── CatalogResourceApplication.java (enable JAX-RS @ApplicationPath)
│ └── CatalogFacesConfig.java (enable or configure JSF)
├── jira/ <- Fachliche Deteils zum Item Konzept bezüglich eines externen Business-Kontextes 
│ ├── Jira.java
│ ├── IssueItem.java
│ └── StorySync.java
├── codebeamer/ <- Fachliche Deteils zum Item Konzept bezüglich eines externen Business-Kontextes 
│ ├── Codebeamer.java
│ ├── RequirementItem.java
│ └── RequirementSync.java
├── doors/ <- Fachliche Deteils zum Item Konzept bezüglich eines externen Business-Kontextes 
│ ├── Doors.java
│ ├── DocumentItem.java
│ └── DocumentSync.java
├── ItemCleanupTask.java ← Fachlicher Task im Root
├── ItemSyncTask.java ← Fachlicher Task im Root
├── Catalog.java
├── Item.java
└── Synchronization.java
```

---
## Paketstruktur für Clean- und DDD-Architektur (Implementierung mit Jakarta EE oder Spring)

---
### Single Artefak Project
(In einem Single Artefakt Projekt bleibt alles in einem Deployment-Artefakt, aber mit klarer fachlicher Struktur.)
```
com.test.catalog
├── app/
│ ├── CatalogApplication.java
│ ├── CatalogConfig.java
│ └── DependencyProvider.java
├── jira/
│ ├── Jira.java
│ ├── IssueItem.java
│ └── StorySync.java
├── codebeamer/
│ ├── Codebeamer.java
│ ├── RequirementItem.java
│ └── RequirementSync.java
├── doors/
│ ├── Doors.java
│ ├── DocumentItem.java
│ └── DocumentSync.java
├── catalog/
│ ├── CatalogView.java
│ ├── CatalogApi.java
│ ├── CatalogResource.java
│ └── CatalogList.java
├── item/
│ ├── ItemView.java
│ ├── IssueItemView.java
│ ├── RequirementItemView.java
│ ├── DocumentItemView.java
│ └── ItemList.java
├── ItemCleanupTask.java
├── ItemSyncTask.java
├── Catalog.java
├── Item.java
└── Synchronization.java
```

---
### Multi Artefakt Project

(In einem Multi-Artefakt Projekt werden die Business-Kontexte in separate Module aufgeteilt, die einzeln versioniert und ggf. deployt werden können, aber die Regeln bleiben gleich.)

```
catalog-core (Modul)
├── com.test.catalog.core
│ ├── Catalog.java
│ ├── Item.java
│ └── Synchronization.java
```

```
catalog-jira (Modul)
├── com.test.catalog.jira
│ ├── Jira.java
│ ├── IssueItem.java
│ └── StorySync.java
```

```
catalog-codebeamer (Modul)
├── com.test.catalog.codebeamer
│ ├── Codebeamer.java
│ ├── RequirementItem.java
│ └── RequirementSync.java
```

```
catalog-doors (Modul)
├── com.test.catalog.doors
│ ├── Doors.java
│ ├── DocumentItem.java
│ └── DocumentSync.java
```

```
catalog-api (Modul) ← wenn Business API verlangt
├── com.test.catalog.api
│ ├── CatalogApi.java
│ └── ItemApi.java
```

```
catalog-ui (Modul) ← wenn Business UI verlangt
├── com.test.catalog.ui
│ ├── catalog/
│ │ ├── CatalogView.java
│ │ ├── CatalogForm.java
│ │ └── CatalogTable.java
│ ├── item/
│ │ ├── ItemView.java
│ │ ├── IssueItemView.java
│ │ ├── RequirementItemView.java
│ │ └── DocumentItemView.java
```

```
catalog-root (Modul)
├── com.test.catalog
│ ├── ItemCleanupTask.java
│ ├── ItemSyncTask.java
│ └── (optional) Synchronization.java
```

**Visualisierung: Modul-Abhängigkeiten**

```mermaid
graph TD
    subgraph Core[Main Kontexte Business]
        A[Catalog]
        B[Item]
        C[Synchronization]
    end

    subgraph ExternalContexts[Externe Business-Kontexte]
        J[Jira]
        CB[Codebeamer]
        D[Doors]
    end

    subgraph API[Business API Schnittstellen]
        API_Resource[ApiResource]
        API_User[ApiUser]
    end

    subgraph UI[User Interface]
        U_Catalog[CatalogView]
        U_Item[ItemView]
    end

    %% Abhängigkeitspfeile, nur wenn geschäftlich verlangt
    J --> A
    CB --> A
    D --> A

    UI --> A
    UI --> B

    API_Resource --> A
    API_Resource --> B

    API_User --> U_Catalog
    API_User --> U_Item

    Core --> Synchronization
    B --> C
    A --> C
```

**Erläuterung: Modul-Abhängigkeiten**

Core (Business Core) enthält die fundamentalen Domänenobjekte: Catalog, Item, Synchronization. Diese sollten keine Abhängigkeit auf Module außerhalb des Cores haben (Regel 1 & 3).

- Externe Business-Kontexte wie Jira, Codebeamer, Doors dürfen vom Core abhängig sein — z. B. wenn Sync-Logik benötigt wird. Nicht umgekehrt.
- UI-Module (z. B. CatalogView, ItemView) sind fachliche Darstellungskonzepte, erlauben Abhängigkeit auf Core, aber nicht umgekehrt.
- API-Module (z. B. ApiResource, ApiUser) erlauben ebenfalls Abhängigkeit auf Core, da API Schnittstellen oft Daten oder Aktionen aus dem Core exposen.
- Es wird nicht vorgesehen, dass UI Module auf externe Kontexte wie Jira direkt zugreifen, sofern das Business es nicht verlangt. Wenn UI z. B. Daten von Jira darstellen muss, dann geschieht das über eine fachlich benannte API bzw. über Sync-Klassen im Jira Paket.


| Regel                                                                    | Anwendung im Beispiel                                                                                                         |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| *Regel 1*: Kein Paket sollte von seinen Sub-Paketen abhängen             | Core hat keine Abhängigkeit zu UI oder API, nur umgekehrt. Subpakete (z. B. UI, API) hängen auf Core.                           |
| *Regel 2*: Sub-Pakete führen keine neuen Konzepte ein                    | UI, API, Externe Kontexte sind bestehende Business-Konzepte, Sub-Pakete gliedern nur Details (View, Resource etc.).             |
| *Regel 3*: Pakete spiegeln Business-Konzepte, keine technischen Begriffe | Namen wie `Jira`, `Item`, `Catalog`, `ApiUser`, `CatalogView` sind fachlich. Kein `infrastructure`, kein `task` Paketname, etc. |


---

## Fazit

Eine gute Packaging-Strategie geht über reine Ordnung hinaus.  
Sie **transportiert Wissen, verdeutlicht Business-Konzepte** und schützt vor wachsender Komplexität.  

- Das `app`-Paket bleibt **rein technisch**.  
- APIs sind **fachliche Einstiegspunkte**.  
- Tasks gehören **in den Root**, da sie keine eigenständigen Business-Konzepte sind.  
- Sub-Packages sind **Detailierungen**, keine neuen Konzepte.  

So entsteht eine **kontextgetriebene, fachlich nachvollziehbare** Architektur.  
