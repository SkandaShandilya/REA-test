flowchart TD
    %% Styling Definitions
    classDef confirmed fill:#d4edda,stroke:#28a745,stroke-width:2px,color:black;
    classDef inferred fill:#cce5ff,stroke:#007bff,stroke-width:2px,color:black;
    classDef hypothetical fill:#fff3cd,stroke:#ffc107,stroke-width:2px,color:black;
    classDef unexamined fill:#e2e3e5,stroke:#6c757d,stroke-width:2px,color:black;

    %% USER LAYER
    subgraph UserLayer [1. USER LAYER]
        Dev[Developers E-002, E-004]:::confirmed
        Browser[Browser E-006]:::confirmed
        CLI[Git CLI E-003]:::confirmed
        IDE[IDE Integration U-001]:::unexamined
    end

    %% FRONTEND LAYER
    subgraph FrontendLayer [2. FRONTEND LAYER]
        WebUI[GitHub Web Interface E-006]:::confirmed
        RepoDash[Repository Dashboard E-001]:::confirmed
        CommitUI[Commit History UI E-002]:::confirmed
        PRUI[Pull Request UI E-004]:::confirmed
        ActionsUI[GitHub Actions UI E-005]:::confirmed
        CDN[Content Delivery / CDN H-001]:::hypothetical
    end

    %% API LAYER
    subgraph APILayer [3. API LAYER]
        REST[GitHub REST API E-001]:::confirmed
        RepoAPI[Repository Endpoints E-001]:::confirmed
        CommitAPI[Commit Endpoints I-001]:::inferred
        PRAPI[Pull Request Endpoints I-002]:::inferred
        ActionsAPI[Actions Endpoints I-003]:::inferred
    end

    %% AUTHENTICATION & SECURITY
    subgraph AuthLayer [5. AUTHENTICATION & SECURITY]
        Auth[User Authentication U-002]:::unexamined
        RBAC[Authorization & Permissions U-003]:::unexamined
    end

    %% BACKEND SERVICES
    subgraph BackendLayer [4. BACKEND SERVICES]
        RepoSvc[Repository Management Svc I-004]:::inferred
        GitSvc[Git Version-Control Svc I-005]:::inferred
        BranchSvc[Branch & Commit Mgmt I-006]:::inferred
        PRSvc[Pull Request Mgmt I-007]:::inferred
        ActionSvc[Actions Workflow Mgmt I-008]:::inferred
    end

    %% BACKGROUND PROCESSING
    subgraph BackgroundLayer [7. BACKGROUND PROCESSING]
        EventProc[Event Processing / Triggers I-009]:::inferred
        MsgQueue[Message Queue H-002]:::hypothetical
        Orchestrator[Actions Orchestration I-010]:::inferred
        Runner[Ubuntu Runner E-005]:::confirmed
    end

    %% EXTERNAL INTEGRATIONS
    subgraph ExternalLayer [8. EXTERNAL INTEGRATIONS]
        Checkout[actions/checkout@v4 E-005]:::confirmed
    end

    %% DATA AND STORAGE
    subgraph StorageLayer [6. DATA AND STORAGE]
        GitStore[(Git Repository Storage I-011)]:::inferred
        MetaStore[(Repository Metadata I-012)]:::inferred
        PRStore[(Pull Request Metadata I-013)]:::inferred
        Cache[(Cache Layer H-003)]:::hypothetical
    end

    %% MONITORING AND LOGGING
    subgraph MonitorLayer [9. MONITORING AND LOGGING]
        Logs[Workflow Execution Logs E-005, E-006]:::confirmed
        Metrics[Internal Monitoring U-004]:::unexamined
    end

    %% INTERACTIONS (Solid = Observed, Dashed = Inferred/Hypothetical)
    Browser -->|E-006| WebUI
    CLI -->|E-003| GitSvc
    Dev -->|E-002| CLI
    WebUI -->|E-001| REST
    CDN -.-> WebUI
    
    REST --> RepoAPI
    REST -.-> CommitAPI
    REST -.-> PRAPI
    REST -.-> ActionsAPI

    RepoAPI --> RepoSvc
    CommitAPI -.-> BranchSvc
    PRAPI -.-> PRSvc
    ActionsAPI -.-> ActionSvc

    WebUI -.-> Auth

    RepoSvc -.-> MetaStore
    GitSvc -.-> GitStore
    BranchSvc -.-> GitStore
    PRSvc -.-> PRStore

    ActionSvc -.-> EventProc
    EventProc -.-> MsgQueue
    MsgQueue -.-> Orchestrator
    Orchestrator -.-> Runner
    Runner -->|E-005| Checkout
    Runner -->|E-005| Logs

    Cache -.-> MetaStore
    Cache -.-> PRStore
