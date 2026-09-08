# Mobile Forces 2027 — research and implementation specification

**Version 0.2, September 8, 2026.** A detailed research and build package for an original, offline Mobile Forces spiritual successor on **Apple Silicon Mac and Windows x64**. This workspace currently contains planning documents and agent instructions, not a built Unreal game. The earlier network plan is [archived and superseded](docs/archive/v0.1-network-plan/README.md).

The selected game is one human plus seven specific bot characters in 4v4 Holdout and CTF matches on **Fort Crossing**, an original desert/stone-fort arena inspired by the user's confirmed favorite map, **Western**. Arcade driving with excellent physical feel is a core requirement. The required vehicles are a **Hummer-style utility vehicle, dune buggy, six-wheel truck, and dirtbike**. Shoot individual tires, keep limping with degraded grip/steering, lose propulsion after enough failures, abandon or destroy the vehicle, and receive a fresh base replacement.

## About this repository

This is a **specification and research package for an unbuilt game**. No engine is
installed, no code is written, no asset exists, and nothing here has been executed or
playtested. Every number is a stated design hypothesis, and every acceptance case is a
contract to implement rather than a result.

**The seven bot characters are pseudonymous.** They are modelled on real people the
project owner used to play with; their handles have been replaced throughout with
invented ones so that no real person is publicly characterized. Their described skills
and personalities are design targets for fictional characters.

Engineering practices in the day-one contract were adapted from a read-only inspection
of a separate private project, whose name, layout and internal records are deliberately
not reproduced here.

## The cast is fixed

| Character | Required identity |
|---|---|
| `arcl1ght` | Expert grenade thrower with extraordinary long-range accuracy; charged throws, close drops, timed/impact modes and lethal near blasts |
| `hotlap` | Highly skilled, reckless off-road/stunt driver; uses terrain as ramps and delivers passengers to surprising reachable places |
| `stitch3r` | Machine-gun expert with dangerous tracking and sustained-fire technique |
| `farsight` | Excellent sniper and skilled driver; uses mobility to create firing opportunities |
| `b0bbin` | Weakest overall, with occasional earned surprises, Russian-accented voice and jokes |
| `ramrod` | Aggressive driver and machine-gunner who storms in, NFG |
| `lattice` | Prolific laser-mine trapper who turns routes and approaches into dangerous choices |

These are behavior contracts, not seven labels on a generic controller. Specialists use the same controls, ammo, information limits and physical rules as the human. arcl1ght is not a grenade puzzle/trajectory-search feature, and hotlap is not a road-following bot with a canned jump animation.

## Read and use this package

| Document | What it supplies |
|---|---|
| [Full specification](docs/SPEC.md) | Canonical scope, concrete rules, tuning seeds, human handling, tires/seats/lifecycle, map/modes, architecture, native budgets and completion |
| [Seven-character contracts](docs/BOT-CAST.md) | User canon versus proposed habits, recognizable behavior, strengths, mistakes, motor skills and paired evaluation |
| [Day-one engineering](docs/DAY1-ENGINEERING.md) | Twelve useful mechanisms adapted from a read-only inspection of a private reference project, required harness/artifacts and ENG-001–016 |
| [Acceptance matrix](docs/ACCEPTANCE.md) | Named scenarios and the distinction between automated, native, rendered and human evidence |
| [Build runbook and kickoff](docs/BUILD-RUNBOOK.md) | M0–M7 implementation sequence, dependencies, native command templates, recovery, and copy-ready sustained implementation prompt |
| [Fable review packet](docs/FABLE-REVIEW.md) and [result](docs/FABLE-REVIEW-RESULT.md) | Independent adversarial review brief, and the 2026-09-08 specification review with Codex cross-check and dispositions |
| [Agent instructions](AGENTS.md) | Short standing context and rules for future work |
| [Specification review record](docs/SPEC-REVIEW.md) | Accepted corrections, remaining implementation obligations, and documentation-only validation boundary |

The supporting research explains the choices and alternatives:

| Research | Focus |
|---|---|
| [What players loved](docs/research/PLAYER-EXPERIENCE.md) | Contemporary reviews and first-person player comments: playful vehicles, passenger cooperation, spectacular grenades, map flow; sample limitations |
| [Original-game design](docs/research/GAME-DESIGN.md) | Publisher/manual evidence, Western, source facts versus new selected rules |
| [Humanlike AI](docs/research/HUMANLIKE-BOTS.md) | Current research, what human-likeness studies actually demonstrate, small-roster hybrid architecture and honest limits |
| [Motor skills](docs/research/BOT-MOTOR-SKILLS.md) | Expert throws, creative terrain driving, fair forecasts, optional imitation/training, four vehicle rigs and damage-aware control |
| [Unreal automation](docs/research/UNREAL-AUTOMATION.md) | Epic native MCP versus four community bridges, measured qualification, scripts, editor ownership and package exclusion |
| [Engine architecture](docs/research/ENGINE-ARCHITECTURE.md) | Small C++ project, shared action interface, Chaos qualification, local rules, optional model inference and content |
| [Platforms and agents](docs/research/PLATFORMS-AND-AGENTS.md) | Native Mac/Windows setup, Astra/Fable roles, actual evidence boundaries and practical feasibility |

## Recommended technology and realistic expectations

Use **UE5.8.2 provisionally**, qualifying Epic's new **Experimental native Unreal MCP** for editor assistance and retaining versioned Python/C++ asset recipes and native UBT/UAT build/test automation. Community MCP is a fallback for a measured gap, not an automatic dependency. No bot uses MCP or a live LLM during gameplay; delivered packages exclude the editor bridge and native MCP runtime modules. [Epic 5.8.2](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335), [Epic Unreal MCP](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor)

Highly convincing specialists are a realistic research-and-engineering target in this bounded arena. Evidence supports useful imitation and control techniques, but it does not establish universal human indistinguishability or a ready-made seven-character plugin. Build fair perception, character decisions and excellent shared motor control first; use learned policies where measured benefit warrants their training and native-runtime cost. Require full-session human evaluation and blinded same-weapon/same-vehicle identity tests. [Humanlike-bot evidence and limitations](docs/research/HUMANLIKE-BOTS.md)

Mac remains a first-class target. Dropping networking removes substantial work and the dedicated-server/source-build requirement. It does not remove native toolchain, Chaos/bike, graphics or optional inference qualification. The September7 machine inspection found limited free disk and no detected full Xcode/Unreal installation in standard locations; recheck before setup. Epic reports a UE5.8.2 compile issue with Xcode26.4 or newer, so the runbook starts qualification with a supported earlier toolchain. [Epic known issue](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335)

“One shot” means a single kickoff that can continue through staged implementation and repair, resume after interruption, and produce a reviewable complete result. It does not mean one response or no human playtesting. Numerical handling and combat values are explicit starting hypotheses, not original-game measurements. Changes must preserve the user's pillars and rerun affected evidence.

## Current completion boundary

The research, specification, acceptance design, runbook and independent-review prompt are written. A private reference project supplied engineering patterns through read-only inspection; its project was not changed. No engine was installed for this specification, no gameplay was implemented or benchmarked, no model was trained, and no native package or Fable review is represented as passed.

The Fable specification review occurred on 2026-09-08 ([result](docs/FABLE-REVIEW-RESULT.md)); three findings were accepted and applied ([DECISIONS.md](DECISIONS.md)), the rest remain unresolved for the integrator. The next intended action is dispositioning the remaining findings, then the kickoff in [BUILD-RUNBOOK.md](docs/BUILD-RUNBOOK.md) for implementation.
