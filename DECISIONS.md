# Decisions

Readable prose per [DAY1-ENGINEERING.md](docs/DAY1-ENGINEERING.md) §10: problem, chosen behavior, alternatives, checked evidence, known cost, affected requirements, reopener. Latest user decisions outrank SPEC; entries here outrank SPEC's prior text. Dated 2026-09-08 unless stated.

## D-001 — CTF is vehicle-required; the 90 s lease stays

**Problem.** Fable finding FR-01: with the default kit plus flag, a straight-line sprint between stands takes about 78 s and a run about 94 s against a 90 s lease, so an on-foot carry has no margin for the fort detour or a fight, and no acceptance case measured it.
**Chosen behavior.** Keep the 90 s lease. CTF is designed around vehicle transport: the shipped route must make the fastest legal all-foot capture take at least 90 s while a vehicle-assisted capture (reaching/boarding, dismount, capture hold included) completes before expiry. Pickup, reaching a vehicle and final delivery are on-foot segments; the bot coordinator plans flag carries with a vehicle leg.
**Alternatives.** Lengthen the lease to 1.5× a measured carry time (rejected: dilutes the intended race and keep-away limit). Lease counting only while dropped (not considered further).
**Checked evidence.** Arithmetic from SPEC §6/§10/§11, confirmed by the Codex cross-check; no gameplay measurement yet.
**Known cost.** An all-foot capture must be infeasible within the lease; gameplay validation is pending. The human must be told this in the mode description. Vehicle denial (tire damage, mines on vehicle approaches) becomes the core CTF defense.
**Affected.** SPEC §11, §12; OBJ-07, UX-06.
**Reopener.** UX-06 shows a legal all-foot capture completing before expiry, or the fastest vehicle-assisted carry failing to complete inside the lease on the shipped map, or playtests find vehicle denial makes captures effectively impossible.

## D-002 — Grenade release must clear thrower and vehicle hulls; no timed owner-ignore

**Problem.** FR-02: a drop shares the owner's velocity, so "until the projectile clears the owner or 0.10 s" could never clear; the release point relative to the hulls was unspecified and the owner's vehicle had no immunity at all, so a co-moving drop could contact the thrower or own vehicle at once.
**Chosen behavior.** Every release requires a projectile-volume-clear point outside the thrower's and the release-time vehicle's hulls, reached by the validated sweep; otherwise blocked-release cancel without stock loss. Exemptions end before flight; later contacts with either source are ordinary.
**Alternatives.** Reviewer's first draft ("ignore until non-overlapping for one step, cap 0.5 s"), rejected on Codex's counterexample that a same-step rebound stays exempt.
**Checked evidence.** Document inspection and arithmetic (4.9 cm of fall in 0.10 s); no engine test yet.
**Known cost.** Some cramped positions refuse a drop that a timed exemption would have allowed; the refusal is visible and consistent.
**Affected.** SPEC §7; GRE-02, GRE-07 (its former "owner ignore ends on clearance or 0.10 s" clause is retired).
**Reopener.** GRE-02 shows legitimate drops refused in ordinary standing/seated poses, or spurious self-contacts survive the rule.

## D-003 — Decision-side queries obey the observation boundary; decision step is repeatable

**Problem.** FR-03: BOT-02 requires a same-snapshot counterfactual, but nothing required the decision step to be repeatable with reset state, and EQS/clearance/feasibility queries hit unobserved actors through world collision, leaking hidden enemies without a legal cue.
**Chosen behavior.** Decision-side queries and caches evaluate static geometry plus observation-derived proxies from frozen belief state (position, age, uncertainty), never refreshed from unobserved world state. Perception keeps sensing the real world through legal channels; physical actions resolve against real collision. The decision step must reproduce identical intents in a fixture with all decision-affecting state reset (beliefs, random streams, clock, pending queries, caches, commitments, coordinator state) and legal observations and public match state held fixed; the probe re-runs one live decision tick and compares intents before physical consequences.
**Alternatives.** Reviewer's first draft put perception on a filtered collision channel (rejected: an enemy stepping into view has no belief proxy and could never be discovered). Out-of-tick re-execution (not required; Codex showed a bounded in-tick repeat suffices).
**Checked evidence.** Document inspection; Codex cross-check; no code exists.
**Known cost.** A bot can drive into an unseen enemy its clearance query ignored; that is the intended fairness outcome. Query filtering adds a small runtime cost to be measured in PERF-02.
**Affected.** SPEC §12; BOT-02, BOT-03; ENG-011.
**Reopener.** The repeat-tick harness cannot reach identical intents on the chosen engine systems, or PERF-02 shows the filtered queries exceed the bot CPU budget.

## D-004 — Bounded airborne pitch/roll control for all vehicles

