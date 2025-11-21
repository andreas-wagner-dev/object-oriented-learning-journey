# OOP Paketstruktur - KI Prompts zur Reproduktion

## Übersicht
Diese Prompts ermöglichen die Generierung einer objektorientierten Paketstruktur nach bewährten Prinzipien (Package Design Principles) mit automatischer Metrik-Optimierung.

---

## 1. Basis-Prompt: OOP Paketstruktur initialisieren

```
Erstelle eine OOP-Paketstruktur für [PROJEKT-NAME] nach folgenden Axiomen:

DEFINITIONEN:
- n[0] = com.company.<app> = globaler Namespace (Ebene 0)
- a[n] = Abstraktion auf Ebene n (Interface/abstrakte Klasse)
- p[n] = Package mit Implementierungen von a[n]

AXIOME:
1. Existenzregel für Abstraktionen:
   a[n] ∈ {n[0], n[0].*}

2. Existenzregel für Packages:
   ∃ a[n] : a[n] ∈ {n[0], n[0].*} ⇒ p[n] darf existieren
   (Package entsteht auf gleicher Ebene wie gleichnamige Abstraktion)

3. Inhaltsregel:
   ∀ c ∈ p[n] : c ⊑ a[n]
   (Package enthält NUR Implementierungen der gleichnamigen Abstraktion)

NAMENSREGELN (Yegor256):
- ❌ KEINE "-er" Suffixe (Manager, Handler, Validator, Service)
- ❌ KEINE "Client" Suffixe
- ❌ KEINE zusammengesetzten Namen wenn Kontext klar
- ✅ Namen beschreiben "WAS es ist", nicht "WAS es tut"
- ✅ Implementierungen als Adjektive/Zustände (Cached, Validated, Encrypted)
- ✅ Spezifische Algorithmen als Substantive (Bcrypt, Sha256)

Domäne: [BESCHREIBUNG]
Kernentitäten: [LISTE]
```

---

## 2. Prompt: Paketstruktur mit Metriken validieren

```
Analysiere die Paketstruktur und berechne für jedes Package:

METRIKEN:
1. Abstractness (A):
   A = N_abstract / N_total
   (Anteil abstrakter Klassen im Package)

2. Instability (I):
   I = C_e / (C_a + C_e)
   Wobei:
   - C_e = efferent coupling (ausgehende Dependencies)
   - C_a = afferent coupling (eingehende Dependencies)

3. Distance from Main Sequence (D):
   D = |A + I - 1|
   
   Bewertung:
   - D ≤ 0.2 : ✅ Optimal (auf Main Sequence)
   - D ≤ 0.4 : ⚠️ Akzeptabel
   - D > 0.4 : ❌ Problematisch

ZIELE:
- Abstrakte Packages sollten stabil sein (A hoch, I niedrig)
- Konkrete Packages sollten instabil sein (A niedrig, I hoch)
- Alle Packages sollten nahe der Main Sequence liegen

Erstelle:
1. Tabelle mit A, I, D für jedes Package
2. A vs I Plot Visualisierung
3. Empfehlungen für problematische Packages
```

---

## 3. Prompt: DDD zu OOP konvertieren

```
Konvertiere die folgende DDD-Struktur in OOP-Struktur:

TRANSFORMATIONSREGELN:

DDD Layer → OOP:
❌ domain.model         → ✅ n[0] (Abstraktionen direkt im Root)
❌ domain.repository    → ✅ n[0] (z.B. Users, Documents als Collection)
❌ domain.service       → ✅ n[0] (z.B. Authentication als Interface)
❌ infrastructure.*     → ✅ Implementierungs-Packages unter entsprechenden Abstraktionen
❌ application.*        → ✅ Packages unter fachlichen Abstraktionen

DDD Namen → OOP Namen:
❌ UserRepository       → ✅ Users (Collection-Konzept)
❌ DocumentRepository   → ✅ Documents
❌ UserService          → ✅ User (oder spezifischer Aspekt)
❌ AuthenticationService → ✅ Authentication (Interface)
❌ PasswordValidator    → ✅ Password (Object)
❌ EmailValidator       → ✅ Email (Object)

Struktur-Beispiel:
VORHER (DDD):
- domain.model.User
- domain.repository.UserRepository
- infrastructure.persistence.JpaUserRepository

NACHHER (OOP):
- User (interface in n[0])
- Users (interface in n[0])
- user/ (package für die Realisierung des Benutzer-Konzepts)
  - AuthenticatedUser
  - RegisteredUser  
  - AnonymousUser
  - CachedUsers
  - PersistentUsers
  - InMemoryUsers

Eingabe: [DDD-STRUKTUR]
```

