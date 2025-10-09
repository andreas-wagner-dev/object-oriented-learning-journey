# **Das View/Action-Muster (OO-DDD)**


Das View/Action-Muster ist eine Architekturstrategie zur **strikten Entkopplung** der Domäne von der Benutzeroberfläche (UI). Es ist eine fortgeschrittene Interpretation des **Presentation-Model/Supervising Controller** Musters, das die Invarianten von Domänenobjekten (Contexts) schützt.

## **1\. Problemstellung und Konsequenzen**

Das Design von Benutzeroberflächen steht im Konflikt mit den Kernprinzipien der Objektorientierten Programmierung (OOP), insbesondere der **Datenkapselung** und dem **Single Responsibility Principle (SRP)**.

### **1.1 Die Problemstellung im UI-Kontext (Das Anti-Pattern)**

Herkömmliche Architekturen zwingen Domänenobjekte (Contexts) dazu, sich in **Datenstrukturen (DTOs)** umzuwandeln. Dies ist notwendig, damit UI-Frameworks (z. B. JSF, Spring MVC) die Daten binden können, führt aber zur **Überladung der Domäne** mit UI-spezifischen Verantwortlichkeiten:

**Beispiel: Das Setter-Anti-Pattern in der Domäne**

```java
public class Employee {  
    private String name;  
    private int salary;

    // Das Domänenobjekt stellt Setter zur Verfügung, um vom UI-Framework befüllt zu werden  
    public void setSalary(int salary) {  
        // WICHTIG: Die Validierung (z.B. salary darf nicht negativ sein) fehlt hier oft.  
        this.salary \= salary;   
    }  
    // ... andere Setter und Getter  
}

```

1. **Framework-Kopplung:** Domänenobjekte müssen direkt Setter und Getter bereitstellen. Dies koppelt die Domäne an den Lebenszyklus des UI-Frameworks.  
2. **Verletzung der Invarianten:** Wie im obigen Beispiel kann der Zustand des Objekts von außen in einem ungültigen Zustand hinterlassen werden. Wenn ein Controller den Aufruf employee.setSalary(-100) ausführt, ist das Domänenobjekt **inkonsistent**. Der Schutz der Geschäftsregeln liegt nicht mehr beim Objekt selbst, was gegen die **Kapselung** verstößt.  
3. **Verletzung des SRP:** Das Domänenobjekt wird indirekt verantwortlich für:  
   * **Geschäftslogik** (Berechnung, Validierung).  
   * **UI-Formatierung** (z. B. Währungssymbole, Datumsformate).  
   * **UI-Aktionen** (z. B. Logik zur Aktivierung/Deaktivierung von Buttons).

### **1.2 Die Konsequenz: Geteilter Zustand**

* **Datenstrukturen** (DTOs, Objekte mit Gettern/Settern) sind **Zustand**.  
* Das Weitergeben von Datenstrukturen bedeutet das **Teilen von Zustand**.  
* **Geteilter Zustand** ist die **Wurzel allen Übels** in komplexen Softwaresystemen, da er zu unkontrollierbaren, nicht-lokalen Änderungen und schwer zu verfolgenden Fehlern führt.

OOP-Objekte wurden erfunden, um ein Paradigma zu schaffen, in dem der geteilte Zustand **minimiert und kontrolliert** werden kann. Ziel ist die **Maximierung der Kohäsion** (Zusammenhalt von Logik und Zustand im Objekt) und **Minimierung der Kopplung** (gegenseitige Abhängigkeit zwischen Objekten).

### **1.3 Detaillierte Fallstudien zu OOP-Verletzungen**

Die Verweigerung von **Gettern und Settern** in der Domäne ist die direkte Konsequenz der Einhaltung von Kapselung:

#### **Fallstudie A: Das Getter/Setter-Anti-Pattern (DTO)**

Angenommen, ein Mitarbeiter (Employee) darf kein negatives Gehalt (salary) haben (eine **Invariante**).

