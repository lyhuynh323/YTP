# Gap Analysis & Best Practices (with UX Recommendations)

After reviewing the initial User Stories and designing the system, the following gaps in requirements are identified, along with industry Best Practices and UX recommendations tailored for each Actor's workflow.

## 1. Gap Analysis (Missing Requirements in User Stories)
- **Notifications & Alerts System**: User stories mention "System notifies" but lack detailed mechanics (Push, Email, In-app? Read/unread states?). We need a dedicated Notification Service.
- **Revision / Loop-back Mechanics**: What happens if the Cameraman shoots footage that the Video Editor finds completely unusable, overriding the QC? The loop-back flow from Editor back to Cameraman is missing.
- **Dispute Resolution**: If a Freelancer or Shop Owner disagrees with the revenue calculated or video rejected, there is no "Appeal" user story.
- **Edge Cases in AI Failure**: If AI Editor fails to generate a valid JSON timeline, or QC Agent errors out due to bad file encoding, the fallback manual overrides must be explicitly defined.

## 2. Best Practices & UX Recommendations by Case

### 2.1 Topic Selector & Planner (AI Planning)
- **Best Practice & UX**: Provide a **"Confidence Score"** from the AI next to each topic. Use a **Tinder-like swipe interface** on mobile or **Drag-and-Drop Kanban** on Web to quickly sort 20 daily AI topics into "Approve", "Save for Later", and "Trash".

### 2.2 Script Creator (AI Scripting)
- **Best Practice & UX**: Implementing a **Side-by-Side View**. Left side: AI Chatbot / Prompt tweaker. Right side: The actual script document. Implement **Auto-save** every 5 seconds and collaborative cursors if multiple people edit.

### 2.3 Cameraman / Shooter (Upload & Media)
- **Best Practice & UX**: Large video uploads fail often. Use **Resumable Uploads (TUS Protocol)**. The UI must show a granular progress bar with pause/resume capability, and time-remaining estimates. Add offline-first support for tagging scenes while the upload happens in the background.

### 2.4 Video Editor (AI Timeline & Export)
- **Best Practice & UX**: For the timeline JSON import, provide a **1-click install plugin** for Adobe Premiere or a direct integration with CapCut Web. The web-editor fallback should heavily utilize **Keyboard Shortcuts** (J, K, L for scrubbing) standard in NLEs (Non-Linear Editors) to ensure professionals don't feel bogged down by a web interface.

### 2.5 SEO Creator (AI Metadata)
- **Best Practice & UX**: Provide a **Real-time Keyword Density / SEO Score** gauge alongside the text boxes. As the user alters the AI's suggested title, the score gauge updates color (Red -> Yellow -> Green) to ensure they don't break the SEO optimization.

### 2.6 Reviewer / QA (AI QC)
- **Best Practice & UX**: Do not just list "Faces detected". The video player MUST have **clickable timeline markers**. Clicking a red marker jumps the video exactly to the frame where the logo/face was flagged by the AI, saving the reviewer from watching the whole video.

### 2.7 Creator (Cộng Đồng)
- **Best Practice & UX**: Gamify the dashboard. Show actionable insights like "Upload 1 more video this week to maintain your streak!". The revenue chart should default to a simple cumulative line graph for motivation, rather than complex CPA/RPM metrics unless expanded.

### 2.8 Shop Owner (Camera Partner)
- **Best Practice & UX**: Privacy is paramount. Provide a **Privacy Masking Tool** in their app where they can draw black boxes over non-kitchen areas to reassure them that customers are not being recorded. Ensure "1-click video removal" is highly visible to build trust.
