# OOP UI-Patterns für JSF-Anwendungen - Vollständige Dokumentation
Inhaltsverzeichnis

- [Fundamentale OOP-Prinzipien]
- [View Pattern - Die praktische Lösung]
- [View-Erweiterungen]
- [Vergleich mit Anti-Patterns]
- [Best Practices]
- [Implementierungs-Checkliste]

## 1. Fundamentale OOP-Prinzipien
### 1.1 Quellen der Prinzipien

**Basierend auf:**

- Robert Bräutigam (MATHEMA): Object-Oriented Domain-Driven Design, Single Responsibility Principle
- Yegor Bugayenko: Seven Virtues of Good Objects, SQL-Speaking Objects
- Vytautas Žurauskas: True Encapsulation
- Mihai Emil Andronache: Self-documenting code

### 1.2 Kernprinzipien

**Keine Getter/Setter für Business-Daten**

```java
// FALSCH - Datenexposition
public String getDescription() { return description; }
public void setCompleted(boolean completed) { this.completed = completed; }

// RICHTIG - Verhalten
public Task complete() { return new Task(id, description, true); }
public void showTo(Display display) { /* Task präsentiert sich */ }
```

**Tell, Don't Ask**

```java
// FALSCH - Fragen und dann tun
if (task.isCompleted()) {
    task.setStatus("done");
}
// RICHTIG - Sagen
task.complete();
```

**UI ist Teil der Business-Logik**

```java
// FALSCH - Externe View-Klasse holt Daten
public class TaskView {
    void render(Task task) {
        String desc = task.getDescription(); // Data leak
    }
}

// RICHTIG - Objekt zeigt sich selbst
public interface Task {
    Task.View asView();
}
```
**Immutability statt Mutation**
```java
// FALSCH - Mutation
public void setDescription(String desc) {
    this.description = desc;
}

// RICHTIG - Neue Instanz
public Task withDescription(String desc) {
    return new Task(id, desc, completed);
}
```
**No Data Boundaries**

- Keine DTOs zwischen Schichten
- Objekte behalten Kontrolle über ihre Daten
- Keine Mapper/Converter

**Decorator über Inheritance**
```java
// FALSCH - Vererbung
public class UrgentTask extends Task { }

// RICHTIG - Komposition
public final class UrgentTask implements Task {
    private final Task origin;
}
```
## 2. View Pattern - Die praktische Lösung
### 2.1 Grundkonzept
Das View Pattern ist der pragmatische Kompromiss zwischen OOP-Reinheit und JSF-Realität.
**Kernidee:**

- Business-Objekt bleibt immutable
- View ist mutable DTO für JSF-Binding
- View ist kontrollierte Grenze zwischen Business und UI
- Objekt entscheidet, was in View exponiert wird

### 2.2 Basis-Implementation
```java
package de.example.todos;

/**
 * Task - Business object mit kontrollierter View.
 */
public interface Task extends Serializable {
    String id();
    Task complete();
    Task edit(Task.View view);
    
    // Kontrollierte Datenexposition für UI
    Task.View asView();
    
    /**
     * View - Inneres Interface für JSF-Binding.
     * Getter/Setter sind OK, weil kontrolliert durch Task.
     */
    interface View extends Serializable {
        String getId();
        void setId(String id);
        
        String getDescription();
        void setDescription(String description);
        
        boolean getCompleted();
        void setCompleted(boolean completed);
        
        // Actions für UI
        Action edit();
        Action remove();
        Action complete();
        
        // Styling
        String getStyle();
    }
}

/**
 * Action - Typsichere UI-Aktionen.
 */
public interface Action extends Serializable {
    boolean exists();
    String call();  // EL-Expression für JSF
}
```
### 2.3 Task Implementation
```java
package de.example.todos.task;

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
    public String id() {
        return id;
    }
    
    @Override
    public Task complete() {
        return new SimpleTask(id, description, true);
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
    public Task.View asView() {
        return new TaskView(id, description, completed);
    }
}
```

### 2.4 View Implementation
```java
package de.example.todos.task;

/**
 * TaskView - Mutable DTO für JSF.
 */
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
    
    @Override
    public String getId() {
        return id;
    }
    
    @Override
    public void setId(String id) {
        this.id = id;
    }
    
    @Override
    public String getDescription() {
        return description;
    }
    
    @Override
    public void setDescription(String description) {
        this.description = description;
    }
    
    @Override
    public boolean getCompleted() {
        return completed;
    }
    
    @Override
    public void setCompleted(boolean completed) {
        this.completed = completed;
    }
    
    @Override
    public Action edit() {
        return new Action() {
            @Override
            public boolean exists() {
                return !completed;
            }
            
            @Override
            public String call() {
                return "#{tasks.startEdit('" + id + "')}";
            }
        };
    }
    
    @Override
    public Action remove() {
        return new Action() {
            @Override
            public boolean exists() {
                return true;
            }
            
            @Override
            public String call() {
                return "#{tasks.remove('" + id + "')}";
            }
        };
    }
    
    @Override
    public Action complete() {
        return new Action() {
            @Override
            public boolean exists() {
                return !completed;
            }
            
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

public interface Action{
    boolean exists();
    // JSF expression
    String call();
}

```

