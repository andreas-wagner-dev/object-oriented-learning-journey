# Wie man `null`-Checks vermeidet
`null` ist eine der häufigsten Fehlerquellen in der Programmierung, oft als "**Milliarden-Dollar-Fehler**" bezeichnet. Die ständige Notwendigkeit, Objekte auf `null` zu prüfen, führt zu **redundantem und schwer lesbarem Code**. Dieser Beitrag beleuchtet, warum `null` problematisch ist und wie man es durch bessere Design-Muster **vermeiden** kann.

---

## 1. Problemstellung: Die Tyrannei des `null`-Checks

Das Kernproblem mit `null` liegt darin, dass es die **Zweideutigkeit** in den Code bringt. 
* Eine Variable vom Typ `String` sollte immer einen String enthalten. Wenn sie jedoch `null` sein darf, bedeutet das:
* **Der Wert ist ein String.**
* **Der Wert ist *kein* String (er ist `null`).**

Das zwingt jeden Aufrufer der Methode, die diesen Wert erhält, eine **explizite Prüfung** (`if (object != null)`) durchzuführen, um eine unerwartete **`NullPointerException`** zu vermeiden.

**Konsequenzen:**
* **Code-Verwässerung:** Der eigentliche Business-Logik-Code wird von zahlreichen `if (null)`-Blöcken überschattet.
* **Erhöhte Komplexität:** Jeder Code-Pfad verdoppelt sich praktisch (mit `null` und ohne `null`).
* **Fehleranfälligkeit:** Ein vergessener `null`-Check führt zur Laufzeit zum Absturz.
---

## 2. Fälle, warum und wo `null`-Checks entstehen
Die Notwendigkeit von `null`-Checks entsteht hauptsächlich an **Schnittstellen** und bei **fehlenden oder optionalen Werten**.
### Fall 1: Übergabe von optionalen Argumenten oder Daten
Ein Entwickler erlaubt, dass ein **Methoden-Parameter** `null` sein darf, weil der Wert optional ist.

```java
// Schlechter Stil: akzeptiert null
public void saveArticle(String title, String content, String optionalComment) {
    if (optionalComment != null) { // <-- Der null-Check entsteht hier
        // ... Logik mit Kommentar
    }
    // ... restliche Logik
}
```
* **Grund für** null: Signalisiert die Abwesenheit eines Wertes.
* **Wo der Check entsteht: Innerhalb** der aufgerufenen Methode.

### Fall 2: Rückgabe von Werten, die möglicherweise nicht existieren
Eine Methode, die ein Objekt abruft, kann nichts finden und gibt null zurück.
```java
// Schlechter Stil: gibt null zurück
public User findUserById(int id) {
    // ... Datenbankabfrage
    if (userFound) {
        return userObject;
    } else {
        return null; // <-- null signalisiert "nicht gefunden"
    }
}
// Aufrufer-Seite
User user = repository.findUserById(42);
if (user != null) { // <-- Der null-Check entsteht beim Aufrufer
    // ... Logik mit dem gefundenen User
}
```

* Grund für null: Signalisiert das Scheitern bei der Suche oder die Abwesenheit des erwarteten Objekts.
* Wo der Check entsteht: Außerhalb (beim Aufrufer).


## 3. Lösungsoptionen zur Vermeidung von null-Checks
Die Strategie ist, Schnittstellen so zu gestalten, dass null syntaktisch unmöglich oder zumindest explizit gehandhabt wird.

### 3.1. Design-Muster: Das "Null Object Pattern" 👻
Verwenden Sie ein konkretes Objekt mit einer neutralen ("Do-nothing") Implementierung der Schnittstelle, statt null zurückzugeben.
```
* Statt ...	Nimm ...
* null User	NullUser Objekt (z.B. mit ID = 0, Name = "Gast")
* null List	Eine leere Liste (Collections.emptyList())
```
* **Vorteile:** Man kann Methoden auf dem Objekt aufrufen, ohne vorher prüfen zu müssen.
```java
// Nachher (mit Null Object):
user.sendEmail("Willkommen!"); // NullUser.sendEmail() tut einfach nichts.
```

