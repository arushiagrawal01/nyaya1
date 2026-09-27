# Nyaya: backend decisions and implementation checklist

> Superseded on 27 September 2026 by `Nyaya_Master_Prompt_v4.docx` and `NYAYA_V4_INSPECTION.md`. This is the historical local-model proposal; use v4 for current implementation decisions.

Saved 26 September 2026. Status: selected architecture, not implemented or benchmarked. This is the current decision record and supersedes earlier undecided model/database suggestions in the project brief.

## 1. Objective and realistic scope

Make Nyaya respond usefully to varied English, Hindi and mixed-language descriptions, explain documents, and prepare reviewable drafts. Handle inputs through four explicit outcomes: answer from sufficient evidence, ask for missing facts, report missing coverage, or route an urgent/complex issue toward qualified help. Handling a question does not mean claiming to solve it.

No LLM, RAG system or source collection guarantees correct answers to every Indian legal problem. Accuracy requires current sources, jurisdiction and event-date checks, testing, and qualified review. A state selector is not nationwide legal coverage.

Existing assets: bilingual static demo, justice branding, themes, sample analysis/document walkthrough, editable templates, Gmail compose links, and setup-only API settings. Backend, live inference, RAG, uploads and legal applicability checks remain unimplemented. No application code or packages are installed by this planning task.

## 2. Selected components

| Layer | Decision | Purpose / constraints |
|---|---|---|
| Interface | Retain HTML/CSS/JavaScript | Connect existing forms and cards to the backend; preserve bilingual UI and editable drafts. |
| Backend | Python + FastAPI + Uvicorn | Typed API endpoints, validation and request coordination. |
| Contracts | Pydantic | Strict request/response schemas; reject malformed or unexpected model output. |
| Initial LLM | Existing local Qwen3.5 via Ollama | `qwen3.5:latest`, installed ID prefix `6488c96fa5fa`; local inspection reports 9.7B parameters, Q4_K_M. Pin the exact installed digest before implementation instead of relying on the mutable `latest` tag. |
| Inference client | HTTPX to local Ollama | Server-side calls with timeouts and bounded concurrency; no browser access to Ollama. |
| Embedding model | `intfloat/multilingual-e5-small` through Sentence Transformers | English/Hindi semantic retrieval on CPU initially, leaving GPU memory for the LLM. Pin model revision. Follow its query/passage prefixes and tokenizer limit. |
| Keyword retrieval | SQLite FTS5 / BM25 | Find exact phrases, sections, authority names and legal terms. Test Hindi tokenization explicitly. |
| Semantic retrieval | Normalized embedding vectors + NumPy cosine search | Small corpus: store vectors and metadata in SQLite, load eligible vectors into memory, search exactly. No separate vector database initially. |
| Rank fusion | Reciprocal Rank Fusion | Merge keyword and semantic rankings; scores are ranking signals, not confidence probabilities. |
| Reranker | `BAAI/bge-reranker-v2-m3`, second-stage option | Reorder a small candidate list if it improves measured relevance within RAM/latency limits. Keep off until benchmarked; run on CPU or separately from LLM inference. |
| Metadata database | SQLite | Source versions, chunks, coverage, review records and evaluation results. |
| PDF extraction | pypdf + pdfplumber | Extract text, page numbers and tables where practical; inspect extraction failures. |
| OCR | Deferred, explicit unreadable-scan response initially | Add a separately tested OCR pipeline only when needed; never infer text from unreadable scans silently. |
| Tests | pytest + HTTPX; browser interaction tests | API contracts, retrieval, citations, uploads, bilingual flows and UI failures. |
| Larger deployment | PostgreSQL + pgvector + full-text search | Migration target when concurrent users/corpus size justify it; not a second database to install now. |

Choose direct Python services first; no agent framework, autonomous browsing, fine-tuning, or multi-agent inference is required for the core workflows.

## 3. LLM settings, hardware and decision gate

