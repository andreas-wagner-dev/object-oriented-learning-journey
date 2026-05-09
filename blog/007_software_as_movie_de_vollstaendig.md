
**Teaser Statement**
> KI schreibt Code heute in Sekunden. Aber wer versteht ihn morgen noch?  
> Warum wir aufhören sollten, Schichten zu bauen, und anfangen müssen, Drehbücher zu schreiben. 
 
 Tags: 🎬💻 #SoftwareArchitecture #CleanCode #AI #Storytelling #DesignPattern 


# **🎞️ Software als Spielfilm-Drehbuch**

Das Aufkommen von Künstlicher Intelligenz wie ChatGPT, Claude oder GitHub Copilot hat die Softwareentwicklung grundlegend verändert. Die alte Faustregel „zehnmal mehr lesen als schreiben" gilt heute stärker denn je – nur dass wir inzwischen nicht mehr unseren eigenen Code lesen, sondern vor allem den von Maschinen generierten. Um diese Flut überhaupt noch menschlich erfassen und beherrschen zu können, braucht es eine neue Perspektive auf Softwarearchitektur.

Computer verarbeiten beliebige Datenstrukturen. Menschen dagegen verstehen die Welt über Geschichten. Biologisch gesehen ist unser Gehirn darauf programmiert, Informationen über Ursache, Wirkung und **Emotionen** zu speichern. Wo nackte Daten eher kognitive Kälte erzeugen und schnell in Vergessenheit geraten, erzeugen gute Geschichten Resonanz. Sie wecken Neugier, schaffen Empathie für die fachlichen Abläufe und motivieren zum Weiterlesen. Deshalb liegt es nahe, Code wie ein Filmdrehbuch zu gestalten.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_01_code_like_movie.png)

Im Gegensatz zu Ansätzen, die Software als technische Schichtenlandschaft aus zustandslosen Services mit passiven Datencontainern betrachten, inszeniert die Spielfilmphilosophie ein objektorientiertes System als lebendiges Ensemble intelligenter Akteure. Diese Akteure treten in Szenen auf, handeln in Akten und verleihen der logischen Abfolge eine menschlich greifbare Dramaturgie. Die Geschichte wird so nicht mehr bloß ausgeführt, sondern durch das Verhalten der Akteure emotional erlebbar gemacht.

## 1. **Drehbuchs als Bauanleitung**

Die Modellierung eines objektorientierten Systems erfolgt über drei zentrale Graphen. In diesem Kontext übernimmt das Drehbuch die Rolle einer Bauanleitung: Es definiert den Rahmen, in dem die statische Struktur und der flüchtige Moment der Interaktion nahtlos ineinandergreifen.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_02_code_like_movie.png)

* **Der Konstruktions-Graph (Die Regie / Der Set-Aufbau):** Er beschreibt die Instanziierung und den Aufbau des Objektgerüsts durch den [Composition Root](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) (Wer besitzt wen?). Hier wird die „Bühnentechnik" installiert, bevor die erste Szene beginnt.
* **Der Kollaborations-Graph (Das Ensemble):** Er definiert die dauerhaften Beziehungen zwischen den Objekten (Wer kennt wen und kann miteinander kommunizieren?). Dies entspricht der festen Besetzung der Rollen und ihrer Vernetzung untereinander.
* **Der Aufruf-Graph (Die Handlung):** Er bildet die dynamischen Interaktionen zur Laufzeit ab  (Wer ruft gerade welche Methode auf?) – den eigentlichen Dialog zwischen den Akteuren während der Ausführung einer Szene.

**Die zentralen Regieanweisungen**

Das **Law of Demeter** und das Prinzip **Tell Don't Ask** fungieren als zentrale Regieanweisungen. Das Law of Demeter verhindert transitive Kopplung, indem ein Objekt ausschließlich mit seinen direkten Kollaborationspartnern interagiert. Das Tell‑Don't‑Ask‑Prinzip stellt sicher, dass Verhalten dort ausgeführt wird, wo es semantisch hingehört, anstatt internen Zustand nach außen zu tragen. So entsteht eine lebendige, verhaltensorientierte Struktur, die uns emotional berührt, anstelle eines prozeduralen Abfrageskripts, das schnell monoton wirkt.

**Das Inhaltsverzeichnis des Drehbuchs**

Um einen fachlichen und progressiven Informationsfluss zu gewährleisten, muss das Inhaltsverzeichnis des Drehbuchs einen klaren Navigationspfad abbilden, der dem Leser hilft, die Bestandteile der Geschichte effizient zu lokalisieren. Hierfür gelten drei pragmatische Regeln:

1. **Klare Abhängigkeiten:** Pakete dürfen niemals von ihren Unterpaketen abhängen. Zirkelbezüge sind strikt verboten, um den gerichteten Informationsfluss zu wahren.
2. **Präzise Detailverfeinerung:** Unterpakete führen keine neuen Konzepte ein. Sie konkretisieren lediglich bereits bestehende Begriffe der übergeordneten Pakete.
3. **Lebendige Fachsprache:** Pakete spiegeln die Sprache der Domäne wider (Ubiquitous Language), nicht die technischen Begriffe der Umsetzer.

## 2. **🎬 Die cineastische Projektstruktur**

Die Struktur des Projekts ist die sichtbare Form des Drehbuchs. Sie trennt die fachliche Identität von der technischen Umsetzung. So wird die Inszenierung der Anwendung auf den ersten Blick verständlich. Wer das Manuskript öffnet, sieht keine technische Schablone, sondern das Drehbuch einer individuellen Geschichte, bereit für die Produktion in einem Filmstudio:

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_03_code_like_movie.png)

Egal ob Investor, Fachexperte, Architekt oder Entwickler: Niemand muss raten. Jeder liest sofort das Inhaltsverzeichnis einer Lebensgeschichte.

```
manuscript/
│
├─ directing/         ← REGIE (Das Steuerungssystem / Composition Root)
│
├─ character/         ← ENSEMBLE (Die Hauptdarsteller & Rollenprofile)
├─ outfit/            ← MASKE (Strukturelle Hüllen & Verkleidungen)
├─ wedding/           ← AKT 1 (Fachlicher Prozess: Zusammenführung)
├─ honeymoon/         ← AKT 2 (Fachlicher Prozess: Übergangsphase)
├─ birth/             ← AKT 3 (Fachlicher Prozess: Initialisierung)
├─ familylife/        ← AKT 4 (Fachlicher Prozess: Dauerzustand)
│
├─ backstage/         ← BÜHNENTECHNIK (Datenbanken, APIs, Infrastruktur)  
│  
├─ stage/             ← DIE BÜHNE (Benutzeroberflächen & Präsentation)
│
├─ Actor.java         ← AKTEUR (Zentrales Interface)
├─ Name.java          ← REQUISITEN (Präzise Fachwerte / Value Objects)
├─ Outfit.java        ← AUSSTATTUNG (Basis-Decorator)
├─ Scene.java         ← SZENE (Das Interaktionsprotokoll)
├─ Media.java         ← Medium (Der Informationsträger)
└─ Movie.java         ← DREHBUCH (Der rote Faden / Einstiegspunkt)
```

**Bestandteile und Abhängigkeiten der Inszenierung**

Um die Geschichte ohne Logikfehler zu erzählen, folgen die Pakete einer strikten Hierarchie:

**Ideas & Concepts (Domain):** Das begriffliche Fundament. Hier stehen die zentralen Ideen und fachlichen Regeln. Diese Ebene ist völlig autonom und hängt von nichts ab. Alle anderen Pakete bauen auf ihr auf.

**Backstage (Infrastructure):** Die technische Infrastruktur. Hier liegen Werkzeuge, Datenbanken und APIs. Sie dient dem Ensemble und darf ausschließlich von der Domäne abhängen.

**Scenes (Details):** Die fachlichen Detail-Pakete. Sie füllen die abstrakten Domänenkonzepte mit Leben. Hier werden konkrete Abläufe und Interaktionen der Story ausgestaltet.

**Stage (UI):** Die sichtbare Oberfläche. Hier wird das Geschehen für das Publikum (die Nutzer) sichtbar gemacht. Die Bühne greift auf Domäne, Szenen und die Infrastruktur zu.

**Directing (Composition-Root):** Das Paket Directing fungiert als operative Regiezentrale. In einer Composition Root Klasse stellt sie den zentralen Einstiegspunkt `main()` bereit und orchestriert das Zusammenspiel aller Klassen. Hier wird über die Instanziierung der Objekte sowie alle zum Start erforderlichen Einstellungen entschieden. Die Regie bildet somit die einzige Instanz, die das gesamte Ensemble kennt und die verschiedenen Bestandteile zu einer fertigen Inszenierung zusammenführt. Kein anderes Paket darf von der Regie abhängen.

## **3. Das Ensemble und die Requisiten (Die Domänen-Ebene)**

Das Herzstück der Struktur bildet die fachliche Identität auf Ebene Null. Hier werden die Hauptcharaktere als Interfaces, der Handlungsrahmen als Szenen sowie ihre Attribute als Requisiten (Value Objects) definiert. Diese Ebene ist frei von technischem Rauschen und macht die Essenz des Systems sofort greifbar.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_04_code_like_movie.png)
```
manuscript/
│
├─ Actor.java         ← Das universelle Akteur-Interface
├─ Name.java          ← Das Requisit: Präziser Fachwert
├─ Outfit.java        ← Die Ausstattung: Basis-Decorator
├─ Scene.java         ← Das Interaktionsprotokoll
├─ Media.java         ← Medium: Der Informationsträger
└─ Movie.java         ← Der rote Faden / Einstiegspunkt
```

