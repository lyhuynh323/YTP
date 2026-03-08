# System Design (System Architecture)

As the System Architect, I have designed the target architecture to support the AI Digital Content Platform described in the BRD and SRS.

## 1. High-Level Architecture
The system will employ a **Microservices-oriented** event-driven architecture to handle high-bandwidth media processing and orchestrate multiple asynchronous AI agents.

### Components:
- **Client Interfaces**:
  - Web Portal (React/Next.js) for Admin, Internal Teams, and Editors.
  - Mobile App (React Native/Flutter) for Creators, Freelancers, and Shop Owners.
- **API Gateway & BFF (Backend for Frontend)**: Routes traffic, handles rate limiting, and terminates SSL.
- **Microservices** (Node.js/Go/Python):
  - `User & IAM Service`: Manages authentication, RBAC, OAuth tokens.
  - `Content Planning Service`: Manages topics, calendars.
  - `Media Processing Service`: Handles video ingest, chunking, transcoding.
  - `AI Orchestrator Service`: The core hub managing state machines for AI Agents.
  - `Billing & Payment Service`: Tracks RPM, calculates revenue splits, handles payouts.
- **AI Worker Nodes** (Python + GPU Instances):
  - PlannerAgent, ScriptAgent, EditorAgent (LLMs).
  - QC/LegalAgent (Computer Vision routines, OpenCV, YOLO, Face blurring).
- **Message Broker**: Apache Kafka or RabbitMQ for handling asynchronous video processing queues and AI job queuing.
- **Databases**:
  - `PostgreSQL`: Core relational data (Users, Transactions, Video Metadata).
  - `Vector DB (Pinecone/Milvus)`: RAG storage for Script templates and SOPs.
  - `Redis`: Caching and session management.
  - `S3 / Cloud Storage`: CDN-backed storage for raw footage and final renders.

## 2. Key Data Models (Conceptual ERD)
- **User**: ID, Role, Email, OAuth_Tokens, Team_ID.
- **Topic**: ID, Title, Status, Target_Date, Region.
- **Script**: ID, Topic_ID, Content_JSON, Version, Status.
- **MediaAsset**: ID, Script_ID, S3_URL, Type (Raw/Final), QC_Status.
- **VideoTask**: ID, Status (Planning -> Scripting -> Filming -> Editing -> SEO -> Review -> Uploaded).
- **Transaction**: ID, User_ID, Amount, Status (Pending/Paid).

## 3. Core System Workflows

### 3.1 Upload & AI QC Flow (Event-Driven)
1. Cameraman uploads video to `Media Service` via pre-signed S3 URL.
2. `Media Service` fires an event to Kafka: `media.uploaded`.
3. `AI Orchestrator` consumes event, triggers `QC/LegalAgent` worker.
4. `QC/LegalAgent` downloads chunks, runs inference (faces, logos, minors).
5. Output is saved to DB, event `qc.completed` fired.
6. Web Socket pushes notification to Reviewer dashboard.

### 3.2 AI Editing Flow
1. Editor clicks "Generate Timeline".
2. `AI Orchestrator` sends prompt + transcript to `EditorAgent` (LLM).
3. LLM returns Timeline JSON.
4. `Media Service` handles lightweight browser preview using the JSON without full server-side render.
5. Upon export, GPU Worker node performs the actual FFMPEG heavy render.

## 4. Security & Performance
- **Data Privacy**: Shop Owner camera feeds are encrypted. Face-blurring happens in-memory on the QC agent before saving to permanent storage.
- **Scalability**: AI Node workers and Media Transcoders are set up on Kubernetes auto-scaling groups based on queue depth.
