# Decorate it All...

## 1. Einleitung und Definition

Das Decorator-Pattern ist eines der elegantesten und mächtigsten Entwurfsmuster der objektorientierten Programmierung. Es ermöglicht, Funktionalität zu Objekten hinzuzufügen, ohne deren Interface zu verändern. Im Gegensatz zu vielen anderen Patterns, die oft überkomplex wirken, besticht das Decorator-Pattern durch seine Einfachheit und gleichzeitige Vielseitigkeit.

### Was sind Decorators?

Ein Decorator ist eine Klasse, die ein Interface implementiert und dabei eine Instanz desselben Interfaces kapselt. Durch diese Komposition können Decorators zusätzliches Verhalten hinzufügen, ohne die ursprüngliche Implementierung zu verändern. Der größte Vorteil von Decorators liegt in ihrer Kompositionsfähigkeit - sie machen unseren Code komponierbar.

### Das Problem mit Utility-Methoden

In vielen Codebases findet man statt Decorators häufig imperative Utility-Methoden. Klassen wie `StringUtils`, `FileUtils` oder `IOUtils` sind typische Beispiele für diesen Ansatz. Utility-Klassen besitzen nur statische Methoden und kapseln keinen Zustand. Sie führen zu prozeduralem Code, der schwer testbar und wenig wartbar ist.

## 2. Praktische Beispiele

### 2.1 Composable Decorators vs. Imperative Utility Methods

Betrachten wir ein Text-Interface:

```java
interface Text {
    String read();
}
```

**Imperativ mit Utility-Methoden (schlechtes Design):**

```java
Text text = new TextInFile(new File("/tmp/a.txt"));
String content = text.read();
content = StringUtils.trim(content);
content = StringUtils.toUpperCase(content);
content = removeUnprintable(content);
```

**Deklarativ mit Decorators (gutes Design):**

```java
final Text text = new AllCapsText(
    new TrimmedText(
        new PrintableText(
            new TextInFile(new File("/tmp/a.txt"))
        )
    )
);
String content = text.read();
```

Bei der deklarativen Variante wird zuerst eine Text-Instanz durch Komposition mehrerer Decorators erstellt, ohne dass etwas ausgeführt wird. Erst wenn read() aufgerufen wird, beginnt die Verarbeitung. Dies ist ein wesentlicher Unterschied zum imperativen Stil.

### 2.2 Vertical und Horizontal Decorating

Es gibt zwei grundlegende Ansätze beim Decorating: vertikal und horizontal. 

**Vertical Decorating:**

```java
Numbers numbers = new Sorted(
    new Unique(
        new Odds(
            new Positive(
                new ArrayNumbers(1, -2, 3, 0, 2)
            )
        )
    )
);
```

Jeder Decorator implementiert das `Numbers`-Interface und fügt eine Funktionalität hinzu.

**Horizontal Decorating:**

```java
interface Diff {
    Iterable<Integer> apply(Iterable<Integer> origin);
}

Numbers numbers = new Modified(
    new ArrayNumbers(1, -2, 3, 0, 2),
    new Positive(),
    new Odds(),
    new Unique(),
    new Sorted()
);
```

Vertical Decorating ist einfacher zu implementieren und eignet sich für kleinere Objekte mit wenigen Methoden. Bei wachsender Anzahl von Decorators bietet sich ein Wechsel zu Horizontal Decorating an.

### 2.3 Defensive Programming mit Validating Decorators

Anstatt Validierungslogik direkt in eine Klasse zu packen, sollten wir Validierungs-Decorators verwenden:

```java
interface Report {
    void export(File file);
}

class NoNullReport implements Report {
    private final Report origin;
    
    NoNullReport(Report rep) {
        this.origin = rep;
    }
    
    @Override
    void export(File file) {
        if (file == null) {
            throw new IllegalArgumentException("File is NULL");
        }
        this.origin.export(file);
    }
}

class NoWriteOverReport implements Report {
    private final Report origin;
    
    NoWriteOverReport(Report rep) {
        this.origin = rep;
    }
    
    @Override
    void export(File file) {
        if (file.exists()) {
            throw new IllegalArgumentException("File already exists");
        }
        this.origin.export(file);
    }
}
```

Verwendung:

```java
Report report = new NoNullReport(
    new NoWriteOverReport(
        new DefaultReport()
    )
);
report.export(file);
```

Dieser Ansatz führt zu kleineren Objekten, und kleinere Objekte bedeuten höhere Wartbarkeit. Die DefaultReport-Klasse bleibt klein, unabhängig davon, wie viele Validierungen in Zukunft hinzukommen.