**Problem.** Fable finding FR-14: the air-control decision was deferred past the point where hotlap's controller and the BOT-07/VEH-13 fixtures must be designed.
**Chosen behavior.** Vehicles have a small, documented airborne pitch/roll torque while no wheel touches ground, on the same bindings for human and bot, with magnitude, rate limits and per-rig values in versioned handling data. No lift, no yaw, no arbitrary flight. Qualified on the human handling course at M1 before bot use.
**Alternatives.** No air control (rejected: less recoverable landings, less expressive jumps). Decide after first jump tests (rejected: fixtures need the decision now).
**Checked evidence.** None yet; owner preference recorded 2026-09-08.
**Known cost.** One more assist to tune and to prove identical for human and bot; BOT-07 must record airborne inputs in the trace.
**Affected.** SPEC §9; VEH-02, VEH-13, BOT-07.
**Reopener.** M1 handling evaluation finds the torque makes jumps feel weightless or unreadable, or a bot-only advantage in airborne input rate is measured.

## D-005 — Mac-first execution; Windows is an external blocker; no installation authorized yet

**Problem.** No Windows x64 host exists (Fable U1); available workspace storage is far below the ~150 GiB planning allowance (U2).
**Chosen behavior.** Implementation, when authorized, starts on the Mac. Every Windows cell is registered `UNAVAILABLE` with a `blocked_external` residual owned by the user; dual-platform acceptance cannot close until a Windows host is qualified. No Xcode, Epic launcher or Unreal installation is authorized as of 2026-09-08; the workspace stays documentation-only until the owner says otherwise, and the storage plan (free internal space or an external SSD) is decided then.
**Alternatives.** Buy/borrow a Windows machine now; external SSD now. Both deferred by the owner.
**Checked evidence.** Host inspection 2026-09-08 (macOS 26.x, Apple Silicon M-series, 24 GB, free space far below the allowance, Command Line Tools only, no Unreal).
**Known cost.** Windows-specific defects (toolchain, Chaos behavior, input, performance) surface late; SPEC §17's warning about discovering Mac incompatibility at the end applies in reverse.
**Affected.** ENG-001, ENV-01/03, every N/R/U Windows cell; M0 start.
**Reopener.** A Windows host is identified, or the owner authorizes installation and a storage plan.

## D-006 — Third-person chase camera for driver and bike; first-person for infantry and passengers

**Problem.** FR-13: camera perspective was undecided although M1 human-handling approval depends on it.
**Chosen behavior.** Driver and bike seats: third-person chase camera with FOV and lag exposed in settings. Infantry and passenger seats: first-person, so passengers keep an aimable view.
**Alternatives.** Third-person everywhere; first-person everywhere; player-switchable. Deferred; switchable may return as an M6 option if it does not delay gates.
**Checked evidence.** Owner preference 2026-09-08; no handling evidence yet.
**Known cost.** Two camera families to qualify in UX-04; passenger aim from a chase view is not needed.
**Affected.** SPEC §3, §14; UX-04, M1 handling approval.
**Reopener.** M1 handling evaluation finds jumps or rollovers unreadable in chase view, or passengers cannot aim usefully.

## D-007 — Vehicle-inherited grenade velocity is kept uncapped

**Problem.** FR-06: a buggy passenger's full-charge forward throw reaches roughly 420–450 m of equal-height range (the review first stated 570–640 m by wrongly adding the vehicle speed collinearly to the throw; corrected in Codex pass 5), most of the 640 m map.
**Chosen behavior.** Keep inherited velocity added once with no cap; the extraordinary range is the brief. GRE-06 records the envelope; balance is handled by map design and counterplay.
**Alternatives.** Cap the inherited component at 15 m/s or at sprint speed (8.4 m/s, ~263 m). Rejected by the owner.
**Checked evidence.** Arithmetic from SPEC §7, confirmed by Codex; no gameplay evidence.
**Known cost.** arcl1ght riding with hotlap becomes a cross-map threat; spawn interiors need cover from long lobs (SPEC §10 already forbids unanswerable spawn views).
**Affected.** SPEC §7; GRE-06; map design in §10.
**Reopener.** Playtests find cross-map bombardment dominant or un-counterable.

## D-008 — Public objective state is flag state and Holdout ownership only

**Problem.** FR-09: which objective facts are public (and therefore legal bot-coordinator inputs) was undefined.
**Chosen behavior.** Public: Holdout owner and totals, match timer, each flag's Home/Carried/Dropped state. Not public: dropped-flag location, carrier identity, carrier position. These require observation or a legal teammate report.
**Alternatives.** Publish dropped-flag location; publish carrier identity; publish carrier position. Rejected by the owner.
**Checked evidence.** None; design decision.
**Known cost.** Recovering a dropped flag needs a search; bots must remember where they saw it drop.
**Affected.** SPEC §11, §12, §14 HUD; OBJ-05/06; BOT-03/04.
**Reopener.** Playtests show dropped flags routinely lost until auto-return, making CTF stall.

## D-009 — b0bbin's voice is recorded by the owner or a consenting friend

**Problem.** U9/FR: the required Russian-accented performance needed a production route and authorization.
**Chosen behavior.** The owner or a consenting friend performs the lines with accent direction. Written consent and per-line-bank provenance are recorded. The performer must not be the real person being evoked unless that person agrees. No voice cloning.
**Alternatives.** Licensed synthetic voice; hired actor; decide at M5. Not selected.
**Checked evidence.** None; no recording exists.
**Known cost.** Recording quality and scheduling depend on the owner; a recording session and line bank become an M6 prerequisite with the owner as blocker if absent.
**Affected.** SPEC §14; UX-05; M6.
**Reopener.** No recording is available by M6 planning, in which case a licensed synthetic route is reconsidered.

