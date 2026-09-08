# Engine architecture research: offline cast and physical play

Research revision v0.2, 2026-09-07. This appendix supports the current offline specification. It replaces the multiplayer architecture retained only in the [archived v0.1 package](../archive/v0.1-network-plan/README.md). No engine build, vehicle experiment or packaged game was run for this revision. [SPEC.md](../SPEC.md), [BOT-CAST.md](../BOT-CAST.md), [ACCEPTANCE.md](../ACCEPTANCE.md) and [DAY1-ENGINEERING.md](../DAY1-ENGINEERING.md) own requirements; implementation names below describe responsibilities, not additional competing interfaces.

## 1. Foundation and scope

Use the provisionally selected **UE 5.8.2**, a small C++ gameplay project, native editor automation, and separate native Mac/Windows packages. Epic published the 5.8.2 hotfix; the version is a starting pin, not evidence that this project or every plugin works on it. Lock the actual distribution/build/toolchain and qualify it before generating substantial content. [Epic hotfix announcement](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335)

The complete slice is one human plus seven named bots in offline 4v4, four vehicle types, the original **Fort Crossing** desert/central-fort map, primary Holdout and secondary CTF. Build gameplay for one local simulation. Installed engine distributions are the default candidate; there is no mandatory source-engine build. All runtime decisions, bot control and packaged content must function without a developer agent or editor connection.

Start with `ACharacter`/Character Movement for infantry, Enhanced Input for local control, UMG for menus/HUD, explicit weapon/health/inventory components, and Data Assets for profiles/tuning. Keep C++ responsible for shared rules and state transitions, with Blueprints assembling assets and presentation. This limits the amount of opaque graph logic an agent must rewrite and makes the same actions available to the human and bots.