### **3.1. Der Akteur (Actor) – Das zentrale Interface**

In der Welt des Films gibt es Stars, Statisten und Komparsen. Im Drehbuch spielen alle dieselbe Rolle: Sie sind Akteure, die etwas performen und miteinander kommunizieren. Diese fachliche Einheitlichkeit wird im Code über ein zentrales Interface ausgedrückt:

```java
public interface Actor {
    
    /** Name Requisite. */
    Name name();
    
    /** The call to action. */
    void perform();

    /**
     * The communication channel. 
     * @param media to speak on.
     */
    void speak(Media media);
}
```

Jeder `Actor` kann agieren (`perform`), kommunizieren (`speak`) und besitzt einen Namen (`name`). Diese drei Kernfähigkeiten sind universell – egal ob Hauptdarsteller, Statist oder Komparse. Die Ausprägung ihrer Rolle erfolgt über konkrete Implementierungen, nicht über technische Kategorisierungen.

### **3.2 Das Requisit (Name) – Der präzise Fachwert**

Im Film haben Requisiten eine klare Bedeutung: Sie sind keine austauschbaren Objekte. Ein Ehering ist nicht „irgendein Gegenstand", sondern ein Symbol mit emotionaler und funktionaler Bedeutung.

So verhält es sich auch mit fachlichen Werten im Code. Ein Name ist kein primitiver `String`, sondern ein präziser Fachwert, der semantische Validierung und Identität trägt:

```java
public class Name {

    private String value;

    public Name(String value) {
        if (value == null || value.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty!");
        }
        this.value = value;
    }

    @Override
    public String toString() {
        return value;
    }

    public String value() {
        return value;
    }
}
```

Durch diese Kapselung erhält der Wert eine fachliche Identität. Der Konstruktor validiert bereits beim Entstehen, dass der Name nicht leer sein kann. Diese defensive Strategie verhindert fehlerhafte Zustände, noch bevor sie sich im System ausbreiten können. Was technisch wie ein Wrapper aussieht, ist funktional ein Garant für Integrität und Ausdrucksstärke.

### **3.3 Die Ausstattung (Outfit) – Der Basis-Decorator**

Akteure tragen im Film verschiedene Kostüme. Ein Anzug verleiht Eleganz, eine Uniform Autorität. Im Code erfüllt das **Decorator-Pattern** denselben Zweck: Es erweitert Akteure mit zusätzlichem Verhalten, ohne ihre Identität zu verändern.

```java
/**
 * Abstract outfit to decorate an actor.
 */
public abstract class Outfit implements Actor {
    
    protected final Actor decoratedActor;
    
    protected Outfit(Actor actor) {
        this.decoratedActor = actor;
    }
    
    @Override
    public Name name() {
        return decoratedActor.name();
    }
    
    @Override
    public void perform() {
        decoratedActor.perform();
    }
}
```

Die Basisklasse `Outfit` ist ein abstrakter Decorator. Sie hält eine Referenz auf den dekorierten Akteur und delegiert die zentralen Methoden `name()` und `perform()` direkt an ihn weiter. Dies garantiert, dass die Grundidentität des Akteurs unverändert bleibt. Konkrete Subklassen können spezifisches Verhalten vor, nach oder anstelle der Delegation hinzufügen. So wird ein Akteur mit zusätzlichen Fähigkeiten ausgestattet, ohne seinen Kern zu verändern. Die `speak(Media)` Methode wird bewusst nicht automatisch delegiert, da jedes konkrete `Outfit` selbst entscheiden soll, wie es kommuniziert.

### **3.4 Die Szene (Scene) – Das Interaktionsprotokoll**

Eine Szene ist der kleinste kohärente Handlungsbaustein eines Films. Sie beschreibt eine in sich geschlossene Interaktion zwischen Akteuren an einem Ort und zu einer Zeit.

```java
/**
 * Scene.
 */
@FunctionalInterface  
public interface Scene {
    
    void execute();  
}
```

Dieses Interface reduziert die Szene auf ihre Essenz: die Ausführung einer Handlung. Dank der Annotation `@FunctionalInterface` kann es auch als Lambda oder Methodenreferenz eingesetzt werden. Eine Szene sagt nicht, *wie* die Handlung abläuft, sondern nur, *dass* sie ausgeführt wird. Alle Details bleiben der konkreten Implementierung vorbehalten.

### **3.5 Das Medium (Media) – Der Informationsträger**

Kommunikation in Filmen findet über verschiedene Medien statt: Sprache, Gesten, Briefe, Telefonate. Im Code repräsentiert `Media` den Träger dieser Kommunikation.

```java

public interface Media {

    Media with(String name, String value);

    Media with(String name, boolean value);

    public String content(String name);
}
```

```java
public abstract class Default implements Media {
    
    protected final Map<String, Object> content = new HashMap<>(0);

    @Override
    public Media with(String name, String value) {
        content.put(name, value);
        return this;
    }
    
    @Override
    public Media with(String name, boolean value) {
        content.put(name, value);
        return this;
    }
    
    public String content(String name) {
        return content.getOrDefault(name, "").toString();
    }
    
    @Override
    public String toString() {
        return content.toString();
    }
}
```

```java
public final class Air extends Media.Default {

}
```

Das Interface `Media` definiert eine fluent API, um Inhalte zu befüllen. Die Klasse `Media.Default` bietet eine Standard-Implementierung, die Inhalte in einer `Map` speichert. `Air` ist ein konkretes Medium für die unsichtbare Übertragung – etwa das gesprochene Wort in die Luft. Weitere Spezialisierungen können für unterschiedliche Kontexte hinzugefügt werden, etwa `Email`, `RecordMedia` oder `VideoCanvas`.

### **3.6 Der Film (Movie) – Der rote Faden**

Der Film ist das Gesamtkunstwerk. Er orchestriert die Szenen und definiert den Ablauf der Geschichte.

```java
public interface Movie {
    void play();  
}
```

Das `Movie`-Interface ist bewusst minimalistisch: Eine einzige Methode `play()` startet die Inszenierung. Die konkrete Reihenfolge der Szenen, ihre Verkettung und Logik wird in einer Implementierung wie `AnalogMovie` festgelegt. So bleibt die fachliche Abstraktion („spiele den Film ab") klar von der technischen Orchestrierung getrennt.

## **4. Das Ensemble (Die Charaktere)**

Das Herzstück jeder Geschichte sind die Charaktere. Sie tragen die Handlung, repräsentieren fachliche Rollen und agieren als autonome Akteure mit klarem Verhalten.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_05_code_like_movie.png)

```
manuscript/character/
│
├─ Bride.java         ← Die Braut (Zentrale Protagonistin)
├─ Groom.java         ← Der Bräutigam (Zentraler Protagonist)
├─ Newborn.java       ← Das Neugeborene (Neuer Charakter im Familienleben)
├─ Guest.java         ← Der Gast (Einzelner Hochzeitsgast)
├─ Guests.java        ← Die Gäste (Gästegruppe als Ensemble)
├─ Musician.java      ← Der Musiker (Einzelner Bandmitglied)
└─ Band.java          ← Die Band (Musikgruppe als Ensemble)
```

Alle Charaktere implementieren das zentrale `Actor`-Interface und repräsentieren ihre fachliche Rolle durch spezifisches Verhalten. Im Gegensatz zu passiven Datencontainern entscheiden sie selbst über ihre Aktionen und Kommunikation.

### **4.1 Die Braut (Bride)**

Die Braut ist eine der zentralen Protagonistinnen der Hochzeitsgeschichte. Sie kann ihre Gelübde vortragen, Ringe tauschen und ihre Zustimmung erklären.

```java

public class Bride implements Actor {

    private final Name name;
    private boolean hasRing = false;

    public Bride(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() {
        return name;
    }

    @Override
    public void perform() {
        System.out.println("💍 " + name + " recites her wedding vows..");
    }

    public void giveRing(Media ringBasket) {
        ringBasket.with("groom", "Wedding-Ring");
        System.out.println("💍 " + name + " hands over " + ringBasket + ".");
    }

    public void receiveRing(Media ringBasket) {
        if (hasRing) {
            throw new IllegalStateException("The bride is already wearing a ring!");
        }
        System.out.println("💍 " + name + " take the " + ringBasket + ".");
        hasRing = true;
    }

    public void speak(Media media) {
        media.with("role", "Bride").with("name", name.value()).with("text", "Yes, I will...").with("hasRing", hasRing);
    }
}
```

Die Braut verwaltet ihren eigenen Zustand (`hasRing`) und trifft Entscheidungen selbstständig. Die Methode `receiveRing` prüft defensiv, ob bereits ein Ring vorhanden ist. So bleibt die Geschäftslogik dort, wo sie semantisch hingehört: bei der Braut selbst, nicht in einem externen Service.

### **4.2 Der Bräutigam (Groom)**

Der Bräutigam spielt die komplementäre Rolle zur Braut. Seine Implementierung folgt demselben Prinzip: Er ist ein autonomer Akteur mit eigener Identität und eigenem Verhalten.

