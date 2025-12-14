# **Teach your Objects speak to User "otherwise they will be dumb…"**

**Warum man aufhören muss, Objekte wie stumme Datencontainer zu behandeln**

**Status:** Entwurf

**Thema:** OOP, OOUX, UI of Objects, Data Animation

[Meme]()


Dieser Beitrag dient als Impuls für eine konsequente objektorientierte Perspektive auf Design und Implementierung von Anwendersoftware. Er betrachtet die Abweichungen in der aktuellen Softwareentwicklung von der ursprünglichen Idee der objektorientierten Programmierung und stellt die modernen Konzepte OOUX, UI of Objects und Data Animation als Lösungsansätze vor:

> "OOP means only messaging, local retention, protection and hiding of state-process, with extreme late-binding of all things." - Alan Kay

## **1. Das Schweigen der Objekte**

> Eine schweigende Party

Stellen Sie sich vor, sie betreten eine Party. Sie gehen auf jemanden zu, um sich zu unterhalten. Aber anstatt dass diese Person antwortet, eilt ein Assistent (ein 'Service') herbei, zieht der Person einen Ausweis aus der Tasche, liest den Namen vor, überprüft den Puls und sagt dann: Diese Person ist ansprechbar. Die Person selbst steht regungslos da – eine stumme Hülle, definiert nur durch die Daten, die sie mit sich herumträgt.

Klingt absurd? In der modernen Softwareentwicklung ist das der Standard.

Die Analogie findet man in modernen "Enterprise"-Anwendungen wo "Domain"-Objekte, nichts weiter als aufgewertete Aktenschränke (Datencontainer) sind. Sie besitzen Schubladen (Getter und Setter), haben aber keine Fähigkeiten, aktive Nachrichten an ihre Benutzer zu übermitteln. Stattdessen werden sie auf Rollwägen überall hin gefahren (oft als Data Transfer Objects (DTOs) missbraucht) und deren Inhalte von gesonderten ZustellernBoten (meist von Service-Controller-Klassen) ans Tageslicht gezerrt und dann manipuliert.

### **1.1 Das Problem mit stummen Objekten**

Der Status quo in vielen Anwendungen wird von Architekturen bestimmt, die (oft in Anlehnung an das Model-View-Controller (MVC) Muster) eine strikte Layer-Trennung fordern. Diese Praxis wird primär durch die Dominanz der Mainstream-Literatur (wie bestimmte Interpretationen von Clean Architecture und Domain-Driven Design) und Best-Practice-Guides populärer Frameworks (z. B. Spring, Java EE oder Hibernate) gefördert. Diese strikte Trennung führt zur Fehlinterpretation des Model-Teils in MVC und resultiert im **Anemic Domain Model** (blutleeres Domänenmodell). Das Objekt hält zwar den Zustand, besitzt aber kein oder kaum eigenes Verhalten. Die gesamte Business-Logik wird dabei in Service-Klassen aggregiert, was diese zu schwer wartbaren "God Objects” macht.

Die Verwendung von Mutator-Methoden (wie Getter und Setter) verletzt die Kapselung und führt zur **semantischen Kopplung**. Diese Kopplungsart liegt vor, wenn eine Klasse Wissen über die interne Struktur einer anderen Klasse benötigt. Wenn beispielsweise ein UI Controller die folgende Sequenz `user.getAddress().getCity().getZipCode()` aufruft, weiß er plötzlich alles über die geschachtelte Struktur des Users. Dies ist auch eine direkte Verletzung des **Law of Demeter** (Gesetz des geringsten Wissens). Ändert sich die interne Struktur des Users (z. B. die Adresse wird durch eine Location-Klasse ersetzt), bricht die gesamte Aufrufkette in der UI, obwohl die eigentliche Domänenlogik intakt bleibt.

Das Kernproblem wurzelt bereits im traditionellen UI-Design, da es sich primär auf Aktionen und Abläufe – also auf das, was der Nutzer tun möchte (Verben) – konzentriert. Beispielsweise legt eine Musik-App den Fokus auf Funktionen wie 'Musik abspielen' statt die Objekte 'Lied' oder 'Album' als zentrale Einheiten in den Vordergrund zu stellen.

