## **Architektur-Gesamtanalyse: Radikale OO-DDD Kapselung vs. Klassische Schichtenarchitektur**

Dieses Dokument bietet eine vollständige Analyse des grundlegenden Konflikts zwischen **Objektorientierter Programmierung (OOP)** und der traditionellen **Schichtenarchitektur** in Bezug auf die **Datenkapselung**. Ziel ist die Etablierung eines **aktiven, selbstkontrollierenden Domänenobjekts** als Kernprinzip des Object-Oriented Domain Driven Design (OO-DDD).

Der zentrale Paradigmenwechsel liegt in der Kontrolle des **Kontrollflusses**: weg von der externen **Orchestrierung** (Service Layer) hin zur **Autonomie** des Domänenobjekts.

### **1\. Die Krise der Kapselung: Das Enterprise-Anti-Pattern**

OOP verlangt, dass Objekte **Verantwortung** für ihren Zustand übernehmen und diesen schützen. Kapselung bedeutet, **Geheimnisse** zu haben und eine **effektive Abstraktion** zu bieten, sodass Probleme einmal gelöst und danach nie wieder bedacht werden müssen.

#### **1.1 Der Bruch der Kapselung: Ask vs. Tell**

Das häufigste Anti-Pattern in der Unternehmenssoftware (**"Enterprise"-Code**) bricht das Prinzip **"Tell, Don't Ask"**, indem es Logik aus dem Objekt in den Service-Layer verlagert. Dies reduziert das Domänenobjekt auf einen passiven **Datenhalter** mit öffentlichen Gettern.

