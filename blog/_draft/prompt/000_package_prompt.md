# Universal Prompt for AI: OOP Package Structure based on an Axiomatic, Index-Based Rule System

## OBJECTIVE

Generate a package structure for any given project requirement (Java, C#, Python, etc.) according to these axioms and rules (adjusting syntax to the target language). The rules are universal, recursive, and formally applicable to any OOP domain.

## DEFINITIONS

* n[0] = com.company. = Global namespace (Level 0)
* a[n] = Abstraction at level n (Interface/Abstract Class)
* aa[n.m] = Aggregate abstraction at level n, derived from a[n] (e.g., `Collection/Aggregate Interface`)
* p[n] = Package containing implementations of a[n] and aa[n.m]

## AXIOMS

**Existence Rule for Abstractions:**

`a[n] ∈ {n[0], n[0].*}`

`aa[n.m] ∈ {n[0], n[0].*}`

(Every single and aggregate abstraction exists at level n within the namespace.)

**Existence Rule for Packages:**

`∃ a[n] ∨ ∃ aa[n.m] : a[n], aa[n.m] ∈ {n[0], n[0].*} ⇒ p[n] may exist.`

(A package is created at the same level as its associated abstractions and contains only their implementations.)

**Content Rule:**

`∀ c ∈ p[n] : c ⊑ a[n] ∨ c ⊑ aa[n.m]`

(A package contains ONLY implementations/specializations of the corresponding single and aggregate abstractions.)

**Index Rule for Implementations:**

Every implementation receives the full index chain of its origin abstraction or aggregate as a prefix:

**Single Abstraction:** `ia[n.m.k].java`

**Aggregate:** `iaa[n.m.k.l].java`

(The index chain is recursive and unique.)

**Recursion Rule:**

Further abstractions and their implementations are stored recursively according to the same schema in sub-packages p[n.m]/.

(The index chain extends with each level.)

## RULES (Index-based and Recursive)

**Namespace**
The global namespace is n[0] (e.g., com.company.[name]).

**Abstractions**
Each abstraction is named a[k].java and is located directly in the namespace of level k.
Aggregate abstractions are named aa[k.n].java and receive the origin abstraction k.n as their index.

**Packages for Implementations**  
For every abstraction at level k, there exists a (usually eponymous) package `p[k]/` at the same level as the abstraction.
The package `p[k]/` contains only implementations/specializations of `a[k]` and associated aggregates.

**Implementations**  
Implementations use the full index chain of their origin abstraction as a prefix:

**Single Abstraction:** `ia[k.n.m].java`

**Aggregate:** `iaa[k.n.m.l].java`  
The indices are extended recursively at deeper levels.

**Recursion and Sub-abstractions**  
Additional abstractions (e.g., sub-abstractions) are stored recursively in sub-packages (`p[k.n]/`) following the same schema.
The index chain is continued for each sub-abstraction and its implementations.

## EXAMPLE STRUCTURE (Arbitrary Depth)
```
n[0]                                   # Global namespace, e.g., com.company.[name]
├── a[1].java                          # Abstraction Level 1
├── aa[1.1].java                       # Aggregate abstraction for a[1]
├── p1/                                # Package for implementations of a[1] and aa[1.1]
│   ├── ia[1.1.1].java                 # 1st implementation of a[1.1]
│   ├── ia[1.1.2].java                 # 2nd implementation of a[1.1]
│   ├── iaa[1.1.1.1].java              # 1st implementation of aa[1.1]
│   ├── iaa[1.1.1.2].java              # 2nd implementation of aa[1.1]
│   ├── a[1.2].java                    # Abstraction Level 2 (Prefix 1)
│   ├── aa[1.2].java                   # Aggregate abstraction Level 2
│   └── p1.2/                          # Package for implementations of a[1.2] and aa[1.2]
│       ├── ia[1.2.1].java             # 1st implementation of a[1.2]
│       ├── iaa[1.2.1.1].java          # 1st implementation of aa[1.2]
│       └── ...                        # Further depth follows same logic

```