```java

public class Groom implements Actor {

    private final Name name;
    private boolean hasRing;

    public Groom(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() {
        return name;
    }

    @Override
    public void perform() {
        System.out.println("💍 " + name + " exchanges his wedding vows.");
    }

    public void giveRing(Media ringBasket) {
        ringBasket.with("bride", "Wedding-Ring");
        System.out.println("💍 " + name + " presents the " + ringBasket + ".");
    }

    public void receiveRing(Media ringBasket) {
        if (hasRing) {
            throw new IllegalStateException("The groom is already wearing a ring!");
        }
        System.out.println("💍 " + name + " take the " + ringBasket + ".");
        hasRing = true;
    }
    
    public void speak(Media media) {
        media.with("role", "Groom")
        .with("name", name.value())
        .with("text", "Yes, I will...")
        .with("hasRing", hasRing);
    }

}
```

Bride und Groom sind strukturell ähnlich, aber nicht identisch. Jeder Charakter hat seine eigene Perspektive auf das Geschehen. Diese Duplizierung ist bewusst: Sie bewahrt die Semantik und erlaubt es beiden Rollen, unabhängig voneinander zu evolvieren, ohne dass Änderungen an der einen automatisch die andere betreffen.

### **4.3 Das Neugeborene (Newborn)**

Mit der Geburt betritt ein neuer Charakter die Bühne: das Neugeborene. Es repräsentiert die nächste Generation und erweitert das Ensemble.

```java

public class Newborn implements Actor {
    
    private final Name name;
    
    public Newborn(String name) {
        this.name = new Name(name);
    }
    
    @Override
    public Name name() {
        return name;
    }
    
    @Override
    public void perform() {
        System.out.println("👶 " + name + " smiles cheerfully.");
    }

     /** Baby cry.*/
    public void speak(Media media) {
        String value = name.value();
        media.with("role", "Newborn")
            .with("name", value)
            .with("text", "😭 " + name + " cries loudly!");
    }
}
```

Das Neugeborene zeigt, wie einfach sich neue Charaktere hinzufügen lassen. Es implementiert `Actor` und definiert sein eigenes Verhalten. Keine zentrale Service-Schicht muss angepasst werden. Der Charakter ist sofort spielfähig.

### **4.4 Der Gast (Guest)**

Gäste sind Nebencharaktere, die die Hochzeit bereichern. Sie können applaudieren und gratulieren.

```java

public class Guest implements Actor {

    private final Name name;

    public Guest(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() {
        return name;
    }

    @Override
    public void perform() {
        System.out.println("🧍‍♂️" + name + " lächelt fröhlich.");
    }

    public void clap() {
        Media.Air air = new Media.Air();
        speak(air);
        System.out.println(air.toString());
    }

    public void speak(Media media) {
        media.with("role", "Guest")
        .with("name", name.value())
        .with("text", "💬 " + name + " congratulations!")
        .with("action", "👏 " + name + " applaud!");
    }
}
```

Die Methode `clap()` zeigt, wie ein Gast seine Kommunikation selbst inszeniert: Er erstellt ein `Air`-Medium, spricht hinein und gibt es aus. Dies ist ein Beispiel für autonomes Verhalten, das keine externe Koordination benötigt.

### **4.5 Die Gästegruppe (Guests)**

Mehrere Gäste bilden zusammen eine Gruppe. Diese wird als iterierbare Sammlung modelliert, sodass alle Gäste einfach durchlaufen werden können.

```java
public class Guests implements Iterable<Guest> {

    private List<Guest> guests;

    public Guests(List<Guest> guests) {
        this.guests = new ArrayList<>(guests);
    }

    @Override
    public Iterator<Guest> iterator() {
        return guests.iterator();
    }

    public Guests add(Guest guest) {
        List<Guest> newList = new ArrayList<>(guests);
        newList.add(guest);
        return new Guests(newList);
    }
}
```

Die Klasse `Guests` ist bewusst immutable gestaltet. Die Methode `add` erzeugt eine neue Instanz mit dem hinzugefügten Gast, statt die bestehende Liste zu verändern. So bleibt die Datenintegrität gewahrt und Nebeneffekte werden vermieden.

### **4.6 Der Musiker (Musician)**

Musiker spielen eine wichtige Rolle bei der Hochzeit. Sie sorgen für musikalische Untermalung.

```java
public class Musician implements Actor {
    
    private final Name name;

    public Musician(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() {
        return name;
    }

    @Override
    public void perform() {
        System.out.println("🧍‍ " + name + " play music.");
    }

    @Override
    public void speak(Media media) {
        media.with("role", "Musician")
        .with("name", name.value())
        .with("text", "🎤💬 " + name + " singing a song!");
    }
}
```

Auch der Musiker ist ein vollwertiger `Actor`. Seine Rolle ist klar definiert: Musik spielen und singen. Diese Klarheit macht den Code lesbar und wartbar.

### **4.7 Die Band (Band)**

Mehrere Musiker bilden zusammen eine Band. Diese Gruppe hat einen eigenen Namen und kann iteriert werden.

```java
public class Band implements Iterable<Musician> {

    private List<Musician> group;

    private final Name name;

    public Band(String name, List<Musician> group) {
        this.name = new Name(name);
        this.group = new ArrayList<>(group);
    }

    public Name name() {
        return name;
    }

    @Override
    public Iterator<Musician> iterator() {
        return group.iterator();
    }

    public Band add(Musician guest) {
        List<Musician> newList = new ArrayList<>(group);
        newList.add(guest);
        return new Band(name.value(), newList);
    }
}
```

Die `Band` trägt selbst einen Namen und gruppiert Musiker. Wie bei `Guests` wird auch hier die Immutability gewahrt: `add` erzeugt eine neue Band-Instanz statt die bestehende zu verändern.

## **5. Die Maske (Die strukturellen Hüllen)**

In der Filmwelt verwandelt die Maske Schauspieler in ihre Rollen. Ein Anzug macht aus einem Darsteller einen Gentleman, ein Kostüm verleiht ihm Autorität oder Eleganz. Im Code erfüllt das **Decorator-Pattern** denselben Zweck: Es stattet Akteure mit zusätzlichem Verhalten aus, ohne ihre Kernidentität zu verändern.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_06_code_like_movie.png)

```
manuscript/outfit/
│
├─ ActorInSuite.java          ← Akteur im Anzug (Visuelle Ausstattung)
├─ BrideWithArchive.java      ← Braut mit Datenbankzugriff
├─ BrideWithMailbox.java      ← Braut mit E-Mail-Funktionalität
├─ GroomWithCard.java         ← Bräutigam mit Zahlungskarte
└─ GroomWithPhone.java        ← Bräutigam mit Telefon
```

Jedes `Outfit` erweitert einen Akteur mit spezifischem Verhalten. Dies geschieht durch Komposition statt Vererbung: Ein dekorierter Akteur delegiert die Kernfunktionalität an den umhüllten Akteur und fügt nur selektiv neue Fähigkeiten hinzu.

### **5.1 Akteur im Anzug (ActorInSuite)**

Der Anzug ist eine rein visuelle Ausstattung. Er verändert, wie der Akteur auf die Bühne tritt, ohne seine Fähigkeiten zu ändern.

```java
public class ActorInSuite extends Outfit {
    
    public ActorInSuite(Actor actor) {
        super(actor);
    }
    
    @Override
    public void perform() {
        System.out.println("🤵 " + name() + " steps onto the stage in a wedding suit.");
        decoratedActor.perform();
    }

    @Override
    public void speak(Media media) {
        decoratedActor.speak(media);
    }
}
```

