# V0.2 acceptance: the offline game and its seven players

Updated **2026-09-08**. This is an unexecuted acceptance plan: **90 product cases plus ENG-001–016**, with no game, native, performance or human passes claimed. [SPEC.md](SPEC.md) defines behavior; [BOT-CAST.md](BOT-CAST.md) defines identities; [DAY1-ENGINEERING.md](DAY1-ENGINEERING.md) defines evidence integrity. The archived network plan is superseded. Every mandatory case must pass before full V0.2 acceptance; milestones do not waive later variants.

## 1. Evidence, execution and milestones

Register each ID in `Verification/checks.json`, with named subcases, milestone, dependencies, platforms, evidence class and expected count. One row may require multiple runs; a favorable variant cannot qualify the entire row. Record actual executable fixture entrypoints during implementation. These descriptions are contracts to implement, not existing commands.

Evidence classes: **S** source/static; **H** isolated harness/math; **E** engine functional; **N** native packaged execution; **R** rendered/input/audio/performance; **U** human evaluation. Functional gameplay cases require E and N on **Mac arm64 and Windows x64**. H checks supplement engine behavior. R cannot come from headless output; U cannot come from agent review. Early E results remain provisional until N variants pass.

Results record run/case IDs, package/source/content/profile/policy hashes, engine/SDK/OS/hardware, scenario manifest, seed, clock/timestep, settings, raw child exit, observations and artifacts. Independent expected values belong in fixture data, not the implementation helper being tested. Logical game rules and physical tolerances require different assertions.

Use `PASS`, `FAIL`, `UNAVAILABLE`, `SKIPPED`, `INVALIDATED`, `ERROR` as DAY1 defines. Missing hardware or human evidence stays unavailable. Inconclusive human findings cannot satisfy a gate. Aggregate acceptance remains `INCOMPLETE` until every required cell has sufficient evidence, or `FAIL` when a valid failure establishes a defect. Missing/empty reports, wrong filters, timeout, stale evidence and nonzero exits never become PASS through a green log line.

| Milestone | Required increment |
|---|---|
| M0 | Environment, editor/harness seams, exact IDs, minimal native rendered boot; applicable ENG infrastructure |
| M1 | Human weapons/grenades, reference Buggy and damaged driving/lifecycle; handling playground |
| M2 | Provisional expert throwing and creative driving, reproduced through human controls |
| M3 | Remaining weapons/mines and all seven individual character behaviors |
| M4 | Four complete rigs, Fort Crossing, Holdout/CTF and integrated matches |
| M5 | Frozen held-out skill/fairness/recognition and repeated full-session evaluation |
| M6 | Final presentation/voice, packages, performance and soak |
| M7 | Independent dispositions, final artifact-bound evidence and portable handoff |

Rows identify earliest milestone and final expansion. Physics/content changes invalidate affected earlier results. All four rigs receive early feasibility probes; final completeness is M4. Dirtbike and character specialties cannot be quietly deferred.

## 2. Native foundation and world lifetime

