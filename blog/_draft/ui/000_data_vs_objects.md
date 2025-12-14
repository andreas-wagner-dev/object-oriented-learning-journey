# Objekte vs. Objekte: Der Unterschied im Java-Kontext 🧑‍💻

Quelle: https://hackernoon.com/objects-vs-data-structures-e380b962c1d2

 In der Welt der Java-Entwicklung gibt es Objekte (objects) und es gibt Objects. Ersteres ist wichtiger.
*Verwirrt?*

- Die objektorientierte Programmierung (OOP) führte das Konzept der Objekte (kleines „o“) ein. 
- OOP-Objekte sind Kapselungsgrenzen um einen Zustand, die eine Art öffentliches Verhalten bieten. 
- Das Konzept der Objekte ist nicht auf eine bestimmte Sprache beschränkt. 
- Tatsächlich sehen wir Objekte in klassenbasierten Sprachen wie Java/C#/Ruby/Python sowie in prototypenbasierten Sprachen wie JavaScript repräsentiert.
- Java führte eine Java-Klasse namens „Object“ (mit großem „O“) ein. 
- Java-Objects sind Instanzen der Object-Klasse (einschließlich aller Unterklassen). Diese Objects sind Sprachkonstrukte, keine konzeptionellen Konstrukte.

**Daher stellt sich die Frage:** Qualifizieren Java-Objects als OOP-Objekte? Nun, es kommt auf die Umstände an. 

**Dieser Artikel beleuchtet die spezifischen Umstände rund um die Verwendung von Datenstruktur-Objects im Vergleich zu OOP-Objekten.**

## Datenstrukturen
Betrachten Sie eine Datenstruktur, die eine Person darstellt und einen Vornamen, Nachnamen und eine Telefonnummer enthält. 
Wie könnte diese Datenstruktur in verschiedenen prozeduralen Sprachen aussehen?

**Person-Datenstruktur in C:**

```java
struct Person {
    char firstName[20];
    char lastName[20];
    char phoneNumber[10];
};
```

**Person-Datenstruktur in Pascal:**
```java
type
    Person = record
        firstName : string;
        lastName : string;
        phoneNumber : string;
    end
```

**In Java könnte dieselbe Datenstruktur so aussehen:**
```java
public class Person {
    public String firstName;
    public String lastName;
    public String phoneNumber;
}
```
Die Java-Datenstruktur unterscheidet sich „technisch“ von den C- und Pascal-Versionen, da die Java-Datenstruktur eine Klasse anstelle eines struct oder record ist. 
Aber ist die Java Person funktional anders als die C-struct oder der Pascal-record? Nein. Funktional ist es genau dasselbe. 
Alle drei Datenstrukturen stellen drei String-Felder bereit, die gelesen oder geschrieben werden können.

Der wichtige Punkt ist, dass das Java Person Object überhaupt kein „Objekt“, sondern eine Datenstruktur ist. 
Das Person Object existiert, um einige Daten in einer einzigen Entität zu organisieren, 
die als Ganzes weitergegeben und verwaltet werden kann – genau wie eine C-struct und ein Pascal-record.

Aber was wäre, wenn das Java Person Object so aussieht:

```java
public class Person {
    private String mFirstName;
    private String mLastName;
    private String mPhoneNumber;

    public String getFirstName() {
        return mFirstName;
    }

    public void setFirstName(String firstName) {
        mFirstName = firstName;
    }

    public String getLastName() {
        return mLastName;
    }

    public void setLastName(String lastName) {
        mLastName = lastName;
    }

    public String getPhoneNumber() {
        return mPhoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        mPhoneNumber = phoneNumber;
    }
}
```

Ist das Java Object jetzt ein echtes Objekt? Jetzt hat es private Daten und öffentliche Methoden. 
- Es muss ein OOP-Objekt sein, oder?
Selbst mit öffentlichen Gettern und Settern ist Person immer noch eine Datenstruktur.
- Hat sich ihr Zweck geändert? Hat sich ihr Verhalten geändert? **Nein.**
  