Der `ActorInSuite` überschreibt `perform()` und fügt einen visuellen Hinweis hinzu („betritt die Bühne im Hochzeitsanzug"), bevor er die ursprüngliche Aktion des Akteurs ausführt. Die `speak`-Methode wird direkt delegiert. Dies zeigt, wie gezielt Verhalten erweitert wird, ohne die gesamte Funktionalität neu zu definieren.

### **5.2 Braut mit Datenbankzugriff (BrideWithArchive)**

Die Braut erhält durch diesen Decorator die Fähigkeit, ihre Daten in einer Datenbank zu speichern. Dies ist ein Beispiel für die Trennung von fachlicher Logik (Braut) und technischer Infrastruktur (Datenbank).

```java
public class BrideWithArchive extends Outfit {

    private Archive arhive;

    public BrideWithArchive(Bride bride, Archive arhive) {
        super(bride);
        this.arhive = arhive;
    }
    
    @Override
    public void speak(Media spaek) {
        decoratedActor.speak(spaek);
    }
    
    @Override
    public void perform() {
        decoratedActor.perform();
        // Simulate database storage
        // The Archive hands the Actor a blank sheet (RecordMedia).
        RecordMedia record = new RecordMedia();
        // The actor "speaks" his data onto the sheet.
        speak(record);
        arhive.store(record);
        System.out.println("💾 " + name() + " was saved in the database.");
    }

}
```

Die `BrideWithArchive` führt nach der ursprünglichen Aktion (`decoratedActor.perform()`) zusätzlich eine Datenbankoperation durch. Der Akteur „spricht" seine Daten in ein `RecordMedia`-Objekt, das dann im `Archive` gespeichert wird. Diese Choreographie zeigt, wie Infrastruktur nahtlos in die fachliche Handlung eingebettet wird, ohne dass die Kernlogik der Braut selbst davon wissen muss.

### **5.3 Braut mit E-Mail-Funktionalität (BrideWithMailbox)**

Dieser Decorator stattet die Braut mit der Fähigkeit aus, Einladungen per E-Mail zu versenden.

```java
public class BrideWithMailbox extends Outfit {

    private EmailBox emailBox;

    public BrideWithMailbox(Bride bride, EmailBox emailBox) {
        super(bride);
        this.emailBox = emailBox;
    }
    
    @Override
    public void speak(Media spaek) {
        decoratedActor.speak(spaek);
    }
    
    public void invite(Actor actor) {
        Email mail = new Email(name().value(), actor.name().value(), "Invitation to Wedding..."); 
        emailBox.put(mail);
    }
}
```

Die Methode `invite` ist neu und nutzt die `EmailBox`, um eine Einladung zu versenden. Die Braut selbst bleibt unverändert; nur die dekorierte Version erhält diese zusätzliche Fähigkeit. Dies zeigt die Stärke des Decorator-Patterns: Verhalten kann selektiv und kontextbezogen hinzugefügt werden.

### **5.4 Bräutigam mit Zahlungskarte (GroomWithCard)**

Der Bräutigam erhält durch diesen Decorator die Fähigkeit, Zahlungen zu tätigen. Er implementiert zusätzlich das Interface `Payer`, um die Zahlungsfunktion explizit zu machen.

```java
public class GroomWithCard extends Outfit implements Payer {

    private StripeApi stripeApi;

    public GroomWithCard(Actor actor, StripeApi stripeApi) {
        super(actor);
        this.stripeApi = stripeApi;
    }

    @Override
    public void speak(Media spaek) {
        decoratedActor.speak(spaek);
    }

    @Override
    public void pay(double amount, String toAccountId) {
        System.out.println("💰 " + name() + " Initialize payment of €" + amount);
        String groomAccountId = "stripe_account_" + name().value().toLowerCase();
        stripeApi.execute(new Transaction(groomAccountId, toAccountId), amount);
    }
}
```

Die `pay`-Methode übersetzt die fachliche Aktion („der Bräutigam zahlt") in eine technische API-Operation. Der Decorator fungiert hier als **Adapter**: Er kapselt die Details der Stripe-API und macht sie über eine fachliche Schnittstelle zugänglich.

### **5.5 Bräutigam mit Telefon (GroomWithPhone)**

Dieser Decorator stattet den Bräutigam mit einem Telefon aus, über das er Anrufe tätigen und empfangen kann.

```java
public class GroomWithPhone extends Outfit implements Headset {

    private Phone phone;

    public GroomWithPhone(Actor actor, Phone phone) {
        super(actor);
        this.phone = phone;
        this.phone.receive(this);
    }

    public void call(String message) {
        System.out.println("📞 Calling the Band 🎼...");
        phone.publish(message);
    }

    @Override
    public void accept(String message) {
        System.out.println("📞 Confirmation from the band 🎹: " + message);
    }

    @Override
    public void speak(Media spaek) {
        decoratedActor.speak(spaek);
    }

}
```

Durch die Implementierung des `Headset`-Interfaces kann der Bräutigam als Empfänger von Nachrichten registriert werden. Die `call`-Methode sendet eine Nachricht über das `Phone`, während `accept` eingehende Antworten verarbeitet. Dies ist ein Beispiel für asynchrone Kommunikation, die nahtlos in das Objektmodell integriert ist.

## **6. Die Bühnentechnik (Die Infrastruktur-Ebene)**

Während die Akteure und Szenen die fachliche Essenz der Geschichte tragen, arbeitet im Hintergrund die Bühnentechnik. Sie sorgt dafür, dass Licht, Ton und Kulissen funktionieren – bleibt aber für das Publikum unsichtbar. Im Code entspricht dies der Infrastruktur-Ebene: Datenbanken, APIs, Kommunikationskanäle und Zahlungsdienste.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_07_code_like_movie.png)

```
manuscript/backstage/
│
├─ Archive.java              ← Schnittstelle zum Archiv (Datenbank)
├─ Channel.java              ← Kommunikationskanal (Publish/Subscribe)
├─ PostBox.java              ← Schnittstelle für Nachrichtenversand
├─ Payer.java                ← Schnittstelle für Zahlungsabwicklung
│
├─ archive/
│  ├─ DatabaseArchive.java   ← Konkrete Datenbankverbindung
│  └─ RecordMedia.java       ← Datenträger für Datenbankeinträge
│
├─ email/
│  ├─ Email.java             ← E-Mail-Datenobjekt
│  └─ EmailBox.java          ← E-Mail-Versandadapter
│
├─ payment/
│  ├─ StripeApi.java         ← Adapter zur Stripe-Zahlungs-API
│  └─ Transaction.java       ← Transaktionsobjekt für Zahlungen
│
└─ phone/
   ├─ Phone.java             ← Telefon-Kommunikationsadapter
   └─ Headset.java           ← Interface für Nachrichtenempfänger
```

Die Bühnentechnik ist strikt von der Domäne getrennt. Alle Infrastrukturkomponenten sind über Interfaces abstrahiert, sodass die fachliche Logik keine direkten Abhängigkeiten zu technischen Implementierungen hat. Dies ermöglicht es, Datenbanken, APIs oder Kommunikationswege auszutauschen, ohne die Kerngeschichte zu verändern.

### **6.1 Das Archiv (Archive & DatabaseArchive)**

Das Archiv speichert Daten dauerhaft. Im Film wäre das die Filmrolle, die später entwickelt wird. Im Code ist es die Datenbank.

**Interface:**
```java
public interface Archive {
    void store(RecordMedia media);
}
```

**Implementierung:**
```java
public class DatabaseArchive implements Archive {
    
    private final String connectionString;
    
    public DatabaseArchive(String connectionString) {
        this.connectionString = connectionString;
        System.out.println("💾 Connect database: " + connectionString);
    }
    
    @Override
    public void store(RecordMedia media) {
        // Simulate database operation
        System.out.println("💾 Storing in database: " + media.toRecord());
    }
}
```

Das Interface `Archive` definiert die fachliche Aktion: Speichern. Die konkrete Implementierung `DatabaseArchive` übernimmt die technische Umsetzung. So bleibt die Domäne unabhängig von der Wahl der Datenbank.

**Das Aufzeichnungsmedium (RecordMedia):**
```java
public class RecordMedia extends Media.Default {
    
    public String toRecord() {
        return content.toString();
    }
}
```

`RecordMedia` ist ein spezialisiertes Medium für Datenbankeinträge. Es erbt von `Media.Default` und fügt eine Methode hinzu, um Inhalte in ein speicherbares Format zu bringen.

### **6.2 Die Post (PostBox, Email & EmailBox)**

Die Post ist ein Kommunikationskanal. Im Film wären das Briefe, im Code E-Mails.

**Interface:**
```java
public interface PostBox<T> {
    void put(T content);
}
```

Das generische Interface `PostBox<T>` abstrahiert den Versandmechanismus. Es kennt nur die Aktion „etwas ablegen", nicht die Art des Inhalts.

**E-Mail-Datenobjekt:**
```java
public class Email {

    private String from;
    private String to;
    private String content;

    public Email(String from, String to, String content) {
        this.from = from;
        this.to = to;
        this.content = content;
    }

    public String from() {
        return from;
    }

    public String to() {
        return to;
    }

    public String content() {
        return content;
    }

    @Override
    public String toString() {
        return "From: " + from + ", To: " + to + ", Text: " + content;
    }
}
```

`Email` ist ein simples Datenobjekt, das die wesentlichen Informationen einer Nachricht kapselt. Es folgt dem Prinzip der **Single Responsibility**: Es repräsentiert eine E-Mail, ohne zu wissen, wie sie versendet wird.

**E-Mail-Versandadapter:**
```java
public class EmailBox implements PostBox<Email> {

    public EmailBox(String serverAddress) {
        System.out.println("📫 Connect to mail server: " + serverAddress);
    }

    @Override
    public void put(Email mail) {
        // simulate send email
        System.out.println("📧 Send mail: " + mail.toString());
    }

}
```

Die `EmailBox` implementiert `PostBox<Email>` und simuliert den Versand über einen Mail-Server. In einer realen Anwendung würde hier der Aufruf einer Mail-API stehen. Die Abstraktion über `PostBox` ermöglicht es, den Versandmechanismus auszutauschen, ohne die Domäne zu berühren.

### **6.3 Die Zahlung (Payer, Transaction & StripeApi)**

Zahlungen sind ein weiteres Beispiel für Infrastruktur. Im Film wäre das die Kasse, im Code die Zahlungs-API.

**Interface:**
```java
public interface Payer {
    void pay(double amount, String toAccountId);
}
```

Das `Payer`-Interface abstrahiert die Zahlungsfunktion. Es kennt nur den fachlichen Auftrag: Einen Betrag an ein Konto überweisen.

**Transaktionsobjekt:**
```java
public class Transaction extends Media.Default {
    
    private String fromAccount;
    private String toAccount;

    public Transaction(String fromAccount, String toAccount) {
        validate(fromAccount);
        validate(toAccount);
        this.fromAccount = fromAccount;
        this.toAccount = toAccount;
    }

    private void validate(String accountId) {
        if (accountId == null || accountId.trim().isEmpty()) {
            throw new IllegalArgumentException("Account-Id must not be null or empty!");
        }
    }

    public String from() {
        return fromAccount;
    }

    public String to() {
        return toAccount;
    }
}
```

`Transaction` ist ein Medium, das die Details einer Zahlung trägt. Es validiert defensiv, dass Konten-IDs nicht leer sein dürfen. Diese Validierung geschieht bereits beim Entstehen des Objekts, was spätere Fehler verhindert.

**Stripe-API-Adapter:**
```java
public class StripeApi {

    public StripeApi() {
        System.out.println("💳 Connect to Stripe API...");
    }

    public void execute(Transaction transaction, double amount) {
        // Simulate API call
        System.out.println("💳 Payment of €" + amount + " from " + transaction.from() + " to " + transaction.to() + " executed via Stripe.");
    }
}
```

Die `StripeApi` simuliert die Verbindung zu einer Zahlungs-API. Sie erhält eine `Transaction` und einen Betrag und führt die Zahlung aus. In einer realen Anwendung würde hier der HTTP-Call zur Stripe-API stehen.

### **6.4 Das Telefon (Phone, Headset & Channel)**

Das Telefon ermöglicht asynchrone Kommunikation zwischen Akteuren. Es folgt dem **Observer-Pattern**: Nachrichten werden publiziert, und registrierte Empfänger werden benachrichtigt.

**Interface für Empfänger:**
```java
public interface Headset {
    void accept(String message);
}
```

Das `Headset`-Interface definiert einen Empfänger, der Nachrichten annehmen kann.

**Kommunikationskanal:**
```java
public interface Channel {
    void subscribe(Headset headset);
    void publish(String message);
}
```

Das `Channel`-Interface abstrahiert einen Publish/Subscribe-Mechanismus. Headsets können sich registrieren und werden bei neuen Nachrichten benachrichtigt.

**Telefon-Implementierung:**
```java
public class Phone implements Channel {

    private final String phoneNumber;
    private final List<Headset> subscribers = new ArrayList<>();

    public Phone(String phoneNumber) {
        this.phoneNumber = phoneNumber;
        System.out.println("📞 Phone ready: " + phoneNumber);
    }

    public void receive(Headset headset) {
        subscribers.add(headset);
        System.out.println("📞 Headset registered for phone: " + phoneNumber);
    }

    @Override
    public void subscribe(Headset headset) {
        receive(headset);
    }

    @Override
    public void publish(String message) {
        System.out.println("📞 Sending message: " + message);
        for (Headset headset : subscribers) {
            headset.accept("Message received: " + message);
        }
    }
}
```

Das `Phone` verwaltet eine Liste von `Headset`-Empfängern. Beim Publizieren einer Nachricht werden alle registrierten Empfänger benachrichtigt. Dies zeigt, wie asynchrone Kommunikation objektorientiert modelliert werden kann, ohne auf Message-Broker oder Event-Systeme angewiesen zu sein.

## **7. Die Akte und Szenen (Die fachlichen Prozesse)**

Ein Film ist in Akte unterteilt, die wiederum aus Szenen bestehen. Jeder Akt erzählt einen abgeschlossenen Teil der Geschichte. Im Code entsprechen Akte den fachlichen Prozessen, die in Szenen zerlegt sind.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_06_code_like_movie.png)
```
manuscript/
│
├─ wedding/           ← AKT 1: Die Hochzeit
│  ├─ WeddingCeremony.java        ← Hauptszene der Trauung
│  ├─ RingExchange.java           ← Atomare Szene: Ringtausch
│  ├─ CallTheBand.java            ← Szene: Band anrufen
│  ├─ InviteTheGuests.java        ← Szene: Gäste einladen
│  ├─ PayTheBand.java             ← Szene: Band bezahlen
│  ├─ LoveStoryFlashback.java     ← Szene: Video-Rückblick
│  ├─ RingBasket.java             ← Medium für Ringübergabe
│  ├─ Video.java                  ← Interface für Video-Dateien
│  ├─ VideoCanvas.java            ← Medium für Video-Wiedergabe
│  └─ video/
│     ├─ LazyVideo.java           ← Lazy-Loading-Video (Proxy)
│     └─ RealVideo.java           ← Tatsächliches Video
│
├─ honeymoon/         ← AKT 2: Die Hochzeitsreise
│  ├─ HotelCheckIn.java           ← Szene: Hotel-Check-in
│  └─ SunsetDinner.java           ← Szene: Abendessen bei Sonnenuntergang
│
├─ birth/             ← AKT 3: Die Geburt
│  ├─ BirthScene.java             ← Szene: Geburt des Kindes
│  └─ Hospital.java               ← Ort: Krankenhaus
│
└─ familylife/        ← AKT 4: Das Familienleben
   ├─ MorningRoutine.java         ← Szene: Morgenroutine
   └─ Bedtime.java                ← Szene: Gute-Nacht-Geschichte
```

