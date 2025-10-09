## **Manifest der Aktiven Objekte: UI-Speaking Objects und die Rettung der Kapselung**

Die Architektur moderner Unternehmenssoftware steht im fundamentalen Konflikt mit den Kernprinzipien der **Objektorientierten Programmierung (OOP)**. Die traditionelle **Schichtenarchitektur** reduziert Domänenobjekte zu passiven Datenstrukturen. Dieses Dokument argumentiert, dass das Prinzip der **UI-Speaking Objects (UI of Objects)** der notwendige architektonische Rahmen ist, um die **Kapselung** zu retten und Domänenobjekten ihre **Autonomie** zurückzugeben.

### **1\. Die Krise der Kapselung: Das Enterprise-Anti-Pattern**

Die Definition von OOP ist klar: Ein Objekt muss **Verantwortung** für seinen Zustand übernehmen und diesen schützen. Kapselung bedeutet, **Geheimnisse** zu haben und eine effektive Abstraktion zu bieten. Die Missachtung dieses Prinzips manifestiert sich im sogenannten "Enterprise-Code".

#### **1.1 "Tell, Don't Ask" – Der gebrochene Kontrollfluss**

Das häufigste Anti-Pattern bricht die Regel **Tell, Don't Ask**:

* **Anti-Pattern (Ask):** Externe Dienste (Service Layer, Controller) fragen das Objekt nach seinem Zustand (obj.getAge()) und entscheiden dann, was zu tun ist. Das Objekt ist ein passiver **Datenhalter**.  
* **Prinzipienkonform (Tell):** Das Objekt wird angewiesen, eine Handlung auszuführen (obj.celebrateBirthday()). Das Objekt selbst enthält die Logik und entscheidet, *wie* sich der Zustand ändert.

#### **1.2 Das Übel der Getter und Setter (Naked Data)**

Öffentliche Getter (get...) und Setter (set...) stellen **Naked Data** (nackte Daten) dar. Sie entziehen dem Objekt die Kontrolle über seine **Invarianten** (Gültigkeitsregeln).

* **Getter-Problem:** Getter geben den internen Zustand preis und binden externe Klassen an die interne Repräsentation. Anstatt String getName(), sollte ein Objekt eine Methode bereitstellen, die das Ergebnis direkt verwendet (z.B. ein Printer oder eine **View**).  
* **Setter-Problem:** Setter ermöglichen es externen Entitäten, den Zustand zu manipulieren, ohne die Geschäftslogik oder Validierung auszuführen. Dies führt zur Verlagerung der Validierung in den Service Layer, was die **Kapselung bricht**.

#### **1.3 Objekt-Integrität und Konstruktion**

Ein gesundes, aktives Objekt befolgt strenge Regeln für seine Konstruktion:

1. **Immutability:** Objekte sollten, wann immer möglich, **unveränderlich** sein. Änderungen am Zustand erzeugen ein neues Objekt.  
2. **Code-freie Konstruktoren:** Konstruktoren dienen nur dazu, die **Invarianten** zu validieren und zu setzen. Sie dürfen keine komplexe Logik oder Seiteneffekte (wie I/O) ausführen.  
3. **Namenskonventionen:** Objekt-Namen müssen **aktiv** sein und dürfen keine generischen Rollensuffixe tragen. **Vermeiden Sie** Suffixe wie \-er, \-Helper, \-Util, \-Manager, \-Client oder generische Komposita (z.B. DataProcessor), da sie auf Utility-Klassen oder passive Datenhalter hindeuten.

### **2\. Die Autonomie des Domänenobjekts**

Um aktiv und autonom zu sein, muss ein Domänenobjekt seine Schnittstellen klar definieren und die Verantwortung für I/O und UI übernehmen.

#### **2.1 Der Kontrakt-Aufbau: Interfaces als Verhalten**

Interfaces sind **starke, durchsetzbare Verträge**.

* Ein Kontrakt definiert, was ein Objekt **tut** (Verhalten), nicht, was es **enthält** (Daten).  
* Die Verwendung von Interfaces in einer OO-DDD-Architektur dient in erster Linie der **Dependency Inversion**, um die Domäne von technischen Details zu entkoppeln.  
* Das Muster der **Dekoration** (*Composition over Inheritance*) ermöglicht es, Objekten zur Laufzeit zusätzliche Verantwortlichkeiten (z.B. Logging, Caching) zu geben, ohne die ursprüngliche Klasse zu verändern.