### 2.5 JSF Controller
```java
package de.example.todos;

@Named("tasks")
@SessionScoped
public final class TaskController implements Serializable {
    private Tasks tasks = new InMemoryTasks();
    private String newTaskText = "";
    
    // Edit state
    private String editingTaskId = null;
    private TaskView editingView = null;
    
    public void addTask() {
        if (newTaskText != null && !newTaskText.trim().isEmpty()) {
            tasks = tasks.with(newTaskText.trim());
            newTaskText = "";
        }
    }
    
    public void complete(String taskId) {
        tasks = tasks.complete(taskId);
    }
    
    public void remove(String taskId) {
        tasks = tasks.remove(taskId);
    }
    
    public void startEdit(String taskId) {
        Task task = tasks.find(taskId);
        if (task != null) {
            this.editingTaskId = taskId;
            this.editingView = new TaskView(task.asView());
        }
    }
    
    public void saveEdit() {
        if (editingTaskId != null && editingView != null) {
            tasks = tasks.edit(editingTaskId, editingView);
            cancelEdit();
        }
    }
    
    public void cancelEdit() {
        editingTaskId = null;
        editingView = null;
    }
    
    public boolean isEditing(String taskId) {
        return editingTaskId != null && editingTaskId.equals(taskId);
    }
    
    // JSF Bindings
    public List<Task.View> getViews() {
        return tasks.views();
    }
    
    public String getNewTaskText() { return newTaskText; }
    public void setNewTaskText(String text) { this.newTaskText = text; }
    
    public TaskView getEditingView() { return editingView; }
    public void setEditingView(TaskView view) { this.editingView = view; }
}
```

### 2.6 JSF Template

```xml
<!DOCTYPE html>
<html xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:c="http://xmlns.jcp.org/jsp/jstl/core">
<h:head>
    <title>View Pattern Todo</title>
</h:head>
<h:body>
    <div class="container">
        <!-- Add Task Form -->
        <h:form>
            <h:inputText value="#{tasks.newTaskText}" />
            <h:commandButton value="Add" action="#{tasks.addTask}" />
        </h:form>
        
        <!-- Task List -->
        <ui:repeat value="#{tasks.views}" var="item">
            <c:choose>
                <c:when test="#{tasks.isEditing(item.id)}">
                    <!-- Edit Mode -->
                    <h:form>
                        <h:inputText value="#{tasks.editingView.description}" />
                        <h:commandButton value="Save" action="#{tasks.saveEdit}" />
                        <h:commandButton value="Cancel" action="#{tasks.cancelEdit}" />
                    </h:form>
                </c:when>
                <c:otherwise>
                    <!-- Display Mode -->
                    <div class="#{item.style}">
                        <span>#{item.description}</span>
                        <h:form style="display: inline;">
                            <h:commandButton value="Complete" 
                                           action="#{item.complete.call()}"
                                           rendered="#{item.complete.exists()}" />
                            <h:commandButton value="Edit" 
                                           action="#{item.edit.call()}"
                                           rendered="#{item.edit.exists()}" />
                            <h:commandButton value="Remove" 
                                           action="#{item.remove.call()}"
                                           rendered="#{item.remove.exists()}" />
                        </h:form>
                    </div>
                </c:otherwise>
            </c:choose>
        </ui:repeat>
    </div>
</h:body>
</html>
```

## 3. View-Erweiterungen

**Das View Pattern kann für komplexere UI-Anforderungen erweitert werden.**

### 3.1 Paging (Hollywood Principle)

```java
/**
 * Paged - Verhalten für paginierte Anzeige.
 */
public interface Paged<T> {
    void displayOn(Callable<Page> page, Consumer<List<T>> data);
    void totalOn(Callable<Page> page, Consumer<Integer> total);
}

public interface Page extends Serializable {
    int offset();
    int size();
    Filter[] filters();
    Sort[] sorts();
}
```
**Implementation:**
```java
public final class PagedTasks implements Tasks, Paged<Task> {
    private final List<Task> items;
    
    @Override
    public void displayOn(Callable<Page> page, Consumer<List<Task>> data) {
        try {
            Page p = page.call();  // Hollywood: Tasks rufen page.call()
            List<Task> filtered = applyFilters(items, p.filters());
            List<Task> sorted = applySorts(filtered, p.sorts());
            List<Task> paged = paginate(sorted, p.offset(), p.size());
            data.accept(paged);  // Hollywood: Tasks rufen data.accept()
        } catch (Exception e) {
            throw new IllegalStateException(e);
        }
    }
}
```
**Controller:**
```java
@Named("tasks")
@SessionScoped
public final class PagedTaskController implements Serializable {
    private PagedTasks tasks = new PagedTasks();
    private int currentPage = 0;
    private int pageSize = 10;
    private List<Task.View> currentViews = new ArrayList<>();
    
    private void refresh() {
        // Hollywood: Tasks rufen unsere Consumer zurück
        tasks.displayOn(
            () -> buildPage(),  // Callable<Page>
            items -> currentViews = toViews(items)  // Consumer<List<Task>>
        );
    }
    
    private Page buildPage() {
        return new SimplePage(
            currentPage * pageSize,
            pageSize,
            buildFilters(),
            buildSorts()
        );
    }
    
    public List<Task.View> getCurrentViews() {
        if (currentViews.isEmpty()) {
            refresh();
        }
        return currentViews;
    }
}
```

