# **🎞️ Software als Spielfilm-Drehbuch**

Das Aufkommen von Künstlicher Intelligenz (KI) wie ChatGPT, Claude oder GitHub Copilot hat die Softwareentwicklung grundlegend verändert. Die alte Faustregel „zehnmal mehr lesen als schreiben“ gilt heute stärker denn je — nur dass wir inzwischen nicht mehr unseren eigenen Code lesen, sondern vor allem den von Maschinen generierten. Um diese Flut überhaupt noch menschlich erfassen und beherrschen zu können, braucht es eine neue Perspektive auf Softwarearchitektur.

Computer können beliebige Datenstrukturen verarbeiten. Menschen dagegen verstehen die Welt über Geschichten. Deshalb liegt es nahe, auch unseren Code wie das Drehbuch eines Spielfilms zu gestalten.

Während klassische Architekturmodelle Software als technische Schichtenlandschaft aus zustandslosen Services und passiven Datencontainern betrachten, inszeniert die Spielfilm‑Philosophie ein objektorientiertes System als Ensemble intelligenter Akteure. Diese Akteure treten in Szenen auf, handeln in Akten und treiben die Geschichte durch ihr Verhalten voran.

**Drehbuchs als Bauanleitung**

Im Sinne eines Drehbuchs als Bauanleitung lässt sich die fachliche Domäne über drei zentrale Graphen beschreiben — als strukturierte Erzählung eines Spielfilms:

**Der Konstruktions-Graph (Die Regie / Der Set-Aufbau):** Dieser Graph zeigt den Aufbau des Sets durch den Composition Root. Hier wird die „Bühnentechnik“ (Infrastruktur) aufgebaut, bevor die Kamera rollt. Im Fokus steht das statische Objektgerüst (Wer besitzt wen?). In der Fachlogik selbst ist dieser Graph unsichtbar.

**Der Kollaborations-Graph (Das Ensemble):** Er zeigt, welche Charaktere dauerhaft miteinander verbunden sind. Das entspricht der Besetzung der Hauptrollen und ihren Beziehungen im Drehbuch. Hier steht die statische Vernetzung im Vordergrund (Wer kennt wen und kann miteinander kommunizieren?).

**Der Aufruf-Graph (Die Handlung):** Dies ist der flüchtige Moment der Interaktion – der Dialog zwischen den Akteuren während des Ablaufs der Szenen. Hier wird sichtbar, wie zur Laufzeit Nachrichten ausgetauscht werden (Wer ruft gerade welche Methode auf?).

Das **„Tell, Don’t Ask“-Prinzip** fungiert dabei als zentrale Regieanweisung: Objekte sollen nicht befragt werden wie Datencontainer, sondern als autonome Charaktere handeln, die ihre Aufgaben selbst lösen.

Damit bei der Umsetzung des Drehbuchs im Projekt keine Verwirrungen entstehen, müssen die Akte und Szenen in einer systematisch nachvollziehbaren Struktur organisiert sein. Dafür gelten drei pragmatische Regeln für die Paket-Struktur:

1. **Abhängigkeiten:** Pakete dürfen nicht von ihren Unterpaketen abhängen und keine Zirkelbezüge bilden.
2. **Detailverfeinerung:** Unterpakete führen keine neuen Konzepte ein, sondern konkretisieren nur bestehende.
3. **Fachsprache:** Pakete spiegeln die Sprache der Domäne wider (Ubiquitous Language), nicht die technische Sprache der Umsetzer.

# **🎬 Die cineastische Projektstruktur**

Die Struktur des Projekts ist die sichtbare Form des Drehbuchs. Sie trennt die fachliche Identität von der technischen Umsetzung und macht die Inszenierung der Anwendung auf den ersten Blick verständlich.

Wer das Manuskript öffnet, sieht keine technische Schablone, sondern das Drehbuch einer individuellen Geschichte, bereit für die Produktion in einem Filmstudio:


Die Struktur des Projekts ist die physische Manifestation des Drehbuchs. Sie trennt die fachliche Identität von der technischen Realisierung und macht die Inszenierung der Anwendung auf den ersten Blick lesbar.

