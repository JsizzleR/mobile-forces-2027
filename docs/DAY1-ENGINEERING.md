# Day-one agentic engineering contract

Status: proposed implementation requirements, researched 2026-09-07. This file adapts selected mechanisms from a private reference project into the offline game specification. The scripts, schemas, control files, and checks named below are **deliverables to implement**, unless another document explicitly supplies current evidence that they exist. No game, engineering harness, Unreal installation, or model experiment was executed for this document.

Read this with [SPEC.md](SPEC.md), [BUILD-RUNBOOK.md](BUILD-RUNBOOK.md), and [ACCEPTANCE.md](ACCEPTANCE.md). The product is one human playing with and against the seven specific characters `arcl1ght`, `hotlap`, `stitch3r`, `farsight`, `b0bbin`, `ramrod`, and `lattice`: an eight-player offline 4v4 roster. Mac and Windows remain native targets. Runtime online services and multiplayer infrastructure have no engineering allocation in this plan.

## 1. What to transfer from the reference project

The reference project's most useful contribution is a way to prevent apparently successful agent work from becoming an unreviewable artifact: know the exact inputs, make progress resumable, exercise the real interface, and identify what each piece of evidence actually establishes. Its operational infrastructure and accumulated governance are unnecessary here.

The inspection was read-only, at a revision recorded privately on 2026-09-07, and it read standing-context files first and then targeted sources. No build, hook, startup script, service, provider, remote or deployment of that project was invoked, and nothing in it was modified. This project did not yet have a Git repository at inspection.

References `[B01]`–`[B12]` below identify the inspected mechanisms, summarized in §12. Those references document provenance; this file contains the resulting requirements, so another implementer needs no access to that project.

| the reference project mechanism | Adopt here | Deliberately omit |
|---|---|---|
| Measured external contracts [B01] | Native Unreal/MCP/Python spike before depending on an API; save observed versions, tool schemas, commands and restart behavior | Assuming a README or a model-generated method name establishes compatibility |
| Registered checks and tier honesty [B02] | Small JSON registry with explicit evidence class, platform, named cases and minimum counts | Go, Podman, Linux server tiers and a giant shell registry |
| Harness checks [B03] | Test actual child exits, timeout latching, missing/empty reports and reused output | Treating a final `PASS` log string as a verdict |
| Exact artifact validation [B04, B05] | Bind checks to frozen source/content/toolchain hashes; revalidate after integration; inspect native packages | Two-remotes landing convention, blanket merge infrastructure, pre-push bypasses |
| Residual queue consistency [B06] | One compact JSON ledger with owner, next action, evidence, decision and reopener | Hundreds of copied historical rows, duplicate backlog/bundle taxonomies |
| Independent review [B07] | Fable and Astra examine distinct questions, actual artifacts and dispositions; user evaluates feel | Inherited model effort pins, fabricated review receipts, treating agreement as execution evidence |
| Crash-window probes [B08] | Interrupt content generation, cooking and checkpoint publication inside named steps; prove safe resume | the reference project service recovery architecture, custom daemon or deployment journal |
| Non-vacuous qualification [B09] | Positive controls, named scenario inventory, launch nonce and actual consulted inputs | A smoke test that passes after running zero scenarios |
| Small standing context [B10] | Short `AGENTS.md`, one status file, targeted architecture/evidence links | Copying that project's large standing-context file and playbook wholesale |
| Retired-host override [B11] | Preserve the explicit prohibition if encountered during source research | Every command or operational assumption involving the retired host |
| Planned versus built distinction [B12] | Separate requirement, implementation, native verification, human assessment and release acceptance | Promoting a design decision into a capability claim |

This adaptation adopts mechanisms, not that project's estimated productivity multipliers or general claims about model effectiveness. Their value here must be measured through build recovery, reduced rework, and reproducible gameplay evidence.

## 2. Day zero and day one outputs

“Day one” means the first implementation increment and its prerequisites, not a promise that engine downloads, two operating systems, the finished game, and convincing bots fit into one calendar day. Missing hardware or toolchains must not prevent specification-independent source work; they prevent the dependent acceptance claim.