---

## 4. Prompt: Package Dependencies prüfen (Acyclic Dependencies Principle)

```
Prüfe die Package-Dependencies auf Zyklen:

REGEL (ADP - Acyclic Dependencies Principle):
Die Dependency-Struktur MUSS ein DAG (Directed Acyclic Graph) sein.

PRÜFUNG:
1. Erstelle Dependency-Graph aller Packages
2. Erkenne Zyklen mit Tiefensuche
3. Für jeden Zyklus:
   - Zeige betroffene Packages
   - Schlage Auflösung vor:
     a) DIP anwenden (Dependency Inversion via Interface)
     b) Package splitten (Common Reuse Principle)
     c) Package reorganisieren

AUSGABE:
- ✅ "Keine Zyklen gefunden - DAG ist valide"
- ❌ "Zyklen gefunden: [Package A] → [Package B] → [Package A]"
  - Empfehlung: [Konkrete Lösung]

Paketstruktur: [STRUKTUR]
```

---

## 5. Prompt: Package Cohesion analysieren

```
Analysiere Package Cohesion nach drei Prinzipien:

1. REP (Reuse Release Equivalence Principle):
   - Bildet das Package eine kohärente, wiederverwendbare Einheit?
   - Könnte es als eigenständige Library released werden?
   
2. CCP (Common Closure Principle):
   - Ändern sich Klassen im Package aus gleichen Gründen?
   - Sind sie gegen gleiche Änderungsarten geschlossen?
   
3. CRP (Common Reuse Principle):
   - Werden alle Klassen im Package gemeinsam wiederverwendet?
   - Oder gibt es Klassen, die isoliert benutzt werden?

AUSGABE für jedes Package:
- REP Score: [1-5] mit Begründung
- CCP Score: [1-5] mit Begründung  
- CRP Score: [1-5] mit Begründung
- Empfehlung: [Split/Merge/OK]

Paketstruktur: [STRUKTUR]
```

---

## 6. Prompt: Vollständige Projektstruktur generieren

```
Generiere vollständige OOP-Paketstruktur für: [PROJEKT]

SCHRITT 1 - Kernabstraktionen identifizieren:
- Welche echten Entitäten existieren? (keine Jobtitel!)
- Welche davon sind Kollektionen? (Users statt UserRepository)
- Welche Verhaltensweisen? (Authentication, nicht AuthenticationService)

SCHRITT 2 - Hierarchie aufbauen:
- Ebene 0: Kern-Abstraktionen in n[0]
- Ebene 1+: Spezialisierungen in Sub-Packages
- Regel: Abstraktion erzeugt gleichnamiges Package

SCHRITT 3 - Implementierungen benennen:
- Adjektive für Zustände (Cached, Validated, Encrypted)
- Substantive für Algorithmen (Bcrypt, Jwt)
- "With..." für Varianten (WithEmail, WithOAuth)
- "From..." für Ursprung (FromTemplate, FromDatabase)

SCHRITT 4 - Metriken berechnen:
- A, I, D für jedes Package
- Dependency-Graph prüfen (keine Zyklen!)
- REP, CCP, CRP bewerten

SCHRITT 5 - Visualisierung:
- Mermaid-Diagramm der Struktur
- A vs I Plot
- Dependency-Graph

AUSGABE:
1. Vollständige Package-Hierarchie mit Begründungen
2. Alle Metriken in Tabellenform
3. Visualisierungen
4. Vergleich zu typischer DDD-Struktur

Domain: [BESCHREIBUNG]
Requirements: [LISTE]
```

---

## 7. Prompt: Anti-Pattern Detektion

