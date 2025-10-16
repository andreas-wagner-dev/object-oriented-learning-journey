# **Richtlinien strikte Objektorientierte Programmierung (OOP) / "Object Thinking"**

Diese Richtlinien müssen **immer** befolgt werden, um anämische Domänenmodelle, "Naked Data" und unnötige Kopplungen zu vermeiden. Die höchste Priorität liegt auf **Kapselung** und der **Verlagerung von Verhalten** in die Objekte, deren Zustand betroffen ist.

## **1\. Das Domänenmodell (Entities / Value Objects)**

### **1.1 Getter und Setter (Naked Data)**

* **Anti-Muster (Verboten):** Die Verwendung von Gettern und Settern, die den internen Zustand freilegen.  
* **Richtige OOP-Umsetzung (Erforderlich):** **Tell, Don't Ask.** Objekte müssen Aktionen (Verhalten) implementieren, anstatt ihren Zustand preiszugeben.

### **1.2 DTOs (Data Transfer Objects)**

* **Anti-Muster (Verboten):** Reine Datencontainer zwischen Schichten.  
* **Richtige OOP-Umsetzung (Erforderlich):** Vermeiden Sie DTOs. Die Kommunikation muss über **polymorphe Verträge** oder **Methodenaufrufe** erfolgen, die die Kapselung respektieren.

### **1.3 Datenbank/I/O-Kopplung**

