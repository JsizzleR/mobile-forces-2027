# Humanlike bots: evidence, feasibility, and development strategy

Research date: 2026-09-07. This assessment supports the owner's change to an offline, single-player infantry-and-vehicle game for native Apple Silicon Mac and Windows x64. No bot, model, training environment, Unreal project, runtime integration, benchmark, or human study was implemented or executed for this document.

**Authority:** [SPEC.md](../SPEC.md) selects requirements and tuning; [ACCEPTANCE.md](../ACCEPTANCE.md) owns release gates; [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md) owns implementation order. This document distinguishes published evidence from proposed design. Research alternatives do not silently add dependencies or replace the canonical scope.

## 1. Direct answer

**Yes: memorable opponents and teammates whose play feels convincingly human are a realistic development target today.** Seven recognizable specialists in a bounded game are a much stronger target than an unrestricted agent that can master any map, vehicle, and game mechanic. The desired feeling is plausible: identify arcl1ght from a remarkable grenade throw, recognize hotlap from an outrageous but physically legitimate approach, and start respecting farsight's habit of relocating after a shot. The selected roster is the human plus exactly seven named bots, eight actors in a 4v4 match; no anonymous filler is needed.

**Reliable indistinguishability from real friends over long, repeated sessions remains an unproven outcome.** Winning, looking human in a short recording, producing amusing dialogue, and feeling like a particular familiar player are different achievements. None of the research below establishes all four for this game. Spending substantial time can buy deeper movement, perception, tactics, authoring, and evaluation; it cannot make a universal promise responsible.

The recommended investment is a native hybrid bot system with fair perception, imperfect memory, consistent preferences, strong specialist skills, and meaningful situational mistakes. Optional imitation learning or reinforcement learning should improve specific skills after the base game works. A language model can help develop and critique these systems; a cloud conversation model should not own steering, aim, physics, or frame-by-frame combat.

## 2. What primary evidence actually establishes

### FPS competence and teamwork: DeepMind FTW

