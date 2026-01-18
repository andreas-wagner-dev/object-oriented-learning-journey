# **Teach your Objects to speak to the User "otherwise they will be dumb…"**

* **Status:** Draft  
* **Appeal:** *Why we must stop treating objects like mute data containers*  
* **Topic:** \#OOP, \#OOUX, UI of Objects, Data Animation

**TL;DR:** Through Object-Oriented User Experience (OOUX) and the concept of "UI of Objects," you create a seamless object-oriented connection from backend and UI design deep into the source code. Your objects will no longer be silent; they will speak to the user.

To prevent your objects from remaining mute, you must provide them with a method to translate their internal state (data, function, or existence) into a language understandable by the user (text, visual feedback, or narrative dialogue).

*(The most common argument against user interface logic in the domain objects)*

## **Introduction and Target Audience**

Most of modern business applications—especially in the context of layered architectures like Domain-Driven Design or Clean Architecture—suffer from a paradigm shift at odds with true object orientation: while the business core is highly decoupled, backend and frontend components often dull into anemic, stateless, procedural structures.

This article shows developers and architects which tools and OOP principles they can use to bridge this paradigm gap. It presents OOUX (Object-Oriented User Experience) as a method and the concept of the "UI of Objects" to ensure a consistent domain identity from interface design deep into the source code in an object-oriented manner.

## **1\. The Silence of the Objects**

A Silent Party

Imagine you walk into a party. You approach someone to strike up a conversation. But instead of that person answering, an assistant (a 'service') rushes over, pulls an ID card out of the person's pocket, reads the name aloud, checks the pulse, and then says: "This person is approachable." The person themselves stands there motionless—a mute shell, defined only by the data they carry around.

Sounds absurd? In modern software development, this is the standard.

