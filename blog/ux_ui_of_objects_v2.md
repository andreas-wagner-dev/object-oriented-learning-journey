# **Teach your Objects speak to User "otherwise they will be dumb…"**

* **Status:** Entwurf
* **Appell:** *Warum man aufhören muss, Objekte wie stumme Datencontainer zu behandeln*
* **Thema:** #OOP, #OOUX, UI of Objects, Data Animation

> Um zu verhindern, dass Ihre Objekte stumm bleiben, müssen Sie ihnen eine Methode verleihen, um ihren internen Zustand (Daten, Funktion oder Existenz) in eine für den Benutzer verständliche Sprache (Text, visuelles Feedback oder narrativer Dialog) zu übersetzen.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ui_of_objects_meme.png)

*(The most common argument against user interface logic in the domain objects)*

Gute Software spricht die Sprache der Objekte ebenso wie die der Benutzer. Dieser Beitrag beleuchtet die Diskrepanz zwischen heutiger Entwicklungspraxis und der ursprünglichen Idee der Objektorientierung und zeigt, wie man durch OOUX und "UI of Objects" eine intuitive Verbindung vom Design der User Interfaces bis in den Code hinein schaffen kann.

## **1. Das Schweigen der Objekte**

Eine schweigende Party

Stellen Sie sich vor, Sie betreten eine Party. Sie gehen auf jemanden zu, um sich zu unterhalten. Aber anstatt dass diese Person antwortet, eilt ein Assistent (ein 'Service') herbei, zieht der Person einen Ausweis aus der Tasche, liest den Namen vor, überprüft den Puls und sagt dann: Diese Person ist ansprechbar. Die Person selbst steht regungslos da – eine stumme Hülle, definiert nur durch die Daten, die sie mit sich herumträgt.

Klingt absurd? In der modernen Softwareentwicklung ist das der Standard.

