> **Superseded historical plan.** The user subsequently removed networking and selected the seven-character offline game with four vehicles, Fort Crossing, Holdout and CTF. Use the [current project README](../../../README.md) and [v0.2 specification](../../SPEC.md). The original v0.1 text follows for provenance only.

# Mobile Forces 2027 — research and implementation specification

An original combined-arms spiritual successor to Mobile Forces, planned for **native Apple Silicon Mac and Windows x64**. This workspace currently contains research/specification documents only. No game, engine installation, MCP connection or Fable review is claimed.

**Recommendation:** provision and qualify Unreal Engine **5.8.2**, use Epic's native **experimental Unreal MCP** for editor work, and keep C++ gameplay, versioned content recipes, native builds and repeatable tests as the dependable implementation path. Epic has now included MCP in Unreal, so older community bridges are alternatives to evaluate for specific gaps. [Epic MCP documentation](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor)

The first accepted slice is **4v4, one original map, one carried-objective mode, bots, weighted infantry kits and a two-seat transport with passenger shooting**. Include real cross-OS client play and a Windows dedicated-server proof. Expand to larger matches/content after this foundation works.

## Read in this order

| Document | Purpose |
|---|---|
| [Detailed specification](docs/SPEC.md) | Canonical game rules, architecture, map, weapons, vehicles, bots, UI, platforms, budgets and scope |
| [Acceptance matrix](docs/ACCEPTANCE.md) | Named pass/fail scenarios, native process matrix and required evidence |
| [Build runbook and implementation prompt](docs/BUILD-RUNBOOK.md) | G0–G6 sequence, toolchain/content lock, agent roles, commands, recovery and copy-ready kickoff |
| [Fable review packet](docs/FABLE-REVIEW.md) | Copy-ready independent review prompt and disposition process |
| [Unreal automation research](docs/research/UNREAL-AUTOMATION.md) | Epic native MCP versus four community implementations, actual source/maintenance evidence and limitations |
| [Engine architecture research](docs/research/ENGINE-ARCHITECTURE.md) | Lyra/C++, replication, Chaos/seat lifecycle, native platforms and verification alternatives |
| [Original-game/design research](docs/research/GAME-DESIGN.md) | Original manual/store evidence, identity, mode selection, gameplay and content reasoning |
| [Platforms, agents and feasibility](docs/research/PLATFORMS-AND-AGENTS.md) | Observed machine, native build plan, Astra/Fable roles, assets, rights and realistic effort |

Research checked 2026-09-07. Current engine/plugin statements are bounded to the cited sources; exact installed-build compatibility must be tested. Research alternatives do not override selected SPEC defaults.

## Mac is included

Mac is practical for the proposed restrained visual baseline; that is an engineering assessment, not a benchmark. This laptop was observed as an Apple Silicon M-series machine with 24 GB RAM, **limited free space**, no configured full Xcode, and no Unreal installation found at the standard locations. Storage and native toolchains must be prepared before implementation. A Windows build/playtest machine is also required and has not been identified.

“One shot” here means **one explicit kickoff followed by a sustained, checkpointed build/test/fix workflow**. It does not mean a commercial remake in one untested model response. The next step is the user's Fable review using the packet above; implementation can then proceed from the revised brief.
