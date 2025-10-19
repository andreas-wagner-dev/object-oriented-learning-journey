
# **Objektorientiertes Package-Design: Eine Abkehr von der starren Schichtenarchitektur**

## **0. Das Problem mit der geschichteten Architektur (Clean/DDD)**

Traditionelle, geschichtete Architekturen, wie sie oft in der Clean Architecture oder im Domain-Driven Design (DDD) vorkommen, organisieren den Code nach technischen Schichten (Domain, Application, Infrastructure, Presentation). Obwohl diese Ansätze die Trennung von Belangen fördern sollen, führen sie in der Praxis häufig zu Problemen, die die Wartbarkeit und Entwicklung behindern.

Robert Bräutigam beschreibt in seinen Blog-Artikeln die Kernprobleme:

* **Falsche Abstraktionen durch Daten-Grenzen:** Die ständige Übertragung von Daten über Schichtgrenzen hinweg (User -> UserDTO -> UserViewModel) erzeugt unnötigen Code und lässt die ursprüngliche, fachliche Abstraktion zerfallen. Jede Schicht muss die Daten für ihren eigenen Zweck transformieren, was zu starren und wartungsaufwändigen Systemen führt.  
* **Schlechte Kommunikation über die Architektur:** Eine geschichtete Architektur beschreibt die technischen Abhängigkeiten, nicht die fachlichen. Ein neuer Entwickler muss erst die gesamte Architektur verstehen, bevor er sich in die fachliche Logik einarbeiten kann.  
* **Verlust des objektorientierten Charakters:** Indem die Daten von der Logik getrennt und über Schichtgrenzen hinweg transportiert werden, verliert die Anwendung ihren objektorientierten Charakter. Am Ende entsteht oft prozeduraler Code, der um Datentransferobjekte (DTOs) herum aufgebaut ist.

Im Rahmen der Erstellung des Blogs "Objekt-orientiertes Package Design" konzentriert sich dieser Abschnitt auf den ersten Schritt: die Einführung in eine alternative Methode der Paketierung, die sich von der traditionellen Schichtenarchitektur (wie in Clean Architecture oder DDD) abgrenzt. Der Ansatz basiert auf den drei Regeln von Robert Bräutigam, die eine konsequent fachliche und objektorientierte Sichtweise in den Vordergrund stellen.

## **1. Lösung: Objektorientiertes Package Design**