Jeder Akt ist als eigenes Paket organisiert. Die Szenen innerhalb eines Akts implementieren das `Scene`-Interface und kapseln einen abgeschlossenen fachlichen Ablauf.

### **7.1 Akt 1: Die Hochzeit**

Der erste Akt erzählt die Geschichte der Hochzeit. Er besteht aus mehreren Szenen, die nacheinander ablaufen.

**Szene: Die Band anrufen (CallTheBand)**

```java
public class CallTheBand implements Scene {
    
    private final GroomWithPhone groomWithPhone;
    private final String phoneNumber;
    
    public CallTheBand(GroomWithPhone groomWithPhone, String phoneNumber) {
        this.groomWithPhone = groomWithPhone;
        this.phoneNumber = phoneNumber;
    }
    
    @Override
    public void execute() {
        System.out.println("\n☎️ === THE BAND GETS A CALL ===");
        groomWithPhone.call(phoneNumber); 
        System.out.println("☎️ === CALL COMPLETED ===\n");
    }
}
```

Die Szene `CallTheBand` orchestriert einen Anruf. Der Bräutigam (ausgestattet mit einem Telefon) ruft die Band an. Die Szene selbst ist schlank: Sie delegiert die Aktion an den Akteur und rahmt sie ein.

**Szene: Gäste einladen (InviteTheGuests)**

```java
public class InviteTheGuests implements Scene {

    private BrideWithMailbox brideWithLetters;
    private Guests guests;

    public InviteTheGuests(BrideWithMailbox brideWithLetters, Guests guests) {
        this.brideWithLetters = brideWithLetters;
        this.guests = guests;
    }

    @Override
    public void execute() {
        System.out.println("\n☎️ === INVINTATION OF GUESTS ===");
        guests.forEach(guest -> brideWithLetters.invite(guest));
        System.out.println("☎️ === INVINTATION COMPLETED ===\n");
    }

}
```

Die Szene `InviteTheGuests` durchläuft alle Gäste und lässt die Braut (ausgestattet mit E-Mail-Funktionalität) jedem eine Einladung schicken. Dies zeigt, wie Schleifen und Iterationen fachlich modelliert werden: Die Szene orchestriert, die Akteure handeln.

**Szene: Der Ringtausch (RingExchange)**

```java
public class RingExchange implements Scene {

    private Groom groom;
    private Bride bride;

    public RingExchange(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }

    @Override
    public void execute() {
        System.out.println("\n💍 === RING EXCHANGE ===");
        
        RingBasket ringBasket = new RingBasket();
        
        groom.giveRing(ringBasket);
        bride.receiveRing(ringBasket);
        
        bride.giveRing(ringBasket);
        groom.receiveRing(ringBasket);
        
        System.out.println("💍 === EXCHANGE COMPLETED ===\n");
    }
}
```

Der Ringtausch ist eine atomare Szene: Sie orchestriert die Übergabe der Ringe zwischen Braut und Bräutigam. Der `RingBasket` dient als Medium für den Austausch. Diese Szene zeigt, wie fachliche Abläufe durch Choreographie von Akteuren modelliert werden.

**Medium: Der Ringkorb (RingBasket)**

```java
public class RingBasket extends Media.Default {

}
```

Der `RingBasket` ist ein spezialisiertes Medium für den Ringtausch. Er erbt von `Media.Default` und benötigt keine zusätzliche Logik, da die gesamte Semantik bereits durch die Akteure und die Szene definiert ist.

**Szene: Die Band bezahlen (PayTheBand)**

```java
public class PayTheBand implements Scene {

    private Payer payer;
    private Band band;
    private double amount;

    public PayTheBand(Payer payer, Band band, double amount) {
        this.payer = payer;
        this.band = band;
        this.amount = amount;
    }

    @Override
    public void execute() {
        System.out.println("\n💰 === PAYMENT FOR THE BAND ===");
        String bandAccountId = "stripe_account_" + band.name().value().toLowerCase();
        payer.pay(amount, bandAccountId);
        System.out.println("💰 === PAYMENT COMPLETED ===\n");
    }
}
```

Die Szene `PayTheBand` orchestriert eine Zahlung. Der Bräutigam (als `Payer`) überweist den Betrag an die Band. Die Szene kennt die fachliche Regel: Die Konto-ID der Band wird aus ihrem Namen abgeleitet. So bleibt die Geschäftslogik dort, wo sie semantisch hingehört.

**Video-Rückblick mit Proxy-Pattern**

