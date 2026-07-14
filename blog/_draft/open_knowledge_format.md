# Open Knowledge Format (OKF) — Index-Based Structural Specification

This specification formalizes the [**Open Knowledge Format (OKF)**](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing?hl=en) as a purely file-based, indexed rule system. 
It translates the **"LLM-Wiki-Pattern"** into an unambiguous, recursive folder and file structure that can be read, indexed,
and manipulated by AI agents and humans alike without an translation layer.

The system controls knowledge domains exclusively through structure anchors (index files), category directories, and atomic concepts.

## 1. Definitions

* **N:**: The root folder / global `namespace` (Level 0).  
* **I[k]:** A structure anchor (the `index.md` file) at logical path level `k`
* **D[k]:** A directory / knowledge domain at logical path level `k`
* **C[k.m]:** A specific concept file within domain `k` with the sequential object number `m`
* **S[k.o]:** A support snippet / asset file within domain `k` with the sequential object number `o`

**Mathematical Index Chains**

Every system node is mapped to a structural index chain `k`.
* Let `k` be a sequence of positive integers: `k` = `[x1, x2, ..., xn]`, where `xi` are positive integers
* Dot-notation represents sequence concatenation: `k.y` = `[x1, x2, ..., xn, y]`
* The string representation of an index chain replaces brackets with raw dot-delimited digits (e.g., `1.4.3.2`)  

The Wildcard `[*]` notation is a placeholder  denotes an arbitrary valid index component sequence.

## **2. Abstract Structure Model (Tree)**

```text
N/                                    # Root folder (Level 0)
│
├── index.md                          # I[1]       (Main structure anchor)
├── D[1]/                             # D[1]       (Main directory Level 1)
│   ├── index.md                      # I[1.1]     (Structure anchor of domain D[1])
│   ├── C[1.2].md                     # C[1.2]     (First concept in D[1])
│   ├── S[1.3].json                   # S[1.3]     (First snippet in D[1])
│   └── D[1.4]/                       # D[1.4]     (Subdirectory Level 2)
│       ├── index.md                  # I[1.4.1]   (Structure anchor of subdomain D[1.4])
│       ├── C[1.4.2].md               # C[1.4.2]   (First concept in subdomain D[1.4])
│       └── D[1.4.3]/                 # D[1.4.3]   (Sub-subdirectory Level 3)
│           ├── index.md              # I[1.4.3.1] (Structure anchor Level 3)
│           └── C[1.4.3.2].md         # C[1.4.3.2] (Concept on Level 3)
│
└── D[*]/                             # D[*]       (Any directory n auf Level 1)
    ├── index.md                      # I[*.1]     (Structure anchor of domain D[*])
    ├── C[*.2].md                     # C[*.2]     (First concept in D[*])
    ├── S[*.3].sql                    # S[*.3]     (First snippet in D[*])
    ├── D[*.4]/                       # D[*.4]     (Subdirectory Level 2)
    └── D[*.5]/                       # D[*.5]     (Second Subdirectory Level 2)
        ├── index.md                  # I[*.5.1]   (Structure anchor auf Level 2)
        └── D[*.5.2]/                 # D[*.5.2]   (Infinite recursion starts here)
```

### Axioms (Structural Core Rules)

#### Root Existence (Root Axiom)

The top level of the bundle (the root folder `N`) must contain exactly one initial structure anchor, designated as `I[1]` (the `index.md` file).

#### Domain Existence (Directory Axiom)

For every existing domain `D[k]`, there must be an associated structure anchor `I[k.1]` (the `index.md` file) as a direct child element. 
This explicitly declares the context for LLMs on this level.

#### Sub-Domain Existence (Sub-Domain Axiom)

A subdomain `D[k.n]` is always created as a direct child element of a directly superior parent domain `D[k]`.

#### Exclusive Content Area (Content Rule)

A directory may only contain its own structure anchor, concepts belonging to the same level, support snippets, or deeper subdirectories. Loose, unstructured files outside of this pattern are not permitted. Every element in a domain `D[k]` must be either the structure anchor `I[k.1]`, a concept `C[k.m]`, a snippet `S[k.o]`, or a subdomain `D[k.n]`.


## 3. Practical Example (E-Commerce Order System)

Below is a practical application of the OKF mapping a business domain (E-Commerce Orders) without any technical boilerplate clutter.

```text

E-Commerce-Project/                    # N Root Folder (namespace)
├── index.md                           # I[1] (System overview)
│
├── customer/                          # D[1] (Domain: Customer Context)
│   ├── index.md                       # I[1.1] (Context map for customer)
│   ├── profile.md                     # C[1.2] (Concept: Customer profile & GDPR guidelines) 
│   └── payment/                       # D[1.3] (Sub-Domain: Payment)
│       ├── index.md                   # I[1.3.1] (Overview of payments)
│       ├── stripe_payload.json        # S[1.3.2] (Snippet: Raw API payload structure for Stripe)
│       └── paypal_flow.mermaid        # S[1.3.3] (Snippet: Visual authentication flow chart)
│
├── billing/                           # D[2] (Domain: Billing)
│   ├── index.md                       # I[2.1] (Billing context)
│   ├── payment_methods.md             # C[2.2] (Concept)
│   └── invoices/                      # D[2.3] (Sub-Domain: Invoices)
│       ├── index.md                   # I[2.3.1] (Invoice context)
│       └── tax_calculation.md         # C[2.3.2] (Atomic logic: Tax calculation rules)
│
└── logistics/                         # D[3] (Domain: Logistics)
    ├── index.md                       # I[3.1] (Context map for logistics)
    ├── stock_management.md            # C[3.2] (Concept: Inventory logic & safety stock thresholds)
    └── shipping/                      # D[3.3] (Sub-Domain: Shipping fulfillment)
        ├── index.md                   # I[3.3.1] (Shipping context)
        └── tracking/                  # D[3.3.2] (Sub-Domain: Tracking)
            ├── index.md               # I[3.3.2.1] (Tracking context)
            └── dhl_webhook.sql        # S[3.3.2.2] (Snippet: Database schema for DHL webhook updates)


```

In this setup:

* The folder `customer` remains entirely independent of how nested `payment` details work.
* The folders under `billing` (like `invoices`) do not declare new global contexts; they strictly specialize and detail the concepts introduced in their parent directories.
* The folders `logistics`, `shipping`, and `tracking` demonstrate recursive refinement without polluting the namespace.

## 4. Three Pragmatic Rules

To ensure that the file-based knowledge system remains highly readable, intuitive, and easy for AI agents to process, follow these [three core guidelines](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/):

1. **Folders Should Never Depend on Sub-Folders:**  
   Dependencies must always point inward (or downward).
3. **Sub-Folders Do Not Introduce New Concepts, Just More Details:**  
   A sub-folder must only exist to expand upon, refine, or detail a concept already introduced in its parent folder.
5. **Folders Must Reflect Business Concepts, Not Technical Ones:**  
   Organize by business domains (e.g., `customer/`, `billing/`), not technical roles (`controllers/`, `models/`).

## 5. Summary

The Trick to achieving the next-level readability is focusing heavily on the highest (parent) hierarchy level (Level 0 and 1). By placing files representing the main, high-level business concepts directly at the top levels without technical clutter, anyone (and any AI model) can open the project and immediately understand *what* the system does. The sub-folders then naturally act as chapters, revealing finer details and sub-concepts only as the reader chooses to dive deeper.
