# **Richtlinien: Strikte Objektorientierte Programmierung (OOP) / "Object Thinking"**

Diese Richtlinien müssen **immer** befolgt werden, um anämische Domänenmodelle, "Naked Data" und unnötige Kopplungen zu vermeiden. Die höchste Priorität liegt auf **Kapselung** und der **Verlagerung von Verhalten** in die Objekte, deren Zustand betroffen ist.

## **1. Das Domänenmodell (Entities / Value Objects)**

### **1.1 Getter und Setter (Naked Data)**

* **Anti-Muster (Verboten):** Die Verwendung von Gettern und Settern, die den internen Zustand freilegen.  
* **Richtige OOP-Umsetzung (Erforderlich):** **Tell, Don't Ask.** Objekte müssen Aktionen (Verhalten) implementieren, anstatt ihren Zustand preiszugeben.

### **1.2 DTOs (Data Transfer Objects)**

* **Anti-Muster (Verboten):** Reine Datencontainer zwischen Schichten.  
* **Richtige OOP-Umsetzung (Erforderlich):** Vermeiden Sie DTOs. Die Kommunikation muss über **polymorphe Verträge** oder **Methodenaufrufe** erfolgen, die die Kapselung respektieren.

### **1.3 Datenbank/I/O-Kopplung**

