
**Teaser Statement**
> KI schreibt Code heute in Sekunden. Aber wer versteht ihn morgen noch?  
> Warum wir aufhören sollten, Schichten zu bauen, und anfangen müssen, Drehbücher zu schreiben. 
 
 Tags: 🎬💻 #SoftwareArchitecture #CleanCode #AI #Storytelling #DesignPattern 


# **🎞️ Software als Spielfilm-Drehbuch**

Das Aufkommen von Künstlicher Intelligenz wie ChatGPT, Claude oder GitHub Copilot hat die Softwareentwicklung grundlegend verändert. Die alte Faustregel „zehnmal mehr lesen als schreiben“ gilt heute stärker denn je – nur dass wir inzwischen nicht mehr unseren eigenen Code lesen, sondern vor allem den von Maschinen generierten. Um diese Flut überhaupt noch menschlich erfassen und beherrschen zu können, braucht es eine neue Perspektive auf Softwarearchitektur.

Computer verarbeiten beliebige Datenstrukturen. Menschen dagegen verstehen die Welt über Geschichten. Biologisch gesehen ist unser Gehirn darauf programmiert, Informationen über Ursache, Wirkung und **Emotionen** zu speichern. Wo nackte Daten eher kognitive Kälte erzeugen und schnell in Vergessenheit geraten, erzeugen gute Geschichten Resonanz. Sie wecken Neugier, schaffen Empathie für die fachlichen Abläufe und motivieren zum Weiterlesen. Deshalb liegt es nahe, Code wie ein Filmdrehbuch zu gestalten.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_01_code_like_movie.png)

Im Gegensatz zu Ansätzen, die Software als technische Schichtenlandschaft aus zustandslosen Services mit passiven Datencontainern betrachten, inszeniert die Spielfilmphilosophie ein objektorientiertes System als lebendiges Ensemble intelligenter Akteure. Diese Akteure treten in Szenen auf, handeln in Akten und verleihen der logischen Abfolge eine menschlich greifbare Dramaturgie. Die Geschichte wird so nicht mehr bloß ausgeführt, sondern durch das Verhalten der Akteure emotional erlebbar gemacht.

## 1. **Drehbuchs als Bauanleitung**

Die Modellierung eines objektorientierten Systems erfolgt über drei zentrale Graphen. In diesem Kontext übernimmt das Drehbuch die Rolle einer Bauanleitung: Es definiert den Rahmen, in dem die statische Struktur und der flüchtige Moment der Interaktion nahtlos ineinandergreifen.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_02_code_like_movie.png)

* **Der Konstruktions-Graph (Die Regie / Der Set-Aufbau):** Er beschreibt die Instanziierung und den Aufbau des Objektgerüsts durch den [Composition Root](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) (Wer besitzt wen?). Hier wird die „Bühnentechnik“ installiert, bevor die erste Szene beginnt.
* **Der Kollaborations-Graph (Das Ensemble):** Er definiert die dauerhaften Beziehungen zwischen den Objekten (Wer kennt wen und kann miteinander kommunizieren?). Dies entspricht der festen Besetzung der Rollen und ihrer Vernetzung untereinander.
* **Der Aufruf-Graph (Die Handlung):** Er bildet die dynamischen Interaktionen zur Laufzeit ab  (Wer ruft gerade welche Methode auf?) – den eigentlichen Dialog zwischen den Akteuren während der Ausführung einer Szene.

**Die zentralen Regieanweisungen**

Das **Law of Demeter** und das Prinzip **Tell Don’t Ask** fungieren als zentrale Regieanweisungen. Während das *Law of Demeter* den Zugriffspfad strikt auf unmittelbare Nachbarn beschränkt und ein Akteur somit nur mit seinen direkten Szenenpartnern interagiert, fordert *Tell Don’t Ask* Prinzip dazu auf, Objekten Befehle zu erteilen statt deren internen Zustand abzufragen. Ein Held, der nur tut, was man ihm sagt, ist langweilig. Ein Charakter, der selbst entscheidet, berührt uns.

**Das Inhaltsverzeichnis des Drehbuchs**

Um einen fachlichen und progressiven Informationsfluss zu gewährleisten, muss das Inhaltsverzeichnis des Drehbuchs einen klaren Navigationspfad abbilden, der dem Leser hilft, die Bestandteile der Geschichte effizient zu lokalisieren. Hierfür gelten drei pragmatische Regeln:

1. **Klare Abhängigkeiten:** Pakete dürfen niemals von ihren Unterpaketen abhängen. Zirkelbezüge sind strikt verboten, um den gerichteten Informationsfluss zu wahren.
2. **Präzise Detailverfeinerung:** Unterpakete führen keine neuen Konzepte ein. Sie konkretisieren lediglich bereits bestehende Begriffe der übergeordneten Ebene.
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
├─ Actor.java         ← AKTEUR (Zentrales Interface)  
├─ Name.java          ← REQUISITE (Präzise Fachwerte / Value Object)  
├─ Outfit.java        ← AUSSTATTUNG (Basis-Decorator)  
├─ Scene.java         ← SZENE (Das Interaktionsprotokoll)
├─ Media.java         ← Medium (Der Informationsträger)  
└─ Movie.java         ← DREHBUCH (Der rote Faden / Einstiegspunkt)
```
An dieser Stelle des Entwurfs ist es wichtig die Fachbegriffe der Ubiquitous Language zu verwenden denn, diese sind nicht nur präzise, sondern erzeugen Resonanz. Sie wecken die Emotionen des Fachbereichs und sorgen dafür, dass Entwickler und Fachexperten dieselbe Geschichte fühlen, nicht nur lesen.

### **3.1 Der Actor (Die Hauptrolle)**

Das `Actor`-Interface ist der Dreh- und Angelpunkt. Jede handelnde Figur im System muss diese Rolle annehmen. Es reduziert alle Akteure auf ihre fachliche Essenz: Identität und Handlung.

```java
package manuscript;

public interface Actor {
    /** Der Name als unveränderliches Requisit. */
    Name name();
    
    /** Der primäre Kommunikationskanal: Die Aufforderung zum Handeln. */
    void perform();
}
```

### **3.2 Das Name Value Object (Die Requisite)**

Ein `Name` ist mehr als ein simpler String. Er ist ein unveränderliches Objekt mit eingebauter Validierung. In unserer Filmwelt ist es ein Requisit, das sicherstellt, dass kein Charakter "namenlos" die Bühne betritt.

```java
package manuscript;

public record Name(String value) {
    
    public Name {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("Name darf nicht leer sein!");
        }
    }
    
    @Override
    public String toString() {
        return value;
    }
}
```

### **3.3 Das Outfit (Die Maske)**

Das `Outfit` nutzt das Decorator-Pattern, um Akteuren dynamisch neue Fähigkeiten zu verleihen (z. B. ein `LoggingOutfit` oder ein `TransactionalOutfit`), ohne deren Kernidentität zu verändern. Es ist die "Verkleidung", die das Verhalten für eine bestimmte Szene anpasst.

```java
package manuscript;

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