| ID / milestone | Procedure and required outcome |
|---|---|
| ENV-01 / M0 | Inspect both native hosts. Record actual engine/compiler/SDK/plugin identities, storage and architecture; missing prerequisites produce unavailable cells, never inferred compatibility. |
| ENV-02 / M0 | Create an editor fixture, save, restart and read it back; execute one named real test and capture fresh rendered output. Qualify identical postconditions through the fallback if MCP fails. |
| ENV-03 / M0 | Independently build/cook/package on both hosts; launch native executables and control the pawn. Preserve receipts, hashes, input evidence and exit status. |
| ENV-04 / M0→M6 | Validate seven exact IDs through recipes/assets/cooked roster. Missing/duplicate profiles, unresolved large-file pointers and absent maps fail visibly; no generic replacement. A test-only human-slot stand-in controller used by automated matches and soaks is not one of the seven; it and its assets exist only in editor/test targets and are absent from every packaged build, verified through build dependencies and cooked contents (D-017). |
| ENV-05 / M0→M6 | Regenerate twice around an authored bystander. Semantic IDs/properties/counts remain stable; the bystander survives. Changed generator/content invalidates dependent evidence. |
| ENV-06 / M0→M6 | Inspect cooked dependency/module receipts and sockets. MCP/editor bridges are excluded; close developer tools and disable connectivity, then start and finish a packaged match. |
| LIF-01 / M0→M4 | Queue actions/inference, then restart/menu before completion. Old world/life/control/profile outputs cannot move, fire, possess or score in the new generation. |
| LIF-02 / M1→M4 | Pause moving vehicles, airborne grenades, arming mines, claims and respawns. Every simulation clock freezes; resume applies no accumulated action burst. |
| LIF-03 / M4 | Throw/fire/claim immediately before warmup ends. Active performs one full generation reset of projectiles/mines/actions/jobs/beliefs/seats/respawns/health/ammo/objectives; no warmup damage or pending action survives. |
| LIF-04 / M4 | Deliver duplicate lethal events with a due respawn. One death, flag drop/seat release and five-second respawn occur; obsolete life actions fail. |
| LIF-05 / M4 | Block every spawn, then free one. No overlapping forced spawn. Test two-second protection and each early-ending action; protection cannot deal damage or trigger traps. |
| LIF-06 / M4→M6 | Finish/rematch repeatedly with pending effects/jobs/interactions. Results stay immutable; scoring/damage stop; new matches contain eight combatants and clean objectives. |

## 3. Infantry, loadouts and shared weapons

| ID / milestone | Procedure and required outcome |
|---|---|
| INF-01 / M1 | Exercise run/sprint/crouch/ADS and incompatible inputs. Verify 700 cm/s base and the selected stance multiplier; no stacked incompatible modifiers, wall passage or lost control. |
| INF-02 / M1→M4 | Select valid/overfull/duplicate kits, consume bundles, carry/drop flags. Six-slot/capacity rules hold; default 13 kg kit runs at 630 cm/s; flag adds/removes 4 kg. |
| INF-03 / M1→M3 | Sample full/falloff/hard ranges: MG five-body-hit kill, pistol 38 head, charged sniper 110 body, uncharged sniper head 101 (D-015). Verify pistol 12 damage at 70–100 m, MG 12 at 150–250 m, no hits beyond caps, and cadence limits. |
| INF-04 / M1→M3 | Interrupt partial/empty reloads by switching/death/seat entry. Ammo stays bounded; canceled reloads grant nothing; repeated trigger IDs consume each shot once. |
| INF-05 / M1 | Expose a target to camera while blocking muzzle; repeat legally clear shot. Cover prevents damage; same-run positive control proves weapon/target path functions. |
| INF-06 / M1→M4 | Hit overlapping head/body/chassis/tire zones, including hidden tires. One classified target; vehicle/tire damage uses adjusted body damage without head multiplier. Standard friendly fire protects occupied friendly vehicles; empty vehicles remain damageable by either team. |
| INF-07 / M1→M4 | Resupply depleted kits for two seconds; interrupt each specified way. Only completed legal depot use refills selected stock; it neither heals nor repairs. |
| INF-08 / M1→M4 | Land at 10/16/22 m/s relative downward speed: expect 0/50/100 damage. Moving ground changes relative speed correctly; seated occupants avoid duplicate infantry landing damage. |

Include actual passenger weapons after seats exist. Tuning changes require deliberate fixture revision and fresh evidence, not tolerance widened to fit observed results.

## 4. Grenades: drop, impact and extreme-distance throws

