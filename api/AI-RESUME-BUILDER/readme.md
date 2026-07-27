# AI Resume Builder — PRD

**PRODUCT REQUIREMENTS DOCUMENT**

A modern, ATS-friendly resume and cover letter platform with a design-grade editor and AI-assisted writing.

**Technology Stack:** Angular • Express.js (Node) • MySQL
**Version:** 1.0
**Category:** Career Tools / SaaS • Competitive alternative to EnhanceCV

---

## Table of Contents

1. Overview & Summary
2. Users & Personas
3. Core User Journey
4. Functional Requirements
5. Non-Functional Requirements
6. Technical Architecture
7. Data Model (Core Entities)
8. Representative API Endpoints
9. Delivery Milestones
10. Risks & Assumptions

---

## 1. Overview & Summary

This document defines an AI-powered resume builder: a web application where job seekers create, tailor, and export professional resumes and cover letters. The product competes directly with EnhanceCV and similar tools (Zety, Resume.io, Kickresume) by combining a design-grade drag-and-drop editor, AI-assisted writing, real-time ATS feedback, and, importantly, exports that those tools restrict, including clean Word (.docx) output and watermark-free downloads on a fair free tier.

The system is built on Angular for the editor and marketing site, an Express.js REST API on Node for accounts, documents, AI orchestration, and exports, and MySQL as the system of record. AI features call an external large language model provider through a thin server-side layer so prompts, cost, and safety stay under our control.

### 1.1 Problem Statement

Job seekers face two problems at once. First, writing is hard: turning a plain job history into sharp, achievement-oriented bullet points that read well and pass automated screening takes skill most people do not have. Second, existing tools that solve the writing problem lock the results behind friction: watermarks on free downloads, PDF-only export with no editable Word file, subscriptions that feel steep for a one-time need, and editors that lag on longer documents. The opportunity is a builder that writes as well as the incumbents but is more generous and more flexible about getting your document out.

### 1.2 Goals & Objectives

- **Make good writing easy:** AI that turns rough input into quantified, recruiter-ready bullet points, summaries, and skills.
- **Pass the robots and impress the human:** templates that parse cleanly in ATS while still looking modern and designed.
- **Remove export friction:** PDF, and Word (.docx), plus a shareable public link; no watermark on the core free export.
- **Tailor in one click:** paste a job description and re-point the resume's keywords, summary, and bullets to match it.
- **Keep the editor fast:** real-time editing that stays smooth even on a long, multi-section resume.

### 1.3 Differentiators vs. EnhanceCV

The competitive wedges below come from documented gaps and common complaints about EnhanceCV. They are the reasons a user would switch.

| Area | EnhanceCV today | Our position |
|------|-------------------|----------------|
| Word export | PDF and TXT only; no .docx | Native, clean .docx export as a first-class feature |
| Free downloads | Branded / watermarked; 7-day trial | Watermark-free core export on a permanent free tier |
| Pricing | Subscription felt steep for one-time use | Fair free tier plus a low-cost short pass option |
| Editor performance | Reported lag on long resumes | Virtualized, performance-budgeted editor |
| ATS feedback | Full insights gated to paid | Core ATS check available free; deep insights paid |

### 1.4 Non-Goals (v1.0)

- A full job board or application-submission engine. We track applications; we do not host listings.
- A native mobile app. The Angular app is responsive; no separate app ships in v1.0.
- Recruiter-side or team/enterprise seats. v1.0 targets the individual job seeker.
- Building our own language model. We orchestrate an external provider.

---

## 2. Users & Personas

v1.0 is single-sided: the user is the job seeker. Accounts are simple, with a free tier and a paid tier rather than complex roles.

| Persona | Who they are | What they need |
|---------|----------------|------------------|
| The first-time grad | Building a first real resume, no template instinct | Guidance, prewritten examples, AI to phrase experience, a free export |
| The active switcher | Employed, applying to many roles | Fast tailoring per job, multiple resume versions, an application tracker |
| The returner | Re-entering work after a gap | Reassurance, clean structure, help framing the gap positively |
| The design-conscious pro | Wants it to look sharp, not templated | Fine layout control, modern templates, custom sections |

### 2.1 Account Tiers

- **Free:** unlimited editing, core templates, one active resume export to PDF and DOCX without watermark, basic ATS check.
- **Pro:** all templates, unlimited resumes and versions, one-click tailoring, full ATS insights, version history, cover letters, priority AI.
- **Day pass:** a short, low-cost pass unlocking Pro for a fixed window, aimed at the one-time user the incumbents lose.

---

## 3. Core User Journey

