# **Teach your Objects to speak to the User "otherwise they will be dumb…"**

**TL;DR:** Through Object-Oriented User Experience (OOUX) and the concept of "UI of Objects," you create a seamless object-oriented connection from backend and UI design deep into the source code. Your objects will no longer be silent; they will speak to the user.

---

> To prevent your objects from remaining silent (dumb), you must give them a method to translate their internal state (data, function, or existence) into a user-understandable language (text, visual feedback, or narrative dialogue).

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ui_of_objects_meme.png)

*(The most common argument against user interface logic in the domain objects)*

## **Introduction and Target Audience**

Most of modern business applications—especially in the context of layered architectures like Domain-Driven Design or Clean Architecture—suffer from a paradigm shift at odds with true object orientation: while the business core is highly decoupled, backend and frontend components often dull into anemic, stateless, procedural structures.

This article shows developers and architects which tools and OOP principles they can use to bridge this paradigm gap. It presents OOUX (Object-Oriented User Experience) as a method and the concept of the "UI of Objects" to ensure a consistent domain identity from interface design deep into the source code in an object-oriented manner.

## **1. The Silence of the Objects**

A Silent Party

Imagine you walk into a party. You approach someone to strike up a conversation. But instead of that person answering, an assistant (a 'service') rushes over, pulls an ID card out of the person's pocket, reads the name aloud, checks the pulse, and then says: "This person is approachable." The person themselves stands there motionless—a mute shell, defined only by the data they carry around.

Sounds absurd? In modern software development, this is the standard.

