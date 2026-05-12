## **1. Objektorientiertes Domain-Driven Design (OO-DDD): Translating Complexity into the Business Story of Software**

**Abstract: OO-DDD – Wenn Code zur Geschichte wird**

Dieser Artikel präsentiert einen Ansatz als Kombination aus klassischem Domain-Driven Design, strikter Objektorientierung und den Prinzipien des Storytellings.

Während herkömmliche Ansätze Software-Strukturen oft als Ansammlung technischer Schichten, zustandslosen Service-Klassen und passiven Datencontainern begreifen, transformiert dieser Weg das System in eine Erzählung intelligenter Akteure. Im Zentrum steht das „Tell, Don’t Ask“-Prinzip: Objekte werden nicht nach ihren Daten abgefragt, sondern als autonome Problemlöser mit fachlich relevantem Verhalten adressiert.

Durch das Storytelling-Prinzip wird die Architektur zur Dramaturgie: Das Root-Paket fungiert als Inhaltsverzeichnis (Ebene 0), das die fachliche Essenz widerspruchsfrei darstellt, während technische Details durch verhaltensorientierte Entwurfsmuster wie Decorators und Adapter modular in die Kapitel (Sub-Packages) verbannt werden.

Das Ergebnis ist eine Softwarearchitektur, die technische Robustheit bietet und deren Quellcode sich für Entwickler und Fachexperten gleichermaßen wie eine flüssige, fachliche Geschichte liest. Das methodische Vorgehen stützt sich auf zwei wesentliche Säulen von DDD:

* **Strategisches Design:** Die Gestaltung des „Großen Ganzen“. Hier werden die Schauplätze der Geschichte (**Bounded Contexts**) festgelegt, damit die Handlung einen klaren Rahmen erhält.  
* **Taktisches Design:** Die Ausarbeitung der Details. Durch verhaltensorientierte Entwurfsmuster erhalten die Protagonisten (Objekte) spezifische Fähigkeiten, ohne ihre Geheimnisse preiszugeben.

## **1.1 Strategisches Design: Die Schauplätze der Handlung**

Das strategische Design bildet das Fundament einer Geschichte. Es dient dazu, die Schauplätze (Kontexte) so voneinander abzugrenzen, dass die Protagonisten (Objekte) in ihrer jeweiligen Welt eine klare und ungestörte Rolle einnehmen können.

* **Domänen-Analyse (Identifikation der Schauplätze):** Es erfolgt eine Unterscheidung zwischen der **Core Domain** (dem Hauptdarsteller und Kern der Handlung), unterstützenden Subdomänen und generischen Standardlösungen. Ziel ist es, die Aufmerksamkeit gezielt auf jene Aspekte zu lenken, welche die Geschichte einzigartig machen.  
* **Definition von Bounded Contexts (Ziehen von Kapitelgrenzen):** Um Mehrdeutigkeiten bei Begriffen zu vermeiden, werden strikte Grenzen definiert. Innerhalb eines Kontextes besitzt jedes Wort eine feste, unveränderliche Bedeutung. Diese Grenzen verhindern, dass die Handlung eines Kapitels die Logik eines anderen korrumpiert.  
* **Ubiquitous Language (Die Erzählsprache):** Die Etablierung einer gemeinsamen Sprache für Fachexperten und Entwickler stellt sicher, dass sich die Fachterminologie 1:1 im Code widerspiegelt. Ein Satz der Fachabteilung transformiert sich so ohne Übersetzungsverluste in eine Zeile Code.  
* **Storytelling (Struktur-Grammatik):** Diese Norm macht die *Ubiquitous Language* lesbar. Sie definiert klare Regeln für die Benennung und sorgt für eine progressive Vermittlung von Information. Ein Leser sollte auf dem ersten Blick auf die Paketstruktur der Ebene 0 sofort verstehen, welche Geschichte das System erzählt, ohne in die technischen Details der Umsetzung eintauchen zu müssen.  
* **Context Mapping (Verknüpfung der Handlung):** Dieser Schritt definiert das Zusammenspiel der verschiedenen Schauplätze. Er legt fest, wie Informationen fließen, ohne dass ein Protagonist interne Geheimnisse („Secrets“) an einen fremden Kontext preisgeben muss.

## **1.2 Taktisches Design: Die Charakterentwicklung und Dramaturgie**

In der Phase des taktischen Designs erwachen die Protagonisten der Geschichte zum Leben. Es wird festgelegt, wie diese auf Ereignisse reagieren und welche individuellen Fähigkeiten sie besitzen. Im OO-DDD werden Entwurfsmuster nicht als technische Schablonen, sondern als Werkzeuge genutzt, um sicherzustellen, dass die Objekte als „intelligente Akteure“ die Handlung selbst vorantreiben.

* **Behavioral Entities (Die Protagonisten):** Objekte mit einer lebenslangen Identität fungieren als Hauptdarsteller, welche ihre Motive und Zustände („Secrets“) für sich behalten. Sie folgen strikt dem **„Tell, Don’t Ask“-Prinzip**: Ein Protagonist wird nicht nach seinem Status gefragt, um extern über ihn zu entscheiden, sondern mit einer fachlichen Aufgabe beauftragt. Er weiß selbst am besten, wie Regeln validiert und Kurse geändert werden.  
* **Smart Value Objects (Die Attribute der Welt):** Diese beschreiben Eigenschaften (z. B. `Geldbeträge` oder `Adressen`). Im OO-DDD sind sie keine passiven Datenfelder, sondern besitzen eigene Logik (z. B. Betrag.addieren()). Ihre Unveränderlichkeit (**Immutability**) hält die Geschichte konsistent – ein einmal geschriebenes Wort ändert seinen Wert nicht.  
* **Domain Events (Die Wendepunkte der Handlung):** Signale über bedeutsame Ereignisse (z. B. `PaymentReceived`) informieren andere Akteure über einen fachlichen Abschluss, ohne dass der Auslöser seine Kapselung aufgeben muss. Sie sind die Nachrichten, welche die Geschichte in das nächste Kapitel tragen.

**Umdeutung klassischer Building Blocks**

Zur Wahrung der erzählerischen Dichte werden klassische DDD-Konzepte im OO-DDD neu ausgerichtet oder als redundant entfernt:

* **Aggregates (Redundanz durch echte Kapselung):** Da jedes Objekt im OO-DDD als „Geheimnisträger“ die eigene Integrität schützt, entfällt die Notwendigkeit für eine künstliche Klammer. Integrität wird als natürliche Eigenschaft jedes Akteurs begriffen.  
* **Domain Services (Vermeidung von „Ghostwritern“):** Services werden eliminiert, da sie Protagonisten zu Statisten degradieren. Die Logik verbleibt in den Akteuren durch:  
  * **Selbstkoordination:** Ein PlacedOrder koordiniert die eigene Abwicklung.  
  * **Ereignissteuerung:** Die Handlung entwickelt sich durch Reaktion auf Ereignisse (OrderConfirmed).  
  * **Fachliche Koordinationsobjekte:** Komplexe Aufgaben führen zur Erschaffung neuer fachlicher Akteure (z. B. Checkout statt CheckoutService).  
* **Repository (Persistenz als stilles Archiv):** Das Repository fungiert nicht als Lagerverwalter, der Objekte seziert. Persistenz wird so tief in die Kapitel (Sub-Packages) verbannt, dass die Fachlogik unberührt bleibt. Das Ziel ist ein Akteur, der sein Schicksal selbst bestimmt, ohne dass technische Archive in private Aufzeichnungen blicken.  
* **Abkehr von Layered Architecture:** Die klassische Schichtenarchitektur wird abgelehnt, da sie die Kapselung schwächt. Stattdessen erfolgt eine hierarchische Organisation nach Domänenkonzepten. Die Abhängigkeit fließt immer von der konkreten Detailtiefe hin zum abstrakten Fachkonzept.

## **1.3 Die Grammatik der Geschichte (Inhaltsverzeichnis, Naming & Vokabular)**

Damit der Code eine widerspruchsfreie Geschichte erzählt, die für Entwickler und Fachexperten gleichermaßen lesbar ist, braucht er eine präzise Grammatik. Diese Grammatik eliminiert technisches Rauschen und rückt die fachliche Dichte in den Fokus.

### **1.3.1 Das Inhaltsverzeichnis führt die Handlung (Packaging)**

Ein gutes Buch beginnt mit einem klaren Inhaltsverzeichnis. Das **Root-Paket (Ebene 0)** übernimmt diese Rolle.

* **Abhängigkeitsrichtung:** Eine Geschichte wird von oben nach unten erzählt. Das Root-Paket enthält die Protagonisten (`Car`, `Customer`) und ist absolut unabhängig. Unterpakete liefern lediglich die schauspielerische Umsetzung (Details wie StoredCar) und hängen strikt vom Kern ab.  
* **Konzepttreue:** Kapitel (Unterpakete) dürfen keine neuen Hauptfiguren einführen. Ein `carpool/StoredCar` ist lediglich ein Detail der bereits bekannten Figur Car. Wer das Inhaltsverzeichnis kennt, darf in den Kapiteln nicht von unbekannten Fachbegriffen überrascht werden.

### **1.3.2 Schauplätze statt technischer Abteilungen**

Der Code wird organisiert nach den Schauplätzen der Handlung, nicht nach IT-Strukturen.

* **Fachliche Schauplätze:** Namen wie `payment/`, `inventory/` oder `user/` beschreiben, *wo* etwas passiert. Das Paket `exchange/` ist das Tor zur Außenwelt und application/ ist die Bühne, auf der das Stück beginnt.  
* **Technisches Rauschen vermeiden:** Begriffe wie `controller/`, `service/`, `repository/` oder `impl/` sind wie Regieanweisungen, die versehentlich im Text des Schauspielers gelandet sind. Sie unterbrechen den Lesefluss der Fachgeschichte und gehören gelöscht.

Die folgende Übersicht zeigt, wie technische Paketbegriffe aus gängigen Architekturmustern durch domänenorientierte Entsprechungen ersetzt werden können.

| Kategorie | Technisch (Wie?) | Domänenorientiert (Was?) |
| :---- | :---- | :---- |
| **Modell** | entity/, model/, aggregate/ | bill/, order/, car/, customer/ |
| **Prozess** | service/, usecase/, consumer/ | billing/, payment/, audit/, task/ |
| **Persistenz** | persistence/, repository/, db/ | storage/, store/, database/ |
| **API** | controller/, adapter/, rest/ | exchange/, expose/, checkout/ |
| **UI** | web/, ui/, view/ | user/, display/, portal/ |
| **Util** | common/, util/, lib/, helper/ | tax/, rule/, text/, log/ |
| **Bootstrap** | config/, properties/, injections/ | application/, boot/, startup/ |

Die rechte Spalte enthält keine Framework-Begriffe, sondern Fachkonzepte, die Entwickler und Fachseite gleichermaßen verstehen.

### **1.3.3 Substantive und Adjektive (Naming Conventions)**

Die Benennung folgt einer natürlichen Sprachlogik, die das **„Was“** (Dinge) vom **„Wie“** (Kontext) trennt.