- Das Person Object würde immer noch auf die gleiche Weise verwendet wie sein Vorgänger mit öffentlichen Eigenschaften.
- Die Getter/Setter-Version von Person ist immer noch zu 100 % eine Datenstruktur.

Ok, was passiert, wenn wir beginnen, Person etwas Verhalten hinzuzufügen, so wie hier:
```java
public class Person {
    //... gleiche Getter/Setter wie zuvor, außer einem:

    public void setPhoneNumber(String phoneNumber) throws FormatException {
        validatePhoneNumber(phoneNumber);
        mPhoneNumber = phoneNumber;
    }

    private void validatePhoneNumber(String phoneNumber) throws FormatException {
        // Hier Validierung durchführen, um eine legitime Telefonnummer sicherzustellen.
        // Eine Ausnahme auslösen, falls ungültig.
    }
}
```
Haben wir jetzt ein echtes OOP-Objekt? 
Diese neueste Version von Person enthält jetzt ein Validierungsverhalten und verwendet sogar eine private Methode zur Implementierung der Validierung.

Selbst diese neueste Inkarnation von Person ist kein vollständiges OOP-Objekt. 
Es ist eher ein Franken-Objekt. Das Verhalten zur Validierung der Telefonnummer ist zwar Verhalten – es ist ein Dienst – was OOP-Objekte sein sollen. Aber beachten Sie, dass wir immer noch all diese Methoden haben, die zum Lesen und Schreiben des Zustands existieren. Es versteckt sich hier wirklich kein Zustand, und es gibt kaum Verhalten. Person bleibt in erster Linie eine Datenstruktur, die ihren gesamten Zustand offenlegt, fast nichts tut und im gesamten Code als konkreter Typ verwendet wird.

Egal, wie viel „Lippenstift man diesem Schwein aufträgt“, Person ist eine Datenstruktur, kein Objekt.

## OOP-Objekte
Wie sieht ein OOP-Objekt aus? 
- Es sieht aus wie ein Dienst. 
- Ein OOP-Objekt ist ein Konstrukt, das Dinge tut – es verhält sich und handelt.

Eine Person-Datenstruktur hat einen Vornamen, Nachnamen und eine Telefonnummer. 
Ein Person-Objekt geht, läuft, springt und spricht. Ein Person-Objekt tut Dinge.

Hier sind einige Beispiele für OOP-Objekte:
```java
public interface PhoneNumberValidator {
    boolean validate(String phoneNumber);
}
```
PhoneNumberValidator validiert, dass ein gegebener String eine korrekt formatierte Telefonnummer darstellt. Es gibt keinen Hinweis auf internen Zustand innerhalb des Validators. Vielleicht hat der Validator einen Zustand, vielleicht auch nicht, aber wir wissen, dass er den Dienst der Telefonnummernvalidierung anbietet.
```java
public interface PersonDataStore {
    Person getPeople(PeopleQuery query);
    void addPerson(Person person);
    void removePerson(Person person);
}
```
- `PersonDataStore` bietet einen Mechanismus zum Speichern und Abfragen von Person-Datenstrukturen. 
- In diesem Beispiel sind sowohl Person als auch `PeopleQuery` Datenstrukturen – sie organisieren einfach Informationen. 
- Der `PersonDataStore` ist jedoch ein Objekt, das Dienste bereitstellt.
- Der `PersonDataStore` kann insbesondere eine `PeopleQuery`-Datenstruktur entgegennehmen und alle `Person`-Datenstrukturen finden, die den Kriterien in der Abfrage entsprechen.

- Speichert `PersonDataStore` im Arbeitsspeicher? Speichert es auf der Festplatte? 
- Indiziert es Daten? Als Client des PersonDataStore wissen wir nichts davon und es ist uns egal.
- Uns kümmert nur, was der PersonDataStore tut, denn `PersonDataStore` ist ein **OOP-Objekt**.

