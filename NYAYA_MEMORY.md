# Nyaya project memory

## ACTIVE CHECKPOINT — 27 September 2026, RESUMED

**Latest verified result:** the complete paced live integration test now passes (employment checklist, document upload/Q&A, Hindi follow-up, saved case/document reopen). 18 Python tests and JavaScript draft checks pass. Groq rate-limit errors are explicit. The running server includes the current backend. Case Gmail drafting and Hindi main/draft controls are now implemented and browser-checked. Dependencies were frozen including FastEmbed. Read `NYAYA_BUILD_STATUS.md` for the current truth; earlier failed-run and unimplemented-drafting notes below are historical.

The user explicitly resumed website work and cancelled JPG work. The earlier usage pause is revoked. Current verification and pending tasks are in `NYAYA_BUILD_STATUS.md`. Latest changes: explicit Groq rate-limit response, paced live test, connected-case editable Gmail draft with review gate, Hindi controls, frozen dependencies including FastEmbed. Automated tests: 18 Python tests plus JavaScript draft checks pass.

The user asked to pause Astra website development at 10% remaining usage. Account check confirmed 90% used in the five-hour window. Do not resume backend/frontend development until the user resumes; presentation concept snapshots are separately authorized. Reset timestamp reported: 1790496779. No automatic resume scheduled.

User explicitly approved **Groq + local embeddings**, superseding the OpenAI requirement below. Current connected project: `nyaya-first-aid`; read its `README_BACKEND.md` first. Groq model `openai/gpt-oss-120b`; local FastEmbed multilingual MiniLM-L12-v2; Chroma + SQLite FTS5; FastAPI; encrypted SQLite cases/documents; cookie owner isolation; live case chat and document uploads; dynamic checklists; conservative claim guard and risk escalation. Original demo is preserved separately.

18 automated tests passed. Live employment and document Q&A succeeded in isolated checks. The complete live smoke sequence has NOT passed: repeated calls encountered Groq 400 structured-generation failures and later 429 rate limits. Retry for known generation failures added. Final edit makes 429 return an explicit wait/retry message; this last edit has not been rerun or loaded into the currently running server. Resume by restarting server, running tests, then a paced live smoke/Hindi follow-up/reopen check. No more provider calls during pause.

Upload Windows named-pipe failure fixed using a time-bounded subprocess. Rejected paraphrases now fall back to exact document excerpts. Local snapshot corpus: six official statute PDFs, nine records, six short eligible summaries; historical records excluded. State-specific laws and lawyer review are NOT complete. No universal accuracy claim. A hallucinated Delhi deposit claim observed in testing prompted stricter output guards; residual model factual errors remain possible.

Still pending: refresh frozen requirements to include FastEmbed; update older project documents; safe distributable package; full live Hindi/UI/mobile testing; reviewed state/topic corpus; legal evaluation; integrate live drafting/Gmail (original demo retains it); production authentication/privacy/security/deployment. No public deployment verified. Automatic 90-day record deletion was rejected by approval review and was NOT implemented; deletion remains user-controlled. Never print or package `.env`, `outputs/nyaya.env`, private `data`, keys or model caches.

These checkpoint notes supersede all older statements below saying no backend exists or OpenAI is configured.

Saved 26 September 2026. This is a file-backed project handoff, not a claim of account-wide assistant memory.

## Resume from here

Read `NYAYA_V4_INSPECTION.md` and `Nyaya_Master_Prompt_v4.docx` first. On 27 September 2026 the user adopted v4 and authorized the actual build. It supersedes the older local-model and transient-personal-data decisions below.

Current stack: FastAPI, SQLite, OpenAI Chat Completions (`gpt-4o-mini` default), OpenAI embeddings (`text-embedding-3-small` default), Chroma plus lexical retrieval. Require persistent encrypted cases/documents, owner-scoped sessions, contextual chat, dynamic checklists, readiness, grounded citations, document Q&A and risk escalation. Preserve branding and useful UI. Update synchronization uses a labelled on-demand demo and real version/diff schema. OCR/live verification/PostgreSQL are stretch.

Inspection and existing demo checks completed. Standalone bundle matches source assets. No backend exists yet. V4 section 0.1 requires a credential pause: no OPENAI_API_KEY is available in this process and no project-root .env exists. User must configure a key privately and confirm a small live-test budget; never request secrets in chat. Then implement the ordered roadmap in the inspection report. Do not silently revert to Ollama. Human legal review remains required before release.

## Historical 26 September plan

User wants a broadly useful and accurate legal-awareness app. Current request was to decide and save the plan for later; backend implementation has not started.

- Name: Nyaya. Tagline: First-Aid to your legal issues.
- Current site: `nyaya-first-aid/dist/`; standalone `Nyaya_First_Aid_Demo.html`; package `Nyaya_Website.zip`.
- Working demo: English/Hindi, dark/light themes, all states/UTs, fixed sample analysis/document explanation, editable templates, Gmail compose, setup-only API panel. No live AI, RAG, backend or uploads yet.
- Initial LLM selected: installed Qwen3.5 via local Ollama, ID prefix `6488c96fa5fa`, 9.7B Q4_K_M per local inspection. Pin full digest before use. Not benchmarked or legally validated.
- Backend: FastAPI, Uvicorn, Pydantic, HTTPX.
- RAG: source/date/jurisdiction filters + SQLite FTS5/BM25 + multilingual-e5-small embeddings + NumPy exact cosine search + reciprocal rank fusion. BGE reranker-v2-m3 only after benchmark benefit.
- Database: SQLite metadata/chunks/vectors/coverage/evaluations. PostgreSQL + pgvector is the later scaling target.
- Sources: India Code (migrated to indiacode.gov.in), central/state gazettes, official labour/consumer/cybercrime/RBI/NALSA/CPGRAMS material; later state housing/RERA and selected official judgments. No corpus ingested. Gazette fetch failed; labour homepage text was not extractable.
- Start employment/consumer with a small reviewed corpus; first state still to be selected. All-state UI is not all-state legal coverage.
- Hardware: about 16 GB RAM, 8 GB VRAM. No hosted API access/budget confirmed. Local-model quality and memory must be measured; no silent cloud fallback.
- Accuracy: clarify, ground, cite exact passages, check dates/applicability, decline unsupported claims, qualified legal review and bilingual evaluation. No universal accuracy guarantee.
- Privacy: public-source persistence only by default; personal data transient; credentials server-side; Gmail never sends automatically.
- Next work: backend skeleton and health endpoint, followed by generic structured-output model benchmark. Follow the detailed checklist in the plan.

For retrieval in this task, the user can say: “Open the saved Nyaya backend plan.” In another task, supply this file or its path if the workspace context is unavailable.
