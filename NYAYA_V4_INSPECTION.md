# Nyaya v4 inspection and implementation roadmap

**Historical pre-build inspection.** For current implemented Groq/local-embedding status, read `NYAYA_BUILD_STATUS.md` and `nyaya-first-aid/README_BACKEND.md`. The user explicitly approved the provider change. Backend/key-waiting statements below describe the earlier inspection.

27 September 2026. Authority: the user's adopted `Nyaya_Master_Prompt_v4.docx`. This supersedes the technology and transient-personal-data decisions in the earlier backend plan. This report records inspection and planned work, not completed backend functionality.

## A. Current architecture

`nyaya-first-aid/dist/index.html`, `styles.css`, `app.js` and `assets/lady-justice.png` form a static HTML/CSS/JavaScript application. `Nyaya_First_Aid_Demo.html` is the equivalent bundled offline artifact. No backend, database, dependency environment, legal corpus or authenticated session exists. The project directory is not a Git repository. Hosting configuration is present, but successful publication is not established.

## B. What already works

English/Hindi interface, dark/light themes, 36 state/UT options, district field, prepared salary walkthrough, fictional employment-letter explanation, editable bilingual salary-request template, download/copy and reviewed Gmail compose flow. Gmail opens a draft and does not send it. Language/theme are saved locally; personal form values exist only in page memory.

The existing JavaScript verification script passed this inspection: jurisdiction uniqueness, Hindi drafting, preservation of edits across language changes, HTML escaping, Gmail Unicode encoding, review gating, latest-message handling, long-message fallback, disabled API-key input. Standalone HTML was checked against current source CSS, JavaScript and embedded artwork and matched. These are simulated-DOM checks, not a new browser end-to-end pass or evidence of backend functionality.

## C. What is hard-coded

The problem form recognizes only the exact prepared salary example. Analysis, missing-fact list, fictional letter, three document questions and their answers are fixed strings. Draft generation fills a fixed template. API settings only download a future configuration. There is no upload processing or model request.

## D. What must change

Replace the exact-example gate with a continuous conversation and server requests. Infer categories and material missing facts from the case. Add user-owned case history, document context, readiness state, risk routing, evidence-based answers and source freshness. Replace obsolete local-model setup copy and page-only privacy copy when the new behavior exists. Never relabel the static demo as live prematurely.

## E. What can be preserved

Nyaya name, scales mark, requested tagline, Lady Justice artwork, navy/gold themes, language controls, responsive layouts, focus styles, reduced-motion support, state/UT list, input escaping and reviewed Gmail compose behavior. Keep the existing demo usable while building the new backend. Avoid loading the large artwork on every chat turn.

## F. Required backend

FastAPI monolith serving the frontend and API on one origin. Pydantic validation, bounded requests, controlled errors and explicit health/capability status. OpenAI is the v4 default: `ai_service.py` owns Chat Completions, default `gpt-4o-mini`; `rag_service.py` owns embedding/retrieval, default `text-embedding-3-small`. Verify current API compatibility before implementation. Timeouts, bounded retries, token limits and atomic per-session daily call reservation belong inside the AI service. Bound embedding expenditure separately, including ingestion and uploads. No model fallback that silently changes provider or sends additional data.

## G. Database

SQLite for sessions, cases, messages, facts/checklists, documents, legal sources, immutable versions, chunks, change logs, case-source links and usage counters. Every private row carries an owner/session ID. Scope every read/write/join by that owner. Chroma stores public legal vectors with version/jurisdiction metadata. Private document vectors require a separately protected storage strategy; initially use encrypted extracted text and short bounded context, with encrypted vectors searched in memory for longer documents if needed. Do not put plaintext private chunks into an unencrypted Chroma database.

## H. RAG architecture

Real OpenAI embeddings plus persistent Chroma retrieval, combined with SQLite FTS5 lexical retrieval and rank fusion. Filter eligible versions by jurisdiction, legal date and review status before ranking; retrieve central law with an explicit `jurisdiction_fallback` flag when state coverage is missing. Citations resolve through stored chunk IDs, never model-invented URLs. Separate user-document evidence from law. Historical incidents require transition-aware handling rather than assuming new criminal codes apply retrospectively.

## I. Source verification and updates

Seed official BNS/BNSS/BSA and historical IPC/CrPC/Evidence Act material, with precise source URLs and verified commencement/transition metadata before making legal claims. Retain publication, amendment, commencement and effective dates separately, including unknown values. Add topic-specific official material only after review. Source acquisition and legal verification are pending; the supplied prompt itself is not legal evidence.

