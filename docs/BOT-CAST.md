# The seven players: character and play-style contracts

Version 0.2. User-confirmed cast: **arcl1ght, hotlap, stitch3r, farsight, b0bbin, ramrod, lattice**. Spell these handles exactly. They are seven distinct persistent bot identities, not randomly named difficulty presets. One human plus this complete cast makes eight combatants. The current game specification is [SPEC.md](SPEC.md).

The descriptions marked **user canon** below are supplied by the user. Specific numeric settings, habits, sample lines and test designs are implementation proposals; they must not be represented as facts about the actual players. Recreating these play styles is the goal. Claiming to reconstruct someone's mind or exact historical behavior is not.

## 1. What every character must share

All seven use the same guns, explosives, vehicle physics, input capabilities, ammo, health, collision and objective rules as the human. They receive filtered observations and maintain beliefs; their decision systems cannot read unseen enemies or future human input. Remembering familiar terrain is allowed. Seeing through opaque cover, getting guaranteed hits through corrected grenade motion, or gaining hidden vehicle traction is not.

Human likeness comes from coherent preferences, skilled execution, attention, commitment, recovery and occasional errors with causes. It does not mean inserting random misses or forcing everyone to have an average kill/death ratio. Specialists must actually be excellent at their specialties. Difficulty may change the selected competence envelope between matches; it must not erase personality or secretly change projectile damage/physics during a match.

Every character needs a stable ID, one profile asset, a play-style description, preferred loadout, skill-specific controller settings, a decision log, a held-out scenario suite and a short set of recognizable example situations. A data/profile revision and motor-policy revision must be included in the build manifest. No duplicate profile may be substituted under a different handle to fill a slot.

### Shared dimensions

| Dimension | What it controls | What it must not secretly control |
|---|---|---|
| Specialty competence | Aim/movement/throw/driving technique in relevant situations | Damage multiplier, ignored collision or extra ammo |
| Risk appetite | Willingness to take a difficult jump or exposed assault | Probability of a secretly forced successful outcome |
| Patience | Time spent gathering evidence, lining up, holding a useful position | Unbounded inactivity or refusal to help |
| Attention | Primary focus, tunnel vision, checks of flanks and teammates | Omniscient targeting |
| Commitment | How long a plan persists before changed evidence causes replanning | Blindly completing a path through new obstacles |
| Adaptation | Bounded response to observed enemy tactics | Access to player input, exact hidden state or evaluation holdouts |
| Social expression | Timing/content of relevant chatter and acknowledgements | Speech replacing missing tactical behavior |

## 2. arcl1ght — the expert grenade thrower

**User canon:** repeatedly uses grenades and is extraordinarily accurate with them, including at extreme distance. In Mobile Forces, grenades can be dropped or thrown very far; impact or nearby explosions can kill or nearly kill. Preserve the skill and power of that play style.

**Signature experience:** the human changes position across the desert, feels temporarily safe behind the distance, then watches a grenade arrive almost exactly where their movement was taking them. At close range arcl1ght can drop an explosive during a retreat or put one through an opening. He is recognizable from range judgment, release timing, target reading and repeated quality throws.

This character is specified as an expert **thrower**, not a puzzle-solving gimmick. His controller outputs normal view direction, throw charge, fuse-mode selection and release/drop inputs. Learn or calibrate a practiced throw technique over distance, height, movement, vehicle velocity and projectile modes. Physics experiments may characterize the common weapon during development; the runtime character should not enumerate thousands of perfect futures or steer an already released grenade toward the victim.

Required behaviors:

- Select grenades frequently when a credible throw opportunity exists. Deliberate range and movement assessment can be quick because this is his expertise.
- Throw accurately over short, medium and extreme legal distances; do not quietly impose a 20–30 m tactical-shooter grenade limit.
- Lead moving targets using observed motion and uncertainty. Make an informed read of a likely strafe/exit; a last-instant unpredictable dodge may still defeat the throw.
- Use impact mode for very long shots where a short timed fuse would burst in flight. Use timed mode and drop tactically where their actual behavior helps.
- Exploit a near hit's lethal blast without requiring direct physical collision with the target. Walls still occlude damage.
- Move between throws, seek a useful release angle and resupply. Do not become a stationary throwing turret or run out of ammunition and cease participating.
- He may attempt a difficult confident throw; do not deliberately degrade the calibrated motor skill to manufacture a universal miss percentage.

Mistakes should come from a bad read, lost sight, an obstructed release, a target changing behavior, or a too-aggressive opportunity—not an unexplained aim randomizer. A visible, correctly executed throw at an inferred empty location is an honest miss; a hidden projectile correction is not expert play.