**Vorteile:**

- Tasks kontrollieren, wann page.call() aufgerufen wird
- Lazy Evaluation mit Callable
- Standard Java Functional Interfaces
- Inversion of Control - "Don't call us, we'll call you"

### 3.2 Tree/Hierarchische Strukturen
```java
/**
 * Treed - View-Verhalten für Tree-Darstellung.
 */
public interface Treed extends Serializable {
    String label();
    String icon();
    boolean isExpanded();
    Treed expand();
    Treed collapse();
    List<Treed> children();
    boolean hasChildren();
}

/**
 * TreedFolder - Folder mit Tree-View-Verhalten.
 */
public interface TreedFolder extends Folder, Treed {
    @Override
    TreedFolder expand();  // Kovarianter Rückgabetyp
    
    @Override
    TreedFolder collapse();
    
    // Folder.View für JSF-Binding
    Folder.View asView();
    
    interface View extends Serializable {
        String getId();
        String getName();
        String getIcon();
        boolean isExpanded();
        List<View> getChildren();
        Action expand();
        Action collapse();
        Action select();
    }
}
```
**Implementation:**
```java
public final class HierarchicalFolder implements TreedFolder {
    private final String id;
    private final String name;
    private final List<TreedFolder> subfolders;
    private final PagedTasks tasks;
    private final boolean expanded;
    
    @Override
    public TreedFolder expand() {
        return new HierarchicalFolder(id, name, subfolders, tasks, true);
    }
    
    @Override
    public String label() {
        return name;
    }
    
    @Override
    public String icon() {
        return expanded ? "📂" : "📁";
    }
    
    @Override
    public List<Treed> children() {
        return new ArrayList<>(subfolders);
    }
    
    @Override
    public Folder.View asView() {
        return new FolderView(
            id, 
            name, 
            icon(), 
            expanded, 
            childViews()
        );
    }
    
    private List<Folder.View> childViews() {
        return subfolders.stream()
            .map(TreedFolder::asView)
            .collect(Collectors.toList());
    }
}
```
**JSF Template (rekursiv):**

```xml
<!-- tree-node.xhtml -->
<ui:composition>
    <h:form>
        <div class="tree-node #{folders.isSelected(node.id) ? 'selected' : ''}">
            <h:commandLink action="#{node.select().call()}">
                <span>#{node.icon} #{node.name}</span>
            </h:commandLink>
        </div>
    </h:form>
    
    <ui:fragment rendered="#{node.expanded and not empty node.children}">
        <div class="tree-children">
            <ui:repeat value="#{node.children}" var="child">
                <ui:include src="tree-node.xhtml">
                    <ui:param name="node" value="#{child}" />
                </ui:include>
            </ui:repeat>
        </div>
    </ui:fragment>
</ui:composition>
<!-- Layout:  -->
css.app-container {
    display: grid;
    grid-template-columns: 300px 1fr;
    height: 100vh;
}

.tree-panel {
    background: #2c3e50;
    color: white;
    padding: 20px;
    overflow-y: auto;
}

.tasks-panel {
    background: white;
    padding: 30px;
    overflow-y: auto;
}
```
### 3.3 Filtering & Sorting
```java
/**
 * Filter & Sort als View-Erweiterung.
 */
public interface Filter extends Serializable {
    String field();
    Object value();
    Op operator();
    
    enum Op {
        EQUALS, CONTAINS, STARTS_WITH, 
        GREATER_THAN, LESS_THAN, IN
    }
}

public interface Sort extends Serializable {
    String field();
    Order order();
    
    enum Order {
        ASCENDING, DESCENDING
    }
}

/**
 * Task entscheidet, ob es dem Filter entspricht.
 */
public interface Task {
    boolean matches(Filter filter);
    int compareTo(Task other, Sort sort);
}
```
**Implementation:**
```java
public final class SimpleTask implements Task {
    @Override
    public boolean matches(Filter filter) {
        if ("completed".equals(filter.field())) {
            return completed == (Boolean) filter.value();
        }
        if ("description".equals(filter.field())) {
            String filterValue = filter.value().toString().toLowerCase();
            String desc = description.toLowerCase();
            
            switch (filter.operator()) {
                case CONTAINS:
                    return desc.contains(filterValue);
                case STARTS_WITH:
                    return desc.startsWith(filterValue);
                default:
                    return true;
            }
        }
        return false;
    }
    
    @Override
    public int compareTo(Task other, Sort sort) {
        if (!(other instanceof SimpleTask)) {
            return 0;
        }
        SimpleTask o = (SimpleTask) other;
        
        if ("description".equals(sort.field())) {
            int cmp = this.description.compareTo(o.description);
            return sort.order() == Sort.Order.ASCENDING ? cmp : -cmp;
        }
        return 0;
    }
}
```

