```mermaid
flowchart LR
    %% Nodes
    A[🖥️ Host: PRENEO]
    B[📊 Item: Error Detection\n(Timestamp)]
    C[⚠️ Trigger: Log-Based Alert]
    D[🚨 Trigger: Failure Alert]
    E[❗ Problem: Incident Created]
    F[🛠️ Service: Infrastructure]
    G[🗄️ Service: DLT]
    H[⚙️ Action: Incident Triggered]
    I[📡 Media: Notification System]
    J[👨‍💻 Recipients: Admin/User]

    %% Flow Connections
    A --> B
    B --> C
    B --> D
    C -.-> E
    D -.-> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J

    %% Styling (Dark Theme + Glow Effect)
    style A fill:#0f172a,color:#ffffff,stroke:#38bdf8,stroke-width:2px
    style B fill:#0f172a,color:#ffffff,stroke:#38bdf8,stroke-width:2px
    style C fill:#1e293b,color:#ffffff,stroke:#facc15,stroke-width:2px,stroke-dasharray: 5 5
    style D fill:#1e293b,color:#ffffff,stroke:#ef4444,stroke-width:2px,stroke-dasharray: 5 5
    style E fill:#450a0a,color:#ffffff,stroke:#ef4444,stroke-width:3px
    style F fill:#022c22,color:#ffffff,stroke:#22c55e,stroke-width:2px
    style G fill:#022c22,color:#ffffff,stroke:#22c55e,stroke-width:2px
    style H fill:#1e1b4b,color:#ffffff,stroke:#818cf8,stroke-width:2px
    style I fill:#0c4a6e,color:#ffffff,stroke:#38bdf8,stroke-width:2px
    style J fill:#111827,color:#ffffff,stroke:#a78bfa,stroke-width:2px
```