Evidence: independent tests measure projectile landing/impact residual under fixed target motion separately from whether the target's future behavior was predicted correctly. Compare his throw errors with competent human demonstrations in the project's actual physics. Showcase at least one repeatable throw beyond 150 m and an impact-mode envelope around 180–220 m in the calibrated fixture; these are initial project targets, not measured original-game constants. Include 30 m, 75 m, 125 m and 175 m opportunities, elevated targets, lateral motion, interrupted sight, close drops and blocked launches. Long-range competence must persist outside one memorized throwing spot.

## 3. hotlap — the outrageous stunt driver

**User canon:** a kamikaze-style driver who can use almost anything as a ramp and land in places passengers did not realize they could reach. Ordinary road-following is inadequate.

**Signature experience:** the human climbs into his passenger seat, expects a road approach, and instead gets a fast off-road line over rock/terrain into a surprising but physically reachable fort approach. The landing and recovery are real. He is reckless about the line he chooses, but highly skilled at executing it.

Separate risk from competence. High risk means a narrow landing or exposed shortcut can beat the safer road in his preferences. It does not mean deliberate random crashing, refusing every difficult move, or making the car invulnerable. His signature should survive with speech/nameplate turned off.

Required behaviors:

- Assess reachable terrain features, ramp faces, run-up space, landing areas and objective value beyond a road spline.
- Use takeoff speed, approach angle, steering, throttle/brake and the same permitted airborne inputs as the human.
- Find new combinations of familiar terrain primitives on held-out layouts. A library of practiced maneuvers is acceptable; a fixed cinematic trigger for every jump is not the completed character.
- Take the human passenger somewhere useful or delightfully surprising, not into a technically valid but inescapable hole.
- Correct a poor approach before takeoff where possible; recover from a rough landing, spin or missed line. Attempting a stunt does not guarantee completion.
- Acknowledge a boarded passenger and provide a brief contextual warning before a major stunt when possible. Do not wait for dialogue to finish before physics can run.
- Respect a stop/dismount request at a feasible safe stop. He can be recklessly confident without repeatedly trapping the player in an unwanted stunt loop.

Initial maneuver families: powerslide/180; rock-lip jump; berm-to-platform jump; wall/fort-rim entry where geometry permits; fast descent with a recoverable landing; traversable route across rough terrain. Map geometry and vehicle handling must support human reproduction of these maneuvers before bot quality is judged.

Failure recovery may abort or stop; it cannot teleport, inject upward impulses unavailable to the human, alter collision, or reset the car mid-flight. Optional authored fallback routes keep the game functioning while the learned/generalized controller improves; using them alone cannot pass hotlap's full acceptance.

Evidence: three evaluation layouts made after the maneuver library is frozen, with changes in takeoff angle, run-up, landing width and obstacles. Log attempted/aborted/succeeded maneuvers, actual path/input trace, passenger damage and recovery. Compare him with farsight and ramrod in the same opportunities. Check both discovery of off-road approaches and successful execution; a high success rate obtained by taking only the main road fails identity.

## 4. stitch3r — the machine-gun expert

**User canon:** exceptionally good with the machine gun.

**Signature experience:** crossing his lane is dangerous. He puts accurate sustained fire where it matters, tracks movement, handles recoil and uses good bursts/reloads. His effectiveness should come through a controllable weapon that a skilled human can also master.

The selected portable machine gun is an automatic rifle-style weapon; it is not silently replaced with the original game's separate immobile tripod heavy gun. A deployed heavy gun can be later content if wanted. stitch3r and ramrod may use the same machine gun while remaining different players.

Proposed distinguishing tendencies: tighter tracking, better burst discipline, stronger angle selection and fewer unnecessary advances than ramrod. He can push, flank or ride; “machine-gun expert” must not collapse into “stands forever at one cover node.” Record these distinctions as proposed interpretations, open to the user's correction.

Required behaviors: settle/track a visible moving target; compensate consistent recoil through view input; adjust burst duration to range and exposure; choose a real reload window; remember a briefly occluded target's last motion without continuing to track its hidden live transform; switch targets intentionally rather than snapping every frame; cover a teammate's crossing through plausible suppressive fire. Suppression means bullets and tactical pressure, not secretly increasing the human's spread.

His disadvantage can be sustained focus on a lane while another route becomes important. It should not be deliberately terrible aim in every third encounter. Compare paired scenarios against ramrod with the same weapon, ammunition and target opportunities. Measurements include tracking error, hit burst structure, time spent exposed, reload timing and usefulness as cover.

## 5. farsight — the sniper and skilled driver

**User canon:** good with the sniper rifle and driving.