### **1.2 Die Lösung sind sprechende Objekte**

Während sich das traditionelle UI-Design oft um Aufgaben und Aktionen dreht, verlagert der moderne Ansatz des **Object-Oriented UX (OOUX)** den Fokus auf die reale Objekte, mit denen Benutzer interagieren. Nutzer denken an reale Dinge (Ich archiviere diese E-Mail) statt an Datenbankzeilen (Ich setze das Status-Flag der Entity ID 123 auf 'ARCHIVED'). Diese Denkweise impliziert den Termin "User Interface of Objects”, welcher in der modernen Softwarearchitektur-Debatte maßgeblich von Robert Bräutigam geprägt und propagiert wird.

**Die UI-Debatte: Gehört die Präsentation in die Domäne?**

Der Vorschlag, dass Domänenobjekte sich selbst präsentieren sollen, ist kontrovers, aber wesentlich für echte Kohäsion.

* **Contra:** Kritiker weisen oft auf einen Abstraktionsbruch sowie eine technische Kopplung der Domäne an spezifische UI-Bibliotheken (z.B. React oder JavaFX) hin. Dies würde das Domänenobjekt weniger wiederverwendbar machen.  
* **Pro:** Befürworter (wie Robert Bräutigam) argumentieren mit hoher Kohäsion (**High Cohesion**). Das Wissen darüber, wie ein Kunde in Listenform dargestellt wird (Name fett, ID kursiv), gehört zum Kunde-Objekt selbst, da es dessen Verantwortung ist. Die UI soll als Teil der Anforderungen (Ubiquitous Language) vom *Business* betrachtet werden. Die kritische Unterscheidung liegt in der Abstraktion: Das Objekt spricht mit abstrakten Interfaces (`InfoPanel`, `TextInput`), nicht mit konkreten *HTML-Tags* oder *CSS-Klassen*.

Ein Objekt sollte demnach wie ein reales Subjekt sprechen und seine Daten **animieren**, anstatt sie zu mappen. Es sollte nicht gefragt werden: 'Gib mir deinen Titel, damit ich ihn sehen oder übertragen kann'. Stattdessen sollte man ihm sagen: **Hier ist eine Bühne (z. B. ein View oder Interface), bitte präsentiere dich.** Dies ist die Anwendung des objektorientierten Designprinzips "**Tell, Don't Ask**” in seiner reinsten Form.

## **2. Object-Oriented User-Experience (UX) und -Interface (UI)**

> Vom mentalen Modell zur visuellen Darstellung

### **Object-Oriented UX**

OOUX ist ein Designansatz, der die gleichen Konzepte wie die objektorientierte Programmierung anwendet. Es konzentriert sich auf die Identifizierung und Priorisierung von Objekten – konzeptuellen Entitäten, die reale Elemente, Ideen, Personen, Orte oder Ereignisse abbilden.

**Aufgabenorientiertes vs. Objektorientiertes Design:**

Im Gegensatz zum traditionellen, aufgabenorientierten UX-Design, das sich auf die Abfolge von Aktionen (**Verben**) konzentriert und bei wachsender Aufgabenanzahl zu komplexen, verzweigten Schnittstellen führen kann, stellt OOUX die Objekte (**Nomen**) in den Mittelpunkt. OOUX organisiert die Benutzeroberfläche um Entitäten (z. B. `Album`, `Song`), was zu einer effizienteren, entitätszentrierten Struktur führt. Die Aktionen sind dabei logisch an den Objekten selbst angebracht. Diese Ausrichtung auf die mentalen Modelle der Benutzer (z.B. der Gedanke an "E-Mails" oder "Kontakte" anstatt an "Senden" oder Sortieren) schafft intuitive Benutzererlebnisse.

**Prinzipien der OOUX-Implementierung:**

* **Objekte:** Die Bausteine des Systems (z. B. `Produkt`, `Benutzer`).  
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

## **3. User Interface of Objects**

> Vom Design zum Code mit Kapselung der Darstellung

