
🔍 Was Yegor sagt

**Virtue #6:** **His Name Is Not a Job Title** — ein gutes Objekt sollte sagen, was es ist, nicht was es tut oder orchestriert. Jobtitel wie Controller, Manager, Service etc. sind problematisch, da sie keine reale Entität repräsentieren. 
yegor256.com

Ein Manager in einem Domain-Kontext ist kein reales Ding, wenn er nur Dinge verwaltet. Wenn jedoch der Begriff Manager tatsächlich eine reale Entität bezeichnet („Manager“ im HR-System, also eine Person, Chef), dann ist das anders. 
yegor256.com

**Beispiel:** EntityManager in JPA ist eine technische Hilfskomponente, kein reales Wesen in der Domäne. Daher laut Yegor ein schlechter Objektname im Business-Kontext. 
yegor256.com

**🛠 Regel aus deinem Kontext**

**Regel für gute Objekt-/Klassennamen (Business‐/Domain‐Kontext):**

- Der Name muss eine reale Entität repräsentieren oder in der Domäne existieren („Person“, „Abteilung“, „Dokument“, „Rechnung“ etc.).
- Namen, die Funktionen beschreiben („Manager“, „Service“, „Repository“, „Controller“) sind nur erlaubt, wenn sie tatsächlich reale Rollen repräsentieren, nicht als technische Artefakte oder Infrastruktur‐Hilfsklassen.
- Für technische Infrastruktur oder Bibliotheken gelten andere Regeln – obige Einschränkung gilt primär im Business-/Domänenkontext.
- Wenn ein Name wie Service/Manager etc. benutzt wird, sollte geprüft werden, ob stattdessen ein Real‐Objekt mit passendem Namen und Verantwortung existiert.
