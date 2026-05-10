
**Teaser Statement**
> AI writes code in seconds today. But who will still understand it tomorrow?  
> Why we should stop building layers and start writing screenplays.

Tags: 🎬💻 #SoftwareArchitecture #CleanCode #AI #Storytelling #DesignPattern

# **🎞️ Software as a Feature Film Screenplay**

The rise of artificial intelligence tools like ChatGPT, Claude, or GitHub Copilot has fundamentally changed software development. The old rule of thumb "read ten times more than you write" holds more true today than ever — except that we no longer read our own code, but primarily machine-generated code. To be able to grasp and manage this flood in a human way, we need a new perspective on software architecture.

Computers process arbitrary data structures. Humans, by contrast, understand the world through stories. Biologically speaking, our brains are wired to store information through cause, effect, and **emotion**. Where raw data tends to generate cognitive coldness and is quickly forgotten, good stories create resonance. They spark curiosity, foster empathy for the domain processes, and motivate the reader to keep going. It therefore makes sense to design code like a film screenplay.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_00_code_like_movie.png)

In contrast to approaches that view software as a technical layered landscape of stateless services with passive data containers, the feature film philosophy stages an object-oriented system as a living ensemble of intelligent actors. These actors appear in scenes, act across multiple acts, and give the logical sequence a dramaturgy that is humanly tangible. The story is thus no longer merely executed, but made emotionally experiential through the behavior of the actors.

## 1. **The Screenplay as a Blueprint**

The modeling of an object-oriented system occurs through three central graphs. In this context, the screenplay takes on the role of a blueprint: it defines the framework in which the static structure and the fleeting moment of interaction interlock seamlessly.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_01_code_like_movie.png)

* **The Construction Graph (The Direction / The Set Construction):** It describes the instantiation and assembly of the object framework through the [Composition Root](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) (Who owns whom?). This is where the "stage machinery" is installed before the first scene begins.
* **The Collaboration Graph (The Ensemble):** It defines the permanent relationships between objects (Who knows whom and can communicate with each other?). This corresponds to the fixed cast of roles and their interconnection.
* **The Call Graph (The Plot):** It maps the dynamic interactions at runtime (Who is currently calling which method?) — the actual dialogue between actors during the execution of a scene.

**The Central Directing Instructions**

The **Law of Demeter** and the principle **Tell Don't Ask** function as central directing instructions. The Law of Demeter prevents transitive coupling by ensuring that an object interacts exclusively with its direct collaboration partners. The Tell-Don't-Ask principle ensures that behavior is executed where it semantically belongs, rather than carrying internal state to the outside. The result is a living, behavior-oriented structure that moves us emotionally, rather than a procedural query script that quickly becomes monotonous.

**The Table of Contents of the Screenplay**

To ensure a domain-oriented and progressive flow of information, the screenplay's table of contents must map out a clear navigation path that helps the reader efficiently locate the components of the story. Three pragmatic rules apply:

1. **Clear Dependencies:** Packages must never depend on their sub-packages. Circular references are strictly forbidden to preserve the directed flow of information.
2. **Precise Detail Refinement:** Sub-packages do not introduce new concepts. They merely concretize existing terms from the parent packages.
3. **Living Domain Language:** Packages reflect the language of the domain (Ubiquitous Language), not the technical terms of the implementors.

## 2. **🎬 The Cinematic Project Structure**

The structure of the project is the visible form of the screenplay. It separates the domain identity from the technical implementation. This makes the staging of the application immediately understandable at first glance. Whoever opens the manuscript sees no technical template, but the screenplay of an individual story, ready for production in a film studio:

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_02_code_like_movie.png)

Whether investor, domain expert, architect, or developer: no one needs to guess. Everyone immediately reads the table of contents of a life story.

```
manuscript/
│
├─ directing/         ← DIRECTION (The Control System / Composition Root)
│
├─ character/         ← ENSEMBLE (The Lead Actors & Role Profiles)
├─ outfit/            ← COSTUME & MAKEUP (Structural Shells & Disguises)
├─ wedding/           ← ACT 1 (Domain Process: Coming Together)
├─ honeymoon/         ← ACT 2 (Domain Process: Transition Phase)
├─ birth/             ← ACT 3 (Domain Process: Initialization)
├─ familylife/        ← ACT 4 (Domain Process: Steady State)
│
├─ backstage/         ← STAGE MACHINERY (Databases, APIs, Infrastructure)  
│  
├─ stage/             ← THE STAGE (User Interfaces & Presentation)
│
├─ Actor.java         ← ACTOR (Central Interface)
├─ Name.java          ← PROPS (Precise Domain Values / Value Objects)
├─ Outfit.java        ← COSTUME (Base Decorator)
├─ Scene.java         ← SCENE (The Interaction Protocol)
├─ Media.java         ← MEDIUM (The Information Carrier)
└─ Movie.java         ← SCREENPLAY (The Narrative Thread / Entry Point)
```

**Components and Dependencies of the Staging**

To tell the story without logical errors, the packages follow a strict hierarchy:

**Ideas & Concepts (Domain):** The conceptual foundation. This is where the central ideas and domain rules reside. This layer is completely autonomous and depends on nothing. All other packages build on top of it.

**Backstage (Infrastructure):** The technical infrastructure. This is where tools, databases, and APIs live. It serves the ensemble and may only depend on the domain.

**Scenes (Details):** The domain-specific detail packages. They bring the abstract domain concepts to life. Here, the concrete processes and interactions of the story are elaborated.

**Stage (UI):** The visible surface. Here, the action is made visible to the audience (the users). The stage accesses the domain, scenes, and the infrastructure.

