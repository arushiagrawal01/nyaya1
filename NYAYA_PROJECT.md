# Nyaya

**First-Aid to your legal issues**

## Latest connected build — 27 September 2026

The user approved Groq + local multilingual embeddings. A FastAPI backend, encrypted persistent cases, document upload/Q&A, hybrid retrieval, risk escalation and case-email drafting are implemented. See `nyaya-first-aid/README_BACKEND.md` and `NYAYA_BUILD_STATUS.md` for verified status and limits. This supersedes the older OpenAI/key-waiting statements below. State-specific legal coverage and professional review remain pending.

## Historical website updates

**27 September 2026:** user adopted master prompt v4. See `NYAYA_V4_INSPECTION.md` for current architecture and scope, and `NYAYA_MEMORY.md` for the handoff. OpenAI replaces the old local-model proposal; encrypted persistent cases are now required. Inspection is complete; backend implementation awaits private credential setup under v4 section 0.1.

**Backend decisions saved:** see `NYAYA_BACKEND_PLAN.md` for the selected LLM, hybrid RAG, database, source registry and implementation checklist, and `NYAYA_MEMORY.md` for the resume handoff. These decisions supersede older undecided stack suggestions below. They are plans, not implemented features.

The product is now named Nyaya. The demo includes a scales logo, generated Lady Justice artwork, navy/gold styling, light/dark themes, smooth transitions and reduced-motion support. English and Hindi cover navigation, forms, prepared explanations, messages and draft templates. Language changes preserve user edits.

All 28 states and 8 union territories can be selected for the work/incident location, with optional city/district context. State-specific law and applicability remain unverified; the interface explicitly says legal coverage is not connected and links to NALSA for state-authority discovery.

The draft view now accepts a recipient email and subject and opens the latest reviewed message in Gmail. It never sends automatically. Users must review, sign in if needed, add attachments and send. A setup-only API connection view offers provider/model selection and an empty-key environment template. Real credentials must be configured on a future backend; the browser key field is disabled.

The standalone `Nyaya_First_Aid_Demo.html` is updated and contains all assets for offline use. The source website has five views including API connection. Earlier PDF and historical planning sections below describe the earlier project stage; live AI, RAG, PDF uploads and backend are still not implemented. Publishing has not succeeded.

Project brief, development blueprint, and Round 1 presentation guide. Updated 26 September 2026.

## 1. Current milestone

Round 1 is idea presentation and refinement, with approximately one hour available. The team is starting fresh, has internet access, and has no hosted AI API access or credits confirmed. Later-round deadlines are unknown.

The immediate deliverable is an interactive concept demo, not a completed legal AI service. The original discovery-first sequence has been adapted at the team's explicit request to prioritize this demo.

### Implemented today

- Responsive website with four views: problem walkthrough, document walkthrough, draft preparation, and trust/sources.
- One explicitly prepared unpaid-salary scenario with illustrative next steps and missing-information questions.
- Fictional employment-letter explanation and three prepared document questions.
- Locally generated, editable salary-request template with copy and text download.
- Clear sample labels, privacy information, professional-help boundary, and official resource links.
- No dependencies for the portable demo: open `nyaya-first-aid/dist/index.html` in a browser.

### Not implemented yet

Live AI, a FastAPI backend, file uploads, PDF extraction, OCR, database storage, embeddings, RAG, personalized legal analysis, claim-level legal citations, login, complaint filing, and professional representation.

The demo does not infer a legal answer from arbitrary input. It rejects unsupported scenarios and invites the presenter to use the prepared example. State selection is illustrative and does not change the sample. This distinction must remain visible during judging.

## 2. Problem statement

People facing an everyday legal concern often cannot tell what kind of issue they have, which details matter, where to find trustworthy information, or what a practical first step could be. Search results and unfamiliar terminology can make the starting point difficult.

These are the team's problem hypotheses, not validated user-research findings. We will test them with interviews and supervised usability sessions. Do not invent adoption, accuracy, or impact statistics.

## 3. Proposed solution

Nyaya First-Aid is a legal-awareness and first-aid assistant for India. It converts a plain-language situation into an understandable issue summary, relevant source-backed information, a checklist of missing facts, and possible next steps. It can explain documents and help users prepare editable drafts.

It is not a lawyer, a court-outcome predictor, or an emergency service. It does not guarantee that a complaint route is applicable or that a document clause is enforceable.

## 4. Focus and first audience

Initial focus: adults trying to understand an unpaid-salary concern and organize their records before seeking help. Employment is the demonstration topic, not a claim that one employment process applies to everyone.

Start with a small reviewed topic collection. Expand later into consumer grievances and other topics only when the relevant sources, jurisdictions, and tests are ready. Do not attempt to ingest all Indian law.

## 5. Three core journeys