| Ansatz | Code (Konzept) | Problem (Verletzung) |
| :---- | :---- | :---- |
| **DTO/Getter-Setter** | employee.setSalary(-100); | Externe Akteure (Controller, Services) **ignorieren die Invariante**. Die Geschäftslogik zur Gehaltsprüfung muss **überall** im Code wiederholt oder in einer separaten Validierungsschicht implementiert werden. Das Domänenobjekt wird passiv und schutzlos. |
| **View/Action (Context)** | employee.increaseSalary(amount); | Der **Context** (Employee) führt die Änderung **selbst** durch. Da das Objekt **immutable** (unveränderlich) ist, wird bei der Erstellung des neuen Objekts (new Employee(...)) im Konstruktor die Invariante garantiert (if (salary \< 0\) throw new IllegalArgumentException();). |

#### **Fallstudie B: Der Prozedurale Service Layer**

Wenn die Geschäftslogik in Services (EmployeeService) ausgelagert wird, entsteht ein **prozeduraler** Stil, der gegen das Prinzip **"Tell, Don't Ask"** verstößt.

1. **ASK:** Der Service muss das Domänenobjekt um Daten bitten (employee.getSalary()).  
2. **CALCULATE:** Der Service führt die eigentliche Logik aus (newSalary \= oldSalary \+ 50).  
3. **TELL:** Der Service setzt das Ergebnis zurück (employee.setSalary(newSalary)).

Die **Kohäsion** ist niedrig, da die Logik (was mit dem Gehalt zu tun ist) vom Zustand (das Gehalt selbst) getrennt ist. Das **View/Action-Muster** korrigiert dies, indem es die Logik **lokalisiert** und das Objekt aktiviert (employee.increaseSalary()).

## **2\. Muster zur UI-Entkopplung und deren Bewertung**

Es gibt verschiedene Muster zur Entkopplung der Domäne von der UI. Die Bewertung erfolgt anhand der Einhaltung des Prinzips: **Das Domänenobjekt darf keine UI-spezifische Verantwortung tragen.**

| Muster | Rolle des Domänenobjekts | OOP-Konformität | Nachteile |
| :---- | :---- | :---- | :---- |
| **Active Record** | Hält den Zustand und die Persistenzlogik. | **Niedrig.** Stark gekapselt, aber verletzt SRP durch das Mischen von Geschäftslogik und Persistence/UI-Binding. | Domänenobjekt ist an DB/UI gekoppelt. |
| **DTO (Data Transfer Object)** | Zustandstransport zwischen Layers. | **Niedrig.** Das Domänenobjekt wird durch Getter/Setter zur reinen Datenstruktur, verliert Kapselung. | Shared State wird im gesamten System verbreitet. |
| **Service Layer** | Domänenobjekt ist passiv; Services führen die Logik aus. | **Niedrig.** Die Domäne wird zur Datenstruktur. Die Logik ist prozedural im Service verteilt (geringe Kohäsion). | Verletzung des "Tell, Don't Ask"-Prinzips. |
| **UI of Objects** | Das Domänenobjekt erzeugt seine eigene UI-Komponente. | **Mittel.** Domäne kapselt die Logik und kennt UI-Framework-Interfaces (Component). | Direkte Kopplung an das UI-Framework-API. |
| **Presentation Model (PM) / Supervising Controller (SC)** | Trennt UI-Zustand (PM) von Domäne. | **Hoch.** Domänenobjekt ist entkoppelt, interagiert nur über eine definierte Schnittstelle mit dem PM. | Die Synchronisation zwischen PM und Domäne kann komplex werden. |

## **3\. Synthese: Das OOP-Ziel**

Das Ziel muss die **Maximierung der Kohäsion** und **Minimierung der Kopplung** sein, was zur strikten Einhaltung der Kapselung führt.

Die Synthese der obigen Muster führt zur Notwendigkeit, einen **Adapter/Vertrag** zu schaffen, der:

1. **Den geteilten Zustand für die UI aufnimmt:** Der Adapter hält die rohen, unvalidierten UI-Daten (Strings, Employee.View).  
2. **Die Geschäftslogik dem Objekt überlässt:** Das Domänenobjekt muss die Kontrolle darüber behalten, **wann** und **wie** die Daten vom Adapter übernommen werden (Import-Logik).  
3. **Die Aktionsausführung entkoppelt:** Die Logik zur Ausführung eines UI-Events (z. B. onclick, action) muss vom UI-Framework losgelöst sein.

Dies wird durch eine Kombination des **Presentation Model** mit dem **Command-Muster** erreicht.

## **4\. Die View/Action-Muster Lösung**

Das View/Action-Muster ist die konkrete Umsetzung des Presentation Model/Supervising Controller, die das **Command-Muster** (Action) nutzt, um die Aktionsausführung zu entkoppeln.

