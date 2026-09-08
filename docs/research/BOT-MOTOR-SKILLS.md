# Bot motor skills, creative driving, and optional learning

Research checked **2026-09-07**. This is an original implementation proposal for the offline pivot, subordinate to [SPEC.md](../SPEC.md). It describes future components and experiments; no bot, model, vehicle controller, training run, or native inference test exists yet. Numeric budgets and targets below are tuning seeds to qualify, not measured capabilities.

## 1. What is feasible, and where the difficulty sits

The target is seven recognizable people **in their play**: **arcl1ght**, **hotlap**, **stitch3r**, **farsight**, **b0bbin**, **ramrod**, and **lattice**. These exact handles and requested specialties are product requirements. A grenade label, sniper loadout, or different voice on the same generic controller does not satisfy them.

Humanlike play is a reasonable development objective with current techniques. Published work demonstrates human-rated improvements in Counter-Strike movement through imitation learning, and another study demonstrates efficient perception and humanlike behavior in a constrained 2v2 tactical shooter. These results support the direction; they do not establish that a new combined-arms game can produce indistinguishable human opponents from short descriptions. [Durst et al., humanlike Counter-Strike movement](https://arxiv.org/abs/2408.13934), [Justesen et al., tactical shooter bots](https://arxiv.org/abs/2501.00078)

Expert weapon execution is more tractable than **choosing interesting actions for believable reasons**. Creative vehicle traversal is the largest engineering and content risk: discovering a plausible launch, judging traction and speed, coping with a changed approach, preserving passenger safety enough to remain enjoyable, and continuing usefully after landing. The production route is a hybrid of authored tactics, practiced motor policies, recorded maneuvers, and feedback control. Train compact policies only where measured shortcomings justify it.

The user describes hotlap as reckless and capable of launching from unexpected geometry, and values **arcade-like driving with excellent physics feel**. Build that enjoyable human-controlled vehicle first. “Any distance” and “ramp off anything” mean extraordinary performance across a deliberately generous physical envelope, not a small conventional grenade range or road-only driving. Bot and human use the same grenade and vehicle physics. Removing network play removes replication and service obligations, but does not solve these AI problems automatically.

## 2. Use Unreal's AI systems as infrastructure

| Facility | Assigned responsibility | Boundary |
|---|---|---|
| AI Perception | Sight, hearing, damage notifications feeding a belief store | Raw actor references must not become continuous hidden-target tracking |
| StateTree | Hierarchical lifecycle, tactical execution, interrupts and recovery | One tactical owner; avoid competing trees fighting over the pawn |
| EQS | Rank candidate cover, peeks, dismounts, grenade positions and lookout locations | Supply belief-based enemy contexts; do not use a tutorial's live player actor context unchanged |
| Recast navigation | Foot routes and reachability checks | A navigable polygon does not certify vehicle traction, clearance or a stunt |
| Smart Objects, if useful | Reserve scarce cover/interaction locations | Reservations provide metadata and ownership, not the motor behavior itself |
| C++ skill controllers | Aim, fire timing, throws, driving, recovery and shared action validation | Runtime gameplay remains independent of editor MCP and model APIs |

Epic documents these building blocks separately: [AI Perception](https://dev.epicgames.com/documentation/unreal-engine/ai-perception-in-unreal-engine?lang=en-US), [StateTree](https://dev.epicgames.com/documentation/en-us/unreal-engine/statetree-quick-start-guide), [EQS](https://dev.epicgames.com/documentation/en-us/unreal-engine/environment-query-system-in-unreal-engine), [navigation](https://dev.epicgames.com/documentation/unreal-engine/basic-navigation-in-unreal-engine?lang=en-US), [Smart Objects](https://dev.epicgames.com/documentation/en-us/unreal-engine/smart-objects-in-unreal-engine---overview). The composition above is this project's design recommendation, not an Epic turnkey bot feature.

Use ordinary actors/controllers for this small cast. Mass-based crowd simulation and a full end-to-end neural agent are unnecessary foundation dependencies. A bot's runtime inputs are timestamped observation snapshots, its own state, permitted map knowledge, received team messages, and public objective state. A familiar map may be known in advance. Hidden dynamic occupants, enemies, and future actions may not.

Maintain `ObservedContact` values with observed position/velocity estimates, source, observation time, uncertainty, visibility state and expiry. Enemy velocity comes from observations, not unrestricted `GetVelocity()` while occluded. Hearing supplies an uncertain origin and event category appropriate to the audible event. A loud engine can justify looking toward a vehicle; it does not reveal an unseen passenger's health or aim. Damage events need similarly bounded directional information. The engine's stimulus lifetime is not the complete memory model; configure finite ages and explicitly test forgetting. Epic notes that a zero perception `Max Age` means never forgotten. [Perception configuration](https://dev.epicgames.com/documentation/unreal-engine/ai-perception-in-unreal-engine?lang=en-US)

## 3. Action ownership and skill contracts

Implement one `BotActionArbiter` between every decision system and gameplay. It emits the same legal movement, view, trigger, stance, interaction and vehicle intents accepted from the human. It cannot directly assign damage, health, projectile outcomes, vehicle transforms or successful objective interactions.

Every request carries `MatchId`, `LifeId`, `ControlRevision`, `SkillInstanceId`, observation revision, expiry, channels claimed and its proposed input. Possession or seat changes increment `ControlRevision`. Async query/inference results are discarded when any identity or revision is stale. Death, rematch and map unload cancel tasks and release claims. World pause freezes decision and skill time; unpausing cannot replay accumulated fire or steering requests.

The arbiter uses this sequence on each control step:

1. Enforce lifecycle and gameplay legality: alive, possessed, legal seat, valid weapon, ammunition, cooldown, finite values and current revisions.
2. Honor an irreversible maneuver phase within those rules. An airborne car cannot become an infantry dodge because a tactical score changed. Recovery may alter only still-available controls.
3. Process detected immediate hazards and explicit human transport commands. Hazard priority changes by personality within hard constraints; no bot receives an unseen-grenade alarm.
4. Score objective, combat, transport and reposition proposals. Add commitment hysteresis and short cooldowns to avoid weapon/route oscillation. Record candidate scores and reasons.
5. Acquire channels atomically; invalidate incompatible owners before issuing inputs. Speech consumes no movement channel and never blocks a critical gameplay action.

| Skill | Exclusive channels | Legal coexistence | Exit behavior |
|---|---|---|---|
| Foot movement/peek | Locomotion | Aim/fire unless sprint, mantle or weapon rule forbids it | Clear movement intent and release location reservation |
| Rifle/MG/sniper engagement | View, weapon trigger, optional stance | Foot movement at weapon-specific accuracy cost | Stop trigger; preserve ordinary weapon state |
| Grenade charge/throw or drop | View, weapon, throw/drop interaction | Movement governed by shared human grenade rules | Before release: cancel legally; after release: ordinary timed/impact projectile lifecycle |
| Laser trip-mine placement | View, weapon, placement interaction | Approach movement before placement commitment | Clear ghost/placement intent; actual placed mine persists by shared lifetime rules |
| Driver maneuver | Vehicle control | Passenger uses its own view/weapon channels | Brake/recover according to actual motion; no driver gunfire |
| Seat transition | Interaction, possession transition | No weapon fire | Commit or unwind roster/body/controller references together |
| Objective/loadout interaction | Interaction, required stance/movement restrictions | Only actions allowed to a human interacting with the point, flag or loadout | Complete or cancel through the shared objective/loadout rules |

A skill is a closed-loop policy over a bounded task, not an animation that dictates success. Its definition contains preconditions, goal/tolerance, permitted observations, parameter range, control limits, phases, cancellation points, maximum duration, recovery options and telemetry. An instance contains current phase, random stream, leases, observed progress and failure reason. Separate **identity preferences**, **execution competence**, **risk tolerance**, and **current condition**; a brave bot is not necessarily accurate, and a great driver can still choose a terrible plan.

Initial scheduling proposal (SPEC §12's 2–5 Hz utility seed governs where they differ): utility decisions 4–8 Hz, staggered environment queries 2–5 Hz, observation integration 10–20 Hz, motor outputs 30–60 Hz. Shared weapon and physics timing remains governed by gameplay, independent of tactical update frequency. Event-driven interrupts coexist with deliberately delayed recognition. Profile the entire cast under combat; skip optional candidate expansion under load, never legality checks. Log missed budgets rather than silently reducing fairness delays or freezing one opponent.

## 4. Make aim skilled and humanlike

Use a staged aim controller: notice → orient → acquire → track → commit shot → correct. Target switching and reacquisition have costs. Produce angular velocity/acceleration with finite limits and short overshoot/correction patterns. Add temporally correlated error: a small bias persists across a burst and decays or changes after a correction. Independent random direction every frame produces noise, not human motor behavior.

Model error sources separately: uncertain enemy motion, response delay, current weapon recoil estimate, screen-space tracking error, fatigue/suppression context and shot commitment. Use seeded per-bot streams for reproducible local diagnostics, without promising identical Chaos outcomes across platforms. Weapon spread still follows the same randomization and gameplay rules as the human. An expert may learn recoil compensation; it may not read the unrevealed future spread sample and cancel it exactly.

Specialists gain better positioning, relevant pattern recognition, timing and motor consistency. They do not receive faster bullets, enlarged hitboxes or higher damage. Head targeting is conditional on visible target area, exposure and acquisition time. Bots may pre-aim a known corner or continue a short, previously committed burst toward last seen cover, but the causal record must distinguish prediction/suppression from confirmed sight.

- **stitch3r:** wins through MG control. Selects a position with useful lines, tracks across exposure, uses sustained fire when it denies a crossing, manages recoil, and reloads behind cover. His competence remains evident when he misses: the chosen lane, controlled sweep and timing are purposeful. He is not required to sit still permanently.
- **farsight:** strong sniper and strong driver. Prioritizes sightline quality and escape access, waits for a worthwhile exposure, shoots through the shared ballistics, then changes position after a meaningful cue that the perch is compromised. Drives to create or escape sniper opportunities. He cannot aim/shoot his rifle while occupying the driver seat.
- **ramrod:** good MG execution while closing distance, coupled with fast vehicle entry into a fight. He exposes himself more and commits sooner than stitch3r. A committed approach, hard stop, valid dismount and immediate MG pressure should identify him before a nameplate appears. Courage changes risk scoring, not invulnerability.

Evaluate static tracking, lateral acceleration, partial cover, suppressed re-peeks, target switches, near-muzzle obstructions and shooting from a moving passenger seat. Report acquisition-time distributions, angular corrections, hit rates by range/exposure, burst length, reload exposure and impossible-information violations. Raw kill/death ratio alone cannot establish skill identity or human likeness.

## 5. arcl1ght: an expert grenade thrower, including extraordinary distances

His defining skill is exceptionally accurate **throwing**: reading range and height, leading movement, choosing release timing, and executing practiced throws at short through astonishingly long distances. The motor policy produces ordinary aim, charge and release inputs. Calibrate its expertise from the shared weapon and skilled demonstrations; keep the implementation centered on that practiced motor skill.

The original manual supports a charged throw released from primary fire, a timed fuse starting after release, and an alternate impact detonation mode. The user additionally requires distinct **drop** and **throw** actions and recalls lethal or nearly lethal nearby explosions. This does not imply a grenade proximity sensor. [Original manual, printed p. 13](https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/837940/manuals/Manual.pdf?t=1528450785)

Implement the common human grenade first: release transform, charge-to-speed curve, drop impulse, inherited player/vehicle movement, gravity, collision radius, timed/impact mode, fuse/arming rules, damage falloff and blast occlusion. These values are identical for bots and the human. Drop is a deliberate low-energy local release, not an inventory delete or weakly charged throw disguised as a separate button. Throw supports the generous cross-map range in the main spec. Impact mode supports long airtime without a short timed fuse detonating the projectile halfway there; impact expiry and timed-fuse values remain owned by SPEC.md.

Choose one projectile simulation model and keep its tuning authoritative. Epic's projectile movement component supports bouncing; if the updated component instead simulates rigid-body physics, that physics takes over after initial launch. Qualify the actual selected model, collision response and high-speed sweeps. [Projectile movement API](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/UProjectileMovementComponent)

`ExpertThrowMotorPolicy` has this operational sequence:

1. Read timestamped visual observations: target position, height, recent movement, exposure and the thrower's own movement. Interpret distance and anticipated motion over the practiced flight time. His estimation is strong; no live hidden target transform enters the feature vector.
2. Select a practiced technique from context: direct impact throw, high long-distance throw, timed lob/bank, or intentional local drop while retreating. Select based on opportunity, weapon mode and familiar geometry, not exhaustive per-frame trajectory optimization.
3. Produce aim angle, charge duration and release timing using a calibrated motor curve/table or compact learned policy. Begin with empirically recorded successful throws and interpolate only within the qualified technique envelope; demonstrations can refine the release rhythm and moving throws.
4. Execute through finite view movement and the shared charge/release system. His motor variance is tight, particularly for familiar techniques. Recheck immediate release obstruction and weapon legality without adding a visible hesitation to every practiced throw.
5. Observe the actual result when visible, update his local range/technique calibration within bounded limits, then choose the next play. The released grenade follows ordinary physics; its direction, damage and collision are not corrected to guarantee the result.

Establish expertise with an instrumented range and expert human reference runs: short, medium, long, cross-map, raised/lowered targets, moving launches and coherent moving targets. For impact throws measure distance from the intended interception point to realized contact, plus direct-hit and near-blast success. For timed throws measure detonation error as well as timing. Track technique execution error separately from target prediction error: a target abruptly reversing after release does not mean his throw technique was poor. Across comparable controlled trials he must be convincingly expert, not merely better than a deliberately weak generic bot.

Offline calibration may inspect complete physics and use trajectory visualization to find parameter mistakes. Epic's projectile predictor offers a gravity-driven arc with collision checks, useful as a development diagnostic; it is not a complete multibounce simulator or the defining gameplay behavior. [Projectile prediction](https://dev.epicgames.com/documentation/unreal-engine/BlueprintAPI/Game/PredictProjectilePath_Advanced?lang=en-US)

Record long throws in actual packaged matches, with the camera showing release and distant impact, and preserve the same inputs for a human reproduction fixture. No artificial low grenade-distance cap, special bot launch speed, hidden homing, or near-instant flight can stand in for the physical skill the user remembers.

## 6. hotlap: creative stunt transport without scripted teleportation

The first vehicle gate is a **human driving playground**, before bot training: fast acceleration, controllable braking and handbrake slides, readable grip loss, responsive off-road steering, suspension compression/rebound, ramps from several approaches, long airtime, hard-but-recoverable landings and collisions that preserve understandable momentum. Tune these together as an arcade game; simulation realism is not the goal. The user must enjoy driving the empty vehicle before a bot is credited with vehicle mastery. Repeat feel checks on both OSes and at different rendering rates.

The map should let useful physics work on ordinary scenery. Do not surround the intended stunt route with invisible barriers or restrict the vehicle to a few designated low ramps. Qualified bot techniques describe what the controller has evidence for; they are not invisible permissions controlling what a human may drive over.

Use a **vehicle traversal graph** separate from foot navigation. Ordinary edges represent driveable corridors with width, slope and clearance limits. Stunt edges represent a family of physically achievable transitions with approach, launch, landing and continuation regions. A road spline may help ordinary travel; it cannot be the complete controller. Unreal navigation links can connect disconnected foot regions, but such a connection alone does not supply vehicle control or prove a jump. [Navigation links](https://dev.epicgames.com/documentation/en-us/unreal-engine/overview-of-how-to-modify-the-navigation-mesh-in-unreal-engine)

### Discover opportunities at two levels

**Offline qualification:** sample suitable terrain/mesh surfaces and supplement them with designer or human-driver demonstrations. For each candidate, sweep a range of approach speeds/headings and execute trials in the actual pinned vehicle simulation. Include awkward starts, changed grip and imperfect launch alignment. Store successful and failed envelopes rather than one magic input recording. This makes authored geometry useful without requiring every route to be a hidden scripted rail.

**Runtime generalization:** sample local static geometry for ramps, banks, embankments, low walls and launch/landing pairs. Match their geometric descriptors to qualified maneuver families. Rank by destination value, approach feasibility, novelty, predicted success and likely time saved. Predict dynamic obstructions only from currently observed states. Begin with a small candidate cap and cached geometry; never fork dozens of complete live Chaos worlds every frame.

**Forecast information boundary:** any runtime rollout or clearance forecast operates on known static geometry plus bounded proxy states derived from the bot's observations and memories. Unknown dynamic actors are not copied from the live world into prediction, collision queries, terminal scoring or model features. Use uncertainty margins for a remembered moving blocker. Do not advance the live world, read another controller's planned path, or reuse future replay states. Actual gameplay collision still resolves against every real actor; this distinction allows a bot to be surprised by a previously unseen obstruction. A copied collision scene containing hidden enemies would violate the boundary even if their coordinates were never explicitly handed to the tactical code.

Distinguish demonstrated ability honestly: following a designer-tagged ramp is authored traversal; adapting a maneuver to an untagged changed ramp is generalization; connecting unfamiliar geometry into a useful shortcut is discovery. The final hotlap target includes generalization and useful discovery within the arena's supported geometry families. An authored showcase is an early milestone, not fulfillment of the whole request.

### Execute through feedback and limited physics

A `VehicleSkillClip` stores reference progress and control envelopes, with these fields:

| Field | Required contents |
|---|---|
| Identity | Clip/schema version, engine build, rig/physics/material hashes, author/source and dataset split |
| Geometry | Local coordinate frame, takeoff/landing regions, corridor hulls, slope/height/clearance limits |
| Start envelope | Speed, heading, position, angular rates, suspension/contact state, occupancy/mass assumptions |
| Phases | Align, accelerate, commit, airborne, touchdown, stabilize, continue/dismount |
| Reference | Time/progress samples, desired local pose/velocity, legal control exemplars and tolerances |
| Policy | Feedback gains or model hash, allowed residual magnitude, action-rate and force limits |
| Failure model | Abort corridor, late-failure recovery, timeout and invalidation causes |
| Evidence | Trial seeds/counts, successful arrival plus post-landing continuation, failures and platform matrix |

Run a tracking controller that corrects steering/throttle/brake from current local state. Use throttle/brake in `[0,1]`, steering in `[-1,1]`, legal gear/direction requests and the same handbrake rules as the human. Clip time is a guide; blindly replaying an input tape cannot cope with changed entry speed or collision. Do not set transforms or inject corrective launch velocity.

Air-control authority is an explicit vehicle-design choice. If arcade pitch/roll inputs are introduced, implement the same finite torque and control access for the human, expose the controls, and requalify the rig. If the car has no air control, the bot cannot invent it: launch state determines most of the jump. Clearance must account for the full vehicle body, angular motion and landing footprint. A ballistic center-of-mass arc is only a coarse screening estimate.

At touchdown, success requires remaining upright or legally recovering, passenger survival under ordinary damage rules, a usable dismount/continuation path and actual progress toward the trip's purpose. Merely crossing a destination trigger while tumbling is failure. If the human requests a stop, brake at the next feasible place; a committed airborne maneuver completes its physically available recovery first.

hotlap has exceptional vehicle execution and a strong preference for surprising routes, including options other drivers reject as too risky. He can misjudge one and crash. Separate empty-car experimentation from carrying a passenger or objective: reduce needless fatal attempts without turning him into a timid taxi. Never repeatedly strand the human on an unreachable roof. A surprising perch needs an exit route, a valid dismount or an explicitly playable tactical purpose.

Differentiate the other drivers using the same controller family: farsight values stable repositioning and sightline access; ramrod values rapid violent arrival and a useful MG dismount; hotlap values inventive traversal itself. These are meaningful objective weights and behavior sequences, not cosmetic steering wobble.

## 7. b0bbin: weak overall, with earned surprises

b0bbin's weaknesses should be coherent: late recognition, poor reload timing, overcommitted pursuit, weak threat prioritization, inconsistent tracking and occasional navigation indecision. Keep basic lifecycle and objective competence intact; getting permanently stuck is a defect, not personality. Do not make him randomly spin, throw every grenade at his feet, or sabotage the match to advertise incompetence.

Occasional successes arise from opportunity: an opponent crosses his already-held line, his awkward route becomes an accidental flank, or he correctly finishes a damaged vehicle. Ordinary changes in attention can help him execute a simple action within his stable weaker profile; do not add a special clutch state or change accuracy because the score, death count or dialogue system wants a surprise. No guaranteed critical hit, health adjustment or concealed outcome intervention. Evaluate both aggregate weakness and the occasional useful clutch across many opportunities.

The requested Russian accent and jokes belong in authored, locally packaged voice content with contextual triggers and subtitles. They should be warm characterization independent of the reason he is the weakest player. Example original line after a fortunate recovery: “I was testing the suspension. Very thorough test.” Use a performance direction and actor/content choice, not phonetic spelling as the accent implementation. The other document owns the final bark inventory; this motor system emits semantic events such as `AccidentalFlankPaidOff` or `UnexpectedPassengerSave`, never an automatic claim that an unobserved enemy died.

## 8. lattice: laser trip mines throughout the fight

lattice repeatedly turns useful routes into dangerous territory with laser trip mines. He should leave a recognizable spread of placements over a match, including less obvious approaches and places where opponents naturally rush, rather than revisiting two marked sockets. Match his frequent placement preference with the main spec's generous but finite stock/resupply/active-mine rules; the human uses the same stock and placement mechanics.

Place a real mine on a valid supporting surface. Its laser reaches the first valid opposite surface within the weapon's beam limit, and intersection detection uses the real beam segment and relevant moving collision shapes. The original manual describes the projected beam and interruption mechanism; the new game's trigger allegiance, mine persistence and placement limits are explicit gameplay decisions in SPEC.md. [Original manual, printed p. 13](https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/837940/manuals/Manual.pdf?t=1528450785)

`MinePlacementSkill` samples nearby support surfaces and plausible beam spans. Rank placements using map familiarity, observed enemy crossings, public objective routes, coverage gaps, likely concealment, available escape routes and own-team traffic. A sparse remembered traffic map decays with time; it never imports unseen enemy intentions or future pathfinding destinations. Approach, aim and place through the same distance, surface, clearance, timing and inventory validation as a human.

Place mines at varied heights and angles only when the common weapon allows them and the beam actually intersects a useful crossing. Do not award a kill because a pawn entered the placement's tactical region without crossing the beam. Include moving vehicles, fast crossings, beams blocked after placement, support destruction, rematch cleanup and duplicate detonation callbacks in tests. Mine removal and destruction must update his remembered coverage when observable; he cannot instantly know that a hidden mine was silently removed unless the human receives the same equipment-status information.

Evaluate placement diversity, repeated meaningful resupply, actual beam triggers, coverage of multiple routes and adaptation after the opponent avoids a known trap. A baseline can use authored encounter geometry for initial examples, but final behavior needs valid placements on untagged supported surfaces. His character should be evident in the growing web of traps even when no enemy has yet crossed one.

## 9. Optional learned motor policies and platform qualification

Learning Agents supports reinforcement and imitation learning and remains **Experimental** in the checked UE 5.8 API index. It is an option for a narrow steering controller, maneuver selector or aim/movement refinement, not a dependency for basic play. The plugin lists a basic CPU runtime dependency, but that listing is not a packaged-platform test. [Learning Agents](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/LearningAgents)

Authored motor policies can satisfy the complete release if they pass the same specialty, generalization, fairness and human-recognition gates on both platforms. A neural policy is not inherently a higher acceptance level. Conversely, a native fallback that merely keeps the match running cannot substitute for a required specialist skill: fallback operation must pass those gates too before being described as the accepted full experience. If a selected model is essential to meeting them, qualify and package that model on both OSes or keep the release unaccepted until a sufficient native replacement exists.

Do not say Mac training is impossible: Epic's UE 5.5 release notes explicitly introduced Mac/Linux training, with Mac using sockets at that time. Treat that as historical support evidence; test the pinned 5.8.2 build, bundled Python packages and selected trainer transport before promising current acceleration or throughput. A local trainer socket is development infrastructure and creates no requirement for game network play. [UE 5.5 Learning Agents changes](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-5-release-notes)

For a custom exported model, NNE provides runtime interfaces; runtime availability and model compatibility depend on platform. A successful import or editor cook is insufficient. Select a compact CPU inference path first and test actual native packaged execution. Do not assume DirectML provides a Mac path. [NNE overview](https://dev.epicgames.com/documentation/en-us/unreal-engine/neural-network-engine-overview-in-unreal-engine), [ONNX-backed NNE runtime](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/PluginIndex/NNERuntimeORT)

Qualification must separately record: trainer starts; sample collection works; training updates weights; checkpoint reload works; model creation succeeds; inference outputs have correct shape/finite values; native Mac arm64 and Windows x64 cooked packages load it offline; sustained cast-level inference meets budget; missing/corrupt/incompatible models fall back to the baseline controller. No Python trainer or remote service is required in the shipping game. All these legs remain **unverified**.

Bind policy inputs to the same sanitized observation structure as procedural bots. Learning Agents' interactor separates observation/action schemas, observation gathering and action execution; use that boundary to share instrumentation and validation. [Interactor API](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/LearningAgents/ULearningAgentsInteractor)

## 10. Training data, reproducibility and honest acceptance

Record in-engine demonstrations only after stabilizing weapon and vehicle feel. Each record includes build/schema hashes, map/geometry family, vehicle rig and physical-material hashes, physics-step configuration, platform, input device, bot identity/style label, observation snapshot, legal action, outcome, interruption and random seed. Keep trainer-only world truth in a physically separate debug/reward stream; policy features cannot accidentally join it. Store actual controls and state trajectories as well as optional reference video.

Capture recovery and failed attempts alongside successful stunts. If learning is adopted, begin with behavior cloning; then collect corrective demonstrations at states the current policy actually visits. This addresses the distribution shift that motivates dataset aggregation methods such as DAgger. It is a methodology choice, not a guarantee that a small recording session trains an expert. [Ross et al., dataset aggregation](https://arxiv.org/abs/1011.0686)

Split by whole session, driver, geometry family and map variant where appropriate; never randomly scatter adjacent frames between training and test. Maintain a development set for tuning and a held-out acceptance set controlled by the reviewer. Human-designed demonstrations of these styles are useful even if the original players are unavailable; describe the resulting characters as authored interpretations of the user's recollections, not recovered models of the real players.

Freeze controller/model hashes and evaluation recipes before opening held-out results. Once a held-out case informs a code, profile, reward or model change, it becomes a regression case; commission a fresh independently generated acceptance set for the next generalization claim. Keep final test outcomes, seeds and input traces so a later reviewer can reproduce the claimed run. Runtime adaptation in an evaluation is permitted only through the same bounded observations and update rules present in the packaged game, with fresh session state and no carryover from earlier acceptance attempts.

If reinforcement learning is needed, reward useful arrival, control stability and objective contribution. Penalize fatal passenger outcomes, illegal controls, progress-free loops and irrecoverable landings. Audit reward exploits explicitly: farming airtime, rocking on a ramp, crossing a goal while overturned, repeatedly triggering a checkpoint, entering forbidden space, abandoning the human, and exploiting simulator reset logic. Training-only resets never become shipping recovery powers.

Use paired seeded scenarios to compare baseline and candidate, but expect distributional rather than bit-identical cross-platform physics. A model, map collision, tire setup, mass, wheel radius, suspension, gravity or fixed-step change invalidates relevant maneuver evidence. Re-run affected skill tests when any fingerprint changes.

Acceptance should include all of these independent views:

- **Physical competence:** realized hit/throw/arrival metrics by range, obstruction and maneuver difficulty; capture failures and denominators.
- **Generalization:** unseen grenade target motion; untagged stunt layouts and dimensions within declared supported geometry families, with perturbations of angle, height, approach and grip. Entirely new unsupported geometry families test graceful refusal/recovery and exploratory capability separately; success on every imaginable family is not the release claim.
- **Fairness:** zero hidden-state reads in policy inputs; impossible reactions and outcome overrides rejected; legitimate mistakes remain visible.
- **Character identity:** randomized clips without handles/voice, then full match sessions; testers distinguish stitch3r's deliberate MG control, ramrod's entry pressure, farsight's repositioning, arcl1ght's expert long throws, hotlap's traversal and lattice's expanding mine coverage. Evaluate b0bbin over enough opportunities to observe both weakness and surprises.
- **Sustained human feel:** repeated matches and adversarial play, including baiting corners, reversing after grenade release, blocking an approach and repeatedly exploiting a favorite route. Report perceived repetition, fairness and fun separately from recognition.
- **Runtime delivery:** the full cast works in both native offline packages, with captured frame timings, no model-service dependence, valid pause/rematch behavior and no retained leases after death.

The first implementation order is: shared legal controls → human driving and long-throw physics feel gates → bounded bot sensors → fair MG/sniper and expert throw motor behavior → laser mine placement → basic vehicle feedback/recovery → qualified stunt families and hotlap's creative traversal → richer cross-skill tactics → optional learning improvements. Introduce all seven identities early, then deepen them at these milestones. A graybox can establish feasibility; the final “these feel like our people” judgment requires the user's playtests and repeated tuning.

## 11. Four vehicles, shot-out tires and damage-aware driving

The expanded scope requires **all four vehicles** in the full deliverable: the original-style dune Buggy, Humma/Hummer-style utility vehicle, Truck, and an added Dirtbike. This is not a single-car implementation with other silhouettes deferred. The user's desired tire loop is shoot tires → continue with impaired steering and traction → become unable to drive after sufficient damage → abandon or destroy → replacement at base. Repair is not required.

| Vehicle | Initial seat contract | Distinct controller/feel requirement |
|---|---|---|
| Dune Buggy | Driver + one passenger | Agile cross-country response, readable slides, excellent ramp/landing recovery; expose occupants |
| Utility vehicle | Driver + three passengers | Stable fast group transport, heavier momentum, purposeful off-road climbing and recovery |
| Truck | Driver + four passengers; six physical wheels | Largest transport, wider approaches, slower rotation and meaningful braking distance; preserve arcade usability |
| Dirtbike | One rider | Narrow two-wheel traversal, turning lean, controllable balance and separate takeoff/landing behavior |

These capacities are project seeds, not a promise to reproduce every historical seat. The Buggy and utility vehicle each have four physical wheels, the Truck six, and the Dirtbike two; each physical tire has its own hit/damage identity. The original manual describes the six-wheel Truck. [Original manual, vehicle descriptions](https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/837940/manuals/Manual.pdf?t=1528450785)

### Tire damage must alter contact forces

Each wheel has a stable identifier, hit shape, damage state and current physics parameters. A valid shot changes its tire state once, queues the matching physics update at a safe simulation boundary, and updates visuals/audio from that same event. Flat-tire deformation alone is insufficient; lowering whole-car health or top speed alone is also insufficient. A hit must change lateral grip, available traction and the resulting steering response at the affected wheel. Asymmetric failures should cause an understandable pull, understeer or oversteer under suitable load.

Use the SPEC seed for a failed tire, **20–30% of that tire's healthy grip** (SPEC §9 governs; an earlier 0.2 here was superseded), plus qualified changes to its cornering response where needed. This is a tire-state seed, not a universal friction floor overriding different ground surfaces. Epic exposes wheel friction configuration, cornering stiffness and wheel slip/force diagnostics; the movement component includes a runtime `SetWheelFrictionMultiplier` entry point. Verify changes actually reach the pinned Chaos simulation; editing a class default after physics creation is not proof. [Wheel API](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/ChaosVehicles/UChaosVehicleWheel), [Vehicle movement API](https://dev.epicgames.com/documentation/unreal-engine/API/Plugins/ChaosVehicles/UChaosWheeledVehicleMovementComp-?lang=en-US)

For the Buggy and utility vehicle, the proposed initial hard threshold is **three of four tires failed**; for the six-wheel Truck it is **four of six**; for the Dirtbike it is **both tires failed**. At the applicable threshold disable powered propulsion through the shared vehicle rules. Momentum and gravity still work: the vehicle may coast, slide, respond to impacts or roll downhill, and available brakes still function. Do not freeze its transform or instantly erase velocity. A bike with one failed tire should remain briefly driveable, with conspicuously compromised grip and balance; qualify front steering-grip and rear traction failures separately.

### The driver adapts to what it can feel

The motor controller observes its own velocity, orientation, steering input and resulting yaw/side-slip over time, representing proprioceptive feedback available through the human's driving experience. Tire-pop audio, visible damage, and any common HUD indicator provide additional evidence. World-truth tire identifiers may be used by the vehicle simulation and test oracle, but the tactical policy receives only damage information justified by those cues. It does not instantly identify an unseen damaged wheel on another car.

After a perceived change, reduce planned speed, widen turning margins and increase grip uncertainty; adapt steering corrections within the ordinary input limits. Re-evaluate a stunt before takeoff. During a committed jump, prepare for reduced landing traction instead of granting corrective air forces. After landing, measure actual progress and stability before attempting another aggressive maneuver. Already available wheel contact diagnostics can support the underlying vehicle controller, but an AI-only traction or stability assist cannot erase the damage effect.

Make skill differences visible: hotlap can nurse a damaged Buggy through an unusual escape; farsight favors a recoverable route to cover; ramrod may finish his close assault and dismount; weaker drivers compensate late. None restores missing grip. If safe progress is no longer credible, select a stopping place, inform passengers through a short contextual cue, release seats normally and continue on foot. Destruction uses ordinary weapons/explosions, followed by the common base respawn rules; there is no instant bot replacement request. Respect a human passenger's ability to exit and avoid repeatedly stealing the newly respawned vehicle from them.

### Dirtbike qualification is independent

A motorcycle cannot be accepted by narrowing a four-wheel chassis and hiding its wheels. Qualify a genuine two-contact configuration or explicitly designed two-wheel model with a shared, bounded ground-balance assist. Lean must have physical meaning through steering, body orientation and/or center-of-mass behavior; it cannot be only a rider animation pasted onto an invisible car.

The assist uses the same gains, limits, damage response and controls for human and bot. It may make low-speed balance forgiving, but must not hold the bike upright against arbitrary collisions or supply hidden midair corrections. Test ground balance, tight turns, slopes, rear/front grip loss, airborne orientation, rough touchdown and rider dismount separately on both platforms. Maintain a separate bike maneuver library and policy family; reuse skill interfaces and observation schemas where suitable, not unqualified car control gains.

### Required new driving fixtures

Use the Western/desert-fort reference arena's open approaches, fort entrances, banks and changes in elevation, plus an instrumented handling playground. Ordinary ramps, recoverable landings and the intended arcade collisions should not drain vehicle health. Combat remains responsible for the tire-damage loop; a driver practicing stunts should not destroy the car through normal repeated landings.

For every vehicle, replay identical legal input scripts healthy versus damaged and record path, speed, yaw response, stopping distance and wheel forces. Inject previously unseen tire failures during acceleration, hard turning, approach, just after takeoff and touchdown. Test wheel combinations, passenger loads, threshold crossing while moving, destruction with occupants, abandonment and clear/blocked base replacement. Verify zero-drive threshold behavior without losing momentum. Record both successful recoveries and justified abandonment decisions; a visually flat tire with unchanged handling fails acceptance, as does an expert bot that secretly regains healthy grip. All four vehicles must pass native-package playtests before the full vehicle scope is accepted.

Profile the full **eight-spawner** fleet, one of each vehicle at each base, alongside the cast. Empty settled vehicles may sleep; retain damage/query collision and wake on legitimate interaction, impact or relevant physics changes. Offscreen occupied vehicles continue their actual simulation. Sleeping is a performance optimization, not permission to skip tire hits, teleport a vehicle, or defer the Dirtbike from the accepted release.