**Directing (Composition Root):** The Directing package functions as the operational direction center. In a Composition Root class, it provides the central entry point `main()` and orchestrates the interplay of all classes. All decisions regarding the instantiation of objects and all settings required at startup are made here. The direction is thus the only entity that knows the entire ensemble and brings the various components together into a finished staging. No other package may depend on the direction.

## **3. The Ensemble and the Props (The Domain Layer)**

The centerpiece of the structure is the domain identity at level zero. Here, the main characters are defined as interfaces, the plot framework as scenes, and their attributes as props (Value Objects). This layer is free of technical noise and makes the essence of the system immediately tangible.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_03_code_like_movie.png)
```
manuscript/
...
├─ Actor.java         ← The universal actor interface
├─ Name.java          ← The prop: Precise domain value
├─ Outfit.java        ← The costume: Base decorator
├─ Scene.java         ← The interaction protocol
├─ Media.java         ← Medium: The information carrier
└─ Movie.java         ← The narrative thread / entry point
```

### **3.1. The Actor — The Central Interface**

In the world of film, there are stars, supporting actors, and extras. In the screenplay, all play the same role: they are actors who perform and communicate with each other. This domain uniformity is expressed in the code through a central interface:

```java
public interface Actor {
    Name name();
    void perform();
    void speak(Media media);
}
```

Every `Actor` can act (`perform`), communicate (`speak`), and has a name (`name`). These three core capabilities are universal — regardless of whether they are the lead actor, a supporting actor, or an extra. The expression of their role is achieved through concrete implementations, not through technical categorizations.

### **3.2 The Prop (Name) — The Precise Domain Value**

In film, props have a clear meaning: they are not interchangeable objects. A wedding ring is not "just any object," but a symbol with emotional and functional significance.

The same applies to domain values in code. A name is not a primitive `String`, but a precise domain value that carries semantic validation and identity:

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
    public String toString() { return value; }

    public String value() { return value; }
}
```

Through this encapsulation, the value gains a domain identity. The constructor validates at the moment of creation that the name cannot be empty. This defensive strategy prevents erroneous states before they can spread through the system. What technically looks like a wrapper is functionally a guarantee of integrity and expressiveness.

### **3.3 The Costume (Outfit) — The Base Decorator**

Actors in film wear different costumes. A suit confers elegance, a uniform conveys authority. In code, the **Decorator Pattern** serves the same purpose: it extends actors with additional behavior without changing their identity.

```java
/**
 * Abstract outfit to decorate an actor.
 */
public abstract class Outfit implements Actor {
    
    protected final Actor decoratedActor;
    
    protected Outfit(Actor actor) { this.decoratedActor = actor; }
    
    @Override
    public Name name() { return decoratedActor.name(); }
    
    @Override
    public void perform() { decoratedActor.perform(); }
}
```

The base class `Outfit` is an abstract decorator. It holds a reference to the decorated actor and delegates the central methods `name()` and `perform()` directly to it. This guarantees that the basic identity of the actor remains unchanged. Concrete subclasses can add specific behavior before, after, or instead of the delegation. An actor is thus equipped with additional capabilities without altering its core. The `speak(Media)` method is deliberately not automatically delegated, since each concrete `Outfit` should decide for itself how it communicates.

### **3.4 The Scene — The Interaction Protocol**

A scene is the smallest coherent unit of action in a film. It describes a self-contained interaction between actors in one place and at one time.

```java

@FunctionalInterface  
public interface Scene {
    void execute();  
}
```

This interface reduces the scene to its essence: the execution of an action. Thanks to the `@FunctionalInterface` annotation, it can also be used as a lambda or method reference. A scene does not say *how* the action unfolds, only *that* it is executed. All details are reserved for the concrete implementation.

### **3.5 The Medium (Media) — The Information Carrier**

Communication in films happens through various media: speech, gestures, letters, phone calls. In code, `Media` represents the carrier of this communication.

The interface `Media` defines a fluent API for populating content.
```java
public interface Media {
    Media with(String name, String value);
    Media with(String name, boolean value);
    public String content(String name);
}
```
The class `Media.Default` provides a standard implementation that stores content in a `Map`.
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
    public String toString() { return content.toString(); }
}
```
`Air` is a concrete medium for invisible transmission — such as the spoken word into the air.

```java
public final class Air extends Media.Default { }
```
Further specializations can be added for different contexts, such as `Email`, `RecordMedia`, or `VideoCanvas`.

### **3.6 The Movie — The Narrative Thread**

The movie is the total work of art. It orchestrates the scenes and defines the progression of the story.

```java
public interface Movie {
    void play();  
}
```

The `Movie` interface is deliberately minimalist: a single method `play()` starts the staging. The concrete order of scenes, their chaining and logic, is defined in an implementation such as `AnalogMovie`. This keeps the domain abstraction ("play the movie") clearly separated from the technical orchestration.

## **4. The Ensemble (The Characters)**

The heart of every story is its characters. They carry the plot, represent domain roles, and act as autonomous actors with clear behavior.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_04_code_like_movie.png)

```
manuscript/character/
│
├─ Bride.java         ← The Bride (Central Protagonist)
├─ Groom.java         ← The Groom (Central Protagonist)
├─ Newborn.java       ← The Newborn (New Character in Family Life)
├─ Guest.java         ← The Guest (Individual Wedding Guest)
├─ Guests.java        ← The Guests (Guest Group as Ensemble)
├─ Musician.java      ← The Musician (Individual Band Member)
└─ Band.java          ← The Band (Music Group as Ensemble)
```

All characters implement the central `Actor` interface and represent their domain role through specific behavior. Unlike passive data containers, they decide for themselves about their actions and communication.

