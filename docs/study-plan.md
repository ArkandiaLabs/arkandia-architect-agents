# Study Plan

## Per-phase ritual

Same loop every phase. Don't start coding before step 2 is done.

| Step | What | Output |
|---|---|---|
| 1. Learn | Read the phase guide + linked MS Learn pages. Run the official sample for the concept. | Notes in the issue |
| 2. Frame | Create GitHub issue from `templates/phase-template.md`. Fill "Done when" and "Out of scope" **before** coding. | Issue |
| 3. Build | Branch `feature/vX.Y-<slug>`. Implement only what "Done when" needs. | Commits |
| 4. Prove | Tests / demo script that shows the MAF concept working with **both** providers. | Test or demo |
| 5. Decide | Write the ADR (`templates/ADR-template.md`). If you can't explain the decision, you haven't learned the concept. | `docs/adr/ADR-00N.md` |
| 6. Ship | PR → merge → tag `vX.Y.0` → GitHub Release with notes. | Release |
| 7. Reflect | 5 lines in the Release notes: what surprised you, what you'd do differently. | Release notes |

## Guardrails against "product mode"

The risk: building features instead of learning MAF. Rules:

1. **Goal = concept.** The phase's objective is written as a MAF concept, never as a feature. Feature is the vehicle.
2. **"Done when" is about the concept.** Example: *"Concurrent workflow runs 3 agents in parallel and aggregates via shared state"* — not *"Review Board UI looks good"*.
3. **Explicit "Out of scope".** Every phase lists what you will NOT build. When tempted, add it to the backlog, not the branch.
4. **ADR is the exam.** If the ADR can't explain the trade-off, go back to the docs.
5. **Polish is optional and last.** Only after "Done when" is green, only if energy remains.
6. **Timebox rabbit holes.** Parsing `.sln` with Roslyn, perfect prompts, pretty CLI output — these are not MAF. 1 session max, then simplify.

## Lab authoring rule

Labs are written **just-in-time**, one phase ahead at most:

- Before starting phase N, write `roadmap/vN-*/lab.md` against current MS Learn docs.
- Use the `mslearn` MCP / MS Learn search to verify every API and package name.
- Keep the lab under 15 min of reading. Steps, code, expected output, repo diff.

Reason: core MAF is GA, but integrations (Anthropic, AG-UI, Foundry hosting, MCP) and optional harness features still move. A lab written months ahead will lie to you.

## Both providers, every phase

Each phase must run with OpenAI **and** Anthropic. Not to compare quality — to prove the abstraction holds. If a feature only works with one provider (e.g. hosted tools), document it in the phase notes and the ADR.

## Arkandia flywheel

Every release produces five assets:

| Asset | Where |
|---|---|
| Code | GitHub Release |
| Decision | ADR |
| Collaboration | PR history |
| Visual | Diagram / GIF in `docs/assets` |
| Content | Blog / video / post (optional, Reflect step feeds it) |

By v1.0: a reference implementation, 9 ADRs, a PR trail, and workshop material.
