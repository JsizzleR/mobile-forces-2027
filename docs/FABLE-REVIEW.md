# Fable independent review packet — v0.2

Prepared 2026-09-08 for the user's separate Fable review. **Fable reviewed the specification on 2026-09-08**; the result, its input hashes, the Codex cross-check and the owner's dispositions are in [FABLE-REVIEW-RESULT.md](FABLE-REVIEW-RESULT.md). That was a document review only; no implementation review has occurred. This packet remains the request for later re-reviews. No Unreal runtime, native package, trained controller, or human evaluation is established by writing it. Record the actual model/version and input documents when review occurs; do not invent a Fable model identifier or claim another agent's work was Fable's.

## Read in this order

1. [SPEC.md](SPEC.md): canonical active scope, rules, numbers, and release labels.
2. [BOT-CAST.md](BOT-CAST.md): seven identities, shared capabilities, signature behaviors, and recognition tests.
3. [ACCEPTANCE.md](ACCEPTANCE.md) and [BUILD-RUNBOOK.md](BUILD-RUNBOOK.md): required evidence and implementation sequence.
4. [DAY1-ENGINEERING.md](DAY1-ENGINEERING.md): the small engineering harness, reference-derived mechanisms, and ENG-001 through ENG-016.
5. [Game design](research/GAME-DESIGN.md), [player-experience research](research/PLAYER-EXPERIENCE.md), and [humanlike-bot research](research/HUMANLIKE-BOTS.md): original mechanics, audience evidence, owner priorities, and research limits.
6. [Engine architecture](research/ENGINE-ARCHITECTURE.md), [Unreal automation](research/UNREAL-AUTOMATION.md), and [platform/agent research](research/PLATFORMS-AND-AGENTS.md) for relevant implementation claims.

The active v0.2 selection supersedes the archived network plan. Research alternatives do not create competing defaults. If active files disagree, identify the concrete conflict and proposed reconciliation; do not silently choose whichever requirement makes the project easier.

## Copy-ready specification review prompt

