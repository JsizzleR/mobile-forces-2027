# Fable independent review result — v0.2 specification

## Verdict

**Viable after listed corrections.** The v0.2 package is coherent, unusually honest about what is and is not established, and its numeric seeds mostly survive arithmetic. It is not buildable as frozen: three contract defects would let acceptance cases pass while the product they describe is wrong (FR-01, FR-02, FR-03), and one architectural requirement is missing without which the fairness gate cannot be validly executed (FR-03). None requires redesign. All are small, coordinated wording or contract changes plus new fixtures.

A Codex cross-check (Astra, `gpt-6-astra` at `xhigh`, three scoped passes; see below) confirmed the arithmetic and the existence of the three blocker seams but corrected their framing: FR-01 is a missing margin, not an impossibility; FR-02 is an unspecified release point plus absent vehicle immunity, not a guaranteed self-detonation; FR-03's fix must not filter perception, only decision-side queries. One finding (FR-20) is withdrawn on Codex's counterexample and six new findings (FR-30 to FR-35) come from it. The owner has dispositioned the three blockers; see **Dispositions**.

Nothing in this review marks physics, packaging, fair runtime behavior, bot skill, or human enjoyment as passed. No implementation exists; that is expected. Findings that say "checks out" mean the *document* is internally consistent, not that the behavior has been observed.

## Input identity and review provenance

| Item | Value |
|---|---|
| Review date | 2026-09-08 |
| Reviewer | Claude Fable 5.1 (`claude-fable-5-1`) running in Claude Code on the owner's workstation; no Unreal MCP, Astra, or editor connection was used or available |
| Access | Read-only on all project and the reference project files. Only this file was written. Nothing installed, purchased, published, or contacted |
| Repository | No Git repository exists in `mobile-forces2027`; identity is by SHA-256 below |
| the reference project reference | `the reference project checkout` HEAD is now a later revision, not the revision recorded in DAY1 §1. All six cited files exist; line references may have drifted |

Input manifest (SHA-256, active documents as read):

```text
9aeb5410f7f43f755048a74aaf88c40cd8ad44b334a8d2bb6666ebc7fa20466d  docs/SPEC.md
ac915e746846db12ad4ce007b5a561a1bd5f264bcb47d040c0d8c6e2da9d5b2e  docs/ACCEPTANCE.md
509b6c6053067c453ccd519a004226154436a00356dd6c40cf1fb2683b7a2f25  docs/DAY1-ENGINEERING.md
951356d2aafcf0d254018563ebe667cff3d8cc827393333e2166142486f82982  docs/BUILD-RUNBOOK.md
6b25909d5d79d7a5a8a87f7579f4987c2e90a0d42641a0be591df2d0a34e8eeb  docs/research/BOT-MOTOR-SKILLS.md
4cbd50c03ad27315074104129f990d3fa26d9a0a40f91655dbe7455f4333d17c  DECISIONS.md
```

Evidence categories used below: **D** document inspection; **P** external primary source fetched today; **X** executed check (script run by the reviewer); **C** code inspection (the reference project only; no game code exists); **J** judgment or arithmetic derived from the documents' own numbers.

### Executed checks (X)

- Independent link/fence/ID scan over the 16 active Markdown files: 105 relative links resolve; 71 unique external links; no unbalanced fences; all seven handles spelled canonically everywhere; ACCEPTANCE defines exactly 90 unique product IDs (VEH 15, BOT 14, OBJ 10, GRE 9, INF 8, MIN 8, ENV 6, LIF 6, UX 6, PERF 4, REL 4) and 16 ENG IDs; no product ID is referenced anywhere that ACCEPTANCE does not define. This agrees with SPEC-REVIEW's count of 90 + 16. SPEC-REVIEW reports 159 references because it counts differently; the substance matches.
- Read-only host refresh (supersedes the "September 7" observations in SPEC §16 and PLATFORMS §2): macOS 26.x, an Apple Silicon M-series laptop, 12 cores, 24 GB RAM, free workspace storage far below the 150 GiB allowance, `xcode-select` points at Command Line Tools, no `/Applications/Xcode*.app`, no `~/Epic Games`, no Unreal in `/Applications`, Python 3.9.6, git 2.50.1. No Windows host is identifiable from this machine.

### Primary-source verification (P)

| Claim in package | Verified today | Result |
|---|---|---|
| UE 5.8.2 hotfix exists; known issue UE-377426 with Xcode 26.4 or newer; recommended 26.1 | Epic forum announcement, dated 2026-08-25 | **Confirmed** verbatim |
| Mac table: macOS 14.5 min, Xcode 26.0 min / 26.1.1 recommended, 26.4 incompatible, 16/32 GB RAM | Epic macOS requirements page for 5.8 | **Confirmed** |
| Windows: VS 2022 17.14+ or VS 2026 18.0+, MSVC 14.50 recommended, SDK 10.0.26100+ | Epic Visual Studio setup page for 5.8 | **Confirmed** (page also lists MSVC 14.38 and SDK 10.0.22621 minimums) |
| Native Unreal MCP: plugin `ModelContextProtocol`, Experimental, loopback `127.0.0.1:8000/mcp`, `list_toolsets`/`describe_toolset`/`call_tool`, serial game-thread execution, **runtime modules that cooked/shipping builds can host** | Epic Unreal MCP page | **Confirmed**; the package-exclusion obligation in SPEC §15 and ENV-06 is justified by Epic's own wording |
| Astra is `gpt-6-astra` | OpenAI latest-model guide | **Confirmed** |

The research documents' mutable claims are therefore current as of today. They remain claims about the engine, not about this project's compatibility; ENV-01/ENG-001 still have to measure the installed release.

## Findings table

Severity: **B** blocker (a gate would freeze a wrong contract or cannot be validly executed); **M** material risk; **I** improvement; **O** optional future idea.