### **3.4 Die Szene (Der Handlungsrahmen)**

Die `Scene`-Interface ist das atomare Element unserer Erzählung. Sie kapselt eine spezifische fachliche Interaktion in einer ausführbaren Einheit.

```java
package manuscript;
  
@FunctionalInterface  
public interface Scene {
    void execute();  
}
```

### **3.5 Das Medium (Der Informationsträger)**

Das `Media` Interface bildet die Brücke zwischen der Kapselung und der notwendigen Informationausgabe. Anstatt einen Akteur nach seinem internen Zustand zu fragen (ask), um diesen dann extern zu verarbeiten, erhält der Akteur die Anweisung, sich selbst auf einem Medium darzustellen (tell). Ein Charakter entblößt nicht seine gesamte Privatsphäre gegenüber dem Regisseur, sondern liefert gezielt die Informationen, die für die aktuelle Szene oder die Kameraeinstellung relevant sind.

```java
package manuscript;

public interface Media {

    Media with(String name, String value);
    Media with(String name, BigDecimal value);
    Media with(String name, LocalDate value);
    Media with(String name, boolean value);

 // optional extension
 // Media with(String name, String [] strings);  // ← list
 // Media with(String name, Media media);       // ← nested
 // Media with(String name, Media[] medias);   // ← nested list
}
```

Es ist nicht nur ein technisches Hilfsmittel, sondern die Erzählweise, mit der die Akteure ihre Informationen an die Stage (UI) oder das Backstage (Datenbank) übermitteln. Durch diese Umkehrung der Verantwortlichkeit wird das Objekt vom passiven Datenlieferanten zum aktiven Teilnehmer der Inszenierung.


### **3.6 Die Movie als Composition Root (Die Geschichte selbst)**

Das `Movie` Interface repräsentiert einen klaren Einstiegspunkt für den gesamten Ablauf der Erzählung. Es dient als zentraler Koordinator, der die verschiedenen Komponenten wie Szenen und Akteure in eine logische und zeitliche Reihenfolge bringt. Als Abstraktion der Geschichte selbst gewährleistet es die Entkopplung der fachlichen Inhalte von der technischen Regie.

```java
package manuscript;

/**
 * Das Movie Interface ist die fachliche Repräsentation der Geschichte.
 * Es definiert den Lebenszyklus und die Abfolge der Inszenierung.
 */
public interface Movie {

    /** Startet die Vorführung und befüllt die Filmrolle. */  
    void start();

    /** Spielt alle geladenen Szenen nacheinander ab. */  
    void nextScene();  
}
```

## **4. Die Charaktere (Das Casting)**

In diesem Paket werden die grundlegenden Persönlichkeiten und Rollenprofile der Anwendung definiert. Es beschreibt die konkreten Figuren, die im Laufe der Geschichte auftreten können, noch ohne spezifische Verkleidung oder technische Bindung.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_05_code_like_movie.png)

```
manuscript/
...
├─ character/  
│  ├─ Groom.java               ← Der Bräutigam  
│  ├─ Bride.java               ← Die Braut  
│  └─ Newborn.java             ← Das Neugeborene
...
```

### **4.1 Der Bräutigam**

Der Bräutigam ist einer der Hauptcharaktere in der Hochzeitsgeschichte. Er artikuliert seine Identität gegenüber dem Medium, ohne seine interne Struktur zu entblößen.

```java
package manuscript.character;

import manuscript.Actor;
import manuscript.Name;

/**  
 * Der Bräutigam ist einer der Hauptcharaktere in der Hochzeitsgeschichte.  
 */  
public class Groom implements Actor {
    
    private final Name name;
    
    public Groom(String name) {
        this.name = new Name(name);
    }
    
    @Override
    public Name name() {
        return name;
    }
    
    @Override
    public void perform() {
        System.out.println("💍 " + name + " gibt sein Eheversprechen ab.");
    }
    
    /**  
     * Der Bräutigam überreicht den Ring.  
     */  
    public void giveRing() {
        System.out.println("💍 " + name + " überreicht den Trauring.");
    }

     /**
     * Der Akteur artikuliert seine Identität.
     * Er "spricht" zum Medium und übergibt seine fachlichen Details, ohne seine interne Struktur zu entblößen.
     */
    public Media speak(Media media) {
        return media
            .with("role", "Groom")
            .with("name", name.value())
            .with("text", "Yes, I will...");
    }
    
}
```

### **4.2 Die Braut**

Die Braut ist die zweite Hauptfigur. Sie verwaltet ihren Zustand (den Empfang des Rings) intern und teilt diesen über die fachliche `speak(...)` Methode mit.

```java
package manuscript.character;

import manuscript.Actor;
import manuscript.Name;

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
        System.out.println("💍 " + name + " gibt ihr Eheversprechen ab.");
    }
    
    /**  
     * Die Braut empfängt den Ring.  
     */  
    public void receiveRing() {
        if (hasRing) {
            throw new IllegalStateException("Die Braut trägt bereits einen Ring!");
        }
        System.out.println("💍 " + name + " nimmt den Trauring entgegen.");
        hasRing = true;
    }

     /**
     * Die Braut "spricht" das "Ja-Wort".
     */
    public Media speak(Media media) {
        return media
            .with("role", "Bride")
            .with("name", name.value())
            .with("text", "Yes, I will...");
            .with("hasRing", hasRing);
    }
}
```

### **4.3 Das Neugeborene**

Das `Neugeborene` erscheint in der Familiengeschichte. Seine primäre Ausdrucksform ist das Weinen, was fachlich als Mitteilung an ein Medium realisiert wird.

```java
package manuscript.character;

import manuscript.Actor;
import manuscript.Name;

/**  
 * Das Neugeborene ist ein neuer Charakter, der während der Familiengeschichte erscheint.  
 */  
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
        System.out.println("👶 " + name + " lächelt fröhlich.");
    }
    
    /**  
     * Das Baby weint.  
     */  
    public void cry() {
        Air air = new Air(); // default Medium
        speak(air);
        System.out.println(air.toString());
    }
    
     /**
     * Das Baby weint.
     */
    public Media speak(Media media) {
        return media
            .with("role", "Newborn")
            .with("name", name.value())
            .with("text", "😭 " + name + " weint laut!");
    }
}
```

### **4.4 Die Maskenbildnerei (Das Outfit)**

Hier liegen die Decorator der Architektur. Ein Outfit fungiert als Objekt in Verkleidung. Wie ein Schauspieler schlüpft die Figur in ein Kostüm (z. B. für die Persistenz oder Validierung), um zusätzliche Fähigkeiten zu simulieren, wobei sie dieselbe Person bleibt und denselben fachlichen Text spricht.