Wer das Manuskript öffnet, sieht keine technische Schablone, sondern ein Drehbuch einer individuellen Geschichte – einsatzbereit für ein Filmstudio:

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
└─ Movie.java         ← DREHBUCH (Der rote Faden / Einstiegspunkt)
```

Ein Teammitglied (Investor, Fachexperte, Architekt oder Entwickler), das dieses Projekt öffnet, muss nicht raten, worum es geht - es liest das Inhaltsverzeichnis einer Lebensgeschichte.

**Die Bestandteile der Inszenierung**

* **Die Domänen-Ebene (Wurzelverzeichnis):** Das Movie-Interface gibt die Gesamthandlung vor. Actor bildet das zentrale Interface für alle agierenden Figuren. Name fungiert als unveränderliches Requisit (*Value Object*), während Scene den Kontext für flüchtige Interaktionen definiert.  
* **Die Akte (Chronologische Prozesse):** Die Ordner wedding, honeymoon, birth und familylife spiegeln die tatsächlichen Phasen der Geschäftslogik (bzw. der Lebensgeschichte) wider. Jeder Ordner kapselt die Logik, die für diesen spezifischen Lebensabschnitt der Applikation notwendig ist.  
* **Die Charaktere & Outfits:** Unter character liegen die konkreten Ausprägungen der Akteure. Der Ordner outfit beherbergt die Kleidung, die den Figuren je nach Szene neue Fähigkeiten verleihen, ohne deren innere Identität zu verändern.  
* **Backstage:** Die technische Infrastruktur bleibt als dienende Technik strikt in der backstage verborgen.  
* **Stage:** Die Benutzeroberfläche agiert als stage im Rampenlicht und nimmt die Vorführung für den Zuschauer auf.  
* **Directing:** In diesem Paket fließen die Fäden zusammen. Hier wird entschieden, welche Figuren in welcher Szene aufeinandertreffen.

**Die Abhängigkeiten der Inszenierung**

Die **Domänen Ebene** ist der Kern und hängt von niemandem. Die fachlichen Konzepte als **Unterpakete** wie *Akte*, *Charaktere* und *Outfits* bauen auf der Domänen Ebene und auf **Backstage** auf und sind untereinander unabhängig. **Backstage** liefert Technik und hängt von der Domänen Ebene. **Stage** nutzt Domäne, Akte, Charaktere, Outfits und Backstage. **Directing** umschliesst alles, hängt von allen Ebenen und orchestriert die Inszenierung; niemand darf von Directing abhängen.

Die **Domänen Ebene** bildet den Kern und ist von niemandem abhängig. Akte sowie Charaktere und Outfits bauen auf der Domänen Ebene und auf Backstage auf, bleiben aber untereinander unabhängig. **Backstage** liefert die technische Basis und darf selbst nur von der Domänen Ebene abhängig sein. Die **Stage** nutzt Domäne, Akte, Charaktere, Outfits und Backstage für die Darstellung, während niemand von der Stage abhängig ist. **Directing** umschließt alle Ebenen und orchestriert die gesamte Inszenierung, zugleich darf keine andere Ebene von Directing abhängig sein.


## **1. Das Ensemble und die Requisiten (Die Domänen-Ebene)**

Das Herzstück der Architektur bildet die fachliche Identität auf Ebene Null. Hier werden die Hauptcharaktere als Interfaces, der Handlungsrahmen als Szenen sowie ihre Attribute als Requisiten (Value Objects) definiert. Diese Ebene ist frei von technischem Rauschen und macht die Essenz des Systems sofort greifbar.

```
manuscript/  
├─ Actor.java         ← AKTEUR (Zentrales Interface)  
├─ Name.java          ← REQUISITE (Präzise Fachwerte / Value Object)  
├─ Outfit.java        ← AUSSTATTUNG (Basis-Decorator)  
├─ Scene.java         ← SZENE (Das Interaktionsprotokoll)  
└─ Movie.java         ← DREHBUCH (Der rote Faden / Einstiegspunkt)
```

### **1.1 Das Scene.java Interface**

Das Scene-Interface definiert den kleinsten Handlungsrahmen im Drehbuch. Jede Szene kapselt eine spezifische fachliche Interaktion.

```java
package manuscript;

/**  
 * Das Scene-Interface definiert den kleinsten Handlungsrahmen im Drehbuch.  
 * Jede Szene kapselt eine spezifische fachliche Interaktion.  
 */  
