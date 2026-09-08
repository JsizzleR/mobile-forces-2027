# Engine architecture and verification research

Research date: 2026-09-07. This is research supporting a new, original infantry-and-vehicle multiplayer game. No Unreal installation, compilation, networking experiment, packaging, or performance measurement was performed for this document. Proposed class names, scripts, configurations, thresholds, and test names below do not yet exist.

**Authority:** [SPEC.md](../SPEC.md) selects all canonical scope, class names, defaults, budgets and fallback decisions; [ACCEPTANCE.md](../ACCEPTANCE.md) and [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md) define the selected gates and execution. This appendix explains evidence, failure modes and alternatives. Its optional decompositions, instrumentation and stretch experiments do not create extra V0.1 requirements or supersede those documents. Apply later reviewed decisions through the main spec before using a research alternative.

## 1. Recommended foundation

Use the selected, pinned UE 5.8 release with a small C++ gameplay project, standard Actor replication, `ACharacter`/`UCharacterMovementComponent` for infantry, Enhanced Input, UMG, and a tightly isolated vehicle implementation. Start with a playable 4v4 match, one map, and one two-seat transport whose passenger can fight. Treat 8v8 as a subsequent measured capacity target. Deliver native Apple Silicon Mac and Windows x64 clients from the first packaged milestone; use a Windows dedicated server first, with Linux x86_64 server qualification as a separate deployment milestone. A Mac development machine is viable for this scope, with rendering and build-host choices made deliberately.

The highest-risk experiment is a real client driving while a second client shoots from the passenger seat, with loss and latency, followed by disconnect, death, reentry, and late join. Run that before elaborate art, weapon breadth, online account integration, or a large map. A successful local vehicle demo is insufficient evidence.

This selection is an engineering judgment for this project's bounded scope. It is not a claim that blank C++ projects are generally superior to Lyra, that Chaos vehicle networking works automatically, or that current experimental systems are unsuitable for every project.

### 1.1 Lyra versus a narrow C++ project

