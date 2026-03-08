# Detailed System Architecture Diagrams

Dựa trên tài liệu `System_Design.md`, dưới đây là các biểu đồ thiết kế hệ thống chi tiết (Logical, Physical) và Luồng xử lý công việc (Workflow) sử dụng Mermaid.

## 1. Logical Architecture (Kiến trúc Logic)
Biểu đồ này thể hiện sự phân tách các module phần mềm, dịch vụ và cách chúng giao tiếp với nhau ở mức logic.

```mermaid
graph TD
    %% Khối Client Apps
    subgraph Clients["Client Applications"]
        WebApp["Web Portal (Admin/Editor/Planner)"]
        MobileApp["Mobile App (Creator/Freelancer/Shop)"]
    end

    %% Khối Gateway
    API_GW["API Gateway / BFF\n(Rate Limiting, Auth, Load Balancing)"]

    %% Khối Microservices
    subgraph Microservices["Core Microservices"]
        IAM["IAM & User Service\n(Role, OAuth)"]
        ContentPlanner["Content Planning Service\n(Topics, Schedule)"]
        MediaService["Media Processing Service\n(Upload, Timeline)"]
        Billing["Billing & Payment Service\n(Revenue, Payout)"]
        Analytics["Analytics Service\n(KPIs, YouTube Stats)"]
    end

    %% Khối AI Orchestration & Agents
    subgraph AI_Layer["AI Orchestration & Agents"]
        AI_Orchestrator["AI Orchestrator\n(State Machine)"]
        PlannerAgent["Planner Agent (LLM)"]
        ScriptAgent["Script Agent (RAG)"]
        EditorAgent["Editor Agent (JSON Timeline)"]
        SEOAgent["SEO Agent (Metadata)"]
        QCAgent["QC/Legal Agent (Computer Vision)"]
    end

    %% Khối Database & Storage
    subgraph DataStorage["Data & Storage Layer"]
        PG[(PostgreSQL\nCore Data)]
        Redis[(Redis\nCache/Session)]
        VectorDB[(Vector DB\nRAG Templates)]
        S3[(S3/Cloud Storage\nMedia Assets)]
    end

    %% Khối External Integration
    subgraph External["External Integrations"]
        YouTube["YouTube API\n(OAuth, Publish, Analytics)"]
        PaymentGateway["Payment Gateways\n(Momo, Bank, PayPal)"]
    end

    %% Message Broker
    Kafka{{"Message Broker (Kafka / RabbitMQ)\n(Async Events)"}}

    %% Relationships
    Clients -->|HTTPS/REST/GraphQL| API_GW
    API_GW --> IAM
    API_GW --> ContentPlanner
    API_GW --> MediaService
    API_GW --> Billing
    API_GW --> Analytics

    %% Service to DB
    IAM --> PG
    IAM --> Redis
    ContentPlanner --> PG
    Billing --> PG
    Analytics --> PG
    MediaService --> S3

    %% Service to External
    IAM --> YouTube
    Analytics --> YouTube
    MediaService --> YouTube
    Billing --> PaymentGateway

    %% Async & AI Trigger
    MediaService -->|Publish Events| Kafka
    ContentPlanner -->|Publish Events| Kafka
    Kafka -->|Consume Events| AI_Orchestrator
    
    AI_Orchestrator --> PlannerAgent
    AI_Orchestrator --> ScriptAgent
    AI_Orchestrator --> EditorAgent
    AI_Orchestrator --> SEOAgent
    AI_Orchestrator --> QCAgent
    
    ScriptAgent --> VectorDB
    QCAgent --> S3
```

---

## 2. Physical / Deployment Architecture (Kiến trúc Vật lý)
Biểu đồ này thể hiện cách hệ thống được triển khai trên hạ tầng Cloud (ví dụ AWS/GCP), tập trung vào Containerization và Scaling.

