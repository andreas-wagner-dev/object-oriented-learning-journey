# OOP UI-Patterns für Todo-Anwendungen
## 1. Allgemeine OO UI Prinzipien mit Todo Usecase
Fundamentale OOP-Regeln (aus den Quellen):
- Keine Getter/Setter: Objekte TUN etwas, statt Daten preiszugeben
- Tell, Don't Ask: task.complete() statt task.setCompleted(true)
- UI ist Business-Logik: Objekte rendern sich selbst (task.display())
- Immutability: Neue Objekte statt Mutation
- No Data Boundaries: Keine DTOs zwischen Schichten
- Decorator über Inheritance: Komposition, keine Vererbung
- Final Classes: Nur durch Dekoration erweiterbar

**Todo als Beispiel-Domain:**
- Task repräsentiert eine echte Aufgabe (nicht TaskManager, TaskService).
- Task weiß, wie er sich abschließt, darstellt und persistiert.

## 2. Konzepte und Varianten
**Variante 1:** User-Speaking Objects (Direct JSF Communication)
```java
public interface Task {
    UIComponent speakToUser();  // Task erstellt eigene JSF Components
}

@Override
public UIComponent speakToUser() {
    HtmlPanelGroup panel = ctx.createComponent(...);
    panel.setStyleClass(completed ? "done" : "pending");
    // Task baut komplettes UI selbst
    return panel;
}
```
**Anwendung:**

- Task erstellt programmatisch JSF UIComponents
- Volle Kontrolle über Rendering

**Vorteile:**

- Maximale Kapselung - UI komplett im Objekt
- Keine EL-Expressions im Template nötig
- Änderungen am UI lokalisiert im Objekt

**Nachteile:**

- Serialization-Probleme mit ActionListeners
- Viel Boilerplate-Code für UI-Erstellung
- Schwierig zu debuggen
- JSF Component-IDs müssen manuell verwaltet werden

**Fazit:** Theoretisch rein, praktisch zu komplex für echte Anwendungen.

**Variante 2:** Present Pattern (Presentation Objects)

```java
public interface Task {
    Present present();  // Task gibt Presentation zurück
}

public interface Present {
    String show();
    String cssClass();
    Actions actions();
}

public final class TaskPresent implements Present {
    @Override
    public String show() {
        return description + (completed ? " ✓" : "");
    }
}
```
**Anwendung:**

- Task delegiert an Present-Objekt
- Present kapselt alle Präsentationslogik
- JSF bindet an Present-Properties

**Vorteile:**

Saubere Trennung: Task (Business) / Present (UI)
Decorator-Pattern funktioniert: UrgentPresent(task.present())
Testbar - Present ist einfach zu mocken
Serialization-safe

Nachteile:**

- Zusätzliche Abstraktionsschicht
- Present-Objekte müssen parallel zu Tasks gepflegt werden
- Für einfache UIs overhead

**Fazit:** Guter Kompromiss für mittlere Komplexität.

**Variante 3:** Horizontale Interface-Erweiterung
```java
public interface Task {
    // Business
    Task complete();
    
    // Horizontale UI-Erweiterung
    String text();
    String style();
    TaskAction action();
}
```
**Anwendung:**

- Interface wird um UI-Methoden erweitert
- JSF bindet direkt: #{task.text()}, #{task.style()}
- Task entscheidet selbst über Präsentation

**Vorteile:**

- Einfach und direkt
- Keine zusätzlichen Objekte (Present, View)
- JSF-Template bleibt deklarativ
- Objekt kontrolliert Darstellung vollständig

**Nachteile:**

- Interface "vermischt" Business und UI
- Schwer, UI-Framework zu wechseln
- Bei komplexen UIs werden Interfaces groß

Fazit: Beste Lösung für kleine bis mittlere Apps mit festem UI-Framework.

Variante 4: Paged mit Hollywood Principle (Functional)
```java
public interface Paged<T> {
    void displayOn(Callable<Page> page, Consumer<List<T>> data);
}

@Override
public void displayOn(Callable<Page> page, Consumer<List<T>> data) {
    try {
        data.accept(display(page.call()));  // Hollywood!
    } catch (Exception e) {
        throw new IllegalStateException(e);
    }
}
```
**Anwendung:**

- Tasks rufen Controller-Callbacks auf
- Controller gibt Callable/Consumer
- Inversion of Control - "Don't call us, we'll call you"

**Vorteile:**

- Echte IoC - Objekt kontrolliert Fluss
- Standard Java Functional Interfaces
- Lazy Evaluation mit Callable
- Testbar durch Consumer-Mocking