```
manuscript/
...
├─ outfit/  
│  ├─ SuitedActor.java         ← Actor im Hochzeitsanzug  
│  ├─ PersistentActor.java     ← Actor mit Datenbank-Anbindung  
│  └─ ValidatedActor.java      ← Actor mit Regelprüfung
...
```

* `SuitedActor`: Verleiht dem Auftritt den passenden visuellen Rahmen.
* `PersistentActor`: Sorgt dafür, dass die Taten des Akteurs für die Nachwelt (Datenbank) erhalten bleiben.
* `ValidatedActor`: Agiert als Souffleur, der vor dem Auftritt prüft, ob der Akteur bereit für seine Rolle ist.

### **4.5 Der Suited Actor**

Der `SuitedActor` repräsentiert einen Akteur im Hochzeitsanzug. Er behält seine Identität, tritt aber formeller auf.

```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;

public class SuitedActor extends Outfit {
    
    public SuitedActor(Actor actor) {
        super(actor);
    }
    
    @Override
    public void perform() {
        System.out.println("🤵 " + name() + " tritt im Hochzeitsanzug auf die Bühne.");
        decoratedActor.perform();
    }
}
```

### **4.6 Der Validated Actor**

Der `ValidatedActor` prüft vor jeder Handlung, ob die Voraussetzungen erfüllt sind.
 
```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;

public class ValidatedActor extends Outfit {
    
    public ValidatedActor(Actor actor) {
        super(actor);
    }
    
    @Override
    public void perform() {
        if (name().value().length() < 3) {
            throw new IllegalStateException("Name zu kurz für eine Aufführung!");
        }
        System.out.println("✅ " + name() + " wurde validiert.");
        decoratedActor.perform();
    }
}
```

### **4.7 Der Persistent Actor**

Der `PersistentActor` versieht einen Actor mit Persistenz-Fähigkeiten. Nach jeder Handlung wird der Zustand in der Datenbank gespeichert.

```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;
import manuscript.backstage.DatabaseArchive;

public class PersistentActor extends Outfit {
    
    private final DatabaseArchive archive;
    
    public PersistentActor(Actor actor, DatabaseArchive archive) {
        super(actor);
        this.archive = archive;
    }
    
    @Override
    public void perform() {
        decoratedActor.perform();
        archive.save(decoratedActor);
        System.out.println("💾 " + name() + " wurde in der Datenbank gespeichert.");
    }

}
```

Hinter dem Vorhang, im Verborgenen, arbeitet die Bühnentechnik. Hier werden die technischen Dienste implementiert, die unsere Akteure nutzen, um ihre Rollen auszufüllen. Die Domäne weiß zwar, dass es ein Archiv gibt, aber nicht, wie die Regale darin gebaut sind.


## **5. Die Akte der Dramaturgie (Die Fachprozesse)**

In den fachlichen Paketen findet die schrittweise Realisierung der Anforderungen statt. Hier werden die abstrakten Figuren für konkrete Szenarien ausgestattet und in den Dialog geschickt. Das *Tell, Don't Ask*-Prinzip sorgt in diesen Paketen für eine zielgerichtete Handlung ohne technisches Rauschen.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_06_code_like_movie.png)

Jedes Paket repräsentiert einen Akt im Leben der Anwendung:
```
manuscript/
...
├─ wedding/  
│  ├─ WeddingCeremony.java     ← Die Trauungs-Szene  
│  ├─ PayTheBand.java          ← Das Bezahlen der Band  
│  ├─ RingExchange.java        ← Ringtausch Interaktion
│  ├─ LoveStoryFlashback.java  ← Das Abspielen eines Films   
│  ├─ RealVideo.java           ← Film-Implementierung   
│  ├─ LazyVideo.java           ← Verzögertes Laden des Films  
│  └─ Video.java               ← Film-Interface  
│  
├─ honeymoon/  
│  ├─ HotelCheckIn.java        ← Ankunft im Hotel  
│  └─ SunsetDinner.java        ← Das romantische Abendessen  
│  
├─ birth/  
│  ├─ Hospital.java            ← Die Klinik  
│  └─ BirthScene.java          ← Die Geburt  
│  
├─ familylife/  
│  ├─ MorningRoutine.java      ← Der Alltag  
│  ├─ Bedtime.java             ← Die Gute-Nacht-Geschichte
...
```

### **5.1 AKT 1: Die Hochzeit**

#### **5.1.1 Der Ringtausch**

Der `RingExchange` ist eine eigenständige Szene, die ausschließlich den Austausch der Ringe zwischen Braut und Bräutigam darstellt.
 
```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;

public class RingExchange implements Scene {
    
    private final Groom groom;
    private final Bride bride;
    
    public RingExchange(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }
    
    @Override
    public void execute() {
        System.out.println("\n💍 === DER RINGTAUSCH ===");
        groom.giveRing();
        bride.receiveRing();
        System.out.println("💍 === RINGE WURDEN GETAUSCHT ===\n");
    }
}
```

#### **5.1.2 Die Inszenierung der Trauung**

Die `WeddingCeremony` ist unser Regieplan für den Höhepunkt des ersten Aktes. Sie bringt die Akteure zusammen und lässt sie gemäß ihrer fachlichen Bestimmung handeln.

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;

public class WeddingCeremony implements Scene {
    private final Groom groom;
    private final Bride bride;
    private final Scene ringExchange; // Die Zeremonie beinhaltet den Ringtausch

    public WeddingCeremony(Groom groom, Bride bride, Scene ringExchange) {
        this.groom = groom;
        this.bride = bride;
        this.ringExchange = ringExchange;
    }

    @Override
    public void execute() {
        System.out.println("\n💒 === DIE TRAUUNGSZEREMONIE BEGINNT ===");
        
        // Die Akteure geben ihr Versprechen ab
        groom.perform();
        bride.perform();
        
        // Der Höhepunkt: Der Ringtausch wird als Unterszene aufgerufen
        ringExchange.execute();
        
        System.out.println("💒 === DIE TRAUUNG IST VOLLZOGEN ===\n");
    }
}
```
#### **5.1.3 Das Video-Interface**

Ein Film im Film? Das erfordert Fingerspitzengefühl. Das `Video`-Interface abstrahiert die Darstellung, während der `LazyVideo` als geschickter Statist agiert: Er wartet im Hintergrund und lädt das schwere Bildmaterial erst dann, wenn der Regisseur tatsächlich „Action!“ ruft (Lazy Loading).

* `Video`: Das fachliche Versprechen, ein Video abspielen zu können.
* `RealVideo`: Die ressourcenintensive Umsetzung.
* `LazyVideo`: Der Platzhalter, der die Performance schont, bis der Moment gekommen ist.

```java
package manuscript.wedding;