```
Prüfe Code auf OOP-Anti-Patterns:

ANTI-PATTERNS:

1. "-er" Suffixe:
   ❌ Manager, Handler, Validator, Controller, Helper, Builder
   → Finde alle Klassen mit diesen Suffixen
   → Schlage OOP-konforme Namen vor

2. "Client" Pattern:
   ❌ XyzClient mit 50+ Methoden
   → Sollte in mehrere Objekte aufgeteilt werden
   → Jedes Objekt repräsentiert eine Entität

3. Utility Classes:
   ❌ Static-only Klassen
   → Konvertiere zu echten Objekten mit State

4. Compound Names:
   ❌ UserAuthenticationService
   → Im richtigen Package-Kontext: Authentication

5. God Objects:
   ❌ Klassen mit 20+ Methoden
   → Split nach Single Responsibility

6. Naked Data (DTOs):
   ❌ Klassen mit nur Gettern/Settern
   → Verhalten hinzufügen oder als Value Object

AUSGABE:
- Liste aller Anti-Patterns mit Fundstellen
- Konkrete Refactoring-Vorschläge
- Neue OOP-konforme Struktur

Codebase: [PFAD/CODE]
```

---

## 8. Prompt: Spring/Jakarta zu OOP migrieren

```
Migriere Spring/Jakarta-Projekt zu OOP-Struktur:

TYPISCHE SPRING-PATTERN → OOP:

❌ @Service UserService           → ✅ User (interface) + user/ (implementations)
❌ @Repository UserRepository     → ✅ Users (interface) + users/ (implementations)
❌ @Component PasswordValidator   → ✅ Validated (in password package)
❌ @Controller UserController     → ✅ Request (interface) + request/ (implementations)
❌ @RestController                → ✅ Http (interface) + http/ (implementations)

DEPENDENCY INJECTION → Constructor Injection:
❌ @Autowired private UserService userService
✅ public MyClass(Users users) { this.users = users; }

CONFIGURATION → Composition:
❌ @Configuration + @Bean
✅ Objekte komponieren andere Objekte im Constructor

BEISPIEL-MIGRATION:

VORHER:
```java
@Service
public class UserService {
    @Autowired private UserRepository repo;
    public User findById(Long id) { ... }
}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {}
```

NACHHER:
```java
// n[0]/Users.java
interface Users {
    User with(Id id);
}

// n[0]/users/Persistent.java
final class Persistent implements Users {
    private final Database db;
    public Persistent(Database db) { this.db = db; }
    public User with(Id id) { ... }
}

// n[0]/users/Cached.java  
final class Cached implements Users {
    private final Users origin;
    private final Cache cache;
    public Cached(Users origin, Cache cache) { ... }
    public User with(Id id) { ... }
}
```

Projekt: [SPRING-PROJEKT]
```

---

## 9. Prompt: Test-Strategie für OOP-Packages

```
Erstelle Test-Strategie für OOP-Paketstruktur:

PRINZIPIEN:

1. Teste gegen Interfaces, nicht Implementierungen:
   ✅ test(Users users) { ... }
   ❌ test(PersistentUsers users) { ... }

2. Fake-Objekte statt Mocks:
   ✅ class FakeUsers implements Users { ... }
   ❌ Mockito.mock(Users.class)

3. Package-Level Tests:
   - Jedes Package ist eine testbare Einheit
   - Test alle Implementierungen gegen das gleiche Interface
   - Decoration testen (Kombination von Implementierungen)

4. Integration Tests nur für Leaf-Packages:
   - Packages mit hohem I (instabil) brauchen Integrationstests
   - Packages mit hohem A (abstrakt) brauchen nur Unit-Tests

STRUKTUR:
```
src/
  main/
    com.company.app/
      User.java (interface)
      user/
        Cached.java
        Persistent.java
  test/
    com.company.app/
      UserContract.java (test alle Implementierungen)
      user/
        CachedTest.java (extends UserContract)
        PersistentTest.java (extends UserContract)
        FakeUsers.java (für andere Tests)