@FunctionalInterface  
public interface Scene {

    /**  
     * Führt die Szene aus.   
     * Hier entfaltet sich der Dialog und die Interaktion zwischen den Akteuren.  
     */  
    void execute();  
}
```

### **1.2 Die fertige Montage: Movie.java**

Damit das Ganze funktioniert, sieht das zentrale **Movie.java** Interface wie folgt aus:

```java
package manuscript;

public interface Movie {  
    /** Startet die Vorführung und befüllt die Filmrolle. */  
    void start();

    /** Spielt alle geladenen Szenen nacheinander ab. */  
    void nextScene();  
}
```

### **1.3 Der Actor als zentrale Figur**

Das **Actor**-Interface ist die Hauptrolle im Drehbuch. Alle handelnden Figuren müssen dieses Interface implementieren.

```java
package manuscript;

/**  
 * Das Actor-Interface repräsentiert jeden handelnden Charakter im System.  
 * Es ist das zentrale Abstraktum, das alle Akteure auf ihre fachlichen Fähigkeiten reduziert.  
 */  
public interface Actor {
    
    /**  
     * Gibt den Namen des Akteurs zurück.  
     * @return Name als unveränderliches Value Object  
     */  
    Name name();
    
    /**  
     * Fordert den Akteur auf, eine bestimmte Handlung auszuführen.  
     * Dies ist der primäre Kommunikationskanal im Drehbuch.  
     */  
    void perform();
}
```

### **1.4 Das Name Value Object**

Das **Name**-Value Object ist ein unveränderliches Requisit, das den Namen eines Akteurs repräsentiert.

```java
package manuscript;

/**  
 * Name repräsentiert einen unveränderlichen Namen als Value Object.  
 * Zwei Namen sind gleich, wenn ihre Werte identisch sind.  
 */  
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

### **1.5 Das Outfit als Basis-Decorator**

Das **Outfit** ist die Basis für alle Decorator-Implementierungen. Es erlaubt es, Akteuren zusätzliche Fähigkeiten zu verleihen, ohne ihre Identität zu verändern.

```java
package manuscript;

/**  
 * Outfit ist die Basis für alle Decorator.  
 * Es fügt einem Actor zusätzliche Fähigkeiten hinzu,  
 * ohne dessen Kernidentität zu verändern.  
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

## **2. Die Charaktere (Das Casting)**

In diesem Paket werden die grundlegenden Persönlichkeiten und Rollenprofile der Anwendung definiert. Es beschreibt die konkreten Figuren, die im Laufe der Geschichte auftreten können, noch ohne spezifische Verkleidung oder technische Bindung.

```
manuscript/  
└─ character/  
   ├─ Groom.java               ← Der Bräutigam  
   ├─ Bride.java               ← Die Braut  
   └─ Newborn.java             ← Das Neugeborene
```

### **2.1 Der Bräutigam (Groom.java)**

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
}
```

### **2.2 Die Braut (Bride.java)**

```java
package manuscript.character;

import manuscript.Actor;
import manuscript.Name;

/**  
 * Die Braut ist die zweite Hauptfigur in der Hochzeitsgeschichte.  
 */  
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
}
```

### **2.3 Das Neugeborene (Newborn.java)**

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
        System.out.println("😭 " + name + " weint laut!");
    }
}
```

## **3. Die Maskenbildnerei (Das Outfit)**

Hier liegen die Decorator der Architektur. Ein Outfit fungiert als Objekt in Verkleidung. Wie ein Schauspieler schlüpft die Figur in ein Kostüm (z. B. für die Persistenz oder Validierung), um zusätzliche Fähigkeiten zu simulieren, wobei sie dieselbe Person bleibt und denselben fachlichen Text spricht.

```
manuscript/  
└─ outfit/  
   ├─ SuitedActor.java         ← Actor im Hochzeitsanzug  
   ├─ PersistentActor.java     ← Actor mit Datenbank-Anbindung  
   └─ ValidatedActor.java      ← Actor mit Regelprüfung
```

### **3.1 Der Suited Actor (SuitedActor.java)**

```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;

/**  
 * SuitedActor repräsentiert einen Akteur im Hochzeitsanzug.  
 * Er behält seine Identität, tritt aber formeller auf.  
 */  
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