| ID | Sev | Area | Affected requirement / section | Evidence | One-line defect |
|---|---|---|---|---|---|
| FR-01 | B | 5 | SPEC §11 CTF lease; §10 base positions; §6 burden; OBJ-07/08; UX-06 | J, Codex | 90 s lease from theft leaves no margin for an on-foot carry (78 s straight-line sprint, 94 s at run speed, by SPEC's numbers); any detour or stop loses the flag, and no case measures it. **Owner decision: CTF is vehicle-required; lease stays 90 s** |
| FR-02 | B | 3 | SPEC §7 owner-ignore 0.10 s and Drop; GRE-02, GRE-07; BOT-CAST §2 | J, Codex | Drop shares the owner's velocity, so "clears the owner" cannot happen within 0.10 s; the spec fixes neither the release point relative to the hulls nor any immunity for the owner's vehicle, so a co-moving impact drop can detonate on the thrower or own vehicle. **Owner: accepted** |
| FR-03 | B | 6, 8 | SPEC §12 counterfactual probe; BOT-02/03; ENG-011; BOT-MOTOR §2 EQS | D, J, Codex | No requirement that a bot's decision step be repeatable with reset belief/RNG/clock/pending-query state, and no rule that decision-side feasibility queries and caches obey the observation boundary; live EQS/clearance traces hit hidden actors. Without both, BOT-02 cannot be run as a controlled comparison. **Owner: accepted after Codex check** |
| FR-04 | M | 1, 6 | SPEC §9, §15; VEH-02; GRE-09 | D, J | Frame-rate-independent vehicle simulation is not required and not tested; GRE-09 covers grenades at 30/60/120 FPS but no vehicle case does, so jump distance can differ by frame rate and OS |
| FR-05 | M | 3, 8 | SPEC §7 charge curve; §12 motor rates; BOT-06 tolerances | J, Codex | Charge duration sampled at motor/frame rate spaces reachable ranges 1.8–5.7 m apart at 30–75 m (worst error half that); timing alone misses BOT-06's ≤1 m target at 30 Hz and at 75 m, and the spec does not define charge-time resolution or its human/bot parity |
| FR-06 | M | 3, 5 | SPEC §7 inherited velocity; GRE-06 | J, Codex | Full-charge forward throw from a 30–34 m/s buggy passenger reaches about 420–450 m of equal-height range at an optimized elevation, most of the 640 m map; intent was unstated. (The review first said 573–636 m by adding the vehicle speed collinearly to the throw; corrected in Codex pass 5.) **Owner: kept uncapped (D-007)** |
| FR-07 | M | 5, 4 | SPEC §8 mine casing; §7 explosion targets; MIN-06, GRE-08 | D | Whether grenade or mine blasts damage a mine casing is unspecified; if not, a 16-beam terminal nest has no counter except shooting each casing, and arcl1ght has no role against lattice |
| FR-08 | M | 1 | SPEC §9 bike, recovery action; VEH-11, VEH-13 | D | Dirtbike at rest, empty, fallen, and during mount/dismount is unspecified; the only stated path for a fallen bike is the 5 s + 3 s overturned-vehicle recovery |
| FR-09 | M | 5, 6 | SPEC §11 flag visibility; §12 coordinator "public match state"; HUD §14 | D | Which objective facts are public (dropped-flag location, carrier identity) is undefined, so bots may plan from information the human HUD does not show |
| FR-10 | M | 5 | SPEC §11 Holdout/CTF "line of sight" | D, Codex | LOS is undefined against dynamic actors; a pawn-blocking reading would defeat "enemy presence alone does not stop a claim", so the blocker set must be stated (Codex: ambiguity, not a contradiction) |
| FR-11 | M | 6, 8 | SPEC §12 holdouts; ACCEPTANCE §9; ENG-013 | D, J | "Held-out" layouts are generated by the same agent that tunes; custody and sealing are unspecified, so the holdout is procedural only |
| FR-12 | M | 5 | SPEC §11 Holdout "exact equality draws" with fractional accumulation; OBJ-10 | J | Floating-point owned time makes "exact equality" a rounding accident; an integer time unit must be declared |
| FR-13 | M | 1 | SPEC §9, §14 camera; UX-04; M1 exit criteria | D | Driver/passenger/bike camera perspective is undecided although M1 human-handling approval depends on it |
| FR-14 | M | 1, 2 | SPEC §9 air control "if added"; BOT-07; VEH-13 | D | Air-control decision is deferred past the point where hotlap's controller and BOT-07's fixture must be designed |
| FR-15 | M | 7 | SPEC §7 "swept collision"; BOT-MOTOR §5 projectile model | D | Projectile simulation model (ProjectileMovementComponent vs. rigid body) is undecided; every grenade number is downstream |
| FR-16 | I | 8 | DAY1 §5 harness cases; ENG-003 | D | The ten harness cases are all negative; no positive-control case proves a correct child yields PASS with correct counts |
| FR-17 | I | 8 | DAY1 §7 classifier table; ENG-009 | D | SPEC.md and BOT-CAST.md are not named in any row; a numeric-seed change classifies as "ordinary Markdown" |
| FR-18 | I | 5 | SPEC §11 ramming; §6 fall damage; §9 bail | D | Rammer self-damage, rammed-occupant damage, wall crashes, and horizontal bail-out impacts are all unspecified; a 34 m/s bail into a wall is free |
| FR-19 | I | 5 | SPEC §9 spawn pad "two-second retry" | D | Indefinite retry allows permanent starvation of one enemy family by parking on the pad |
| FR-20 | — | 2 | SPEC §9 return timers | J, Codex | **Withdrawn.** Codex counterexample: an enemy lingering near the disabled wreck until t = 30 s delays its return to 45 s, while destruction at t = 0 replaces at 20 s; the spec never promises destruction is fastest |
| FR-21 | I | 2, 3 | SPEC §7 blast targets; GRE-08, MIN-08, VEH-08 | D | Seated occupants as blast targets (exposed buggy seat vs. truck cab) are implied but never stated or tested |
| FR-22 | I | 4, 8 | ACCEPTANCE ENV-04, BOT-13, PERF-04 | D | Automated full matches and soaks need a human-slot stand-in controller; "no generic filler" must exempt it explicitly and exclude it from the packaged roster |
| FR-23 | I | 7, 8 | DAY1 §6; ENG-002, ENG-007 | D | Who launches, restarts, and confirms quiescence of the editor process is unassigned |
| FR-24 | I | 8 | DAY1 §2 file list; RUNBOOK §11 ENG-009 at M0 | J | Classifier and governance validator are M0 deliverables before a repository exists to classify |
| FR-25 | I | 1, 4 | SPEC §9 vs BOT-MOTOR §3, §11 | D | Seed conflicts: failed-tire grip 20–30% vs 0.2; utility rate 2–5 Hz vs 4–8 Hz; ramming applies to whom |
| FR-26 | I | 3 | SPEC §6 sniper 65 base | J | Uncharged sniper headshot is 98 damage, leaving 2 HP; confirm intent |
| FR-27 | I | 8 | DAY1 §1 the reference project revision | X | Recorded the reference project's HEAD differs from current; line references may drift |
| FR-28 | O | 3 | SPEC §5 Practice | J | A Practice-only range readout would make 125–175 m human throws learnable; otherwise the "practiced human" parity claim rests on unaided distance estimation |
| FR-29 | O | 1 | SPEC §9 bike | J | Driver weapons suppressed plus no passenger makes the bike a defenseless pure-traversal vehicle; decide whether that is the intended role |
| FR-30 | M | 5 | SPEC §11 shared rules, same-time priority; OBJ-01/10 | Codex | Nonlethal damage at exactly the claim's completion timestamp is unordered: the priority list places death before completions but not interrupting damage; damage-first cancels, completion-first awards ownership |
| FR-31 | M | 3, 8 | ACCEPTANCE §8 BOT-06 absolute fixture | Codex | "20 throws per bin spanning two release elevations" is satisfied by 19 throws from one elevation and one 20 m miss from the other, with pooled P90 = 0 m; one elevation can fail completely |
| FR-32 | I | 5 | SPEC §11 spawn protection; §8 mine persistence | Codex | Whether "protected players cannot deal damage" suppresses a mine the player planted in a previous life is unspecified |
| FR-33 | I | 3 | SPEC §6 bundle mass rule | Codex | With two grenade bundles, which bundle supplies each charge is unspecified, so freeing 2 kg after four throws depends on an unstated order |
| FR-34 | I | 3 | SPEC §7 inherited platform velocity | Codex | The velocity reference point is unspecified: a passenger 2 m from the axis of a vehicle rotating at 1 rad/s has 2 m/s tangential velocity while the vehicle's center has none |
| FR-35 | I | 3 | SPEC §7 impact lifetime | Codex | A first collision at exactly 12 s after release needs a stated precedence between detonation and harmless expiry |

Checked and **not** found defective (D, J): MG 5-hit kill, pistol head 38, sniper charged 110, damage plateaus, burden 13 kg → 630 cm/s, grenade 110/55/0 at 1/3/5 m, mine 120/60/0 at 1/2.5/4 m, fall 0/50/100 at 10/16/22 m/s, the 90 + 16 ID inventory, the same-time event priority list, the flag conservation states, spawn-protection versus mine-exclusion radii (5 m ends protection; 10 m exclusion), and the 180–220 m envelope (see FR-05 detail: 45 m/s at 45° on level ground gives 206.4 m, flight 6.49 s, inside the 12 s impact lifetime; a 150 m low-arc throw flies 3.63 s, longer than the 3.5 s timed fuse, which is exactly why SPEC says to use impact mode).

## Consequential findings in detail

### FR-01 — CTF lease versus map scale (Blocker; owner decision recorded)

**Scenario.** Bases at x = −270 m and +270 m (SPEC §10). Carrier speed with flag: 700 cm/s × clamp(1 − 0.02·(17 − 8)) = 574 cm/s at base run speed, 689 cm/s sprinting (SPEC §6; sprint has no stamina limit). Straight-line base-to-base is 540 m; the 90 × 70 m fort sits on that line, so a real route is longer, though the layout seeds do not establish by how much.

| Carrier | 540 m | 600 m |
|---|---|---|
| Base run speed with flag | 94 s | 105 s |
| Sprinting with flag, no stop | 78 s | 87 s |

The lease is 90 s from theft (SPEC §11) and the capture hold adds 0.3 s. A straight-line sprint fits with about 11 s to spare; a carrier at run speed, or a sprinter who takes the covered route, stops to fight, or detours around the fort, loses the flag to expiry. The original wording of this finding ("on-foot capture is impossible") was overstated, as Codex pointed out; the defect is the absence of any margin and of any case that measures it. OBJ-07 tests that expiry returns Home and would pass either way.

**Owner decision (2026-09-08).** CTF is vehicle-required by design; the 90 s lease stays. On-foot segments (pickup, walking to a vehicle, final delivery) remain legal; a whole-route on-foot carry is not the intended play.

**Applied wording (SPEC §11, CTF; applied 2026-09-08 as D-001).**

> The 90-second lease is deliberately shorter than an on-foot base-to-base carry: CTF is designed around vehicle transport, and a carrier who runs the whole route is expected to lose the flag to expiry. Pickup, reaching a vehicle, and final delivery are on-foot segments.

Add to UX-06 (Codex's tighter form): "With lease expiry disabled, measure theft-to-completed-capture time in each direction with the default kit, sprinting, on the documented shortest route and by the fastest vehicle, including the capture hold; record both against the lease." Add to OBJ-07: "A vehicle carry on the documented route completes a capture before lease expiry in the positive control." SPEC §12's objective coordinator must plan flag carries with a vehicle leg, not forbid on-foot segments.

**New evidence.** UX-06 carry-time measurements (E), OBJ-07 vehicle positive control (E/N).

### FR-02 — Drop, the 0.10 s owner-ignore, and the owner's vehicle (Blocker; accepted)

**Scenario A.** Player running at 7 m/s drops an impact-mode grenade during a retreat (arcl1ght's signature move, BOT-CAST §2). Drop "applies negligible intentional throw velocity while retaining platform velocity" (SPEC §7), so the grenade keeps 7 m/s alongside the runner and falls only 4.9 cm in 0.10 s (from ~1.2 m, ground contact at 0.49 s). Owner overlap is ignored "until the projectile clears the owner or 0.10 s elapses, whichever occurs first". Whether this ends in self-detonation depends on where the release point sits relative to the capsule, and the spec fixes that nowhere; the hand-to-release sweep and blocked-release policy constrain obstruction but do not require a release outside the owner's hull. It is also unspecified whether restoring collision during an existing overlap counts as an eligible impact.

**Scenario B.** Buggy passenger at 30 m/s drops a grenade. It inherits 30 m/s and stays beside the vehicle for 0.49 s. Codex's sharper observation: the spec grants the owner's **vehicle no immunity at all**, so a release point touching the cabin contacts it immediately in either mode.

**Why the gate misses it.** GRE-02 and GRE-07 test drop states and owner-overlap timing separately; neither constructs a co-moving release, and neither mentions the vehicle.

**Applied wording (SPEC §7; applied 2026-09-08 as D-002), Codex's form, replacing my earlier "non-overlapping for one step plus 0.5 s cap" (which a same-step rebound could defeat).**

> Drop requires a projectile-volume-clear release point outside the thrower's and the release-time vehicle's collision hulls, reached through the validated release sweep; otherwise cancel visibly without stock loss. Source collision exemptions end before flight begins. Subsequent physical contacts with either source are eligible normally.

Add to GRE-02: "Running drop and passenger drop at top speed in both modes produce no spurious initial contact with the owner or own vehicle; record the release offset and the resulting landing point." Do not promise "never contacts the owner": a dropped timed grenade legitimately lands near a stationary thrower, and that is blast damage, not a proximity fuse.

**New evidence.** GRE-02 co-moving drop subcases with recorded release offsets (E on both OS).

### FR-03 — The fairness counterfactual has no executable contract (Blocker; accepted after Codex check)

**Scenario.** BOT-02 requires "same snapshot/seed, change wholly unobserved target state without changing legal cues; observations/actions remain unchanged". Two gaps:

1. **Repeatable decision step.** A controlled comparison needs the bot's decision step to be run twice with belief memory, random stream, clocks, and pending queries reset identically, differing only in hidden world state. Nothing in SPEC §12 or BOT-02 requires the decision path to be resettable and repeatable; SPEC requires immutable snapshots only for async planners. Codex's correction, adopted: this does not require running decisions *outside* the live tick; a bounded engine fixture that re-runs one live decision tick with reset state is sufficient, and uncontrolled scheduling or naturally divergent worlds are what make the comparison invalid.
2. **Decision-side queries.** EQS trace tests, grenade release-obstruction pre-checks, vehicle clearance forecasts, and mine beam previews query world collision, which includes unobserved pawns and vehicles. A hidden enemy in the evaluated corridor flips "clear/blocked" with no legal cue. Codex's correction, adopted: **perception must keep sensing the real world** (an enemy stepping into a doorway has no belief proxy yet and must be discoverable), and physical actions resolve against real collision (bumping into an unseen enemy is legitimate). The leak is a *speculative* decision-side query or its cache revealing an actor the bot has not observed. My earlier patch put perception on a filtered channel; that was wrong.

**Why the gate misses it.** ENG-011 requires a visible positive and occluded negative, but a leak through a feasibility query produces no observation and no obvious action change in ordinary runs; only a deliberately placed hidden blocker exposes it.

**Applied wording (SPEC §12; applied 2026-09-08 as D-003).**

> All decision-side queries, including EQS tests, release/clearance/feasibility checks, forecasts and their caches, obey the observation boundary: they evaluate static geometry plus actors present in the bot's belief set, never the live set of unobserved dynamic actors. Perception senses the real world through legal sight/hearing/damage channels; physical actions resolve against real collision. The decision step must be repeatable in a fixture: given reset belief memory, random stream, clock and pending-query state, it produces identical intents.

Add to BOT-03: "Place an unobserved enemy inside the bot's evaluated throw corridor / EQS trace path / vehicle clearance volume; intents and EQS scores are identical to the empty-corridor control; a *visible* blocker in the same position changes them." Add to ENG-011: "The probe re-runs the decision step with reset state, differing only in hidden world state; identical intents required; compare intents before physical consequences."

**New evidence.** BOT-02/03 repeat-tick harness (E), hidden-blocker case with visible positive control (E, both OS).

### FR-04 — Frame-rate-independent vehicles (Material)

SPEC §15 says to record timestep/substepping and not to expect bit-identical replay, but never requires that vehicle motion be independent of rendered frame rate. Chaos vehicle simulation under variable tick produces different jump distances at 30 vs 120 FPS unless fixed-step/async physics is configured. PLAYER-EXPERIENCE §6 already warns about the original's speed varying with hardware. **Patch:** SPEC §9: "Vehicle and projectile simulation run at a fixed physics step (initial 60 Hz, substepping recorded in `PROJECT-LOCK.json`); identical input scripts at 30/60/120 rendered FPS must produce takeoff speed, jump distance, and landing pose within declared tolerances." Add VEH-02 subcase mirroring GRE-09. Blocks: VEH-02, BOT-07, cross-OS comparisons in BOT-14.

### FR-05 — Charge quantization versus BOT-06 tolerances (Material)

Release speed rises 6 → 45 m/s over 1.25 s: 31.2 m/s per second of hold. At 45° a 30 m throw needs a 358 ms hold and 75 m needs 677 ms; range sensitivity to hold time is 109 m/s and 173 m/s. Release sampled at 30 Hz therefore spaces reachable ranges 3.6 m apart at 30 m and 5.7 m at 75 m; at 60 Hz, 1.8 m and 2.9 m (Codex verified). The worst-case error from choosing the nearest quantum is half the spacing, so timing alone can meet BOT-06's ≤1 m target at 30 m only at ≥60 Hz and cannot meet it at 75 m; pitch modulation (7.1 and 6.7 m per degree at full charge, giving 0.14–0.15° budgets) closes the gap. The same quantization applies to the human at their frame rate. Codex's caveat, adopted: timestamping frame-sampled events does not by itself equalize human and bot quantization or give sub-frame precision. **Patch (SPEC §7):** "Charge duration is computed on fixed-step simulation time from the input sample that carried the press and the release; state the resolution, apply it identically to human and bot inputs, and record the effective range spacing per frame rate in GRE-01." **Patch (ACCEPTANCE §8):** BOT-06 placement tolerances presume angle-plus-charge control at the measured resolution; freeze after GRE-01. Blocks: BOT-06 validity.

### FR-06 — Passenger throws from a moving vehicle (Material)

Inherited velocity added once (SPEC §7) gives a buggy passenger at 30–34 m/s about 420–450 m of equal-height range at an optimized 56–57° release, flight 7.6–7.7 s (inside the 12 s impact lifetime); a sprinting thrower reaches about 263 m. **Correction:** this finding originally stated 573–636 m and 291 m by adding the platform speed collinearly to the 45 m/s throw; Codex pass 5 caught the error and the corrected optimum is used everywhere now. The map is 640 × 420 m. GRE-06 tests opposing directions but states no expected envelope. Either this is the intended "extraordinary" ceiling or it is a balance defect that lets arcl1ght riding with hotlap bombard a base from the far settlement. **Patch:** add the derived table to SPEC §7 and record the decision (accept, or cap the inherited component for thrown grenades at e.g. 15 m/s) in `DECISIONS.md`; GRE-06 gains expected ranges for foot-sprint and buggy-top-speed cases.

### FR-07 — Mines as blast targets (Material)

Casing health is 20 (SPEC §8), destructible by shooting. Explosion targets are listed as infantry, chassis, tires (SPEC §7, §8); mines are not listed, and "no mine-to-mine chain" covers only mine explosions. If grenade blasts do not destroy casings, a lattice nest of up to 16 beams around the Holdout terminal is counterable only by shooting each casing while under fire, and arcl1ght's obvious counter-role disappears. **Patch (SPEC §7 and §8):** "Mine casings are blast targets with the same occlusion rule; a casing reduced to zero by a grenade or mine blast is destroyed safely (no chain). The blast from a destroyed casing is not triggered by its destruction." Add MIN-06 subcase "grenade at 2 m destroys casing without chain; casing behind full cover survives". Also decide whether the terminal's immediate surroundings receive a placement exclusion; GAME-DESIGN §6's "no unanswerable position" needs one of the two.

### FR-08 — Dirtbike rest, mount, and fall states (Material)

A two-contact rig with a bounded balance assist is allowed (SPEC §9). Unspecified: whether an empty bike stands, falls, or is held upright; whether a fallen bike can be mounted; what "overturned" means for a bike. As written, a bike that falls at a stop can only be righted through the 5 s stationary wait plus 3 s hold, which would make it unusable in combat. **Patch (SPEC §9):** "Empty or stationary bikes are held upright by the documented assist (kickstand behavior); a bike on its side may be mounted directly from within 2.5 m, mounting rights it in place through the shared entry rule; the overturned-vehicle recovery does not apply to bikes." VEH-13 gains "fall at stop, mount fallen bike, dismount while moving". Blocks: VEH-13, M1 bike feasibility.

### FR-09 / FR-10 — Public objective facts and line of sight (Material)

Define once in SPEC §11 the public match state visible to the human HUD and to bot coordinators: Holdout owner and totals; each flag's Home/Carried/Dropped state; dropped-flag world location (decide yes/no); carrier identity (decide yes/no). Bots may use exactly that set. Define LOS for claims and flag interactions explicitly, for example "unobstructed trace from the player's eye point to the objective anchor; walls and vehicles block it, pawns and mines do not" (Codex notes that ignoring all dynamic actors is a further design choice; whether vehicles block is the decision to record). OBJ-01 gains "enemy standing on the terminal does not interrupt a claim; a wall does".

### FR-11 — Holdout custody (Material)

ACCEPTANCE §9 says holdouts are frozen and reopened on tuning, but the same integrator generates and tunes. **Patch (ACCEPTANCE §9, ENG-013):** "Held-out terrain layouts and target-motion sessions are produced by a parameterized generator committed before tuning; the generator seed for acceptance layouts is chosen by the user or reviewer, committed as a hash, and revealed only at evaluation. Reopening a holdout requires a new sealed seed." This makes ENG-013 checkable without a second team.

### FR-12 — Holdout draws (Material)

Accumulate owned time as integer simulation milliseconds (or fixed steps) and define "exact equality" on that integer. OBJ-10 tie sweep then has a defined outcome.

### FR-13 / FR-14 / FR-15 — Decisions blocking M1–M2 fixtures (Material)

Three design decisions are deferred past the milestones whose fixtures depend on them. Each needs a `DECISIONS.md` entry before its fixture is written:

| Decision | Blocks | Recommendation |
|---|---|---|
| Driver/passenger/bike camera perspective and lag | M1 handling approval, UX-04 | Third-person chase for driver and bike, first-person for infantry and passengers; expose FOV and lag; approve at M1 with the handling course |
| Airborne pitch/roll input: yes or no, and torque limits | BOT-07 fixture, VEH-13, hotlap controller | Decide at M1 after the first buggy jump tests; SPEC already says it must be shared and documented |
| Grenade projectile model (ProjectileMovementComponent with bounce vs. Chaos rigid body) | GRE-01–09 numbers, BOT-06 | Pin PMC with swept collision first; requalify all GRE numbers if changed |

### FR-16 / FR-17 / FR-23 / FR-24 — Engineering contract (Improvement)

- ENG-003: add case 0, "a well-formed child with N named cases exits zero and yields PASS with count N", so a broken harness that rejects everything cannot pass all ten negative cases vacuously.
- ENG-009: add rows "SPEC.md, BOT-CAST.md numeric seeds or rules → affected product IDs via a maintained requirement-to-check map; unmapped seed change selects broad gameplay validation".
- ENG-002/007: assign editor process ownership to `editor_lease.py` (launch, restart, liveness, quiescence check) so "restart" and "establish quiescence" have an owner.
- Order: defer `classify_change.py` and `validate_governance.py` closure to the first commit with a base; at M0 keep doctor, build, run_scenarios, check, lease, checkpoint. Keep ENG-009/010 registered as pending, not skipped.

### FR-30 — Same-time nonlethal damage versus completion (Material, from Codex)

Claim begins at t = 0 and completes at t = 1; a nonlethal bullet hits at exactly t = 1. SPEC §11 says damage interrupts, but the same-time priority list orders death and destruction before completions and says nothing about nonlethal interrupting damage. Damage-first cancels the claim; completion-first awards ownership. **Patch (SPEC §11 shared rules):** insert "then damage or other interaction-invalidating events" between "death/destruction/world-bound invalidation" and "interaction completions". OBJ-10 gains this boundary.

### FR-31 — BOT-06 elevation coverage (Material, from Codex)

"At least 20 throws in each range bin per OS, spanning two release elevations" is satisfied by 19 throws from elevation A and one 20 m miss from elevation B, with pooled P90 = 0 m. **Patch (ACCEPTANCE §8):** "at least ten throws per elevation per bin per OS; P90 gated separately per elevation, bin and OS; percentile estimator declared."

### FR-32 to FR-35 — Improvements from Codex

- **FR-32.** State whether spawn protection suppresses damage from a mine the same player planted in a previous life. Recommendation: protection restricts only the respawned body; prior devices keep their attribution and effect.
- **FR-33.** State which grenade/mine bundle supplies each charge (recommend: deplete one bundle fully before the next) so the "frees mass when consumed" rule is deterministic.
- **FR-34.** State the inherited-velocity reference point (recommend: the seat anchor's world velocity, translational plus rotational) for passenger throws and drops.
- **FR-35.** State precedence for a first collision at exactly 12 s of impact-mode flight (recommend: expiry wins, harmless).

## Codex cross-check (Astra)

Performed after the review above was written, at the owner's request, using the reference project's review-invocation wrapper unchanged (foreground, watchdog 540 s, output to file, ASCII-folded, run header verified) with `CODEX_NO_CHARTER=1` because that wrapper's standing charter is Go/Podman-specific; GIVENs were restated in each prompt. Codex was told not to use tools; each pass inlined verbatim SPEC/ACCEPTANCE/DAY1 excerpts plus the findings under check and asked four numbered questions. This is a document-level cross-check by the implementer-role model, not execution evidence, and it does not make Astra's and Fable's work interchangeable.

| Pass | Scope | Session | Outcome |
|---|---|---|---|
| 1 | FR-01, FR-02; SPEC §6, §7, §10, §11 CTF | session id recorded in the local run log | Arithmetic confirmed; FR-01 "impossible" refuted (straight-line sprint fits at 78 s); FR-02 reframed as unspecified release point and absent vehicle immunity; better patch wording; FR-33/34/35 |
| 2 | FR-03, FR-05; SPEC §12, BOT-02/03/06, ENG-011 | session id recorded in the local run log | FR-03 seam confirmed, "must run outside the tick" refuted, perception filtering rejected as wrong; FR-05 arithmetic confirmed with the half-spacing correction; FR-31 |
| 3 | FR-07, FR-09/10, FR-12, FR-19, FR-20; SPEC §8, §9, §11 | session id recorded in the local run log | FR-07 confirmed; FR-10 downgraded to ambiguity; no duplicate-vehicle, duplicate-ownership or nonterminating scenario constructible from the text; FR-19 real but conditional; FR-20 refuted and withdrawn; FR-30, FR-32 |
| 5 (re-verify) | D-010–D-016 gameplay batch: SPEC and ACCEPTANCE passages carrying those markers | session id recorded in the local run log | Caught the reviewer's collinear-velocity error in FR-06 (corrected to 420–450 m); bike mount must require a collision-free righting path; interruption-before-completion tie rule made explicit at the Holdout sentence; owned time needs fractional carry; GRE-01, BOT-06, GRE-08/VEH-08, MIN-06 and VEH-03 assertions tightened. All applied |
| 6 (re-verify) | D-010, D-017, D-018 engineering batch: DAY1, RUNBOOK, ACCEPTANCE rows, DECISIONS D-010–D-018 | session id recorded in the local run log | Fixed-tick wording must define catch-up and forbid silent time loss; GRE-01 record list specified; positive-control, classifier, stand-in package exclusion, editor-lease quiescence, pending-at-M0 and holdout-commitment wordings strengthened; BOT-06 tolerances made required rather than proposed. All applied |
| 4 (re-verify) | Applied D-001/D-002/D-003 text in SPEC §7/§11/§12, GRE-02/07, OBJ-07, UX-06, BOT-02/03, ENG-011, DECISIONS.md | session id recorded in the local run log | No contradictions with adjacent rules. Five corrections, all applied: sweep-exemption wording made explicit and GRE-07's retired 0.10 s clause replaced; D-001 restated as a measurable route requirement (all-foot ≥ 90 s, vehicle-assisted < 90 s) instead of an asserted outcome, reopener widened; "belief set" replaced by frozen belief proxies and the reset list widened to all decision-affecting state; BOT-02 wording narrowed to intents before physical consequences; the Carried-flag interaction sentence narrowed so a carrier can capture |

All three runs echoed `model: gpt-6-astra` and `reasoning effort: xhigh`; none hit the watchdog. Raw outputs are in the session scratchpad, not in the repository.

## Dispositions (owner, 2026-09-08)

| ID | Disposition | Applied patch | Verification still required |
|---|---|---|---|
| FR-01 | **Accepted** as a product decision: CTF is vehicle-required; lease stays 90 s | SPEC §11 wording above; UX-06 and OBJ-07 additions; coordinator note in §12 | UX-06 carry measurements, OBJ-07 vehicle positive control (E/N) |
| FR-02 | **Accepted** | SPEC §7 wording above (Codex form); GRE-02 subcases | GRE-02 co-moving drops on both OS (E) |
| FR-03 | **Accepted** after Codex check, with the corrected fix (decision-query boundary and repeatable decision step; perception unfiltered) | SPEC §12 wording above; BOT-03 and ENG-011 additions | Repeat-tick harness and hidden-blocker case (E) |
| FR-20 | **Withdrawn** by the reviewer | none | none |
| FR-04–FR-19, FR-21–FR-35 | **Accepted** by the owner 2026-09-08 ("accept all"), applied as D-010 to D-018 and re-verified by Codex passes 5 and 6 | SPEC, ACCEPTANCE, DAY1, RUNBOOK, BOT-MOTOR-SKILLS; `DECISIONS.md` | the fixtures named in each finding |

Acceptance of a finding is not closure until the named fixtures run. The wording was applied on 2026-09-08 as D-001, D-002 and D-003 in `DECISIONS.md`; the edited files are:

```text
846102434adb245e59fe75a61c14a16da9378b3f94e7659b8d8e52972ba3a133  docs/SPEC.md
a7751328e970dc38179b07aba8c13b5e01f25609f52b64e54495ad44aac1cfde  docs/ACCEPTANCE.md
dfbc3573fe13b54ad9d150ae055e3e3099a421fed675599ba309247e1f9927c0  docs/DAY1-ENGINEERING.md
a519546aa5f880e45cb882189e1a531c8c183e20e7374c7a5ff365d834cadf83  DECISIONS.md
```

These hashes supersede the input manifest above for SPEC, ACCEPTANCE and DAY1. The applied text for D-001 to D-018 was re-verified by Codex (passes 4 to 6 below) and corrected on every point raised before these hashes were taken.

### ENG-001 – ENG-016 coverage

| ID | Covered by | Status of the obligation as written | Gap or note |
|---|---|---|---|
| ENG-001 | DAY1 §2, §11; ENV-01/03 | Adequate | Windows cell will be UNAVAILABLE on day one; no host identified (unresolved decision U1) |
| ENG-002 | DAY1 §2, §11; ENV-02 | Adequate | Editor process ownership unassigned (FR-23) |
| ENG-003 | DAY1 §5 ten cases | Adequate for negatives; includes the zero-exit-after-timeout case the packet asks for | Add positive-control case 0 (FR-16); macOS process-group supervision cannot bound `setsid` escapees; recording is acceptable |
| ENG-004 | DAY1 §3–4 | Adequate | — |
| ENG-005 | DAY1 §3 | Adequate | Define the source-manifest include set (exclude `Saved/`, `Intermediate/`, DDC) |
| ENG-006 | DAY1 §3, §6; ENV-05 | Adequate | Bystander must be an actor inside a generated level, not only a separate asset |
| ENG-007 | DAY1 §6 | Adequate | Qualify OS lock on both filesystems as stated; FR-23 |
| ENG-008 | DAY1 §6 | Adequate | — |
| ENG-009 | DAY1 §7 | Incomplete | SPEC/BOT-CAST rows missing (FR-17); M0 ordering (FR-24) |
| ENG-010 | DAY1 §10 | Adequate | — |
| ENG-011 | DAY1 §5, §8; BOT-02/03 | **Not validly executable as specified** | Needs snapshot re-execution and bot-only query channel (FR-03) |
| ENG-012 | DAY1 §4, §8; ENV-04, BOT-01 | Adequate | Human-slot stand-in exemption (FR-22) |
| ENG-013 | DAY1 §8; ACCEPTANCE §9 | Incomplete | Holdout custody and sealed seeds (FR-11) |
| ENG-014 | DAY1 §4; UX/PERF | Adequate | In-package screenshot/trace capture command must exist in Development builds only |
| ENG-015 | DAY1 §9; REL-03 | Adequate | This document is the first actual Fable review; record it as such, and record that it is a specification review only |
| ENG-016 | DAY1 §9; REL-04 | Adequate | — |

The mechanisms remain small enough to inspect: twelve scripts, one registry, one ledger, JSON schemas. They are adaptations, not inherited evidence from that project; DAY1 says so and I found no place where the reference project's maturity is claimed for this project. The reference-project revision mismatch (FR-27) should be corrected or the six cited excerpts copied into DAY1 §12 so the requirement text stays self-contained.

## Area-by-area assessment against the packet

1. **Human handling and vehicle scope.** Order is right: M1 human course before M2 bot driving, all four rigs prototyped in M1. Gaps: camera (FR-13), air control (FR-14), frame-rate invariance (FR-04), bike rest states (FR-08), horizontal impact costs (FR-18). Bots and humans are held to the same constraints in every place I checked; the "bounded assists, documented, identical" rule is the correct shape.
2. **Tire impairment and lifecycle.** Threshold and coasting rules are consistent across SPEC, ACCEPTANCE, RUNBOOK, ENGINE-ARCHITECTURE, and BOT-MOTOR (FR-25 seed conflict aside). VEH-06 injects failures in corner/run-up/takeoff/landing; add "during boarding" and "while carrying a flag" to match the packet's list. Replacement rules with a slot-generation counter are sound; the pad-blocking starvation (FR-19) and the 15 s vs. 20 s ordering (FR-20) remain.
3. **Grenade skill and fair information.** The envelope arithmetic is consistent. Blocking issues are the co-moving drop (FR-02) and charge quantization (FR-05); the vehicle-throw ceiling needs a decision (FR-06). The separation of motor error from prediction error is well specified; the bot-only query channel (FR-03) is what actually prevents a hidden-transform shortcut through "is my corridor clear".
4. **The rest of the cast.** Paired same-weapon and three-driver comparisons, neutral-profile ablation, and hidden-identity trials are the right instruments. lattice's counterplay depends on FR-07. b0bbin's "no hidden clutch" is stated in five places consistently. No finding requires cutting any specialty.
5. **Match rules and map incentives.** Event ordering is explicit and testable. CTF has no on-foot margin as frozen; the owner has resolved this as vehicle-required (FR-01). Holdout LOS (FR-10), public information (FR-09), draws (FR-12), and the terminal mine nest (FR-07) are the remaining seams. I could not construct duplicate ownership or a nonterminating match from the written rules; both modes have a 12-minute regulation and every drop path ends in Home or a swept ground point.
6. **Information fairness and evaluation validity.** Principles are right; the executable form is missing (FR-03) and holdout custody is weak (FR-11). The human protocol's sample sizes are stated as proposals and are internally consistent; the user's n = 1 recognition judgment is correctly kept as a separate requirement rather than pooled.
7. **Unreal automation and native delivery.** Engine, toolchain, and MCP claims are current (verified today). Package exclusion of `ModelContextProtocol` runtime modules is correctly required and Epic's page confirms cooked builds can host the server. Mac and Windows evidence are kept separate everywhere. Headless-versus-rendered separation is explicit. The unexecuted command templates are labeled as such.
8. **Day-one engineering contract.** See coverage table. Mechanisms are proportionate; a mandatory unavailable check cannot become PASS under DAY1 §4 as written.

## Three experiments with the highest information value

1. **Mac native round trip (ENG-001/002/ENV-03).** Install the recommended Xcode 26.1.x and UE 5.8.2, build a minimal C++ project, run one MCP create/save/restart/read-back, package and launch a rendered boot with `ModelContextProtocol` excluded and its absence proven from module receipts. Everything else is conditional on this, and the host's free space is far below the 150 GiB planning allowance, so the outcome is genuinely uncertain.
2. **Human handling and frame-rate invariance with the buggy and a bike proxy (M1).** Same input script at 30/60/120 FPS on the handling course; owner drives it; decide camera and air control from what is observed. Resolves FR-04, FR-08, FR-13, FR-14 and de-risks the whole M2 stunt track.
3. **Grenade and fairness seam fixture (M1–M2).** One instrumented range: measured envelope, charge quantization, sprint and buggy inherited-velocity extremes, co-moving drop in both modes, and a bot throw decision re-executed from a snapshot with a hidden pawn inserted in its corridor. Resolves FR-02, FR-03, FR-05, FR-06 with hermetic and engine evidence before any character work.

## Unresolved decisions (owner)

| ID | Decision | Blocks |
|---|---|---|
| U1 | ~~Identify and qualify a Windows x64 host~~ **Resolved 2026-09-08: Mac-first; Windows blocked_external (D-005)** | Windows cells stay UNAVAILABLE |
| U2 | Workspace storage and install authorization: **deferred 2026-09-08, no installation yet (D-005)** | M0 |
| U3 | ~~CTF lease value or "vehicle-required CTF" (FR-01)~~ **Resolved 2026-09-08: vehicle-required, lease 90 s** | record in `DECISIONS.md` |
| U4 | ~~Accept or cap vehicle-inherited grenade range (FR-06)~~ **Resolved: kept uncapped (D-007)** | GRE-06 records envelope |
| U5 | ~~Driver/bike camera perspective (FR-13)~~ **Resolved: chase for driver/bike, first-person otherwise (D-006)** | UX-04 |
| U6 | ~~Airborne inputs yes/no (FR-14)~~ **Resolved 2026-09-08: bounded pitch/roll torque (D-004)** | SPEC §9 updated |
| U7 | Projectile model (FR-15) | GRE-01–09 |
| U8 | ~~Public objective facts (FR-09)~~ **Resolved: flag state and Holdout ownership only (D-008)** | OBJ-05/06, BOT-03/04 |
| U9 | ~~b0bbin voice~~ **Resolved: owner or consenting friend records (D-009)** | UX-05 |
| U10 | ~~Source of expert-human demonstrations~~ **Resolved: owner records them in the project build; original-game footage is reference only (D-018)** | BOT-06/07 |
| U11 | ~~Holdout seed custodian~~ **Resolved: Fable reviewer seals the commitment (D-018)** | ENG-013, BOT-14 |

## Unavailable measurements

- Any native build, package, render, or performance number on either OS; no engine is installed.
- Windows: no host.
- Human evaluation: none conducted; sample sizes remain proposals.
- Chaos two-wheel feasibility, arcade-handling feel, and the actual charge/frame quantization: require the fixtures above.
- Expert-human throw baselines and demonstration data: no source identified.
- Fable review of implementation artifacts: not applicable; this is a specification review only, performed with document inspection, arithmetic, executed document checks, host inspection, and primary-source fetches. Nothing here is an execution receipt.

## Integration note

FR-01 to FR-03 are dispositioned above; disposition the remaining findings in `docs/SPEC-REVIEW.md` or `DECISIONS.md`, citing the applied wording. Re-review after the SPEC/ACCEPTANCE edits for FR-01, FR-02, FR-03 against the new file hashes; the other findings can be closed by the integrator with the named fixtures. Record this review as Fable's, and Astra's work as Astra's; agreement between them establishes nothing about runtime behavior.
