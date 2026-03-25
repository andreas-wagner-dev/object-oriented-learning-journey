# **Typsicherheit jenseits des Compilers: Warum Class Casting ein Anti-Pattern ist**

Typsicherheit ist das Fundament zuverlässiger Software. Es geht darum, sicherzustellen, dass Operationen nur auf Daten angewendet werden, für die sie konzipiert sind. Doch während viele Programmierer sich auf statische Typsysteme verlassen, argumentieren Experten, dass wahre Robustheit durch **Design und Architektur** erreicht wird – und dass einige gängige Techniken, wie das Class Casting, diese Sicherheit untergraben.

## **1\. Typsysteme: Der Wächter der Operationen**

Ein **Typsystem** ist ein Regelwerk, das jeder Programmiersprache vorschreibt, wie sie Typen zuordnet, verwendet und verwaltet, um Fehler zu verhindern.

### **Statische vs. Dynamische Typisierung**

* **Statisch typisiert (z. B. Java, C\#, C++):** Die Typprüfung erfolgt zur **Kompilierzeit**. Fehler werden frühzeitig erkannt, was zu robusterem Code führt.  
* **Dynamisch typisiert (z. B. Python, Ruby, JavaScript):** Die Typprüfung erfolgt zur **Laufzeit**. Dies bietet größere Flexibilität, verschiebt das Fehlerrisiko aber in die Produktionsumgebung.

**Typsicherheit** (Type Safety) ist die Eigenschaft einer Sprache, die garantiert, dass zur Laufzeit keine Typfehler auftreten. Ob statisch oder dynamisch: Das Ziel ist immer, die Integrität der Daten zu schützen.

## **2\. Das Anti-Pattern: Class Casting (Typumwandlung)**

Eine der größten Gefahren für die Typsicherheit im Code ist die **Typumwandlung** oder das **Class Casting**. Class Casting, oft in Verbindung mit instanceof oder dem Downcasting von Schnittstellen ((SpecificType) object), ist ein starkes Indiz für schlechtes Design.

### **Warum Class Casting ein Anti-Pattern ist**

1. **Verschiebung des Fehlers:** Casting eliminiert die Typprüfung des Compilers an genau dieser Stelle. Es ersetzt einen potenziellen Kompilierfehler durch eine **ClassCastException** zur Laufzeit. Dies ist besonders gefährlich, da Laufzeitfehler in Produktionssystemen teuer und schwer zu reproduzieren sind.  
2. **Verletzung der Abstraktion:** Wenn Sie ein Objekt als allgemeinen Typ (z. B. Object oder eine generische Schnittstelle) erhalten und es dann auf einen spezifischen Typ casten müssen, beweist dies, dass der **Vertrag** (die Methodensignatur) unzureichend war. Die Methode hätte von vornherein den spezifischen Typ oder eine geeignetere Schnittstelle erwarten müssen.  
3. **Verstoß gegen Design-Prinzipien:** Casting verstößt häufig gegen das **Liskov Substitution Principle (LSP)**, da Sie implizit die Notwendigkeit schaffen, das Verhalten eines Objekts zur Laufzeit zu überprüfen, anstatt sich auf seine deklarierten Fähigkeiten zu verlassen.

*„Wenn Sie Class Casting in Ihrem Code sehen, wurde der Code falsch entworfen. Der Compiler sollte für Sie die Arbeit machen.“*

– Yegor Bugayenko, Class Casting is an Anti-Pattern

## **3\. Typsicherheit durch Design: "Typing Without Types"**

Der Ansatz "**Typing Without Types**" ist eine Design-Philosophie, die darauf abzielt, die Vorteile einer starken Typisierung – nämlich klare Verträge und Fehlervermeidung – zu nutzen, selbst wenn die Programmiersprache selbst nicht streng statisch ist oder wenn man mit externen Abhängigkeiten arbeitet, die nur primitive Typen liefern.

Hier sind die Schlüsselstrategien, um Typsicherheit durch Design zu erzwingen:

### **3.1. Starke Typisierung mit Value Objects (Wertobjekten)**

Value Objects (Wertobjekte) sind kleine, unveränderliche Objekte, die primitive Typen kapseln und deren Gültigkeit und Kontext definieren. Sie stellen sicher, dass ein Wert nur in einem gültigen Zustand existieren kann.

**Beispiele und Prinzip:**

* **Verkapselung von Validierung:** Wenn Sie beispielsweise eine EmailAddress\-Klasse erstellen, erfolgt die Formatprüfung (enthält @, gültige Domain) **einmalig im Konstruktor**. Ab dem Zeitpunkt, an dem das EmailAddress\-Objekt erstellt wurde, garantiert der Typ, dass die enthaltenen Daten gültig sind. Die aufrufende Logik muss den String nicht mehr prüfen.  
* **Keine Mehrdeutigkeit:** Ein String kann ein Name, eine Adresse oder ein Passwort sein. Ein CustomerName\-Objekt ist immer ein Name. Dies beseitigt die typische Verwechslungsgefahr bei Methodenaufrufen mit vielen String\-Parametern.

| Statt... | Nimm... | Vorteile |
| :---- | :---- | :---- |
| public void send(String email) | public void send(EmailAddress recipient) | Die EmailAddress-Klasse kann Validierungslogik im Konstruktor erzwingen. Es ist unmöglich, einen ungültigen String zu übergeben. |
| public void setPrice(int cents) | public void setPrice(Amount price) | Die Amount-Klasse kapselt Währung und stellt sicher, dass der Wert positiv ist. |

### **3.2. Interfaces und Komposition (Fähigkeiten statt Identität)**

Statt zur Laufzeit herauszufinden, *was* ein Objekt ist (mittels instanceof und Casting), sollte der Code erwarten, *was* ein Objekt **kann** (durch Interfaces).

**Beispiele und Prinzip:**

* **Erwartete Fähigkeiten:** Wenn Ihre Methode eine Eigenschaft protokolieren muss, sollte sie ein Logger\-Interface erwarten. Es ist irrelevant, ob das Objekt ein FileLogger, ein DatabaseLogger oder ein ConsoleLogger ist – Hauptsache, es erfüllt den Vertrag (log(message)).  
* **Vermeidung von Fallunterscheidungen:** Wenn ein Objekt optional ein spezielles Verhalten unterstützen soll (z. B. logDetails()), sollte das Interface dieses Verhalten definieren. Wird es nicht benötigt, kann das Objekt das **Null Object Pattern** implementieren (wie in unserem vorherigen Blog besprochen), das die Methode ohne Nebenwirkungen ausführt. Dadurch entfällt der explizite $if (obj instanceof Loggable)$\-Check im Konsumenten-Code.

**Schlecht (Casting):**

if (obj instanceof Loggable) {  
    ((Loggable) obj).logDetails();  
}

Gut (Komposition/Delegation):  
Die aufrufende Methode erwartet ein Objekt, das die gewünschte Funktion immer erfüllt.

### **3.3. Keine primitiven Datenstrukturen in Methodensignaturen**

Generische Datenstrukturen wie List\<Object\> oder Map\<String, String\> sind oft "typ-unsicherer" Code, da der Compiler nichts über die Bedeutung der gespeicherten Daten weiß und man leicht falsche Schlüssel oder Werte einfügen kann.

**Beispiele und Prinzip:**

* **Klarheit durch Records/Datentypen:** Statt eine List\<Object\> zurückzugeben, die Kundennamen und IDs enthält, sollten Sie eine List\<CustomerRecord\> oder List\<CustomerSummary\> zurückgeben. Dadurch weiß der Aufrufer sofort, welche Felder er erwarten kann, und erhält direkten Zugriff über typisierte Eigenschaften (z. B. record.id, record.name), ohne unsichere Map-Lookups (map.get("name")) oder Castings.  
* **Strukturierte Übergabe:** Statt einer Methode drei ungeordnete String\-Parameter zu übergeben, die die Funktion eines Benutzers, dessen Abteilungs-ID und eine Berechtigungsstufe darstellen, verwenden Sie ein **UserContext** Value Object. Dieses Objekt stellt sicher, dass alle drei Werte vorhanden und validiert sind, und übergibt sie als eine Einheit mit klarer Struktur.

## **4\. Fazit**

Typsicherheit ist zu wichtig, um sie allein dem Compiler zu überlassen – und zu gefährlich, um sie zur Laufzeit durch Casting zu erzwingen.

Die eleganteste und robusteste Lösung ist die **Design-Lösung**: Gestalten Sie Ihre Verträge und Datenstrukturen von Grund auf so, dass sie keine Mehrdeutigkeit zulassen. **Vermeiden Sie Class Casting** und ersetzen Sie primitive Typen durch **stark typisierte Value Objects**, um die Typsicherheit tief in der Architektur Ihres Codes zu verankern.

## **5\. Quellen**

Dieser Blog-Beitrag basiert auf folgenden Ideen und Ressourcen:

* **Yegor Bugayenko:** [Typing Without Types](https://www.yegor256.com/2020/11/10/typing-without-types.html)  
* **Yegor Bugayenko:** [Class Casting is an Anti-Pattern](https://www.yegor256.com/2015/04/02/class-casting-is-anti-pattern.html)  
* **Wikipedia:** [Type Safety](https://en.wikipedia.org/wiki/Type_safety)  
* **Wikipedia:** [Type System](https://en.wikipedia.org/wiki/Type_system)