## D-010 — Fixed-step physics, charge-time resolution, projectile model, BOT-06 gating (FR-04, FR-05, FR-15, FR-31)

**Chosen behavior.** Vehicle and projectile simulation at a fixed physics step (initial 60 Hz, substepping in the lock); identical inputs at 30/60/120 FPS stay within tolerance (VEH-02, GRE-09). Charge duration on fixed-step simulation time from the input samples, resolution stated and identical for human and bot, range spacing recorded in GRE-01. Grenades use the projectile movement component with sweeps and bounce parameters. BOT-06 gates P90 per elevation, bin and OS with at least ten throws per elevation. **Reopener.** Fixed step measurably harms input feel at M1, or the projectile component cannot meet GRE-03/04 bounce behavior.

## D-011 — Mine casings and seated occupants are blast targets (FR-07, FR-21)

**Chosen behavior.** Casings take grenade/mine blast damage under the occlusion rule and are destroyed safely without detonating; no chain. Seated occupants are sampled from their seat pose, so exposed seats take blast damage and cabs occlude. **Reopener.** Playtests show mine nests still uncounterable, or occupant sampling makes trucks a blast-immune box.

## D-012 — Dirtbike rest, mount and fall behavior (FR-08)

**Chosen behavior.** Empty or stationary bikes stand via the balance assist; a fallen bike is mounted from within 2.5 m at ≤ 4 m/s and rights in place only along a collision-free swept path to an upright pose, otherwise entry fails; the overturned-vehicle recovery action does not apply to bikes. **Reopener.** VEH-13 shows mount-righting exploitable as a free escape from a crash.

## D-013 — Objective line of sight, integer owned time, event priority, spawn protection scope (FR-10, FR-12, FR-30, FR-32)

**Chosen behavior.** Objective LOS is eye-to-anchor; walls and vehicles block, pawns and mines do not. Holdout owned time accumulates as integer milliseconds. Interaction-invalidating damage is ordered before interaction completions. Spawn protection restricts only the respawned body; earlier-life devices keep effect and attribution. **Reopener.** A vehicle parked on the terminal proves to be an uncounterable block (vehicles could then be removed from the blocker set).

## D-014 — Ramming attribution and spawn-pad fallback (FR-18, FR-19)

**Chosen behavior.** Ramming damage hits the struck infantry or chassis only; rammer, struck-vehicle occupants, wall crashes and infantry horizontal impacts take none in the arcade preset; a bail-out costs only landing damage. A blocked pad falls back to the next validated anchor within the base after ten retries. **Reopener.** Free bail-outs make vehicle destruction meaningless in playtests, or alternate anchors spawn vehicles in unreachable spots.

## D-015 — Small rule closures (FR-26, FR-28, FR-33, FR-34, FR-35)

**Chosen behavior.** Sniper base damage 67 so an uncharged headshot (101) is lethal. Practice may show a range/impact readout. Bundles deplete one at a time. Inherited velocity is the release point's world velocity including rotation. A collision at exactly 12 s of impact flight is expiry. **Reopener.** Uncharged lethal headshots make farsight dominant in BOT-09 or human sessions.

## D-016 — The dirtbike is an unarmed traversal vehicle (FR-29)

**Chosen behavior.** No passenger, no rider weapons while mounted; speed and exposure are its identity. **Reopener.** Human sessions rate the bike useless; a rider sidearm could then be considered.

## D-017 — Engineering contract closures (FR-16, FR-17, FR-22, FR-23, FR-24)

**Chosen behavior.** Harness positive-control case 11. Classifier rows for SPEC/BOT-CAST/DECISIONS seed changes. A test-only human-slot stand-in exempt from the seven-ID rule, present only in editor/test targets and absent from every packaged build. `editor_lease.py` owns editor launch, restart, liveness and quiescence. Classifier and governance validator stay `pending` until the first commit. **Reopener.** None specific; each closes through its ENG case.

## D-018 — Holdout custody and demonstration sources (FR-11, U10, U11)

**Chosen behavior.** Held-out layouts and target-motion sessions come from a committed parameterized generator; the Fable reviewer seals a commitment binding a secret seed and nonce to the generator revision, parameters, splits and evaluation protocol, revealed and verified only at evaluation; any bound change or reopening needs a fresh seed and commitment, and unrevealed secrets stay outside tuner-accessible artifacts. Expert human demonstrations for BOT-06/BOT-07 are recorded by the owner in the project's own instrumented build; original-game footage (for example from YouTube) is reference material only and cannot serve as a demonstration or baseline in this physics. **Known cost.** The owner's own skill ceiling bounds the reference; the absolute BOT-06 fixture exists so a weak reference cannot lower the bar. **Reopener.** No owner demonstrations exist by M5, or the reviewer role changes hands (a new seed is sealed).