Lyra remains a useful reference for modular shooter code, settings, animation and equipment, but its experiences, feature-plugin activation and customized Gameplay Ability System add integration work. For this narrower offline game, adopting the whole sample is not the default. Do not partially import its ability lifecycle into otherwise independent pawn/vehicle logic. [Epic Lyra architecture](https://dev.epicgames.com/documentation/unreal-engine/lyra-sample-game-in-unreal-engine?lang=en-US)

GAS is unnecessary for the presently bounded inventory, damage and cooldown rules. Gameplay Tags can still name state and equipment. Reconsider a larger framework only after a concrete feature demonstrates the need; there must remain one health/ammo authority.

## 2. Human vehicle feel is the first physical qualification

A convincing `hotlap` cannot rescue vehicles that are unpleasant for the human to drive. Establish the human handling course and camera first, then expose the same steering/throttle/brake/handbrake inputs to the bot controller. The required handling should reward momentum, rough-terrain lines, powerslides, ramps and recoverable landings while remaining readable. Exact tuning belongs in SPEC; this appendix adds no substitute speed, grip or assist thresholds.

Conventional Chaos Vehicles is the initial candidate behind a narrow drive adapter. Epic calls it a lightweight vehicle-physics system, while its plugin index still labels it Experimental. Qualify handling, damage and reset behavior rather than inferring maturity from its name. [Chaos Vehicles overview](https://dev.epicgames.com/documentation/en-us/unreal-engine/chaos-vehicles), [plugin status](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/ChaosVehiclesPlugin)

| Required vehicle | Seats including driver | Distinct qualification |
|---|---:|---|
| Dune buggy | 2 | Light off-road response, exposed suspension, passenger view/fire, ramp recovery |
| Hummer-style utility vehicle | 4 | Stable group transport, occupied-seat visibility, distinct weight/turning response |
| Truck | 5 | Six physical wheels, heavier mass/turning, four passengers, seating/egress without overlap |
| Dirtbike | 1 | Two-wheel balance, lean, rough-ground stability and landing recovery |

The dirtbike is a separate feasibility spike: a four-wheel tuning preset is not evidence of usable two-wheel dynamics. Any shared arcade stabilization must act consistently for human and bot input, be represented in the handling specification, and preserve contact, momentum and collision. If a candidate fails, measure a bounded alternative through the same human course; do not delete the bike or reduce four vehicles to one.

Damage must change physical handling, not merely a dashboard percentage. Each destroyed tire reduces the relevant contact/grip contribution and affects turning/stability as specified. Engine propulsion is disabled at three destroyed tires out of four on the buggy/utility, four out of six on the truck, and two out of two on the bike. Existing velocity, gravity and collisions can still move the vehicle: disable drive torque, not world velocity. Verify zero-, partial- and threshold-damage configurations, including engine-off coasting downhill. Define occupied wreck and abandoned-vehicle cleanup through the main lifecycle rules; retired vehicles return through their base respawn path without stale occupants, old timers or duplicated replacements.

Author rigs to the actual engine contract: wheel/root orientation, wheel centers/radii, skeleton hierarchy, chassis collision, mass distribution, suspension travel and tire visuals. Epic's art guide specifies X-forward/Z-up joint alignment and emphasizes wheel placement. A mesh that imports successfully does not prove a useful physical rig. Validate each vehicle after cooking, including wheel damage visuals and steering animation. [Epic vehicle art setup](https://dev.epicgames.com/documentation/en-us/unreal-engine/vehicle-art-setup-in-unreal-engine)

## 3. Shared runtime authority and lifecycle

Keep the simulation's truth in bounded gameplay components, separate from UI and bot beliefs. Match rules own team membership, phase, score, objectives and resets. Combatant records own the seven fixed identities and the human's identity across pawn death. A life generation distinguishes a new pawn from delayed timers associated with its previous life.

The action interface accepts ordinary intents: move/look, equip/fire/reload, grenade mode/charge/release, place a laser mine, interact, and vehicle inputs. It resolves ammunition, cadence, alive state, movement/seat restrictions and collision through shared rules. A bot never directly edits target health or actor transforms to accomplish an action. Route human and AI controllers through that interface, with bot steering and aim outputs subject to their defined control limitations.

Seat transitions retain explicit occupant, body, controller and vehicle references. Entry, exit, death, vehicle destruction and round reset each have one terminal cleanup path. Occupants remain hittable according to the main spec even if a driver possesses a vehicle pawn; damage must not accidentally address only the controlled pawn. Passenger firing derives its muzzle and allowed view from the current seat/world pose. Blocked exits stay blocked with a clear cue. Validate all seats on all four vehicles, including a passenger-to-driver change and a vehicle recycled while an old callback remains pending.

Holdout and CTF use separate rule objects sharing combat, spawns and vehicles. UI derives objective progress from rule state. Pause, results, restart and mode changes cancel outstanding objective holds, reloads, throw preparation, mines/projectiles and vehicle operations according to their intended lifetime. Save settings and approved long-term preferences, not arbitrary live actor pointers. Do not smuggle later campaign persistence into the first slice.

## 4. A fair bot stack with room for distinctive skill

Use a hierarchy of filtered perception, remembered beliefs, tactical choice, maneuver/skill execution and shared actions. AI Perception supplies sight/hearing-style events, but the project's observation adapter must decide which data is admissible and how it ages. Merely adding the component does not prevent other code from reading an unseen opponent. [Epic AI Perception](https://dev.epicgames.com/documentation/en-us/unreal-engine/ai-perception-in-unreal-engine)

Use one readable tactical controller, optionally authored with StateTree, plus small explicit skill controllers. StateTree provides hierarchical state organization; it does not supply believable tactics or character identity automatically. Avoid duplicating decisions across a Behavior Tree, StateTree, utility system and unrestricted language model. [Epic StateTree](https://dev.epicgames.com/documentation/en-us/unreal-engine/state-tree-in-unreal-engine)

The canonical profiles are `arcl1ght`, `hotlap`, `stitch3r`, `farsight`, `b0bbin`, `ramrod` and `lattice`. Their specializations must change choices and execution, not just accuracy. Infantry navigation needs cover/approach options; creative driving needs a vehicle traversal graph and maneuver candidates that include physically viable off-road lines. Familiar map knowledge may be authored, while target knowledge remains perception-bound. A traversal candidate identifies a feasible technique; the bot still drives it through normal controls and can fail.

Learned local motor policies are an optional escalation for a demonstrated weakness, particularly driving or practiced throws. Epic's Learning Agents API exposes observation/action schemas, controllers and policies in an Experimental plugin. That is an integration option, not a ready-made human imitation system. [Epic Learning Agents API](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/LearningAgents)

Qualify training and runtime inference separately. A training tool running on one host does not prove the packaged policy works on the other. NNE is Beta; Epic explicitly notes that runtimes differ by platform and that successful model import does not guarantee successful model creation. Test the actual policy, tensor schema, runtime, package inclusion and execution cost on each target. Preserve the hand-authored fallback and its distinct evaluation record. [Epic NNE overview](https://dev.epicgames.com/documentation/en-us/unreal-engine/neural-network-engine-overview-in-unreal-engine)

## 5. Content, presentation and verification

Fort Crossing should make ordinary infantry, useful cover, cross-fort attacks, mine placement and unconventional vehicle approaches coexist. The recipe includes geometry/collision, objective/spawn anchors, vehicle bases, traversable landing zones and authored tactical tags. The old game's Western-style setting is a reference for the requested experience; use an original layout and assets.

Use one coherent character skeleton/animation family and a simple readable visual baseline. Explicitly qualify first-person weapon sockets, third-person aim/reload, seat poses, wheel/suspension motion and death transitions. Keep footstep, engine, tire, grenade, mine and weapon sounds distinct enough to support fair perception. Pre-authored character banter is local content with event priorities, repetition limits and subtitles; it cannot stall play or substitute for recognizable behavior.

Cooked references are a major agent failure point. Stable asset IDs and explicit cook inclusion must cover maps, modes, seven profiles, four vehicle classes, animation, audio and UI. Use native asset inspection and fresh packaged launches: editor success may depend on assets that the package never includes. Epic describes cooking as target-specific asset conversion, so native packaging is part of correctness, not a final cosmetic task. [Epic packaging workflow](https://dev.epicgames.com/documentation/en-us/unreal-engine/packaging-your-project)

Use DAY1's separate hermetic, engine, package, rendered and human evidence classes. Measure physical trajectories with recorded input/configuration and bounded outcomes; do not promise bit-identical cross-OS Chaos replay. Ground-truth evaluator data stays inaccessible to bot controllers. Acceptance must include held-out geometry, ordinary failed maneuvers and full mixed matches, then human assessment of recognizable identities and fun. No count of passing code tests establishes that playing against these bots feels human.
