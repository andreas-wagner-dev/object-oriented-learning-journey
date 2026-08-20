# **Open Knowledge Format (OKF) — Index-Based Structural Specification**

This specification formalizes the Open Knowledge Format [(OKF)](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing?hl=en) as a purely file-based, indexed rule system. It translates the "LLM-Wiki-Pattern" into an unambiguous, recursive folder and file structure that can be read, indexed, and manipulated by AI agents and humans alike without a translation layer.

## **1. Core Principles & Mandatory Combined Anchor Design**

To ensure that the file-based knowledge system remains highly readable, intuitive, and easy for AI agents to process, every single folder in an OKF tree must strictly implement the Single Combined Anchor Pattern via adr_index.md.

The Combined Anchor (adr_index.md) serves a dual purpose in a single file:

1. Decision Log (ADR): Records why architectural, domain, or operational decisions were made at this specific scope level, including trade-offs, context, and current status.  
2. Structural Index: Defines what the domain is, its boundaries, sub-domains, navigation, and active concepts.

### **Pragmatic Rules**

* Mandatory Anchor Rule: Every folder at every hierarchy level MUST contain an adr_index.md file as its very first element at sequential index position .1.   
* Locality of Architectural Decisions: Decisions belong to the exact domain level they affect. Global architecture decisions sit at Level 0 (`AI[1]`), while localized domain or sub-domain decisions reside in their respective sub-directories inside that sub-domain's adr_index.md.  
* Strict Alphabetical Precedence & Sequential Indexing: Every item inside a directory receives a strictly unique, consecutive index integer based on its alphabetical position. adr_index.md is always index .1, followed strictly by concepts, snippets, and subdirectories in continuous sequence (.2, .3, .4, ...).
  
To ensure that the file-based knowledge system remains highly readable, intuitive, and easy for AI agents to process, combine these [three core guidelines](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/):

1. Folders Should Never Depend on Sub-Folders: Dependencies must always point inward (or downward).  
2. Sub-Folders Do Not Introduce New Concepts, Just More Details: A sub-folder must only exist to expand upon, refine, or detail a concept already introduced in its parent folder.
3. Folders Must Reflect Business Domains, Not Technical Layers: Organize by business domains (e.g., `billing/`, `customer/`), not technical roles (controllers/, models/).
  
By enforcing `adr_index.md` (.1) in every directory, any human or AI agent navigating into any folder gets an immediate, self-contained overview of both the governing decision history and the domain's structure in one place.

## **2. Abstract Structure Model (Tree)**

According to the Open Knowledge Format (OKF), the system controls knowledge domains exclusively through combined decision/structure anchors (adr_index.md), category directories, and atomic concepts/snippets. All directory contents are strictly sorted in alphabetical order and indexed sequentially.

```text
sales/  
├── adr_index.md  
├── datasets/  
│   ├── adr_index.md  
│   └── orders_db.md  
├── metrics/  
│   ├── adr_index.md  
│   └── weekly_active_users.md  
└── tables/  
    ├── adr_index.md  
    ├── customers.md  
    └── orders.md
    
```

### **2.1. Formalization**

Let the knowledge tree be defined by the following set of elements, where every element receives a strictly unique, consecutive sequence number based on its alphabetical position within its parent folder:

* `N[k=0]`: Root folder / global namespace (Level 0).  
* `AI[k]`: Combined Decision & Structure Anchor (`adr_index.md`) at logical path sequence `k`.  
* `C[k]`: Concept file at logical path sequence k.  
* `D[k]`: Directory / knowledge domain at logical path sequence `k`.  
* `S[k]`: Support snippet / asset file at logical path sequence `k`.

#### **Index Chains**

Every tree node is mapped to a strictly unique, sequential structural index chain `k`.

Let `k` be a sequence of positive integers:

`k = [x1, x2, ..., xn]`

where `x1, x2, ..., xn` are positive integers representing the exact 1-based alphabetical position of each element within its parent folder.

Dot-notation represents sequence concatenation:

`k.y = [x1, x2, ..., xn, y]`

The string representation of an index chain replaces brackets with raw dot-delimited digits (e.g., 2.4.3.1).

```text
N/                                    # Root folder (Level 0)  
│  
├── adr_index.md                      # AI[1]      (Global combined anchor: 1st element)  
├── D[2]/                             # D[2]       (First directory Level 1: 2nd element)  
│   ├── adr_index.md                  # AI[2.1]    (Anchor of domain D[2]: 1st item in D[2])  
│   ├── C[2.2].md                     # C[2.2]     (First concept in D[2]: 2nd item in D[2])  
│   ├── S[2.3].json                   # S[2.3]     (First snippet in D[2]: 3rd item in D[2])  
│   └── D[2.4]/                       # D[2.4]     (Subdirectory Level 2: 4th item in D[2])  
│       ├── adr_index.md              # AI[2.4.1]  (Anchor: 1st item in D[2.4])  
│       ├── C[2.4.2].md               # C[2.4.2]   (First concept: 2nd item in D[2.4])  
│       └── D[2.4.3]/                 # D[2.4.3]   (Sub-subdirectory Level 3: 3rd item in D[2.4])  
│           ├── adr_index.md          # AI[2.4.3.1] (Anchor: 1st item in D[2.4.3])  
│           └── C[2.4.3.2].md         # C[2.4.3.2] (Concept: 2nd item in D[2.4.3])  
│  
└── D[*]/                             # D[*]       (Any directory on Level 1)  
    ├── adr_index.md                  # AI[*.1]    (Anchor: 1st item)  
    ├── C[*.2].md                     # C[*.2]     (First concept: 2nd item)  
    ├── S[*.3].sql                    # S[*.3]     (First snippet: 3rd item)  
    ├── D[*.4]/                       # D[*.4]     (First Subdirectory Level 2: 4th item)  
    └── D[*.5]/                       # D[*.5]     (Second Subdirectory Level 2: 5th item)  
        ├── adr_index.md              # AI[*.5.1]  (Anchor: 1st item in D[*.5])  
        └── D[*.5.2]/                 # D[*.5.2]   (Sub-subdirectory: 2nd item in D[*.5])
```