1. **Start** — the user signs up, then either uploads an existing resume, imports from LinkedIn/a profile, or starts from a template.
2. **Parse** — an uploaded resume is parsed into structured sections the user can edit, rather than a blank form.
3. **Edit** — the drag-and-drop editor lets the user add, reorder, and fill sections with live preview.
4. **Enhance** — AI suggests stronger bullet points, summaries, skills, and a headline from the user's raw input.
5. **Check** — the ATS checker scores the resume, flags formatting and keyword issues, and suggests fixes.
6. **Tailor** — the user pastes a job description; the tool aligns keywords, summary, and bullets to it (Pro).
7. **Export & share** — the user downloads PDF or DOCX, or generates a public share link.
8. **Track** — the user logs where they applied and each application's status in the tracker.

---

## 4. Functional Requirements

### 4.1 Resume Editor

- Drag-and-drop sections: reorder and toggle sections (experience, education, skills, projects, custom) with live preview.
- Real-time editing: changes render instantly; the canvas stays responsive on long documents via virtualized rendering.
- Custom sections: user-defined sections (for example strengths, interests, a personal statement) alongside standard ones.
- Layout control: fonts, colors, spacing, and single/double-column layouts within each template's guardrails.
- Auto-save: every change persists automatically; the user never loses work on a crash or refresh.

### 4.2 Templates

- A library of modern templates across categories (simple, modern, creative, traditional), each ATS-tested.
- Templates keep all text in a real text layer (no text baked into images) so ATS parsers read them correctly.
- Switching templates preserves content; only presentation changes.

### 4.3 AI Writing Assistant

- Bullet generation: turn a plain task ("managed a team") into quantified, achievement-oriented lines.
- **Summary, skills, and headline generation** from the user's role and history, or from scratch.
- Rewrite / improve: tighten existing text, cut clichés and repetition, raise the impact.
- Prompt-based edits: the user can type an instruction ("make my experience sound more senior") and apply it to a section.
- All AI runs server-side through our LLM layer so prompts, moderation, and cost controls stay centralized.

### 4.4 ATS Checker

- Scores the resume and flags parsing risks: complex tables, images as text, missing standard sections, unusual headings.
- Checks keyword coverage against a target job description when one is provided.
- Free tier gets a score and top issues; Pro gets the full itemized report and fixes.

### 4.5 Job Tailoring (Pro)

- The user pastes a job description; the system extracts key skills and keywords.
- It suggests adjustments to the summary, skills, and bullets to align with the role, which the user accepts or rejects.
- Each tailored result can be saved as a separate version so the base resume is preserved.

### 4.6 Cover Letters (Pro)

- Generate a cover letter from the resume plus a pasted job description, using a matching template.
- Editable with the same AI text tools; exportable in the same formats.

### 4.7 Export & Sharing

- **PDF export** in A4 and US Letter, with selectable text and clean structure for ATS.
- **Word (.docx) export** that is genuinely editable, our headline differentiator versus EnhanceCV.
- Public share link: a hosted, view-only version of the resume for LinkedIn or feedback.
- **No watermark** on the core free export.

### 4.8 Documents & Versions

- Cloud storage of multiple resumes and cover letters per account.
- Duplicate any document to spin off a tailored version.
- Version history (Pro) so a user can restore an earlier draft.

### 4.9 Application Tracker

- Log applications with company, role, date, and status (applied, interview, offer, rejected).
- Link each application to the specific resume version used.

---

## 5. Non-Functional Requirements

| Category | Requirement |
|----------|--------------|
| Editor performance | Live preview updates under ~100 ms; no perceptible lag on a resume with 10+ sections and multiple pages. |
| API performance | Standard reads under 400 ms at expected load; AI calls stream or show progress so the UI never appears frozen. |
| Scale | Support tens of thousands of registered users and their stored documents, with a stateless, horizontally scalable API. |
| Reliability | Auto-save must never lose more than the last few seconds of edits; exports are deterministic and repeatable. |
| Security | HTTPS everywhere, hashed passwords, per-user document isolation, signed URLs for stored files and share links. |
| Privacy | Resume data is personal; it is never sold, and AI provider calls send only what a feature needs. |
| ATS fidelity | Every shipped template must pass parsing checks against common ATS parsers before release. |
| Cost control | AI usage is metered per account and rate-limited by tier to keep provider costs predictable. |

---

## 6. Technical Architecture

The system is a three-tier web application: an Angular client, an Express.js API on Node, and MySQL as the system of record, with an object store for generated files and an external LLM provider behind a server-side AI layer.

### 6.1 High-Level Flow (described)

Browser (Angular editor) → HTTPS/REST → Express.js API → MySQL. The API also calls an AI Service layer (which talks to the external LLM provider), an Export Service (which renders PDF and DOCX), an Object Store (for generated files and uploads), and a Parser (for imported resumes). Background workers handle heavier export and parsing jobs so the request path stays fast.

### 6.2 Frontend — Angular