Das Proxy-Pattern wird hier eingesetzt, um das Laden eines ressourcenintensiven Videos zu verzögern, bis es tatsächlich benötigt wird.

**Video-Interface:**
```java
public interface Video {
    void show(Media canvas);
}
```

**Echtes Video (RealVideo):**
```java
public class RealVideo implements Video {

    private String filename;

    public RealVideo(String filename) {
        this.filename = filename;
        load();
    }

    private void load() {
        System.out.println("⏳ Loading heavy video file: " + filename + "...");
        try {
            Thread.sleep(2000); // Simulate loading time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println("✅ Video loaded: " + filename);
    }

    @Override
    public void show(Media canvas) {
        canvas.with("video", filename);
    }
}
```

Das `RealVideo` simuliert das Laden einer großen Datei. Der Konstruktor ruft `load()` auf, was Zeit kostet. Würde dieses Video sofort bei der Initialisierung geladen, würde die Anwendung unnötig verzögert.

**Lazy-Loading-Proxy (LazyVideo):**
```java
public class LazyVideo implements Video {

    private RealVideo realVideo;
    private String filename;

    public LazyVideo(String filename) {
        this.filename = filename;
    }

    @Override
    public void show(Media canvas) {
        if (realVideo == null) {
            System.out.println("🎬 Initializing video for the first time...");
            realVideo = new RealVideo(filename);
        }
        realVideo.show(canvas);
    }
}
```

Der `LazyVideo`-Proxy verzögert die Initialisierung des echten Videos bis zum ersten Aufruf von `show()`. So wird das Video nur geladen, wenn es tatsächlich abgespielt werden soll. Dies ist ein klassisches Beispiel für **Lazy Loading**.

**Medium: Die Videoleinwand (VideoCanvas)**

```java
/** Canvas for a video film. */
public class VideoCanvas extends Media.Default {

    @Override
    public Media with(String name, String value) {
        super.with(name, value);
        System.out.println("📺 Video playing...");
        return this;
    }

}
```

Der `VideoCanvas` ist ein spezialisiertes Medium für die Wiedergabe von Videos. Jedes Mal, wenn ein Inhalt hinzugefügt wird, gibt er eine Meldung aus. Dies simuliert die visuelle Darstellung.

**Szene: Der Liebesgeschichten-Rückblick (LoveStoryFlashback)**

```java
public class LoveStoryFlashback implements Scene {

    private Video video;
    private VideoCanvas screen;

    public LoveStoryFlashback(Video video, VideoCanvas screen) {
        this.video = video;
        this.screen = screen;
    }

    @Override
    public void execute() {
        System.out.println("\n🎥 === LOVE STORY FLASHBACK ===");
        video.show(screen);
        System.out.println("🎥 === FLASHBACK COMPLETED ===\n");
    }
}
```

Die Szene `LoveStoryFlashback` zeigt ein Video auf einer Leinwand. Dank des Proxy-Patterns wird das Video nur geladen, wenn die Szene tatsächlich ausgeführt wird. Dies zeigt, wie Design Patterns nahtlos in fachliche Szenen eingebettet werden.

**Hauptszene: Die Trauungszeremonie (WeddingCeremony)**

```java
public class WeddingCeremony implements Scene {

    private Groom groom;
    private Bride bride;
    private Guests guests;
    private Band band;
    private Scene ringExchange;

    public WeddingCeremony(Groom groom, Bride bride, Guests guests, Band band, Scene ringExchange) {
        this.groom = groom;
        this.bride = bride;
        this.guests = guests;
        this.band = band;
        this.ringExchange = ringExchange;
    }

    @Override
    public void execute() {
        System.out.println("\n💒 === WEDDING CEREMONY ===");

        groom.perform();
        bride.perform();

        ringExchange.execute();

        band.forEach(musician -> musician.perform());
        guests.forEach(guest -> guest.clap());

        System.out.println("💒 === CEREMONY COMPLETED ===\n");
    }
}
```

Die `WeddingCeremony` ist die zentrale Szene des ersten Akts. Sie orchestriert die Gelübde, den Ringtausch, die Musik und den Applaus. Die Szene selbst ist eine Komposition kleinerer Aktionen. Dies zeigt, wie komplexe fachliche Abläufe durch Komposition einfacher Bausteine entstehen.

### **7.2 Akt 2: Die Hochzeitsreise**

Der zweite Akt erzählt die Hochzeitsreise. Zwei Szenen bilden diesen Akt.

**Szene: Hotel-Check-in (HotelCheckIn)**

```java
public class HotelCheckIn implements Scene {

    private Groom groom;
    private Bride bride;

    public HotelCheckIn(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }

    @Override
    public void execute() {
        System.out.println("\n🏨 === HOTEL CHECK-IN ===");
        System.out.println("🏨 " + groom.name() + " and " + bride.name() + " arrive at the hotel.");
        System.out.println("🏨 They receive the keys to the honeymoon suite.");
        System.out.println("🏨 === CHECK-IN COMPLETED ===\n");
    }
}
```

Die Szene `HotelCheckIn` ist narrativ: Sie erzählt, was passiert, ohne dass die Akteure explizit handeln. Dies zeigt, dass nicht jede Szene eine Choreographie von Methoden sein muss. Manchmal ist die Erzählung selbst die Handlung.

**Szene: Abendessen bei Sonnenuntergang (SunsetDinner)**

```java
public class SunsetDinner implements Scene {

    private Groom groom;
    private Bride bride;

    public SunsetDinner(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }

    @Override
    public void execute() {
        System.out.println("\n🌅 === SUNSET DINNER ===");
        System.out.println("🌅 " + groom.name() + " and " + bride.name() + " enjoy a romantic dinner by the sea.");
        System.out.println("🌅 The sun sets on the horizon.");
        System.out.println("🌅 === DINNER COMPLETED ===\n");
    }
}
```

Auch diese Szene ist narrativ. Sie beschreibt eine Situation, ohne dass technische Logik ausgeführt wird. Dies zeigt, dass der Code nicht nur funktionieren, sondern auch erzählen soll.

### **7.3 Akt 3: Die Geburt**

Der dritte Akt erzählt die Geburt des Kindes. Er führt einen neuen Charakter ein: das Neugeborene.

**Szene: Die Geburt (BirthScene)**

```java
public class BirthScene implements Scene {

    private Bride mother;
    private Newborn newborn;
    private Hospital hospital;

    public BirthScene(Bride mother, Newborn newborn, Hospital hospital) {
        this.mother = mother;
        this.newborn = newborn;
        this.hospital = hospital;
    }

    @Override
    public void execute() {
        System.out.println("\n👶 === BIRTH SCENE ===");
        hospital.admit(mother.name());
        System.out.println("👶 After hours of labor, " + newborn.name() + " is born!");
        newborn.perform();
        System.out.println("👶 === BIRTH COMPLETED ===\n");
    }
}
```

Die Szene `BirthScene` orchestriert die Geburt. Das Krankenhaus nimmt die Mutter auf, das Kind wird geboren, und es lächelt. Die Szene zeigt, wie neue Charaktere in die Geschichte eingeführt werden.

**Ort: Das Krankenhaus (Hospital)**

```java
public class Hospital {

    private final Name name;

    public Hospital(String name) {
        this.name = new Name(name);
    }

    public void admit(Name patientName) {
        System.out.println("🏥 " + patientName + " is admitted to " + name + ".");
    }
}
```

Das `Hospital` ist kein Akteur, sondern ein Ort. Es bietet Dienste an (wie die Aufnahme eines Patienten), ohne selbst zu performen. Dies zeigt, dass nicht alle Klassen Akteure sein müssen. Manche repräsentieren Orte, Dienste oder Ressourcen.

### **7.4 Akt 4: Das Familienleben**

Der vierte Akt zeigt das tägliche Leben der Familie. Zwei Szenen bilden diesen Akt.

**Szene: Die Morgenroutine (MorningRoutine)**

```java
public class MorningRoutine implements Scene {

    private Groom father;
    private Bride mother;
    private Newborn child;

    public MorningRoutine(Groom father, Bride mother, Newborn child) {
        this.father = father;
        this.mother = mother;
        this.child = child;
    }

    @Override
    public void execute() {
        System.out.println("\n☀️ === MORNING ROUTINE ===");
        System.out.println("☀️ " + father.name() + " prepares breakfast.");
        System.out.println("☀️ " + mother.name() + " wakes up " + child.name() + ".");
        child.perform();
        System.out.println("☀️ The family sits down together.");
        System.out.println("☀️ === ROUTINE COMPLETED ===\n");
    }
}
```

Die Morgenroutine zeigt, wie alltägliche Abläufe als Szenen modelliert werden. Die Familie interagiert, und das Kind reagiert. Die Szene ist narrativ, aber zeigt auch Verhalten (das Kind lächelt).

**Szene: Die Gute-Nacht-Geschichte (Bedtime)**

```java
public class Bedtime implements Scene {

    private Groom father;
    private Newborn child;

    public Bedtime(Groom father, Newborn child) {
        this.father = father;
        this.child = child;
    }

    @Override
    public void execute() {
        System.out.println("\n🌙 === BEDTIME ===");
        System.out.println("🌙 " + father.name() + " reads a bedtime story to " + child.name() + ".");
        child.perform();
        System.out.println("🌙 " + child.name() + " falls asleep peacefully.");
        System.out.println("🌙 === BEDTIME COMPLETED ===\n");
    }
}
```