**Nachteile:**

- Konzept für Anfänger schwer verständlich
- Exception-Handling nötig (Callable throws)
- Debugging komplexer

Fazit: Elegant für Paging/Filtering, aber steile Lernkurve.

Variante 5: Tree/Treed für hierarchische Strukturen
```java
public interface Treed {
    String label();
    String icon();
    boolean isExpanded();
    List<Treed> children();
}

public interface TreedFolder extends Folder, Treed {
    TreedFolder expand();
}
```
**Anwendung:**

- Objekte wissen, wie sie im Tree dargestellt werden
- Rekursives JSF-Template ruft children() auf
- Folder + Tree-Verhalten kombiniert

**Vorteile:**

- Tree-Logik im Objekt, nicht im UI
- Funktioniert für jede Hierarchie (Folder, Menu, Org-Chart)
- Rekursive Templates sind einfach

**Nachteile:**

- Komplexe Immutable-Updates durch ganze Hierarchie
- findFolder() und updateFolder() sind aufwendig
- Performance bei großen Bäumen

Fazit: Sehr gut für hierarchische Strukturen, wenn richtig implementiert.

Variante 6: Strikte Datenkapselung (Question/Display)
```java
public interface Task {
    boolean answersTo(Question question);
    void showTo(Display display);
}

// Type-safe Questions
task.answersTo(IsCompleted.instance());
task.answersTo(new DescriptionContains("urgent"));

// Hollywood Display
task.showTo(display -> display.show(text, style, action));
```
**Anwendung:**

- Keine Getter - nur Fragen beantworten
- Display-Callbacks für Rendering
- Objekt gibt NIEMALS Daten direkt raus

**Vorteile:**

Strikte Kapselung - Invarianten geschützt
Type-safe Questions statt String-Queries
Unmöglich, Zustand inkonsistent zu machen

**Nachteile:**

- Viel Boilerplate für Questions
- JSF-Integration komplex
- Schwer für Team zu verstehen
- Overhead für einfache Anwendungen

**Fazit:** Akademisch korrekt, praktisch zu puristisch für Business-Apps.

**Variante 7:** View Pattern (EMPFOHLEN)
```java
public interface Task {
    Task.View asView();
    Task edit(Task.View view);
    
    interface View {
        String getDescription();
        void setDescription(String description);
        boolean getCompleted();
        void setCompleted(boolean completed);
        Action edit();
        Action remove();
    }
}
```
**Anwendung:**

- Task gibt mutable View für JSF zurück
- JSF bindet direkt an View-Getter/Setter
- Task nimmt modifizierte View und erstellt neue Version

**Vorteile:**

- JSF-kompatibel ohne OOP zu opfern
- Task bleibt immutable, View ist mutable DTO
- Direktes Binding: #{view.description}
- Skaliert für komplexe Forms
- View ist kontrollierte Grenze
- Actions im View - keine externe Logic

**Nachteile:**

- View-Objekte müssen gepflegt werden
- Scheinbar "Getter/Setter" (aber kontrolliert)
- Edit-State im Controller

**Fazit:** Beste praktische Lösung für echte JSF-Anwendungen. Kompromiss zwischen OOP-Reinheit und JSF-Realität.

## 3. Empfehlung nach Anwendungsfall
AnwendungsfallEmpfohlene VarianteGrundKleine Todo-AppHorizontale Interface-ErweiterungEinfach,direkt, wenig CodeEnterprise CRUDView PatternJSF-freundlich, wartbar, skalierbarComplex FormsView PatternView kann beliebig erweitert werdenHierarchische DatenTreed PatternTree-Logik im ObjektPaging/FilteringHollywood PagedElegante IoCLern-ProjektPresent PatternZeigt OOP-Konzepte klarAkademischStrikte KapselungZeigt pure OOP

## 4. Generelle Lessons Learned

**Was funktioniert:**
- Objekte, die sich selbst präsentieren können
- Immutability mit neuen Objekten statt Mutation
- Decorator für Erweiterungen (Urgent, Timestamped)
- Hollywood Principle für Callbacks
- Kovariante Rückgabetypen (kein Casting!)

**Was nicht funktioniert:**
- Pure programmatische JSF-Component-Erstellung (zu komplex)
- ActionListener mit Lambdas (Serialization)
- Strikte "No Getters" für JSF (zu puristisch)
- Type-Casting zwischen Interfaces (Design-Fehler)

**Pragmatischer Ansatz:**
- View Pattern kombiniert mit horizontaler Erweiterung ist der sweet spot für produktive JSF-Entwicklung mit OOP-Prinzipien.