### Understand my problem

Describe events → collect jurisdiction and missing facts → retrieve relevant official passages → generate a cautious explanation → validate output → show next steps and claim-linked sources.

### Explain my document

Validate an allowed file → extract text → retain page references → identify parties, dates, amounts, requests and clauses → explain with document evidence → answer questions from that document. Clearly distinguish document statements from legal conclusions. Flag unreadable or incomplete extraction.

### Prepare a draft

Choose purpose → collect known facts → show missing fields → generate a restrained draft → let the user edit → copy/download. Never silently invent names, dates, amounts, allegations, or statutory claims. Do not send or file anything automatically.

Live AI drafts must say: **AI-generated draft — review carefully before use.** Today's demo is a deterministic template and is labelled accordingly.

## 6. What makes the approach useful

- A structured first-aid workflow rather than an open-ended chat screen.
- Missing facts and uncertainty are shown alongside the answer.
- Important legal claims should be traceable to retained official passages.
- Document explanations should reference the user's document, not model memory.
- Each flow ends in something reviewable: a checklist, a question, or an editable draft.
- The interface separates a document's wording, general awareness, and verified legal claims.

This is a product direction, not a claim of unique technology or proven superiority over existing products.

## 7. Architecture

```text
Browser: HTML + CSS + JavaScript
             |
             v
Python / FastAPI: validate input and coordinate requests
             |
       +-----+--------------------+
       |                          |
Document extraction         Trusted-source retrieval
       |                          |
       +-----------+--------------+
                   v
AI adapter: selected local model OR hosted provider
                   |
                   v
Structured-output and citation validation
                   |
                   v
Browser cards: explanation, missing facts, next steps, sources

SQLite: official-source metadata and review records
Personal input: transient by default, not saved unnecessarily
```

The browser is the visible interface. FastAPI is the coordinator behind it. An AI adapter lets us change model providers without redesigning the interface. Retrieval supplies source passages. Validation checks that the answer has the right structure and references only permitted source IDs.

The hosted Round 1 demo is static. A future Python backend requires a Python-capable hosting environment or local server; static hosting does not run FastAPI. Choose deployment separately after the backend is tested.

## 8. Environment and model decision

Detected: Windows 11, approximately 16 GB RAM, Intel CPU with 28 logical processors, RTX 5050 Laptop GPU with approximately 8 GB VRAM, and approximately 145 GB free storage. Python 3.14.7, Node 24.21.0, Git 2.55.0, VS Code, and Ollama 0.34.4 are installed. Ollama lists `qwen3.5:latest` and `gemma4:26b`.

No hosted API credentials were detected in the development session, and the team confirmed no API access. Ollama is an available option, not a requirement. Download size does not establish runtime memory use or performance. Benchmark the existing smaller model with generic structured output before any legal workflow. Record latency, output validity, memory use, grounding behavior, and failure rates. Do not assume either installed model is suitable.

If local inference is inadequate, evaluate hosted API access, cost, retention, consent, context size, and rate limits before selecting a provider. No provider has been selected or paid for.

## 9. Planned API and response contract

The response contract is a checklist that makes results predictable for the interface. It must distinguish unknown information from confident conclusions. Avoid a numerical confidence score unless it has been calibrated and evaluated.

Analysis response fields: possible_issue, summary, jurisdiction, missing_information, legal_information, next_steps, documents_to_collect, risk_flags, professional_help, sources, limitations, and mode. Each substantive legal claim should carry supporting source IDs. Mode distinguishes sample, live, and unavailable states.

| Endpoint | Request / purpose | Response | Main validation and errors |
|---|---|---|---|
| GET /api/health | Service check | Health and enabled capabilities | Do not expose secrets or infrastructure details |
| POST /api/analyze | Description and jurisdiction | Validated analysis cards | Length limits; 422 invalid input, 429 rate limit, 503 AI unavailable |
| POST /api/documents/upload | Supported file | Transient document handle and extraction status | Proposed 5 MB limit; 413 too large, 415 unsupported, 422 unreadable |
| POST /api/documents/explain | Document handle and optional question | Explanation with page references | Expiring session-bound handles; 404/410 missing or expired |
| POST /api/drafts/generate | Draft type and known fields | Editable draft and missing fields | No invented details; 422 invalid fields, 503 AI unavailable |
| POST /api/rag/search | Query and jurisdiction | Source chunks and metadata | Input limits; explicit no-match response |

These endpoints are planned, not present in the static demo. Public prototype endpoints may omit accounts but still require rate limits and request limits. Uploaded-document access must be scoped to an unguessable, expiring session. Production authentication will be evaluated before any persistent sensitive-data use.

## 10. RAG, explained simply

