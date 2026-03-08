# System Use Cases

Based on the BRD and User Stories, following are the detailed Use Cases organized by Actor.

## 1. Admin
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-ADM-01 | Manage Users & Roles | **Desc**: Create, assign roles (Topic Selector, Script Creator, etc.), deactivate users. <br> **AC**: Only Admin assigns roles. Users are restricted by role. Audit logs record changes. Deactivated users cannot login. |
| UC-ADM-02 | Manage Teams | **Desc**: Group users into teams (e.g., US vs VN). <br> **AC**: Admin creates/edits teams. Users only see tasks assigned to their team. |
| UC-ADM-03 | Manage Channels | **Desc**: Link YouTube channels via OAuth and edit channel settings (timezone, SEO template). <br> **AC**: OAuth flow saves encrypted tokens. UploadAgent can use these tokens to publish. |
| UC-ADM-04 | Configure SOP & Templates | **Desc**: Upload SOP files and templates for production rules. <br> **AC**: System version-controls templates. Users see the latest active version. |
| UC-ADM-05 | Manage Legal Policies | **Desc**: Update compliance rules (minors, logos, explicit). <br> **AC**: LegalAgent fetches latest policy for QC. Flag videos violating current policy. |
| UC-ADM-06 | Billing/Revenue Rules | **Desc**: Configure revenue sharing percentages. <br> **AC**: Applied automatically in revenue calculations. Payout needs admin approval. |
| UC-ADM-07 | View System KPIs | **Desc**: View dashboards (RPM, revenue, video counts). <br> **AC**: Filter by date, channel, team, creator. Matches YouTube APIs. |

## 2. Topic Selector & PlannerAgent
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-TS-01 | Generate & Receive AI Topics | **Desc**: PlannerAgent generates daily topics; Selector receives them. <br> **AC**: Minimum 10 unique topics per day containing title, desc, category, and regional tags. |
| UC-TS-02 | Approve/Reject & Plan Topics | **Desc**: Selector approves/rejects topics and adds them to a weekly/monthly calendar. <br> **AC**: Approved move to Script queue. Rejected disappear. Calendar syncs to Editor & SEO teams. |

## 3. Script Creator & ScriptAgent
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-SC-01 | Generate AI Script | **Desc**: ScriptAgent (RAG) uses past data to generate scripts (Hook, Storyline, Shotlist, Dialogue, Duration). <br> **AC**: AI output matches RAG templates. |
| UC-SC-02 | Edit Script & Submit | **Desc**: Creator customizes script manually and sends to Cameraman. <br> **AC**: Text is editable. Saved as new version. When "Finalized", Cameraman is notified. |

## 4. Cameraman / Shooter
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-CM-01 | View Script & Shotlist | **Desc**: Read-only view of finalized script formatting. |
| UC-CM-02 | Upload & Tag Footage | **Desc**: Upload mp4/mov and tag scenes (good/retake/noisy). <br> **AC**: File size validation. Footage tagged with timestamp and script ID. Editors see tags instantly. |

## 5. Video Editor & EditorAgent
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-VE-01 | Auto-Generate Timeline | **Desc**: EditorAgent generates a rough timeline JSON. <br> **AC**: Includes intro, main scenes, transitions. Importable to CapCut/Premiere. |
| UC-VE-02 | Edit & Upload Final Video | **Desc**: Override AI timeline, finalize video, and upload. <br> **AC**: Output meets Min 1080p quality. |

## 6. SEO & Metadata Creator & SEOAgent
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-SEO-01 | Generate & Edit Metadata | **Desc**: SEOAgent provides 3 title variants, description, tags, thumbnail text. Creator edits manually. <br> **AC**: Regional keywords included. |
| UC-SEO-02 | Submit for Review | **Desc**: Pass finalized metadata to Reviewer. |

## 7. Reviewer / QA & QC/LegalAgent
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-REV-01 | Review QC Results | **Desc**: View AI QC flags (Faces, Logos, Missing shots, Copyright). <br> **AC**: QC accuracy >90%. Reviewer can mark flags as "fixed" or "ignored". |
| UC-REV-02 | Approve/Reject Content & Schedule | **Desc**: Approve video/metadata/publishing time to go live via UploadAgent, or Reject. <br> **AC**: Reject requires mandatory reason and notifies Editor/SEO. Approve changes state to "Ready for Upload". |

## 8. Freelancer
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-FL-01 | Accept Job & View Requirements | **Desc**: Accept job from pool. Status changes to In Progress. Reject returns it to pool. |
| UC-FL-02 | Upload Deliverables & Feedback | **Desc**: Upload work, system notifies Reviewer, view feedback in thread. |
| UC-FL-03 | Submit Invoice / Final Approval | **Desc**: Once deliverables marked complete, submit invoice. Admin must approve payout. |

## 9. Creator (Cộng Đồng)
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-CR-01 | Register & Link Channel | **Desc**: Register via OTP. Link YouTube via OAuth. |
| UC-CR-02 | AI Assisted Creation | **Desc**: Get AI Topic, Script, Auto-edit video (under 60s shorts). |
| UC-CR-03 | Upload & AI QC | **Desc**: Upload raw video. Receive QC report. <br> **AC**: Reject minors/brands/explicit. Auto-blur unauthorized faces. |
| UC-CR-04 | Publish & Track Revenue | **Desc**: Choose to publish on own or system channel. Track RPM/earnings. Withdraw (min $10 threshold). |

## 10. Shop Owner (Chủ Quán / Camera Partner)
| ID | Use Case | Description & Acceptance Criteria (AC) |
|---|---|---|
| UC-SO-01 | Register & Approve Locations | **Desc**: Verify business location. Approve camera zones. <br> **AC**: Only kitchen/garden allowed. Reject zones with customers. |
| UC-SO-02 | View Footage & Approve Edits | **Desc**: Owner views own private footage. Must Approve/Reject edited videos before they go public. |
| UC-SO-03 | Manage Content & Revenue | **Desc**: Request video removal (24h processing). Track revenue sharing. Withdraw earnings. |