* **Anti-Muster (Verboten):** Die Aufteilung der Verantwortung für Persistenz und Darstellung auf externe Services (Repository, Service Layer).  
* **Richtige OOP-Umsetzung (Erforderlich):** Domänen-Objekte **übernehmen zwingend** die Verantwortung für ihre **Persistenz** und **Darstellung** (UI of Objects). Die direkte Kopplung an Infrastruktur-Sinks (z.B. java.sql.Connection) oder UI-Komponenten (z.B. Tree) ist der **korrekte Weg**, sofern die Methode die Kapselung wahrt und **Verhalten delegiert** (Tell, Don't Ask). Es ist verboten, diese Klassen zu verwenden, um Daten *herauszuziehen* (Getters).

### **1.4 Mutable State**

* **Anti-Muster (Verboten):** Objekte, deren Zustand nach der Konstruktion änderbar ist.  
* **Richtige OOP-Umsetzung (Erforderlich):** Bevorzugen Sie **unveränderliche (Immutable)** Objekte. Methoden, die den Zustand ändern (z.B. complete()), müssen ein **neues Objekt** zurückgeben.

### **1.5 Konstruktoren und Gültigkeit**

* **Anti-Muster (Verboten):** Leere (Default) Konstruktoren ohne Argumente. Konstruktoren, die keine Gültigkeitsprüfung (Validierung) der Argumente durchführen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen **immer in einem gültigen Zustand** erzeugt werden. Der Konstruktor **muss** alle notwendigen Argumente entgegennehmen und deren Gültigkeit (z.B. Nicht-Null, korrekte Wertebereiche) sofort prüfen.

### **1.6 Kapselung von Primitiven und Collections**

* **Anti-Muster (Verboten):** Direkte Verwendung und Offenlegung von Primitiven (z.B. String, int) für Fachkonzepte (z.B. E-Mail-Adresse, Preis) oder die Verwendung nativer Collections (z.B. List\<Task\>, Map\<K, V\>) als Objektattribute oder Rückgabewerte.  
* **Richtige OOP-Umsetzung (Erforderlich):**  
  * **Value Objects:** Fachliche Primitive müssen in **Value Objects** gekapselt werden (z.B. EmailAddress statt String, Price statt double). Diese Value Objects sind immutable und enthalten die Gültigkeitslogik.  
  * **Custom Collections:** Native Collections müssen in **Custom Collection**\-Objekten gekapselt werden (z.B. Tasks statt List\<Task\>). Diese Custom Collections enthalten das Verhalten für die Gruppe von Objekten (z.B. tasks.find(id)) und verhindern das Auslaufen von "Naked Data".

### **1.7 Vermeidung von Null-Werten**

* **Anti-Muster (Verboten):** Rückgabe oder Verwendung von null. Verwendung prozeduraler Null-Checks (if (x \== null)).  
* **Richtige OOP-Umsetzung (Erforderlich):** **Das Null Object Pattern** muss verwendet werden. Objekte dürfen niemals null zurückgeben; stattdessen müssen sie eine polymorphe, "leere" Implementierung (NullTask, UnknownUser) zurückgeben, die alle Methodenaufrufe ohne Seiteneffekte verarbeitet. Dadurch entfällt die Notwendigkeit von null-Überprüfungen durch den Konsumenten.

## **2\. Architektur und Design: Fokus auf Kapselung und Pakete**

Das Konzept horizontaler, strikter Schichten (wie Domain, Application, Infrastructure in klassischem DDD) ist in der strikten OOP **verboten**. Die Architektur muss auf **kontextgesteuerter Paketierung (Context-Driven Packaging)** basieren, bei der die Abhängigkeiten vertikal über Objekte und deren Kapselung organisiert werden. Der Fokus liegt auf der **Delegation von Verantwortung** und nicht auf der Trennung in "Layer".

Regeln für Pakete:

1.   
   * Root Package \= Domain Core Klassen/Interfaces, unabhängig von allem  
   * Externe Kontexte \= Adapter, abhängig von Core Klassen/Interfaces  
2. Sub-Pakete führen keine neuen Konzepte ein, nur Details  
   * jira/JiraClient \= Detail der Jira-Integration  
   * Keine neuen fachlichen Konzepte in Sub-Packages  
3. **Pakete spiegeln ausschließlich Business-Aspekte wider, technische Paketnamen sind verboten**  
   * ✅ jira, codebeamer, doors (aus Context-Diagramm)  
   * ❌ service, repository, controller (technische Details)  
4. **Abhängigkeitskontrolle (Composition over Inheritance):** Objekte müssen von **Interfaces** (implements) abhängen, nicht von konkreten Klassen. Nutzen Sie **Komposition** statt Vererbung (außer in eng definierten Taxonomien), um Flexibilität zu gewährleisten.

Beispiel für äußere Komposition (Einstiegspunkt):

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

### **2.1 Statische Methoden/Factories**

* **Anti-Muster (Verboten):** Statische Methoden, insbesondere für die Objekterzeugung oder den Datenzugriff (Verstoß gegen das DIP).  
* **Richtige OOP-Umsetzung (Erforderlich):** Die Objekterzeugung erfolgt über den Konstruktor oder polymorphe Factory-Objekte.

### **2.2 Service Layer**

* **Anti-Muster (Verboten):** "Allmächtige Service-Layer", die nur Daten zwischen Repository und DTO hin- und herschieben.  
* **Richtige OOP-Umsetzung (Erforderlich):** Die **Verantwortung** muss direkt an die entsprechenden Objekte delegiert werden.

### **2.3 Klassisches ORM/Active Record**

* **Anti-Muster (Verboten):** Die Nutzung von ORMs, die Getter/Setter erfordern und Daten von Verhalten trennen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Die Verantwortung für das Speichern/Laden sollte **gekapselt** und über eine **abstrakte Collection/Storage-Abhängigkeit** an das Domänenobjekt delegiert werden.

### **2.4 Aktion in der Ressource/Controller**

* **Anti-Muster (Verboten):** Controller/Ressourcen, die Business-Logik oder I/O-Formatierung enthalten.  
* **Richtige OOP-Umsetzung (Erforderlich):** Ressourcen/Controller müssen schlank sein. Die gesamte Logik (Laden, Validieren, I/O-Formatierung) muss an **dekorierende Objekte** delegiert werden (z.B. RestTaskResource).

## **3\. Umsetzung und Polymorphie**

* **Polymorphe Dekoration:** Infrastruktur-Abhängigkeiten (z.B. das Schreiben einer JSON-Antwort) müssen durch **Decorator-Objekte** gekapselt werden, die das Domänen-Objekt zur Darstellung auffordern.  
* **Abhängigkeitsinjektion:** Implementieren Sie **Dependency Injection ohne Container**. Alle Abhängigkeiten müssen über den **Konstruktor** injiziert werden.  
* **Verträge (Interfaces):** Interfaces dienen dazu, **Verhalten** (Verantwortung) zu definieren, nicht nur Datenstrukturen.

### **3.1 Horizontale Zerlegung (Decorator Pattern zur Kapselung von Querschnittsbelangen)**

* **Anti-Muster (Verboten):** Monolithische Implementierung, die fachliche und nicht-fachliche Aspekte (Logging, Caching, Transaktionen) in einer Klasse mischt, oder die Verwendung von Vererbung zur Implementierung von Verhalten.  
* **Richtige OOP-Umsetzung (Erforderlich):** **Horizontale Zerlegung** aller nicht-fachlichen (cross-cutting) Belange mittels **Decorator Pattern**. Jede zusätzliche Verantwortung (z.B. Logging, Caching, Zugriffskontrolle) muss in einem eigenen, unveränderlichen Objekt gekapselt werden, das das gleiche Interface implementiert und das ursprüngliche Objekt im Konstruktor erhält.

Beispiel für Komposition durch Dekoration (inkl. UI/I/O-Delegation):

// Annahme: Das Domänen-Interface enthält sowohl Business- als auch I/O-Verhalten  
// interface Task { void complete(); void presentTo(IOPresenter sink); }

// 1\. Das End-Objekt, das nur die reine Business-Logik enthält  
Task businessTask \= new DatabaseTask(connection);   
// Hier wird die DB-Verantwortung direkt an das Domänenobjekt gekoppelt (siehe 1.3)

// 2\. Horizontale Decorators (Querschnittsbelange, implementieren das gleiche Interface)  
Task decoratedTask \= new CachedTask(  
    new LoggedTask(  
        new SecuredTask(  
            businessTask  
        )  
    )  
);

// 3\. Finaler Decorator für das UI-Verhalten (Polymorphe Darstellung)  
// Dieser Decorator KANN zusätzlich Events auslösen oder die finale I/O-Logik übernehmen  
Task finalTask \= new PresentableTask(  
    decoratedTask,  
    new JsonResponseWriter() // Das Ziel (Sink) der I/O-Delegation  
);

// \--- Nutzung durch den Controller/Client \---

// 1\. Aufruf der Business-Logik (geht durch Presentable, Cached, Logged, Secured, DB)  
finalTask.complete();

// 2\. Delegation der I/O-Verantwortung (UI of Objects):  
// Das Domänenobjekt steuert die Darstellung und schreibt direkt in den Sink (ResponseWriter).  
finalTask.presentTo(httpServletResponse); 

### **3.2 UI of Objects (Polymorphe Darstellung & Observer)**

* **Anti-Muster (Verboten):** Die UI-Komponente (Controller, View) fragt das Domänenobjekt nach Daten (per Getter) und ist dann für die Formatierung verantwortlich. (Ask statt Tell).  
* **Richtige OOP-Umsetzung (Erforderlich):** Das Domänenobjekt muss die **volle Verantwortung für seine Darstellung** übernehmen. Die Methode erhält das **Ziel (Sink)**, also die konkrete UI-Komponente (z.B. PrimeFaces Tree oder HtmlOutputText), und delegiert an diese die notwendigen Anweisungen, um sich selbst darzustellen. Querschnitts-Verhalten (wie UI-Events) werden über Decorator-Ketten integriert.

Beispiel für UI-Integration und Event-Auslösung:

// Folder Interface im Domain-Package  
public interface Folder {  
    // Erzählt der UI-Komponente, wie sie sich selbst darstellen soll.  
    void displayTo(Tree treeComponent);   
    // ...  
}

// Implementierung  
public class TaskFolder implements Folder {  
    // ...  
    @Override  
    public void displayTo(Tree treeComponent) {  
        // Logik, die treeComponent mit Daten des Folders füllt.  
        treeComponent.setTitle(this.name);   
        // ...  
    }  
}

### **3.3 Immutable Proxies und Delegation**

* **Anti-Muster (Verboten):** Monolithische Klassen, die alle Logik selbst enthalten, ohne Delegation, oder die Implementierungsdetails (z.B. Caching, Persistenz) offenlegen.  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen als **unveränderliche Proxies** (oder Fassaden) entworfen werden. Sie delegieren ihre Komplexität an interne, private Komponenten (andere Objekte), die über den Konstruktor injiziert werden. Das Domänen-Objekt ist der einzige **Vertrag (Interface)** für seine Konsumenten.

Beispiel für den Immutable Proxy:

// 1\. Vertikales Interface (Domänen-Verhalten)  
interface Document {  
    String title();  
    void title(String text);  
}

// 2\. Vertikales Interface für die technische Schicht (Persistenz-Verhalten)  
interface DocumentStorage { /\* ... \*/ } 

// 3\. Der Unveränderliche Proxy (Encapsulates nur die ID und Storage-Kette)  
// @Immutable  
class StoredDocument implements Document {    
    private final DocumentStorage externalStorage;  
    private final int id;

    public StoredDocument(int id, DocumentStorage storage) {    
        this.id \= id;    
        // Injektion des gesamten Storage/Caching-Graphen  
        this.externalStorage \= storage;  
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
        return doc instanceof StoredDocument && StoredDocument.class.cast(doc).id \== this.id;    
    }    
}

## **4\. Naming Conventions (Objektbenennung)**

### **4.1 Nomen und Identität**

* **Anti-Muster (Verboten):** Benennung von Objekten nach ihrer *Fähigkeit* oder *Funktion* (Processor, Manager, Validator, Service, Utility).  
* **Richtige OOP-Umsetzung (Erforderlich):** Objekte müssen nach dem **Ding (Nomen)** benannt werden, das sie repräsentieren. *Beispiele:* Order, Payment, Invoice, Database, User. Sie müssen eine klare **Identität** haben.

### **4.2 Funktions-Suffixe**

* **Anti-Muster (Verboten):** Die Endungen \-er, \-or, \-able, Client, Data oder Info (z.B. FileWriter, OrderProcessor, TaskClient). Diese Suffixe deuten auf prozedurale Zerlegung oder "Naked Data" hin.  
* **Richtige OOP-Umsetzung (Erforderlich):** Vermeiden Sie diese Suffixe auf Domänen- oder Kerngeschäftsobjekten. *Beispiele für Korrekturen:* Statt FileWriter  File. Statt DataValidator  Rule. Statt TaskClient  Endpoint.

### **4.3 Compound Names**

* **Anti-Muster (Verboten):** Zusammengesetzte Namen (Compound Names) wie UserAccountManager, DatabaseConnectionHolder, da sie oft auf eine Aggregation von Verantwortlichkeiten hindeuten (Code Smell).  
* **Richtige OOP-Umsetzung (Erforderlich):** Streben Sie nach einfachen, präzisen Namen, die nur eine einzige Verantwortung widerspiegeln. *Beispiele für Korrekturen:* Statt UserAccountManager  User oder Account. Statt DatabaseConnectionHolder  Connection.

## **5\. Methodik und Namenskonventionen**

### **5.1 Single Action Rule (Einzelaktion)**

* **Anti-Muster (Verboten):** Methoden, die mehrere Verantwortlichkeiten zusammenfassen. *Beispiel:* createAndPersistUser(User u).  
* **Richtige OOP-Umsetzung (Erforderlich):** Jede Methode muss eine **einzelne, klar definierte und abgeschlossene Aktion** ausführen, die sich auf das Objekt selbst bezieht. *Beispiel:* user.persist().

### **5.2 Command Naming (Mutatoren)**

* **Anti-Muster (Verboten):** Verwendung von update, set, modify. *Beispiel:* updateThePrice(NewPrice p).  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die einen **Seiteneffekt** haben oder den Zustand ändern (oder ein neues, geändertes Objekt zurückgeben), müssen als **Befehle** benannt werden (Infinitiv/Imperativ). *Beispiele:* price(NewPrice p), increase(Amount a), complete().

### **5.3 Query Naming (Accessoren)**

* **Anti-Muster (Verboten):** Verwendung von get als Präfix. *Beispiel:* getFirstName().  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die keinen Zustand ändern (keine Seiteneffekte), müssen als **Abfragen** benannt werden und klar ihre Natur kennzeichnen. *Beispiele:* firstName(), isCompleted(), hasPermissions(), toXml().

### **5.4 Method Chaining (Zugriff auf Objekte)**

* **Anti-Muster (Verboten):** Aufrufe wie user.getGroup().getRights() (Verstöße gegen 5.3).  
* **Richtige OOP-Umsetzung (Erforderlich):** Methoden, die andere Domänenobjekte (Value Objects, Entities, Collections) liefern, müssen nach dem **Namen des zurückgegebenen Objekts** benannt werden. Dies fördert lesbare, flüssige Aufrufe (Fluent API) und unterstützt die Kapselung. *Beispiel:* user.group().rights().

**Zusammenfassend:** Jede Code-Einheit muss eine klare, einzige Verantwortung haben. Das Domänenobjekt ist König.