# Fable review packet

Status: prepared for the user's independent Fable review. **Fable has not reviewed this package.** No Fable model ID, executable or account integration was verified in this session. Use the user's existing Fable workflow and provide these files as local context or attachments.

## What to read

Start with [SPEC.md](SPEC.md), [ACCEPTANCE.md](ACCEPTANCE.md), and [BUILD-RUNBOOK.md](BUILD-RUNBOOK.md). Consult [Unreal automation research](research/UNREAL-AUTOMATION.md) for version/plugin claims, [engine architecture](research/ENGINE-ARCHITECTURE.md) for replication alternatives, [game design research](research/GAME-DESIGN.md) for original-game evidence, and [platform/agent research](research/PLATFORMS-AND-AGENTS.md) for the local machine and workflow limits.

The selected defaults are in SPEC. Research appendices explain alternatives and must not become competing implementation instructions. There is no engine installation or gameplay code in this research deliverable.

## Copy-ready specification review prompt

```text
Independently and adversarially review this research/specification package for
an original spiritual successor to Mobile Forces. The goal is a realistic
chance of one coherent implementation kickoff using Astra, your Fable review,
and the best appropriate Unreal editor automation, followed by build/test/fix
iterations. Do not evaluate it as a promise of a commercial game in one model
response.

Read docs/SPEC.md, docs/ACCEPTANCE.md, docs/BUILD-RUNBOOK.md and the research
appendices. SPEC is the source of selected rules/defaults. Do not implement
the game, install tools, purchase assets or publish anything during review.
Write your review to docs/FABLE-REVIEW-RESULT.md if file access is available;
otherwise return a portable review that can be saved there. Inspect current
primary sources for drift-prone engine/plugin claims. Separate direct source
evidence, source-code inspection, reasoning and unperformed runtime tests.

User requirement: Mac and Windows clients from the first playable unless Mac
is a demonstrated serious hurdle. Known local host is an M-series laptop, 24GB, with only
limited free space at research time and no configured full Xcode/standard Unreal
installation. Separate Windows hardware has not been identified. Engine
candidate is 5.8.2; native Epic MCP is Experimental; exact Mac operation and
Chaos vehicle network quality are untested. Do not claim your review has
validated those runtime capabilities.

Find the smallest changes that materially improve buildability, correct
gameplay, useful vehicles/bots and credible acceptance. Challenge:
1. Whether the 4v4 scope preserves the reference game's identity, and whether
   an 8-player capacity test is appropriately separated from server AI bots.
2. Minimal C++ versus Lyra, ordinary replication/CMC, no initial GAS, native
   Chaos qualification and the cost of the explicitly gated fallback.
3. Driver possession, retained damageable body, passenger weapon ownership,
   dedicated-server hit transforms, exit sweeps, death/disconnect/late joins,
   stolen-vehicle allegiance and stale command generations.
4. Every objective transition, cancellation, possession lease, timer ordering,
   tick overshoot, overtime/tie, reset and dedicated rematch rule. Try to
   construct states with zero/two cores, double score, stranded controllers,
   undroppable core, infinite channels or a match that cannot end.
5. Native Mac/Windows toolchains, matching server/client engine build identity,
   shared rendering baseline, content rigging availability, asset rights and
   actual storage/build-runner prerequisites.
6. Native Unreal MCP versus the researched community options. Inspect actual
   transport, tool discovery, inner dispatcher scope, serial editor mutation,
   recipe determinism, async ambiguity and recovery. Ensure runtime MCP and
   other editor services cannot leak into delivered packages.
7. Acceptance that could pass vacuously or merely mirror implementation:
   zero tests, one-process PIE, eight server bots called eight connections,
   null rendering called performance, screenshots called gameplay proof,
   missing signing/store steps called public release readiness.
8. Whether the agent handoff has enough contracts, file ownership, defaults,
   evidence and recovery steps to continue without repeatedly redesigning.

For each finding give ID, severity, affected file/section, concrete failure
scenario, why existing tests do or do not catch it, proposed correction and
new acceptance evidence. Distinguish P0 implementation blockers, P1 material
correctness/scope risks, P2 improvements and optional future features. Do not
inflate the initial scope with every feature a shooter could eventually need.

Finish with: viable as written / viable after listed corrections / redesign
required, plus the three most important preimplementation experiments. List
unresolved questions separately from recommendations. Never mark an Unreal
runtime test or human gameplay assessment passed if you did not perform it.
```

## Expected review artifact

Use a compact findings table, followed by details for consequential findings:

| Finding | Severity | Requirement / location | Concrete failure | Smallest correction | Required evidence |
|---|---|---|---|---|---|
| F-001 | To be assigned | To be reviewed | Not yet reviewed | Not yet reviewed | Not yet reviewed |

After the review, the integrator records every recommendation as **accepted**, **rejected with reason**, or **unresolved with the decision/evidence needed**. Accepted changes are applied to the canonical spec, acceptance cases and runbook together; changing a research paragraph alone does not update implementation requirements. Remove stale alternative defaults. Do not claim consensus merely because both models produced a similar design.

## Questions for focused review

- Does a two-seat transport plus a bounded shuttle provide enough of the game's identity, or should a larger transport be the next feature after the initial networking gate?
- Is requiring a source-built Windows dedicated server early worth the infrastructure burden? If recommending deferral, state exactly which ownership failures would remain unproven.
- Is current-server hitscan acceptable for a closed 100 ms RTT playtest? If not, define the smallest coherent historical hitbox/reconciliation scope and its cost.
- Is the core's public periodic marker and possession lease sufficient against keep-away while preserving interception and routes?
- Is a blank C++ foundation really smaller than adapting same-version Lyra after accounting for menus, input, settings and bot behavior?
- Are the visual/content requirements achievable with original placeholders and available engine assets without purchasing or manually rigging unavailable content?
- Which performance targets should be acceptance gates versus diagnostics after a first measured baseline?

## Later implementation audit prompt

```text
Review the implemented V0.1 against the final reviewed SPEC and ACCEPTANCE.
Inspect the exact source/content revisions, native package hashes, engine lock
and real evidence manifest. Reproduce high-risk scenarios where runnable.
Prioritize dedicated remote seat/body/weapon authority, stale requests across
lifetimes, core/timer edge cases, native Mac packaging, cooked soft references,
eight real connections, clean-checkout reconstruction and excluded tooling.
Do not change source during a read-only review. Separate reproduced defects,
unverified suspicions, missing evidence and future enhancements. Green ordinary
tests do not refute a failed desired-invariant probe. Record any unavailable
platform or game-run capability and do not invent playtest results.
```

## Internal consistency work already performed

Independent research agents reviewed the draft; these were internal Astra-side checks, not Fable review. Their material findings were incorporated: timestamp-ordered objective resolution, request generations, dedicated rematch flow, retained server-side seated hit primitives, fractional damage rounding, damaged-vehicle return, stolen-vehicle allegiance, wall-safe exit checks, one canonical control-file layout, explicit MCP package exclusion and ambiguous job-status recovery. Fable should still challenge these decisions independently.