### 2.4 Synchronized Decorators für Thread-Safety

Keine Klasse sollte thread-safe sein müssen, stattdessen sollten alle Klassen synchronized Decorators bereitstellen.

```java
interface Position {
    void increment();
}

class SimplePosition implements Position {
    private int number = 0;
    
    @Override
    public void increment() {
        int before = this.number;
        int after = before + 1;
        this.number = after;
    }
}

class SyncPosition implements Position {
    private final Position origin;
    
    SyncPosition(Position pos) {
        this.origin = pos;
    }
    
    @Override
    public synchronized void increment() {
        this.origin.increment();
    }
}
```

Wenn Thread-Safety benötigt wird:

```java
Position position = new SyncPosition(new SimplePosition());
```

Wenn nicht:

```java
Position position = new SimplePosition();
```

Klassen-Funktionalität sowohl umfangreich als auch thread-safe zu gestalten, verletzt das Single Responsibility Principle.

### 2.5 Decorating Envelopes

Manchmal brauchen wir Decorators, die lediglich andere Decorators zusammenfassen - sogenannte "Decorating Envelopes":

```java
class RsHtml implements Response {
    RsHtml(final String text) {
        this(
            new RsWithType(
                new RsWithStatus(text, 200),
                "text/html"
            )
        )
    }
    
    // Alle Response-Methoden werden weitergeleitet
}
```

Anstatt die Komposition von Decorators überall zu wiederholen, verwendet man RsHtml. Es ist sehr praktisch, aber die Implementierung ist verbose, da alle Methodenaufrufe weitergeleitet werden müssen.

### 2.6 Temporal Coupling vermeiden

Temporal Coupling tritt zwischen sequenziellen Methodenaufrufen auf, wenn diese in einer bestimmten Reihenfolge bleiben müssen. Dies ist ein häufiges Anti-Pattern in der objektorientierten Programmierung, das die Wartbarkeit erheblich beeinträchtigt.

**Schlechtes Beispiel (mit Temporal Coupling):**

```java
class Cash {
    private int dollars;
    
    public void setDollars(int dlr) {
        this.dollars = dlr;
    }
    
    public void setEuro(int eur) {
        this.dollars = eur * 1.20;
    }
}

// Verwendung:
Cash price = new Cash();
price.setDollars(29);
price.setEuro(20);  // Das zerstört den vorherigen Wert!
```

Die Reihenfolge der Methodenaufrufe ist entscheidend. Wenn wir zuerst `setDollars()` und dann `setEuro()` aufrufen, geht der erste Wert verloren. Das ist Temporal Coupling.

**Gutes Beispiel (ohne Temporal Coupling):**

Die Lösung liegt in unveränderlichen Objekten (Immutability):

```java
class Cash {
    private final int dollars;
    
    Cash(int dlr) {
        this.dollars = dlr;
    }
    
    public Cash euro(int eur) {
        return new Cash(eur * 1.20);
    }
}

// Verwendung:
Cash five = new Cash(5);
Cash ten = five.euro(10);
```

Jeder Methodenaufruf erstellt ein neues Objekt, anstatt das bestehende zu modifizieren. Die ideale Methode in OOP hat nur eine einzige Anweisung, und diese Anweisung ist `return`. Noch besser wäre die Verwendung von Composable Decorators anstelle von Buildern oder Settern.

### 2.7 Fluent Interfaces vs. Decorators

Fluent Interfaces wurden von Martin Fowler geprägt und sind eine sehr bequeme Art, mit Objekten zu kommunizieren. Sie machen Facades einfacher zu benutzen, ruinieren aber das interne Design und erschweren die Wartbarkeit erheblich.

**Beispiel eines Fluent Interface:**

```java
String html = new JdkRequest("https://www.google.com")
    .method("GET")
    .fetch()
    .as(RestResponse.class)
    .assertStatus(200)
    .body();
```

Dies sieht elegant aus und liest sich fast wie natürliche Sprache. Aber das interne Design leidet massiv:

**Die Probleme mit Fluent Interfaces:**

1. **Große, aufgeblähte Klassen**: Die `JdkRequest`-Klasse muss die Methoden `method()`, `fetch()` und viele weitere enthalten. Wenn neue Funktionalität benötigt wird, muss die Klasse immer größer werden.

2. **Schwierige Erweiterbarkeit**: Jede neue Funktion bedeutet neue Methoden in bereits großen Klassen. Dies verletzt das Single Responsibility Principle massiv.