**Day zero: make the environment and assumptions measurable.** Inspect the target directory, preserve existing files, and initialize version control only as part of the authorized implementation session. Record source control and binary-asset strategy; verify any chosen large-file extension actually retrieves bytes rather than pointer files. Run the proposed `Scripts/doctor.py` on each available native host. It reports engine identity, project path, compiler/SDK, platform/architecture, plugin availability, writable output directories, free storage and available memory. Checks without an installed engine remain `UNAVAILABLE`, with their dependent gates named.

Complete one small native editor round trip: connect through the selected automation path; enumerate actual tools; create or modify a disposable fixture asset; save; close/reopen the project; rediscover the asset; query a known property; run one named automation case; export one fresh screenshot. The fixture belongs to the project test area and is created only during implementation. If the preferred MCP path fails, qualify the specified editor-Python/commandlet fallback with the same observable postconditions. Save the measured contract; do not silently invent replacement tool names. [B01]

**Day one: produce the smallest useful harness and runnable seam.** Create the durable files listed below, implement the command wrappers and harness integrity cases, and compile the smallest project containing the shared action/perception boundary. Produce one human-controlled pawn and one bot-controlled pawn through that boundary in a test map. The test roster already contains all seven exact IDs and versioned personality assets, even though their finished behaviors arrive in later gates. Prove a bot can act on visible information and loses privileged target information after occlusion; save the actual event evidence. This is the first fairness seam, not proof of humanlike play.

Required first-increment artifacts:

```text
AGENTS.md                              short project rules and authority links
PROJECT-LOCK.json                       exact software/content identities
IMPLEMENTATION-STATE.md                 current gate and resumable next action
DECISIONS.md                           decisions, alternatives, evidence, reopeners
Verification/checks.json                registered checks and named case inventory
Verification/residuals.json             one tracked known-unfinished ledger
Verification/schemas/                   versioned lock/check/result/experiment schemas
ContentRecipe/                          stable map/asset/personality generation inputs
Scripts/doctor.py                       read-only prerequisite inspection
Scripts/build.py                        native UBT/UAT adapter
Scripts/generate_content.py             editor Python content generation entrypoint
Scripts/validate_content.py             engine-native asset/reference inspection
Scripts/run_scenarios.py                process supervision and report normalization
Scripts/collect_evidence.py             artifact hashes and evidence index
Scripts/check.py                        registry selection and aggregate verdict
Scripts/classify_change.py              closed change-to-gate classification
Scripts/editor_lease.py                 exclusive editor-operation ownership
Scripts/checkpoint.py                   publication and recovery of operation state
Scripts/validate_governance.py           requirements, residual and decision consistency
Saved/AgentState/                       local leases, checkpoints and operation records
Saved/Verification/<run-id>/            immutable per-run evidence after finalization
docs/evidence/<gate-id>.md               portable result summary and artifact locator
```

Use one supported host Python interpreter for ordinary scripts; record it in the lock. `generate_content.py` and engine-native inspection execute inside Unreal's Python environment. They cannot be validated merely by importing them with the host interpreter. Keep editor-only modules and MCP capabilities out of packaged runtime dependencies.

The day-one harness must be small enough to inspect in one review. Use ordinary JSON, Python subprocess handling, native Unreal automation reports, and filesystem primitives. Do not build an agent orchestration service, a distributed scheduler, or a generic policy language.

## 3. Exact inputs and artifact identity

`PROJECT-LOCK.json` must have a versioned strict schema. Its mandatory fields cover:

- Unreal marketing version, build changelist, distribution type, observed build identity, and source revision when available. Record `null` plus a reason when a distribution does not expose a source SHA; never synthesize one.
- Mac and Windows host OS, CPU architecture, compiler, SDK, build configuration and engine target. Machine labels are task-specific identifiers; omit hardware serials and credentials.
- Enabled plugin identities, versions or source hashes, platform support and runtime/editor scope; qualified automation transport and observed schema digest.
- Source revision, source manifest digest, content recipe digest, generated-asset inventory digest, generator revision, asset source/license records, character-profile version and scenario-suite revision.
- Agent harness identity and actual model labels used for the implementation/review. These describe provenance, not gameplay dependencies.

A run captures the committed revision **and** the exact uncommitted input manifest when development work is dirty. A Git SHA alone cannot describe dirty source, untracked assets, or generated binaries. Enumerate relevant inputs by canonical relative path and hash; reject duplicate paths, unresolved large-file pointers, missing required inputs and platform-dependent case collisions. Verification runs freeze their inputs. If source or content changes before completion, retain the run as `INVALIDATED`, not as evidence for the newer tree. [B04]