| ID / milestone | Procedure and required outcome |
|---|---|
| GRE-01 / M1 | Charge at zero/intermediate/1.25 s; measure release against the selected 6–45 m/s curve. HUD matches mode/charge and bots receive no extra impulse. Using the same timestamped input script per path at 30/60/120 FPS, record input-event and captured-sample timestamps, applied press/release tick IDs, sampling cadence and rounding, effective charge duration, reachable charge levels (including zero and the 75-tick maximum, sweeping edge phases around tick boundaries), throw-relative launch speed, and consecutive impact-range gaps at a fixed documented release angle, heights and inherited velocity; report capture latency separately and reject bot-only intermediate charge states (D-010). |
| GRE-02 / M1 | Drop stationary/running/riding and compare charged throw. Drop has negligible intentional impulse, retains platform velocity once, and is neither sticky placement nor inventory deletion. Running drop and passenger drop at top speed in both modes produce no spurious initial contact with the owner or own vehicle; record projectile-volume clearance at release, release offset and landing point (D-002). |
| GRE-03 / M1 | Release timed grenades against ground/wall/open air. Detonation occurs 3.5 simulation seconds after release, with qualified bounce/friction; charging does not consume the fuse. |
| GRE-04 / M1 | Impact targets/nearby ground and test empty flight. First eligible collision explodes once; 12-second unobstructed lifetime expires harmlessly; nearby enemies never trigger a proximity fuse. |
| GRE-05 / M1 | Demonstrate approximately 180–220 m maximum-charge impact envelope and repeatable contacts beyond 150 m. Timed grenades retain their own fuse; range is physical, not silently extended. |
| GRE-06 / M1 | Throw from moving foot/platform/passenger states in opposing directions. Inherited velocity is added once; human/bot input replays produce the same qualified physical envelope. Record the sprint (~260 m) and buggy-top-speed (~420–450 m, optimized elevation) forward-throw ranges against the 12 s impact lifetime (D-007). |
| GRE-07 / M1 | Block hand/release volume and test owner and vehicle overlap. Blocked release cancels visibly without stock loss; world collision stays active; both source hulls are clear at release, no source exemption persists into flight, and subsequent contacts, including a same-step rebound, are eligible normally (D-002). |
| GRE-08 / M1→M4 | Blast at 1/3/5 m unobstructed infantry distance: expect 110/55/0 damage. Test partial/full cover, contact-offset walls, self/team settings and chassis/tire subparts without duplication. At a documented in-range distance, for grenade and mine blasts separately, assert a fully exposed buggy passenger receives the calculated blast damage and a fully occluded truck cab occupant receives zero, with both chassis intact (D-011). |
| GRE-09 / M1→M2 | Repeat moving-target contacts at 30/60/120 rendered FPS with recorded simulation settings. No tunneling or missed impacts; no post-release homing; qualified accuracy remains stable. |

Range evidence records elevation, geometry, launch velocity, timestep and realized contact. A predictor's success flag is insufficient. Specialist trials separate precise execution from unpredictable target movement after release.

## 5. Mines and real laser-beam crossings

| ID / milestone | Procedure and required outcome |
|---|---|
| MIN-01 / M3 | Place within/outside 1.5 m and beam limits 0.3–12 m; interrupt 0.8-second placement. Valid static endpoints consume once; invalid attempts never create hidden traps. |
| MIN-02 / M3 | Cross before/after 1.5-second arming. Only armed mines trigger; an eligible actor already intersecting at arming triggers once. Rendered/collision endpoints agree after placement and fixture reload. |
| MIN-03 / M3 | Sweep infantry/every vehicle through beams between frames; pass nearby without crossing. Actual fast crossings trigger once; proximity and repeated stationary overlaps do not fabricate triggers. |
| MIN-04 / M3 | Exercise enemies-only/Classic Traps with planter/team/protected actors. Trigger allegiance and separately selected blast friendly fire obey visible settings. |
| MIN-05 / M3 | Place simultaneously at 16 owner/32 team/64 world limits, including arming reservations. Caps hold atomically; refusal is clear; old traps remain. |
| MIN-06 / M3 | Shoot 20-health casing, remove allied mine for one second and interrupt removal. Safe destruction/no chain; cleanup frees quota; removal never refunds ammo. For grenade and mine blasts separately, paired 20-health casings at 2 m: the unobstructed casing is destroyed without detonating while the fully occluded casing retains 20 health (D-011). |
| MIN-07 / M3→M4 | Test 10 m protected base/depot exclusion, planter death, attribution and rematch. Exterior traps persist after death; protected placement fails; rematch leaves no invisible beams. |
| MIN-08 / M3→M4 | Blast at 1/2.5/4 m: expect 120/60/0 infantry damage; test occlusion/exposed subparts. Move a blocker across a beam; occluded segments cannot secretly remain active. |