3. **Wartungsprobleme**: Bei 43 Methoden (wie in Java's Stream-API) wird die Klasse zum Alptraum für Maintainer.

4. **Verletzung der OOP-Prinzipien**: Fluent Interfaces führen zu schlechter Kohäsion und enger Kopplung.

**Die bessere Alternative mit Decorators:**

```java
String html = new BodyOfResponse(
    new ResponseAssertStatus(
        new FetchedResponse(
            new RequestWithMethod(
                new JdkRequest("https://www.google.com"),
                "GET"
            )
        ),
        200
    )
).toString();
```

**Vorteile dieser Lösung:**

1. **Kleine, fokussierte Klassen**: Jede Klasse hat eine einzige Verantwortung
2. **Einfache Erweiterbarkeit**: Neue Funktionalität = neue Decorator-Klasse, keine Änderung bestehender Klassen
3. **Vereinfachtes Testing**: Kleine Klassen sind einfacher zu testen
4. **Immutability**: Alle Klassen können unveränderlich sein
5. **Lose Kopplung**: Decorators sind unabhängig voneinander

Der Nachteil ist, dass IDE-Auto-Completion nicht so gut funktioniert und die Syntax weniger wie ein DSL aussieht. Aber das ist ein geringer Preis für die erheblich bessere Wartbarkeit.

**Der Konflikt:**

Es scheint einen Konflikt zwischen Benutzerfreundlichkeit und Wartbarkeit zu geben:
- Fluent Interfaces sind gut für Benutzer, aber schlecht für Entwickler
- Kleine Objekte sind gut für Entwickler, aber schwieriger zu verwenden

Dieser Konflikt existiert jedoch nur, wenn man an große Klassen und prozedurales Programmieren gewöhnt ist. Für echte OOP-Entwickler ist eine große Anzahl kleiner Klassen ein Vorteil, kein Nachteil.

## 3. Grenzen und Nachteile des Decorator-Patterns

Trotz all seiner Vorteile hat das Decorator-Pattern auch Grenzen und Nachteile, die man kennen sollte:

### 3.1 Komplexität und "Decorator Soup"

Das größte Problem ist die potenzielle Komplexität durch viele verschachtelte Decorators. Bei exzessivem Einsatz kann eine verwirrende Hierarchie entstehen, die schwer zu verstehen ist. Dies wird manchmal als "Decorator Soup" bezeichnet - eine verworrene Klassenhierarchie.

```java
String result = new UpperCaseText(
    new TrimmedText(
        new EncodedText(
            new ValidatedText(
                new CachedText(
                    new LoggedText(
                        new TextInFile("data.txt")
                    )
                )
            )
        )
    )
).read();
```

Bei zu vielen Decorators wird es schwierig, den Überblick zu behalten.

### 3.2 Objektidentität und Typprüfungen

Ein Decorator und seine eingeschlossene Komponente sind nicht identisch, daher schlagen Typ-Prüfungen fehl. Das kann zu unerwartetem Verhalten in Code führen, der auf Objektidentität angewiesen ist, beispielsweise bei Gleichheitsprüfungen mit `===`.

```java
Text original = new SimpleText("Hello");
Text decorated = new UpperCaseText(original);

// Dies schlägt fehl:
if (decorated instanceof SimpleText) {
    // Wird nie ausgeführt
}
```

### 3.3 Increased Number of Classes

Die Implementierung von Decorators für mehrere Features führt zu vielen Klassen. Das kann die Codebasis umfangreicher und schwerer zu verwalten machen, besonders bei zahlreichen Decorator-Kombinationen.

### 3.4 Performance-Overhead

Jeder Decorator führt eine Ebene der Methodendelegierung ein, was zu einem geringen Performance-Overhead führen kann. Bei tiefen Decorator-Ketten kann dies messbar werden, auch wenn es in der Praxis selten ein echtes Problem darstellt.

### 3.5 Instanziierungskomplexität

Decorators können den Prozess der Instanziierung der Komponente verkomplizieren, da man nicht nur die Komponente instanziieren, sondern sie auch in mehrere Decorators wrappen muss.

### 3.6 Limitations bei Interface-Vererbung

Das Decorator-Pattern basiert auf Interface-Vererbung und kann daher nur das Verhalten von Objekten durch Interfaces oder abstrakte Klassen erweitern. Es kann keine neuen Datenfelder zum ursprünglichen Objekt hinzufügen.

### 3.7 Das Problem mit Test Data

Ein interessanter Aspekt aus der Test-Perspektive: Während das Builder-Pattern (verwandt mit Decorators) für Testdaten gut funktioniert, kann das Object Mother Pattern problematisch werden, wenn es viele Variationen der Testdaten gibt.

**Object Mother vs. Test Data Builder:**

Das Object Mother Pattern ist ein Versuch, komplexe Testobjekte bereitzustellen:

```java
// Object Mother Ansatz
Invoice invoice = TestInvoices.newDeerstalkerAndCapeInvoice();
```

Das Problem: Mit der Zeit wird die Object Mother aufgebläht, unübersichtlich und schwer zu warten. Bei jeder neuen Variation muss eine neue Factory-Methode hinzugefügt werden.

Der bessere Ansatz sind Test Data Builders (eine Form von Decorators):

```java
Invoice invoice = new InvoiceBuilder()
    .withRecipient("Sherlock Holmes")
    .withAddress("221b Baker Street")
    .withItem(new ItemBuilder().withName("Deerstalker").build())
    .build();
```

Test Data Builders machen es einfacher, Testdaten inline zu erstellen, ohne Tests brüchig zu machen oder viel Duplikation zu erzeugen. Tests sind von den Aspekten der Objektstruktur isoliert, die für den Test keine Bedeutung haben.

### 3.8 Wann man Decorators NICHT verwenden sollte

- **Bei einfacher Funktionalität**: Wenn die hinzuzufügende Funktionalität sehr einfach ist, kann es leichter sein, sie direkt zur Klasse hinzuzufügen, anstatt einen neuen Decorator zu erstellen.
- **Bei Third-Party Klassen**: Es ist oft schwierig, das Decorator-Pattern auf Third-Party-Komponenten-Klassen anzuwenden.
- **Bei finale Klassen**: Viele Programmiersprachen haben das final-Keyword, das weitere Erweiterungen einer Klasse verhindert.

### 3.9 Pragmatischer Ansatz

Die Lösung liegt im pragmatischen Einsatz:

1. **Für kleine Interfaces** (1-3 Methoden): Verwende Vertical Decorating
2. **Für große Interfaces**: Erwäge Horizontal Decorating oder eine Facade
3. **Für Tests**: Kombiniere Object Mother mit Test Data Builders
4. **Im Produktionscode**: Verwende Decorating Envelopes, um häufige Kombinationen zu kapseln

Das Decorator-Pattern ist mächtig, aber wie alle Patterns sollte es mit Bedacht eingesetzt werden. Das Pattern kann zu einem System mit "vielen kleinen Objekten" führen, die für den wartenden Programmierer alle gleich aussehen - das kann ein Wartungs-Alptraum sein.

## 4. Quellen

### Primärquellen (Yegor Bugayenko)

1. **Composable Decorators vs. Imperative Utility Methods** (2015)  
   https://www.yegor256.com/2015/02/26/composable-decorators.html

2. **Vertical and Horizontal Decorating** (2015)  
   https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html

3. **Defensive Programming via Validating Decorators** (2016)  
   https://www.yegor256.com/2016/01/26/defensive-programming.html

4. **Synchronized Decorators to Replace Thread-Safe Classes** (2017)  
   https://www.yegor256.com/2017/01/17/synchronized-decorators.html

5. **Decorating Envelopes** (2017)  
   https://www.yegor256.com/2017/01/31/decorating-envelopes.html

6. **OOP Alternative to Utility Classes** (2014)  
   https://www.yegor256.com/2014/05/05/oop-alternative-to-utility-classes.html

7. **Temporal Coupling Between Method Calls** (2015)  
   https://www.yegor256.com/2015/12/08/temporal-coupling-between-method-calls.html

8. **Fluent Interfaces Are Bad for Maintainability** (2018)  
   https://www.yegor256.com/2018/03/13/fluent-interfaces.html

### Sekundärquellen

9. **Test Data Builders: an alternative to the Object Mother pattern** (2007) - Nat Pryce  
   http://www.natpryce.com/articles/000714.html

### Technische Referenzen

- Design Patterns: Elements of Reusable Object-Oriented Software (Gang of Four)
- Effective Java by Joshua Bloch
- Growing Object-Oriented Software, Guided by Tests by Steve Freeman, Nat Pryce

### Weiterführende Literatur

- Bugayenko, Y. (2016). *Elegant Objects, Volume 1*
- Bugayenko, Y. (2017). *Elegant Objects, Volume 2*
- Gang of Four: *Design Patterns: Elements of Reusable Object-Oriented Software*

---

*Dieser Artikel basiert auf den Erkenntnissen und Konzepten von Yegor Bugayenko zur objektorientierten Programmierung und dem Elegant Objects Ansatz.*