Generated Unreal package bytes need not be identical between editor saves. Distinguish their literal hashes, used to identify exactly what was tested, from a normalized semantic asset inventory, used to test generation idempotence. That inventory contains stable IDs, class paths, relevant property values, dependencies, transforms, collision/navigation settings and source recipe hashes. Generation twice must preserve this normalized contract and must not accumulate duplicate actors/assets; it need not promise byte-for-byte determinism from Unreal serialization.

Changing generator code invalidates its generated-output qualification even when recipe input is unchanged. Check the actual destination inventory against expected output; a filename containing a content hash or a cached input digest does not prove current integrity. [B05]

Each evidence run uses a new random run ID and empty output directory. The supervisor supplies that ID to the tested application; reports must return it with the package/source identity and scenario revision. Never scan “the newest report” from a shared directory. Hash copied packages and reports after processes have stopped writing. Finalize `manifest.json` last by atomic replacement within the same filesystem. Incomplete runs retain logs and an incomplete state; they never acquire a completion marker.

## 4. Evidence classes and truthful gates

Evidence is a matrix of class, native platform, configuration, map, character profile and scenario revision. It is not a ladder where a higher-sounding label implies every lower cell passed.

| Class | What executes | What it establishes | What it cannot establish |
|---|---|---|---|
| `hermetic` | Host scripts, schemas, pure logic fixtures, classifier and supervisor self-checks; no engine, service or downloads | Harness semantics, data validity, bookkeeping invariants | Unreal compilation, physics, rendering or bot feel |
| `engine` | Native UBT build, editor/commandlet tests, actual content inspection and simulation scenarios | Named engine behavior in the observed configuration | Shipping-package inclusion, rendered usability, cross-OS equivalence |
| `package` | Freshly cooked native executable with the actual packaged assets | Installation/launch, cooked references, offline complete loop, lifecycle and platform behavior | Human resemblance or subjective fun |
| `rendered` | Real package or editor with the renderer and input path active; screenshots, clips and performance traces | HUD readability, presentation, input behavior, visible animation/vehicle quality and measured performance | General human resemblance from one curated clip |
| `human` | Registered blinded or controlled play sessions on identified package hashes | Player perceptions, character recognition, enjoyment and obvious cheating judgments within the sampled cohort | Universal indistinguishability or deterministic correctness |

Use native Mac and native Windows cells explicitly. A Mac package is not Windows evidence; a successful Windows compilation is not a rendered Mac smoke test. `NullRHI` or another headless execution mode cannot supply rendered evidence. A fake scene-query service cannot establish that the shipped Unreal implementation respects occlusion.

Each check yields `PASS`, `FAIL`, `UNAVAILABLE`, `SKIPPED`, `INVALIDATED`, or `ERROR`. `ERROR` means the measuring system malfunctioned; `FAIL` means a valid observation violated the contract. Both prevent a required gate from passing. A timed-out child is an `ERROR` unless the tested property explicitly expects and observes that timeout. `SKIPPED` includes an explicit selection reason; unavailable prerequisites are separate. The report prints each count and every missing required cell.

Aggregate `PASS` requires every mandatory selected case to run with valid inputs and pass. Any mandatory fail produces `FAIL`; mandatory unavailable/skipped/invalidated/error produces `INCOMPLETE` when no valid failure already establishes failure. Optional findings remain visible. A known defect may have an accepted residual, but cannot be transformed into a passing mandatory case. This intentionally tightens the reference project's legacy known-red skipping convention for a new project without inherited debt. [B02]

`Verification/checks.json` entries require `id`, `requirement_ids`, `evidence_class`, `platforms`, argument-vector command, prerequisites, named case IDs, minimum completed count, timeout policy, expected artifact schema and owning subsystem. No shell command strings assembled from map/player names. Registration fails for duplicate IDs, unknown classes, missing scripts or missing mandatory requirement coverage. Case renames must update both inventory and report consumers. Count and name checks: seven bot IDs are required by value, not merely seven arbitrary entries.

Example shape, illustrating the schema rather than promising an existing command:

```json
{
  "id": "eng.harness.integrity",
  "requirement_ids": ["ENG-003"],
  "evidence_class": "hermetic",
  "platforms": ["Mac", "Win64"],
  "argv": ["{host_python}", "Scripts/check.py", "--self-test"],
  "case_ids": ["green_nonzero", "zero_empty", "latched_timeout"],
  "minimum_completed": 3,
  "timeout_seconds": 60,
  "artifact_schema": "harness-result.v1",
  "owner": "engineering"
}
```

## 5. Test the harness before trusting the game report

`run_scenarios.py` launches the exact shipping/test command as an argument vector, records the actual process exit code, and reads the actual engine-generated report through a measured adapter. A Python function returning `1` is not equivalent to its launcher process exiting with `1`; test the invocation used in production. Editor script APIs may report exceptions differently from commandlets, so qualify each supported route. [B03, B05]

Required harness cases, all runnable without Unreal through controlled fixture processes:

1. A child prints `PASS` and exits nonzero: failure/error, with raw exit preserved.
2. A child exits zero but emits no report, an empty report, or zero completed cases: incomplete/error.
3. A report has the wrong run ID, source digest, platform or scenario inventory: reject it.
4. An old successful report exists beside a newly failed run: only the new run's directory is consulted.
5. A child catches termination and exits zero after its deadline: timeout remains latched; it cannot become `PASS`.
6. A child starts a descendant that keeps writing or holding pipes: the owned process tree is bounded and cleanup outcome is recorded.
7. A malformed or partly written JSON report, duplicate case ID, unknown outcome or missing required field: reject the report.
8. A scenario is renamed or filtered out: named inventory fails even if total count stays constant.
9. The input manifest changes during the run: invalidate that result.
10. Two independent fixture runs overlap: neither can consume the other's completion state or cleanup targets.
11. Positive control: a fixed fixture containing N > 0 distinct expected case IDs passes the same validator used by the negative fixtures, with exit zero, `PASS`, and the exact expected ID set and count, so a harness that rejects everything cannot pass the negative cases vacuously (D-017).

Implement native process-tree supervision for each platform. On Windows, qualify a Job Object or an equivalently bounded native approach; on macOS, supervise only the launched process group. Never kill every process named UnrealEditor or Python. Record timeout start/end, termination escalation, child exit, remaining descendants and artifact completion separately. Keep deadlines explicit rather than borrowing ambient shell variables.

Non-vacuous engine cases also need positive controls. An occlusion test must first demonstrate the target was detected when exposed. A prohibited shortcut case must show a legal maneuver remains possible. A content validator must load a deliberately valid fixture and reject a separately controlled invalid fixture. Place the positive control in the same run where possible, so a disconnected or inert test system cannot turn all negative cases green. [B09]

## 6. Editor ownership and recovery

Parallel text work may occur in independent worktrees or disjoint owned paths. One operation owns a given editor process/project content tree at a time. C++ edits, map generation, Blueprint changes, navigation builds, cooking and binary-asset saves must not race one another in the same project instance.

The proposed `editor_lease.py` owns the editor process for the leased operation: every automated project writer uses this lease; it launches or attaches to the editor (attachment requires verified project identity and an idle-operation handoff), restarts it when a round trip requires, checks liveness by PID plus start time, and before any successor mutation verifies completion or termination of the previous operation and all associated writer processes, with unresolved ownership or activity blocking mutation (D-017). It uses exclusive atomic acquisition with an OS-held lock plus descriptive metadata. Metadata includes schema, project identity, host label, owning run and process identity/start time, operation ID, acquired/heartbeat timestamps, expected output paths and checkpoint locator. The OS lock establishes exclusivity; a JSON file or expired timestamp alone does not. The supervisor holds the lock through asynchronous editor work and its postcondition checks. A dead supervisor can release its OS lock while its editor operation remains active, so a replacement must establish editor quiescence before issuing another mutation. Qualify the locking implementation on both native filesystems before calling it safe.

Lease expiry is a suspicion signal. Recovery checks whether the exact owning process is alive, whether its operation is still executing in the editor, and whether uncommitted/unsaved assets remain. PID reuse cannot prove ownership; pair PID with start identity. Do not take an expired lease from a live owner. If ownership is ambiguous, preserve the state and report the unresolved operation while independent source work continues.

Every mutating editor operation has stable ID, preconditions, expected semantic postconditions, owned paths and a recovery action. Checkpoints transition through `prepared`, `running`, `outputs_observed`, `validated`, `complete`, or `failed`. Write intent before the operation and completion only after reading its postcondition from the actual editor/disk. A recorded intention to save is not proof the save happened.