## 6. Four physical vehicle families and seats

| ID / milestone | Procedure and required outcome |
|---|---|
| VEH-01 / M1→M4 | Contact every wheel/occupy every seat: Buggy 4/2, utility 4/4, Truck 6/5, Dirtbike 2/1. Each functions; hidden stabilizing wheels or decorative Truck tires fail. |
| VEH-02 / M1→M4 | Measure each rig against SPEC acceleration/speed bands, then slalom/brake/off-road. Record mass/suspension/steering/assists; human handling approval remains separate. Replay one identical input script over the ramp course at 30/60/120 rendered FPS: takeoff speed, jump distance and landing pose stay within declared tolerances (D-010). |
| VEH-03 / M1→M4 | Repeat brushes/ramps/landings without chassis-health loss. Ram at relative 5/10/15 m/s: expect 0/50/100 once per separated contact. Last driver retains credit after bail/death; never-driven impacts have environmental attribution. The rammer chassis takes no damage; struck-vehicle occupants take no direct ramming damage (survive with an intact chassis, die exactly once each when the ram destroys it); a 30 m/s wall crash costs no health; a 30 m/s bail into a wall costs only landing damage (D-014). |
| VEH-04 / M1→M4 | Damage 40-health tires through Intact/Damaged/Failed. Identical inputs reveal actual wheel-specific grip/braking/drag/yaw changes for front/rear/asymmetric failures; cosmetics alone fail. |
| VEH-05 / M1→M4 | Fail 3/4 Buggy/utility, 4/6 Truck and 2/2 bike tires while moving. Powered drive stops; coasting/slopes/braking/external impulses remain physical. |
| VEH-06 / M2→M5 | Inject unseen tire failures in corner/run-up/takeoff/landing. Drivers respond to permitted cues and recover/abandon; no hidden traction restoration, teleport or forced success. |
| VEH-07 / M1→M4 | Race actors for a seat; enter remotely/obstructed/above 4 m/s/hostile occupancy. One valid claim succeeds. Empty boarding accepts either team; occupied allegiance follows occupants, then last driver/original allocation when empty; marker and mine rules agree. |
| VEH-08 / M1→M4 | Use every legal passenger firearm/grenade with cabin/arc obstructions. Shared rules hold; driver weapons/seated mines fail; bike has no passenger; seated occupants are blast targets sampled from their seat pose (D-011). |
| VEH-09 / M1→M4 | Exit below 4 m/s with alternate/all exits blocked; attempt high-speed exit and 0.4-second bail. Clear egress inherits motion; blocked cases preserve possession without wall teleport. |
| VEH-10 / M1→M4 | Kill individual occupants, then destroy full/empty vehicles. Seated deaths/flag drops occur once; visual destruction adds no area blast. Empty destruction grants no player kill/score; stale references cannot control replacements. |
| VEH-11 / M1→M4 | Overturn, wait five nearly-stationary seconds, hold recovery three seconds. Shared clear-pose recovery drops flags; moving/airborne/blocked attempts fail and earn no stunt credit. |
| VEH-12 / M1→M4 | Abandon and shoot/grenade one's empty car with friendly fire off; replacement occurs. Test continuous 35/15 s returns, original-team enemy resets, 8 s wreck/20 s destruction replacement and blocked-pad two-second retries with alternate-anchor fallback after ten; no duplicates or retained attribution. |
| VEH-13 / M4 | Bike low-speed balance/slopes/lean/tight turns, front/rear failures and landings. Actual two-contact dynamics and bounded assists operate equally for human and bot. Fall at a stop, mount the fallen bike from 2.5 m (it rights in place), attempt to mount a fallen bike under an overhang that blocks the upright pose (entry fails, bike unmoved), dismount while moving, and confirm an empty bike stands (D-012). |
| VEH-14 / M4→M5 | Request pickup/stop from each driver. Acknowledge boarding and seek a feasible stop promptly; repeated requests cannot cause indefinite stunt loops or strand passengers. |
| VEH-15 / M4→M6 | Perform 100 entry/exit cycles per seat and three rematches, including theft/sleep/wake. Actor/seat counts stabilize; hits wake settled vehicles without lost damage. |