public interface Video {
    /** Lädt das Video in den Arbeitsspeicher. */  
    void load();
    /** Spielt das Video ab. */  
    void play();
}
```

#### **5.1.4 Das echte Video**

Das `RealVideo` ist die konkrete Implementierung eines Videos. Das Laden verbraucht viele Ressourcen, daher sollte es nur bei Bedarf geschehen.

```java
package manuscript.wedding;

public class RealVideo implements Video {
    
    private final String filename;
    private boolean loaded = false;
    
    public RealVideo(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void load() {
        if (!loaded) {
            System.out.println("📼 Lade Video: " + filename + " (dies dauert einen Moment...)");
            // Simuliere schwere Lade-Operationen
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            loaded = true;
        }
    }
    
    @Override
    public void play() {
        if (!loaded) {
            load();
        }
        System.out.println("▶️  Spiele Video ab: " + filename);
    }
}
```

#### **5.1.5 Das Video-Proxy (VideoProxy.java)**

Das `LazyVideo` ist ein Lazy-Loading-Proxy für Videos. Das eigentliche Video wird erst geladen, wenn es tatsächlich abgespielt wird.
   
```java
package manuscript.wedding;

public class LazyVideo implements Video {
    
    private final String filename;
    private RealVideo realVideo;
    
    public LazyVideo(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void load() {
        if (realVideo == null) {
            realVideo = new RealVideo(filename);
        }
        realVideo.load();
    }
    
    @Override
    public void play() {
        if (realVideo == null) {
            System.out.println("🎬 Proxy bereitet Video vor...");
            realVideo = new RealVideo(filename);
        }
        realVideo.play();
    }
}
```

#### **5.1.6 Die Liebesgeschichten-Rückblende**

Die `LoveStoryFlashback` Klasse spielt einen Video-Rückblick ab, der die Liebesgeschichte des Paares. Hier werden die Requisiten   zusammengeführt. Die Szene nutzt `LazyVideo` als Proxy, um die Geschichte des Paares auf die Leinwand (`SpeechBubble`) zu bringen.zeigt.  

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.stage.accessory.SpeechBubble;

public class LoveStoryFlashback implements Scene {
    
    private final Video video;
    private final SpeechBubble screen;
    
    public LoveStoryFlashback(Video video, SpeechBubble screen) {
        this.video = video;
        this.screen = screen;
    }
    
    @Override
    public void execute() {
        System.out.println("\n📽️  === LIEBESGESCHICHTE - RÜCKBLICK ===");
        screen.display();
        video.play();
        System.out.println("📽️  === ENDE DES RÜCKBLICKS ===\n");
    }
}
```

#### **5.1.7 Die Band bezahlen**

Auch ein Drehbuch kommt nicht ohne weltliche Dinge aus. Diese Szene nutzt einen Adapter (`Payable`), um die fachliche Welt der Hochzeit mit dem nüchternen Zahlungssystem der Bühnentechnik zu verbinden. Die `PayTheBand` repräsentiert die Szene, in der die Hochzeitsband bezahlt wird. Dies demonstriert den Adapter-Pattern für externe Zahlungssysteme.

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.backstage.payment.Payable;

public class PayTheBand implements Scene {
    
    private final Payable payer;
    private final double amount;
    
    public PayTheBand(Payable payer, double amount) {
        this.payer = payer;
        this.amount = amount;
    }
    
    @Override
    public void execute() {
        System.out.println("\n🎵 === DIE BAND WIRD BEZAHLT ===");
        payer.pay(amount);
        System.out.println("🎵 === ZAHLUNG ABGESCHLOSSEN ===\n");
    }
}
```

### **5.2 AKT 2: Die Flitterwochen**

Nach dem Trubel der Hochzeit fokussiert sich das Drehbuch auf die Zweisamkeit. Technisch gesehen nutzen wir hier das `Actor`-Interface anstelle konkreter Klassen. Das bedeutet: In den Flitterwochen ist es egal, ob es sich um den „nackten“ Bräutigam oder einen „persistierten“ Bräutigam im Anzug handelt – für das Hotel zählt nur der Gast (die Rolle).

* `HotelCheckIn`: Die Ankunft am Set.
* `SunsetDinner`: Eine Szene, die den Zustand der Akteure (Zufriedenheit) subtil verändert.

#### **5.2.1 Hotel Check-In**

`HotelCheckIn` ist die Szene, in der das Paar im Hotel ankommt.

```java
package manuscript.honeymoon;

import manuscript.Scene;
import manuscript.Actor;

public class HotelCheckIn implements Scene {
    
    private final Actor groom;
    private final Actor bride;
    
    public HotelCheckIn(Actor groom, Actor bride) {
        this.groom = groom;
        this.bride = bride;
    }
    
    @Override
    public void execute() {
        System.out.println("\n🏨 === HOTEL CHECK-IN ===");
        System.out.println("🗝️  " + groom.name() + " und " + bride.name() + " beziehen ihr Zimmer.");
        System.out.println("🏨 === CHECK-IN ABGESCHLOSSEN ===\n");
    }
}
```

#### **5.2.2 Romantisches Abendessen**

`SunsetDinner` ist die romantische Abendessen-Szene während der Flitterwochen.

```java
package manuscript.honeymoon;

import manuscript.Scene;
import manuscript.Actor;

public class SunsetDinner implements Scene {
    
    private final Actor groom;
    private final Actor bride;
    
    public SunsetDinner(Actor groom, Actor bride) {
        this.groom = groom;
        this.bride = bride;
    }
    
    @Override
    public void execute() {
        System.out.println("\n🌅 === ROMANTISCHES ABENDESSEN BEI SONNENUNTERGANG ===");
        System.out.println("🍷 " + groom.name() + " und " + bride.name() + " genießen ein köstliches Dinner.");
        System.out.println("🌅 === ABENDESSEN BEENDET ===\n");
    }
}
```

### **5.3 AKT 3: Die Geburt**

Dieser Akt führt eine neue **Location (Hospital)** und einen neuen **Charakter (Newborn)** ein. Hier zeigt sich, wie externe Systeme (Krankenhaus) kontrolliert in die Geschichte integriert werden, um einen neuen Lebensabschnitt einzuläuten.Hospital: Ein Infrastruktur-Dienst, der den Rahmen für das Geschehen bietet.BirthScene: Die Geburtsstunde eines neuen Objekts im System.

#### **5.3.1 Das Krankenhaus**

`Hospital` repräsentiert das Krankenhaus als Ort der Geburt.

```java
package manuscript.birth;

public class Hospital {
    
    private final String name;
    
    public Hospital(String name) {
        this.name = name;
    }
    
    public void admitPatient(String patientName) {
        System.out.println("🏥 " + patientName + " wird im " + name + " aufgenommen.");
    }
    