```

AUSGABE:
1. Test-Struktur für alle Packages
2. Contract-Tests für Interfaces
3. Fake-Implementierungen
4. Integration-Test-Strategie

Paketstruktur: [STRUKTUR]
```

---

## 10. Prompt: Performance-Analyse der Struktur

```
Analysiere Performance-Implikationen der OOP-Struktur:

METRIKEN:

1. Compilation Units:
   - Anzahl .java-Dateien
   - Durchschnittliche LOC pro File
   - Compile-Time-Schätzung

2. Runtime Overhead:
   - Anzahl Interface-Indirections
   - Decorator-Chains-Länge
   - Object-Allocation-Rate

3. JAR Size:
   - Geschätzte Package-Größen
   - Mögliche Aufteilung in separate JARs
   - Tree-Shaking-Potential

4. Dependency Loading:
   - Anzahl transitiver Dependencies pro Package
   - Class-Loading-Order
   - Startup-Time-Schätzung

OPTIMIERUNGEN:

1. Package-Splitting:
   - Welche Packages können separate JARs werden?
   - Reduziert das Transitive Dependencies?

2. Inlining-Candidates:
   - Welche Interfaces haben nur eine Implementierung?
   - Sollten diese inlined werden?

3. Caching-Strategy:
   - Welche Decorator-Kombinationen sind häufig?
   - Können Instanzen gecached werden?

AUSGABE:
- Performance-Report
- Optimierungs-Vorschläge
- Trade-offs (Struktur vs Performance)

Paketstruktur: [STRUKTUR]
```

---

## Verwendungsbeispiel

### Komplett-Prompt für neues Projekt:

```
Ich möchte ein E-Commerce-System entwickeln.

Anforderungen:
- Produkte verwalten
- Bestellungen abwickeln
- Kunden authentifizieren
- Zahlungen verarbeiten
- Lager verwalten

Bitte:
1. Erstelle OOP-Paketstruktur (Prompt #1 + #6)
2. Berechne alle Metriken (Prompt #2)
3. Validiere Dependencies (Prompt #4)
4. Erstelle Test-Strategie (Prompt #9)
5. Zeige Vergleich zu typischer Spring-DDD-Struktur

Verwende com.company.ecommerce als Root-Package.
```

---

## Metriken-Zielwerte

| Metrik | Optimal | Akzeptabel | Problematisch |
|--------|---------|------------|---------------|
| Distance (D) | ≤ 0.2 | 0.2 - 0.4 | > 0.4 |
| Abstractness (A) für stabile Packages | > 0.7 | 0.5 - 0.7 | < 0.5 |
| Instability (I) für konkrete Packages | > 0.7 | 0.5 - 0.7 | < 0.5 |
| Zyklen in Dependencies | 0 | 0 | > 0 |
| Klassen pro Package | 3-7 | 8-12 | > 12 oder = 1 |

---

## Checkliste für valide OOP-Struktur

- [ ] Alle Packages haben gleichnamige Abstraktion im Parent
- [ ] Keine Zyklen in Package-Dependencies (DAG)
- [ ] Keine "-er" Suffixe in Klassennamen
- [ ] Keine "Client" Klassen mit 20+ Methoden
- [ ] Distance D ≤ 0.4 für 80%+ der Packages
- [ ] Abstrakte Packages sind stabil (I < 0.3)
- [ ] Konkrete Packages sind instabil (I > 0.7)
- [ ] Alle Klassen in p[n] implementieren a[n]
- [ ] Keine Utility-Klassen (nur static methods oder Lösung mit Decorator)
- [ ] Keine getter und setter
- [ ] Keine Service Klassen (die Logik ist in der Damain Klasse z.B. User)
- [ ] Keine Repositories Klassen (Lösung mit Decorator z.B. DbUser)
- [ ] Keine Aggregate Klassen (nur Damain Klassen z.B. User oder Users oder UserGroup)
- [ ] Keine DTOs ohne Verhalten (Daten sind ein Geheimnis von Damain Klassen)


---

## Referenzen

- **Yegor Bugayenko**: https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html
- **Package Design Principles**: Robert C. Martin
- **Main Sequence**: A + I = 1 (optimal)
- **Acyclic Dependencies**: Dependency structure must be a DAG