On resume, reconstruct from the checkpoint plus current observed state. For a repeated map-generation request, locate stable recipe IDs and repair/reuse the intended actors; do not spawn another copy. Save generated assets in task-owned locations; preserve authored assets and previous qualified packages. Failed cooking cannot replace the last verified distributable. [B08]

Recovery tests must stop inside an operation after a meaningful side effect and before its completion record: after fixture asset creation, during multi-asset generation, before final manifest replacement, and during package production. Each test proves its named seam was reached. A generic process exit does not prove the crash occurred in the intended window. Fault-injection seams are fixture-only and rejected outside the dedicated test project/run directory. Resume must converge to one semantic result without duplicate assets or deletion of a bystander file.

## 7. Change classification and proportionate validation

`classify_change.py` examines the complete net changed-path set against the integration base, including additions, deletions, renames, untracked required inputs and generated-content changes. It produces a machine-readable gate selection and a short rationale. It does not infer risk from a commit title such as “docs only,” nor from scanning arbitrary scripts for filenames. [B04]

| Changed surface | Required verification |
|---|---|
| Ordinary Markdown under a closed documentation allowlist | Links, requirement/decision references, whitespace and scope consistency |
| `AGENTS.md`, runbook, acceptance criteria or evidence policy | Documentation checks plus independent review of changed operating/acceptance rules |
| `SPEC.md`, `BOT-CAST.md` or `DECISIONS.md` numeric seeds or rules | Documentation checks plus the affected product IDs from a maintained requirement-to-check map; any unmapped or unclassifiable seed or rule change selects broad gameplay validation (D-017) |
| Harness scripts, schemas, registry, locks or classifier | Hermetic harness checks plus the affected native adapter smoke; classifier changes default to broad selection |
| Gameplay C++, Blueprint logic, runtime configuration or personality data | Native compile, relevant named engine scenarios, packaged regression; personality changes add character evaluations |
| Map recipes, generated assets, collision or traversal definitions | Regeneration/idempotence, real content validation, traversal and combat scenarios, rendered inspection |
| Materials, animation, audio or UI | Asset/reference validation, package inclusion and rendered/input/audio evidence |
| Engine, compiler, SDK, plugin or inference dependency | Requalification spike, native Mac/Windows builds and packages, full required scenario matrix |
| Unknown path or ambiguous impact | Broad relevant product gate; no reduced classification by assumption |

Broad evidence requirements may be unavailable during iteration, but remain visible and block acceptance until executed. After integration/rebase, recompute classification and qualify the final source/content identities; a passing pre-integration tree does not qualify the integrated artifact. Avoid running duplicate expensive editor/build/performance jobs on one host. A cached prerequisite may be reused only when its exact immutable inputs, environment and required evidence class match; presence of a cache entry alone is insufficient.

## 8. Bot experiments must preserve identity and fairness

The seven named characters are product requirements, not seven cosmetic skins on a generic skill slider. Each experiment records the full profile vector, policy/controller revision, shared ability tuning, map/traversal revision, observation/action schemas, simulation configuration, seeds, native package hashes, opponent/team composition and any data/model provenance. Keep `arcl1ght` grenade expertise, `hotlap` improvised stunt driving, `stitch3r` machine-gun skill, `farsight` sniper/driver skill, `b0bbin` weaker play with occasional surprises and Russian-accented jokes, `ramrod` aggressive driving/machine-gun pushes, and `lattice` laser-trap play represented separately in the metrics.

Report dimensions independently: competence, fairness, recognizable identity, behavioral variety, recovery, frame cost, and player enjoyment. Kill/death ratio cannot stand in for any of the other dimensions. A more accurate sniper that reads a hidden position fails fairness regardless of its score. A better pathfinder that removes `hotlap`'s distinctive creative driving may be a regression. `b0bbin` must remain meaningfully weaker without becoming inert, deliberately throwing every fight, or depending on abusive stereotyped dialogue.

Create development, validation and sealed acceptance scenario manifests before substantial tuning. Partition by scenario family and geometry, not just random seed: held-out ramp approaches/landing areas, occlusion changes, target motion, cover layouts, blocked exits and ammunition pressure. Keep a controlled variant set of map geometry even if the shipped slice has one map. Otherwise a bot can memorize that map's test lanes while appearing adaptable.