### **4.1 The Bride**

The bride is one of the central protagonists of the wedding story. She can recite her vows, exchange rings, and express her consent.

```java
public class Bride implements Actor {

	private final Name name;
	private boolean hasRing = false;

	public Bride(String name) {
		this.name = new Name(name);
	}

	@Override
	public Name name() { return name;}

	@Override
	public void perform() { System.out.println("💍 " + name + " recites her wedding vows.."); }

	public void giveRing(Media ringBasket) {
		ringBasket.with("groom-ring", "Wedding-Ring");
		System.out.println("💍 " + name + " hands over " + ringBasket + ".");
	}

	public void receiveRing(Media ringBasket) {
		String ring = ringBasket.content("bride-ring");
		if (ring.isEmpty()) {
			throw new IllegalStateException("Wedding ring is missing!");
		}
		if (hasRing) {
			throw new IllegalStateException("The bride is already wearing a ring!");
		}
		System.out.println("💍 " + name + " take the " + ring + ".");
		hasRing = true;
	}

	public void speak(Media media) {
		media.with("role", "Bride")
		.with("name", name.value())
		.with("text", "Yes, I will...")
		.with("hasRing", hasRing);
	}
}
```

The bride manages her own state (`hasRing`) and makes decisions independently. The method `receiveRing` defensively checks whether a ring is already present. This keeps the business logic where it semantically belongs: with the bride herself, not in an external service.

### **4.2 The Groom**

The groom plays the complementary role to the bride. His implementation follows the same principle: he is an autonomous actor with his own identity and his own behavior.

```java
public class Groom implements Actor {

	private final Name name;
	private boolean hasRing;

	public Groom(String name) {
		this.name = new Name(name);
	}

	@Override
	public Name name() { return name;}

	@Override
	public void perform() { System.out.println("💍 " + name + " recites her wedding vows.."); }

	public void giveRing(Media ringBasket) {
		ringBasket.with("bride-ring", "Wedding-Ring");
		System.out.println("💍 " + name + " presents the " + ringBasket + ".");
	}
	
	public void receiveRing(Media ringBasket) {
		String ring = ringBasket.content("groom-ring");
		if (ring.isEmpty()) {
			throw new IllegalStateException("Wedding ring is missing!");
		}
		if (hasRing) {
			throw new IllegalStateException("The groom is already wearing a ring!");
		}
		System.out.println("💍 " + name + " take the " + ring + ".");
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

Bride and Groom are structurally similar, but not identical. Each character has its own perspective on events. This duplication is intentional: it preserves the semantics and allows both roles to evolve independently, without changes to one automatically affecting the other.

### **4.3 The Newborn**

With the birth, a new character enters the stage: the newborn. It represents the next generation and expands the ensemble.

```java

public class Newborn implements Actor {
    
    private final Name name;
    
    public Newborn(String name) {
        this.name = new Name(name);
    }
    
    @Override
    public Name name() { return name;}
    
    @Override
    public void perform() { System.out.println("👶 " + name + " smiles cheerfully.");}

    public void speak(Media media) {
        media.with("role", "Newborn")
            .with("name", name.value())
            .with("text", "😭 " + name + " cries loudly!");
    }
}
```

The newborn shows how easily new characters can be added. It implements `Actor` and defines its own behavior. No central service layer needs to be adapted. The character is immediately ready to perform.

### **4.4 The Guest**

Guests are supporting characters who enrich the wedding. They can applaud and congratulate.

```java
public class Guest implements Actor {

    private final Name name;

