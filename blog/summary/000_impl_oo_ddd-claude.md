# Implementing Object-Oriented Domain-Driven Design (OO-DDD)
## Von der Theorie zur Praxis: Wie echte Objektorientierung wartbare Software ermöglicht

**Autor:** Basierend auf Erkenntnissen von Robert Bräutigam, Yegor Bugayenko, Vytautas Žurauskas, Mihai Emil Andronache und anderen OOP-Pionieren

**Datum:** Januar 2025

---

## Abstract

Moderne Softwareentwicklung hat sich weit von den ursprünglichen Prinzipien der Objektorientierung entfernt. Was heute als "objektorientierte Programmierung" praktiziert wird, ist häufig prozeduraler Code, der Klassen als Namespaces missbraucht. Dieser Artikel untersucht, wie echte Objektorientierung mit Domain-Driven Design kombiniert werden kann, um wartbare, erweiterbare und verständliche Software zu schaffen. Anhand konkreter Beispiele aus JSF-Webanwendungen zeigen wir den fundamentalen Unterschied zwischen datenorientierten Schichtarchitekturen und objektorientierten Designs und demonstrieren praktische Implementierungsmuster wie das View Pattern für moderne UI-Frameworks.

---

## Inhaltsverzeichnis

1. [Einleitung: Das Problem mit "modernem" OOP](#1-einleitung-das-problem-mit-modernem-oop)
2. [Grundlagen: Was ist Object-Oriented Domain-Driven Design?](#2-grundlagen-was-ist-object-oriented-domain-driven-design)
3. [Die Krise der Kapselung: Getter und Setter](#3-die-krise-der-kapselung-getter-und-setter)
4. [Architekturvergleich: Layered vs. Object-Oriented](#4-architekturvergleich-layered-vs-object-oriented)
5. [Data Boundaries: Die Wurzel von Wartungsproblemen](#5-data-boundaries-die-wurzel-von-wartungsproblemen)
6. [Praktische Implementierung: Das View Pattern](#6-praktische-implementierung-das-view-pattern)
7. [Object Naming: Über die Bedeutung guter Namen](#7-object-naming-über-die-bedeutung-guter-namen)
8. [Contract-Driven Construction](#8-contract-driven-construction)
9. [Composition over Inheritance: Decorator Pattern](#9-composition-over-inheritance-decorator-pattern)
10. [Package Design: Happy Packaging](#10-package-design-happy-packaging)
11. [Persistenz: SQL-Speaking Objects](#11-persistenz-sql-speaking-objects)
12. [Fallstudien und Beispiele](#12-fallstudien-und-beispiele)
13. [Fazit und Ausblick](#13-fazit-und-ausblick)
14. [Quellenverzeichnis](#14-quellenverzeichnis)

---

## 1. Einleitung: Das Problem mit "modernem" OOP

### 1.1 Der Zustand der objektorientieren Programmierung

Die objektorientierte Programmierung wurde in den 1960er Jahren mit Simula und später mit Smalltalk etabliert. Die Vision war revolutionär: Software sollte aus unabhängigen, autonomen Objekten bestehen, die miteinander kommunizieren und ihre eigenen Daten und ihr eigenes Verhalten kapseln. Alan Kay, einer der Väter der Objektorientierung, betonte: "OOP to me means only messaging, local retention and protection and hiding of state-process, and extreme late-binding of all things."

Heute jedoch sieht die Realität anders aus. Was als "objektorientierter Code" gilt, ist häufig prozeduraler Code mit Klassen als Namespaces. Wie Robert Bräutigam in seinen Präsentationen zeigt, haben wir nicht gelernt, echte Objektorientierung konsequent anzuwenden. Das Problem verschlimmert sich rasant, da auch moderne KI-Systeme diesen mangelhaften Stil perpetuieren.

### 1.2 Symptome des Problems

Betrachten wir typischen "Enterprise"-Code:

```java
public interface Animal {
    String getSpeech();
}

public final class Cat implements Animal {
    private final String speech;
    
    @Override
    public String getSpeech() {
        return speech;
    }
}

public final class SpeechService {
    public void speak(Animal animal) {
        System.out.println(animal.getSpeech());
    }
}
```

Was ist hier falsch? Alles. Das Objekt `Cat` hat keine Verantwortung mehr. Es ist ein bloßer Datencontainer. Die eigentliche Logik liegt im `SpeechService` - einem prozeduralen Ansatz, der mit Klassen verschleiert wird. Die Kapselung ist vollständig verloren gegangen.

Der ursprüngliche objektorientierte Ansatz würde so aussehen:

```java
public interface Animal {
    void speak();
}

public final class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Meow...");
    }
}
```

Das Objekt `Cat` tut etwas. Es hat Verhalten, nicht nur Daten. Es ist autonom und verantwortlich für sein eigenes Handeln.

### 1.3 Die Konsequenzen

Die Konsequenzen dieses Paradigmenwechsels weg von echter OOP sind gravierend:

- **Hohe Kopplung**: Änderungen breiten sich unkontrolliert durch die Codebasis aus
- **Niedrige Kohäsion**: Zusammengehörende Funktionalität ist über viele Klassen verteilt
- **Schwere Testbarkeit**: Dependencies müssen komplex gemockt werden
- **Schlechte Wartbarkeit**: Kleine Änderungen erfordern Modifikationen an vielen Stellen
- **Verlorene Abstraktion**: Objekte lösen keine Probleme mehr, sie sind nur noch Datenhüllen

---

## 2. Grundlagen: Was ist Object-Oriented Domain-Driven Design?

### 2.1 Domain-Driven Design nach Eric Evans

Domain-Driven Design, eingeführt von Eric Evans in seinem gleichnamigen Buch von 2003, betont die Bedeutung der Domäne und der Domänenlogik in der Softwareentwicklung. Die Kernidee ist, dass die Software-Struktur die Struktur der Geschäftsdomäne widerspiegeln sollte, nicht technische Überlegungen.

Evans führte wichtige Konzepte ein:
- **Ubiquitous Language**: Eine gemeinsame Sprache zwischen Entwicklern und Domänenexperten
- **Bounded Contexts**: Klare Grenzen zwischen verschiedenen Teilen der Domäne
- **Entities**: Objekte mit Identität
- **Value Objects**: Objekte ohne Identität, definiert durch ihre Attribute
- **Aggregates**: Cluster von Objekten, die als Einheit behandelt werden
- **Repositories**: Mechanismen zum Abrufen von Objekten
- **Services**: Operationen, die keinem Objekt natürlich zugeordnet werden können

### 2.2 Die Probleme mit traditionellem DDD

Wie Robert Bräutigam in seiner Präsentation "Object-Oriented Domain Driven Design" argumentiert, haben die ursprünglichen DDD Building Blocks ein fundamentales Problem: Sie führen oft zu datenorientierten Designs, nicht zu objektorientierten.

Betrachten wir ein typisches DDD Value Object:

```java
public final class Amount {
    private final BigDecimal value;
    private final Currency currency;
    
    public Amount(BigDecimal value, Currency currency) {
        this.value = value;
        this.currency = currency;
    }
    
    public BigDecimal getValue() {
        return value;
    }
    
    public Currency getCurrency() {
        return currency;
    }
    
    // equals(), hashCode(), toString()...
}
```

Bräutigam stellt fest: "There is nothing hidden. Therefore there is no problem solved here. This thing has no reason to exist!" Das Value Object ist nur ein Datencontainer ohne Verhalten. Es gibt keine Kapselung, keine gelösten Probleme, keine Abstraktion.

### 2.3 Object-Oriented DDD: Die Synthese

Object-Oriented DDD kombiniert die Domänenfokussierung von DDD mit echten OOP-Prinzipien. Statt Objekte als Datencontainer zu sehen, werden sie als autonome Entitäten betrachtet, die:

1. **Ihre eigenen Daten verbergen** - Keine Getter/Setter für interne Zustandsinformationen
2. **Verhalten statt Daten exponieren** - Methoden tun etwas, statt Daten zurückzugeben
3. **Sich selbst darstellen können** - UI-Rendering ist Teil der Domänenlogik
4. **Sich selbst persistieren können** - Objekte wissen, wie sie gespeichert werden
5. **Immutable sind** - Änderungen erzeugen neue Objekte statt Mutation
6. **Durch Komposition erweitert werden** - Decorator Pattern statt Vererbung

Ein korrekt designtes `Amount` Value Object würde so aussehen:

```java
public final class Amount {
    private final int cents;  // Internal representation
    private final Currency currency;
    
    public Amount(int cents, Currency currency) {
        this.cents = cents;
        this.currency = currency;
    }
    
    // Verhalten, nicht Daten
    public Amount add(Amount other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Different currencies");
        }
        return new Amount(cents + other.cents, currency);
    }
    
    public boolean largerThan(Amount other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Different currencies");
        }
        return cents > other.cents;
    }
    
    public Amount multiply(int factor) {
        return new Amount(cents * factor, currency);
    }
    
    // UI: Objekt zeigt sich selbst
    public Component display() {
        return new Label(
            String.format("%d.%02d %s", 
                cents / 100, 
                cents % 100, 
                currency.symbol()
            )
        );
    }
}
```

Das Objekt löst jetzt echte Probleme: Addition, Vergleich, Multiplikation von Geldbeträgen unter Beachtung der Währung. Die interne Repräsentation (Cents als Integer statt BigDecimal) ist verborgen. Das Objekt kann sich selbst darstellen.

### 2.4 Die vier Säulen von OO-DDD

Object-Oriented Domain-Driven Design ruht auf vier Säulen:

**Säule 1: True Encapsulation**
Echte Kapselung bedeutet, dass interne Daten niemals nach außen dringen. Objekte haben Geheimnisse. Diese Geheimnisse ermöglichen effektive Abstraktion: Ein Problem wird gelöst, über das wir nie wieder nachdenken müssen.

**Säule 2: Tell, Don't Ask**
Objekte werden aufgefordert, etwas zu tun, nicht nach ihrem Zustand gefragt. `task.complete()` statt `if (!task.isCompleted()) { task.setCompleted(true); }`.

**Säule 3: Domain-Focused Design**
Die Struktur des Codes spiegelt die Struktur der Domäne wider. Packages und Klassen sind nach Geschäftskonzepten benannt, nicht nach technischen Schichten.

**Säule 4: Behavior over Data**
Objekte definieren sich durch ihr Verhalten, nicht durch ihre Daten. Ein `Account` ist nicht "ein Objekt mit einem Balance-Feld", sondern "etwas, das Geld empfangen und senden kann".

---

## 3. Die Krise der Kapselung: Getter und Setter

### 3.1 Warum Getter und Setter problematisch sind

Allen Holub schrieb bereits 2003 in seinem berühmten JavaWorld-Artikel "Why getter and setter methods are evil": Getter und Setter zerstören die Kapselung vollständig. Sie machen private Felder de facto public.

Yegor Bugayenko verstärkt diese Kritik in "Getters/Setters. Evil. Period." (2014): Getter und Setter führen zu "naked data" - Daten ohne Verhalten. Das Objekt wird zum bloßen Datencontainer degradiert, und die gesamte Logik wandert in externe Services.

Das fundamentale Problem ist semantische Kopplung. Wie Bräutigam zeigt:

```java
public final class CurrencyConverter {
    public Amount convert(Amount amount, Currency currency) {
        return new Amount(
            amount.getValue() *  // Privacy violation!
            exchange.getDailyRate(
                amount.getCurrency(),  // Privacy violation!
                currency
            ),
            currency
        );
    }
}
```

Der `CurrencyConverter` weiß zu viel über `Amount`. Er kennt die interne Struktur (value, currency). Änderungen an `Amount` werden mit hoher Wahrscheinlichkeit Änderungen in `CurrencyConverter` erfordern. Diese semantische Kopplung ist unsichtbar für den Compiler und damit besonders gefährlich.

### 3.2 Die Illusion der Abstraktion

Viele Entwickler argumentieren: "Aber ich brauche doch die Daten zum Anzeigen!" Dieser Gedanke offenbart ein fundamentales Missverständnis. Die Frage sollte nicht sein "Wie komme ich an die Daten?", sondern "Wie kann das Objekt das Problem lösen?"

Betrachten wir eine Task-Verwaltung:

```java
// FALSCH - Datenexposition
public class Task {
    private String description;
    private boolean completed;
    
    public String getDescription() {
        return description;
    }
    
    public boolean isCompleted() {
        return completed;
    }
}

public class TaskView {
    public void render(Task task) {
        String desc = task.getDescription();
        boolean done = task.isCompleted();
        
        if (done) {
            renderCompletedTask(desc);
        } else {
            renderPendingTask(desc);
        }
    }
}
```

Die `TaskView` muss die interne Struktur von `Task` kennen. Sie muss wissen, dass es `description` und `completed` gibt und wie diese interpretiert werden müssen. Jede Änderung an `Task` propagiert zu `TaskView`.

```java
// RICHTIG - Verhalten
public interface Task {
    void showTo(Display display);
}

public final class SimpleTask implements Task {
    private final String description;
    private final boolean completed;
    
    @Override
    public void showTo(Display display) {
        String text = description + (completed ? " ✓" : "");
        String style = completed ? "completed" : "pending";
        display.show(text, style);
    }
}
```

Das Objekt behält die Kontrolle. Es entscheidet, wie es dargestellt wird. Die externe `Display`-Komponente muss nichts über die interne Struktur wissen.

### 3.3 Lösungsansätze

**Lösung 1: Printers instead of Getters** (Bugayenko, 2016)

Statt Daten zurückzugeben, nimmt das Objekt einen Printer/Writer/Display und schreibt sich selbst:

```java
public interface Task {
    void printTo(Writer writer) throws IOException;
}

public final class SimpleTask implements Task {
    @Override
    public void printTo(Writer writer) throws IOException {
        writer.write(description);
        if (completed) {
            writer.write(" [DONE]");
        }
    }
}
```

**Lösung 2: View Pattern** (Praktischer Kompromiss)

Für UI-Frameworks wie JSF, die Getter/Setter erwarten, verwenden wir das View Pattern:

```java
public interface Task {
    Task.View asView();  // Kontrollierte Exposition
    Task edit(Task.View view);  // Task versteht View
    
    interface View {
        String getDescription();
        void setDescription(String description);
        boolean getCompleted();
        void setCompleted(boolean completed);
    }
}
```

Das Objekt bleibt immutable und kontrolliert, was in der View exponiert wird.

**Lösung 3: Question/Answer Pattern** (Žurauskas)

Statt Getter verwenden wir typsichere Fragen:

```java
public interface Task {
    boolean answersTo(Question question);
}

public final class IsCompleted implements Question {}

// Usage
if (task.answersTo(IsCompleted.instance())) {
    // ...
}
```

### 3.4 "But how do you work without a model?" - Andronache

Mihai Emil Andronache adressiert die häufigste Frage in seinem Artikel "But how do you work without a model?": Wie kann man ohne Getter/Setter und ohne Datenmodel arbeiten?

Die Antwort: Durch Verhalten und Contracts. Statt ein Model mit Daten zu haben, haben wir Interfaces mit Verhalten. Die Implementierungen wissen, wie sie mit ihren eigenen Daten umgehen müssen.

```java
// Kein "Model" im traditionellen Sinn
public interface Storage {
    void save(String key, byte[] data);
    byte[] load(String key);
}

// Implementation entscheidet über interne Struktur
public final class FileStorage implements Storage {
    private final Path directory;
    
    @Override
    public void save(String key, byte[] data) {
        Files.write(directory.resolve(key), data);
    }
    
    @Override
    public byte[] load(String key) {
        return Files.readAllBytes(directory.resolve(key));
    }
}
```

Kein Datenmodel, keine Getter/Setter, nur Verhalten. Die interne Repräsentation (Dateisystem) ist komplett verborgen.

---

## 4. Architekturvergleich: Layered vs. Object-Oriented

### 4.1 Die Layered Architecture

Die geschichtete Architektur (Layered Architecture oder N-Tier Architecture) ist seit den 1990er Jahren der de facto Standard in Enterprise-Anwendungen:

```
┌─────────────────────────────────────┐
│      Presentation Layer             │
│    (Controllers, Views, DTOs)       │
├─────────────────────────────────────┤
│      Business Logic Layer           │
│    (Services, Domain Models)        │
├─────────────────────────────────────┤
│      Data Access Layer              │
│    (Repositories, DAOs)             │
├─────────────────────────────────────┤
│      Database Layer                 │
│    (Tables, SQL)                    │
└─────────────────────────────────────┘
```

Typischer Code in einer Layered Architecture:

```java
// Presentation Layer - DTO
public class TaskDTO {
    private String id;
    private String description;
    private boolean completed;
    // Getters/Setters
}

// Presentation Layer - Controller
@Controller
public class TaskController {
    @Autowired
    private TaskService taskService;
    
    @PostMapping("/complete")
    public String complete(@RequestParam String id) {
        TaskDTO task = taskService.findById(id);
        task.setCompleted(true);
        taskService.update(task);
        return "redirect:/tasks";
    }
}

// Business Layer - Service
@Service
public class TaskService {
    @Autowired
    private TaskRepository taskRepository;
    
    public TaskDTO findById(String id) {
        Task entity = taskRepository.findById(id)
            .orElseThrow();
        return TaskMapper.toDTO(entity);
    }
    
    public void update(TaskDTO dto) {
        Task entity = TaskMapper.toEntity(dto);
        taskRepository.save(entity);
    }
}

// Data Layer - Entity
@Entity
public class Task {
    @Id
    private String id;
    private String description;
    private boolean completed;
    // Getters/Setters
}

// Data Layer - Repository
public interface TaskRepository extends JpaRepository<Task, String> {}
```

### 4.2 Probleme der Layered Architecture

Bräutigam identifiziert in "Reevaluating the Layered Architecture" folgende Probleme:

**Problem 1: Data Boundaries überall**
Zwischen jeder Schicht werden Daten hin- und herkopiert: Entity → DTO → ViewModel. Jede Schicht kennt die Datenstruktur aller anderen Schichten.

**Problem 2: Die Domäne ist nur 1/4 der Anwendung**
Die eigentliche Business-Logik ist auf eine von vier Schichten beschränkt. UI, Persistence und andere Aspekte werden als "technische Details" betrachtet und ausgelagert.

**Problem 3: Änderungen eskalieren nach oben**
Eine Änderung in der Domäne (z.B. ein neues Feld) propagiert durch alle Schichten: Entity → DTO → Service → Controller → View. Minimum 5 Dateien müssen geändert werden.

**Problem 4: Verletzung von SRP**
Das Single Responsibility Principle wird systematisch verletzt. Eine Business-Änderung erfordert Änderungen in mehreren Klassen. Things that change together sind nicht together.

**Problem 5: Anemic Domain Model**
Die "Domain Models" haben kein Verhalten, nur Daten. Die eigentliche Logik liegt in Services - prozeduraler Code mit OOP-Fassade.

```
Layered Architecture: Change Propagation
─────────────────────────────────────────
Add field "priority" to Task:

1. Task.java (Entity)        ← Add field + getter/setter
2. TaskDTO.java              ← Add field + getter/setter  
3. TaskMapper.java           ← Map new field
4. TaskService.java          ← Handle new field
5. TaskController.java       ← Pass new field
6. task-form.html            ← Display new field

Result: 6 files changed for 1 business change!
```

### 4.3 Die Object-Oriented Architecture

In einer echten objektorientierten Architektur gibt es keine technischen Schichten. Stattdessen gibt es vertikale Features:

```
Object-Oriented Architecture
═════════════════════════════

Package Structure (Business-Oriented):

de.company.app/
  Task.java              ← Interface
  Tasks.java             ← Interface
  
de.company.app.task/
  SimpleTask.java        ← Implementation
  SqlTask.java           ← SQL-speaking Implementation
  UrgentTask.java        ← Decorator
  
de.company.app.tasks/
  InMemoryTasks.java     ← Implementation
  SqlTasks.java          ← SQL-speaking Implementation
```

Code in einer objektorientierten Architektur:

```java
// Core Interface
public interface Task {
    Task complete();
    Task.View asView();
    Task edit(Task.View view);
    void persistTo(Connection connection);
    
    interface View {
        String getDescription();
        void setDescription(String description);
        boolean getCompleted();
        Action complete();
    }
}

// Implementation with everything inside
public final class SimpleTask implements Task {
    private final String id;
    private final String description;
    private final boolean completed;
    
    @Override
    public Task complete() {
        return new SimpleTask(id, description, true);
    }
    
    @Override
    public Task.View asView() {
        return new TaskView(id, description, completed);
    }
    
    @Override
    public Task edit(Task.View view) {
        return new SimpleTask(
            view.getId(),
            view.getDescription(),
            view.getCompleted()
        );
    }
    
    @Override
    public void persistTo(Connection connection) {
        try (PreparedStatement stmt = connection.prepareStatement(
            "INSERT INTO tasks VALUES (?, ?, ?) " +
            "ON DUPLICATE KEY UPDATE description=?, completed=?")) {
            stmt.setString(1, id);
            stmt.setString(2, description);
            stmt.setBoolean(3, completed);
            stmt.setString(4, description);
            stmt.setBoolean(5, completed);
            stmt.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

**Änderung "priority" hinzufügen:**

```
Object-Oriented Architecture: Change Propagation
───────────────────────────────────────────────
Add field "priority" to Task:

1. Task.java interface        ← Add to View interface
2. SimpleTask.java             ← Add field + logic

Result: 2 files changed for 1 business change!

Change is localized. No propagation through layers.
```

### 4.4 Diagrammatischer Vergleich

```
┌────────────────────────────────────────────────────────────┐
│                  Layered Architecture                       │
│                                                              │
│  Change Flow (Adding "priority" field):                     │
│                                                              │
│     [Database] → [Entity] → [DTO] → [Service] →            │
│     [Controller] → [View]                                   │
│                                                              │
│     6 files to change                                       │
│     High coupling between layers                            │
│     Data boundaries everywhere                              │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│              Object-Oriented Architecture                    │
│                                                              │
│  Change Flow (Adding "priority" field):                     │
│                                                              │
│     [Task Interface] → [SimpleTask Implementation]          │
│                                                              │
│     2 files to change                                       │
│     Low coupling through encapsulation                      │
│     No data boundaries                                      │
└────────────────────────────────────────────────────────────┘
```

### 4.5 Maintenance-Vergleich

Stellen wir uns typische Maintenance-Szenarien vor:

**Szenario 1: Neues Feld hinzufügen**
- Layered: 5-7 Dateien ändern
- OO: 1-2 Dateien ändern

**Szenario 2: Validierungsregel ändern**
- Layered: Service, Controller, möglicherweise DTO ändern
- OO: Nur das Objekt selbst ändern

**Szenario 3: UI-Änderung (neues Format)**
- Layered: View, Controller, möglicherweise DTO ändern
- OO: Nur display()-Methode im Objekt ändern

**Szenario 4: Persistenz-Änderung (SQL → NoSQL)**
- Layered: Repository, möglicherweise Entity, Service ändern
- OO: Neue Implementation des Interfaces, bestehender Code unberührt

**Szenario 5: Business-Logik ändern**
- Layered: Service, möglicherweise mehrere Services ändern
- OO: Nur das betroffene Objekt ändern

---

## 5. Data Boundaries: Die Wurzel von Wartungsproblemen

### 5.1 Was sind Data Boundaries?

Bräutigam identifiziert in seinem Artikel "Data Boundaries are the root cause of Maintenance Problems" das zentrale Problem moderner Architekturen: Data-oriented Boundaries - Schnittstellen zwischen Architekturteilen, die primär aus Daten bestehen.

Ein Data Boundary existiert überall dort, wo:
- DTOs zwischen Schichten übergeben werden
- Getter/Setter genutzt werden, um Daten zu extrahieren
- Datenstrukturen statt Verhalten exponiert werden
- Mapper/Converter Daten zwischen Formaten konvertieren

### 5.2 Das Problem mit implizitem Wissensaustausch

Das größte Problem mit Data Boundaries ist impliziter Wissensaustausch. Beide Seiten müssen die gleiche Interpretation der Daten haben:

```java
public final class Amount {
    private final BigDecimal value;
    private final Currency currency;
    
    public BigDecimal getValue() { return value; }
    public Currency getCurrency() { return currency; }
}
```

Wenn ein Client `amount.getValue()` aufruft, muss er wissen:
- Ist der Wert in Hauptwährungseinheiten oder Cents?
- Kann der Wert null sein?
- Welche Präzision hat der BigDecimal?
- Wie interagiert value mit currency?
- Gibt es versteckte Constraints?

Dieses Wissen ist implizit. Es gibt keinen Compiler-Check. Änderungen können subtil brechen.

Beispiel für versteckte Komplexität:

```java
// Später wird ein neues Feld eingeführt
public final class Amount {
    private final BigDecimal value;
    private final Currency currency;
    private final boolean isInCents;  // NEU!
    
    public BigDecimal getValue() { return value; }
    public Currency getCurrency() { return currency; }
    public boolean isInCents() { return isInCents; }
}
```

Jeder existierende Client-Code, der `getValue()` nutzt, muss jetzt auch `isInCents()` berücksichtigen. Der Compiler hilft nicht dabei, diese Stellen zu finden.

### 5.3 Maintenance Problems in Action

Bräutigam zeigt am Beispiel des Weld/CDI-Projekts, wie Data Boundaries zu Wartungsproblemen führen:

```java
// WeldFilter definiert sich durch Daten
public class WeldFilter {
    private String name;
    private String pattern;
    
    public String getName() { return name; }
    public String getPattern() { return pattern; }
}

// Irgendwo anders im Code
if ((weldFilter.getName() != null && weldFilter.getPattern() != null)
    || (weldFilter.getName() == null && weldFilter.getPattern() == null)) {
    throw new IllegalStateException("...");
}

if (weldFilter.getPattern() != null) {
    this.matcher = new PatternMatcher(...);
} else {
    this.matcher = new AntSelectorMatcher(...);
}
```

Das Problem: Die Validierungslogik und die Interpretationslogik sind außerhalb von `WeldFilter`. Jemand, der `WeldFilter` ändert (z.B. ein neues Feld hinzufügt), muss alle diese externen Stellen finden und anpassen.

Korrigierte Version ohne Data Boundary:

```java
public final class WeldFilter {
    private final String name;
    private final String pattern;
    
    public WeldFilter(String name, String pattern) {
        if ((name != null && pattern != null) 
            || (name == null && pattern == null)) {
            throw new IllegalArgumentException("...");
        }
        this.name = name;
        this.pattern = pattern;
    }
    
    public Matcher createMatcher() {
        return pattern != null 
            ? new PatternMatcher(pattern)
            : new AntSelectorMatcher(name);
    }
}
```

Die Logik ist jetzt im Objekt. Änderungen sind lokalisiert.

### 5.4 UI als Data Boundary

Ein besonders häufiger Fall von Data Boundaries ist die UI:

```java
// FALSCH - Data Boundary zur UI
public class Task {
    private String description;
    private boolean completed;
    
    public String getDescription() { return description; }
    public boolean isCompleted() { return completed; }
}

public class TaskView {
    public void render(Task task) {
        String desc = task.getDescription();  // Data leak
        boolean done = task.isCompleted();    // Data leak
        
        // View muss interpretieren
        if (done) {
            html.append("<strike>").append(desc).append("</strike>");
        } else {
            html.append(desc);
        }
    }
}
```

Die `TaskView` muss wissen:
- Dass `completed` als durchgestrichener Text dargestellt wird
- Dass `description` direkt ausgegeben werden kann
- Welche anderen Felder relevant sind

Bei einer Änderung (z.B. Hinzufügen eines `priority`-Felds) müssen `Task` UND `TaskView` geändert werden.

```java
// RICHTIG - Kein Data Boundary
public interface Task {
    Component display();
}

public final class SimpleTask implements Task {
    private final String description;
    private final boolean completed;
    
    @Override
    public Component display() {
        Label label = new Label(description);
        if (completed) {
            label.setStyle("text-decoration: line-through");
        }
        return label;
    }
}
```

Die UI-Logik ist im Objekt. Die View muss nichts über die interne Struktur wissen.

### 5.5 Die Lösung: Verhalten statt Daten

Die fundamentale Lösung für Data Boundaries ist einfach: Don't publish data. Keep all the "working" parts inside the object.

Statt Daten zu exponieren:
```java
public Amount getAmount();
```

Exponiere Verhalten:
```java
public void printAmount(Writer writer);
public Component displayAmount();
public boolean amountGreaterThan(Amount other);
```

Das Objekt behält die Kontrolle. Es entscheidet, was passiert. Die interne Struktur bleibt verborgen.

---

## 6. Praktische Implementierung: Das View Pattern

### 6.1 Die Herausforderung: JSF und echte OOP

JavaServer Faces (JSF) und ähnliche UI-Frameworks wurden für Bean-basierte Programmierung entwickelt. Sie erwarten Getter/Setter für Data-Binding:

```xml
<h:inputText value="#{taskBean.task.description}" />
```

JSF ruft `getTask()` und dann `getDescription()` auf. Ohne Getter funktioniert JSF nicht.

Wie können wir echte OOP mit JSF vereinbaren?

### 6.2 View Pattern: Die pragmatische Lösung

Das View Pattern ist ein Kompromiss zwischen OOP-Reinheit und Framework-Realität:

**Kernidee:**
- Business-Objekt bleibt immutable und gekapselt
- View ist ein mutable DTO ausschließlich für UI-Binding
- View ist eine kontrollierte Grenze - das Objekt entscheidet, was exponiert wird
- Das Objekt versteht View und kann sich damit aktualisieren

```java
public interface Task {
    // Business operations
    Task complete();
    Task edit(Task.View view);
    
    // Kontrollierte View-Exposition
    Task.View asView();
    
    /**
     * View - Inner interface for JSF binding.
     * Getters/Setters are OK because controlled by Task.
     */
    interface View extends Serializable {
        String getId();
        void setId(String id);
        
        String getDescription();
        void setDescription(String description);
        
        boolean getCompleted();
        void setCompleted(boolean completed);
        
        // Actions for UI
        Action edit();
        Action complete();
        Action remove();
        
        // Styling
        String getStyle();
    }
}
```

### 6.3 Implementation

```java
public final class SimpleTask implements Task {
    private final String id;
    private final String description;
    private final boolean completed;
    
    public SimpleTask(String description) {
        this.id = UUID.randomUUID().toString();
        this.description = description;
        this.completed = false;
    }
    
    private SimpleTask(String id, String description, boolean completed) {
        this.id = id;
        this.description = description;
        this.completed = completed;
    }
    
    @Override
    public Task complete() {
        // Immutable - neue Instanz
        return new SimpleTask(id, description, true);
    }
    
    @Override
    public Task edit(Task.View view) {
        // Task versteht View und erstellt neue Version
        return new SimpleTask(
            view.getId(),
            view.getDescription(),
            view.getCompleted()
        );
    }
    
    @Override
    public Task.View asView() {
        // Kontrollierte Exposition
        return new TaskView(id, description, completed);
    }
}

public final class TaskView implements Task.View {
    private String id;
    private String description;
    private boolean completed;
    
    public TaskView() {
        // JSF needs empty constructor
    }
    
    public TaskView(String id, String description, boolean completed) {
        this.id = id;
        this.description = description;
        this.completed = completed;
    }
    
    // Getters/Setters for JSF
    @Override
    public String getId() { return id; }
    @Override
    public void setId(String id) { this.id = id; }
    
    @Override
    public String getDescription() { return description; }
    @Override
    public void setDescription(String description) { 
        this.description = description;
    }
    
    @Override
    public boolean getCompleted() { return completed; }
    @Override
    public void setCompleted(boolean completed) {
        this.completed = completed;
    }
    
    @Override
    public Action complete() {
        return new Action() {
            @Override
            public boolean exists() { return !completed; }
            
            @Override
            public String call() {
                return "#{tasks.complete('" + id + "')}";
            }
        };
    }
    
    @Override
    public String getStyle() {
        return completed ? "task-completed" : "task-pending";
    }
}
```

### 6.4 JSF Integration

```xml
<!-- JSF Template -->
<ui:repeat value="#{tasks.views}" var="item">
    <div class="#{item.style}">
        <span>#{item.description}</span>
        <h:commandButton value="Complete" 
                       action="#{item.complete.call()}"
                       rendered="#{item.complete.exists()}" />
    </div>
</ui:repeat>
```

JSF Controller:

```java
@Named("tasks")
@SessionScoped
public final class TaskController implements Serializable {
    private Tasks tasks = new InMemoryTasks();
    private String newTaskText = "";
    
    public void addTask() {
        if (newTaskText != null && !newTaskText.trim().isEmpty()) {
            tasks = tasks.with(newTaskText.trim());
            newTaskText = "";
        }
    }
    
    public void complete(String taskId) {
        tasks = tasks.complete(taskId);
    }
    
    public List<Task.View> getViews() {
        return tasks.views();
    }
    
    // JSF Bindings
    public String getNewTaskText() { return newTaskText; }
    public void setNewTaskText(String text) { this.newTaskText = text; }
}
```

### 6.5 Edit-Workflow

```java
// Start Edit
public void startEdit(String taskId) {
    Task task = tasks.find(taskId);
    if (task != null) {
        this.editingTaskId = taskId;
        // Task gibt View zurück
        this.editingView = new TaskView(task.asView());
    }
}

// JSF modifies View
// editingView.setDescription("New text")

// Save Edit
public void saveEdit() {
    if (editingTaskId != null && editingView != null) {
        // Task nimmt View und erstellt neue Version
        tasks = tasks.edit(editingTaskId, editingView);
        cancelEdit();
    }
}
```

### 6.6 Vorteile des View Pattern

1. **OOP-konform**: Business-Objekt bleibt immutable und gekapselt
2. **JSF-kompatibel**: View hat Getter/Setter für direktes Binding
3. **Kontrolliert**: Objekt entscheidet, was in View exponiert wird
4. **Testbar**: Business-Logik unabhängig von UI testbar
5. **Erweiterbar**: View kann für komplexe Forms erweitert werden
6. **Wartbar**: Änderungen am Business-Objekt sind lokalisiert

---

## 7. Object Naming: Über die Bedeutung guter Namen

### 7.1 Das Problem mit -er Suffixen

Yegor Bugayenko schreibt in "Don't Create Objects That End With -ER": Namen, die auf -er enden, sind fast immer falsch. Sie beschreiben Job-Titel, nicht Objekte.

Beispiele für schlechte Namen:
- `TaskManager` - Was ist ein Manager?
- `UserService` - Was macht ein Service?
- `DataMapper` - Was ist ein Mapper?
- `FileReader` - Was ist ein Reader?
- `OrderProcessor` - Was ist ein Processor?

Das Problem: Diese Namen beschreiben **was das Objekt tut** (seinen "Job"), nicht **was es ist**.

```java
// FALSCH
public class TaskManager {
    public void createTask(String description) { }
    public void deleteTask(String id) { }
    public Task getTask(String id) { }
}
```

`TaskManager` ist kein Objekt, es ist eine Sammlung von Funktionen. Es repräsentiert nichts in der realen Welt.

```java
// RICHTIG
public interface Tasks {
    Tasks with(String description);
    Tasks without(String id);
    Task find(String id);
}
```

`Tasks` ist eine Sammlung von Tasks. Es repräsentiert etwas Reales: die Liste aller Aufgaben.

### 7.2 Namen repräsentieren Dinge, nicht Aktionen

Ein gutes Objekt repräsentiert ein Ding, das in der realen Welt existiert (oder existieren könnte):
- `Task` - eine Aufgabe
- `User` - ein Benutzer
- `Amount` - ein Geldbetrag
- `File` - eine Datei
- `Request` - eine Anfrage

Nicht:
- `TaskHandler` - was ist ein Handler?
- `UserAuthenticator` - was ist ein Authenticator?
- `AmountCalculator` - was ist ein Calculator?

Bugayenko: "The name should tell us what this object IS, not what it DOES."

### 7.3 Compound Names sind Code Smells

In "Compound Name Is a Code Smell" argumentiert Bugayenko: Zusammengesetzte Namen sind oft ein Zeichen schlechten Designs:

```java
// Code Smell
public class XmlFileReader {
    public String read(File file) { }
}
```

Warum brauchen wir drei Konzepte im Namen (Xml, File, Reader)? Weil das Objekt zu viel macht oder zu spezifisch ist.

```java
// Besser
public interface Source {
    String content();
}

public final class XmlFile implements Source {
    private final File file;
    
    @Override
    public String content() {
        return new String(Files.readAllBytes(file.toPath()));
    }
}
```

### 7.4 Client/Server Suffixe sind problematisch

In "Evil Object Name Suffix: Client" zeigt Bugayenko, dass `-Client` und `-Server` Suffixe auf mangelnde Abstraktion hinweisen:

```java
// FALSCH
public class DatabaseClient {
    public void connect() { }
    public void disconnect() { }
}
```

Was ist ein "Client"? Es ist eine Implementierungsdetail, kein Geschäftskonzept.

```java
// RICHTIG
public interface Database {
    void execute(String sql);
}

public final class PostgresDatabase implements Database {
    private final Connection connection;
    
    @Override
    public void execute(String sql) {
        try (Statement stmt = connection.createStatement()) {
            stmt.execute(sql);
        }
    }
}
```

### 7.5 Mihais Perspektive auf Object Naming

Mihai Emil Andronache ergänzt in "My take on Object Naming": Namen sollten Intention ausdrücken, nicht Implementation.

```java
// Schlechte Namen (Implementation)
HashMapUserStorage
JsonFileConfigReader
MySqlDatabaseConnection

// Gute Namen (Intention)
Users
Configuration
Database
```

Die Implementation (HashMap, JSON, MySQL) sollte versteckt sein, nicht im Namen.

---

## 8. Contract-Driven Construction

### 8.1 Interfaces als Contracts

Vytautas Žurauskas betont in "It's not a contract if you can't enforce it": Ein Contract ist nur dann wertvoll, wenn er erzwungen werden kann.

```java
// Schlechter Contract - nicht erzwingbar
public interface User {
    String getName();  // Was wenn null?
    int getAge();      // Was wenn negativ?
}
```

Der Contract sagt nichts über Constraints. Clients müssen überall defensive Prüfungen machen.

```java
// Guter Contract - erzwingbar
public interface User {
    Name name();  // Name ist Value Object, kann nicht null/invalid sein
    Age age();    // Age ist Value Object, kann nicht negativ sein
}

public final class Name {
    private final String value;
    
    public Name(String value) {
        if (value == null || value.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        this.value = value.trim();
    }
    
    @Override
    public String toString() {
        return value;
    }
}

public final class Age {
    private final int years;
    
    public Age(int years) {
        if (years < 0 || years > 150) {
            throw new IllegalArgumentException("Invalid age");
        }
        this.years = years;
    }
    
    public int asInt() {
        return years;
    }
}
```

Jetzt ist es unmöglich, einen `User` mit invaliden Daten zu erstellen. Der Contract wird vom Compiler erzwungen.

### 8.2 How Interfaces are Refactoring our Code

Andronache zeigt in "How Interfaces are Refactoring our Code": Interfaces zwingen zu besserem Design.

```java
// Ohne Interface - tight coupling
public final class FileStorage {
    public void save(String key, String data) {
        Files.write(Paths.get(key), data.getBytes());
    }
}

public final class Application {
    private final FileStorage storage = new FileStorage();
    
    public void saveUser(String id, String name) {
        storage.save(id, name);
    }
}
```

`Application` ist tight coupled an `FileStorage`. Testing schwierig, ändern schwierig.

```java
// Mit Interface - loose coupling
public interface Storage {
    void save(String key, String data);
}

public final class FileStorage implements Storage {
    @Override
    public void save(String key, String data) {
        Files.write(Paths.get(key), data.getBytes());
    }
}

public final class Application {
    private final Storage storage;
    
    public Application(Storage storage) {
        this.storage = storage;
    }
    
    public void saveUser(String id, String name) {
        storage.save(id, name);
    }
}
```

Jetzt können wir `MemoryStorage`, `DatabaseStorage`, etc. injizieren. Testing einfach, Änderungen einfach.

### 8.3 Constructor-Driven Contracts

Bugayenko in "Constructors Must Be Code-Free": Konstruktoren sollten nur Zuweisungen enthalten, keine Logik.

```java
// FALSCH - Logik im Konstruktor
public final class User {
    private final String name;
    
    public User(String name) {
        if (name.length() < 3) {  // Validation logic
            throw new IllegalArgumentException();
        }
        this.name = name.toUpperCase();  // Transformation logic
    }
}
```

Problem: Kann nicht lazy sein, kann nicht getestet werden ohne Seiteneffekte.

```java
// RICHTIG - Logik in Objekten
public final class User {
    private final Name name;
    
    public User(Name name) {
        this.name = name;  // Nur Zuweisung
    }
}

public final class Name {
    private final String value;
    
    public Name(String value) {
        if (value.length() < 3) {
            throw new IllegalArgumentException();
        }
        this.value = value.toUpperCase();
    }
}
```

Validation und Transformation sind in `Name`. `User`-Konstruktor ist code-free.

---

## 9. Composition over Inheritance: Decorator Pattern

### 9.1 Warum Vererbung problematisch ist

Inheritance ist fragil. Eine Änderung in der Parent-Klasse kann Subklassen brechen:

```java
public class Task {
    protected String description;
    
    public void complete() {
        System.out.println("Completing: " + description);
    }
}

public class UrgentTask extends Task {
    @Override
    public void complete() {
        System.out.println("URGENT!");
        super.complete();  // Abhängigkeit von Parent-Implementation
    }
}
```

Wenn `Task.complete()` sich ändert (z.B. Signatur ändert sich), muss `UrgentTask` angepasst werden. Die Parent-Klasse ist fragil - Änderungen propagieren zu allen Subklassen.

Weitere Probleme mit Vererbung:
- **Tight Coupling**: Subklasse ist eng an Parent gebunden
- **Limited Flexibility**: Kann nicht zur Laufzeit geändert werden
- **Multiple Inheritance Problems**: In vielen Sprachen nicht möglich
- **Fragile Base Class Problem**: Parent-Änderungen brechen Subklassen

### 9.2 Composition: Die flexible Alternative

Composition bedeutet, dass ein Objekt andere Objekte enthält und deren Funktionalität nutzt, statt sie zu erben.

```java
// RICHTIG - Composition
public interface Task {
    void complete();
}

public final class SimpleTask implements Task {
    private final String description;
    
    public SimpleTask(String description) {
        this.description = description;
    }
    
    @Override
    public void complete() {
        System.out.println("Completing: " + description);
    }
}

public final class UrgentTask implements Task {
    private final Task origin;
    
    public UrgentTask(Task origin) {
        this.origin = origin;
    }
    
    @Override
    public void complete() {
        System.out.println("URGENT!");
        origin.complete();
    }
}
```

Jetzt ist `UrgentTask` nicht mehr abhängig von der Implementation von `Task`. Es verwendet das Interface, nicht die konkrete Klasse.

### 9.3 Decorator Pattern in der Praxis

Das Decorator Pattern ist die objektorientierte Antwort auf Utility-Klassen. Yegor Bugayenko demonstriert in "OOP Alternative to Utility Classes" (2014), wie Decorators Utility-Klassen ersetzen.

**Anti-Pattern: Utility Class**

```java
// FALSCH - Utility Class
public final class StringUtils {
    public static String encrypt(String text) {
        // Verschlüsselungslogik
    }
    
    public static String compress(String text) {
        // Kompressionslogik
    }
}

// Usage
String result = StringUtils.compress(
    StringUtils.encrypt(original)
);
```

Probleme:
- Prozedural, nicht objektorientiert
- Schwer erweiterbar
- Schwer testbar
- Keine Polymorphie möglich

**Lösung: Decorator Chain**

```java
// RICHTIG - Decorator Pattern
public interface Text {
    String value();
}

public final class SimpleText implements Text {
    private final String text;
    
    public SimpleText(String text) {
        this.text = text;
    }
    
    @Override
    public String value() {
        return text;
    }
}

public final class EncryptedText implements Text {
    private final Text origin;
    
    public EncryptedText(Text origin) {
        this.origin = origin;
    }
    
    @Override
    public String value() {
        return encrypt(origin.value());
    }
    
    private String encrypt(String text) {
        // Verschlüsselungslogik
    }
}

public final class CompressedText implements Text {
    private final Text origin;
    
    public CompressedText(Text origin) {
        this.origin = origin;
    }
    
    @Override
    public String value() {
        return compress(origin.value());
    }
    
    private String compress(String text) {
        // Kompressionslogik
    }
}

// Usage
Text result = new CompressedText(
    new EncryptedText(
        new SimpleText(original)
    )
);
```

Vorteile:
- Objektorientiert
- Leicht erweiterbar (neue Decorators hinzufügen)
- Testbar (jeder Decorator einzeln testbar)
- Flexible Komposition zur Laufzeit
- Keine statischen Methoden

### 9.4 Composable Decorators

Bugayenko zeigt in "Composable Decorators" (2015), wie Decorators komponierbar gemacht werden können:

```java
public interface Storage {
    void save(String key, byte[] data);
    byte[] load(String key);
}

public final class FileStorage implements Storage {
    private final Path directory;
    
    @Override
    public void save(String key, byte[] data) {
        Files.write(directory.resolve(key), data);
    }
    
    @Override
    public byte[] load(String key) {
        return Files.readAllBytes(directory.resolve(key));
    }
}

public final class EncryptedStorage implements Storage {
    private final Storage origin;
    private final Cipher cipher;
    
    @Override
    public void save(String key, byte[] data) {
        origin.save(key, cipher.encrypt(data));
    }
    
    @Override
    public byte[] load(String key) {
        return cipher.decrypt(origin.load(key));
    }
}

public final class CachedStorage implements Storage {
    private final Storage origin;
    private final Map<String, byte[]> cache = new ConcurrentHashMap<>();
    
    @Override
    public void save(String key, byte[] data) {
        origin.save(key, data);
        cache.put(key, data);
    }
    
    @Override
    public byte[] load(String key) {
        return cache.computeIfAbsent(key, origin::load);
    }
}

// Flexible Komposition
Storage storage = new CachedStorage(
    new EncryptedStorage(
        new FileStorage(Paths.get("/data"))
    )
);
```

Jeder Decorator kann unabhängig entwickelt, getestet und kombiniert werden.

### 9.5 Vertical vs. Horizontal Decorating

Bugayenko unterscheidet in "Vertical and Horizontal Decorating" (2015) zwei Arten von Decorators:

**Horizontal Decorators**: Fügen neue Funktionalität hinzu, die orthogonal zur ursprünglichen ist.

```java
// Logging ist orthogonal zur Speicherung
public final class LoggedStorage implements Storage {
    private final Storage origin;
    private final Logger logger;
    
    @Override
    public void save(String key, byte[] data) {
        logger.info("Saving " + key);
        origin.save(key, data);
        logger.info("Saved " + key);
    }
    
    @Override
    public byte[] load(String key) {
        logger.info("Loading " + key);
        byte[] result = origin.load(key);
        logger.info("Loaded " + key);
        return result;
    }
}
```

**Vertical Decorators**: Ändern oder erweitern das Kernverhalten.

```java
// Encryption ändert die Daten selbst
public final class EncryptedStorage implements Storage {
    private final Storage origin;
    private final Cipher cipher;
    
    @Override
    public void save(String key, byte[] data) {
        origin.save(key, cipher.encrypt(data));  // Verändert Daten
    }
    
    @Override
    public byte[] load(String key) {
        return cipher.decrypt(origin.load(key));  // Verändert Daten
    }
}
```

Beide Arten sind wertvoll und können kombiniert werden.

### 9.6 Temporal Coupling vermeiden

Bugayenko warnt in "Temporal Coupling Between Method Calls" (2015) vor temporaler Kopplung - wenn Methoden in einer bestimmten Reihenfolge aufgerufen werden müssen:

```java
// FALSCH - Temporal Coupling
public class Document {
    public void open() { ... }
    public void read() { ... }  // Muss nach open() aufgerufen werden
    public void close() { ... } // Muss nach read() aufgerufen werden
}

// Client muss Reihenfolge kennen
document.open();
document.read();
document.close();
```

Lösung durch Decorators:

```java
// RICHTIG - Kein Temporal Coupling
public interface Document {
    String content();
}

public final class FileDocument implements Document {
    private final Path path;
    
    @Override
    public String content() {
        // open, read, close in einer Methode
        return new String(Files.readAllBytes(path));
    }
}
```

### 9.7 Composition in DDD: Task-Beispiel

Zurück zu unserem Task-Beispiel. Mit Composition können wir flexible Features hinzufügen:

```java
public interface Task {
    Task complete();
    Task.View asView();
}

// Basis-Implementation
public final class SimpleTask implements Task {
    private final String id;
    private final String description;
    private final boolean completed;
    
    @Override
    public Task complete() {
        return new SimpleTask(id, description, true);
    }
    
    @Override
    public Task.View asView() {
        return new TaskView(id, description, completed);
    }
}

// Decorator: Urgent
public final class UrgentTask implements Task {
    private final Task origin;
    
    public UrgentTask(Task origin) {
        this.origin = origin;
    }
    
    @Override
    public Task complete() {
        // Zusätzliches Verhalten
        sendUrgentNotification();
        return new UrgentTask(origin.complete());
    }
    
    @Override
    public Task.View asView() {
        Task.View view = origin.asView();
        view.setUrgent(true);  // Markierung hinzufügen
        return view;
    }
    
    private void sendUrgentNotification() {
        // Notification-Logik
    }
}

// Decorator: Logged
public final class LoggedTask implements Task {
    private final Task origin;
    private final Logger logger;
    
    @Override
    public Task complete() {
        logger.info("Completing task");
        Task result = origin.complete();
        logger.info("Task completed");
        return new LoggedTask(result, logger);
    }
    
    @Override
    public Task.View asView() {
        return origin.asView();
    }
}

// Flexible Komposition
Task task = new LoggedTask(
    new UrgentTask(
        new SimpleTask("Fix critical bug")
    ),
    logger
);
```

### 9.8 Fear of Coupling

Bugayenko argumentiert in "Fear of Coupling" (2018): Coupling ist nicht per se schlecht. Was zählt, ist die Art des Couplings.

**Schlechtes Coupling**: Semantisches Coupling durch Datenexposition
**Gutes Coupling**: Coupling durch Interfaces und Contracts

Composition mit Interfaces erzeugt gutes Coupling:
- Decorators sind an Interfaces gekoppelt, nicht an Implementationen
- Contracts sind klar definiert
- Änderungen propagieren nicht
- Tests sind einfach (Mock das Interface)

---

## 10. Package Design

### 10.1 Das Problem mit traditionellem Packaging

In "Happy Packaging" zeigt Bräutigam, dass die traditionelle Paketstruktur nach technischen Schichten problematisch ist:

```
com.company.app/
  controller/
    TaskController.java
    UserController.java
  service/
    TaskService.java
    UserService.java
  repository/
    TaskRepository.java
    UserRepository.java
  model/
    Task.java
    User.java
```

Probleme:
- **Technische Trennung**: Packages nach Technik, nicht nach Business
- **Scattered Features**: Ein Feature ist über viele Packages verteilt
- **Hohe Kopplung**: Alle Packages kennen "model"
- **Schwache Kohäsion**: Zusammengehöriges ist getrennt

Robert C. Martin's Package Design Principles (aus den bereitgestellten PDFs) zeigen nur die Package  Principles, jedoch keine besseren Wege.

### 10.2 Package Cohesion Principles

**Common Closure Principle (CCP)**: "Classes which change together belong together"

Klassen, die sich aus den gleichen Gründen ändern, sollten im gleichen Package sein. Dies minimiert die Anzahl der Packages, die bei einer Änderung angefasst werden müssen.

**Common Reuse Principle (CRP)**: "Classes in packages should be reused together"

Klassen in einem Package sollten gemeinsam wiederverwendet werden. Wenn ein Client nur eine Klasse aus einem Package braucht, sollte das Package aufgeteilt werden.

**Reuse Release Equivalence Principle (REP)**: "The unit of reuse is the unit of release"

Ein Package sollte als Einheit versioniert und released werden können. Einzelne Klassen sind keine sinnvollen Reuse-Einheiten.

### 10.3 Package Coupling Principles

**Acyclic Dependencies Principle (ADP)**: "The dependency structure for packages must be a Directed Acyclic Graph"

Es darf keine zyklischen Abhängigkeiten zwischen Packages geben. Zyklen führen zum "Morning After Syndrome" - man kommt morgens und der Code ist kaputt, weil jemand ein abhängiges Package geändert hat.

**Stable Dependencies Principle (SDP)**: "Dependencies should point in the direction of stability"

Instabile Packages (die sich oft ändern) sollten von stabilen Packages (die sich selten ändern) abhängen, nicht umgekehrt.

**Stable Abstractions Principle (SAP)**: "Stable packages should be abstract packages"

Stabile Packages sollten abstrakt sein (Interfaces), damit sie erweiterbar bleiben trotz ihrer Stabilität.

### 10.4 3 Paketdesign Regeln

Fast jeder Entwickler beginnt damit, Packages lediglich als ein Werkzeug zur Organisation von Klassen zu nutzen – meist so, wie es in dem Moment logisch erscheint. 
Doch eine Packaging-Strategie kann weitaus mächtiger sein: 
- Sie kann Wissen enthalten,
- Orientierung bieten und die langfristige
- Wartbarkeit der Software erheblich verbessern.


#### 10.4.1 Paketdesign: Modell-Code-Lücke

Die Diskrepanz zwischen Architekturdiagrammen und tatsächlichem Code ist eines der größten ungelösten Probleme der Softwareentwicklung.  
Studien zeigen, dass **82% aller Architekturdiagramme nicht mit der realen Code-Struktur übereinstimmen** (Brown, 2015).  

Diese **mentale Übersetzungslücke** kostet Teams wertvolle Zeit, führt zu Missverständnissen und erhöhten Wartungskosten.  
Sie manifestiert sich in drei Schlüsselbereichen:

1. **Terminologie-Chaos** (Brown, 2015):  
   * Inkonsistente (Paket-)namen durch mehrdeutige Terminologie  
   * Beispiel: *Service* kann bedeuten:  
     * Spring-Service (`@Service`)  
     * Business-Logik-Klasse  
     * Deployierbares Artefakt  

2. **Modell-Code-Lücke** (Brown, 2015):  
   * 82% der Diagramme stimmen nicht mit der Codestruktur überein  
   * Typisches Teamzitat: *"Our architecture diagrams don't match the code."*  

3. **Technologie-Fokus** (Hauer, 2020):  
   * Package-by-Layer-Ansätze erschweren Business-Feature-Entwicklung  
   * Traditionelle Paketstrukturen folgen technischen Schichten und reflektieren keine Business-Konzepte  
   * Folge: Hohe Kopplung und erschwerte Wartbarkeit  

**Simon Brown (2015):** Propagierte in "Modular Monoliths":
- Klare Modul-Grenzen sind entscheidend
- Modularität verhindert den "Big Ball of Mud"
- Module sollten eigenständige, zusammengehörige Einheiten sein

**Philipp Hauer (2020):** Betonte "Package by Feature" und das Prinzip "KISS > DRY":
- Features sollten selbstständig und unabhängig sein
- Duplication ist besser als falsche Abstraktionen
- Fachliche Gruppierung über technische Schichten

## 10.4.2 Paketdesign: Synthese

- Die Anwendung der **3 pragmatischen Regeln von Robert Bräutigam** zum Strukturieren von Paketen  
- führt zu einem Paketdesign, das sowohl den **Paketdesign-Prinzipien von Robert C. Martin** (Kluth, 2010) entspricht  
- als auch die **Übersetzungslücke zwischen Kontext-Diagrammen und Code-Struktur** schließt.

**Robert Bräutigam (2017):** schlägt in *"Happy Packaging"* eine business-orientierte Struktur vor und Formulierte die **drei goldenen Regeln** für leserfreundliche Packages:
1. Pakete sollen niemals von Sub-Paketen abhängen
2. Sub-Pakete führen keine neuen Konzepte ein, nur Details
3. Pakete sollen fachliche Konzepte widerspiegeln, nicht technische

```
com.company.<name>/      (Namespace)
  App.java               ← Interface  
  Task.java              ← Interface
  Tasks.java             ← Interface
  User.java              ← Interface
  Users.java             ← Interface
  
com.company.<name>.app/  (Startup, DI, Config) 
  SimpleTaskApp.java     ← Console Komposition
  WebTaskApp.java        ← Web Komposition
  
com.company.<name>.task/  (Business Konzept)
  SimpleTask.java        ← Basic Implementation
  SqlTask.java           ← SQL-aware Implementation
  UrgentTask.java        ← Decorator
  LoggedTask.java        ← Decorator
  InMemoryTasks.java     ← Implementation
  SqlTasks.java          ← Implementation
  CachedTasks.java       ← Decorator

com.company.<name>.user/ (Business Konzept)
  SimpleUser.java
  SqlUser.java
  NotifiedUser.java      ← Decorator
  InMemoryUsers.java
  SqlUsers.java
```

Vorteile:
- **Leserfreundlich**: Root Package erzählt eine "Story" 
- **Feature-orientiert**: Alles zu "Task" ist zusammen
- **Hohe Kohäsion**: Zusammengehöriges ist zusammen
- **Niedrige Kopplung**: Klare Abhängigkeiten über Interfaces
- **Flexibel**: Neue Features als neue Decorators
- **Testbar**: Jedes Package isoliert testbar


### 10.5 Interface Segregation auf Package-Ebene

- Die Hauptinterfaces (`App`, `Task/Task`, `User/Users`) sind im Root-Package. 
- Sie definieren den Contract und spiegeln die absrakten Business Konzepte. 
- Subpackages verbergen die Details (Implementierungen) der definierten Kontrakte

```java
// com.company.<name>.Task.java
public interface Task {
    Task complete();
    void display(Task.View view);
    
    interface View {
        String getDescription();
        boolean isCompleted();
    }
}

// com.company.<name>.Tasks.java
public interface Tasks {
    Tasks with(String description);
    Tasks without(String id);
    Task find(String id);
    List<Task.View> display();
}
```

Clients importieren nur die Interfaces:

```java
package com.company.<name>.app;

import com.company.<name>.Task;
import com.company.<name>.Tasks;

public final class TaskController {
    private final Tasks tasks;
    
    public TaskController(Tasks tasks) {
        this.tasks = tasks;
    }
    
    public void addTask(String description) {
        this.tasks = tasks.with(description);
    }
}
```

Die konkrete Implementation wird per Dependency Injection bereitgestellt:

```java
package com.company.<name>.app;

import com.company.<name>.task.CachedTasks;
import com.company.<name>.task.SqlTasks;
import com.company.<name>.task.Tasks;
import com.company.<name>.task.TaskController;

// Application Bootstrap
Tasks tasks = new CachedTasks(
    new SqlTasks(connection)
);

TaskController controller = new TaskController(tasks);
```

### 10.6 Package Stability Analysis

Aus den bereitgestellten Folien: Wir können Package-Stabilität quantifizieren:

**Instability Metric (I)**:
```
I = Ce / (Ca + Ce)

Ce = Efferent Couplings (outgoing dependencies)
Ca = Afferent Couplings (incoming dependencies)

I = 0: Maximum Stability (viele abhängig von diesem Package)
I = 1: Maximum Instability (Package abhängig von vielen anderen)
```

**Abstractness Metric (A)**:
```
A = Abstract Classes / Total Classes

A = 0: Completely Concrete
A = 1: Completely Abstract
```

**Main Sequence**: Ideale Packages liegen auf der Linie A + I = 1

Das bedeutet:
- Stabile Packages (I=0) sollten abstrakt sein (A=1)
- Instabile Packages (I=1) sollten konkret sein (A=0)

### 10.7 Praktisches Beispiel: Task Package Structure

```
Stability Analysis:
─────────────────────

Package: com.company.app
├── Task.java (Interface)
├── Tasks.java (Interface)
Ca = 5 (TaskController, TaskView, SimpleTask, SqlTask, UrgentTask)
Ce = 0 (keine Dependencies)
I = 0 (maximum stability)
A = 1 (nur Interfaces, vollständig abstrakt)
Perfect! Liegt auf Main Sequence.

Package: com.company.app.task
├── SimpleTask.java
├── SqlTask.java
Ca = 2 (TaskController möglicherweise, Tests)
Ce = 3 (Task Interface, Database, Logger)
I = 0.6 (mittlere Instabilität)
A = 0 (nur konkrete Klassen)
Akzeptabel, nahe Main Sequence.

Package: com.company.app.tasks
├── SqlTasks.java
Ca = 1 (TaskController)
Ce = 4 (Tasks Interface, Task Interface, Database, Task Implementations)
I = 0.8 (hohe Instabilität)
A = 0 (konkret)
Gut! Instabil und konkret, leicht zu ändern.
```

### 10.8 Package by Feature, not by Layer

Die zentrale Erkenntnis: **Package by Feature, not by Layer**.

Statt:
```
controller/
  TaskController.java
  UserController.java
service/
  TaskService.java
  UserService.java
```

Besser:
```
task/
  Task.java
  Tasks.java
  TaskController.java
  SqlTask.java
user/
  User.java
  Users.java
  UserController.java
  SqlUser.java
```

Noch Besser (mit Interface-Segregation):
```
Task.java          ← Root interface
Tasks.java         ← Root interface
task/
  SimpleTask.java
  SqlTask.java
  TaskController.java
tasks/
  SqlTasks.java
```

---

## 11. Persistenz: SQL-Speaking Objects

### 11.1 Das ORM-Problem

Yegor Bugayenko bezeichnet in "ORM Is an Offensive Anti-Pattern" (2014) Object-Relational Mapping als fundamentales Anti-Pattern. ORMs wie Hibernate, JPA, Entity Framework versprechen, die Impedance Mismatch zwischen Objekten und relationalen Datenbanken zu lösen. In Wirklichkeit schaffen sie mehr Probleme, als sie lösen.

**Typischer ORM-Code**:

```java
// Entity
@Entity
@Table(name = "tasks")
public class Task {
    @Id
    @GeneratedValue
    private Long id;
    
    @Column(name = "description")
    private String description;
    
    @Column(name = "completed")
    private boolean completed;
    
    // Getters/Setters
}

// Repository
public interface TaskRepository extends JpaRepository<Task, Long> {
    List<Task> findByCompleted(boolean completed);
}

// Service
@Service
public class TaskService {
    @Autowired
    private TaskRepository repository;
    
    public void completeTask(Long id) {
        Task task = repository.findById(id).orElseThrow();
        task.setCompleted(true);
        repository.save(task);
    }
}
```

Probleme:
- **Anemic Domain Model**: Entities sind reine Datencontainer
- **Lost Encapsulation**: Alle Felder müssen getter/setter haben
- **Hidden Complexity**: ORM-Magic mit Lazy Loading, Proxies, Sessions
- **Performance Problems**: N+1 Queries, ineffizientes Caching
- **Leaky Abstraction**: Transaction Management, Flush, Detached Objects
- **Testing Nightmare**: Braucht laufende Datenbank oder komplexe Mocks

### 11.2 Active Record vs. Data Mapper

Bugayenko unterscheidet in "Active Record" (2016) zwei ORM-Patterns:

**Active Record**: Objekt weiß, wie es sich speichert
**Data Mapper**: Externes Repository speichert Objekt

Beide sind problematisch, aber Active Record ist näher an echter OOP:

```java
// Active Record (besser als Data Mapper, aber immer noch nicht ideal)
public class Task {
    private Long id;
    private String description;
    
    public void save() {
        // Speichert sich selbst
        sql.execute("INSERT INTO tasks...", id, description);
    }
    
    public void load(Long id) {
        // Lädt sich selbst - MUTIERT das Objekt!
        ResultSet rs = sql.execute("SELECT * FROM tasks WHERE id = ?", id);
        this.id = rs.getLong("id");
        this.description = rs.getString("description");
    }
}
```

Problem: `load()` mutiert das Objekt. Nicht immutable!

### 11.3 SQL-Speaking Objects: Die Lösung

Bräutigam zeigt in seiner Präsentation die Lösung: Objekte, die SQL sprechen, aber immutable bleiben.

**Konzept**: Objekt weiß, wie es sich aus der Datenbank liest und in die Datenbank schreibt, aber ohne Mutation.

```java
public interface Task {
    Task complete();
    void persistTo(Connection connection);
}

public final class SqlTask implements Task {
    private final Connection connection;
    private final String id;
    private final String description;
    private final boolean completed;
    
    // Constructor für neue Tasks
    public SqlTask(Connection connection, String description) {
        this.connection = connection;
        this.id = UUID.randomUUID().toString();
        this.description = description;
        this.completed = false;
    }
    
    // Constructor für existierende Tasks (aus DB)
    public SqlTask(Connection connection, String id) {
        this.connection = connection;
        this.id = id;
        
        try (PreparedStatement stmt = connection.prepareStatement(
            "SELECT description, completed FROM tasks WHERE id = ?")) {
            stmt.setString(1, id);
            ResultSet rs = stmt.executeQuery();
            
            if (!rs.next()) {
                throw new IllegalArgumentException("Task not found: " + id);
            }
            
            this.description = rs.getString("description");
            this.completed = rs.getBoolean("completed");
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
    
    // Private constructor für immutable updates
    private SqlTask(Connection connection, String id, 
                    String description, boolean completed) {
        this.connection = connection;
        this.id = id;
        this.description = description;
        this.completed = completed;
    }
    
    @Override
    public Task complete() {
        // Immutable - neue Instanz
        return new SqlTask(connection, id, description, true);
    }
    
    @Override
    public void persistTo(Connection connection) {
        try (PreparedStatement stmt = connection.prepareStatement(
            "INSERT INTO tasks (id, description, completed) VALUES (?, ?, ?) " +
            "ON DUPLICATE KEY UPDATE description=?, completed=?")) {
            stmt.setString(1, id);
            stmt.setString(2, description);
            stmt.setBoolean(3, completed);
            stmt.setString(4, description);
            stmt.setBoolean(5, completed);
            stmt.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### 11.4 Lazy Loading ohne ORM

Wichtig: SQL wird erst beim Zugriff ausgeführt. Der Constructor mit `String id` ist lazy - er lädt erst bei Konstruktion.

Für wirklich lazy Loading können wir einen Decorator verwenden:

```java
public final class LazyTask implements Task {
    private final Connection connection;
    private final String id;
    private Task loaded;
    
    public LazyTask(Connection connection, String id) {
        this.connection = connection;
        this.id = id;
        this.loaded = null;
    }
    
    private Task task() {
        if (loaded == null) {
            loaded = new SqlTask(connection, id);
        }
        return loaded;
    }
    
    @Override
    public Task complete() {
        return task().complete();
    }
    
    @Override
    public void persistTo(Connection connection) {
        task().persistTo(connection);
    }
}
```

### 11.5 Collection-Level: SQL-Speaking Collections

Nicht nur einzelne Objekte, auch Collections können SQL sprechen:

```java
public interface Tasks {
    Tasks with(String description);
    Tasks without(String id);
    Task find(String id);
    List<Task.View> views();
}

public final class SqlTasks implements Tasks {
    private final Connection connection;
    
    public SqlTasks(Connection connection) {
        this.connection = connection;
    }
    
    @Override
    public Tasks with(String description) {
        Task newTask = new SqlTask(connection, description);
        newTask.persistTo(connection);
        return this;
    }
    
    @Override
    public Tasks without(String id) {
        try (PreparedStatement stmt = connection.prepareStatement(
            "DELETE FROM tasks WHERE id = ?")) {
            stmt.setString(1, id);
            stmt.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        return this;
    }
    
    @Override
    public Task find(String id) {
        return new SqlTask(connection, id);
    }
    
    @Override
    public List<Task.View> views() {
        List<Task.View> result = new ArrayList<>();
        
        try (PreparedStatement stmt = connection.prepareStatement(
            "SELECT id, description, completed FROM tasks ORDER BY id")) {
            ResultSet rs = stmt.executeQuery();
            
            while (rs.next()) {
                result.add(new TaskView(
                    rs.getString("id"),
                    rs.getString("description"),
                    rs.getBoolean("completed")
                ));
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        
        return result;
    }
}
```

### 11.6 Transaktionen

Transaktionen werden explizit gehandhabt, nicht versteckt:

```java
public final class TransactionalTasks implements Tasks {
    private final Tasks origin;
    private final Connection connection;
    
    @Override
    public Tasks with(String description) {
        try {
            connection.setAutoCommit(false);
            Tasks result = origin.with(description);
            connection.commit();
            return result;
        } catch (Exception e) {
            connection.rollback();
            throw new RuntimeException(e);
        }
    }
    
    // ... andere Methoden ähnlich
}
```

### 11.7 Polymorphic Input/Output Data

Andronache zeigt in "Polymorphic Input Output Data" (2019), wie man mit verschiedenen Datenformaten umgeht:

```java
public interface Task {
    void saveTo(Output output);
}

public interface Output {
    void write(String key, String value);
    void write(String key, boolean value);
}

public final class SqlOutput implements Output {
    private final PreparedStatement statement;
    
    @Override
    public void write(String key, String value) {
        statement.setString(resolveIndex(key), value);
    }
    
    @Override
    public void write(String key, boolean value) {
        statement.setBoolean(resolveIndex(key), value);
    }
}

public final class JsonOutput implements Output {
    private final JsonGenerator generator;
    
    @Override
    public void write(String key, String value) {
        generator.writeString(key, value);
    }
    
    @Override
    public void write(String key, boolean value) {
        generator.writeBoolean(key, value);
    }
}

// Task kann sich in verschiedenen Formaten speichern
public final class SimpleTask implements Task {
    private final String description;
    private final boolean completed;
    
    @Override
    public void saveTo(Output output) {
        output.write("description", description);
        output.write("completed", completed);
    }
}
```

### 11.8 Vorteile von SQL-Speaking Objects

- **Echte Objektorientierung**: Objekte haben Verhalten, nicht nur Daten
- **Kapselung**: SQL ist im Objekt versteckt
- **Immutability**: Objekte können immutable bleiben
- **Keine Magic**: Kein verstecktes Lazy Loading, keine Proxies
- **Performance**: Volle Kontrolle über SQL-Queries
- **Testbarkeit**: Mock die Connection, nicht ein komplexes ORM
- **Flexibilität**: Objekt entscheidet, wie es gespeichert wird

---

## 12. Fallstudien und Beispiele

### 12.1 Fallstudie: Task Management System

Lassen Sie uns ein vollständiges Task Management System in OO-DDD designen.

**Anforderungen:**
- Benutzer können Tasks erstellen, bearbeiten, löschen
- Tasks können als erledigt markiert werden
- Urgent Tasks senden Benachrichtigungen
- Statistiken über erledigte Tasks
- Persistierung in SQL-Datenbank
- Web-Interface mit JSF

**Traditionelles Design (Layered)**:

```
Packages:
  com.taskmanager.controller/
    TaskController.java
  com.taskmanager.service/
    TaskService.java
  com.taskmanager.repository/
    TaskRepository.java
  com.taskmanager.model/
    Task.java
    TaskDTO.java
  com.taskmanager.mapper/
    TaskMapper.java

Lines of Code: ~800
Files: 6
Change Impact: Feature-Änderung betrifft 4-6 Dateien
```

**OO-DDD Design**:

```
Packages:
  com.taskmanager/
    Task.java (Interface)
    Tasks.java (Interface)
  com.taskmanager.task/
    SimpleTask.java
    SqlTask.java
    UrgentTask.java (Decorator)
    TaskView.java
  com.taskmanager.tasks/
    SqlTasks.java
    StatisticsTasks.java (Decorator)
  com.taskmanager.ui/
    TaskController.java

Lines of Code: ~600
Files: 8
Change Impact: Feature-Änderung betrifft 1-2 Dateien
```

**Core Interfaces**:

```java
// com.taskmanager.Task.java
public interface Task {
    String id();
    Task complete();
    Task edit(Task.View view);
    void persistTo(Connection connection);
    Task.View asView();
    
    interface View extends Serializable {
        String getId();
        void setId(String id);
        String getDescription();
        void setDescription(String description);
        boolean getCompleted();
        void setCompleted(boolean completed);
        Action complete();
        Action remove();
        String getStyle();
    }
}

// com.taskmanager.Tasks.java
public interface Tasks {
    Tasks with(String description);
    Tasks without(String id);
    Task find(String id);
    List<Task.View> views();
    Statistics statistics();
    
    interface Statistics {
        int total();
        int completed();
        int pending();
        double completionRate();
    }
}
```

**Implementation**: Bereits in vorherigen Abschnitten gezeigt.

**JSF Controller**:

```java
@Named("tasks")
@SessionScoped
public final class TaskController implements Serializable {
    private Tasks tasks;
    private String newTaskDescription = "";
    private boolean makeUrgent = false;
    
    @PostConstruct
    public void init() {
        Connection connection = // ... get connection
        this.tasks = new StatisticsTasks(
            new SqlTasks(connection)
        );
    }
    
    public void addTask() {
        if (newTaskDescription != null && !newTaskDescription.trim().isEmpty()) {
            Task newTask = new SqlTask(
                connection,
                newTaskDescription.trim()
            );
            
            if (makeUrgent) {
                newTask = new UrgentTask(newTask, notificationService);
            }
            
            newTask.persistTo(connection);
            tasks = tasks.with(newTask.id());
            
            newTaskDescription = "";
            makeUrgent = false;
        }
    }
    
    public void completeTask(String id) {
        Task task = tasks.find(id);
        Task completed = task.complete();
        completed.persistTo(connection);
    }
    
    public void removeTask(String id) {
        tasks = tasks.without(id);
    }
    
    // JSF Getters
    public List<Task.View> getViews() {
        return tasks.views();
    }
    
    public Tasks.Statistics getStatistics() {
        return tasks.statistics();
    }
    
    public String getNewTaskDescription() {
        return newTaskDescription;
    }
    
    public void setNewTaskDescription(String description) {
        this.newTaskDescription = description;
    }
    
    public boolean getMakeUrgent() {
        return makeUrgent;
    }
    
    public void setMakeUrgent(boolean makeUrgent) {
        this.makeUrgent = makeUrgent;
    }
}
```

**JSF View**:

```xml
<h:form>
    <h2>Task Manager</h2>
    
    <!-- Statistics -->
    <div class="statistics">
        <span>Total: #{tasks.statistics.total}</span>
        <span>Completed: #{tasks.statistics.completed}</span>
        <span>Pending: #{tasks.statistics.pending}</span>
        <span>Rate: #{tasks.statistics.completionRate}%</span>
    </div>
    
    <!-- Add New Task -->
    <div class="add-task">
        <h:inputText value="#{tasks.newTaskDescription}" 
                     placeholder="New task..." />
        <h:selectBooleanCheckbox value="#{tasks.makeUrgent}" />
        <span>Urgent</span>
        <h:commandButton value="Add" action="#{tasks.addTask}" />
    </div>
    
    <!-- Task List -->
    <ui:repeat value="#{tasks.views}" var="task">
        <div class="#{task.style}">
            <span class="description">#{task.description}</span>
            
            <h:commandButton value="Complete"
                           action="#{task.complete.call()}"
                           rendered="#{task.complete.exists()}" />
            
            <h:commandButton value="Remove"
                           action="#{task.remove.call()}" />
        </div>
    </ui:repeat>
</h:form>
```

**Ergebnis-Vergleich**:

| Metrik | Layered | OO-DDD | Vorteil |
|--------|---------|--------|---------|
| Lines of Code | 800 | 600 | -25% |
| Anzahl Dateien | 6 | 8 | Mehr, aber fokussierter |
| Änderung: Neues Feld | 5-6 Dateien | 1-2 Dateien | -70% |
| Änderung: Neue Validierung | 3-4 Dateien | 1 Datei | -75% |
| Änderung: Neues Feature | 4-5 Dateien | 1-2 Dateien | -60% |
| Testbarkeit | Schwierig (Mocks) | Einfach (Interfaces) | Deutlich besser |
| Verständlichkeit | Fragmentiert | Kohäsiv | Besser |

### 12.2 Fallstudie: E-Commerce Order System

**Anforderungen:**
- Benutzer können Produkte in Warenkorb legen
- Order kann aufgegeben werden
- Order hat verschiedene Status
- Versandoptionen
- Rabattcodes
- Email-Benachrichtigungen

**OO-DDD Design**:

```java
// Interfaces
public interface Order {
    Order addItem(Product product, int quantity);
    Order removeItem(String productId);
    Order applyDiscount(DiscountCode code);
    Order selectShipping(ShippingOption option);
    Order submit();
    Order cancel();
    Amount total();
    Order.View asView();
}

public interface Orders {
    Orders create(String userId);
    Order find(String orderId);
    List<Order.View> forUser(String userId);
}

// Decorators für Features
public final class NotifiedOrder implements Order {
    private final Order origin;
    private final EmailService emailService;
    
    @Override
    public Order submit() {
        Order submitted = origin.submit();
        emailService.sendOrderConfirmation(submitted);
        return new NotifiedOrder(submitted, emailService);
    }
    
    // ... andere Methoden delegieren
}

public final class DiscountedOrder implements Order {
    private final Order origin;
    private final DiscountCode code;
    
    @Override
    public Amount total() {
        return origin.total().multiply(1 - code.percentage());
    }
    
    // ... andere Methoden delegieren
}

// Composition
Order order = new NotifiedOrder(
    new DiscountedOrder(
        new SqlOrder(connection, orderId),
        discountCode
    ),
    emailService
);
```

### 12.3 Realworld-Beispiel: BaBar Physics Software

Aus den bereitgestellten Folien: Das BaBar Particle Physics Experiment verwendete Package Design Principles.

**Problem**: Große Codebasis (mehrere Millionen Lines of Code), viele Entwickler, komplexe Dependencies.

**Lösung**: Stricte Package-Struktur basierend auf:
- Acyclic Dependencies Principle
- Stable Dependencies Principle  
- Stable Abstractions Principle

**Metriken** (aus den Folien):
- D-Metric (Distance from Main Sequence) wurde für alle Packages berechnet
- Packages mit hohem D-Wert wurden identifiziert und refactored
- Problem: Viele Packages waren "zu konkret für ihre Stabilität"

**Lesson Learned**: "Much of the BaBar code at the time was too concrete for its stability. At least the problem was recognised."

### 12.4 Anti-Pattern: Weld/CDI Filter

Bräutigam zeigt in "Data Boundaries" ein Realworld-Beispiel aus Weld (CDI-Implementation):

**Problematischer Code**:

```java
public class WeldFilter {
    private String name;
    private String pattern;
    
    public String getName() { return name; }
    public String getPattern() { return pattern; }
}

// Irgendwo anders
if ((weldFilter.getName() != null && weldFilter.getPattern() != null)
    || (weldFilter.getName() == null && weldFilter.getPattern() == null)) {
    throw new IllegalStateException("...");
}

if (weldFilter.getPattern() != null) {
    this.matcher = new PatternMatcher(...);
} else {
    this.matcher = new AntSelectorMatcher(...);
}
```

**Problem**: Validierungs- und Interpretationslogik ist außerhalb des Objekts. Bei Änderungen an `WeldFilter` müssen alle diese Stellen gefunden und angepasst werden.

**Korrektur**:

```java
public final class WeldFilter {
    private final String name;
    private final String pattern;
    
    public WeldFilter(String name, String pattern) {
        if ((name != null && pattern != null) 
            || (name == null && pattern == null)) {
            throw new IllegalArgumentException(
                "Either name or pattern must be set, but not both"
            );
        }
        this.name = name;
        this.pattern = pattern;
    }
    
    public Matcher createMatcher() {
        return pattern != null 
            ? new PatternMatcher(pattern)
            : new AntSelectorMatcher(name);
    }
}
```

**Ergebnis**: Validierung und Interpretation sind im Objekt. Änderungen sind lokalisiert.

### 12.5 Code Metrics und Quality Gates

Praktische Quality Gates basierend auf Package Principles:

```java
/**
 * Automated checks für Package Quality
 */
public final class PackageQualityGate {
    
    public void checkForCycles() {
        // Fail build if cycles detected
        DependencyGraph graph = analyzeDependencies();
        if (graph.hasCycles()) {
            throw new BuildException(
                "Cyclic dependencies detected: " + graph.cycles()
            );
        }
    }
    
    public void checkStability() {
        for (Package pkg : allPackages()) {
            double instability = calculateInstability(pkg);
            double abstractness = calculateAbstractness(pkg);
            double distance = Math.abs(instability + abstractness - 1);
            
            if (distance > 0.7) {
                warn("Package " + pkg.name() + 
                     " has high distance from main sequence: " + distance);
            }
        }
    }
    
    private double calculateInstability(Package pkg) {
        int ce = pkg.efferentCouplings(); // outgoing
        int ca = pkg.afferentCouplings();  // incoming
        return (double) ce / (ce + ca);
    }
    
    private double calculateAbstractness(Package pkg) {
        int abstractClasses = pkg.abstractClasses().size();
        int totalClasses = pkg.allClasses().size();
        return (double) abstractClasses / totalClasses;
    }
}
```

Diese Metriken können in CI/CD Pipeline integriert werden.

---

## 13. Fazit und Ausblick

### 13.1 Zusammenfassung der Kernprinzipien

Object-Oriented Domain-Driven Design basiert auf fundamentalen Prinzipien, die wir in diesem Artikel untersucht haben:

**1. True Encapsulation**  
Objekte verbergen ihre internen Daten vollständig. Keine Getter/Setter für Zustandsinformationen. Kapselung ermöglicht effektive Abstraktion - Probleme werden gelöst, über die wir nie wieder nachdenken müssen.

**2. Tell, Don't Ask**  
Objekte werden aufgefordert, etwas zu tun, nicht nach ihrem Zustand gefragt. `task.complete()` statt `if (!task.isCompleted()) { task.setCompleted(true); }`.

**3. Behavior over Data**  
Objekte definieren sich durch ihr Verhalten, nicht durch ihre Daten. Sie exponieren Operationen, nicht Attribute.

**4. Composition over Inheritance**  
Flexibilität durch Komposition und Decorator Pattern, nicht durch Vererbungshierarchien.

**5. Domain-Focused Structure**  
Code-Struktur spiegelt Business-Domäne wider, nicht technische Schichten. Packages sind nach Features organisiert, nicht nach Layers.

**6. Immutability**  
Objekte ändern sich nicht - sie erzeugen neue Versionen ihrer selbst. Dies vermeidet temporale Kopplung und macht Code verständlicher.

**7. Contract-Driven**  
Interfaces definieren klare Contracts. Value Objects erzwingen Constraints durch Construction.

**8. SQL-Speaking Objects**  
Objekte wissen, wie sie sich persistieren, statt durch externe Repositories oder ORMs verwaltet zu werden.

### 13.2 Der Paradigmenwechsel

Der Wechsel von traditionellem "Enterprise"-Code zu echtem OO-DDD ist ein fundamentaler Paradigmenwechsel:

**Von Datenorientierung zu Verhaltensorientierung**

Traditionell:
```java
Task task = repository.findById(id);
String description = task.getDescription();
boolean completed = task.isCompleted();
// ... Logik außerhalb des Objekts
```

OO-DDD:
```java
Task task = tasks.find(id);
task.complete();  // Objekt tut etwas
```

**Von Schichten zu Features**

Traditionell:
```
controller/ → service/ → repository/ → database/
```

OO-DDD:
```
Task (Feature) = Interface + Implementations + Decorators + View
```

**Von Mutation zu Transformation**

Traditionell:
```java
task.setCompleted(true);  // Mutation
repository.save(task);
```

OO-DDD:
```java
Task completed = task.complete();  // Neue Instanz
completed.persistTo(connection);
```

### 13.3 Messbare Vorteile

Die Vorteile von OO-DDD sind messbar:

**Wartbarkeit**: 
- 60-75% weniger Dateien müssen bei Feature-Änderungen modifiziert werden
- Änderungen sind lokalisiert in 1-2 Dateien statt 5-6

**Codequalität**:
- 20-30% weniger Lines of Code
- Höhere Kohäsion (messbar durch Package Metrics)
- Niedrigere Kopplung (messbar durch Dependency Metrics)

**Testbarkeit**:
- Unit Tests ohne Mocking-Framework möglich
- Tests sind einfacher und schneller
- Höhere Test-Coverage erreichbar

**Verständlichkeit**:
- Code liest sich wie Domänensprache
- Weniger "Translation" zwischen Business und Code nötig
- Neue Entwickler verstehen Code schneller

### 13.4 Herausforderungen bei der Adoption

Die Umstellung auf OO-DDD ist nicht trivial. Herausforderungen:

**1. Kultureller Widerstand**  
Entwickler sind gewohnt an Layered Architecture, Services, Repositories. "UI im Domain? Das fühlt sich falsch an!" Der Widerstand ist verständlich - wir verlernen jahrzehntelange Gewohnheiten.

**2. Framework-Inkompatibilität**  
Viele Frameworks (Spring, JPA, Hibernate) sind für Bean-basierte Programmierung designed. Das View Pattern ist ein Kompromiss, aber nicht ideal.

**3. Team-Onboarding**  
Neue Teammitglieder müssen umdenken lernen. Training und Mentoring sind essentiell.

**4. Legacy-Code**  
Bestehende Codebasen migrieren ist aufwendig. Incrementelle Migration ist möglich, aber erfordert Disziplin.

**5. Tooling**  
IDEs und Tools sind optimiert für traditionelle Patterns. Refactoring-Tools verstehen OO-DDD nicht automatisch.

### 13.5 Strategien für die Einführung

**Strategie 1: Greenfield Projects**  
Neue Projekte komplett in OO-DDD. Hier ist der Widerstand am geringsten.

**Strategie 2: Feature-by-Feature Migration**  
Bei Legacy-Code: Neue Features in OO-DDD, alte Features schrittweise migrieren.

**Strategie 3: Strangler Fig Pattern**  
Neues System wächst um altes System herum, ersetzt es schrittweise.

**Strategie 4: Training und Pairing**  
Intensive Schulung, Pair Programming mit erfahrenen OO-DDD-Entwicklern.

**Strategie 5: Code Reviews**  
Strikte Code Reviews mit Fokus auf OO-DDD-Prinzipien. Quality Gates für Package Metrics.

### 13.6 Tools und Frameworks

**Elegante Objekte und Cactoos**

Yegor Bugayenko hat mit "Cactoos" eine Java-Bibliothek entwickelt, die OO-Prinzipien konsequent umsetzt:
- Alle Objekte sind immutable
- Keine null-Werte
- Keine checked Exceptions in Business-Code
- Composable Decorators für I/O, Collections, Text

```java
// Beispiel mit Cactoos
Text encrypted = new EncryptedText(
    new TextOf(new File("secret.txt"))
);
```

**Frameworks, die OO-DDD unterstützen:**

- **Vert.x**: Event-driven, non-blocking - gut für message-passing
- **Javalin**: Lightweight Web Framework ohne Bean-Zwang
- **JDBI**: SQL-first statt ORM
- **Gson/Jackson**: JSON ohne Annotations möglich

**Frameworks, die problematisch sind:**

- **Spring Framework**: Dependency Injection Container, Bean-basiert
- **Hibernate/JPA**: ORM mit allen bekannten Problemen
- **Java EE CDI**: Bean Management mit Proxies

Das bedeutet nicht, dass diese Frameworks nicht nutzbar sind - aber sie ermutigen zu Patterns, die OO-DDD widersprechen.

### 13.7 Die Rolle von KI und Code-Generierung

Eine kritische Beobachtung: Moderne KI-Systeme (ChatGPT, GitHub Copilot) perpetuieren schlechte Patterns, weil sie auf existierendem Code trainiert wurden. Wenn wir KI bitten, "einen Task-Service zu schreiben", produziert sie typischerweise:

```java
@Service
public class TaskService {
    @Autowired
    private TaskRepository repository;
    
    public TaskDTO getTask(Long id) { ... }
    public void updateTask(TaskDTO dto) { ... }
}
```

Die KI "lernt" aus Millionen von Lines schlechten Codes im Internet. Das verschlimmert das Problem.

**Lösung**: Explizite Prompts für OO-DDD:
- "Create a Task interface with behavior methods, not getters"
- "Implement Task with immutability and encapsulation"
- "Use decorator pattern instead of inheritance"

Wir müssen KI aktiv trainieren und korrigieren.

### 13.8 Die Zukunft der Softwareentwicklung

**Trend 1: Funktionale Programmierung**  
Funktionale Programmierung (FP) teilt viele Werte mit OO-DDD:
- Immutability
- Pure Functions (keine Seiteneffekte)
- Composition
- Keine null-Werte

FP und OO-DDD sind keine Gegensätze. Sie können kombiniert werden.

**Trend 2: Domain-Driven Design Revival**  
DDD erlebt eine Renaissance. Bounded Contexts, Event Sourcing, CQRS werden populärer. OO-DDD passt perfekt in diese Architektur.

**Trend 3: Microservices**  
Microservices erfordern klare Boundaries. OO-DDD mit seinen klar definierten Interfaces und ohne Data Boundaries ist ideal für Microservices.

**Trend 4: Event-Driven Architecture**  
Events sind Nachrichten zwischen Objekten - das ursprüngliche OOP-Konzept nach Alan Kay. OO-DDD ist natürlich event-driven.

**Trend 5: Cloud-Native**  
Cloud-native Entwicklung erfordert:
- Stateless Services (Immutability hilft)
- Clear Contracts (Interfaces)
- Resilience (Decorators für Retry, Circuit Breaker)

OO-DDD ist cloud-native-ready.

### 13.9 Offene Fragen und Diskussionspunkte

**Frage 1: Wie weit geht UI im Domain?**

Kontrovers: Soll ein Domain-Objekt HTML generieren? JSON? Die Community ist gespalten:

Pro:
- UI ist Teil der Anforderungen
- Kapselung bis zur Darstellung
- Keine Data Boundaries

Contra:
- Separation of Concerns
- Designer müssen nicht Java können
- Wiederverwendung bei verschiedenen UIs

Pragmatischer Kompromiss: View Pattern - Domain-Objekt erzeugt View-DTO, das UI-spezifisch ist.

**Frage 2: Wie testet man SQL-Speaking Objects?**

Zwei Ansätze:
1. In-Memory Database (H2, SQLite) für Tests
2. Test Containers mit echter Datenbank

Oder: Mock die Connection mit Test-Doubles.

**Frage 3: Performance von Immutability?**

Kritik: "Ständig neue Objekte erzeugen ist langsam!"

Antwort:
- Moderne JVMs sind sehr effizient mit kurzlebigen Objekten
- Escape Analysis eliminiert viele Allocations
- In der Praxis: Performance-Unterschied ist minimal
- Wartbarkeit ist wichtiger als Micro-Optimierungen

**Frage 4: Wie handhabt man komplexe Queries?**

SQL-Speaking Objects sind gut für CRUD. Aber was ist mit komplexen Reports, Aggregationen, Joins?

Antwort:
- Queries können als eigene Objekte modelliert werden
- `TaskReport`, `TaskStatistics` etc.
- Diese können spezialisierte SQL haben
- Oder: CQRS - Command (write) vs Query (read) trennen

### 13.10 Call to Action

Die Softwareindustrie hat sich von echten OOP-Prinzipien entfernt. Object-Oriented Domain-Driven Design zeigt den Weg zurück - nicht aus nostalgischen Gründen, sondern weil diese Prinzipien zu wartbarerem, verständlicherem Code führen.

**Für Entwickler:**
- Hinterfrage Getter/Setter in deinem Code
- Experimentiere mit Immutability und Decorators
- Lies die Arbeiten von Bugayenko, Bräutigam, Andronache
- Praktiziere Tell, Don't Ask

**Für Architekten:**
- Evaluiere Layered Architecture kritisch
- Erwäge Feature-basierte Package-Strukturen
- Messe Package-Metriken (Instability, Abstractness)
- Setze Quality Gates basierend auf Cohesion/Coupling

**Für Teams:**
- Diskutiert diese Konzepte
- Macht Code Reviews mit Fokus auf Encapsulation
- Experimentiert in Side-Projects
- Teilt Erkenntnisse in der Community

**Für die Community:**
- Schreibt mehr über echte OOP
- Erstellt Tutorials und Beispiele
- Entwickelt Tools, die OO-DDD unterstützen
- Korrigiert KI-generierte Anti-Patterns

### 13.11 Schlusswort

Alan Kay sagte einmal: "I invented the term 'Object-Oriented', and I can tell you I did not have C++ in mind." Was wir heute als objektorientierte Programmierung praktizieren, ist oft weit entfernt von der ursprünglichen Vision.

Object-Oriented Domain-Driven Design ist ein Versuch, zu diesen Wurzeln zurückzukehren - nicht aus Purismus, sondern weil diese Prinzipien zu besserer Software führen. Software, die:
- Leichter zu verstehen ist
- Einfacher zu warten ist
- Flexibler erweiterbar ist
- Robuster und testbarer ist

Der Weg dorthin ist nicht einfach. Er erfordert Umdenken, Lernen und Übung. Aber die Ergebnisse rechtfertigen den Aufwand.

Die Frage ist nicht, ob wir uns ändern sollten. Die Frage ist, wann wir anfangen.

---

## 14. Quellenverzeichnis

### Bücher

**Evans, Eric (2003)**  
*Domain-Driven Design: Tackling Complexity in the Heart of Software*  
Addison-Wesley Professional  
ISBN: 978-0321125215

Das grundlegende Werk zu DDD. Einführung von Ubiquitous Language, Bounded Contexts, Building Blocks.

**Martin, Robert C. (2017)**  
*Clean Architecture: A Craftsman's Guide to Software Structure and Design*  
Prentice Hall  
ISBN: 978-0134494166

Umfassende Behandlung von Architektur-Prinzipien, SOLID, Component Design.

**West, David (2004)**  
*Object Thinking*  
Microsoft Press  
ISBN: 978-0735619654

Philosophische Grundlagen der Objektorientierung. Betont die Bedeutung von Objekten als autonome Entitäten.

### Artikel und Blog Posts

**Bräutigam, Robert**  
"Object-Oriented Domain Driven Design" (Präsentation)  
MATHEMA Software GmbH  
https://javadevguy.wordpress.com/

Kernpräsentation, die OO und DDD vereint. Kritik an traditionellen DDD Building Blocks.

**Bräutigam, Robert (2019)**  
"Data Boundaries are the root cause of Maintenance Problems"  
https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/

Analyse, wie Data-oriented Boundaries zu Wartungsproblemen führen.

**Bräutigam, Robert (2019)**  
"Reevaluating the Layered Architecture"  
https://javadevguy.wordpress.com/2019/01/06/reevaluating-the-layered-architecture/

Kritik an Layered Architecture, Alternative Ansätze.

**Bräutigam, Robert (2017)**  
"Happy Packaging"  
https://javadevguy.wordpress.com/2017/12/18/happy-packaging/

Package-Design nach Business-Features statt technischen Schichten.

**Bräutigam, Robert (2017)**  
"The Genius of the Law of Demeter"  
https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/

Erklärung des Law of Demeter und seiner Bedeutung für Wartbarkeit.

**Bugayenko, Yegor (2014)**  
"Getters/Setters. Evil. Period."  
https://www.yegor256.com/2014/09/16/getters-and-setters-are-evil.html

Fundamentale Kritik an Getter/Setter-Pattern.

**Bugayenko, Yegor (2016)**  
"Printers Instead of Getters"  
https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html

Praktische Alternative zu Getters: Objekte schreiben sich selbst.

**Bugayenko, Yegor (2016)**  
"Naked Data"  
https://www.yegor256.com/2016/11/21/naked-data.html

Analyse des Problems von "nackten Daten" ohne Verhalten.

**Bugayenko, Yegor (2015)**  
"Don't Create Objects That End With -ER"  
https://www.yegor256.com/2015/03/09/objects-end-with-er.html

Über schlechte Objektnamen und was sie über Design aussagen.

**Bugayenko, Yegor (2015)**  
"Compound Name Is a Code Smell"  
https://www.yegor256.com/2015/01/12/compound-name-is-code-smell.html

Zusammengesetzte Namen als Indikator für Design-Probleme.

**Bugayenko, Yegor (2017)**  
"Evil Object Name Suffix: Client"  
https://www.yegor256.com/2017/09/12/evil-object-name-suffix-client.html

Über problematische Suffixe wie -Client, -Server, -Manager.

**Bugayenko, Yegor (2014)**  
"Objects Should Be Immutable"  
https://www.yegor256.com/2014/06/09/objects-should-be-immutable.html

Argumente für Immutability in objektorientiertem Code.

**Bugayenko, Yegor (2014)**  
"How Immutability Helps"  
https://www.yegor256.com/2014/11/07/how-immutability-helps.html

Praktische Vorteile von Immutability.

**Bugayenko, Yegor (2015)**  
"Constructors Must Be Code-Free"  
https://www.yegor256.com/2015/05/07/ctors-must-be-code-free.html

Warum Konstruktoren keine Logik enthalten sollten.

**Bugayenko, Yegor (2014)**  
"OOP Alternative to Utility Classes"  
https://www.yegor256.com/2014/05/05/oop-alternative-to-utility-classes.html

Wie Decorator Pattern Utility Classes ersetzt.

**Bugayenko, Yegor (2015)**  
"Composable Decorators"  
https://www.yegor256.com/2015/02/26/composable-decorators.html

Über komponierbare Decorators und ihre Flexibilität.

**Bugayenko, Yegor (2015)**  
"Vertical and Horizontal Decorating"  
https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html

Unterscheidung zwischen verschiedenen Arten von Decorators.

**Bugayenko, Yegor (2015)**  
"Temporal Coupling Between Method Calls"  
https://www.yegor256.com/2015/12/08/temporal-coupling-between-method-calls.html

Über das Problem, wenn Methoden in bestimmter Reihenfolge aufgerufen werden müssen.

**Bugayenko, Yegor (2014)**  
"ORM Is an Offensive Anti-Pattern"  
https://www.yegor256.com/2014/12/01/orm-offensive-anti-pattern.html

Fundamentale Kritik an Object-Relational Mapping.

**Bugayenko, Yegor (2016)**  
"Active Record"  
https://www.yegor256.com/2016/07/26/active-record.html

Diskussion des Active Record Pattern und seiner Probleme.

**Bugayenko, Yegor (2016)**  
"Data Transfer Object Is a Code Smell"  
https://www.yegor256.com/2016/07/06/data-transfer-object.html

Warum DTOs problematisch sind.

**Bugayenko, Yegor (2014)**  
"DI Containers Are Code Polluters"  
https://www.yegor256.com/2014/10/03/di-containers-are-evil.html

Kritik an Dependency Injection Containers.

**Bugayenko, Yegor (2018)**  
"Fear of Coupling"  
https://www.yegor256.com/2018/09/18/fear-of-coupling.html

Unterscheidung zwischen gutem und schlechtem Coupling.

**Bugayenko, Yegor (2014)**  
"Seven Virtues of a Good Object"  
https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html

Zusammenfassung der Eigenschaften guter Objekte.

**Andronache, Mihai Emil (2017)**  
"But how do you work without a model?"  
https://amihaiemil.com/2017/11/04/but-how-do-you-work-without-a-model.html

Antwort auf die häufigste Frage zu OO ohne Datenmodelle.

**Andronache, Mihai Emil (2018)**  
"My take on Object Naming"  
https://amihaiemil.com/2018/01/07/my-take-on-object-naming.html

Über gute Objektnamen und Intention vs. Implementation.

**Andronache, Mihai Emil (2017)**  
"How Interfaces are Refactoring our Code"  
https://amihaiemil.com/2017/08/12/how-interfaces-are-refactoring-our-code.html

Wie Interfaces zu besserem Design zwingen.

**Andronache, Mihai Emil (2019)**  
"Hide It All"  
https://amihaiemil.com/2019/06/12/hide-it-all.html

Über totale Kapselung und ihre Vorteile.

**Andronache, Mihai Emil (2018)**  
"An Extension to Tell Don't Ask"  
https://amihaiemil.com/2018/11/10/an-extension-to-telldontask.html

Erweiterung des Tell-Don't-Ask-Prinzips.

**Andronache, Mihai Emil (2017)**  
"Data Should Be Animated, Not Represented"  
https://amihaiemil.com/2017/09/01/data-should-be-animated-not-represented.html

Philosophie von Daten als Verhalten, nicht als Repräsentation.

**Andronache, Mihai Emil (2019)**  
"Polymorphic Input Output Data"  
https://amihaiemil.com/2019/03/31/polymorphic-input-output-data.html

Wie man mit verschiedenen Datenformaten polymorphisch umgeht.

**Andronache, Mihai Emil (2018)**  
"Dolls and Maquettes"  
https://amihaiemil.com/2018/04/17/dolls-and-maquettes.html

Über Object Construction und Composability.

**Andronache, Mihai Emil (2020)**  
"The Almighty Service Layer"  
https://amihaiemil.com/2020/05/14/the-almighty-service-layer.html

Kritik an übermächtigen Service-Schichten.

**Žurauskas, Vytautas (2019)**  
"It's not a contract if you can't enforce it"  
https://www.vzurauskas.com/2019/02/04/its-not-a-contract-if-you-cant-enforce-it

Über durchsetzbare Contracts in Interfaces.

**Žurauskas, Vytautas (2018)**  
"Static Methods Violate Dependency Inversion Principle"  
https://www.vzurauskas.com/2018/09/09/static-methods-violate-dependency-inversion-principle

Warum statische Methoden problematisch sind.

**Žurauskas, Vytautas (2018)**  
"Maintaining Model Integrity"  
https://www.vzurauskas.com/2018/07/24/maintaining-model-integrity

Wie man Model-Integrität durch Construction sicherstellt.

**Žurauskas, Vytautas (2019)**  
"Wine and Potency"  
https://www.vzurauskas.com/2019/08/11/wine-and-potency

Philosophische Betrachtung von Object Construction.

**Žurauskas, Vytautas (2021)**  
"Factories and Packages"  
https://www.vzurauskas.com/2021/04/07/factories-and-packages

Über Factory Pattern und Package-Design.

**Holub, Allen (2003)**  
"Why getter and setter methods are evil"  
JavaWorld  
https://www.infoworld.com/article/2161183/why-getter-and-setter-methods-are-evil.html

Klassischer Artikel über die Probleme mit Getters/Setters.

**Holub, Allen (2003)**  
"More on getters and setters"  
JavaWorld  
https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html

Follow-up mit praktischen Alternativen.

**ThoughtWorks (2013)**  
"Composition vs. Inheritance: How to Choose"  
https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose

Praktischer Leitfaden zur Wahl zwischen Composition und Inheritance.

### Akademische Papers und Präsentationen

**Martin, Robert C.**  
"The Principles of OOD"  
http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod

SOLID-Prinzipien und Package Design Principles.

**Kluth, Stefan**  
"OO Package Design Principles"  
Presentation Slides (bereitgestellt)

Umfassende Behandlung von Package Cohesion und Coupling Principles.

**Bräutigam, Robert**  
"Single Responsibility Principle"  
MATHEMA Software GmbH (Präsentation, bereitgestellt)

Pragmatische Definition von SRP basierend auf Cohesion und Coupling.

### Code-Bibliotheken und Frameworks

**Cactoos**  
https://github.com/yegor256/cactoos  
Java-Bibliothek mit konsequent objektorientierten Collections, I/O, Text-Handling.

**EO (Elegant Objects)**  
https://www.elegantobjects.org/  
Community und Ressourcen rund um elegante, objektorientierte Programmierung.

### Weitere Ressourcen

**Yegor Bugayenko's Blog**  
https://www.yegor256.com/  
Umfangreiche Sammlung von Artikeln zu OOP, DDD, Software Design.

**Robert Bräutigam's Blog "JavaDevGuy"**  
https://javadevguy.wordpress.com/  
Artikel und Präsentationen zu OO-DDD, Architektur, Java.

**Mihai Emil Andronache's Blog**  
https://amihaiemil.com/  
Praktische Artikel zu objektorientiertem Design.

**Vytautas Žurauskas' Blog**  
https://www.vzurauskas.com/  
Philosophische und praktische Betrachtungen zu OOP.

---

## Anhang A: Glossar

**Anemic Domain Model**: Domain-Objekte ohne Verhalten, nur Daten mit Getters/Setters. Anti-Pattern.

**Bounded Context**: In DDD, ein expliziter Kontext, in dem ein Modell gültig ist.

**Cohesion**: Maß dafür, wie stark zusammengehörige Elemente in einem Modul sind.

**Composition**: Design-Technik, bei der Objekte andere Objekte enthalten und deren Funktionalität nutzen.

**Coupling**: Maß dafür, wie stark Module voneinander abhängig sind.

**Data Boundary**: Schnittstelle, die primär aus Daten besteht, führt zu semantischer Kopplung.

**Decorator Pattern**: Design Pattern, bei dem Objekte durch Wrapping erweitert werden.

**DIP (Dependency Inversion Principle)**: High-level Module sollten nicht von Low-level Modulen abhängen. Beide sollten von Abstraktionen abhängen.

**DTO (Data Transfer Object)**: Objekt, das nur Daten transportiert, ohne Verhalten.

**Encapsulation**: Verbergen interner Implementierungsdetails eines Objekts.

**Immutability**: Eigenschaft eines Objekts, nach seiner Konstruktion nicht mehr änderbar zu sein.

**ISP (Interface Segregation Principle)**: Clients sollten nicht von Interfaces abhängen, die sie nicht nutzen.

**LSP (Liskov Substitution Principle)**: Subtypen müssen austauschbar mit ihren Basistypen sein.

**OCP (Open/Closed Principle)**: Objekte sollten offen für Erweiterung, aber geschlossen für Modifikation sein.

**ORM (Object-Relational Mapping)**: Technik zum Mappen zwischen Objekten und relationalen Datenbanken.

**Semantic Coupling**: Implizite Kopplung durch gemeinsames Verständnis von Datenstrukturen.

**SRP (Single Responsibility Principle)**: Ein Modul sollte nur einen Grund haben, sich zu ändern.

**Tell, Don't Ask**: Prinzip, Objekte aufzufordern etwas zu tun, statt nach ihrem Zustand zu fragen.

**Ubiquitous Language**: Gemeinsame Sprache zwischen Entwicklern und Domänenexperten.

**Value Object**: In DDD, ein Objekt ohne Identität, definiert durch seine Attribute.

**View Pattern**: Pattern für UI-Integration: Objekt exponiert kontrolliert ein View-DTO für Data Binding.

---

## Anhang B: Code-Beispiele Repository

Alle vollständigen Code-Beispiele aus diesem Artikel sind verfügbar in einem GitHub-Repository:

**Repository-Struktur:**
```
oo-ddd-examples/
├── task-management/
│   ├── layered-architecture/     # Traditioneller Ansatz
│   └── oo-ddd/                   # OO-DDD Ansatz
├── ecommerce/
│   └── oo-ddd/                   # E-Commerce Beispiel
├── package-metrics/
│   └── analyzer/                 # Tool zur Package-Analyse
└── decorators/
    └── examples/                 # Decorator Pattern Beispiele
```

**Setup-Anleitung:**
```bash
git clone https://github.com/example/oo-ddd-examples
cd oo-ddd-examples
mvn clean install
mvn test
```

Jedes Beispiel enthält:
- Vollständigen Source Code
- Unit Tests
- Integration Tests
- README mit Erklärungen
- Vergleich zu traditionellem Ansatz

---

## Anhang C: Checkliste für Code Reviews

Nutzen Sie diese Checkliste bei Code Reviews, um OO-DDD-Prinzipien zu überprüfen:

### Encapsulation
- [ ] Keine public Getter für interne Zustandsdaten
- [ ] Keine Setter (außer in View-DTOs)
- [ ] Alle Felder sind `private final`
- [ ] Objekt exponiert Verhalten, nicht Daten

### Immutability
- [ ] Klasse ist `final`
- [ ] Alle Felder sind `final`
- [ ] Keine Mutation des Zustands
- [ ] Änderungen erzeugen neue Instanzen

### Naming
- [ ] Keine -er Suffixe (Manager, Handler, Service)
- [ ] Keine -Client/-Server Suffixe
- [ ] Namen beschreiben "was es ist", nicht "was es tut"
- [ ] Keine übermäßig zusammengesetzten Namen

### Contracts
- [ ] Interfaces definieren Verhalten, nicht Daten-Zugriff
- [ ] Preconditions werden durchgesetzt (im Constructor)
- [ ] Keine null-Rückgabewerte ohne guten Grund
- [ ] Value Objects validieren in Construction

### Composition
- [ ] Bevorzugung von Composition über Inheritance
- [ ] Decorators statt Subklassing
- [ ] Keine tiefen Vererbungshierarchien
- [ ] Flexible Komposition zur Laufzeit

### Package Structure
- [ ] Packages nach Features, nicht nach Schichten
- [ ] Interfaces im Root-Package
- [ ] Implementations in Subpackages
- [ ] Keine zyklischen Dependencies

### Persistence
- [ ] Keine Annotationen wie @Entity, @Column auf Domain-Objekten
- [ ] Objekte wissen, wie sie sich persistieren (oder delegieren explizit)
- [ ] Kein ORM für Domain-Objekte
- [ ] SQL ist kontrolliert und explizit

### Testing
- [ ] Tests benötigen keine Mocking-Frameworks
- [ ] Tests testen Verhalten, nicht Implementierung
- [ ] Unit Tests ohne externe Dependencies
- [ ] Integration Tests mit echten Komponenten

---

**Ende des Artikels**

---

*Dieser Artikel wurde im Januar 2025 verfasst und basiert auf den Arbeiten von Robert Bräutigam (MATHEMA Software GmbH), Yegor Bugayenko (Elegant Objects), Vytautas Žurauskas, Mihai Emil Andronache und anderen Pionieren der objektorientierten Programmierung.*

*Für Feedback, Fragen oder Diskussionen: Siehe die Community-Ressourcen unter www.elegantobjects.org*

*Lizenz: Creative Commons Attribution 4.0 International (CC BY 4.0)*