For behavior cloning or learned policies, if later adopted, record dataset/source rights, capture conditions, observation privilege, trajectory IDs, filtering, train/validation splits, training configuration, checkpoints and inference compatibility per native platform. Keep related trajectories out of opposing splits. Publish no raw private voice/player recordings by default. A policy model cannot observe data the hand-authored controller is prohibited from receiving.

Test-runner ground truth is separate from bot observations. The evaluator may measure an unseen target's true position to detect cheating; the bot must receive only authorized perceptual/memory information. Audit the actual callers of aim, target selection, grenade prediction, vehicle destination and shared squad-information code. Testing one clean controller path cannot prove alternate constructors/debug paths do not bypass the boundary. [B07]

Use reproducible inputs and event traces, but do not promise bit-identical cross-platform Chaos replays. Record timestep/substepping/frame caps and compare bounded physical outcomes or distributions as appropriate. Pure math fixtures can require exact/tolerance-based values; complete physics play requires measured tolerances and sample counts established before candidate evaluation.

Human evaluation packets include package hash, scenario manifest, randomized presentation order, condition labels hidden during scoring, predeclared rubric, participant count and familiarity, duration, all scored sessions and uncertainty. Score recognizable characters without nameplates/voice cues in one condition so dialogue cannot carry the whole result. Also test the complete experience with names and banter restored. Preserve negative sessions and failed stunts, not just highlights. Agent reviewers may identify suspicious behavior; their prose is not a substitute for the user's judgment of these particular personalities.

## 9. Independent review and dispositions

Fable and Astra have complementary review assignments rather than interchangeable ceremonial passes. The implementer receives acceptance criteria and must gather its own evidence. A fresh reviewer inspects the fairness/information boundary, runtime lifecycle and packaged artifacts. The other reviewer challenges character identity, test blind spots, platform assumptions and claims in the documentation. Either model may fill either role according to the user's chosen workflow; record the actual model/version and context supplied.

Review packets include exact input/package digests, scoped source and relevant callers, requirement IDs, commands and raw reports, screenshots/clips, current residuals, and explicit questions. Ask reviewers to identify false claims and construct counterexamples, including a case not already represented by the author. Give reviewers evidence, not only the implementer's narrative. A read-only code review cannot supply an executed test receipt. [B07]

Track each finding as `accepted`, `rejected`, or `unresolved`. Accepted findings identify the fix and regression evidence. Rejected findings require a checked factual reason or a documented product tradeoff, with counterevidence and reopener. Unresolved findings retain the blocked measurement/decision and the acceptance criterion affected. Re-review the resulting patch and integrated artifact to convergence for material findings. Never write that Fable reviewed a package until an actual Fable review has occurred.

## 10. A small residual ledger that drives the next action

`Verification/residuals.json` is the single machine-readable queue. Each row has stable ID, affected requirement IDs, title, consequence, state, owner/decision holder, discovered source revision, evidence references, next executable action, gate it blocks, decision reference when dispositioned, and a concrete reopener. Keep defect severity, review effort and scheduling priority as separate fields. The highest review tier is not automatically the next product task. [B06]

Allowed states are `open`, `blocked_external`, `deferred`, `resolved`, and `accepted_limit`. `blocked_external` names the actual missing resource or user decision. `deferred` names its trigger/next milestone. `resolved` requires closure evidence bound to an artifact. `accepted_limit` names the decision and condition that would reopen it. An unavailable Windows machine is an external prerequisite, not a claim that Mac-only work meets dual-platform acceptance.

`validate_governance.py` rejects duplicate IDs, unknown states, missing references, unresolved mandatory requirements with no next action, and a task still marked blocked after its dependency has been resolved. Initially an empty residual list is valid only when there are no known unimplemented/unavailable mandatory obligations; the implementation kickoff should therefore have explicit prerequisite and unbuilt-gate rows. Do not create one row per unfinished line of code. Group by a deliverable or shared mechanism.

`DECISIONS.md` remains readable prose: problem, chosen behavior, alternatives, checked evidence, known cost, affected requirements and reopener. `IMPLEMENTATION-STATE.md` stays roughly two pages and links to the ledger. It records current gate, last qualified artifact, current owners, running editor/build processes, pending operation IDs, source/content divergence, next concrete command, and unavailable native evidence. Refresh before long operations and handoff. Completion updates the plan/checklist as well as the chronology. [B10]