## 4. Vergleich mit Anti-Patterns
### 4.1 Traditional MVC (Anti-Pattern)
```java
// FALSCH - Traditional MVC
public class Task {
    private String description;
    private boolean completed;
    
    // Getters/Setters
    public String getDescription() { return description; }
    public void setDescription(String d) { this.description = d; }
    public boolean isCompleted() { return completed; }
    public void setCompleted(boolean c) { this.completed = c; }
}

public class TaskController {
    public void complete(Task task) {
        task.setCompleted(true);  // Tell, don't ask verletzt
    }
}

public class TaskView {
    public void render(Task task) {
        String desc = task.getDescription();  // Data leak
        boolean done = task.isCompleted();
        // Externe Rendering-Logik
    }
}
```
**Probleme:**

- Task ist nur Datencontainer
- Business-Logik im Controller
- View holt Daten ab statt empfangen
- Tight Coupling zwischen allen drei
- Änderung an Task erfordert Änderung in Controller und View

### 4.2 Repository Pattern (Anti-Pattern)
```java
// FALSCH - Repository mit Gettern
public interface TaskRepository {
    Task findById(String id);
    void save(Task task);
}

public class TaskRepositoryImpl implements TaskRepository {
    @Override
    public void save(Task task) {
        String id = task.getId();  // Privacy violation
        String desc = task.getDescription();  // Data leak
        boolean completed = task.isCompleted();
        
        sql.execute("INSERT INTO tasks...", id, desc, completed);
    }
}
```
**Probleme:**

- Repository verletzt Kapselung
- Task gibt interne Daten preis
- Keine Kohäsion - Persistence außerhalb von Task

**RICHTIG** - SQL-Speaking Object:
```java
public interface Task {
    void persistTo(Connection connection);
}

public final class SqlTask implements Task {
    @Override
    public void persistTo(Connection conn) {
        try (PreparedStatement stmt = conn.prepareStatement(
            "INSERT INTO tasks (id, description, completed) VALUES (?, ?, ?)")) {
            stmt.setString(1, id);
            stmt.setString(2, description);
            stmt.setBoolean(3, completed);
            stmt.executeUpdate();
        }
    }
}
```
### 4.3 Service Layer (Anti-Pattern)
```java
// FALSCH - TaskService
public class TaskService {
    public void completeTask(String taskId) {
        Task task = repository.findById(taskId);
        task.setCompleted(true);
        repository.save(task);
    }
    
    public boolean isTaskUrgent(Task task) {
        return task.getPriority() > 5;  // Business logic outside Task
    }
}
```
**Probleme:**

- Business-Logik außerhalb des Objekts
- Task ist anämisches Domain Model
 -Service ist prozeduraler Code mit OOP-Fassade

**RICHTIG** - Verhalten im Objekt:
```java
public interface Task {
    Task complete();
    boolean isUrgent();
}

public final class PrioritizedTask implements Task {
    private final Task origin;
    private final int priority;
    
    @Override
    public boolean isUrgent() {
        return priority > 5;
    }
}
```
## 5. Best Practices
### 5.1 Package-Struktur (3. Regel von Happy Packaging)
```
de.example.todos/              # Root - Kernabstraktionen
    Task.java
    Tasks.java
    Action.java
    Paged.java
    Treed.java
    
de.example.todos.task/         # Sub-package - Task-Details
    SimpleTask.java
    TaskView.java
    UrgentTask.java            # Decorator
    TimestampedTask.java       # Decorator
    
de.example.todos.tasks/        # Sub-package - Tasks-Details
    InMemoryTasks.java
    PagedTasks.java
    FilteredTasks.java         # Decorator
    
de.example.todos.folder/       # Sub-package - Folder-Details
    HierarchicalFolder.java
    FolderView.java
    
de.example.todos.page/         # Sub-package - Paging-Details
    SimplePage.java
    SimpleFilter.java
    SimpleSort.java
```
**Regeln:**

- Root-Package enthält Interfaces - keine Dependencies auf Sub-Packages
- Sub-Packages fügen nur Details hinzu, keine neuen Konzepte
- Business-orientierte Namen, keine technischen (nicht service, repository)

### 5.2 Naming Conventions (Yegor256)
Objekte repräsentieren WAS sie sind:

- Task nicht TaskManager
- Todo nicht TodoService
- Folder nicht FolderHandler

**Keine -er Endungen:**

- Filter nicht TaskFilter
- Sort nicht TaskSorter