Die Analogie findet man in modernen "Enterprise"-Anwendungen wo "Domain"-Objekte, nichts weiter als aufgewertete Aktenschränke (Datencontainer) sind. Sie besitzen Schubladen (Getter und Setter), haben aber keine Fähigkeiten, aktive Nachrichten an ihre Nutzer zu übermitteln. Stattdessen werden sie auf Rollwägen überall hin gefahren (oft als [Data Transfer Objects](https://www.yegor256.com/2016/07/06/data-transfer-object.html) (DTOs) missbraucht) und deren Inhalte von gesonderten Zustellern (meist von Service- oder Controller-Klassen) ans Tageslicht gezerrt und dann manipuliert.

### **1.1 Das Problem mit stummen Objekten**

Der Status quo vieler Anwendungen ist geprägt von Architekturen, die – oft unter Berufung auf das [Model-View-Controller-Muster](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (MVC) – eine strikte Schichtentrennung erzwingen und dadurch schwer wartbare sowie starre [Datengrenzen](https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/) entstehen lassen. 

Diese Praxis wird primär durch die Dominanz der Mainstream-Literatur und deren spezifische Auslegung von Konzepten wie [Clean Architecture](https://github.com/cleancoders/CleanCodeCaseStudy/tree/master/src/cleancoderscom) oder [Domain-Driven Design](https://github.com/VaughnVernon/IDDD_Samples) befeuert. Verstärkt wird diese Entwicklung durch die Best-Practice-Guides populärer Frameworks wie [Spring](https://github.com/spring-petclinic/spring-framework-petclinic/tree/main/src/main/java/org/springframework/samples/petclinic), [Jakarta EE](https://github.com/eclipse-ee4j/jakartaee-examples/tree/main/applications/kickoff/src/main/java/org/example/kickoff) oder [Hibernate](https://github.com/hibernate/hibernate-demos), die diese Schichtentrennung als Standard vorgeben.

Diese strikte Trennung der Belange führt zu einer [Fehlinterpretation](https://martinfowler.com/eaaDev/uiArchs.html) des Models innerhalb geschichteter MVC Architekturen und mündet zwangsläufig im [Anemic Domain Model](https://www.martinfowler.com/bliki/AnemicDomainModel.html). Dabei verkommt das Objekt zum reinen Datenträger, der zwar den Zustand hält, aber kaum oder gar kein eigenes Verhalten besitzt.

Durch das Auslagern der gesamte Business-Logik in Service- oder Controller-Klassen wird die Kapselung aufgebrochen; die nötigen Getter und Setter erzeugen dabei eine unerwünschte [semantische Kopplung](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) zwischen den Komponenten. Diese Kopplungsart liegt vor, wenn eine Klasse Wissen über die interne Struktur einer anderen Klasse benötigt. Wenn beispielsweise ein UI-Controller die folgende Sequenz `user.getAddress().getCity().getZipCode()` aufruft, ist er semantisch an die Struktur des `User`-Objekts gekoppelt. Dies ist auch eine direkte Verletzung des [Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/) (Gesetz des geringsten Wissens). Ändert sich die interne Struktur des Users (z. B. die Adresse wird durch eine Location-Klasse ersetzt), bricht die gesamte Aufrufkette in der UI, obwohl die eigentliche Domänenlogik intakt bleibt.

Das Kernproblem wurzelt bereits im traditionellen UI-Design, das sich primär auf Aktionen und Abläufe konzentriert – also auf das, was der Nutzer tun möchte (**Verben**). Eine Musik-Applikation fokussiert sich beispielsweise auf Funktionen wie "Musik abspielen", anstatt die Objekte `Lied` oder `Album` als handelnde Einheiten in den Vordergrund zu stellen. Diese prozedurale Sichtweise führt dazu, dass Software-Strukturen die UI-Abläufe spiegeln. Selbst moderne Ansätze wie Domain-Driven Design (DDD) und Clean Architecture tappen in der Praxis oft in diese "Aktions-Falle": Anstatt die Autonomie der Domänenobjekte zu stärken, rücken ihre Implementierungsbeispiele oft `Use Cases` oder Application `Services` ins Zentrum. Diese fungieren wie statuslose Hilfs-Klassen, die den Ablauf steuern und das Objekt zum reinen Datenlieferanten degradieren.

### **1.2 Die Lösung sind sprechende Objekte**

Während sich das traditionelle UI-Design oft um Aufgaben und Aktionen dreht, verlagert der moderne Ansatz des **Object-Oriented UX (OOUX)** den Fokus auf die realen Objekte, mit denen Anwender interagieren. Nutzer denken an reale Dinge (Ich archiviere diese E-Mail) statt an Datenbankzeilen (Ich setze den Status-Flag der Entity ID 123 auf 'ARCHIVED'). Diese Denkweise impliziert den Termin "User Interface of Objects”, welcher in der modernen Softwarearchitektur-Debatte maßgeblich von Robert Bräutigam geprägt und propagiert wird.

**Die UI-Debatte: Gehört die Präsentation in die Domäne?**

Der Vorschlag, dass Domänenobjekte sich selbst präsentieren sollen, ist kontrovers, aber wesentlich für echte Kohäsion.

**Contra:** DDD-Experten (wie Vaughn Vernon) und Anhänger der Clean Architecture (nach Robert C. Martin, "Uncle Bob") weisen oft auf einen Abstraktionsbruch sowie eine technische Kopplung der Domäne an spezifische UI-Bibliotheken (z. B. React oder JavaFX) hin. Dies mindere die Wiederverwendbarkeit der Domänenobjekte. Sie argumentieren, dass ein Domänenobjekt ausschließlich für die Geschäftslogik zuständig sein sollte; UI-Code innerhalb des Objekts verstoße gegen das [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) **(SRP)**. Eine Klasse sollte demnach nur einen einzigen Grund haben, sich zu ändern: Ändert sich das UI-Layout, darf nicht die Business-Klasse angepasst werden müssen.

**Pro:** Befürworter (wie Allen Holub und Robert Bräutigam) argumentieren mit hoher Kohäsion (**High Cohesion**). Das Wissen darüber, wie ein Kunde in einer Liste oder in einem editierbaren Formular dargestellt wird, gehöre zum `Kunden`-Objekt selbst, da dies Teil seiner Verantwortung sei. Die Darstellung sollte als Teil der Anforderungen und der [Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html) des Fachbereichs betrachtet werden.
* Die entscheidende Differenzierung liegt in der Abstraktion: Das Objekt kommuniziert über abstrakte Interfaces statt über konkrete *HTML-Tags* oder *[CSS](https://en.wikipedia.org/wiki/CSS)-Klassen*. Anstatt Daten per Getter preiszugeben (was Kopplung erzeugt), nutzen sie ausgabespezifische Interfaces (z. B. `Media`, `InfoPanel`, `TextInput`), um die UI-Abhängigkeit zu invertieren ([Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)). 
* Aspekte zur optischen Gestaltung, wie Schriftarten oder Farben (z. B. Fettdruck für Namen, Kursivsetzung für IDs), lassen sich wahlweise in die Implementierung der UI-Komponenten, in zentrale [Look-and-Feel](https://en.wikipedia.org/wiki/Look_and_feel)-Klassen oder in globale CSS-Definitionen auslagern ([Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)). 
* Zudem wird an den eher philosophischen und soziologischen SRP-Definitionen bemängelt, dass sie aufgrund ihrer Subjektivität kaum eine klare Handlungsgrundlage bieten: Begriffe wie *Verantwortung*, *Änderungsgrund* oder die Orientierung an *Akteuren* sind zu vage für die praktische Umsetzung und führen oft zu einer unnötigen Zersplitterung des Codes. Robert Bräutigam schlägt stattdessen eine pragmatische Definition vor: **SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung**. Durch die Gleichsetzung mit Kohäsion und Kopplung wird das SRP von einer Entwurfs-Philosophie zu einer Struktur-Metrik ([Lack of Cohesion of Methods](https://en.wikipedia.org/wiki/Programming_complexity)). Während man über „Verantwortung“ streiten kann, lassen sich Kohäsion (z. B. über den LCOM-Wert) und Kopplung (Abhängigkeitsgraph) objektiv im Code nachweisen. 

Ein Objekt sollte demnach wie ein reales Subjekt agieren (sprechen) und seine Daten animieren, anstatt sie lediglich zu mappen. Man sollte es nicht fragen: **Gib mir deinen Titel, damit ich ihn anzeigen oder übertragen kann**. Stattdessen sagt man ihm: **Hier ist eine Bühne (z. B. ein UI-Control oder Interface) – bitte präsentiere dich dort.** Dies ist die Anwendung des Designprinzips [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html) in seiner reinsten Form.

## **2. Object-Oriented User-Experience (UX) und -Interface (UI)**

Vom mentalen Modell zur visuellen Darstellung

### **Object-Oriented UX**

OOUX ist ein Designansatz, der die gleichen Konzepte wie die objektorientierte Programmierung anwendet. Es konzentriert sich auf die Identifizierung und Priorisierung von Objekten – konzeptuellen Entitäten, die reale Elemente, Ideen, Personen, Orte oder Ereignisse abbilden.

Im Gegensatz zum traditionellen, aufgabenorientierten UX-Design, das sich auf die Abfolge von Aktionen (**Verben**) konzentriert und bei wachsender Aufgabenanzahl zu komplexen, verzweigten Schnittstellen führen kann, stellt OOUX die Objekte (**Nomen**) in den Mittelpunkt. OOUX organisiert die Benutzeroberfläche um Entitäten (z. B. `Album`, `Song`), was zu einer effizienteren, entitätszentrierten Struktur führt. Die Aktionen sind dabei logisch an den Objekten selbst angebracht. Diese Ausrichtung auf die mentalen Modelle der Benutzer (z.B. der Gedanke an "E-Mails" oder "Kontakte" anstatt an "Senden" oder "Sortieren") schafft intuitive Benutzererlebnisse.

**Prinzipien der OOUX-Implementierung:**

* **Objekte:** Die Bausteine des Systems (z. B. `Produkt`, `Benutzer`).  
* **Struktur:** Die Definition der Eigenschaften jedes Objekts.  
* **Beziehungen:** Die Abbildung, wie Objekte interagieren (z. B. eine Eins-zu-Viele-Beziehung zwischen einem Nutzer und seinen Bestellungen). Die klare Definition dieser Beziehungen ist essenziell für die Erstellung kohärenter Navigationspfade.  
* **Aktionen:** Die Interaktionen, die Benutzer mit Objekten durchführen können.  
* **Kontext:** Verständnis dafür, dass Objekte sich je nach Kontext (Listenansicht vs. Detailansicht) unterschiedlich verhalten, aber dennoch dieselbe Kernidentität beibehalten müssen.  
* **Modulares Denken:** Gestaltung von Objekten als wiederverwendbare Komponenten. Dieser modulare Ansatz fördert die Konsistenz des Designsystems.

Um die Lücke zwischen Software-Architektur und UI-Design zu schließen, bietet das OOUX-Framework den **ORCA-Prozess** (Objects, Relationships, Capabilities, Attributes) nach Sophia Prater. Der Prozess umfasst vier Phasen und fordert, dass Designer nicht in flüchtigen "Seiten" (Views), sondern in stabilen „Objekten“ denken. Dabei wird strikt darauf geachtet, dass diese Objekte der mentalen Welt des Nutzers entsprechen (Domain-Modell). Die Methodik orientiert sich an Navigationsmodellen, die auf der logischen Struktur der Daten basieren, anstatt auf willkürlichen Menüstrukturen. Zudem wird konsequent zwischen Kerninhalten (Content) und Metadaten unterschieden

* Phase 1. **Objects** (Objekte)
Zuerst werden die „Dinge“ identifiziert, die für den Nutzer und das Business real sind (z. B. Buch, Autor, Bestellung).
* Phase 2. **Relationships** (Beziehungen)
Hier wird definiert, wie die Objekte zueinander stehen (z. B. "Ein Autor schreibt viele Bücher").
* Phase 3. **Capabilities** (Fähigkeiten / Aktionen)
Was kann man mit den Objekten tun? (z. B. Veröffentlichen, Archivieren, Drucken).
* Phase 4. **Attributes** (Attribute)
Erst im letzten Schritt werden die konkreten Datenfelder definiert (z. B. Titel, ISBN, Preis).

Für die praktische Anwendung des Prozesses ist der Guide von [Rewired](https://www.rewiredux.com/ooux) UX sehr empfehlenswert.

### **Object-Oriented UI (OOUI)**

Der Erfolg von OOUX hängt stark davon ab, wie diese konzeptuellen Objekte visuell zum Leben erweckt werden. Hier kommt Object-Oriented UI (OOUI) ins Spiel. OOUI nutzt interaktive Objekte, indem es von der realen Welt inspirierte Texturen und Dimensionen hinzufügt. OOUI ist die optische und bedienbare Umsetzung der OOUX-Struktur. Dabei geht es darum, dass Nutzer abstrakte Daten als konkrete "Dinge" (Objekte) wahrnehmen, die sie im Interface "anfassen" können.

**Visuelle Techniken:**

* **Texturen und Materialität:** Einbindung von Texturen, die realen Materialien (Papier, Metall) ähneln, um digitale Elemente vertrauter und fühlbarer zu machen.  
* **Tiefe und Schatten:** Nutzung von realistischen Schatten und Ebenen zur Erzeugung von Hierarchie und räumlichen Beziehungen. Dies lenkt die Aufmerksamkeit des Nutzers und visualisiert Interaktionsmöglichkeiten.  
* [Neumorphismus](https://en.wikipedia.org/wiki/Neumorphism): Ein Design-Trend, der durch die Kombination von [Skeuomorphismus](https://en.wikipedia.org/wiki/Skeuomorph) und [Flat Design](https://en.wikipedia.org/wiki/Flat_design) UI-Elemente sanft geprägt oder vertieft aus dem Hintergrundmaterial erscheinen lässt. Diese Technik schafft ein subtiles Gefühl von Körperlichkeit, wodurch digitale Schaltflächen den Eindruck erwecken, als könnten sie tatsächlich gedrückt werden.

**Vorteile der Kombination:**

Die Übernahme dieses ganzheitlichen Ansatzes richtet das Design konsequent an den mentalen Modellen der Benutzer aus, fördert Konsistenz und Wiederverwendbarkeit im gesamten Designsystem und verbessert die Skalierbarkeit. Wenn Systeme wachsen, ermöglicht der modulare Ansatz von OOUX, dass neue Funktionen oder Objekte hinzugefügt werden können, ohne die bestehende Domänenstruktur zu stören, was die langfristige Wartbarkeit verbessert.

## **3. User Interface of Objects**

Vom Design zum Code mit Kapselung der Darstellung

Während klassische [OOUI-Begriffe](https://en.wikipedia.org/wiki/Object-oriented_user_interface) von IBM oder Dave Collins stammen, ist Robert Bräutigam derjenige, der den spezifischen Begriff "UI of Objects" im Kontext moderner Architektur und DDD als Lösung für das "Anemic Domain Model" verwendet.

### **Kernprinzipien von UI of Objects**

* **Objekt im Zentrum:** Die UI organisiert sich um Domänenobjekte (`Kunde`, `Produkt`), nicht um generische Aktionen oder Tabellen.  
* **Starke Kohäsion:** Die UI-Struktur (was dargestellt wird und welche Felder zur Interaktion notwendig sind) wird als natürliche Eigenschaft des Objekts betrachtet. Das Objekt weiß, wie es sich in verschiedenen Kontexten darstellen soll (`displaySummary()`, `displayFullDetails()`).  
* **Verhalten statt Daten:** Die UI-Komponente fordert das Objekt auf, etwas zu tun, anstatt Daten abzufragen. (z.B. `person.displayEditForm(uiContext)`).  
* **Komposition:** Komplexe Oberflächen entstehen durch die Komposition kleinerer, sich selbst darstellender Objekte.

**Die MVC-Rollen bei UI of Objects**

Anstatt Daten in Controller und View zu fragmentieren, werden die Rollen von MVC im Kontext von UI of Objects wie folgt betrachtet:

* **Model:** Das Domänenobjekt selbst (unveränderlich, mit Verhalten und Darstellungswissen).  
* **Controller:** Als (abstraktes) UI-Steuerelement (Control), das die Aktionen (z. B. Klick, Submit) des Nutzers als Befehl an das Model als Nachricht weiterleitet, anstatt dessen Daten abzufragen.  
* **View:** Die konkrete Rendering-Logik (HTML, JSF-Tags, React-Komponenten).

### **Die Mechanik von "UI of Objects"**

Weil Nutzer an Objekte denken, muss der Code dies spiegeln. Wenn aber Getter verbannt werden, wie greift man auf Daten zu?

Die Antwort ist: *Gar nicht*.

Man lässt das Objekt die Arbeit machen und behält die Kontrolle. Das Objekt fungiert als unveränderlicher [Animator von veränderlichen Daten](https://www.yegor256.com/2014/12/09/immutable-object-state-and-behavior.html). Dabei wird fundamental zwischen **State, Identität und Verhalten** eines Objekts unterschieden:

* **State:** Das sind Daten, statische und potenziell mutierbare Informationen (in der Datenquelle wie Datenbank, JSON, etc.).  
* **Identität:** Die Identität eines Objekts ist seine unveränderliche Einzigartigkeit (z. B. seine Speicheradresse oder seine eindeutige ID).  
* **Behavior:** Das ist die lebendige Hülle um die Daten. Es muss **unveränderlich** (immutable) sein, um seine Integrität zu gewährleisten und Race Conditions zu vermeiden.

Ein gutes Objekt mappt die Daten nicht einfach aus einem Speicher, stattdessen agiert es als **Immutable-Proxy**. Es repräsentiert die Identität einer Entität in der Domäne, ohne deren ständig wechselnden Zustand in internen Feldern zu speichern. 

Ein möglicher Ansatz, um die eingangs erwähnte "*Animation*" von Daten in Objekten umzusetzen, ist der Verzicht auf Getter zugunsten von **Printers**. Wie Yegor Bugayenko in seinem Blog-Artikel [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html) erläutert, fragen wir ein Objekt nicht nach seinem Zustand, um ihn dann mühsam in ein anderes Format zu mappen. Stattdessen übergeben wir dem Objekt ein Ziel – einen Printer – und lassen es sich selbst dort manifestieren. Das Objekt bleibt Herr über seine Daten, und wir erreichen eine saubere Trennung zwischen der logischen Essenz des Objekts und seiner technischen Repräsentation.“ Indem man Getter entfernt, zwingt man sich dazu, Verhalten in die Objekte zu legen. Das Objekt wird von einer passiven Datenstruktur zu einem aktiven, "lebendigen" Subjekt.

Man stelle sich ein Objekt `Person` vor.

* **Traditionell:** Das Objekt lädt beim Start title und address in den Speicher. Wenn sich die externe Datenbasis (DB) ändert, ist das Objekt veraltet (stale) und die Kapselung ist nur scheinbar vorhanden.  
* **Immutable Proxy:** Das UI Objekt hält nur eine einzige Information: Seine Identität (z. B. die ID 50). Es ist ein Proxy für die echte Datenquelle.

Wenn `person.title()` aufgerufen wird, gibt es nicht einen gespeicherten String zurück, sondern es geht in diesem Moment zur Datenquelle und holt ihn (oder animiert ihn). Das Objekt sagt: **Ich bin nicht der Titel. Ich bin der Repräsentant von Dokument Nummer: 50 und ich weiß, wo der Titel steht und wie ich ihn formatiere und präsentiere.**

Anstatt prozedural:
```java
// View zieht Daten aus dem Objekt (Pull)    
String title = person.getTitle(); // -> verletzt Tell, Don't Ask

// ...daten mapping.    
render(title);
```

Macht man objektorientiert:

```java
// Objekt animiert seine Daten auf einem Printer (Push and Tell)    
person.displayTitle(inputTitlePanel);

// alternativ mit Rückgabe von UI-Controls (nur Tell)

// Objekt erstellt einen Teil der View selbst   
InputText inputTitle = person.displayTitle();

// ...oder die komplette, editierbare View von sich selbst  
InputPanel panelPerson = person.displayInput();
```

**Modulare Komposition:** Ein `Person`-Objekt delegiert die Verantwortung für seine Darstellung an seine verschachtelten Objekte (`Address`), wodurch das 'Tell, Don't Ask'-Prinzip konsequent angewendet wird.

```java
public final class Person {

    private final String name;      
    private final Address address; // ← nested Object      
          
    public InputComponent<Person> displayInput() {      
        return new InputGroup()      
            .add(new TextInput("Name", name))      
            .add(address.displayInput())  // ← Composition: UI of Address!      
            .map(Person::new);      
    }      
}
```

Die `Person` weiß, wie man sich zur Eingabe darstellt, indem sie sich aus kleineren, selbst-darstellenden Objekten (`Address`) zusammensetzt. Das Objekt behält die volle Kontrolle darüber, wie und wann seine Daten exponiert werden, und schützt seine internen Regeln. Dadurch kann das Prinzip der [Data Animation](https://amihaiemil.com/2017/09/01/data-should-be-animated-not-represented.html) konsequent umgesetzt werden, indem das Objekt die View aktiv instruiert (Push-Prinzip) und Darstellung, Validierung sowie Formatierung selbst durchführt.

**Abstrakte UI:** Um die Kopplung der Domäne an spezifische UI-Bibliotheken zu lösen, kann eine zusätliche Abstraktion der UI Objekte verwendet werden. Ein Ansatz hierzu wird vom Allen Holub als sogennanter [Bidirectional Builder](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html) vorgeschlagen, das eine Kombination aus dem Builder- und Visitor-Pattern darstellt.

```java
public final class Person {

	private final String name;  
	private final Address address; // ← nested Object

	// constructor to build Object from View  
	public Person(Person.View view) {  
		name = view.name();  
		address = new Address(view.address());  
	}

	// visitor method  
	public void display(Person.View view) {  
		view.addName(name);  
		view.addAddress(address.toString());  
	}

	// define the view of this object  
	public interface View {

		// display itself via builder pattern  
		void addName(String name);  
		void addAddress(String address);  
	  
        // export or rebuild the object from the view  
		String name();  
		String address();  
	}  
}

// specific implementation of Person.View for Html serialization  
public final class HtmlPersonView implements Person.View {

	private final String name;  
	private final String address; 

	@Override  
	public void addName(String name) { this.name = name;}

	@Override  
	public void addAddress(String address) { this.address = address;}

	@Override   
    public String name() { return name;}

	@Override   
    public String address() { return address;}

	/**  
	* Prints content as the HTML table.  
	*
	* @return HTML table string  
	*/  
    @Override  
	public String toString() {  
        StringBuffer out = new StringBuffer();  
	    out.append("<table class=\"person-input-table\" border=\"0\"\>");  
        // wrap Name with HTML  
		out.append("<tr><td>");  
		out.append("Name:");  
		out.append("</td><td>");  
		out.append("<input type=\"text\" name=\"name\" value=\"");  
		out.append(name);  
		out.append("\"\>");  
		out.append("</td></tr>");  
        // wrap Address with HTML  
		out.append("<tr><td>");  
		out.append("Address:");  
		out.append("</td><td>");  
		out.append("<input type=\"text\" name=\"address\" value=\"");  
		out.append(address);  
		out.append("\"\>");  
		out.append("</td></tr>");  
		out.append("</table>");  
		return out.toString();  
	}  
}
```

```java
// Usage:

Person jonathan = //...

PersonView htmlPersonView = new HtmlPersonView();  
jonathan.display(htmlPersonView);

String html = htmlPersonView.toString();
```

**API Serialisierung:** Das **Tell, Don't Ask**-Prinzip kann analog nicht nur auf die Logik, sondern auch auf die **Serialisierung** angewendet werden. Anstatt dass die API-Schicht (z. B. JAX-RS oder Spring Controller) Daten aus dem Domänenobjekt extrahiert und diese dann über einen externen Mapper (wie Jackson) serialisiert, kann das Objekt angewiesen, sich **selbst in den Response-Stream zu schreiben oder die Antwort zu generieren**. 

```java
/**  
 * Domain interface for Person.  
 */  
interface Person {

    // identity (immutable)
	String id();

	// printer method
	String toJson();
}

/**  
 * Vertical decorator of Unknown object instead of to check null.  
 */  
class UnknownPerson implements Person {

	private final String id;  
    // other fields name and address...

	public UnknownPerson() {  
		this.id = UUID.randomUUID().toString();  
		this.name = "Unknown";  
        this.address = new Address("Unknown");  
	}

	@Override   
    public String id() { return id;}

	@override
	public String toJson() {
	    return String.format(
	      "{\"id\":\"%s\", \"name\":\"%s\", \"address\":\"%s\"}",
	      this.id, this.name, this.address.toString()
	    );
	}

    // implementation of equals(...) and hashCode()
}

/**  
 * Horizontal decorator (instead of DB layer) of Person using a JDBC DataSource.  
 */  
class DsPerson implements Person {

	private final String id;  
	private final DataSource ds;  
    // other fields id, name and address...

    // constructs new person from request...  
	public DsPerson(DataSource ds, String name, Address address) {  
		this.ds = ds;
		this.id = UUID.randomUUID().toString();  
 		this.name = name;  
		this.address = address;  
	}

    // constructs person from database  
	public DsPerson(DataSource ds, ResultSet rs) {  
		this.ds = ds;  
		this.id = rs.getString("id");  
		this.name = rs.getString("name");  
		this.address = new Address(rs.getString("address"));  
	}

	@Override   
    public String id() { return id;}

    // factory/looup method for persons from database  
	public static Person ofId(DataSource ds, String id) throws SQLException  {  
		try (PreparedStatement stmt = ds.getConnection()  
				.prepareStatement("SELECT id, name, address FROM person WHERE id = ?;")) {  
			stmt.setString(1, id);  
			try (ResultSet rs = stmt.executeQuery()) {  
				while (rs.next()) {  
					return new DsPerson(ds, rs);  
				}  
			}  
		}  
		return new UnknownPerson();  
	}

    @override
	public String toJson() {
	    return String.format(
	      "{\"id\":\"%s\", \"name\":\"%s\", \"address\":\"%s\"}",
	      this.id, this.name, this.address.toString()
	    );
	}

    // implementation of equals(...) and hashCode()
}

/**  
 * Horizontal decorator (instead of API layer) for the Person object.  
 */  
final class RsPerson implements Person {

	private final Person person;  
	private final String personIdToFind;

	public RsPerson(Person person, String personIdToFind) {  
		this.person = person;  
		this.personIdToFind = personIdToFind;  
	}

	/**  
	 * Builds a structured Response.  
	 */  
	public Response toJsonResponse() {  
		try {   
			// can not be null, but unknown...    
			if (person.id().equals(personIdToFind)) {  
				return Response.status(Status.OK)  
				.entity(person.toJson())  
				.build();  
			} else {  
				return Response.status(Status.NOT_FOUND)  
				.entity("{\"message\": \"Person not found.\"}")  
				.build();  
			}  
		} catch (SQLException e) {  
			return Response.status(Status.BAD_REQUEST)  
					.entity("{\"Error\": \"" + e.getMessage()   
							+ "\", \"message\": \"Data Error.\"}")  
					.build();  
		} catch (Exception e) {  
			return Response.status(Status.INTERNAL_SERVER_ERROR)  
					.entity("{\"Error\": \"" + e.getMessage()   
							+ "\", \"message\": \"Internal Server Error.\"}")  
					.build();  
		}  
	}

    // implementation of equals(...) and hashCode()
}

// Usage: some HTTP GET method with Url "api/person/{id}"

String personIdFind = // requested from client...

// DataSource ds is assumed to be available in scope  
DataSource ds = null; // Placeholder

// translated result as (JAX-RS) Response  
return new RsPerson(DsPerson.ofId(ds, personIdFind), personIdFind).toJsonResponse();
```

Dieser Ansatz ist konsequent objektorientiert: **Das Domänenobjekt spricht direkt zum Client**, während die API-Schicht lediglich die Transport- und Protokollverantwortung trägt. Es kontrolliert vollständig, wie es dargestellt wird – ob auf einem UI-Control oder in einem JSON-Stream. Dadurch wird die Kohäsion maximiert und der "Service"-Layer eliminiert. 

## **4. Praktische Anwendung**

Am Beispiel eines typischen Login-Prozesses wird im Folgedem demonstriert, wie die Prinzipien von OOUX und UI of Objects in der Praxis angewendet werden.

### **4.1 Anwendung der OOUX Methodik**

Im OOUX-Framework (Object-Oriented User Experience) steht die Identifizierung der realen Objekte im Fokus, bevor über Bildschirme oder Flows nachgedacht wird. Ein Login-Prozess wird hier nicht als bloßer Ablauf betrachtet, sondern als Interaktion zwischen Objekten.

Hier ist das mentale Modell eines Benutzers für ein Login-Beispiel nach der OOUX-Methodik (ORCA-Prozess):


![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ux_user_mental_model.png) 


#### **4.1.1 Die Kern-Objekte (Objects)**

Der Benutzer denkt beim Login nicht an "Code", sondern an digitale Repräsentationen von realen Dingen:

* **Konto (Account):** Das übergeordnete Objekt, das den Zugriff auf den Dienst repräsentiert.  
  * *Attribute:* E-Mail-Adresse, Benutzername, Passwort (versteckt), Sicherheitsstatus.  
* **Identität (Identity):** Wer bin ich? (Privatperson vs. geschäftliches Profil).  
* **Berechtigung (Permission):** Was darf ich tun, wenn ich drin bin?  
* **Sitzung (Session):** Der Zustand des Eingeloggt-Seins auf einem bestimmten Gerät.

#### **4.1.2 Beziehungen (Relationships)**

Das mentale Modell des Nutzers verknüpft diese Objekte:

* Ein Benutzer besitzt ein oder mehrere Konten.  
* Ein Konto ist mit einer E-Mail-Adresse verknüpft.  
* Eine Sitzung gehört zu genau einem Konto auf einem spezifischen Gerät.

#### **4.1.3. Handlungen (CapabilitiesActions)**

In OOUX werden Aktionen direkt an die Objekte geknüpft. Der Benutzer fragt sich: Was kann ich mit diesem Objekt tun?

* **Konto:** Erstellen, Verifizieren, **Authentifizieren (Login)**, Löschen.  
* **Sitzung:** Starten (Login), Beenden (Logout), Verlängern (Angemeldet bleiben).  
* **Passwort:** Ändern, Zurücksetzen (wenn vergessen).

#### **4.1.4 Mentales Modell: Das Schlüssel-Schloss-Prinzip**

Der Benutzer navigiert durch den Login-Prozess mit folgendem mentalen Schema:

1. **Identifikation:** Ich sage dem System, welches Konto mir gehört (E-Mail/Username).  
2. **Authentifizierung:** Ich beweise mit einem Schlüssel (Passwort/Token), dass ich der Besitzer bin.  
3. **Autorisierung:** Das System startet eine Sitzung und gewährt mir Zugriff auf meine Inhalte.

### **4.2 Anwendung im UI-Design**

Wenn wie das Modell auf 4.1.4 umsetzen möchten, sollte die UI diese logischen Einheiten widerspiegeln:

* **Gruppierung:** Platzieren Sie Felder für die Identifikation (E-Mail) und Authentifizierung (Passwort) in einem klar abgegrenzten Bereich (Panel), der das Login-Objekt repräsentiert.  
* **Feedback:** Wenn das Passwort falsch ist, ist das Objekt Konto blockiert. Die Fehlermeldung sollte sich auf das Objekt beziehen (Konto nicht gefunden oder das Passwort für dieses Konto inkorrekt).  
* **Zustandsanzeige:** Nutzen Sie ein Label (z.B. im Header der Main Form), dass das Objekt Identität (z.B. Eingeloggt als Max Mustermann) anzeigt, um die aktive Sitzung zu visualisieren.

Durch diesen OOUX-Ansatz vermeiden Sie ein rein prozessgesteuertes Design und schaffen eine Benutzeroberfläche, die intuitiv mit dem Verständnis des Nutzers von Besitz und Zugang übereinstimmt.

### **4.3 UI of Objects (mit Swing)**

Um die Theorie in die Praxis umzusetzen, betrachten wir ein Java Swing-Beispiel. Anstatt ein JFrame zu bauen, das Daten aus einem User-Objekt zieht (`user.getName()`), drehen wir den Spieß um. Wir geben dem Objekt eine "Leinwand" (Interface als eine digitale Arbeitsfläche), auf die es sich selbst malt. Das Objekt `AccountSession` ist der Chef. Es bestimmt, wie der Login aussieht und was passiert, wenn geklickt wird. Die Swing-Klassen sind nur dumme Werkzeuge.

```java
import java.sql.*;  
import javax.sql.DataSource;  
import javax.swing.*;

import java.awt.*;  
import java.awt.event.ActionListener;  
import java.sql.Connection;  
import java.sql.PreparedStatement;  
import java.sql.ResultSet;  
import java.sql.SQLException;  
import java.util.Objects;

// 1. The Abstraction (The "Canvas" as the digital workspace)  
// The object communicates with this interface, not directly with Swing or HTML.  
interface AccountCanvas { 

	/** Adds an input email and binds it to a buffer. */  
	AccountCanvas addEmail(String label, TextBuffer buffer);  
	
	/** Adds an input password and binds it to a buffer. */  
	AccountCanvas addPassword(String label, TextBuffer buffer);  
	
	/** Adds a button and binds an action to it. */  
	AccountCanvas addButton(String label, ActionListener action);  
	
	/** Shows the entire UI. */  
	void show();  
}

// 2. A simple buffer for data transfer (instead of Getters/Setters)  
final class TextBuffer {

	private String content = "";

	/** Updates the buffer content (e.g., via UI input). */  
	public void update(String text) {  
		// Ensure that the content is not null  
		this.content = Objects.requireNonNull(text, "text must not be null");  
	}

	/** Returns the current content. */  
	public String content() {  
		return content;  
	}  
}

// 3. Domain object hierarchy (according to OOUX/OOP principles)

// 3.1 Domain object Password Interface  
interface Password {

    //  performs actual checking    
	boolean matches(String cleartextPassword);  
}

// 3.2 Implementation of Password for a found hash
final class HashedPassword implements Password {

	private final String storedHash;

	public HashedPassword(String storedHash) {  
		this.storedHash = storedHash;  
	}

	@Override  
	public boolean matches(String cleartextPassword) {  
		// Simulation of a hash check  
		return storedHash.equals(hashSimulated(cleartextPassword));  
	}

	// Simulated hashing function for demonstration  
	private String hashSimulated(String password) {  
		if (password.equals("passwort")) {  
			return "Hashed_Pass_123";  
		}  
		return "Simulated_Invalid_Hash";  
	}  
}

// 3.3 Implementation of Password when no Account is found (Always fails)   
final class InvalidPassword implements Password {

	@Override  
	public boolean matches(String cleartextPassword) {  
		return false;  
	}  
}

// 3.4 Domain object Account Interface  
interface Account {  
	String authenticate(String email, String password) throws SecurityException;  
}

// 3.5 DbAccount implementation  
class DsAccount implements Account {

	private final DataSource dataSource;

	public DsAccount(DataSource dataSource) {  
		this.dataSource = dataSource;  
	}

	@Override  
	public String authenticate(String email, String userPassword) throws SecurityException {  
		// 1. Domain logic: Database query  
		String SQL = "SELECT password FROM accounts WHERE email = ?";  
		try (Connection conn = dataSource.getConnection();   
				PreparedStatement stmt = conn.prepareStatement(SQL)) {  
			stmt.setString(1, email);  
			Password password;  
			try (ResultSet rs = stmt.executeQuery()) {  
				if (rs.next()) {  
					String storedPasswordHash = rs.getString("password");  
					// Decorating: A hash was found  
					password = new HashedPassword(storedPasswordHash);  
				} else {  
					// Decorating: No Account found for this email  
					password = new InvalidPassword();  
				}  
			}  
			// 2. Matching: The Password object is instructed
            // to check the cleartext password.  
			if (password.matches(userPassword)) {  
				// Logic: Generate Session Token  
				return "JWT_TOKEN_" + email.toUpperCase();  
			} else {  
				// Failed authentication  
				throw new SecurityException("Invalid credentials.");  
			}  
		} catch (SQLException e) {  
			throw new SecurityException("Connection error.", e);  
		}  
	}  
}

// 4. Domain UI-Object:   
// Represents the active state of the login process (Session)  
final class AccountSession {

	// Data input buffer  
	private final TextBuffer email = new TextBuffer();  
	private final TextBuffer password = new TextBuffer();

	// Account domain Object interface  
	private final Account account;

	public AccountSession(Account account) {  
		this.account = account;  
	}

	public void displayOn(AccountCanvas canvas) {  
		// Tell principle: The AccountSession instructs the canvas how to render itself  
		canvas.addEmail("Email Address", email)  
				.addPassword("Password", password)  
				.addButton("Sign In", e -> this.authenticate());  
		// display  
		canvas.show();  
	}

	private void authenticate() {  
		String emailContent = email.content();  
		String passwordContent = password.content();  
		System.out.println("Attempting login for: " + emailContent);  
		// verify inputs  
		if (emailContent.isEmpty() || passwordContent.isEmpty()) {  
			// Display Error dialog  
			JOptionPane.showMessageDialog(null, "Please enter email and password.", "Error", JOptionPane.ERROR_MESSAGE);  
		} else {  
			try {  
				// Tell principle: Instruct the Account domain object to authenticate  
				String token = account.authenticate(emailContent, passwordContent);  
				// Display results using UI message dialog  
				JOptionPane.showMessageDialog(null, "Login successful. Token: " + token, "Success",  
						JOptionPane.INFORMATION_MESSAGE);  
			} catch (SecurityException e) {  
				// Display Error dialog  
				JOptionPane.showMessageDialog(null, e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);  
			}  
		}  
	}  
}

// 5. Concrete Swing Implementation of the Canvas (The View layer)  
final class SwingAccountCanvas implements AccountCanvas {

	private final JFrame frame = new JFrame("OOUX Account");  
	private final JPanel panel = new JPanel(new GridLayout(0, 1, 10, 10));

	public SwingAccountCanvas() {  
		// Layout and Styling  
		panel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));  
		frame.add(panel, BorderLayout.CENTER);  
		frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);  
		frame.setSize(400, 250);  
		frame.setLocationRelativeTo(null); // central to window  
	}

	@Override  
	public AccountCanvas addEmail(String label, TextBuffer buffer) {  
		JTextField field = new JTextField();  
		// Add listener to receive message as action events  
		ActionListener updateAction = e -> buffer.update(field.getText());  
		field.addActionListener(updateAction);  
		field.addFocusListener(new java.awt.event.FocusAdapter() {  
			public void focusLost(java.awt.event.FocusEvent evt) {  
				buffer.update(field.getText());  
			}  
		});  
		// Add to panel  
		panel.add(new JLabel(label));  
		panel.add(field);  
		return this;  
	}

	@Override  
	public AccountCanvas addPassword(String label, TextBuffer buffer) {  
		JTextField field = new JPasswordField();  
		// Add listener to receive message as action events  
		ActionListener updateAction = e -> buffer.update(field.getText());  
		field.addActionListener(updateAction);  
		field.addFocusListener(new java.awt.event.FocusAdapter() {  
			public void focusLost(java.awt.event.FocusEvent evt) {  
				buffer.update(field.getText());  
			}  
		});  
		// Add to panel  
		panel.add(new JLabel(label));  
		panel.add(field);  
		return this;  
	}

	@Override  
	public AccountCanvas addButton(String label, ActionListener action) {  
		JButton btn = new JButton(label);  
		// Bind Button action  
		btn.addActionListener(action);  
		// Minimal styling - Medium Sea Green for better esthetics  
		btn.setBackground(new Color(60, 179, 113));  
		btn.setForeground(Color.WHITE);  
		btn.setFocusPainted(false);  
		btn.setFont(btn.getFont().deriveFont(Font.BOLD, 14f));  
		// Add to panel  
		panel.add(btn);  
		return this;  
	}

	@Override  
	public void show() {  
		// Ensure UI updates are on the Event Dispatch Thread  
		SwingUtilities.invokeLater(() -> frame.setVisible(true));  
	}

}

// 6. Application Start  
public class SwingApplication {

	public static void main(String[] args) {  
		// Initialize Data Source  
		DataSource dataSource = initH2DataSource();  
		// Create the Account Session object, injecting the Account domain logic  
		AccountSession accountSession = new AccountSession(new DsAccount(dataSource));  
		// Tell the AccountSession object to display itself on the Swing Canvas  
		accountSession.displayOn(new SwingAccountCanvas());  
	}

	// Utility method to set up an in-memory H2 database  
	static DataSource initH2DataSource() {  
		// 1. Initialize and configure the H2 DataSource  
		org.h2.jdbcx.JdbcDataSource ds = new org.h2.jdbcx.JdbcDataSource();  
		// Use an in-memory database.  
		// DB_CLOSE_DELAY=-1 keeps the data alive as long as the JVM is running.  
		ds.setURL("jdbc:h2:mem:account_db;DB_CLOSE_DELAY=-1");  
		ds.setUser("sa");  
		ds.setPassword("");  
		// 2. Establish a connection from the DataSource  
		try (Connection conn = ds.getConnection()) {  
			// 3. Define the DDL for the Account schema  
			String createTableSql = "CREATE TABLE IF NOT EXISTS accounts ("   
			        + "id BIGINT AUTO_INCREMENT PRIMARY KEY,"  
					+ "username VARCHAR(50) NOT NULL UNIQUE,"   
			        + "email VARCHAR(100) NOT NULL UNIQUE,"  
					+ "password VARCHAR(255) NOT NULL,"   
			        + "active BOOLEAN DEFAULT TRUE,"  
					+ "created TIMESTAMP DEFAULT CURRENT_TIMESTAMP" + ")";  
			try (Statement stmt = conn.createStatement()) {  
				stmt.execute(createTableSql);  
				System.out.println("DDL executed: Table 'accounts' created successfully.");  
			}  
			// 4. Insert a sample record (DML)  
			String insertSql = "INSERT INTO accounts (username, email, password) VALUES (?, ?, ?)";  
			// Simulate a simple hash for 'password' (matches the HashedPassword simulation)  
			String sampleHash = "Hashed_Pass_123";  
			try (PreparedStatement pstmt = conn.prepareStatement(insertSql)) {  
				pstmt.setString(1, "john_doe");  
				pstmt.setString(2, "john.doe@example.com");  
				pstmt.setString(3, sampleHash);  
				pstmt.executeUpdate();  
				System.out.println("Sample account inserted.");  
			}  
		} catch (Exception e) {  
			throw new RuntimeException(e);  
		}  
		return ds;  
	}

}
```

**Zusammenfassung der Kernprinzipien im Code**

* **Volle Kapselung und Verzicht auf Getter:** Die AccountSession fragt nicht nach Daten (keine Getter), sondern delegiert die Verantwortung an andere Objekte (Tell, Don't Ask). Dadurch bleibt der interne Zustand (E-Mail und Passwort) gekapselt.  
* **Inversion of Control (Push-Prinzip):** Das Domänenobjekt (Model) instruiert die View (canvas.addEmail(), canvas.addPassword()) aktiv über seine Darstellung (Push-Prinzip). Dadurch wird die logische Trennung zwischen 'Was' (Objekt) und 'Wie' (View) gewährleistet.  
* **Starke Verhaltenskohäsion (Single Responsibility Principle - SRP):** Die gesamte Interaktionslogik (z. B. der ActionListener für den 'Login'-Knopf) ist *innerhalb* der AccountSession gekapselt (this.authenticate()). Das Objekt trägt die alleinige Verantwortung für sein Verhalten.

### **4.4 Selbst-Serialisierung (für REST API mit JAX-RS)**
```java
import jakarta.ws.rs.ApplicationPath;  
import jakarta.ws.rs.core.Application;

/**  
* JAX-RS application configuration class.   
* Define the base path for all resources as "/api".  
*/  
@ApplicationPath("/api")  
public class ApplicationResource extends Application {  
	// used for configuration  
}

import jakarta.ws.rs.*;  
import jakarta.ws.rs.POST;  
import jakarta.ws.rs.Path;  
import jakarta.ws.rs.Produces;  
import jakarta.ws.rs.core.MediaType;  
import jakarta.ws.rs.core.Response;  
import jakarta.ws.rs.core.Response.Status;

import jakarta.inject.Inject;  
import jakarta.annotation.PostConstruct;  
import jakarta.servlet.ServletContext;

import javax.sql.DataSource;

import java.sql.Connection;  
import java.sql.PreparedStatement;  
import java.sql.ResultSet;  
import java.sql.SQLException;

/** Data Transfer Object (DTO) for login credentials. */  
final class Credentials {

	private String email;  
	private String password;

	public String getEmail() {  
		return email;  
	}

	public String getPassword() {  
		return password;  
	}

	/** Checks if both email and password fields are present and non-empty. */  
	public boolean isValid() {  
		return email != null && !email.trim().isEmpty() && password != null && !password.trim().isEmpty();  
	}  
}

/**  
* Domain interface for Account operations.  
*/  
interface Account {  
	/** Authenticates a user and returns a session token upon success. */  
	String authenticate(String email, String password) throws SecurityException, SQLException;  
}

/**  
* Domain interface for Password logic (Tell, Don't Ask).  
*/  
interface Password {  
	/** Instructs the password object to check if the cleartext matches. */  
	boolean matches(String cleartextPassword);  
}

/**  
* Implementation for a known hashed password.  
*/  
final class HashedPassword implements Password {

	private final String storedHash;

	public HashedPassword(String storedHash) {  
		this.storedHash = storedHash;  
	}

	@Override  
	public boolean matches(String cleartextPassword) {  
		// Simulation of a hash check  
		return storedHash.equals(hashSimulated(cleartextPassword));  
	}

	// Simulated hashing function for demonstration purposes  
	private String hashSimulated(String password) {  
		if (password.equals("passwort")) {  
			return "hashed_passwort_12345";  
		}  
		return "invalid_hash";  
	}  
}

/**  
* Implementation for a non-existent password (always fails authentication).  
*/  
final class InvalidPassword implements Password {

	@Override  
	public boolean matches(String cleartextPassword) {  
		return false;  
	}  
}

/**  
* Domain implementation of Account using a JDBC DataSource.  
*/  
class DsAccount implements Account {

	private final DataSource dataSource;

	public DsAccount(DataSource dataSource) {  
		this.dataSource = dataSource;  
	}

	@Override  
	public String authenticate(String email, String passwordText) throws SecurityException, SQLException {  
		// 1. Domain logic: Database query  
		String SQL = "SELECT password_hash FROM accounts WHERE email = ?";  
		try (Connection conn = dataSource.getConnection(); PreparedStatement stmt = conn.prepareStatement(SQL)) {  
			stmt.setString(1, email);  
			Password password;  
			try (ResultSet rs = stmt.executeQuery()) {  
				if (rs.next()) {  
					String storedPasswordHash = rs.getString("password_hash");  
					// Hash found  
					password = new HashedPassword(storedPasswordHash);  
				} else {  
					// No account found for this email  
					password = new InvalidPassword();  
				}  
			}  
			// 2. The Password object is instructed to check itself.  
			if (password.matches(passwordText)) {  
				// Logic: Generate Session Token  
				return "JWT_TOKEN_" + email.toUpperCase();  
			} else {  
				// Failed authentication  
				throw new SecurityException("Invalid credentials.");  
			}  
		} catch (SQLException e) {  
			// Database connection/query error  
			throw new SecurityException("Connection error.", e);  
		}  
	}  
}

/**  
 * API layer decorator for the Account domain object.  
 * It translates the domain result (token or exception) into a JAX-RS Response.  
 */  
final class ApiAccount implements Account {

	private final Account account;

	public ApiAccount(Account account) {  
		this.account = account;  
	}

	@Override  
	public String authenticate(String email, String password) throws SecurityException, SQLException {  
		// 1. Delegate domain logic and receive token  
		return account.authenticate(email, password);  
	}

	/**  
	 * Main method to authenticate and build a structured Response.  
	 */  
	public Response authenticate(Credentials credentials) {  
		try {  
			if (!credentials.isValid()) {  
				throw new SecurityException("Email or password missing.");  
			}  
			String sessionToken = authenticate(credentials.getEmail(), credentials.getPassword());  
			// Success: 200 OK  
			return Response.status(Status.OK)  
					.entity("{\"token\": \"" + sessionToken + "\", \"message\": \"Login successful.\"}")
					.build();  
		} catch (SecurityException e) {  
			// Domain Error: Invalid credentials (401 Unauthorized)  
			return Response.status(Status.UNAUTHORIZED)  
					.entity("{\"Error\": \"" + e.getMessage() + "\", \"message\": \"Login unauthorized.\"}")  
					.build();  
		} catch (SQLException e) {  
			// Database Error (e.g., connection lost) (400 Bad Request / Data issue)  
			return Response.status(Status.BAD_REQUEST)  
					.entity("{\"Error\": \"" + e.getMessage() + "\", \"message\": \"Login failed.\"}")  
					.build();  
		} catch (Exception e) {  
			// Generic Error: 500 Internal Server Error  
			return Response.status(Status.INTERNAL_SERVER\_ERROR)  
					.entity("{\"Error\": \"" + e.getMessage() + "\", \"message\": \"Internal Server Error.\"}")  
					.build();  
		}  
	}  
}
```


```java
/**  
 * The JAX-RS Resource (API Layer) - Facade for the Account Resource.   
 * The URL /account reflects the interaction with the Account domain object (Login).  
 * URL path becomes /api/account  
 */  
@Path("account")  
public class AccountResource {

	@Inject  
	private ServletContext server;

	private DataSource dataSource;

	@PostConstruct  
	public void init() {  
		// Get the DataSource provided by the ServletContext (e.g., from CDI or JNDI)  
		dataSource = (DataSource) server.getAttribute(DataSource.class.getSimpleName());  
	}

	/**  
	 * POST /api/account/login  
	 * Performs the login and creates a session.  
	 * @param credentials The credentials from the client.  
	 * @return Response object, mainly for status and header control.  
	 */  
	@POST  
	@Path("/login")  
	@Produces(MediaType.APPLICATION_JSON)  
	@Consumes(MediaType.APPLICATION_JSON)  
	public Response login(Credentials credentials) {  
		// TELL PRINCIPLE: We use the ApiAccount Decorator, which handles the domain logic  
		// and the conversion to the JAX-RS Response.  
		return new ApiAccount(new DsAccount(this.dataSource)).authenticate(credentials);  
	}  
}
```
### **4.5 Anwendung mit UI of Objects (mit React)**

## **5. Fazit**

**Object-Oriented UX** stellt eine bedeutende Verschiebung in der Art und Weise dar, wie das Design von Benutzeroberflächen angegangen wird. Durch die Konzentration auf Objekte anstelle von Aufgaben stimmt OOUX stärker mit den mentalen Modellen der Benutzer überein, fördert die Konsistenz und verbessert die Skalierbarkeit.

**Object-Oriented UI** ist dabei der starke Partner, der diese abstrakten Konzepte in greifbare, interaktive Elemente übersetzt. Durch die Nutzung von Texturen, Dimensionen und interaktiven Elementen macht OOUI digitale Schnittstellen ansprechender.

**UI of Objects** erfordert auf Code-Ebene ein Umdenken: Weg von anämischen Datencontainern hin zu **Immutable-Proxies-Objekten**. Die konsequente Umsetzung von OOP manifestiert sich in Architekturen mit maximaler Kohäsion und minimaler semantischer Kopplung.

**Die Objekte schweigen nicht mehr: Sie sprechen zum User.**

## **6. Quellen**

* Alen Key: [Definition of Object-Oriented-Programming (2003)](www.quora.comWhat-does-Alan-Kay-mean-when-he-said-OOP-to-me-means-only-messaging-local-retention-and-protection-and-hiding-of-state-process-and-extreme-late-binding-of-all-things-It-can-be-done-in-Smalltalk-and-in-LISP)  
* Allen Holub: [More on getters and setters (2004)](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html)
* Robert Bräutigam: [Data boundaries are the root cause of maintenance problems (2019)](https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/)
* Robert Bräutigam: [Law of Demeter (2017)](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/)
* Robert Bräutigam: [Single Responsibility Principle (2018)](https://speakerdeck.comrobertbraeutigamsingle-responsibility-principle)  
* Robert Bräutigam: [Object-Oriented Domain-Driven Design (2018)](https://speakerdeck.comrobertbraeutigamobject-oriented-domain-driven-design)
* Martin Fowler: [GUI Architecturesl (2006)](https://martinfowler.com/eaaDev/uiArchs.html)
* Martin Fowler: [Anemic Domain Model (2003)](https://www.martinfowler.com/bliki/AnemicDomainModel.html)
* Martin Fowler: [Tell, Don’t Ask (2013)](https://martinfowler.com/bliki/TellDontAsk.html)
* Sophia Prater: [Object-Oriented UX (2025)](https://www.rewiredux.com/ooux)
* Max Stepanov: [Object-Oriented UX and Object-Oriented UI (2024)](https://outmn.medium.com/object-oriented-ux-and-object-oriented-ui-722b5abcb763)
* Mihai A. RODEGBFR: [Data Should Be Animated, Not Mapped (2017)](https://amihaiemil.com/2017/09/01/data-should-be-animated-not-represented.html)
* Yegor Bugayenko: [Data Transfer Object Is a Shame (2016)](https://www.yegor256.com/2016/07/06/data-transfer-object.html)
* Yegor Bugayenko: [How an Immutable Object Can Have State and Behavior? (2014)](https://www.yegor256.com/2014/12/09/immutable-object-state-and-behavior.html)
* Yegor Bugayenko: [Printers Instead of Getters (2016)](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html)

**Source Code Samples**
* Clean Coders: [Clean Code Case Study](https://github.com/cleancoders/CleanCodeCaseStudy/tree/master/src/cleancoderscom)
* Vaughn Vernon: [Code Samples](https://github.com/VaughnVernon/IDDD_Samples)
* Springframework: [Spring-Petclinic](https://github.com/spring-petclinic/spring-framework-petclinic/tree/main/src/main/java/org/springframework/samples/petclinic)
* Jakarta EE: [Kickoff](https://github.com/eclipse-ee4j/jakartaee-examples/tree/main/applications/kickoff/src/main/java/org/example/kickoff)
* Hibernate: [Hibernate-Demos](https://github.com/hibernate/hibernate-demos) 