**Signature experience:** he uses a vehicle to obtain an angle, lands a difficult shot, changes position before becoming predictable and drives competently when an objective or pickup warrants it.

Proposed distinction from hotlap: farsight values a useful vantage and a controlled arrival; hotlap values an aggressive unexpected traversal. Proposed distinction from ramrod: farsight creates distance and a firing opportunity, while ramrod compresses distance and storms in. These are design interpretations grounded in the supplied specialties, not additional biographical claims.

Required behaviors: choose an angle from observed/remembered routes; manage scope attention and visibility; align and commit to a shot through normal aim/fire controls; exploit any selected charge mechanic shared with the human; reposition after giving away a firing position; respond to nearby threats without perfect awareness through the scope; drive across rough terrain and transport a passenger; switch between travel and sniping for a reason.

The sniper has finite traverse/acquisition/settling time. A skilled prepared shot may be fast; a 180° instant unseen headshot is not supported. Do not force him to abandon a strong position simply because a fixed timer expired; use exposure, missed opportunity, threat and objective pressure.

Evidence: same visibility budget as the human; acquisition/settling and shot timing distribution; unseen-enemy counterfactual; relocation after shots; native driving task and passenger pickup; paired driving tests show fewer gratuitous stunts than hotlap without reducing farsight to a road follower.

## 6. b0bbin — the weakest player, with occasional surprises

**User canon:** the worst player overall, sometimes surprising, with a Russian accent and jokes.

**Signature experience:** he makes recognizable poor decisions or fumbles execution, then occasionally contributes a real clutch shot, lucky explosive or unexpectedly useful move—and has something funny to say about it. The surprising event comes from a legal successful action, not a hidden damage buff, forced enemy miss or dramatic cutscene.

His lower skill and his accent are independent traits. Write him as an individual, not a nationality caricature. Do not use phonetic broken-English text as the whole characterization. Use natural dialogue with a performed accent for the final voice; subtitles remain readable conventional text.

Proposed tendencies: more hesitation, poorer reload timing, wider tracking error, occasional tunnel vision, overconfident route choice, slower recovery after a mistake. Keep him mobile, able to use equipment, obey basic orders, reach objectives and participate in fights. “Weakest” must not mean repeatedly stuck, permanently idle or technically broken.

Surprises emerge from a few preserved strengths and normal variance: sometimes a simple shot is well executed; a dropped grenade catches a pursuit; he reaches an undefended point while everyone else is distracted. Do not create an invincible clutch state. Log clutch-like events after they occur so the dialogue system can react truthfully.

Sample original lines, **proposals rather than quotations from the real player**:

- After a poor landing: “That was the shortcut. I did not say it was the good one.”
- After a legitimate unexpected kill: “Yes. Exactly how I planned it.”
- After a rescue: “You can thank me after we stop rolling.”

Final acceptance includes a distinct Russian-accented voice performance for b0bbin using appropriately licensed/original audio. Day-one placeholders may be text or temporary audio but cannot be labeled final voice completion. No copying or cloning a person's actual voice is implied. A prerecorded line bank is sufficient; cloud speech generation at runtime is not required.

Evidence: he remains below the other six in controlled repeated aggregate competence tasks, yet completes legal useful actions in mixed matches. No exact win-rate guarantee per match. Human evaluators should recognize both weakness and occasional delight without judging him as a broken bot. An observed lucky action must not silently retune his whole profile to elite skill.

## 7. ramrod — the fearless driver and machine-gun attacker

**User canon:** good at driving and machine guns; storms in, “NFG.”

**Signature experience:** fast vehicle entry, forceful dismount or drive-by, immediate accurate machine-gun pressure, pursuit through the fort and commitment while other bots hesitate. He is a strong assault player, not a disposable suicide charge.

Required behaviors: exploit a short opportunity to close distance; use terrain and vehicle momentum for a fast arrival; fire as a passenger when appropriate; dismount into a viable attack position; handle the machine gun with competence while advancing; push after observed weakness; keep momentum when a route is viable; replan when a blocked path would make continued commitment meaningless.

Proposed difference from stitch3r: ramrod accepts more exposure and uses the same weapon to create an entry. Proposed difference from hotlap: ramrod's driving primarily makes an assault happen; hotlap seeks spectacular traversal opportunities. Both may jump. Do not make these distinctions absolute prohibitions that produce predictable robots.

Recklessness may get him killed when an aggressive read is wrong. No fear does not mean no perception, no steering skill or no knowledge that a minefield was just discovered. He can intentionally accept known risk, but the decision log should make the reason inspectable.