### **4.1 Die Architektur-Zwiebel (Abhängigkeits-Schichten)**

Dieses Zwiebel-Diagramm visualisiert die strikte Entkopplung: Die Abhängigkeiten zeigen immer vom Äußeren (UI) zum Inneren (Domäne). Der innere Kern (Context) ist **UI-agnostisch**.

```mermaid
graph TD
    subgraph UI_Layer [Äußere Schicht - UI/JSF]
        A(UI / JSF Framework)
    end
    subgraph Adapter_Layer [Adapter]
        B(JsfEmployeeView / JsfAction)
    end
    subgraph Orchestrator_Layer [Orchestrator]
        C(EmployeeFrom - Controller)
    end
    subgraph Domain_State_Layer [Domain State]
        D(Employees - Collection Object)
    end
    subgraph Domain_Core_Layer [Innerer Kern - Context]
        E(Employee - Context)
    end
    A --> B
    B --> C
    C --> D
    D --> E
    style E fill:#f4c7c3,stroke:#600
    style UI_Layer fill:#d9edf7,stroke:#31708f

```

### **4.2 Die Idee und der Entkopplungsmechanismus**

Die Kernidee ist, dass das Domänenobjekt nicht die Verantwortung für die Darstellung trägt, sondern seine **eigenen, abstrakten Anforderungen** an die Präsentation stellt.

* **1\. Domänenobjekt (Employee):** Das unveränderliche Herz. Es kennt **nur** sein inneres Interface (Employee.View). Es ist *UI-agnostisch*.  
* **2\. View-Interface (Employee.View):** Der Vertrag/Adapter. Er definiert die abstrakten **Datenfelder** und **Aktionen** der UI.  
* **3\. UI-Framework (JsfEmployeeView):** Der Adapter/die Implementierung. Sie bindet das Interface an die technische UI (z. B. JSF-Tags, HTML-Input-Felder).

Das Domänenobjekt (Employee) arbeitet ausschließlich mit dem **View-Interface** als bidirektionale Schnittstelle:

* **Import (Formular → Domain):** new Employee(View view)  Das Domänenobjekt zieht (pullt) die Daten selbst aus der View, um sich **unveränderlich** zu erstellen und die Invarianten zu prüfen.  
* **Export (Domain → Formular):** employee.display(View view)  Das Domänenobjekt drückt (pusht) seine formatierten Daten in eine View, um sich darzustellen.

## **5\. Kernkomponenten und Struktur**

### **5.1 Der Context (Das Subjekt)**

* **Klasse:** Employee.java  
* **Rolle:** Das unveränderliche Domänenobjekt. Definiert **Employee.View** als internes Interface.

### **5.2 Der View-Vertrag (Interface)**

* **Interface:** Employee.View  
* **Rolle:** Das **Presentation Model**. Definiert bidirektionalen Datenaustausch und alle verfügbaren **Aktionen** (updateAction()).

### **5.3 Die View-Implementierung (Der Adapter)**

* **Klasse:** JsfEmployeeView.java  
* **Rolle:** Implementiert den Employee.View-Vertrag. Hält die rohen UI-Daten (Strings) und ist der **JSF-Bindungspunkt**.

### **5.4 Das Action Command (Der Decorator)**

* **Klasse:** JsfAction.java  
* **Rolle:** Kapselt eine ausführbare Operation (Command). Speichert den **EL-String** ("\#{employeeFrom.update()}") und führt ihn **manuell über die FacesContext-API** in der Methode call() aus. Dies entkoppelt die View von der Ausführungslogik.

### **5.5 Das Collection Object**

* **Klasse:** Employees.java  
* **Rolle:** Kapselt die Liste der **Employee** Domänenobjekte. Verwaltet den **Zustand der Sammlung** (Hinzufügen, Ersetzen, Entfernen) und bietet eine Methode toViews() zur Darstellung an.

### **5.6 Der Controller (Der Orchestrator)**

* **Klasse:** EmployeeFrom.java  
* **Rolle:** Überwacht den **inputView** (Supervising Controller), verwaltet den Session-Zustand und delegiert alle Domänenoperationen an die **Employees**\-Instanz. Führt UI-spezifische Aufgaben (Navigation, FacesMessage) aus.

## **6\. Der Interaktionsfluss**

