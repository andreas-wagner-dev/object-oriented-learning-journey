# **Teach your Objects speak to User "otherwise they will be dumb…"**

**Warum man aufhören muss, Objekte wie stumme Datencontainer zu behandeln**

**Status:** Entwurf

**Thema:** OOP, OOUX, UI of Objects, Data Animation

*(Common argument against user interface logic in the domain objects)*

Gute Software spricht die Sprache der Objekte ebenso wie die der Benutzer. Dieser Beitrag beleuchtet die Diskrepanz zwischen heutiger Entwicklungspraxis und der ursprünglichen Idee der Objektorientierung und zeigt, wie man durch OOUX und "UI of Objects" eine intuitive Verbindung vom Design der User Interfaces bis in den Code hinein schaffen kann.

"OOP means only messaging, local retention, protection and hiding of state-process, with extreme late-binding of all things." \- Alan Kay

## **1\. Das Schweigen der Objekte**

Eine schweigende Party

Stellen Sie sich vor, sie betreten eine Party. Sie gehen auf jemanden zu, um sich zu unterhalten. Aber anstatt dass diese Person antwortet, eilt ein Assistent (ein 'Service') herbei, zieht der Person einen Ausweis aus der Tasche, liest den Namen vor, überprüft den Puls und sagt dann: Diese Person ist ansprechbar. Die Person selbst steht regungslos da – eine stumme Hülle, definiert nur durch die Daten, die sie mit sich herumträgt.

Klingt absurd? In der modernen Softwareentwicklung ist das der Standard.

Die Analogie findet man in modernen "Enterprise"-Anwendungen wo "Domain"-Objekte, nichts weiter als aufgewertete Aktenschränke (Datencontainer) sind. Sie besitzen Schubladen (Getter und Setter), haben aber keine Fähigkeiten, aktive Nachrichten an ihre Benutzer zu übermitteln. Stattdessen werden sie auf Rollwägen überall hin gefahren (oft als Data Transfer Objects (DTOs) missbraucht) und deren Inhalte von gesonderten ZustellernBoten (meist von Service-Controller-Klassen) ans Tageslicht gezerrt und dann manipuliert.

### **1.1 Das Problem mit stummen Objekten**

Der Status quo in vielen Anwendungen wird von Architekturen bestimmt, die (oft in Anlehnung an das Model-View-Controller (MVC) Muster) eine strikte Layer-Trennung fordern. Diese Praxis wird primär durch die Dominanz der Mainstream-Literatur (wie bestimmte Interpretationen von Clean Architecture und Domain-Driven Design) und Best-Practice-Guides populärer Frameworks (z. B. Spring, Java EE oder Hibernate) gefördert. Diese strikte Trennung führt zur Fehlinterpretation des Model-Teils in MVC und resultiert im **Anemic Domain Model** (blutleeres Domänenmodell). Das Objekt hält zwar den Zustand, besitzt aber kein oder kaum eigenes Verhalten. Die gesamte Business-Logik wird dabei in Service-Klassen aggregiert, was diese zu schwer wartbaren "God Objects” macht.

Die Verwendung von Mutator-Methoden (auch sogenannte Setter) verletzt die Kapselung und führt zur **semantischen Kopplung**. Diese Kopplungsart liegt vor, wenn eine Klasse Wissen über die interne Struktur einer anderen Klasse benötigt. Wenn beispielsweise ein UI Controller die folgende Sequenz user.getAddress().getCity().getZipCode() aufruft, weiß er plötzlich alles über die geschachtelte Struktur des Users. Dies ist auch eine direkte Verletzung des **Law of Demeter** (Gesetz des geringsten Wissens). Ändert sich die interne Struktur des Users (z. B. die Adresse wird durch eine Location-Klasse ersetzt), bricht die gesamte Aufrufkette in der UI, obwohl die eigentliche Domänenlogik intakt bleibt.

Das Kernproblem wurzelt bereits im traditionellen UI-Design, da es sich primär auf Aktionen und Abläufe – also auf das, was der Nutzer tun möchte (Verben) – konzentriert. Beispielsweise legt eine Musik-App den Fokus auf Funktionen wie 'Musik abspielen' statt die Objekte 'Lied' oder 'Album' als zentrale Einheiten in den Vordergrund zu stellen.