The Wildcard `[*]` notation is a placeholder denoting an arbitrary valid index component sequence.

## **2.2. Axioms (Structural Core Rules)**

1. Domain Anchor Existence (Directory Axiom)  
   For every existing domain or subdomain directory `D[k]`, there MUST exist an associated combined decision and structure anchor `AI[k.1]` (adr_index.md) at sequence index position 1 as a direct child element.  
2. Exclusive Content Area & Consecutive Indexing (Content Rule)  
   A directory `D[k]` may only contain its combined anchor `AI[k.1]`, concepts `C[k.m]`, support snippets `S[k.o]`, or deeper subdirectories `D[k.n]`. Every element in a domain D[k] is assigned a strictly fortlaufend (consecutive) index (.1, .2, .3, .4, ...) determined by alphabetical sorting. No two elements in the same directory may share an index number.  
3. Root Anchor Existence (Root Axiom)  
   The top level of the bundle (the root folder `N`) MUST contain exactly one global combined anchor `AI[1]` (adr_index.md) at index position 1.  
4. Sub-Domain Existence (Sub-Directory Axiom)  
   A subdomain` D[k.n]` is always created as a direct child element of a directly superior parent domain `D[k]`. It inherits the Combined Anchor requirement (AI[k.n.1] as adr_index.md).

## **3. Practical Example (E-Commerce Order System)**

Below is a practical application of the OKF mapping a business domain (E-Commerce Orders). Every single directory strictly contains adr_index.md as its first element (.1), all items are sorted alphabetically, and every element has a unique, fortlaufend index position.

```text
e-commerce/                            # N Root Folder (namespace)  
├── adr_index.md                       # AI[1] (Global decisions & system overview)  
│  
├── billing/                           # D[2] (Domain: Billing - 2nd element in N)  
│   ├── adr_index.md                   # AI[2.1] (Currency exchange decisions & billing context)  
│   ├── invoice/                       # D[2.2] (Sub-Domain: Invoices - 2nd element in D[2])  
│   │   ├── adr_index.md               # AI[2.2.1] (PDF engine decisions & invoice overview)  
│   │   └── tax_calculation.md         # C[2.2.2] (Atomic logic: Tax calculation rules)  
│   └── payment_methods.md             # C[2.3] (Concept: Supported payment types - 3rd element in D[2])  
│  
├── customer/                          # D[3] (Domain: Customer Context - 3rd element in N)  
│   ├── adr_index.md                   # AI[3.1] (GDPR decisions & customer context map)  
│   ├── payment/                       # D[3.2] (Sub-Domain: Payment - 2nd element in D[3])  
│   │   ├── adr_index.md               # AI[3.2.1] (Stripe PCI scope decisions & payment overview)  
│   │   ├── paypal_flow.mermaid        # S[3.2.2] (Snippet: Visual authentication flow chart)  
│   │   └── stripe_payload.json        # S[3.2.3] (Snippet: Raw API payload structure)  
│   └── profile.md                     # C[3.3] (Concept: Customer profile schema & rules - 3rd element in D[3])  
│  
└── logistic/                          # D[4] (Domain: Logistics - 4th element in N)  
    ├── adr_index.md                   # AI[4.1] (Real-time consistency decisions & logistics context map)  
    ├── shipping/                      # D[4.2] (Sub-Domain: Shipping fulfillment - 2nd element in D[4])  
    │   ├── adr_index.md               # AI[4.2.1] (Multi-carrier decisions & shipping overview)  
    │   └── tracking/                  # D[4.2.2] (Sub-Domain: Tracking - 2nd element in D[4.2])  
    │       ├── adr_index.md           # AI[4.2.2.1] (Webhook retry decisions & tracking overview)  
    │       └── dhl_webhook.sql        # S[4.2.2.2] (Snippet: Database schema)  
    └── stock_management.md            # C[4.3] (Concept: Inventory logic - 3rd element in D[4])

```

### **Key Takeaways**

* **Strict Universality (adr_index.md)**: Every single directory across the hierarchy (`sales/`, `datasets/`, `tables/`, `billing/`, `invoice/`, `shipping/`, `tracking/`, etc.) strictly holds `adr_index.md` as its mandatory entry point at sequential index .1.  
* **Structural + Rationale Parity**: The combined anchor adr_index.md eliminates separate index/ADR file bloat by answering both *"What is in this domain?"* (structural map, active concepts, taxonomy) and ***"Why was this domain designed this way?"*** (decision records, architectural constraints, trade-offs) in one cohesive document.  
* **Localized Context**: An AI agent or human developer navigating into any sub-folder (e.g., `logistic/shipping/tracking/`) immediately obtains both the precise domain context and the relevant operational/architectural decisions locally, eliminating the need to search or parse upper-level decision logs.  
* **Strictly Fortlaufende (Sequential) Indexing**: Index position `.1` is permanently reserved for `adr_index.md`. All sibling elements follow in unbroken, sequential integer sequence (`.2, .3, .4, ...`).  
* **Deterministic Precedence**: Ordering directory contents strictly by alphabetical name establishes a stable, reproducible, and unambiguous indexing system across all tools and environments.
