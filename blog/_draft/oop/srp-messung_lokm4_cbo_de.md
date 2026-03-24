# The Pragmatic Single Responsibility Principle: Beyond Myths to Metrics

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_blog_post_min.png)

> Das pragmatische SRP: Von subjektiven Debatten zu objektiven Metriken

Das [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) (SRP) ist das erste der fünf [SOLID-Prinzipien](https://en.wikipedia.org/wiki/Package_principles), das von Robert C. Martin zu Beginn der 2000er-Jahre etabliert wurde. Trotz seiner scheinbaren Einfachheit gehört es in der Praxis zu den am schwierigsten umzusetzenden Entwurfsprinzipien der objektorientierten Programmierung.

Der Zweck des Prinzips ist es, Software modular, wartbar und verständlich zu halten. Eine Klasse, die einen eindeutigen Verantwortungsbereich besitzt, ist leichter zu testen, einfacher zu ändern und klarer in ihrer Absicht. SRP ist damit nicht nur ein Designprinzip, es ist ein Qualitätsmerkmal, das maßgeblich die langfristige Wartbarkeit beeinflusst und die Ansammlung technischer Schulden minimiert.

## 1. Gegenstand und Zielsetzung

Im Mittelpunkt dieser Betrachtung steht das **Single Responsibility Principle** sowie die Herausforderung, dessen **Einhaltung auf Klassenebene objektiv zu bewerten**. Durch eine objektive Untersuchung des SRP soll ein tieferes Verständnis für dessen Anwendung im Entwickleralltag vermittelt werden. Der hier gewählte Ansatz stützt sich auf die Formalisierung von Robert Bräutigam, welcher das [Single Responsibility Principle](https://speakerdeck.com/robertbraeutigam/single-responsibility-principle) über die Konjunktion (logisches UND) zweier messbarer [Softwaremetriken](https://en.wikipedia.org/wiki/Software_metric) definiert. Die daraus resultierende Formel lautet:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

Die Operationalisierung der Formel erfolgt dabei über zwei zentrale Kennzahlen. Die Kohäsion wird mittels *[Lack of Cohesion of Methods Version 4](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4)* (LCOM4) über eine Graphenanalyse ermittelt, wobei der ideale Zielwert bei 1 liegt. Parallel dazu wird die Kopplung mithilfe von *[Coupling Between Objects](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo)* (CBO) durch das Zählen externer Abhängigkeiten bestimmt mit dem Ziel eines minimalen Wertes. Beide Kennzahlen werden zunächst anhand von Beispielklassen explizit hergeleitet und in einer abschließenden Gegenüberstellung evaluiert. Als Vergleichsobjekte dienen zwei verbreitete Entwurfsansätze: das weitverbreitete Service-Muster des datenzentrierten Ansatzes und das Decorator-Muster des objektorientierten Ansatzes. Diese Konzepte werden zur Analyse innerhalb einer Domäne zur Bestellverwaltung in Java implementiert.

## 2. Das Dilemma mit SRP

Die **Problematik** des SRP liegt in den **subjektiven Formulierungen**, die in der Praxis mehrdeutig interpretiert werden können. Dadurch bieten sie keine konkreten Bewertungs- oder Handlungsgrundlagen für den Klassenentwurf. Eine gängige Definition besagt, dass eine Klasse nur einen einzigen Grund für Änderungen haben sollte („only one reason to change").  Was jedoch konkret als Änderungsgrund gilt, offenbart sich meist erst im Moment der tatsächlichen Anpassung. Eine vorausschauende Trennung beruht daher oft auf reiner Spekulation, da theoretisch jede minimale Anforderungsänderung als neuer, eigenständiger Grund interpretiert werden könnte.

Aufgrund der Unklarheiten rund um den Begriff des *"Änderungsgrundes"* präzisierte Robert C. Martin seine Ausführungen später, woraus sich im Laufe der Zeit verschiedene Definitionsebenen entwickelten. Diese reichen von der Forderung nach genau einer Aufgabe pro Modul über die Identifikation eines spezifischen Änderungsgrundes bis hin zur Empfehlung, Dinge mit identischen Änderungsursachen zusammenzufassen. In der konkreten Auslegung wird ein solcher Grund schließlich als Anforderung definiert, die von einem einzelnen Akteur oder Geschäftsmann stammt.

> "Each software module should have one and only one responsibility"
> - Robert C. Martin (2003)

> "Each software module should have one and only one reason to change"
> - Robert C. Martin (2007)

> "Gather together the things that change for the same reasons. Separate those things that change for different reasons"
> - Robert C. Martin (2014)

> "'Same reason' means it originates from the same business person"
> - Robert C. Martin (2014)

In der praktischen Anwendung werfen diese Definitionen jedoch neue Fragen auf:

* So ist beispielsweise unklar, ob eine Klasse `Order`, die eine Bestellung sowohl validiert als auch persistiert, bereits gegen die Beschränkung auf ***eine einzelne Aufgabe*** verstößt.
* Auch die praktische Faustregel, ***Zusammengehöriges basierend auf künftigen Änderungsgründen zu bündeln***, ist zum Zeitpunkt der Implementierung kaum prüfbar, da sie eine Vorausplanung der künftigen Produktentwicklung erfordert.
* Ebenso erweist sich der Verweis auf den ***Geschäftsmann*** zwar für die strategische Domänenmodellierung als hilfreich, bleibt jedoch als konkretes Programmierkriterium für den Codealltag unbrauchbar.

Diese kontextabhängigen Interpretationen führen in Codereviews häufig zu zeitraubenden Grundsatzdebatten. Ohne objektive Bewertungskriterien können solche Diskussionen in subjektive Meinungsverschiedenheiten auszuarten, was die Effizienz und Konsistenz der Softwareentwicklung verhindert.

## 3. Die Formalisierung von Kohäsion und Kopplung

### 3.1 Formale Definition von SRP 

Die eher philosophisch und soziologisch geprägten Definitionen des SRP bieten aufgrund ihrer Subjektivität kaum eine klare Handlungsgrundlage für die Softwareentwicklung. Begriffe wie Verantwortung, Änderungsgrund oder Akteursorientierung erweisen sich für die praktische Umsetzung als zu vage und können in der Konsequenz häufig zu einer unnötigen Codefragmentierung führen. Robert Bräutigam schlägt stattdessen eine pragmatische Definition vor:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

Durch die formale Gleichsetzung des SRP mit der Konjunktion aus maximaler [Kohäsion](https://en.wikipedia.org/wiki/Cohesion_(computer_science)) und minimaler [Kopplung](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) wandelt sich das Prinzip von einer rein abstrakten Designphilosophie zu einer präzisen Strukturmetrik. Diese mathematische Definition ermöglicht es, die ehemals vagen Verantwortlichkeiten einer Klasse durch messbare strukturelle Eigenschaften objektiv zu bewerten.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_coupling_cohesion.png)

Die interne Qualität einer Klasse bemisst sich an ihrer **Kohäsion**, also dem **Grad des Zusammenhangs**, in dem Methoden und Felder eine funktionale Einheit bilden und somit das Versprechen einlösen, nur eine Sache zu tun.

Die externe Qualität wird hingegen durch die **Kopplung** bestimmt, welche die **Abhängigkeiten zu fremden Objekten** beschreibt. Je geringer diese externe Vernetzung ausfällt, desto isolierter und wartungsfreundlicher bleibt die Klasse gegenüber globalen Änderungen im System.

Innerhalb der Kopplung wird zwischen **physikalischen** und **semantischen** Abhängigkeiten unterschieden. Während sich physikalische Verbindungen durch Feldtypen statisch nachweisen lassen, verbirgt sich die semantische Kopplung hinter einem impliziten Wissen über fremde Objektstrukturen. Da diese Abhängigkeiten für den Compiler nicht greifbar sind, führen sie oft zu schwer nachvollziehbaren Fehlfortpflanzungen bei Codeänderungen. Sobald eine Klasse beispielsweise über Ketten wie `user.getAddress().getCity()` auf tieferliegende Daten zugreift, entsteht eine strukturelle Abhängigkeit, die über die reine Typkenntnis hinausgeht. Folglich fungiert jede Lesemethode als möglicher Kanal für eine erhöhte semantische Kopplung.

### 3.2 Minimierung von Kopplungen und Maximierung von Kohäsion

Grundsätzlich ist eine Kopplung zwischen Klassen für die Funktionsfähigkeit eines Systems unumgänglich. Ein übermäßiges Maß an Abhängigkeiten erschwert jedoch die Modifikation, das Testen und die Wiederverwendbarkeit von Komponenten erheblich. Um die externe und interne Kopplung von Klassen systematisch zu reduzieren, ist die Anwendung der weiteren SOLID-Prinzipien, grundlegender OOP-Entwurfsrichtlinien sowie bewährter Entwurfsmuster essenziell.

Eine zentrale Lösung bietet das [Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle), indem es hochstufige Module durch stabile Abstraktionen von konkreten Implementierungen entkoppelt. Das Prinzip [Encapsulate what varies](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)) unterstützt dies durch die Isolation änderungsanfälliger Logik hinter Schnittstellen. Ergänzend stellt das [Interface Segregation Principle](https://en.wikipedia.org/wiki/Interface_segregation_principle) sicher, dass Klassen lediglich an spezifisch benötigte Teilschnittstellen gebunden werden, anstatt unnötig breite Abhängigkeiten zu erzeugen. Eine korrekte Segregation wird dabei maßgeblich durch die Einhaltung des [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle) (LSP) gewährleistet. Nach diesem Prinzip sollte eine Unterklasse so konzipiert sein, dass sie ihre Basisklasse vollständig ersetzen kann, ohne das Programmverhalten durch unerwartete Leerschritte oder eingeschränktes Verhalten zu verfälschen.

Gegen semantische Abhängigkeiten dienen das [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) und das Prinzip [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html) als strukturelle Schranken. Während das Law of Demeter den Zugriffspfad auf unmittelbare Nachbarobjekte beschränkt, fordert Tell, Don’t Ask dazu auf, Objekten Befehle zu erteilen, statt deren internen Zustand abzufragen. Die konsequente Anwendung beider Prinzipien wahrt die Kapselung und schützt effektiv vor semantischer Instabilität, indem sie die Verantwortlichkeiten klar isoliert. Dies führt unmittelbar zu einer hohen Kohäsion, da Daten und die darauf operierende Logik innerhalb eines Moduls vereint bleiben. Indem eine Klasse ihre eigenen Informationen autonom verwaltet und lediglich fachlich relevante Operationen anbietet, entsteht eine starke funktionale Bindung. Das Modul konzentriert sich rein auf eine spezifische Aufgabe, ohne fremdes Wissen mitzuführen oder den eigenen internen Zustand in form von Daten preiszugeben.

Des Weiteren helfen verschiedene Strukturmuster (wie Adapter, Bridge oder Dekorator) und Verhaltensmuster (wie Strategy oder Visitor), das [Open-Closed-Prinzip](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) umzusetzen. Demnach sollten Funktionserweiterungen idealerweise ohne die Entstehung neuer, direkter Kopplungen innerhalb der bestehenden Klasse realisiert werden. Diese Entkopplung stützt ebenso maßgeblich die Kohäsion, indem sie verhindert, dass eine Klasse durch immer neue Zuständigkeiten (Varianten oder Sonderfälle) ausgeweitet wird. Anstatt die interne Logik zu verkomplizieren, können neue Verantwortlichkeiten in spezialisierte, eigenständige Klassen ausgelagert werden. So bleibt der ursprüngliche Code fokussiert auf seine Kernaufgabe, während Erweiterungen als hochkohärente, austauschbare Bausteine hinzugefügt werden.


## 4. Messverfahren für Kohäsion (LCOM4) und Kopplung (CBO)

### 4.1 Lack of Cohesion of Methods

Eine der bekanntesten Metriken zur Messung der inneren Klassenstruktur ist die Lack of Cohesion of Methods (LCOM). Da in der Literatur verschiedene Versionen dieser Kohäsionsmetrik existieren, wird in diesem Beitrag gezielt die vierte Iteration (LCOM4) nach [Hitz und Montazeri (1995)](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4) verwendet. Diese zeichnet sich durch eine hohe praktische Relevanz aus, da sie aufgrund ihrer einfachen Anwendung und klaren Interpretierbarkeit eine objektive Bewertung der SRP-Konformität ermöglicht. 

Die **LCOM4 Metrik** ermittelt die **Anzahl** verbundener Elementgruppen innerhalb einer Klasse, wobei die Kohäsion mittels einer Graphenanalyse veranschaulicht wird. Ein zusammenhängender Teilgraph entsteht dabei immer dann, wenn **Methoden** entweder auf dieselben **Instanzvariablen** zugreifen oder sich durch gegenseitige Aufrufe direkt beeinflussen.

Die konkreten Aussagen zur Bewertung der Klassenstruktur liefern die folgenden Kennzahlen: 

* **LCOM4-Wert von 1** bedeutet, dass sämtliche Methoden und Felder direkt oder indirekt miteinander verbunden sind, was einer hochgradig kohäsiven Klasse im Idealzustand entspricht.
* **LCOM4-Wert über 1** bedeutet, dass die Klasse in mehrere unabhängige Teilgraphen zerfällt, was als objektives Indiz für eine Verletzung des Single Responsibility Principles gewertet wird.
* **LCOM4-Wert von 0** kennzeichnet hingegen eine Klasse ohne Methoden, wie etwa einen reinen Datencontainer, was im Rahmen dieser Analyse neutral bewertet wird.

Die Herleitung der LCOM4-Metrik verdeutlichen die folgenden Fallbeispiele anhand der Graphenanalyse.

**Fallbeispiel 1: Niedrige Kohäsion (LCOM4 = 2)**

In diesem Szenario enthält die Klasse `Order` zwei logische Zuständigkeiten für die Anzeige `display()` einer Bestellung und Abwicklung `pay(int amount)` einer Zahlung.


```java
// Die Klasse zerfällt in zwei isolierte Teilgraphen (LCOM4 = 2)
public class Order {

    private Cart cart;                   // Feld 1
    private Customer customer;           // Feld 2
    private String status = "PENDING";   // Feld 3
    private int amount;                  // Feld 4

    // M1: Anzeige-Logik (nutzt Feld 1 und Feld 2)
    public String display() {
        return customer.getName() + ": " + cart.itemCount() + " items";
    }

    // M2: Zahlungs-Logik (nutzt Feld 3 und Feld 4)
    public void pay(int amount) {
        this.amount = amount;
        this.status = "PAID";
    }
}
```

Die Graphenanalyse zeigt, dass zwischen diesen beiden Gruppen keine Verbindung existiert, da weder ein gemeinsames Feld genutzt noch ein gegenseitiger Methodenaufruf getätigt wird. 

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case1.png)

Die Klasse besitzt zwei unabhängige Verantwortlichkeiten, was sich in zwei isolierten Teilgraphen und einem LCOM4-Wert von 2 widerspiegelt.

**Fallbeispiel 2: Ideale Kohäsion durch Aufteilung (LCOM4 = 1)**

Um eine echte fachliche Kohäsion zu erreichen, zerlegen wir die Klasse anhand ihrer Verantwortlichkeiten in zwei spezialisierte Einheiten.

```java
// Fokus auf Inhalt/Anzeige
public class OrderView {

    private Cart cart;                 // Feld 1
    private Customer customer;         // Feld 2

    public String display() {
        // Nutzt Feld 1 und 2
        return customer.getName() + ": " + cart.itemCount();
    }
}

// Fokus auf Zahlung/Transaktion
public class OrderPayment {

    private String status = "OPEN";   // Feld 3
    private int amount;               // Feld 4

    public void pay(int amount) {
        // Nutzt Feld 3 und 4
        this.amount = amount;
        this.status = "PAID";
    }
}

```


Durch die Aufteilung entstehen zwei unabhängige Graphen, die jeweils eine in sich geschlossene funktionale Einheit bilden. 

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case2.png)

Innerhalb der Klasse `OrderView` erzeugt die Methode `display()` eine direkte Verbindung zwischen dem Kundenobjekt und dem Warenkorb. In der Klasse `OrderPayment` modifiziert die Methode `pay()` beide Instanzvariablen gleichzeitig, was eine starke interne Bindung bewirkt. In beiden Fällen ergibt die Analyse einen einzelnen, vollständig zusammenhängenden Teilgraph mit einem LCOM4-Wert von 1, was die fachliche Isolation bestätigt.

**Fallbeispiel 3: Kohäsion durch Feld-Zusammenhang (LCOM4 = 1)**

In diesem Szenario fungiert das Feld status (Feld 3) als Knotenpunkt, da es nun von beiden Methoden verwendet wird. 
Rein strukturell erhöht sich die Kohäsion, wodurch der LCOM4-Wert auf 1 sinkt. 

```java
public class Order {

    private Cart cart;                   // Feld 1
    private Customer customer;           // Feld 2
    private String status = "OPEN";      // Feld 3
    private int amount;                  // Feld 4

    public String display() {
        // Nutzt Feld 1, 2 und 3
        return customer.getName() + ": " + cart.itemCount() + ", Status: " + status;
    }

    public void pay(int amount) {
        // Nutzt Feld 3 und 4
        this.amount = amount;
        this.status = "PAID";
    }
}
```

Die Graphenanalyse der Klasse `Order` verdeutlicht das folgende Bild.

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case3.png)

Da beide Methoden auf das Feld `status` zugreifen, sind die ursprünglich isolierten Logikbereiche über diese Instanzvariable miteinander verbunden. Im Sinne der Graphentheorie entsteht ein einziger zusammenhängender Graph, da ein Pfad von M1 über Feld 3 zu M2 existiert. Das Ergebnis ist ein LCOM4-Wert von 1.

**Fallbeispiel 4: Kohäsion durch Methoden-Abhängigkeiten (LCOM4 = 1)**

In diesem Szenario wird die Klasse Order um die Methode `isFinalized()` (M3) ergänzt. Diese prüft zentral, ob die Bestellung bereits einen Endzustand erreicht hat, wodurch das Objekt seinen Lebenszyklus autonom verwaltet. Eine Änderung der fachlichen Definition von „abgeschlossen“ (beispielsweise durch neue Statuswerte) erfordert somit nur noch eine Anpassung an dieser zentralen Stelle, was die Wartbarkeit erheblich steigert.

In dieser Struktur wird die Klasse Order durch die zentrale Methode `isPaid()` (M3) funktional verknüpft. Diese kapselt die Logik des Zahlungsstatus und sorgt für eine autonome Verwaltung des Objektzustands. Änderungen an der Definition von „bezahlt“ müssen so nur an dieser zentralen Stelle vorgenommen werden, was die Wartbarkeit optimiert.

```java
public class Order {

    private Cart cart;           // Feld 1
    private Customer customer;   // Feld 2
    private int amount = 0;      // Feld 3

    // M1: Anzeige-Logik
    public String display() {
        // Nutzt Feld 1, 2 und M3 (indirekt Feld 3)
        String prefix = isPaid() ? "[PAID] " : "[OPEN] ";
        return prefix + customer.getName() + ": " + cart.itemCount();
    }

    // M2: Zahlungs-Logik
    public void pay(int amount) {
        // Nutzt Feld 3 und M3
        if (isPaid()) throw new IllegalStateException("Bereits bezahlt.");
        this.amount = amount;
    }

    // M3: Zentraler Knotenpunkt
    private boolean isPaid() {
        // Greift auf Feld 3 zu
        return amount > 0;
    }
}
```
Durch diese Struktur wird im Graphen ersichtlich, dass **M3** als zentraler Verbindungsknoten fungiert. 

![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_cohesion_case4.png)

Durch die Aufruf-Abhängigkeiten von **M1** und **M2** zu **M3** entsteht ein vollständig zusammenhängender Graph. Der resultierende **LCOM4-Wert von 1** ist hier das Ergebnis einer funktionalen Abhängigkeit (Kapselung des Lebenszyklus) und keine rein technische Hilfsbrücke.


**Qualitative Analyse von Kohäsion**

Wie im **Fallbeispiel 2** gezeigt, kann ein idealer Metrikwert künstlich durch technische Querschnittsbelange (wie etwa eine `id`, ein `status`-Feld oder UI-spezifische Daten) erzeugt werden, ohne die zugrunde liegende Vermischung von Verantwortlichkeiten tatsächlich zu lösen. Aus der Sicht eines datenzentrierten Entwurfs mag die strikte Trennung von Zahlungs- und Präsentationslogik, wie in **Fallbeispiel 3** gezeigt, sinnvoll erscheinen. Diese widerspricht jedoch einer verhaltensorientierten Sichtweise, weil dabei die Kapselung im Sinne der Objektorientierung aufgebrochen wird.

Aus der verhaltensorientierten Perspektive ist die Einbettung einer `display()`-Methode in das `Order`-Objekt keine künstliche Verbindung, sondern Ausdruck echter Kapselung. Da die Darstellung einer Bestellung untrennbar mit ihrem fachlichen Zustand (z. B. dem `status`) verknüpft ist, gehört dieses Wissen zum Kern der Entität selbst. Eine Aufspaltung dieser Einheit würde den Einsatz von Getter-Methoden erzwingen, was das **Tell, Don’t Ask-Prinzip** missachtet und damit eine semantische Kopplung verursacht.

Das **Fallbeispiel 4** führt diese gegensätzlichen Positionen zusammen. Durch die Einführung der zentralen Hilfsmethode `isPaid()` wird eine echte funktionale Abhängigkeit geschaffen. Diese Methode wertet das Feld Betrag logisch aus und wird von beiden Hauptmethoden genutzt. Damit wird die Kohäsion nicht mehr nur durch ein einzelnes Feld (technische Brücke) gehalten, sondern durch eine gemeinsame Geschäftsregel (fachliche Kapselung) im Graphen verankert.

Diese Fallbeispiele verdeutlichen eine zentrale Erkenntnis für die Praxis: Ein **LCOM4-Wert von 1 ist eine notwendige, aber keine hinreichende Bedingung** für die SRP-Konformität. Die LCOM4-Kennzahl bestätigt lediglich die strukturelle Verbundenheit, **ersetzt jedoch nicht die qualitative Prüfung**, ob die verknüpften Elemente tatsächlich eine fachliche Einheit bilden.

Als Handlungsempfehlung lässt sich daraus Folgendes ableiten: Existiert kein fachlicher Zusammenhang zwischen den Methoden einer Klasse, ist eine Aufteilung geboten. Eine künstliche Verbindung durch rein technische Infrastrukturbelange sollte vermieden werden, um die Transparenz über die tatsächliche Kohäsion nicht zu verzerren. Ein robustes Design nach **Fallbeispiel 4** zeigt hingegen, wie z.B. interne Hilfsmethoden die fachliche Kohäsion stärken und gleichzeitig die Wartbarkeit durch Zentralisierung von Logik erhöhen.

### 4.2 Coupling Between Objects

Ergänzend zur Kohäsion misst die Metrik **Coupling Between Objects** (CBO) nach Chidamber & Kemerer (1994) die Anzahl der externen Typen, zu denen eine Klasse eine direkte Abhängigkeit unterhält. Die Ermittlung des CBO-Werts erfolgt durch die Zählung aller gekoppelten Klassen innerhalb folgender Strukturen:
* **Klassenerweiterungen** (Vererbung und Implementierung),
* **Feldtypen** (Instanzvariablen der Klasse),
* **Methodenaufrufe** (Referenzen auf fremde Logik),
* **Methodensignaturen** (Typen von Argumenten und Rückgabewerten),
* **Lokale Variablen** (Instanziierungen oder Referenzen innerhalb von Methoden).

*Primitive* Datentypen und *Standardwrapper* wie `int` oder `String` bleiben hierbei unberücksichtigt, da sie zu den Basiselementen einer Programmiersprache gehören.

Die Metrik-CBO dient als Indikator für die Wartbarkeit und Testbarkeit, da ein hoher CBO-Wert bedeutet, dass die Klasse bei Änderungen an ihren Partnerklassen überproportional oft mitangepasst werden muss.

Die Bewertung der Messergebnisse folgt einer klaren Skala:

* **CBO-Wert von 0** deutet darauf hin, dass eine Klasse isoliert ist und somit faktisch nicht am System teilnimmt.
* **CBO-Werte zwischen 1 und 5** gelten als Idealbereich, da dieser eine lose Kopplung signalisiert und die Wartbarkeit unterstützt.
* **CBO-Wert größer als 5** deutet auf eine zu enge Verflechtung mit anderen Klassen hin.

Der in diesem Beitrag verwendete Schwellenwert von **CBO > 5** ist kein universeller Standard. Je nach Quelle variiert der empfohlene Grenzwert erheblich: 
Während [Sahraoui, Godin & Miceli](https://www.iro.umontreal.ca/~sahraouh/papers/ICSM00.pdf) in ihren Artikel einen Maximalwert von **14** festlegen, empfiehlt [ObjectScript Quality](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo) einen deutlich niedrigeren Schwellenwert von **4**.

Die Ermittlung der CBO-Werte und die Nuancen von Kopplungstypen veranschaulichen die folgenden Fallbeispiele.

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

    public ReportData generate(Query q) { // Query (CBO +1), ReportData (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        return exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, ReportData, Query
```

**Fallbeispiel 3: Signaturoptimierung**

Sofern auf einen spezifischen Rückgabetyp (`void` statt `ReportData`) verzichtet werden kann, lässt sich die Kopplung weiter senken. Ein entscheidender Faktor ist hierbei die Unterscheidung zwischen Signaturkopplung und lokaler Kopplung.

```java
// Kopplung an Schnittstellen ohne Rückgabetyp (CBO = 3)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generate(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, Query
```

Der Typ `DataRow` taucht hier nur noch als lokaler „Durchlaufwert“ auf. Da er weder Teil der Felder noch der Methodensignatur ist, wird er in der Metrik nicht als direkte Kopplung gewertet. Die Klasse `Report` reicht das `DataRow`-Objekt lediglich zwischen `Repository` und `Exporter` weiter, ohne eine funktionale Abhängigkeit zur internen Struktur von `DataRow` zu besitzen (Pass-through-Effekt).

**Fallbeispiel 4: Semantische Kopplung**

Der CBO-Wert erhöht sich wieder auf 4, sobald eine explizite Abhängigkeit zu `DataRow` entsteht. Dies ist der Fall, wenn der `Report` aktiv Methoden des Typs aufruft (z. B. eine Validierung via `rows.get(0).validate()`).

```java
// Kopplung an Semantik (CBO = 4)
public class Report {

    private Repository repository; // Interface (CBO +1)
    private Exporter exporter;     // Interface (CBO +1)

    public void generate(Query q) { // Query (CBO +1)
        List<DataRow> rows = repository.fetch(q);
        rows.get(0).validate(); // Aktiver Aufruf an DataRow (CBO +1)
        exporter.export(rows);
    }
}
// Gezählte Typen: Repository, Exporter, Query, DataRow
```

Sobald die Klasse Methoden wie `validate()` aufruft, entsteht eine semantische Kopplung. Die Klasse `Report` benötigt nun „Wissen“ über das interne Verhalten und die Geschäftsregeln von `DataRow` (Verletzung des Law of Demeter).  Die Klasse verlässt damit ihre Rolle als reiner Koordinator und spricht mit einem „Fremden“, den sie eigentlich nur durchreichen sollte. Dadurch ist die Klasse nicht mehr nur technisch gekoppelt (Kenntnis des Typs), sondern auch logisch (Kenntnis des Prozesses), was die Wartbarkeit erschwert.

### 4.3 Die Synergie von LCOM4 und CBO

Wie die vorangegangenen Fallbeispiele zeigen, kann ein LCOM4-Wert von 1 trügerisch sein. Sobald eine Klasse technisch notwendige Querschnittsfelder wie eine id, ein Statusfeld oder einen Logger nutzt, werden im Graphen Brücken zwischen eigentlich fremden fachlichen Verantwortlichkeiten geschlagen. Die strukturelle Analyse wertet dies als Kohäsion, obwohl das Single Responsibility Principle faktisch verletzt bleibt.

An dieser Stelle entfaltet die Kombination mit der CBO-Metrik ihre volle Diagnosekraft. Während der LCOM4 im `OrderService` (vgl. Abschnitt 5.1) eine ideale interne Bindung suggeriert, würde eine Messung der Kopplung (CBO) bei einem „Fat Service“ sofort Alarm schlagen. Ein hoher CBO-Wert offenbart, dass die Klasse trotz ihrer internen Verknüpfung über ein Statusfeld eine übermäßige Anzahl externer Abhängigkeiten bedienen muss. Ein Entwurf ist erst dann wirklich SRP-konform, wenn er beide Kriterien gleichzeitig erfüllt:

* **LCOM4 = 1**, wobei die Verbindung auf **fachlicher Logik** basiert und nicht auf rein technischer Infrastruktur.
* **CBO ≤ 5**, was sicherstellt, dass die Klasse nicht zu viele externe „Wissensbereiche“ in sich vereint.

Erst in der Gesamtbetrachtung beider Kennzahlen lässt sich objektiv feststellen, ob eine Klasse eine echte fachliche Einheit bildet oder lediglich eine Ansammlung lose gekoppelter Aufgaben darstellt, die durch technische Hilfsvariablen zusammengehalten werden. 

Ein „sauberes“ Design nach der Formalisierung von Robert Bräutigam strebt demnach eine Klasse an, die durch maximale Kohäsion bei minimaler Kopplung besticht, was sich in der Zielmarke eines **LCOM4-Werts von 1** und eines **CBO-Bereichs von 0 bis 5** widerspiegelt.

> `SRP ≡ max(LCOM4 = 1) ∧ min(CBO ≤ 5)`

Das nachstehende **Diagram** visualisiert das Zusammenspiel zwischen LCOM4 und CBO zur Bewertung der Softwarequalität und der Einhaltung des SRP.
* **X-Achse (CBO): Misst die Anzahl der externen Abhängigkeiten einer Klasse. Der kritische Schwellenwert (CBO-Threshold) ist bei 5 markiert.
* Y-Achse (LCOM4): Misst die Anzahl der isolierten Teilgraphen (Logik-Inseln) innerhalb einer Klasse. Ein Wert von 1 steht für maximale Kohäsion, Werte von 2 oder höher für mangelnde Kohäsion.
  
![](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/picture/oop_srp_conceptual_diagram_slim.png)

Die Grafik unterteilt Klassen anhand ihrer Metriken in vier spezifische Qualitätsbereiche. 

Der Quadrant **unten links** markiert dabei das **SRP-Ideal**, in dem die Klasse sowohl eine maximale Kohäsion (LCOM4 = 1) als auch eine lose Kopplung (CBO < 5) aufweist. In diesem Zustand ist das Single Responsibility Principle vollständig erfüllt, da die Klasse eine klar abgegrenzte Aufgabe mit minimalen externen Abhängigkeiten bewältigt.

Im Gegensatz dazu beschreibt der Quadrant **oben links** eine Fragmentierung. Hier ist die Kopplung zwar ebenfalls gering, doch die Klasse weist eine niedrige Kohäsion (LCOM4 = 2) auf. Das bedeutet, dass sie intern in zwei oder mehr voneinander isolierte Logikbereiche zerfällt und folglich in separate Klassen aufgeteilt werden sollte.

Der Bereich **unten rechts** identifiziert den sogenannten Fat Service. Klassen in diesem Sektor sind intern zwar kohäsiv und bearbeiten eine fachliche Einheit, sind jedoch mit zu vielen externen Klassen vernetzt (CBO > 5). Dies deutet darauf hin, dass die Klasse zu viele Infrastruktur-Abhängigkeiten in sich vereint und trotz fachlicher Stimmigkeit zu komplex für eine einfache Wartung wird.

Den kritischsten Zustand bildet schließlich der Quadrant **oben rechts**. In diesem kritischen Bereich ist die Klasse weder intern kohäsiv noch extern entkoppelt. Eine solche Kombination aus geringer Kohäsion und hoher Kopplung charakterisiert ein klassisches Anti-Pattern, oft als „God Object“ bezeichnet, welches aufgrund seiner Komplexität kaum testbar und nur unter hohem Risiko zu modifizieren ist.

Die **Wahl des Schwellenwerts** beeinflusst mindestens zwei gegenläufige Qualitätsziele. Ein niedriger Grenzwert fördert kleine, fokussierte Klassen, erhöht jedoch gleichzeitig die Fragmentierung des Systems und damit die Anzahl der zu verwaltenden Abstraktionen. Ein höherer Grenzwert erlaubt kompaktere Klassen, erschwert aber das Testen, da pro Klasse mehr Abhängigkeiten als Mocks bereitgestellt werden müssen. Der optimale Schwellenwert ergibt sich daher aus einer Abwägung dieser beiden Pole und könnte durch empirische Analysen organisationsspezifisch ermittelt werden. Im Rahmen dieses Beitrags wird einheitlich ein Schwellenwert von **maximal 5** verwendet.

### 4.4 Der Zielkonflikt zwischen LCOM4 und CBO

Die isolierte Optimierung einer der beiden Kennzahlen führt unweigerlich in eine architektonische Sackgasse. In der Praxis stehen die Werte von LCOM4 und CBO in einer umgekehrten Beziehung.

* **Extreme Kohäsion (LCOM4 = 1 durch Atomisierung):** Versucht man, den LCOM-Wert durch das Aufspalten einer Klasse in kleinste Einheiten zu perfektionieren, steigt die Kopplung (CBO) im Gesamtsystem drastisch an. Viele hochspezialisierte Klassen müssen nun über komplexe Schnittstellen miteinander kommunizieren, um eine fachliche Aufgabe zu lösen. Die Komplexität verschiebt sich von der Inneren Logik (Intra-Modul) hin zur Interaktion (Inter-Modul).
* **Minimale Kopplung (CBO → 0 durch Zentralisierung):** Reduziert man hingegen die externe Kopplung radikal, landet man bei massiven „Gott-Klassen“. Diese benötigen zwar kaum externe Partner, vereinen aber so viele unterschiedliche Zuständigkeiten in sich, dass die interne Kohäsion (LCOM) wegbricht.

[Bild]

Ein SRP-konformer Entwurf befindet sich daher auf der Pareto-Front: Er sucht den Punkt, an dem die Klasse fachlich „fokussiert genug“ ist (LCOM4 = 1), ohne das System in ein unüberschaubares Netz aus Kleinstabhängigkeiten (CBO > 5) zu stürzen. Die Verbesserung des einen Wertes darf nicht durch eine überproportionale Verschlechterung des anderen erkauft werden.

Die optimale Designqualität zeigt sich dort, wo eine Klasse eine eindeutige fachliche Identität besitzt, ohne zu viele andere Klassen für die Realisierung der Anforderungen zu verwenden. Dies beschreibt das Gleichgewicht zwischen hoher Kohäsion (Identität) und niedriger Kopplung (Abhängigkeit). Dieses Gleichgewicht verhindert die Entstehung von ‚Spaghetti-Code‘ (unkontrollierte Querverbindungen) ebenso wie ‚Lasagne-Code‘ (zu viele kleinteilige Schichten), wodurch die Wartbarkeit und Erweiterbarkeit des Systems langfristig gesichert wird.


## 5. Beispiele: Datenzentrierter Service vs. Objektorientierter Dekorator

Um die praktische Anwendung von LCOM4 und CBO zu demonstrieren, wird im Folgenden dieselbe Domäne mit unterschiedlichen Entwurfsansätzen untersucht. Ziel ist es, die Unterschiede in der SRP-Konformität objektiv messbar zu machen.

Als Szenario dient der Bestellvorgang eines Onlineshops mit drei Kernoperationen, wie Anlegen, Bezahlen und Stornieren.

* **Anlegen:** Reserviert Artikel im Lager (InventoryApi), speichert die Bestellung (OrderRepository), versendet eine Reservierung (Email) und protokolliert den Vorgang (Audit).
* **Bezahlen:** Zieht den Betrag ein (PaymentApi), markiert die Bestellung als bezahlt, versendet eine Bestätigung (Email) und protokolliert den Vorgang (Audit).
* **Stornieren:** Gibt Artikel im Lager frei (InventoryApi), markiert die Bestellung als storniert, versendet eine Nachricht (Email) und protokolliert den Vorgang (Audit).

Daraus ergeben sich sieben beteiligte Komponenten, deren Verantwortlichkeiten in den folgenden Implementierungen unterschiedlich verteilt werden: `Cart`, `Customer`, `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email` und `Audit`.

### 5.1 Service-Pattern (datenzentriert)

Das klassische, datenzentrierte Service-Pattern bündelt alle Operationen einer Bestellung in einer zentralen Klasse, die auf einem anämischen Domänenmodell operiert.

```java
// Anemic Domain Model
public class Order {
    public String id;
    public String status;
    // Getter und Setter...
}
```

```java
public class OrderService {

    private OrderRepository orderRepository;   // Feld 1
    private InventoryApi inventoryApi;         // Feld 2
    private PaymentApi paymentApi;             // Feld 3
    private Email email;                       // Feld 4
    private Audit audit;                       // Feld 5

    public Order reserve(Cart cart, Customer customer) { // Parameter: Cart (CBO +1), Customer (CBO +1), Rückgabe: Order (CBO +1)
        // Hinweis: Typen werden klassenübergreifend gezählt; Duplikate (z. B. Order in pay/release) werden nicht mehrfach gewertet.
        inventoryApi.reserve(cart);
        Order order = new Order(cart, customer);
        orderRepository.save(order);
        email.sendReservation(order);
        audit.log("Order reserved: " + order.getId());
        return order;
    }

    public void pay(Order order) {
        paymentApi.charge(order);
        order.markAsPaid();
        orderRepository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }

    public void release(Order order) {
        inventoryApi.release(order);
        order.markAsReleased();
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
Order order = service.reserve(cart, customer);

// Bezahlen kennt 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.pay(order);

// Stornieren — service kennt 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.release(order);
```

Eine Untersuchung nach LCOM4 zeigt, dass die Methode `reserve` auf die Felder 1, 2, 4 und 5 zugreift, während `pay` die Felder 1, 3, 4 und 5 sowie `release` die Felder 1, 2, 4 und 5 beansprucht. Folglich weist die Klasse einen **LCOM4-Wert von 1** auf, was auf eine hohe Kohäsion hindeutet, da alle Methoden über das `Repository` und das `Audit`-Logging miteinander verknüpft sind.

Die Ermittlung der Metriken ergibt für den **CBO einen Wert von 8**, was nach der strikten Definition von Chidamber & Kemerer (1994) einer sehr hohen Kopplung entspricht. Hierbei werden sämtliche externen Typen gezählt, die entweder als Felder wie `OrderRepository`, `InventoryApi`, `PaymentApi`, `Email` und `Audit` oder als Parameter und Rückgabetypen wie `Order`, `Cart` und `Customer` auftreten.

In der Interpretation liefert der `OrderService` damit ein vermeintlich ideales LCOM4-Ergebnis. Bei genauerer Betrachtung entlarvt sich diese Kohäsion jedoch als künstlich erzwungen durch technische Querschnittsbelange wie Persistenz und Logging. Die fachlichen Kernaufgaben der Lagerverwaltung, Zahlung und des Mailversands sind eigentlich voneinander unabhängig, werden aber lediglich durch die gemeinsame Nutzung der Infrastrukturkomponenten im Graphen zusammengehalten.

Die eigentliche Problematik verdeutlicht der kritische **CBO-Wert von 8**, welcher weit über dem empfohlenen **Schwellenwert von 5** liegt und die Klasse objektiv als „Fat Service“ identifiziert. Da jede Methode die gesamte Last der Abhängigkeiten mit sich zieht, müssen für einen Unittest der Methode `reserve` alle **5 Felder** als Mocks bereitgestellt werden, obwohl ein Großteil davon für die jeweilige Methode funktional unbeteiligt bleibt.

Das SRP ist hier verletzt, da die Klasse mehrere fachlich unabhängige Änderungsgründe wie Logikänderungen bei der Zahlung, im Lager oder beim Mailversand in sich vereint. Diese Analyse zeigt deutlich, dass LCOM4 allein zur Diagnose dieser Problematik nicht ausreicht und die strukturellen Defizite erst durch den CBO-Wert entlarvt werden.

### 5.2 Service-Pattern (datenzentriert) – Aufgespalten nach Methode

Ein naheliegender Refactoringansatz besteht darin, die subjektive Definition des SRP aus Abschnitt 2 anzuwenden: *„Each software module should have one and only one reason to change"*. Da jede der drei Methoden des `OrderService` einen eigenständigen fachlichen Änderungsgrund repräsentiert — Reservierung, Zahlung und Stornierung — werden sie in drei separate Services überführt, wobei jeder Service genau eine Methode enthält.

```java
// Verantwortlichkeit: Bestellung anlegen und Lager reservieren
public class OrderReservationService {

    private OrderRepository repository; // Feld 1
    private InventoryApi inventory;     // Feld 2
    private Email email;                // Feld 3
    private Audit audit;                // Feld 4

    public Order reserve(Cart cart, Customer customer) {
        // Cart (CBO +1), Customer (CBO +1), Order (CBO +1)
        inventory.reserve(cart);
        Order order = new Order(cart, customer);
        repository.save(order);
        email.sendReservation(order);
        audit.log("Order reserved: " + order.getId());
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
        // Order (CBO +1)
        payment.charge(order);
        order.markAsPaid();
        repository.save(order);
        email.sendConfirmation(order);
        audit.log("Payment processed: " + order.getId());
    }
}

// Verantwortlichkeit: Bestellung stornieren und Lager freigeben
public class OrderReleaseService {

    private OrderRepository repository; // Feld 1
    private InventoryApi inventory;     // Feld 2
    private Email email;                // Feld 3
    private Audit audit;                // Feld 4

    public void release(Order order) { // Order (CBO +1)
        inventory.release(order);
        order.markAsReleased();
        repository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
    }
}
```

In der praktischen Verwendung werden alle drei Services als eigenständige Komponenten instanziiert.

```java
OrderReservationService reservationSvc = new OrderReservationService(repository, inventoryApi, email, audit);
OrderPaymentService      paymentSvc    = new OrderPaymentService(repository, paymentApi, email, audit);
OrderReleaseService      releaseSvc    = new OrderReleaseService(repository, inventoryApi, email, audit);

Order order = reservationSvc.reserve(cart, customer);
paymentSvc.process(order);
releaseSvc.release(order);
```

Die LCOM4-Herleitung ergibt für alle drei Klassen per Definition einen Wert von **1**, da eine Klasse mit genau einer Methode mathematisch nicht in mehrere Graphen zerfallen kann. Diese Kohäsion ist damit mathematisch trivial und liefert keinen qualitativen Erkenntnisgewinn.

Die CBO-Ermittlung ergibt für `OrderReservationService` einen **CBO-Wert von 7** (4 Felder + `Cart`, `Customer`, `Order` aus der Signatur). `OrderPaymentService` und `OrderReleaseService` kommen jeweils auf einen **CBO-Wert von 5** (4 Felder + `Order` aus der Signatur).

In der Interpretation wird deutlich, dass die Aufspaltung nach subjektiven Änderungsgründen zwar die Klassengröße reduziert, das Kernproblem jedoch vollständig erhält. Technische Querschnittsbelange wie `Audit`, `Email` und `OrderRepository` sind nun in alle drei Services hineinkopiert. Eine Änderung der Loggingstrategie erfordert Modifikationen an drei verschiedenen Stellen. Zudem teilen `OrderReservationService` und `OrderReleaseService` dieselbe `InventoryApi`-Abhängigkeit, was auf einen fachlichen Zusammenhang dieser Operationen hinweist — ein Indiz, das im folgenden Abschnitt aufgegriffen wird.

Das SRP ist hier im Sinne der subjektiven Definition formal eingehalten, da jede Klasse genau eine Methode besitzt. Die Metrikanalyse zeigt jedoch, dass diese Aufteilung keine strukturelle Verbesserung gegenüber dem Monolithen bringt: Die Kopplung bleibt hoch, und die Querschnittsbelange werden lediglich repliziert statt isoliert.

### 5.3 Service-Pattern (datenzentriert) – Aufgespalten nach fachlichem Zusammenhang

Im Gegensatz zur rein methodenbasierten Aufteilung aus Abschnitt 5.2 orientiert sich dieser Ansatz am fachlichen Zusammenhang der Operationen. Da `reserve()` und `release()` beide auf der `InventoryApi` operieren und damit dieselbe Domäne der Lagerverwaltung betreffen, werden sie in einem gemeinsamen `OrderStockService` zusammengefasst. Die Zahlung bildet als eigenständige fachliche Verantwortlichkeit den `OrderPaymentService`. Diese Gruppierung entspricht eher dem Prinzip *„Gather together the things that change for the same reasons"* (vgl. Abschnitt 2), da die beiden Methoden des `OrderStockService` denselben fachlichen Änderungsgrund teilen.

Ein naheliegender Refactoringschritt besteht darin, den ursprünglichen „Fat Service“ in zwei spezialisierte Klassen aufzuteilen, die jeweils einen fachlichen Verantwortungsbereich abbilden.

```java


// Verantwortlichkeit: Lagerverwaltung — Reservierung beim Anlegen und Freigabe beim Stornieren
public class OrderStockService {

    private OrderRepository repository; // Feld 1
    private InventoryApi inventory;     // Feld 2
    private Email email;                // Feld 3
    private Audit audit;                // Feld 4

    public Order reserve(Cart cart, Customer customer) {
        inventory.reserve(cart);
        Order order = new Order(cart, customer);
        repository.save(order);
        audit.log("Order reserved: " + order.getId());
        return order;
    }

    public void release(Order order) {
        inventory.release(order);
        order.markAsReleased();
        repository.save(order);
        email.sendCancellation(order);
        audit.log("Order cancelled: " + order.getId());
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

```
In der praktischen Verwendung werden nun der `OrderStockService` und der `OrderPaymentService` als eigenständige Komponenten instanziiert.

```java
OrderStockService stockSvc = new OrderStockService(orderRepository, inventoryApi, email, audit);
OrderPaymentService paymentSvc = new OrderPaymentService(orderRepository, paymentApi, email, audit);

// usage
Order order = stockSvc.reserve(cart, customer);
paymentSvc.process(order);
stockSvc.release(order);
```
Durch diese Dekonstruktion sinkt der CBO-Wert pro Klasse spürbar, da jede Einheit nur noch die Abhängigkeiten erhält, die sie für ihre dedizierte Aufgabe zwingend benötigt.

Die LCOM4-Herleitung ergibt für `OrderPaymentService` per Definition einen Wert von 1, da eine Klasse mit einer einzigen Methode mathematisch nicht in mehrere Graphen zerfallen kann. Der `OrderStockService` enthält dagegen zwei Methoden, die jedoch fachlich zusammengehören: Beide operieren auf der Lagerverwaltung, greifen auf dieselben Felder zu und bilden daher einen einzigen zusammenhängenden Graphen mit LCOM4 = 1.

Die Analyse der Metriken zeigt eine deutliche Verbesserung der Kopplungswerte. Der `OrderStockService` erreicht einen **CBO-Wert von 7**, da neben den vier Feldern (`OrderRepository`, `InventoryApi`, `Email`, `Audit`) auch die Signatur-Typen `Order`, `Cart` und `Customer` zählen. Der `OrderPaymentService` kommt auf einen **CBO-Wert von 5**, da seine Methode `process()` lediglich `Order` als zusätzlichen Typ einbringt. Obwohl die Klassen dadurch kleiner und im Unittest leichter zu handhaben sind, bleibt ein grundlegendes Problem bestehen.

In der Interpretation wird deutlich, dass technische Querschnittsbelange wie das Logging via `Audit` oder die Persistenz über das `OrderRepository` weiterhin über alle drei Klassen verteilt sind. Eine Änderung der Loggingstrategie oder eine Anpassung des `Repository`-Interface erfordert somit nach wie vor Modifikationen an drei verschiedenen Stellen im System. Die Verantwortlichkeiten sind zwar physisch auf separate Dateien verteilt, bleiben jedoch logisch in jede einzelne Operation „hineingeflochten“.

Das SRP wird hier zwar besser adressiert, ist aber nicht vollständig erfüllt, da die Klassen trotz ihrer trivialen Kohäsion unter einer hohen konzeptionellen Redundanz leiden.

### 5.4 Vertikales Dekorator-Pattern (objektorientiert)

Im Object-Oriented Design existieren verschiedene Strukturmuster (wie Adapter, Bridge oder Dekorator) und Verhaltensmuster (wie die Strategy). Dabei erweist sich das Dekoratormuster, insbesondere in Kombination mit anderen Entwurfsmustern, als besonders geeignet, um die strikte Einhaltung des Single Responsibility Principle (SRP) zu gewährleisten. Hierbei wird die Kernlogik in einer Basisklasse isoliert, während fachliche Funktionserweiterungen und technische Aspekte wie Logging oder Persistenz in separate Hüllen ausgelagert werden. Das Muster separiert Verantwortlichkeiten konsequent über Objektkomposition, sodass jede Klasse genau eine Aufgabe übernimmt. Die Querschnittsbelange entstehen hier durch das Umhüllen von Objekten und nicht durch das Anhäufen von Feldern innerhalb einer Klasse.

Das Basisinterface wird dabei schlicht als Order definiert, was einen rein fachlichen Begriff ohne das technische Suffix „Service“ darstellt. 

```java
// Basis Interface
public interface Order {
    String id();
    void process();
    void release();
}
```

Im neuen Entwurf wandeln sich die Methoden zu reinen Verhaltensaufforderungen. Die Abhängigkeiten zu den APIs werden direkt in die zuständigen Dekoratoren injiziert. Jeder Decorator erzeugt bei einer Zustandsänderung eine neue Instanz seiner selbst (oder der Kette), um dem funktionalen Charakter des Interfaces gerecht zu werden. Die Klasse `StoredOrder` nutzt hierfür zwei öffentliche Konstruktoren: Ein Konstruktor dient dem initialen Erstellen einer Bestellung unter automatischer Vergabe einer `UUID`, während der zweite Konstruktor es ermöglicht, eine bestehende Bestellung anhand ihrer `id` zu laden oder den `id`-Erhalt innerhalb der Kette bei Statusänderungen sicherzustellen.


```java
// Kern: Persistenz — Repository eingekapselt, ID im Konstruktor
public class StoredOrder implements Order {

    private String id;             // Feld 1
    private Cart cart;             // Feld 2
    private OrderRepository repo;  // Feld 3

    // Konstruktor 1: Initiales Anlegen einer neuen Bestellung (generiert UUID)
    public StoredOrder(Cart cart, OrderRepository repo) {
        this(UUID.randomUUID().toString(), cart, repo);
    }

    // Konstruktor 2: Laden einer bestehenden Bestellung oder ID-Erhalt bei Statusänderung
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
    public void release() {
        repo.updateStatus(this.id, "CANCELLED");     // → Feld 3, 1
    }
}

// Vertikaler Dekorator: Zahlung — injiziert PaymentApi, verwendet es in process()
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
    public void release() { delegate.release(); }   // → Feld 1

}

// Vertikaler Dekorator: Lagerverwaltung — injiziert InventoryApi, verwendet es in release()
public class StockedOrder implements Order {

    private Order delegate;        // Feld 1
    private InventoryApi inv;      // Feld 2

    public StockedOrder(Order delegate, InventoryApi inv) {
        this.delegate = delegate; this.inv = inv;
    }

    @Override
    public String id() { return delegate.id(); }   // → Feld 1

    @Override
    public void process() { delegate.process(); }  // → Feld 1
                              
    @Override
    public void release() {
        inv.release(delegate);                    // → Feld 2, 1
        delegate.release();                        // → Feld 1
    }
}

// Dekorator: Audit-Logging — beide Methoden betroffen
public class AuditingOrder implements Order {

    private Order delegate;        // Feld 1
    private Audit audit;           // Feld 2

    public AuditingOrder(Order delegate, Audit audit) {
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
    public void release() {
        delegate.release();                              // → Feld 1
        audit.log("Cancelled: " + delegate.id());       // → Feld 2, 1
    }
}

// NotifiedOrder: Gleiche Struktur wie AuditingOrder → LCOM4 = 1, CBO = 2
// Dekorator: Mailbenachrichtigung — beide Methoden betroffen
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
    public void release() {
        delegate.release();                              // → Feld 1
        email.sendCancellation(delegate.id());          // → Feld 2, 1
    }
}

```
In der praktischen Verwendung wird die gewünschte Funktionalität durch eine tiefe Schachtelung der Objekte wie `new NotifiedOrder(new AuditingOrder(...))` zusammengesetzt. Beim Aufruf von `order.process()` durchläuft die Anfrage die gesamte Kette, wobei jede Schicht ihren spezifischen Beitrag, von der Zahlung über die Persistenz bis hin zum Logging und dem Mailversand, leistet.

```java

// bestehenden Bestellung via id bearbeiten
Order order = new NotifiedOrder(
    new AuditingOrder(
        new StockedOrder(
            new PaidOrder(
                new StoredOrder(id, cart, repo), // optional neue Bestellung ohne id bearbeiten
                paymentApi),
            inventoryApi),
        audit),
    email);

// Nutzung: reservieren und bearbeiten

order.process();
// Ablauf: NotifiedOrder → AuditingOrder → StockedOrder → PaidOrder → StoredOrder
// PaidOrder zieht Zahlung ein, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Bestätigungsmail.

order.release();
// Ablauf: NotifiedOrder → AuditingOrder → StockedOrder → PaidOrder → StoredOrder
// StockedOrder gibt Lagerbestand frei, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Stornierungsmail.
```

Die Messung der Kennzahlen ergibt ein beeindruckendes Bild der Entkopplung. Jede beteiligte Klasse weist einen **CBO-Wert von 2** sowie einen **LCOM4-Wert von 1** auf. Diese Ergebnisse sind kein Zufallsprodukt, sondern fachlich im Entwurf begründet. So benötigt die `StoredOrder` exakt zwei Felder, da die Persistenz zwangsläufig die zu speichernde Entität sowie das entsprechende Werkzeug in Form des Repositories voraussetzt. Analog dazu kombiniert die `PaidOrder` das Zielobjekt mit der benötigten Infrastruktur der Zahlungsschnittstelle.

Dasselbe Prinzip gilt für jede weitere Klasse in diesem Entwurf. Im Gegensatz zu den zuvor betrachteten aufgespaltenen Services erscheinen Komponenten wie `Audit` oder `OrderRepository` hier jeweils nur in einer einzigen, dedizierten Klasse. Eine Änderung an der Loggingstrategie erfordert daher lediglich die Anpassung der `AuditingOrder`, während eine Änderung am `Repository`-Interface ausschließlich die `StoredOrder` betrifft. Da kein anderer Codeteil von diesen Anpassungen beeinflusst wird, werden die Querschnittsbelange nicht über das System verteilt, sondern vollständig innerhalb ihrer jeweiligen Verantwortlichkeit isoliert.

Dieser Entwurf erfüllt zugleich das *Open-Closed-Prinzip*: Soll eine neue Anforderung, etwa ein SMS-Versand nach erfolgreicher Bezahlung, ergänzt werden, genügt eine neue Dekoratorklasse `SmsOrder`. Der bestehende Code bleibt unberührt, da die neue Klasse lediglich in die Kompositionskette eingehängt wird. Jede Erweiterung erfolgt durch Hinzufügen, nicht durch Ändern.

### 5.5 Horizontales Dekorator-Pattern (objektorientiert)

Weil der vertikale Dekoratorentwurf mit zunehmender Anzahl an Komponenten an Übersichtlichkeit verliert, schlägt Yegor Bugayenko (2015) einen horizontalen Ansatz vor. Hierbei verwaltet ein zentrales `Wrapper`-Objekt namens `Orders` eine flache Liste von Transformationen, die in einem separaten Interface als `OrderAction` definiert sind. Anstatt einer tiefen Verschachtelung erfolgt die Ausführung durch eine einfache Iteration über alle registrierten Prozessschritte, wobei jede `OrderAction`-Klasse eine spezifische technische oder fachliche Aufgabe isoliert.

```java

public interface OrderAction {
    void process(String id, Cart cart);
    void release(String id, Cart cart);
}

public class Orders implements Order {
    private String id;                  // Feld 1
    private Cart cart;                  // Feld 2
    private List<OrderAction> acts;        // Feld 3

    public Orders(String id, Cart cart, List<OrderAction> acts) {
        this.id = id; this.cart = cart; this.acts = acts;
    }

    @Override
    public String id() { return this.id; }

    @Override
    public void process() {
        acts.forEach(a -> a.process(this.id, this.cart)); // → Feld 3, 1, 2
    }

    @Override
    public void release() {
        acts.forEach(a -> a.release(this.id, this.cart));  // → Feld 3, 1, 2
    }
}

// Die Implementierungen der Verantwortlichkeiten
// Jede OrderAction-Klasse isoliert eine spezifische technische oder fachliche Aufgabe:

// Persistenz
public class Persist implements OrderAction {
    private OrderRepository repo;
    public Persist(OrderRepository repo) { this.repo = repo; }

    @Override public void process(String id, Cart cart) { repo.updateStatus(id, "PAID"); }
    @Override public void release(String id, Cart cart) { repo.updateStatus(id, "CANCELLED"); }
}

// Zahlung (nur process() relevant)
public class Pay implements OrderAction {
    private PaymentApi gateway;
    public Pay(PaymentApi gateway) { this.gateway = gateway; }

    @Override public void process(String id, Cart cart) { gateway.charge(id); }
    @Override public void release(String id, Cart cart) { /* leer */ }
}

// Lagerverwaltung (nur release() relevant)
public class Stock implements OrderAction {
    private InventoryApi inv;
    public Stock(InventoryApi inv) { this.inv = inv; }

    @Override public void process(String id, Cart cart) { /* leer */ }
    @Override public void release(String id, Cart cart) { inv.release(cart); }
}

// Weitere Klassen wie 'Audit' (Logging) und 'Notify' (Email) folgen derselben Struktur.

```

In der praktischen Verwendung zeigt sich die Komposition im Vergleich zur vertikalen Kette als flach und gleichrangig. Während das vertikale Dekorieren oft den einfacheren Einstieg darstellt, erweist sich die horizontale Variante als die skalierbarere Form für komplexe Systeme. Ein Aufruf von `order.process()` führt dazu, dass der Wrapper sequenziell über alle Implementierungen iteriert, wodurch die Persistenz den Status setzt, die Zahlung eingezogen wird und abschließend Protokollierung sowie Benachrichtigung erfolgen.

```java
Order order = new Orders(id, cart, List.of(
    new Persist(repo),
    new Pay(paymentApi),
    new Stock(inventoryApi),
    new Audit(audit),
    new Notify(email)
));

order.process(); 
// Orders iteriert: Persist setzt Status, Pay zieht Zahlung ein, 
// Audit protokolliert, Notify sendet Bestätigung.
```

Die Analyse der Metriken verdeutlicht die strukturellen Vorteile dieses Modells. Die `OrderAction`-Klassen erreichen einen **CBO-Wert von 2**, da neben dem eigenen Werkzeug (z. B. `PaymentApi`) auch der Interface-Typ `Cart` aus der Methodensignatur als externer Typ zählt. Dies stellt eine weitere Reduktion gegenüber den vertikalen Dekoratoren dar, da kein `delegate`-Feld mehr zur Weiterreichung der Aufrufe benötigt wird, da die Steuerung der Kette vollständig auf den `Orders`-Wrapper übergeht. Querschnittsbelange wie Audit oder Persistenz bleiben dabei strikt in jeweils einer Klasse isoliert, während neue Anforderungen wie ein SMS-Versand einfach als neue Implementierung hinzugefügt werden können, ohne bestehenden Code zu berühren, was dem **Open-Closed-Prinzip** entspricht.

Ein struktureller Nebeneffekt dieses Patterns sind die leeren Methoden in Klassen wie `Pay` oder `Stock`, da nicht jeder Prozessschritt zwangsläufig auf jede Aktion reagieren muss. Das Akzeptieren dieser leeren Implementierungen stellt den notwendigen Preis für den sauberen horizontalen Schnitt dar, bedeutet jedoch gleichzeitig einen Verstoß gegen das [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). Nach diesem Prinzip sollte eine Unterklasse so konzipiert sein, dass sie ihre Basisklasse vollständig ersetzen kann, ohne das Programmverhalten durch unerwartete Leerschritte oder eingeschränktes Verhalten zu verfälschen.

Alternativ ließe sich das `OrderAction`-Interface im Sinne des **Interface Segregation Principle (ISP)** in spezialisierte Schnittstellen wie `OnProcess` und `OnRelease` aufteilen.

```java
// Zwei segregierte Schnittstellen anstelle eines gemeinsamen Interfaces
public interface OnProcess {
    void process(String id, Cart cart);
}

public interface OnRelease {
    void release(String id, Cart cart);
}

// Der Wrapper akzeptiert beide Typen in getrennten Listen
public class Orders implements Order {
    private String id;
    private Cart cart;
    private List<OnProcess> onProcess; // Feld 1
    private List<OnRelease> onRelease; // Feld 2

    public Orders(String id, Cart cart,
                  List<OnProcess> onProcess, List<OnRelease> onRelease) {
        this.id = id; this.cart = cart;
        this.onProcess = onProcess; this.onRelease = onRelease;
    }

    @Override public String id() { return this.id; }

    @Override
    public void process() {
        onProcess.forEach(a -> a.process(this.id, this.cart));
    }

    @Override
    public void release() {
        onRelease.forEach(a -> a.release(this.id, this.cart));
    }
}

// Pay implementiert nur OnProcess — release() entfällt vollständig
public class Pay implements OnProcess {
    private PaymentApi gateway;
    public Pay(PaymentApi gateway) { this.gateway = gateway; }

    @Override public void process(String id, Cart cart) { gateway.charge(id); }
}

// Stock implementiert nur OnRelease — process() entfällt vollständig
public class Stock implements OnRelease {
    private InventoryApi inv;
    public Stock(InventoryApi inv) { this.inv = inv; }

    @Override public void release(String id, Cart cart) { inv.release(cart); }
}

// Persist reagiert auf beide Aktionen und implementiert daher beide Interfaces
public class Persist implements OnProcess, OnRelease {
    private OrderRepository repo;
    public Persist(OrderRepository repo) { this.repo = repo; }

    @Override public void process(String id, Cart cart) { repo.updateStatus(id, "PAID"); }
    @Override public void release(String id, Cart cart) { repo.updateStatus(id, "CANCELLED"); }
}
```

Die Komposition übergibt `Persist` in beide Listen, während `Pay` und `Stock` ausschließlich in die jeweils passende Liste eingetragen werden:

```java
Persist persist = new Persist(repo);

Order order = new Orders(id, cart,
    List.of(persist, new Pay(paymentApi)),   // nur OnProcess
    List.of(persist, new Stock(inventoryApi)) // nur OnRelease
);
```

Diese Variante ist LSP-konform, da keine Klasse mehr eine Methode implementiert, die für ihre Verantwortlichkeit nicht relevant ist. Der Preis dafür ist die gestiegene Komplexität des `Orders`-Wrappers, der nun zwei separate Listen verwaltet, sowie die Notwendigkeit, Klassen wie `Persist` explizit in beide Listen einzutragen. So zeigt sich an dieser Stelle ein klassischer Zielkonflikt zwischen einer flachen, skalierbaren Struktur und der strikten Einhaltung aller SOLID-Prinzipien.

## 6. Gegenüberstellung

Die Wahl eines Softwaredesigns stellt stets eine Abwägung zwischen der initialen Entwicklungsgeschwindigkeit und den langfristigen Wartungskosten (Total Cost of Ownership) dar. Ein direkter Vergleich der fünf Ansätze zeigt die strukturelle Evolution von der monolithischen Bündelung hin zur granularen Entkopplung. Die Tabelle veranschaulicht die systematische Verschiebung der Qualitätsparameter über die verschiedenen Entwurfsstufen hinweg. Die CBO-Werte sind dabei je Klasse angegeben und basieren auf einer vollständigen Zählung aller externen Typen aus Feldern und Methodensignaturen (Parameter und Rückgabetypen), wie in Abschnitt 4.2 definiert. Der Testaufwand bezieht sich auf die Anzahl der zu mockenden Felder (injizierte Abhängigkeiten) pro Klasse.

| Merkmal | Service (Monolith) | Services (nach Methode) | Services (nach Fachlichkeit) | Vertikaler Dekorator | Horizontaler Dekorator |
|---|---|---|---|---|---|
| **Fachliche Kohäsion** | ❌ LCOM4 = 1 (erzwungen) | ⚠️ LCOM4 = 1 (trivial) | ⚠️ LCOM4 = 1 (trivial) / ✅ LCOM4 = 1 (echt) | ✅ LCOM4 = 1 (fachlich) | ✅ LCOM4 = 1 (fachlich) |
| **Kopplung (CBO)** | ❌ CBO = 8 | ⚠️ CBO = 7 / 5 je Klasse | ⚠️ CBO = 7 (`StockSvc`) / 5 (`PaySvc`) | ✅ CBO = 2 je Klasse | ✅ CBO = 2 je Klasse |
| **Änderungsausbreitung** | ❌ gesamte Klasse | ❌ alle 3 Klassen | ❌ mehrere Klassen | ✅ nur 1 Klasse | ✅ nur 1 Klasse |
| **Testaufwand (Mocks)** | ❌ 5 Mocks pro Methode | ⚠️ 4 Mocks pro Klasse | ⚠️ 4 Mocks pro Klasse | ✅ 2 Mocks pro Klasse | ✅ 1 Mock pro Klasse |
| **Erweiterbarkeit (OCP)** | ❌ Methoden ändern | ❌ Methoden ändern | ❌ Methoden ändern | ✅ neuer Dekorator | ✅ neues `OrderAction` + Listeneintrag |
| **Komposition** | ✅ eine Klasse | ✅ drei Klassen | ✅ zwei Klassen | ⚠️ tiefe Kette | ✅ flache Liste |
| **Strukturelle Risiken** | ⚠️ Fat Service, hohe Kopplung | ⚠️ maximale Redundanz | ⚠️ Redundanz, hohe Streuung | ⚠️ Klassen-Facefragilität | ⚠️ LSP-Verletzung oder Interface-Fragilität  |

**Der datenzentrierte Service-Schnitt**

Der klassische `OrderService` bündelt sämtliche Belange der Bestellung innerhalb einer einzigen Klasse. Ein wesentlicher Vorteil dieses Ansatzes ist die zentrale Anlaufstelle, da die gesamte Geschäftslogik einer Domäne an einem Ort als verlässliche Informationsquelle konzentriert bleibt. Zudem ermöglicht diese Struktur ein lineares Debugging, bei dem der Kontrollfluss innerhalb einer Datei leicht nachvollziehbar ist, ohne dass zwischen vielen kleinen Klassen gesprungen werden muss. Da dieses Muster als Industriestandard gilt, bietet es zudem eine geringe kognitive Einstiegshürde für neue Entwickler.

Demgegenüber stehen jedoch signifikante Nachteile, da die hohe Kopplung dazu führen kann, dass Änderungen an technischen Komponenten wie dem Logging unvorhersehbare Seiteneffekte auf die Fachlogik haben. Auch die Testbarkeit wird erschwert, weil jede Methode eine hohe Anzahl an Mocks erfordert, was Unittests schwerfällig und wartungsintensiv gestaltet. Schließlich entsteht bei Änderungen eine hohe kognitive Last, da stets die gesamte Komplexität der Klasse durchdrungen werden muss, um selbst isolierte Stellen sicher anzupassen.

Die Aufspaltung nach der subjektiven Änderungsgrundlogik (`OrderReservationService`, `OrderPaymentService`, `OrderReleaseService`) maximiert zwar die Granularität, verschärft das Grundproblem jedoch weiter. Jeder der drei Services trägt dieselben vier Querschnittsabhängigkeiten (`OrderRepository`, `Email`, `Audit` und die jeweilige fachliche API), wodurch die Redundanz gegenüber dem Monolithen noch zunimmt. `OrderReservationService` und `OrderReleaseService` teilen zudem die `InventoryApi`, was zeigt, dass die Aufteilung den fachlichen Zusammenhang dieser Operationen missachtet. Die Kopplung sinkt für zwei der drei Klassen zwar auf CBO = 5, bleibt aber strukturell identisch belastet.

Die Zerlegung nach fachlichem Zusammenhang (`OrderStockService` und `OrderPaymentService`) ist ein Schritt in die richtige Richtung. Ein wesentlicher Vorteil liegt in der verbesserten Übersicht, da die Klassen kleiner werden und sich auf spezifische Teilprozesse fokussieren. Der `OrderStockService` fasst die beiden Lager-Operationen korrekt zusammen, da sie denselben fachlichen Änderungsgrund teilen. Zudem begünstigt dieser Ansatz die Parallelisierung, weil verschiedene Teams zeitgleich an unterschiedlichen Services arbeiten können.

Dem stehen jedoch Nachteile gegenüber, da technische Querschnittsbelange wie `Audit` oder `OrderRepository` weiterhin in jeden Service injiziert werden müssen. Dies führt zu einer verteilten Wartung, bei der globale Änderungen am Loggingformat an mehreren Stellen gleichzeitig nachgezogen werden müssen. Letztlich bleibt eine echte Isolation aus, da die strukturelle Vermischung von technischer Infrastruktur und Domänenlogik bestehen bleibt.

**Der OOD-Dekorator-Schnitt**

Dieser Ansatz schneidet die Domäne streng nach Verantwortlichkeiten in Klassen wie `StoredOrder` oder `PaidOrder`. Im Unterschied zur objektorientierten Programmierung (OOP), die den Sprachrahmen beschreibt, bezeichnet *Object-Oriented Design* (OOD) die Entwurfsdisziplin, in der Verantwortlichkeiten strukturell auf Objekte verteilt werden. Die Vorteile liegen in der strikten Einhaltung von **SRP** und Open-Closed-Prinzip, da neue Anforderungen durch zusätzliche Dekoratoren gelöst werden, ohne stabilen Code zu gefährden. Dies ermöglicht ein gezieltes Debugging, da Fehler im Logging garantiert in der `AuditingOrder` zu finden sind, während die kognitive Last auf die jeweils aktuelle Zuständigkeit begrenzt bleibt. Zudem wird eine minimale Kopplung erreicht, da jede Klasse lediglich das Interface und ihre spezifische Abhängigkeit kennt.

Nachteilig wirkt sich jedoch die Projektexplosion durch eine deutlich steigende Anzahl an Dateien und Konstruktoren aus. Auch die komplexe Komposition über tiefe Verschachtelungen wie `new PaidOrder(new StoredOrder(...))` ist gewöhnungsbedürftig. Das Muster begünstigt zwar die funktionalen Erweiterungen (Open-Closed-Prinzip), macht aber die Interfacestruktur zu einem kritischen Punkt, der bei Änderungen hohe Aufwände verursacht, da jede Änderung am zentralen `Order`-Interface Anpassungen in sämtlichen Dekoratoren erzwingt.

Das horizontale Muster optimiert zwar die Lesbarkeit bei tiefen Ketten, erzwingt jedoch bei einer größeren Anzahl von Methoden strukturelle Kompromisse in Form von leeren Implementierungen. Ein Wechsel zu diesem Modell ist daher nur dann ratsam, wenn das Design keine leeren Implementierungen von Methoden erfordert, sodass möglichst das *Liskov Substitution Principle* nicht verletzt wird.

## 7. Zusammenfassung und Handlungsempfehlung

Das SRP ist eines der einfachsten Prinzipien und gleichzeitig eines der am schwierigsten umzusetzenden. Die Wurzel dieses Widerspruchs liegt in seiner subjektiven Formulierung: Begriffe wie „Änderungsgrund", „Verantwortlichkeit" oder „Akteur" sind kontextabhängig und führen in Codereviews regelmäßig zu zeitraubenden Grundsatzdebatten, ohne zu einem objektiven Ergebnis zu gelangen.

Die von Robert Bräutigam vorgeschlagene Formalisierung überführt das Prinzip in eine messbare Strukturmetrik:

> `SRP ≡ max(COHESION) ∧ min(COUPLING)`

Diese Definition löst das philosophische Problem, indem sie SRP-Konformität an zwei gleichzeitig zu erfüllende, objektiv messbare Bedingungen knüpft: eine hohe interne Kohäsion und eine niedrige externe Kopplung. Erstere wird durch LCOM4 über eine Graphenanalyse der Methoden- und Feldbeziehungen bewertet, letztere durch CBO über das Zählen externer Typabhängigkeiten.

Der Vergleich der fünf Designvarianten illustriert, wie sich diese Bedingungen in der Praxis verhalten und gegenseitig bedingen.

Der **monolithische `OrderService`** zeigt, dass ein LCOM4-Wert von 1 trügerisch sein kann. Fünf fachlich voneinander unabhängige Verantwortlichkeiten, nämlich Lagerverwaltung, Zahlung, Mailversand, Persistenz und Protokollierung, werden lediglich über technische Querschnittsfelder wie `Audit` und `OrderRepository` im Graphen verbunden. Die scheinbar ideale Kohäsion ist damit nicht fachlich begründet, sondern ein Artefakt der gemeinsamen Infrastruktur. Der CBO-Wert von 8 entlarvt die eigentliche Problematik: Jede Methode schleppt die gesamte Last aller Abhängigkeiten mit sich, was Unittests aufwändig macht und die Klasse zu einem zentralen Änderungsrisiko werden lässt.

Die **Aufspaltung nach Methode** (`OrderReservationService`, `OrderPaymentService`, `OrderReleaseService`) demonstriert die Grenzen der subjektiven SRP-Definition. Das Prinzip *„one reason to change"* wird formal eingehalten, da jede Klasse exakt eine Methode besitzt. Metrisch ergibt sich jedoch kein Fortschritt: Alle drei Klassen tragen dieselben Querschnittsabhängigkeiten, die Redundanz steigt auf ihr Maximum, und `OrderReservationService` und `OrderReleaseService` teilen dieselbe `InventoryApi` — ein Hinweis, dass die Aufteilung den fachlichen Zusammenhang ignoriert. Die LCOM4-Werte von 1 sind mathematisch erzwungen und liefern keinen Erkenntnisgewinn.

Die **Aufspaltung nach fachlichem Zusammenhang** (`OrderStockService` und `OrderPaymentService`) ist ein konzeptueller Fortschritt, da die gemeinsame `InventoryApi`-Abhängigkeit von `reserve()` und `release()` korrekt als fachlicher Zusammenhang erkannt und gruppiert wird. Die Kopplung sinkt auf CBO = 7 (`OrderStockService`) bzw. 5 (`OrderPaymentService`). Das Kernproblem bleibt jedoch bestehen: Querschnittsbelange wie Logging und Persistenz werden lediglich in jede neue Klasse hineinkopiert, anstatt strukturell isoliert zu werden. Eine Änderung an der Loggingstrategie erfordert nach wie vor Eingriffe an mehreren Stellen.

Der **vertikale Dekorator** erreicht erstmals eine echte fachliche Isolation. Jede Klasse (`StoredOrder`, `PaidOrder`, `AuditingOrder`) kennt exakt zwei Abhängigkeiten: ihr Delegateobjekt und ihr spezifisches Werkzeug. Der CBO-Wert von 2 und der LCOM4-Wert von 1 sind hier nicht erzwungen, sondern fachlich im Entwurf begründet. Eine Änderung an der Persistenzlogik betrifft ausschließlich `StoredOrder`. Dieses Design erfüllt als erstes der vier Muster die Bräutigam-Formel vollständig.

Der **horizontale Dekorator** treibt die Entkopplung mit einem CBO-Wert von 2 pro Prozessklasse auf die strukturelle Spitze. Die flache Listenkomposition über `Orders` ist leichter verständlich als tiefe Verschachtelungen und erlaubt es, neue Anforderungen durch einen einzigen Listeneintrag zu ergänzen. Der Preis für diese Skalierbarkeit sind jedoch leere Methoden in Klassen wie `Pay` oder `Stock`, die das Liskov Substitution Principle verletzen. Dieses Spannungsfeld lässt sich durch eine Aufspaltung des `OrderAction`-Interfaces nach dem Interface Segregation Principle auflösen, was jedoch die Anzahl der Abstraktionen weiter erhöht.

### Handlungsempfehlungen für die Praxis

Die Erkenntnisse aus Theorie und Designvergleich lassen sich in vier konkreten Leitlinien zusammenfassen.

**Metriken gemeinsam betrachten, nie isoliert.** Ein LCOM4-Wert von 1 ist eine notwendige, aber keine hinreichende Bedingung für SRP-Konformität. Erst die Kombination beider Kennzahlen liefert ein verlässliches Bild. Die diagnostische Leitfrage lautet daher stets: „Warum sind diese Methoden fachlich verbunden: durch echte Domänenlogik oder durch technische Infrastruktur?" Ein **niedriger LCOM4 bei hohem CBO** ist ein zuverlässiges **Warnsignal** für einen „Fat Service". Der dabei anzuwendende **CBO-Schwellenwert ist nicht universell festgelegt** und variiert je nach Quelle zwischen 4 und 14. Da ein niedrigerer Wert die Systemfragmentierung erhöht, ein höherer hingegen das Testen durch mehr erforderliche Mocks erschwert, sollte der Grenzwert durch die Organisation oder das Team auf Basis dieser Abwägung explizit definiert werden.

**Kohäsion: Technische Brücken identifizieren und fachlich bewerten.** Felder wie ein `Logger`, eine `id` oder ein `Statusfeld` können im Kohäsionsgraphen Brücken zwischen fachlich fremden Verantwortlichkeiten schlagen. Fungieren solche Querschnittsfelder als einzige Verbindung zwischen Methodengruppen, deutet dies auf eine versteckte Verletzung des SRP hin. Die qualitative Bewertung dieser Verbindung hängt jedoch maßgeblich vom gewählten Entwurfsparadigma ab. 

* Aus der Sicht eines datenzentrierten Entwurfs, bei dem die Klasse als Datenbehälter fungiert, erscheint die Trennung nach technischen Änderungsgründen, wie die Auslagerung einer `display()`-Methode in eine UI-bezogene Klasse, als die strukturell geeignete Lösung. 
* Im Gegensatz dazu versteht der verhaltensorientierte Entwurf ein Objekt als autonome Einheit, die ihren Zustand vollständig kapselt. In dieser Perspektive gehört die Darstellung untrennbar zum Kern der Entität, da eine Auslagerung den Einsatz von Getter-Methoden erzwingen und das Prinzip **Tell, Don’t Ask** missachten würde.

Da die LCOM4-Metrik lediglich die Existenz einer Verbindung im Graphen bewertet und keine fachliche Einordnung vornimmt, bleibt es die Aufgabe des Entwicklers zu entscheiden, ob ein Zusammenhang eine technisch künstliche Brücke oder eine legitime fachliche Kapselung darstellt.

**Kopplung: Physikalische und semantische Abhängigkeiten trennen.** Ein **CBO-Wert größer als 5** signalisiert übermäßige Vernetzung. Als erste Maßnahme empfiehlt sich die Anwendung des **Dependency Inversion Principle**: Abhängigkeiten von konkreten Implementierungen werden durch stabile Interfaces ersetzt, was den CBO unmittelbar senkt. Zusätzlich sollte semantische Kopplung durch die konsequente Einhaltung des **Law of Demeter** und des **Tell-Don't-Ask-Prinzips** verhindert werden. Jede Methode, die auf den internen Zustand eines fremden Objekts zugreift, etwa über Aufrufketten wie `order.getCustomer().getAddress()`, erzeugt eine Abhängigkeit, die kein statisches Analysewerkzeug erfasst, aber bei Änderungen zu unerwarteten Fehlerfortpflanzungen führt.

**Entwurfsmuster wählen, nicht dogmatisch anwenden.** Das **Dekoratormuster** löst das Problem der verteilten Querschnittsbelange strukturell sauber und ist dem Service-Pattern in puncto SRP-Konformität deutlich überlegen. Es ist jedoch **kein universelles Allheilmittel**. Tiefe Dekoratorketten erhöhen die Komplexität der Objektkomposition und machen den Systemüberblick schwerer, während horizontale Varianten das **LSP** unter Druck setzen. Der **Service-Schnitt** bleibt ein **pragmatischer Standard** für einfache [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) Anwendungen oder Teams, deren Fähigkeiten noch nicht auf kompositionsbasierte OOD-Entwurfsmuster ausgerichtet sind, da die geringe kognitive Einstiegshürde und die zentrale Übersicht in diesen Kontexten überwiegen. Die Entscheidung für einen Entwurfsansatz sollte sich daher an der konkreten **Kompetenz des Entwicklungsteams** und den **Wartungskosten** orientieren.

Unter Beachtung dieser Leitlinien erweist sich das **SRP** nicht als starres Dogma, sondern als **pragmatisches Werkzeug**, das erst durch messbare Metriken konkrete Handlungsoptionen bietet. Nichtsdestotrotz sollten die **Lesbarkeit und Nachvollziehbarkeit** des Gesamtsystems stets stärker gewichtet werden als die einseitige Optimierung einer einzelnen Kennzahl, denn das übergeordnete Ziel bleibt die **langfristige Wartbarkeit** der Software.

---

## 8. Abschließende Betrachtung

Die hier vorgestellten Metriken und Entwurfsmuster stellen nur einen Ausschnitt des **objektorientierten Designs** dar. In der akademischen Literatur existieren zweifellos präzisere und mathematisch tiefergehende Modelle zur Messung von Kohäsion und Kopplung, welche weitere Nuancen der Softwarekomplexität erfassen.

Im Vordergrund dieses Beitrags stand jedoch das **Ziel**, einen **einfachen und pragmatischen Ansatz aufzuzeigen**. Da komplexe Formeln im **Praxisalltag** oft nur schwer anwendbar sind, ist ein **einfach bedienbares Instrument entscheidend**, das schnelle und fundierte Designentscheidungen unterstützt. Durch die Kombination der **LCOM4- und CBO-Metriken** mit bewährten **Design-Patterns** wurde ein Weg aufgezeigt, der das abstrakte **Single Responsibility Principle** in eine handhabbare Praxis überführt.

Letztlich ist Softwareentwicklung ein Handwerk, bei dem Metriken lediglich als Kompass dienen. Die Erfahrung und das Urteilsvermögen des Entwicklungsteams bilden dabei das entscheidende Element für nachhaltige Codequalität.

---


## 9. Quellen und Referenzen

### Hauptquelle
* ACM Digital Library (2026) [Applying the Single Responsibility Principle in Industry: Modularity
Benefits and Trade-offs](https://scispace.com/pdf/applying-the-single-responsibility-principle-in-industry-rewdq52at2.pdf)
* Robert Martin (2007) [The Single Responsibility Principle](https://drive.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/view?resourcekey=0-AbuGpXQzwZcUGExkktKt0g)
* Robert Martin (2014) [The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html) 
* Robert Bräutigam (2014) [The Single Responsibility Principle](https://speakerdeck.com/robertbraeutigam/single-responsibility-principle)
* Robert Bräutigam (2017) [The Genius of the Law of Demeter](https://javadevguy.wordpress.com/2017/05/14/the-genius-of-the-law-of-demeter/)
* Sahraoui, Godin & Miceli (2000) [Can Metrics Help Bridging the Gap Between the Improvement of OO Design Quality and Its Automation?](https://www.iro.umontreal.ca/~sahraouh/papers/ICSM00.pdf)
* Martin Fowler (2013) [Tell, Don’t Ask](https://martinfowler.com/bliki/TellDontAsk.html)
* Matthieu Cneude (2020) [The Single Responsibility Principle Revisited](https://thevaluable.dev/single-responsibility-principle-revisited/)
* Mihai A. RODEGBFR (2018) [My Take On Object Naming](https://amihaiemil.com/2018/01/07/my-take-on-object-naming.html)
* ObjectScript_Q (2026) [Lack of Cohesion in Methods (LCOM4)](https://objectscriptquality.com/docs/metrics/lack-cohesion-methods-lcom4)
* ObjectScript_Q (2026) [Coupling Between Object classes (CBO)](https://objectscriptquality.com/docs/metrics/coupling-between-object-classes-cbo)
* Yegor Bugayenko (2015) [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html)
* Yegor Bugayenko (2020) [Prefixed Naming](https://www.yegor256.com/2020/03/03/prefixed-naming.html)

### Weiterführende Artikel und Metriken

* Aivosto Oy Helsinki, Finland (1994–2021) [Chidamber & Kemerer Object-Oriented Metrics Suite](https://www.aivosto.com/project/help/pm-oo-ck.html)
* European Southern Observatory (2026) [A METRICS SUITE FOR OBJECT ORIENTED DESIGN](https://www.eso.org/~tcsmgr/oowg-forum/TechMeetings/Articles/OOMetrics.pdf)
* Universität Turku (2016) [Design Principles And Patterns](https://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf)
* University of Vienna (1994) [Measuring Coupling and Cohesion In Object-Oriented Systems](http://www.isys.uni-klu.ac.at/PDF/1995-0043-MHBM.pdf) 


### Verwandte Bücher

- Martin, R. C. (2003). *Agile Software Development: Principles, Patterns, and Practices*. Prentice Hall.
- Martin, R. C. (2008). *Clean Code*. Prentice Hall.
- Martin, R. C. (2017). *Clean Architecture*. Prentice Hall.
- Gamma, E. et al. (1994). *Design Patterns*. Addison-Wesley. *(Decorator Pattern)*
- Evans, E. (2003). *Domain-Driven Design*. Addison-Wesley.
- Chidamber, S. R., & Kemerer, C. F. (1994). *A Metrics Suite for Object Oriented Design*. IEEE TSE 20(6).
- Hitz, M., & Montazeri, B. (1995). *Measuring Coupling and Cohesion in Object-Oriented Systems*.