**Interfaces sind die Protagonisten (Substantive):** Sie beschreiben das Wesen der Dinge: Car, Customer, Order. Sie sind das „Was“.

**Klassen beschreiben den Kontext (Präfix-basiert):** Klassennamen wirken wie Adjektive, die den aktuellen Zustand oder das Resultat einer Handlung beschreiben.

| Kontext Typ | Domänenorientiert (Was?) | statt Technisch (Wie?) |
| :---- | :---- | :---- |
| *Fachliche:* | PaidCar, CancelledCar, ReservedCar, AuditCar, ValidCar | |  
| *Technische:* | AuthCar, StoredCar, CachedCar, LoggedCar | |
| *Process* | DispatchedCar(s), ProcessedCar | CarJob, CarValidationTask |
| *Kommunikation:* | PublishedCars, ReceivedCars | CarProducer, CarConsumer |   
| *Protokoll:* | HttpPayPal, SmtpsEmail | PayPalClient, EmailSender  |   
| *Parsing:* | XmlCar, JsonCar | CarParser, CarMapper |   
| *API* | ServedCars, ApiCars | CarController |   
| *UI* | DisplayedCars, Printable*  | CarController, CarViewModel | 

**Vermeiden von Verben und technischen Suffixen:** Namen wie *Handler, *Manager, *Processor *Validator, *Calculatoroder *Worker klingen nach mechanischen “Jobs“ . Ebenso verschleiern Suffixe wie *Domain,*Model, *DTO, *Utiloder *Helper die fachliche Geschichte durch technische Klassifizierungen.

### **1.3.4 UI-Vokabular: Sichtbare Schauplätze benennen**

Auch im Frontend ist jede Komponente ein intelligenter Akteur. Gute Namen sprechen die Sprache der Anwender und vermeiden Framework-Jargon.

* **Realwelt-Sprache:** Ein Komponentenname setzt sich aus dem Domänenbegriff (Was) und dem Control-Typ (Form) zusammen (z. B. OrderTable, CustomerCard, PaymentForm).  
* **Vermeidung von Framework-Suffixen:** Begriffe wie -Component, -Container, -View oder -Widget  werden unterlassen, da sie keine Information über den fachlichen Inhalt vermitteln.

|  Control-Typ | Beispiel | Fachliche Bedeutung |
| :---- | :---- | :---- |
| Table | InvoiceTable | Tabellarische Übersicht & Interaktion |
| Card | ProductCard | Kompakte Darstellung einer Entität | 
| Form | RegistrationForm | Eingabemaske / Bearbeitung | 
| Dashboard | SalesDashboard  | Aggregierte Statusübersicht |
| Dialog | CancelOrderDialog | Modaler Interaktionspunkt |

Im UI existiert eine etablierte Fachsprache aus der realen Welt, die Anwender täglich benutzen: Tabellen, Karten, Masken, Kataloge. Diese Begriffe sind keine technischen Artefakte, sondern geteiltes Vokabular zwischen Entwicklung und Fachseite.

### **1.3.5 Verben und Adjektive: Die Handlungen und Eigenschaften der Akteure**

Während Klassennamen die Charaktere definieren, beschreiben Methoden deren Taten. Im OO-DDD wird das Objekt als autonomer Akteur begriffen, nicht als passiver Datencontainer. Methodenbenennungen erfolgen konsequent aus der Perspektive des Objekts: Ein Invoice-Objekt bietet beispielsweise total() an anstatt calculateTotalForMe().

**Single Action Rule (Eindeutigkeit):** Jede Methode führt genau eine vollständige fachliche Aktion aus. Zusammengesetzte Namen gelten als Indikator für eine zu hohe Komplexität.

* invoice.send() statt validateAndSendInvoice()  
* user.persist() statt createAndPersistUser()

**Command-Naming (Veränderung):** Methoden, die eine Zustandsänderung bewirken (Mutatoren), nutzen knappe, imperative Verben. Technische Füllwörter wie set, update oder modify werden eliminiert, da sie den technischen Vorgang statt der fachlichen Handlung beschreiben.

* price(NewPrice p) statt updatePrice()  
* complete() statt setStatus(Status.COMPLETED)  
* activate() statt setActive(true)

**Query-Naming (Eigenschaften):** Methoden ohne Seiteneffekte (Accessoren) geben zurück, was das Objekt ist oder besitzt. Das technische Präfix get entfällt, da es keinen fachlichen Mehrwert bietet.

* orderOf(String id) statt findOrderById(String id)  
* firstName() statt getFirstName()  
* active() statt isActiveUser()  
* xml() statt convertToXml()

**Method Chaining (Flüssige Erzählung):** Um ausdrucksstarke Ketten zu bilden, die sich wie ein Satz lesen, werden Methoden nach dem zurückgegebenen Objekt benannt. Dies unterstützt das „Law of Demeter“ und vermeidet technisches Rauschen.

* user.group().rights() statt user.getGroup().getRights()  
* order.customer().address() statt order.getCustomer().getAddress()

Handlungen innerhalb eines Systems werden dann nachvollziehbar, wenn der Code eine flüssige Geschichte erzählt: „Die Rechte der Gruppe des Benutzers“ (user.group().rights()) ist eine fachliche Aussage – „Hole Gruppe und hole Rechte“ eine technische Anweisung.

### **1.3.6 Attribute und Eigenschaften: Die Geheimnisse der Akteure**

Attribute werden als interne Details (**Secrets**) eines Akteurs begriffen. Für deren Benennung sowie den Zugriff darauf gilt das Prinzip der **Kontext-Prägnanz**:

**Avoid Context Heavy Names:** Innerhalb eines Objekts ist der Kontext bereits durch den Klassennamen definiert. Wiederholungen des Klassennamens in Attributen werden konsequent vermieden, um die natürliche Lesbarkeit zu fördern und technisches Rauschen zu minimieren.

* In Car: color statt carColor  
* In Product: price statt p

**Fachliche Präzision:** Auch interne Felder folgen der *Ubiquitous Language*. Anstatt generischer technischer Typen werden bevorzugt *Smart Value Objects* verwendet (z. B. Betrag statt double), um den Wert bereits intern fachlich zu qualifizieren.

Das Objekt wird somit nicht als Datensatz, sondern als eine fachliche Einheit betrachtet, bei der die Benennung der Eigenschaften so erfolgt, als würde eine Checkliste für diesen spezifischen Charakter ausgefüllt.

## **1.4 Zusammenfassung: Der Kern von OO-DDD mit Storytelling-Ansatz**

Objektorientiertes Domain-Driven Design bricht mit der Tradition, Fachlogik in zustandslosen Service-Klassen zu isolieren. Stattdessen transformieren sich Entities und Value Objects zu aktiven Entscheidungsträgern. Die konsequente Anwendung von „Tell, Don’t Ask“ und der Verzicht auf klassische Schichten schützen die fachliche Integrität durch echte Kapselung.

Architektur wird hier nicht als technisches Gerüst, sondern als hierarchische Dramaturgie von Geschäftskonzepten begriffen, die eine klare fachliche Geschichte erzählt. Diese Symbiose macht Software zum lebendigen Dokument. Ein System erreicht seine Vollendung, wenn der Quellcode für Fachexperten lesbar ist wie ein präzises Manuskript des eigenen Geschäftsprozesses.

# **2. Die Inszenierung: Implementierung von OO-DDD**

Nachdem das strategische und taktische Fundament gelegt ist, beginnt die praktische Inszenierung: Wie schreibt man Code, der eine widerspruchsfreie Geschichte erzählt? Im Mittelpunkt steht die Frage, wie Objekte ihre „Secrets“ bewahren können, während sie gleichzeitig die Handlung vorantreiben. Wir betrachten die technischen Muster, die diese radikale Kapselung ermöglichen:

* **Behavioral Coding:** Die Sprache der Akteure (Techniken für „Tell, Don’t Ask“).  
* **Persistenz-Strategien:** Das stille Archiv (Zustandssicherung ohne Kapselungsbruch via Memento oder Double Dispatch).  
* **Inter-Objekt-Kommunikation:** Der Dialog der Protagonisten (Koordination ohne „Ghostwriter“/Services).  
* **Package-Design:** Die Dramaturgie der Struktur (Hierarchie von Ebene 0 bis zum Detail).

## **2.1 Die Schauplätze: Identifikation der Bounded Contexts**

Die Identifikation von Bounded Contexts ist das Festlegen der Schauplätze. Ein Context definiert die Grenze, innerhalb derer eine Erzählung konsistent bleibt. Im DDD dient diese Grenze primär dem Schutz der Objekt-Geheimnisse.

* **Business Capability Mapping:** In modularen Monolithen ist dies die primäre Methode. Sie konzentriert sich auf das **„Was“** des Geschäfts (die Fähigkeiten). Diese Fähigkeiten bilden die **Top-Level-Packages**, sodass die Softwarearchitektur die Geschäftsdomäne direkt widerspiegelt.  
* **Event Storming:** Ein Workshop, um fachliche Ereignisse zu visualisieren. Wir suchen hier nach „Verhaltensknoten“ – den Momenten, in denen ein Protagonist eine Entscheidung trifft.  
* **Analyse fachlicher Verantwortlichkeiten:** Gruppierung von Funktionen zu logischen Einheiten, damit eine Figur nicht über mehrere Kapitel „zerstückelt“ wird.  
* **Organisatorische Grenzen (Conway’s Law):** Die Autonomie der intelligenten Objekte wird oft durch die Struktur der Fachabteilungen gestärkt.

## **2.2 Die Erzählsprache: Ubiquitous Language**

Die Ubiquitous Language ist das Bindeglied, das sicherstellt, dass die Geschichte im Code exakt so erzählt wird wie in der Fachwelt.

* **Methodik:** Begriffe werden präzise definiert und fließen direkt in die **Grammatik des Codes** ein. Methoden werden als Verben der Fachsprache benannt (order.confirm() statt orderService.confirm(order)).  
* **Kontextabhängigkeit:** Ein Protagonist (z. B. „Kunde“) hat je nach Schauplatz andere Fähigkeiten. Im *Vertrieb* kann er „bestellen“, im *Support* „ein Ticket eröffnen“. OO-DDD vermeidet „Gott-Objekte“ und schafft stattdessen schlanke, kontextspezifische Charaktere.

## **2.3 Die Verknüpfung: Context Mapping (Beziehungen definieren)**

Da Schauplätze nicht isoliert existieren, definiert das Context Mapping, wie Informationen fließen, ohne die Integrität der Akteure zu gefährden. Die Wahl des Musters hängt von der Architektur des „Theaters“ (Monolith, Modulith oder Microservices) ab:

| Muster | Monolith | Modulith | Microservices |
| :---- | :---- | :---- | :---- |
| **Shared Kernel** | Oft unvermeidbar | Nur für Basistypen | Hohes Risiko (Anti-Pattern) |
| **Customer-Supplier** | Gut für Struktur | **Empfohlen** (API-basiert) | Standard (Events/APIs) |
| **Anticorruption Layer** | Bei Legacy-Code | Sehr hilfreich | **Essenziell** für Autonomie |
| **Separate Ways** | Kaum möglich | Möglich | **Idealzustand** |

