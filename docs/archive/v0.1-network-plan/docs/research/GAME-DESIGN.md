# Mobile Forces: game research and design rationale

Research date: 2026-09-07. This document records primary-source findings, design reasoning, and review questions. **[SPEC.md](../SPEC.md) is the canonical implementation contract.** Its rules, names, numbers, scope and precedence govern; this research does not create a second set of tunables. All modern proposals are original design choices, not claims about the reference game's exact behavior. No original-game executable or new Unreal game was run for this document.

## 1. What the reference game actually did

The publisher-supplied Steam listing identifies Rage Software as developer, Funbox Media Ltd as current store publisher, and 31 May 2002 as the original release date. It describes AI matches, LAN play, a progression-oriented Missions option, and configurable Skirmish play. Store feature badges should not be treated as a tested compatibility matrix for the original game. [Steam listing, accessed 2026-09-07](https://store.steampowered.com/app/837940/Mobile_Forces/).

### Mode inventory

| Mode | Documented objective |
| --- | --- |
| Deathmatch | Individual elimination scoring. |
| Team Deathmatch | Elimination scoring by team. |
| Captains | Score by eliminating the opposing leader. |
| Capture the Flag | Take the opposing flag home. |
| Detonation | Obtain a neutral keycard and activate the enemy-base console. |
| Safe Cracker | Raid the enemy safe; sides alternate. |
| Trailer | Deliver an explosive vehicle payload into the enemy base. |
| Holdout | See manual correction below. |

The listing establishes the eight-mode inventory, but its short Holdout description conflicts with the manual. [Publisher store description](https://store.steampowered.com/app/837940/Mobile_Forces/).

### Manual corrections and identity-bearing details

The official manual describes Holdout as tagging a neutral timer and accumulating ownership time until the other team switches it. Detonation uses a key, enemy-HQ console, countdown, and team-wide collar explosion; it does not specify a modern defuse rule. Loadout capacity is limited and heavier equipment slows running. Home loadout rooms resupply; equipment can be dropped. Vehicles provide transport and armed passenger positions. Bots accept orders and can board or drive. Vehicle classes are Buggy, Humma, Truck, and APC, with different capacity/protection/mobility. Tires and fuel caps are vulnerable. Weapons have alternate functions. Map descriptions repeatedly combine vehicle roads with infantry shortcuts, cover, and elevations. These are stronger anchors than recreating particular textures or matching screenshots. [Official manual, printed pp. 6–15 and 18–21](https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/837940/manuals/Manual.pdf?t=1528450785).

Historical details still requiring direct original-game observation, if strict fidelity becomes a requirement: exact damage and movement constants, interaction distances, key-return behavior, countdown interruption, physics response, spawn timing, practical original bot reliability, and per-map travel times. This research did not run the 2002 executable. Do not silently fill these gaps from recollection.

### Evidence confidence

| Finding class | Confidence and limit |
| --- | --- |
| Mode inventory and identity | Strong primary-source documentation; enough to guide the concept |
| Holdout discrepancy | Manual is more detailed than the store summary; use it for historical description |
| Exact contemporary compatibility | Not established; store badges and old requirements are insufficient |
| Relative movement/damage feel | Not measured; must not be represented as copied numerical fidelity |
| Original bot quality | Feature existence documented; reliability and fairness need observation |
| Current remake/brand rights | Not investigated; distribution credit does not establish clearance |

## 2. The selected interpretation

Build an original, readable combined-arms arena game: small teams race for a neutral objective, choose a fast or heavily equipped role at spawn, improvise with a transport, and fight through an industrial map where road routes and foot routes intersect. Preserve the sensation of a quick improvised assault and a useful bot squad. Modernization should improve controls, readability, match setup, reliability, and feedback before expanding weapon counts or visual effects.

The selected first deliverable is the 4v4 **Relay Breach** slice on **Quarry Exchange**, with native Apple Silicon Mac and Windows clients, listen play, Windows dedicated authority, and a bounded authored bot shuttle. The full content and platform boundaries are in [SPEC §3](../SPEC.md#3-scope-and-release-levels). Names remain provisional internal labels. The project directory name does not decide the public title.

The credible promise is a complete playable match with the distinctive transport/objective loop, original blockout content, bots, native packages on both desktop platforms, and real multiplayer evidence. A commercial-quality remake with many maps, complete animation sets, polished online systems, and a mature asset pipeline is a later production program. One coherent agent build means one reviewed contract executed through compilation, editor work, testing, and fixes. A single response that produces untested source files is not that outcome.

### The decisions each match should create

1. Travel quickly on foot with the light kit, or accept slower movement for anti-vehicle capability.
2. Take the fast road with an exposed vehicle, or the covered pedestrian approach.
3. Carry the core, escort its carrier, or prepare an interception.
4. Commit to the enemy terminal, or return to defend when possession changes.
5. Stop to collect a teammate, or attack immediately and lose passenger support.

These are design hypotheses. Passing rules tests does not prove that the choices are useful or fun. During playtests, record what players actually choose and why. If everyone always picks the heavy kit, drives alone, or ignores the core, change the incentives or map before adding content.

## 3. Why this mode comes first

| Candidate | Strength for this project | First-build cost or weakness | Decision |
| --- | --- | --- | --- |
| Neutral core to enemy terminal | Concentrates contact; creates carrier, escort and interception roles; connects transport to attack | Needs careful objective lifecycle and bot understanding | Selected as original Relay Breach |
| Team deathmatch | Quick weapon/navigation test | Does not prove transport/objective identity alone | Internal fixture; later player mode |
| Two-flag capture | Familiar retrieval/escort loop | Two carriers and home-flag dependencies add state and strategy | Later alternative |
| Neutral zone control | Clear location contest and defender roles | Can become static infantry combat with vehicles only for arrival | Later alternative |
| Captain hunt | Moving defense objective | Leader assignment, visibility, disconnect and fairness need separate work | Later alternative |
| Timed robbery assault | Clear asymmetric pacing and side swaps | Safe damage, loot lifecycle and side-swap fairness multiply cases | Later alternative |
| Explosive trailer/convoy | Strong direct vehicle objective | Towing physics and bot vehicle strategy add major early risk | Defer until transport works |

Relay Breach adds explicit install, defuse, timeout, and reset rules. The install/disarm duel gives defenders an understandable response after losing the core and provides an audible/visible climax. It also creates implementation risks: cancellation, deadline ties, replication and after-time resolution. Those risks are worth addressing in one mode; they are not a reason to implement seven others simultaneously.

The selected match rules are exclusively in [SPEC §8](../SPEC.md#8-relay-breach-rules). The arm countdown, defuse duration, regulation limit, and exact-deadline outcome come from that section. An earlier research alternative used longer defuse/countdown values and defender-favored ties; **that alternative was not selected**. Do not copy tunables from an earlier draft, agent message, or reference-game recollection.

### Public objective knowledge and denial

The selected design distinguishes a continuously known friendly carrier from a periodically updated public core marker, with visible staleness. This makes objective information understandable while retaining interception uncertainty. A bot may use the same public information, but should not query a hidden current enemy carrier transform every frame. A stale marker visually interpolated toward the exact enemy position would defeat its stated information policy.

The selected possession lease prevents keepaway. Same-team drop/pickup does not renew the window; losing it to the other team is a meaningful new possession. The lease must survive seats, death/drop recovery and ordinary actor replacement, and stop when installation completes. Its canonical duration and transitions are in SPEC. Test deliberate delay tactics instead of assuming objectives will progress voluntarily.

## 4. Scope that preserves identity

A generic infantry demo is an intermediate milestone. First-slice acceptance requires driveable transport, personal-weapon passenger fire, objective-aware infantry bots, the selected bounded bot driving route, a full match lifecycle, and native mixed-platform networking. If these fail, retain the useful intermediate result and label it accurately. Do not silently remove one and claim the original brief is complete.

The transport archetype has two jobs. A human can drive while another participant shoots; a bot can use an authored shuttle route to provide bounded transport. These share seat services, damage rules, weapon restrictions, and exit checks. General-purpose wheeled navigation is not selected: the bot route may stop, back off, and dismount when obstructed instead of solving arbitrary driving problems.

A passenger-only bot prototype was considered because it is cheaper, but it is **not the selected complete first-slice bot scope**. Likewise, a cinematic spline vehicle with no real player driving, collisions, damage, or possession does not satisfy the transport requirement. The shuttle uses authored route intent; it still drives the gameplay vehicle through its actual movement path.

### Distinctive features deferred with intent

Tire damage, fuel weak points, trailers, APC turrets, mines, deployed weapons, armor and free-form loadouts offer tactical depth. Each expands interaction combinations: an impaired car with a core carrier, a mine on a moving object, a turret passenger during network corrections, or a bot choosing inventory subsets. Their absence is a scope decision. Introduce them later with dedicated rules and tests.

Eight participants make role coordination observable without making population scaling a concurrent unknown. Moving to 8v8 later changes cover pressure, vehicle queues, objective survival, replication load, bot CPU, and spawn safety. Preserve data-driven capacities and measure the larger configuration before calling it supported.

## 5. Infantry, weapons and loadout rationale

Use [SPEC §6](../SPEC.md#6-infantry-and-loadouts) for every selected movement speed, weapon value, mass contribution, health rule, input request and state transition. Scout has rifle/pistol; Demolition has rifle/launcher. The core contributes burden. Earlier ideas giving the heavy kit all three weapons or making the core weightless were **not selected**.

The tradeoff should be apparent at selection and during a run. Showing mass alone is insufficient: explain the resulting movement change, update it on core acquisition, and ensure authority uses the same calculation as the UI. Fixed mass through a life avoids changing prediction and balance on every shot; separate carrier mass creates an intentional temporary change.

The chosen rifle seed gives fast arcade lethality. This can make exposed passengers and objective channels fragile. Evaluate human encounters and defended installation attempts before changing values. If attempts fail because defenders have perfect uninterrupted sightlines, adjust the map before raising health. If players die before identifying shot direction, review feedback, cover and reaction time rather than blindly shortening respawn.

The rocket should make the slow kit meaningfully dangerous to transport. Canonical vehicle health and direct rocket damage imply two full direct impacts to destroy an undamaged transport; this is arithmetic from selected seeds, not historical fidelity. Validate that direct and radial paths do not both damage the same target and that world walls occlude blasts. Otherwise a balanced shooting-range demonstration may conceal duplicated network damage.

Passenger restrictions define roles: driver drives; rifle/pistol passenger covers the approach; rockets require dismount. Demolition's missing pistol therefore matters while seated. Explain restrictions instead of silently ignoring input. Apply SPEC's single damage-rounding convention consistently so authority, remote feedback and test expectations agree.

Self-rocket damage and no teammate damage are selected. Ramming damage is excluded. Roadkill and no self-damage were earlier alternatives and must not enter through raw physics callbacks. Collision still has to push/block safely and cannot permanently pin pedestrians in critical objective space.

Reloading, firing, switching, carrying and seats form a connected lifecycle. Animation is secondary to a correct transition: canceled reload cannot grant ammo; exit cannot unexpectedly restore held fire; hit markers must distinguish predicted shots from confirmed damage. Convincing weapons in one editor viewport do not establish these behaviors.

## 6. Transport usability and counterplay

Selected speed, health, entry/exit limits, firing arcs, flip recovery, abandonment and replacement rules are in [SPEC §7](../SPEC.md#7-vehicles-and-seats). Cameras are first-person infantry, third-person driver and first-person passenger. Manual seat swaps are excluded; exit and re-enter. Do not reintroduce another camera or seat model from research notes.

Transport should save enough time to justify finding, boarding and maneuvering it, while creating exposure risk. A top-speed comparison with infantry is insufficient if corners, boarding and dismount erase the advantage. Time a complete spawn-to-useful-position trip under comparable foot and vehicle conditions.

Exposed occupants create counterplay distinct from chassis destruction, but bodywork must explain what can be hit. Bullets must not damage an invisible standing capsule left at entry or pass through a solid chassis because seated collision was disabled broadly. Separate walking collision, hurt geometry, weapon obstruction and camera obstruction. Inspect this from the driver's and remote opponent's views.

Blocked exits should retain a controllable seated actor and show a reason, not leave an invisible pawn inside a wall. Driver death/disconnect must neutralize input promptly. A surviving passenger does not become driver implicitly. Flip recovery must follow authority and cannot move a carried core through collision.

Abandonment deserves the same scrutiny as destruction. The selected return policy includes damaged vehicles; excluding them would let one incidental hit remove transport from circulation indefinitely. Nearby enemies can suppress return, so this denial mechanic still needs intentional counterplay. Blocked pads defer replacement instead of spawning on players; repeated deferral in a clean test indicates a defect.

Transport validation needs remote authority and moving passengers. A parked car with enter/exit prompts does not prove driving, collision, passenger fire, death, rejoin, or match completion. Useful recordings combine driver and passenger viewpoints with authoritative seat/damage observations rather than only a flythrough.

## 7. Map composition and pacing

**Quarry Exchange** is selected. Coordinates, widths, elevation limits, cover seeds, timing targets and symmetry tolerance are in [SPEC §10](../SPEC.md#10-map-recipe-quarry-exchange). The earlier name Switchyard and alternate placements/widths were **not selected**. Use canonical metre coordinates and convert once to Unreal centimetres.

Three route families should interact: broad roads for travel, covered foot paths for approach, and short links where passengers dismount and infantry threaten vehicles. Each must sometimes be preferable. A road that is fastest and safe to the terminal makes infantry a temporary seated mode; a foot path that is safer and almost as fast makes cars scenery.

### Spatial principles

- Match functional travel times before asymmetric decoration. Different base signage should not conceal a shorter covered plant route.
- Make final objective access pedestrian-only with visible geometry. A vehicle must not fit because collision is narrower than its mesh.
- Avoid one elevated position seeing the core, both roads and a spawn exit. Strong positions need alternative approaches and readable counters.
- Put cover around the core courtyard without trapping the pickup. Check capsule clearance and splash at the actual object location.
- Treat intersections and dismount bays as deliberate encounters. First transport proof should not require water, stairs, elevators or jumps.
- Provide cover useful to a newly arriving player. Featureless runs demonstrate size while reducing meaningful play per life.
- Separate spawns physically and visually from firing lanes. An invulnerability timer alone cannot solve repeated camping or blocked exits.

### Timing observations

Measure spawn-to-contact, spawn-to-central-dismount, core-to-terminal and death-to-defense for both teams. Include boarding/acceleration and actual collision. Record kit, carrier state, path and road occupancy. Report minimum and typical time; a single best racing line is not the default experience.

Assess the burden and possession lease against a slow ordinary assault, including brief cover stops and installation. Too short a lease becomes a hidden speed-class restriction; too long permits stalling for much of regulation. Review possession histories and keep the value in the canonical match data.

Art follows the functional map proof. Imported props can add unexpected collision and narrow navigation gaps. After an art pass, repeat routes, exits and projectile-obstruction checks. Keep generated gameplay geometry separate from manual decoration so regeneration remains safe.

## 8. Bots that make the game playable alone

Canonical roles, perception, reaction timing, route driving and recovery are in [SPEC §11](../SPEC.md#11-bots). Ordinary game AI is selected. Runtime LLM calls add dependency, latency, nondeterminism and cost without helping the immediate aiming/navigation decisions required here.

Roles are intentions rather than restrictions. Defenders can pick up dropped cores; escorts can defuse if needed. If a human carries, bots should support instead of competing for acquisition. Reevaluate on meaningful state changes with enough commitment to prevent constant reversal.

Completing an interaction under pressure is a difficult behavioral gate. A carrier can reach the terminal yet oscillate out of range, making every subsystem look functional while matches never score. Log cancellation reason, position and state. Investigate path endpoints, LOS, action conflicts and threat priorities before increasing accuracy to force results.

Perception fairness is more than reaction delay. Test cover occlusion, expired last-known positions, head snaps, and world-obstructed shots. Bot objective knowledge must match public snapshots; a stale human marker does not justify perfect current carrier knowledge.

Exercise the shuttle as a complete loop: legal driver possession, route departure, passenger boarding/fire where selected, safe stop, dismount/resumed infantry role, and return. Obstacles should cause bounded stop/backoff and safe failure, not force the vehicle through or consume its driver forever. Release reservations when an actor dies or changes task.

Stuck recovery contains faults; it is not success. It cannot teleport the core into a terminal, score, restore an occupied seat twice, or conceal repeated route defects. Report frequency. A clean authored route should require none; a noisy match can expose issues worth fixing instead of loosening thresholds.

Unattended bot diagnostics need a harness consistent with the no-human server lifecycle. Keep a connected observer or explicitly record a test-only override when normal authority returns to waiting after humans leave. Do not silently disable normal empty-server behavior to pass a bot test.

## 9. Modern presentation and UX

Selected controls, settings, camera convention, UI ranges and sound floor are in [SPEC §13](../SPEC.md#13-presentation-controls-and-accessibility). Microsoft's Xbox Accessibility Guidelines are useful game-specific references for text, non-color cues, input and motion. They describe best practices rather than a legal compliance checklist. [XAG overview](https://learn.microsoft.com/en-us/xbox/accessibility/guidelines), [motion/camera guidance](https://learn.microsoft.com/en-us/xbox/accessibility/xbox-accessibility-guidelines/117), accessed 2026-09-07.

A new player should understand play without a spoken tutorial from the developer. Change the instruction with possession/armed state; show marker staleness intentionally. Do not show ordinary fire hints in the driver's seat. Explain denied actions: full seat, hostile occupant, blocked exit, excessive distance, unavailable weapon or occupied interaction.

Menus are gameplay infrastructure. Connection failures must restore usable focus and mouse capture. A network menu cannot freeze authority; offline pause must pause gameplay time, not just the visual clock. Held fire/interaction must not continue behind modals accidentally. Test settings persistence and corrupt-load recovery on both native platforms.

Readable industrial art suits the scope: modular concrete/painted metal, few materials, clear machinery silhouettes, restrained dust and one daylight setup. Team identity needs shape/text as well as color. Wheels, seated poses and weapon direction matter before decorative density. Simplified placeholders can meet these goals; standing bodies through the roof cannot.

Audio confirms accepted events and spatial context. Separate local firing feedback from confirmed damage. Engine sound responds to motion and stops on destruction. Objective cues should be distinguishable and captioned where appropriate. Pooled audio/VFX reset across rematches. Original or licensed placeholders suffice; reference-game audio reuse is not selected.

## 10. Evidence that tests the design

The normative matrix is [ACCEPTANCE.md](../ACCEPTANCE.md), interpreted under SPEC. The following explains why evidence matters; it is not another conflicting gate list.

| Evidence | Design question |
| --- | --- |
| Native offline match | Can someone play without friends, editor or services? |
| Mixed clients on dedicated authority | Are platform parity and remote ownership real? |
| Carrier seats/death/disconnect | Is the distinctive loop safe across lifecycle boundaries? |
| Passenger shooting during movement | Does transport provide useful cooperative combat? |
| Shuttle with an obstruction | Is bounded driving honest about supported behavior? |
| Bot matches with transition logs | Can objectives progress without scripted success? |
| Timed foot/road routes | Is driving a meaningful choice? |
| Deadline tests with overshooting steps | Do rules, rather than frame rate, decide ties? |
| Repeated rematches/join/leave | Does ownership reset instead of accumulating defects? |
| Unfamiliar-player observation | Can people understand and use intended mechanics? |

Multiple fixed seeds provide better bot diagnostics than one favorable video. Record possession, canceled channels, no-progress intervals, roles, scores and recoveries. Bots win by legal gameplay; tests do not directly set scores. Seeds reproduce setup, while physics/scheduling can still require outcome ranges instead of identical trajectories.

Ask an unfamiliar observer to launch, explain the objective, choose a kit, board/exit, and attempt a defended installation. Observe before coaching. Report participant count and concrete confusion. This is limited usability evidence, not market validation or statistical balance.

## 11. Roadmap rationale

The canonical roadmap is [SPEC §17](../SPEC.md#17-roadmap-after-the-slice). Expand after current interaction combinations stabilize. New vehicles should create counters rather than merely change meshes. New modes need explicit rules and bots. Larger teams need performance and spawn-pressure evidence. Public wider-latency play needs hit-registration and operations decisions beyond trusted-network acceptance.

A larger content list is not necessarily progress. Another rifle cannot repair passengers; a second map doubles routes to maintain before navigation is reliable. Production estimates should use observed asset throughput and repair effort. Model capability and detailed prompts do not replace these observations.

## 12. Originality, naming and asset provenance

Default to an original spiritual successor. This research did not verify rights to use the Mobile Forces brand, reproduce maps/art/audio, or distribute a derivative remake. A publisher credit identifies distribution, not a complete chain of title.

The U.S. Copyright Office distinguishes ideas, systems and methods from protected expression and notes that names may raise trademark questions. The USPTO describes confusion involving related goods and similar marks. These general sources support original code, art, map composition, text and branding; they do not clear this project or every jurisdiction. Obtain an actual rights arrangement if a branded remake is desired before public use. [Copyright Office FAQ](https://www.copyright.gov/help/faq/faq-protect.html), [USPTO likelihood of confusion](https://www.uspto.gov/trademarks/search/likelihood-confusion), accessed 2026-09-07.

For each external asset record source URL, creator, version, acquisition date, license and captured license location, permitted use, redistribution limits, modifications, and whether raw files may be shared with agents/reviewers. Buying the reference game does not establish permission to extract assets. Free, Epic sample, Fab, or generated labels do not establish unrestricted rights. Pin actual licenses at selection.

Concept images can communicate atmosphere and silhouettes. They do not supply rigs, collision, LODs, sockets, animation, audio or multiplayer-ready content. An original simple mesh with correct structure is a better first vehicle than an attractive undriveable render. Align public presentation and reproducibility with actual asset provenance.

## 13. Fable review focus

1. Does the map make infantry and transport interdependent?
2. Do burden, lease, lethality and route times permit contested attacks by both kits?
3. Does the rocket/vehicle balance create counters without making vehicles pointless?
4. Can spawning, abandonment, recovery or core denial cause permanent stalls?
5. Do interaction deadlines work under simulation-step overshoot and exact ties?
6. Can death/disconnect/join/seats duplicate or orphan core, point, pawn or reservation?
7. Is the authored shuttle bounded enough to build while remaining a real gameplay vehicle?
8. Do native package, dedicated authority and passenger gates expose failure before art production?
9. Are chosen values sourced consistently from SPEC and alternatives clearly unselected?

Return ranked blockers and concrete corrections before adding features. Accepted changes update SPEC and tests together. Research may discuss alternatives; implementation follows one reviewed contract.
