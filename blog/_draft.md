# Kontextgetriebene Paketierung in Softwareprojekten

Fast jeder Entwickler beginnt damit, Packages lediglich als ein Werkzeug zur Organisation von Klassen zu nutzen – meist so, wie es in dem Moment logisch erscheint.  
Doch eine Packaging-Strategie kann weitaus mächtiger sein: Sie kann Wissen enthalten, Orientierung bieten und die langfristige Wartbarkeit der Software erheblich verbessern.

Dieses Blogpost zeigt drei zentrale Regeln und beleuchtet typische Corner Cases aus der Praxis, die beim Einsatz in realen Projekten auftauchen.

---

## Die drei Regeln

### Regel 1: Packages sollten niemals von ihren Sub-Packages abhängen
Ein Package darf nicht von seinen eigenen Sub-Packages abhängig sein.  
Beispiel: `com.example.customer` sollte nicht von `com.example.customer.details` abhängen.

Damit bleibt die Hierarchie stabil und die Abhängigkeiten zeigen nach „unten“, nicht nach „oben“.  
Was **nicht** gemeint ist: Packages auf der gleichen Ebene („Siblings“) dürfen sich durchaus gegenseitig referenzieren, wenn sie fachlich verbunden sind.

### Regel 2: Sub-Packages führen keine neuen Konzepte ein
Sub-Packages dienen der **Detaillierung** eines bestehenden Konzepts, nicht der Einführung neuer.  
Ein Beispiel:  
- `com.example.billing` (Business-Konzept „Billing“)  
- `com.example.billing.rules` (Details: Geschäftsregeln des Billings)

Falsch wäre:  
- `com.example.billing.rules` enthält plötzlich ein Konzept „Notification“, das nichts mit Billing zu tun hat.

### Regel 3: Packages spiegeln Business-Konzepte wider
Packages sollten **Business-Terminologie** und **fachliche Konzepte** reflektieren.  
Dadurch wird die Software auch für Fachfremde nachvollziehbar.

Beispiel:  
- `com.example.api.user` (für die Interaktion menschlicher Benutzer)  
- `com.example.api.resource` (für externe Systeme, die Daten konsumieren)  
- `com.example.api.service` (für Dienste, die Business-Logik triggern)

---

## Praktische Corner Cases

In der Praxis treten oft Situationen auf, die nach Klärung verlangen. Hier ein paar Beispiele:

### Corner Case 1: Abhängigkeiten zwischen Sub-Packages
Wenn zwei Sub-Packages derselben Ebene voneinander abhängen, ist das erlaubt, sofern es fachlich Sinn ergibt.  
Beispiel:  
- `com.example.customer.address` ↔ `com.example.customer.contact`

Beide gehören zum übergeordneten Konzept „Customer“ und dürfen zusammenarbeiten.

### Corner Case 2: API als eigenes Business-Konzept
Wenn das Business fordert, dass eine Applikation Daten als Ressourcen bereitstellt oder externe Interaktionen ermöglicht, entsteht eine **API** als Business-Konzept.  
Die Sub-Pakete der API können z. B. sein:  
- `api.user` (Interaktion durch Menschen)  
- `api.resource` (bereitgestellte Daten-Ressourcen)  
- `api.service` (externe Services, die Business-Logik auslösen)

Hier ist die API also nicht nur ein technischer Layer, sondern ein **vollwertiges Business-Konzept**.

### Corner Case 3: Technische Utilities
Utilities und technische Hilfsklassen sollten **kein eigenes Business-Konzept** sein.  
Sie gehören entweder klar zu einem bestehenden Business-Konzept oder in ein technisches Basis-Package (`.common`, `.shared`).

---

## Fazit

Eine gute Packaging-Strategie geht über reine Ordnung hinaus.  
Sie **transportiert Wissen, verdeutlicht Business-Konzepte** und schützt vor wachsender Komplexität.  

Die drei Regeln geben eine klare Leitlinie, während die Corner Cases zeigen, wie man sie in realen Projekten sinnvoll anwendet.