**Monolith:** Komponenten teilen sich oft denselben Speicher. Ein *Shared Kernel* führt hier schnell zu Verflechtungen. Ein *Anticorruption Layer* hilft, Fachbereiche von der technik zu isolieren.

**Modulith:** Strebt hohe fachliche Trennung an. **Customer-Supplier** ist ideal, da Module über klare Schnittstellen kommunizieren. Ein *Shared Kernel* darf nur absolute Basistypen (z. B. das Value Object Waehrung) enthalten.

**Microservices:** Autonomie ist das höchste Ziel. Der **Anticorruption Layer (ACL)** ist Pflicht, damit Änderungen an einem Dienst nicht die gesamte Erzählung korrumpieren. Der Idealzustand sind **Separate Ways**, bei denen Dienste völlig entkoppelt (z. B. via Event-Streaming) agieren.

## **2.4 Die Ausgestaltung der Handlung: Detail-Implementierung (Behavioral Enrichment)**

Innerhalb der domänenspezifischen Unterpakete erfolgt die inhaltliche Vertiefung der im Root-Paket gesetzten Konzepte. Während das Inhaltsverzeichnis (Ebene 0) lediglich definiert, **was** ein fachlicher Akteur darstellt, wird hier festgelegt, **wie** er agiert. Technische Anforderungen werden dabei als spezifische „Fähigkeiten“ so an die Akteure geheftet, dass ihre fachliche Identität ungetrübt bleibt.

**Die Komposition der „Smart Objects“**

Anstatt technische Belange in starren Schichten unter der Fachlogik zu vergraben, werden sie im OO-DDD modular um die Akteure gewickelt. Hierzu dienen etablierte Entwurfsmuster als „grammatikalische“ Werkzeuge, um die fachliche Geschichte technisch zu vervollständigen:

* **Decorator Pattern (Die Kette der Verantwortung):** Dies ist das primäre Instrument, um fachliches Verhalten oder technische Facetten zu ergänzen. Ein Decorator erweitert einen Akteur um Funktionen wie Validierung, Persistenz oder Protokollierung, während die ursprüngliche Identität nach außen hin gewahrt bleibt. So entstehen beispielsweise „gewissenhafte“ Akteure (Validierung), solche mit „Gedächtnis“ (Persistenz) oder „Kommunikatoren“, die bedeutsame Wendepunkte der Handlung als Events verkünden.  
* **Adapter Pattern (Die Brücke zur Außenwelt):** Adapter übersetzen technische Werkzeuge aus dem Austausch-Paket (ACL) in die spezifische Sprache der Domäne. Sie ermöglichen es, dass externe API-Clients fachliche Rollen einnehmen können, ohne dass technische Details in das Domänenmodell einsickern.  
* **Bridge & Proxy (Kapselung der Infrastruktur):** Diese Muster dienen dazu, technische Notwendigkeiten wie verzögertes Laden (Lazy Loading) oder unterschiedliche Ausgabeformate (z. B. PDF, Messaging) zu kapseln. Der fachliche Akteur delegiert hierbei die technische Umsetzung, behält jedoch stets die Regie über den Gesamtvorgang.

Durch diese kompositionelle Herkunft bleibt die Software ein System aus intelligenten Objekten, die ihre fachliche Geschichte eigenständig erzählen, während die technische Komplexität modular im Hintergrund verwaltet wird.

# **3 Die Generalprobe der Story: Autovermietungssystem**

In diesem Abschnitt wird die gesamte Kette vom Strategischen Design über die Behavioral Objects bis hin zur technischen Komposition im OO-DDD vollständig beschrieben. Ziel ist es, die theoretischen Prinzipien in ein greifbares, technisches Modell zu überführen.

Um die praktische Anwendung von OO-DDD zu verdeutlichen, betrachten wir den Systemkontext einer Autovermietung. Diese Domäne eignet sich ideal, da sie verschiedene Herausforderungen vereint: die Koordination komplexer Lebenszyklen (Fuhrpark), die Integration externer Zahlungsdienstleister (PayPal/Stripe), die Verwaltung sensibler Stammdaten (Kundendatenbank) sowie die Bereitstellung einer intuitiven Benutzerschnittstelle für den Buchungsprozess.

Anhand dieses Beispiels wird gezeigt, wie die fachliche Essenz des Mietgeschäfts gewahrt bleibt, während technische Details konsequent in die Peripherie verbannt werden.

## **3.1 Das Systemkontextdiagramm: Die Schauplätze der Handlung**

Der erste Schritt in der praktischen Umsetzung ist die Untersuchung der Systemumgebung. Das Systemkontextdiagramm dient hierbei als **analytischer Ausgangspunkt**, um die Schauplätze unserer Geschichte – die Bounded Contexts – präzise zu identifizieren und die daraus resultierende Top-Level-Paketstruktur abzuleiten. Jede externe Interaktion und jeder funktionale Kernbereich wird als eigenständiger Kandidat für einen Schauplatz bewertet:
```
                       User  
                    (Booking)  
                        │  
                        ▼  
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐  
 │ Payment │    │                 │    │  Customer   │  
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │  
 │         │    │  (Application)  │    │             │  
 └─────────┘    └─────────────────┘    └─────────────┘  
                        │  
                        ▼  
                ┌───────────────┐  
                │               │  
                │   Car Pool    │  
                │  (Inventory)  │  
                └───────────────┘
```
## **3.2 Identifikation der Bounded Contexts: Die Kapitelstruktur**

Ausgehend von diesem analytischen Startpunkt werden die Verantwortungsbereiche scharf voneinander abgegrenzt. Diese Trennung wird durch die **Top-Level-Paketstruktur** physisch im Code manifestiert, was der Aufteilung einer Geschichte in logische Kapitel entspricht:

**Booking (User Interface):** Umfasst die Logik der Benutzerinteraktion und die Reservierungsplanung.

* *Paket:* booking/

**Payment (PayPal/Stripe):** Kapselt ausschließlich die Logik der Finanztransaktionen und deren Absicherung.

* *Paket:* payment/

**Customer (Database):** Fokus auf die Verwaltung von Mieterprofilen und das Beziehungsmanagement.

* *Paket:* customer/

**Car Pool (Inventory):** Verwaltet den gesamten Lebenszyklus der physischen Fahrzeuge.

* *Paket:* carpool/

**Die daraus resultierende initiale Projektstruktur:**
```
carrental/  
├── carpool/  
├── customer/  
├── payment/  
└── booking/
```
## **3.3 Abbildung der fachlichen Essenz (Ubiquitous Language)**

Um die fachliche Essenz abzubilden, definiert jeder Schauplatz (Bounded Context) seine eigene, spezifische Sichtweise auf die Domäne. Ein „zentrales Datenobjekt“ (Shared Kernel) wird im OO-DDD strikt abgelehnt, um die Kapselung nicht zu schwächen. Stattdessen nutzen wir **kontextspezifische Interfaces** und **Value Objects** direkt im Root der jeweiligen Pakete (**Ebene 0**), um die Rollen der Akteure und deren Wissen festzulegen:

* **Fuhrpark-Kontext (carpool/):**  
  * *Fokus:* Physische Verwaltung und Bestandsführung. Das Auto ist ein Asset mit Hardware-Eigenschaften.  
  * Car: Definiert Fähigkeiten rund um den Zustand des Fahrzeugs.  
  * *Value Objects (grau):* Kilometerstand, Tankfüllung, Wartungsstatus.  
* **Kunden-Kontext (customer/):**  
  * *Fokus:* Mieterprofile und Beziehungsmanagement.  
  * ICustomer: Repräsentiert die Person und deren Kommunikationswege.  
  * *Value Objects (grau):* EmailAdresse, Postanschrift, KundenStatus.  
* **Zahlungs-Kontext (payment/):**  
  * *Fokus:* Finanzielle Transaktionssicherheit. Hier existiert kein physisches Auto, sondern nur ein Zahlungsstrom.  
  * IPayment: Definiert den Transaktionswert und dessen Verrechnung.  
  * *Value Objects (grau):* Waehrungsbetrag, TransaktionsID, Zahlungsart.  
* **Buchungs- / Nutzer-Kontext (booking/):**  
  * *Fokus:* Reservierungslogik und Benutzersitzung. Das Auto ist hier lediglich eine Zeit-Ressource.  
  * Reservation: Definiert die zeitliche Verfügbarkeit und den Slot.  
  * *Value Objects (grau):* Zeitraum, BuchungsNummer, SitzungsToken.

Durch diese Aufteilung stellen wir sicher, dass jedes Objekt nur das Verhalten und die Werte anbietet, die im jeweiligen Kontext fachlich relevant sind.

## **3.4 Domain Interfaces und das Composition Root Pattern**

Um eine verständliche Navigation im Code zu ermöglichen, müssen die wichtigsten Konzepte einer Software sofort sichtbar sein – direkt im Top-Level-Paket. Dies sichert die konzeptionelle Integrität und bewahrt die fachliche Identität des Systems, bevor technische Details oder Frameworks das Modell verzerren.

**Das fachliche Vorwort einer Story (Ebene 0)**

Durch die Platzierung der Domänen-Schnittstellen und zentralen Value Objects im Hauptpaket wird die „Geschichte“ des Systems für jeden Leser sofort greifbar. Die Struktur auf Ebene 0 liest sich wie ein **Inhaltsverzeichnis der Geschäftslogik**:
```
carrental/  
├── CarNumber.cs           ← Value Object (Fachliche Identität)  
├── ICar.cs                ← Domain Interface (Das Fahrzeug und seine Taten)  
├── ICarPool.cs            ← Collection Interface (Der Fuhrpark-Bestand)  
├── ICustomer.cs           ← Domain Interface (Der Mieter als Akteur)  
├── ICustomers.cs          ← Collection Interface (Das Kunden-Verzeichnis)  
├── IReservation.cs        ← Domain Interface (Der Zeit-Slot der Miete)  
├── IReservations.cs       ← Collection Interface (Das Buchungs-Journal)  
└── ICarRentalApp.cs       ← Composition Root (Einstiegspunkt)
```
**Der Composition Root als „Einstieg in die Geschichte“**

Indem wir auch den **Composition Root** (ICarRentalApp) als Interface im Top-Level-Paket definieren, markieren wir unmissverständlich den Beginn der fachlichen Erzählung. Er dient als einziger logischer Einstiegspunkt, über den man zu den fachlichen Kollektionen und deren Objekten navigiert:
```
// ICarRentalApp.cs - Composition Root Interface (Ebene 0)  
namespace CarRental;

public interface ICarRentalApp  
{  
    ICarPool CarPool();  
    ICustomers Customers();  
    IReservations Reservations();  
}
```
Die ICarRentalApp ermöglicht den kontrollierten Zugriff auf die Kollektions-Schnittstellen (z. B. ICarPool), welche wiederum den Zugriff auf die einzelnen verhaltensorientierten Domänenobjekte (z. B. ICar) steuern.

**Warum dieses Muster entscheidend für OO-DDD ist:**