```text
Independently review the active v0.2 Mobile Forces-inspired game specification.
Challenge its buildability, fidelity to the owner's priorities, and evidence
quality. Find concrete blockers and propose small, coordinated corrections.
Do not implement the game, install tools, buy assets, publish, or contact anyone.
If file access is available, write only docs/FABLE-REVIEW-RESULT.md; otherwise
return a self-contained review suitable for saving there.

Read the documents listed in this packet. Record the exact input revision or
file-manifest hashes, actual model/version, date, and whether each conclusion
comes from document inspection, external primary sources, code inspection,
executed tests, or judgment. This is initially a specification review. Absence
of implementation is expected; pretending that a planned mechanism is already
validated is a defect. Verify mutable engine/plugin claims through current
primary documentation or the actual installed release when available.

The owner wants a realistic chance of a sustained, coherent implementation
kickoff using Astra, independent Fable review, and appropriate Unreal automation.
Interpret this as resumable build/test/fix work with explicit stage gates, not
an assurance of finished human likeness after one response or an unattended
run without playtesting. Challenge any critical seam that remains unspecified.

The selected product is fully offline: one human plus exactly seven unique
named bots, eight combatants in 4v4. The names are arcl1ght, hotlap, stitch3r,
farsight, b0bbin, ramrod, and lattice. No anonymous filler, duplicate personalities,
network play, matchmaking, dedicated server, or cloud inference is required.
Mac Apple Silicon and Windows x64 are native targets from the beginning.

The owner confirmed Western as the remembered desert map. Fort Crossing is
an original interpretation of its desert-town/central-fort relationships;
Holdout is primary and CTF is supported. Confirmation identifies the intended
reference, not copied geometry or measured original physics constants.

All four vehicle families are mandatory: dune buggy, four wheels/two seats;
Hummer-style utility, four wheels/four seats; truck, six wheels/five seats;
and the new dirtbike, two wheels/one seat. Both four-wheel families disable
driving at three disabled wheels; the truck at four of six; the bike at two
of two. The dirtbike is a new project feature, not an original-game historical
claim. Do not cut it,
tire damage, passenger combat, or a specialty to make a passing review easier.
If something requires redesign, state the tradeoff and its affected gate.

Review the following areas with adversarial examples.

1. Human handling and vehicle scope.
The owner's leading priority is accessible arcade handling with convincing
momentum, suspension, grip, airtime, landing, and recovery. Is enjoyable human
driving established before bot-driving sophistication? Can the four families
feel different, including a credible two-wheel bike, within the chosen physics
architecture? Inspect camera, input, passenger visibility, and recovery costs.
Require the same constraints for humans and bots. A scripted successful jump
or hidden stabilizing impulse cannot establish skilled play.

2. Tire impairment and lifecycle.
Does each tire state affect actual steering/traction, not only a visual or
health scalar? Distinguish degraded control from crossing the drive-disable
threshold. Can the bot adapt its speed, route, and jump decisions to damage?
Construct damage during approach, airborne travel, landing, boarding, and
objective transport. At disablement, seats and carried flags remain coherent;
abandonment/destruction eventually produces the correct replacement without
duplication, overlap, invisible repair, or permanent transport starvation.

3. Grenade skill and fair information.
arcl1ght is a superb physical thrower: close drops, charged long throws, timed
and impact modes, and powerful near hits. The common weapon must make those
moves possible for a practiced human. Separate motor execution error from
incorrect prediction of what the opponent will do. Challenge hidden target
transforms, future human input, post-release correction, and perfect runtime
forecast searches that bypass the intended practiced-skill controller.
Measured throw calibration, controlled strokes, or optional imitation policies
are acceptable when they produce ordinary legal inputs and preserve uncertainty.
Prove skill outside one memorized throw position and counterplay through a
last-instant unobserved change of direction or real geometric obstruction.

4. The rest of the cast.
hotlap needs surprising off-road combinations and meaningful landings, not
only a fixed road or cinematic jump list. stitch3r and ramrod must differ with
the same machine gun. Compare hotlap, farsight, and ramrod driving the same
vehicle in matched opportunities. farsight's sniping and relocation must coexist
with limited scope attention. lattice needs actual destructible laser devices,
plausible placement, finite resources, useful patterns, and continued objective
participation. b0bbin must be weaker yet operational and sometimes legitimately
useful, without a hidden clutch buff. His accent is independent of skill.
Hide names, skins, and voices during identity tests, then assess the complete
experience separately. Neither funny speech nor a high kill count proves
recognizable humanlike behavior.

5. Match rules and map incentives.
Walk every Holdout and CTF transition, including simultaneous events, boundary
timing, flag return/capture conditions, death, seats, unreachable drops, pause,
restart, and rematch. Try to create duplicate ownership, lost objectives,
unclear results, and nonterminating matches. Check that vehicle routes, tunnels,
ramps, sniper angles, and mine placements offer counters and useful choices.
Can all seven stay themselves while actually helping in both modes? Can one
weapon, a tire-denial loop, or a dominant fort position erase those choices?

6. Information fairness and evaluation validity.
Audit the intended perception boundary across target choice, aiming, throw
prediction, route planning, squad reports, and debugging paths. Map familiarity
is legitimate; live hidden enemies are not. Require a same-run visible positive
control and an occluded negative case. In paired counterfactuals, preserve the
bot's complete permitted observation/history and relevant randomness while
changing hidden enemy state; its decisions must not gain hidden knowledge.
Do not mistake different public observations for a valid counterfactual.
Freeze development, validation, and acceptance scenario sets; record tuning
exposure and replacement holdouts. Require held-out geometry, ordinary failures,
complete repeated sessions, matched presentation, actual sample counts, and
uncertainty. Selected short clips and same-map familiarity are insufficient.

7. Unreal automation and native delivery.
Check the measured engine/toolchain/plugin lock, actual MCP capabilities,
editor-Python or commandlet fallback, one editor owner, reproducible content,
and ambiguous asynchronous-job recovery. Native Mac and Windows must each
have real build/package/run evidence; availability on one does not qualify the
other. Qualify any optional learned-policy runtime on both platforms. Ordinary
play cannot depend on Python training tools, editor services, the reference project, MCP,
network connectivity, or downloading a model. Headless tests cannot establish
rendering, input feel, or performance of the rendered package.

8. The day-one engineering contract.
Review ENG-001 through ENG-016 individually against DAY1-ENGINEERING.md and
ACCEPTANCE.md. In particular: measured native prerequisites and editor round
trips (001–002); raw exit/timeout/report integrity and nonzero named-case counts
(003–004); exact input/output binding and semantic generation idempotence
(005–006); real editor exclusivity, stale-owner handling, and interrupted-action
recovery (007–008); conservative change classification and actionable residuals
(009–010); genuine fairness controls and all seven exact IDs through packaging
(011–012); held-out experiment separation and real native rendered evidence
(013–014); independent dispositions and truthful final claims (015–016).
Do these mechanisms remain small enough to inspect and useful from the first
increment? They are selected adaptations from the reference project, not evidence that this
game inherits the reference project's tests, infrastructure, maturity, or productivity claims.
A mandatory unavailable, skipped, invalidated, or erroneous check cannot become
PASS through a residual or an optimistic summary. Require positive controls
and actual launcher behavior, including a timed-out child that exits zero.

For every finding supply a stable ID, severity, affected requirement and file
section, reproducible or precisely constructible scenario, evidence category,
why the current gate catches or misses it, smallest corrective patch, and
required new evidence. Propose exact replacement wording or a focused diff
where a contract is wrong. Coordinate SPEC, ACCEPTANCE, RUNBOOK, and cast/day-one
changes rather than repairing only a research paragraph. Separate blockers,
material risks, improvements, and optional future ideas. Do not inflate scope
with generic shooter features unrelated to the owner's stated priorities.

Conclude: viable as written / viable after listed corrections / redesign
required. Name the three experiments with the highest information value.
List unresolved decisions and unavailable measurements separately. You may
judge the specification promising; you cannot mark physics, packaging, fair
runtime behavior, or human enjoyment passed without the corresponding work.
```

## Required review and integration record

The review artifact should begin with its verdict and input identity, followed by a findings table and details for consequential findings. Findings need a counterexample and correction, not merely a preference for a different framework. Include a compact ENG-001–ENG-016 coverage table so missing engineering obligations remain visible.

After actual review, the integrator assigns every finding **accepted**, **rejected**, or **unresolved**. Accepted findings name the applied patch and verification; acceptance of a recommendation alone is not closure. Rejection requires checked counterevidence or an explicit product tradeoff and reopener. Unresolved findings retain the missing decision/measurement, owner, next action, and blocked gate. Re-review material fixes against the resulting document or package identity. Record Astra and Fable work separately; agreement does not establish executed evidence.

For a later implementation audit, provide the reviewed v0.2 contract, actual source/content manifests, native package hashes, raw reports, complete session records, and current residuals. Ask Fable to reproduce high-risk scenarios where runnable and inspect alternate callers around the perception/action boundary. Preserve read-only scope for source; save findings separately. Missing Windows access, unplayed handling, or a failed holdout evaluation must stay visible in the final verdict.
