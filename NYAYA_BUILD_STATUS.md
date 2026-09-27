# Nyaya — build status

Updated 27 September 2026 after the user resumed the saved checkpoint. JPG work was cancelled; this report concerns the website.

## Working and verified

- Local website: http://127.0.0.1:8000/; start with `nyaya-first-aid/START_NYAYA.cmd`.
- Groq `openai/gpt-oss-120b` chat; server-side key; local multilingual MiniLM embeddings; FastAPI backend; Chroma + SQLite FTS5 hybrid retrieval.
- Contextual chat, dynamic fact checklist, readiness limits, risk escalation and source references.
- Encrypted persistent cases and extracted documents, cookie owner isolation, rename/archive/delete, PDF/TXT upload and document Q&A.
- Editable case email from user-entered statements, manual recipient entry, review gate, Unicode-safe Gmail composition and text download. No automatic sending. Gmail URL creation is tested; no email was transmitted.
- English/Hindi response selection; translated main controls and draft controls. The full live interface is not yet completely translated.
- Original Nyaya branding, Lady Justice, themes and separate concept demo retained.

## Verification results

**18 Python tests pass.** Coverage includes ownership, encrypted storage, persistence, case/document deletion, risk bypass, quota/request/date checks, dynamic checklist/readiness, source filtering, PDF/TXT extraction, unsupported claims, document quote fallback, provider retries and outages. One upstream Starlette TestClient deprecation warning remains.

**JavaScript draft checks pass:** user-only statements, review gate, recipient validation, Hindi/Unicode URL encoding and long-message fallback. JavaScript syntax check passes.

**Complete live integration sequence passed:** employment checklist → fictional text document upload → cited document Q&A → Hindi follow-up → reopen saved messages and document. Detailed synthetic results: `nyaya-first-aid/rag/live-test-report.json`. Calls were spaced 60 seconds apart because rapid tests hit Groq's token limit. This is a functional smoke test, not a legal-accuracy benchmark.

**Browser verification:** saved conversation renders, main language controls switch, case-email draft opens with user statements, and opening Gmail without review shows the intended validation message. No real private documents, recipients or outgoing messages were used.

## Important limits

The source index contains nine records, including six short eligible educational summaries and three excluded historical identities. Six official statute PDFs are downloaded, but their full text has not been converted into a reviewed legal knowledge base. State selection does not imply state-specific legal coverage. Current law is not verified live. No lawyer has reviewed the outputs.

Risk checks and output guards reduce errors but do not guarantee correct factual extraction or legal interpretation. A fabricated deposit rule observed in earlier testing was removed by broader output guarding. Unsupported paraphrases fall back to matching original document text. Semantic citation verification and adversarial coverage still need improvement.

The legal-update cycle is labelled synthetic and excluded from answer retrieval. No live crawler, OCR, production accounts, cross-device recovery or public deployment is complete. Local encryption keys live beside the data unless configured externally. Use fictional data for demonstrations. Groq processes relevant excerpts under its own policies.

## Next priorities

1. Select a bounded issue/state scope and ingest precise, versioned official provisions; obtain qualified legal review of at least 3–5 representative outputs.
2. Build a bilingual evaluation set for unseen questions, follow-up consistency, source applicability, hallucination, prompt injection and risk routing.
3. Complete live-interface Hindi translation, mobile/accessibility checks and document extraction hardening/OCR if needed.
4. Expand the current guidance-email template into reviewed application types; improve document explanations without asserting enforceability.
5. Add production authentication/recovery, managed secrets, HTTPS, abuse monitoring, privacy/retention policy, backup and deployment checks.

The earlier automatic age-based deletion change was rejected by approval review and was not implemented. Deletion remains an explicit user action.