The analogy is found in modern "Enterprise" applications where "domain" objects are nothing more than glorified filing cabinets (data containers). They possess drawers (getters and setters) but have no ability to transmit active messages to their users. Instead, they are wheeled around on carts (often misused as [Data Transfer Objects](https://www.yegor256.com/2016/07/06/data-transfer-object.html) (DTOs)), and their contents are dragged into the light and manipulated by separate couriers (usually service or controller classes).

### **1.1 The Problem with Mute Objects**

The status quo of many applications is characterized by architectures that—often citing the [Model-View-Controller pattern](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (MVC)—force a strict separation of layers, creating hard-to-maintain and rigid [data boundaries](https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/).

This practice is primarily fueled by the dominance of mainstream literature and its specific interpretation of concepts like [Clean Architecture](https://github.com/cleancoders/CleanCodeCaseStudy/tree/master/src/cleancoderscom) or [Domain-Driven Design](https://github.com/VaughnVernon/IDDD_Samples). This development is reinforced by the best-practice guides of popular frameworks like [Spring](https://www.google.com/search?q=https://github.com/spring-petclinic/spring-framework-petclinic/tree/main/src/main/java/org/springframework/samples/petclinic), [Jakarta EE](https://www.google.com/search?q=https://github.com/eclipse-ee4j/jakartaee-examples/tree/main/applications/kickoff/src/main/java/org/example/kickoff), or [Hibernate](https://github.com/hibernate/hibernate-demos), which prescribe this layer separation as a standard.

This strict separation of concerns leads to a [misinterpretation](https://martinfowler.com/eaaDev/uiArchs.html) of the model within layered MVC architectures and inevitably results in the [Anemic Domain Model](https://www.martinfowler.com/bliki/AnemicDomainModel.html). Here, the object degenerates into a pure data carrier that holds state but possesses little to no behavior of its own.

By outsourcing the entire business logic into service or controller classes, encapsulation is broken; the necessary getters and setters create unwanted [semantic coupling](https://en.wikipedia.org/wiki/Coupling_\(computer_programming\)) between components. This type of coupling occurs when a class needs knowledge about the internal structure of another class. If, for example, a UI controller calls the sequence user.getAddress().getCity().getZipCode(), it is semantically coupled to the structure of the User object. This is also a direct violation of the [Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/) (Principle of Least Knowledge). If the internal structure of the user changes (e.g., the address is replaced by a location class), the entire call chain in the UI breaks, even though the actual domain logic remains intact.

The core problem is rooted in traditional UI design, which focuses primarily on actions and workflows—what the user wants to do (**verbs**). A music application, for example, focuses on functions like "Play Music" instead of placing objects like Song or Album as acting entities in the foreground. This procedural view leads software structures to mirror UI workflows. Even modern approaches like Domain-Driven Design (DDD) and Clean Architecture often fall into this "action trap" in practice: instead of strengthening the autonomy of domain objects, their implementation examples often place Use Cases or Application Services at the center. These function like stateless helper classes that control the workflow and degrade the object to a pure data provider.

### **1.2 The Solution: Speaking Objects**

While traditional UI design often revolves around tasks and actions, the modern approach of **Object-Oriented UX (OOUX)** shifts the focus to the real objects with which users interact. Users think of real things ("I am archiving this email") rather than database rows ("I am setting the status flag of Entity ID 123 to 'ARCHIVED'"). This mindset implies the term "User Interface of Objects," which has been significantly shaped and propagated in the modern software architecture debate by Robert Bräutigam.

**The UI Debate: Does Presentation Belong in the Domain?**

The suggestion that domain objects should present themselves is controversial but essential for true cohesion.

**Contra:** DDD experts (like Vaughn Vernon) and followers of Clean Architecture (per Robert C. Martin, "Uncle Bob") often point to a break in abstraction and technical coupling of the domain to specific UI libraries (e.g., React or JavaFX). This, they argue, reduces the reusability of domain objects. They maintain that a domain object should be exclusively responsible for business logic; UI code inside the object violates the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) **(SRP)**. According to this, a class should have only one reason to change: if the UI layout changes, the business class must not need adjustment.

**Pro:** Proponents (like Allen Holub and Robert Bräutigam) argue for **High Cohesion**. Knowledge of how a customer is represented in a list or an editable form belongs to the Customer object itself, as this is part of its responsibility. The representation should be viewed as part of the requirements and the [Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html) of the business domain.

* The crucial differentiation lies in abstraction: the object communicates via abstract interfaces instead of concrete *HTML tags* or [*CSS*](https://en.wikipedia.org/wiki/CSS)\* classes\*. Instead of revealing data via getters (which creates coupling), they use output-specific interfaces (e.g., Media, InfoPanel, TextInput) to invert the UI dependency ([Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)).  
* Visual design aspects, such as fonts or colors (e.g., bold for names, italics for IDs), can optionally be outsourced to the implementation of the UI components, to central [Look-and-Feel](https://en.wikipedia.org/wiki/Look_and_feel) classes, or global CSS definitions ([Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)).  
* Furthermore, philosophical and sociological SRP definitions are criticized for providing little clear guidance due to their subjectivity: terms like *responsibility*, *reason for change*, or orientation toward *actors* are too vague for practical implementation and often lead to unnecessary code fragmentation. Robert Bräutigam proposes a pragmatic definition instead: **SRP ≡ Maximum Cohesion ∧ Minimum Coupling**. By equating it with cohesion and coupling, SRP moves from a design philosophy to a structural metric ([Lack of Cohesion of Methods](https://en.wikipedia.org/wiki/Programming_complexity)). While "responsibility" can be debated, cohesion (e.g., via the LCOM value) and coupling (dependency graph) can be objectively proven in the code.

An object should therefore act like a real subject (speak) and animate its data instead of merely mapping it. One should not ask: **"Give me your title so I can display or transmit it."** Instead, one says: **"Here is a stage (e.g., a UI control or interface)—please present yourself there."** This is the application of the design principle [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html) in its purest form.

## **2\. Object-Oriented User Experience (UX) and Interface (UI)**

From Mental Model to Visual Representation

### **2.1 Object-Oriented UX**

OOUX is a design approach that applies the same concepts as object-oriented programming. It focuses on identifying and prioritizing objects—conceptual entities that map to real-world elements, ideas, people, places, or events.

Unlike traditional, task-oriented UX design, which focuses on the sequence of actions (**verbs**) and can lead to complex, branching interfaces as the number of tasks grows, OOUX puts objects (**nouns**) at the center. OOUX organizes the user interface around entities (e.g., Album, Song), resulting in a more efficient, entity-centric structure. Actions are logically attached to the objects themselves. This alignment with users' mental models (e.g., thinking about "Emails" or "Contacts" rather than "Send" or "Sort") creates intuitive user experiences.

**Principles of OOUX Implementation:**

* **Objects:** The building blocks of the system (e.g., Product, User).  
* **Structure:** The definition of each object's properties.  
* **Relationships:** Mapping how objects interact (e.g., a one-to-many relationship between a user and their orders). Clear definition of these relationships is essential for creating coherent navigation paths.  
* **Actions:** The interactions users can perform with objects.  
* **Context:** Understanding that objects behave differently depending on the context (list view vs. detail view) but must maintain the same core identity.  
* **Modular Thinking:** Designing objects as reusable components. This modular approach promotes design system consistency.

To bridge the gap between software architecture and UI design, the OOUX framework offers the **ORCA process** (Objects, Relationships, Capabilities, Attributes) by Sophia Prater. The process spans four phases and demands that designers think in stable "objects" rather than fleeting "pages" (views). It strictly ensures that these objects correspond to the user's mental world (Domain Model). The methodology is oriented toward navigation models based on the logical structure of the data rather than arbitrary menu structures. Furthermore, it consistently distinguishes between core content and metadata.

* **Phase 1: Objects** First, the "things" that are real for the user and the business are identified (e.g., Book, Author, Order).  
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

## **3\. User Interface of Objects**

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

One possible approach to implementing the aforementioned "*animation*" of data in objects is to forgo getters in favor of **Printers**. As Yegor Bugayenko explains in his blog post [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html), we don't ask an object for its state just to laboriously map it into another format. Instead, we give the object a target—a printer—and let it manifest itself there. The object remains master of its data, and we achieve a clean separation between the object's logical essence and its technical representation. By removing getters, you force yourself to put behavior into the objects. The object turns from a passive data structure into an active, "living" subject.

Imagine a Person object.

* **Traditional:** The object loads title and address into memory at start. If the external database changes, the object is stale, and encapsulation is only apparent.  
* **Immutable Proxy:** The UI object holds only one piece of information: its identity (e.g., ID 50). It is a proxy for the true data source.

When person.title() is called, it doesn't return a stored string; instead, it goes to the data source at that moment and retrieves it (or animates it). The object says: **"I am not the title. I am the representative of document number 50, and I know where the title is and how to format and present it."**

Instead of procedural:

// View pulls data from the object (Pull)      
String title \= person.getTitle(); // \-\> violates Tell, Don't Ask

// ...data mapping.      
render(title);

We do object-oriented:

// Object animates its data on a printer (Push and Tell)      
person.displayTitle(inputTitlePanel);

// alternatively with return of UI controls (only Tell)

// Object creates part of the view itself     
InputText inputTitle \= person.displayTitle();

// ...or the complete, editable view of itself    
InputPanel panelPerson \= person.displayInput();

**Modular Composition:** A Person object delegates responsibility for its representation to its nested objects (Address), consistently applying the 'Tell, Don't Ask' principle.

public final class Person {

    private final String name;        
    private final Address address; // ← nested Object        
            
    public InputComponent\<Person\> displayInput() {        
        return new InputGroup()        
            .add(new TextInput("Name", name))        
            .add(address.displayInput())  // ← Composition: UI of Address\!        
            .map(Person::new);        
    }        
}

The Person knows how to represent itself for input by composing itself from smaller, self-representing objects (Address). The object maintains full control over how and when its data is exposed, protecting its internal rules. This allows the principle of [Data Animation](https://amihaiemil.com/2017/09/01/data-should-be-animated-not-represented.html) to be consistently implemented by having the object actively instruct the view (push principle) and handle representation, validation, and formatting itself.

**Abstract UI:** To decouple the domain from specific UI libraries, an additional abstraction of UI objects can be used. One approach is what Allen Holub calls a [Bidirectional Builder](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html), which is a combination of the Builder and Visitor patterns.

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

	/\*\* \* Prints content as an HTML table.    
	\*  
	\* @return HTML table string    
	\*/    
    @Override    
	public String toString() {    
        StringBuffer out \= new StringBuffer();    
	    out.append("\<table class=\\"person-input-table\\" border=\\"0\\"\\\>");    
        // wrap Name with HTML    
		out.append("\<tr\>\<td\>");    
		out.append("Name:");    
		out.append("\</td\>\<td\>");    
		out.append("\<input type=\\"text\\" name=\\"name\\" value=\\"");    
		out.append(name);    
		out.append("\\"\\\>");    
		out.append("\</td\>\</tr\>");    
        // wrap Address with HTML    
		out.append("\<tr\>\<td\>");    
		out.append("Address:");    
		out.append("\</td\>\<td\>");    
		out.append("\<input type=\\"text\\" name=\\"address\\" value=\\"");    
		out.append(address);    
		out.append("\\"\\\>");    
		out.append("\</td\>\</tr\>");    
		out.append("\</table\>");    
		return out.toString();    
	}    
}

// Usage:

Person jonathan \= //...

PersonView htmlPersonView \= new HtmlPersonView();    
jonathan.display(htmlPersonView);

String html \= htmlPersonView.toString();

### **3.4 Client User and API Serialization**

The **Tell, Don't Ask** principle can be applied not just to logic but also to **serialization**. Instead of the API layer (e.g., JAX-RS or Spring Controller) extracting data from the domain object and serializing it via an external mapper (like Jackson), the object can be instructed to **write itself to the response stream or generate the response**.

/\*\* \* Domain interface for Person.    
 \*/    
interface Person {

    // identity (immutable)  
	String id();

	// printer method  
	String toJson();  
}

/\*\* \* Vertical decorator of Unknown object instead of checking null.    
 \*/    
class UnknownPerson implements Person {

	private final String id;    
    // other fields name and address...

	public UnknownPerson() {    
		this.id \= UUID.randomUUID().toString();    
		this.name \= "Unknown";    
        this.address \= new Address("Unknown");    
	}

	@Override     
    public String id() { return id;}

	@Override  
	public String toJson() {  
	    return String.format(  
	      "{\\"id\\":\\"%s\\", \\"name\\":\\"%s\\", \\"address\\":\\"%s\\"}",  
	      this.id, this.name, this.address.toString()  
	    );  
	}  
}

/\*\* \* Horizontal decorator of Person using a JDBC DataSource.    
 \*/    
class DsPerson implements Person {

	private final String id;    
	private final DataSource ds;    
    private final String name;  
    private final Address address;

	public DsPerson(DataSource ds, String name, Address address) {    
		this.ds \= ds;  
		this.id \= UUID.randomUUID().toString();    
 		this.name \= name;    
		this.address \= address;    
	}

	public DsPerson(DataSource ds, ResultSet rs) throws SQLException {    
		this.ds \= ds;    
		this.id \= rs.getString("id");    
		this.name \= rs.getString("name");    
		this.address \= new Address(rs.getString("address"));    
	}

	@Override     
    public String id() { return id;}

	public static Person ofId(DataSource ds, String id) throws SQLException  {    
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

    @Override  
	public String toJson() {  
	    return String.format(  
	      "{\\"id\\":\\"%s\\", \\"name\\":\\"%s\\", \\"address\\":\\"%s\\"}",  
	      this.id, this.name, this.address.toString()  
	    );  
	}  
}

/\*\* \* Horizontal decorator for API response logic.    
 \*/    
final class RsPerson {

	private final Person person;    
	private final String personIdToFind;

	public RsPerson(Person person, String personIdToFind) {    
		this.person \= person;    
		this.personIdToFind \= personIdToFind;    
	}

	public Response toJsonResponse() {    
		try {     
			if (person.id().equals(personIdToFind)) {    
				return Response.status(Status.OK)    
				.entity(person.toJson())    
				.build();    
			} else {    
				return Response.status(Status.NOT\_FOUND)    
				.entity("{\\"message\\": \\"Person not found.\\"}")    
				.build();    
			}    
		} catch (Exception e) {    
			return Response.status(Status.INTERNAL\_SERVER\_ERROR)    
					.entity("{\\"Error\\": \\"" \+ e.getMessage() \+ "\\"}")    
					.build();    
		}    
	}  
}

// Usage:  
return new RsPerson(DsPerson.ofId(ds, personIdFind), personIdFind).toJsonResponse();

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

* **Calculations in the View:** If the UI template calculates: if (user.age \> 18 && user.hasBalance). This belongs in the method user.isEligibleForPurchase().  
* **Database Knowledge in the UI:** If the UI knows that an object is "dirty" or needs to be saved.  
* **Duplication:** If you have to write the same if-condition in the **web app**, the **mobile app**, and the **export module**.  
* **Import Smell:** If classes like Color, Button, or Layout appear in your domain class (e.g., Invoice.java).

### **4.3 Separation of Domain and Presentation**

The separation of domain and presentation layers can be viewed technically as follows:

The domain manages state and business logic. It defines the business intent (e.g., a status change or a specific context condition). The UI handles concrete representation and technical implementation. It decides how this business intent is translated visually or haptically for the user.

**The Mistake:** When the domain dictates technological details (e.g., "Set CSS class X to active"). As soon as the UI technology is changed, the domain logic loses its validity and must be adjusted.

**The Solution:** The domain defines an abstract requirement (e.g., signaling a warning). How the technical layer implements this—whether through a color change, an icon, or an acoustic signal—is solely up to the implementation of the UI component.

## **5\. Practical Application**

Using a typical login process, we demonstrate how OOUX and UI of Objects principles are applied in practice.

### **5.1 Application of the OOUX Methodology**

In the OOUX framework, identifying real objects is the focus before thinking about screens or flows. A login process is not viewed as a mere workflow, but as an interaction between objects.

Here is a user's mental model for a login example using the OOUX methodology (ORCA process):

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

* **Grouping:** Place fields for identification (email) and authentication (password) in a clearly defined area (panel) representing the login object.  
* **Feedback:** If the password is wrong, the account object is blocked. The error message should refer to the object (account not found or the password for this account is incorrect).  
* **Status Display:** Use a label (e.g., in the header of the main form) showing the identity object (e.g., "Logged in as John Doe") to visualize the active session.

### **5.3 Desktop Client (with Swing)**

To put theory into practice, we look at a Java Swing example. Instead of building a JFrame that pulls data from a user object (user.getName()), we flip it. We give the object a "canvas" (interface as a digital workspace) on which it paints itself. The AccountSession object is the boss. It determines what the login looks like and what happens when clicked. The Swing classes are just tools for the presentation.

package com.example.account.desktop;

import java.awt.\*;  
import java.sql.\*;  
import javax.sql.\*;  
import javax.swing.\*;

// \==========================================================  
// 1\. ABSTRACT CANVAS (The Canvas for OOUX Objects)  
// \==========================================================  
interface AccountCanvas {  
	void addEmailField(String label);  
	void addPasswordField(String label);  
	void addSignInAction(String label, SignInAction action);  
	void notifySuccess(String message);  
	void notifyError(String message);  
	void updateIdentityStatus(String identityInfo);  
}

interface SignInAction {  
	void authenticate(String email, String password);  
}

// \==========================================================  
// 2\. DOMAIN: IDENTITY  
// \==========================================================  
interface Identity {  
	boolean verify(String secret);  
	boolean unknown();  
}

final class VerifiedIdentity implements Identity {  
	private final String email;  
	private final String storedSecret;

	public VerifiedIdentity(String email, String storedSecret) {  
		this.email \= email;  
		this.storedSecret \= storedSecret;  
	}

	@Override public boolean verify(String secret) { return storedSecret.equals(secret); }  
	@Override public boolean unknown() { return false; }  
	@Override public String toString() { return email; }  
}

final class AnonymousIdentity implements Identity {  
	@Override public boolean verify(String secret) { return false; }  
	@Override public boolean unknown() { return true; }  
	@Override public String toString() { return "Not logged in"; }  
}

// \==========================================================  
// 3\. DOMAIN: ACCOUNT  
// \==========================================================  
interface Account {  
	Identity identify(String email);  
}

final class DbAccount implements Account {  
	private final DataSource dataSource;

	public DbAccount(DataSource dataSource) { this.dataSource \= dataSource; }

	@Override  
	public Identity identify(String email) {  
		String sql \= "SELECT password FROM accounts WHERE email \= ?";  
		try (Connection conn \= dataSource.getConnection(); PreparedStatement stmt \= conn.prepareStatement(sql)) {  
			stmt.setString(1, email);  
			try (ResultSet rs \= stmt.executeQuery()) {  
				if (rs.next()) return new VerifiedIdentity(email, rs.getString("password"));  
			}  
		} catch (SQLException e) { System.err.println("Domain Error: " \+ e.getMessage()); }  
		return new AnonymousIdentity();  
	}  
}

// \==========================================================  
// 4\. DOMAIN: SESSION  
// \==========================================================  
final class AccountSession {  
	private final Account account;

	public AccountSession(Account account) { this.account \= account; }

	public void displayOn(AccountCanvas canvas) {  
		canvas.addEmailField("Email");  
		canvas.addPasswordField("Password");

		canvas.addSignInAction("Sign In", (email, pass) \-\> {  
			if (email.isEmpty() || pass.isEmpty()) {  
				canvas.notifyError("Please fill in all fields.");  
				return;  
			}  
			Identity identity \= account.identify(email);  
			if (identity.unknown()) {  
				canvas.notifyError("Account not found.");  
			} else if (identity.verify(pass)) {  
				canvas.updateIdentityStatus("Logged in as: " \+ email);  
				canvas.notifySuccess("Session active.");  
			} else {  
				canvas.notifyError("Authentication failed.");  
			}  
		});  
	}  
}

// \==========================================================  
// 5\. INFRASTRUCTURE: SWING IMPLEMENTATION  
// \==========================================================  
final class SwingAccountPanel extends JPanel implements AccountCanvas {  
	private final JTextField emailField \= new JTextField("john.doe@example.com");  
	private final JPasswordField passwordField \= new JPasswordField("secure123");  
	private final JLabel statusLabel \= new JLabel(" ", SwingConstants.CENTER);  
	private final SwingMainForm parentForm;

	public SwingAccountPanel(SwingMainForm parentForm) {  
		this.parentForm \= parentForm;  
		setLayout(new GridLayout(6, 1, 5, 5));  
		setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));  
	}

	@Override public void addEmailField(String label) { add(new JLabel(label)); add(emailField); }  
	@Override public void addPasswordField(String label) { add(new JLabel(label)); add(passwordField); }  
	@Override public void addSignInAction(String label, SignInAction action) {  
		JButton button \= new JButton(label);  
		button.addActionListener(e \-\> action.authenticate(emailField.getText(), new String(passwordField.getPassword())));  
		add(button);  
		add(statusLabel);  
	}  
	@Override public void notifySuccess(String m) { statusLabel.setForeground(Color.GREEN); statusLabel.setText(m); }  
	@Override public void notifyError(String m) { statusLabel.setForeground(Color.RED); statusLabel.setText(m); }  
	@Override public void updateIdentityStatus(String info) { parentForm.setIdentityHeader(info); }  
}

class SwingMainForm extends JFrame {  
	private final JLabel identityLabel \= new JLabel("Not logged in", SwingConstants.RIGHT);

	public SwingMainForm() {  
		setSize(400, 300);  
		setDefaultCloseOperation(EXIT\_ON\_CLOSE);  
		add(identityLabel, BorderLayout.NORTH);  
	}

	public void setIdentityHeader(String t) { identityLabel.setText(t); }  
}

public class SwingAccountApplication {  
	public static void main(String\[\] args) {  
		SwingUtilities.invokeLater(() \-\> {  
			Account account \= new DbAccount(setupDatabase());  
			AccountSession session \= new AccountSession(account);  
			SwingMainForm form \= new SwingMainForm();  
			SwingAccountPanel panel \= new SwingAccountPanel(form);  
			session.displayOn(panel);  
			form.add(panel);  
			form.setVisible(true);  
		});  
	}

	private static DataSource setupDatabase() {  
		org.h2.jdbcx.JdbcDataSource ds \= new org.h2.jdbcx.JdbcDataSource();  
		ds.setURL("jdbc:h2:mem:ooux\_db;DB\_CLOSE\_DELAY=-1");  
		try (Connection c \= ds.getConnection(); Statement s \= c.createStatement()) {  
			s.execute("CREATE TABLE accounts(email VARCHAR PRIMARY KEY, password VARCHAR)");  
			s.execute("INSERT INTO accounts VALUES('john.doe@example.com', 'secure123')");  
		} catch (Exception e) {}  
		return ds;  
	}  
}

### **5.4 Backend API (with JAX-RS)**

The following backend implementation demonstrates how the **Tell, Don't Ask** principle is transferred to a web service. Instead of the controller orchestrating the logic procedurally, it delegates the entire authentication and response generation to the domain objects. The `ApiAccount` acts as a specialized decorator that translates the domain outcome directly into the HTTP protocol.

package com.example.account.api;

import jakarta.ws.rs.\*;  
import jakarta.ws.rs.core.\*;  
import javax.sql.DataSource;  
import java.sql.\*;

@Path("account")  
public class AccountResource {  
    private DataSource dataSource;

    @POST  
    @Path("/login")  
    @Produces(MediaType.APPLICATION\_JSON)  
    @Consumes(MediaType.APPLICATION\_JSON)  
    public Response login(Credentials creds) {  
        return new ApiAccount(new DsAccount(dataSource)).authenticate(creds);  
    }  
}

final class ApiAccount {  
    private final Account account;  
    public ApiAccount(Account a) { this.account \= a; }

    public Response authenticate(Credentials creds) {  
        Identity identity \= account.identify(creds.getEmail());  
        if (identity.unknown()) return Response.status(401).entity("{\\"error\\":\\"Not found\\"}").build();  
        if (identity.verify(creds.getPassword())) {  
            return Response.ok("{\\"token\\":\\"ABC\\", \\"identity\\":\\""+identity+"\\"}").build();  
        }  
        return Response.status(401).entity("{\\"error\\":\\"Auth failed\\"}").build();  
    }  
}

### **5.5 Frontend Client (with JavaScript)**

On the client side, the object-oriented approach continues by encapsulating the interaction state within an `AccountSession` class. This frontend "speaking object" manages the asynchronous communication with the API and pushes status updates directly to a callback mechanism. This ensures that the UI components remain thin and focused on layout, while the behavioral logic of the login session is centralized and self-contained.

\<\!DOCTYPE html\>  
\<html lang="en"\>  
\<head\>  
    \<meta charset="UTF-8"\>  
    \<title\>OOUX Account Client\</title\>  
    \<script src="\[https://cdn.tailwindcss.com\](https://cdn.tailwindcss.com)"\>\</script\>  
\</head\>  
\<body class="bg-gray-100 p-8"\>  
    \<div id="app-root" class="max-w-md mx-auto bg-white p-6 rounded shadow"\>\</div\>

    \<script type="module"\>  
        class AccountSession {  
            constructor(api, onStatus) {  
                this.api \= api;  
                this.onStatus \= onStatus;  
            }

            async login(email, pass) {  
                this.onStatus("Connecting...");  
                const res \= await this.api.post('/login', { email, pass });  
                if (res.token) this.onStatus("Welcome " \+ res.identity);  
                else this.onStatus("Error: " \+ (res.error || "Failed"));  
            }  
        }

        // Logic to build the UI based on the Session object...  
        const root \= document.getElementById('app-root');  
        // ... (simplified rendering)  
    \</script\>  
\</body\>  
\</html\>

## **6\. Summary and Conclusion**

**Good software speaks the language of objects as well as that of users.**

**Object-Oriented UX** represents a significant shift in how user interface design is approached. By focusing on objects instead of tasks, OOUX aligns more closely with users' mental models, promotes consistency, and improves scalability.

**Object-Oriented UI** is the strong partner that translates these abstract concepts into tangible, interactive elements. By utilizing textures, dimensions, and interactive elements, OOUI makes digital interfaces more engaging.

**UI of Objects** requires a rethink at the code level **(Tell, Don't Ask)**: away from anemic data containers toward **Immutable Proxy objects**. Consistent implementation of OOP manifests in architectures with maximum cohesion and minimum semantic coupling.

**The core principle:** it is about UI intention in the domain, not UI code. The unification of OOUX and UI of Objects does not lead to a blurring of layers. On the contrary: it returns responsibility for presentation to where the knowledge lies—in the object.

* UI code in the domain? No.  
* UI intention in the domain? Yes.

The objects are no longer silent: they speak to the user, while the UI forms the protective framework for it.

Giving up “procedural thinking” isn’t easy, but it’s worth doing. \- (Allen Holub)

## **7\. Sources and References**

* Alan Kay: [Definition of Object-Oriented Programming (2003)](https://www.google.com/search?q=https://www.quora.com/What-does-Alan-Kay-mean-when-he-said-OOP-to-me-means-only-messaging-local-retention-and-protection-and-hiding-of-state-process-and-extreme-late-binding-of-all-things)  
* Allen Holub: [More on getters and setters (2004)](https://www.infoworld.com/article/2161050/more-on-getters-and-setters.html)  
* Robert Bräutigam: [Data boundaries are the root cause of maintenance problems (2019)](https://javadevguy.wordpress.com/2019/06/06/data-boundaries-are-the-root-cause-of-maintenance-problems/)  
* Martin Fowler: [Anemic Domain Model (2003)](https://www.martinfowler.com/bliki/AnemicDomainModel.html)  
* Sophia Prater: [Object-Oriented UX (2025)](https://www.rewiredux.com/ooux)  
* Yegor Bugayenko: [Printers Instead of Getters (2016)](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html)