    public Guest(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() { return name; }

    @Override
    public void perform() {
		System.out.println("🧍‍♂️" + name + " smiles.");
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

The `clap()` method shows how a guest stages their own communication: they create an `Air` medium, speak into it, and output it. This is an example of autonomous behavior that requires no external coordination.

### **4.5 The Guest Group (Guests)**

Several guests together form a group. This is modeled as an iterable collection so that all guests can be easily traversed.

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

The `Guests` class is deliberately designed to be immutable. The `add` method creates a new instance with the added guest rather than modifying the existing list. This preserves data integrity and avoids side effects.

### **4.6 The Musician**

Musicians play an important role at the wedding. They provide musical accompaniment.

```java
public class Musician implements Actor {
    
    private final Name name;

    public Musician(String name) {
        this.name = new Name(name);
    }

    @Override
    public Name name() { return name; }

    @Override
    public void perform() { System.out.println("🧍‍ " + name + " play music.");}

    @Override
    public void speak(Media media) {
        media.with("role", "Musician")
             .with("name", name.value())
             .with("text", "🎤💬 " + name + " singing a song!");
    }
}
```

The musician too is a fully-fledged `Actor`. Its role is clearly defined: playing music and singing. This clarity makes the code readable and maintainable.

### **4.7 The Band**

Several musicians together form a band. This group has its own name and can be iterated.

```java
public class Band implements Iterable<Musician> {

    private List<Musician> group;

    private final Name name;

    public Band(String name, List<Musician> group) {
        this.name = new Name(name);
        this.group = new ArrayList<>(group);
    }

    public Name name() { return name; }

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

The `Band` carries its own name and groups musicians. Just as with `Guests`, immutability is maintained here too: `add` creates a new band instance rather than modifying the existing one.

## **5. Costume & Makeup (The Structural Shells)**

In the world of film, costume and makeup transform actors into their roles. A suit makes a performer into a gentleman; a costume grants authority or elegance. In code, the **Decorator Pattern** serves the same purpose: it equips actors with additional behavior without changing their core identity.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_05_code_like_movie.png)

```
manuscript/outfit/
│
├─ ActorInSuite.java          ← Actor in a Suit (Visual Costuming)
├─ BrideWithArchive.java      ← Bride with Database Access
├─ BrideWithMailbox.java      ← Bride with Email Functionality
├─ GroomWithCard.java         ← Groom with Payment Card
└─ GroomWithPhone.java        ← Groom with Phone
```

Each `Outfit` extends an actor with specific behavior. This is done through composition rather than inheritance: a decorated actor delegates core functionality to the wrapped actor and only selectively adds new capabilities.

### **5.1 Actor in a Suit (ActorInSuite)**

The suit is a purely visual outfit. It changes how the actor steps onto the stage, without altering their capabilities.

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

The `ActorInSuite` overrides `perform()` and adds a visual cue ("steps onto the stage in a wedding suit") before executing the actor's original action. The `speak` method is delegated directly. This shows how behavior can be extended in a targeted manner without redefining the entire functionality.

### **5.2 Bride with Database Access (BrideWithArchive)**

Through this decorator, the bride gains the ability to save her data to a database. This is an example of the separation between domain logic (the bride) and technical infrastructure (the database).

```java
public class BrideWithArchive extends Outfit {

    private Archive arhive;

    public BrideWithArchive(Bride bride, Archive arhive) {
        super(bride);
        this.arhive = arhive;
    }
    
    @Override
    public void speak(Media spaek) { decoratedActor.speak(spaek); }
    
    @Override
    public void perform() {
        // perform the default behavier
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

The `BrideWithArchive` performs an additional database operation after the original action (`decoratedActor.perform()`). The actor "speaks" its data into a `RecordMedia` object, which is then stored in the `Archive`. This choreography shows how infrastructure is seamlessly embedded in the domain narrative, without the core logic of the bride itself needing to know about it.

### **5.3 Bride with Email Functionality (BrideWithMailbox)**

This decorator equips the bride with the ability to send invitations by email.

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
        emailBox.put(new Email(name().value(), actor.name().value(), "Invitation to Wedding..."));
    }
}
```

The `invite` method is new and uses the `EmailBox` to send an invitation. The bride herself remains unchanged; only the decorated version gains this additional capability. This demonstrates the strength of the Decorator Pattern: behavior can be added selectively and contextually.

### **5.4 Groom with Payment Card (GroomWithCard)**

Through this decorator, the groom gains the ability to make payments. He additionally implements the `Payer` interface to make the payment function explicit.

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

The `pay` method translates the domain action ("the groom pays") into a technical API operation. The decorator functions here as an **Adapter**: it encapsulates the details of the Stripe API and makes them accessible through a domain-oriented interface.

### **5.5 Groom with Phone (GroomWithPhone)**

This decorator equips the groom with a phone through which he can make and receive calls.

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

By implementing the `Headset` interface, the groom can be registered as a receiver of messages. The `call` method sends a message through the `Phone`, while `accept` processes incoming responses. This is an example of asynchronous communication seamlessly integrated into the object model.

## **6. The Stage Machinery (The Infrastructure Layer)**

While the actors and scenes carry the domain essence of the story, the stage machinery works in the background. It ensures that lights, sound, and sets function — but remains invisible to the audience. In code, this corresponds to the infrastructure layer: databases, APIs, communication channels, and payment services.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_06_code_like_movie.png)

```
manuscript/backstage/
...
├─ archive/
│  ├─ DatabaseArchive.java   ← Concrete Database Connection
│  └─ RecordMedia.java       ← Data Carrier for Database Entries
├─ email/
│  ├─ Email.java             ← Email Data Object
│  └─ EmailBox.java          ← Email Dispatch Adapter
├─ payment/
│  ├─ StripeApi.java         ← Adapter for the Stripe Payment API
│  └─ Transaction.java       ← Transaction Object for Payments
├─ phone/
│  ├─ Phone.java             ← Phone Communication Adapter
│  └─ Headset.java           ← Interface for Message Recipients
├─ Archive.java              ← Interface to the Archive (Database)
├─ Channel.java              ← Communication Channel (Publish/Subscribe)
├─ PostBox.java              ← Interface for Message Dispatch
├─ Payer.java                ← Interface for Payment Processing
...
```

The stage machinery is strictly separated from the domain. All infrastructure components are abstracted through interfaces, so that the domain logic has no direct dependencies on technical implementations. This makes it possible to swap out databases, APIs, or communication channels without changing the core story.

### **6.1 The Archive (Archive & DatabaseArchive)**

The archive stores data persistently. In a film, this would be the film reel developed later. In code, it is the database.

**Interface:**
```java
public interface Archive {
    void store(RecordMedia media);
}
```

**Implementation:**
```java
public class DatabaseArchive implements Archive {
    
    private final String connectionString;
    
    public DatabaseArchive(String connectionString) {
        this.connectionString = connectionString;
        System.out.println("💾 Connect database: " + connectionString);
    }
    
    @Override
    public void store(RecordMedia media) {
        // Simulate database manipulation operation
        System.out.println("💾 Storing in database: " + media.toDML());
    }
}
```

The interface `Archive` defines the domain action: storing. The concrete implementation `DatabaseArchive` handles the technical execution. This keeps the domain independent of the choice of database.

**The Recording Medium (RecordMedia):**
```java
public class RecordMedia extends Media.Default {
    
    public String toDML() {
        // Simulate build DML statement
        return content.toString();
    }
}
```

`RecordMedia` is a specialized medium for database entries. It inherits from `Media.Default` and adds a method to bring content into a storable format.

### **6.2 The Post (PostBox, Email & EmailBox)**

The post is a communication channel. In a film, these would be letters; in code, emails.

**Interface:**
```java
public interface PostBox<T> {
    void put(T content);
}
```

The generic interface `PostBox<T>` abstracts the dispatch mechanism. It knows only the action "deposit something," not the type of content.

**Email Data Object:**
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

    public String from() { return from; }
    public String to() { return to; }
    public String content() { return content;}

    @Override
    public String toString() {return "From: " + from + ", To: " + to + ", Text: " + content;}
}
```

`Email` is a simple data object that encapsulates the essential information of a message. It represents an email without knowing how it is sent.

**Email Dispatch Adapter:**
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

The `EmailBox` implements `PostBox<Email>` and simulates dispatch via a mail server. In a real application, the call to a mail API would stand here. The abstraction through `PostBox` makes it possible to swap out the dispatch mechanism without touching the domain.

### **6.3 The Payment (Payer, Transaction & StripeApi)**

Payments are another example of infrastructure. In a film, this would be the box office; in code, the payment API.

**Interface:**
```java
public interface Payer {
    void pay(double amount, String toAccountId);
}
```

The `Payer` interface abstracts the payment function. It knows only the domain task: transfer an amount to an account.

**Transaction Object:**
```java
public class Transaction extends Media.Default {
    
    private String fromAccount;
    private String toAccount;

    public Transaction(String fromAccount, String toAccount) {
        this.fromAccount = fromAccount;
        this.toAccount = toAccount;
    }

    public String from() { return fromAccount; }
    public String to() { return toAccount; }
}
```

`Transaction` is a medium that carries the details of a payment. It defensively validates that account IDs must not be empty. This validation occurs already at the moment of object creation, preventing later errors.

**Stripe API Adapter:**
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

The `StripeApi` simulates the connection to a payment API. It receives a `Transaction` and an amount and executes the payment. In a real application, the HTTP call to the Stripe API would stand here.

### **6.4 The Phone (Phone, Headset & Channel)**

The phone enables asynchronous communication between actors. It follows the **Observer Pattern**: messages are published, and registered recipients are notified.

**Communication Channel:**
```java
public interface Channel<T> {
	void subscribe(Consumer<T> subscriber);
	void publish(T message);
}
```

The `Channel` interface abstracts a publish/subscribe mechanism. Headsets can register and are notified when new messages arrive.

**Phone Implementation:**
```java
public class Phone implements Channel<String> {

	private String phoneNummer;
	private List<Consumer<String>> subscribers = new ArrayList<>(0);

	public Phone(String phoneNummer) {
		this.phoneNummer = phoneNummer;
		System.out.println("📞 Phone ready: " + this.phoneNummer);
	}

	@Override
	public void subscribe(Consumer<String> subscriber) {
		this.subscribers.add(subscriber);
	}

	@Override
	public void publish(String message) {
		System.out.println("📞 Sending message: " + message);
		subscribers.forEach(subscriber -> subscriber.accept(message));
	}
}

```

The `Phone` manages a list of `Headset` recipients. When a message is published, all registered recipients are notified. This shows how asynchronous communication can be modeled in an object-oriented way without relying on message brokers or event systems.

**Interface for Recipients:**
```java
public interface Headset extends Consumer<String> {
    void accept(String message);
}
```

The `Headset` interface defines a recipient who can receive messages.

## **7. The Acts and Scenes (The Domain Processes)**

A film is divided into acts, which in turn consist of scenes. Each act tells a self-contained part of the story. In code, acts correspond to the domain processes, which are broken down into scenes.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_07_code_like_movie.png)
```
manuscript/
...
├─ wedding/           ← ACT 1: The Wedding
│  ├─ video/
│  │  ├─ LazyVideo.java           ← Lazy-Loading Video (Proxy)
│  │  └─ RealVideo.java           ← Actual Video
│  ├─ WeddingCeremony.java        ← Main Scene of the Ceremony
│  ├─ RingExchange.java           ← Atomic Scene: Ring Exchange
│  ├─ CallTheBand.java            ← Scene: Call the Band
│  ├─ InviteTheGuests.java        ← Scene: Invite the Guests
│  ├─ PayTheBand.java             ← Scene: Pay the Band
│  ├─ LoveStoryFlashback.java     ← Scene: Video Flashback
│  ├─ RingBasket.java             ← Medium for Ring Exchange
│  ├─ Video.java                  ← Interface for Video Files
│  └─ VideoCanvas.java            ← Medium for Video Playback
│
├─ honeymoon/         ← ACT 2: The Honeymoon
│  ├─ HotelCheckIn.java           ← Scene: Hotel Check-in
│  └─ SunsetDinner.java           ← Scene: Dinner at Sunset
│
├─ birth/             ← ACT 3: The Birth
│  ├─ BirthScene.java             ← Scene: Birth of the Child
│  └─ Hospital.java               ← Location: Hospital
│
├─ familylife/        ← ACT 4: Family Life
│  ├─ MorningRoutine.java         ← Scene: Morning Routine
│  └─ Bedtime.java                ← Scene: Bedtime Story
...
```

Each act is organized as its own package. The scenes within an act implement the `Scene` interface and encapsulate a self-contained domain process.

### **7.1 Act 1: The Wedding**

The first act tells the story of the wedding. It consists of several scenes that play out in sequence.

**Scene: Call the Band (CallTheBand)**

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

The scene `CallTheBand` orchestrates a phone call. The groom (equipped with a phone) calls the band. The scene itself is lean: it delegates the action to the actor and frames it.

**Scene: Invite the Guests (InviteTheGuests)**

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

The scene `InviteTheGuests` iterates over all guests and has the bride (equipped with email functionality) send each one an invitation. This shows how loops and iterations are modeled in domain terms: the scene orchestrates, the actors act.

**Scene: The Ring Exchange (RingExchange)**

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

The ring exchange is an atomic scene: it orchestrates the handover of rings between the bride and groom. The `RingBasket` serves as the medium for the exchange. This scene shows how domain processes are modeled through the choreography of actors.

**Medium: The Ring Basket (RingBasket)**

```java
public class RingBasket extends Media.Default {}
```

The `RingBasket` is a specialized medium for the ring exchange. It inherits from `Media.Default` and requires no additional logic, since all the semantics are already defined by the actors and the scene.

**Scene: Pay the Band (PayTheBand)**

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

The scene `PayTheBand` orchestrates a payment. The groom (as `Payer`) transfers the amount to the band. The scene knows the domain rule: the band's account ID is derived from its name. This keeps the business logic where it semantically belongs.

**Video Flashback with the Proxy Pattern**

The Proxy Pattern is used here to delay the loading of a resource-intensive video until it is actually needed.

**Video Interface:**
```java
public interface Video {
    void show(Media canvas);
}
```

**Real Video (RealVideo):**
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
    public void show(Media canvas) { canvas.with("video", filename);}
}
```

The `RealVideo` simulates the loading of a large file. The constructor calls `load()`, which is time-consuming. If this video were loaded immediately during initialization, the application would be unnecessarily delayed.

**Lazy-Loading Proxy (LazyVideo):**
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

The `LazyVideo` proxy delays the initialization of the real video until the first call to `show()`. This way, the video is only loaded when it is actually intended to be played. This is a classic example of **Lazy Loading**.

**Medium: The Video Canvas (VideoCanvas)**

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

The `VideoCanvas` is a specialized medium for playing back videos. Every time a piece of content is added, it outputs a message. This simulates the visual display.

**Scene: The Love Story Flashback (LoveStoryFlashback)**

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

The scene `LoveStoryFlashback` shows a video on a canvas. Thanks to the Proxy Pattern, the video is only loaded when the scene is actually executed. This shows how design patterns are seamlessly embedded in domain scenes.

**Main Scene: The Wedding Ceremony (WeddingCeremony)**

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

The `WeddingCeremony` is the central scene of the first act. It orchestrates the vows, the ring exchange, the music, and the applause. The scene itself is a composition of smaller actions. This shows how complex domain processes arise through the composition of simple building blocks.

### **7.2 Act 2: The Honeymoon**

The second act tells the story of the honeymoon. Two scenes form this act.

**Scene: Hotel Check-in (HotelCheckIn)**

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

The scene `HotelCheckIn` is narrative: it tells what happens without the actors explicitly acting. This shows that not every scene needs to be a choreography of method calls. Sometimes the narration itself is the action.

**Scene: Dinner at Sunset (SunsetDinner)**

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

This scene is also narrative. It describes a situation without any technical logic being executed. This shows that the code is not only supposed to function, but also to tell a story.

### **7.3 Act 3: The Birth**

The third act tells the story of the birth of the child. It introduces a new character: the newborn.

**Scene: The Birth (BirthScene)**

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

The scene `BirthScene` orchestrates the birth. The hospital admits the mother, the child is born, and it smiles. The scene shows how new characters are introduced into the story.

**Location: The Hospital**

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

The `Hospital` is not an actor but a location. It provides services (such as admitting a patient) without performing itself. This shows that not all classes need to be actors. Some represent locations, services, or resources.

### **7.4 Act 4: Family Life**

The fourth act shows the daily life of the family. Two scenes form this act.

**Scene: The Morning Routine (MorningRoutine)**

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

The morning routine shows how everyday processes are modeled as scenes. The family interacts, and the child responds. The scene is narrative but also shows behavior (the child smiles).

**Scene: The Bedtime Story (Bedtime)**

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

The bedtime story is the ending of the film. It shows that the story does not have to end spectacularly, but can fade out peacefully. This is a deliberate narrative decision: not every story ends with a bang.

## **8. The Stage (The Presentation Layer)**

The stage is the place where the action becomes visible to the audience. In code, this corresponds to the presentation layer: UI components, visual representations, and interactions.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_08_code_like_movie.png)
```
manuscript/stage/
...
├─ accessory/
│  ├─ Action.java                ← Interactive Action
│  ├─ Button.java                ← Interactive Button
│  ├─ CinematicGrid.java         ← Cinematic Grid Layout
│  └─ SpeechBubble.java          ← Speech Bubble for Dialogues
├─ wedding/
│  └─ WeddingCloseUp.java        ← Close-up of the Wedding
├─ Multimedia.java               ← Composite: Component with Sub-Components
├─ Take.java                     ← Interface for Visual Takes
...
```

The stage is strictly separated from the domain. It accesses actors but does not modify them. All interactions flow from the UI to the domain, never the other way around.

### **8.1 The Take**

A take is the visual representation of a scene.

```java
@FunctionalInterface
public interface Take {
    void render();
}
```

The interface `Take` is analogous to `Scene`, but for the presentation. It defines a method `render()` that generates the visual representation.

### **8.2 The Close-up (WeddingCloseUp)**

```java
public class WeddingCloseUp implements Take {