### **3.2 Der Persistent Actor (PersistentActor.java)**

```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;
import manuscript.backstage.DatabaseArchive;

/**  
 * PersistentActor versieht einen Actor mit Persistenz-Fähigkeiten.  
 * Nach jeder Handlung wird der Zustand in der Datenbank gespeichert.  
 */  
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

### **3.3 Der Validated Actor (ValidatedActor.java)**

```java
package manuscript.outfit;

import manuscript.Actor;
import manuscript.Outfit;

/**  
 * ValidatedActor prüft vor jeder Handlung,  
 * ob die Voraussetzungen erfüllt sind.  
 */  
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

## **4. Die Akte der Dramaturgie (Die Fachprozesse)**

In den chronologischen Paketen findet die schrittweise Realisierung der Anforderungen statt. Hier werden die abstrakten Figuren für konkrete Szenarien ausgestattet und in den Dialog geschickt. Das *Tell, Don't Ask*-Prinzip sorgt in diesen Paketen für eine zielgerichtete Handlung ohne technisches Rauschen.

```
manuscript/  
├─ wedding/  
│  ├─ WeddingCeremony.java     ← Die Trauungs-Szene  
│  ├─ PayTheBand.java          ← Das Bezahlen der Band  
│  ├─ RingExchange.java        ← Ringtausch Interaktion  
│  ├─ RealVideo.java           ← Film-Implementierung   
│  ├─ LoveStoryFlashback.java  ← Das Abspielen eines Films   
│  ├─ VideoProxy.java          ← Verzögertes Laden des Films  
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
└─ familylife/  
   ├─ MorningRoutine.java      ← Der Alltag  
   └─ Bedtime.java             ← Die Gute-Nacht-Geschichte
```

### **4.1 AKT 1: Die Hochzeit**

#### **4.1.1 Die Trauungs-Szene (WeddingCeremony.java)**

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;

/**  
 * WeddingCeremony orchestriert die Trauungszeremonie.  
 * Hier treffen Bräutigam und Braut aufeinander und geben ihr Eheversprechen ab.  
 */  
public class WeddingCeremony implements Scene {
    
    private final Groom groom;
    private final Bride bride;
    
    public WeddingCeremony(Groom groom, Bride bride) {
        this.groom = groom;
        this.bride = bride;
    }
    
    @Override
    public void execute() {
        System.out.println("\n💒 === DIE TRAUUNGSZEREMONIE BEGINNT ===");
        
        groom.perform();
        bride.perform();
        
        groom.giveRing();
        bride.receiveRing();
        
        System.out.println("💒 === DIE TRAUUNG IST VOLLZOGEN ===\n");
    }
}
```

#### **4.1.2 Der Ringtausch (RingExchange.java)**

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;

/**  
 * RingExchange ist eine eigenständige Szene, die ausschließlich  
 * den Austausch der Ringe zwischen Braut und Bräutigam darstellt.  
 */  
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

#### **4.1.3 Das Video-Interface (Video.java)**

```java
package manuscript.wedding;

/**  
 * Video repräsentiert einen Film, der während der Hochzeit abgespielt wird.  
 * Dies ist eine fachliche Abstraktion, unabhängig von der technischen Quelle.  
 */  
public interface Video {
    
    /**  
     * Lädt das Video in den Arbeitsspeicher.  
     */  
    void load();
    
    /**  
     * Spielt das Video ab.  
     */  
    void play();
}
```

#### **4.1.4 Das echte Video (RealVideo.java)**

```java
package manuscript.wedding;

/**  
 * RealVideo ist die konkrete Implementierung eines Videos.  
 * Das Laden verbraucht viele Ressourcen, daher sollte es nur bei Bedarf geschehen.  
 */  
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

#### **4.1.5 Das Video-Proxy (VideoProxy.java)**

```java
package manuscript.wedding;

/**  
 * VideoProxy ist ein Lazy-Loading-Proxy für Videos.  
 * Das eigentliche Video wird erst geladen, wenn es tatsächlich abgespielt wird.  
 */  
public class VideoProxy implements Video {
    
    private final String filename;
    private RealVideo realVideo;
    
    public VideoProxy(String filename) {
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

#### **4.1.6 Die Liebesgeschichten-Rückblende (LoveStoryFlashback.java)**

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.stage.accessory.SpeechBubble;

/**  
 * LoveStoryFlashback spielt einen Video-Rückblick ab,  
 * der die Liebesgeschichte des Paares zeigt.  
 */  
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

#### **4.1.7 Die Band bezahlen (PayTheBand.java)**

```java
package manuscript.wedding;

