


graph LR
    subgraph N0["n[0] = com.company.app"]
        direction LR
        
        subgraph L0["Ebene 0"]
            A1["User<br/><i>interface</i>"]
            A2["Document<br/><i>interface</i>"]
        end
        
        subgraph P1["user package"]
            U1["Authenticated"]
            U2["Cached"]
            U3["Persistent"]
        end
        
        subgraph P2["document package"]
            D1["Validated"]
            D2["Encrypted"]
        end
        
        subgraph L1["Ebene 1"]
            A3["Credentials<br/><i>in user</i>"]
        end
        
        subgraph P3["credentials package"]
            C1["Hashed"]
            C2["Encrypted"]
            C3["Temporary"]
        end
        
        subgraph L2["Ebene 2"]
            A4["Hash<br/><i>in credentials</i>"]
        end
        
        subgraph P4["hash package"]
            H1["Bcrypt"]
            H2["Sha256"]
        end
        
        A1 --> U1 & U2 & U3
        A2 --> D1 & D2
        A3 --> C1 & C2 & C3
        A4 --> H1 & H2
    end
    
    style A1 fill:#e8f4fd,stroke:#3b82f6,stroke-width:2px
    style A2 fill:#e8f4fd,stroke:#3b82f6,stroke-width:2px
    style A3 fill:#e8f4fd,stroke:#3b82f6,stroke-width:2px
    style A4 fill:#e8f4fd,stroke:#3b82f6,stroke-width:2px
    
    style U1 fill:#f0fdf4,stroke:#22c55e
    style U2 fill:#f0fdf4,stroke:#22c55e
    style U3 fill:#f0fdf4,stroke:#22c55e
    style D1 fill:#f0fdf4,stroke:#22c55e
    style D2 fill:#f0fdf4,stroke:#22c55e
    style C1 fill:#f0fdf4,stroke:#22c55e
    style C2 fill:#f0fdf4,stroke:#22c55e
    style C3 fill:#f0fdf4,stroke:#22c55e
    style H1 fill:#f0fdf4,stroke:#22c55e
    style H2 fill:#f0fdf4,stroke:#22c55e
