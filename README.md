graph TD
    %% Styling Definitions
    classDef confirmed fill:#d4edda,stroke:#28a745,stroke-width:2px,color:#000;
    classDef inferred fill:#d1ecf1,stroke:#17a2b8,stroke-width:2px,color:#000;
    classDef hypothesis fill:#fff3cd,stroke:#ffc107,stroke-width:2px,color:#000;
    classDef unexamined fill:#e2e3e5,stroke:#6c757d,stroke-width:2px,color:#000;

    %% 1. FRONTEND / PRESENTATION LAYER
    subgraph FrontendLayer [1. FRONTEND / PRESENTATION LAYER]
        F_WEB["GitHub Web App (Dashboard, Code, PRs, Actions) [GH-001]"]:::confirmed
        F_MOB["GitHub Mobile App [GH-001]"]:::confirmed
        F_IDE["Desktop & IDE Integrations [GH-001]"]:::confirmed
    end

    %% 2. CONTENT DELIVERY AND API GATEWAY LAYER
    subgraph APILayer [2. CDN & API GATEWAY LAYER]
        API_CDN("Content Delivery Network (CDN) [GH-001]"):::confirmed
        API_GW["API Gateway / Request Router [GH-002]"]:::inferred
        API_REST["REST API v3 [GH-002]"]:::confirmed
        API_GQL["GraphQL API v4 [GH-002]"]:::confirmed
        API_HOOK["Webhook Receiver [GH-009]"]:::confirmed
    end

    %% 4. AUTHENTICATION AND SECURITY LAYER
    subgraph AuthLayer [4. AUTHENTICATION & SECURITY LAYER]
        AUTH_USER["User Authentication & OAuth [GH-004]"]:::confirmed
        AUTH_RBAC["Role-Based Access Control / Perms [GH-004]"]:::inferred
        AUTH_SESS["Session & Token Management [GH-004]"]:::confirmed
    end

    %% 3. BACKEND / APPLICATION SERVICES LAYER
    subgraph BackendLayer [3. BACKEND / APPLICATION SERVICES]
        B_USER["User & Org Management [GH-004]"]:::confirmed
        B_REPO["Repository Management [GH-005]"]:::confirmed
        B_GIT["Git Version Control Service [GH-003]"]:::confirmed
        B_PR["Pull Request & Code Review [GH-006]"]:::confirmed
        B_ISSUE["Issues & Discussions [GH-001]"]:::confirmed
        B_SEARCH["Search & Indexing Service [GH-001]"]:::confirmed
    end

    %% 11. AI AND INTELLIGENT SERVICES
    subgraph AILayer [11. AI & INTELLIGENT SERVICES]
        AI_COPILOT["GitHub Copilot Service [GH-011]"]:::confirmed
        AI_MODELS("External LLM Infrastructure [GH-011]"):::hypothesis
    end

    %% 6. CACHING AND PERFORMANCE LAYER
    subgraph CacheLayer [6. CACHING & PERFORMANCE LAYER]
        C_APP[("Application-Level Cache [GH-001]")]:::inferred
        C_META[("Repository Metadata Cache [GH-005]")]:::hypothesis
    end

    %% 7. ASYNCHRONOUS PROCESSING AND QUEUE LAYER
    subgraph AsyncLayer [7. ASYNCHRONOUS PROCESSING & QUEUE LAYER]
        Q_EVENT["Event Bus / Message Queue [GH-008]"]:::hypothesis
        Q_WORKER["Background Job Workers [GH-008]"]:::inferred
        Q_NOTIFY["Notification Processing [GH-008]"]:::confirmed
    end

    %% 8. CI/CD AND GITHUB ACTIONS LAYER
    subgraph ActionsLayer [8. CI/CD & GITHUB ACTIONS LAYER]
        ACT_ORCH["Workflow Orchestrator [GH-007]"]:::inferred
        ACT_SCHED["Job Scheduling System [GH-007]"]:::hypothesis
        ACT_RUN_H["GitHub-Hosted Runners [GH-007]"]:::confirmed
        ACT_RUN_S["Self-Hosted Runners [GH-007]"]:::confirmed
        ACT_LOGS["Workflow Logs & Artifacts [GH-007]"]:::confirmed
    end

    %% 5. DATA AND STORAGE LAYER
    subgraph StorageLayer [5. DATA & STORAGE LAYER]
        DB_GIT[("Git Repo Storage (Blobs, Trees) [GH-005]")]:::confirmed
        DB_META[("Relational Metadata DB [GH-005]")]:::inferred
        DB_SEARCH[("Search Index Data [GH-001]")]:::inferred
        DB_BLOB[("File & Artifact Object Storage [GH-007]")]:::confirmed
    end

    %% 9. EXTERNAL SERVICES AND INTEGRATIONS
    subgraph ExternalLayer [9. EXTERNAL SERVICES & INTEGRATIONS]
        EXT_OAUTH("Third-Party OAuth Apps [GH-009]"):::confirmed
        EXT_HOOK("Webhook Consumers [GH-009]"):::confirmed
    end

    %% 10. MONITORING AND OBSERVABILITY LAYER
    subgraph MonitorLayer [10. MONITORING & OBSERVABILITY LAYER]
        MON_LOG["Application & Audit Logging [GH-010]"]:::confirmed
        MON_METRIC["Metrics & Health Monitoring [GH-010]"]:::inferred
    end

    %% --- Connections & Data Flow ---
    %% Frontend to API
    F_WEB --> API_CDN
    F_MOB --> API_GW
    F_IDE --> API_GW
    API_CDN --> API_GW

    %% API to Auth & Backend
    API_GW --> AUTH_USER
    API_GW --> API_REST
    API_GW --> API_GQL
    API_HOOK --> Q_EVENT
    
    API_REST --> AUTH_RBAC
    API_GQL --> AUTH_RBAC
    AUTH_USER --> AUTH_SESS
    
    %% API to Backend routing
    API_REST --> B_USER
    API_REST --> B_REPO
    API_REST --> B_PR
    API_GQL --> B_ISSUE
    API_REST --> B_SEARCH

    %% Backend to Cache
    B_REPO -.-> C_APP
    B_PR -.-> C_META

    %% Backend to Storage
    B_GIT --> DB_GIT
    B_REPO -.-> DB_META
    B_USER -.-> DB_META
    B_PR -.-> DB_META
    B_SEARCH -.-> DB_SEARCH
    B_ISSUE -.-> DB_META

    %% Backend to Async/Queues
    B_GIT -.-> Q_EVENT
    B_PR -.-> Q_EVENT
    B_ISSUE -.-> Q_EVENT
    Q_EVENT -.-> Q_WORKER
    Q_WORKER -.-> Q_NOTIFY
    Q_WORKER -.-> B_SEARCH

    %% Actions & CI/CD Flow
    Q_EVENT -.-> ACT_ORCH
    ACT_ORCH -.-> ACT_SCHED
    ACT_SCHED --> ACT_RUN_H
    ACT_SCHED --> ACT_RUN_S
    ACT_RUN_H --> ACT_LOGS
    ACT_RUN_S --> ACT_LOGS
    ACT_LOGS --> DB_BLOB

    %% AI Integration
    F_IDE --> AI_COPILOT
    F_WEB --> AI_COPILOT
    AI_COPILOT -.-> AI_MODELS
    AI_COPILOT -.-> B_GIT

    %% External
    API_REST --> EXT_OAUTH
    Q_WORKER --> EXT_HOOK

    %% Monitoring
    B_REPO -.-> MON_LOG
    B_GIT -.-> MON_LOG
    ACT_ORCH -.-> MON_METRIC
