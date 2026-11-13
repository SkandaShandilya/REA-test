graph TD
    %% Frontend
    subgraph Frontend & User Interface [E-006]
        Browser(Web Browser)
        CDN(GitHub CDN)
    end
    
    Browser --> CDN
    CDN --> API_GW
    
    %% Backend & APIs
    subgraph API Gateway & Backend Services
        API_GW[API Gateway / api.github.com]
        RepoService(Repository Service) -.->|E-001| API_GW
        PRService(Pull Request Service) -.->|E-004| API_GW
    end
    
    Browser --> API_GW
    API_GW --> RepoService
    API_GW --> PRService
    
    %% Data Storage & Git VCS
    subgraph Data Storage & Git Version Control
        MetaDB[(Metadata DB)]
        GitBackend[(Git Backend Storage)]
        
        RepoService --> MetaDB
        PRService --> MetaDB
        
        API_GW --> GitBackend
        GitBackend -.->|E-002, E-003| CommitData[Commits & Blob Storage]
    end
    
    %% CI/CD & Automation
    subgraph CI/CD Queues & Runners
        Queue(Message Queue / Webhooks)
        Runner1[Ubuntu-Latest Runner]
        Runner2[Worker Nodes]
        
        API_GW --> Queue
        Queue --> Runner1
        Queue --> Runner2
        Runner1 -.->|E-005| Logs[(Action Logs)]
    end
    
    Logs -.->|WebSocket| Browser
    
    classDef frontend fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef backend fill:#cce5ff,stroke:#007bff,stroke-width:2px;
    classDef data fill:#f8d7da,stroke:#dc3545,stroke-width:2px;
    classDef cicd fill:#fff3cd,stroke:#ffc107,stroke-width:2px;
    
    class Browser,CDN frontend;
    class API_GW,RepoService,PRService backend;
    class MetaDB,GitBackend,CommitData data;
    class Queue,Runner1,Runner2,Logs cicd;