### Verwechselung von Objekten und Datenstrukturen
Betrachten Sie die Person-Datenstruktur erneut. Im letzten Beispiel haben wir ein Validierungsverhalten für die Telefonnummer hinzugefügt. Man kann sich vorstellen, dass wir ähnliche Validierungen für den Vornamen und Nachnamen wünschen könnten. Darüber hinaus müssen möglicherweise auch neue Felder, die wir dieser Datenstruktur hinzufügen, validiert werden.

Das Problem mit dem Validierungsverhalten in unserer Person-Datenstruktur ist, dass wir einen Kandidaten geschaffen haben, der routinemäßig gegen das Open/Closed Principle, das Single Responsibility Principle und das Interface Segregation Principle verstoßen wird:

### Open/Closed Principle:
Jedes neue Feld erfordert das Öffnen der Person-Klasse und das Hinzufügen von Code für die Validierung.

### Single Responsibility Principle:
Die Person-Klasse hat nun die Verantwortung für die Strukturierung von Daten, die Validierung von Vornamen, die Validierung von Nachnamen und die Validierung von Telefonnummern. Das sind viele Verantwortlichkeiten, die sich unabhängig voneinander ändern können.

### Interface Segregation Principle:
Stellen Sie sich vor, Sie kümmern sich nur um die Erfassung einer Telefonnummer. Wenn Sie mit einer Instanz von Person arbeiten, hängen Sie nicht nur von der Methode validatePhoneNumber() ab, sondern auch von den Methoden validateFirstName() und validateLastName(). Sie sind also von Methoden abhängig, die Sie nicht benötigen, und haben daher das Interface Segregation Principle verletzt.

### Daten vs. Verhalten

- Die **allgemeine Lektion**, die wir aus diesem Person-Beispiel lernen können, ist, dass sich Daten und Verhalten nicht zusammen ändern. 
- Daten werden nach I/O-Gesichtspunkten wie Web-API-Eingabeformaten und Datenbankschemata gruppiert. 
- Das Verhalten wird jedoch nach Anwendungsfällen gruppiert, die darstellen, was ein Client mit der Anwendung tun möchte.

Wenn wir Datenstrukturen nehmen und anfangen, Verhalten hinzuzufügen, laden wir alle im Person-Beispiel aufgezeigten OOP-Verletzungen ein.

- Definieren Sie stattdessen **Verhalten** basierend auf Ihren **Anwendungsfällen**, 
- definieren Sie **Datenstrukturen** basierend auf Ihren **I/O-Anforderungen** und
- definieren Sie dann Objekte, um die beiden zu verbinden. 

Zum Beispiel könnte eine *Android Activity* eine Methode wie die folgende definieren:

```java
// Wird aufgerufen, wenn der Benutzer die Eingabe einer Telefonnummer beendet hat.
private void onPhoneNumberSet() {
    String phoneNumber = mPhoneNumberTextView.getText().toString();
    boolean isValid = mPhoneNumberValidator.validate(phoneNumber);

    if (isValid) {
        mPerson.setPhoneNumber(phoneNumber);
    } else {
        // Benutzer über Problem informieren.
    }
}
```

- In diesem Beispiel bleibt **Person** ihre eigene **Datenstruktur**, die nichts über das Validierungsverhalten weiß. 
- Dann gibt es ein **PhoneNumberValidator-Objekt**, das weiß, wie man einen Telefonnummern-String validiert, aber nichts über die **Person-Datenstruktur** weiß. 
- Schließlich **orchestriert** die enthaltende *Activity* die *Validierung* der *Telefonnummer*, gefolgt vom Setzen der *Telefonnummerndaten* in der **Person-Datenstruktur**.

Wenn es Zeit ist, die Person-Daten an eine Datenquelle (Web oder lokale DB) zu übermitteln, kann man sich so etwas vorstellen:

```java
private void doSubmit() {
    mPersonDataStore.addPerson(mPerson);
}
```