### **6.1 Datenaustausch (Bidirektional)**

| Fluss | Methode | Verantwortlich | Mechanismus |
| :---- | :---- | :---- | :---- |
| **Import** (Formular → Domain) | new Employee(View view) | Employee (Context) | Der Context **zieht** die Daten aus der View (Builder-Muster). |
| **Export** (Domain → Formular) | employees.toViews() | Employees (Collection) | Die Collection erzeugt **Adapter** (JsfEmployeeView) für die Darstellung. |

### **6.2 Action-Ausführung (Command)**

1. **View Kapselung:** Die JsfEmployeeView erzeugt eine JsfAction und speichert den Ziel-EL-String.  
2. **UI-Aufruf:** Im Frontend wird action="\#{inputView.updateAction.call()}" gebunden.  
3. **Action Ausführung:** Die Methode JsfAction.call() **unterbricht den normalen JSF-Zyklus**, wertet den gespeicherten EL-String manuell aus und ruft die Controller-Methode (EmployeeFrom.update()) auf.  
4. **Delegation:** Der Controller delegiert die Änderung des Sammlungszustands an **Employees.addOrReplace(...)**.

## **7\. Visuelle Veranschaulichung (Mermaid)**

### **7.1 Statische Struktur (Klassendiagramm)**

Dieses Diagramm zeigt die Kapselungs- und Implementierungsbeziehungen. Es hebt hervor, dass die Domäne (Employee) nur ihre eigenen Interfaces kennt, während der Controller die Employees-Collection besitzt.

```mermaid
classDiagram
    direction LR

    class Employee {
        <<Context/Domain>>
        -Name name
        -Address address
        +display(View view)
        +Employee(View view)
    }

    class Employees {
        <<Collection Object>>
        -List~Employee~ employees
        +addOrReplace(Employee e, isEditing)
        +remove(Employee e)
        +toViews() List~View~
    }

    class EmployeeView {
        <<View Contract/Presentation Model>>
        +getName() String
        +setName(String name) void
        +updateAction() Action
        +getAddress() AddressView
    }

    class JsfEmployeeView {
        <<View Adapter/JSF>>
        -Employee employee
        -String name
        -JsfAddressView address
        +getName() String
        +updateAction() JsfAction
    }

    class Action {
        <<Command>>
        +call() String
    }

    class JsfAction {
        <<Decorator/JSF>>
        -String elExpression
        +call() String
    }

    class EmployeeFrom {
        <<Controller/Orchestrator>>
        -Employees employees
        -View inputView
        +update() String
    }

    Employee o-- EmployeeView : defines
    Employees *-- Employee : manages (Composition)
    EmployeeView <|-- JsfEmployeeView : implements
    EmployeeView o-- Action : provides
    Action <|-- JsfAction : implements
    JsfEmployeeView --> JsfAction : creates
    JsfAction --> FacesContext : uses
    EmployeeFrom *-- Employees : owns
    EmployeeFrom o-- EmployeeView : manages input view
    Employees --> EmployeeView : creates adapter (toViews)
```

### **7.2 Dynamischer Flow (Sequenzdiagramm: Action-Ausführung)**

Dieses Diagramm veranschaulicht den Delegations-Flow von der UI bis zur Employees-Collection.

```mermaid
sequenceDiagram
    participant UI as index.xhtml
    participant JEV as JsfEmployeeView (Adapter)
    participant JA as JsfAction (Decorator)
    participant FC as FacesContext API
    participant EF as EmployeeFrom (Controller)
    participant Es as Employees (Collection)

    UI->>JEV: ruft updateAction.call()
    JEV->>JA: updateAction() liefert JsfAction object
    JA->>JA: ruft call() auf
    
    Note over JA: 1. Liest EL-Expression
    
    JA->>FC: evaluateExpressionGet(elExpression)
    FC->>EF: ruft update() Methode auf
    
    Note over EF: 2. ERSTELLT das immutable Employee-Objekt
    EF->>Es: addOrReplace(newEmployee, isEditing)
    
    Note over Es: 3. Collection ersetzt Domain-Objekt
    
    EF-->>FC: gibt Navigations-String zurück
    FC-->>JA: gibt Navigations-String zurück
    JA-->>UI: gibt Navigations-String zurück
    
    Note over UI: 4. JSF verwendet String zur Navigation
```