## 7. Holdout, flags and chronological rules

| ID / milestone | Procedure and required outcome |
|---|---|
| OBJ-01 / M4 | Claim Holdout within 1.5 m/LOS for one second; interrupt every listed condition and contest completion. No canceled progress survives; timestamp/stable-ID ordering chooses the owner. An enemy standing on the terminal does not interrupt a claim; a wall or a vehicle interrupts when it blocks the eye-to-anchor trace (D-013). |
| OBJ-02 / M4 | Claim at t=10, switch at t=12, evaluate at t=14 in an oversized tick. Each team receives exactly two owned seconds; ownership persists without standing at the point. |
| OBJ-03 / M4 | Cross 240 owned seconds or 12-minute expiry with same-frame claims. Chronological integration picks winner/draw once; threshold at the deadline counts, late claims do not. |
| OBJ-04 / M4 | Compete for 0.3-second enemy-flag pickup within 1.5 m/LOS; test out-of-range/seated/friendly-home/obstructed actions. One legal carrier/flag state survives; +4 kg applies once. |
| OBJ-05 / M4 | Carry/drive/ride, then drop/die/destroy/invalidate carrier. One flag reaches swept recoverable ground; inaccessible/out-of-world drops reset Home; vehicle references clear. |
| OBJ-06 / M4 | Complete/interrupt one-second friendly return within 1.5 m/LOS; test 20-second automatic return. No duplicate Home state or retained burden; dropped lifetime uses simulation time. |
| OBJ-07 / M4 | Drop/re-pick/pass/drive through the 90-second same-team lease. Passing never renews it; expiry returns Home; new theft after reset gets a new lease. Positive control: a vehicle-assisted carry on the documented route, including reaching and boarding the vehicle, dismount and the capture hold, completes a capture before expiry; the fastest legal all-foot capture takes at least 90 s (D-001). |
| OBJ-08 / M4 | Deliver on foot with a 0.3-second capture within 1.5 m/LOS, own flag Home throughout. Steal own flag mid-hold; race return/delivery. Only eligible ordered captures score; later returns cannot retroactively validate progress. |
| OBJ-09 / M4 | Reach three captures/regulation expiry with tied/unequal scores. One result; captured flag resets without resetting unrelated mines/vehicles or inventing overtime. |
| OBJ-10 / M4 | Sweep exact ties/overshot ticks for score, regulation, death, lease, auto-return and interactions. Apply SPEC priority; capture/pickup at lease expiry loses; completion at regulation expiry cannot count. Owned time accumulates with fractional carry and compares as floored integer milliseconds (sixty 60 Hz ticks = 1,000 ms); nonlethal interrupting damage at the completion timestamp cancels the claim (D-013). |

Clock cases require H injected-clock checks and actual engine interactions. Integrate owned time to each event first, resolve reached thresholds, then regulation expiry, lifecycle/flag expiries, and valid interactions with stable-ID ties. Randomizing callback delivery without changing effective chronology cannot change logical results.

## 8. Fairness, specialties and recognition

