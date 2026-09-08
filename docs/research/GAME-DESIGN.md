# Mobile Forces: reference mechanics and the selected modern game

Research and design revision: 2026-09-07. **[SPEC.md](../SPEC.md) is the canonical implementation contract.** Its selected controls, numbers, capacities, states, and release gates govern. This research distinguishes documented original mechanics, the owner's confirmed memories and preferences, and original modern design judgments. No original-game executable or new Unreal game was run for this document.

The selected project is an offline single-player game for native Apple Silicon Mac and Windows x64: the human plus seven named bots in 4v4 matches, four vehicle families, an original desert-fort map called **Fort Crossing**, **Holdout as the primary mode**, and **CTF supported**. The owner confirmed **Western** as the remembered reference. Exact original geometry and physics constants remain unmeasured. The superseded network plan is historical material under the archive, not active implementation scope.

## 1. Reference facts and their limits

The publisher-provided manual is the strongest source for original rules. Its instructions establish the following concise anchors:

| Original system | Documented behavior |
|---|---|
| Holdout | Tag a neutral timer; teams accumulate ownership time |
| CTF | Return the enemy flag; scoring requires the home flag to be secure |
| Loadouts | Limited equipment capacity; carrying more reduces running speed |
| Grenades | Charge the throw; timed or impact detonation |
| Trip bombs | Interruptible laser beam; an alternative proximity mode |
| Vehicles | Buggy, Humma, Truck, APC; drivers and armed passengers |
| Damage and return | Shot-out tires affect control; empty vehicles eventually respawn |
| Team bots | Orders, boarding, driving, and passenger combat |

[Original manual, printed pp. 6–15](https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/837940/manuals/Manual.pdf?t=1528450785).

The store lists eight game types and separately describes Missions and Skirmish, but its abbreviated Holdout description is less reliable than the manual's explicit timer rule. Feature badges are not proof of tested modern compatibility. [Publisher Steam listing](https://store.steampowered.com/app/837940/Mobile_Forces/).

