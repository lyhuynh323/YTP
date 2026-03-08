---
description: End-to-End PM to UI Designer Workflow with Strict Audit Gates
---

This workflow defines the sequential collaboration between the Product Manager, Business Analyst, System Architect, and UI Designer. At each transition, a strict review and audit process MUST be performed before the next role can begin its work.

### Phase 1: Product Strategy and Vision (Product Manager)
1. **Agent:** `@product_manager`
2. **Task:** Analyze user inputs, market landscape, and business goals to prioritize features and define the product roadmap. 
3. **Output:** Product Strategy Document, OKRs, North Star Metric, and High-Level Feature List.
4. **Audit Gate 1 (Review & Audit):** 
   - The user or a designated reviewer MUST analyze the PM's output.
   - Verify alignment with business objectives.
   - **Do not proceed to Phase 2** until the PM's output is formally approved.

### Phase 2: Business Analysis and Requirements (Business Analyst)
1. **Agent:** `@business-analyst`
2. **Input:** Approved Product Strategy and Feature List from Phase 1.
3. **Task:** Elicit detailed requirements, identify process improvements, and deliver actionable insights. Create the Business Requirements Document (BRD) and Software Requirements Specification (SRS).
4. **Output:** BRD, SRS, Business Process/Workflow Diagrams.
5. **Audit Gate 2 (Review & Audit):** 
   - The user or a designated reviewer MUST audit the BA's documents.
   - Verify that all product features are correctly translated into detailed requirements without gaps.
   - **Do not proceed to Phase 3** until the BA's output is formally approved.

### Phase 3: System Architecture Design (System Architect)
1. **Agent:** `@system-architect`
2. **Input:** Approved BRD and SRS from Phase 2.
3. **Task:** Design the distributed system architecture, define microservices, database schemas, and communication patterns based on the requirements.
4. **Output:** Architecture Overview Diagram, System Design Document, API Specifications, Database Schema Design.
5. **Audit Gate 3 (Review & Audit):** 
   - The user or technical lead MUST review the System Architect's design.
   - Verify scalability, resilience, cloud-native principles, and that it supports all use cases defined by the BA.
   - **Do not proceed to Phase 4** until the architecture design is formally approved.

### Phase 4: User Interface Design (UI Designer)
1. **Agent:** `@ui-designer`
2. **Input:** Approved BRD, SRS, and System Design documentation.
3. **Task:** Create visual concepts, wireframes, and high-fidelity mockups. Define interaction patterns and prepare developer handoff specifications.
4. **Output:** Visual UI Mockups, Component Library/Design System updates, Developer Handoff Specs.
5. **Final Audit Gate (Review & Audit):**
   - The user or product owner MUST review the UI designs.
   - Verify accessibility, UX/UI consistency, and that all business requirements and system constraints are respected.
   - Once approved, the project is ready for the development phase.
