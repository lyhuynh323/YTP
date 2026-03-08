# Software Requirements Specification (SRS)

## 1. Introduction
### 1.1 Purpose
This document specifies the software requirements for the AI Digital Content Platform. It serves as a comprehensive guide for developers, QA testers, and project stakeholders to understand the system's expected functional and non-functional behaviors.

### 1.2 Scope
The system handles the entire video content lifecycle, integrating human workflows (Internal Team, Freelancers, Creators, Shop Owners) with AI Agents (Planner, Script, QC, Editor, SEO, Analytics) to automate and optimize digital content production for platforms like YouTube.

## 2. Overall Description
### 2.1 User Characteristics
The system supports multiple distinct actor roles, each with specific permissions:
- **Admin**: Full system configuration, policy, and revenue management.
- **Content Team (Internal)**: Includes Topic Selectors, Script Creators, Cameramen, Video Editors, SEO Creators, and Reviewers.
- **Freelancers**: External contractors executing specific tasks (filming, editing) for payment.
- **Creators (Cộng Đồng)**: Independent creators submitting videos/channels for AI processing and revenue sharing.
- **Shop Owners (Chủ Quán)**: External partners providing continuous camera streams for content mining.

### 2.2 System Architecture Overview
A microservices-based or modular monolith architecture is recommended, composed of:
1. **Core Web App**: Manages users, roles, teams, workflows, and dashboards.
2. **AI Orchestration Layer**: Interfaces with external LLMs and Computer Vision APIs for PlannerAgent, ScriptAgent, QC/LegalAgent, EditorAgent, SEOAgent.
3. **Media Processing Engine**: Handles large video uploads, transcoding, and timelines.
4. **Integration Layer**: OAuth for YouTube API (publishing, analytics) and Payment Gateways (bank transfers, Momo, PayPal).

## 3. Functional Requirements

### 3.1 Authentication & Authorization (FR-Auth)
- **FR-Auth.1**: System shall allow Email/Phone/OTP registration for Creators and Shop Owners.
- **FR-Auth.2**: System shall support Role-Based Access Control (RBAC) managed by Admins. Users cannot access modules outside their assigned roles.
- **FR-Auth.3**: System shall support OAuth 2.0 integration for linking YouTube channels securely (storing encrypted access/refresh tokens).

### 3.2 System Configuration (Admin) (FR-Admin)
- **FR-Admin.1**: Admin shall be able to create, edit, delete, and group users into Teams.
- **FR-Admin.2**: Admin shall manage SOPs (Standard Operating Procedures) and version-controlled templates.
- **FR-Admin.3**: Admin shall configure global compliance policies (e.g., flagging minors, brand logos) impacting AI QC.
- **FR-Admin.4**: Admin shall configure revenue sharing percentages and approve payout processing.

### 3.3 AI-Driven Content Ideation (FR-Plan)
- **FR-Plan.1**: PlannerAgent shall generate daily topic ideas based on user schedules and tags (e.g., US vs. VN audience).
- **FR-Plan.2**: Topic Selectors shall approve/reject topics and schedule approved ones on a calendar.

### 3.4 AI Script Generation & Management (FR-Script)
- **FR-Script.1**: ScriptAgent (acting as RAG) shall retrieve past successful templates to generate scripts (Hook, Storyline, Shotlist, Dialogue, Duration).
- **FR-Script.2**: Script Creators shall have a rich-text editor to modify AI-generated scripts, maintaining version history.
- **FR-Script.3**: Finalized scripts must be submittable to Cameramen in a read-only format.

### 3.5 Media Upload & AI QC (FR-Media)
- **FR-Media.1**: Cameramen, Creators, and Freelancers shall be able to upload raw footage (mp4/mov) with defined max size limits.
- **FR-Media.2**: Cameramen shall be able to tag specific scenes (good, retake, noisy) which are immediately visible to Editors.
- **FR-Media.3**: QC/LegalAgent shall automatically scan uploaded footage with >90% accuracy for faces, logos, minors, explicit content, and copyright risk.
- **FR-Media.4**: Automatic blurring of unauthorized faces shall be applied for Creator-uploaded content.

### 3.6 Editing & Timeline Generation (FR-Edit)
- **FR-Edit.1**: EditorAgent shall generate rough timeline JSON (intro, transitions, captions) based on script and tagged footage.
- **FR-Edit.2**: System shall allow Video Editors to import timeline JSON into Premiere/CapCut or override the AI timeline.
- **FR-Edit.3**: For Creator shorts, the system shall auto-render videos under 60 seconds matching a selected visual template.

### 3.7 SEO & Publishing (FR-SEO)
- **FR-SEO.1**: SEOAgent shall output 3 title variants, description, tags, and thumbnail text based on the final video and region.
- **FR-SEO.2**: Reviewers shall receive a consolidated QC flag report + finalized metadata to Approve or Reject (with mandatory reason).
- **FR-SEO.3**: Approved videos shall be automatically published to the linked YouTube channel using stored OAuth tokens at the scheduled time.

### 3.8 External Portals (Freelancer, Creator, Shop Owner)
- **FR-Ext.1**: Freelancers shall view a pool of jobs, accept tasks, upload deliverables, view feedback threads, and submit invoices.
- **FR-Ext.2**: Creators shall view a dashboard with daily RPM, views, and revenue earnings synchronized with YouTube Analytics APIs.
- **FR-Ext.3**: Shop Owners must verify their business location for camera setup, and system must restrict camera view to non-customer zones (kitchen/garden).
- **FR-Ext.4**: Shop Owners shall receive footage edits for Approval/Rejection and can request video removal within 24 hours.

## 4. Non-Functional Requirements
### 4.1 Performance
- **NFR-1**: AI text generation (Topic, Script, SEO) must respond within 5-10 seconds.
- **NFR-2**: AI Video QC scanning must process a 10-minute 1080p video within 5 minutes.
- **NFR-3**: Dashboards must load within 2 seconds.

### 4.2 Security & Compliance
- **NFR-4**: Passwords and OAuth tokens must be encrypted at rest (AES-256).
- **NFR-5**: Full audit logging required for Admin actions (User creation/deletion, policy changes).
- **NFR-6**: System must strictly isolate tenant data (Creators see only their revenue, Shop Owners only their footage).

### 4.3 Availability
- **NFR-7**: The system must have 99.9% uptime, utilizing cloud-scalable storage for large video files (e.g., AWS S3 or Google Cloud Storage).