- **Chunking:** split a reviewed document into small passages while keeping headings and page references.
- **Embeddings:** numerical representations used to compare the meaning of a query and a passage.
- **Similarity / vector search:** rank passages by closeness; similarity is not proof of correctness or legal applicability.
- **Retrieval:** select useful passages, filtered by topic, jurisdiction, and status.
- **Context window:** the limited amount of text the model can consider in one request.
- **Grounding:** ask the model to use the supplied passages and state when they are insufficient.
- **Citations:** map an answer's source IDs back to real retained passages and metadata.
- **Hallucination prevention:** combine narrow scope, verified content, abstention, output checks, and evaluation. RAG reduces risk; it does not eliminate it.

Pipeline: official source → review → extract → chunk → metadata → embeddings → retrieval → source validation → model → answer validation → claim-linked citations.

Store title, authority, canonical URL, publication/effective date when known, retrieval date, jurisdiction, current/historical status, document type, section/page, chunk ID, checksum, and review status. Track unknown dates explicitly. Link availability alone is not source verification.

## 11. Source policy

Prefer current official legislation, notifications, government portals, and legal-services authorities. Check central versus state applicability and effective dates. Never assume an older statute remains operative. Do not invent sections, judgments, citations, eligibility, deadlines, or complaint routes.

Official resource pages inspected for the concept demo on 26 September 2026:

- [National Legal Services Authority](https://nalsa.gov.in/): legal-aid services and state authority links.
- [National Consumer Helpline](https://consumerhelpline.gov.in/public/): consumer grievance information. It is not presented as an employment complaint route.

These are resource links, not a legal corpus or evidence for all sample suggestions. No statute-specific advice is supplied by the demo. A SAMADHAN page fetch timed out during discovery; its content was not used as verified support.

If no suitable source supports an important claim, the live application should state that it could not verify the information and ask for missing facts or recommend qualified help.

## 12. Privacy, safety, and security

Current demo: input remains in page memory, with no app-owned storage or AI requests. Copy and download are user-triggered. Hosting and linked sites have separate data practices. Use fictional information during presentation.

Future requirements:

- Minimize personal information; explain where data is sent before hosted inference.
- Keep keys on the server in environment variables; never in browser files or Git.
- Validate file size, extension, file signature, extracted length, and page limits.
- Use random temporary identifiers, extraction timeouts, and explicit deletion/expiry rules.
- Reject unsupported encrypted or scanned files until appropriate handling exists.
- Treat documents and retrieved passages as untrusted data; instructions inside them cannot override system rules.
- Render user and model text safely without raw HTML; use parameterized database queries.
- Rate-limit requests, cap token budgets, and prevent arbitrary URL fetching.
- Avoid sensitive request bodies in logs and hide stack traces from users.
- Provide safe error states and an explicit model-unavailable state. Never silently substitute a sample answer for live output.
- Escalate urgent, complex, deadline-sensitive, or threatening situations appropriately without promising outcomes.

## 13. Build roadmap and gates

| Phase | Deliverable | Completion gate |
|---|---|---|
| Round 1 | Idea, concept demo, project brief | Team can explain scope and show a three-minute walkthrough |
| 0 | Discovery and model options | Environment and constraints documented |
| 1 | Repository, environment, FastAPI health check | Browser and backend communicate |
| 2 | UI foundation | Accessible navigation, forms, loading/errors, mobile layout |
| 3 | AI adapter | Generic text succeeds; failures and timeouts are handled |
| 4 | Response schema | Invalid model output cannot crash the app |
| 5 | Problem analysis | Multiple reviewed scenarios, missing facts, and uncertainty work |
| 6 | Document processing | Allowed uploads, extraction, explanation and Q&A verified |
| 7 | Draft generation | Missing fields, edits, labels and downloads verified |
| 8 | Small RAG pipeline | Relevant passages are retrieved from the reviewed collection |
| 9 | Source verification | Claims trace to appropriate retained passages |
| 10 | Security | Upload, injection, XSS, abuse, secrets and logging checks |
| 11 | Integration tests | End-to-end flows and failure paths pass |
| 12 | Performance | Measured latency supports a reliable demo |
| 13 | Demo rehearsal | Three-to-five-minute salary/document/draft flow works |
| 14 | Polish | Readable, responsive UI, documentation and pitch ready |
| 15 | Final check | Honest feature status, reproducible setup, clean repository |

Install dependencies only at the relevant milestone. Use one Python virtual environment and pinned tested dependencies. Node is not required for the static demo. Check Python compatibility before adding PDF/AI libraries. Avoid Docker, accounts, a large vector service, or unrelated infrastructure until needed.

## 14. Proposed later repository

```text
nyaya-first-aid/
  frontend/                 # browser interface
  backend/main.py           # FastAPI entry
  backend/routes/           # analysis, documents, drafts
  backend/services/         # AI, retrieval, extraction, citations
  backend/schemas/          # input/output validation
  rag/                      # reviewed source metadata and ingestion
  tests/                    # API, retrieval, safety, integration
  docs/                     # setup, architecture, demo
  .env.example              # variable names; never secrets
  .gitignore
  requirements.txt
  README.md
```

Create folders when needed. Keep the current demo intact as a baseline. Create meaningful commits after tested milestones. No GitHub repository is claimed to exist; configure the team's GitHub destination later.

## 15. Evaluation and acceptance criteria

Evaluate on a small manually reviewed scenario set covering supported, unsupported, ambiguous, state-specific, urgent, and adversarial inputs. Add document samples with missing pages, unclear dates, and attempted prompt injection.

Track structured-output validity, retrieval relevance, citation support, abstention quality, missing-fact detection, document extraction accuracy, response latency, and usability. Publish measured results only after testing; set target thresholds with reviewers rather than inventing accuracy percentages.

Every core flow needs successful, empty, loading, invalid-input, and service-failure behavior. Check keyboard access, visible focus, screen-reader labels, narrow mobile layouts, and text enlargement. Evaluate meaningful legal claims with qualified reviewers before real-world use.

## 16. Round 1 presentation script (approximately 3 minutes)

**0:00–0:30 — Problem**

“Imagine your salary has not arrived for three months. You have messages and payslips, but you do not know which facts matter or where to start. Legal information can be difficult to turn into a practical first step.”

**0:30–0:55 — Solution**

“Nyaya First-Aid helps people describe what happened, understand what may matter, and prepare their next step. It is a legal-awareness tool, not a replacement for a lawyer.”

**0:55–1:40 — Problem walkthrough**

Click **Try the unpaid-salary example**, then **Explore next steps**. Show the issue summary, practical checklist, missing facts, safety note, and official support resource. Say: “Today this is a prepared example. The later version will retrieve verified official passages before producing an explanation.”

**1:40–2:10 — Document walkthrough**

Open **Explain a document** and click **Explain this sample**. Show the payment wording and notice period. Ask the legal-validity question to demonstrate the boundary between reading a clause and determining its enforceability.

**2:10–2:40 — Draft**

Open **Prepare a draft**, fill fictional details, generate the template, edit it, and download it. Explain that no message or complaint is sent.

**2:40–3:00 — Architecture and next round**

“The next stage connects this interface to a Python backend, a tested AI model, and a small reviewed source collection. Our focus is understandable next steps, visible uncertainty, and sources the user can inspect.”

## 17. Judge questions and honest answers

**Is the AI working now?** No. This is an interactive concept prototype with prepared examples and a functional editable template. Live inference is a later milestone.

**Why not just use a chatbot?** We propose a narrower workflow that collects missing facts, separates document wording from legal conclusions, links claims to evidence, and produces a reviewable next step. This proposed benefit still needs user testing.

**How will you prevent wrong advice?** Limit scope, use current official sources, validate citations, show uncertainty, test unsupported questions, and involve qualified reviewers. No model or retrieval system guarantees correctness.

**How can you build it without an API budget?** An existing local runtime is available for benchmarking. We will assess whether it meets quality and latency needs before committing. Round 1 runs without AI costs.

**What about different state laws?** Collect jurisdiction and filter sources accordingly. If coverage is absent, say so. The concept demo does not apply state law.

**What happens to documents?** The demo does not accept uploads. The planned backend will minimize collection, process temporarily, and explain any external inference before sending data.

**Who is responsible for review?** Users review their drafts; qualified legal reviewers are needed to validate the knowledge base and high-stakes guidance. Such review is planned, not claimed to be secured.

## 18. One-hour preparation plan

1. First 10 minutes: agree on the problem, initial audience, and honest demo boundaries.
2. Next 15 minutes: rehearse all three website journeys with fictional data.
3. Next 15 minutes: refine the pitch and answer judge questions.
4. Next 10 minutes: assign speaking roles and verify the demo machine.
5. Final 10 minutes: rehearse once, keep the local HTML as an offline backup, and freeze changes.

## 19. Open decisions after Round 1

Confirm later deadlines, team roles, initial jurisdiction and source scope, access to a legal reviewer, local-model benchmark results, any API budget, and the team's repository destination. Then resume development at the next tested milestone.

Future possibilities include Hindi and regional languages, OCR, voice, broader retrieval, and human-help referrals. They remain outside the immediate MVP until the core experience is reliable.

## 20. Technical references

- [FastAPI documentation](https://fastapi.tiangolo.com/)
- [Python SQLite documentation](https://docs.python.org/3/library/sqlite3.html)

Guiding priorities: reliability over complexity; accuracy over impressive claims; useful UX over gimmicks; trusted sources over model memory.