import manuscript.Scene;
import manuscript.backstage.payment.Payable;

/**  
 * PayTheBand repräsentiert die Szene, in der die Hochzeitsband bezahlt wird.  
 * Dies demonstriert den Adapter-Pattern für externe Zahlungssysteme.  
 */  
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

### **4.2 AKT 2: Die Flitterwochen**

#### **4.2.1 Hotel Check-In (HotelCheckIn.java)**

```java
package manuscript.honeymoon;

import manuscript.Scene;
import manuscript.Actor;

/**  
 * HotelCheckIn ist die Szene, in der das Paar im Hotel ankommt.  
 */  
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

#### **4.2.2 Romantisches Abendessen (SunsetDinner.java)**

```java
package manuscript.honeymoon;

import manuscript.Scene;
import manuscript.Actor;

/**  
 * SunsetDinner ist die romantische Abendessen-Szene während der Flitterwochen.  
 */  
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

### **4.3 AKT 3: Die Geburt**

#### **4.3.1 Das Krankenhaus (Hospital.java)**

```java
package manuscript.birth;

/**  
 * Hospital repräsentiert das Krankenhaus als Ort der Geburt.  
 */  
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

#### **4.3.2 Die Geburts-Szene (BirthScene.java)**

```java
package manuscript.birth;

import manuscript.Scene;
import manuscript.character.Bride;
import manuscript.character.Newborn;

/**  
 * BirthScene orchestriert die Geburt des Kindes.  
 */  
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

### **4.4 AKT 4: Das Familienleben**

#### **4.4.1 Die Morgenroutine (MorningRoutine.java)**

```java
package manuscript.familylife;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Bride;
import manuscript.character.Newborn;

/**  
 * MorningRoutine repräsentiert den typischen Morgen einer jungen Familie.  
 */  
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

#### **4.4.2 Die Gute-Nacht-Geschichte (Bedtime.java)**

```java
package manuscript.familylife;

import manuscript.Scene;
import manuscript.character.Groom;
import manuscript.character.Newborn;

/**  
 * Bedtime ist die Szene, in der der Vater seinem Kind eine Gute-Nacht-Geschichte vorliest.  
 */  
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

## **5. Die Bühnentechnik (Backstage)**

Die Backstage-Infrastruktur bleibt strikt von der Fachlogik getrennt. Hier liegen alle technischen Adapter, Datenbankzugriffe und externe Schnittstellen.

```
manuscript/  
└─ backstage/  
   ├─ DatabaseArchive.java         ← Datenbank-Zugriff  
   ├─ payment/  
   │  ├─ Payable.java              ← Payment-Interface  
   │  ├─ StripeApiClient.java      ← Stripe API Client  
   │  └─ StripePaymentAdapter.java ← Adapter für Stripe  
   └─ external/  
      └─ ApiConnector.java         ← Externe API-Verbindungen
```

### **5.1 Das Datenbank-Archiv (DatabaseArchive.java)**

```java
package manuscript.backstage;

import manuscript.Actor;

/**  
 * DatabaseArchive ist die technische Schnittstelle zur Datenbank.  
 * Sie kümmert sich um die Persistierung von Akteuren.  
 */  
public class DatabaseArchive {
    
    private final String connectionString;
    
    public DatabaseArchive(String connectionString) {
        this.connectionString = connectionString;
        System.out.println("🗄️  Datenbank-Archiv verbunden: " + connectionString);
    }
    
    public void save(Actor actor) {
        // Simuliere Datenbank-Speicherung
        System.out.println("💾 [DB] Speichere " + actor.name() + " in der Datenbank...");
    }
    
    public Actor load(String name) {
        // Simuliere Datenbank-Laden
        System.out.println("📂 [DB] Lade " + name + " aus der Datenbank...");
        return null; // In einer echten Implementierung würde hier ein Actor zurückgegeben
    }
}
```

### **5.2 Das Payment-Interface (Payable.java)**