    public void deliverBaby(String babyName) {
        System.out.println("👶 " + babyName + " wurde im " + name + " geboren!");
    }
}
```

#### **5.3.2 Die Geburts-Szene**

`BirthScene` orchestriert die Geburt des Kindes.

```java
package manuscript.birth;

import manuscript.Scene;
import manuscript.character.Bride;
import manuscript.character.Newborn;
 
public class BirthScene implements Scene {
    
    private final Bride mother;
    private final Newborn baby;
    private final Hospital hospital;
    
    public BirthScene(Bride mother, Newborn baby, Hospital hospital) {
        this.mother = mother;
        this.baby = baby;
        this.hospital = hospital;
    }
    
    @Override
    public void execute() {
        System.out.println("\n🤰 === DIE GEBURT ===");
        hospital.admitPatient(mother.name().value());
        hospital.deliverBaby(baby.name().value());
        baby.cry();
        System.out.println("🤰 === GEBURT ERFOLGREICH ===\n");
    }
}
```

### **5.4 AKT 4: Das Familienleben**

Der finale Akt beschreibt den „Dauerzustand“. Die Szenen werden repetitiver, aber nicht weniger wichtig. Hier sehen wir die Kollaboration des gesamten Ensembles. Vater, Mutter und Kind agieren als eingespieltes Team.

* `MorningRoutine`: Koordination mehrerer Akteure zur Erreichung eines gemeinsamen Ziels (Frühstück/Versorgung).
* `Bedtime`: Eine ruhige Szene, die zeigt, wie Akteure auf die Handlungen anderer reagieren (Einschlafen nach dem Vorlesen).

#### **5.4.1 Die Morgenroutine**

Die `MorningRoutine` repräsentiert den typischen Morgen einer jungen Familie.

```java
package manuscript.familylife;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;
import manuscript.character.Newborn;

public class MorningRoutine implements Scene {
    
    private final Groom father;
    private final Bride mother;
    private final Newborn child;
    
    public MorningRoutine(Groom father, Bride mother, Newborn child) {
        this.father = father;
        this.mother = mother;
        this.child = child;
    }
    
    @Override
    public void execute() {
        System.out.println("\n☀️ === MORGENROUTINE DER FAMILIE ===");
        System.out.println("⏰ " + father.name() + " bereitet das Frühstück vor.");
        System.out.println("🍼 " + mother.name() + " füttert " + child.name() + ".");
        child.perform();
        System.out.println("☀️ === MORGENROUTINE ABGESCHLOSSEN ===\n");
    }
}
```

#### **5.4.2 Die Gute-Nacht-Geschichte**

Die `Bedtime` ist die Szene, in der der Vater seinem Kind eine Gute-Nacht-Geschichte vorliest.

```java
package manuscript.familylife;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Newborn;

public class Bedtime implements Scene {
    
    private final Groom father;
    private final Newborn child;
    
    public Bedtime(Groom father, Newborn child) {
        this.father = father;
        this.child = child;
    }
    
    @Override
    public void execute() {
        System.out.println("\n🌙 === GUTE-NACHT-GESCHICHTE ===");
        System.out.println("📖 " + father.name() + " liest " + child.name() + " eine Geschichte vor.");
        child.perform();
        System.out.println("😴 " + child.name() + " schläft friedlich ein.");
        System.out.println("🌙 === GUTE NACHT ===\n");
    }
}
```

## **6. Die Bühnentechnik (Backstage)**

Damit die Akteure auf der Bühne glänzen können, stellt die Bühnentechnik verschiedene Kanäle und Speichersysteme bereit.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_07_code_like_movie.png)

```
manuscript/
... 
├─ backstage/
│  ├─ archive/  
│  │  ├─ DatabaseArchive.java   ← Datenbank-Zugriff
│  │  └─ RecordArchive.java     ← DTO für Archive  
│  ├─ payment/
│  │  ├─ StripeApi.java         ← Stripe API Client
│  │  ├─ StripeRequest.java     ← DTO für Stripe API 
│  │  ├─ StripeResponse.java    ← DTO für Stripe API 
│  │  └─ StripePayment.java     ← Adapter für Stripe  
│  ├─ phone/  
│  │  ├─ Phone.java             ← Channel
│  │  └─ Headset.java           ← DTO für Channal
│  ├─ Archive.java              ← Archive-Interface
│  ├─ Channel.java              ← Channel-Interface
│  ├─ Payable.java              ← Payment-Interface
...   ...

```

Die Architektur nutzt hier konsequent das **Interface-Segregation-Prinzip**:

* `Archive`: Das abstrakte Gedächtnis. Es definiert, wie Informationen aufbewahrt werden, ohne sich auf eine Datenbank-Technologie festzulegen.
* `Channel`: Die Leitung nach draußen. Ob Telefon, Funk oder API – ein `Channel` ermöglicht die Kommunikation mit der Außenwelt.
* `Payable`: Die finanzielle Infrastruktur, die den Austausch von Werten ermöglicht.

**Die Detail-Pakete (Implementierungen)**

In den Unterpaketen wie `archive/`, `payment/` oder `phone/` liegen die „schweren“ Abhängigkeiten. Hier finden wir:

* **DTOs (Data Transfer Objects):** Klassen wie `RecordArchive` oder `StripeRequest` sind rein technische Container. Sie transportieren Daten über Systemgrenzen hinweg und haben keine fachliche Logik – sie sind die Frachtbriefe der Bühnentechnik.
* **Spezifische Clients**: Die `StripeApi` oder das `Phone` sind die tatsächlichen technischen Geräte, die wir für die Inszenierung eingekauft haben.

Durch diese Struktur erreichen wir, dass die Domäne (das Drehbuch) nur gegen die Interfaces in `backstage/` programmiert wird. Ob im Hintergrund ein Headset (im Paket `phone/`) oder eine DatabaseArchive (im Paket `archive/`) schuftet, bleibt für die Fachlogik vollkommen transparent.

### **6.1 Das Datenbank-Archiv**

Das `DatabaseArchive` ist der Tresor unseres Theaters. Akteure müssen sich nicht mit SQL-Verbindungen belasten; sie übergeben ihren Zustand dem Archiv, das die technische Abwicklung im Hintergrund übernimmt.

```java
package manuscript.backstage.archive;

import manuscript.Actor;

public class DatabaseArchive implements Archive {
    
    private final String connectionString;
    
    public DatabaseArchive(String connectionString) {
        this.connectionString = connectionString;
        System.out.println("🗄️  Datenbank-Archiv verbunden: " + connectionString);
    }
    
    public Actor load(String name) {
        // Simuliere Datenbank-Laden
        System.out.println("📂 [DB] Lade " + name + " aus der Datenbank...");
        return null; // In einer echten Implementierung würde hier ein Actor zurückgegeben
    }