Contemporary hands-on coverage independently describes short explosive drops, powerful throws, powerslides, and ramp jumps over a defended fort. The March 2002 Shacknews demo article names Western and its central Holdout contest. These are useful observations of early builds, not exact release-code measurements. [GameSpot hands-on](https://www.gamespot.com/articles/hands-onmobile-forces/1100-2855996/), [Shacknews demo impressions](https://www.shacknews.com/article/19167/mobile-forces-demo-impressions).

## 2. The experience to preserve

The owner's priority is unusually specific: arcade accessibility with an exceptionally satisfying physical sense of driving. Preserve readable momentum, suspension, traction, airtime, landing, and recovery while permitting audacious maneuvers. Photorealism or mechanically realistic simulation is not the measure of success.

This interpretation has historical support. Contemporary critics recognized that exaggerated vehicle physics could complement fast action, and players describe memorable rides, attacks, jumps, and transport teamwork. Their judgments about weapons, bots, and longevity vary. The audience evidence is documented separately, with dates and sampling limitations, in [PLAYER-EXPERIENCE.md](PLAYER-EXPERIENCE.md).

The first question for a playable prototype is whether driving is enjoyable without a match objective. The second is whether the same handling creates useful and surprising choices under pressure. The third is whether bots can express recognizable styles through those shared mechanics. Reversing that order risks building impressive AI around an unsatisfying game.

## 3. Four vehicle families with different jobs

| Selected family | Intended modern role | Qualification focus |
|---|---|---|
| Dune buggy | Responsive, exposed, adventurous transport and jumps | Readable suspension, approach control, useful passenger sightlines |
| Hummer-style utility vehicle | Versatile transport and rough-terrain access | Distinct mass, traction, pickup usefulness, damage response |
| Truck | Group movement and forceful arrivals | Broad turning, momentum, passenger space, constrained-route recovery |
| Dirtbike | New nimble and exposed traversal option | Two-wheel control, balance, landing, fall/recovery, route access |

The dirtbike is an original addition requested for this project. Do not describe it as part of the original roster or quietly substitute an APC for it. Use original vehicle designs rather than treating a real manufacturer's visual identity as a ready-made asset specification. Exact seats, speeds, mass, damage, stability assistance, and spawn allocation are owned by SPEC.

Vehicle families must differ in behavior. Tune the human controls through the same handling course, then compare which lines and maneuvers each supports. A truck should communicate why a buggy's shortcut may be inappropriate. A dirtbike must remain a credible two-wheel vehicle even if deliberate assistance makes it accessible.

Stunts use the real movement system. Authored opportunities can describe approach and landing requirements, but they must not teleport, hide collision, or add a bot-only impulse. Test ordinary corrections and failures alongside successful spectacular runs. A vehicle that always lands perfectly can be as unsatisfying as one that overturns unpredictably.

## 4. Tire damage is a tactical change, not decoration

The owner explicitly requires tire shootouts to degrade actual steering and traction. Track damage per relevant wheel and feed it into the same control/physics path used by humans and bots. Visual damage and interface feedback should explain a change the driver can already feel.

A partially impaired vehicle may still limp, turn differently, or require a different route. Sufficient impairment changes the task: abandon, continue on foot, seek another ride, or finish a short nearby objective under the rules selected in SPEC. The bot must not regain normal grip through a separate idealized path follower.

Exercise this entire lifecycle: healthy driving, one or more punctures, changed maneuver capability, stopping or abandoning, destruction where applicable, retirement, and replacement at the proper spawn. Replacement must not duplicate an occupied vehicle, overlap actors, strand seat ownership, or erase a carried flag. Blocked spawn space requires a bounded retry policy, not a vehicle spawned through somebody.

Destruction and abandonment should return useful transport to circulation without allowing instant mid-combat repairs. Their precise timers and conditions are game rules; choose them once in SPEC and validate full matches. An engine tire-damage demonstration does not prove those lifecycle transitions.

## 5. Passenger combat is part of the handling brief

A ride should let the human contribute. Cameras, seats, firing arcs, boarding, dismount, and weapon restrictions need to work together. Shake or vehicle motion can communicate speed without preventing aim or concealing every threat. Occupants need understandable exposure and exit opportunities.

Test the human driving with bot passengers, then the human riding with bot drivers. Include pickup requests, changing destinations, damaged handling, unexpected obstructions, death, and rematch cleanup. Merely attaching a pawn to a seat proves very little about the actual experience.

An exciting insertion should end somewhere useful. hotlap's jump, farsight's relocation, and ramrod's attack should feel different even with identical cameras and no dialogue. A driver who constantly sacrifices the passenger to advertise recklessness will stop being a welcome teammate.

## 6. Fort Crossing gives mobility a purpose

Western's identification is settled by the owner's confirmation. Fort Crossing is an original map built around the remembered relationship: two opposing approaches, a prominent desert fort, and a central fight worth racing toward. The original geometry is not an asset or coordinate source to reproduce blindly.

Connect vehicle roads, off-road opportunities, infantry approaches, elevated positions, and constrained entrances. Provide several reasons to choose a route: speed, cover, surprise, a better landing, a sniper angle, or avoidance of known mines. Terrain should suggest experiments while remaining readable enough that failures teach something.

Measure complete journeys, including boarding, acceleration, turns, dismount, and the last approach. Compare equivalent trips on foot and in each vehicle. Top speed alone does not establish useful mobility. Conversely, a short protected foot route that always wins makes transport incidental.

Holdout must offer more than a single unanswerable defensive perch. CTF needs plausible escape, pursuit, and interception routes. lattice's preparations and farsight's sightlines should create problems with alternatives. No position should combine perfect observation of every approach, permanent safety, and objective control.

## 7. Two modes, coherent rules

Holdout is the first complete match because it directly matches the owner's memory and concentrates the cast around an understandable contest. Establish the selected timer interaction, ownership changes, contested cases, cumulative scoring, deadlines, and tie outcomes in SPEC. Do not accidentally replace the chosen tag-and-ownership design with a generic standing-in-a-volume rule because an engine sample provides one.

CTF broadens the same map's interactions: stealing, escorting, pursuing, intercepting, defending, dropping, recovering, and returning. Its flag lifecycle must remain coherent through seats, death, falls, blocked exits, and vehicle replacement. Public objective information and a bot's private enemy knowledge are separate contracts.

The full accepted build supports both modes with the same roster. Build order may prove Holdout first, but an early Holdout-only milestone must be labeled accordingly. Trailer is an informative historical example of vehicle-centered objectives; its audience appeal does not automatically add towing or another mode to this scope.

## 8. Expressive weapons and recognizable people

The selected grenade supports close drops and charged throws, with timed and impact modes. arcl1ght's identity comes from exceptional estimation, timing, and physical execution using those controls. He can make extraordinary long throws within the valid weapon envelope. He cannot adjust the projectile toward a hidden target after release or obtain different physics from the human.

Laser mines must be visible, bounded, and causally understandable. lattice should anticipate likely routes, prepare them, and exploit the result. Mine stock, placement restrictions, beam checks, triggering, owner/team behavior, destruction, cleanup, and active limits need one explicit contract. Do not import original proximity or vehicle-attachment features unless SPEC selects them.

Machine guns and sniper equipment need situations where their handling and tactical value matter. The infantry kit must support the personalities rather than funneling every competent actor into one universally best weapon. Loadout burden should make transport useful without making a character's specialty a punishment.

| Bot | Behavioral identity to establish |
|---|---|
| arcl1ght | Remarkable grenade drops/throws and target prediction |
| hotlap | Ambitious off-road lines, ramps, and unusual insertions |
| stitch3r | Machine-gun handling, sustained pressure, and good firing positions |
| farsight | Sniping linked to purposeful driving and relocation |
| b0bbin | Weaker, inconsistent play with occasional legitimate surprises and individual humor |
| ramrod | Aggressive driving into forceful machine-gun attacks |
| lattice | Habitual laser-mine preparation and route denial |

The roster is exactly these seven plus the human. b0bbin's Russian accent is an individual presentation preference, independent of skill. Voices and names must not be the only way to distinguish the cast.

## 9. Evidence before expansion

The recommended implementation sequence is a qualified native package, enjoyable human driving, physical damage/lifecycle, useful passenger play, expressive weapon fixtures, a complete Holdout match, CTF, and repeated full-roster evaluation. Work can overlap where the interfaces are stable, but acceptance should follow actual evidence.

Native gameplay logic, fair perception, private beliefs, utility choices, and robust tactical execution form the initial bot foundation. Optional learned motor policies can improve a measured weak point later. The relevant research and its limitations are in [HUMANLIKE-BOTS.md](HUMANLIKE-BOTS.md); neither a model's reputation nor an attractive editor recording certifies human likeness.

Mac and Windows remain first-class packaged targets. Verify equivalent gameplay, input behavior, timing, and performance envelopes on actual supported machines. Development automation and any reference-project supporting services belong to the build/review workflow; an ordinary offline match must not depend on editor MCP, cloud inference, or a development service remaining online.

The specification should make an initial integrated build reproducible and reviewable. Handling feel, fair difficulty, and long-session personality still require revision informed by human play. Report what passed, what was observed, and what remains open using [ACCEPTANCE.md](../ACCEPTANCE.md) and [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md), without relabeling a partial prototype as the finished game.