Observed machine: approximately 16 GB RAM and 8 GB GPU VRAM. Qwen's installed file is about 6.6 GB; file size is not total runtime memory. The larger installed Gemma model is not the default on this machine. Qwen's advertised maximum context is not a practical local context budget.

Start experiments at 4,096-8,192 context tokens, bounded output, one inference request at a time, and conservative sampling. Compare low-temperature and model-recommended settings on the actual evaluation set rather than assuming either guarantees truth. Use Ollama schema-constrained output and Pydantic validation. Measure cold/warm latency, memory use, JSON validity, abstention, source fidelity and Hindi quality.

**Decision:** Qwen3.5 is selected for the first build under the current no-hosted-API constraint. It is not declared the most accurate model or approved for production legal guidance. If it fails the quality gate, narrow coverage or evaluate a stronger hosted model with the same adapter. Hosted provider selection then requires actual account access, budget, data-handling review and comparative tests; no paid service is authorized or provisioned now. Do not silently send legal text to a cloud fallback.

## 4. RAG workflow

```text
Input in English / Hindi / Hinglish
    -> detect possible urgency and domain(s)
    -> identify missing state, event date and material facts
    -> ask essential clarifying questions
    -> check topic x jurisdiction x date coverage
    -> filter eligible reviewed source versions
    -> keyword search + multilingual embedding search
    -> merge rankings (optional tested reranker)
    -> retrieve relevant sections with definitions/exceptions
    -> LLM produces structured claims using supplied passage IDs
    -> check schema, cited passages, numbers and applicability
    -> answer with inspectable evidence OR explain uncertainty
```

Input limits: distinguish empty, oversized, unrelated, ambiguous, multi-issue and adversarial inputs. Do not replace arbitrary real questions with canned sample answers. Resolve Hindi/Hinglish ambiguity through clarification; a translation must not silently change allegations, dates or amounts.

Ingestion: use an allowlist of official sources; respect access and reuse terms; download public materials without bypassing controls. Preserve original content, checksum, extraction output and review history. Split by section/heading, retaining exceptions and definitions. For E5-small start around 250-350 tokenizer tokens per passage with limited overlap and reserve space for headings; enforce the actual model token limit. Never slice away a legally material exception merely to fit the budget.

Initial search settings to tune: top 20 lexical plus top 20 semantic candidates, deduplicate and fuse, then assemble roughly 4-8 useful passages within the available context. No fixed score threshold is assumed reliable across languages; calibrate evidence sufficiency on labeled cases.

Keep uploaded-document passages in a separate session namespace. They establish what that document says, not what the law requires. Legal sources and personal-document citations must be labelled differently. Never merge personal documents into the public legal corpus.

## 5. Source acquisition list

These are source families to acquire and review, not an already ingested or legally validated dataset. Homepages establish discovery routes; cite exact documents/sections for legal claims.