1. **Keine technischen Leaks:** Der Leser sieht keine Repositories, Datenbanken oder Controller. Er sieht nur die fachliche Landkarte.  
2. **Gesteuerte Navigation:** Man „hangelt“ sich von der App über die Kollektionen zu den Verhaltens-Objekten. Dies verhindert unkontrollierte Zugriffe quer durch das System.  
3. **Kapselung auf Systemebene:** Die Implementierungen dieser Interfaces liegen in tieferen Paketen und bleiben für den Konsumenten der ICarRentalApp verborgen. Das System bleibt ein „intelligenter Problemlöser“, dessen interne Mechanik geschützt ist.

## **3.5 Anti-Corruption Layer (ACL): Isolation von Frameworks und Bibliotheken**

Damit diese fachliche Idylle der Ebene 0 nicht durch technisches Fachchjargon – wie SQL-Dialekte, REST-DTOs oder externe API-Strukturen – unverständlich wird, benötigt die Geschichte einen Schutzwall. Hier tritt der **Anti-Corruption Layer (ACL)** auf den Plan: Er fungiert als diplomatischer Übersetzer an den Grenzen des Systems und stellt sicher, dass technische Details die Reinheit der Domäne niemals gefährden. Damit kann sicher gestellt werden, dass technische Sachzwänge externer Abhängigkeiten (z. B. Datenbanken, PayPal-APIs oder Kafka) niemals in die Domäne einsickern.

Um diese strikte Trennung zwischen der fachlichen Logik und der technischen Infrastruktur zu gewährleisten, bieten sich zwei Strategien an:

**Option 1: Auslagerung in separate Projekte (Empfohlen für große Systeme)**

Sämtliche technischen Aspekte werden in eigenständige Projekte ausgelagert und lediglich als Abhängigkeiten in das Hauptprojekt eingebunden. Das Kernprojekt carrental bleibt dadurch vollständig frei von technischem „Ballast“:
```
carrental                     → depends on: -endpoint, -resource, -storage, -... 

carrental-endpoint            → HTTP classes JSON/XML DTOs  
carrental-resource            → REST classes JSON/XML DTOs  
carrental-storage             ← EF Core ...DbContexts/Entities  
carrental-paypal              ← Paypal REST client JSON/XML DTOs  
carrental-mailing             ← EMAIL sending by SMTPS/IMAPS/POP3S   
carrental-messaging           ← QUEUES integration like Kafka with AVRO classes  
carrental-pdf                 ← PDF library with DTOs/helper classes  
carrental-text                ← Textformatting library with DTOs/helper classes  
carrental-...                 ← other framework or library
```
**Wichtig:** Die Domänen-Interfaces auf **Ebene 0 (Root)** dürfen unter keinen Umständen Klassen aus diesen technischen Projekten referenzieren.

**Option 2: Isolation im exchange/-Paket (Lösung für kompakte Codebasen)**

Alle technischen Belange, die einen Datenaustausch mit externen Systemen erfordern, werden innerhalb eines dedizierten Pakets exchange/ isoliert und nach technischen Aspekten unterteilt:
```
carrental/  
├── .../   
├── exchange/  
│   ├── paypal/             ← PayPal-API Client & technische DTOs  
│   ├── resource/           ← REST-Service JSON/XML Strukturen  
│   ├── storage/            ← Persistenz-Details (z. B. EF Core)  
│   ├── mailing/            ← Kommunikationswege (SMTP/IMAP)  
│   └── messaging/          ← Ereignis-Infrastruktur (Kafka/AVRO)  
├── .../      
└── ICar.cs                 ← Hat keinerlei Kenntnis von EF Core oder REST
```
**Die Rolle der technischen Klassen als „Statisten“**

Klassen innerhalb des exchange/-Bereichs werden ausschließlich als **Werkzeuge** betrachtet. Sie treten erst in den tieferen Kapiteln (z. B. innerhalb der Implementierung von carpool/) auf, um die fachlichen Versprechen der Interfaces einzulösen, ohne die Identität auf Ebene 0 zu berühren.

Ein ORM wie **EF Core** bleibt beispielsweise vollständig im Paket exchange/storage/ gekapselt. Die Domäne nutzt diese Strukturen lediglich für den Persistierungsvorgang. Es wird strikt vermieden, dass technische Attribute (z. B. Annotations) oder Framework-Basisklassen in das verhaltensorientierte Domänenmodell einsickern.

## **3.6 Die Charakterentwicklung: Detail-Implementierung (Behavioral Enrichment)**

Innerhalb der domänenspezifischen Kapitel (z. B. carpool/) erhalten die Protagonisten ihre Tiefe. Während das Inhaltsverzeichnis (Ebene 0) lediglich festlegt, **was** ein Akteur ist (Interface), definieren wir hier, **wie** er sich verhält. Wir nutzen Entwurfsmuster als „grammatikalische Werkzeuge“, um technische Anforderungen als Fähigkeiten an den Akteur zu heften, ohne seine fachliche Identität zu verzerren.

**Die Komposition der „Smart Objects“**

Statt einer starren Schichtenarchitektur setzen wir auf eine flexible Komposition von Objekten. Jedes Objekt übernimmt eine spezifische Rolle in der Erzählung:

* **Decorator Pattern (Die Kette der Verantwortung):** Dies ist unser primäres Werkzeug, um technische Aspekte um die Kernlogik zu legen. Jeder Decorator fügt eine Facette hinzu, ohne das Interface zu verändern:  
  * ValidCar prüft die **Geschäftsregeln** (Validierung).  
  * StoredCar verleiht dem Akteur ein **Gedächtnis** (Persistenz).  
  * LoggedCar führt das **Protokoll** (Audit).  
  * PublishedCar ist der **Verkünder**, der Ereignisse an das Messaging-System sendet.  
* **Adapter Pattern (Die Brücke zur Außenwelt):** Adapter übersetzen technische Werkzeuge aus dem exchange/-Paket (ACL) in die Sprache unserer Domäne. So kann ein technischer API-Client die fachliche Rolle eines „Zahlers“ übernehmen.  
* **Bridge & Proxy (Infrastruktur-Geheimnisse):** Diese Muster kapseln Details wie verzögertes Laden (Lazy Loading) oder verschiedene Ausgabeformate (PDF/CSV). Der Protagonist delegiert die Technik, bleibt aber der Regisseur der Handlung.