**Verhalten als Verben:**
```
task.complete() nicht task.setCompleted()
task.edit(view) nicht task.update()
```
### 5.3 Immutability
```java
// Alle Felder final
public final class Task {
    private final String id;
    private final String description;
    private final boolean completed;
    
    // Keine Setters
    
    // Neue Instanz bei Änderung
    public Task complete() {
        return new Task(id, description, true);
    }
}
```
### 5.4 Decorator Pattern
```java
// Base
Task task = new SimpleTask("Review code");

// Decorators
task = new UrgentTask(task);
task = new TimestampedTask(task);
task = new CategorizedTask(task, Category.WORK);

// View bleibt gleich
Task.View view = task.asView();
```
### 5.5 Kovariante Rückgabetypen
```java
// FALSCH - Casting nötig
public interface Tasks {
    Tasks with(String description);
}

PagedTasks tasks = ...;
tasks = (PagedTasks) tasks.with("New");  // BAD!

// RICHTIG - Kovariante Rückgabetypen
public interface PagedTasks extends Tasks {
    @Override
    PagedTasks with(String description);  // Override mit spezifischerem Typ
}

PagedTasks tasks = ...;
tasks = tasks.with("New");  // Kein Cast!
```
### 5.6 Hollywood Principle
```java
// FALSCH - Pull-Style
List<Task> items = tasks.getPagedItems(offset, size);
int total = tasks.getTotalCount();

// RICHTIG - Push-Style (Hollywood)
tasks.displayOn(
    () -> buildPage(),                    // Callable: Wir geben Rezept
    items -> this.currentItems = items    // Consumer: Tasks geben Daten
);
```

## 6. Implementierungs-Checkliste
### 6.1 Für neue Features

- Interface im Root-Package definiert?
- Keine technischen Namen (Manager, Service, Handler)?
- Alle Klassen final?
- Alle Felder private final?
- Keine Getter/Setter für Business-Daten?
- Verhalten statt Datenexposition?
- asView() für UI-Binding?
- Actions im View definiert?
- Decorator statt Inheritance?
- Immutable - neue Instanzen bei Änderung?
- Serializable implementiert (JSF)?
- Unit-Tests für Business-Logik?

### 6.2 Code Review Fragen
**Datenkapselung:**

- Gibt das Objekt interne Daten direkt zurück?
- Sind Getter/Setter wirklich nötig oder nur Gewohnheit?
- Kann das Objekt sein Verhalten selbst implementieren?

**Verhalten:**

- Ist die Methode ein Verb (complete, edit) oder ein Getter (getX)?
- Macht das Objekt etwas oder gibt es nur Daten?
- Ist "Tell, Don't Ask" verletzt?

**UI-Integration:**

- Rendert sich das Objekt selbst oder wird es extern gerendert?
- Ist View als kontrollierte Grenze implementiert?
- Sind Actions typsicher im View?

**Immutability:**

- Wird der Zustand mutiert oder neue Instanz erstellt?
- Sind alle Felder final?
- Gibt es Setters (außer in View)?

### 6.3 Migration von Legacy Code
Schritt 1: Interface extrahieren
```java
// Vorher
public class Task {
    private String description;
    public String getDescription() { return description; }
    public void setDescription(String d) { this.description = d; }
}

// Nachher
public interface Task {
    Task.View asView();
}

public final class SimpleTask implements Task {
    private final String description;
    
    @Override
    public Task.View asView() {
        return new TaskView(description);
    }
}
```
Schritt 2: Verhalten hinzufügen
```java
public interface Task {
    Task complete();
    Task edit(Task.View view);
}
```
Schritt 3: Externe Logik ins Objekt verschieben
```java
// Vorher - im Service
if (task.getPriority() > 5) {
    // urgent logic
}

// Nachher - im Objekt
public interface Task {
    boolean isUrgent();
}

public final class PrioritizedTask implements Task {
    @Override
    public boolean isUrgent() {
        return priority > 5;
    }
}
```
Schritt 4: View Pattern einführen
```java
public interface Task {
    interface View {
        String getDescription();
        void setDescription(String description);
        Action edit();
        Action complete();
    }
}
```
Schritt 5: Controller vereinfachen
```java
// Vorher
@Named("tasks")
public class TaskController {
    public void edit(Task task, String newDesc) {
        task.setDescription(newDesc);
        repository.save(task);
    }
}

// Nachher
@Named("tasks")
public final class TaskController {
    private Tasks tasks;
    
    public void saveEdit(String taskId) {
        tasks = tasks.edit(taskId, editingView);
    }
}
```
## 7. Zusammenfassung
### 7.1 View Pattern ist die Lösung
Das View Pattern kombiniert:

OOP-Prinzipien (Kapselung, Verhalten, Immutability)
JSF-Realität (direktes Binding, Getter/Setter)
Erweiterbarkeit (Paging, Trees, Filtering)

**Zentrale Elemente:**

- Task bleibt immutable - Business-Logik sauber
-View ist mutable DTO - JSF kann direkt binden
-View ist kontrolliert - Task entscheidet, was exponiert wird
-Actions im View - Typsicher und testbar
-Erweiterungen gehören zum View - Paging, Trees, Filtering