	private final Groom groom;
	private final Bride bride;

	private Button button;

	public WeddingCloseUp(Groom groom, Bride bride) {
		this.groom = groom;
		this.bride = bride;
	}

	@Override
	public void render() {

		System.out.println("\n🎥 === CLOSE-UP: THE WEDDING ===");
		System.out.println("📸 Camera focuses on " + groom.name() + " and " + bride.name());
		System.out.println("🎞️ Recording tape rolling...");

		SpeechBubble groomSpeechBubble = new SpeechBubble("Groom bubble");
		SpeechBubble brideSpeechBubble = new SpeechBubble("Bride bubble");

		CinematicGrid grid = new CinematicGrid();
		grid.add(groomSpeechBubble);
		grid.add(brideSpeechBubble);

		button = new Button("Perform Wedding", () -> {

			try {
				RingBasket ringBasket = new RingBasket();
				// groom.giveRing(ringBasket);
				bride.receiveRing(ringBasket);
				bride.giveRing(ringBasket);
				groom.receiveRing(ringBasket);
			} catch (Exception e) {
				// Print the trace
				e.printStackTrace();
			}

			groom.speak(groomSpeechBubble);
			bride.speak(brideSpeechBubble);

			System.out.println("📺 The image appears on the screen...");
			grid.render();

		});

	}