Die in vielen Projekten übliche Aufteilung in technische Schichten (entity, repository, controller, port, adapter, data, client, service, converter, baen oder mapper führt zu starren Strukturen und zirkulären Abhängigkeiten, wie im vorigen Abschnitt dargelegt.

Ein objektorientiertes System basiert auf OO Paket Design Prinzipien. Dessen **Herzstück** ist das **Root-Paket**, das als die stabile, fachliche API der gesamten Anwendung fungiert. Ziel ist es, durch Komposition sowie Kapselung von Details, lose gekoppelte, wartbare und vor allem fachlich klare Strukturen zu schaffen.

Es gibt keine Schichten im traditionellen Sinne von Clean Architecture oder DDD. Stattdessen werden Pakete hierarchisch nach fachlichen Konzepten organisiert. Die Abhängigkeiten fließen von spezifischen Details zu allgemeinen Abstraktionen, wodurch eine saubere und nachvollziehbare Struktur entsteht.

**Das Ausgangsbeispiel:**

com.example.todo/  
├── api/                       (Paket für die externe API-Schnittstelle)  
│   ├── envelop/               (Paket zum Envelop Interface zum Umhüllen von API I/O-Klassen, verwendet in Paketen resource, media, message oder service)  
│   │   ├── WrappedRequest.java   
│   │   └── WrappedResponse.java  
│   ├── media                  (Paket zum Media Interface)  
│   │   └── TodoHype.java      (JAX-RS)  
│   ├── message                (Paket zum Message Interface )  
│   │   └── NotifiedUser.java  (z. B. mit Kafka)  
│   ├── resource/              (Paket zum Resource Interface)  
│   │   └── TodoResource.java  (JAX-RS)  
│   ├── service/               (Paket zum Service Interface)  
│   │   └── TodoService.java   (JAX-WS)  
│   ├── Envelop.java           (Interface)  
│   ├── Media.java             (Interface - Kunden Begriff)  
│   ├── Message.java           (Interface  - Kunden Begriff)  
│   ├── Resource.java          (Interface  - Kunden Begriff)  
│   └── Service.java           (Interface  - Kunden Begriff)  
├── app/                       (Paket für App-Realisierungen)  
│   ├── TodoApp.java  
│   ├── ConsoleApp.java  
│   ├── ResourceApp.java       (JAX-RS application initialisierung)  
│   └── WebApp.java  
├── folder/                    (Paket für Folder Realisierungen)  
│   ├── DbFolder.java          (Injects DataSource.java)    
│   └── JsonFolder.java  
├── task/                      (Paket für  Task Realisierungen)  
│   ├── DbTask.java            (Injects javax.sql.DataSource)   
│   └── JsonTask.java  
├── user/                      (Paket für User Realisierungen)  
│   ├── component/             (Paket für wiederverwendbare UI-Steuerelemente)  
│   │   ├── ... (Eigene UI Komponenten oder Umhüllen von Framework UI-Komponenten)  
│   │   └── ...  
│   ├── page/                  (Paket zum Page Interface, für User-spezifische Seiten )  
│   │   ├── ... (Eigene View mit Controller Klassen)  
│   │   └── ...  
│   ├── Component.java  
│   ├── Page.java  
│   ├── DbUser.java           (Injects javax.sql.DataSource)   
│   └── JsonUser.java  
├── App.java                   (Interface - zum Umhüllen der gasamten Anwendung)   
├── Db.java                    (Interface - Kunden Begriff - Datenbank)      
├── Folder.java  
├── Task.java  
└── User.java

Die Grundlage dieses Ansatzes sind die drei Regeln von Robert Bräutigam, die eine lesbare und wartbare Paketstruktur fördern:

### **Regel 1: "Packages should never depend on sub-packages."**

Diese Regel besagt, dass ein übergeordnetes Paket keine Abhängigkeiten zu seinen eigenen Unterpaketen haben soll. Das zugrundeliegende Prinzip ist die Abhängigkeitsumkehr (Dependency Inversion). Ein Paket, das eine höhere Abstraktionsebene darstellt, darf keine Kenntnis von den konkreten Implementierungsdetails in den darunter liegenden Paketen haben.

* **Implikation:** Abhängigkeiten fließen immer von den spezifischeren (Unterpakete) zu den abstrakteren (übergeordneten Paket). Dies stellt sicher, dass die High-Level-Abstraktionen stabil bleiben, unabhängig von Änderungen in den Low-Level-Implementierungen.  
* **Anwendung:** In unserer todo-Anwendung fungiert das Root-Paket com.example.todo als die **Anwendungs-API** selbst. Es ist das **Herzstück der Architektur** und enthält die stabilen, hochgradigen Abstraktionen (Kern-Interfaces wie App, User, Folder, Task). Alle Unterpakete (app, user, folder, task) implementieren oder nutzen diese Interfaces, wodurch eine strikte Abhängigkeitsumkehr gewährleistet wird. Das Root-Paket bleibt somit unabhängig von den Implementierungsdetails.

### **Regel 2: "Sub-packages should not introduce new concepts, just more details."**

Diese Regel stellt sicher, dass die Paketstruktur eine klare Hierarchie von Abstraktionen darstellt. Ein Unterpaket sollte keine fachlichen Konzepte einführen, die in keinem Zusammenhang mit dem übergeordneten Paket stehen. Stattdessen sollten Unterpakete nur spezifischere Details, Implementierungen oder Spezialisierungen des übergeordneten Pakets enthalten.

* **Implikation:** Ein Entwickler kann die Anwendung verstehen, indem er die Pakete von oben nach unten durchgeht. Auf jeder Ebene gewinnt er ein vollständigeres Bild, ohne überraschende, neue Konzepte in den Unterpaketen zu finden.  
* **Anwendung:** Das user-Paket enthält alles, was für das User-Konzept relevant ist. Die Unterpakete control und page führen keine neuen fachlichen Konzepte ein, sondern bieten spezifischere Details zur Implementierung der Benutzeroberfläche für das User-Konzept. Die technischen Implementierungen wie DbUser sind ebenfalls Details, die das User-Konzept konkretisieren.

### **Regel 3: "Packages should reflect business-concepts, not technical ones."**

Diese Regel ist der Schlüssel zu einer objektorientierten Paketstruktur. Paketnamen sollten die Sprache des Geschäftes oder der Anwendung sprechen, nicht die der technischen Implementierung. Namen wie util, db, service, audit, configoder resource sind zu technisch und Pakete wie sql, sass, jsf, jooq, jpa, jms, http, jwt, xmloder kafka verraten die Implementierungsdetails.

* **Implikation:** Eine Paketstruktur, die sich an fachlichen Konzepten orientiert, ist für Stakeholder und Entwickler gleichermaßen verständlich. Sie macht die Struktur robuster gegen technologische Änderungen.  
* **Anwendung:** In unserer Struktur haben wir Pakete wie user, folder und task, die fachliche Konzepte darstellen. Pakete wie control und page repräsentieren ebenfalls fachliche Aspekte (Benutzersteuerung und -ansichten). Selbst die konkreten Implementierungen wie DbFolder oder JsonFolder sind in die fachlichen Pakete eingebettet, ohne dass der Paketname die Technologie verrät.

Diese drei Regeln bilden das Fundament für ein robustes, wartbares und vor allem fachlich klar strukturiertes Package Design, das den Prinzipien der Objektorientierung treu bleibt und sich von den starren Schichtmodellen abhebt.

## **2. Zusätzliche Aspekte für eine reale Fullstack-Anwendung**

Eine reale Fullstack-Anwendung besteht aus mehr als nur den grundlegenden fachlichen Konzepten. Die Integration weiterer Aspekte wie Anwendungskonfiguration, Benutzeroberfläche, Datenbankpersitenz, API-Schnittstellen und Hilfsklassen erfordert eine sorgfältige Berücksichtigung der objektorientierten Paketierungsregeln. Im Folgenden werden diese Aspekte unter Berücksichtigung der fachlichen Perspektive und der Vermeidung technischer Paketnamen betrachtet.

### **Initialisierung, Dependency Injection und Konfiguration (app/)**

* **Fachliche Begründung:** Die "App" ist aus fachlicher Sicht der zentrale Einstiegspunkt der Anwendung. Sie ist das Konzept, das alle anderen fachlichen Bereiche orchestriert und miteinander verbindet. Die Konfiguration und Initialisierung sind aus dieser Perspektive keine technischen Details, sondern Teil der Definition, wie die Anwendung als Ganzes funktioniert.  
* **Paketname:** app/. Dieser Name ist fachlich und beschreibt, was das Paket tut: Es ist der Kern der Anwendung.  
* **Inhalt:** TodoApp.java (Implementiert das zentrale App-Interface), ConsoleApp.java und WebApp.java (Spezifische Einstiegspunkte für verschiedene Anwendungsfälle).  
* **Keine DI-Container:** In strikter OOP-Manier wird auf DI-Container verzichtet. Die Konfiguration und die Injektion von Abhängigkeiten erfolgen an einem einzigen Ort, typischerweise im Einstiegspunkt (main-Methode), um das Objektdiagramm zu konstruieren.

### **Benutzeroberfläche (user/ im Kontext der User-Schnittstelle)**

* **Fachliche Begründung:** Die Benutzeroberfläche ist, wie bereits erörtert, aus der Sicht des Endanwenders ein zentrales Business-Konzept. Der Nutzer interagiert mit Formularen, Seiten und Steuerelementen, und die Paketstruktur sollte diese Sprache widerspiegeln.  
* **Paketname:** user/. Das Paket ist dem fachlichen Konzept "User" gewidmet und kapselt alles, was mit der Benutzerverwaltung und -interaktion zu tun hat.  
* **Inhalt:** control/ (Wiederverwendbare Steuerelemente), page/ (Vollständige Seitenansichten).

### **Datenbank-Persistenz (Decorator)**

* **Fachliche Begründung:** In einer streng objektorientierten Sichtweise ist die Persistenz ein internes Detail eines Objekts. Statt ein technisches db/-Paket zu haben, wird das Decorator-Entwurfsmuster verwendet, um ein Objekt mit Persistenzverhalten zu erweitern.  
* **Paketierung:** Die Persistenzlogik wird direkt in die fachlichen Pakete (folder/, task/, user/) integriert.  
* **Beispiel:** DbFolder.java (Decorator): Eine Klasse, die das Folder-Interface implementiert und ein anderes Folder-Objekt dekoriert, um es persistent zu machen. Die Details der Datenbankimplementierung (JDBC, ORM) sind innerhalb dieser Klasse gekapselt.

### **API-Schnittstelle (api/)**

* **Fachliche Begründung:** Eine API ist die Schnittstelle, über die externe Systeme (Kunden) mit der Anwendung kommunizieren. Sie ist das fachliche Konzept der externen Interaktion.  
* **Paketname:** api/. Dieser Name ist fachlich und beschreibt die externe Schnittstelle der Anwendung.  
* **Inhalt:** Schnittstellen (Resource.java, Message.java, etc.) und fachliche Unterpakete (todo/, user/), die die Implementierungen (JAX-RS-Ressourcen, Kafka-Nachrichten) gruppieren.

### **Hilfsklassen (Utilities)**

* **Fachliche Begründung:** In einer strikt objektorientierten Welt existieren keine statischen Utility-Klassen. Stattdessen wird wieder auf die Komposition oder das Decorator-Muster zurückgegriffen.  
* **Vermeidung von util/-Paketen:** Pakete wie util/ oder helper/ sind zu technisch und verstoßen gegen die Regel, nur fachliche Konzepte zu verwenden.  
* **Lösung:** Package-private Hilfsklassen oder wiederverwendbare Hilfslogik in einem lokalen Objekt, das per Komposition angefügt wird.

## **3. Beispielstruktur**

Dieses Kapitel veranschaulicht die drei Regeln anhand der Todo-Anwendung. Wir sehen, dass das **Root-Paket (com.example.todo/) das stabile Zentrum der Architektur** bildet, das nur die Kern-Abstraktionen bereitstellt, während alle Details in den Unterpaketen organisiert sind.

### **Die Beispielstruktur im Überblick**

com.example.todo/  
├── api/              (Paket für die externe API-Schnittstelle)  
│   ├── todo/         (Fachliches Paket für Todo-Ressourcen)  
│   │   ├── TodoResource.java  
│   │   └── TodoRepresentation.java  
│   ├── user/         (Fachliches Paket für User-Ressourcen)  
│   │   └── UserResource.java  
│   ├── Resource.java (Interface für API-Ressourcen)  
│   └── TodoService.java (Interface für Service-APIs)  
├── app/              (Paket für die App-Initialisierung)  
│   ├── TodoApp.java  (Implementiert das App-Interface)  
│   ├── ConsoleApp.java (Einstiegspunkt für die Konsole)  
│   ├── ResourceApp.java (Einstiegspunkt für die API)  
│   └── WebApp.java   (Einstiegspunkt für die Web-UI)  
├── folder/           (Paket für Folder-Realisierungen)  
│   ├── FileFolder.java (Decorator für Dateipersistenz)  
│   ├── JpaFolder.java  (Decorator für Jpa-Persistenz)  
│   └── ...  
├── task/             (Paket für Task-Realisierungen)  
│   ├── FileTask.java   (Decorator für Dateipersistenz)  
│   ├── JpaTask.java    (Decorator für Jpa-Persistenz)  
│   └── ...  
├── user/             (Paket für User-Realisierungen)  
│   ├── control/      (Paket für wiederverwendbare UI-Steuerelemente)  
│   │   ├── Button.java  
│   │   ├── Field.java  
│   │   └── Table.java  
│   ├── page/         (Paket für User-spezifische Seiten)  
│   │   ├── LoginPage.java  
│   │   └── ProfilePage.java  
│   ├── UserJpa.java  (Decorator für Jpa-Persistenz)  
│   └── FileUser.java (Decorator für Dateipersistenz)  
├── App.java          (Interface)  
├── User.java         (Interface)  
├── Folder.java       (Interface)  
└── Task.java         (Interface)

### **Erklärung der Struktur nach den 3 Regeln**

* **Regel 1:** Im obigen Beispiel hängen die Pakete der höchsten Ebene (com.example.todo) nicht von ihren Unterpaketen ab. Das **Root-Paket** enthält nur Interfaces, abstrakte Klassen oder Klassen ohne technische Abhängigkeiten. Die Unterpakete wie app/, api/, folder/ und user/ hängen von den Schnittstellen/Klassen im **Root-Paket** ab, nicht umgekehrt.  
* **Regel 2:** Das user/-Paket stellt das fachliche Konzept "Benutzer" dar. Die Unterpakete control/ und page/ führen keine neuen fachlichen Konzepte ein, sondern liefern spezifischere Details und Realisierungen für die Benutzeroberfläche (UI) des Users.  
* **Regel 3:** Die Namen der Pakete wie app/, user/, folder/ und task/ spiegeln direkt die Fachkonzepte der Todo-Anwendung wider. Pakete, die potenziell technische Aspekte abdecken (api/), werden so benannt, dass sie den fachlichen Zweck (externe Schnittstelle) widerspiegeln.

## **4. Allgemeine Vorgehensweise zum Erstellen von Paketen**

Nach dem objektorientierten Ansatz, der auf den Prinzipien von Robert Bräutigam basiert, ist die Paketstruktur nicht das Ergebnis einer mechanischen Trennung nach Schichten, sondern das Ergebnis eines bewussten Designprozesses.

1. **Schritt 1: Identifiziere die Kern-Fachkonzepte**  
   * Definieren Sie die zentralen, abstrakten Konzepte (Nomen) Ihrer Anwendung als Interfaces im Root-Paket (com.example.todo).  
2. **Schritt 2: Gruppiere Realisierungen in fachlichen Paketen**  
   * Erstellen Sie für jedes Kern-Fachkonzept ein separates, fachlich benanntes Paket auf der ersten Ebene (user/, folder/, task/).  
3. **Schritt 3: Kapsle technische Details in Unterpaketen oder Klassen**  
   * Verwenden Sie Unterpakete, um spezifischere Details zu gruppieren, aber benennen Sie diese nach ihrer fachlichen Funktion (control/, page/), nicht nach der Technologie.  
4. **Schritt 4: Verwende Dekoratoren oder Komposition für flexible Implementierungen**  
   * Vermeiden Sie technische Paketnamen für austauschbare Aspekte wie Persistenz. Nutzen Sie das Decorator-Entwurfsmuster (JpaFolder.java, FileFolder.java) direkt innerhalb der fachlichen Pakete.  
5. **Schritt 5: Behandle wiederverwendbare Hilfsobjekte als eigenständige Entitäten**  
   * Ersetzen Sie statische Utility-Klassen wie (FileIO.java, StringUtil.java),durch eigenständige Objekte (DataFile.java, ReplasedString.java), die per Komposition eingebunden werden. Ein Paket wie etwa util/ ist **kein** akzeptierter Kompromiss, ein absolutes “No Go“. Es sei denn, ein solches Paket steht im expliziten fachlichen Kontext der Anwendung, analog zu Paketen des Sprachkerns vom Java wie java.util/.  
6. **Schritt 6: Berücksichtige externe Schnittstellen als Business-Aspekte**  
   * Benennen Sie externe Schnittstellen als das fachliche Konzept, das sie repräsentieren (api/). Die technischen Details (JAX-RS) sind in den Implementierungsklassen versteckt.

## **5. Generisches Template für Pakete**

Dieses Template dient als Blaupause für die Organisation der Paketstruktur. Es ist modular aufgebaut, sodass nicht benötigte Pakete einfach weggelassen werden können.

com.<ihre_domain>.<ihr_projektname>/  
├── api/               (Paket für die externe API-Schnittstelle)  
│   ├── <fachliches_konzept1>/ (Paket für API-Ressourcen des ersten Konzepts)  
│   │   ├── <Fachkonzept1>Resource.java  
│   │   └── <Fachkonzept1>Representation.java  
│   ├── <fachliches_konzept2>/ (Paket für API-Ressourcen des zweiten Konzepts)  
│   │   └── <Fachkonzept2>Resource.java  
│   │── <Hilfsobjekt1>.java (Objekt für wiederverwendbare Logik, keine Statics)  
│   ├── Resource.java    (Interface für API-Ressourcen)  
│   └── TodoService.java (Interface für Service-APIs)  
├── app/               (Paket für die App-Initialisierung)  
│   ├── <MainApp>.java   (Implementiert das App-Interface)  
│   ├── <ConsoleApp>.java  (Einstiegspunkt für die Konsole)  
│   ├── <ResourceApp>.java (Einstiegspunkt für die API)  
│   └── <WebApp>.java    (Einstiegspunkt für die Web-Anwendung)  
├── <fachliches_konzept1>/ (Paket für Realisierungen des ersten Konzepts)  
│   ├── <Konzept1>Decorator1.java (Decorator für Persistenz/sonstige Details)  
│   ├── <Konzept1>Decorator2.java (Ein weiterer Decorator)  
│   └── ...  
├── <fachliches_konzept2>/ (Paket für Realisierungen des zweiten Konzepts)  
│   ├── <Konzept2>Decorator1.java  
│   ├── ...  
├── <user/human>/          (Paket für Benutzer und deren UI-Elemente, z. B. "user", "control", etc.)  
│   ├── control/         (Paket für wiederverwendbare UI-Steuerelemente)  
│   │   ├── Button.java  
│   │   ├── Field.java  
│   │   └── Table.java  
│   ├── page/          (Paket für spezifische Seiten)  
│   │   │── <Hilfsobjekt2>.java (Objekt für wiederverwendbare Logik, keine Statics)  
│   │   ├── <Fachkonzept1>Page.java  
│   │   └── <Fachkonzept2>Page.java  
│   └── <Konzept_für_UI>Decorator.java (Decorator für UI-Persistenz/sonstige Details)  
├── <App-Interface1> (Abstraktion für die gesammte Anwendung)  
├── <User> (Abstraktion für Benutzer der Anwendung)  
├── <Haupt-Interface1>  
├── <Haupt-Interface2>  
├── <Haupt-Interface3>  
└── <Haupt-Hilfsobjekt>.java (Objekt für wiederverwendbare Logik, keine Statics)

### **Anwendung des Templates**

* **Ersetze Platzhalter:** Ersetze alle <...> -Platzhalter durch die entsprechenden fachlichen Konzepte deines Projekts.  
* **Verwende Singular:** Achte darauf, dass die Paketnamen im Singular gehalten werden.  
* **Füge Unterpakete hinzu:** Unterpakete führen nur Details, aber keine neuen Konzepte ein.  
* **Nutze den Decorator:** Wann immer Funktionalität hinzugefügt werden muss, die nicht zum Kernkonzept gehört, nutze einen Decorator.  
* **Kapsle technische Details:** Verstecke technische Details wie JAX-RS oder JPA innerhalb der Klassen.

## **6. Fazit**

Das traditionelle schichtenbasierte Architekturmodell, wie es oft mit Clean Architecture oder DDD in Verbindung gebracht wird, mag für einige Projekte funktionieren, führt aber in der Praxis häufig zu starren, wartungsintensiven Strukturen.

Der in diesem Blog vorgestellte objektorientierte Ansatz, der auf den Prinzipien von Robert Bräutigam basiert, bietet eine überlegene Alternative, die konsequent auf die Stärken der Objektorientierung setzt: Komposition und Kapselung.

Die Kernprinzipien für den Erfolg:

* **Fachliche Paketstruktur:** Organisieren Sie Ihre Pakete ausschließlich nach fachlichen Konzepten.  
* **Abhängigkeitsfluss von Abstraktion zu Detail:** Platzieren Sie die abstrakten Interfaces im Root-Paket.  
* **Decorator-Muster für flexible Erweiterungen:** Verwenden Sie den Decorator, um Objekten zusätzliche Funktionalität wie Persistenz hinzuzufügen.  
* **Konsequente Komposition statt Vererbung:** Nutzen Sie Komposition, um wiederverwendbare Hilfsobjekte zu integrieren.  
* **Injektion am Anwendungseinstiegspunkt:** Verschieben Sie die Verantwortung für die Erstellung des Objektdiagramms in einen zentralen Punkt.

Durch die konsequente Anwendung dieser Prinzipien schaffen Sie eine Paketstruktur, die die Architektur Ihrer Anwendung klar und verständlich widerspiegelt. Das Ergebnis ist eine lose gekoppelte, hochgradig wartbare und erweiterbare Anwendung, die den Anforderungen der realen Softwareentwicklung gerecht wird.

## **7. Der Weg zum richtigen Package Design**

Das objektorientierte Package Design, das wir in diesem Blog erarbeitet haben, ist eine bewusste Abkehr von starren, technisch orientierten Schichtenmodellen. Es ist ein Ansatz, der die Prinzipien der Objektorientierung in den Vordergrund stellt und zu einer Paketstruktur führt, die nicht nur für die Maschine, sondern vor allem für den Menschen verständlich, wartbar und erweiterbar ist.

Wie es richtig geht:

* Denken Sie in Business-Konzepten, nicht in technischen Schichten.  
* Setzen Sie auf lose Kopplung durch Interfaces und Komposition.  
* Estellen Sie keine statische Utility-Klassen.  
* Kapseln Sie technische Implementierungsdetails.  
* Folgen Sie den Regeln von Robert Bräutigam.

Dieses Vorgehen mag anfangs unkonventionell erscheinen, führt jedoch zu Software, die weniger überraschend, leichter zu verstehen und einfacher zu warten ist. Es ist ein pragmatischer, objektorientierter Weg, um robuste und langlebige Anwendungen zu entwickeln.