DeepMind's 2019 Quake III Capture the Flag work demonstrated learned cooperation with humans and other agents. A study with a 267 ms observation delay still found the agents outperforming strong human participants; that group's reported win rate against the delayed agents was 21%. This is evidence that strong FPS team play can exceed simple aim advantage. It is not a blind test of whether players believed they were facing humans. The underlying project used population training and substantial research infrastructure. [DeepMind research explanation](https://deepmind.google/blog/capture-the-flag-the-emergence-of-complex-cooperative-agents/), [author paper](https://arxiv.org/abs/1807.01281).

**Project implication:** coordinated attacks, escorts, defense, and complementary team roles are technically credible. This does not supply an Unreal plugin, this game's controls, stunt driving, or seven personalities.

### Humanlike FPS movement: CSKnow / MLMove

The 2024 *Learning to Move Like Professional Counter-Strike Players* paper trained movement from 123 hours of professional CS:GO traces. It reports less than 0.5 ms amortized CPU cost per game step for team movement. Actual model queries and amortized per-step cost differ; the paper describes queries taking up to 8 ms. Aim and firing remain rule based. Its domain is Retakes on Dust2, and the main human ranking study uses short overhead recordings with x-ray rendering, median length 17 seconds. Model inputs include player-state tokens; they are not this project's approved perception contract. [Full author paper](https://arxiv.org/html/2408.13934v1).

**Project implication:** replacing one well-defined subsystem with a small learned model can improve perceived human movement. Neither the performance figure nor the observation schema transfers directly to our Mac/Windows game. This is a particularly relevant precedent for a hybrid architecture.

### A relevant Unreal experiment: Lyra:Ascent

The modl.ai/Riot-authored *Human-like Bots for Tactical Shooters Using Compute-Efficient Sensors* studies an Unreal/Lyra-based, VALORANT-inspired 2v2 game. It uses structured sensors and imitation learning, collecting 48.6 hours from 28 players. Its study had 20 raters judge selected 15-second clips: 30% of bot judgments called them human; 54% correctly identified them as bots. Its preferred model reports 9.59 ms average inference per decision on the tested PC. Appendix features include minimum enemy and enemy-grenade distance; such features need explicit perception filtering before adoption here. [Full author preprint, including methods and results](https://arxiv.org/html/2501.00078v1).

**Project implication:** sensor-driven local imitation is relevant and measurable. Claims of indistinguishability, cheap inference for an entire roster, or fully fair information access would overstate this result. Short selected clips also leave long-session repetition and recovery unresolved.

### Shipped learned driving: Gran Turismo Sophy

This is a product precedent, beyond a laboratory demonstration. Gran Turismo's current product page describes Sophy 3.0 powering the PS5 Power Pack, released December 4, 2025. The earlier Sophy 2.1 release added Custom Race integration in March 2025. [Current Power Pack product page](https://us.gran-turismo.com/us/products/gt7/powerpack/), [Sony 2.1 release announcement](https://ai.sony/news/sony-ai-expands-capabilities-of-breakthrough-ai-agent-gran-turismo-sophy-offering-players-a-more-flexible-and-personalized-experience).

Sony's July 2026 retrospective describes both major training infrastructure and sustained work on racing etiquette, finesse, and temperament. It also identifies continued research on policy customization and generalization. This supports the distinction between learning strong control and designing enjoyable competition. It does not establish a downloadable Sophy controller, arbitrary off-road stunt mastery, or an inexpensive recipe for a new studio. [Sony research retrospective](https://ai.sony/blog/gran-turismo-sophy-five-years-on-from-nature-cover-to-open-frontier).

**Project implication:** exceptionally capable driving bots are possible in a well-defined simulation. hotlap's desired behavior is a separate problem: terrain opportunities, takeoff and landing, passengers, routes, recovery, and combat context.

### Generalist gaming companions: SIMA 2

DeepMind introduced SIMA 2 in November 2025 as a Gemini-powered research agent operating across virtual worlds. The announcement describes limited research-preview access and explicitly identifies long-horizon tasks, short interaction memory, precise low-level control, and complex visual understanding as open challenges. These are directly relevant limitations for sustained FPS combat. The reviewed source does not establish a generally available, redistributable offline Unreal runtime. [DeepMind SIMA 2 announcement and limitations](https://deepmind.google/blog/sima-2-an-agent-that-plays-reasons-and-learns-with-you-in-virtual-3d-worlds/).

**Project implication:** watch this research, but do not make delivery dependent on obtaining an unreleased agent, licensing an unspecified runtime, or continuous network inference. A foundation-model demonstration is not an integration contract.

### Older evidence still useful: BotPrize

UT Austin's UT² project crossed the BotPrize competition's 50% humanness threshold in 2012 using a combination of approaches, including evolved combat and human navigation traces. That matters because convincing behavior did not begin with modern language models. The competition's particular judging setup is not equivalent to proving an opponent indistinguishable across a whole commercial game. [UT Austin project and software record](https://nn.cs.utexas.edu/?ut2=).

**Project implication:** measure what players notice, including navigation and behavior transitions. Changing the model family is not a substitute for designing the actual experience.

## 3. Select techniques by the job they do

| Technique | Useful job here | Principal limitation | Recommended status |
|---|---|---|---|
| Utility scoring | Weigh attack, flank, reload, transport, regroup, and objective choices using persona preferences | Needs coherent inputs, commitment, and tuning; can oscillate | Base decision layer |
| State machine / behavior execution | Run plans with phases, interruption, cleanup, and recovery | A large tree alone does not create personality | Base execution layer; one clear owner |
| Authored map affordances | Encode useful positions, jumps, approaches, and contextual opportunities | Over-authoring can make every match identical | Seed opportunities, with varied approaches and validation |
| Practiced and calibrated native control | Smooth aim, normal charge/release throws, steering, speed control, obstacle response | Requires coverage of useful situations and recovery; unrestricted state access would undermine fairness | Base motor layer; no perfect future-world grenade rollouts |
| Behavioral cloning | Learn movement, timing, steering, and preferences from demonstrations | Dataset coverage, multimodal choices, and recovery errors | Optional bounded improvement |
| Reinforcement learning | Optimize a narrow motor skill in repeatable simulation | Training cost, reward exploitation, and style loss | Optional after a measurable native baseline |
| Language models | Help developers author profiles, investigate traces, generate test cases, and refine dialogue | Latency, reproducibility, control precision, and runtime dependencies | Development assistance only; shipped banter uses local authored content |

Behavioral cloning learns an observation-to-action relationship from examples. It does not automatically acquire the intention behind every example. Compounding errors arise because actions change the later situations the learner encounters; a recovery dataset and interventions are therefore essential. DAgger is a foundational example of iteratively collecting expert guidance on learner-visited states. [Ross, Gordon, and Bagnell, 2011](https://proceedings.mlr.press/v15/ross11a.html).

Policy customization can also combine learned control with an explicit planner. Residual-MPPI studies changing a prior control policy at execution time, including a GT Sophy case. This is evidence for a research direction, not a decision to import that algorithm into the initial game. [Sony-hosted ICLR 2025 publication record](https://ai.sony/publications/residual-mppi-online-policy-customization-for-continuous-control).

The architecture should allow a learned driver or aim controller to replace its native counterpart behind an identical observation/action contract. Avoid a parallel AI universe with different collision, damage, ammo, or vehicle rules.

## 4. Human likeness starts with information and attention

Each bot needs a private belief about the match. A live opponent's exact position, health, intended input, and future movement must not be globally readable by tactical code. Record what the bot saw, heard, inferred, or received through a teammate report, when that happened, and how uncertain it is now.

Useful map knowledge is legitimate: an experienced player remembers ramps and common sniper spots. Knowledge of a currently hidden person is conditional. Knowing a route exists does not reveal whether an enemy has silently taken it. A last-seen point may seed a search corridor; its confidence should decay and its spatial uncertainty should grow.

Attention should explain both strengths and weaknesses. farsight can focus on a difficult shot and be slow to notice a nearby flank. stitch3r can track one threat well while missing a second approach. hotlap can concentrate on takeoff alignment while briefly reducing tactical scanning. This creates coherent limitations rather than arbitrary dice rolls that make a competent bot inexplicably malfunction.

An aim controller should move through a trajectory with acceleration limits, acquisition, correction, and tracking. A reaction delay followed by an instantaneous head snap remains recognizable as a bot. Errors should persist and change with movement, range, recoil, stress, visibility, and recent corrections. Independent random offsets every frame produce trembling; the useful target is a plausible time series of control.

Avoid reproducing every human nuisance. Intentionally getting stuck, ignoring obvious interactions, or sabotaging teammates are not necessary for believability. Preserve coherent tactical mistakes while eliminating mechanical deadlocks.

## 5. Turn the seven names into recognizable play

These are owner-described gameplay personas, not claims that a model of the real people already exists. Their identities should remain recognizable with names, skins, subtitles, and voices hidden. The patterns below are proposed design directions; canonical tables and defaults belong in SPEC.

### arcl1ght: grenade specialist

arcl1ght is exceptionally practiced at placing and throwing a physical grenade, including long throws and useful close drops. His skill appears in movement, release orientation, timing, and his estimate of where a target will be. Direct sight, remembered motion, an audible event, or a common position can justify an attempt. None permits following a hidden target's new movement through walls. A practiced controller estimates range, angle, charge, movement, and release through the ordinary player controls; calibration or demonstrations can improve that mapping. Runtime perfect future-world rollouts, hidden-state trajectory selection, and post-release corrections are prohibited. SPEC now fixes the shared timed/impact modes and separate drop input. A nearby lethal blast is an explosion outcome, not a proximity fuse; the mode and physical release determine when it happens.

His signature is a strong estimate and precise execution: getting the grenade to a distant opponent or into the route they are likely to use. His error is a wrong prediction, an unexpectedly moving teammate, or a target choosing another exit. Distance should degrade certainty; "from any distance" becomes remarkable throws throughout the weapon's feasible envelope. There must be unreachable targets and occasions to switch weapons. Use the same throwing controls and projectile physics for humans and bots, without post-release target correction.

### hotlap: stunt driver

hotlap values shortcuts, unusual elevations, rapid insertions, and calculated jumps. His aggressive route preference must be backed by an actual driving skill. A vehicle graph should support off-road corridors and stunt transitions as well as roads. A stunt opportunity contains approach space, takeoff geometry, speed/heading tolerance, estimated landing region, and a recovery route.

The owner first wants driving to feel arcade-like and physically convincing in human hands. Qualify that handling before scoring hotlap's skill. The selected roster contains a dune buggy, a Hummer-style utility vehicle, a truck, and a new dirtbike. These require distinct control profiles and capability limits. Success with the buggy does not qualify truck turning or two-wheel stability, and the dirtbike is an original addition rather than a claim about the 2002 roster.

Vary approach lines and combine route segments so he does more than replay one spline. Discovery and generalization within the supported maneuver families are mandatory, demonstrated on held-out arrangements of approach, launch, landing, and obstruction geometry. Offline characterization can establish vehicle limits and provide practice; replaying an authored route library alone does not satisfy that requirement. He must reject launches outside the vehicle's capabilities, respond to blockers before takeoff, and recover from poor landings. Midair correction may use only controls available to humans. Arbitrary invention of safe jumps on arbitrary geometry remains a research stretch, not the minimum promise.

Shot-out tires must change actual traction, steering response, and the feasible maneuver envelope. A bot must notice the resulting vehicle state, reduce or change its plans, and eventually abandon an unusable vehicle under the selected lifecycle rules. It cannot compensate with invisible extra grip or continue following the old perfect trajectory. Test damage before takeoff, during travel, and after landing, including abandonment and destruction followed by replacement. Exact impairment thresholds and respawn rules belong in SPEC.

Passenger transport makes the behavior social: approach for pickup, allow boarding, make the exciting insertion, and leave the passenger somewhere useful. Reckless flavor should not reduce every ride to a forced death.

### stitch3r: machine-gun specialist

stitch3r's strength is sustained-fire discipline: pre-aim likely entries, select useful firing positions, manage burst length, anticipate reload exposure, and create openings for teammates. His aim skill should coexist with distinguishable movement and engagement choices.

Suppression can affect other bots' tactical assessment of incoming fire without changing bullet damage or making humans lose input control. stitch3r may hold a position slightly too long or prioritize an ongoing gunfight over a rotation. He should remain useful after ammunition loss or an unfavorable angle.

### farsight: sniper and driver

farsight uses driving to obtain sightlines and escape after revealing a position. The recognizable sequence is scout, choose a position, take a deliberate shot, observe the response, then relocate when the threat warrants it. Avoid constant unnecessary repositioning simply to advertise activity.

His driving prioritizes useful placement and a viable exit over hotlap's spectacle. Skilled scope work costs peripheral attention. Close pressure, misleading sound, or an enemy refusing his expected route should counter him through ordinary mechanics.

### b0bbin: weak player with occasional surprises

b0bbin is comparatively poor at aim, timing, threat prioritization, or positioning, but can navigate, board, follow a simple request, and complete an objective. His failures should come from plausible choices: reloading early, overcommitting to a chase, or misunderstanding a likely enemy route.

A surprise emerges because an unusual choice happens to work, an opponent underestimates him, or he executes a simple shot well. Do not inject hidden accuracy, damage, invulnerability, or score-aware miracles. Rare successful sequences must be traceable to the same rules that produced his ordinary play.

The requested Russian accent and jokes are separate presentation traits, unrelated to poor skill. Give him an individual sense of humor. Written lines can be performed by an authorized actor; recreating a real person's voice requires that person's authorization. Limit repetition and allow banter to be muted.

### ramrod: aggressive driver and machine-gunner

ramrod commits early, drives into pressure, dismounts into a forceful engagement, and keeps an attack moving. His identity differs from stitch3r's firing discipline and hotlap's stunt appetite: the vehicle is a way to seize initiative, and the machine gun sustains it.

He accepts more exposure and may press an advantage too far. Aggression should be conditional on what he believes, with a bounded retreat or recovery when the plan collapses. "NFG" energy belongs in his pace and commitment, not omniscience, impossible handling, or systematic friendly-fire griefing.

### lattice: laser-mine specialist

lattice habitually prepares routes and spaces with laser mines: doorways, shortcuts, likely vehicle approaches, escape paths, and tempting positions. His signature is a deliberately prepared threat that changes where opponents can safely move. Mine availability, placement validity, deployment time, visibility, triggering, and damage follow the same rules available to the human.

He should infer likely traffic from map knowledge and observations, place a mine, then continue playing. He cannot place devices remotely along an unseen player's current path. Avoid a perfect global coverage planner: his preferences should leave patterns and gaps a perceptive player can learn. A weak placement, a conspicuous beam, or an opponent choosing a new route provides counterplay. Set finite stock and active-device limits so "everywhere" expresses a recognizable obsession without unbounded object accumulation or a map that becomes unusable.

## 6. Personality should survive difficulty changes

Treat identity, ability, and current mental state as separate inputs. Identity governs tendencies such as grenade use, route adventurousness, patience, preferred range, teamwork, and willingness to disengage. Ability governs the quality of estimation and control. Short-lived state reflects recent events, attention, commitment, and confidence.

Difficulty should adjust an approved subset of ability parameters and opportunity selection without turning every bot into the same perfect opponent. On easier settings, arcl1ght still looks for clever grenades and hotlap still chooses adventurous routes. On harder settings, b0bbin remains the least consistent member of this roster; the player should not watch his defining weakness disappear whenever the score changes.

Sample tendencies at meaningful boundaries. Choose a plan, stay with it long enough to be legible, and reconsider on new information or a relevant failure. Randomly resampling personality every decision creates noise. Equally, permanent commitment creates exploitable loops. The design needs both minimum commitment and explicit cancellation conditions.

Permit bounded adaptation from observed behavior: after repeated attacks from a lane, a bot becomes more cautious about it. Do not let the match director reveal the player's new route, secretly boost stats, or rewrite everyone's identity to force a close finish. Offline play removes adversarial clients; it does not remove the value of understandable rules.

## 7. If imitation learning is added, collect the right evidence

A few highlight clips are valuable reference material but insufficient for a reproducible behavior clone. For this project, record consented sessions in the project's own instrumented build, including failures and routine play. If the original friends participate, those sessions can guide their personas. If they do not, use volunteer demonstrations and label the result as an inspired character rather than an exact reconstruction.

Capture synchronized input commands, view rotation, locomotion, weapon state, locally available observations, relevant audio events, vehicle state, and outcomes. Record scenario, map/content version, control settings, and persona/task labels. Preserve a clear distinction between privileged evaluation data and permitted policy inputs. Camera video is useful for review; structured telemetry makes action timing and causal context measurable.

Collect difficult transitions: missed grenade estimates, interrupted reloads, blocked boarding, failed takeoffs, poor landings, lost targets, teammate obstruction, and regrouping. Split evaluation by complete sessions and held-out situations rather than adjacent frames. Otherwise near-identical sequences can leak between training and evaluation and make a model look more robust than it is.

Start with one skill and compare it against the native implementation under identical observations and controls. Preserve several valid actions when the situation allows several choices; training toward an average of incompatible turns can yield a bad turn. Freeze a reviewed policy for packaging, version its data and parameters, and keep a tested native fallback. No customer GPU training, developer Python environment, or online model download should be required to start a match.

## 8. Evaluate feel separately from correctness

Use three complementary evidence layers. Mechanical tests establish legal inputs, stable lifecycle, fair information flow, and successful recovery. Behavioral measurements establish distributions: reaction and correction timing, range-conditioned accuracy, burst lengths, grenade errors, route diversity, objective engagement, teammate distance, and persona preferences. Human playtests establish whether those patterns feel believable, fair, distinctive, and fun.

Blind recordings should randomize and balance human and bot examples, include routine transitions, and use matched presentation. Keep a separate identity test with voices and labels hidden. Include an ablation in which the same competent baseline loses its persona parameters; otherwise a good-looking animation or a funny line can be mistaken for behavioral individuality.

Short clips are screening tools. Repeated full matches must probe exploit discovery, stale plans, annoying chatter, predictable openings, and failed recovery. Record why raters decided "bot," then convert recurring reasons into specific reproductions. Separate FPS veterans from less experienced players and report participant/session counts and uncertainty. Avoid treating dozens of correlated clips from one match as independent evidence.

Human reference footage does not require shipping networking. Volunteers can provide controlled local runs, sequential scenario recordings, and instrumented sessions in the project's test harness. If comparisons cannot match the actual interactive situation, report that limitation. A classifier's chance-level accuracy on a small clip set is not proof of general human indistinguishability.

## 9. Practical recommendation and remaining uncertainty

Dropping network play materially simplifies authority, vehicle prediction, matchmaking, remote lifecycle, and test topology. Spend the recovered effort on fair sensing, aim trajectories, grenade prediction, off-road affordances, passenger usefulness, and long-session variety. Mac remains part of the target: the baseline should use local native logic, and any learned policy must qualify its packaged runtime, latency, and memory on both Mac and Windows before it becomes required.

The earliest convincing proof should be one repeated combat encounter and one repeated passenger ride where testers recognize the specialist from behavior alone. The owner has confirmed Western as the remembered reference map; Fort Crossing is the original interpretation, with Holdout primary and CTF supported. This establishes the intended reference, not verification of the original map's complete geometry. Test failure cases, unfamiliar approaches, ordinary objective play, and the full roster together. Expanding maps or training an entire end-to-end agent before this evidence would multiply unresolved problems.

Astra, Fable, and Unreal automation can make the implementation process more productive and reviewable. They cannot certify feel from prose. A realistic "one-shot" specification should make an initial end-to-end build coherent and leave explicit evidence-driven tuning loops; it should not promise final human likeness after one unattended generation.

The strongest justified ambition is a small, deeply tuned game populated by recognizable people-like competitors who surprise the player for understandable reasons. Whether these seven personas reach that standard remains to be established through the actual build and repeated human play.
