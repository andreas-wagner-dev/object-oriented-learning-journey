Chat:
- https://chat.mistral.ai/chat/a779af79-b2ab-4a8d-b9c5-1568a6610030


Propm:

Formale Regeln für Paketstrukturen

1. Paketentstehung
Pakete entstehen ausschließlich auf der Ebene von gleichnamigen Abstraktionen und nur dann, wenn eine Abstraktion existiert:
```
∃p[n]  ⟺  ∃a[n]∈b[n−1]\exists p[n] \iff \exists a[n] \in b[n-1]∃p[n]⟺∃a[n]∈b[n−1]
```
a[n]: Abstraktion (Interface oder abstrakte Klasse)
p[n]: Paket mit Namen a[n].toLowerCase()
b[n-1]: Übergeordneter Namespace (z. B. com.example.pdc)

2. Namenskonventionen für Paketnamen:
Der Paketname muss dem Namen der korrespondierenden Abstraktion entsprechen:
- Kleinbuchstaben,Singularform, 1:1-Korrespondenz
- Formale Definition
```
∀p[n]:Name(p[n])=Name(a[n]).toLowerCase()∧isSingular(Name(a[n]))\forall p[n]: \text{Name}(p[n]) = \text{Name}(a[n]).\text{toLowerCase()} \land \text{isSingular}(\text{Name}(a[n]))∀p[n]:Name(p[n])=Name(a[n]).toLowerCase()∧isSingular(Name(a[n]))
```
1. Paketinhalte
Pakete dürfen nur Realisierungen ihrer gleichnamigen Abstraktion enthalten:
```
∀c∈p[n]:c⪯a[n]\forall c \in p[n]: c \preceq a[n]∀c∈p[n]:c⪯a[n]
```
- ⊑: "ist eine Realisierung von" (Implementierung, Vererbung oder Dekoration)
- c: Klasse im Paket
- a[n]: Abstraktion des Pakets

**4. Zyklenfreiheit**
Es dürfen keine zirkulären Abhängigkeiten zwischen Paketen existieren:
```
∄Zyklus (p1,p2,...,pn,p1) im Abha¨ngigkeitsgraphen\nexists \text{Zyklus } (p_1, p_2, ..., p_n, p_1) \text{ im Abhängigkeitsgraphen}∄Zyklus (p1​,p2​,...,pn​,p1​) im Abha¨ngigkeitsgraphen
```
Beispiel für ungültigen Zyklus:
```
com.example.billing.a → com.example.billing.b → com.example.billing.a
```



Attmpts:


**Chats**

1. Formale Definition der Hierarchie
Gegeben:

n[0]: Höchste Abstraktionsgrenze der Anwendung (Root-Namespace).
m: Anzahl aller Abstraktionen innerhalb der Grenze n[0].
Hierarchieebenen: n[0] bis n[m], wobei jede Ebene eine Abstraktion oder Implementierung repräsentiert.

Definition der Elemente
2.1 Boundary (Namespace-Grenze)
b[n]=Namespace auf Ebene nmit0≤n≤mb[n] = \text{Namespace auf Ebene } n \quad \text{mit} \quad 0 \leq n \leq mb[n]=Namespace auf Ebene nmit0≤n≤m

Beispiel:
b[0]=com.companyb[0] = \text{com.company}b[0]=com.company (Root-Namespace).
b[1]=com.company.billingb[1] = \text{com.company.billing}b[1]=com.company.billing (Kontext-Boundary).