| ID / milestone | Procedure and required outcome |
|---|---|
| BOT-01 / M0→M4 | Start standard/reassigned matches and cycle deaths. Seven unique canonical profiles persist; each fights/travels/resupplies/interacts/recovers when favorite equipment is unavailable. |
| BOT-02 / M0→M5 | Same snapshot/seed, change wholly unobserved target state without changing legal cues. Legal observations and decision intents remain unchanged before physical consequences; same-run visible positive control demonstrates an actual response. |
| BOT-03 / M3→M5 | Repeat counterfactual through aim/EQS/throws/driving forecasts/mines/squad reports and alternate/debug constructors. Hidden actors, future plans and evaluator truth never enter policy inputs. Place an unobserved enemy inside the evaluated throw corridor / EQS trace path / vehicle clearance volume: intents and EQS scores equal the empty-corridor control, while a legally perceived visible blocker in the same position demonstrably changes both the query result and the intent, compared before physical consequences (D-003). |
| BOT-04 / M3→M5 | Expose/hide/move targets, delay reports and exhaust memory. Beliefs retain original timestamp/uncertainty and decay; teammate reports do not become fresh exact sightings. |
| BOT-05 / M1→M5 | Replay equivalent human/bot intents and contradictory/stale requests. Same controls/damage/ammo/physical assists apply; finite aim correction replaces instant unseen snaps. |
| BOT-06 / M2→M5 | Test arcl1ght at 30/75/125/175 m, elevations/motion/close drops/unseen release positions. Expert measured placement persists; movement-reading error is separated from motor error. |
| BOT-07 / M2→M5 | Give hotlap unseen supported ramp/berm arrangements with a passenger. Useful off-road combinations and physical recoverable landings occur; road-only runs/cinematic resets cannot pass. |
| BOT-08 / M3→M5 | Compare stitch3r/ramrod with matched MG/targets/ammo. stitch3r demonstrates sustained tracking, deliberate reloads and useful cover, rather than stationary camping. |
| BOT-09 / M3→M5 | farsight drives to a useful angle, shoots accurately and relocates after relevant exposure/threat. Scope attention has limits; driving competence and legal transitions both appear. |
| BOT-10 / M3→M5 | Balanced repeated b0bbin tasks show weakest aggregate competence plus legal useful surprises. Trace successes to ordinary inputs; no score-triggered clutch/accuracy/health changes or mechanical deadlocks. |
| BOT-11 / M3→M5 | ramrod performs vehicle arrival/dismount/competent MG pressure. Matched comparisons show earlier commitment and actual entry pressure without hidden durability. |
| BOT-12 / M3→M5 | lattice mines fresh corridors/rocks/routes, resupplies and adapts after observed clearance. Diverse valid beams shape traffic; counts alone or two sockets cannot pass identity. |
| BOT-13 / M4→M5 | Complete twelve seeded matches per mode/platform using ordinary human-slot intents. Both teams perform legal bot-attributed objectives in at least ten runs; no indefinite inactivity or objective/seat loss. |
| BOT-14 / M5 | Freeze profiles/models/physics; run the blinded held-out/full-session protocol below. Each identity meets specialty/fairness/user-recognition criteria; insufficient evidence cannot become a pass. |

BOT-02 compares bounded identical observation contexts, not naturally divergent long simulations. Include changed-visible-state controls and action provenance. Actual collision uses the real world; forecasts use known static geometry and observation-derived proxies. Collision-score caches cannot leak hidden actors.

BOT-13's twelve-run/ten-success participation threshold is a proposed seed to freeze before candidate evaluation. BOT-06 uses independent expert-human demonstrations in the same range/motion bins: proposed target is no worse median/90th-percentile placement residual, with direct-hit/near-blast rates reported separately. Unsupported or insufficient reference samples leave expertise unproved; outperforming a deliberately poor baseline is insufficient.

Add an absolute BOT-06 stationary-target motor fixture so a weak reference cannot lower the bar: at least 20 throws in each 30/75/125/175 m range bin per OS, spanning two release elevations. Required 90th-percentile impact-placement error, measured as 3D distance from the target point to the first impact with every attempted throw in its cell's denominator and expiry or missing impact counted as infinite error, is ≤1 m at 30/75 m and ≤2 m at 125/175 m, using reachable, unobstructed impact-mode targets and frozen controls/physics. A rigged target relocation or projectile correction fails regardless of error. Report moving-target prediction separately; this placement target does not promise hits after unpredictable dodges. Placement tolerances presume angle-plus-charge control at the charge-time resolution measured in GRE-01; freeze tolerances, percentile estimator and control/physics configuration after GRE-01 and before any BOT-06 tuning or acceptance evaluation. Distribute the minimum as at least ten throws per release elevation per bin per OS; gate the 90th percentile separately per elevation, bin and OS; declare the percentile estimator (D-010).

For BOT-07, freeze 30 eligible, human-reproduced buggy stunt opportunities across three held-out layouts, including at least two maneuver families per layout and a genuine objective/travel benefit. Proposed targets: choose a useful off-road attempt in at least half of eligible offers and complete at least 70% of those attempts with recoverable, nonfatal passenger arrival. Report aborts, failures and all denominators. Other vehicles require their own supported-envelope control/recovery evidence; buggy success cannot qualify the truck or bike. These are initial acceptance hypotheses to review before evaluation, not measured original-player statistics.