Build immutable versions, content hashes, diffs and an admin-triggered update cycle. A changed page is only a candidate update until applicability is checked. Demo fixtures must be visibly labelled simulations and isolated from answerable legal sources. No claim of continuous crawling. Answer-time live verification and a scheduled crawler are deferred.

## J. Documents

Start with size/page-bounded PDF and UTF-8 text extraction, safe random storage names, file signature checks, parser errors/timeouts and page/paragraph provenance. Reject unsupported scans clearly. Persist the owner/case relationship and extracted context so follow-up questions need no re-upload. Explain dates/amounts as document wording, not automatically valid statutory obligations. OCR and regional scripts remain stretch work.

## K. Case memory

Create, rename, reopen, continue, archive and delete cases; remove individual documents. Store facts, missing questions, summaries, messages, source links, timeline, pending actions and risk flags. Preserve contradictory facts for clarification rather than silently replacing them. A secure opaque session cookie identifies the owner. Explain that clearing the cookie loses access unless a recovery/authentication feature is added; no account recovery is claimed by a basic session implementation.

## L. Security concerns

Require plain-language consent before storing cases or sending relevant extracts to OpenAI. Verify provider retention/processing policies before live use; do not claim regulatory compliance solely because consent is present. Use authenticated encryption for private case content and uploaded documents, with an external server-side key; do not assume full-disk encryption is enabled. Keep plaintext out of indexes/logs/backups. Implement deletion and retention for related rows, files and indexes. Use owner checks, CSRF/origin protection, safe cookies, safe text rendering, parameterized SQL, upload limits, rate limits and server-only secrets. Treat retrieved content, documents and user text as untrusted. Risk screening, including minor-harm escalation, precedes normal checklist/retrieval work. Verify helpline sources before shipping specific numbers.

## M. Ordered roadmap and scope

| Step | Scope | Completion evidence |
|---|---|---|
| Inspection and migration plan | MUST | This report and refreshed project memory saved; complete |
| Backend and frontend round trip | MUST | Server health responds; custom message reaches backend |
| AI adapter and cost/error controls | MUST | Real provider response; timeout/quota paths tested; no browser key |
| Session isolation and encrypted storage | MUST | Two-user access tests fail closed; private data encrypted on disk |
| Contextual chat and case controls | MUST | Pronoun follow-up; restart/reopen; rename/archive/delete tests |
| Classification, dynamic checklist, readiness | MUST | Different unseen problem types produce relevant checks; education remains available |
| Curated versioned hybrid RAG | MUST | Real embeddings; known gold passage retrieved with exact provenance; date/state filters tested |
| Source freshness disclosure | MUST | Cached retrieval labelled honestly; unknown applicability triggers caution |
| PDF/text upload, explanation and follow-up | MUST | Extracted page evidence survives reopening; document isolation/deletion tested |
| High-risk and minor-harm escalation | MUST | Bypasses ordinary dialogue; bilingual/adversarial cases tested |
| Version and update-cycle demonstration | SIMULATE | Real version/diff schema and admin endpoint; labelled fixture update preserves history |
| Security and functional regression suite | MUST | IDOR, XSS, injection, invalid uploads, provider failures and secret checks pass |
| Human legal review and demo rehearsal | MUST | Human reads 3–5 outputs; full demo executed; failures recorded |
| OCR, live verification, scheduled crawling, PostgreSQL, lawyer preparation | STRETCH | Only claim individually after implementation and tests |
| Broader accessibility and low-bandwidth audit | STRETCH | Preserve baseline labels/focus now; test expanded support later |

Backend step order follows section 20 (the prompt's earlier reference to section 21 as development order is a numbering mismatch). Risk handling is designed at foundation stage so no later milestone accidentally treats urgent inputs as routine.

## Current gate

No `OPENAI_API_KEY` is configured in the current process, and no private `.env` is present in the inspected project root. No secret value was read or printed. Section 0.1 of the adopted prompt requires stopping to ask when a credential or paid service is required but unavailable. The new provider choice is explicit user direction, so no separate architecture approval is needed. Backend implementation and paid model calls have not begun. Next: user privately configures a billed OpenAI project key and confirms a small test budget, then proceed through the implementation checks above. Do not put a key into chat or the website.