```mermaid
graph TB
    subgraph Internet
        User((End User))
    end

    subgraph CDN_Layer["CDN & Edge (AWS CloudFront / Cloudflare)"]
        Edge["CDN (Static Assets & Video Streaming)"]
        WAF["Web Application Firewall (WAF)"]
    end

    User -->|HTTPS| Edge
    Edge --> WAF

    subgraph VPC["Virtual Private Cloud (VPC)"]
        
        subgraph Public_Subnet["Public Subnet"]
            ALB["Application Load Balancer"]
        end
        WAF --> ALB

        subgraph Private_Subnet_EKS["Private Subnet (Compute - EKS/K8s)"]
            subgraph NodeGroup_General["General Node Group (CPU - API)"]
                Pod_Gateway["API Gateway Pods"]
                Pod_IAM["IAM Pods"]
                Pod_Media["Media Pods"]
                Pod_Billing["Billing Pods"]
            end
            
            subgraph NodeGroup_GPU["GPU Node Group (AI Workers)"]
                Pod_Orchestrator["Orchestrator Pods"]
                Pod_QCAgent["QC/Vision Pods (GPU)"]
                Pod_LLM["LLM Interface Pods"]
                Pod_FFMPEG["Transcoding Workers"]
            end
            
            Pod_Media -.-> Pod_FFMPEG
        end
        
        ALB --> Pod_Gateway
        Pod_Gateway --> Pod_IAM
        Pod_Gateway --> Pod_Media
        Pod_Gateway --> Pod_Billing

        subgraph Managed_Services["Private Subnet (Data & Message Broker)"]
            RDS[(RDS PostgreSQL\nMulti-AZ)]
            Elasticache[(ElastiCache Redis)]
            MSK{{"Managed Kafka (MSK)"}}
            Pinecone[(Managed Vector DB)]
        end

        %% Connections within VPC
        Pod_IAM --> RDS
        Pod_Media --> RDS
        Pod_Media --> MSK
        Pod_Orchestrator --> MSK
        Pod_LLM --> Pinecone
        Pod_Gateway --> Elasticache
    end

    subgraph Object_Storage["Cloud Storage"]
        S3_Bucket[(S3 Bucket\nRaw & Final Media)]
    end

    Pod_Media --> S3_Bucket
    Pod_QCAgent --> S3_Bucket
    Pod_FFMPEG --> S3_Bucket
    S3_Bucket -.-> Edge
```

---

## 3. Detailed Component Worflow (Luồng Xử lý Dữ liệu Chi tiết)

Dưới đây là Sequence Diagram mô tả một luồng xử lý phức tạp nhất: **Creator Upload Video -> AI QC -> Auto Edit -> Publish**.

```mermaid
sequenceDiagram
    autonumber
    actor Creator
    participant Web/App as Client (Web/Mobile)
    participant MediaSvc as Media Service
    participant S3 as S3 Storage
    participant Kafka as Message Broker
    participant AI_Orch as AI Orchestrator
    participant QCAgent as QC/Legal Agent (GPU)
    participant EditorAgent as Editor Agent
    participant DB as PostgreSQL
    participant YT as YouTube API

    Creator->>Web/App: 1. Selects Video & Clicks Upload
    Web/App->>MediaSvc: 2. Request Pre-signed URL
    MediaSvc->>S3: 3. Generate Pre-signed URL
    S3-->>MediaSvc: URL returns
    MediaSvc-->>Web/App: 4. Returns URL to Client
    
    Web/App->>S3: 5. Direct Upload Video Content (TUS/Chunked)
    S3-->>Web/App: 6. Upload Complete Success
    Web/App->>MediaSvc: 7. Confirm Upload Success (Asset ID)
    
    MediaSvc->>DB: 8. Create Asset Record (Status: PROCESSING_QC)
    MediaSvc->>Kafka: 9. Event: `media.uploaded` {asset_id}
    
    Kafka-->>AI_Orch: 10. Consume Event
    AI_Orch->>QCAgent: 11. Trigger QC Scan Job
    
    activate QCAgent
    QCAgent->>S3: 12. Download Video Chunks
    QCAgent->>QCAgent: 13. Run CV Models (Faces, Minors, Logos)
    QCAgent->>AI_Orch: 14. Return QC Report (Flags + Timecodes)
    deactivate QCAgent
    
    AI_Orch->>DB: 15. Update Asset QC Status
    
    alt QC FAILED (e.g. Minors Detected)
        AI_Orch->>Kafka: Event: `qc.failed`
        Kafka-->>MediaSvc: Consume
        MediaSvc-->>Web/App: 16a. Notify Creator (Action Required)
    else QC PASSED / Auto-Blurred
        AI_Orch->>Kafka: Event: `qc.passed`
        Kafka-->>AI_Orch: 16b. Trigger `EditorAgent` Next Step
        
        activate EditorAgent
        EditorAgent->>EditorAgent: 17. Analyze Script & Footage
        EditorAgent->>AI_Orch: 18. Generate Timeline JSON
        deactivate EditorAgent
        
        AI_Orch->>DB: 19. Save Timeline JSON (Status: READY_FOR_REVIEW)
        
        AI_Orch->>Kafka: Event: `timeline.generated`
        Kafka-->>MediaSvc: Consume
        MediaSvc-->>Web/App: 20. Notify Creator/Reviewer (Video Ready for Final Check)
        
        Creator->>Web/App: 21. Review Timeline & Click "Approve & Publish"
        Web/App->>MediaSvc: 22. Publish Command
        MediaSvc->>YT: 23. OAuth API Call (Upload + Metadata)
        YT-->>MediaSvc: 24. Success (YouTube Video ID)
        MediaSvc->>DB: 25. Update Status: PUBLISHED
    end
```