- Wir wissen nicht, ob der PersonDataStore mit einer lokalen DB, einem Webserver oder sogar mit dem lokalen Speicher spricht. 
- Wir wissen nur, dass unsere Anwendungsfall-Verantwortlichkeit darin besteht, Person-Daten zu sammeln und zu speichern. 
- Wir haben OOP-Objekte verwendet, um die Eingabe zu validieren, und jetzt verwenden wir ein OOP-Objekt, um die Daten zu speichern. 
- Während dieses Prozesses wurden die Daten in der Person-Datenstruktur gesammelt.

### Warum das ganze Getue?**

Warum sollte sich jemand um diese nuancierte Unterscheidung zwischen Datenstrukturen und Objekten kümmern?

- Datenstrukturen sind Zustand.
- Ich wiederhole das, um die Wirkung zu betonen:
- Datenstrukturen sind Zustand.

**Daher bedeutet:** 
- das Weitergeben von Datenstrukturen das Teilen von Zustand, und
- **geteilter Zustand** ist die `Wurzel allen Übels`.
- **OOP-Objekte** wurden erfunden, um ein Paradigma zu schaffen,
- in dem der **geteilte Zustand** `minimiert` und `kontrolliert` werden kann
- deshalb sollten wir Weise verpacken - Pakete erstellen.

**Betrachten Sie Datenstrukturen als ein Austauschformat innerhalb Ihres Codes, zwischen Ihren OOP-Objekten.** 

*Betrachten Sie den folgenden Pseudocode:*

```java
objectA.doThing1();
objectA.doThing2();
myDataStructure = objectA.extractState();

objectB.setState(myDataStructure);
objectB.doThing3();
objectB.doThing4();
```

Wir verwenden `objectA`, um etwas Arbeit zu erledigen. Dann extrahieren wir den Zustand von `objectA`, indem wir eine Datenstruktur erhalten. 
Wir initialisieren `objectB`, indem wir die Datenstruktur, die wir von A erhalten haben, senden, und jetzt erledigt `objectB` etwas Arbeit. 
In diesem Beispiel gibt es eine implizite Unveränderlichkeit für `myDataStructure`, 
sodass eine Änderung von myDataStructure keine versteckten, internen Auswirkungen auf `objectA` oder `objectB` hätte.

- Die Datenstruktur bietet lediglich einen Mechanismus, um organisierten Zustand zu verschieben. 
- Sie bietet kein eigenes Verhalten. Dies ist der Weg, wie wir Datenstrukturen in unserem Code nutzen sollten.

- Es ist auch wichtig zu erkennen, dass die Zustandsgewinnung ein relativ seltener Vorgang sein sollte. 
- Meistens sollten Sie darauf abzielen, dass Objekte andere Objekte akzeptieren und zurückgeben, nicht Datenstrukturen.

- Sie sollten versuchen, Datenstrukturen nur dann einzusetzen, wenn Sie zwischen verschiedenen Domänen in Ihrer Anwendung wechseln. 
- Wenn Sie beispielsweise Eingaben in Ihrer I/O-Domäne erhalten, verwenden Sie möglicherweise eine Datenstruktur, um diese Daten in Ihre Geschäftsdomäne einzuspeisen. 
- Ebenso, wenn Sie dem Benutzer etwas visuell präsentieren müssen, verwenden Sie möglicherweise eine Datenstruktur, um die Informationen aus Ihrer Geschäftsdomäne zu extrahieren und an Ihre visuelle Domäne zu senden. Datenstrukturen sind ein Austauschformat zwischen Anwendungsdomänen.

**Behalten** Sie bei der Entwicklung Ihrer Anwendungen im Hinterkopf, dass
- Java Objects nicht notwendigerweise OOP-Objekte sind, 
- Datenstrukturen nie dasselbe sind wie OOP-Objekte, und
- Sie sollten sicherstellen, diese unterschiedlichen Konstrukte zu erkennen und zu trennen.