Epic describes Lyra as a modular learning sample with multiplayer, weapons, UI, and a customized Gameplay Ability System. It is useful both as a runnable reference and as a foundation when its architecture matches the game. Its experiences activate gameplay plugins; this introduces dependencies and initialization sequencing that must remain coherent when extending it. [Epic: Lyra Sample Game](https://dev.epicgames.com/documentation/unreal-engine/lyra-sample-game-in-unreal-engine?lang=en-US)

| Criterion | Lyra-derived project | Narrow C++ project | Decision for the first slice |
| --- | --- | --- | --- |
| Infantry shooter scaffold | Existing implementation to understand and adapt | Must implement a small, explicit weapon/health/respawn loop | C++ scope remains small enough to specify completely |
| Team match and bot references | Valuable existing patterns | Explicit game rules and bot state machine | Use Lyra as read-only reference where useful |
| First-person presentation | Requires adaptation from the sample's presentation and animation assumptions | Design view/body/camera ownership directly | Narrow C++ reduces inherited presentation assumptions |
| Vehicle possession | Integrates with experience, pawn extension, ability and equipment lifecycles | Explicit PlayerController-to-pawn and seat state contracts | Narrow C++ lowers the number of interacting systems |
| Ability complexity | Existing GAS prediction and attribute/effect framework | No GAS in the slice; bounded components suffice | Avoid half adopting GAS |
| UI/settings breadth | Richer starting point | Smaller desktop menu/settings implementation | UMG plus Enhanced Input initially |
| Automated content generation | Many existing assets and dependencies to preserve | Fewer assets; explicit generation manifest | Easier to make generation idempotent |
| Future cross-platform online services | Existing abstractions and sample integration | Future work behind session interface | Defer hosted matchmaking/account complexity |
| Upgrade work | Sample and project code require coordinated upgrades | Project code still requires engine compatibility work | Pin either choice; no automatic engine migration |

If the implementers already have demonstrated experience extending the exact pinned Lyra version, one bounded foundation comparison is permitted before freezing architecture: compile/package it on Mac and Windows; join the same dedicated server; replace the pawn presentation; enter, ride, fire from, and exit a prototype two-seat vehicle; verify restart/death. Adopt Lyra only if those checks pass and all retained experience/ASC dependencies are documented. Do not run two foundations through later production milestones or mechanically copy the full sample into a blank project.

Epic states that Lyra C++ requires manual changes across engine releases. An older sample that merely opens in a newer editor is not qualification evidence. [Epic: Upgrading Lyra](https://dev.epicgames.com/documentation/en-us/unreal-engine/upgrading-the-lyra-starter-game-to-the-latest-engine-release-in-unreal-engine)

### 1.2 Gameplay Ability System decision

For the narrow C++ route, implement health, inventory, weapon timing, interaction, and match scoring in explicit components. Gameplay Tags may still identify weapon types, damage sources, states, and UI events; using tags does not require adding an Ability System Component.

Choose GAS later only for a concrete need such as numerous interacting abilities, predicted costs, buffs, debuffs, or equipment-granted behavior. Do not introduce it solely because multiplayer is present. Do not let a future GAS migration create a second authoritative health or ammo model.

If Lyra is selected, retain its established GAS integration. Lyra keeps the ASC on PlayerState so state can survive pawn changes, but its pawn-specific abilities and initialization require careful teardown/reinitialization. That makes repeated driver possession an explicit test case. [Epic: Abilities in Lyra](https://dev.epicgames.com/documentation/unreal-engine/abilities-in-lyra-in-unreal-engine?lang=en-US)

GAS is not a general security or rollback guarantee: the documented prediction support excludes some instantaneous effects, including damage. Client hit feedback must never be treated as authoritative damage confirmation. [Epic: Understanding GAS](https://dev.epicgames.com/documentation/unreal-engine/understanding-the-unreal-engine-gameplay-ability-system?lang=en-US)

## 2. Engine maturity and documentation pitfalls

Record the exact engine patch, source commit/build identifier, plugin versions, SDK versions, and target architectures in a toolchain lock file before implementation. Version-family web documentation is not a substitute for checking the selected engine's headers, `.uplugin` descriptors, and build tools.

| System | Verified documentation status or capability | Project stance |
| --- | --- | --- |
| Character Movement Component | Network prediction/correction and replicated default movement are documented | Default infantry foundation |
| Mover | Current documentation explicitly labels it Experimental and says APIs/data may change | Exclude from first slice |
| Conventional Chaos Vehicles | Current plugin API index explicitly labels it Experimental | First bounded vehicle candidate; qualification required |
| Chaos Modular Vehicles | Current plugin API index explicitly labels it Experimental; overview documents native resimulation integration | Researched alternative, explicitly excluded by SPEC D06; not the automatic fallback |
| Network Physics component | Low-level C++ integration, with prediction/history/resimulation responsibilities | Never assume toggling physics prediction completes integration |
| Iris | 5.8 release notes say production-ready; migration page still displays an Experimental banner | Record the inconsistency; standard replication remains sufficient for this slice |
| CommonUI / Enhanced Input integration | 5.8 release notes describe unification; an integration guide still carries 5.2-era experimental language | Test actual pinned-version API; do not blindly copy old integration steps |

Sources: [CMC network behavior](https://dev.epicgames.com/documentation/en-us/unreal-engine/understanding-networked-movement-in-the-character-movement-component-for-unreal-engine), [Mover status](https://dev.epicgames.com/documentation/en-us/unreal-engine/mover-in-unreal-engine), [Chaos Vehicles status](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/ChaosVehiclesPlugin), [Chaos Modular Vehicle status](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/PluginIndex/ChaosModularVehicle), [Networked Physics overview](https://dev.epicgames.com/documentation/unreal-engine/networked-physics-overview?lang=en-US), [5.8 release notes](https://dev.epicgames.com/documentation/unreal-engine/unreal-engine-5-8-release-notes), [Iris migration page](https://dev.epicgames.com/documentation/en-us/unreal-engine/migrate-to-iris-in-unreal-engine), [CommonUI integration page](https://dev.epicgames.com/documentation/unreal-engine/using-commonui-with-enhnaced-input-in-unreal-engine?lang=en-US).

Evidence caveat: the large release-notes page did not open fully through the research browser, but the relevant Epic-indexed sections were returned by targeted searches. Other listed architecture and platform pages were opened. Verify the release-note claims against the installed release and current full page before changing a baseline choice because of them.

## 3. Platforms, rendering, and build topology

### 3.1 Mac and Windows are first-class clients

The local M-series/24 GB configuration and actual toolchain/storage findings are recorded in [PLATFORMS-AND-AGENTS.md](PLATFORMS-AND-AGENTS.md). That document owns the current Mac SDK requirements and primary-source citations, including the discrepancy between stale indexed SDK text and the freshly opened version table. Resolve the final toolchain against the pinned engine's SDK validation and a clean native compile.

Do not expect the local Mac to run an editor, shader compilation, a server, and eight rendered clients simultaneously. One rendered client plus another host or reduced headless test clients is the practical validation layout. Resource limitations are a reason to distribute runs, not to remove Mac gameplay parity.

Build Mac arm64 on a Mac with a compatible full Xcode installation. Build Windows x64 on a Windows build host with the engine-supported compiler/SDK. Do not infer that the Mac can cross-compile a Windows game. Intel Mac and Windows ARM64 are not part of the slice unless added explicitly.

Initial common renderer:

- One medium-quality profile for both operating systems, with conventional LODs and collision meshes available for every asset.
- A 1920x1080 output target, with the internal resolution/upscaler setting recorded. Do not compare native rendering on one platform against upscaled rendering on the other without labeling it.
- No requirement for hardware ray tracing, MegaLights, Nanite, virtual shadow maps, frame generation, or a vendor-specific upscaler.
- Begin with inexpensive conventional lighting and shadows. Software Lumen can be an optional quality profile only after measurement.
- Limit translucent overdraw, dynamic shadow-casting lights, unique large textures, and expensive foliage. Preserve clear player silhouettes and vehicle approach visibility before adding effects density.
- Validate materials, first-person depth/occlusion, decals, particles, UI scaling, mouse capture and audio devices on both platforms. Full controller parity is deferred by the main spec.

The platform feature-support evidence and its primary citations are consolidated in [PLATFORMS-AND-AGENTS.md](PLATFORMS-AND-AGENTS.md). The selected visual baseline must remain within that shared feature set.

### 3.2 Server and hosting order

Use one shared gameplay implementation for standalone practice, listen hosting, and dedicated servers. Client-only camera/UI/audio code must guard access to local players and be absent from dedicated-server dependencies. A listen-server pass remains a listen-server result; it does not prove headless lifecycle correctness.

Epic's documented dedicated-server workflow requires an engine source build and a C++ multiplayer project. Treat acquiring and building that source configuration as an explicit infrastructure task. The first dedicated target may run on Windows; it must host both Mac and Windows clients together. [Epic: Setting Up Dedicated Servers](https://dev.epicgames.com/documentation/unreal-engine/setting-up-dedicated-servers-in-unreal-engine)

Proposed targets: `MobileForces2027Editor`, `MobileForces2027`, and `MobileForces2027Server`; a distinct Client target is optional if the packaging design needs one. These are project-owned target names, not preexisting engine targets.

Linux server is a separate compatibility gate. Epic documents Linux cross-compilation from Windows only; use the matched Windows Linux toolchain or a native Linux builder. The 5.7-5.8 table lists v26/clang 20.1.8 and supports Linux x86_64. Do not present Mac-to-Linux cross-compilation as the default route. [Epic: Linux development requirements](https://dev.epicgames.com/documentation/unreal-engine/linux-development-requirements-for-unreal-engine)

First slice connection scope is direct IP/LAN and private dedicated sessions. DNS, NAT traversal, public server discovery, account auth, platform invitations, anti-cheat integration, crash upload, commercial hosting, and region selection need distinct requirements before public release. The UI still needs connect, cancel, timeout, server-full, incompatible-build, disconnect-reason, and return-to-menu paths from the first slice.

## 4. Runtime organization and authority

Use the main spec's runtime module and add an editor-only module only if needed. Keep test-only helpers in development/test targets and editor tooling outside runtime dependencies. The following is a research decomposition of responsibilities; the main spec's class table is canonical, and optional helper/service types need not become additional classes:

| Area | Proposed type or responsibility | Owns authoritative state |
| --- | --- | --- |
| Match | `AMFGameMode`, `AMFGameState` | GameMode decides rules; GameState exposes match phase, score and timers |
| Player identity | `AMFPlayerState` | Team, score, session identity, life generation |
| Connection/control | `AMFPlayerController` | Server request gateway; controlled entity and player-body linkage |
| Infantry | `AMFCharacter`, `UMFCharacterMovementComponent` | Position via CMC; current body and view/pose state |
| Combatant state | `UMFHealthComponent`, `UMFInventoryComponent`, `UMFWeaponComponent` | Server health/ammo/equipment/cooldowns for one character life |
| Transport | `AMFTransport`, `UMFSeatComponent`, optional `IMFVehicleDriveAdapter` | Vehicle health, seat roster, driver and physics/movement |
| Objective | `AMFObjectiveActor` | Progress, owner, contest state; never inferred from client HUD |
| Respawn | `UMFSpawnDirector` owned by GameMode | Spawn candidates, reservations and respawn scheduling |
| Bots | `AMFAIController`, bounded behavior controller | Server decisions through shared gameplay command functions |
| UI | Player-owned UMG widgets and view models | Presentation and local settings only |
| Tests | `UMFNetTestController` and project Gauntlet nodes | Orchestration/evidence only; no release gameplay powers |

Epic's architecture is server authoritative and requires deliberate replication of gameplay state beyond the common actor mechanisms. The project must design multiplayer immediately, including practice mode. [Epic: Networking Overview](https://dev.epicgames.com/documentation/unreal-engine/networking-overview-for-unreal-engine?lang=en-US)

### 4.1 Shared contracts

The following are proposed logical interfaces and identity concepts to refine the main spec's contracts, not a competing public API. Use its canonical names and state fields. Implementers must resolve actual UE headers, macros, serialization, and API signatures against the pinned engine before coding callers.

```text
FPlayerLifeKey = { StableSessionPlayerId, LifeGeneration }
FMatchKey = { ServerSessionId, MatchGeneration }
FInteractionRequest = { RequestId, LifeKey, TargetActor, TargetRevision, Action, SeatId }
FInteractionResult = { RequestId, Accepted, Reason, AuthoritativeRevision }
FWeaponIntent = { LifeKey, EquipRevision, ShotSequence, WeaponId, AimDirection, ClientTime }
FShotResult = { LifeKey, ShotSequence, Accepted, Reason, AmmoRevision, ConfirmedHit }
FSeatRoster = { VehicleGeneration, Revision, DriverSeat, PassengerSeat, VehicleState }
FSeatOccupant = { LifeKey, PlayerStateRef, BodyRef, SeatRole }

IMFInteractable.QueryLocalPrompt(ViewContext) -> presentation options
UMFSeatComponent.TryApplyServerRequest(Controller, Request) -> result
UMFCombatService.TryFireServer(ControllerOrBot, Intent) -> shot result
UMFDamageService.ApplyServerDamage(ResolvedDamageEvent) -> result
UMFSpawnDirector.RequestRespawn(PlayerState, MatchKey) -> scheduled result
IMFVehicleDriveAdapter.SetAuthorizedInput(DriverKey, Input)
IMFVehicleDriveAdapter.ClearDriverAndBrake()
IMFVehicleDriveAdapter.ReadAuthoritativeMotion() -> snapshot
```

Use stable life/equipment/vehicle generations to reject late requests after respawn, pawn replacement, weapon change, and vehicle respawn. Actor references are convenient runtime handles, not durable save IDs. Clamp collection sizes and rates; never accept arbitrary client class paths, asset paths, transforms, damage, inventory counts, or console commands.

Clients call interaction RPCs on their owned PlayerController, not on an unowned vehicle. A driver change must not invalidate the request channel. Client results arrive via an owner-directed response and/or authoritative state; RPCs themselves do not return values. Ownership and server authority are separate concepts. [Epic: Actor ownership](https://dev.epicgames.com/documentation/en-us/unreal-engine/actor-owner-and-owning-connection-in-unreal-engine), [Epic: RPCs](https://dev.epicgames.com/documentation/unreal-engine/remote-procedure-calls-in-unreal-engine?lang=en-US)

Low-frequency interaction requests may be reliable, with a bounded rate and recent-request cache. Four requests/second with a small burst allowance is an optional starting experiment if the canonical implementation contract has not chosen a bound; it is not an extra acceptance default. A duplicate request returns the prior logical result without taking another seat, consuming another item, or replaying damage. Normal stale/contested requests return a game rejection rather than disconnecting a legitimate client. Malformed protocol values and sustained flooding need separate handling and metrics.

A replicated property is the durable state; a multicast cosmetic event is not. Late joiners must reconstruct score, objective, weapon appearance, vehicle health, and occupancy from current state. Do not rely on client receipt of an earlier sound, montage, or effect.

Property callbacks and RPCs across different actors must tolerate reordering. In particular, the vehicle roster may arrive before the occupant actor, or possession may arrive before input data. Reconcile from state when each dependency arrives, with a timeout/error diagnostic; never require a single lucky callback order. Epic documents limited reliable ordering within an actor, not a global causal order for the entire world. [Epic: Replicated object execution order](https://dev.epicgames.com/documentation/unreal-engine/replicated-object-execution-order-in-unreal-engine)

### 4.2 Infantry movement

Use CMC's networked movement pipeline. Walking, falling, jumping and crouching should be conventional first. Represent sprint intent in saved move data if speed differs under prediction; do not send a separate client-authoritative speed setter. Test starting/stopping sprint around a server correction, jump, death and vehicle entry.

Do not move an infantry character each frame with ad hoc `SetActorLocation` calls. Server teleports for spawn/exit must use a coherent teleport path and reset relevant movement state. Keep client cosmetic first-person camera shake separate from collision and server aim origin. Avoid arbitrary root-motion locomotion and climbing until the initial movement gate passes.

CMC and `ACharacter` are designed together; the documented prediction/saved-move framework is the reference for extensions. [Epic: Networked Character Movement](https://dev.epicgames.com/documentation/en-us/unreal-engine/understanding-networked-movement-in-the-character-movement-component-for-unreal-engine)

## 5. Vehicle implementation and the decisive early gate

### 5.1 Candidate order

Candidate A is a conventional Chaos wheeled vehicle with fixed topology, four wheels, moderate speed, arcade handling, no vehicle deformation, and one driver plus one armed passenger. Use engine input/movement integration and verify replication on the pinned build. Its assets require a coherent skeletal mesh, physics asset, wheel setup, torque curve, animation setup, and vehicle pawn; generating a visually convincing mesh does not generate these functional assets. [Epic: Vehicle setup](https://dev.epicgames.com/documentation/unreal-engine/how-to-set-up-vehicles-in-unreal-engine?lang=en-US)

The current vehicle API exposes a server input update path. Inspect that implementation before adding custom networking. Do not run native input replication plus a second multicast/set-transform loop that fights it. Do not assume a mesh's wheel animation proves remote physical state is correct. [Epic: UChaosVehicleMovementComponent](https://dev.epicgames.com/documentation/unreal-engine/API/Plugins/ChaosVehicles/UChaosVehicleMovementComponent?lang=en-US)

An alternative researched here is a fixed, non-destructible configuration of Chaos Modular Vehicles. Epic documents native Network Physics/resimulation support using Geometry Collections and simulation components. This changes content setup and introduces an experimental dependency. **SPEC D06 excludes it from V0.1; it is not the automatic second candidate or selected fallback.** Reconsidering it requires a recorded architecture decision and its own qualification. [Epic: Modular Vehicle overview](https://dev.epicgames.com/documentation/unreal-engine/chaos-modular-vehicles-overview?lang=en-US)

Networked physics has default, predictive interpolation, and resimulation approaches. Resimulation requires input/state history and additional CPU/memory; faster motion and higher latency make physical interactions harder. The engine's low-level Network Physics integration must be implemented correctly, not presumed complete because actor replication is enabled. [Epic: Networked Physics overview](https://dev.epicgames.com/documentation/unreal-engine/networked-physics-overview?lang=en-US)

If conventional Chaos fails the selected G3 gate, the main spec's fallback is a deliberately simplified server-authoritative arcade transport with client input prediction/reconciliation and unchanged seat contracts. It is a substantial architecture revision with its own spike, not an assumed quick fix. Record the failed gate, evidence and revised handling constraints, and resolve any delivery-scope change through the main spec's decision process. Do not silently replace the transport with a nonreplicated prop, remove passenger combat, or count an offline vehicle as completion. Freeze one accepted implementation behind the selected drive interface; do not support multiple live physics stacks in the initial slice.

### 5.2 Vehicle spike acceptance

Before content production, run a flat test lane with a curb, slope, wall, moving vehicle, stationary vehicle, and a clear exit area. Use one dedicated server and at least three independent client processes: driver, passenger and observer. Include a native Mac client and native Windows client across the qualification runs.

Required scenario: accelerate, brake, reverse, slalom, strike curb, brush wall, collide at moderate speed with second car, park on slope, passenger fires at stationary and moving targets, driver exits/reenters, driver disconnects at speed, observer joins while occupied, vehicle is destroyed, both former occupants respawn. Run each direction of platform assignment: Mac driver/Windows passenger and Windows driver/Mac passenger.

The canonical G3 pass/fail thresholds are in [ACCEPTANCE.md](../ACCEPTANCE.md). The following are additional instrumentation suggestions and **optional stricter/stretch targets** for diagnosing vehicle quality. They must not silently override the selected acceptance matrix; adopt any additional mandatory bound by an explicit spec/acceptance update before a run:

- In a 10-minute run at the canonical normal profile (measured 100 ms RTT, 20 ms peak-to-peak added jitter and 1% loss), no crash, NaN/Inf, falling through ground, persistent duplicate occupant, lost controls, or unrecoverable physics sleep.
- At zero impairment, post-smoothing driver motion has no corrective teleport above 50 cm during ordinary road travel. At the normal impaired profile, no correction above 100 cm outside the explicitly marked collision/reset segments; p95 correction magnitude below 25 cm. Log correction events, not just screenshots.
- Driver input produces a local visual response within two rendered frames if the selected prediction path supports it; otherwise record the measured input delay and fail the desired responsive-driving gate. Server-authoritative does not require sluggish local presentation.
- Passenger attachment error stays below 5 cm relative to the locally rendered seat anchor after initialization; the body/camera never renders a frame at world origin. Validate aim against the server's moving seat transform separately.
- An ordinary accepted seat transition becomes usable within `2 * measured RTT + 250 ms`; there are no stuck transitions after five seconds. A lost request can resolve via the bounded request/reconciliation path.
- The main spec's adverse exploratory profile is 200 ms RTT and 3% loss. A further optional 500 ms RTT/10% loss run probes survival/rejection/cleanup invariants; smooth competitive play is not promised.
- After driver disconnect, authoritative throttle clears within one second of the server detecting disconnection; the vehicle brakes and a valid remaining passenger can later exit. Detection time itself depends on the separately configured connection timeout.

Correction metrics must compare the same simulation time or correction event, not a current client transform against a current server transform while ignoring the client's prediction timeline. Capture physics mode, tick rate, effective latency, collision segment markers, engine commit and asset revisions with results.

### 5.3 Seat and possession model

Use the following explicit initial design. It is proposed, and the spike may change it only with a recorded contract update.

| State | Controller possession | Character body | Combat/input |
| --- | --- | --- | --- |
| On foot | Character | CMC active, world collision | Infantry move/aim/fire |
| Driver | Vehicle pawn | Original character retained, CMC disabled, attached to driver anchor | Driving/look/exit; no driver handheld fire |
| Passenger | Original character | CMC disabled, attached to passenger anchor | Passenger aim/fire/reload/exit; no movement input |
| Dead | Spectator or explicit no-pawn state | Life ended, seat cleared | Scoreboard/respawn flow only |

Keep character health/inventory on the retained character for that life; vehicle health is separate. The PlayerController stores the authoritative player-body reference so code does not assume `GetPawn()` always returns a character. Do not heal or refill ammo on entry/exit. The server's occupant hit representation must follow the seat independently of first-person cosmetics and remain valid while the driver body is unpossessed.

Only one controller possesses the vehicle. The passenger must not steal vehicle ownership to make a firing RPC work. Passenger weapon requests use the existing owned character or persistent controller gateway and resolve the server's current passenger body/muzzle. Disable incompatible CMC movement while attached; do not allow attachment replication and CMC corrections to fight over the same transform. Sitting bodies must not add accidental physical mass or forces to the vehicle.

Entry is a server transaction:

1. Resolve the requesting connection, current life, alive character and requested vehicle generation.
2. Validate match phase, distance, line of sight, seat availability, allowed team/locking rule, speed limit, and current interaction state.
3. Reserve and fill exactly one seat on the server game thread; increment one roster revision. Two same-frame contenders get one winner and one explicit rejection.
4. Cancel incompatible firing/reload/movement state as specified by gameplay rules; save original body reference and collision/movement configuration.
5. Attach body, establish appropriate collision, and possess vehicle only for the driver. Maintain durable replicated seat state for observers/late joiners.
6. Reconcile local camera/input only when required replicated dependencies are available; acknowledge the transaction using its request ID.

An entry request is not an animation promise. The first slice uses a short camera/pose transition; long enter/exit animation synchronisation is deferred. No in-vehicle seat switching is required initially; reject an unsupported switch request explicitly. Exiting and reentering another seat is supported.

Exit is server-resolved: enumerate authored candidate transforms around the car, perform capsule sweeps and ground checks, reject positions inside geometry, another character or a damaging/out-of-bounds volume, and choose the first valid candidate deterministically. If all candidates are blocked, remain seated and show a reason. The candidate list is data owned by the vehicle definition; the client never provides a teleport destination. Apply the design's speed limit consistently. Restore possession, detach, teleport, movement mode, collision, camera and input as one recoverable state transition.

Lifecycle rules for the slice:

- A driver's death clears driver control, brakes the vehicle and ends that character life; it does not delete the surviving passenger.
- Passenger death clears only the passenger seat and character life.
- Vehicle destruction ends both occupied character lives exactly once before cleanup; this deliberately avoids an unimplemented emergency ejection through walls. Subsequent explosion damage must not score those same deaths twice.
- Disconnect clears the relevant seat, stops driver inputs if applicable and destroys or retires the disconnected body through the normal logout path.
- The main spec defines empty-vehicle return/respawn timers and a deliberate occupied rollover recovery hold with a swept placement and core drop. Follow those selected rules; never add an unannounced occupied teleport as a recovery shortcut.
- A match restart destroys old vehicles and life generations, rejects old requests and rebuilds the new match. Cosmetic corpses have bounded lifetime and no authoritative damage role.

## 6. Combat authority and latency boundaries

### 6.1 Hitscan contract

The initial implementation is server-authoritative hitscan with client-predicted presentation, and no server rewind. That is sufficient to qualify a private LAN/core slice, but it is a documented limitation for competitive internet gunplay. Do not claim that Character Movement prediction or GAS implements shooter lag compensation.

For every shot, the server validates life generation, current weapon/equipment revision, finite normalized aim, legal passenger firing arc, match phase, ammo, cooldown and actor state. It computes origin, damage, range, spread seed and accepted cadence itself. A client-supplied actor hit, hit point, damage amount or arbitrary muzzle position is not authoritative.

Resolve camera aim and muzzle obstruction consistently. A third-person or first-person camera can see around a wall while the physical muzzle cannot. Trace from the server's permitted view location toward a candidate aim point, then from the authoritative muzzle to that point; nearer obstruction wins. The passenger uses the moving seat's server transform and permitted arc. Ignore only specifically allowed self/seat collision objects; do not ignore the whole map or all occupants indiscriminately.

Each accepted shot has a unique `(LifeKey, EquipRevision, ShotSequence)` result. Duplicate or stale requests never consume additional ammo or apply another damage event. Project-owned firing traffic must be bounded by weapon cadence and a hard request ceiling; do not bind a reliable RPC to every rendered frame. Lost presentation events may disappear; durable ammo/health state must still converge.

Show muzzle flash, recoil and provisional local ammo immediately where appropriate. Confirm hit markers and kill credit only after server results. Reconcile rejected/pending ammo without refilling the magazine. A remote cosmetic tracer is not a replicated lethal projectile. Kill attribution is decided once from server damage resolution, including self-kill, passenger kill and vehicle destruction.

### 6.2 Projectiles and explosions

If the product slice includes a grenade or launcher, use a server-spawned projectile with a unique shot key, bounded lifetime, finite speed, and authoritative swept collision. Clients may render a predicted visual projectile and replace/blend it when the server actor resolves; only one authoritative projectile can damage anything. `UProjectileMovementComponent` supports movement/bounce/homing, but its existence does not choose the project's damage, replication or prediction policy. [Epic: Movement Components](https://dev.epicgames.com/documentation/en-us/unreal-engine/movement-components-in-unreal-engine)

Explosion authority calculates falloff, cover/occlusion, friendly-fire rules and direct-hit policy on the server. Direct hit plus radial overlap must have an explicit combination rule. Damage a target once per explosion event, even if several components overlap. Vehicle explosion occupant deaths and the external blast must share a deduplication key. Replicate the explosion result/state needed by active clients; maintain a short visual lifetime only where join-in-progress behavior requires it.

### 6.3 Required future rewind contract

Before claiming good internet hitscan fairness, add a separately reviewed lag-compensation package: a bounded server history of relevant collision primitives; trusted server time mapping with a maximum rewind window; rejection/clamping of forged/future/stale times; historical capsule/hitbox traces; explicit treatment of vehicle/passenger transforms and moving cover; and fairness/abuse tests. A proposed maximum window is 150 ms, subject to measured game design review. Do not rewind only infantry while leaving their transporting vehicle or occluding cover in an incompatible time state.

This is not part of the first one-shot core implementation. Its absence must appear in acceptance results and playtest instructions. The first slice may still exercise 100–150 ms RTT to expose defects without claiming finished competitive hit registration.

## 7. Match, bots, input and UI

Match phase is one replicated state machine: waiting/warmup, playing, post-match, travel/reset. Server timers use server time; clients display remaining time from replicated endpoints. Define ties, simultaneous win events, overtime if any, disconnects at score resolution, and restart behavior explicitly in the main game spec.

Spawn selection performs server-side collision checks, bounded candidate evaluation and temporary reservations to prevent simultaneous overlapping spawns. A dead body and the new character have different life generations. Spawn protection ends by the main spec's server policy, including ending on vehicle entry; entry never resets it. If every spawn is blocked, keep the player in respawn state and retry on the selected bounded timer; never spawn inside a wall as a fallback.

### 7.1 Bots

All bots run on the server and use the same damage, interaction, ammo and objective rules as humans. The first bot implementation can be a small explicit state machine or Behavior Tree: acquire objective, navigate, perceive enemy, aim, fire burst, seek cover/reposition, recover when blocked. A full learned planner, runtime LLM, Mass AI population or generalized vehicle navigation stack is unnecessary.

Use baked Recast navigation for on-foot routes. Pathfinding over a navigation mesh does not prove that a wheeled vehicle can follow the path within its turning radius or reverse around obstacles. Epic's basic navigation documentation establishes walkable-space pathfinding; driving requires additional control constraints. [Epic: Basic Navigation](https://dev.epicgames.com/documentation/unreal-engine/basic-navigation-in-unreal-engine?lang=en-US)

The first vehicle bot follows an authored road route with look-ahead steering, speed limits at turns/stops, obstacle traces, a bounded stuck timer and a stop/recover policy. It uses the same authorized vehicle input adapter as a human driver. A small scripted objective/transport demonstration is acceptable if labeled; do not label it general autonomous driving. Infantry bots should avoid stepping directly into active vehicle paths where the authored route is predictable.

Fix bot decision seeds and scenario schedules for reproducibility, but do not demand bitwise cross-platform Chaos simulation equality. Assert gameplay invariants and bounded motion outcomes instead. Eight bots test gameplay/AI load, not eight network connections. The 4v4 network gate requires eight real client connections or equivalent real UE client processes.

### 7.2 Input and UI

Use Enhanced Input contexts for on foot, driver, passenger, spectator and menu states. Add/remove contexts idempotently on local state reconciliation, including late possession and repeated callbacks. Test held fire/use keys across context changes; they must not immediately consume ammo, trigger reentry or close a just-opened menu unless explicitly intended. Enhanced Input supports runtime context addition/removal and prioritization. [Epic: Enhanced Input](https://dev.epicgames.com/documentation/unreal-engine/enhanced-input-in-unreal-engine?lang=en-US)

For the blank C++ route, use UMG for a compact desktop frontend and HUD, with one deliberate focus/input-mode controller. CommonUI is a justified optional layer for more complex gamepad-focused menus, especially if Lyra is selected; it should not be partially integrated with duplicate UI action paths. Its routing system manages how viewport input reaches active widgets. [Epic: CommonUI Input Guide](https://dev.epicgames.com/documentation/unreal-engine/commonui-input-technical-guide-for-unreal-engine?lang=en-US)

The runtime UI exposes game concepts, not MCP connections, code generation, engine module names or backend tool status. Needed screens/states include play/practice, direct connect, loading/cancel, team/scoreboard, death/respawn, match result, settings, pause/disconnect and actionable connection failures. Persist local controls/audio/video settings with schema versioning. Do not let corrupt settings prevent startup; provide defaults and a safe reset.

## 8. Verification architecture

### 8.1 Different test layers prove different things

| Layer | Proves | Does not prove |
| --- | --- | --- |
| C++ compile/UHT/module dependency checks | API correctness, reflected types, target boundaries | Gameplay or visual correctness |
| Automation tests | Local rule functions, lifecycle invariants, content validation | Real multi-process networking unless explicitly orchestrated |
| Single-instance Functional Tests | Map fixtures, traces, spawn geometry, local interactions | Server-to-client assertions across independent processes |
| Separate editor processes | Early ownership/replication debugging | Cooked content/package correctness |
| Packaged multi-process tests | Real client/server lifecycle and cook/runtime behavior | Visual quality when clients use NullRHI |
| Rendered platform tests | Actual Mac/Windows input, rendering, UI, audio and frame time | Internet deployment/anti-cheat acceptance |
| Mixed-host/network tests | Cross-platform connection, delay/loss and server host compatibility | A production service's reliability |
| Human two-player review | Feel, readability, passenger fun, confusing flows | Automated regression protection |

Epic explicitly says a single Functional Test cannot coordinate assertions across separate client/server instances by itself. Use a project-owned multi-process coordinator/test controllers. Gauntlet supplies session/process orchestration, including server-plus-client sessions; it does not automatically invent game-specific success assertions. [Epic: Networked game testing](https://dev.epicgames.com/documentation/en-us/unreal-engine/testing-and-debugging-networked-games-in-unreal-engine), [Epic: Gauntlet](https://dev.epicgames.com/documentation/unreal-engine/gauntlet-automation-framework-in-unreal-engine?lang=en-US)

### 8.2 Test process protocol

Implement one project Gauntlet test family, for example `MF.NetworkScenario`, plus a development-only game controller. These names are proposed. Each run uses a unique RunId and isolated output directory, user-settings directory, save namespace and reserved ports. One authoritative coordinator names the expected role of each process.

Each process writes structured evidence records containing run/scenario ID, build ID, role, local monotonic time, server tick/time where available, match/life/seat generation, event, assertion and bounded diagnostic values. Do not use display names as identity. The coordinator waits for explicit `Ready`, `StepComplete` and `Finished` records from the declared processes; a silent or absent process is a failed/incomplete test, never an implicit pass.

Tests use state barriers and bounded waits instead of long hard-coded sleeps. Examples: all clients report current match generation; seat owner and observer both report roster revision N; a projectile resolves; all clients see the updated objective owner. For each assertion specify authority/observer process and deadline. If a scenario intentionally disconnects a client, declare the expected exit and continue only with the remaining roles.

Terminate the processes owned by the test run in cleanup, including on timeout/crash. Never use a broad process-kill command that could stop an editor owned by another agent. Preserve failure traces/screenshots and a minimal rerun command. Mark a rerun after failure as a separate attempt; do not replace red evidence with an unexplained green result.

Automation tests must not depend on execution order or assumed editor state, following Epic's own test guidance. Generated fixtures need deterministic names scoped to the run, and test-only assets should not enter the shipping cook. [Epic: Automation Test Framework](https://dev.epicgames.com/documentation/en-us/unreal-engine/automation-test-framework-in-unreal-engine)

### 8.3 Network impairment profiles

| Profile | Measured RTT target | Jitter target | Bidirectional loss target | Required result |
| --- | --- | --- | --- | --- |
| Clean LAN | less than 10 ms | negligible | 0% | All gameplay and visual state checks |
| Normal private internet | 100 ms | 20 ms peak-to-peak added jitter | 1% | Selected core playability targets plus invariants |
| Adverse exploratory, as selected by SPEC | 200 ms | Record actual/configured jitter; no additional default | 3% | No crash/corruption; feel explicitly unguaranteed |
| Optional research stress, not a V0.1 requirement | 500 ms | 100 ms candidate; record effective value | 10% | Probe server survival, safe rejection, eventual cleanup/reconnect |

Verify effective RTT rather than assuming a console setting equals round-trip latency. Avoid applying full intended delay independently at both send and receive ends and accidentally doubling the scenario. Apply reorder/duplication where supported as separate named cases. Repeat a representative run across real hosts, because same-machine impairment still shares CPU scheduling and clocks.

Epic provides network emulation for lag/loss and recommends harsh conditions, including 500 ms round trip and at least 10% loss, to expose problems. Those conditions are resilience tests here, not promises of competitive play quality. [Epic: Network Emulation](https://dev.epicgames.com/documentation/unreal-engine/using-network-emulation-in-unreal-engine?lang=en-US)

### 8.4 Required failure cases

| Test | Trigger | Pass condition |
| --- | --- | --- |
| Seat contention | Two players request the same empty seat in the same server tick | Exactly one accepted occupant and one clear rejection |
| Seat replay | Replay accepted entry/exit with identical RequestId | Same result; no second transition or side effect |
| Invalid ownership | Passenger sends driver controls; unrelated client requests exit | Server rejects; existing occupants and movement remain valid |
| Late actor arrival | Roster arrives before body/PlayerState reference resolves | UI/body converges when references resolve; no origin flash or permanent null |
| Driver disconnect | Disconnect while accelerating with a passenger | Inputs clear, vehicle brakes, passenger remains usable |
| Driver death | Kill the driver's retained body | Driver seat clears once; passenger survives unless separately damaged |
| Blocked exit | Walls/players block all exit candidates | Remain seated with reason; no teleport into geometry |
| Vehicle destruction race | Destroy while entry/exit request is pending | At most one death per life, no stranded possessed destroyed pawn |
| Respawn stale request | Deliver old fire/seat request after respawn | Rejected by generation; new ammo/seat unchanged |
| Fire abuse | Negative/NaN aim, impossible weapon, excessive cadence, duplicate sequence | Rejection; no invalid damage; bounded server work |
| Muzzle obstruction | Camera sees target while muzzle is behind wall | No damage through wall |
| Passenger aim | Shoot across legal and illegal seat arcs while turning | Valid shots resolve from authoritative seat; forbidden arc rejected |
| Projectile duplicate | Duplicate spawn intent or overlap multiple target components | One projectile and defined damage count |
| Death simultaneity | Lethal shot and vehicle explosion in one frame | One life termination, deterministic attribution policy |
| Late join | Join mid-match with occupied vehicle and contested objective | Correct current state within deadline; no replay dependence |
| Empty server | Last client leaves, new client later joins | Server remains healthy and match state follows defined policy |
| Match reset | Restart while players dead, seated or connecting | New match generation; no old timers, seats, projectiles or UI subscriptions |
| Input context | Hold fire/use while entering vehicle or opening menu | No stuck fire/throttle or accidental transition loop |
| Bot blocked | Obstruct route and invalidate infantry path | Bounded recovery/stop; no per-frame expensive search storm |
| Build mismatch | Connect mismatched content/protocol version | Clear failure; no crash or half-entered match |
| Package dependency | Launch without editor, source folder or developer machine asset cache | Required map/assets load and match can complete |

For authority rules, test both a legitimate action that succeeds and the nearby illegal action that fails. A test that rejects everything can otherwise appear secure while making the game unusable.

### 8.5 Command and artifact contract

The main [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md) controls command names and wrappers. The following alternative wrapper shapes illustrate possible separation of responsibilities; they are not additional scripts that must be created:

```text
tools/verify-toolchain.{ps1,sh}
tools/build-editor.{ps1,sh}
tools/package-client.{ps1,sh} --platform Mac|Win64 --configuration Development
tools/package-server.ps1 --platform Win64|Linux --configuration Development
tools/run-automation.{ps1,sh} --group MF.Core --run-id <id>
tools/run-network-tests.{ps1,sh} --scenario SeatLifecycle --profile Normal --run-id <id>
tools/run-network-tests.{ps1,sh} --scenario Match4v4 --clients 8 --run-id <id>
tools/capture-perf.{ps1,sh} --scenario HarborRoute --duration-seconds 300 --run-id <id>
```

These are proposed wrapper interfaces, not commands validated in this research session. Resolve platform paths, UBT/UAT switches, automation module compilation and process shutdown against the installed engine. The wrappers must validate arguments and prerequisites, print the exact build/run configuration, fail on nonzero tool exit, reject missing results, and preserve actual newlines in reports.

Epic documents Gauntlet's `RunUnreal` entrypoint and existing `UE.EditorAutomation`, `UE.TargetAutomation`, and `UE.Networking` tests. Use their installed implementations as the starting reference for wrappers and custom nodes. Do not claim a project scenario exists because an engine-level command name exists. [Epic: Running Gauntlet Tests](https://dev.epicgames.com/documentation/unreal-engine/running-gauntlet-tests-in-unreal-engine?lang=en-US)

Produce a per-run manifest and results directory with exact engine/project revisions, uncommitted diff state, plugin/SDK manifest, role/host/architecture map, all launch arguments, effective network metrics, logs, machine specs, test JSON, traces and screenshots/video where required. Record PASS, FAIL, NOT_RUN and BLOCKED separately. A successful editor boot cannot upgrade NOT_RUN Windows packaging to PASS.

## 9. Performance and capacity budgets

The main [SPEC.md](../SPEC.md) and [ACCEPTANCE.md](../ACCEPTANCE.md) control every performance default and mandatory workload. Figures below restate the selected baseline or explicitly identify optional diagnostic proposals; none is a measured capability. Confirm the named Windows reference hardware before its performance gate; the known M-series is the Mac reference. Frame-time capture uses a packaged non-debug build with one rendered client on the measured device and the server/other clients elsewhere; measure listen-host cost separately.

| Metric | First 4v4 slice target | Measurement method |
| --- | --- | --- |
| Client frame pacing | Median <=16.7 ms; p95 <=22.2 ms; p99 <=33.3 ms | Ten-minute rendered capture at 1080p medium after 60 s warmup, eight combatants/two live transports/objective/combat; repeat as acceptance requires |
| Long hitches | Report >100 ms hitches; no recurring hitch more than once/minute after warmup | Trace with markers for travel/loading; report cold-start separately |
| Server game tick | 30 Hz configured initially; p95 <=33.3 ms with no persistent backlog | Eight real connections for capacity, active combat/two transports; human/bot gameplay mix tested separately |
| Server memory | Peak <=2 GiB; no sustained growth >10% after first completed match across three-match soak | Name native resident/committed measurement with spawn/despawn/travel cycles |
| Client memory | Peak <=6 GiB; same selected growth limit | OS metrics plus engine memory/texture stats; unified memory reported explicitly |
| Cooked client archive | <=5 GiB before optional art | Measure delivered archive; excess triggers asset-size review |
| Optional network budget proposal | Mean <=100 KB/s downstream and <=30 KB/s upstream per active client | Diagnostic candidate only unless adopted into SPEC; units bytes/second, excluding map distribution |
| Network peak diagnostics | Report p95 one-second traffic and reliable backlog | Networking trace during simultaneous fire, seat changes and vehicle collision; mandatory limits come from acceptance |
| Soak | Selected three-match workload and required lifecycle repetitions | No crash, leaked occupied seat or sustained growth beyond canonical bound; optional additional 30-minute stress run |

Do not sum game-thread, render-thread and GPU time as if they were serial. Identify the limiting work with traces and frame-time distribution. Use Unreal Insights/Timing Insights for captured CPU/GPU timelines and Networking Insights for replication traffic. [Epic: Timing Insights](https://dev.epicgames.com/documentation/unreal-engine/timing-insights-in-unreal-engine?lang=en-US), [Epic: Unreal Insights](https://dev.epicgames.com/documentation/unreal-engine/unreal-insights-in-unreal-engine?lang=en-US)

Bots and NullRHI clients are useful for server capacity. They cannot validate material compatibility, draw-call load, visible interpolation, input-to-photon latency, audio or UI. Mac and Windows rendered captures are separate required evidence. Report actual resolution scale, VSync/frame cap, power mode, thermal conditions and competing processes.

For the 8v8 expansion, repeat the real-connection gate with sixteen clients, higher combat/event density, and an explicit transport count. Measure before adding Replication Graph or Iris. Use actor relevancy, sensible update rates, owner-only inventory data, dormancy for inactive objects and bounded cosmetic event rates first. A server with eight clients and eight local bots is not sixteen-client networking evidence.

## 10. Work concurrency and reproducibility for Astra/Fable execution

One integration owner controls the main checkout, `.uproject`, target/build files, shared config, gameplay tags, asset registry paths and accepted interfaces. Other agents work in isolated source branches/worktrees or nonoverlapping assigned paths. No two editor processes write the same `.uasset`, `.umap`, config or generated manifest. Binary asset merge conflicts require choosing/rebuilding one coherent asset, not textual conflict markers.

Before parallel implementation, freeze the class/interface names in the main spec and reviewed implementation decisions, state ownership, gameplay units, collision channels, input action names, asset naming manifest, network generations and acceptance test IDs. This appendix supplies refinement ideas only. A contract change lands before dependent consumers. Agents may propose changes, but cannot silently create parallel health/seat/weapon systems because an interface was inconvenient.

The Unreal editor automation/MCP connection has one active writer at a time. A tool action that reports success must be followed by observed asset/state inspection, save, and where appropriate a restart/reload verification. C++ header/UHT/constructor changes require a clean editor-close rebuild when Live Coding cannot safely represent the change. A live-patched editor state is not the final reproducibility baseline.

Suggested execution division:

| Workstream | Independent ownership | Integration dependency |
| --- | --- | --- |
| Runtime foundation | Character, controller, state and match skeleton | Establishes contracts first |
| Vehicle risk package | Drive adapter, seats, test lane, net scenarios | Can proceed with minimal mock character/weapon interface after contracts freeze |
| Combat package | Inventory/weapon/damage and unit/authority tests | Uses character/seat muzzle interface, not vehicle internals |
| Content/UI | Map greybox, meshes, HUD/settings and input | Uses agreed manifest; one editor writer |
| Verification | Gauntlet nodes, run manifests, impairment and perf capture | Orchestrates real builds from integration revision |
| Fable review | Adversarial read/reproduce/report | Reviews concrete revision and evidence; does not replace failing gates with prose |

The user intends a later Fable review; no such review is claimed here. A useful reviewer packet includes the main spec, this appendix, exact implementation diff, toolchain lock, unfiltered test status, failed spike evidence, and short video of two real players using the transport. Review should challenge impossible acceptance claims, hidden client authority, possession races, stale-life requests, untested Mac rendering, asset licensing/provenance and unverifiable one-shot completion language.

## 11. Release-readiness boundary

The realistic one-shot outcome is a complete, reproducible first slice whose major risks were gated in order, with a playable packaged build for both requested desktop platforms and honest results for the dedicated server. It is not a finished commercial multiplayer service.

The slice is incomplete if the transport only works in standalone mode, passenger shooting is missing, Mac is deferred without an explicit revised decision, Windows was never packaged, the server relies on editor-only modules, the tests coordinate only one process, or critical acceptance cells remain unrun while the report says all done.

Public release requires additional scope: source/asset provenance and final original branding review, signing/notarization and distribution packaging, external playtesting, internet lag compensation/fairness, abuse controls, auth/hosting/operations, crash recovery/telemetry, accessibility QA, content breadth, and measured commercial performance requirements. These are separate packages with their own acceptance evidence.