### **3\. Das UI-Speaking Objects Prinzip (Der vertragliche Kanal)**

**UI-Speaking Objects** ist die konsequente Anwendung von Kapselung im Kontext der Benutzeroberfläche.

* **(Don’t Ask) \-** anstatt direkt die internen Datenfelder “lesen” oder “schreiben*"* **,**  
* **(Tell) \-** spricht die UI das Domänenobjekt über vertraglich zugesicherte Kanäle an **.**  
* Das Domänenobjekt selbst stellt diese Verträge bereit und kontrolliert damit den Lebenszyklus der Antwort beziehungweise der Presentation.

#### **3.1 Taktischer Vertrag 1: Der View (Lesekanal)**

Die UI **fragt das Objekt** über eine dedizierte Methode (display(), toView()) nach seiner Darstellung. Das Objekt **antwortet**, indem es eine **fertige Darstellung** oder sozusagen ein “Snapshot” von einem **UI-Modell**  bereitstellt.

* **Funktion:** Das Domänenobjekt entscheidet, wie seine Daten **formatiert** und **dargestellt** werden, ohne seine internen Felder preiszugeben. Das Objekt **weiß, wie es sich zu präsentieren hat**.  
* **Resultat:** Die UI erhält einen lesbaren, bereits für die Darstellung optimierten **Snapshot** des Zustands (z.B. in Form eines View-Adapters), der nur **Getter** enthält, da dieser Snapshot nicht mehr veränderbar ist. Die Domäne kontrolliert, wie sie gesehen wird.

#### **3.2 Taktischer Vertrag 2: Der Action Command (Schreibkanal)**

Um den Zustand zu ändern, **spricht die UI das Objekt** mit einer gültigen Geschäftsaktion an. Das **Objekt** stellt heirfür einen oder mehrer **Schreibkänale** bereit. Ein Schreibkanal umfasst eine vollständig gekapselte **Geschäftsaktion**.

* **Mechanismus:** Die UI löst einen **Action Command** aus (z.B. RenameEmployeeAction). Dieser Command enthält die Benutzer-Eingabedaten und ruft die geschäftskritische Domänenmethode auf (employee.rename(newName)).  
* **Kontrollfluss:** Der Command garantiert, dass die **Geschäftsregeln (Invarianten)** der Domäne vor der Ausführung geschützt werden.  
* **Resultat:** Die UI kann **keine beliebigen Setter** aufrufen und somit die Validierungslogik umgehen. Die Kommunikation ist auf **gültige Geschäftsaktionen** beschränkt.

#### **3.3 Self-Persistency (Der DB-Speaking Part)**

Als radikale Form der Autonomie integriert das aktive Domänenobjekt die Persistenzverantwortung.

* Nach einer erfolgreichen Zustandsänderung (invoice.pay()) ruft das Domänenobjekt intern seine I/O-Logik auf, um den geänderten Zustand zu speichern.  
* **Resultat:** Die Domäne entscheidet selbstständig, **wann** und **wie** sie persistiert wird. Der externe Service Layer wird überflüssig, und die Domäne wird von der Abhängigkeit zu einem externen **Repository**\-Muster entlastet, das oft nur eine Hülle um ein ORM darstellt.

### **4\. Fazit: Der Paradigmenwechsel**

Das **UI of Objects** Prinzip ist der konsequente Weg, die Domänenobjekte zu **autonomen, verantwortlichen Akteuren** zu machen. Es ersetzt die traditionelle Orchestrierung durch einen externen Service Layer durch eine **vertraglich definierte, bidirektionale Kommunikation** zwischen UI und Domäne:

| Funktion | Klassische Architektur | OO-DDD (UI of Objects) |
| :---- | :---- | :---- |
| **Lesen (Display)** | UI fragt Zustand über Getter ab (Ask). | UI ruft **Ouput-Vertrag** auf, Objekt liefert View (Tell). |
| **Schreiben (Ändern)** | UI setzt Zustand über Setter (Ask). | UI löst **Action Command** als **Input-Vertrag** aus, der Domänenmethode aufruft (Tell). |
| **Kontrollfluss** | Externe Orchestrierung durch Service Layer. | Autonomie und **Selbstkontrolle** durch das Domänenobjekt. |

Dieser Ansatz führt zu robusterem, wartbarerem Code, da alle Geschäftsregeln und Invarianten zentral an ihrem logischen Ort – im Domänenobjekt selbst – gekapselt und geschützt werden.