    @Override
    public void save(Actor actor) {
        // Simuliere Datenbank-Speicherung
        // Das Archiv reicht dem Akteur ein leeres Blatt (RecordMedia)
        RecordMedia record = new RecordMedia();
        
        // Der Akteur "spricht" seine Daten auf das Blatt
        actor.speak(record);
        
        // Das Archiv speichert das Ergebnis
        System.out.println("⚙️ [BACKSTAGE] Archiviert: " + record.getContent());
    }

     /**
     * Ein privates Medium, das nur innerhalb des Archivs existiert,
     * um die Daten des Akteurs entgegenzunehmen.
     */
    private static class RecordMedia implements Media {

        private final Map<String, Object> data = new HashMap<>(0);

        @Override
        public Media with(String name, String value) {
            data.put(name, value);
            return this;
        }

        // ... weitere with Methoden für BigDecimal, LocalDate etc.

        public String getContent() {
            return data.toString();
        }
    }
}
```

### **6.2 Das Payment-Interface**

Hier sehen wir das **Adapter-Pattern** in seiner reinsten Form. Wir möchten die Hochzeit nicht mit Details einer Kreditkarten-Abrechnung belasten.

* `Payable`: Die fachliche Anforderung („Jemand muss bezahlen können“).
* `StripeApi`: Das technische Fremdsystem, das eine völlig andere Sprache spricht.
* `StripePayment`: Unser Dolmetscher. Er ist gleichzeitig ein Outfit (Maske) für den Akteur und ein Payable (Zahlungsdienst). Er übersetzt den fachlichen Wunsch in einen API-Aufruf.

```java
package manuscript.backstage;

public interface Payable {
    /** Führt eine Zahlung durch. */  
    void pay(double amount);
}
```

### **6.3 Die Stripe API**

`StripeApi` ist die technische Schnittstelle zur Stripe-API. Diese Klasse kennt nur die technische Sprache von Stripe, nicht die Fachdomäne.

```java
package manuscript.backstage.payment;

public class StripeApi {
    
    public void executeTransaction(double amount, String accountId) {
        System.out.println("💳 [Stripe API] Transaktion wird ausgeführt...");
        System.out.println("💳 [Stripe API] Betrag: €" + amount);
        System.out.println("💳 [Stripe API] Konto: " + accountId);
        System.out.println("💳 [Stripe API] Transaktion erfolgreich!");
    }
}
```

### **6.4 Der Stripe Payment Adapter**

`StripePayment`-Adapter übersetzt die fachliche `pay()`-Methode in die technische `executeTransaction()` der Stripe-API.

```java
package manuscript.backstage.payment;

import manuscript.Actor;
import manuscript.Outfit;

public class StripePayment extends Outfit implements Payable {
    
    private final StripeApi stripeApi;
    
    public StripePayment(Actor actor, StripeApi stripeApi) {
        super(actor);
        this.stripeApi = stripeApi;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("💰 " + name() + " initiiert eine Zahlung von €" + amount);
        String accountId = "stripe_account_" + name().value().toLowerCase();
        stripeApi.executeTransaction(amount, accountId);
    }
}
```

## **7. Die Bühne (Stage)**

Die Bühne ist das Präsentationskonzept unserer Anwendung. Hier wird die Handlung für den Zuschauer (Benutzer) sichtbar gemacht. Das Entscheidende: Die Bühne nutzt die Fachlogik, verändert sie aber nicht. Sie ist der Ort, an dem Requisiten (Accessories) platziert und Einstellungen (Takes) vorgenommen werden.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_08_code_like_movie.png)


```
manuscript/
...
├─ stage/  
│  ├─ accessory/  
│  │  ├─ SpeechBubble.java         ← Sprechblase für Ausgaben  
│  │  ├─ ActionButton.java         ← Interaktiver Button  
│  │  └─ CinematicGrid.java        ← Visuelles Raster  
│  ├─ take/  
│  │   └─ WeddingCloseUp.java       ← Nahaufnahme der Hochzeit
...
```

### **7.1 Die Requisiten der Darstellung (Accessories)**

Um dem Zuschauer Informationen zu vermitteln, benötigt die Bühne Werkzeuge. Diese haben keine eigene Business-Logik, sondern dienen rein der Interaktion und Darstellung:

* `SpeechBubble`: Ein visuelles Element, um Dialoge oder Nachrichten hervorzuheben.
* `CinematicGrid`: Sorgt für die visuelle Ordnung und Struktur auf dem Bildschirm.
* `ActionButton`: Die Brücke zwischen Zuschauer und Handlung. Ein Klick löst eine fachliche Szene aus – die UI stößt den Prozess an, führt ihn aber nicht selbst aus.

### **7.1.1 Die Sprechblase**

Die `SpeechBubble` repräsentiert eine visuelle Sprechblase auf der Bühne.

```java
package manuscript.stage.accessory;

public class SpeechBubble {
    
    private final String message;
    
    public SpeechBubble(String message) {
        this.message = message;
    }
    
    public void display() {
        System.out.println("💬 [" + message + "]");
    }

    public void display(Media media) {
        System.out.println("💬 [" + message + "]");
    }
}
```

### **7.1.2 Der Action Button**

Der `ActionButton` ist ein interaktiver Button auf der Bühne, der eine Szene auslöst, wenn er betätigt wird.

```java
package manuscript.stage.accessory;

import manuscript.Scene;

public class ActionButton {
    
    private final String label;
    private final Scene action;
    
    public ActionButton(String label, Scene action) {
        this.label = label;
        this.action = action;
    }
    
    public void click() {
        System.out.println("🔘 Button geklickt: " + label);
        action.execute();
    }
}
```

### **7.1.3 Das Cinematic Grid**

Das `CinematicGrid` ist ein visuelles Raster für die Darstellung von Szenen.

```java
package manuscript.stage.accessory;
 
public class CinematicGrid {
    
    private final int columns;
    
    public CinematicGrid(int columns) {
        this.columns = columns;
    }
    
    public void render() {
        System.out.println("🎞️  [Cinematic Grid wird gerendert mit " + columns + " Spalten]");
    }
}
```

### **7.2 Die Kameraführung (Takes)**

Unter `stage/take/` definieren wir spezifische Kameraperspektiven. Ein Take kombiniert Akteure mit visuellen Elementen, um einen bestimmten Moment der Geschichte einzufangen. 

* `WeddingCloseUp`: Hier wird die Kamera auf das Brautpaar gerichtet. Es zeigt, wie die UI-Ebene die Domänen-Objekte (`Groom`, `Bride`) nutzt, um sie innerhalb eines Rasters (`CinematicGrid`) zu inszenieren.

```java
package manuscript.stage.take;

import manuscript.character.Groom;
import manuscript.character.Bride;
import manuscript.stage.accessory.CinematicGrid;

public class WeddingCloseUp {
    
    private final Groom groom;
    private final Bride bride;
    private final CinematicGrid grid;
    
