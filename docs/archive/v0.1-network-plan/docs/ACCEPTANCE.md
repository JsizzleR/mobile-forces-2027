# V0.1 acceptance and verification specification

Status: **all game tests NOT_RUN**. This file defines future tests; authoring test names is not executing them. [SPEC.md](SPEC.md) supplies canonical gameplay values. [BUILD-RUNBOOK.md](BUILD-RUNBOOK.md) supplies implementation gates. These acceptance targets are original project requirements, not vendor-certified capabilities.

## 1. Completion policy

V0.1 needs native Mac and Windows client evidence, true separate-process multiplayer evidence, a dedicated Windows server, complete bot matches, rendered performance and recovery from a clean checkout. A missing runner or unavailable dependency is `BLOCKED`; an unattempted case is `NOT_RUN`; neither counts as passing. No test may be waived by silently changing the requirement or removing the exercised feature.

The implementer should automate stable rules and lifecycle assertions. Human tests cover control feel, readability, camera comfort and the usefulness of vehicles. Run focused tests while implementing; run the full mandatory matrix once the slice is integrated and again only for changes or unresolved concerns that justify it.

Use four result layers:

| Layer | What it establishes | What it does not establish |
|---|---|---|
| Pure rules / native unit tests | Deterministic arithmetic, state/event ordering, eligibility | Engine ownership, actual replication, render behavior |
| Engine functional/content | Collision, components, navigation, asset references, editor state | Independent-client routing or packaged behavior by itself |
| Packaged multi-process | True controller ownership, native lifecycle, gameplay connectivity | Public internet service/anti-cheat readiness |
| Rendered/human | Visual/audio/input experience and observed frame cost | All authority/correctness invariants |