- The editor is the product: a canvas-style, drag-and-drop editor built as a focused Angular feature module.
- **Virtualized rendering** of resume sections so long documents stay smooth, directly addressing the lag competitors are criticized for.
- **Reactive Forms** back each section's fields with structured validation.
- **A live preview** renders the selected template from the same structured data used for export, so what the user sees matches the file.
- **An HTTP interceptor** attaches auth tokens, and a state store (NgRx or a lightweight signal store) holds the working document.
- **Lazy-loaded modules** for marketing, editor, dashboard, and tracker to keep initial load light.

### 6.3 Backend — Express.js (Node)

- **A REST API** by resource: auth, users, documents, templates, ai, exports, tailoring, applications.
- **Middleware** for JWT auth, tier-based authorization and rate limiting, request validation, and centralized errors.
- **An AI Service layer** that owns prompt construction, provider calls, retries, moderation, and per-account metering, so the frontend never touches the provider directly.
- **An Export Service** that renders a resume's structured data to PDF and to genuinely editable DOCX.
- **A Parser Service** that converts uploaded resumes and LinkedIn data into the structured document model.
- **Background workers** (for example BullMQ) for exports, parsing, and batch AI so slow jobs never block the request.

### 6.4 Database — MySQL

MySQL is the system of record for accounts, documents, and their structured content. Resume content is stored as structured data (sections and items) so it can be re-rendered into any template or export format rather than being locked to one layout.

- Access through an ORM/query builder (Sequelize, TypeORM, or Knex) with versioned migrations.
- Document content stored in a normalized set of tables (or JSON columns for flexible section bodies) keyed to the document.
- Indexes on the columns used by the dashboard and tracker (user, updated_at, status).

---

## 7. Data Model (Core Entities)

An indicative core schema; column lists are representative.

| Table | Purpose | Key columns |
|-------|---------|---------------|
| `users` | Accounts | id, name, email, password_hash, tier, ai_credits_used, created_at |
| `documents` | A resume or cover letter | id, user_id, type, title, template_id, updated_at, is_public, share_slug |
| `document_sections` | Sections within a document | id, document_id, section_type, position, is_visible |
| `section_items` | Entries within a section | id, section_id, position, content (JSON), created_at |
| `document_versions` | Saved / tailored versions | id, document_id, label, snapshot (JSON), created_at |
| `templates` | Available designs | id, name, category, config, ats_verified |
| `ai_requests` | AI usage log | id, user_id, feature, tokens, created_at |
| `applications` | Job application tracker | id, user_id, document_id, company, role, status, applied_at |
| `exports` | Generated files | id, document_id, format, file_url, created_at |

---

## 8. Representative API Endpoints

REST over HTTPS, JSON, JWT-secured, rate-limited by tier.

| Method & Path | Purpose | Tier |
|----------------|---------|------|
| `POST /api/auth/register` | Create an account | Public |
| `POST /api/documents` | Create a resume / cover letter | Free |
| `POST /api/documents/import` | Parse an uploaded resume | Free |
| `PUT /api/documents/:id` | Save document content | Free |
| `POST /api/ai/bullets` | Generate / improve bullet points | Free (metered) |
| `POST /api/ai/summary` | Generate summary or headline | Free (metered) |
| `POST /api/ats/check` | Run the ATS check | Free (basic) |
| `POST /api/tailoring` | Tailor to a job description | Pro |
| `POST /api/exports/pdf` | Export as PDF | Free |
| `POST /api/exports/docx` | Export as editable Word | Free |
| `POST /api/documents/:id/share` | Create a public link | Free |
| `POST /api/applications` | Log an application | Free |

---

## 9. Delivery Milestones

| Phase | Scope | Outcome |
|-------|-------|---------|
| M1 — Editor core | Auth, document model, drag-and-drop editor, live preview, auto-save, PDF export | A user can build and download a resume |
| M2 — AI + DOCX | AI bullets/summary/rewrite, editable Word export, basic ATS check | The core differentiators are live |
| M3 — Tailoring + tracker | Job tailoring, versions, application tracker, cover letters | Pro feature set complete |
| M4 — Templates + import | Full template library, resume/LinkedIn import parser, share links | Onboarding friction removed |
| M5 — Monetize + harden | Tiers, day pass, metering, ATS verification pass, performance tuning | Launch-ready |

---

## 10. Risks & Assumptions

### Assumptions

- An external LLM provider is available and its cost per request supports a free metered tier.
- Editable DOCX export can meet a quality bar users accept without heavy manual cleanup.

### Risks

- **AI cost at scale:** heavy free usage could be expensive. Mitigated by per-account metering and tier rate limits.
- **ATS parity claims:** templates must genuinely parse well. Mitigated by a required parsing check before any template ships.
- **Editor performance:** the feature that beats competitors is also the hardest. Mitigated by virtualized rendering and a performance budget from M1.
- **Crowded market:** many incumbents. Mitigated by leading with the concrete gaps (Word export, no watermark, fair pricing) rather than competing on sameness.

---

*End of Document — AI Resume Builder PRD v1.0*