Während klassische OOUI-Begriffe von IBM oder Dave Collins stammen, ist Robert Bräutigam derjenige, der den spezifischen Begriff "UI of Objects" im Kontext moderner Architektur und DDD als Lösung für das "Anemic Domain Model" verwendet.

### **Kernprinzipien von UI of Objects**

* **Objekt im Zentrum:** Die UI organisiert sich um Domänenobjekte (Kunde, Produkt), nicht um generische Aktionen oder Tabellen.  
* **Starke Kohäsion:** Die UI-Struktur (was dargestellt wird und welche Felder zur Interaktion notwendig sind) wird als natürliche Eigenschaft des Objekts betrachtet. Das Objekt weiß, wie es sich in verschiedenen Kontexten darstellen soll (`displaySummary()`, `displayFullDetails()`).  
* **Verhalten statt Daten:** Die UI-Komponente fordert das Objekt auf, etwas zu tun, anstatt Daten abzufragen. (z.B. `person.displayEditForm(uiContext)`).  
* **Komposition:** Komplexe Oberflächen entstehen durch die Komposition kleinerer, sich selbst darstellender Objekte.

### **Die Mechanik von "UI of Objects"**

Weil Nutzer an Objekte denken, muss der Code dies spiegeln. Wenn aber Getter verbannt werden, wie greift man auf Daten zu?

> Die Antwort ist: Gar nicht.

Man lässt das Objekt die Arbeit machen und behält die Kontrolle. Das Objekt fungiert als unveränderlicher Animator von veränderlichen Daten. Dabei wird fundamental zwischen **State, Identität und Verhalten** eines Objekts unterschieden:

* **State:** Das sind Daten, statische und potenziell mutierbare Informationen (in der Datenquelle wie Datenbank, JSON, etc.).  
* **Identität:** Die Identität eines Objekts ist seine unveränderliche Einzigartigkeit (z. B. seine Speicheradresse oder seine eindeutige ID).  
* **Behavior:** Das ist die lebendige Hülle um die Daten. Es muss **unveränderlich** (immutable) sein, um seine Integrität zu gewährleisten und Race Conditions zu vermeiden.

Ein gutes Objekt mappt die Daten nicht einfach in den Speicher (wie es traditionelle Entities oft tun). Stattdessen agiert es als **Immutable-Proxy**. Es repräsentiert die Identität einer Entität in der Domäne, ohne deren ständig wechselnden Zustand als internes Feld zu speichern.

Man stelle sich ein Objekt `Person` vor.

* **Traditionell:** Das Objekt lädt beim Start title und address in den Speicher. Wenn sich die externe Datenbasis (DB) ändert, ist das Objekt veraltet (stale) und die Kapselung ist nur scheinbar vorhanden.  
* **Als Proxy:** Das UI Objekt hält nur eine einzige Information: Seine Identität (z.B. die ID 50). Es ist ein Proxy für die echte Datenquelle.

Wenn `person.title()` aufgerufen wird, gibt es nicht einen gespeicherten String zurück, sondern es geht in diesem Moment zur Datenquelle und holt ihn (oder animiert ihn). Das Objekt sagt: **Ich bin nicht der Titel. Ich bin der Repräsentant von Dokument #50 und ich weiß, wo der Titel steht und wie ich ihn formatiere und präsentiere.**

Anstatt prozedural:
```java
// View zieht Daten aus dem Objekt (Pull)  
String title = person.getTitle(); // -> verletzt Tell, Don't Ask

// ...daten mapping.  
render(title);
```

Macht man objektorientiert:

```java
// Objekt animiert seine Daten auf der View (Push  Tell)  
person.displayTitle(inputTitlePanel);

// alternative Rückgabe von UI-Controls (nur Tell)

// Objekt erstellt ein Teil der View selbst  
InputText inputTitle = person.displayTitle();

// ...oder die komplette, editierbare View von sich selbst  
InputPanel panelPerson = person.displayInput();
```

**Die MVC-Rollen bei UI of Objects**

Anstatt Daten in Controller und View zu fragmentieren, werden die Rollen von MVC im Kontext von UI of Objects wie folgt betrachtet:

* **Model:** Das Domänenobjekt selbst (unveränderlich, mit Verhalten und Darstellungswissen).  
* **Controller:** Als abstraktes UI-Steuerelement (Control), das die Aktionen (z. B. Klick, Submit) des Nutzers als Befehl an das Model (**Tell, Don't Ask**) als Nachricht weiterleitet, anstatt dessen Daten abzufragen.  
* **View:** Die konkrete Rendering-Logik (HTML, JSF-Tags, React-Komponenten).

**Modulare Anwendung mittels Komposition:** Ein Person-Objekt delegiert die Verantwortung für seine Darstellung an seine verschachtelten Objekte (Address), wodurch das 'Tell, Don't Ask'-Prinzip konsequent angewendet wird.

```java
public final class Person {

    private final String name;    
    private final Address address;  Verschachteltes Objekt    
        
    public InputComponent<Person> displayInput() {    
        return new InputGroup()    
            .add(new TextInput(Name, name))    
            .add(address.displayInput())  // ← Komposition: Darstellung der Adresse!    
            .map(Person::new);    
    }    
}
```

Die Person weiß, wie man sich zur Eingabe darstellt, indem sie sich aus kleineren, selbst-darstellenden Objekten (`Address`) zusammensetzt. Das Objekt behält die volle Kontrolle darüber, wie und wann seine Daten exponiert werden, und schützt seine internen Regeln. Dadurch kann das Prinzip der **Data Animation** konsequent umgesetzt, indem das Objekt die View aktiv instruiert (Push-Prinzip) und Darstellung, Validierung sowie Formatierung selbst durchführt.

## **4. Praktische Anwendung**

Am Beispiel eines typischen Login-Prozesses demonstrieren wir, wie die Prinzipien von OOUX und UI of Objects in der Praxis angewendet werden.

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

* **Konto:** Erstellen, Verifizieren, Löschen.  
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

### **4.3 Anwendung UI of Objects (mit Swing)**

Um die Theorie in die Praxis umzusetzen, betrachten wir ein Java Swing-Beispiel. Anstatt ein `JFrame` zu bauen, das Daten aus einem User-Objekt zieht (`user.getName()`), drehen wir den Spieß um. Wir geben dem Objekt eine "Leinwand" (Interface als eine digitale Arbeitsfläche), auf die es sich selbst malt. Das Objekt LoginSession ist der Chef. Es bestimmt, wie der Login aussieht und was passiert, wenn geklickt wird. Die Swing-Klassen sind nur dumme Werkzeuge.

```java
import javax.swing.*;    
import java.awt.*;    
import java.awt.event.ActionListener;    
import java.util.Objects;

 /**   
 * Dies ist ein Demonstrationsbeispiel für das Prinzip User Interface of Objects    
 * (UI of Objects) in einer Swing-Anwendung.    
 * Das Domänenobjekt (LoginSession) wird aktiv angewiesen, sich auf einem abstrakten    
 * Canvas (LoginCanvas) darzustellen (Tell, Don't Ask-Prinzip), anstatt    
 * von der UI nach seinen Daten gefragt zu werden.    
 */

// 1) Die Abstraktion (Die Leinwand als digitale Arbeitsfläche)    
// Das Objekt spricht mit diesem Interface, nicht direkt mit Swing oder HTML.    
interface LoginCanvas {  
    
    //  Fügt ein Feld zur Eingabe hinzu und bindet es an einen Puffer.  
    LoginCanvas addField(String label, TextBuffer buffer);    
        
    // Fügt einen Button hinzu und bindet eine Aktion daran.   
    LoginCanvas addButton(String label, ActionListener action);    
        
    // Zeigt die gesamte UI an.    
    void show();    
}

// 2) Ein simpler Buffer für den Datentransfer (anstatt GetterSetter)    
// Dies erlaubt dem Objekt, Daten zu empfangen, ohne seinen internen State zu exponieren.    
final class TextBuffer {

    private String content = ;

    // Aktualisiert den Inhalt des Puffers (z.B. durch UI-Eingabe). 
    public void update(String text) {     
        // Sicherstellen, dass der Inhalt nicht null ist    
        this.content = Objects.requireNonNullElse(text, );     
    }

    // Gibt den aktuellen Inhalt zurück.  
    public String content() { return content; }    
}

// 3) Das Domain-Objekt (Das Model)    
// Es weiß, welche Felder es braucht und was beim Klick passiert.    
final class LoginSession {

    // Die Datenpuffer halten den aktuellen Eingabezustand    
    private final TextBuffer email = new TextBuffer();    
    private final TextBuffer password = new TextBuffer();

     /**   
     * Das Objekt instruiert die Leinwand, wie es sich darstellen soll.    
     * Dies ist die Anwendung des Tell, Don't Ask-Prinzips:      
     * Das Domänenobjekt ist der Chef der Darstellung.    
     * @param canvas Die abstrakte Zeichenfläche, auf der sich das Objekt malt.    
     */    
    public void displayOn(LoginCanvas canvas) {    
        canvas    
            .addField(E-Mail Adresse, email)    
            .addField(Passwort, password)    
             // Die Logik für den Button-Klick wird als Lambda-Ausdruck im Objekt definiert    
            .addButton(Einloggen, e -> this.authenticate())    
            .show();    
    }

    /**   
     * Die gesamte Authentifizierungslogik bleibt gekapselt im Domain-Objekt.    
     * Die UI muss den State nicht kennen oder manipulieren.    
     */    
    private void authenticate() {  
    
        String emailContent = email.content();    
        String passwordContent = password.content();    
            
        System.out.println(Versuche Login für:  + emailContent);    
            
        // Simuliere einfache Validierung und Ausgabe mit Custom Modal UI    
        String message;    
        String title;    
        int messageType;    
            
        if (emailContent.isEmpty() || passwordContent.isEmpty()) {    
            message = Bitte E-Mail und Passwort eingeben.;    
            title = Fehler;    
            messageType = JOptionPane.ERROR_MESSAGE;  
        } else if (emailContent.equals(demo@ooux.de) && passwordContent.equals(passwort)) {    
            message = Login erfolgreich für:  + emailContent;    
            title = Erfolg;    
            messageType = JOptionPane.INFORMATION_MESSAGE;    
        } else {    
            message = Ungültige Anmeldedaten.;    
            title = Fehler;    
            messageType = JOptionPane.ERROR_MESSAGE;    
        }    
            
        // Anzeige der Nachricht    
        JOptionPane.showMessageDialog(null, message, title, messageType);    
    }    
}


// 4) Die technische Implementierung (Die View)**

// Ein Mechanismus, der Pixel auf den Bildschirm bringt (hier Swing).    
final class SwingLoginCanvas implements LoginCanvas {

    private final JFrame frame = new JFrame(OOUX Login);    
    private final JPanel panel = new JPanel(new GridLayout(0, 1, 10, 10));

    public SwingLoginCanvas() {    
         Grundlegendes Layout und Styling    
        panel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));    
        frame.add(panel, BorderLayout.CENTER);    
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);    
        frame.setSize(400, 250);     
        frame.setLocationRelativeTo(null);  Zentriert das Fenster    
    }

    @Override    
    public LoginCanvas addField(String label, TextBuffer buffer) {  
    
        // Für das Passwortfeld eine JPasswordField verwenden    
        JTextField field = label.toLowerCase().contains(passwort) ? new JPasswordField() : new JTextField();    
            
        // Bindung: Swing schreibt in den Buffer des Objekts, sobald der Inhalt geändert wird (Enter oder Fokusverlust)    
        ActionListener updateAction = e -> buffer.update(field.getText());    
        field.addActionListener(updateAction);    
        field.addFocusListener(new java.awt.event.FocusAdapter() {    
            public void focusLost(java.awt.event.FocusEvent evt) {    
                buffer.update(field.getText());    
            }    
        });    
          
        panel.add(new JLabel(label));    
        panel.add(field);    
        return this;    
    }

    @Override    
    public LoginCanvas addButton(String label, ActionListener action) {    
          
        JButton btn = new JButton(label);    
        // Bindung: Der Button löst die Aktion aus, die das Objekt definiert hat    
        btn.addActionListener(action);    
            
         Minimales Styling für eine bessere Ästhetik    
        btn.setBackground(new Color(60, 179, 113));  Medium Sea Green    
        btn.setForeground(Color.WHITE);    
        btn.setFocusPainted(false);    
        btn.setFont(btn.getFont().deriveFont(Font.BOLD, 14f));    
            
        panel.add(btn);    
        return this;    
    }

    @Override    
    public void show() {    
        // Startet die UI im Swing Event Dispatch Thread (obligatorisch)    
        SwingUtilities.invokeLater(() -> frame.setVisible(true));    
    }    
}

// 5) App Start    
public class MainApp {

    public static void main(String[] args) {  
    
        // Wir erstellen das Domänenobjekt    
        LoginSession session = new LoginSession();    
            
        // Wir sagen dem Objekt: Hier ist eine UI (SwingCanvas), stell dich dar.    
        // Tell, Don't Ask in Reinform.    
        session.displayOn(new SwingLoginCanvas());    
    }    
}
```

**Zusammenfassung der Kernprinzipien im Code**

* **Kein GetAnemic Model:** Die LoginSession exponiert keine Getter. Das Domänenobjekt wird nicht 'ausgefragt'; sein interner Zustand (E-`MailPasswort`) bleibt gekapselt.  
* **Inversion of Control (Push-Prinzip):** Das Domänenobjekt (Model) instruiert die View (`canvas.addField()`) aktiv über seine Darstellung (Push-Prinzip). Die UI agiert als passiver Empfänger dieser Befehle.  
* **Starke Verhaltenskohäsion:** Die gesamte Interaktionslogik (z.B. der `ActionListener` für den 'Einloggen'-Knopf) ist *innerhalb* der `LoginSession` gekapselt (`this.authenticate()`). Das Objekt trägt die alleinige Verantwortung für sein Verhalten.

### **4.4 Anwendung mit UI of Objects (mit React)**

## **5. Fazit**

**Object-Oriented UX** stellt eine bedeutende Verschiebung in der Art und Weise dar, wie das Design von Benutzeroberflächen angegangen wird. Durch die Konzentration auf Objekte anstelle von Aufgaben stimmt OOUX stärker mit den mentalen Modellen der Benutzer überein, fördert die Konsistenz und verbessert die Skalierbarkeit.

**Object-Oriented UI** ist dabei der starke Partner, der diese abstrakten Konzepte in greifbare, interaktive Elemente übersetzt. Durch die Nutzung von Texturen, Dimensionen und interaktiven Elementen macht OOUI digitale Schnittstellen ansprechender.

Designer müssen sich der Herausforderungen bewusst sein – von Barrierefreiheit bis zur Balance zwischen Ästhetik und Funktionalität. Doch mit Blick auf die Zukunft wird die Partnerschaft zwischen OOUX, OOUI und OOP unerlässlich sein, um Schnittstellen zu schaffen, die nicht nur funktional, sondern auch zutiefst ansprechend und benutzerzentriert sind.

Auf der Code-Ebene erfordert dies ein Umdenken: Weg von anämischen Datencontainern, hin zum **UI of Objects**, welche als **Immutable-Proxies** fungieren. Diese konsequente Umsetzung von OOP manifestiert sich in Architekturen mit maximaler Kohäsion und minimaler semantischer Kopplung.

**Die Objekte schweigen nicht mehr – sie sprechen zum User.**

## **6. Quellen**

* Alen Key: [Definition of Object-Oriented-Programming (2003)](https:www.quora.comWhat-does-Alan-Kay-mean-when-he-said-OOP-to-me-means-only-messaging-local-retention-and-protection-and-hiding-of-state-process-and-extreme-late-binding-of-all-things-It-can-be-done-in-Smalltalk-and-in-LISP)
* Max Stepanov: [Object-Oriented UX and Object-Oriented UI (2024)]  
* Robert Bräutigam: [Single Responsibility Principle (2018)](https:speakerdeck.comrobertbraeutigamsingle-responsibility-principle)  
* Robert Bräutigam: [Object-Oriented Domain-Driven Design (2018)](https:speakerdeck.comrobertbraeutigamobject-oriented-domain-driven-design)  
* Andreas Wagner:  [The Mechanics of Good Object (2025)]