Gauntlet or the project harness must start and supervise real processes and contain project-specific gameplay assertions. Epic describes Gauntlet as an automation/session framework; game correctness still requires purpose-built checks. [Epic Gauntlet overview](https://dev.epicgames.com/documentation/unreal-engine/gauntlet-automation-framework-overview-in-unreal-engine?lang=en-US)

## 2. Evidence schema

For each test record:

```json
{
  "schema_version": 1,
  "test_id": "NET-03",
  "status": "NOT_RUN",
  "run_id": "assigned-at-execution",
  "source_revision": null,
  "content_manifest_sha256": null,
  "engine_build_ids": {},
  "package_sha256": {},
  "hosts": [],
  "process_topology": [],
  "seed": null,
  "impairment_config": null,
  "measured_rtt_ms": null,
  "expected": "Native Mac and Windows clients complete the scenario on dedicated authority",
  "observed": null,
  "artifacts": [],
  "limitations": ["Specification only; not executed"]
}
```

This example is deliberately unpopulated. Real results must include exact commands/argument arrays, exit codes, scenario version, hardware/OS/toolchain, log locations and relevant server observations. Keep sensitive addresses/credentials out of public reports. Preserve full local logs while summarizing only important evidence in source control.

Suggested structured gameplay events: `MatchStarted`, `PlayerAdmitted`, `Spawned`, `DamageApplied`, `Died`, `SeatTransition`, `VehicleDestroyed`, `CoreTransition`, `InteractionCancelled`, `BreachScored`, `RoundReset`, `MatchEnded`, `BotRecovery`. Each includes server time, MatchId/RoundId/entity/life/generation IDs, revision and reason. Do not log every position/frame by default. Explicit measurement scenarios may sample transforms and bandwidth for bounded intervals.

The harness fails on timeout, crash, missing expected process, missing report, zero discovered tests, missing assertion, mismatched build identity or duplicate actor/core invariants. Screenshots should identify build/run and actual platform; an AI-generated illustration or mockup is never runtime evidence.

## 3. Required native test topology

| ID | Authority | Clients / population | Required use |
|---|---|---|---|
| T1 | Mac local/listen | One local human + seven bots | Solo launch/full match, listen host cost |
| T2 | Windows local/listen | One local human + seven bots | Windows solo, parity |
| T3 | Mac listen | Mac host + Windows client + six bots | Host direction A; real remote possession |
| T4 | Windows listen | Windows host + Mac client + six bots | Host direction B |
| T5 | Windows dedicated | Mac human client + Windows human client + six bots | Main lifecycle/authority regression |
| T6 | Windows dedicated | Eight distinct client connections, at least one native Mac and one native Windows | Capacity/relevance/ownership; other connections may be test-driven native processes |
| T7 | Native Shipping game builds | Mac and Windows independent launch plus a cross-OS match | Shipping configuration, content and tooling exclusion |

Headless test-driven connections in T6 count only if they complete normal login, own distinct PlayerControllers, and issue ordinary gameplay actions. Eight AI bots inside the server do not prove eight client connections. T6 does not replace real rendered clients in T3–T5 or performance tests. Keep at least one human/test client connected in bot soaks so the ordinary empty-server shutdown policy does not end the test unexpectedly.

Networks: nominal local/LAN; N1 measured 100 ms RTT + 20 ms peak-to-peak added jitter + 1% packet loss; N2 exploratory 200 ms RTT + 3% loss. Record injector location and one-way/RTT semantics. Use Epic's current network-emulation controls or an external harness configured for the installed version; do not invent a CVar name or apply lag twice. [Epic network emulation](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-network-emulation-in-unreal-engine)

## 4. Setup, automation and content acceptance

| ID / gate | Procedure | Pass condition |
|---|---|---|
| ENV-01 / G0 | Inspect native engines, toolchains, disks, architecture and build IDs | Real lock populated on both hosts; no placeholder version; native empty project builds and launches |
| ENV-02 / G0 | Check asset/plugin provenance and missing LFS/content inputs | Required inputs present with rights/version/hash; optional art has named placeholder |
| AUT-01 / G1 | Discover native MCP; inspect project/map; create/save/reopen scratch assets on both OSs | Exact project identity matches and operations persist; unavailable MCP has an explicit native-script fallback report |
| AUT-02 / G1 | Run same content recipe twice, export semantic manifest | Same generated entity IDs/counts/properties; no duplicate packages or actors |
| AUT-03 / G1 | Change one recipe transform, regenerate, compare manifests | Only allowed expected semantic changes; manual content remains untouched |
| AUT-04 / G1 | Disconnect bridge after request, reconnect; inspect receipt; repeat idempotently | At most one committed mutation; timeout never treated as proof it failed |
| AUT-05 / G1 | Fail a content validation mid-operation and restart editor from checkpoint | Partial state reported, known saved revision recoverable, regenerated output matches semantic contract |
| AUT-06 / G1 | Attempt stale-revision/wrong-project/out-of-root custom mutation | Operation rejected before mutation; native dispatcher restrictions are actually enforced where required |
| CNT-01 / G4 | Validate/cook all maps and runtime data soft references | Zero missing required references; no Blueprint compile errors; all cooked classes/assets load |
| CNT-02 / G4 | Probe map routes, objective reachability, spawn/seat collision and bounds | Both teams can reach all required locations; no required route crosses invalid nav/collision |
| CNT-03 / G6 | Fresh checkout + asset fetch + generation/build; reopen saved map | Repeatable first match with documented inputs; no hidden local editor state needed |
| PKG-01 / G5 | Native Development and Shipping launch outside editor, offline | Intended map/menu loads; normal inputs work; no Python, LLM or MCP dependency |
| PKG-02 / G5 | Inspect dependency/build receipts and process listeners for all packages | MCP runtime modules/editor authoring/Remote Control/Terminal endpoints absent; only intended gameplay networking enabled |
| PKG-03 / G5 | Launch from path with spaces; inspect case-sensitive references and user settings path | No path-specific missing content; writes only to expected per-user writable directories |

Native MCP operational success is a tooling qualification outcome. Script fallback can satisfy the gameplay production workflow while MCP remains a recorded limitation; no false claim of native MCP success is needed to finish the slice.

## 5. Infantry and authority acceptance

| ID / gate | Procedure | Pass condition |
|---|---|---|
| INF-01 / G2 | Traverse/jump/crouch/ADS/sprint on both native clients and remote view | No stuck capsule or camera loss; legal speed; correct input context after focus changes |
| INF-02 / G2, G4 | G2: compare Scout/Demolition on route and unit-test the +4 kg modifier. G4: repeat with real core pickup/release | Server speed matches formula; firing ammo does not change mass; real core release removes burden |
| INF-03 / G2 | Fire rifle/pistol at known body/head targets at full/falloff/hard ranges | Correct declared damage/cadence/falloff/head rounding; misses/occlusion do not damage |
| INF-04 / G2, G3 | G2: reload full/partial/empty, switch/die/spam reload. G3: enter seat during reload | Ammo conserved and bounded; no completion after canceled reload or old life |
| INF-05 / G2 | Repeat/stale shot IDs, invalid equip/life/round IDs, excessive cadence, invalid aim | Server rejects invalid actions; zero unauthorized ammo consumption/damage/state changes |
| INF-06 / G2 | Camera can see target but muzzle is behind wall/vehicle body | Muzzle obstruction prevents shot through cover; client confirmation agrees with server result |
| INF-07 / G2 | Apply lethal damage twice, simultaneous damage and respawn deadline | One death per life, one score update, one respawn; no old-life request acts on new pawn |
| INF-08 / G4 | Fire rocket at vehicle/infantry/covered target; trigger overlapping hit callbacks | One explosion; correct single direct/splash resolution, LOS, self/team policy, lifespan cleanup |
| INF-09 / G4 | Block spawn candidates and approach with enemies; fire while protected | No overlap/unsafe forced spawn; defined fallback/retry; protection removed before outgoing damage |
| INF-10 / G5 | Switch control focus, rebind keys, apply settings, restart, corrupt local settings | Settings persist; corruption falls back safely; gameplay input resumes; no unreachable menu action |

For INF-03, compute expected outcomes from the versioned **test fixture** with independently specified examples, not by calling the same implementation helper and comparing it to itself. Include four close-range rifle body hits from 100 HP and two direct rockets to a 500 HP vehicle under the selected seed rules. If tuning changes after review, update canonical data and deliberate fixture expectations together with a decision record.

## 6. Vehicle and seat acceptance

Vehicle movement/seat cases are mandatory G3; every variant involving a real relay core is a G4 integration subcase. Test T3, T4 and T5. Repeat state/lifecycle cases under N1. Use simple debug shapes while establishing correctness. Track the baseline and integration subcases separately: a G3 vehicle gate can pass before core integration, but the whole test row cannot be declared finally complete until its G4 subcase passes. The same rule applies to INF-02 and INF-04's explicitly split subcases.

| ID | Procedure | Pass condition |
|---|---|---|
| VEH-01 | Remote client drives the route; other client observes straight, turns, ramp, wall impact | Authority/prediction remain coherent; input arrives promptly; no uncontrolled motion or persistent divergence |
| VEH-02 | Two clients request same free seat in same frame; repeat request | Exactly one succeeds; same valid-generation replay is idempotent; rejected player remains controllable |
| VEH-03 | Drive with remote passenger firing rifle/pistol across allowed arcs | Passenger owns legal weapon path; server applies hits once; aim limits and vehicle obstruction hold |
| VEH-04 | Request entry at speed/out of range/through wall/hostile occupied vehicle | Invalid requests rejected; no pawn hidden, attached or unpossessed accidentally |
| VEH-05 | Exit normally, block one exit, block all exits, request moving exit | Safe swept egress or explicit blocked state; no teleport through geometry or abandoned controller |
| VEH-06 | Remote enemy shoots driver-only and passenger-only on dedicated authority, then repeat disconnect variants while carrying core | Server hit proxies target intended occupant; seat cleared once; driver input neutralized; correct core drop; survivor remains valid and dead player respawns |
| VEH-07 | Destroy vehicle with both seats full; immediately repeat stale seat/fire commands | Both occupant deaths once, core recoverable, no stale-generation control, correct wreck/respawn timing |
| VEH-08 | Late join while both seats occupied, then leave/re-enter network relevance | Same seat/body/weapon state reconstructed; no need for historical enter RPC |
| VEH-09 | Delay actor/reference delivery relative to seat roster revision | Client waits/reconciles safely; no null deref, duplicate possession or permanently floating body |
| VEH-10 | Flip vehicle, attempt recovery occupied/carrying and with blocked recovery volume | Valid server reposition or explicit blocked result; no added health/ammo; core dropped safely |
| VEH-11 | Abandon damaged and healthy vehicles; block pad; remain near enemy vehicle | Return/respawn policy correct; no duplicate live vehicle/spawn overlap; retries bounded and visible in logs |
| VEH-12 | Replay prior vehicle generation request after vehicle replacement | New vehicle unaffected, old reference safely rejected |
| VEH-13 | Complete a bot driver + bot passenger base→bay shuttle on each route | Same seat/control systems; safe stop/dismount; no teleporting; passenger resumes useful infantry role |
| VEH-14 | Run 100 enter/exit cycles per seat plus three consecutive match resets | No leaked pawns/controllers/seat reservations, no input loss; expected live actor counts stable |

VEH-01 numeric instrumentation: over a two-minute representative route, sample server collision-body transform and client rendered vehicle transform against aligned authoritative timestamps; exclude a declared short interval around deliberate resets. Target p95 position error ≤0.5 m and no unexplained jump >2 m under N1. A comparison of raw simultaneous timestamps on unsynchronized clocks is invalid. Also record owning-driver correction magnitude/frequency and passenger camera jitter; visual comfort requires a human pass even if numeric averages pass.

If these thresholds prove inappropriate for the selected engine's built-in interpolation/prediction, preserve the measurement and propose a justified revision before claiming a pass. Do not adjust tolerance to the observed result without explaining the product effect.

## 7. Objective/match acceptance

Run pure injected-clock tests for all timing rules and engine/multi-process cases for the corresponding owner/collision transitions. Every objective test asserts exactly one logical core, at most one carrier/armed terminal, nonnegative score, and no transition from a stale MatchId/RoundId.

| ID / gate | Procedure | Pass condition |
|---|---|---|
| OBJ-01 / G4 | Contested pickup, same-frame repeats, carrier entering transport | One carrier; correct burden and marker; no pickup while seated |
| OBJ-02 / G4 | Drop/death/disconnect/out-of-bounds while carrying on foot and in seats | Core at valid ground or centre, burden removed, pickup works for either team |
| OBJ-03 / G4 | Drop/reacquire same team, opposing capture, possession lease expiry seated | Same-team lease not extended; opponent starts new lease; expiry recovers core safely |
| OBJ-04 / G4 | Install at wrong/friendly terminal, through cover, while seated, after core loss | All invalid channels rejected/canceled without consumption or arming |
| OBJ-05 / G4 | Interrupt pickup/install/defuse through damage, death, release, movement, disconnect | Hold progress resets; no delayed completion after cancellation or target revision change |
| OBJ-06 / G4 | Complete plant, defuse, countdown breach, point reset | Canonical deadlines, scoring, core/vehicle/player reset and round IDs correct |
| OBJ-07 / G4 | Defuse before/equal/after breach with both due in one overshot tick | Before wins, equal loses, after loses according to effective timestamps |
| OBJ-08 / G4 | Plant before/equal/after regulation deadline; core lease expires before plant | Eligible plant creates bounded overtime; late/ineligible plant rejected; no callback-order dependence |
| OBJ-09 / G4 | Deadline with tied/unequal scores and carried/dropped/armed core | Correct winner/draw and only permitted single resolution of overtime |
| OBJ-10 / G4 | Duplicate countdown/reset callbacks; old round RPC after new round | Score increments once, exactly one core/vehicle set, no old command affects new round |
| OBJ-11 / G4 | Join during installed core, overtime, reset and postmatch | Correct HUD/server time/current phase without prior multicast history |
| OBJ-12 / G4 | Solo/listen rematch; dedicated results countdown; all humans leave | Match reachable without console; correct auto-rematch/waiting/host-disconnect behavior |
| OBJ-13 / G4 | Offline pause and online menu while interactions/countdowns active | Offline world-time freeze consistent; online match continues; pause cannot stop remote play |
| OBJ-14 / G4 | Send/hold fire, seat, reload and interaction requests across every phase transition | Server phase eligibility enforced; no old held action, projectile or damage leaks into reset/results/new round |

Example clock fixture: active defuse completes at 24.990, breach at 25.000, evaluation occurs at 25.010. Defuse succeeds unless a cancellation effective before/equal to 24.990 invalidated it. At exact 25.000 defuse completion, breach wins. Test separately a cancellation observed at 25.005; it must not retrospectively invalidate a transition completed at 24.990.

## 8. Bots, gameplay identity and UX

| ID / gate | Procedure | Pass condition |
|---|---|---|
| BOT-01 / G4 | Run solo match with one human + seven bots on both OSs | Match starts/ends/rematches; bots attack/defend/escort and legal objective actions occur |
| BOT-02 / G4 | Run 10 seeded automated matches: seven server bots plus one connected test client automating ordinary player intent in the eighth slot | No crash/core loss; ≥8 matches contain a legal server-bot-attributed plant; each team has ≥2 bot plants across set; test-client actions excluded from bot counts |
| BOT-03 / G4 | Hide enemy behind occluder, expose, hide/move again | Perception/reaction/memory bounds respected; no perfect hidden target tracking |
| BOT-04 / G4 | Block path/shuttle, remove goal, kill target, force role reassignment | Safe bounded fallback and recorded recovery; no infinite busy loop or teleport objective shortcut |
| BOT-05 / G4 | Join/leave humans while bot carries/drives | Four slots/team preserved; bot retired through normal cleanup; no free inherited core/seat |
| BOT-06 / G4 | Give each team controlled reachable pickup/attack/defuse opportunities on dedicated authority, with test client observing without performing objective actions | Server AIController-attributed acquire/carry/install/defuse succeeds for both teams through ordinary legal actions; no completion injected by harness |
| MAP-01 / G4 | Measure both foot/vehicle objective approach routes from fresh spawns | Teams' functional travel times within 10%; infantry contact/vehicle utility windows evaluated |
| MAP-02 / G4 | Two-human pillar playtest, alternate kits/driver/passenger/defender | Both kits and transport useful; terminal defense has counterplay; final approach requires infantry |
| UX-01 / G5 | New player launches, hosts/joins, enters/exits, plays objective, rematches | No console needed; key prompts/next action understood within first match |
| UX-02 / G5 | Inspect teams, core, timers, interaction/exit errors at 1080p and 16:10/Retina | Readable text/markers; color-independent cues; no clipping/hidden state |
| UX-03 / G5 | Mute sound then inspect objective; change team palette; reduce camera shake | Match-critical events remain understandable; settings apply and persist |
| UX-04 / G5 | View seated/standing/running/firing characters remotely; listen to combat | Coherent pose/weapon direction, no roof clipping, required audio cues distinct |

BOT-02 uses real game rules. A test client may automate its own bot-like intent and remain connected to prevent empty-server shutdown; it does not grant the server hidden objectives or bypass legal interaction. Report recovered-stuck incidents: target <1 per bot-hour after warmup, zero recovery while secretly retaining the core or seat. A deterministic seed stabilizes decisions; it does not guarantee bit-identical physics or cross-platform random scheduling.

Human feel review should collect concrete observations: time to first useful action; deaths before understanding attacker; vehicle-use reasons; heavy-kit tradeoff; passenger camera comfort; spawn safety; confusing HUD/audio. At least two people should play both team/vehicle roles across three matches if available. If only the implementer inspected video, human acceptance remains pending with that limit stated.

## 9. Networking, performance and soak

| ID / gate | Procedure | Pass condition |
|---|---|---|
| NET-01 / G2 | T3 and T4 with normal remote fire/death/respawn and join/leave | Both native host directions work; no authority shortcut |
| NET-02 / G3, G4 | G3: T5 remote driver/passenger and non-owning malicious requests. G4: repeat with real objective interactions | Correct dedicated routing, seat/weapon ownership and rejection; objective subcase completes only in G4 |
| NET-03 / G6 | T6 eight real clients for one full match, then ninth joins | Eight legal connections stable, capacity enforced, distinct ownership retained |
| NET-04 / G6 | Repeat principal weapon/seat/objective cases under N1 | No duplicated score/ammo/core/seat; mandatory declared prediction/correction targets met |
| NET-05 / G6 | N2 harsh-network exploratory run and abrupt connection loss | No crash, persistent invalid state or unbounded queues; feel limitations documented |
| NET-06 / G6 | Connect mismatched content/protocol/build package | Clean refusal and useful message; no network-version bypass |
| PERF-01 / G6 | 10-minute rendered match after 60 s warmup on both reference hosts | SPEC median/p95/p99/hitch targets met with recorded native render settings |
| PERF-02 / G6 | T6 eight-real-client workload trace at 30 Hz, reusing NET-03 run if instrumented; separately record T5 bot workload for AI cost | SPEC tick target met with no sustained backlog on eight actual connections; CPU/bandwidth/memory recorded; bots cannot substitute for connections |
| SOAK-01 / G6 | Three full matches including round/match reset, disconnect/rejoin, vehicles | No crash, leaks/duplicate actors, unbounded state or >SPEC post-warmup memory growth |
| SOAK-02 / G6 | One-hour dedicated mixed activity with at least two native rendered clients | No fatal, deadlock, missing core, unrecoverable seat/input, or ever-growing RPC/job queue |

For performance, record frame-time distribution rather than average FPS alone. Capture Unreal Insights or another compatible native trace, include CPU/GPU attribution and measurement mode, and note whether VSync/frame cap masks headroom. [Epic Unreal Insights](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-insights-in-unreal-engine)

Record network bytes per client and actor update counts. Initial diagnostic alert levels: average >100 KB/s downstream or >30 KB/s upstream per client over sustained gameplay merits investigation; these are monitoring thresholds, not hard release gates unless a reviewed decision promotes them. Always record the actual measured transport units and whether packet headers/compression are included.

## 10. Review and release report

The final acceptance report must include:

1. Every ID above with status and evidence pointer; grouped summaries cannot hide unrun cases.
2. Native client/server package paths, hashes, build identity, instructions for Solo/Host/Join and supported host matrix.
3. Scope completed vs remaining, including whether Fable review and human feel tests happened.
4. Known defects with severity, reproduction and workaround; separate blocked infrastructure tests from reproduced game defects.
5. Native MCP qualification status per OS and actual fallback used.
6. Clean checkout/content regeneration result and required asset/toolchain prerequisites.
7. Confirmation that publication/signing/online services are separate from a closed-playtest package unless those additional gates were actually completed.

An independent reviewer should reproduce a sample from each evidence layer and attack the boundary cases, not simply count green rows. Prioritize stale commands across respawn, timer ties/overshot ticks, driver disconnect, blocked egress, late join during installed core, packaged Mac soft references and runtime automation exclusion.