```java
package manuscript.backstage.payment;

/**  
 * Payable definiert die fachliche Fähigkeit, Zahlungen durchzuführen.  
 * Dies ist die Schnittstelle zwischen der Fachdomäne und den technischen Zahlungssystemen.  
 */  
public interface Payable {
    
    /**  
     * Führt eine Zahlung durch.  
     * @param amount Der zu zahlende Betrag  
     */  
    void pay(double amount);
}
```

### **5.3 Der Stripe API Client (StripeApiClient.java)**

```java
package manuscript.backstage.payment;

/**  
 * StripeApiClient ist die technische Schnittstelle zur Stripe-API.  
 * Diese Klasse kennt nur die technische Sprache von Stripe, nicht die Fachdomäne.  
 */  
public class StripeApiClient {
    
    public void executeTransaction(double amount, String accountId) {
        System.out.println("💳 [Stripe API] Transaktion wird ausgeführt...");
        System.out.println("💳 [Stripe API] Betrag: €" + amount);
        System.out.println("💳 [Stripe API] Konto: " + accountId);
        System.out.println("💳 [Stripe API] Transaktion erfolgreich!");
    }
}
```

### **5.4 Der Stripe Payment Adapter (StripePaymentAdapter.java)**

```java
package manuscript.backstage.payment;

import manuscript.Actor;
import manuscript.Outfit;

/**  
 * StripePaymentAdapter übersetzt die fachliche pay()-Methode  
 * in die technische executeTransaction() der Stripe-API.  
 */  
public class StripePaymentAdapter extends Outfit implements Payable {
    
    private final StripeApiClient stripeClient;
    
    public StripePaymentAdapter(Actor actor, StripeApiClient stripeClient) {
        super(actor);
        this.stripeClient = stripeClient;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("💰 " + name() + " initiiert eine Zahlung von €" + amount);
        String accountId = "stripe_account_" + name().value().toLowerCase();
        stripeClient.executeTransaction(amount, accountId);
    }
}
```

## **6. Die Bühne (Stage)**

Die Stage ist die Präsentationsschicht, die dem Zuschauer (Benutzer) die Handlung sichtbar macht. Sie nutzt die Fachlogik, ohne sie zu verändern.

```
manuscript/  
└─ stage/  
   ├─ accessory/  
   │  ├─ SpeechBubble.java         ← Sprechblase für Ausgaben  
   │  ├─ ActionButton.java         ← Interaktiver Button  
   │  └─ CinematicGrid.java        ← Visuelles Raster  
   └─ take/  
      └─ WeddingCloseUp.java       ← Nahaufnahme der Hochzeit
```

### **6.1 Die Sprechblase (SpeechBubble.java)**

```java
package manuscript.stage.accessory;

/**  
 * SpeechBubble repräsentiert eine visuelle Sprechblase auf der Bühne.  
 */  
public class SpeechBubble {
    
    private final String message;
    
    public SpeechBubble(String message) {
        this.message = message;
    }
    
    public void display() {
        System.out.println("💬 [" + message + "]");
    }
}
```

### **6.2 Der Action Button (ActionButton.java)**

```java
package manuscript.stage.accessory;

import manuscript.Scene;

/**  
 * ActionButton ist ein interaktiver Button auf der Bühne,  
 * der eine Szene auslöst, wenn er geklickt wird.  
 */  
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

### **6.3 Das Cinematic Grid (CinematicGrid.java)**

```java
package manuscript.stage.accessory;

/**  
 * CinematicGrid ist ein visuelles Raster für die Darstellung von Szenen.  
 */  
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

### **6.4 Die Wedding Close-Up Szene (WeddingCloseUp.java)**

```java
package manuscript.stage.take;

import manuscript.character.Groom;
import manuscript.character.Bride;
import manuscript.stage.accessory.CinematicGrid;

/**  
 * WeddingCloseUp ist eine visuelle Nahaufnahme der Hochzeitszeremonie.  
 */  
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

## **7. Die Regie (Directing)**

Die Regie ist der **Composition Root** der Anwendung. Hier werden alle Fäden zusammengeführt: Die *Charaktere* werden gecastet, die *Outfits* werden angezogen, die *Bühnentechnik* wird aufgebaut und die *Szenen* werden in die Filmrolle eingefügt.

```
manuscript/  
└─ directing/  
   └─ InstalledMovie.java      ← Der Composition Root