Die Gute-Nacht-Geschichte ist der Abschluss des Films. Sie zeigt, dass die Geschichte nicht spektakulär enden muss, sondern friedlich ausklingen kann. Dies ist eine bewusste narrative Entscheidung: Nicht jede Geschichte endet mit einem Knall.

## **8. Die Bühne (Die Präsentationsebene)**

Die Bühne ist der Ort, an dem das Geschehen für das Publikum sichtbar wird. Im Code entspricht dies der Präsentationsschicht: UI-Komponenten, visuelle Darstellungen und Interaktionen.


![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_08_code_like_movie.png)
```
manuscript/stage/
│
├─ Take.java                     ← Schnittstelle für visuelle Aufnahmen
│
├─ accessory/
│  ├─ ActionButton.java          ← Interaktive Schaltfläche
│  ├─ CinematicGrid.java         ← Cineastisches Rasterlayout
│  └─ SpeechBubble.java          ← Sprechblase für Dialoge
│
└─ take/
   └─ WeddingCloseUp.java        ← Nahaufnahme der Hochzeit
```

Die Bühne ist strikt von der Domäne getrennt. Sie greift auf Akteure zu, verändert sie aber nicht. Alle Interaktionen fließen von der UI zur Domäne, nie umgekehrt.

### **8.1 Die Aufnahme (Take)**

Eine Aufnahme ist die visuelle Darstellung einer Szene.

```java
@FunctionalInterface
public interface Take {
    void render();
}
```

Das Interface `Take` ist analog zu `Scene`, aber für die Präsentation. Es definiert eine Methode `render()`, die die visuelle Darstellung erzeugt.

### **8.2 Die Nahaufnahme (WeddingCloseUp)**

```java
public class WeddingCloseUp {

    private Groom groom;
    private Bride bride;

    public WeddingCloseUp(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }

    public void render() {
        System.out.println("\n🎥 === WEDDING CLOSE-UP ===");
        
        CinematicGrid grid = new CinematicGrid();
        
        SpeechBubble groomBubble = new SpeechBubble(groom);
        SpeechBubble brideBubble = new SpeechBubble(bride);
        
        grid.add(groomBubble);
        grid.add(brideBubble);
        
        ActionButton ringButton = new ActionButton("Give Ring", () -> {
            RingBasket ringBasket = new RingBasket();
            groom.giveRing(ringBasket);
            bride.receiveRing(ringBasket);
        });
        
        grid.add(ringButton);
        
        System.out.println(grid);
        
        System.out.println("\n🎥 === CLOSE-UP COMPLETED ===\n");
    }
}
```

Die `WeddingCloseUp` zeigt, wie die Präsentationsschicht aufgebaut ist: Ein `CinematicGrid` organisiert die visuellen Elemente, `SpeechBubble` zeigt Dialoge, und `ActionButton` ermöglicht Interaktionen. Die Aufnahme selbst greift nur lesend auf die Domäne zu und erzeugt eine visuelle Repräsentation.

### **8.3 Die Hilfselemente**

**Sprechblase (SpeechBubble):**
```java
public class SpeechBubble {

    private Actor actor;

    public SpeechBubble(Actor actor) {
        this.actor = actor;
    }

    @Override
    public String toString() {
        Media.Air speech = new Media.Air();
        actor.speak(speech);
        return "💬 " + actor.name() + ": " + speech.content("text");
    }
}
```

Die `SpeechBubble` lässt einen Akteur in ein `Air`-Medium sprechen und zeigt das Ergebnis an. Sie ist ein reines Präsentations-Element.

**Interaktive Schaltfläche (ActionButton):**
```java
public class ActionButton {

    private String label;
    private Runnable action;

    public ActionButton(String label, Runnable action) {
        this.label = label;
        this.action = action;
    }

    public void click() {
        System.out.println("🖱️ Button clicked: " + label);
        action.run();
    }

    @Override
    public String toString() {
        return "[" + label + "]";
    }
}
```

Der `ActionButton` kapselt eine Aktion, die beim Klicken ausgeführt wird. Er ist ein Beispiel für Command-Pattern in der UI.

**Cineastisches Raster (CinematicGrid):**
```java
public class CinematicGrid {

    private List<Object> elements = new ArrayList<>();

    public void add(Object element) {
        elements.add(element);
    }

    @Override
    public String toString() {
        return elements.stream()
            .map(Object::toString)
            .collect(Collectors.joining("\n"));
    }
}
```

Das `CinematicGrid` sammelt visuelle Elemente und zeigt sie in einer Liste an. Dies ist eine stark vereinfachte Darstellung eines Layouts.

## **9. Die Regie (Der Composition Root)**

Die Regie ist die Schaltzentrale. Sie orchestriert die gesamte Inszenierung, instanziiert alle Akteure, baut die Infrastruktur auf und füllt die Filmrolle mit Szenen.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_09_code_like_movie.png)

```
manuscript/directing/
│
└─ AnalogMovie.java              ← Composition Root & Einstiegspunkt
```

Die Klasse `AnalogMovie` ist die einzige Stelle im System, die alle Abhängigkeiten kennt. Sie baut das Objektgeflecht auf und startet die Inszenierung.

```java
/** The Composition Root of the console application. */
public class AnalogMovie implements Movie {

    public static void main(String[] args) {
        new AnalogMovie().play();
    }

    private final Queue<Scene> filmRoll = new LinkedList<>();

    private void start() {
        
        // ============================================================
        // BACKSTAGE & MAKEUP (Preparation of the infrastructure)
        // ============================================================
        System.out.println("\n🎭 [BACKSTAGE & MAKEUP] Preparation...\n");
        DatabaseArchive archive = new DatabaseArchive("jdbc:cinema://localhost/movie");
        Phone phone = new Phone("0170.777.888");
        StripeApi stripe = new StripeApi();
        EmailBox mailBox = new EmailBox("0127.555.333");

        System.out.println("🎥 Quiet, please! Camera rolling, sound rolling...");
        System.out.println("🎬 Roll film: The screening begins!\n");

        // ============================================================
        // CASTING & MAKEUP (Preparation of the Performers)
        // ============================================================
        Groom romeo = new Groom("Romeo");
        Bride julia = new Bride("Julia");
        Newborn leon = new Newborn("Leon");

        Guests guests = new Guests(Arrays.asList(new Guest("Mary"), new Guest("John")));
        Band band = new Band("Happy Wedding", Arrays.asList(new Musician("Jenny"), new Musician("Ben")));

        GroomWithCard groomWithCard = new GroomWithCard(romeo, stripe);
        GroomWithPhone groomWithPhone = new GroomWithPhone(romeo, phone);

        BrideWithArchive brideWithArchive = new BrideWithArchive(julia, archive);
        BrideWithMailbox brideWithLetters = new BrideWithMailbox(julia, mailBox);

        // ============================================================
        // THE STAGING OF THE SCENES (The Film Reel Is Filled)
        // ============================================================

        // ACT 1: The Wedding
        // Call the Band
        filmRoll.add(new CallTheBand(groomWithPhone, "0170.777.888"));
        // Inviting Guests
        filmRoll.add(new InviteTheGuests(brideWithLetters, guests));
        // Ceremony with atomic scene (RingExchange)
        Scene ringtausch = new RingExchange(romeo, julia);
        filmRoll.add(new WeddingCeremony(romeo, brideWithArchive, guests, band, ringtausch));
        filmRoll.add(new PayTheBand(groomWithCard, band, 500.00));
        // Video Re-cap (with Proxy-Pattern)
        Video video = new LazyVideo("lifestory_hd.mp4");
        VideoCanvas screen = new VideoCanvas();
        filmRoll.add(new LoveStoryFlashback(video, screen));
        // Visual Close-up
        WeddingCloseUp visualTake = new WeddingCloseUp(romeo, julia);
        filmRoll.add(visualTake::render);

        // ACT 2: The Honeymoon
        filmRoll.add(new HotelCheckIn(romeo, julia));
        filmRoll.add(new SunsetDinner(romeo, julia));

        // ACT 3: The Birth
        Hospital hospital = new Hospital("St. Mary's Hospital");
        filmRoll.add(new BirthScene(julia, leon, hospital));

        // ACT 4: The Family Life
        filmRoll.add(new MorningRoutine(romeo, julia, leon));
        filmRoll.add(new Bedtime(romeo, leon));
    }

    @Override
    public void play() {

        start();

        while (!filmRoll.isEmpty()) {
            Scene currentScene = filmRoll.poll();
            System.out.println("\n🎞️ Next scene playing...");
            currentScene.execute();
        }
        System.out.println("\n🎬 It's a wrap! Many thanks to everyone involved.");
    }

}
```

Die `AnalogMovie`-Klasse zeigt die gesamte Orchestrierung:

1. **Infrastruktur aufbauen:** Datenbank, Telefon, Zahlungs-API und E-Mail-Service werden initialisiert.
2. **Akteure casten:** Braut, Bräutigam, Kind, Gäste und Band werden erstellt.
3. **Akteure ausstaffieren:** Dekoratoren statten die Hauptcharaktere mit zusätzlichen Fähigkeiten aus (Telefon, Zahlungskarte, E-Mail, Datenbank).
4. **Filmrolle füllen:** Alle Szenen werden in der richtigen Reihenfolge zur Filmrolle hinzugefügt.
5. **Film abspielen:** Die Szenen werden nacheinander ausgeführt.