### 3.2. Verwendung von Standardwerten (Default Arguments) ⚙️
* Nutzen Sie einen sinnvollen Standardwert im Konstruktor oder der Methode, wenn ein optionaler Parameter nicht übergeben wird.
```java
public void process(String data) {
    String actualData = (data != null) ? data : "Keine Daten vorhanden"; 
    // ... Logik, die jetzt immer mit einem String arbeitet
}
```

### 3.3. Einsatz von Optional oder Maybe 🎁 (Explizite Abwesenheit)
Moderne Sprachen nutzen das Optional<T>-Objekt, das den Aufrufer zwingt, die Möglichkeit der Abwesenheit explizit zu behandeln.
```java
// Nachher (gibt Optional zurück):
Optional<User> optionalUser = repository.findUserById(42);

// Behandlung der Abwesenheit (kein null-Check nötig!)
User user = optionalUser.orElseGet(() -> new NullUser()); // Kombiniert mit Null-Object-Pattern!
```

### 3.4. Garantierte Übergabe (Non-Null-Parameter) ✅
Für Parameter, die niemals null sein dürfen:

* Exceptions: Prüfen Sie am Anfang und werfen Sie eine IllegalArgumentException, wenn null übergeben wird.
* Annotationen: Verwenden Sie @NonNull (z.B. JSR-305). Tools und IDEs können so schon zur Kompilierzeit warnen.

## 4. Zusammenfassung und Umgang mit Legacy-Code
* Die Vermeidung von null-Checks sollte zur Design-Philosophie werden. 
* Die verbreitete Meinung, dass null-Checks den Code sicherer machen, ist oft falsch. Sie machen ihn weniger sicher und weniger lesbar.

### 4.1. Grundsätze für sauberen Code:**

* Methoden und Konstruktoren sollten nicht nach null prüfen. Sie sollten davon ausgehen, dass alle Parameter gültig (non-null) sind und andernfalls eine Ausnahme werfen.
* Methoden sollten kein null zurückgeben. Stattdessen sollten sie Optional, eine leere Collection oder eine Null-Object-Implementierung zurückliefern.
* Komplexe Konstruktoren mit vielen optionalen Parametern sollten das Builder Pattern nutzen.

### 4.2. Umgang mit unvermeidbaren nulls (Leftovers) 📦

* Es gibt viele Legacy-Klassen, externe Frameworks und JDK-Funktionen, auf die wir keinen Einfluss haben und die null liefern.
* Nur für diese Fälle sind null-Checks erlaubt – allerdings idealerweise, um den null-Wert sofort in ein sauberes Objekt zu verpacken:

* **Verpacken:** Nutzen Sie Optional.ofNullable(untrustedObject) oder ähnliche Helfer, um den unsicheren null-Wert sofort in ein sicheres Optional zu kapseln.
* **Validieren:** Nutzen Sie Objects.requireNonNull(parameter) an der Eintrittsstelle in Ihren Code, um eine frühe NullPointerException zu erzwingen, wenn ein essentieller Parameter fehlt.

**Behandeln Sie null als Programmierfehler** und nicht als normales Daten-Signal, um robusteren, klareren und wartbareren Code zu schreiben.

## 5. Quellen
Dieser Blog-Beitrag basiert auf folgenden Ideen und Ressourcen:
* **Yegor Bugayenko:** [Why NULL Is Bad](https://www.yegor256.com/2014/05/13/why-null-is-bad.html)  
* **Yegor Bugayenko:** [Default Arguments Against NULL](https://www.yegor256.com/2018/05/22/default-arguments-against-null.html)  
* **Yegor Bugayenko:** [Try-Finally-If-Not-Null](https://www.yegor256.com/2016/03/22/try-finally-if-not-null.html)  
* **JavaDevGuy:** [Why I Never Null Check Parameters](https://javadevguy.wordpress.com/2018/11/24/why-i-never-null-check-parameters/)
