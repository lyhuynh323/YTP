# Business Requirements Document (BRD)

## 1. Project Overview & Vision
The **AI Digital Content Platform** is an end-to-end, AI-driven ecosystem designed to automate and streamline the lifecycle of digital video content creation. The platform empowers internal teams, freelancers, independent creators (Creator Cộng Đồng), and local business owners (Chủ Quán) to seamlessly collaborate. By leveraging advanced AI Agents (Planner, Script, QC, Editor, SEO, Analytics), the system reduces manual labor, ensures legal compliance, optimizes metadata for SEO, and automatically manages revenue sharing and distribution.

## 2. Objectives
- **Automate Content Production**: Reduce the time from ideation to publishing using AI-generated topics, scripts, timelines, and auto-edits.
- **Ensure Compliance & Quality Control**: Automatically scan raw footage for legal risks (minors, brand logos, explicit content, missing shots) before production.
- **Empower Diverse User Roles**: Support a multi-tenant ecosystem including internal admins/editors, external freelancers, general creators, and shop owners contributing camera footage.
- **Monetization & Analytics Integration**: Track revenue accurately per content piece, integrate with YouTube analytics, and provide automated payouts and revenue sharing.

## 3. Scope of Constraints & Phases
The project is divided into four main phases based on user stories:
- **Phase 1: Core Internal Workflow & AI Foundation**
  - Admin controls, Team & SOP management, Channel linking (OAuth).
  - Internal AI assistance for Topic Selection, Script Creation, Filming (Cameraman), Video Editing, SEO, and Reviewing.
  - Core AI Agents (PlannerAgent, ScriptAgent).
- **Phase 2: Freelancer & Gig Economy Integration**
  - Freelancers accepting jobs, viewing requirements, uploading deliverables, receiving feedback, and generating invoices.
- **Phase 3: Community Creator Platform**
  - Independent Creators registering, linking their channels, receiving AI scripts/topics, uploading footage, getting AI QC, and auto-editing.
  - Revenue tracking and withdrawal for creators.
  - Additional AI Agents (QC/LegalAgent, EditorAgent).
- **Phase 4: Shop Owner (Camera) Integration & Analytics**
  - Shop Owners registering their security camera feeds (kitchen/garden) for content.
  - Owners curating footage (Approve/Reject video edits).
  - Advanced AnalyticsAgent and SEOAgent.

## 4. Key Stakeholders & Actors
- **Admin**: Configures the platform, manages users/roles/teams, handles billing rules, defines SOPs/Policies, and views KPIs.
- **Topic Selector**: Uses AI generated ideas to establish daily/weekly/monthly content plans.
- **Script Creator**: Utilizes RAG-based AI to draft scripts, edits them manually, and submits them to shooters.
- **Cameraman/Shooter**: Views shotlists, uploads raw footage, and tags scenes.
- **Video Editor**: Uses AI-generated JSON timelines to finalize edits or allows AI to auto-edit shorts.
- **SEO & Metadata Creator**: Uses AI suggestions to craft and localize titles, descriptions, and tags.
- **Reviewer / QA**: Approves content and schedule based on AI QC (legal/copyright flags).
- **Freelancer**: Accepts external tasks, submits deliverables and invoices.
- **Creator (Cộng Đồng)**: Solopreneurs driving content generation via their channels.
- **Shop Owner / Camera Partner**: Providers of raw camera footage.
- **AI Agents**: PlannerAgent, ScriptAgent, QC/LegalAgent, EditorAgent, SEOAgent, AnalyticsAgent.

## 5. High-Level Requirements
- **Integration**: Secure OAuth integration with YouTube for channels and analytics.
- **AI Processing Engine**: Multi-modal AI pipelines for text generation (scripts, SEO), video analysis (QC scanning faces/logos/minors), and computational editing (timeline JSON generation, short video rendering).
- **Financial Module**: Bank/Momo/PayPal integrations for payouts with a minimum threshold validation and admin approvals.

## 6. Assumptions & Dependencies
- AI services (OpenAI/Anthropic APIs, Computer Vision APIs) must provide required accuracy (>90% for QC).
- YouTube API quotas and limitations permit high-volume OAuth token management and video uploading.
- Users have adequate internet bandwidth to upload raw 1080p/4K footage.
- System complies with GDPR and local data privacy laws (especially regarding shop owner camera footage and faces).