* **Anti-Muster (Verboten):** Die Aufteilung der Verantwortung für Persistenz und Darstellung auf externe Services (Repository, Service Layer).  
* **Richtige OOP-Umsetzung (Erforderlich):** Domänen-Objekte **übernehmen zwingend** die Verantwortung für ihre **Persistenz** und **Darstellung** (UI of Objects). Die direkte Kopplung an Infrastruktur-Sinks (z.B. java.sql.Connection) oder UI-Komponenten (z.B. Tree) ist der **korrekte Weg**, sofern die Methode die Kapselung wahrt und **Verhalten delegiert** (Tell, Don't Ask). Es ist verboten, diese Klassen zu verwenden, um Daten *herauszuziehen* (Getters).

### **1.4 Mutable State & Immutability (Unveränderlichkeit)**

* **Anti-Muster (Verboten):** Objekte, deren Zustand nach der Konstruktion änderbar ist.  
* **Richtige OOP-Umsetzung (Erforderlich):** Bevorzugen Sie **unveränderliche (Immutable)** Objekte. Ein Objekt muss entweder **read-only** sein, oder Methoden, die den Zustand ändern (z.B. complete()), müssen ein **neues Objekt** zurückgeben.

### **1.5 Konstruktoren, Gültigkeit und Vollständigkeit (Single Primary Constructor)**

* **Anti-Muster (Verboten):** Mehrere Konstruktoren. Leere (Default) Konstruktoren ohne Argumente. Konstruktoren, die keine Gültigkeitsprüfung (Validierung) der Argumente durchführen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen **immer in einem gültigen und vollständigen Zustand** erzeugt werden (Completeness). Jede Klasse **muss nur einen einzigen primären Konstruktor** besitzen, der alle notwendigen Abhängigkeiten und Argumente entgegennimmt und deren Gültigkeit (z.B. Nicht-Null, korrekte Wertebereiche) sofort prüft.

### **1.6 Kapselung von Primitiven und Collections**

* **Anti-Muster (Verboten):** Direkte Verwendung und Offenlegung von Primitiven (z.B. String, int) für Fachkonzepte (z.B. E-Mail-Adresse, Preis) oder die Verwendung nativer Collections (z.B. List<Task>, Map<K, V>) als Objektattribute oder Rückgabewerte.  
* **Richtige OOP-Umsetzung (Erforderlich):**  
  * **Value Objects:** Fachliche Primitive müssen in **Value Objects** gekapselt werden (z.B. EmailAddress statt String, Price statt double). Diese Value Objects sind immutable und enthalten die Gültigkeitslogik.  
  * **Custom Collections:** Native Collections müssen in **Custom Collection**-Objekten gekapselt werden (z.B. Tasks statt List<Task>). Diese Custom Collections enthalten das Verhalten für die Gruppe von Objekten (z.B. tasks.find(id)) und verhindern das Auslaufen von **"Naked Data"**.

**Beispiele für die Konstruction von Objects, welche nicht modifizierbare collection bereitstellen.**
```java
// no decorator  
interface Posts extends Iterator<Post> {   
    Post add(Date date, String title); // return a new post  
    Posts filter(Predicate<Post> filter); // return a new subset  
    Posts ofAuthor(String authorId); // return a new subset  
    Posts ofUser(String userId); // return a new subset  
}
```
```java
// with decorator  
interface Posts {   
    Iterable<Post> iterate();   
    Post add(Date date, String title);   
}
// based on predicate
public class FiltredPosts implements Posts {  
    public FiltredPosts(Predicate<Post> filter){//...}   
    public Iterable<Post> iterate(){//...}   
    public Post add(Date date, String title){//...}   
}
// based on authorId (Relation to Authors)
public class AuthorPosts implements Posts {   
    public AuthorPosts(String authorId){//...}   
    public Iterable<Post> iterate(){//...}    
    public Post add(Date date, String title){//...}   
}
// based on userId (Relation to Users)
public class UserPosts implements Posts {
    public UserPosts(String userId){//...}   
    Iterable<Post> iterate(){//...by user id}   
    Post add(Date date, String title){//...}   
}
```
### **1.6.1 Referenzielle Integrität & Aggregate**
**Anti-Muster (Verboten):** Das Zurückgeben der internen Collection oder das Zulassen von direkten, externen Modifikationen (z.B. `tasks.getCollection().add(newTask)`). Das Verletzen der Kapselung durch externe Manipulation der Kindobjekte.

**Richtige OOP-Umsetzung (Erforderlich):** Das Custom Collection Object muss als Aggregate Root fungieren. Alle Hinzufügungen, Löschungen oder Modifikationen müssen ausschließlich über Befehlsmethoden (Commands) auf dem Aggregate Root erfolgen (z.B. `posts.add(newPost)`). Dies gewährleistet die Referenzielle Integrität und die konsistente Validierungslogik des gesamten Aggregats. Die interne Repräsentation der Collection darf niemals außerhalb des Aggregate Roots sichtbar sein.

### **1.7 Vermeidung von Null-Werten**

* **Anti-Muster (Verboten):** Rückgabe oder Verwendung von null. Verwendung prozeduraler Null-Checks (if (x == null)).  
* **Richtige OOP-Umsetzung (Erforderlich):** **Das Null Object Pattern** muss verwendet werden. Objekte dürfen niemals null zurückgeben; stattdessen müssen sie eine polymorphe, "leere" Implementierung (NullTask, UnknownUser) zurückgeben, die alle Methodenaufrufe ohne Seiteneffekte verarbeitet. Dadurch entfällt die Notwendigkeit von null-Überprüfungen durch den Konsumenten.

### **1.8 Identität (Entities vs. Value Objects)**

* **Anti-Muster (Verboten):** Zwei Objekte mit demselben Zustand werden als gleich betrachtet, obwohl sie verschiedene Entitäten sind (oder umgekehrt).  
* **Richtige OOP-Umsetzung (Erforderlich):** Ein Objekt muss entweder eine **stabile, eindeutige Identität (ID)** besitzen (Entity), wodurch die equals()-Methode nur auf der ID basiert, oder es darf **keine stabile ID** besitzen (Value Object), wodurch die equals()-Methode auf **allen** Attributen basiert. Ein Objekt muss eine dieser beiden Rollen klar erfüllen.

### **1.9 Simplicity & Größe**

* **Anti-Muster (Verboten):** Monolithische Klassen mit Dutzenden von Methoden oder Attributen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Ein Domänenobjekt muss **einfach (Simple)** bleiben. Es darf maximal **zehn öffentliche Methoden** und maximal **fünf Instanzattribute** besitzen. Alles, was diese Grenzen überschreitet, muss in ein separates, delegierendes Objekt (Proxy oder Value Object) extrahiert werden (Verletzung des SRP).

### **1.10 Testbarkeit & Keine internen Seiteneffekte**

* **Anti-Muster (Verboten):** Logik verwendet direkt nicht-deterministische oder statische globale Ressourcen (z.B. Aufruf von new Date(), Math.random(), System.getenv()).  
* **Richtige OOP-Umsetzung (Erforderlich):** Domänenobjekte müssen **vollständig testbar** sein. Nicht-deterministische Abhängigkeiten (wie Uhrzeit oder Zufall) müssen als **Interfaces über den Konstruktor injiziert** werden (Dependency Injection, z.B. Clock statt new Date()). Das Objekt darf keine direkten internen Seiteneffekte erzeugen, die nicht einfach mockbar sind.

## **2. Architektur und Design: Fokus auf Kapselung und Pakete**

Das Konzept horizontaler, strikter Schichten (wie Domain, Application, Infrastructure, Usecases, Controller, Bean, Interactors in klassischem DDD) ist in der strikten OOP **verboten**. Die Architektur muss auf **kontextgesteuerter Paketierung (Context-Driven Packaging)** basieren, bei der die Abhängigkeiten vertikal über Objekte und deren Kapselung organisiert werden. Der Fokus liegt auf der **Delegation von Verantwortung** und nicht auf der Trennung in "Layer".

**Regeln für Pakete:**

1. * Root Package = Domain Core Klassen/Interfaces, unabhängig von allem  
   * Externe Kontexte = Adapter, abhängig von Core Klassen/Interfaces  
2. Sub-Pakete führen keine neuen Konzepte ein, nur Details  
   * jira/JiraClient = Detail der Jira-Integration  
   * Keine neuen fachlichen Konzepte in Sub-Packages  
3. **Pakete spiegeln ausschließlich Business-Aspekte wider, technische Paketnamen sind verboten**  
   * ✅ jira, codebeamer, doors (aus Context-Diagramm)  
   * ❌ service, repository, controller (technische Details)  
4. **Abhängigkeitskontrolle (Composition over Inheritance):** Objekte müssen von **Interfaces** (implements) abhängen, nicht von konkreten Klassen. Nutzen Sie **Komposition** statt Vererbung (außer in eng definierten Taxonomien), um Flexibilität zu gewährleisten.

Beispiel für äußere Komposition (Einstiegspunkt):
```java
// Abstraktion für die Anwendung  
App.java  
app/                      // Package für Details der App-Abstraktion  
└── ConsoleApp.java       // Einstiegspunkt, implementiert App, äußere Komposition

// Die Komposition (Dependency Injection ohne Container) erfolgt  
// im äußersten Rand der Anwendung (z.B. in 'main' oder 'startup' Methode):

new ConsoleApp(  
    new InvoiceBook(  
        new Invoices(),  
        new CalculatedTax()  
    ),  
    new Payment(  
        new Payer("Alice"),  
        new Recipient("Bob"),  
        new Amount(100, new Currency("EUR"))  
    ),  
    new CustomerDirectory(...)  
);
```

### **2.1 Statische Methoden/Factories (Prestructors)**

* **Anti-Muster (Verboten):** Statische Methoden für den Datenzugriff, Dependency Injection oder Business-Logik.  
* Richtige OOP-Umsetzung (Erforderlich): Statische Methoden sind nur erlaubt für zwei Zwecke:

* * **Prestructors (Benannte Konstruktoren):** Sie dienen dazu, Argumente vorzubereiten (z.B. Validierung, Typkonvertierung) und rufen sofort den einzigen primären Konstruktor (siehe 1.5) auf, um das Objekt zu erzeugen. Sie dürfen keine I/O-Logik ausführen. **Beispiel:** `User.withNameAndEmail("John", "j@e.com")`.

* * **Vertical Decorators (Composition Root):** Diese Methoden dienen als Composition Root (häufig am äußersten Rand der Anwendung) und sind der einzige Ort, an dem der vollständige vertikale Objekt-Graph einer Domänen-Entität aufgebaut wird. Sie tun dies, indem sie die notwendigen Decorator-Ketten (Logging, Caching, Transaktionalität, Persistenz-Delegation) aus dem Infrastruktur-Layer zusammensetzen und das Domänen-Interface zurückgeben. Sie sind der korrekte Ersatz für Dependency-Injection-Container. Sie dürfen dabei Infrastruktur-Objekte instanziieren und injizieren.

### **2.2 Service Layer**

* **Anti-Muster (Verboten):** "Allmächtige Service-Layer", die nur Daten zwischen Repository und DTO hin- und herschieben.  
* **Richtige OOP-Umsetzung (Erforderlich):** Die **Verantwortung** muss direkt an die entsprechenden Objekte delegiert werden.

### **2.3 Klassisches ORM/Active Record**

* **Anti-Muster (Verboten):** Die Nutzung von ORMs, die Getter/Setter erfordern und Daten von Verhalten trennen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Die Verantwortung für das Speichern/Laden sollte **gekapselt** und über eine **abstrakte Collection/Storage-Abhängigkeit** an das Domänenobjekt delegiert werden.

### **2.4 Aktion in der Ressource/Controller**

* **Anti-Muster (Verboten):** Controller/Ressourcen, die Business-Logik oder I/O-Formatierung enthalten.  
* **Richtige OOP-Umsetzung (Erforderlich):** Ressourcen/Controller müssen schlank sein. Die gesamte Logik (Laden, Validieren, I/O-Formatierung) muss an **dekorierende Objekte** delegiert werden (z.B. RestTask, MediaTask, HyperTask oder RsTask)

### **2.5 Abhängigkeitsregel (Dependency Rule) und Anwendungsfälle**

* **Anti-Muster (Verboten):** Domänenobjekte (Entities, Value Objects) referenzieren Implementierungsdetails der Infrastruktur (z.B. Spring, Jakarta EE, konkrete Datenbanktreiber).  
* **Richtige OOP-Umsetzung (Erforderlich):** Die Architektur muss die **Abhängigkeitsregel** der Clean Architecture strikt befolgen. Die Abhängigkeitsvektoren müssen immer nach innen zeigen: **Infrastruktur muss von der Domäne abhängen, niemals umgekehrt.** Die **Anwendungsfälle (Use Cases/Interactors)** sind in OOP das zentrale Orchestrierungs-Objekt der Anwendung. Sie initiieren Aktionen und nutzen **polymorphe Verträge (Interfaces)**, um mit der Infrastruktur (z.B. Persistenz) zu kommunizieren, ohne deren konkrete Implementierung zu kennen.

## **3. Umsetzung und Polymorphie**

* **Polymorphe Dekoration:** Infrastruktur-Abhängigkeiten (z.B. das Schreiben einer JSON-Antwort) müssen durch **Decorator-Objekte** gekapselt werden, die das Domänen-Objekt zur Darstellung auffordern.  
* **Abhängigkeitsinjektion:** Implementieren Sie **Dependency Injection ohne Container**. Alle Abhängigkeiten müssen über den **Konstruktor** injiziert werden.  
* **Verträge (Interfaces):** Interfaces dienen dazu, **Verhalten** (Verantwortung) zu definieren, nicht nur Datenstrukturen.

### **3.1 Horizontale Zerlegung (Decorator Pattern zur Kapselung von Querschnittsbelangen)**

* **Anti-Muster (Verboten):** Monolithische Implementierung, die fachliche und nicht-fachliche Aspekte (Logging, Caching, Transaktionen) in einer Klasse mischt, oder die Verwendung von Vererbung zur Implementierung von Verhalten.  
* **Richtige OOP-Umsetzung (Erforderlich):** **Horizontale Zerlegung** aller nicht-fachlichen (cross-cutting) Belange mittels **Decorator Pattern**. Jede zusätzliche Verantwortung (z.B. Logging, Caching, Zugriffskontrolle) muss in einem eigenen, unveränderlichen Objekt gekapselt werden, das das gleiche Interface implementiert und das ursprüngliche Objekt im Konstruktor erhält.

**Beispiel für Komposition durch Dekoration (inkl. UI/I/O-Delegation):**
```java
// Annahme: Das Domänen-Interface enthält sowohl Business- als auch I/O-Verhalten  
// interface Task { void complete(); void presentTo(IOPresenter sink); }

// 1. Das End-Objekt, das nur die reine Business-Logik enthält  
Task businessTask = new DatabaseTask(connection);   
// Hier wird die DB-Verantwortung direkt an das Domänenobjekt gekoppelt (siehe 1.3)

// 2. Horizontale Decorators (Querschnittsbelange, implementieren das gleiche Interface)  
Task decoratedTask = new CachedTask(  
    new LoggedTask(  
        new SecuredTask(  
            businessTask  
        )  
    )  
);

// 3. Finaler Decorator für das Oupput-Verhalten (Polymorphe Darstellung)  
// Dieser Decorator KANN zusätzlich Events auslösen oder die finale I/O-Logik übernehmen  
Task resourceTask = new PresentableTask(  
    decoratedTask,  
    new JsonResponseWriter() // Das Ziel (Sink) der I/O-Delegation  
);

// --- Nutzung durch den Controller/Client ---

// 3.1. Aufruf der Business-Logik (geht durch Presentable, Cached, Logged, Secured, DB)  
resourceTask.complete();

// 3.2. Delegation der I/O-Verantwortung (OI of Objects):  
// Das Domänenobjekt steuert die Darstellung und schreibt direkt in den Sink (ResponseWriter).  
resourceTask.printTo(httpServletResponse.getWriter());

// 4. Finaler Decorator für das UI-Verhalten (Polymorphe Darstellung)  
// Dieser Decorator KANN zusätzlich Events auslösen oder die Animation-Logik übernehmen

Task displayed = new DisplayedTask(  
    decoratedTask,  
    connection // Das Ziel (Sink) der I/O-Delegation von und für die UI Komponente  
);

// --- Nutzung durch den Controller/Client ---

// 4.1. Aufruf der Business-Logik (geht durch Presentable, Cached, Logged, Secured, DB)  
displayed.complete();

// 4.2. Delegation der I/O-Verantwortung (UI of Objects):  
// Das Domänenobjekt steuert die Darstellung und schreibt direkt in den Sink (ResponseWriter).  
displayed.displayTo(new ObservedUI(uiComponent)); 
```

### **3.2 UI of Objects (Polymorphe Darstellung & Observer)**

* **Anti-Muster (Verboten):** Die UI-Komponente (Controller, View) fragt das Domänenobjekt nach Daten (per Getter) und ist dann für die Formatierung verantwortlich. (Ask statt Tell).  
* **Richtige OOP-Umsetzung (Erforderlich):** Das Domänenobjekt muss die **volle Verantwortung für seine Darstellung** übernehmen. Die Methode erhält das **Ziel (Sink)**, also die konkrete UI-Komponente (z.B. PrimeFaces Tree oder HtmlOutputText), und delegiert an diese die notwendigen Anweisungen, um sich selbst darzustellen. Querschnitts-Verhalten (wie UI-Events) werden über Decorator-Ketten integriert.

**Beispiel für UI-Integration und Event-Auslösung:**
```java
// Annahme: Tree Interface im Infrastruktur-Package (Definiert den Sink/das Ziel)  
public interface Tree {  
    void setTitle(String name);  
    // Das Domänenobjekt delegiert die Event-Registrierung an das UI-Objekt (Tell!)  
    void onClick(Runnable action);   
    // ... weitere UI-spezifische Methoden  
}

// Folder Interface im Domain-Package  
public interface Folder {  
    // Erzählt der UI-Komponente (Tree), wie sie sich selbst darstellen soll und   
    // welche Aktionen bei Interaktion (z.B. Klick) auszuführen sind.  
    void displayTo(Tree treeComponent);   
    void archive(); // Beispiel für Domain-Verhalten  
}

// Implementierung  
public class TaskFolder implements Folder {  
    private final String id;
    private final DataSource dataSource;    
    // ... weitere interne Attribute

    public TaskFolder(DataSource dataSource, String id) { this.dataSource = dataSource; this.id = id; }

    @Override  
    public void displayTo(Tree treeComponent) {  
        // 1. Delegation der Daten:  
        treeComponent.setTitle("Task Directory");   
        treeComponent.setValue(new FolderTreeNode(dataSource, id));   
        // 2. Delegation der Event-Auslösung:  
        // Das Domänenobjekt injiziert sein eigenes Verhalten in die UI-Komponente.  
        // Das UI-Objekt (Tree) führt dann bei einem Klick diese Methode aus.  
        treeComponent.onClick(this::archive);

        // 3. Delegation der Darstellung (Styling):
        treeComponent.setNodeColor("green");   
    }

    @Override  
    public void archive() {  
        System.out.println("INFO: Folder '" + this.name + "' has been archived.");  
        // Hier folgt die eigentliche Business-Logik (z.B. Persistenz-Aufruf)  
    }  
}
```
### **3.3 Immutable Proxies und Delegation**

* **Anti-Muster (Verboten):** Monolithische Klassen, die alle Logik selbst enthalten, ohne Delegation, oder die Implementierungsdetails (z.B. Caching, Persistenz) offenlegen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen als **unveränderliche Proxies** (oder Fassaden) entworfen werden. Sie delegieren ihre Komplexität an interne, private Komponenten (andere Objekte), die über den Konstruktor injiziert werden. Das Domänen-Objekt ist der einzige **Vertrag (Interface)** für seine Konsumenten.

**Beispiel für den Immutable Proxy:**
```java
// 1. Vertikales Interface (Domänen-Verhalten)  
interface Document {  
    String title();  
    void title(String text);  
}

// 2. Vertikales Interface für die technische Schicht (Persistenz-Verhalten)  
interface DocumentStorage { /* ... */ } 

// 3. Der Unveränderliche Proxy (Encapsulates nur die ID und Storage-Kette)  
// @Immutable  
class StoredDocument implements Document {    
    private final DocumentStorage externalStorage;  
    private final int id;

    public StoredDocument(int id, DocumentStorage storage) {    
        this.id = id;    
        // Injektion des gesamten Storage/Caching-Graphen  
        this.externalStorage = storage;  
    }

    // Delegation des Verhaltens an die injizierte Kette  
    @Override  
    public String title() {    
        return this.externalStorage.readTitle(this.id);    
    }

    @Override  
    public void title(String text) {    
        this.externalStorage.writeTitle(this.id, text);    
    }

    // Identität basiert NUR auf der ID, nicht auf dem Inhalt  
    @Override    
    public boolean equals(Object doc) {    
        return doc instanceof StoredDocument && StoredDocument.class.cast(doc).id == this.id;    
    }    
}
```

### **3.4 Accessibility & Private Logik**

* **Anti-Muster (Verboten):** Private Methoden, die komplexe Business-Logik enthalten, die mehr als ein oder zwei einfache Schritte umfasst.  
* **Richtige OOP-Umsetzung (Erforderlich):** Ein Objekt muss **zugänglich (Accessible)** sein. Private Methoden dürfen nur triviale, interne Implementierungsdetails (z.B. Hilfsmethoden für String-Formatierung oder einfache Berechnungen) enthalten. **Komplexe private Logik muss sofort in ein separates, privates, injiziertes oder inneres Objekt extrahiert werden** (Delegation), um die Single Responsibility und die Testbarkeit zu wahren.

## **4. Naming Conventions (Objektbenennung)**

### **4.1 Nomen und Identität**

* **Anti-Muster (Verboten):** Benennung von Objekten nach ihrer *Fähigkeit* oder *Funktion* (`Processor, Manager, Validator, Service, Utility`).  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen nach dem **Ding (Nomen)** benannt werden, das sie repräsentieren. *Beispiele:* `Order, Payment, Invoice, Database, User`. Sie müssen eine klare **Identität** haben.

### **4.2 Funktions-Suffixe**

* **Anti-Muster (Verboten):** Die Endungen `-er, -or, -able, Client, Data` oder Info (z.B. `FileWriter`, `OrderProcessor`, `TaskClient`). Diese Suffixe deuten auf prozedurale Zerlegung oder "Naked Data" hin.  
* **Richtige OOP-Umsetzung (Erforderlich):** Vermeiden Sie diese Suffixe auf Domänen- oder Kerngeschäftsobjekten. *Beispiele für Korrekturen:* Statt FileWriter  File. Statt DataValidator  Rule. Statt `JiraClient` einfach `Jira` oder Http statt `HttpClient`.

### **4.3 Compound Names**

* **Anti-Muster (Verboten):** Zusammengesetzte Namen (Compound Names) wie `UserAccountManager`, `DatabaseConnectionHolder`, da sie oft auf eine Aggregation von Verantwortlichkeiten hindeuten (Code Smell).  
* **Richtige OOP-Umsetzung (Erforderlich):** Streben Sie nach einfachen, präzisen Namen, die nur eine einzige Verantwortung widerspiegeln. *Beispiele für Korrekturen:* Statt UserAccountManager  User oder Account. Statt `DatabaseConnectionHolder`  `Connection`.

## **5. Methodik und Namenskonventionen**

### **5.1 Single Action Rule (Einzelaktion / Exclusivity)**

* **Anti-Muster (Verboten):** Methoden, die mehrere Verantwortlichkeiten zusammenfassen. *Beispiel:* `createAndPersistUser(User u)`.  
* **Richtige OOP-Umsetzung (Erforderlich):** Jede Methode muss eine **einzelne, klar definierte und abgeschlossene Aktion** ausführen, die sich auf das Objekt selbst bezieht. Das Objekt muss **exklusiv** nur diese eine Hauptverantwortung verfolgen. *Beispiel:* `user.persist()`.

### **5.2 Command Naming (Mutatoren)**

* **Anti-Muster (Verboten):** Verwendung von update, set, modify. *Beispiel:* `updateThePrice(NewPrice p)`.  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die einen **Seiteneffekt** haben oder den Zustand ändern (oder ein neues, geändertes Objekt zurückgeben), müssen als **Befehle** benannt werden (Infinitiv/Imperativ). *Beispiele:* `price(NewPrice p)`, `increase(Amount a)`, `complete()`.

### **5.3 Query Naming (Accessoren)**

* **Anti-Muster (Verboten):** Verwendung von `get` als Präfix. *Beispiel:* `getFirstName()`.  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die keinen Zustand ändern (keine Seiteneffekte), müssen als **Abfragen** benannt werden und klar ihre Natur kennzeichnen. *Beispiele:* `firstName(), isCompleted(), hasPermissions(), toXml()`.

### **5.4 Method Chaining (Zugriff auf Objekte)**

* **Anti-Muster (Verboten):** Aufrufe wie user.getGroup().getRights() (Verstöße gegen 5.3).  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die andere Domänenobjekte (`Value Objects, Entities, Collections`) liefern, müssen nach dem **Namen des zurückgegebenen Objekts** benannt werden. Dies fördert lesbare, flüssige Aufrufe (Fluent API) und unterstützt die Kapselung. *Beispiel:* `user.group().rights()`. Dabei werden in der Aufrufkette keine internen Variablen delegiert, sondern neu Objekte.

**Zusammenfassend:** Jede Code-Einheit muss eine klare, einzige Verantwortung haben. Das Domänenobjekt ist König.