    public WeddingCloseUp(Groom groom, Bride bride, CinematicGrid grid) {
        this.groom = groom;
        this.bride = bride;
        this.grid = grid;
    }
    
    public void render() {
        System.out.println("\n🎥 === NAHAUFNAHME: DIE HOCHZEIT ===");
        grid.render();
        System.out.println("📸 Kamera fokussiert auf " + groom.name() + " und " + bride.name());
        groom.perform();
        bride.perform();
        System.out.println("🎥 === ENDE DER NAHAUFNAHME ===\n");
    }
}
```

## 7.3 Die Selbstdarstellung der Akteure (Die Bühne als Medium)

In einer herkömmlichen Architektur zieht sich die Benutzeroberfläche die Daten mittels Getter aus den Objekten. In der Spielfilmphilosophie bleibt die Autonomie der Akteure gewahrt: Der Akteur präsentiert sich selbst, indem er zu einem Medium spricht. Die Screen Komponente ist dabei lediglich eine spezielle technische Realisierung des Media Interfaces

### 7.3.1 Die Screen Komponente (Screen.java)

Die Screen Komponente fungiert als fachliches Medium der Bühne. Sie empfängt die Erzählung der Akteure und transformiert sie in eine visuelle Darstellung, ohne dass der Akteur die Details der Anzeige kennen muss.
```java
package manuscript.stage;

import manuscript.Media;
import java.math.BigDecimal;

/**
 * Das visuelle Medium der Bühne.
 * Es empfängt die Daten des Akteurs und bringt sie zur Anzeige.
 */
public class Screen implements Media {

    @Override
    public Media with(String name, String value) {
        System.out.println("📺 [SCREEN] " + name + ": " + value);
        return this;
    }

    @Override
    public Media with(String name, BigDecimal value) {
        System.out.println("📺 [SCREEN] " + name + " (Zahl): " + value);
        return this;
    }
    
    // ... Implementierung weiterer with Methoden
}

```

### 7.3.2 Die Inszenierung der Präsentation

Anstatt dass die Bühne den Akteur ausliest, fordert die Regie den Akteur auf, sich auf der bereitgestellten Leinwand zu präsentieren. Dies folgt strikt der Regieanweisung **Tell Don’t Ask**.

```java
// Innerhalb einer Szene oder der Regie
Screen stageScreen = new Screen();
Groom romeo = new Groom("Romeo");

// Der Akteur artikuliert sich gegenüber der Leinwand
romeo.speak(stageScreen);
```

## 7.4 Datenanimation: Der Akteur als sein eigener Szenenbildner

In klassischen Systemen zieht die Präsentationsebene Daten aus passiven Objekten heraus, um sie anzuzeigen. In der cineastischen Architektur übernimmt der Akteur die Regie über seine eigene Darstellung. Das Objekt fungiert nicht als Datenspeicher, sondern als Repräsentant seiner Identität.



## **8. Die Regie (Directing)**

Die Regie ist der **Composition Root** der Anwendung. Hier werden alle Fäden zusammengeführt: Die *Charaktere* werden gecastet, die *Outfits* werden angezogen, die *Bühnentechnik* wird aufgebaut und die *Szenen* werden in die Filmrolle eingefügt.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_09_code_like_movie.png)

```
manuscript/
...
├─ directing/  
│   └─ InstalledMovie.java      ← Die Implementierung von Composition Root
...
├─ Movie.java                   ← Der Composition Root
...
```

Um dem Leser einen direkten Einstieg in den Ablauf der Geschichte zu ermöglichen, wird der Composition Root als Interface direkt im Root-Paket fachlich eingeführt. Das `Movie`-Interface repräsentiert dabei die Anwendung selbst. Die technische Realisierung erfolgt im Paket `directing/`, wo alle Fäden der Inszenierung zusammenlaufen.

### **8.1 Der Regisseur (Composition Root)**

Die `InstalledMovie` Klassen realisiert den *Composition Root* der gesamten Anwendung. Hier wird das komplette Drehbuch zusammengestellt und die Filmrolle befüllt. Erst an dieser stelle wird entschieden, welche konkreten technischen Implementierungen (z. B. Stripe oder SQL) für die abstrakten Rollen im Drehbuch eingesetzt werden.

```java
package manuscript.directing;

import manuscript.Movie;
import manuscript.Scene;
import manuscript.Actor;

// Charaktere
import manuscript.character.Groom;
import manuscript.character.Bride;
import manuscript.character.Newborn;

// Outfits
import manuscript.outfit.SuitedActor;
import manuscript.outfit.PersistentActor;

// Backstage
import manuscript.backstage.DatabaseArchive;
import manuscript.backstage.payment.StripeApiClient;
import manuscript.backstage.payment.StripePaymentAdapter;

// Akte
import manuscript.wedding.WeddingCeremony;
import manuscript.wedding.PayTheBand;
import manuscript.wedding.LazyVideo;
import manuscript.wedding.Video;
import manuscript.wedding.LoveStoryFlashback;

import manuscript.honeymoon.HotelCheckIn;
import manuscript.honeymoon.SunsetDinner;

import manuscript.birth.Hospital;
import manuscript.birth.BirthScene;

import manuscript.familylife.MorningRoutine;
import manuscript.familylife.Bedtime;

// Stage
import manuscript.stage.take.WeddingCloseUp;
import manuscript.stage.accessory.SpeechBubble;
import manuscript.stage.accessory.CinematicGrid;

import java.util.LinkedList;
import java.util.Queue;

/**  
 * Der Composition Root der gesamten Anwendung.
 */  
public class InstalledMovie implements Movie {

    // 1. DIE BÜHNENTECHNIK (Backstage)  
    private final DatabaseArchive archive = new DatabaseArchive("jdbc:cinema://localhost/drehbuch");
    private final StripeApi stripe = new StripeApi();
      
    // Die Filmrolle  
    private final Queue<Scene> filmRoll = new LinkedList<>();

    public static void main(String[] args) {
        Movie movie = new InstalledMovie();
        movie.start();
        movie.nextScene();
    }