### **1.2 Die Lösung sind sprechende Objekte**

Während sich das traditionelle UI-Design oft um Aufgaben und Aktionen dreht, verlagert der moderne Ansatz des **Object-Oriented UX (OOUX)** den Fokus auf die reale Objekte, mit denen Benutzer interagieren. Nutzer denken an reale Dinge (Ich archiviere diese E-Mail) statt an Datenbankzeilen (Ich setze das Status-Flag der Entity ID 123 auf 'ARCHIVED'). Diese Denkweise impliziert den Termin "User Interface of Objects”, welcher in der modernen Softwarearchitektur-Debatte maßgeblich von Robert Bräutigam geprägt und propagiert wird.

**Die UI-Debatte: Gehört die Präsentation in die Domäne?**

Der Vorschlag, dass Domänenobjekte sich selbst präsentieren sollen, ist kontrovers, aber wesentlich für echte Kohäsion.

* **Contra:** Kritiker weisen oft auf einen Abstraktionsbruch sowie eine technische Kopplung der Domäne an spezifische UI-Bibliotheken (z.B. React oder JavaFX) hin. Dies würde das Domänenobjekt weniger wiederverwendbar machen.  
* **Pro:** Befürworter (wie Robert Bräutigam) argumentieren mit hoher Kohäsion (**High Cohesion**). Das Wissen darüber, wie ein Kunde in Listenform dargestellt wird (Name fett, ID kursiv), gehört zum Kunde-Objekt selbst, da es dessen Verantwortung ist. Die UI soll als Teil der Anforderungen (Ubiquitous Language) vom *Business* betrachtet werden. Die kritische Unterscheidung liegt in der Abstraktion: Das Objekt spricht mit abstrakten Interfaces (InfoPanel, TextInput), nicht mit konkreten *HTML-Tags* oder *CSS-Klassen*.

Ein Objekt sollte demnach wie ein reales Subjekt sprechen und seine Daten **animieren**, anstatt sie zu mappen. Es sollte nicht gefragt werden: 'Gib mir deinen Titel, damit ich ihn sehen oder übertragen kann'. Stattdessen sollte man ihm sagen: **Hier ist eine Bühne (z. B. ein View oder Interface), bitte präsentiere dich.** Dies ist die Anwendung des objektorientierten Designprinzips "**Tell, Don't Ask**” in seiner reinsten Form.

## **2\. Object-Oriented User-Experience (UX) und \-Interface (UI)**

Vom mentalen Modell zur visuellen Darstellung

### **Object-Oriented UX**

OOUX ist ein Designansatz, der die gleichen Konzepte wie die objektorientierte Programmierung anwendet. Es konzentriert sich auf die Identifizierung und Priorisierung von Objekten – konzeptuellen Entitäten, die reale Elemente, Ideen, Personen, Orte oder Ereignisse abbilden.

**Aufgabenorientiertes vs. Objektorientiertes Design:**

Im Gegensatz zum traditionellen, aufgabenorientierten UX-Design, das sich auf die Abfolge von Aktionen (**Verben**) konzentriert und bei wachsender Aufgabenanzahl zu komplexen, verzweigten Schnittstellen führen kann, stellt OOUX die Objekte (**Nomen**) in den Mittelpunkt. OOUX organisiert die Benutzeroberfläche um Entitäten (z. B. Album, Song), was zu einer effizienteren, entitätszentrierten Struktur führt. Die Aktionen sind dabei logisch an den Objekten selbst angebracht. Diese Ausrichtung auf die mentalen Modelle der Benutzer (z.B. der Gedanke an "E-Mails" oder "Kontakte" anstatt an "Senden" oder Sortieren) schafft intuitive Benutzererlebnisse.

**Prinzipien der OOUX-Implementierung:**