Evidence: paired same-weapon tests show shorter attack commitment delay/more entry pressure than stitch3r while retaining aim competence. Paired driving tests show more direct assault-oriented destinations than farsight/hotlap. Mixed matches include a real vehicle-to-infantry assault, not just a high aggression scalar with road-path behavior.

## 8. lattice — the laser-mine menace

**User canon:** plants laser mines everywhere.

**Signature experience:** after a few minutes, familiar routes are no longer casually safe. He has mined a doorway, a turn, the route to a good sniper position, a likely escape or a vehicle approach. Destroying or avoiding one pattern encourages him to use another.

Mines must be actual world devices and laser beams that can be spotted, obstructed, crossed and destroyed under the same rules as player mines. A scripted explosion based on the player's location is not a trap. Exact arming, beam, damage, inventory and active limits live in SPEC.

Required behaviors:

- Prefer mines strongly and resupply them through the normal loadout system.
- Find candidate attachment surfaces and opposing beam endpoints from local/known geometry.
- Place multiple spatial patterns: door/corridor crossings, off-angle approach beams, routes around the point, sniper exits and plausible vehicle paths.
- Use observed traffic, detonations, destroyed mines and objective state as evidence. He cannot know which unobserved route the player will choose next.
- Avoid clustering every mine onto one exact marker. “Everywhere” means a persistent visible territorial habit within a generous, explicit world-object budget.
- Maintain enough self-preservation to use the mined map himself. If rules make friendly/own mine crossings dangerous, he must remember the traps he placed and respect known team warnings; this is not permission to know unseen enemy traps.
- Keep fighting and contesting objectives; no permanent return-to-depot planting loop while his team loses unattended.

The original manual describes vehicle attachment and an alternate thrown proximity mode. Those are separately evaluated extensions; the required first slice is planted laser-trip behavior. Do not replace the user's laser-mine specialist with generic timed grenades or proximity-only triggers.

Evidence: fresh held-out corridors/rocks/vehicle approaches, blocked beam endpoints, player-cleared trap routes and finite inventory. Measure spatial coverage, useful interception, trap detection/avoidance and objective contribution. A mine count alone is not evidence that lattice is a believable player. Check neutralization and cleanup; reset cannot leave invisible active trigger volumes.

## 9. Team dynamics and voice

Default configurable 4v4 allocation: **human + hotlap + stitch3r + b0bbin** against **arcl1ght + farsight + ramrod + lattice**. This is a starting matchup, not historical team membership or a proven balance result. The setup screen can move each unique bot between teams while keeping four per side. Difficulty choices apply before a match; all seven remain themselves.

Simple contextual orders: follow, defend point/base, attack objective, pick me up, stop/let me out. Bots acknowledge accepted orders, refuse impossible actions clearly, and can complete an imminent safe action before switching. High-level coordination shares only known information after a short communication delay. No omniscient centralized director moves opponents into the human's sights to manufacture memorable moments.

Use event-grounded prerecorded chatter first. A single conversation arbiter prevents overlapping jokes; urgent warnings outrank banter. Track repeat cooldowns and whether a claim is true. A bot who says “I'm picking you up” must have accepted a pickup task, and its cancellation should update the player. Default limits: one nonurgent voice line globally per 8 s, ≥30 s between jokes by one speaker, no repeat of the same joke within three matches where line supply permits. These are proposed tuning seeds.

Optional future language generation may choose or propose banter from a filtered event summary. It cannot issue per-frame aim/vehicle commands, read hidden state, alter the match, or be needed for offline play. Runtime inference latency or service loss cannot stall control. A tiny local learned motor policy is a different dependency from a conversational model.

## 10. Proving that these are the requested players

Evaluate identity separately from skill and fairness:

1. **Mechanics:** human and bot inputs drive the same gun/grenade/mine/vehicle code; verify hidden-state barriers and shared capabilities.
2. **Specialty:** each character succeeds in real, held-out tasks that require the supplied skill. b0bbin's useful surprises remain legal outcomes within his weaker profile.
3. **Identity:** hide names, voice and cosmetic identifiers. Compare paired behavior clips with matched weapon/opportunity where possible. stitch3r versus ramrod and hotlap versus farsight versus ramrod are the key hard distinctions.
4. **Full match:** at least several complete live sessions, not only selected impressive clips. Record boring/failed runs and rater criticisms too.
5. **User recognition:** the user assesses whether the cast evokes the described players. A classifier score cannot overrule “this does not feel like hotlap.” Record concrete adjustments and rerun the relevant scenarios.

The aim is recognizably faithful, enjoyable play over repeat sessions. Exact indistinguishability from humans is a research ambition requiring stronger evidence; it is not a day-one capability claim.
