User Interface Design: https://www.geeksforgeeks.org/software-engineering/software-engineering-user-interface-design/

**Summary**
Overall, user interface design is a key component of software engineering, 
as it can have a significant impact on the usability, effectiveness, 
and user experience of an application.
**Software engineers** should follow best practices and design principles 
to create interfaces that are user-centered, consistent, simple, and accessible.






Ihr Ansatz mit dem inneren View Interface ist pragmatischer für komplexe JSF UIs.
View Pattern - Das Beste aus beiden Welten:
Warum dieser Ansatz besser für komplexe JSF UIs ist:
## 1. JSF-freundlich ohne OOP zu opfern

```java
public interface Task {
    Task.View asView();  // Kontrollierte Exposition
    Task edit(Task.View view);  // Task versteht View
}
```
- Task bleibt immutable
- View ist mutable DTO für JSF-Binding
- Task kontrolliert, was in View exponiert wird

## 2. Direktes JSF-Binding
```xhtml
<h:inputText value="#{tasks.editingView.description}" />
```
JSF kann direkt auf View-Getter/Setter zugreifen - keine zusätzlichen Wrapper nötig.

## 3. Edit-Flow ist natürlich
```
// Start Edit: Task gibt View zurück
this.editingView = new TaskView(task.asView());
// JSF modifiziert View
editingView.setDescription("New text")
// Save: Task nimmt View und erstellt neue Version
tasks = tasks.edit(taskId, editingView);
```
## 4. Actions im View
```java
@Override
public Action edit() {
    return new Action() {
        @Override
        public String call() {
            return "#{tasks.startEdit('" + id + "')}";
        }
    };
}
```
View weiß, welche Actions verfügbar sind - keine externe Action-Factory.
## 5. Skaliert für komplexe UIs
Für komplexe Forms kann View erweitert werden:
```java
interface View {
    String getDescription();
    Priority getPriority();
    List<Tag> getTags();
    DueDate getDueDate();
    // Alles was JSF für komplexe UIs braucht
}
```
Kernidee: View ist die kontrollierte Grenze zwischen immutabler Business-Logik '(Task)' und mutable JSF-Welt.
Task entscheidet, was exponiert wird `(asView())`, und wie Änderungen angewendet werden '(edit(view))'.