* **Objekte:** Die Bausteine des Systems (z. B. Produkt, Benutzer).  
* **Struktur:** Die Definition der Eigenschaften jedes Objekts.  
* **Beziehungen:** Die Abbildung, wie Objekte interagieren (z. B. eine Eins-zu-Viele-Beziehung zwischen einem Benutzer und seinen Bestellungen). Die klare Definition dieser Beziehungen ist essenziell für die Erstellung kohärenter Navigationspfade.  
* **Aktionen:** Die Interaktionen, die Benutzer mit Objekten durchführen können.  
* **Kontext:** Verständnis dafür, dass Objekte sich je nach Kontext (Listenansicht vs. Detailansicht) unterschiedlich verhalten, aber dennoch dieselbe Kernidentität beibehalten müssen.  
* **Modulares Denken:** Gestaltung von Objekten als wiederverwendbare Komponenten. Dieser modulare Ansatz fördert die Konsistenz des Designsystems.

### **Object-Oriented UI (OOUI)**

Der Erfolg von OOUX hängt stark davon ab, wie diese konzeptuellen Objekte visuell zum Leben erweckt werden. Hier kommt Object-Oriented UI (OOUI) ins Spiel. OOUI nutzt interaktive Objekte, indem es von der realen Welt inspirierte Texturen und Dimensionen hinzufügt. OOUI ist die visuelle und taktile Manifestation des OOUX-Entwurfs.

**Visuelle Techniken:**

* **Texturen und Materialität:** Einbindung von Texturen, die realen Materialien (Papier, Metall) ähneln, um digitale Elemente vertrauter und fühlbarer zu machen.  
* **Tiefe und Schatten:** Nutzung von realistischen Schatten und Ebenen zur Erzeugung von Hierarchie und räumlichen Beziehungen. Dies lenkt die Aufmerksamkeit des Nutzers und visualisiert Interaktionsmöglichkeiten.  
* **Neumorphismus:** Ein Design-Trend, der durch die Kombination von Skeuomorphismus und Flat Design UI-Elemente sanft geprägt oder vertieft aus dem Hintergrundmaterial erscheinen lässt. Diese Technik schafft ein subtiles Gefühl von Körperlichkeit, wodurch digitale Schaltflächen den Eindruck erwecken, als könnten sie tatsächlich gedrückt werden.

**Vorteile der Kombination:**

Die Übernahme dieses ganzheitlichen Ansatzes richtet das Design konsequent an den mentalen Modellen der Benutzer aus, fördert Konsistenz und Wiederverwendbarkeit im gesamten Designsystem und verbessert die Skalierbarkeit. Wenn Systeme wachsen, ermöglicht der modulare Ansatz von OOUX, dass neue Funktionen oder Objekte hinzugefügt werden können, ohne die bestehende Domänenstruktur zu stören, was die langfristige Wartbarkeit verbessert.

## **3\. User Interface of Objects**

Vom Design zum Code mit Kapselung der Darstellung

Während klassische OOUI-Begriffe von IBM oder Dave Collins stammen, ist Robert Bräutigam derjenige, der den spezifischen Begriff "UI of Objects" im Kontext moderner Architektur und DDD als Lösung für das "Anemic Domain Model" verwendet.

### **Kernprinzipien von UI of Objects**

* **Objekt im Zentrum:** Die UI organisiert sich um Domänenobjekte (Kunde, Produkt), nicht um generische Aktionen oder Tabellen.  
* **Starke Kohäsion:** Die UI-Struktur (was dargestellt wird und welche Felder zur Interaktion notwendig sind) wird als natürliche Eigenschaft des Objekts betrachtet. Das Objekt weiß, wie es sich in verschiedenen Kontexten darstellen soll (displaySummary(), displayFullDetails()).  
* **Verhalten statt Daten:** Die UI-Komponente fordert das Objekt auf, etwas zu tun, anstatt Daten abzufragen. (z.B. person.displayEditForm(uiContext)).  
* **Komposition:** Komplexe Oberflächen entstehen durch die Komposition kleinerer, sich selbst darstellender Objekte.

**Die MVC-Rollen bei UI of Objects**

Anstatt Daten in Controller und View zu fragmentieren, werden die Rollen von MVC im Kontext von UI of Objects wie folgt betrachtet:

* **Model:** Das Domänenobjekt selbst (unveränderlich, mit Verhalten und Darstellungswissen).  
* **Controller:** Als (abstraktes) UI-Steuerelement (Control), das die Aktionen (z. B. Klick, Submit) des Nutzers als Befehl an das Model als Nachricht weiterleitet, anstatt dessen Daten abzufragen.  
* **View:** Die konkrete Rendering-Logik (HTML, JSF-Tags, React-Komponenten).

### **Die Mechanik von "UI of Objects"**

Weil Nutzer an Objekte denken, muss der Code dies spiegeln. Wenn aber Getter verbannt werden, wie greift man auf Daten zu?

Die Antwort ist: Gar nicht.

Man lässt das Objekt die Arbeit machen und behält die Kontrolle. Das Objekt fungiert als unveränderlicher Animator von veränderlichen Daten. Dabei wird fundamental zwischen **State, Identität und Verhalten** eines Objekts unterschieden:

* **State:** Das sind Daten, statische und potenziell mutierbare Informationen (in der Datenquelle wie Datenbank, JSON, etc.).  
* **Identität:** Die Identität eines Objekts ist seine unveränderliche Einzigartigkeit (z. B. seine Speicheradresse oder seine eindeutige ID).  
* **Behavior:** Das ist die lebendige Hülle um die Daten. Es muss **unveränderlich** (immutable) sein, um seine Integrität zu gewährleisten und Race Conditions zu vermeiden.

Ein gutes Objekt mappt die Daten nicht einfach in den Speicher (wie es traditionelle Entities oft tun). Stattdessen agiert es als **Immutable-Proxy**. Es repräsentiert die Identität einer Entität in der Domäne, ohne deren ständig wechselnden Zustand als internes Feld zu speichern.

Man stelle sich ein Objekt Person vor.

* **Traditionell:** Das Objekt lädt beim Start title und address in den Speicher. Wenn sich die externe Datenbasis (DB) ändert, ist das Objekt veraltet (stale) und die Kapselung ist nur scheinbar vorhanden.  
* **Imutable Proxy:** Das UI Objekt hält nur eine einzige Information: Seine Identität (z.B. die ID 50). Es ist ein Proxy für die echte Datenquelle.

Wenn person.title() aufgerufen wird, gibt es nicht einen gespeicherten String zurück, sondern es geht in diesem Moment zur Datenquelle und holt ihn (oder animiert ihn). Das Objekt sagt: **Ich bin nicht der Titel. Ich bin der Repräsentant von Dokument \#50 und ich weiß, wo der Titel steht und wie ich ihn formatiere und präsentiere.**

Anstatt prozedural:

// View zieht Daten aus dem Objekt (Pull)    
String title \= person.getTitle(); // \-\> verletzt Tell, Don't Ask

// ...daten mapping.    
render(title);

Macht man objektorientiert:

// Objekt animiert seine Daten auf der View (Push and Tell)    
person.displayTitle(inputTitlePanel);

// alternative Rückgabe von UI-Controls (nur Tell)

// Objekt erstellt einen Teil der View selbst   
InputText inputTitle \= person.displayTitle();

// ...oder die komplette, editierbare View von sich selbst  
InputPanel panelPerson \= person.displayInput();

**Modulare Komposition:** Ein Person-Objekt delegiert die Verantwortung für seine Darstellung an seine verschachtelten Objekte (Address), wodurch das 'Tell, Don't Ask'-Prinzip konsequent angewendet wird.

public final class Person {

    private final String name;      
    private final Address address; // ← nested Object      
          
    public InputComponent\<Person\> displayInput() {      
        return new InputGroup()      
            .add(new TextInput(Name, name))      
            .add(address.displayInput())  // ← Composition: UI of Address\!      
            .map(Person::new);      
    }      
}

Die Person weiß, wie man sich zur Eingabe darstellt, indem sie sich aus kleineren, selbst-darstellenden Objekten (Address) zusammensetzt. Das Objekt behält die volle Kontrolle darüber, wie und wann seine Daten exponiert werden, und schützt seine internen Regeln. Dadurch kann das Prinzip der **Data Animation** konsequent umgesetzt, indem das Objekt die View aktiv instruiert (Push-Prinzip) und Darstellung, Validierung sowie Formatierung selbst durchführt.