## 9. Human evaluation protocol

These **proposed minimum samples and decision rules** must be reviewed/frozen before candidate evaluation. They are practical criteria for this game, not a universal Turing test. Recruit at least **eight independent raters**, recording FPS experience and familiarity with the described people. Include the user for fidelity judgment. Supply a consistent target-style description before identity trials.

Use at least six complete/randomly selected sequences per character from independent opportunities, retaining routine transitions/failures. Match equipment/presentation where possible, remove names/cosmetics/voice, randomize order and include same-game human recordings. Each rater completes at least twelve matched stitch3r/ramrod trials and twelve three-driver trials. A neutral-profile ablation checks whether equipment alone supplies the recognition cue.

Proposed identity targets: ≥75% correct for the MG pair and ≥60% for three-driver identification, with a participant/session-clustered 95% interval above respective 50%/33.3% chance. Report per-character confusion and all exclusions. Inconclusive intervals require more independent evidence; repeated ratings of one clip cannot inflate sample size. Specialty success and the user's recognition of each character remain additional requirements, not overridden by pooled accuracy.

Conduct at least **24 complete human-played sessions**: twelve per OS, each mode represented in at least six per OS; at least four participants on each platform. Include the user's handling/identity assessment. Sequential local play suffices. Record fairness, repetition, camera/control comfort, surprising usefulness and fun separately on a declared five-point rubric. Proposed target: median ≥4 for handling/fairness and each intended identity, with no unresolved systematic cheating, involuntary transport trap or mechanical deadlock. Publish distributions and criticism; medians cannot conceal mandatory failures.

Freeze code/profile/physics hashes and manifests first. Separate development/validation from three held-out terrain layouts and acceptance target-motion sessions. Held-out layouts and target-motion sessions come from a parameterized generator committed before tuning; before tuning, the Fable reviewer seals a commitment binding a secret seed and random nonce to the generator revision, parameters, split definitions and evaluation protocol; that tuple is revealed and verified only at evaluation, any change to a bound element or any holdout reopening requires a fresh seed and commitment, and unrevealed secrets stay outside tuner-accessible artifacts (D-018). A holdout used to tune becomes regression data; generate independent replacement evidence. Optional ML uses whole-session/player/layout splits, immutable packaged policies and separately qualified training/native inference. A native controller may pass everything without ML; an inadequate fallback cannot claim an unachieved specialty.

## 10. Presentation, performance and delivery