```

### **7.1 Der Composition Root**

Die `InstalledMovie.java` Klassen realisiert den *Composition Root* der gesamten Anwendung. Hier wird das komplette Drehbuch zusammengestellt und die Filmrolle befüllt.  

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
import manuscript.wedding.VideoProxy;
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
    private final StripeApiClient stripe = new StripeApiClient();
      
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
        Actor baseGroom = new Groom("Romeo");
        Actor baseBride = new Bride("Julia");
        Actor baseChild = new Newborn("Luna");

        Actor suitedGroom = new SuitedActor(baseGroom);
        StripePaymentAdapter groomWithCard = new StripePaymentAdapter(suitedGroom, stripe);
        Actor persistentBride = new PersistentActor(baseBride, archive);

        // ============================================================
        // DIE INSZENIERUNG DER SZENEN (Die Filmrolle wird befüllt)
        // ============================================================

        // AKT 1: Die Hochzeit
        filmRoll.add(new WeddingCeremony((Groom) baseGroom, (Bride) baseBride));
        filmRoll.add(new PayTheBand(groomWithCard, 500.00));
        
        // Video-Rückblick (mit Proxy-Pattern)
        Video videoProxy = new VideoProxy("liebesgeschichte_hd.mp4");
        SpeechBubble screen = new SpeechBubble("Leinwand bereit...");
        filmRoll.add(new LoveStoryFlashback(videoProxy, screen));

        // Visuelle Nahaufnahme
        CinematicGrid grid = new CinematicGrid(12);
        WeddingCloseUp visualTake = new WeddingCloseUp((Groom) baseGroom, (Bride) baseBride, grid);
        filmRoll.add(visualTake::render);

        // AKT 2: Die Flitterwochen
        filmRoll.add(new HotelCheckIn(baseGroom, baseBride));
        filmRoll.add(new SunsetDinner(baseGroom, baseBride));

        // AKT 3: Die Geburt
        Hospital hospital = new Hospital("St. Mary's Klinik");
        filmRoll.add(new BirthScene((Bride) baseBride, (Newborn) baseChild, hospital));

        // AKT 4: Das Familienleben
        filmRoll.add(new MorningRoutine((Groom) baseGroom, (Bride) baseBride, (Newborn) baseChild));
        filmRoll.add(new Bedtime((Groom) baseGroom, (Newborn) baseChild));
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

### **7.2 Schlüsselelemente der Regie**

* **Die main als Kinosaal:** Die `main()`-Methode ist frei von jeglicher Logik. Sie drückt nur auf den „Play"-Knopf.
* **Die Filmrolle als Queue:** Durch das Hinzufügen der Szenen in eine Queue (Warteschlange) im `start()`-Prozess wird die Reihenfolge der Chronologie festgeschrieben. `nextScene()` rattert diese einfach herunter, ohne die konkreten Szenen-Inhalte kennen zu müssen.
* **Volle Kapselung:** Selbst in der Hauptschleife bleibt das System blind für technische Details. Es kennt nur den Befehl execute() der jeweiligen Szene.

Wenn du die Applikation ausführst, wird die Konsole die Chronologie des Drehbuchs exakt wie ein Drehbuch-Protokoll ausgeben:

1. **Szene 1:** Die Trauung läuft, die Braut nimmt den Ring entgegen und das Persistent-Kostüm speichert das Objekt lautlos im Hintergrund.
2. **Szene 2:** Der Bräutigam zahlt per `pay()`. Der StripePaymentAdapter übersetzt den Befehl für die `executeTransaction()` der Stripe-API.
3. **Szene 3:** Der Beamer schaltet sich ein. Erst **jetzt** ruft der Proxy das echte Video ab und schont bis zu diesem Moment den Arbeitsspeicher.
4. **Szene 4-7:** Die weiteren Akte der Lebensgeschichte laufen nacheinander ab.

## **8. Der cineastische Stacktrace**

Auch der Stack Trace dieses Graphen liest sich am Ende wie eine Inhaltsangabe des Films.

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

Der Debugger „schreit" die Fachlichkeit. Man sieht keinen generischen Daten-Update, sondern eine Braut, die in einer bestimmten Szene einen Ring entgegennimmt und eine fachliche Zustandsänderung bewirkt. Die Technik dient der Geschichte.

Selbst im Fehlerfall „schreit" dieser Stacktrace seine fachliche Bedeutung heraus: Man sieht sofort, in welcher Szene (`Wedding`), bei welcher Kameraeinstellung (`CloseUp`) oder bei welcher konkreten Figur (`Bride`) die Erzählung unterbrochen wurde.

## **9. Prinzipien und Bauplan mit Regeln: Code als Drehbuch**

### **9.1 Die leitenden Prinzipien**

* **Graphen als Skript:** Der *Kollaborations-Graph* bildet das Ensemble, der *Konstruktions-Graph* regelt den Set-Aufbau und der *Aufruf-Graph* beschreibt den Dialog zur Laufzeit.

* **„Tell, Don't Ask":** Objekte werden nicht nach ihrem Zustand abgefragt, sondern durch prägnante Methoden direkt zum Handeln aufgefordert.

* **System als Drehbuch:** Die Projektorganisation erfolgt nicht in Schichten, sondern hierarchisch nach Domänenkonzepten.

### **📦 9.2 Bauplan und Paket-Regeln**

Um eine saubere Struktur zu gewährleisten, müssen die Ebenen und ihre Pakete nach folgenden Prinzipien aufgebaut sein:

**Fokus auf Ebene Null:** Auf der obersten Ebene eines Pakets liegen ausschließlich Schnittstellen, abstrakte Klassen, Wertobjekte und Entitäten der Hauptkonzepte – völlig frei von technischem Ballast.

**Hierarchie ohne Zyklen:** Ein übergeordnetes Paket darf niemals von seinen Unterpaketen abhängen. Die Verwendung von Unterpaketen ist zulässig, sofern sie nicht auf derselben Hierarchieebene liegen. Zyklische Abhängigkeiten sind dabei strikt auszuschließen.

**Rolle der Unterpakete:** Die Unterpakete führen keine neuen Fachkonzepte ein. Sie detaillieren lediglich die bestehenden Konzepte der übergeordnete Ebene und stellen nur die konkreten Implementierungen bereit.

**Fachsprache:** Alle Pakete und deren Klassen müssen die Sprache der realen Fachwelt sprechen, nicht die der technischen Umsetzung.

---

## **🎬 Schlusswort: Code, der Geschichten erzählt**

Wenn wir Software als Drehbuch begreifen, verlassen wir die Welt der leblosen Datencontainer und betreten eine Bühne, auf der die Fachlichkeit endlich sichtbar wird. Die Trennung von Backstage‑Technik und Regie‑Orchestrierung befreit die Domäne von allem Ballast, der sie jahrzehntelang verschüttet hat. Was bleibt, ist die reine Erzählung — klar, menschlich, nachvollziehbar.

Die cineastische Projektstruktur ist keine hübsche Metapher, sondern ein Werkzeug. Sie verwandelt das tägliche Lesen von Code in ein intuitives Erlebnis. Wenn ein Stacktrace nicht mehr kryptische Framework‑Fragmente ausspuckt, sondern eine misslungene Szene beschreibt — eine Braut, die den Ring nicht entgegennehmen kann, eine Kameraeinstellung, die den Moment einfängt — dann dient die Technik endlich der Geschichte, nicht umgekehrt.

Gerade jetzt, in einer Zeit, in der KI in Sekundenbruchteilen unendliche Mengen Code erzeugt, brauchen wir diese Erzählstruktur dringender denn je. Sie ist unser Kompass in einer Welt, in der das Schreiben trivial geworden ist, das Verstehen aber zur eigentlichen Herausforderung wird. Nur wenn unsere Software einer klaren Dramaturgie folgt, können wir KI als Co‑Autor einsetzen, ohne in einer Flut aus generierten Fragmenten die Orientierung zu verlieren.

Hören wir also auf, nur technische Schablonen zu füllen oder blind KI‑Output aneinanderzukleben. Werden wir zu Regisseuren. Zu Autoren. Zu Erzählern. Schreiben wir Coden, der nicht nur funktioniert, sondern der die eigene Bedeutung stolz auf die Leinwand bringt.

**Klappe zu, Film ab!**





https://testing.googleblog.com/2008/08/where-have-all-singletons-gone.html