**Abstrakte UI:** Um die Kopplung der Domäne an spezifische UI-Bibliotheken zu lösen, kann eine zusätliche Abstraktion der UI Objekte verwendet werden. Ein Ansatz hierzu wird vom Alen Bob als sogennanter **“Bidirectional Builder”** vorgeschlagen, das eine Kombination aus dem Builder- und Visitor-Pattern darstellt.

public final class Person {

	private final String name;  
	private final Address address; // ← nested Object

	// constructor to build Object from View  
	public Person(Person.View view) {  
	        name \= view.name();  
		address \= new Address(view.address());  
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
	public void addName(String name) { this.name \= name;}

	@Override  
	public void addAddress(String address) { this.address \= address;}

	@Override   
        public String name() { return name;}

	@Override   
        public String address() { return address;}

	/\*\*  
	 \* Prints content as the HTML table.  
         \* \* @return HTML table string  
	 \*/  
        @Override  
	public String toString() {  
                StringBuffer out \= new StringBuffer();  
	        out.append("\<table class=\\"person-input-table\\" border=\\"0\\"\>");  
                // wrap Name with HTML  
		out.append("\<tr\>\<td\>");  
		out.append("Name:");  
		out.append("\</td\>\<td\>");  
		out.append("\<input type=\\"text\\" name=\\"name\\" value=\\"");  
		out.append(name);  
		out.append("\\"\>");  
		out.append("\</td\>\</tr\>");  
                // wrap Address with HTML  
		out.append("\<tr\>\<td\>");  
		out.append("Address:");  
		out.append("\</td\>\<td\>");  
		out.append("\<input type=\\"text\\" name=\\"address\\" value=\\"");  
		out.append(address);  
		out.append("\\"\>");  
		out.append("\</td\>\</tr\>\\n");  
		out.append("\</table\>");  
		return out.toString();  
	}  
}

// Usage:

Person jonathan \= //...

PersonView htmlPersonView \= new HtmlPersonView();  
jonathan.display(htmlPersonView);

String html \= htmlPersonView.toString();

**API Serialisierung:** Das **Tell, Don't Ask**\-Prinzip kann analog nicht nur auf die Logik, sondern auch auf die **Serialisierung** angewendet werden. Anstatt dass die API-Schicht (z. B. JAX-RS oder Spring Controller) Daten aus dem Domänenobjekt extrahiert und diese dann über einen externen Mapper (wie Jackson) serialisiert, kann das Objekt angewiesen, sich **selbst in den Response-Stream zu schreiben oder die Antwort zu generieren**. 

/\*\*  
 \* Domain interface for Person.  
 \*/  
interface Person {

       String id();

       String name();

       Address address();  
}

/\*\*  
 \* Vertical decorator of Unknown object instead of to check null.  
 \*/  
class UnknownPerson implements Person {

	private final String id;  
       // other fields name and address...

	public UnknownPerson() {  
		this.id \= UUID.randomUUID().toString();  
		this.name \= "Unknown";  
                this.address \= new Address("Unknown");  
	}

        // implementation of id(), name() and address()...  
}

/\*\*  
 \* Horizontal decorator (instead of DB layer) of Person using a JDBC DataSource.  
 \*/  
class DsPerson implements Person {

	private final DataSource ds;  
        // other fields id, name and address...

        // constructs new person from request...  
	public DsPerson(DataSource ds, String name, Address address) {  
		this.ds \= ds;  
                this.id \= UUID.randomUUID().toString();  
                this.name \= name;  
                this.address \= address;  
	}

        // constructs person from database  
	public DsPerson(DataSource ds, ResultSet rs) {  
		this.ds \= ds;  
                this.id \= rs.getString("id");  
                this.name \= rs.getString("name");  
                this.address \= new Address(rs.getString("address"));  
	}

        // factory/looup method for persons from database  
	public static Person of(DataSource ds, String id) throws SQLException  {  
		try (PreparedStatement stmt \= ds.getConnection()  
				.prepareStatement("SELECT id, name, address FROM person WHERE id \= ?;")) {  
			stmt.setString(1, id);  
			try (ResultSet rs \= stmt.executeQuery()) {  
				while (rs.next()) {  
				       return new DsPerson(ds, rs);  
				}  
			}  
		}  
		return new UnknownPerson();  
	}

        // implementation of id(), name() and address()...  
}

/\*\*  
 \* Horizontal decorator (instead of API layer) for the Person object.  
 \*/  
final class RsPerson implements Person {

	private final Person person;  
	private final String personIdToFind;

	public RsPerson(Person person, String personIdToFind) {  
		this.person \= person;  
	        this.personIdToFind \= personIdToFind;  
	}

	/\*\*  
	 \* Builds a structured Response.  
	 \*/  
	public Response toJsonResponse() {  
		try {   
                      // can not be null, but unknown...    
                      if (person.id().equals(personIdToFind)) {  
		        return Response.status(Status.OK)  
					.entity("{\\"name\\": \\"" \+ person.name()   
                                         \+ "\\", \\"address\\": \\"" \+ person.address.toString() \+ "\\"}")  
                                         .build();  
                      } else {  
                 	return Response.status(Status.NOT\_FOUND)  
					.entity("{\\"message\\": \\"Person not found.\\"}")  
					.build();  
                      }  
		} catch (SQLException e) {  
			return Response.status(Status.BAD\_REQUEST)  
					.entity("{\\"Error\\": \\"" \+ e.getMessage()   
                                         \+ "\\", \\"message\\": \\"Data Error.\\"}")  
					.build();  
		} catch (Exception e) {  
			return Response.status(Status.INTERNAL\_SERVER\_ERROR)  
					.entity("{\\"Error\\": \\"" \+ e.getMessage()   
                                         \+ "\\", \\"message\\": \\"Internal Server Error.\\"}")  
					.build();  
		}  
	}

    // implementation of id(), name() and address()...  
}

// Usage: some HTTP GET method with Url "api/person/{id}"

String personIdFind \= // requested from client...

// DataSource ds is assumed to be available in scope  
DataSource ds \= null; // Placeholder

// translated result as (JAX-RS) Response  
return new RsPerson(DsPerson.of(ds, personIdFind), personIdFind).toJsonResponse();

Dieser Ansatz ist konsequent objektorientiert: **Das Domänenobjekt spricht direkt zum Client**, während die API-Schicht lediglich die Transport- und Protokollverantwortung trägt.  Es kontrolliert vollständig, wie es dargestellt wird – ob auf einem UI-Control oder in einem JSON-Stream. Dadurch wird die Kohäsion maximiert und der "Service"-Layer eliminiert. 

## **4\. Praktische Anwendung**

Am Beispiel eines typischen Login-Prozesses wird im Folgedem demonstriert, wie die Prinzipien von OOUX und UI of Objects in der Praxis angewendet werden.

### **4.1 Anwendung der OOUX Methodik**

Im OOUX-Framework (Object-Oriented User Experience) steht die Identifizierung der realen Objekte im Fokus, bevor über Bildschirme oder Flows nachgedacht wird. Ein Login-Prozess wird hier nicht als bloßer Ablauf betrachtet, sondern als Interaktion zwischen Objekten.

Hier ist das mentale Modell eines Benutzers für ein Login-Beispiel nach der OOUX-Methodik (ORCA-Prozess):

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

1. **Identifikation:** Ich sage dem System, welches Konto mir gehört (E-MailUsername).  
2. **Authentifizierung:** Ich beweise mit einem Schlüssel (PasswortToken), dass ich der Besitzer bin.  
3. **Autorisierung:** Das System startet eine Sitzung und gewährt mir Zugriff auf meine Inhalte.

### **4.2 Anwendung im UI-Design**

Wenn wie das Modell auf 4.1.4 umsetzen möchten, sollte die UI diese logischen Einheiten widerspiegeln:

* **Gruppierung:** Platzieren Sie Felder für die Identifikation (E-Mail) und Authentifizierung (Passwort) in einem klar abgegrenzten Bereich (Panel), der das Login-Objekt repräsentiert.  
* **Feedback:** Wenn das Passwort falsch ist, ist das Objekt Konto blockiert. Die Fehlermeldung sollte sich auf das Objekt beziehen (Konto nicht gefunden oder Passwort für dieses Konto inkorrekt).  
* **Zustandsanzeige:** Nutzen Sie ein Label (z.B. im Header der Main Form), das das Objekt Identität (z.B. Eingeloggt als Max Mustermann) anzeigt, um die aktive Sitzung zu visualisieren.

Durch diesen OOUX-Ansatz vermeiden Sie ein rein prozessgesteuertes Design und schaffen eine Benutzeroberfläche, die intuitiv mit dem Verständnis des Nutzers von Besitz und Zugang übereinstimmt.

### **4.3 UI of Objects (mit Swing)**

Um die Theorie in die Praxis umzusetzen, betrachten wir ein Java Swing-Beispiel. Anstatt ein JFrame zu bauen, das Daten aus einem User-Objekt zieht (user.getName()), drehen wir den Spieß um. Wir geben dem Objekt eine "Leinwand" (Interface als eine digitale Arbeitsfläche), auf die es sich selbst malt. Das Objekt AccountSession ist der Chef. Es bestimmt, wie der Login aussieht und was passiert, wenn geklickt wird. Die Swing-Klassen sind nur dumme Werkzeuge.

// ... (Java Swing Code wie oben)  
Person 

**Zusammenfassung der Kernprinzipien im Code**

* **Kein GetAnemic Model:** Die AccountSession exponiert keine Getter. Das Domänenobjekt wird nicht 'ausgefragt', sein interner Zustand (E-Mail und Passwort) bleibt gekapselt.  
* **Inversion of Control (Push-Prinzip):** Das Domänenobjekt (Model) instruiert die View (canvas.addField()) aktiv über seine Darstellung (Push-Prinzip). Die UI agiert als passiver Empfänger dieser Befehle.  
* **Starke Verhaltenskohäsion:** Die gesamte Interaktionslogik (z.B. der ActionListener für den 'Einloggen'-Knopf) ist *innerhalb* der AccountSession gekapselt (this.authenticate()). Das Objekt trägt die alleinige Verantwortung für sein Verhalten.

### **4.4 API of Objects (für REST API mit Selbst-Serialisierung)**

### **4.5 Anwendung mit UI of Objects (mit React)**

## **5\. Fazit**

**Object-Oriented UX** stellt eine bedeutende Verschiebung in der Art und Weise dar, wie das Design von Benutzeroberflächen angegangen wird. Durch die Konzentration auf Objekte anstelle von Aufgaben stimmt OOUX stärker mit den mentalen Modellen der Benutzer überein, fördert die Konsistenz und verbessert die Skalierbarkeit.

**Object-Oriented UI** ist dabei der starke Partner, der diese abstrakten Konzepte in greifbare, interaktive Elemente übersetzt. Durch die Nutzung von Texturen, Dimensionen und interaktiven Elementen macht OOUI digitale Schnittstellen ansprechender.

**UI of Objects** erfordert auf Code-Ebene ein Umdenken: Weg von anämischen Datencontainern hin zu **Immutable-Proxies-Objekten**. Die konsequente Umsetzung von OOP manifestiert sich in Architekturen mit maximaler Kohäsion und minimaler semantischer Kopplung.

**Die Objekte schweigen nicht mehr: Sie sprechen zum User.**

## **6\. Quellen**

* Alen Key: [Definition of Object-Oriented-Programming (2003)](https://www.google.com/search?q=https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/www.quora.comWhat-does-Alan-Kay-mean-when-he-said-OOP-to-me-means-only-messaging-local-retention-and-protection-and-hiding-of-state-process-and-extreme-late-binding-of-all-things-It-can-be-done-in-Smalltalk-and-in-LISP)  
* Max Stepanov: Object-Oriented UX and Object-Oriented UI (2024)  
* Alen bob: [More on getters and setters (2004)](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html)  
* Robert Bräutigam: [Single Responsibility Principle (2018)](https://www.google.com/search?q=https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/speakerdeck.comrobertbraeutigamsingle-responsibility-principle)  
* Robert Bräutigam: [Object-Oriented Domain-Driven Design (2018)](https://www.google.com/search?q=https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/speakerdeck.comrobertbraeutigamobject-oriented-domain-driven-design)  
* Andreas Wagner: The Mechanics of Good Object (2025)