2.2 Abstraktion
a[n+1]=Abstraktion auf Ebene n+1(Interface, abstrakte Klasse oder Domain-Konzept)a[n+1] = \text{Abstraktion auf Ebene } n+1 \quad \text{(Interface, abstrakte Klasse oder Domain-Konzept)}a[n+1]=Abstraktion auf Ebene n+1(Interface, abstrakte Klasse oder Domain-Konze

```mermaid

```


# Fehler-Lösungs-Tabelle


| Fehler | Exception-Meldung | Lösung |
|--------|------------------|--------|
| Fehlendes Paket | FEHLER: Paket fehlt für Abstraktion MissingPackage. Lösung: Erstelle Paket com.example.billing.missing und füge Realisierungen hinzu. | Paket erstellen. |
| Falscher Paketname | FEHLER: Paketname 'wrongname' stimmt nicht mit Abstraktion 'WrongName' überein. Erwartet: wrongname. Lösung: Benenne Paket in 'wrongname' um. | Paket umbenennen. |
| Leeres Paket | FEHLER: Paket com.example.billing.empty enthält keine Realisierungen der Abstraktion EmptyPackage. Lösung: Füge mindestens eine Klasse hinzu, die EmptyPackage implementiert/spezialisiert. | Realisierung hinzufügen. |
| Zirkuläre Abhängigkeit | Zirkuläre Abhängigkeit erkannt: CircularA → CircularB → CircularA. Vorschlag: 1. Extrahiere gemeinsame Schnittstelle 2. Verwende Dependency Injection 3. Restrukturiere Pakete nach Domain-Kontexten | Abhängigkeiten umstrukturieren. |
| Pluralform | FEHLER: Abstraktionsname 'Rules' ist im Plural. Verwende Singularform. | Umbenennen in Rule. |
| Falsche Realisierung | FEHLER: Klasse InvalidBill in Paket com.example.billing.bill realisiert nicht Bill. Lösung: 1. Implementiere Bill in InvalidBill 2. Verschiebe InvalidBill in das korrekte Paket | Klasse korrigieren oder verschieben. |


Vergleiche imperativen Stil gegen OOP-Stil mit den folgenden Anforderungen und Quellen:

**1 Fachliche Anforderungen:**

1.1 Priorisierung der Fehler:
- KRITISCH (🚨): Zirkuläre Abhängigkeiten
- HOCH (🔴): Fehlende Domain-Pakete/Pluralformen
- MITTEL (🟠): Falsche Paketnamen/leere Pakete

1.. Zyklenerkennung:
- Findet direkte und indirekte Zyklen
- Generiert detaillierte Pfadbeschreibungen
- Bietet 3 konkrete Lösungsvorschläge pro Zyklus als text Meldung


**2 Implementierungs Anforderungen:**

2.1 Object-Nameing:
https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html
https://www.yegor256.com/2015/03/09/objects-end-with-er.html
https://www.yegor256.com/2017/09/12/evil-object-name-suffix-client.html

2.2 Realisierung ausliißlier im OOP Stil für Dependency Injects ohne containers und Decorator-Pattern:
https://www.yegor256.com/2014/10/03/di-containers-are-evil.html
https://www.yegor256.com/2015/02/26/composable-decorators.html
https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html
https://www.yegor256.com/2016/01/26/defensive-programming.html

2.3 Package Design Check 'pdc' wie hier:
Quelle: https://javadevguy.wordpress.com/2017/12/18/happy-packaging/
Beachte Formale Regeln für Paketstrukturen:
2.3.1. Paketentstehung 
Pakete entstehen ausschließlich auf der Ebene von gleichnamigen Abstraktionen und nur dann, wenn eine Abstraktion existiert:
```
∃p[n]  ⟺  ∃a[n]∈b[n−1]\exists p[n] \iff \exists a[n] \in b[n-1]∃p[n]⟺∃a[n]∈b[n−1]
```
a[n]: Abstraktion (Interface oder abstrakte Klasse)
p[n]: Paket mit Namen a[n].toLowerCase()
b[n-1]: Übergeordneter Namespace (z. B. com.example.pdc)

2.3.3 Paketinhalte
Pakete dürfen nur Realisierungen ihrer gleichnamigen Abstraktion enthalten:
```
∀c∈p[n]:c⪯a[n]\forall c \in p[n]: c \preceq a[n]∀c∈p[n]:c⪯a[n]
```
- ⊑: "ist eine Realisierung von" (Implementierung, Vererbung oder Dekoration)
- c: Klasse im Paket
- a[n]: Abstraktion des Pakets

2.3.3 
Erweitere diese Paketstruktur

com.example.pdc/            (Namespace)
├── App.java                (Interface)
├── Package.java            (Interface)
├── Rule.java               (Interface)
├── Dependency.java         (Interface)
├── app/                    (Paket für App-Realisierungen)
│   ├── PDCApp.java      (implements App)
│   └── ConsolePDCApp.java (decorates PDCApp -> entry point)
├── package/                   (Paket für Package-Realisierungen)
│   ├── SimplePackage.java      (extends Package)
│   └── ....     
└── rule/                   (Paket für Rule-Realisierungen)
    ├── ....java           (Interface)
    └── .../                (Paket für ...-Realisierungen)
        └── ...java         (implements ..)

//
public ConsolePDCApp {
  //
  public static void main(String[] args){
  // initialisierung start 
  }
}

Hier ist der Beispielscode im imperativen Stil baue um nach OOP Still mit meinen Anforderungen und Quellen von oben:
```


void validatePackageRules(String rootPackage, Map<String, Exception> exceptions) {
    exceptions.clear();

    // Build dependency graph for cycle detection
    Map<String, Set<String>> dependencyGraph = buildDependencyGraph(rootPackage);

    // Find all circular dependencies with detailed analysis
    Map<String, List<CycleDescription>> allCycles = findAllCircularDependencies(dependencyGraph);

    // Add cycle errors to exceptions map (highest priority)
    for (Map.Entry<String, List<CycleDescription>> entry : allCycles.entrySet()) {
        for (CycleDescription cycle : entry.getValue()) {
            exceptions.put(entry.getKey(), new IllegalStateException(
                String.format("🚨 CRITICAL ERROR: Circular dependency detected:\n" +
                              "  Path: %s\n" +
                              "  %s\n" +
                              "  Solutions:\n" +
                              "  1. %s\n" +
                              "  2. %s\n" +
                              "  3. %s",
                              cycle.path,
                              cycle.description,
                              cycle.solution1,
                              cycle.solution2,
                              cycle.solution3)));
        }
    }

    if (!allCycles.isEmpty()) {
        return; // Abort validation if cycles found (highest priority)
    }

    // Validate all abstractions recursively with prioritization
    validateAbstractionsRecursive(rootPackage, rootPackage, new HashSet<>(), exceptions);
}

/**
 * Finds all circular dependencies in the package graph
 * @return Map of package names to lists of cycle descriptions
 */
private Map<String, List<CycleDescription>> findAllCircularDependencies(Map<String, Set<String>> graph) {
    Map<String, List<CycleDescription>> allCycles = new HashMap<>();
    Set<String> visited = new HashSet<>();

    for (String node : graph.keySet()) {
        if (!visited.contains(node)) {
            List<CycleDescription> nodeCycles = new ArrayList<>();
            findCyclesDFS(graph, node, node, visited, new HashSet<>(), new ArrayList<>(), nodeCycles);
            if (!nodeCycles.isEmpty()) {
                allCycles.put(node, nodeCycles);
            }
        }
    }
    return allCycles;
}

/**
 * Depth-first search to find cycles in the dependency graph
 */
private void findCyclesDFS(Map<String, Set<String>> graph, String node, String startNode,
                          Set<String> visited, Set<String> recursionStack,
                          List<String> currentPath, List<CycleDescription> cycles) {
    visited.add(node);
    recursionStack.add(node);
    currentPath.add(node);

    for (String neighbor : graph.getOrDefault(node, Collections.emptySet())) {
        if (!visited.contains(neighbor)) {
            findCyclesDFS(graph, neighbor, startNode, visited, recursionStack, currentPath, cycles);
        } else if (recursionStack.contains(neighbor)) {
            // Cycle found - create detailed description
            int index = currentPath.indexOf(neighbor);
            List<String> cyclePath = new ArrayList<>(currentPath.subList(index, currentPath.size()));
            cyclePath.add(neighbor); // Close the cycle

            String path = String.join(" → ", cyclePath);
            String description = analyzeCycle(cyclePath, graph);
            CycleDescription cycleDesc = createCycleDescription(path, description);
            cycles.add(cycleDesc);
        }
    }

    recursionStack.remove(node);
    currentPath.remove(currentPath.size() - 1);
}

/**
 * Analyzes the nature of the cycle
 */
private String analyzeCycle(List<String> cyclePath, Map<String, Set<String>> graph) {
    if (cyclePath.size() == 2) {
        return "Direct circular dependency between two packages.";
    } else {
        return String.format("Indirect circular dependency through %d packages.", cyclePath.size() - 1);
    }
}

/**
 * Creates a detailed cycle description with solutions
 */
private CycleDescription createCycleDescription(String path, String description) {
    String[] packages = path.split(" → ");
    String package1 = packages[0];
    String package2 = packages[1];

    String solution1 = String.format("Extract common interface from %s and %s into a new 'shared' package.",
                                      package1, package2);
    String solution2 = String.format("Use Dependency Injection (e.g. @Inject) in %s instead of direct instantiation of %s.",
                                      package1, package2);
    String solution3 = String.format("Restructure packages by domain contexts (e.g. move %s and %s to separate modules).",
                                      package1, package2);

    // Add code examples for the first solution
    if (packages.length == 2) {
        solution1 += String.format("\n" +
                "Example:\n" +
                "// In package %s:\n" +
                "public interface CommonInterface {\n" +
                "    void commonMethod();\n" +
                "}\n\n" +
                "// In package %s:\n" +
                "public class ClassA implements CommonInterface {\n" +
                "    @Override\n" +
                "    public void commonMethod() {\n" +
                "        // Implementation\n" +
                "    }\n" +
                "}\n\n" +
                "// In package %s:\n" +
                "public class ClassB {\n" +
                "    private final CommonInterface dependency;\n\n" +
                "    @Inject\n" +
                "    public ClassB(CommonInterface dependency) {\n" +
                "        this.dependency = dependency;\n" +
                "    }\n" +
                "}", package1, package1, package2);
    }

    return new CycleDescription(path, description, solution1, solution2, solution3);
}

/**
 * Validates abstractions recursively with prioritization
 */
private boolean validateAbstractionsRecursive(String currentPackage, String rootPackage,
                                             Set<String> visitedPackages,
                                             Map<String, Exception> exceptions) {
    try {
        // Find all abstractions in current package (sorted by priority)
        List<Class<?>> abstractions = findAbstractions(currentPackage);

        // Sort abstractions by priority:
        // 1. Aggregators (e.g. Billing)
        // 2. Domain concepts (e.g. Bill, Rule)
        // 3. Technical abstractions (e.g. App, Resource)
        abstractions.sort((a, b) -> {
            if (isAggregator(a)) return -1;
            if (isAggregator(b)) return 1;
            if (isDomainConcept(a) && isTechnicalConcept(b)) return -1;
            if (isTechnicalConcept(a) && isDomainConcept(b)) return 1;
            return 0;
        });

        for (Class<?> abstraction : abstractions) {
            String abstractionName = abstraction.getSimpleName();

            // Rule: Names must be singular (high priority)
            if (abstractionName.matches(".*[sS]$") && !isValidPlural(abstractionName)) {
                exceptions.put(currentPackage, new IllegalStateException(
                    String.format("🔴 ERROR (Priority HIGH): Abstraction name '%s' is plural.\n" +
                                  "  Solution: Rename to singular form (e.g. 'Rule' instead of 'Rules').\n" +
                                  "  Exceptions: ISO standards (e.g. 'ISOS') are allowed.",
                                  abstractionName)));
                continue;
            }

            String expectedPackagePath = currentPackage + "." + abstractionName.toLowerCase();

            // Avoid cycles in validation
            if (visitedPackages.contains(expectedPackagePath)) {
                exceptions.put(expectedPackagePath, new IllegalStateException(
                    String.format("🟠 ERROR (Priority MEDIUM): Circular package reference: %s\n" +
                                  "  Solution: Check package structure for redundant references.",
                                  expectedPackagePath)));
                continue;
            }
            visitedPackages.add(expectedPackagePath);

            // Rule 1: Package must exist (highest priority for domain concepts)
            if (!packageExists(expectedPackagePath)) {
                String priority = isDomainConcept(abstraction) ? "HIGH" : "MEDIUM";
                exceptions.put(expectedPackagePath, new IllegalStateException(
                    String.format("🔴 ERROR (Priority %s): Package missing for abstraction %s.\n" +
                                  "  Expected: %s\n" +
                                  "  Solution:\n" +
                                  "  1. Create package '%s'\n" +
                                  "  2. Add at least one realization (e.g. %sImpl.java)",
                                  priority, abstractionName, expectedPackagePath,
                                  abstractionName.toLowerCase(), abstractionName)));
                continue;
            }

            // Rule 2: Package name must match abstraction name
            String actualPackageName = getPackageName(expectedPackagePath);
            if (!actualPackageName.equals(abstractionName.toLowerCase())) {
                exceptions.put(expectedPackagePath, new IllegalStateException(
                    String.format("🟠 ERROR (Priority MEDIUM): Package name '%s' doesn't match abstraction '%s'.\n" +
                                  "  Expected: %s\n" +
                                  "  Solution:\n" +
                                  "  1. Rename package to '%s'\n" +
                                  "  2. Update all imports in dependent classes",
                                  actualPackageName, abstractionName,
                                  abstractionName.toLowerCase(), abstractionName.toLowerCase())));
            }

            // Rule 3: Package must contain realizations (high priority)
            List<Class<?>> realizations = findClassesInPackage(expectedPackagePath);
            if (realizations.isEmpty()) {
                String priority = isDomainConcept(abstraction) ? "HIGH" : "MEDIUM";
                exceptions.put(expectedPackagePath, new IllegalStateException(
                    String.format("🔴 ERROR (Priority %s): Package %s contains no realizations of abstraction %s.\n" +
                                  "  Solution:\n" +
                                  "  1. Add a class that implements %s (e.g. %sImpl.java)\n" +
                                  "  2. If abstraction is not needed: Delete it",
                                  priority, expectedPackagePath, abstractionName,
                                  abstractionName, abstractionName)));
            } else {
                // Check each realization
                for (Class<?> realization : realizations) {
                    if (!isRealizationOf(realization, abstraction)) {
                        exceptions.put(expectedPackagePath, new IllegalStateException(
                            String.format("🟠 ERROR (Priority MEDIUM): Class %s in package %s doesn't realize %s.\n" +
                                          "  Current superclasses/interfaces: %s\n" +
                                          "  Solution:\n" +
                                          "  1. Implement %s in %s:\n" +
                                          "     public class %s extends/implements %s {\n" +
                                          "         // Implementation\n" +
                                          "     }\n" +
                                          "  2. Move %s to the correct package",
                                          realization.getSimpleName(), expectedPackagePath, abstractionName,
                                          Arrays.toString(realization.getInterfaces()),
                                          abstractionName, realization.getSimpleName(),
                                          realization.getSimpleName(), abstractionName,
                                          realization.getSimpleName())));
                    }
                }
            }

            // Recursive validation for sub-abstractions
            if (!validateAbstractionsRecursive(expectedPackagePath, rootPackage,
                                               new HashSet<>(visitedPackages), exceptions)) {
                return false;
            }
        }
    } catch (Exception e) {
        exceptions.put(currentPackage, new IllegalStateException(
            String.format("❌ ERROR: Validation of %s failed: %s", currentPackage, e.getMessage())));
        return false;
    }
    return true;
}

/**
 * Checks if a class is a domain concept
 */
private boolean isDomainConcept(Class<?> clazz) {
    String name = clazz.getSimpleName();
    return name.matches("^(Bill|Rule|Tax|Unit|Billing|User)$");
}

/**
 * Checks if a class is a technical concept
 */
private boolean isTechnicalConcept(Class<?> clazz) {
    String name = clazz.getSimpleName();
    return name.matches("^(App|Resource|Menu)$");
}

/**
 * Checks if a class is an aggregator
 */
private boolean isAggregator(Class<?> clazz) {
    String name = clazz.getSimpleName();
    return name.equals("Billing");
}

/**
 * Checks if a plural form is valid (e.g. ISOS)
 */
private boolean isValidPlural(String name) {
    return name.equals("ISOS");
}

/**
 * Describes a circular dependency with solutions
 */
class CycleDescription {
    String path;
    String description;
    String solution1;
    String solution2;
    String solution3;

    public CycleDescription(String path, String description, String solution1, String solution2, String solution3) {
        this.path = path;
        this.description = description;
        this.solution1 = solution1;
        this.solution2 = solution2;
        this.solution3 = solution3;
    }
}

```