###  7.2 Wann welches Pattern?
SzenarioPatternGrundStandard CRUDView PatternJSF-kompatibel, OOP-konformHierarchische DatenView + TreedTree-Logik im ObjektPaging/FilteringView + PageWiederholenClaude hat noch nicht die Fähigkeit, den von ihm generierten Code auszuführen.AWeiterBearbeitenOOP UI-Patterns für JSF-Anwendungen - Vollständige Dokumentation (Fortsetzung)
### 7.2 Wann welches Pattern? (Fortsetzung)
SzenarioPatternGrundStandard CRUDView PatternJSF-kompatibel, OOP-konformHierarchische DatenView + TreedTree-Logik im ObjektPaging/FilteringView + Paged (Hollywood)IoC, elegant, testbarKomplexe FormsView erweitertView kann beliebig wachsenRead-Only ListenHorizontale ErweiterungEinfacher, weniger CodeLegacy MigrationView Pattern schrittweiseInkrementelle Refactoring
### 7.3 Was NICHT tun
❌ Getter/Setter für Business-Daten
```java
// FALSCH
public String getDescription() { return description; }
```
❌ Services/Manager/Handler
```java
// FALSCH
public class TaskService { }
public class TaskManager { }
```
❌ Data Transfer Objects zwischen Schichten
```java
// FALSCH
public class TaskDTO { }
public class TaskMapper { }
```
❌ Repositories die Objekte verletzen
```java
// FALSCH
repository.save(task); // holt interne Daten
```
❌ Type Casting
```java
// FALSCH
tasks = (PagedTasks) tasks.with(...);
❌ Mutation statt neue Instanzen
```java
// FALSCH
task.setCompleted(true);
```
❌ Externe Rendering-Logik
```java
// FALSCH
public class TaskRenderer {
    void render(Task task) { }
}
```
### 7.4 Was TUN
✅ View Pattern als Standard
```java
public interface Task {
    Task.View asView();
    Task edit(Task.View view);
}
```
✅ Verhalten statt Daten
```java
public interface Task {
    Task complete();
    boolean isUrgent();
    void persistTo(Connection conn);
}```
✅ Immutability
```java
public final class Task {
    private final String id;
    private final String description;
    
    public Task complete() {
        return new Task(id, description, true);
    }
}
```
✅ Decorator für Erweiterungen
```java
Task task = new UrgentTask(
    new TimestampedTask(
        new SimpleTask("Review code")
    )
);
```
✅ Hollywood Principle
```java
tasks.displayOn(
    () -> buildPage(),
    items -> this.items = items
);
```
✅ Kovariante Rückgabetypen
```java
public interface PagedTasks extends Tasks {
    @Override
    PagedTasks with(String description);
}
```
✅ Business-orientierte Packages
```
de.example.todos/
de.example.todos.task/
de.example.todos.folder/
```
## 8. Vollständiges Beispiel
### 8.1 Projekt-Struktur
```
src/main/java/
  de/example/todos/
    Task.java              # Interface
    Tasks.java             # Interface
    Action.java            # Interface
    Paged.java             # Interface
    Treed.java             # Interface
    Page.java              # Interface
    Filter.java            # Interface
    Sort.java              # Interface
    
  de/example/todos/task/
    SimpleTask.java        # Implementation
    TaskView.java          # View DTO
    UrgentTask.java        # Decorator
    TimestampedTask.java   # Decorator
    
  de/example/todos/tasks/
    InMemoryTasks.java     # Implementation
    PagedTasks.java        # Implementation with Paged
    
  de/example/todos/folder/
    HierarchicalFolder.java  # TreedFolder Implementation
    FolderView.java          # View DTO
    
  de/example/todos/page/
    SimplePage.java        # Page Implementation
    SimpleFilter.java      # Filter Implementation
    SimpleSort.java        # Sort Implementation
    
  de/example/todos/
    TaskController.java    # JSF Managed Bean
    FolderTreeController.java  # JSF Managed Bean

src/main/webapp/
  WEB-INF/
    web.xml
    faces-config.xml
  index.xhtml
  tree-node.xhtml          # Recursive Tree Template
  resources/
    css/
      styles.css
```
pom.xml

### 8.2 Vollständiger Code-Flow
1. User fügt Task hinzu:
```
JSF: #{tasks.newTaskText} → Controller.setNewTaskText()
JSF: #{tasks.addTask} → Controller.addTask()
  → tasks = tasks.with(newTaskText)
    → InMemoryTasks.with()
      → new SimpleTask(description)
      → return new InMemoryTasks(newItems)
```
2. User startet Edit:
```
JSF: #{item.edit.call()} → "#{tasks.startEdit('id')}"
Controller: startEdit(taskId)
  → task = tasks.find(taskId)
  → editingView = new TaskView(task.asView())
    → SimpleTask.asView()
      → return new TaskView(id, description, completed)
```
3. JSF bindet Edit-Form:
```
JSF: #{tasks.editingView.description}
  → TaskView.getDescription()
JSF: User ändert Text
  → TaskView.setDescription(newText)
```
4. User speichert Edit:
```
JSF: #{tasks.saveEdit()}
Controller: saveEdit()
  → tasks = tasks.edit(editingTaskId, editingView)
    → InMemoryTasks.edit()
      → task.edit(editingView)
        → SimpleTask.edit()
          → return new SimpleTask(view.getId(), view.getDescription(), view.getCompleted())
      → return new InMemoryTasks(newItems)
   ```
5. View wird aktualisiert:
```
JSF: #{tasks.views}
  → Controller.getViews()
    → tasks.views()
      → InMemoryTasks.views()
        → items.stream().map(Task::asView).collect()
          → SimpleTask.asView()
            → return new TaskView(...)
