# **Die Essenz of Immutable Object**

***Data == State != Behavior == Animation => Data Animation***

Die zentrale These des Blogbeitrags von Yegor Bugayenko ist, dass **gute Objekte immer unveränderlich** (immutable) sein sollten. Dies gilt auch dann, wenn sie Entitäten der realen Welt repräsentieren, die sich häufig ändern, wie z. B. ein Dokumententitel.


```mermaid
%% Diagram 3: Simplified Flow of Data Animation and Behavior Delegation
graph TB
    %% 1. Immutable Domain Object 
    subgraph Model-World

        A["Object (Identity)"]
        style A fill:#4CAF50,stroke:#388E3C,color:#fff

        %% 2. The Contract and Behavior Delegation
        subgraph Contract-Layer
        direction LR
            B["Interface (Behavior)"]
            style B fill:#C8E6C9,stroke:#388E3C
        
            %% 3. Technical Object Layers<br/>(Real World Interface)
            subgraph Object-Layer
             direction LR
                C["UI (Behavior)"]
                D["DB (Behavior)"]

                style C fill:#BBDEFB,stroke:#1976D2
                style D fill:#BBDEFB,stroke:#1976D2
            
                %% Internal interface communication.
            
   

            end

        end

    end

    %% 4. The Real Mutable Data Storage
    subgraph Real World
        F["Database (State)"]
        style F fill:#FFC107,stroke:#FF8F00

        C <-.->|Reads/Writes<br/>UI Data| E["UI Component (State)"]
        C <-.->|Observes/Notify<br/>UI State| E
        D -.->|Reads/Writes Persistence| F
    end
    
    %% The Immutable Object holds the key and delegates all work to the interface.


    A -- Behavior --> B
    D -- Read Identity ID --> F

    %% The Interface is implemented by the technical components.

    B -- UI Update Read --> C
    B -- DB Update Write --> D

   C -- Input --> D
   D -- Output --> C
```

## **1. Das Problem: Zustand vs. Identität**

Ein typisches Objekt besteht aus Identität, Zustand und Verhalten. Ein Objekt ist dann **veränderlich** (mutable), wenn seine interne gekapselte Information (der Zustand) nach der Erstellung geändert werden kann.

**Kritik:** 
- Wenn eine Eigenschaft (z. B. der Titel eines Dokuments) häufig geändert wird, sollte sie nicht Teil des **internen Zustands** des Objekts sein.

### **Java-Beispiel 1: Das veränderliche (schlechte) Objekt**

Dieses Objekt ist schlecht, da es seine interne Identität (die Speicheradresse) beibehält, obwohl sich sein Zustand ändert.
```java
class Document {  
    private int id;  
    private String title;

    public DocumentOne(int id, String title) {  
        this.id = id;  
        this.title = title;  
    }

    // Changes the object's internal state  
    public void setTitle(String newTitle) {  
        this.title = newTitle;  
    }

    // ... Getters and toString()  
}

// Usage: The same object (identity) with new state  
DocumentOne doc = new DocumentOne(50, "Alter Titel");  
doc.setTitle("Neuer Titel"); // State changes, identity remains
```

## **2. Der Unveränderliche Wert (Value Object)**

Beim konventionellen unveränderlichen Design wird bei jeder Änderung ein **neues** Objekt erstellt. Der Zustand wird zur Identität.

### **Java-Beispiel 2: Das unveränderliche Wertobjekt (Ineffizient bei häufigen Änderungen)**
```java
@Immutable  
class Document {  
    private final int id;  
    private final String title; // Part of the internal state

    public Document(int id, String title) {  
        this.id = id;  
        this.title = title;  
    }

    // Returns a new object on change (Wither)  
    public DocumentTwo withTitle(String newTitle) {  
        return new DocumentTwo(this.id, newTitle);  
    }

    // ... printer methods like id() or title() and equals/hashCode (based on id AND title)  
}

// Usage: Two distinct objects (identities)  
DocumentTwo first = new DocumentTwo(50, "Titel A");  
DocumentTwo second = first.withTitle("Titel B"); // Creates a new object
```

**Kritik:** 
- Wenn ein Dokument sehr groß ist, ist es ineffizient, bei jeder kleinen Änderung (wie einem Titel-Update) die gesamte Datenstruktur zu klonen.

## **3. Die Lösung: Unveränderliches Proxy-Objekt**

Die Lösung liegt darin, zu erkennen, dass der Titel nicht Teil des **Objektzustands** ist, sondern Teil der **realen Entität** (z. B. einer Datei oder einer Datenbankzeile), die das Objekt **repräsentiert**.

Ein gutes unveränderliches Objekt sollte nur seine **Identität** intern kapseln (z. B. die ID oder den Speicherort) und **Verhalten** bereitstellen, um die externe, veränderliche Welt zu manipulieren. Das Objekt fungiert als **Proxy**.

### **Java-Beispiel 3: Das unveränderliche Proxy-Objekt (Der empfohlene Ansatz)**

Das Objekt selbst ist unveränderlich (nur die ID ist final), aber seine Methoden manipulieren eine externe, veränderliche Ressource (hier simuliert durch ExternalStorage).

```java
@Immutable  
class Document {  

    private final ExternalStorage externalStorage = new ExternalStorage();
     
    // The ONLY internal state is the identity to the real world  
    private final int id;

    public Document(int id) {  
        this.id = id;  
    }

    // BEHAVIOR: Reads the title from the external world  
    public String title() {  
        return externalStorage.readTitle(this.id);  
    }

    // BEHAVIOR: Writes the title to the external world  
    public void title(String text) {  
        // Manipulates the external entity, not the object itself  
        externalStorage.writeTitle(this.id, text);  
    }

    // equals() and hashCode() are based ONLY on the ID (the identity)  
    @Override  
    public boolean equals(Object doc) {  
        return doc instanceof Document  
            && Document.class.cast(doc).id == this.id;  
    }  
}

// Simulates the external, mutable storage (database, file, etc.)  
class ExternalStorage {  
    private static final Map<Integer, String> storage = new HashMap<>(0);  
    public static String readTitle(int id) {  
        return storage.getOrDefault(id, "Titel nicht gefunden");  
    }  
    public static void writeTitle(int id, String title) {  
        storage.put(id, title);  
    }  
}

// Usage: The object (identity) remains constant, only the behavior changes the external world  
DocumentThree doc = new DocumentThree(50);  
doc.title("Titel A"); // External storage is changed  
doc.title("Titel B"); // External storage is changed  
// doc is still the SAME object
```

## **Fazit: Animator von Daten**

Die Schlussfolgerung ist, dass die Rolle eines Objekts darin besteht, ein **Animator von Daten** zu sein.

* Die Daten selbst (im Speicher, in einer Datei, in einer Datenbank) sind **tot** und **veränderlich**.  
* Das Objekt ist **lebendig** und **unveränderlich** und nutzt seine Identität (ID) als Schlüssel, um auf diese externen, veränderlichen Daten zuzugreifen und sie zu manipulieren.

**Ein gutes Objekt ist ein unveränderlicher Animator von veränderlichen Daten.**