**Beispiel: Die Dramaturgie im Paket carpool/**

Die Komposition ermöglicht es, technische Details modular zu „stapeln“, während die Basis (SimpleCar) rein fachlich bleibt:
```
carrental/  
├── .../  
├── carpool/  
│   ├── SimpleCar.cs       ← Der Kern-Charakter (Pure OO)  
│   ├── StoredCar.cs       ← Charakter mit Gedächtnis (Decorator + Adapter)  
│   ├── ValidCar.cs        ← Der gewissenhafte Charakter (Validierung)  
│   ├── ServedCarPool.cs   ← Der Repräsentant nach außen (REST-Adapter)  
│   └── PublishedCar.cs    ← Der Mitteilsame (Brücke zu Kafka)
```
**Code-Beispiel: Das Zusammenspiel der Akteure**

Das folgende Beispiel zeigt, wie ein StoredCar die Fachlogik erweitert, ohne die Kapselung zu verletzen. Ziel ist es, ein Auto zu vermieten (Rent), ohne dass die Fachlogik von der Datenbank weiß.

**Der pure Charakter eines **Akteurs:
```
namespace CarRental.CarPool;

public class SimpleCar : ICar   
{  
    private bool _isRented;

    public void Rent(ICustomer customer, DateTime from, DateTime to)   
    {  
        // Reine Fachlogik: Invariante prüfen  
        if (_isRented) throw new DomainException("Car already rented");  
        _isRented = true;  
    }  
}
```
Dieser Akteur (mit **Gedächtnis**) nutzt technische Werkzeuge aus dem exchange/storage/-Paket, um die Tat im „Archiv“ zu sichern:
```
using CarRental.Exchange.Storage; 

namespace CarRental.CarPool;

public class StoredCar : ICar   
{  
    private readonly ICar _origin;           // Der dekorierte Charakter  
    private readonly CarDbContext _context;  // Das technische Archiv  
    private readonly string _carId;

    public void Rent(ICustomer customer, DateTime from, DateTime to)   
    {  
        _origin.Rent(customer, from, to);    // Erst die Fachlogik...  
          
        var entity = _context.Cars.Find(_carId);  
        if (entity != null)   
        {  
            entity.IsRented = true;          // ...dann die Speicherung  
            _context.SaveChanges();  
        }  
    }  
}
```
**Code-Beispiel: Die Nebenhandlungen im Paket** payment/

Der Adapter (als **diplomatische Übersetzer**) im Paket payment/ schließt die Lücke zwischen dem fachlichen Interface und der PayPal-Technik:
```
using CarRental.Exchange.Paypal; 

namespace CarRental.Payment;

public class PaypalPayment : IPayment  
{  
    private readonly Paypal _paypal; // Technisches Werkzeug aus dem ACL

    public void Process(Amount amount)  
    {  
        // Übersetzung: Fachliches Wissen -> Technische Parameter  
        var response = _paypal.ExecuteTransaction(amount.Currency(), amount.Value());

        if (response.Status != "Success")  
            throw new PaymentFailedException("PayPal transaction failed");  
    }  
}
```
**Konsequenzen für “Zuschauer”**

Durch diesen Ansatz bleibt die **Ebene 0 vollständig frei von technischem Rauschen**. Die Komplexität wird nicht durch „Services“ versteckt, sondern durch die geschickte **Besetzung von intelligenten Objekten** gelöst. Jedes Objekt bleibt klein, testbar und erzählt einen klaren Teil der Geschichte.

## **3.7 Regie und Montage: Implementierung des Composition Root**

Der **Composition Root** ist der finale Ort der Zusammenkunft. Hier wird die theoretische Struktur in einen lebendigen Objektgraphen übersetzt. In der CarRentalApp legen wir fest, wie die zuvor definierten Decoratoren, Adapter und Basis-Objekte miteinander verschachtelt werden. Er ist der Regietisch, an dem die Besetzung der Rollen stattfindet. Nur an diesem zentralen Ort darf ein **Dependency Injection (DI) Container** verwendet werden. Dadurch verhindern wir, dass technische Infrastruktur in unsere Fachkapete „einsickert“ und den Lesefluss der Domäne stört.

**Die Bühne: Das Paket application/**

Das Paket application/ beherbergt die verschiedenen Einstiegspunkte und die Konfiguration der technischen Infrastruktur:
```
carrental/  
├── application/  
│   ├── CarRentalApp.cs        ← Die Regie: Impl. des Composition Root & Main()  
│   ├── CarRentalAppDI.cs      ← Das Besetzungsbüro: DI-Konfiguration  
│   └── KafkaQueueConfig.cs    ← Bühnentechnik: Infrastruktur-Konfiguration  
├── .../  
└── ICarRentalApp.cs           ← Das Drehbuch: Interface aus Ebene 0
```

**Das „Zusammenbauen“ der Geschichte**

Im Composition Root wird die fachliche Anforderung in eine Kette von Verantwortlichkeiten übersetzt. Wenn wir ein Auto aus dem Fuhrpark anfordern, bauen wir zur Laufzeit eine **„Zwiebel“ aus intelligenten Objekten**. Die Schachtelung im Code entspricht dabei exakt der Dramaturgie des Prozesses:
```
using CarRental.CarPool;  
using CarRental.Exchange.Storage;  
using Microsoft.Extensions.DependencyInjection;

namespace CarRental.Application;

public class CarRentalApp : ICarRentalApp  
{  
    private readonly IServiceProvider _services;

    // Framework-Integration: Nur hier ist der Container erlaubt  
    public CarRentalApp(IServiceProvider services) => _services = services;

    public ICarPool CarPool()   
    {  
        // Die Montage der Kette:   
        // Ein Cache umschließt das Archiv (DB), welches die Fachlogik schützt.  
        return new CachedCarPool(  
            new StoredCarPool(  
                _services.GetRequiredService<CarDbContext>()  
            ),  
            _services.GetRequiredService<IMemoryCache>()  
        );  
    }

    public ICustomers Customers()   
    {     
        // Die Kundenverwaltung erhält ihre Fähigkeiten: Validierung und Gedächtnis  
        return new ValidatedCustomers(  
            new StoredCustomers(  
                _services.GetRequiredService<CustomerDbContext>()  
            )  
        );  
    }  
}
```
**Montage des Storytelling:**

* **Zentrale Regie:** Möchten wir für alle Autos ein Audit-Logging aktivieren? Wir fügen einfach einen LoggedCarPool-Decorator im Composition Root hinzu. Die „Schauspieler“ (Fachobjekte) müssen dafür nicht umgeschult werden.  
* **Vollständige Framework-Agnostik:** Die Business-Pakete bleiben rein fachlich; sie wissen nicht einmal, dass ein DI-Container existiert.  
* **Maximale Transparenz:** Die Reihenfolge der Verschachtelung legt die Ausführungslogik fest. Der Leser sieht sofort: Erst wird im Cache gesucht, dann im Archiv (Datenbank).  
* **Explizite Dramaturgie:** Die Hierarchie der Decoratoren macht die Verarbeitungsreihenfolge (z. B. erst Validieren, dann Speichern) im Code explizit lesbar – wie Regieanweisungen in einem Drehbuch.  
* **Saubere Charaktere:** Da die Entscheidung über die Komposition hier fällt, bleiben die Klassen in carpool/ oder customer/ fokussiert. Sie müssen keine Infrastruktur-Entscheidungen treffen, sondern bieten lediglich ihr Verhalten an.

## **3.8 Das fertige Manuskript: Die Systemstruktur im Überblick**

Die folgende Projektstruktur ist das Ergebnis unserer Reise. Sie verdeutlicht die radikale Trennung zwischen der **fachlichen „Story“ (Ebene 0)**, der **verhaltensorientierten Ausgestaltung (Fachkapitel)** und der **technischen Infrastruktur (Exchange/ACL)**.
```
carrental/  
│  
├── application/                     ← Einstiegspunkt (Composition Root)  
│   ├── CarRentalApp.cs              ← ASP.NET Core Main & Laufzeit-Initialisierung  
│   ├── CarRentalAppDI.cs            ← Exklusive Konfiguration des Dependency-Injections  
│   └── KafkaQueueConfig.cs          ← Infrastruktur-Setup für Messaging  
│  
├── carpool/                         ← Bounded Context: Fuhrparkmanagement  
│   ├── CachedCar.cs                 ← Performance-Decorator (Caching-Logik)  
│   ├── CachedCarPool.cs             ← Caching für die Fahrzeug-Kollektion  
│   ├── LoggedCar.cs                 ← Audit-Decorator (Protokollierung)  
│   ├── SimpleCar.cs                 ← Kern-Implementierung (Reine Fachlogik)  
│   ├── StoredCar.cs                 ← Persistenz-Decorator (nutzt exchange/storage/)  
│   ├── StoredCarPool.cs             ← Datenbank-gestützte Kollektion  
│   ├── ServedCarPool.cs             ← REST-Schnittstelle (nutzt exchange/resource/)  
│   ├── PublishedCar.cs              ← Event-Producer (nutzt exchange/messaging/)  
│   ├── ReceivedCar.cs               ← Event-Consumer (nutzt exchange/messaging/)  
│   └── ValidCar.cs                  ← Validierungs-Decorator (Geschäftsregeln)  
│  
├── customer/                        ← Bounded Context: Kundenverwaltung  
│   ├── StoredCustomer.cs            ← Datenbank-Decorator für Kundenobjekte  
│   ├── StoredCustomers.cs           ← Fachliche Kunden-Kollektion (DB-Anbindung)  
│   ├── NotifiedCustomer.cs          ← Kommunikations-Decorator (nutzt exchange/mailing/)  
│   └── ...                          ← Weitere verhaltensbasierte Erweiterungen  
│  
├── payment/                         ← Bounded Context: Zahlungsabwicklung  
│   ├── PaypalPayment.cs             ← PayPal-Integration als Verhaltens-Decorator  
│   ├── CardPayment.cs               ← Kreditkarten-Logik  
│   └── ...                          ← (Nutzt intern die ACL in exchange/paypal/)  
│  
├── booking/                         ← Bounded Context: Reservierung & User-Interaktion  
│   ├── user/                        ← Sub-Domäne: Web-Sitzung & Server-Side Rendering  
│   │   ├── control/                 ← UI-Komponenten (Buttons, Inputs etc.)  
│   │   ├── layout/                  ← UI-Struktur & Design-Komponenten  
│   │   ├── page/                    ← Spezifische Webseiten-Logik  
│   │   ├── Control.cs               ← Basis-Abstraktion: UI-Element  
│   │   ├── Layout.cs                ← Struktur-Abstraktion: Layout-Hilfen  
│   │   ├── Page.cs                  ← Seiten-Abstraktion (erweitert Control)  
│   │   ├── StoredUser.cs            ← Persistenz-Decorator für Benutzerprofile  
│   │   └── WebUser.cs               ← Sitzungs-Decorator (Zustand der Websession)  
│   ├── IUser.cs                     ← Domänen-Interface: Benutzer-Verhalten  
│   ├── IUsers.cs                    ← Domänen-Interface: Benutzer-Verzeichnis  
│   ├── StoredReservation.cs         ← Persistenz-Decorator für Reservierungen  
│   └── ...                            
│  
├── exchange/                        ← Anti-Corruption Layer (ACL) / Technische Isolation  
│   ├── paypal/                      ← Externe API: PayPal-Bibliothek & DTOs  
│   │   ├── Paypal.cs                ← Technischer API-Client (Zahlungsabwicklung)  
│   │   ├── PaypalRequest.cs         ← Datenstruktur für Anfragen (JSON/XML)  
│   │   └── PaypalResponse.cs        ← Datenstruktur für Antworten (JSON/XML)  
│   ├── resource/                    ← REST-Ressourcen: DTOs für API-Endpunkte  
│   │   ├── CarRequest.cs            ← Technisches Request-Objekt  
│   │   ├── CarResource.cs           ← Technisches Response-Objekt  
│   │   └── CarResources.cs          ← Listen-Ressource  
│   ├── storage/                     ← Persistenz-Technologie (z. B. EF Core)  
│   │   ├── CarEntity.cs             ← Datenbank-DTO (Tabellenabbild)  
│   │   ├── CarDbContext.cs          ← Technischer Datenzugriff (DAO)  
│   │   └── ...                        
│   ├── mailing/                     ← Kommunikation: Protokolle (SMTP/IMAP)  
│   │    └── SmtpsEmail.cs           ← Technische E-Mail-Versand-Logik  
│   └── messaging/                   ← Infrastruktur: Messaging (Kafka)  
│        ├── CarRentedEvent.cs       ← Serialisierte Ereignis-Daten (AVRO)  
│        └── CarReturnedEvent.cs     ← Serialisierte Ereignis-Daten (AVRO)  
│  
├── CarNumber.cs                     ← Value Object (Globale fachliche Identität)  
├── ICar.cs                          ← Domänen-Interface: Das Fahrzeug (Verhalten)  
├── ICarPool.cs                      ← Domänen-Interface: Der Fuhrpark-Bestand  
├── ICustomer.cs                     ← Domänen-Interface: Der Kunde  
├── ICustomers.cs                    ← Domänen-Interface: Das Kunden-Verzeichnis  
├── IReservation.cs                  ← Domänen-Interface: Die Reservierung  
├── IReservations.cs                 ← Domänen-Interface: Das Reservierungs-Verzeichnis  
├── ICarRentalApp.cs                 ← Composition Root Interface (Einstieg Ebene 0)  
└── ...
```
# **4. Die Evolution der Erzählung: Vom Kurzroman zum Epos**

**Der evolutionäre Pfad: Vom Monolithen zum Microservice**

Dieser Abschnitt beschreibt einen Drei-Phasen-Ansatz, um ein System modular und skalierbar zu entwickeln, ohne den roten Faden der fachlichen Integrität zu verlieren. Eine Software ist demnach so zu konzipieren, dass sie wie eine Buchreihe mit steigenden Anforderungen expandieren kann.

Die Wahl zwischen Monolith, Modulith oder Microservice ist dabei maßgeblich von der Art der Kommunikation innerhalb der Entwicklungsorganisation abhängig. Anstatt gegen diese soziale Dynamik anzukämpfen, dient **Conway’s Law** als architektonischer Kompass. Es besagt:

*„Die Struktur eines Softwaresystems wird das Kommunikationsmodell der Organisation widerspiegeln, die es entwirft.“*

**Entscheidungsmatrix: Architektur folgt Kommunikation**

Die Wahl des Modells sollte die Teamdynamik unterstützen, anstatt künstliche Barrieren zu erzeugen.

| Architektur | Organisationsstruktur | Kommunikationsmodell | Fokus des Storytellings |
| :---- | :---- | :---- | :---- |
| **Monolith** | Ein Team (< 10 Personen) | Informell & Synchron | **Der Kurzroman:** Kompakte Einheit, gemeinsamer Wissensstand. |
| **Modulith** | Mehrere Teams (10–30 Personen) | Strukturiert & Dokumentiert | **Die Buchreihe:** Autonome Bände, gemeinsamer Schuber (App). |
| **Microservice** | Viele autonome Teams (> 30 Personen) | Formalisiert & Asynchron | **Das Epos:** Unabhängige, lose verknüpfte Geschichten. |

**Strategische Leitlinien für den Architekturwechsel**

1. **Vom Monolith zum Modulith:** Wenn die parallele Arbeit am Code zu häufigen Konflikten führt, signalisiert dies den Bedarf nach physischen Modulgrenzen. Ziel ist die Senkung der kognitiven Last durch getrennte Kapitel.  
2. **Vom Modulith zum Microservice:** Dieser Schritt dient der organisatorischen Skalierung. Er ist erforderlich, wenn Teams eine vollständige Unabhängigkeit für Deployment- und Release-Zyklen benötigen.

**Das Inverse Conway Maneuver:**

Zur Förderung einer fachlich orientierten Architektur werden Teams **vertikal nach Bounded Contexts** organisiert. Besitzt ein Team einen kompletten fachlichen Schauplatz (z. B. payment/) von der Benutzeroberfläche bis zur Datenhaltung, kann die Geschichte dieses Kontextes ohne Informationsverlust in Code überführt werden.

## **4.1 Phase 1: Der Monolith – Die Geschichte in einem Band**

Der Einstieg in die Systementwicklung erfolgt idealerweise als Monolith. In dieser Phase befinden sich alle Bounded Contexts innerhalb eines einzigen ausführbaren Artefakts (z. B. eine Assembly oder JAR-Datei). Die Trennung der fachlichen Schauplätze erfolgt rein logisch über die Top-Level-Paketstruktur.
```
carrental/          ← Einziges ausführbares Artefakt (Monolith)  
├── application/    ← Einstiegspunkt & Montage (Composition Root)  
├── carpool/        ← Bounded Context: Fuhrpark  
├── customer/       ← Bounded Context: Kunden  
├── exchange/       ← Anti-Corruption Layer (Technische Infrastruktur)  
├── payment/        ← Bounded Context: Finanzen  
└── booking/        ← Bounded Context: Reservierung
```
**Kriterien für die Wahl des Monolithen**

Die Entscheidung für einen Monolithen basiert auf spezifischen organisatorischen und fachlichen Rahmenbedingungen:

* **Projektstart:** Wenn die Domänengrenzen noch fließen und fachliche Konzepte schnell zwischen den Kontexten verschoben werden müssen.  
* **Kleine Teams:** Bei einer Gruppengröße von weniger als 10 Entwicklern bleibt der Overhead für Infrastruktur, Netzwerkkommunikation und Deployment minimal.  
* **Einfachheit der Betriebsumgebung:** Wenn ein unkomplizierter Deployment-Prozess (ein Artefakt, eine zentrale Datenbank) den aktuellen Anforderungen genügt.

**Der Fokus: Etablierung der Ebene 0**

In dieser Phase liegt das Hauptaugenmerk darauf, die **Ebene 0 (Interfaces und Value Objects)** stabil zu etablieren. Da alle Module im selben Prozess laufen, ist die Kommunikation über Interfaces hocheffizient. Eine saubere Definition dieser Schnittstellen ist die Voraussetzung dafür, dass eine spätere physische Trennung ohne ein Refactoring der Geschäftslogik durchgeführt werden kann.

Die Architektur gleicht hier einem **Kurzroman**: Die Geschichte ist kompakt und zentralisiert, aber bereits in klare Kapitel unterteilt, die auf eine spätere Erweiterung vorbereitet sind.

## **4.2 Phase 2: Der Modulith – Die Geschichte als Buchreihe**

Der Übergang zu einer modularen Struktur ist eine bewusste Entscheidung, um die Erzählstruktur gegen zunehmende Komplexität abzusichern. Wenn die Geschichte zu umfangreich für einen einzelnen Band wird, erfolgt die physische Aufteilung in eine **Buchreihe autonomer Module**. Jedes Team agiert in seinem eigenen fachlichen Projekt, bleibt jedoch Teil einer gemeinsamen Gesamtausgabe.

**Kriterien für die Modularisierung**

Diese Phase wird eingeleitet, wenn folgende Faktoren an Relevanz gewinnen:

* **Wachsende Organisation:** Ab einer Größe von ca. 4–5 Entwicklern entstehen natürliche Verantwortungsbereiche. Module ziehen physische Grenzen im Code und verhindern unkontrollierte Eingriffe in fremde Fachbereiche.  
* **Kognitive Entlastung:** Die Notwendigkeit, das gesamte System für lokale Änderungen zu verstehen, wird reduziert. Die modulare Architektur stellt die mentale Landkarte wieder her.  
* **Test-Effizienz:** Unabhängige „Test-Slices“ ermöglichen die Validierung einzelner Module, ohne die gesamte Suite auszuführen.  
* **Vorbereitung auf Verteilung:** Ein Modulith dient als Versicherung gegen einen „verteilten Monolithen“. Erst wenn die funktionalen Schnittstellen innerhalb der modularen Struktur stabil sind, ist ein physischer Schnitt zu Microservices sicher durchführbar..

### **4.2.1 Strategische Entkopplung: Der Abschied vom Shared Kernel**

In dieser Phase wird das geteilte Modul carrental (Ebene 0) idealerweise vollständig eliminiert. Ein zentrales Prinzip lautet: **„Better duplication than the wrong abstraction“** (Sandi Metz). Notwendige Identitäten (Value Objects) wie CarId oder CustomerId werden direkt innerhalb der jeweiligen Kontexte dupliziert.

**Warum wir das „gemeinsame Vorwort“ vermeiden:**

* **Autonomie:** Jeder Bounded Context kann Datenstrukturen anpassen, ohne Nebeneffekte auf andere Module zu riskieren.  
* **Strukturelle Sauberkeit:** Ein „Common“-Modul verliert seine Funktion als unkontrollierter Ablageort für unspezifische Logik.  
* **Semantische Präzision:** Fachbegriffe erhalten kontextspezifische Validierungsregeln (z. B. eine CustomerId im Zahlungsverkehr vs. Support).

### **4.2.2 Die flache Projektstruktur**

Jeder Bounded Context wird als eigenständiges Projekt mit eigenem Inhaltsverzeichnis (Ebene 0) realisiert. Die Domäne bleibt die steuernde Instanz (Regisseur) der jeweiligen Erzählung. Die **Technischen Anhänge** dienen als funktionale Werkzeuge, die streng vom fachlichen Kern getrennt sind:
```
carrental-carpool             ← Projekt: Fuhrpark (Eigene Erzählung)  
├── carpool/                  → Hängt ab von: -endpoint, -resource, -storage, -messaging  
│   ├── CachedCarPool.cs      ← Cache Decorator  
│   ├── LoggedCar.cs          ← Logging Decorator  
├   ...  
├── CustomerId.cs             ← Value Object (Spezifisches Vokabular)  
├── PaymentId.cs              ← Value Object (Spezifisches Vokabular)  
├── ICar.cs                   ← Domain Interface (Rolle des Fahrzeugs)  
├── ICarPool.cs               ← Collection Interface (Das Inventar)  
...  
carrental-carpool-endpoint    ← Technischer Anhang: Http Clients  
carrental-carpool-resource    ← Technischer Anhang: REST Services  
carrental-carpool-storage     ← Technischer Anhang: Das Archiv (ORM)  
carrental-carpool-messaging   ← Technischer Anhang: Die Funkstation (AVRO)

carrental-customer             ← Projekt: Kundenverwaltung  
├── customer/                  → Hängt ab von: -endpoint, -resource, -storage, -mailing  
│   ├── StoredCustomer.cs      ← Persistenz-Decorator  
│   ├── StoredCustomers.cs     ← Persistenz-Decorator  
│   ├── NotifiedCustomer.cs    ← Kommunikations-Decorator  
│   └── ...cs  
├── CarId.cs                   ← Value Object  
├── PaymentId.cs               ← Value Object  
├── ICustomer.cs               ← Domain Interface  
├── ICustomers.cs              ← Collection Interface  
...  
carrental-customer-endpoint    ← Technischer Anhang  
carrental-customer-resource    ← Technischer Anhang  
carrental-customer-storage     ← Technischer Anhang  
carrental-customer-mailing     ← Technischer Anhang: Das Postamt (SMTP)

carrental-payment              ← Projekt: Zahlungsverkehr  
├── payment/                   ← Die Handlung  
├── paypal/                      
│   └── ...cs                  ← Payment Decorator (nutzt Paypal-Technik)  
├── Payment.cs                 ← Domain Interface  
...  
carrental-payment-paypal       ← Technischer Anhang: PayPal-Bibliothek

carrental-booking              ← Projekt: Reservierung & UI  
...
```
Die **Technische Anhänge** sind die funktionalen Werkzeuge, die ein Kapitel (Modul) benötigt, um seine Geschichte in der realen Welt (Datenbank, Netzwerk, E-Mail) zu manifestieren. Sie gehören zum „Wie“ und sind streng vom fachlichen „Was“ getrennt.

### **4.2.3 Das Kompositions-Modul (Der Schuber)**

Das Projekt carrental fungiert in dieser Phase als **ausführbarer Schuber**. Es dient als Ort der Montage, führt alle autonomen Bände zusammen und regelt die Besetzung der Schnittstellen (Dependency Injection):
```
carrental/                     ← Das Gesamtwerk (Deployable Unit)  
└── application/               ← Montage: Hängt von allen Bänden ab  
    ├── CarRentalApp.cs        ← Regie & DI-Konfiguration (Composition Root)  
    └── KafkaQueueConfig.cs    ← Zentrale Messaging-Konfiguration
```
### **4.2.4 Hierarchische Projektstruktur (Kapselung der Technik)**

Wird die Anzahl der Einzelprojekte in einem wachsenden System zu unübersichtlich, bietet sich eine **hierarchische Organisation** an.  Anstatt für jeden technischen Aspekt ein separates Top-Level-Projekt zu erstellen, werden diese innerhalb von **Modul-Gruppen** (Parent Projects) gekapselt. Dies bewahrt die Übersichtlichkeit, ohne die Prinzipien der fachlichen Isolation zu verletzen.

In dieser Struktur bildet jeder Bounded Context eine funktionale Einheit, die ihre technischen Satelliten selbst verwaltet. Der fachliche Kern bleibt dabei das Herzstück jeder Gruppe:
```
carrental                     ← Root-Modul: Komposition aller Projekte  
├── application/              → Hängt ab von: -carpool, -customer, -payment, -booking    
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI (Composition Root)  
│   └── KafkaQueueConfig.cs   ← Zentrale Messaging-Konfiguration  
└── ...

carrental-carpool             ← Modul-Gruppe: Fuhrpark (Parent Project)  
├── carpool/                  ← Der fachliche Kern (Ebene 0 + Decoratoren)  
├── carpool-endpoint/         ← Technische API-Clients  
├── carpool-resource/         ← REST-Ressourcen (DTOs)  
├── carpool-storage/          ← Persistenz-Implementierung (z. B. EF Core)  
└── carpool-messaging/        ← Event-Infrastruktur (z. B. Kafka)

carrental-customer            ← Modul-Gruppe: Kundenverwaltung (Parent Project)  
├── customer/                 ← Fachlicher Kern  
├── customer-endpoint/          
├── customer-resource/          
├── customer-storage/           
└── customer-mailing/         ← Kommunikations-Technik (SMTP/IMAP)

carrental-payment             ← Modul-Gruppe: Zahlungsverkehr  
├── payment/                  ← Fachlicher Kern  
└── payment-paypal/           ← Spezifische PayPal-Integration

carrental-booking             ← Modul-Gruppe: Reservierung & UI  
├── user/                     ← Web-Sitzung & UI-Logik  
└── ...
```
**Die Vorteile der Hierarchie:**

* **Fachliche Kohäsion:** Alle technischen Adapter, die zu einem Kontext gehören, befinden sich räumlich in derselben Gruppe. Das „Was“ (Domäne) und das „Wie“ (Technik) sind nah beieinander, aber physisch getrennt.  
* **Skalierbarkeit:** Neue technische Anforderungen werden einfach als neues Unterprojekt in der entsprechenden Gruppe hinzugefügt, ohne die globale Struktur zu belasten.  
* **Gezielte Abhängigkeiten:** Das application-Modul kann punktgenau referenzieren, was die Build-Pipeline optimiert und die Kapselung stärkt.

Diese Struktur ist der **ideale Reifegrad** für einen langlebigen Modulith, da sie die Ordnung eines Microservice-Ökosystems bietet, aber die operative Einfachheit eines Monolithen beibehält.

**4.2.5 Context Mapping im Modulith: Die Koordination der Bände**

Im Modulith regelt das Context Mapping, wie die verschiedenen autonomen Module miteinander kommunizieren, ohne die fachliche Stabilität der Gesamterzählung zu gefährden. Damit die Geschichte konsistent bleibt, erfolgt die Kommunikation zwischen den Modulen ausschließlich über die **Interfaces der Ebene 0**.

Der **Composition Root** (im Projekt application/) übernimmt hierbei die Rolle des Regisseurs: Er entscheidet, welcher „Supplier“ (Dienstleister) welchem „Customer“ (Auftraggeber) zur Laufzeit übergeben wird. Für die Realisierung dieser Inter-Modul-Kommunikation existieren verschiedene Ansätze, die je nach Kopplungsgrad unterschiedliche Auswirkungen auf Wartbarkeit und Autonomie haben.

**Vergleichstabelle: Realisierung der Modul-Interaktion**

| Kriterium | Variante A: Direkte Kopplung | Variante B: Adapter im Supplier | Variante C: Adapter im Root |
| :---- | :---- | :---- | :---- |
| **Beschreibung** | Ein Modul (Customer) referenziert direkt das Ziel-Modul (Supplier). | Der Supplier referenziert den Customer, um dessen Interface zu bedienen. | Das Root-Modul referenziert beide und enthält die Adapter-Logik. |
| **Circular Dependency Risiko** | **Hoch**: Gegenseitige Abhängigkeiten blockieren das System. | **Mittel**: Erfordert Disziplin bei der Rollenfestlegung (Customer/Supplier). | **Null**: Da Fachmodule sich nie direkt referenzieren, sind Zyklen unmöglich. |
| **Kognitiver Aufwand** | **Niedrig**: Einfachste Lösung, schnell implementiert. | **Mittel**: Erfordert Verständnis von *Dependency Inversion*. | **Hoch**: Erfordert ein zusätzliches Integrations-Design im Root-Projekt. |
| **Wartungsaufwand** | **Hoch**: Änderungen im Supplier schlagen sofort auf den Customer durch. | **Mittel**: Interface-Änderungen betreffen nur den Adapter im Supplier. | **Niedrig**: Fachmodule sind isoliert; Änderungen werden zentral abgefangen. |
| **Portabilität (Reuse)** | **Schlecht**: Module können nicht unabhängig existieren. | **Gut**: Das Customer-Modul ist autark und wiederverwendbar. | **Exzellent**: Beide Module sind völlig autark und portabel. |

**Variante A: Direkte Kopplung (Der technische Kurze)**

In dieser Variante kennt das booking-Projekt das payment-Projekt direkt. Die Geschichte ist hier technisch verknüpft, was die Flexibilität einschränkt.
```
carrental-booking             ← abhängigk von carrental-payment Projekt  
├── booking/  
│   └── Reservation.cs        ← Ruft IPayment aus dem anderen Projekt auf  
...

carrental-payment             ← Projekt  
└── payment/  
    └── IPayment.cs           ← Interface (Ebene 0)
```
* **Story:** „Ich (Booking) brauche dich (Payment) zum Überleben.“  
* **Problem:** Wenn Payment sich ändert, bricht Booking sofort.

Entspricht einem „Quick & Dirty“-Stil. In einem OO-DDD-System sollte dies vermieden werden, da es das Prinzip der autonomen Bounded Contexts verletzt.

**Variante B: Adapter im Supplier (Der Standardweg)**

Das booking-Modul definiert seinen Bedarf (Interface). Das payment-Modul passt sich an und liefert die Umsetzung.

carrental-booking             ← Projekt (Customer)  
├── booking/  
│   └── Reservation.cs        ← Nutzt IBookingPayment  
└── IBookingPayment.cs        ← Interface (Ebene 0): "Ich brauche eine Zahlung"

carrental-payment             ← Projekt (Supplier)  
├── payment/  
│   └── BookingPayment.cs     ← Implementiert IBookingPayment  
└── [Abhängigkeit auf carrental-booking]

* **Story:** „Ich (Booking) sage, was ich brauche, und du (Payment) erfüllst mir den Wunsch.“  
* **Vorteil:** Das Buchungs-Kapitel ist autark und kann ohne das Finanz-Kapitel existieren.

Dies ist der **Standardweg**. Es ist logisch, dass der Dienstleister sich an die Anforderungen des Auftraggebers anpasst. Diese Variante hält die Integrationslogik nah an der Fachlichkeit.

**Variante C: Der Vermittler im Root (Die Königsdisziplin)**

Beide Fachmodule sind völlig isoliert. Die Brücke wird erst im übergeordneten Root-Projekt geschlagen.
```
carrental-booking             ← Projekt (Insel A)  
├── booking/  
├── IBookingPayment.cs        ← Interface (Ebene 0)  
...

carrental-payment             ← Projekt (Insel B)  
├── payment/  
├── IPayment.cs               ← Eigenes Interface (Ebene 0)  
...

carrental                     ← Root-Projekt (Der Erzähler)  
├── application/              ← Montage & DI  
└── booking/                  ← Das Brücken-Paket integration  
      └── BookingPayment.cs   ← Implementiert IBookingPayment (Insel A)   
...                            und ruft IPayment (Insel B) auf
```
* **Story:** „Zwei Fremde (Booking & Payment) begegnen sich nie, aber der Erzähler (Root) verbindet ihre Schicksale durch einen Boten (Adapter).“  
* **Vorteil:** Absolute Portabilität. Beide Fachmodule wissen nichts voneinander und können in völlig anderen Systemen wiederverwendet werden.

Dies stellt die **Königsdisziplin** dar. Sie ist ideal für Systeme, die maximale Flexibilität fordern oder bei denen Fachmodule von verschiedenen Teams entwickelt werden, die keine gegenseitigen Projektreferenzen erlauben.

**4.2.3 Resümee der Phase 2: Die Reife der modularen Erzählung**

Der Modulith ist der Moment, in dem die Software-Story ihre volle strukturelle Klarheit erreicht. Durch die physische Trennung in autonome Projekte und die bewusste Entscheidung gegen einen *Shared Kernel* haben wir fachliche Inseln geschaffen, die ihre eigenen Geheimnisse (Kapselung) bewahren. Die Einführung von **Context Mapping Varianten (A, B oder C)** erlaubt es uns, die Interaktionen zwischen den Modulen präzise zu steuern, ohne die Reinheit der einzelnen Kapitel zu gefährden.

Ein gut strukturierter Modulith bietet die perfekte Balance: Die fachliche Strenge eines Microservice-Ökosystems kombiniert mit der operativen Leichtigkeit eines Monolithen. Die Geschichte ist nun so weit gereift, dass die Grenzen zwischen den Akteuren nicht nur logisch, sondern physisch stabil sind.

**Der Übergang zur Phase 3: Wenn Akteure über Distanzen kommunizieren**

Wenn die Anforderungen an Skalierbarkeit, Team-Autonomie oder technologische Vielfalt weiter wachsen, steht die **Phase 3: Microservices** bevor. In dieser Phase verlässt die Erzählung den gemeinsamen „Raum“ eines einzelnen Prozesses. Für die Domänenlogik ändert sich fast nichts. Die Interfaces der Ebene 0 bleiben bestehen, doch ihre Implementierung wandelt sich:

* **Vom lokalen Aufruf zum Netzwerk-Call:** Ein Adapter wie der PaypalPayment oder der BookingPayment-Vermittler im Root ruft nun nicht mehr eine lokale Klasse auf, sondern wird zu einem **Netzwerk-Client**.  
* **Technik wandert in die Anhänge:** Die Details über HTTP, gRPC oder Message-Queues werden tiefer in den *Technischen Anhängen* (exchange/) vergraben.  
* **Die Story bleibt identisch:** Die Methode reservation.Confirm(payment) sieht im Code immer noch exakt so aus wie im Monolithen. Nur dass der „Partner“ nun auf einem anderen Server lebt.

**Sind Sie bereit, die Geschichte über die Grenzen eines einzelnen Servers hinaus zu führen?**

## **4.3 Phase 3: Microservices – Die verteilte Erzählung**

Microservices stellen keine zwangsläufige Endstation dar, sondern sind eine bewusste Reaktion auf spezifische Anforderungen an **Skalierbarkeit** und **organisatorische Autonomie**. In dieser Phase verlässt die Erzählung den gemeinsamen Prozessraum. Die stabilen Grenzen, die im Modulith definiert wurden, bilden nun die physischen Mauern eigenständiger Services.

**4.3.1 Kriterien für die physische Verteilung**

Der Schritt zu Microservices bringt Komplexität (Netzwerklatenz, Konsistenzfragen). Er ist erst dann gerechtfertigt, wenn:

* **Organisatorische Freiheit:** Mehrere Teams müssen völlig unabhängig voneinander deployen können, ohne sich im „Schuber“ des Modulithen abzustimmen.  
* **Selektive Skalierung:** Der Schauplatz payment/ benötigt aufgrund hoher Last zehnmal mehr Ressourcen als die customer/-Verwaltung.  
* **Technologische Vielfalt:** Ein spezielles Kapitel (z. B. eine KI-gestützte Preiskalkulation im carpool/) lässt sich in einer anderen Sprache (z. B. Python) besser erzählen als im restlichen C#-System.

**Warnung:** Wer die Phasen 1 und 2 überspringt, baut keinen Microservice, sondern einen „verteilten Monolithen“ – ein technisches Chaos, in dem die Fachlichkeit verloren geht.

### **4.3.2 Die Prjekt-Struktur: Vom Methodenaufruf zum Netzwerk-Client**

Das Faszinierende am OO-DDD-Ansatz: Die **Ebene 0** (Interfaces) bleibt stabil. Nur die Implementierung in den *Technischen Anhängen* (exchange/) ändert sich. Ein lokaler Adapter wird zu einem diplomatischen Funkspruch über das Netzwerk.
```
carrental-gateway-service   ← Der Pförtner (Zentraler Einstiegspunkt)

carrental-booking-client    ← Das Gesicht (BFF - Backend for Frontend)

carrental-carpool-service   ← Service A: Ein eigenständiger Prozess  
carrental-customer-service  ← Service B: Mit eigener Datenbank  
carrental-payment-service   ← Service C: Hochverfügbar skaliert
```
### **4.3.3 Kommunikation über Distanzen: Synchron vs. Asynchron**

Wenn die Geschichte auf verschiedene Server verteilt wird, müssen die Akteure lernen, über das Netzwerk zu kommunizieren. Die Wahl der Kommunikationsart beeinflusst maßgeblich die Stabilität und Reaktionsfähigkeit des Systems:

| Art | Technik | Dramaturgie (Die Story) | Auswirkung im OO-DDD |
| :---- | :---- | :---- | :---- |
| **Synchron (Push)** | **HTTP / REST / gRPC** | Ein direkter Dialog: Der Akteur wartet sofort auf eine Antwort. | **Starke zeitliche Kopplung:** Wenn der Partner nicht antwortet, stockt die eigene Handlung. |
| **Asynchron (Pull)** | **Messaging / Kafka / RabbitMQ** | Ein Briefwechsel: Der Akteur hinterlässt eine Nachricht und arbeitet weiter. | **Hohe Autonomie:** Die Geschichte läuft flüssig weiter (Eventual Consistency), auch wenn Partner zeitweise offline sind. |

**Die Auswirkungen auf die Architektur:**

1. **Vom Adapter zum Proxy:** In der synchronen Welt wird aus einem lokalen Adapter ein **Remote-Proxy**. Die Methode reservation.Confirm(payment) ruft im Hintergrund einen HTTP-Client auf. Für die Domänenlogik fühlt es sich immer noch wie ein lokaler Aufruf an, doch technisch entsteht eine kritische Abhängigkeit zur Verfügbarkeit des Partners.  
2. **Event-Driven Storytelling:** In der asynchronen Welt nutzt die Architektur **Domain Events**. Anstatt den Partner direkt zu rufen („Bezahle jetzt!“), verkündet ein Akteur einen Wendepunkt: „Die Buchung wurde angefragt“ (BookingRequested). Andere Services (wie Payment) „hören“ diese Nachricht (Pull-Prinzip) und reagieren darauf, wenn sie bereit sind.  
3. **Fehlertoleranz:** Während synchrone Kommunikation bei Netzwerkfehlern oft sofort zu „Abstürzen“ in der Benutzeroberfläche führt, erlaubt asynchrones Messaging ein robustes Storytelling: Die Nachricht wird im Postfach (Queue) zwischengespeichert und später verarbeitet.

**Strategische Empfehlung: **Nutzen Sie **synchrone Kommunikation** (HTTP) für Lesezugriffe (Queries), bei denen der Nutzer sofort Daten sehen muss (z.B. „Zeige verfügbare Autos“). Nutzen Sie **asynchrone Kommunikation** (Messaging) für Zustandsänderungen (Commands), um die Autonomie der Microservices zu wahren und die Geschichte auch bei technischen Störungen flüssig zu halten.

### **4.3.4 Das Frontend als Standalone Microservice (BFF-Pattern)**

In modernen Cloud-native Architekturen wird das Frontend nicht lediglich als statisches Asset (eine bloße Sammlung von .js-, .css- und .html-Dateien) behandelt. Es agiert stattdessen als eigenständiger **„Backend-for-Frontend“ (BFF)** Service. Das BFF fungiert als maßgeschneiderte Schnittstelle, die präzise auf die Bedürfnisse eines spezifischen Clients (z. B. Web-Browser, Mobile App oder Smart Device) zugeschnitten ist. Dies ermöglicht eine strikte Trennung von Präsentationslogik und Geschäfts-Interfaces.

Die Struktur des carrental-booking-client folgt dabei der bekannten **Screaming Architecture**, wodurch die fachliche Geschichte auch im Frontend unmittelbar lesbar bleibt:
```
carrental-booking-client/            ← Frontend / BFF Service Projekt  
├── src/  
│   ├── application/                 ← Startpunkt: Montage der Applikation  
│   │   ├── NativeCarrentalApp.js    ← Decorator (Pure JS): Main Entry Point  
│   │   ├── ServedCarrentalApp.js    ← Decorator (Node.js): Server-side Entry Point  
│   │   ...  
│   ├── exchange/                    ← Das Tor zur Außenwelt (ACL)  
│   │   ├── database/                ← Lokale Datenhaltung / Schema-Generierung  
│   │   │   └── UserDb.js            ← Clientseitige Nutzer-DB (z. B. IndexedDB)  
│   │   ├── endpoint/                ← Brücken zu den Business-Services (HTTP)  
│   │   │   ├── CarPoolApi.js        ← Adapter für den Fuhrpark-Service  
│   │   │   ├── CustomerApi.js       ← Adapter für den Kunden-Service  
│   │   │   └── PaymentApi.js        ← Adapter für den Zahlungs-Service  
│   │   ...  
│   ├── control/                     ← Requisiten: Fachliche UI-Elemente  
│   │   ├── InputGroup.js            ← Kompositum aus verschiedenen Inputs  
│   │   ├── TextInput.js             ← Einzelerfassungsfeld  
│   │   ├── TextLabel.js             ← Ausgabefeld  
│   │   ├── Form.js                  ← Abstrakte Basis für Eingabemasken  
│   │   ├── Panel.js                 ← Abstrakte Basis für Seitenkomponenten  
│   │   ├── List.js / Table.js       ← Abstrakte Listen- & Tabellenkonzepte  
│   │   ├── Menu.js                  ← Steuerungselemente  
│   │   └── Navigation.js            ← Routing-Logik (Regie der Seitenübergänge)  
│   │   ...  
│   ├── layout/                      ← Die visuelle Identität (Styles & Assets)  
│   │   ├── icon/                    ← Piktogramme der Applikation  
│   │   ├── image/                   ← Bildmaterial (z. B. background.png)  
│   │   ├── layout.css               ← Gemeinsame Gestaltungsregeln  
│   │   └── Layout.js                ← Helferklasse für die visuelle Montage  
│   │   ...  
│   ├── page/                        ← Die Schauplätze: Fachliche Seitenkomponenten  
│   │   ├── admin-form.js            ← Maske für die Administration  
│   │   ├── car-details.js           ← Detailansicht eines Fahrzeugs  
│   │   ├── carpool-list.js          ← Die Übersicht des Fuhrparks  
│   │   ├── payment-form.js          ← Die fachliche Zahlungsmaske  
│   │   ├── user-profile.js          ← Die Kundenakte im UI  
│   │   ├── main-form.js             ← Der zentrale Einstieg  
│   │   └── main-menu.js             ← Die Hauptnavigation  
│   │   ...  
│   ├── customer/                    ← Domänen-Implementierung (Sub-Domäne)  
│   │   ├── ValidAddress.js          ← Validierungs-Decorator für Adressen  
│   │   ├── ValidCustomer.js         ← Validierungs-Decorator für Kunden  
│   │   └── ...                      ← Weitere Decorators (Logged*, Cached*)  
│   │  
│   ├── Address.js                   ← Abstraktes Domänen-Konzept  
│   ├── Control.js                   ← Basis für UI-Komponenten (Composite Pattern)  
│   ├── Car.js                       ← Abstraktes Domänen-Konzept  
│   ├── CarPool.js                   ← Abstraktes Domänen-Konzept  
│   ├── Customer.js                  ← Abstraktes Domänen-Konzept  
│   ├── CarRentalApp.js              ← Abstraktion des Composition Root  
│   └── UserProfile.js               ← Fachliche Entität (erweitert Person)  
│   ...  
├── test/                            ← Unit- und Integrationstests der UI-Logik  
├── .gitignore / .environment        ← Umgebungsvariablen & Git-Regeln  
├── readme.md                        ← Kurzanleitung zur "Bühne"  
└── package.json                     ← Abhängigkeiten des Frontend-Ökosystems
```
### **4.3.5 Fazit der Evolution: Die Screaming Architecture**

Am Ende dieses Pfades steht eine Architektur, die „schreit“. Egal ob Monolith oder Microservice: Beim Öffnen des Codes sind keine Frameworks oder Schichten sichtbar. Es zeigt sich die Autovermietung.

1. **Keine Übersetzung:** Fachbegriffe fließen direkt in den Code.  
2. **Kein technisches Rauschen:** Infrastruktur ist konsequent in Anhängen isoliert.  
3. **Klare Identität:** Jedes Paket und jede Klasse erzählt einen Teil der Geschichte

# **5. Abschlussbetrachtung: Code als Geschichte**

Da jedes Element der Software die Verständlichkeit beeinflusst, nutzt OO-DDD in Verbindung mit Storytelling alle Design-Ebenen, um dem Leser eine intuitive Navigation im Code zu ermöglichen.

Der entscheidende Vorteil ist die kognitive Entlastung: Entwickler und Fachexperten agieren auf derselben sprachlichen Ebene. Das **„Tell, Don’t Ask“-Prinzip** kapselt Logik direkt in intelligenten Akteuren, statt sie in starren Schichten zu verbergen. Technische Komplexität wird dabei modular als Schutzhülle via Decorator und Adapter um den fachlichen Kern gewickelt. So isoliert OO-DDD die Geschäftslogik von kurzlebigen Frameworks und schafft eine Struktur, die ihre eigene Geschichte erzählt.

Die wahre Kunst des Storytellings im Code liegt darin, die Software wie ein Buch zu gestalten: Das Inhaltsverzeichnis (Ebene 0) setzt den Rahmen, während die Kapitel (Sub-Packages) Details vertiefen, ohne die Handlung durch technisches Rauschen zu unterbrechen. Damit die Erzählung widerspruchsfrei bleibt, folgen Struktur und Benennung konsequent dem Narrativ der Nutzer und Fachexperten.

Diese Prinzipien verwandeln den Code von einer technischen Anleitung in ein lebendiges Dokument der Fachdomäne und minimiren damit pragmatisch die Übersetzungslücke zwischen IT und Business.

# **6. Referenzen und weiterführende Literatur**

Die in diesem Leitfaden vorgestellten Prinzipien basieren auf bewährten Mustern der Software-Architektur und radikalen objektorientierten Ansätzen. Zur Vertiefung der Konzepte – insbesondere der **Tell, Don’t Ask**, des **Composition Roots** und der **fachlichen Paketierung** – wird folgende Literatur empfohlen:

**Architektur und Packaging**

* **Java Dev Guy (2017):** *Happy-Packaging* – Grundstein für die hierarchische, fachzentrierte Paketstruktur.  
* **Philipp Hauer (2020):** *Package by Feature* – Plädoyer für die Gruppierung nach Fachlichkeit statt nach technischen Layern.  
* **Mark Seemann (2011/2012):** *Composition Root Pattern* & *Poor Man's DI Pattern* – Die Standardwerke zur sauberen Initialisierung von Objektgraphen ohne Framework-Kopplung.

**Objektorientierung und Naming (Die Grammatik)**

* **Yegor Bugayenko:** *Vertical and Horizontal Decorating* – Die Basis für den hier vorgestellten Decorator-Ansatz zur Kapselung technischer Aspekte.  
* **Mihai A. Rode (2018):** *My Take On Object Naming* – Inspiration für eine präzise, domänenorientierte Benennung.  
* **Yegor Bugayenko:** *Seven Virtues of a Good Object* – Manifest für autonome, intelligente Objekte („Smart Objects“).  
* **Yegor Bugayenko:** *Naming Guidelines* – Strenge Regeln zur Vermeidung technischer Suffixe und Verben in Klassennamen:  
  * *Prefixed Naming* (Kontext vor Identität)  
  * *Don't Create Objects That End With -ER* (Vermeidung von prozeduralen „Managern“)  
  * *A Compound Name Is a Code Smell* (Fokus auf Single Responsibility)  
  * *Evil Suffixes for Object Names* (Eliminierung von technischem Rauschen wie -DTO oder -Impl)
