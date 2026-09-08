# Project instructions

This workspace currently holds a researched specification. Follow the current user request: research/review authorization does not itself mean install Unreal or implement the game. Once implementation is requested, continue through coherent build, test and repair gates without stopping after each small package.

Read `README.md`, `docs/SPEC.md`, `docs/BOT-CAST.md`, and `docs/DAY1-ENGINEERING.md`; use targeted sections of `docs/ACCEPTANCE.md` and `docs/BUILD-RUNBOOK.md`. During implementation, read and refresh `IMPLEMENTATION-STATE.md` before long operations and handoff. Files and APIs described as proposed do not exist merely because a document names them.

The latest user decisions outrank the specification. Record accepted changes in `DECISIONS.md`, resolve contradictory requirements, and update affected checks. `docs/archive/v0.1-network-plan/` is historical reference only.

## Product constraints

- Offline solo: one human plus exactly `arcl1ght`, `hotlap`, `stitch3r`, `farsight`, `b0bbin`, `ramrod`, `lattice`; eight combatants, 4v4. No generic replacement cast or network scope.
- Both native Apple Silicon Mac and Windows x64. Each needs its own build, rendered runtime and package evidence.
- Original Fort Crossing map inspired by confirmed Western: desert travel, central stone fort, Holdout and CTF.
- Four required vehicles: Hummer-style utility, dune buggy, six-wheel truck, dirtbike. Actual tire damage changes handling and eventually disables propulsion; shared abandonment/destruction/base replacement.
- Human arcade driving feel comes before AI driving quality. Bots use the same controls, physics and legal recovery.
- arcl1ght is an expert grenade thrower with enormous legal range. No homing projectiles, hidden target prediction or runtime perfect-future search. hotlap needs physical off-road/stunt generalization, not canned cinematics or road-only paths.
- Learned policies are optional means; expert skill, recognizable personalities, fair information and human evaluation are mandatory outcomes. No runtime LLM/MCP dependency.

## Engineering and evidence

- Preserve existing and parallel work. Inspect actual source-control status before edits/integration; stage explicit owned paths if committing is authorized.
- Keep text-source tasks disjoint. One OS-backed editor lease covers an entire asynchronous mutation and verified postconditions, including content generation, saves and cooks.
- Use exact engine/toolchain/source/content/profile/model identities, named checks and fresh run directories. Implement ENG-001 through ENG-016 as their prerequisites mature.
- A successful log string cannot override process failure, timeout, wrong/stale report, zero tests or missing native evidence. Required skipped/unavailable cases remain incomplete.
- Separate hermetic, engine, package, rendered and human claims. Physics seeds do not imply byte-identical cross-platform simulation.
- Exclude native MCP/runtime bridge modules from delivered packages. Loopback editor access and a closed listener alone do not prove package exclusion.
- Keep fair bot observations separate from privileged evaluation truth. Freeze and version held-out datasets; tuning on one reopens it.
- Keep checkpoints and one actionable residual ledger. Reverify affected final artifacts after fixes; do not mark mandatory requirements complete through scope reduction.
- Fable review must be an actual independent review when invoked. Record accepted, rejected and unresolved findings; model agreement does not replace runtime evidence.

## External reference boundary

A private reference project was inspected read-only to derive the self-contained day-one contract. This game does not require its runtime, credentials, services, operational hosts, or its historical governance backlog. Do not run copied deployment/host commands from it. Its retired host a retired private LAN host is not a trusted build, validation, mirror, backup or deployment target.

Use original or properly licensed content and record provenance. Keep credentials and private operational data out of recipes, logs and reports. Real-person voice cloning is outside the current scope; original voiced character performances are sufficient.