| Prinzipienkonform (Tell, Don't Ask) | Anti-Pattern (Daten-Exposition) |
| :---- | :---- |
| **Aktiv:** Das Objekt führt die Logik selbst aus: cat.speak(). | **Passiv:** Der Service fragt den Zustand ab: service.speak(cat.getSpeech()). |
| Das Cat-Objekt entscheidet in seiner speak()-Methode, *wie* und *was* es sagt. | Der Service ist für die Logik verantwortlich. Die **Invarianten** des Objekts sind gefährdet. |

### **2\. Die Klassische Schichtenarchitektur (Das Passive Domänenobjekt)**

In der klassischen Layered Architecture wird die Domäne auf eine **Datenstruktur** reduziert. Der Use Case (Application Service) agiert als Controller, der Domäne, Repository und UI orchestriert.

#### **2.1 Strukturelle Probleme der Schichtenarchitektur**

Die Architektur schafft zwar technische Trennung, führt aber zu einer logischen Überlastung der externen Layer:

1. **Domänen-Reduzierung:** Die "Domäne" ist oft nur **ein Viertel der gesamten Anwendung** und wird durch die Application- und Infrastructure-Layer dominiert.  
2. **Daten-Leakage und Kopplung:** Layers neigen dazu, Daten nach oben zu leaken (über **DTOs**), was ungewollte Kopplung erzeugt.  
3. **Setter-Anti-Pattern:** Die Notwendigkeit von Settern zur Befüllung durch Frameworks zwingt zur Verlagerung der **Validierung** (Schutz der Invarianten) in den Application Service oder den UI-Controller. **Die Kapselung ist gebrochen.**

#### **2.2 Diagramm: Kontrollfluss der Klassischen Architektur (Orchestrierung)**

Der Application Service ist der Dirigent, der die Domäne und I/O steuert.

```mermaid
graph TD
    subgraph UI/Infrastructure Layer
        A[UI]
        B[UpdateOrderService]
        C[OrderRepository]
        A -->|1. Ruft UseCase auf| B
        B -->|2. Lädt/Speichert| C
    end

    subgraph Application Layer
        B -->|3. Ruft Setter/Domänenmethode| D[Order]
        D -->|4. Gibt Zustand über Getter/DTOs zurück| B
    end

    subgraph Domain Layer
        D -.->|Abhängigkeit zur Repository-Interface| C
    end

    style A fill:#a2d2ff,stroke:#003366,stroke-width:2px,color:#003366
    style B fill:#ffd166,stroke:#cc8800,stroke-width:2px,color:#cc8800
    style D fill:#a8dadc,stroke:#1d3557,stroke-width:2px,color:#1d3557
    style C fill:#f08080,stroke:#8b0000,stroke-width:2px,color:#8b0000

    linkStyle 3 stroke:#ff6600,stroke-width:2px

    DNote((Domänenobjekt\nPassiver Datenhalter,\nInvarianten gefährdet))
    BNote((Application Service\norchestriert und steuert Domäne/I/O))

    classDef noteStyle fill:#fff9e6,stroke:#d6b656,stroke-width:1px,color:#000;
    class DNote,BNote noteStyle

```

### **3\. Radikale OO-DDD Kapselung: Das UI of Objects Prinzip**

Das **UI of Objects** Prinzip ist die Architekturstrategie zur **strikten Entkopplung** der Domäne von der UI. Es fordert, dass das Domänenobjekt selbst die Kontrolle über seine äußere Repräsentation (**View**) und seine Zustandsänderung (**Action**) übernimmt.

Dieses Prinzip wird durch zwei notwendige, **taktische Kommunikationsverträge** implementiert, um das Domänenobjekt vor der direkten Manipulation durch UI-Frameworks zu schützen:

#### **3.1 Taktischer Vertrag: Der View (Der UI-Speaking Part)**

Anstatt den internen Zustand über Getter preiszugeben, stellen Domänenobjekte Methoden bereit, die die **fertige Darstellung** oder ein View-Modell liefern. Die Domäne kontrolliert, wie sie dargestellt wird.

**A. Direktes Komponenten-Rendering (Wicket-Analogie)**

Das Domänenobjekt liefert direkt die **UI-Komponente**.
```java
// Wicket-Beispiel (Konzept der UI of Objects)  
public final class AccountNumber {  
    private final String accountNumber;  
      
    // Gibt die fertige Anzeige-Komponente zurück  
    public Component display(String componentId) {  
        return new Label(componentId, accountNumber);   
    }  
      
    // Gibt die fertige Editier-Komponente zurück  
    public FormComponent\<AccountNumber\> displayEditable(String componentId) {  
        return new TextField\<\>(componentId, ...);  
    }  
}
```
**B. Der View-Adapter (Für Binding-Frameworks)**

Bei Frameworks, die Getter benötigen, wird ein **View-Adapter (JsfEmployeeView)** verwendet. Dieser Adapter enthält **nur Getter** (keine Setter) und wird von der Domäne mit *validierten* Daten befüllt. Er ist das einzige DTO, das die Domäne für die UI bereitstellt.

#### **3.2 Taktischer Vertrag: Der Action Command (Der Invarianten-Gatekeeper)**

Zustandsänderungen erfolgen nur über Domänenmethoden, die eine gültige Geschäftsaktion abbilden (z.B. Employee.rename(String newName)). Der **Action Command** ist der Wrapper, der den Aufruf dieser Domänenmethode kapselt.

Dies verhindert, dass UI-Frameworks direkt Setter aufrufen und somit die Validierungslogik umgehen. Die UI kann keine beliebigen Felder setzen, sondern muss eine gekapselte Geschäftsaktion auslösen.

**Beispiel (JSF Action Command):**
```xml
\<\!-- \\  
    Der Action Command als Gatekeeper:   
    Die UI löst eine gekapselte Action aus, die dann   
    die geschäftliche Domänenmethode aufruft.  
\--\>  
\<h:commandButton value="Speichern (Action)"   
                 action="\#{employeeFrom.inputView.updateAction.call()}" /\>
```
#### **3.3 Self-Persistency (Der DB-Speaking Part)**

Als radikale Form der Kapselung wird die **Persistenz-Logik in das Domänenobjekt verlagert**. Nach einer erfolgreichen Zustandsänderung (rename()) ruft das Domänenobjekt intern seine I/O-Logik auf, um sich selbst zu persistieren.

* **Resultat:** Das Domänenobjekt ist das einzige, das entscheidet, **wann** und **wie** es gespeichert wird. Der Application Service entfällt als orchestraler Persistenz-Steuerer.

#### **3.4 Diagramm: Kontrollfluss der OO-DDD-Kapselung (Autonomie)**

Das Domänenobjekt ist aktiv, es kontrolliert die Persistenz (DB) und die Darstellung (View) autonom.
```mermaid
graph TD
    subgraph UI/Infrastructure Layer
        A[UI Component] -->|1. Ruft View/Action auf| D;
        E[Database Interface / Low-Level-I/O]
    end

    subgraph Domain Object
        D[(Order)] -->|2. Führt I/O über DB-speaking Part aus| E;
        D -->|3. Gibt fertige UI-Komponente View zurück| A;
    end

    style A fill:#a2d2ff,stroke:#003366,stroke-width:2px,color:#003366
    style D fill:#a8dadc,stroke:#1d3557,stroke-width:2px,color:#1d3557
    style E fill:#f08080,stroke:#8b0000,stroke-width:2px,color:#8b0000

    %% Kontrollfluss
    A --> D;
    D --> E;
    D --> A;

    %% Notes als Knoten
    DNote[(Domänenobjekt<br>Aktiv & Selbstkontrollierend)]
    ANote[Ist nur ein Consumer<br>der Domänen-Interfaces]

    classDef noteStyle fill:#fff9e6,stroke:#d6b656,stroke-width:1px,color:#000;
    class DNote,ANote noteStyle

    D -.-> DNote
    A -.-> ANote

    classDef Active fill:#a8dadc,stroke:#1d3557,stroke-width:2px,color:#1d3557
    class D Active

```
### **4\. Umfassender Architektur-Vergleich**

| Merkmal | Klassisch (Schichten/Hexagonal) | OO-DDD (UI of Objects) |
| :---- | :---- | :---- |
| **Rolle des Domänenobjekts** | **Passiv** (Datenhalter), verletzt **Kapselung**. | **Aktiv** (Invarianten-Beschützer), folgt **Tell, Don't Ask**. |
| **Kontrollprinzip** | **Ask, then Tell** (Controller/Service steuert und validiert). | **Tell, Don't Ask** (Domäne steuert sich selbst und ihre Invarianten). |
| **UI-Kommunikation** | **Daten** reisen (Domäne → DTO → UI) und erzeugen Kopplung. | **Views** reisen (Domäne liefert die fertige Darstellung). |
| **Validierung** | **Verteilt** (Service/Controller) oder **gebrochen** (durch Setter). | **Gekapselt** (ausschließlich in der Domäne, geschützt durch **Action Commands**). |
| **Persistenz** | Externer Dienst (**Repository**) orchestriert die Speicherung. | Interner Logikblock (**DB-Speaking Part**) steuert die Speicherung (Self-Persistency). |
| **Ziel** | Klare **Abgrenzung** von technischen Schichten. | Klare **Kapselung** des Objekts, Fokus auf **Wartbarkeit** und **Code-Qualität**. |

