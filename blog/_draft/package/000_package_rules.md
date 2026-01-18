```mermaid
graph TD
    subgraph Root_Package ["Root Package: Business Domain"]
        style Root_Package fill:#f9f,stroke:#333,stroke-width:2px
        TheSystem(ISystemComposition<br/>Interface/Abstract Class)<-->Rule1[Rule 1: Central Entry Point]
        ConceptA(ICoreConceptA<br/>Interface)
        ConceptB(ICoreConceptB<br/>Interface)
    end

    subgraph Sales_Context ["Subpackage: Sales Context"]
        style Sales_Context fill:#ccf,stroke:#333,stroke-width:1px
        SalesImpl(SalesImplementation<br/>implements ICoreConceptA)-->Rule2a[Rule 2: Implements Parent Concept]
    end

    subgraph Shipping_Context ["Subpackage: Shipping Context"]
        style Shipping_Context fill:#ccf,stroke:#333,stroke-width:1px
        ShipImpl(ShippingImplementation<br/>implements ICoreConceptB)-->Rule2b[Rule 2: Implements Parent Concept]
    end

    Sales_Context -->|Depends on| Root_Package
    Shipping_Context -->|Depends on| Root_Package
    
    Rule3[Rule 3: Packages follow<br/>Ubiquitous Language] -.-> Sales_Context
    Rule3 -.-> Shipping_Context

    linkStyle 3,4,5 stroke-width:2px,fill:none,stroke:blue;
```

```mermaid
graph TD
    %% Definitions for styling
    classDef root fill:#f0f0f0,stroke:#333,stroke-width:4px,font-weight:bold;
    classDef sub fill:#e6f2ff,stroke:#333,stroke-width:2px;
    classDef note fill:#ffffcc,stroke:none;

    %% ROOT PACKAGE (Rule 1)
    subgraph ROOT["ROOT PACKAGE (Context Boundary)"]
        direction TB
        ENTRY[("ISystemEntryPoint<br>(Composition Interface)")]
        CONCEPTS[/"Core Domain Interfaces<br>(e.g., IOrder, ICustomer)"/]
        R1_NOTE(Rule 1: Central definitions & Entry Point exclusively here.)
    end
    class ROOT root;

    %% DEPENDENCY FLOW (Rule 2)
    NOTE_DEP[/"Rule 2: Dependency points upwards.<br>Sub-packages only implement defined interfaces.<br>NO NEW CONCEPTS."/]
    class NOTE_DEP note;

    %% SUB-PACKAGES (Rule 3)
    subgraph SUB1["SUB-PACKAGE: 'Sales'"]
        IMPL1["Sales implementations"]
    end
    class SUB1 sub;

    subgraph SUB2["SUB-PACKAGE: 'Logistics'"]
        IMPL2["Logistics implementations"]
    end
    class SUB2 sub;

    subgraph SUB3["SUB-PACKAGE: 'Billing'"]
        IMPL3["Billing implementations"]
    end
    class SUB3 sub;

    %% Relationships
    SUB1 -.- NOTE_DEP
    NOTE_DEP --> ROOT
    SUB2 --> ROOT
    SUB3 --> ROOT

    %% Rule 3 Note placeholer
    R3_NOTE(Rule 3: All packages named using Ubiquitous Language.)
    class R3_NOTE note;
    SUB1 --- R3_NOTE
    SUB2 --- R3_NOTE
    SUB3 --- R3_NOTE

    linkStyle 1,2,3 stroke-width:2px,fill:none,stroke:black;
```
    
Summary of Rules
Rule 1: The Root Foundation The Root Package holds the System Entry Point (Composition Interface) and all core abstract domain concepts. It defines the "what".

Rule 2: Strict Implementation Dependency Sub-packages depend on the Root. They only contain implementations of interfaces defined above. They never introduce new interfaces or concepts.

Rule 3: Ubiquitous Language Naming Package names and structures strictly follow business terminology, ensuring shared understanding between stakeholders and developers.