## 11. Engineering acceptance inventory

These IDs are proposed acceptance requirements to register alongside the product cases. A planning document does not satisfy their execution condition.

| ID | Required observation |
|---|---|
| ENG-001 | Native toolchain reports on Mac and Windows, or explicit unavailable cells with blockers; dual-platform acceptance requires both qualified |
| ENG-002 | Measured automation create/save/restart/read-back and one real named test; preferred path or qualified fallback recorded |
| ENG-003 | All eleven supervisor integrity cases in §5, including the positive control, pass on both supported hosts |
| ENG-004 | Mandatory registry names/counts match actual executed reports; zero/filtered/wrong-run reports are refused |
| ENG-005 | Source/content changes invalidate old evidence; completed manifests identify actual packages and inputs |
| ENG-006 | Generation twice is semantically idempotent; generator-code changes invalidate qualification; authored bystander assets survive |
| ENG-007 | Two contending editor operations cannot both mutate; stale-owner recovery handles live owner, dead owner and PID reuse |
| ENG-008 | Named interruption seams prove recovery inside generation/checkpoint/cook actions without duplicate results |
| ENG-009 | Change classifier covers additions/deletions/renames/untracked inputs and defaults broadly on unknown paths |
| ENG-010 | Every mandatory unproved obligation has a linked actionable residual; resolved dependencies unblock the queue |
| ENG-011 | Fairness probe has a same-run visible positive control, an occluded negative case and actual observation/action provenance; it re-runs the decision step with all decision-affecting state reset (beliefs, random streams, clock, pending queries, caches, commitments, coordinator state), legal observations and public match state held fixed, differing only in hidden world state, and requires identical intents, compared before physical consequences (D-003) |
| ENG-012 | All seven exact profile IDs persist through recipe, generated asset, package roster and experiment records |
| ENG-013 | Development/validation/acceptance manifests are distinct and versioned; tuning exposure and reopened holdouts are recorded; acceptance layout seeds are sealed by the reviewer before tuning as a commitment binding seed, nonce, generator revision, parameters, splits and protocol, revealed and verified only at evaluation (D-018) |
| ENG-014 | Actual native rendered packages produce input, visual and performance evidence; headless results cannot fill those cells |
| ENG-015 | Independent findings have accepted/rejected/unresolved dispositions; material fixes are reverified against final identities |
| ENG-016 | Final report distinguishes implemented, automated-verified, native-packaged, human-evaluated and still unavailable claims |

## 12. Inspected reference sources

The twelve mechanisms above were derived from a read-only inspection of a private
reference project at a recorded revision. The requirement text in this document is
self-contained and portable: an implementer needs no access to that project.

The provenance record, including its exact revision, file paths and line ranges, is
held privately with the project owner and is not reproduced here. References `[B01]`
through `[B12]` in section 1 identify the inspected mechanism, as follows:

- **[B01]** Engineering playbook, measured-interface spike: record the observed adapter contract before writing against an API.
- **[B02]** Standing context and check registry: registration, non-vacuity, tier validation, explicit skipped/known-red accounting.
- **[B03]** Harness self-test fixtures: raw nonzero exits, misleading green output, timeout latching, zero exit after termination.
- **[B04]** Playbook, final-tree qualification: mechanical documentation-only change classification.
- **[B05]** Playbook, launcher exit semantics: compare expected outputs rather than trusting an input digest.
- **[B06]** Residual and queue consistency checks: unique closed rows, executable queue slots, stale block predicates, stated checker limits.
- **[B07]** Standing context, independent model review: reviewers cannot supply test evidence; guard and bypass inventories.
- **[B08]** Playbook, interruption seams inside operations, with evidence that the seam was reached.
- **[B09]** Playbook, non-vacuous qualification: zero exit with an empty consulted source, positive control in the same invocation.
- **[B10]** Playbook, targeted context and harness-owned checks; a standing limit on instruction growth.
- **[B11]** A retired-host override: one decommissioned private host is excluded from every operational role, and no historical command targeting it transfers to this project.
- **[B12]** Playbook, planned-versus-built distinction: decisions can describe unbuilt behavior, so verify the implementation before planning around it.