| ID / milestone | Procedure and required outcome |
|---|---|
| UX-01 / M4→M6 | New player boots offline, selects mode/team/kit, practices/plays/rematches without console. All seven persist; setup cannot duplicate or omit identities. |
| UX-02 / M4→M6 | Inspect 1080p/16:10/Retina and medium/minimum graphics. Objectives, mine/sniper beams, tires, grenade mode/charge and action feedback remain legible and consistent. |
| UX-03 / M1→M6 | Rebind foot/passenger/driver/bike actions, change focus and restart/corrupt settings. Controls remain reachable; drop/exit/fire do not conflict; versioned defaults recover. |
| UX-04 / M4→M6 | Inspect jumps/fort walls/bike lean/passenger firing; reduce shake/blur and mute audio. Cameras avoid opaque clipping; visual/subtitle cues preserve critical state. |
| UX-05 / M3→M6 | Trigger b0bbin's actual failures/surprises/rescues repeatedly. Required Russian-accented local voice, recorded by the owner or a consenting friend with consent on file (D-009), gives truthful contextual jokes/readable subtitles; cooldown/mute works; placeholder text fails final voice acceptance. |
| UX-06 / M4→M6 | Human drives all rigs/explores four Fort Crossing approaches. Arcade/physical feel holds; initial vehicle/foot arrivals target 12–20/25–45 s, baseline sides within 10%, without invisible anti-stunt barriers. With lease expiry disabled, measure theft-to-capture time in each direction with the default kit sprinting on the shortest legal all-foot route and by the fastest vehicle-assisted route, including reaching/boarding, dismount and the capture hold; assert all-foot ≥ 90 s and vehicle-assisted < 90 s (D-001). |
| PERF-01 / M6 | After 60 s warmup, record ten rendered minutes per OS at declared 1080p medium: median ≤16.7 ms, p95≤22.2 ms, p99≤33.3 ms; report CPU/GPU separately. |
| PERF-02 / M6 | Stress seven bots/eight awake vehicles/64 mines with motion/explosions. Combined bot CPU including inference p95≤3 ms; cast/physics remain intact and queues bounded. |
| PERF-03 / M6 | Record resident memory ≤7 GiB target and three matches/cleanup. Settled growth ≤10% from first settled match; inspect live actors/jobs/leases alongside allocator totals. |
| PERF-04 / M6 | One-hour mixed-mode native soak per OS includes combat/tires/traps/bailout/rematch. No crash/deadlock/lost objective/irrecoverable control or continuously growing queue. |
| REL-01 / M6→M7 | Rebuild final integrated inputs on both hosts; verify manifests/offline boot/match. Evidence from different source/plugin/model/content identities is invalid. |
| REL-02 / M5→M7 | Native-only bots prove full specialties without model/trainer dependencies. Otherwise test packaged creation/inference and missing/corrupt models on both OSes; training alone cannot qualify inference and fallback limitations stay explicit. |
| REL-03 / M7 | Independent reviewer audits every evidence layer and executes selected independent probes, including a new counterexample; human ratings remain actual participant evidence. Dispositions cite final artifacts; actual Fable involvement is recorded honestly. |
| REL-04 / M7 | Deliver package hashes/controls/locks/evidence/residuals and resumable recipes. Report every mandatory status; unfinished vehicle/voice/human gates cannot be labeled complete. |

Performance values are SPEC's unmeasured targets. Profile native rendered executables with frame-cap/VSync/upscaling disclosed; editor averages and headless throughput do not qualify them. Revision requires an explicit reviewed decision and fresh evaluation.

## 11. Engineering obligations and final report

Register these sixteen additional obligations. Their exact adversarial procedures are in [DAY1-ENGINEERING.md](DAY1-ENGINEERING.md).

| Engineering ID | Required proof and product mapping |
|---|---|
| ENG-001 | Both native toolchains qualified; ENV-01/03 |
| ENG-002 | Actual editor save/restart/read-back/named test; ENV-02 |
| ENG-003 | All ten supervisor-integrity faults plus the positive-control case, including nonzero/timeout/descendants, handled honestly on both hosts |
| ENG-004 | Missing/zero/filtered/duplicate/wrong-run case inventories rejected |
| ENG-005 | Changed source/content/inputs invalidate evidence; ENV-05, REL-01 |
| ENG-006 | Semantic idempotence and authored-bystander preservation; ENV-05 |
| ENG-007 | Exclusive editor lease; live/dead owner, PID reuse and orphaned-operation recovery |
| ENG-008 | Proven interruption inside generation/checkpoint/cook seams; resume once without collateral deletion |
| ENG-009 | Add/delete/rename/untracked/unknown-path classifier coverage; unknown changes select broad validation |
| ENG-010 | Every unproved mandatory obligation has owner, next action and blocking residual |
| ENG-011 | Same-run visible positive/occluded negative fairness controls; repeat-tick probe with reset state; BOT-02/03 |
| ENG-012 | Exact IDs across recipes/assets/packages/experiments; ENV-04, BOT-01 |
| ENG-013 | Separate frozen splits, provenance and reopened holdouts; BOT-14, REL-02 |
| ENG-014 | Actual native rendered input/visual/performance evidence; UX/PERF |
| ENG-015 | Independent dispositions/material fixes reverified; REL-03 |
| ENG-016 | Built/automated/native/rendered/human/unavailable claims separated; REL-04 |

The closing report lists all **106 IDs** and required platform/subcase outcomes with portable evidence locators. Include artifact identities, known defects, unresolved decisions, human sample counts, rejected/inconclusive findings and last qualified build. Document checks may verify this plan's consistency; they cannot mark implementation cases complete.