The analogy is found in modern "Enterprise" applications where "domain" objects are nothing more than glorified filing cabinets (data containers). They possess drawers (getters and setters) but have no ability to transmit active messages to their users. Instead, they are wheeled around on carts (often misused as [Data Transfer Objects](https://www.yegor256.com/2016/07/06/data-transfer-object.html) (DTOs)), and their contents are dragged into the light and manipulated by separate couriers (usually service or controller classes).

### **1.1 The Problem with Mute Objects**

The status quo of many applications is characterized by architectures that—often citing the [Model-View-Controller pattern](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (MVC)—force a strict separation of layers, creating hard-to-maintain and rigid [data boundaries](https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/).

This practice is primarily fueled by the dominance of mainstream literature and its specific interpretation of concepts like [Clean Architecture](https://github.com/cleancoders/CleanCodeCaseStudy/tree/master/src/cleancoderscom) or [Domain-Driven Design](https://github.com/VaughnVernon/IDDD_Samples). This development is reinforced by the best-practice guides of popular frameworks like [Spring](https://github.com/spring-petclinic/spring-framework-petclinic/tree/main/src/main/java/org/springframework/samples/petclinic), [Jakarta EE](https://github.com/eclipse-ee4j/jakartaee-examples/tree/main/applications/kickoff/src/main/java/org/example/kickoff), or [Hibernate](https://github.com/hibernate/hibernate-demos), which prescribe this layer separation as a standard.

This strict separation of concerns leads to a [misinterpretation](https://martinfowler.com/eaaDev/uiArchs.html) of the model within layered MVC architectures and inevitably results in the [Anemic Domain Model](https://www.martinfowler.com/bliki/AnemicDomainModel.html). Here, the object degenerates into a pure data carrier that holds state but possesses little to no behavior of its own.

By outsourcing the entire business logic into service or controller classes, encapsulation is broken; the necessary getters and setters create unwanted [semantic coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) between components. This type of coupling occurs when a class needs knowledge about the internal structure of another class. If, for example, a UI `controller` calls the sequence `user.getAddress().getCity().getZipCode()`, it is semantically coupled to the structure of the `User` object. This is also a direct violation of the [Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/) (Principle of Least Knowledge). If the internal structure of the user changes (e.g., the `address` is replaced by a `location` class), the entire call chain in the UI breaks, even though the actual domain logic remains intact.

The core problem is rooted in traditional UI design, which focuses primarily on actions and workflows—what the user wants to do (**verbs**). A music application, for example, focuses on functions like "Play Music" instead of placing objects like Song or Album as acting entities in the foreground. This procedural view leads software structures to mirror UI workflows. Even modern approaches like Domain-Driven Design (DDD) and Clean Architecture often fall into this "action trap" in practice: instead of strengthening the autonomy of domain objects, their implementation examples often place Use Cases or Application Services at the center. These function like stateless helper classes that control the workflow and degrade the object to a pure data provider.

### **1.2 The Solution: Speaking Objects**

While traditional UI design often revolves around tasks and actions, the modern approach of **Object-Oriented UX (OOUX)** shifts the focus to the real objects with which users interact. Users think of real things ("I am archiving this email") rather than database rows ("I am setting the status flag of Entity ID 123 to 'ARCHIVED'"). This mindset implies the term "User Interface of Objects," which has been significantly shaped and propagated in the modern software architecture debate by Robert Bräutigam.

**The UI Debate: Does Presentation Belong in the Domain?**

The suggestion that domain objects should present themselves is controversial but essential for true cohesion.

**Contra:** DDD experts (like Vaughn Vernon) and followers of Clean Architecture (per Robert C. Martin, "Uncle Bob") often point to a break in abstraction and technical coupling of the domain to specific UI libraries (e.g., React or JavaFX). This, they argue, reduces the reusability of domain objects. They maintain that a domain object should be exclusively responsible for business logic; UI code inside the object violates the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) **(SRP)**. According to this, a class should have only one reason to change: if the UI layout changes, the business class must not need adjustment.

**Pro:** Proponents (like Allen Holub and Robert Bräutigam) argue for **High Cohesion**. Knowledge of how a customer is represented in a list or an editable form belongs to the Customer object itself, as this is part of its responsibility. The representation should be viewed as part of the requirements and the [Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html) of the business domain.

* The crucial differentiation lies in abstraction: the object communicates via abstract interfaces instead of concrete *HTML tags* or [CSS](https://en.wikipedia.org/wiki/CSS) classes. Instead of revealing data via getters (which creates coupling), they use output-specific interfaces (e.g., Media, InfoPanel, TextInput) to invert the UI dependency ([Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)).  
* Visual design aspects, such as fonts or colors (e.g., bold for names, italics for IDs), can optionally be outsourced to the implementation of the UI components, to central [Look-and-Feel](https://en.wikipedia.org/wiki/Look_and_feel) classes, or global CSS definitions ([Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)).  
* Furthermore, philosophical and sociological SRP definitions are criticized for providing little clear guidance due to their subjectivity: terms like *responsibility*, *reason for change*, or orientation toward *actors* are too vague for practical implementation and often lead to unnecessary code fragmentation. Robert Bräutigam proposes a pragmatic definition instead: **SRP ≡ Maximum Cohesion ∧ Minimum Coupling**. By equating it with cohesion and coupling, SRP moves from a design philosophy to a structural metric ([Lack of Cohesion of Methods](https://en.wikipedia.org/wiki/Programming_complexity)). While "responsibility" can be debated, cohesion (e.g., via the LCOM value) and coupling (dependency graph) can be objectively proven in the code.

An object should therefore act like a real subject (speak) and animate its data instead of merely mapping it. One should not ask: **"Give me your title so I can display or transmit it."** Instead, one says: **"Here is a stage (e.g., a UI control or interface)—please present yourself there."** This is the application of the design principle [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html) in its purest form.

## **2. Object-Oriented User Experience (UX) and Interface (UI)**

From Mental Model to Visual Representation

### **2.1 Object-Oriented UX**

OOUX is a design approach that applies the same concepts as object-oriented programming. It focuses on identifying and prioritizing objects—conceptual entities that map to real-world elements, ideas, people, places, or events.

Unlike traditional, task-oriented UX design, which focuses on the sequence of actions (**verbs**) and can lead to complex, branching interfaces as the number of tasks grows, OOUX puts objects (**nouns**) at the center. OOUX organizes the user interface around entities (e.g., `Album`, `Song`), resulting in a more efficient, entity-centric structure. Actions are logically attached to the objects themselves. This alignment with users mental models (e.g., thinking about "Emails" or "Contacts" rather than "Send" or "Sort") creates intuitive user experiences.

**Principles of OOUX Implementation:**

* **Objects:** The building blocks of the system (e.g., `Product`, `User`).  
* **Structure:** The definition of each object's properties.  
* **Relationships:** Mapping how objects interact (e.g., a one-to-many relationship between a user and their orders). Clear definition of these relationships is essential for creating coherent navigation paths.  
* **Actions:** The interactions users can perform with objects.  
* **Context:** Understanding that objects behave differently depending on the context (`list view` vs. `detail view`) but must maintain the same core identity.  
* **Modular Thinking:** Designing objects as reusable components. This modular approach promotes design system consistency.

To bridge the gap between software architecture and UI design, the OOUX framework offers the **ORCA process** (Objects, Relationships, Capabilities, Attributes) by Sophia Prater. The process spans four phases and demands that designers think in stable "objects" rather than fleeting "pages" (views). It strictly ensures that these objects correspond to the user's mental world (Domain Model). The methodology is oriented toward navigation models based on the logical structure of the data rather than arbitrary menu structures. Furthermore, it consistently distinguishes between core content and metadata.

* **Phase 1: Objects** First, the "things" that are real for the user and the business are identified (e.g., `Book`, `Author`, `Order`).  
* **Phase 2: Relationships** Here, it is defined how objects relate to each other (e.g., "An author writes many books").  
* **Phase 3: Capabilities** What can be done with the objects? (e.g., Publish, Archive, Print).  
* **Phase 4: Attributes** Only in the last step are specific data fields defined (e.g., Title, ISBN, Price).

For the practical application of the process, the guide from [Rewired](https://www.rewiredux.com/ooux) UX is highly recommended.

### **2.2 Object-Oriented UI (OOUI)**

The success of OOUX depends heavily on how these conceptual objects are visually brought to life. This is where Object-Oriented UI (OOUI) comes in. OOUI utilizes interactive objects by adding textures and dimensions inspired by the real world. OOUI is the visual and operational implementation of the OOUX structure. It aims to ensure that users perceive abstract data as concrete "things" (objects) that they can "touch" in the interface.

**Visual Techniques:**

* **Textures and Materiality:** Incorporating textures that resemble real materials (paper, metal) to make digital elements more familiar and tangible.  
* **Depth and Shadows:** Utilizing realistic shadows and layers to create hierarchy and spatial relationships. This guides user attention and visualizes interaction possibilities.  
* [Neumorphism](https://en.wikipedia.org/wiki/Neumorphism): A design trend that, by combining [Skeuomorphism](https://en.wikipedia.org/wiki/Skeuomorph) and [Flat Design](https://en.wikipedia.org/wiki/Flat_design), makes UI elements appear softly embossed or debossed from the background material. This technique creates a subtle sense of physicality, making digital buttons look as if they could actually be pressed.

**Advantages of the Combination:**

Adopting this holistic approach consistently aligns design with users' mental models, promotes consistency and reusability across the design system, and improves scalability. As systems grow, OOUX's modular approach allows new features or objects to be added without disrupting the existing domain structure, enhancing long-term maintainability.

## **3. User Interface of Objects**

From Design to Code with Encapsulation of Representation

While classic [OOUI terms](https://en.wikipedia.org/wiki/Object-oriented_user_interface) originate from IBM or Dave Collins, Robert Bräutigam is the one who uses the specific term "UI of Objects" in the context of modern architecture and DDD as a solution for the "Anemic Domain Model."

### **3.1 Core Principles of UI of Objects**

* **Object at the Center:** The UI organizes itself around domain objects (Customer, Product), not generic actions or tables.  
* **Strong Cohesion:** The UI structure (what is displayed and which fields are necessary for interaction) is viewed as a natural property of the object. The object knows how to represent itself in different contexts (displaySummary(), displayFullDetails()).  
* **Behavior instead of Data:** The UI component asks the object to do something instead of querying data (e.g., person.displayEditForm(uiContext)).  
* **Composition:** Complex interfaces are created through the composition of smaller, self-representing objects.

### **3.2 MVC Roles in UI of Objects**

Instead of fragmenting data into Controller and View, the roles of MVC are viewed as follows in the context of UI of Objects:

* **Model:** The domain object itself (immutable, with behavior and presentation knowledge).  
* **Controller:** As an (abstract) UI control element that forwards the user's actions (e.g., click, submit) as a command to the model as a message, rather than querying its data.  
* **View:** The concrete rendering logic (HTML, JSF tags, React components).

### **3.3 Mechanics of UI of Objects**

Because users think in objects, the code must mirror this. But if getters are banished, how do we access data?

The answer is: *Not at all*.

We let the object do the work and maintain control. The object acts as an immutable [animator of mutable data](https://www.yegor256.com/2014/12/09/immutable-object-state-and-behavior.html). A fundamental distinction is made between **State, Identity, and Behavior** of an object:

* **State:** This is data—static and potentially mutable information (in the data source like a database, JSON, etc.).  
* **Identity:** An object's identity is its immutable uniqueness (e.g., its memory address or its unique ID).  
* **Behavior:** This is the living shell around the data. It must be **immutable** to ensure its integrity and avoid race conditions.

A good object doesn't just map data from storage; instead, it acts as an **Immutable Proxy**. It represents the identity of an entity in the domain without storing its constantly changing state in internal fields.

One possible approach to implementing the aforementioned "*animation*" of data in objects is to forgo getters in favor of **Printers**. As Yegor Bugayenko explains in his blog post [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html), we don't ask an object for its state just to laboriously map it into another format. Instead, we give the object a target — a *printer* — and let it manifest itself there. The object remains master of its data, and we achieve a clean separation between the object's logical essence and its technical representation. By removing getters, you force yourself to put behavior into the objects. The object turns from a passive data structure into an active, "living" subject.

Imagine a Person object.

* **Traditional:** The object loads title and address into memory at start. If the external database changes, the object is stale, and encapsulation is only apparent.  
* **Immutable Proxy:** The UI object holds only one piece of information: its **identity** (e.g., ID 50). It is a proxy for the true data source.

When `person.title()` is called, it doesn't return a stored string; instead, it goes to the data source at that moment and retrieves it (or animates it). The object says: **"I am not the title. I am the representative of document number 50, and I know where the title is and how to format and present it."**

Instead of procedural:

```java
// View pulls data from the object (Pull)      
String title = person.getTitle(); // -> violates Tell, Don't Ask

// ...data mapping.      
render(title);
```

We do object-oriented:

```java
// Object animates its data on a printer (Push and Tell)      
person.displayTitle(inputTitlePanel);

// alternatively with return of UI controls (only Tell)

// Object creates part of the view itself     
InputText inputTitle = person.displayTitle();

// ...or the complete, editable view of itself    
InputPanel panelPerson = person.displayInput();
```

**Modular Composition:** A Person object delegates responsibility for its representation to its nested objects (Address), consistently applying the 'Tell, Don't Ask' principle.

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

The Person knows how to represent itself for input by composing itself from smaller, self-representing objects (Address). The object maintains full control over how and when its data is exposed, protecting its internal rules. This allows the principle of [Data Animation](https://amihaiemil.com/2017/09/01/data-should-be-animated-not-represented.html) to be consistently implemented by having the object actively instruct the view (push principle) and handle representation, validation, and formatting itself.

**Abstract UI:** To decouple the domain from specific UI libraries, an additional abstraction of UI objects can be used. One approach is what Allen Holub calls a [Bidirectional Builder](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html), which is a combination of the Builder and Visitor patterns.

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

### **3.4 Client User and API Serialization**

The **Tell, Don't Ask** principle can be applied not just to logic but also to **serialization**. Instead of the API layer (e.g., JAX-RS or Spring Controller) extracting data from the domain object and serializing it via an external mapper (like Jackson), the object can be instructed to **write itself to the response stream or generate the response**.

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

This approach is consistently object-oriented: **the domain object speaks directly to the client**, while the API layer merely carries transport and protocol responsibility. It fully controls how it is represented—whether on a UI control or in a JSON stream. This maximizes cohesion and eliminates the "service" layer.

## **4 Delineation: UI vs. Domain**

A common misunderstanding with "speaking objects" is the fear of mixing UI code into the domain. Here are the clear dividing lines:

### **4.1 Business Logic in UI Code**

When is it permissible to have business logic in UI code?

Actually: **Never**. But there is one exception often mistaken for business logic: UI logic.

* **Synchronization:** If a button is disabled because a field is empty, that is UI logic (User Experience).  
* **Formatting:** That a date is displayed as "2 days ago" instead of "05/12/2024" is UI logic.  
* **Dependencies:** The UI may know: "If the user selects 'Private Customer', hide the 'VAT ID' field." This is layout control based on the object's state.

### **4.2 UI Code Smell**

When is it a problem (code smell)?

The architecture corrodes if:

* **Calculations in the View:** If the UI template calculates: `if (user.age > 18 && user.hasBalance)`. This belongs in the method `user.isEligibleForPurchase()`.  
* **Database Knowledge in the UI:** If the UI knows that an object is "dirty" or needs to be saved.  
* **Duplication:** If you have to write the same if-condition in the **web app**, the **mobile app**, and the **export module**.  
* **Import Smell:** If classes like Color or Layout appear in your domain class (e.g., `Invoice.java`).

### **4.3 Separation of Domain and Presentation**

The separation of domain and presentation layers can be viewed technically as follows:

The domain manages state and business logic. It defines the business intent (e.g., a status change or a specific context condition). The UI handles concrete representation and technical implementation. It decides how this business intent is translated visually or haptically for the user.

**The Mistake:** When the domain dictates technological details (e.g., "Set CSS class X to active"). As soon as the UI technology is changed, the domain logic loses its validity and must be adjusted.

**The Solution:** The domain defines an abstract requirement (e.g., signaling a warning). How the technical layer implements this—whether through a color change, an icon, or an acoustic signal—is solely up to the implementation of the UI component.

## **5. Practical Application**

Using a typical login process, we demonstrate how OOUX and UI of Objects principles are applied in practice.

### **5.1 Application of the OOUX Methodology**

In the OOUX framework, identifying real objects is the focus before thinking about screens or flows. A login process is not viewed as a mere workflow, but as an interaction between objects. Here is a user's mental model for a login example using the OOUX methodology (ORCA process):

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ux_user_mental_model_nb.png) 

#### **5.1.1 The Core Objects**

The user does not think of "code" during login, but of digital representations of real things:

* **Account:** The overarching object representing access to the service.  
  * *Attributes:* Email address, username, password (hidden), security status.  
* **Identity:** Who am I? (Private person vs. business profile).  
* **Permission:** What am I allowed to do once I'm in?  
* **Session:** The state of being logged in on a specific device.

#### **5.1.2 Relationships**

The user's mental model links these objects:

* A user owns one or more accounts.  
* An account is linked to an email address.  
* A session belongs to exactly one account on a specific device.

#### **5.1.3 Actions (Capabilities)**

In OOUX, actions are directly linked to objects. The user asks: "What can I do with this object?"

* **Account:** Create, verify, **authenticate (login)**, delete.  
* **Session:** Start (login), end (logout), extend (stay signed in).  
* **Password:** Change, reset (if forgotten).

#### **5.1.4 Mental Model: The Lock-and-Key Principle**

The user navigates the login process with the following mental schema:

1. **Identification:** I tell the system which account belongs to me (email/username).  
2. **Authentication:** I prove with a key (password/token) that I am the owner.  
3. **Authorization:** The system starts a session and grants me access to my content.

### **5.2 Application in UI Design**

If we want to implement the model from 5.1.4, the UI should reflect these logical units:

* **Grouping:** Place fields for identification (email) and authentication (password) in a clearly defined area (`panel`) representing the login object.  
* **Feedback:** If the password is wrong, the account object is blocked. The error message should refer to the object (account not found or the password for this account is incorrect).  
* **Status Display:** Use a label (e.g., in the header of the main form) showing the identity object (e.g., "Logged in as John Doe") to visualize the active session.

### **5.3 Desktop Client (with Swing)**

To put theory into practice, we look at a Java Swing example. Instead of building a JFrame that pulls data from a user object (`user.getName()`), we flip it. We give the object a "canvas" (interface as a digital workspace) on which it paints itself. The AccountSession object is the boss. It determines what the login looks like and what happens when clicked. The Swing classes are just tools for the presentation.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ux_ui_swing_sample.png)

```java

package com.example.account.desktop;

import java.awt.*;
import java.sql.*;
import javax.sql.*;
import javax.swing.*;


/**
 * OOUX (Object-Oriented User Experience) IMPLEMENTATION GUIDE (ORCA Process)
 * * 5.1 OOUX Methodology Application:
 * Instead of focusing on flows/screens, we focus on real-world objects.
 * A login process is seen as an interaction between objects, not just a procedure.
 */

// ==========================================================
// 1. ABSTRACT CANVAS (The Canvas for OOUX Objects)
// 5.2 UI Design: Logic units are reflected in the interface.
// ==========================================================
interface AccountCanvas {
	
	void addEmailField(String label); // Part of "Identification"

	void addPasswordField(String label); // Part of "Authentication"

	void addSignInAction(String label, SignInAction action); // Capability: Authenticate

	void notifySuccess(String message);

	void notifyError(String message); // Feedback related to the Account object

	void updateIdentityStatus(String identityInfo); // Visualization of the "Identity" object
}

interface SignInAction {
	void authenticate(String email, String password);
}

// ==========================================================
// 2. DOMAIN: IDENTITY (Who am I?)
// 5.1.1 Objects: Represents the "Identity" (e.g., private vs. business profile)
// ==========================================================
interface Identity {
	boolean verify(String secret);
	boolean unknown();
}

final class VerifiedIdentity implements Identity {
	
	private final String email;
	private final String storedSecret;

	public VerifiedIdentity(String email, String storedSecret) {
		this.email = email;
		this.storedSecret = storedSecret;
	}

	@Override
	public boolean verify(String secret) {
		return storedSecret.equals(secret);
	}

	@Override
	public boolean unknown() {
		return false;
	}
	
	@Override
	public String toString() {
		return email;
	}
}

final class AnonymousIdentity implements Identity {
	
	@Override
	public boolean verify(String secret) {
		return false;
	}

	@Override
	public boolean unknown() {
		return true;
	}
	
	@Override
	public String toString() {
		return "Not logged in: Anonymous";
	}
}

// ==========================================================
// 3. DOMAIN: ACCOUNT (The Overarching Object)
// 5.1.1 Objects: The central object representing access to the service.
// Attributes: Email, Password (hidden), Security Status.
// 5.1.3 Capabilities: Create, Verify, Authenticate (Login).
// ==========================================================
interface Account {
	Identity identify(String email);
}

final class DbAccount implements Account {
	
	private final DataSource dataSource;

	public DbAccount(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	@Override
	public Identity identify(String email) {
		String sql = "SELECT password FROM accounts WHERE email = ?";
		try (Connection conn = dataSource.getConnection(); PreparedStatement stmt = conn.prepareStatement(sql)) {
			stmt.setString(1, email);
			try (ResultSet rs = stmt.executeQuery()) {
				if (rs.next())
					return new VerifiedIdentity(email, rs.getString("password"));
			}
		} catch (SQLException e) {
			System.err.println("Domain Error: " + e.getMessage());
		}
		return new AnonymousIdentity();
	}
}

// ==========================================================
// 4. DOMAIN: SESSION (The state of being logged in)
// 5.1.1 Objects: State of being logged in on a specific device.
// 5.1.4 Mental Model: Identification -> Authentication -> Authorization (Session Start)
// ==========================================================
final class AccountSession {

	private final Account account;

	public AccountSession(Account account) {
		this.account = account;
	}

	public void displayOn(AccountCanvas canvas) {
		
		// 5.1.4 Identification: Telling the system which account belongs to me
		canvas.addEmailField("Identification (Email)");
		// 5.1.4 Authentication: Proving ownership with a key (Password)
		canvas.addPasswordField("Authentication (Password)");

		canvas.addSignInAction("Sign In", (email, pass) -> {
			
			if (email.isEmpty() || pass.isEmpty()) {
				canvas.notifyError("Please fill in all fields.");
				return;
			}

			Identity identity = account.identify(email);
			if (identity.unknown()) {
				// 5.2 Feedback: Error relates to the Account object
				canvas.notifyError("Account not found.");
			} else if (identity.verify(pass)) {
				// 5.1.4 Authorization: System starts a session and grants access
				canvas.updateIdentityStatus("Logged in as: " + email);
				canvas.notifySuccess("Session active.");
			} else {
				// 5.2 Feedback: Object is blocked/access denied
				canvas.notifyError("Authentication failed for this account.");
			}
			
		});
	}
}

// ==========================================================
// 5. INFRASTRUCTURE: SWING IMPLEMENTATION
// 5.2 UI Design - Grouping & Feedback
// ==========================================================
final class SwingAccountPanel extends JPanel implements AccountCanvas {
	
	private static final long serialVersionUID = 1L;
	private final JTextField emailField = new JTextField("john.doe@example.com");
	private final JPasswordField passwordField = new JPasswordField("secure123");
	private final JLabel statusLabel = new JLabel(" ", SwingConstants.CENTER);
	private final SwingMainForm parentForm;

	public SwingAccountPanel(SwingMainForm parentForm) {
		this.parentForm = parentForm;
		setPreferredSize(new Dimension(350, 260));
		setLayout(new GridLayout(6, 1, 5, 5));
		// 5.2 Grouping: Fields placed in a clearly defined area (Panel)
		setBorder(BorderFactory.createCompoundBorder(
				BorderFactory.createLineBorder(new Color(200, 200, 200)),
				BorderFactory.createEmptyBorder(20, 20, 20, 20)));
		setBackground(Color.WHITE);
		statusLabel.setFont(new Font("SansSerif", Font.BOLD, 11));
	}

	@Override
	public void addEmailField(String label) {
		add(new JLabel(label));
		add(emailField);
	}

	@Override
	public void addPasswordField(String label) {
		add(new JLabel(label));
		add(passwordField);
	}

	@Override
	public void addSignInAction(String label, SignInAction action) {
		JButton button = new JButton(label);
		button.setBackground(new Color(41, 128, 185)); // Action tied to the object
		button.setForeground(Color.WHITE);
		button.setFocusPainted(false);
		button.setOpaque(true);
		button.setBorderPainted(false);

		button.addActionListener(e -> {
			statusLabel.setText(" ");
			action.authenticate(emailField.getText(), new String(passwordField.getPassword()));
		});
		add(button);
		add(statusLabel);
	}

	@Override
	public void notifySuccess(String message) {
		statusLabel.setForeground(new Color(39, 174, 96));
		statusLabel.setText(message);
	}

	@Override
	public void notifyError(String message) {
		statusLabel.setForeground(new Color(192, 57, 43));
		statusLabel.setText(message);
	}

	@Override
	public void updateIdentityStatus(String identityInfo) {
		parentForm.setIdentityHeader(identityInfo);
	}
}

class SwingMainForm extends JFrame {
	private static final long serialVersionUID = 1L;
	// 5.2 Status Display: Label in header visualizing the active "Identity" object
	private final JLabel identityLabel = new JLabel("Status: Not logged in", SwingConstants.RIGHT);
	private final JPanel centerWrapper = new JPanel(new GridBagLayout());

	public SwingMainForm() {
		setTitle("OOUX Login Framework (ORCA Process)");
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		setSize(600, 500);

		JPanel header = new JPanel(new BorderLayout());
		header.setBorder(BorderFactory.createEmptyBorder(10, 20, 10, 20));
		header.add(identityLabel, BorderLayout.EAST);
		add(header, BorderLayout.NORTH);

		centerWrapper.setBackground(new Color(245, 245, 245));
		add(centerWrapper, BorderLayout.CENTER);
	}

	public void setIdentityHeader(String text) {
		identityLabel.setText(text);
		identityLabel.setForeground(new Color(41, 128, 185));
	}

	public void addContent(JPanel contentPanel) {
		centerWrapper.removeAll();
		centerWrapper.add(contentPanel);
		centerWrapper.revalidate();
		centerWrapper.repaint();
	}

	public void display() {
		setLocationRelativeTo(null);
		setVisible(true);
	}
}

// ==========================================================
// 6. APPLICATION START
// ==========================================================
public class SwingAccountApplication {
	public static void main(String[] args) {
		System.setProperty("awt.useSystemAAFontSettings", "on");
		System.setProperty("swing.aatext", "true");

		SwingUtilities.invokeLater(() -> {
			try {
				UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());

				DataSource ds = setupDatabase();
				Account account = new DbAccount(ds);
				AccountSession session = new AccountSession(account);

				SwingMainForm mainForm = new SwingMainForm();
				SwingAccountPanel loginPanel = new SwingAccountPanel(mainForm);

				session.displayOn(loginPanel);

				mainForm.addContent(loginPanel);
				mainForm.display();

			} catch (Exception e) {
				e.printStackTrace();
			}
		});
	}

	private static DataSource setupDatabase() {
		org.h2.jdbcx.JdbcDataSource ds = new org.h2.jdbcx.JdbcDataSource();
		ds.setURL("jdbc:h2:mem:ooux_db;DB_CLOSE_DELAY=-1");
		ds.setUser("sa");
		try (Connection conn = ds.getConnection(); Statement st = conn.createStatement()) {
			st.execute("CREATE TABLE accounts(email VARCHAR(255) PRIMARY KEY, password VARCHAR(255))");
			st.execute("INSERT INTO accounts VALUES('john.doe@example.com', 'secure123')");
		} catch (Exception e) {
			e.printStackTrace();
		}
		return ds;
	}
}
```

### **5.4 Backend API (with JAX-RS)**

The following backend implementation demonstrates how the **Tell, Don't Ask** principle is transferred to a web service. Instead of the controller orchestrating the logic procedurally, it delegates the entire authentication and response generation to the domain objects. The `ApiAccount` acts as a specialized decorator that translates the domain outcome directly into the HTTP protocol.

```java
package com.example.account.api;

import jakarta.ws.rs.*;
import jakarta.ws.rs.core.Application;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;
import jakarta.ws.rs.core.Response.Status;
import jakarta.inject.Inject;
import jakarta.annotation.PostConstruct;
import jakarta.servlet.ServletContext;
import javax.sql.*;
import java.sql.*;


/**
 * JAX-RS Configuration. Base path: /api
 */
@ApplicationPath("/api")
class ApplicationResource extends Application {
}

/**
 * DTO for Login Requests. Represents attributes of the mental model:
 * Identification & Authentication.
 */
final class Credentials {
	
	private String email;
	private String password;

	public String getEmail() {
		return email;
	}

	public String getPassword() {
		return password;
	}

	public boolean isValid() {
		return email != null && !email.trim().isEmpty() && password != null && !password.trim().isEmpty();
	}
}

// ==========================================================
// OOUX DOMAIN: OBJECTS (Core of the ORCA Process)
// ==========================================================

/**
 * 5.1.1 Object: Identity - "Who am I?"
 */
interface Identity {
	boolean verify(String secret);
	boolean unknown();
}

final class VerifiedIdentity implements Identity {
	
	private final String email;
	private final String storedSecret;

	public VerifiedIdentity(String email, String storedSecret) {
		this.email = email;
		this.storedSecret = storedSecret;
	}

	@Override
	public boolean verify(String secret) {
		return storedSecret.equals(secret);
	}

	@Override
	public String toString() {
		return email;
	}

	@Override
	public boolean unknown() {
		return false;
	}
}

final class AnonymousIdentity implements Identity {
	
	@Override
	public boolean verify(String secret) {
		return false;
	}

	@Override
	public String toString() {
		return "Not logged in";
	}

	@Override
	public boolean unknown() {
		return true;
	}
}

/**
 * 5.1.1 Object: Account - Overarching object representing access.
 */
interface Account {
	/**
	 * * Determines the identity based on the account (Identification).
	 */
	Identity identify(String email);
}

/**
 * Implementation of the Account object using JDBC.
 */
class DsAccount implements Account {
	
	private final DataSource dataSource;

	public DsAccount(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	@Override
	public Identity identify(String email) {
		String sql = "SELECT password_hash FROM accounts WHERE email = ?";
		try (Connection conn = dataSource.getConnection(); PreparedStatement stmt = conn.prepareStatement(sql)) {
			stmt.setString(1, email);
			try (ResultSet rs = stmt.executeQuery()) {
				if (rs.next()) {
					return new VerifiedIdentity(email, rs.getString("password_hash"));
				}
			}
		} catch (SQLException e) {
			System.err.println("Database Error: " + e.getMessage());
		}
		return new AnonymousIdentity();
	}
}

/**
 * 5.1.1 Object: Session - Orchestrates the login process. Follows the
 * Key-and-Lock Principle (5.1.4).
 */
final class ApiAccount {
	
	private final Account account;

	public ApiAccount(Account account) {
		this.account = account;
	}

	public Response authenticate(Credentials credentials) {
		try {
			if (!credentials.isValid()) {
				return Response.status(Status.BAD_REQUEST).entity("{\"error\": \"Email or password missing.\"}")
						.build();
			}

			// 1. IDENTIFICATION: Which account belongs to me?
			Identity identity = account.identify(credentials.getEmail());

			if (identity.unknown()) {
				// 5.2 Feedback: Reference to the Account object
				return Response.status(Status.UNAUTHORIZED).entity("{\"error\": \"Account not found.\"}").build();
			}

			// 2. AUTHENTICATION: Proving ownership with a key (Password)
			if (identity.verify(credentials.getPassword())) {
				// 3. AUTHORIZATION: Start session (Response with Identity)
				String token = "JWT_" + identity.toString().toUpperCase() + "_" + System.currentTimeMillis();
				return Response.status(Status.OK).entity("{\"token\": \"" + token + "\", \"identity\": \""
						+ identity.toString() + "\", \"message\": \"Session active.\"}").build();
			} else {
				// 5.2 Feedback: Account object is blocked / Password incorrect
				return Response.status(Status.UNAUTHORIZED)
						.entity("{\"error\": \"Authentication failed for this account.\"}").build();
			}

		} catch (Exception e) {
			return Response.status(Status.INTERNAL_SERVER_ERROR).entity("{\"error\": \"Internal server error.\"}")
					.build();
		}
	}
}

// ==========================================================
// API LAYER (Infrastructure)
// ==========================================================

@Path("account")
public class AccountResource {

	// @Inject
	// private ServletContext server;

	private DataSource dataSource;

	@PostConstruct
	public void init() {
		// dataSource = (DataSource) server.getAttribute(DataSource.class.getSimpleName());
		
		org.h2.jdbcx.JdbcDataSource ds = new org.h2.jdbcx.JdbcDataSource();
		ds.setURL("jdbc:h2:mem:ooux_db;DB_CLOSE_DELAY=-1");
		ds.setUser("sa");
		try (Connection conn = ds.getConnection(); Statement st = conn.createStatement()) {
			st.execute("CREATE TABLE accounts(email VARCHAR(255) PRIMARY KEY, password VARCHAR(255))");
			st.execute("INSERT INTO accounts VALUES('john.doe@example.com', 'secure123')");
		} catch (Exception e) {
			e.printStackTrace();
		}
		dataSource = ds;
	}

	/**
	 * POST /api/account/login Interaction between Account, Identity, and Session
	 * objects.
	 */
	@POST
	@Path("/login")
	@Produces(MediaType.APPLICATION_JSON)
	@Consumes(MediaType.APPLICATION_JSON)
	public Response login(Credentials credentials) {
		// Uses the decorator to transform domain logic into API responses
		return new ApiAccount(new DsAccount(this.dataSource)).authenticate(credentials);
	}
}
```

### **5.5 Frontend Client (with JavaScript)**

On the client side, the object-oriented approach continues by encapsulating the interaction state within an `AccountSession` class. This frontend "speaking object" manages the asynchronous communication with the API and pushes status updates directly to a callback mechanism. This ensures that the UI components remain thin and focused on layout, while the behavioral logic of the login session is centralized and self-contained.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/ux_ui_js_sample.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OOUX Account Client - Login Objects</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        :root {
            --primary: #2563eb;
            --secondary: #64748b;
            --label-class: 'text-sm text-gray-500 mb-1 font-medium';
            --input-class: 'border border-gray-300 rounded-lg p-2 focus:ring-2 focus:ring-blue-400 outline-none transition-all';
            --button-class: 'w-full bg-blue-600 text-white font-bold py-3 rounded-lg shadow-md hover:bg-blue-700 transition-colors mt-4';
            --fieldset-class: 'border-l-4 border-gray-200 pl-6 my-6 bg-white/50 p-4 rounded-r-lg shadow-sm';
            --legend-class: 'px-2 font-bold text-blue-800 tracking-wide uppercase text-xs';
            --container-class: 'flex flex-col mb-4';
            --header-class: 'bg-blue-600 text-white p-6 shadow-lg mb-8';
            --header-content-class: 'max-w-md mx-auto';
            --header-title-class: 'font-bold text-center text-1xl';
            --header-subtitle-class: 'text-blue-100 text-base italic text-center block';
            --main-class: 'max-w-md mx-auto px-4';
        }
        body { font-family: 'Inter', sans-serif; background: #f8fafc; }
        fieldset { transition: all 0.2s ease-in-out; }
        fieldset:hover { border-color: var(--primary); }
        .status-msg { font-size: 0.875rem; margin-top: 0.5rem; text-align: center; font-weight: 600; }
    </style>
</head>
<body>

    <div id="app-root"></div>

    <script type="module">

        /**
         * BASIC FRAMEWORK CONCEPTS (Abstracts)
         */
        class Control {
            render() { throw new Error("Render method must be implemented."); }
        }

        class CssVar {
            constructor(element, propertyName) {
                const property = getComputedStyle(document.documentElement).getPropertyValue(propertyName);
                element.className = property.replace(/'/g, "");  
            }
        }

        /**
         * ATOMIC UI CONTROLS
         */
        class TextInput extends Control {
            constructor(labelText, type = "text", initialValue = "") {
                super();
                this.container = document.createElement('div');
                this.label = document.createElement('label');
                this.input = document.createElement('input');
                
                this.label.innerText = labelText;
                this.input.type = type;
                this.input.value = initialValue;

                this.container.appendChild(this.label);
                this.container.appendChild(this.input);

                new CssVar(this.container, '--container-class'); 
                new CssVar(this.input, '--input-class'); 
                new CssVar(this.label, '--label-class');  
            }

            val() { return this.input.value; }

            render() { return this.container; }
        }

        class Button extends Control {
            constructor(labelText) {
                super();
                this.btn = document.createElement('button');                        
                this.btn.innerText = labelText;
                new CssVar(this.btn, '--button-class'); 
            }
            onClick(handler) {
                this.btn.onclick = handler;
                return this;
            }
            render() { return this.btn; }
        }

        class InputGroup extends Control {
            constructor(legendText) {
                super();
                this.legendText = legendText;
                this.children = [];
            }
            add(component) {
                this.children.push(component);
                return this;
            }
            render() {
                const fieldset = document.createElement('fieldset');
                const legend = document.createElement('legend');
                legend.innerText = this.legendText;
                fieldset.appendChild(legend);
                new CssVar(fieldset, '--fieldset-class'); 
                new CssVar(legend, '--legend-class');
                this.children.forEach(child => fieldset.appendChild(child.render()));
                return fieldset;
            }
        }

        /**
         * 5.1.1 OOUX DOMAIN OBJECTS
         */

        /**
         * The Account Gateway (Infrastructure/Backend Connector)
         */
        class AccountApi {
            async login(email, password) {
                console.log(`System: Identifying account for ${email}...`);

                /**
                 * HOW TO CALL LOCALHOST:
                 * There are two primary ways to call the client against a local server:
                 * * 1. Same Host (Recommended):
                 * Host the HTML file on the same server where the Java API is running.
                 * * 2. Different Hosts:
                 * Provide the absolute URL in fetch: 'http://localhost:8080/api/account/login'.
                 * NOTE: CORS must be enabled in the Java app.
                 */
                const apiUrl = '/api/account/login'; 

                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ email, password })
                    });
                    return await response.json();
                } catch (error) {
                    return { error: "Network error: Connection to the Account Service failed." };
                }
            }
        }

        /**
         * The AccountSession Object
         */
        class AccountSession {
            constructor(accountApi, onStatusChange) {
                this.accountApi = accountApi;
                this.onStatusChange = onStatusChange;
                
                this.emailInput = new TextInput("Identification (Email)", "email", "john.doe@example.com");
                this.passwordInput = new TextInput("Authentication (Password)", "password", "secure123");
            }

            async authenticate() {
                const email = this.emailInput.val();
                const pass = this.passwordInput.val();

                this.onStatusChange("Authenticating...", "text-blue-600");

                const result = await this.accountApi.login(email, pass);

                if (result.token) {
                    this.onStatusChange(`Welcome back! Identity: ${result.identity}`, "text-green-600");
                } else {
                    this.onStatusChange(result.error || "Login failed.", "text-red-600");
                }
            }

            displayInput() {
                return new InputGroup("Account Access")
                    .add(this.emailInput)
                    .add(this.passwordInput);
            }
        }

        /**
         * PAGE COMPONENT
         */
        class AccountPage extends Control {
            constructor(session) {
                super();
                this.session = session;
                this.statusDisplay = document.createElement('div');
                this.statusDisplay.className = "status-msg";
            }

            updateStatus(text, colorClass) {
                this.statusDisplay.innerText = text;
                this.statusDisplay.className = `status-msg ${colorClass}`;
            }

            render() {
                const root = document.createElement('div');
                
                const header = document.createElement('header');
                new CssVar(header, '--header-class'); 
                
                const headerContent = document.createElement('div');
                new CssVar(headerContent, '--header-content-class');

                const title = document.createElement('h1');
                title.innerText = "Account Client - OOUX & 'UI of Objects'";
                new CssVar(title, '--header-title-class');

                const subtitle = document.createElement('p');
                subtitle.innerText = "Mental Model: Account with Identification & Authentication";
                new CssVar(subtitle, '--header-subtitle-class');

                headerContent.appendChild(title);
                headerContent.appendChild(subtitle);
                header.appendChild(headerContent);
                root.appendChild(header);

                const main = document.createElement('main');
                new CssVar(main, '--main-class');

                main.appendChild(this.session.displayInput().render());

                const loginBtn = new Button("Sign In").onClick(() => this.session.authenticate());
                main.appendChild(loginBtn.render());
                
                main.appendChild(this.statusDisplay);

                root.appendChild(main);
                return root;
            }
        }

        /**
         * APP ROOT
         */
        class AccountApp {
            run() {
                const appRoot = document.getElementById('app-root');
                const accountApi = new AccountApi();
                
                let page;
                const session = new AccountSession(accountApi, (msg, cls) => page.updateStatus(msg, cls));
                page = new AccountPage(session);

                appRoot.appendChild(page.render());
            }
        }

        window.onload = () => new AccountApp().run();
    </script>
</body>
</html>
```

## **6. Summary and Conclusion**

**Good software speaks the language of objects as well as that of users.**

**Object-Oriented UX** represents a significant shift in how user interface design is approached. By focusing on objects instead of tasks, OOUX aligns more closely with users' mental models, promotes consistency, and improves scalability.

**Object-Oriented UI** is the strong partner that translates these abstract concepts into tangible, interactive elements. By utilizing textures, dimensions, and interactive elements, OOUI makes digital interfaces more engaging.

**UI of Objects** requires a rethink at the code level **(Tell, Don't Ask)**: away from anemic data containers toward **Immutable Proxy objects**. Consistent implementation of OOP manifests in architectures with maximum cohesion and minimum semantic coupling.

**The core principle:** it is about UI intention in the domain, not UI code. The unification of OOUX and UI of Objects does not lead to a blurring of layers. On the contrary: it returns responsibility for presentation to where the knowledge lies—in the object.

* UI code in the domain? No.  
* UI intention in the domain? Yes.

The objects are no longer silent: they speak to the user, while the UI forms the protective framework for it.

> Giving up “procedural thinking” isn’t easy, but it’s worth doing. - (Allen Holub)

## **7. Sources and References**

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