```
## 9. Testing-Strategien
## 9.1 Unit Tests für Business-Logik
``` java
public class SimpleTaskTest {
    
    @Test
    public void completedTaskIsCompleted() {
        Task task = new SimpleTask("Test");
        Task completed = task.complete();
        
        assertTrue(completed.asView().getCompleted());
        assertFalse(task.asView().getCompleted()); // Original unverändert
    }
    
    @Test
    public void editChangesDescription() {
        Task task = new SimpleTask("Original");
        TaskView view = new TaskView();
        view.setId(task.id());
        view.setDescription("Changed");
        view.setCompleted(false);
        
        Task edited = task.edit(view);
        
        assertEquals("Changed", edited.asView().getDescription());
        assertEquals("Original", task.asView().getDescription()); // Original unverändert
    }
    
    @Test
    public void urgentTaskDecorator() {
        Task task = new UrgentTask(new SimpleTask("Test"));
        Task.View view = task.asView();
        
        assertTrue(view.getStyle().contains("urgent"));
    }
}
```

### 9.2 Integration Tests für Controller
``` java
public class TaskControllerTest {
    
    private TaskController controller;
    
    @Before
    public void setup() {
        controller = new TaskController();
    }
    
    @Test
    public void addTaskIncreasesCount() {
        int before = controller.getViews().size();
        
        controller.setNewTaskText("New Task");
        controller.addTask();
        
        assertEquals(before + 1, controller.getViews().size());
    }
    
    @Test
    public void editWorkflow() {
        // Add
        controller.setNewTaskText("Original");
        controller.addTask();
        String taskId = controller.getViews().get(0).getId();
        
        // Start edit
        controller.startEdit(taskId);
        assertNotNull(controller.getEditingView());
        
        // Modify
        controller.getEditingView().setDescription("Modified");
        
        // Save
        controller.saveEdit();
        
        // Verify
        Task.View view = controller.getViews().stream()
            .filter(v -> v.getId().equals(taskId))
            .findFirst()
            .orElse(null);
        
        assertNotNull(view);
        assertEquals("Modified", view.getDescription());
        assertNull(controller.getEditingView()); // Cleared after save
    }
}
```
### 9.3 Tests für Paging
``` java
public class PagedTasksTest {
    
    @Test
    public void pagingReturnsCorrectSubset() {
        PagedTasks tasks = new PagedTasks();
        
        // Add 25 tasks
        for (int i = 0; i < 25; i++) {
            tasks = (PagedTasks) tasks.with("Task " + i);
        }
        
        List<Task.View> result = new ArrayList<>();
        
        // Request page 2, size 10
        tasks.displayOn(
            () -> new SimplePage(10, 10, new Filter[0], new Sort[0]),
            items -> result.addAll(toViews(items))
        );
        
        assertEquals(10, result.size());
        assertEquals("Task 10", result.get(0).getDescription());
    }
    
    @Test
    public void filteringWorks() {
        PagedTasks tasks = new PagedTasks();
        tasks = (PagedTasks) tasks.with("Urgent task");
        tasks = (PagedTasks) tasks.with("Normal task");
        
        List<Task.View> result = new ArrayList<>();
        Filter filter = new SimpleFilter("description", Filter.Op.CONTAINS, "Urgent");
        
        tasks.displayOn(
            () -> new SimplePage(0, 10, new Filter[]{filter}, new Sort[0]),
            items -> result.addAll(toViews(items))
        );
        
        assertEquals(1, result.size());
        assertTrue(result.get(0).getDescription().contains("Urgent"));
    }
}
```

## 10. Performance-Überlegungen
### 10.1 Immutability und Performance
Problem: Jede Änderung erstellt neue Objekte
javaTask task = new SimpleTask("Original");
task = task.complete();  // Neue Instanz
task = new UrgentTask(task);  // Noch eine neue Instanz
Lösungen:

Structural Sharing (für Collections)

```java
public final class InMemoryTasks implements Tasks {
    private final List<Task> items;  // Immutable List
    
    @Override
    public Tasks with(String description) {
        List<Task> newItems = new ArrayList<>(items);  // Copy
        newItems.add(new SimpleTask(description));
        return new InMemoryTasks(newItems);
    }
}
```
Lazy View Creation

```java
public final class SimpleTask implements Task {
    private transient TaskView cachedView;
    
    @Override
    public Task.View asView() {
        if (cachedView == null) {
            cachedView = new TaskView(id, description, completed);
        }
        return cachedView;
    }
}
```
Event Sourcing für große Änderungen

```java
public interface Event {
    Task apply(Task task);
}

public final class CompleteEvent implements Event {
    @Override
    public Task apply(Task task) {
        return task.complete();
    }
}
```
## 10.2 Tree-Performance
Problem: Rekursive Updates durch gesamten Baum
Lösung: Path-based Updates
```java
public final class HierarchicalFolder implements TreedFolder {
    
