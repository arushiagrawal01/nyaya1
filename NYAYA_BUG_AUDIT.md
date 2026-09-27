# Nyaya: bugs and requirement gaps

27 September 2026. Read-only review of the current implementation against the adopted v4 prompt and earlier user requests. No application fixes were made during this audit. Reproduction checks used isolated fictional cases without provider calls. This is not an exhaustive browser/device or penetration audit.

## Confirmed by reproduction

1. **State/date cannot be cleared.** After saving Delhi and 2026-09-01, sending empty values retains both. `backend/main.py` uses `body.state or old_state` and the same pattern for dates. The UI lets users clear them, so displayed intent and stored case differ.
2. **False emergency escalation.** “My child is not in danger. I am asking about school fees.” returns `escalated`. `risk_service.py` keyword matching ignores negation and context.
3. **Escalation cannot recover from a false positive.** The follow-up “There is no emergency. This is only a fee receipt question.” remains escalated because `main.py` unconditionally reuses the saved risk flag. A safe clarification/review path is missing; real emergencies must still remain protected.
4. **Over-broad answer filtering removes legitimate content.** A direct `safe_answer` check removes the fact “I received a notice with a deadline”, its deadline question and a notice-period checklist item. It matches legal keywords without distinguishing reported document facts from unsupported legal claims. The helper can leave an empty checklist with readiness true when called with ready=true; the message route has an earlier missing-item check, so this helper result alone does not demonstrate an end-to-end false-ready case.

## Confirmed from code and prior browser checks

5. **Repetitive answers rather than useful tailored guidance.** `answer_guard.py` always replaces summary, practical steps, cautions and professional-help text with fixed strings. This was added after a hallucinated deposit rule; it trades too much utility for a superficial safety boundary. The fact checklist remains generated, but much of every reply is identical.
6. **Hindi is only partially implemented.** `live.js` translates selected labels, leaves many errors/statuses and checklist status words in English, does not save language preference, and does not re-render existing answer headings when switching language. `openCase` resets Archive/Restore to English. Original source summaries remain English. An explicit English choice can also conflict with the backend's Hindi-script heuristic for fixed response sections.
7. **Short, lossy conversation context.** Only user messages among the last 12 messages and a short regenerated known-facts summary reach the LLM. Assistant questions/explanations are omitted, so “the second option” or “why did you say that?” can lose its referent. Long-history reliability has not been established.
8. **Misleading success feedback.** An AI-unavailable result is saved as a normal HTTP-success response. The frontend clears the input and displays “Reply saved” without inspecting the result status. The failure is visible inside the conversation, but the status feedback is inconsistent. No dedicated retry control exists.
9. **Whole interface locks during requests.** `setBusy` disables every button/input/select, including case navigation and language/theme. There is no streaming, cancel action, frontend timeout or request-scoped loading state. This makes a slow response feel like a frozen app.
10. **Document removal clears unrelated conversation too.** The delete route wipes all messages, summary and checklist for that case, not only content derived from that document. This is an explicit broad privacy shortcut, but users lose unrelated case history. The separate in-browser email draft is not cleared when the same case is reopened, so a stale draft can remain after document removal.
11. **Case drafts are incomplete and ephemeral.** `case_draft.js` concatenates up to six user messages, including questions/irrelevant text, into a generic guidance request. It does not produce issue-specific applications. Edits/recipient are lost on case switch/reload, with no unsaved-changes protection. Drafts do not automatically update with new case facts. The UI discloses temporary storage, but the product experience remains unfinished.
12. **Archive behavior is inconsistent.** Chat rejects archived cases, but upload does not check the archive flag. The frontend still presents chat/upload controls without communicating the restriction up front.
13. **Health display overstates readiness.** “Backend connected” is based partly on a nonempty key, not a successful AI/model check. “9 sources” includes three excluded historical records and is not nine reviewed bodies of law. Embedding/index readiness and provider cooldown are not shown.
14. **New-case/session onboarding is rough.** The new-case button is usable before consent/session creation and hits a 401 instead of guiding the user to onboarding. Startup catches case-list failures silently and can make errors resemble an unauthenticated session.
15. **Retrieval has no minimum relevance gate.** Semantic retrieval returns the nearest eligible records even for unrelated topics; reciprocal-rank fusion does not establish applicability. Document retrieval similarly chooses ranked passages without a threshold. A citation ID proves provenance, not that the source supports the conclusion.
16. **Document validation is shallow.** ID checks and numeric substring presence do not verify entailment: an amount can appear on a page without supporting the generated claim. The fallback can show a quotation rather than explain the requested clause, and does so whenever documents exist and no explanation survived. No document viewer/highlight workflow exists.
17. **Host/session security remains a prototype.** Cookie possession controls case access; no login/recovery/cross-device identity exists. Browser cookie expiry is 90 days, but `Store.owner` does not enforce server-side token expiry. Encryption key and database are adjacent by default. These are not claims of a verified exploit; they are production-hardening gaps.

