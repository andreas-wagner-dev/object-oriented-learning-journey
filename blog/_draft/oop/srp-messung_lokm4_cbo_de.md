# The Pragmatic Single Responsibility Principle: Beyond Myths to Metrics

Das [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) (SRP) ist das erste der fünf [SOLID-Prinzipien](https://en.wikipedia.org/wiki/Package_principles), das von Robert C. Martin zu Beginn der 2000er-Jahre etabliert wurde. Trotz seiner scheinbaren Einfachheit gehört es in der Praxis zu den am schwierigsten umzusetzenden Entwurfsprinzipien der objektorientierten Programmierung.

Der Zweck des Prinzips ist es, Software modular, wartbar und verständlich zu halten. Eine Klasse, die einen eindeutigen Verantwortungsbereich besitzt, ist leichter zu testen, einfacher zu ändern und klarer in ihrer Absicht. SRP ist damit nicht nur ein Designprinzip, es ist ein Qualitätsmerkmal, das maßgeblich die langfristige Wartbarkeit beeinflusst und die Ansammlung technischer Schulden minimiert.

## 1. Gegenstand der Betrachtung und Zielsetzung

Im Mittelpunkt dieser Betrachtung steht das Single Responsibility Principle sowie die Herausforderung dessen Einhaltung auf Klassenebene objektiv zu bewerten. Durch eine objektive Untersuchung des SRP soll ein tieferes Verständnis für dessen Anwendung im Entwickleralltag vermittelt werden. Der hier gewählte Ansatz stützt sich auf die Formalisierung von Robert Bräutigam, welcher das SRP über die Konjunktion (logisches UND) zweier messbarer Softwaremetriken definiert. Die daraus resultierende Formel lautet SRP ≡ max(COHESION) ∧ min(COUPLING).

Die Operationalisierung der Formel erfolgt dabei über zwei zentrale Kennzahlen. Die Kohäsion wird mittels *Lack of Cohesion of Methods Version 4* (LCOM4) über eine Graphenanalyse ermittelt, wobei der ideale Zielwert bei 1 liegt. Parallel dazu wird die Kopplung mithilfe von *Coupling Between Objects* (CBO) durch das Zählen externer Abhängigkeiten bestimmt mit dem Ziel eines minimalen Wertes. Beide Kennzahlen werden zunächst anhand von Beispielklassen explizit hergeleitet und in einer abschließenden Gegenüberstellung evaluiert. Als Vergleichsobjekte dienen zwei verbreitete Entwurfsansätze, wie das moderne Service Muster nach Domain Driven Design (DDD) und das Decorator Muster nach Object Oriented Design (OOD). Diese Konzepte werden zur Analyse innerhalb einer Domäne zur Bestellverwaltung in Java implementiert.

## 2. Die Problematik von SRP

Die Problematik des SRP liegt in seiner subjektiven Formulierung, die in der Praxis schwer greifbar bleibt und somit keine konkrete Bewertungs- oder Handlungsgrundlage für den Klassenentwurf bietet. Eine gängige Definition besagt, dass eine Klasse nur einen einzigen Grund für Änderungen haben sollte („only one reason to change").  Was jedoch konkret als Änderungsgrund gilt, offenbart sich meist erst im Moment der tatsächlichen Anpassung. Eine vorausschauende Trennung beruht daher oft auf reiner Spekulation, da theoretisch jede minimale Anforderungsänderung als neuer, eigenständiger Grund interpretiert werden könnte.

Aufgrund der Unklarheiten rund um den Begriff des ‚Änderungsgrundes‘ präzisierte Robert C. Martin seine Ausführungen später, woraus sich im Laufe der Zeit verschiedene Definitionsebenen entwickelten. Diese reichen von der Forderung nach genau einer Aufgabe pro Modul über die Identifikation eines spezifischen Änderungsgrundes bis hin zur Empfehlung, Dinge mit identischen Änderungsursachen zusammenzufassen. In der konkreten Auslegung wird ein solcher Grund schließlich als Anforderung definiert, die von einem einzelnen Akteur oder Geschäftsmann stammt.

> "Each software module should have one and only one responsibility"

> "Each software module should have one and only one reason to change"

> "Gather together the things that change for the same reasons. Separate those things that change for different reasons"

> "Same reason' means it originates from the same business person"  

In der praktischen Anwendung werfen diese Definitionen jedoch neue Fragen auf:

* So ist beispielsweise unklar, ob eine Klasse `Order`, eine Bestellung sowohl validiert als auch persistiert, bereits gegen die Beschränkung auf *eine einzelne Aufgabe* verstößt.
* Auch die praktische Faustregel, *Zusammengehöriges basierend auf künftigen Änderungsgründen zu bündeln*, ist zum Zeitpunkt der Implementierung kaum prüfbar, da sie eine Vorausplanung der künftigen Produktentwicklung erfordert.
* Ebenso erweist sich der Verweis auf den *Geschäftsmann* zwar für die strategische Domänenmodellierung als hilfreich, bleibt jedoch als konkretes Programmierkriterium für den Code-Alltag unbrauchbar.

Diese kontextabhängigen Interpretationen führen in Code-Reviews häufig zu zeitraubenden Grundsatzdebatten. Ohne objektive Bewertungskriterien drohen solche Diskussionen in subjektive Meinungsverschiedenheiten auszuarten, was die Effizienz und Konsistenz der Softwareentwicklung verhindert.

## 3. Die Formalisierung von Kohäsion und Kopplung

Die eher philosophisch und soziologisch geprägten Definitionen des SRP bieten aufgrund ihrer Subjektivität kaum eine klare Handlungsgrundlage für die Softwareentwicklung. Begriffe wie Verantwortung, Änderungsgrund oder Akteursorientierung erweisen sich für die praktische Umsetzung als zu vage und können in der Konsequenz häufig zu einer unnötigen Codefragmentierung führen. Robert Bräutigam schlägt stattdessen eine pragmatische Definition vor:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

Durch die formale Gleichsetzung des SRP mit der Konjunktion aus maximaler [Kohäsion](https://en.wikipedia.org/wiki/Cohesion_(computer_science)) und minimaler [Kopplung](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) wandelt sich das Prinzip von einer rein abstrakten Designphilosophie zu einer präzisen Strukturmetrik. Diese mathematische Definition ermöglicht es, die ehemals vagen Verantwortlichkeiten einer Klasse durch messbare strukturelle Eigenschaften objektiv zu bewerten.

Die interne Qualität einer Klasse bemisst sich an ihrer Kohäsion, also dem Grad, in dem Methoden und Felder eine funktionale Einheit bilden und somit das Versprechen einlösen, nur eine Sache zu tun.

Die externe Qualität wird hingegen durch die Kopplung bestimmt, welche die Abhängigkeiten zu fremden Objekten beschreibt. Je geringer diese externe Vernetzung ausfällt, desto isolierter und wartungsfreundlicher bleibt die Klasse gegenüber globalen Änderungen im System.

Innerhalb der Kopplung wird zwischen physikalischen und semantischen Abhängigkeiten unterschieden. Während sich physikalische Verbindungen durch Feldtypen statisch nachweisen lassen, verbirgt sich die semantische Kopplung hinter einem impliziten Wissen über fremde Objektstrukturen. Da diese Abhängigkeiten für den Compiler nicht greifbar sind, führen sie oft zu schwer nachvollziehbaren Fehlfortpflanzungen bei Code-Änderungen. Sobald eine Klasse beispielsweise über Ketten wie `user.getAddress().getCity()` auf tieferliegende Daten zugreift, entsteht eine strukturelle Abhängigkeit, die über die reine Typkenntnis hinausgeht. Folglich fungiert jede Read-Methode als möglicher Kanal für eine erhöhte semantische Kopplung.

Um die semantischen Abhängigkeiten zu minimieren, dient das [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) als zentrale Entwurfsrichtlinie, nach der ein Objekt nur mit seinen unmittelbaren Nachbarn kommunizieren darf. Ergänzt wird dies durch das Prinzip [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html), welches dazu auffordert, Objekten Befehle zu erteilen, statt deren internen Zustand abzufragen, um darauf basierend Entscheidungen zu treffen. Die konsequente Anwendung beider Prinzipien fungiert somit als effektiver Schutz gegen semantische Instabilität, indem sie die Kapselung wahrt und die Verantwortlichkeiten klar voneinander isoliert.

## 4. Messverfahren für Kohäsion (LCOM4) und Kopplung (CBO)

### 4.1 Lack of Cohesion of Methods

Eine der bekanntesten Metriken zur Messung der inneren Klassenstruktur ist die Lack of Cohesion of Methods (LCOM). Da in der Literatur verschiedene Versionen dieser Kohäsionsmetrik existieren, wird in diesem Beitrag gezielt die vierte Iteration (LCOM4) nach Hitz und Montazeri (1995) verwendet. Diese zeichnet sich durch eine hohe praktische Relevanz aus, da sie aufgrund ihrer einfachen Anwendung und klaren Interpretierbarkeit eine objektive Bewertung der SRP-Konformität ermöglicht. Die Metrik ermittelt die Anzahl verbundener Elementgruppen innerhalb einer Klasse, wobei die Kohäsion mittels einer Graphenanalyse veranschaulicht wird. Ein zusammenhängender Teilgraph entsteht dabei immer dann, wenn Methoden entweder auf dieselben Instanzvariablen zugreifen oder sich durch gegenseitige Aufrufe direkt beeinflussen.

Die konkreten Aussagen zur Bewertung der Klassenstruktur liefern die folgenden Kennzahlen: 

* **LCOM4-Wert von 1** bedeutet, dass sämtliche Methoden und Felder direkt oder indirekt miteinander verbunden sind, was einer hochgradig kohäsiven Klasse im Idealzustand entspricht.
* **LCOM4-Wert über 1**, zerfällt die Klasse in mehrere unabhängige Teilgraphen, was als objektives Indiz für eine Verletzung des Single Responsibility Principles gewertet wird.
* **LCOM4-Wert von 0** kennzeichnet hingegen eine Klasse ohne Methoden, wie etwa einen reinen Datencontainer, was im Rahmen dieser Analyse neutral bewertet wird.

**Fallbeispiel 1: Niedrige Kohäsion (LCOM4 = 2)**

In diesem Szenario enthält die Klasse `Order` zwei logische Zuständigkeiten für die Anzeige `display()` einer Bestellung und Abwicklung `pay(int many)` einer Zahlung.


```java
// Die Klasse zerfällt in zwei isolierte Teilgraphen (LCOM4 = 2)
public class Order {

    private Cart cart;                   // Feld 1
    private Customer customer;           // Feld 2
    private String status = "PANDING";   // Feld 3
    private int amount;                  // Feld 4

    // M1: Anzeige-Logik (nutzt Feld 1 und Feld 2)
    public String display() {
        return customer.getName() + ": " + cart.itemCount() + " items";
    }

    // M2: Zahlungs-Logik (nutzt Feld 3 und Feld 4)
    public void pay(int many) {
        this.amount = many;
        this.status = "PAID";
    }
}
```

Die Graphenanalyse zeigt, dass zwischen diesen beiden Gruppen keine Verbindung existiert, da weder ein gemeinsames Feld genutzt noch ein gegenseitiger Methodenaufruf getätigt wird. 

[Bild]

Die Klasse besitzt zwei unabhängige Verantwortlichkeiten, was sich in zwei isolierten Teilgraphen und einem LCOM4-Wert von 2 widerspiegelt.


**Fallbeispiel 2: Kohäsion durch Zusammenhang (LCOM4 = 1)**

In diesem Szenario fungiert das Feld status (Feld 3) als Knotenpunkt, da es nun von beiden Methoden verwendet wird. 
Rein strukturell erhöht sich die Kohäsion, wodurch der LCOM4-Wert auf 1 sinkt. 

```java
public class Order {

    private Cart cart;                   // Feld 1
    private Customer customer;           // Feld 2
    private String status = "PANDING";   // Feld 3
    private int amount;                  // Feld 4

    public String display() {
        // Nutzt Feld 1, 2 und 3
        return customer.getName() + ": " + cart.itemCount() + ", Status: " + status;
    }

    public void pay(int many) {
        // Nutzt Feld 3 und 4
        this.amount = many;
        this.status = "PAID";
    }
}
```

Die Graphenanalyse der Klasse `Order` verdeutlicht das folgende Bild.

[Bild]

Da beide Methoden auf das Feld `status` zugreifen, sind die ursprünglich isolierten Logikbereiche über diese Instanzvariable miteinander verbunden. Im Sinne der Graphentheorie entsteht ein einziger zusammenhängender Graph, da ein Pfad von M1 über Feld 3 zu M2 existiert. Das Ergebnis ist ein LCOM4-Wert von 1.


**Fallbeispiel 3: Ideale Kohäsion durch Aufteilung (LCOM4 = 1)**

Um eine echte fachliche Kohäsion zu erreichen, wird die Klasse gemäß ihrer Verantwortlichkeiten in zwei spezialisierte Einheiten dekonstruiert.

```java
// Fokus auf Inhalt/Anzeige
public class OrderView {

    private Cart cart;                 // Feld 1
    private Customer customer;         // Feld 2

    public String display() {
        return customer.getName() + ": " + cart.itemCount();
    }
}

// Fokus auf Zahlung/Transaktion
public class OrderPayment {

    private String status = "PANDING";   // Feld 3
    private int amount;                  // Feld 4

    public void pay(int many) {
        this.amount = many;
        this.status = "PAID";
    }
}

```


Durch die Aufteilung entstehen zwei unabhängige Graphen, die jeweils eine in sich geschlossene funktionale Einheit bilden. 

[Bild]

Innerhalb der Klasse `OrderView` erzeugt die Methode `display()` eine direkte Verbindung zwischen dem Kundenobjekt und dem Warenkorb. In der Klasse `OrderPayment` modifiziert die Methode `pay()` beide Instanzvariablen gleichzeitig, was eine starke interne Bindung bewirkt. In beiden Fällen ergibt die Analyse einen einzelnen, vollständig zusammenhängenden Teilgraph mit einem LCOM4-Wert von 1, was die fachliche Isolation bestätigt.

**Zwischenfazit zur Kohäsionsanalyse (LCOM4)**

Wie das zweite Fallbeispiel verdeutlicht, kann ein idealer Metrik-Wert künstlich durch technische Querschnittsbelange (wie etwa eine ID, ein Status-Feld oder UI-spezifische Daten) erzeugt werden, ohne die zugrunde liegende Vermischung von Verantwortlichkeiten tatsächlich zu lösen. Aus der Sicht eines datenzentrierten Entwurfs mag die strikte Trennung von Zahlungs- und Präsentationslogik, wie in Fallbeispiel 3 gezeigt, sinnvoll erscheinen. Dabei wird jedoch die Kapselung aufgebrochen, was den Prinzipien einer verhaltensorientierten Objektorientierung widerspricht. Aus dieser Perspektive ist die Einbettung einer `display()`-Methode in das Order-Objekt keine künstliche Verbindung, sondern Ausdruck echter Kapselung. Da die Darstellung einer Bestellung untrennbar mit ihrem fachlichen Zustand (z. B. dem `status`) verknüpft ist, gehört dieses Wissen zum Kern der Entität selbst. Ein Aufbrechen dieser Einheit würde den Einsatz von *Gettern*-Methoden erzwingen, was die semantische Kopplung erhöht und das Prinzip *Tell, Don’t Ask* verletzt.

Diese Fallbeispiele verdeutlichen eine zentrale Erkenntnis für die Praxis: Ein LCOM4-Wert von 1 ist eine notwendige, aber keine hinreichende Bedingung für die SRP-Konformität. Die Kennzahl bestätigt lediglich die strukturelle Verbundenheit, ersetzt jedoch nicht die qualitative Prüfung, ob die verknüpften Elemente tatsächlich eine fachliche Einheit bilden.

Als Handlungsempfehlung lässt sich ableiten: Existiert kein fachlicher Zusammenhang zwischen den Methoden einer Klasse, ist eine Aufteilung geboten. Eine künstliche Verbindung durch rein technische Infrastrukturbelange sollte vermieden werden, um die Transparenz über die tatsächliche Kohäsion nicht zu verzerren.

### 4.2 Coupling Between Objects

Ergänzend zur Kohäsion misst die Metrik **Coupling Between Objects (CBO)** nach Chidamber & Kemerer (1994) die Anzahl der externen Typen, zu denen eine Klasse eine direkte Abhängigkeit unterhält. Diese Kopplung manifestiert sich durch Klassenerweiterungen, Feldtypen, Methodenaufrufe sowie durch Argumente und Rückgabetypen innerhalb der Methodensignatur oder lokaler Variablen.

Primitive Datentypen und Standard-Wrapper wie int oder String bleiben hierbei unberücksichtigt, da sie zur Basisinfrastruktur der Programmiersprache gehören und keine Kopplung im Sinne des spezifischen objektorientierten Entwurfs darstellen.

Die Bewertung der Messergebnisse folgt einer klaren Skala:

* **CBO-Wert von 0** deutet darauf hin, dass eine Klasse isoliert ist und somit faktisch nicht am System teilnimmt.
* **CBO-Werte zwischen 1 und 4** gelten als Idealbereich, da er eine lose Kopplung signalisiert und die Wartbarkeit unterstützt.
* **CBO-Wert größer als 5**, deutet auf eine zu enge Verflechtung mit anderen Klassen hin.

Grundsätzlich ist eine Kopplung zwischen Klassen für die Funktionsfähigkeit eines Systems zwar unumgänglich, doch erschwert ein übermäßiges Maß an Abhängigkeiten das Testen sowie die Modifikation und schränkt die Wiederverwendbarkeit der Komponenten erheblich ein.

Um diese Abhängigkeiten auf ein gesundes Maß zu reduzieren, bietet das [Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle) eine zentrale Lösung, indem es High-Level-Module von konkreten Implementierungen entkoppelt und stattdessen die Abhängigkeit von stabilen Abstraktionen erzwingt. Analog unterstützt das Prinzip [Encapsulate what varies](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)) die Entkopplung. Dabei sollte, was sich häufig ändern oder unterschiedlich sein kann, isoliert und hinter einer stabilen Schnittstelle verborgen werden. Ergänzend dazu trägt das [Interface Segregation Principle](https://en.wikipedia.org/wiki/Interface_segregation_principle) zur Kopplungsminimierung bei, indem es Klassen nur gegen spezifisch benötigte Teil-Schnittstellen binden lässt. Zudem wirkt das **Law of Demeter** als strukturelle Schranke gegen semantische Kopplung, da es den Zugriffspfad auf unmittelbare Nachbarobjekte beschränkt.  

 
**Fallbeispiel 1: Direkte Abhängigkeit**

Im ersten Szenario ist der `Report` direkt von konkreten Implementierungen abhängig. Jede Änderung an den beteiligten Klassen kann sich unmittelbar auf die Klasse auswirken.


```java
// CBO = 5 - Hohe Kopplung an konkrete Typen
public class Report {

    private ReportRepository repository; // Class (CBO +1)
    private PdfExporter pdfExporter;     // Class (CBO +1)

    public ReportData generate(DataQuery q) { // DataQuery (CBO +1), ReportData (CBO +1)
        List<DataRow> rows = repository.fetch(q); // DataRow (CBO +1) als Rückgabetyp von fetch
        return pdfExporter.export(rows);
    }
}
// Gezählte Typen: ReportRepository, PdfExporter, DataQuery, ReportData, DataRow
```
Der `Report` muss den Typ `DataRow` hier zwingend „kennen“, um das Ergebnis von `repository.fetch(q)` verarbeiten oder zurückgeben zu können. In der CBO-Metrik zählen Typen in Methodensignaturen (Parameter und Rückgabetypen) stets als Kopplung.

**Fallbeispiel 2: Dependency Inversion**

Um den CBO-Wert zu reduzieren, wird das *Dependency Inversion Principle* angewandt. Anstatt auf konkrete Implementierungen zu verweisen, bindet sich der `Report` an zwei stabile Schnittstellen `Repository` und `Exporter`.

```java
// Kopplung an stabile Schnittstellen (CBO = 4)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public Report generate(Query q) { // Query (CBO +1), ReportData (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        return exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, Report, Query
```

**Fallbeispiel 3: Signatur-Optimierung**

Sofern auf einen spezifischen Rückgabetyp (`void` statt `Report`) verzichtet werden kann, lässt sich die Kopplung weiter senken. Ein entscheidender Faktor ist hierbei die Unterscheidung zwischen Signatur-Kopplung und lokaler Kopplung.

```java
// Kopplung an Schnittstellen ohne Rückgabetyp (CBO = 3)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generateReport(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, Query
```

Der Typ `DataRow` taucht hier nur noch als lokaler „Durchlaufwert“ auf. Da er weder Teil der Felder noch der Methodensignatur ist, wird er in der Metrik nicht als direkte Kopplung gewertet. Die Klasse `Report` reicht das `DataRow` Objekt lediglich zwischen `Repository` und `Exporter` weiter, ohne eine funktionale Abhängigkeit zur internen Struktur von `DataRow` zu besitzen (Pass-Through-Effekt).

**Fallbeispiel 4: Semantische Kopplung**

Der CBO-Wert erhöht sich wieder auf 4, sobald eine explizite Abhängigkeit zu `DataRow` entsteht. Dies ist der Fall, wenn der `Report` aktiv Methoden des Typs aufruft (z. B. eine Validierung via `rows.get(0).validate()`).

```java
// Kopplung an Semantik (CBO = 4)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generateReport(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        rows.get(0).validate(); // Aktiver Aufruf an DataRow (CBO +1)
        exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, Query, DataRow
```

Sobald die Klasse Methoden wie `validate()` aufruft, entsteht eine semantische Kopplung. Die Klasse `Report` benötigt nun „Wissen“ über das interne Verhalten und die Geschäftsregeln von `DataRow` (Verletzung des Law of Demeter).  Die Klasse verlässt damit ihre Rolle als reiner Koordinator und spricht mit einem „Fremden“, den sie eigentlich nur durchreichen sollte. Dadurch ist der Klasse nicht mehr nur technisch gekoppelt (Kenntnis des Typs), sondern auch logisch (Kenntnis des Prozesses), was die Wartbarkeit erschwert.

### 4.3 Die Synergie von LCOM4 und CBO

Wie die vorangegangenen Fallbeispiele zeigen, kann ein LCOM4-Wert von 1 trügerisch sein. Sobald eine Klasse technisch notwendige Querschnittsfelder wie eine id, ein status-Feld oder einen Logger nutzt, werden im Graphen Brücken zwischen eigentlich fremden fachlichen Verantwortlichkeiten geschlagen. Die strukturelle Analyse wertet dies als Kohäsion, obwohl das Single Responsibility Principle faktisch verletzt bleibt.

An dieser Stelle entfaltet die Kombination mit der CBO-Metrik ihre volle Diagnosekraft. Während der LCOM4 in Fallbeispiel 2 eine ideale interne Bindung suggeriert, würde eine Messung der Kopplung (CBO) bei einem „Fat Service“ sofort Alarm schlagen. Ein hoher CBO-Wert offenbart, dass die Klasse trotz ihrer internen Verknüpfung über ein Status-Feld eine übermäßige Anzahl externer Abhängigkeiten bedienen muss. Ein Entwurf ist erst dann wirklich SRP-konform, wenn er beide Kriterien gleichzeitig erfüllt:

* **LCOM4 = 1**, wobei die Verbindung auf **fachlicher Logik** basiert und nicht auf rein technischer Infrastruktur.
* **CBO ≤ 5**, was sicherstellt, dass die Klasse nicht zu viele externe „Wissensbereiche“ in sich vereint.

Erst in der Gesamtbetrachtung beider Kennzahlen lässt sich objektiv feststellen, ob eine Klasse eine echte fachliche Einheit bildet oder lediglich eine Ansammlung lose gekoppelter Aufgaben darstellt, die durch technische Hilfsvariablen zusammengehalten werden. Ein „sauberes“ Design nach der Formalisierung von Robert Bräutigam strebt demnach eine Klasse an, die durch maximale Kohäsion bei minimaler Kopplung besticht, was sich in der Zielmarke eines **LCOM4-Werts von 1** und eines **CBO-Bereichs von 0 bis 5** widerspiegelt.

## 5. Beispiele: DDD-Service vs. OOD-Decorator

Um die praktische Anwendung von LCOM4 und CBO zu demonstrieren, wird im Folgenden dieselbe Domäne mit unterschiedlichen Entwurfsansätzen untersucht. Ziel ist es, die Unterschiede in der SRP-Konformität objektiv messbar zu machen.

Als Szenario dient der Bestellvorgang eines Online-Shops mit drei Kernoperationen, wie Anlegen, Bezahlen und Stornieren.

* **Anlegen:** Reserviert Artikel im Lager (InventoryApi), speichert die Bestellung (OrderRepository), versendet eine Reservierung (Email) und protokolliert den Vorgang (Audit).
* **Bezahlen:** Zieht den Betrag ein (PaymentApi), markiert die Bestellung als bezahlt, versendet eine Bestätigung (Email) und protokolliert den Vorgang (Audit).
* **Stornieren:** Gibt Artikel im Lager frei (InventoryApi), markiert die Bestellung als storniert, versendet eine Nachricht (Email) und protokolliert den Vorgang (Audit).

Daraus ergeben sich sieben beteiligte Komponenten, deren Verantwortlichkeiten in den folgenden Implementierungen unterschiedlich verteilt werden: `Cart`, `Customer`, `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email` und `Audit`.

### 5.1 Service-Pattern (DDD)

Im klassischen Service-Pattern werden alle fachlich zusammengehörigen Operationen in einer zentralen Klasse gebündelt.

```java
public class OrderService {

    private OrderRepository orderRepository;   // Feld 1
    private InventoryApi inventoryApi;         // Feld 2
    private PaymentApi paymentApi;             // Feld 3
    private Email email;                       // Feld 4
    private Audit audit;                       // Feld 5

    public Order createOrder(Cart cart, Customer customer) { // Feld 6, 7, 8
        inventoryApi.reserve(cart);
        Order order = new Order(cart, customer);
        orderRepository.save(order);
        email.sendReservation(order);
        audit.log("Order created: " + order.getId());
        return order;
    }

    public void processPayment(Order order) {
        paymentApi.charge(order);
        order.markAsPaid();
        orderRepository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }

    public void cancelOrder(Order order) {
        inventoryApi.release(order);
        order.cancel();
        orderRepository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}

```
Verwendung:

```java
OrderService service = new OrderService(
    orderRepository,
    inventoryApi,
    paymentApi,
    email,
    audit);

// Bestellung anlegen kennt 5 Abhängigkeiten
Order order = service.createOrder(cart, customer);

// Bezahlen kennt 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.processPayment(order);

// Stornieren — service kennt 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.cancelOrder(order);
```

Eine Untersuchung nach LCOM4 zeigt, dass die Methode `createOrder` auf die Felder 1, 2, 4 und 5 zugreift, während `processPayment` die Felder 1, 3, 4 und 5 sowie `cancelOrder` die Felder 1, 2, 4 und 5 beansprucht. Folglich weist die Klasse einen **LCOM4-Wert von 1** auf, was auf eine hohe Kohäsion hindeutet, da alle Methoden über das `Repository` und das `Audit`-Logging miteinander verknüpft sind.

Die Ermittlung der Metriken ergibt für den **CBO einen Wert von 8**, was nach der strikten Definition von Chidamber & Kemerer (1994) einer sehr hohen Kopplung entspricht. Hierbei werden sämtliche externen Typen gezählt, die entweder als Felder wie `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email` und `Audit` oder als Parameter und Rückgabetypen wie `Order`, `Cart` und `Customer` auftreten.

In der Interpretation liefert der `OrderService` damit ein vermeintlich ideales LCOM4-Ergebnis. Bei genauerer Betrachtung entlarvt sich diese Kohäsion jedoch als künstlich erzwungen durch technische Querschnittsbelange wie Persistenz und Logging. Die fachlichen Kernaufgaben der Lagerverwaltung, Zahlung und des E-Mail-Versands sind eigentlich disjunkt, werden aber lediglich durch die gemeinsame Nutzung der Infrastruktur-Komponenten im Graphen zusammengehalten.

Die eigentliche Problematik verdeutlicht der kritische **CBO-Wert von 8**, welcher weit über dem empfohlenen **Schwellenwert von 5** liegt und die Klasse objektiv als „Fat Service“ identifiziert. Da jede Methode die gesamte Last der Abhängigkeiten mit sich zieht, müssen für einen Unit-Test der Methode `createOrder` sämtliche API-Mocks bereitgestellt werden, obwohl ein Großteil davon funktional unbeteiligt bleibt.

Das SRP ist hier verletzt, da die Klasse mehrere fachlich unabhängige Änderungsgründe wie Logikänderungen bei der Zahlung, im Lager oder beim E-Mail-Versand in sich vereint. Diese Analyse zeigt deutlich, dass LCOM4 allein zur Diagnose dieser Problematik nicht ausreicht und die strukturellen Defizite erst durch den CBO-Wert entlarvt werden.

### 5.2 Service-Pattern (DDD) – Aufgespalten

Ein naheliegender Refactoring-Schritt besteht darin, den ursprünglichen „Fat Service“ in drei spezialisierte Klassen aufzuteilen, die jeweils eine spezifische Operation abbilden.

```java
// Verantwortlichkeit: Bestellung anlegen
public class OrderService {

    private OrderRepository repository; // Feld 1
    private InventoryApi inventory;     // Feld 2
    private Audit audit;                // Feld 3

    public Order create(Cart cart, Customer customer) {
        inventory.reserve(cart);
        Order order = new Order(cart, customer);
        repository.save(order);
        audit.log("Order created: " + order.getId());
        return order;
    }
}

// Verantwortlichkeit: Zahlung abwickeln
public class OrderPaymentService {

    private OrderRepository repository; // Feld 1
    private PaymentApi payment;         // Feld 2
    private Email email;                // Feld 3
    private Audit audit;                // Feld 4

    public void process(Order order) {
        payment.charge(order);
        order.markAsPaid();
        repository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }
}

// Verantwortlichkeit: Bestellung stornieren
public class OrderCancelService {

    private OrderRepository repository; // Feld 1
    private InventoryApi inventory;     // Feld 2
    private Email email;                // Feld 3
    private Audit audit;                // Feld 4

    public void cancel(Order order) {
        inventory.release(order);
        order.cancel();
        repository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}

```
In der praktischen Verwendung werden nun der `OrderService`, der `OrderPaymentService` und der `OrderCancelService` als eigenständige Komponenten instanziiert.

```java
OrderService createSvc = new OrderService(orderRepository, inventoryApi, audit);
OrderPaymentService paymentSvc = new OrderPaymentService(orderRepository, paymentApi, email, audit);
OrderCancelService cancelSvc = new OrderCancelService(orderRepository, inventoryApi, email, audit);

Order order = createSvc.create(cart, customer);
paymentSvc.process(order);
cancelSvc.cancel(order);
```
Durch diese Dekonstruktion sinkt der CBO-Wert pro Klasse spürbar, da jede Einheit nur noch die Abhängigkeiten erhält, die sie für ihre dezidierte Aufgabe zwingend benötigt.

Die LCOM4-Herleitung ergibt für alle drei Klassen per Definition einen Wert von 1. Dies ist jedoch weniger ein Zeichen für echte fachliche Kohäsion als vielmehr eine strukturelle Trivialität, da eine Klasse mit nur einer einzigen Methode mathematisch nicht in mehrere Graphen zerfallen kann.

Die Analyse der Metriken zeigt eine deutliche Verbesserung der Kopplungswerte. Während der `OrderService` mit den Feldern für `Repository`, `Inventory` und `Audit` einen **CBO-Wert von 6** aufweist (Typen: Service-Felder plus `Order`, `Cart`, `Customer`), erreichen der Zahlungs- und der `OrderCancelService` jeweils einen **CBO-Wert von 6** (Typen: Service-Felder plus `Order`, `Email`). Obwohl die Klassen dadurch kleiner und im Unit-Test leichter zu handhaben sind, bleibt ein grundlegendes Problem bestehen.

In der Interpretation wird deutlich, dass technische Querschnittsbelange wie das Logging via `Audit` oder die Persistenz über das `OrderRepository` weiterhin über alle drei Klassen verteilt sind. Eine Änderung der Logging-Strategie oder eine Anpassung des `Repository`-Interface erfordert somit nach wie vor Modifikationen an drei verschiedenen Stellen im System. Die Verantwortlichkeiten sind zwar physisch auf separate Dateien verteilt, bleiben jedoch logisch in jede einzelne Operation „hineingeflochten“.

Das SRP wird hier zwar besser adressiert, ist aber nicht vollständig erfüllt, da die Klassen trotz ihrer trivialen Kohäsion unter einer hohen konzeptionellen Redundanz leiden.

## 5.3 Vertikales Decorator-Pattern (OOD)

Um die verbleibende Redundanz der Querschnittsbelange aufzulösen, bietet sich das Decorator-Pattern an. Hierbei wird die Kernlogik in einer Basisklasse isoliert, während fachliche Funktionserweiterungen und technische Aspekte wie Logging oder Persistenz in separate Hüllen ausgelagert werden. Das Pattern separiert Verantwortlichkeiten konsequent über Objektkomposition, sodass jede Klasse genau eine Aufgabe übernimmt. Die Querschnittsbelange entstehen hier durch das Umhüllen von Objekten und nicht durch das Anhäufen von Feldern innerhalb einer Klasse.

Das Basis-Interface wird dabei schlicht als Order definiert, was einen rein fachlichen Begriff ohne das technische Suffix „Service“ darstellt. 

```java
// Basis Interface
public interface Order {
    String id();
    void process();
    void cancel();
}
```

Im neuen Entwurf entfällt die `PaymentApi` als Methodenparameter, da sich `process()` zu einer reinen Verhaltensaufforderung an das Objekt wandelt. Die Abhängigkeit zur Zahlungs-Schnittstelle wird stattdessen direkt in die zuständige Klasse `PaidOrder` injiziert. Auf diese Weise bleibt die Kopplung dort lokalisiert, wo sie fachlich hingehört, während die übrigen Dekoratoren von diesen Infrastrukturdetails unberührt bleiben.

```java
// Kern: Persistenz — Repository eingekapselt, ID im Konstruktor
public class StoredOrder implements Order {

    private String id;             // Feld 1
    private Cart cart;             // Feld 2
    private OrderRepository repo;  // Feld 3

    public StoredOrder(String id, Cart cart, OrderRepository repo) {
        this.id = id; this.cart = cart; this.repo = repo;
    }

    @Override
    public String id() { return this.id; }           // → Feld 1
                                    
    @Override
    public void process() {
        repo.updateStatus(this.id, "PROCESSING");    // → Feld 3, 1
    }

    @Override
    public void cancel() {
        repo.updateStatus(this.id, "CANCELLED");     // → Feld 3, 1
    }
}

// Horizontaler Dekorator: Zahlung — injiziert PaymentApi, verwendet es in process()
public class PaidOrder implements Order {

    private Order delegate;        // Feld 1
    private PaymentApi gateway;    // Feld 2

    public PaidOrder(Order delegate, PaymentApi gateway) {
        this.delegate = delegate; this.gateway = gateway;
    }

    @Override
    public String id() { return delegate.id(); }  // → Feld 1

    @Override
    public void process() {
        gateway.charge(delegate);                 // → Feld 2, 1
        delegate.process();                       // → Feld 1
    }

    @Override
    public void cancel() { delegate.cancel(); }   // → Feld 1

}

// Horizontaler Dekorator: Lagerverwaltung — injiziert InventoryApi, verwendet es in cancel()
public class InventedOrder implements Order {

    private Order delegate;        // Feld 1
    private InventoryApi inv;      // Feld 2

    public InventedOrder(Order delegate, InventoryApi inv) {
        this.delegate = delegate; this.inv = inv;
    }

    @Override
    public String id() { return delegate.id(); }   // → Feld 1

    @Override
    public void process() { delegate.process(); }  // → Feld 1
                              
    @Override
    public void cancel() {
        inv.release(delegate);                    // → Feld 2, 1
        delegate.cancel();                        // → Feld 1
    }
}

// Dekorator: Audit-Logging — beide Methoden betroffen
public class AuditingOrder implements Order {

    private Order delegate;        // Feld 1
    private Audit audit;           // Feld 2

    public AuditingOrder(Order delegate, Audit auditLogger) {
        this.delegate = delegate; this.audit = audit;
    }

    @Override
    public String id() { return delegate.id(); }        // → Feld 1

    @Override
    public void process() {
        delegate.process();                             // → Feld 1
        audit.log("Processed: " + delegate.id()); // → Feld 2, 1
    }

    @Override
    public void cancel() {
        delegate.cancel();                              // → Feld 1
        audit.log("Cancelled: " + delegate.id());       // → Feld 2, 1
    }
}

// Dekorator: E-Mail-Benachrichtigung — beide Methoden betroffen
public class NotifiedOrder implements Order {

    private Order delegate;          // Feld 1
    private Email email;             // Feld 2

    public NotifiedOrder(Order delegate, Email email) {
        this.delegate = delegate; this.email = email;
    }

    @Override
    public String id() { return delegate.id(); }        // → Feld 1

    @Override
    public void process() {
        delegate.process();                             // → Feld 1
        email.sendConfirmation(delegate.id());          // → Feld 2, 1
    }

    @Override
    public void cancel() {
        delegate.cancel();                              // → Feld 1
        email.sendCancellation(delegate.id());          // → Feld 2, 1
    }
}
// Gleiche Struktur wie AuditingOrder → LCOM4 = 1, CBO = 2

```
In der praktischen Verwendung wird die gewünschte Funktionalität durch eine tiefe Schachtelung der Objekte wie `new NotifiedOrder(new AuditingOrder(...))` zusammengesetzt. Beim Aufruf von `order.process()` durchläuft die Anfrage die gesamte Kette, wobei jede Schicht ihren spezifischen Beitrag, von der Zahlung über die Persistenz bis hin zum Logging und dem E-Mail-Versand, leistet.

```java
Order order = new NotifiedOrder(
    new AuditingOrder(
        new InventedOrder(
            new PaidOrder(
                new StoredOrder(id, cart, repo),
                paymentApi),
            inventoryApi),
        audit),
    email);

// Nutzung:
order.process();
// Ablauf: NotifiedOrder → AuditingOrder → InventedOrder → PaidOrder → StoredOrder
// PaidOrder zieht Zahlung ein, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Bestätigungs-E-Mail.

order.cancel();
// Ablauf: NotifiedOrder → AuditingOrder → InventedOrder → PaidOrder → StoredOrder
// InventedOrder gibt Lagerbestand frei, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Stornierungs-E-Mail.
```

Die Messung der Kennzahlen ergibt ein beeindruckendes Bild der Entkopplung. Jede beteiligte Klasse weist einen **CBO-Wert von 2** sowie einen **LCOM4-Wert von 1** auf. Diese Ergebnisse sind kein Zufallsprodukt, sondern fachlich im Entwurf begründet. So benötigt die `StoredOrder` exakt zwei Felder, da die Persistenz zwangsläufig die zu speichernde Entität sowie das entsprechende Werkzeug in Form des Repositories voraussetzt. Analog dazu kombiniert die `PaidOrder` das Zielobjekt mit der benötigten Infrastruktur der Zahlungs-Schnittstelle.

Dasselbe Prinzip gilt für jede weitere Klasse in diesem Entwurf. Im Gegensatz zu den zuvor betrachteten aufgespaltenen Services erscheinen Komponenten wie `Audit` oder `OrderRepository` hier jeweils nur in einer einzigen, dezidierten Klasse. Eine Änderung an der Logging-Strategie erfordert daher lediglich die Anpassung der `AuditingOrder`, während eine Änderung am `Repository`-Interface ausschließlich die `StoredOrder` betrifft. Da kein anderer Codeteil von diesen Anpassungen beeinflusst wird, werden die Querschnittsbelange nicht über das System verteilt, sondern vollständig innerhalb ihrer jeweiligen Verantwortlichkeit isoliert.

### 5.4 Horizontaler Dekorator-Pattern (OOD)

Weil der vertikale Dekorator-Entwurf mit zunehmender Anzahl an Komponenten an Übersichtlichkeit verliert, schlägt Yegor Bugayenko (2015) einen horizontalen Ansatz vor. Hierbei verwaltet ein zentrales `Wrapper`-Objekt namens `Orders` eine flache Liste von Transformationen, die in einem separaten Interface als `OrderProcess` definiert sind. Anstatt einer tiefen Verschachtelung erfolgt die Ausführung durch eine einfache Iteration über alle registrierten Prozessschritte, wobei jede `OrderProcess`-Klasse eine spezifische technische oder fachliche Aufgabe isoliert.

```java

public interface OrderProcess {
    void process(String id, Cart cart);
    void cancel(String id, Cart cart);
}

public class Orders implements Order {
    private String id;                  // Feld 1
    private Cart cart;                  // Feld 2
    private List<OrderProcess> acts;        // Feld 3

    public Orders(String id, Cart cart, List<OrderProcess> acts) {
        this.id = id; this.cart = cart; this.acts = acts;
    }

    @Override
    public String id() { return this.id; }

    @Override
    public void process() {
        acts.forEach(a -> a.process(this.id, this.cart)); // → Feld 3, 1, 2
    }

    @Override
    public void cancel() {
        acts.forEach(a -> a.cancel(this.id, this.cart));  // → Feld 3, 1, 2
    }
}

// Die Implementierungen der Verantwortlichkeiten
// Jede OrderProcess-Klasse isoliert eine spezifische technische oder fachliche Aufgabe:

// Persistenz
public class Persist implements OrderProcess {
    private OrderRepository repo;
    public Persist(OrderRepository repo) { this.repo = repo; }

    @Override public void process(String id, Cart cart) { repo.updateStatus(id, "PAID"); }
    @Override public void cancel(String id, Cart cart) { repo.updateStatus(id, "CANCELLED"); }
}

// Zahlung (nur process() relevant)
public class Pay implements OrderProcess {
    private PaymentApi gateway;
    public Pay(PaymentApi gateway) { this.gateway = gateway; }

    @Override public void process(String id, Cart cart) { gateway.charge(id); }
    @Override public void cancel(String id, Cart cart) { /* leer */ }
}

// Lagerverwaltung (nur cancel() relevant)
public class Invent implements OrderProcess {
    private InventoryApi inv;
    public Invent(InventoryApi inv) { this.inv = inv; }

    @Override public void process(String id, Cart cart) { /* leer */ }
    @Override public void cancel(String id, Cart cart) { inv.release(cart); }
}

// Weitere Klassen wie 'Audit' (Logging) und 'Notify' (Email) folgen derselben Struktur.

```

In der praktischen Verwendung zeigt sich die Komposition im Vergleich zur vertikalen Kette als flach und gleichrangig. Während das vertikale Dekorieren oft den einfacheren Einstieg darstellt, erweist sich die horizontale Variante als die skalierbarere Form für komplexe Systeme. Ein Aufruf von `order.process()` führt dazu, dass der Wrapper sequenziell über alle Implementierungen iteriert, wodurch die Persistenz den Status setzt, die Zahlung eingezogen wird und abschließend Protokollierung sowie Benachrichtigung erfolgen.

```java
Order order = new Orders(id, cart, List.of(
    new Persist(repo),
    new Pay(paymentApi),
    new Invent(inventoryApi),
    new Audit(audit),
    new Notify(email)
));

order.process(); 
// Orders iteriert: Persist setzt Status, Pay zieht Zahlung ein, 
// Audit protokolliert, Notify sendet Bestätigung.
```

Die Analyse der Metriken verdeutlicht die strukturellen Vorteile dieses Modells. Die `OrderProcess`-Klassen erreichen einen **minimalen CBO-Wert von 1**, da jede Einheit ausschließlich ihr eigenes Werkzeug kennt. Dies stellt eine weitere Reduktion gegenüber den vertikalen Dekoratoren dar, da kein `delegate`-Feld mehr zur Weiterreichung der Aufrufe benötigt wird, da die Steuerung der Kette vollständig auf den `Orders`-Wrapper übergeht. Querschnittsbelange wie Audit oder Persistenz bleiben dabei strikt in jeweils einer Klasse isoliert, während neue Anforderungen wie ein SMS-Versand einfach als neue Implementierung hinzugefügt werden können, ohne bestehenden Code zu berühren, was dem **Open-Closed-Prinzip** entspricht.

Ein struktureller Nebeneffekt dieser Architektur sind die leeren Methoden in Klassen wie `Pay` oder `Invent`, da nicht jeder Prozessschritt zwangsläufig auf jede Aktion reagieren muss. Das Akzeptieren dieser leeren Implementierungen stellt den notwendigen Preis für den sauberen horizontalen Schnitt dar, bedeutet jedoch gleichzeitig einen Verstoß gegen das [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). Nach diesem Prinzip sollte eine Unterklasse so konzipiert sein, dass sie ihre Basisklasse vollständig ersetzen kann, ohne das Programmverhalten durch unerwartete Leerschritte oder eingeschränktes Verhalten zu verfälschen.

Alternativ ließe sich das OrderProcess-Interface im Sinne des **Interface Segregation Principle (ISP)** in spezialisierte Schnittstellen wie `OnProcess` und `OnCancel` aufteilen. Dies würde zwar die Konformität zum LSP wiederherstellen, jedoch die Komplexität des Gesamtsystems durch eine deutlich höhere Anzahl an Abstraktionen und notwendigen Typ-Prüfungen steigern. So zeigt sich an dieser Stelle ein klassischer Zielkonflikt zwischen einer flachen, skalierbaren Struktur und der strikten Einhaltung aller SOLID-Prinzipien.

## 6. Gegenüberstellung

Die Wahl eines Software-Designs stellt stets eine Abwägung zwischen der initialen Entwicklungsgeschwindigkeit und den langfristigen Wartungskosten (Total Cost of Ownership) dar. Ein direkter Vergleich der vier Ansätze verdeutlicht die strukturelle Evolution von der monolithischen Bündelung hin zur granularen Entkopplung. Die Tabelle verdeutlicht die systematische Verschiebung der Qualitätsparameter über die verschiedenen Entwurfsstufen hinweg.

| Merkmal | Service (Monolith) | Services (aufgespalten) | Vertikaler Decorator | Horizontaler Decorator |
|---|---|---|---|---|
| Kohäsion (LCOM4) | LCOM4 = 1 (erzwungen) | LCOM4 = 1 (trivial) | ✅ LCOM4 = 1 (fachlich) | ✅ LCOM4 = 1 (fachlich) |
| Kopplung (CBO) | ❌ CBO = 8 | ⚠️ CBO = 6 je Klasse | ✅ CBO = 2 je Klasse | ✅ CBO = 1 je Klasse |
| Lokale Änderbarkeit | ❌ betrifft alle Methoden | ❌ betrifft alle Klassen | ✅ nur AuditingOrder | ✅ nur Audit |
| Änderungsausbreitung | ❌ trifft gesamten Service | ✅ nur OrderPaymentService | ✅ nur PaidOrder | ✅ nur Pay |
| Erweiterbarkeit (OCP) | ❌ Methoden ändern | ❌ Methoden ändern | ✅ neuer Dekorator | ✅ neues `OrderProcess` + Listeneintrag |
| Testbarkeit (Mocks) | ❌ 8 Mocks erforderlich | ⚠️ 6 Mocks erforderlich | ✅ 2 Mocks pro Klasse | ✅ 1 Mock pro Klasse |
| Komposition | ✅ eine zentrale Klass | ✅ drei kleine Klassen | ⚠️ tiefe Verschachtelung | ✅ flache Liste |
| Strukturelle Risiken | ⚠️ Fat Service / ⚠️ hohe Kopplung | ⚠️ Redundanz / hohe Streuung | ⚠️ Interface-Fragilität | ⚠️ LSP-Verletzung |

**Der DDD Service-Schnitt**

Der klassische `OrderService` bündelt sämtliche Belange der Bestellung innerhalb einer einzigen Klasse. Ein wesentlicher Vorteil dieses Ansatzes ist die zentrale Anlaufstelle, da die gesamte Geschäftslogik einer Domäne an einem Ort als verlässliche Informationsquelle konzentriert bleibt. Zudem ermöglicht diese Struktur ein lineares Debugging, bei dem der Kontrollfluss innerhalb einer Datei leicht nachvollziehbar ist, ohne dass zwischen vielen kleinen Klassen gesprungen werden muss. Da dieses Muster als Industriestandard gilt, bietet es zudem eine geringe kognitive Einstiegshürde für neue Entwickler.

Demgegenüber stehen jedoch signifikante Nachteile, da die hohe Kopplung dazu führen kann, dass Änderungen an technischen Komponenten wie dem Logging unvorhersehbare Seiteneffekte auf die Fachlogik haben. Auch die Testbarkeit wird erschwert, weil jede Methode eine hohe Anzahl an Mocks erfordert, was Unit-Tests schwerfällig und wartungsintensiv gestaltet. Schließlich entsteht bei Änderungen eine hohe kognitive Last, da stets die gesamte Komplexität der Klasse durchdrungen werden muss, um selbst isolierte Stellen sicher anzupassen.

Die Zerlegung in spezialisierte Dienste wie den `OrderPaymentService` verteilt die fachliche Last auf mehrere Klassen. Ein wesentlicher Vorteil liegt in der verbesserten Übersicht, da die Klassen kleiner werden und sich auf spezifische Teilprozesse wie die Zahlung fokussieren. Zudem begünstigt dieser Ansatz die Parallelisierung, weil verschiedene Teams zeitgleich an unterschiedlichen Services arbeiten können.

Dem stehen jedoch Nachteile gegenüber, da technische Querschnittsbelange wie Audit oder Persistenz oft in jedem Service neu injiziert werden müssen. Dies führt zu einer verteilten Wartung, bei der globale Änderungen am Logging-Format an mehreren Stellen gleichzeitig nachgezogen werden müssen. Letztlich bleibt eine echte Isolation aus, da die strukturelle Vermischung von technischer Infrastruktur und Domänenlogik bestehen bleibt.

**Der OOD Decorator-Schnitt**

Dieser Ansatz schneidet die Domäne streng nach Verantwortlichkeiten in Klassen wie `StoredOrder` oder `PaidOrder`. Die Vorteile liegen in der strikten Einhaltung von **SRP** und [Open-Closed-Prinzip](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle), da neue Anforderungen durch zusätzliche Dekoratoren gelöst werden, ohne stabilen Code zu gefährden. Dies ermöglicht ein gezieltes Debugging, da Fehler im Logging garantiert in der `AuditingOrder` zu finden sind, während die kognitive Last auf die jeweils aktuelle Zuständigkeit begrenzt bleibt. Zudem wird eine minimale Kopplung erreicht, da jede Klasse lediglich das Interface und ihre spezifische Abhängigkeit kennt.

Nachteilig wirkt sich jedoch die Projekt-Explosion durch eine deutlich steigende Anzahl an Dateien und Konstruktoren aus. Auch die komplexe Komposition über tiefe Verschachtelungen wie `new PaidOrder(new StoredOrder(...))` ist gewöhnungsbedürftig. Das Muster begünstigt zwar die funktionalen Erweiterungen (Open-Closed-Prinzip), macht aber die Interface-Struktur zu einem kritischen Punkt, der bei Änderungen hohe Aufwände verursacht, da jede Änderung am zentralen `Order`-Interface Anpassungen in sämtlichen Dekoratoren erzwingt.

Das horizontale Muster optimiert zwar die Lesbarkeit bei tiefen Ketten, erzwingt jedoch bei einer größeren Anzahl von Methoden strukturelle Kompromisse in Form von leeren Implementierungen. Ein Wechsel zu diesem Modell ist daher nur dann ratsam, wenn das Design keine leeren Implementierungen von Methoden erfordert, sodass möglichst das *Liskov Substitution Principle* nicht verletzt wird.

## 7. Zusammenfassung und Handlungsempfehlung

Das SRP ist eines der einfachsten Prinzipien und gleichzeitig eines der am schwierigsten umzusetzenden. Es liegt in unserer Natur, Verantwortlichkeiten miteinander zu verknüpfen. Diese zu identifizieren und zu trennen, macht den Kern hochwertigen Softwareentwurfs aus. Letztlich führen fast alle SOLID-Prinzipien auf diesen Kernpunkt zurück.

Um subjektive Debatten in Code-Reviews zu vermeiden, formalisiert Robert Bräutigam das Prinzip als ein Gleichgewicht aus maximaler Kohäsion und minimaler Kopplung (`SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung`). Diese Messbarkeit wird durch konkrete Metriken untermauert, wobei ein LCOM4-Wert von 1 für maximale interne Kohäsion steht und ein CBO-Wert unter 5 die externe Kopplung auf ein gesundes Maß begrenzt.

Der Vergleich der vier Designvarianten verdeutlicht eine systematische Evolution des Softwareentwurfs. In der monolithischen Struktur des `OrderService` werden fünf Verantwortlichkeiten lediglich über technische Querschnittsfelder lose verknüpft, wodurch ein LCOM4-Wert von 1 rein zufällig und ohne fachliche Basis entsteht. Die Aufspaltung in drei spezialisierte Services senkt zwar die Kopplung, verteilt jedoch die Querschnittsbelange lediglich, anstatt sie strukturell zu isolieren. Erst der vertikale Decorator erreicht eine echte Isolation jeder Verantwortlichkeit bei einem niedrigen CBO-Wert von 2. Das horizontale Muster treibt diese Entkopplung mit einem CBO-Wert von 1 pro Prozessklasse auf die Spitze, muss jedoch gegen das Risiko leerer Methoden im Interface abgewogen werden, da diese das *Liskov Substitution Principle* verletzen.

Die bloßen Kennzahlen der Metriken reichen als Beweis für SRP-Konformität indes nicht aus. Wie der monolithische Service zeigt, kann ein LCOM4-Wert von 1 durch rein technische Infrastrukturfelder künstlich erzeugt werden, ohne dass eine fachliche Einheit vorliegt. Erst die kombinierte Betrachtung von LCOM4 und CBO ergibt zusammen mit der qualitativen Analyse der fachlichen Kohäsion (Frage: „Warum sind diese Methoden fachlich verbunden?“) ein vollständiges Bild.

Für die tägliche Praxis lassen sich daraus drei Leitlinien ableiten: 

Ein LCOM4-Wert größer als 1 kann ein Indikator für eine notwendige Dekomposition sein. Existiert kein fachlicher Zusammenhang zwischen den Methoden einer Klasse, ist eine Aufteilung geboten. Eine künstliche Verbindung durch rein technische Infrastrukturbelange sollte vermieden werden, um die Transparenz über die tatsächliche Kohäsion nicht zu verzerren.

Bei einem hohen CBO-Wert > 5 kann die Kopplung durch die Anwendung des *Dependency Inversion Principle* und den Einsatz stabiler Interfaces wirksam gesenkt werden. Zudem sollten Aufrufe von Methoden an fremden Objekten vermieden werden, da sie das Hauptmerkmal semantischer Kopplung darstellen und von rein strukturellen Metriken nicht erfasst werden.  

Unter Beachtung dieser Prämissen erweist sich das SRP nicht als starres Dogma, sondern als präzises Werkzeug, das durch objektive Metriken erst wirklich handhabbar wird. Letztlich dient das Prinzip jedoch dem übergeordneten Ziel der Wartbarkeit, weshalb die Lesbarkeit und Nachvollziehbarkeit des Gesamtsystems stets stärker gewichtet werden sollten als die einseitige Optimierung einer einzelnen Metrik.

---

## Quellen und Referenzen

### Hauptquelle

* Robert Martin (2007) [The Single Responsibility Principle](https://drive.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/view?resourcekey=0-AbuGpXQzwZcUGExkktKt0g)
* Robert Martin (2014) [The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html) 
* Robert Bräutigam (2014) [The Single Responsibility Principle](https://speakerdeck.com/robertbraeutigam/single-responsibility-principle)
* Robert Bräutigam (2017) [The Genius of the Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/)
* Martin Fowler (2013) [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html)
* Matthieu Cneude (2020) [The Single Responsibility Principle Revisited](https://thevaluable.dev/single-responsibility-principle-revisited/)
* Yegor Bugayenko (2015) [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html)

### Weiterführende Artikel und Metriken

* ObjectScript_Q (2026) [Lack of Cohesion in Methods (LCOM4)](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4)
* ObjectScript_Q (2026) [Coupling Between Object classes (CBO)](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo)
* Mihai A. RODEGBFR (2018) [My Take On Object Naming](https://amihaiemil.com/2018/01/07/my-take-on-object-naming.html)
* Yegor Bugayenko (2020) [Prefixed Naming](https://www.yegor256.com/2020/03/03/prefixed-naming.html)
* Yegor Bugayenko (2015) [Don't Create Objects That End With -ER](https://www.yegor256.com/2015/03/09/objects-end-with-er.html)
* Yegor Bugayenko (2017) [Evil Suffix For Object Names](https://www.yegor256.com/2017/09/12/evil-object-name-suffix-client.html)
* Yegor Bugayenko (2015) [A Compound Name Is a Code Smell](https://www.yegor256.com/2015/01/12/compound-name-is-code-smell.html)

### Verwandte Bücher

- Martin, R. C. (2003). *Agile Software Development: Principles, Patterns, and Practices*. Prentice Hall.
- Martin, R. C. (2008). *Clean Code*. Prentice Hall.
- Martin, R. C. (2017). *Clean Architecture*. Prentice Hall.
- Gamma, E. et al. (1994). *Design Patterns*. Addison-Wesley. *(Decorator Pattern)*
- Evans, E. (2003). *Domain-Driven Design*. Addison-Wesley.
- Chidamber, S. R., & Kemerer, C. F. (1994). *A Metrics Suite for Object Oriented Design*. IEEE TSE 20(6).
- Hitz, M., & Montazeri, B. (1995). *Measuring Coupling and Cohesion in Object-Oriented Systems*.
