# Fork changes: learner profile + certification mode

Prompt text and prompt-assembly config only. No UI, no new features, no new dependencies.

## Ported

| What | Where | Agents |
|---|---|---|
| A. Learner profile: pt-PT with English terms, focus check, blocks, pitch, words over pictures, no ability comments, retrieval warm-up, session close, quiz option craft | `src/agent/prompts/shared/50-learner-profile.md` | prober, teacher, practice |
| C. Explanation style (ASD-STE100): progress line, key point first, short sentences, terminology lock, keep the why | same file, last section | prober, teacher, practice |
| B. Certification mode: exam guide via `research`, 4–5 question probe cap, domain-grouped map with weights, weight × gap order, topic sessions, reuse, exam-style questions, near-exam shift, per-domain table | `src/agent/prompts/shared/55-certification-mode.md` | prober, teacher |
| Practice loads the profile | `src/agent/prompt-segments.ts`: `PRACTICE_SHARED_FRAGMENTS` += `50-learner-profile.md` | practice |
| Pinned segment list | `tests/prompt-overrides.test.ts`: the two new paths | — |

The new files load last, so they win over earlier fragments. Each conflict is named in the text:

- `teacher/20-teaching.md` "Never label turns as plan-node chapters" → replaced by the progress line.
- `shared/40-formatting.md` "mermaid whenever" and `teacher/50-demos.md` "build the instrument" → visuals only on request.
- `practice/00-base.md` "the first reply poses" → one focus-check line first.
- `prober/10-probe-doctrine.md` "a strand that never breaks is not done" → 4–5 question cap in certification mode.

Already covered upstream, so not duplicated: the quiz option craft (`shared/20-question-tools.md`), entering the probe near the top, no praise (`shared/10-voice.md`), reading the knowledge graph before probing, the minimal plan tree.

## Not ported

| Requirement | Why |
|---|---|
| Glossary stored in the persistent store | Agents have no write tool for the graph, and `list_propositions` returns names only. Terms stay consistent within a transcript, not reliably across sessions. |
| Per-domain tracking stored as data | The graph has no domain, weight or score field. The agent rebuilds the table each session and prints it in the transcript. |
| Linking a topic session's nodes to an exam domain | Propositions have no link field. It would need a schema and extraction change. |
| Timed sets | There is no timer in the UI. The agent states a time budget; the learner times themselves. |
| Exam-date awareness | The agent has no clock. In certification mode it asks the days left at session start. |