    @Override
    public void start() {
        System.out.println("🎥 Ruhe bitte! Kamera läuft, Ton läuft...");
        System.out.println("🎬 Film ab: Die Vorführung beginnt!");

        // ============================================================
        // DAS CASTING & DIE MASKE (Vorbereitung der Akteure)
        // ============================================================
        Groom groom = new Groom("Romeo");
        Bride bride = new Bride("Julia");
        Newborn newborn = new Newborn("Luna");

        Actor suitedGroom = new SuitedActor(groom);
        StripePayment groomWithCard = new StripePayment(suitedGroom, stripe);
        Actor persistentBride = new PersistentActor(bride, archive);

        // ============================================================
        // DIE INSZENIERUNG DER SZENEN (Die Filmrolle wird befüllt)
        // ============================================================

        // AKT 1: Die Hochzeit
        // Hier wird die atomare Szene (RingExchange) in die Rahmenszene injiziert
        Scene ringtausch = new RingExchange(romeo, julia);
        filmRoll.add(new WeddingCeremony(groom, bride, ringtausch));
        filmRoll.add(new PayTheBand(groomWithCard, 500.00));
        
        // Video-Rückblick (mit Proxy-Pattern)
        Video lazyVideo = new LazyVideo("liebesgeschichte_hd.mp4");
        SpeechBubble screen = new SpeechBubble("Leinwand bereit...");
        filmRoll.add(new LoveStoryFlashback(lazyVideo, screen));

        // Visuelle Nahaufnahme
        CinematicGrid grid = new CinematicGrid(12);
        WeddingCloseUp visualTake = new WeddingCloseUp(groom, bride, grid);
        filmRoll.add(visualTake::render);

        // AKT 2: Die Flitterwochen
        filmRoll.add(new HotelCheckIn(groom, bride));
        filmRoll.add(new SunsetDinner(groom, bride));

        // AKT 3: Die Geburt
        Hospital hospital = new Hospital("St. Mary's Klinik");
        filmRoll.add(new BirthScene(bride, newborn, hospital));

        // AKT 4: Das Familienleben
        filmRoll.add(new MorningRoutine(groom, bride, newborn));
        filmRoll.add(new Bedtime(groom, newborn));
    }

    @Override
    public void nextScene() {
        while (!filmRoll.isEmpty()) {
            Scene currentScene = filmRoll.poll();
            System.out.println("\n🎞️ Nächste Szene wird abgespielt...");
            currentScene.execute();
        }
        System.out.println("\n🎬 Ende im Kasten! Vielen Dank an alle Beteiligten.");
    }
}
```

### **8.2 Schlüsselelemente der Regie**

Was macht diesen "Regisseur" so besonders? Er hält sich strikt an die Trennung von **Konstruktion** und **Laufzeit**:

* **Die main als Kinosaal:** Die `main()`-Methode ist frei von jeglicher Logik. Sie drückt nur auf den „Play"-Knopf.
* **Die Filmrolle als Queue:** Durch das Hinzufügen der Szenen in eine Queue (Warteschlange) im `start()`-Prozess wird die Reihenfolge der Chronologie festgeschrieben. `nextScene()` rattert diese einfach herunter, ohne die konkreten Szenen-Inhalte kennen zu müssen.
* **Volle Kapselung:** Selbst in der Hauptschleife bleibt das System blind für technische Details. Es kennt nur den Befehl `execute()` der jeweiligen Szene.

**Das Ergebnis auf der Leinwand**

Wenn du die Applikation ausführst, wird die Konsole die Chronologie des Drehbuchs exakt wie ein Drehbuch-Protokoll ausgeben:

1. **Szene 1:** Die Trauung beginnt. Während *Romeo* und *Julia* sich das Versprechen geben, sorgt das `PersistentActor`-Kostüm lautlos dafür, dass der Moment im `DatabaseArchive` verewigt wird.
2. **Szene 2:** Die Band will bezahlt werden. Der StripePaymentAdapter übersetzt Romeos fachliches pay() in die technische Sprache `executeTransaction()` der Stripe-API.
3. **Szene 3:** Der Video-Rückblick startet. Dank des **Proxy-Patterns** wird der ressourcenfressende Film erst in dem Moment geladen, in dem der Souffleur die Leinwand bereitstellt.
4. **Akte 2 bis 4:** Das Leben entfaltet sich - von der Klinik bis zur Guten-Nacht-Geschichte - sauber getrennt in Akte und Szenen. Die weiteren Akte der Lebensgeschichte laufen nacheinander ab.

## **9. Der cineastische Stacktrace**

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

## **10. Bauplan als Drehbuch**

Um eine fachliche und progressive Erzählstruktur im Code zu gewährleisten, bedarf es einer klaren Trennung zwischen der Art der Kommunikation und der physischen Organisation des Projekts.

## **10.1 Die leitenden Prinzipien (Die Regieanweisung)**

Diese Prinzipien definieren, wie Akteure innerhalb des Systems interagieren, um die Kapselung und Autonomie zu wahren:

**Graphen als Skript:** Die Modellierung des Systems erfolgt über drei Dimensionen. Der *Kollaborationsgraph* bildet das Ensemble ab, der *Konstruktionsgraph* regelt den Setaufbau und der *Aufrufgraph* beschreibt den Dialog der Objekte zur Laufzeit.

**Tell Don’t Ask:** Interaktionen erfolgen ausschließlich über fachliche Befehle. Ein Objekt wird niemals nach seinem Zustand gefragt, um externe Entscheidungen zu treffen; es wird direkt zum Handeln aufgefordert.

**Law of Demeter:** Akteure kommunizieren nur mit ihren unmittelbaren Nachbarn. Diese Beschränkung der Zugriffspfade garantiert eine lose Kopplung und schützt die Inszenierung vor semantischer Instabilität.

## **10.2 Regeln für die Projektstruktur (Das Inhaltsverzeichnis)**

Damit die Geschichte für den Leser auffindbar bleibt, folgt die Paketierung fachlichen statt technischen Kriterien:

**Domänenzentrierung:** Die Paketstruktur orientiert sich nicht an technischen Schichten (wie *Service* oder *Repository*), sondern folgt hierarchisch den fachlichen Domänenkonzepten.

**Fokus auf Ebene Null:** Die oberste Paketebene enthält ausschließlich den fachlichen Kern. Dazu gehören *Schnittstellen*, *abstrakte Klassen*, *Value Objects*, *Entitäten* und das zentrale *Systeminterface* als Einstiegspunkt der Anwendung. Diese Ebene bleibt frei von technischem Ballast.

**Trennung der Realisierung:** Alle konkreten Implementierungen und technischen Details (wie *Infrastruktur* und *Benutzerpresäntation*) erfolgen strikt getrennt in untergeordneten Paketen.

**Hierarchie ohne Zyklen:** Ein übergeordnetes Paket darf niemals von seinen Unterpaketen abhängen. Zyklische Abhängigkeiten sind strikt auszuschließen, um einen gerichteten Informationsfluss zu wahren. Die Verwendung von Unterpaketen ist zulässig, sofern sie nicht auf derselben Hierarchieebene liegen.

**Detailverfeinerung:** Unterpakete führen keine neuen Fachkonzepte ein. Sie dienen lediglich der inhaltlichen Ausgestaltung der Konzepte aus der übergeordneten Ebene.

**Fachsprache (Ubiquitous Language):** Die Benennung von *Paketen*, *Klassen* und *Methoden* folgt der Sprache der realen Fachwelt, nicht der technischen Umsetzung.

## **11. Schlusswort: Code, der Geschichten erzählt**

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