	public void clickOnButton() {
		System.out.println("🔘 Button clicked");
		button.click();
		System.out.println("🎥 === END OF CLOSE-UP ===\n");
	}
}
```

The `WeddingCloseUp` shows how the presentation layer is structured: a `CinematicGrid` organizes the visual elements, `SpeechBubble` displays dialogues, and `Button` enables interactions. The take itself only accesses the domain in a read-only manner and generates a visual representation.

### **8.3 The Graphical Components**

**Component within a Component (Multimedia):**
```java
public abstract class Multimedia extends Media.Default {

    	private Name name;
    	protected List<Media> elements = new ArrayList<>(0);
    
    	public Multimedia(Name name) {
			this.name = name;
    	}
    
    	public Name name() { return name;}
    
    	public void add(Media media) {
			elements.add(media);
    	}
    
    	public abstract void render();
}
```
The **Composite Pattern** is a structural design pattern that allows objects to be organized in tree structures to represent part-whole hierarchies. The decisive advantage is that clients can treat individual objects (leaves) and groups of objects (composites) uniformly through a shared interface.

**Speech Bubble (SpeechBubble):**
```java
public class SpeechBubble extends Media.Default {

	private String label;

	public SpeechBubble(String label) {
		this.label = label;
	}

	@Override
	public String toString() {
		return label + ": " + content.getOrDefault("text", "");
	}

}
```

The `SpeechBubble` lets an actor speak into an `Air` medium and displays the result. It is a purely presentational element (like an input text field).

**Interactive Button (Button & Action):**
```java
public class Button {