Die Regie ist die einzige Stelle, die alle Abhängigkeiten kennt. Kein anderes Paket darf auf sie zugreifen. Dies garantiert, dass die Domäne unabhängig bleibt und die technische Orchestrierung isoliert ist.

## **10. Die Inszenierung läuft ab**

Wenn die `main`-Methode ausgeführt wird, läuft die gesamte Inszenierung ab. Die Konsole zeigt eine lebendige Erzählung:

1. **Szene 1:** Der Bräutigam ruft die Band an. Das Telefon publiziert die Nachricht, und die Band bestätigt.
2. **Szene 2:** Die Band will bezahlt werden. Der StripePaymentAdapter übersetzt Romeos fachliches pay() in die technische Sprache `executeTransaction()` der Stripe-API.
3. **Szene 3:** Der Video-Rückblick startet. Dank des **Proxy-Patterns** wird der ressourcenfressende Film erst in dem Moment geladen, in dem der Souffleur die Leinwand bereitstellt.
4. **Akte 2 bis 4:** Das Leben entfaltet sich - von der Klinik bis zur Guten-Nacht-Geschichte - sauber getrennt in Akte und Szenen. Die weiteren Akte der Lebensgeschichte laufen nacheinander ab.

## **11. Der cineastische Stacktrace**

Das Qualitätsmerkmal einer guten Struktur zeigt sich im Moment des Scheiterns. Während man sich in klassischen Systemen bei einem Fehler meist durch kryptische Framework-Klassen und generische Service-Layer quält, liest sich der Fehlerspeicher in der Spielfilmphilosophie wie eine dramatische Inhaltsausgabe des Films.


![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_10_code_like_movie.png)

```
Exception in thread "main" java.lang.IllegalStateException: Ring konnte nicht übergeben werden: Trauring ist nicht auffindbar!
    at manuscript.character.Bride.receiveRing(Bride.java:42)
    at manuscript.outfit.PersistentActor.receiveRing(PersistentActor.java:36)
    at manuscript.stage.take.WeddingCloseUp.lambda$render$0(WeddingCloseUp.java:38)
    at manuscript.stage.accessory.ActionButton.click(ActionButton.java:15)
    at manuscript.wedding.WeddingCeremony.execute(WeddingCeremony.java:30)
    at manuscript.directing.InstalledMovie.nextScene(InstalledMovie.java:62)
    at manuscript.directing.InstalledMovie.main(InstalledMovie.java:27)
```

Nicht nur die Braut, sondern auch der Stacktrace "schreit" die Fachlichkeit förmlich heraus. Man sieht keine generische `DataUpdateException` in einem `AbstractServiceFactoryProxy`, sondern ein konkretes Drama am Set:

* Wer? Die Braut (`Bride`).
* Was? Sie konnte den Ring nicht empfangen (`receiveRing`).
* In welchem Kostüm? Während sie als PersistentActor agierte.
* In welcher Situation? Während der Nahaufnahme (`WeddingCloseUp`) innerhalb der Trauungszeremonie (`WeddingCeremony`).

Die Technik wird hier vollkommen transparent und tritt hinter die Erzählung zurück. Ein solcher *Stacktrace* erzeugt keine Frustration durch Unverständnis, sondern unmittelbare Resonanz. Man erkennt sofort, an welcher Stelle im Drehbuch die Erzählung unterbrochen wurde. Fehlersuche wandelt sich so vom stressigen Codewühlen zur gezielten Regiekorrektur, die die emotionale Integrität der Geschichte wiederherstellt.

## **12. Bauplan als Drehbuch**

Um eine fachliche und progressive Erzählstruktur im Code zu gewährleisten, bedarf es einer klaren Trennung zwischen der Art der Kommunikation und der physischen Organisation des Projekts.

## **12.1 Die leitenden Prinzipien (Die Regieanweisung)**

Diese Prinzipien definieren, wie Akteure innerhalb des Systems interagieren, um die Kapselung und Autonomie zu wahren:

**Graphen als Skript:** Die Modellierung des Systems erfolgt über drei Dimensionen. Der *Kollaborationsgraph* bildet das Ensemble ab, der *Konstruktionsgraph* regelt den Setaufbau und der *Aufrufgraph* beschreibt den Dialog der Objekte zur Laufzeit.

**Tell Don't Ask:** Interaktionen erfolgen ausschließlich über fachliche Befehle. Ein Objekt wird niemals nach seinem Zustand gefragt, um externe Entscheidungen zu treffen; es wird direkt zum Handeln aufgefordert.

**Law of Demeter:** Akteure kommunizieren nur mit ihren unmittelbaren Nachbarn. Diese Beschränkung der Zugriffspfade garantiert eine lose Kopplung und schützt die Inszenierung vor semantischer Instabilität.

## **12.2 Regeln für die Projektstruktur (Das Inhaltsverzeichnis)**

Damit die Geschichte für den Leser auffindbar bleibt, folgt die Paketierung fachlichen statt technischen Kriterien:

**Domänenzentrierung:** Die Paketstruktur orientiert sich nicht an technischen Schichten (wie *Service* oder *Repository*), sondern folgt hierarchisch den fachlichen Domänenkonzepten.

**Fokus auf Ebene Null:** Die oberste Paketebene enthält ausschließlich den fachlichen Kern. Dazu gehören *Schnittstellen*, *abstrakte Klassen*, *Value Objects*, *Entitäten* und das zentrale *Systeminterface* als Einstiegspunkt der Anwendung. Diese Ebene bleibt frei von technischem Ballast.

**Trennung der Realisierung:** Alle konkreten Implementierungen und technischen Details (wie *Infrastruktur* und *Benutzerpresäntation*) erfolgen strikt getrennt in untergeordneten Paketen.

**Hierarchie ohne Zyklen:** Ein übergeordnetes Paket darf niemals von seinen Unterpaketen abhängen. Zyklische Abhängigkeiten sind strikt auszuschließen, um einen gerichteten Informationsfluss zu wahren. Die Verwendung von Unterpaketen ist zulässig, sofern sie nicht auf derselben Hierarchieebene liegen.

**Detailverfeinerung:** Unterpakete führen keine neuen Fachkonzepte ein. Sie dienen lediglich der inhaltlichen Ausgestaltung der Konzepte aus der übergeordneten Ebene.

**Fachsprache (Ubiquitous Language):** Die Benennung von *Paketen*, *Klassen* und *Methoden* folgt der Sprache der realen Fachwelt, nicht der technischen Umsetzung.

## **13. Schlusswort: Code, der Geschichten erzählt**

Software als Drehbuch zu begreifen bedeutet, die Welt der leblosen Datencontainer zu verlassen und eine Bühne zu betreten, auf der die Fachlichkeit sichtbar wird. Die strikte Trennung von Backstagetechnik und Regieorchestrierung befreit die Domäne von technischem Ballast. Was bleibt, ist die reine Erzählung — klar, menschlich und nachvollziehbar. 

Die cineastische Struktur ist am Ende ein emotionales Investment. Sie verwandelt eine leblose Maschine in eine verständliche Erzählung. Wenn wir den Stress beim Debugging durch die Klarheit eines Stacktrace ersetzen, der eine Geschichte erzählt, gewinnen wir nicht nur Zeit, sondern auch Freude an unserer Arbeit zurück. Die Softwarearentwicklung ist somit auch die Kunst, Systeme zu bauen, deren Geschichte man gerne teilt.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_11_code_like_movie.png)

**Die neue Rolle des Entwicklers als Regisseurs**

Die Spielfilmphilosophie geht somit über das bloße Befüllen technischer Schablonen oder das Aneinanderkleben von KI Output hinaus und verwandelt das Lesen von Code in ein intuitives Erlebnis. Die Rolle des Entwicklers wandelt sich hierbei zum Regisseur und Autoren. Werden wir also zu Erzählern und erschaffen Code, der nicht nur funktioniert, sondern seine eigene Bedeutung präzise auf die Leinwand bringt.

**Klappe zu, Film ab!**

## Quellen-Verzeichnis

* Imogen Short (2025): [The neuroscience of storytelling](https://www.linkedin.com/pulse/neuroscience-storytelling-why-stories-make-us-remember-imogen-short-c23ec/)
* Robert Bräutigam (2021): [Next Level Readability](https://www.informatik-aktuell.de/entwicklung/programmiersprachen/next-level-lesbarkeit.html) (in German)  
* Robert Bräutigam (2017): [Happy Packaging](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
* Mark Seemann (2011): [Composition Root Pattern](https://blog.ploeh.dk/2011/07/28/CompositionRoot/)
* Mark Seemann (2011): [Compose object graphs with confidence](https://blog.ploeh.dk/2011/03/04/Composeobjectgraphswithconfidence/)
* Miško Hevery (2008): [Where Have All the Singletons Gone?](https://testing.googleblog.com/2008/08/where-have-all-singletons-gone.html)
* Yegor Bugayenko (2016): [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html)

* Wikipedia (2026): [Adapter Pattern](https://en.wikipedia.org/wiki/Adapter_pattern)
* Wikipedia (2026): [Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)
* Wikipedia (2026): [Proxy Pattern](https://en.wikipedia.org/wiki/Proxy_pattern)