    public TreedFolder updateAtPath(List<String> path, TreedFolder updated) {
        if (path.isEmpty() || path.get(0).equals(this.id)) {
            return path.size() == 1 ? updated : this;
        }
        
        List<TreedFolder> newSubfolders = new ArrayList<>();
        for (TreedFolder sub : subfolders) {
            if (sub.id().equals(path.get(0))) {
                newSubfolders.add(sub.updateAtPath(path.subList(1, path.size()), updated));
            } else {
                newSubfolders.add(sub);
            }
        }
        
        return new HierarchicalFolder(id, name, newSubfolders, tasks, expanded);
    }
}
```
## 10.3 Paging-Performance
Problem: Filter/Sort auf gesamter Collection
Lösung: Database-backed Implementation
```java
public final class JpaTasks implements Tasks {
    private final EntityManager em;
    
    @Override
    public void displayOn(Callable<Page> page, Consumer<List<Task>> data) {
        try {
            Page p = page.call();
            
            // SQL-Filtering statt In-Memory
            CriteriaBuilder cb = em.getCriteriaBuilder();
            CriteriaQuery<TaskEntity> cq = cb.createQuery(TaskEntity.class);
            Root<TaskEntity> root = cq.from(TaskEntity.class);
            
            // Apply filters to SQL
            for (Filter filter : p.filters()) {
                // Build WHERE clauses
            }
            
            // Apply sorting to SQL
            for (Sort sort : p.sorts()) {
                // Build ORDER BY
            }
            
            List<Task> tasks = em.createQuery(cq)
                .setFirstResult(p.offset())
                .setMaxResults(p.size())
                .getResultList()
                .stream()
                .map(entity -> new JpaTask(entity, em))
                .collect(Collectors.toList());
            
            data.accept(tasks);
        } catch (Exception e) {
            throw new IllegalStateException(e);
        }
    }
}
```

## 11. Häufige Fehler und Lösungen
### 11.1 "Ich brauche doch die ID für JSF!"
Problem:
java// User will: #{tasks.complete(item.id)}
// Aber: task.id() exponiert interne Daten!
Lösung: ID ist OK in View
```java
public interface Task.View {
    String getId();  // OK - View ist kontrollierte Grenze
}
```
// JSF
```<h:commandButton action="#{tasks.complete(item.id)}" />```
11.2 "View wird zu groß für komplexe Forms"
Problem:
```java
public interface Task.View {
    String getDescription();
    Priority getPriority();
    DueDate getDueDate();
    List<Tag> getTags();
    Assignee getAssignee();
    // 20 weitere Properties...
}
```
Lösung: Verschachtelte Views
```java
public interface Task.View {
    String getDescription();
    Priority.View getPriority();
    DueDate.View getDueDate();
    List<Tag.View> getTags();
    Assignee.View getAssignee();
}

public interface Priority {
    interface View {
        int getLevel();
        String getColor();
        String getLabel();
    }
}
```
### 11.3 "Decorator-Chain wird zu lang"
Problem:
```java 
Task task = new NotifiedTask(
    new TimestampedTask(
        new CategorizedTask(
            new PrioritizedTask(
                new UrgentTask(
                    new SimpleTask("Test")
                )
            ), Category.WORK
        )
    )
);
```
Lösung: Builder Pattern
```java
public final class TaskBuilder {
    private Task task;
    
    public TaskBuilder(String description) {
        this.task = new SimpleTask(description);
    }
    
    public TaskBuilder urgent() {
        this.task = new UrgentTask(task);
        return this;
    }
    
    public TaskBuilder timestamped() {
        this.task = new TimestampedTask(task);
        return this;
    }
    
    public Task build() {
        return task;
    }
}

// Usage
Task task = new TaskBuilder("Test")
    .urgent()
    .timestamped()
    .build();
```

### 11.4 "JSF braucht equals() für Row Selection"
Problem:
java// JSF DataTable mit Selection braucht equals()
Lösung: equals() basierend auf ID
```java
public final class TaskView implements Task.View {
    private String id;
    
    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof TaskView)) {
            return false;
        }
        TaskView other = (TaskView) obj;
        return id != null && id.equals(other.id);
    }
    
    @Override
    public int hashCode() {
        return id != null ? id.hashCode() : 0;
    }
}
```
## 12. Schlussfolgerung
Das View Pattern mit Erweiterungen (Paging, Trees, Filtering) ist die praktikabelste Lösung für JSF-Enterprise-Anwendungen, die OOP-Prinzipien ernst nehmen.
Kernpunkte:

- Business-Objekte bleiben immutable und korrekt gekapselt
- View ist mutable DTO ausschließlich für JSF-UI
- View ist kontrollierte Grenze - Objekt entscheidet, was exponiert wird
- Erweiterungen (Paging, Trees) folgen demselben Pattern
- Hollywood Principle für IoC bei komplexen Operationen
- Decorator Pattern für Funktionserweiterung
- Kovariante Rückgabetypen vermeiden Type-Casting

## Das Ergebnis:

- Wartbarer Code durch Kapselung
- Lokalisierte Änderungen durch Kohäsion
- Testbarer Code durch klare Schnittstellen
- Erweiterbarer Code durch Decorator
- JSF-kompatibel ohne OOP zu opfern

Diese Dokumentation dient als Referenz für die Entwicklung moderner JSF-Anwendungen nach echten OOP-Prinzipien.