| Source | Intended use | Readiness / next action |
|---|---|---|
| [India Code](https://indiacode.gov.in/) | Central/state enactments and linked subordinate material | The old NIC homepage explicitly redirects to this domain. Confirm each instrument's version and applicability. |
| [eGazette](https://egazette.gov.in/) | Notifications, amendments and commencement dates | Direct fetch failed in this session. Verify access and exact documents before ingestion. |
| Official state law departments and state gazettes | State amendments, rules and local applicability | Discover from official state portals; no verified per-state corpus exists yet. |
| [Ministry of Labour and Employment](https://www.labour.gov.in/) + relevant state labour departments | Employment, wages, current rules and authority information | Homepage returned no extractable text here. Acquire exact official instruments and commencement/transitional material before using. |
| [National Consumer Helpline](https://consumerhelpline.gov.in/public/) + linked official consumer authorities | Consumer grievance guidance and referral information | Portal inspected; verify underlying law separately. Not an employment complaint route. |
| [National Cyber Crime Reporting Portal](https://cybercrime.gov.in/) | Cybercrime awareness, official reporting guidance and manuals | Public portal inspected. Keep reporting information current. |
| [NALSA](https://nalsa.gov.in/) + linked State/District Legal Services Authorities | Legal-aid information, eligibility sources and referrals | Portal and state-directory presence inspected. Do not promise individual eligibility. |
| [Reserve Bank of India](https://www.rbi.org.in/) and its linked complaint resources | Banking/payment grievance directions and official guidance | Portal inspected; acquire exact current directions and scheme documents. |
| [CPGRAMS](https://pgportal.gov.in/) | Government grievance procedures and exclusions | Portal inspected. Verify eligibility and scope rather than presenting it as a universal legal remedy. |
| Official state RERA / housing / registration authorities | Housing and property awareness | Later coverage. Requires separate state-specific verification; never assume a model tenancy text is adopted everywhere. |
| [Supreme Court](https://www.sci.gov.in/) and official High Court sites | Selected relevant judgments | Later, curated expansion. Check legal status, jurisdiction and subsequent treatment with qualified review. Do not infer applicability from similarity alone. |

Source hierarchy: authoritative legal text and relevant notifications for legal rules; official guidance for procedures; uploaded documents for facts and wording. A portal FAQ is not a substitute for legislation. Where sources conflict or their current status is unknown, flag the conflict and withhold a definitive claim.

Initial coverage: employment and consumer first; cybercrime and banking next; rental, family, criminal-procedure and other complex domains only after appropriate source review. Start with roughly 20-40 carefully selected documents rather than claiming every Indian law. Choose the first state after team/demo needs are confirmed; keep all other state/topic combinations explicitly unsupported until reviewed.

## 6. Database and storage list

SQLite will hold non-sensitive knowledge and provenance by default:

- `sources`: authority, canonical URL, document type, retrieval permissions and trust classification.
- `source_versions`: title, publication date, effective-from/to dates, dates unknown flag, central/state jurisdiction, amendment relationships, checksum, local original path, language, retrieved-at, reviewed-at, review status, reviewer and superseded-by.
- `chunks`: version ID, section/heading, page, text, character offsets, language and token count.
- `chunk_embeddings`: chunk ID, pinned model/revision, vector dimension, normalized float32 vector and index generation.
- `chunks_fts`: FTS5 index for public-source text; update transactionally with chunks.
- `coverage`: topic, jurisdiction, date range, supported question types, limitations and review state.
- `referrals`: official authority, territory, scope, verified contact URL and last-check date.
- `evaluation_cases` / `evaluation_runs`: synthetic or de-identified cases, expected evidence, outcomes and model/index versions.

Personal input/session history: process memory initially. Uploaded files: private temporary storage outside the public website directory; random identifiers, session isolation, proposed 5 MB/50-page limits and an initial 30-minute expiry policy. Implement deletion on expiry, error and restart recovery, and test it. Drafts remain client-side unless the user explicitly saves them. No permanent personal-data tables by default; no request bodies in logs.

For growth, migrate public chunks, metadata, vectors and lexical search to PostgreSQL/pgvector. Embedding-model changes require a complete re-index with a new version; never compare mixed vector spaces.

## 7. Accuracy and release gates

1. Ask for material missing facts; state choice alone does not establish jurisdiction.
2. Track both incident date and current date; repeal, commencement and transitional rules can matter. Avoid unsupported deadlines and section numbers.
3. Every substantive legal claim needs a source ID resolving to an eligible passage. Render URLs from the database, not model-written URLs.
4. Check passage support, exceptions and contradictions. Valid JSON and real links do not prove the claim is true. Automated checks assist, but do not replace qualified review.
5. Use statuses such as sufficient evidence, needs clarification, insufficient coverage and professional review recommended. Avoid uncalibrated numeric confidence scores.
6. Treat instructions inside uploads/retrieval as untrusted data; no automatic filing, emailing or external tool execution from generated answers.
7. Keep one frozen evaluation set separate from prompt tuning. Proposed initial set: 120-150 reviewed cases across supported and unsupported topics, bilingual variants, mixed issues, historical dates, urgent inputs, misleading documents and malicious prompts.
8. Proposed engineering gates, not achieved results: all citations resolve; no fabricated legal authorities or ungrounded critical deadlines in the release set; at least 95% evidence recall@10 on answerable cases with known gold passages; final displayed responses always pass the schema or return a controlled error. Report denominators and English/Hindi/state breakdowns. Critical failures block release even if an average score passes.
9. Have qualified reviewers assess legal support, applicability and next-step safety on the supported set. Report measured results and known gaps without advertising a universal accuracy percentage.
10. Measure median/p95 end-to-end latency and upload failure behavior on the actual laptop. Target a usable demo response time after measurement; don't promise a hardware-dependent number now.

## 8. Backend interfaces and operational needs

- `GET /api/health`: actual model/retrieval availability; never return credentials.
- `POST /api/analyze`: description, language, state, district, event date, role and follow-up answers; return structured issue cards, missing facts, evidence and coverage status.
- `POST /api/documents/upload`: allowed files, bounded extraction, session-bound expiring handle.
- `POST /api/documents/explain`: explanation and Q&A with page evidence; separate legal commentary if verified sources support it.
- `POST /api/drafts/generate`: known facts, purpose and language; unknown fields remain explicit, no invented allegations, dates or authorities.
- `GET /api/sources/{id}`: approved public-source excerpts/provenance only; never private uploads.

Use request/file/token limits, extraction timeouts, rate limiting, bounded queues, parameterized queries, safe text rendering, restricted CORS and server-side credentials. Keep Ollama bound to localhost. Static website hosting cannot run the planned Python backend; use a Python-capable service or a local backend for the demo. A publicly usable local-GPU service needs deliberate secure deployment and capacity planning, not an exposed Ollama port.

Pin compatible package versions after a clean install/test. Python 3.14 is currently installed; if the selected ML/PDF dependencies lack compatible wheels, use an isolated supported Python version rather than modifying system Python blindly. No dependency install is part of this saved plan.

## 9. Ordered implementation checklist

- [ ] Create backend skeleton, environment, schema and health endpoint; connect the current UI.
- [ ] Pin Qwen model digest; benchmark generic JSON and English/Hindi instructions.
- [ ] Select first state and precise initial topics; establish source registry and legal-review process.
- [ ] Acquire/review versioned sources and build SQLite metadata, FTS5 and E5 embeddings.
- [ ] Test retrieval independently; add reranking only if it helps.
- [ ] Add grounded analysis, clarification, coverage checks and claim-level evidence.
- [ ] Add isolated file extraction and document Q&A.
- [ ] Add live draft generation while preserving manual review and Gmail compose.
- [ ] Run quality, security, bilingual and failure-path evaluation; fix critical errors.
- [ ] Deploy the Python backend securely, package a reproducible build and rehearse the demo.

Next action when resumed: implement the first checklist item, then benchmark Qwen. Do not skip directly to broad live legal answers. Recheck model/package availability and current legal sources before implementation.

## 10. Technical references consulted

- [Ollama structured outputs](https://docs.ollama.com/capabilities/structured-outputs): local JSON schema support and validation pattern.
- [E5-small model card](https://huggingface.co/intfloat/multilingual-e5-small/raw/main/README.md): embedding setup, prefixes and model limits.
- [SQLite FTS5](https://www.sqlite.org/fts5.html): lexical indexing and BM25.
- [BGE reranker documentation](https://bge-model.com/bge/bge_reranker_v2.html): optional multilingual reranking candidate.
- [pgvector](https://github.com/pgvector/pgvector): later PostgreSQL vector-search migration.
- [India Code migration notice](https://www.indiacode.nic.in/): official move to indiacode.gov.in.

Research identifies suitable components and source families; it does not establish model accuracy, corpus completeness or legal validity. No secrets are stored in this document.