    private String label;
    private Runnable action;

    public ActionButton(String label, Runnable action) {
        this.label = label;
        this.action = action;
    }

    public void click() { action.run();}

    @Override
    public String toString() { return "[" + label + "]"; }
}
```
The Command Pattern encapsulates an action.

```java
public interface Action extends Runnable {
	
}
```
The `Button` encapsulates an action that is executed on click. It is an example of the Command Pattern in the UI.

**Cinematic Grid (CinematicGrid):**
```java
public class CinematicGrid extends Multimedia {

     public CinematicGrid() {
        super(new Name("CinematicGrid"));
     }
     
     public void render() {
        System.out.println("📺 Render " + name().value() + "\n" + toString());
     }
     
     @Override
     public String toString() {
         String text = "";
         for (Media media : elements) {
            text = text + "📺 Render " + media.toString() + "\n";
         }
         return text;
     }
}
```

The `CinematicGrid` collects visual elements and displays them in a list. This is a greatly simplified representation of a layout.

## **9. The Direction (The Composition Root)**

The direction is the control center. It orchestrates the entire staging, instantiates all actors, builds the infrastructure, and fills the film reel with scenes.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_09_code_like_movie.png)

```
manuscript/directing/
...
└─ AnalogMovie.java              ← Composition Root & Entry Point
```

The class `AnalogMovie` is the only place in the system that knows all dependencies. It builds the object network and starts the staging.

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
   	    // Visual Close-up & Interaction
   	    WeddingCloseUp visualTake = new WeddingCloseUp(romeo, julia);
   	    filmRoll.add(() -> {
   	        visualTake.render();
   	        visualTake.clickOnButton(); // Simulating the user's choice
   	    });

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

The `AnalogMovie` class shows the entire orchestration:

1. **Build infrastructure:** Database, phone, payment API, and email service are initialized.
2. **Cast the actors:** Bride, groom, child, guests, and band are created.
3. **Costume the actors:** Decorators equip the main characters with additional capabilities (phone, payment card, email, database).
4. **Fill the film reel:** All scenes are added to the film reel in the correct order.
5. **Play the film:** The scenes are executed one by one.

The direction is the only place that knows all dependencies. No other package may access it. This guarantees that the domain remains independent and the technical orchestration is isolated.

**The Flow of the Staging**

When the `main` method is executed, the entire staging runs. The console displays a living narrative:

1. **Scene 1:** The groom calls the band. The phone publishes the message, and the band confirms.
2. **Scene 2:** The band wants to be paid. The `StripePayment` translates Romeo's domain-level `pay()` into the technical language `execute()` of the Stripe API.
3. **Scene 3:** The video flashback starts. Thanks to the **Proxy Pattern**, the resource-intensive film is only loaded at the moment the prompter provides the canvas.
4. **Acts 2 through 4:** Life unfolds — from the clinic to the bedtime story — cleanly separated into acts and scenes. The remaining acts of the life story play out in sequence.

## **10. The Cinematic Stack Trace**

The quality hallmark of a good structure shows itself at the moment of failure. While in classical systems an error typically forces you to struggle through cryptic framework classes and generic service layers, the error log in the feature film philosophy reads like a dramatic summary of the film.


![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_10_code_like_movie.png)

```
Exception in thread "main" java.lang.IllegalStateException: Wedding ring is missing!
	at manuscript.character.Bride.receiveRing(Bride.java:45)
	at manuscript.stage.take.WeddingCloseUp.lambda$0(WeddingCloseUp.java:46)
	at manuscript.stage.accessory.Button.click(Button.java:16)
	at manuscript.stage.take.WeddingCloseUp.clickOnButton(WeddingCloseUp.java:66)
	at manuscript.directing.AnalogMovie.lambda$0(AnalogMovie.java:103)
	at manuscript.directing.AnalogMovie.play(AnalogMovie.java:127)
	at manuscript.directing.AnalogMovieTest.main(AnalogMovieTest.java:9)
```

Not only the bride, but the stack trace too "shouts" the domain out loud. You see no generic `DataUpdateException` in an `AbstractServiceFactoryProxy`, but a concrete drama on set:

* Who? The bride (`Bride`).
* What? She was unable to receive the ring (`receiveRing`).
* In what costume? While acting as `BrideWithArchive`.
* In what situation? During the close-up (`WeddingCloseUp`).

The technology becomes completely transparent here and steps back behind the narrative. Such a stack trace does not create frustration through incomprehension, but immediate resonance. You recognize at once at which point in the screenplay the narrative was interrupted. Debugging thus transforms from stressful code digging into targeted direction corrections that restore the emotional integrity of the story.

## **11. Blueprint as Screenplay**

To ensure a domain-oriented and progressive narrative structure in the code, a clear separation between the type of communication and the physical organization of the project is required.

**The Guiding Principles (The Directing Instructions)**

These principles define how actors within the system interact in order to preserve encapsulation and autonomy:

**Graphs as Script:** The modeling of the system occurs across three dimensions. The *collaboration graph* maps the ensemble, the *construction graph* regulates the set construction, and the *call graph* describes the dialogue of the objects at runtime.

**Tell Don't Ask:** Interactions happen exclusively through domain-oriented commands. An object is never queried about its state in order to make external decisions; it is directly instructed to act.

**Law of Demeter:** Actors communicate only with their immediate neighbors. This restriction of access paths guarantees loose coupling and protects the staging from semantic instability.

**Rules for the Project Structure (The Table of Contents)**

To keep the story findable for the reader, packaging follows domain rather than technical criteria:

**Domain Centering:** The package structure is not oriented toward technical layers (such as *Service* or *Repository*), but follows the domain concepts hierarchically.

**Focus on Level Zero:** The topmost package level contains exclusively the domain core. This includes *interfaces*, *abstract classes*, *Value Objects*, *entities*, and the central *system interface* as the entry point of the application. This level remains free of technical baggage.

**Separation of Realization:** All concrete implementations and technical details (such as *infrastructure* and *user presentation*) are placed strictly in subordinate packages.

**Hierarchy without Cycles:** A parent package may never depend on its sub-packages. Cyclic dependencies must be strictly excluded to preserve a directed flow of information. The use of sub-packages is permissible, provided they do not lie on the same hierarchical level.

**Detail Refinement:** Sub-packages do not introduce new domain concepts. They serve only to elaborate on the concepts from the parent level.

**Domain Language (Ubiquitous Language):** The naming of *packages*, *classes*, and *methods* follows the language of the real domain world, not the technical implementation.

## **12. Closing Words: Code That Tells Stories**

To conceive of software as a screenplay means to leave the world of lifeless data containers and step onto a stage where the domain becomes visible. The strict separation of backstage machinery and direction orchestration frees the domain of technical baggage. What remains is the pure narrative — clear, human, and comprehensible.

The cinematic structure is ultimately an emotional investment. It transforms a lifeless machine into an understandable story. When we replace the stress of debugging with the clarity of a stack trace that tells a story, we gain not only time, but also joy in our work. Software development is thus also the art of building systems whose story one is happy to share.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/008_11_code_like_movie.png)

**The New Role of the Developer as Director**

The feature film philosophy thus goes beyond merely filling in technical templates or gluing together AI output, and transforms reading code into an intuitive experience. The role of the developer evolves here into that of director and author. Let us therefore become storytellers and create code that not only works, but brings its own meaning precisely onto the screen.

**Cut! Roll film!**

## References

* Imogen Short (2025): [The neuroscience of storytelling](https://www.linkedin.com/pulse/neuroscience-storytelling-why-stories-make-us-remember-imogen-short-c23ec/)
* Robert Bräutigam (2021): [Next Level Readability](https://www.informatik-aktuell.de/entwicklung/programmiersprachen/next-level-lesbarkeit.html) (in German)  
* Robert Bräutigam (2017): [Happy Packaging](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
* Mark Seemann (2011): [Composition Root Pattern](https://blog.ploeh.dk/2011/07/28/CompositionRoot/)
* Mark Seemann (2011): [Compose object graphs with confidence](https://blog.ploeh.dk/2011/03/04/Composeobjectgraphswithconfidence/)
* Miško Hevery (2008): [Where Have All the Singletons Gone?](https://testing.googleblog.com/2008/08/where-have-all-singletons-gone.html)
* Yegor Bugayenko (2016): [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html)

* Wikipedia (2026): [Adapter Pattern](https://en.wikipedia.org/wiki/Adapter_pattern)
* Wikipedia (2026): [Composite Pattern](https://en.wikipedia.org/wiki/Composite_pattern)
* Wikipedia (2026): [Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)
* Wikipedia (2026): [Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern)
* Wikipedia (2026): [Proxy Pattern](https://en.wikipedia.org/wiki/Proxy_pattern)