## Missing or partial requirements

18. **Useful legal coverage is far too small.** Six short eligible summaries plus three historical identity records do not fulfill the target of section-level RAG across the downloaded codes and everyday problem areas. Full PDFs were acquired but not comprehensively chunked, applicability-reviewed and indexed. This is partial implementation of a MUST, not merely optional polish.
19. **State-specific law is not loaded.** State/date filtering exists, but no state-specific substantive corpus is present. A selector alone does not deliver the user-requested state-aware legal answers.
20. **Legal transitions are not mapped at section level.** Old codes are excluded rather than implemented as professionally reviewed historical/corresponding provisions with procedural applicability. Version fields are a foundation, not the finished transition system.
21. **Case memory lacks structured timelines, actions and deadlines.** The stored case mainly contains history, summary, checklist, jurisdiction and risk. The fuller v4 case model remains incomplete.
22. **Update simulation is minimal.** It changes a synthetic string and logs a hash/diff. There is no complete demo of changed legal chunks being re-embedded, affected cases identified, or a user-facing change review. The planned simulation itself is only partly fulfilled.
23. **Document explanation is not a full structured report.** No consistently delivered document type/parties/dates/obligations/missing-information view exists. Scans/images/OCR are unsupported; OCR was explicitly stretch, unlike text-PDF extraction and Q&A.
24. **Two product experiences coexist.** The original polished static concept and newer live page are separate. Keeping the old page preserved functionality, but did not integrate all of it into the real application. Feature parity, visual consistency and user-flow acceptance testing were insufficient.
25. **No complete legal/quality evaluation or deployment.** No qualified legal review, broad bilingual/adversarial benchmark, comprehensive mobile/accessibility matrix or public production deployment is complete. Some were declared future work, but must not be confused with a finished product.

## Why this happened

- The build prioritized infrastructure connectivity and a few successful vertical flows over a requirement-by-requirement acceptance checklist.
- Safety patches reacted to observed hallucinations by suppressing output broadly, without enough regression tests for harmless questions or usefulness.
- New UI work was appended incrementally; shared state, localization, draft persistence and error feedback were not designed as one coherent interaction model.
- Automated tests largely check selected backend contracts and mocked outputs. They do not establish legal relevance, multi-turn quality, all browser flows or every original requirement. The live test is a small synthetic sequence, deliberately paced to avoid provider limits.
- Groq limits contribute to availability failures, but do not explain incomplete translations, tiny corpus, fixed answers or missing features. The provider switch was explicitly authorized and is not itself a bug.
- Previous progress wording emphasized passed tests too strongly. “Verified” should have been scoped to named tests and flows, not interpreted as whole-product readiness. This is an engineering and reporting shortfall, not the user's fault or a consequence solely of the pause.

## Repair order

1. Fix harmful state/flow errors: false escalation and correction policy, state/date clearing, preserved facts, draft/history loss and honest failures.
2. Replace broad keyword suppression with claim-specific grounding; build and review a useful bounded legal corpus with relevance/abstention evaluation.
3. Unify the live UI: complete Hindi, consistent case/archive behavior, loading/retry, persistent reviewed drafts and concise answers.
4. Complete the promised case/update demo requirements, then run acceptance tests covering every original MUST and user feature request.
5. Obtain qualified legal review and finish security/deployment work before public use.
