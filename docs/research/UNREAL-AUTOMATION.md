# Unreal automation: offline implementation and reproducible evidence

Research revision **v0.2**, sources revisited 2026-09-07–08. Status: proposed execution contract. No Unreal installation, plugin compilation, editor connection, game build or runtime qualification was performed for this revision. Earlier inspection did not locate a complete engine/toolchain installation at the standard paths checked; current installation state still needs the implementation preflight.

[SPEC.md](../SPEC.md) defines the offline game; [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md) orders its implementation, and [DAY1-ENGINEERING.md](../DAY1-ENGINEERING.md) defines exact inputs, leases, recovery and evidence. The target is one human plus seven specific bots, native Mac/Windows packages, four vehicles and the specified Fort Crossing modes. The superseded online plan remains only in the [archive](../archive/v0.1-network-plan/README.md).

## 1. Recommended route

Use **UE 5.8.2 provisionally**, Epic's native Experimental MCP for editor discovery/interaction, checked-in Python/C++ recipes for repeatable asset work, and UBT/UAT plus project-owned scenario scripts for builds and acceptance. Epic's hotfix announcement establishes the release, not this project's compatibility. Its known issue `UE-377426` warns about Xcode 26.4 or newer and recommends the 26.1 line; use the exact platform guidance and installed-engine validation before compiling. [Epic 5.8.2 announcement and known issue](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335), [platform research](PLATFORMS-AND-AGENTS.md)

MCP is a development interface. The shipped bots use the local game controllers/policies in the specification; neither MCP nor a conversational model drives their frame-by-frame behavior. The game must start, play, restart and exit with all developer agents disconnected. A source-engine build is not required by this offline scope.

If native MCP lacks an operation, add a small editor Python/C++ operation. If its connection is unreliable on a platform, qualify the script/commandlet path there. Only then evaluate a pinned community bridge for a measured remaining gap. Do not load overlapping bridges by default or turn a convenience integration into a reason to drop Mac.

## 2. What Epic provides, and what must be measured

Epic documents a local unauthenticated HTTP/SSE service, normally at `http://127.0.0.1:8000/mcp`. The plugin identifier is `ModelContextProtocol`; toolsets use the registry. Default discovery exposes `list_toolsets`, `describe_toolset` and `call_tool`. Tool invocations run serially on the game thread; new reflected functions require editor restart. The selected engine's descriptors and observed schemas remain the authority. [Epic Unreal MCP guide](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor)

During preflight, record engine/build identity, enabled plugins, toolset catalog/schema digests and the observed target project. Keep discovery enabled and retrieve only relevant toolset schemas. Do not treat an advertised tool count or a response saying “connected” as successful editing. The needed operations are read state, generate/update assets, save, reload, inspect postconditions, start a named test and obtain its actual report.

A generated client config is a starting artifact. Preserve unrelated configuration and confirm that the agent starts in the intended workspace. The tool host must actually reach this workstation; a hosted review session's loopback address refers to its own environment. Give Fable exported artifacts when no qualified local integration exists. No public editor endpoint or new remote service is needed.

For missing editor functionality, Epic exposes `UToolsetDefinition` and reflected `AICallable` methods. A project tool should wrap a specific, validated operation with structured results. Compile reflected C++ helpers in a declared editor module/plugin, not an arbitrary scripts folder; Python registration likewise needs an actual qualified loader. [Epic toolset API](https://dev.epicgames.com/documentation/unreal-engine/API/Plugins/ToolsetRegistry/UToolsetDefinition)

## 3. Community options: conditional alternatives

These are maintainer descriptions re-opened during this revision, not tested compatibility results. None supplies the game's mechanics, personality design or acceptance evidence automatically.

| Option | Useful published capability | Project disposition |
|---|---|---|
| [db-lyon/ue-mcp](https://github.com/db-lyon/ue-mcp) | TypeScript/Node MCP process, C++ editor bridge, workflow/journal facilities and integration with Epic toolsets; README lists Windows UE 5.4–5.8 and Mac/Linux 5.6+ | First community candidate to spike if a documented native/script gap remains. Adds dependencies and another compiled integration. |
| [chongdashu/unreal-mcp](https://github.com/chongdashu/unreal-mcp) | Python/C++ bridge for actors and Blueprint work; README explicitly describes Experimental status and discourages production use | Reference material; insufficient reason to replace the native baseline. |
| [flopperam/unreal-engine-mcp](https://github.com/flopperam/unreal-engine-mcp) | Public local bridge and a separately described hosted Flop/Aura offering | The README says the hosted service and local Python server do not share code. Do not infer hosted capability, terms or availability from the public repository. |
| [sam-david/unreal-mcp](https://github.com/sam-david/unreal-mcp) | Multiple transports, editor Python/Remote Control and broad tooling | Its setup includes widened binding/remote execution. Those settings do not transfer into this project's local-only baseline. |

Select one immutable commit/release before any adoption; inspect its manifest, lockfile, plugin descriptors, startup changes and required engine plugins. Moving README/raw-file snapshots can disagree. Do not blend one revision's setup with another revision's dependencies. A claimed rollback feature cannot guarantee restoration of unsaved authored assets or another agent's changes.

The fallback spike must resolve a named missing operation on both relevant hosts, survive save/restart/read-back, reproduce content without duplication, bound stalled work, and disappear from delivered game packages. Preserve the native/script fallback and record the extra maintenance cost. Exact patch compatibility remains unverified until that spike runs.

## 4. Scripts own repeatability

Use the existing canonical proposed files rather than introducing another build manager:

| Project entrypoint | Responsibility and evidence |
|---|---|
| `Scripts/doctor.py` | Read-only prerequisites, exact lock validation and unavailable dependencies |
| `Scripts/build.py` | Native UBT/UAT argument vectors, raw exits, build receipts and fresh package outputs |
| `Scripts/generate_content.py` | Engine Python recipe execution with stable IDs and explicit input/output ownership |
| `Scripts/validate_content.py` | Actual loaded-asset/property/reference inspection and normalized semantic inventory |
| `Scripts/run_scenarios.py` | Owned processes, deadlines, named case inventory and actual report parsing |
| `Scripts/collect_evidence.py` | Source/content/package hashes, per-run artifact index and finalized manifest |

Epic documents editor Python through full-editor script execution and the `pythonscript` commandlet. Those are different environments; explicitly load the required map and verify required subsystems. Python is editor tooling, not packaged game logic. Avoid early startup execution that assumes the world is already ready. [Epic editor Python guide](https://dev.epicgames.com/documentation/en-us/unreal-engine/scripting-the-unreal-editor-using-python)

UBT/UAT perform compilation and build/cook/stage/package operations. Verify installed command help and record the actual Mac/Windows executable paths and arguments. Run native commands on their corresponding hosts. An editor play session does not establish that cooked references exist in the standalone package. [Epic build operations](https://dev.epicgames.com/documentation/en-us/unreal-engine/build-operations-cooking-packaging-deploying-and-running-projects-in-unreal-engine)

Rules/content/scenario assertions belong in the project test suite, using Unreal automation where appropriate. Engine automation supplies mechanisms for tests and screenshots; the implementer must supply game-specific assertions and fixtures. [Epic Automation Test Framework](https://dev.epicgames.com/documentation/en-us/unreal-engine/automation-test-framework-in-unreal-engine)

## 5. Content operations and async ownership

An editor operation records run/operation ID, project identity, expected input revision, owned paths, preconditions and semantic postconditions. A useful result distinguishes `started`, `running`, `completed`, `failed` and `unknown`, with job ID, modified assets, warnings and evidence paths. “Request accepted” cannot complete a save, shader compile, navigation build, cook or automated test.

Hold the DAY1 editor lease through actual asynchronous completion and postcondition observation. The supervisor's OS lock prevents concurrent operations only while it is alive. If it dies while editor work continues, a successor must establish editor quiescence before replaying anything. A timed-out client does not prove the engine operation stopped; record uncertainty and inspect it. Never kill all editors or delete a lease merely because its timestamp expired.

Recipes create/update by stable ID, preserve authored bystanders, save explicitly and validate after a clean reopen. Generation repeated twice must preserve the normalized semantic inventory; byte-identical Unreal packages are not promised. Generator-code changes invalidate old qualification even when recipe input is unchanged.

For Fort Crossing and the cast, inspect all seven exact profile IDs, both modes, objective/spawn anchors and four vehicle definitions. Vehicle inventories include seats, wheel bones/contact setup, tire states and destruction/respawn dependencies. The six-wheel truck's four-destroyed-tire propulsion threshold must remain distinct from the other rigs. A tool successfully placing vehicle meshes does not prove handling, occupancy, damaged grip or cleanup.

## 6. Local authority and packaged exclusion

The editor bridge has broad local process authority. The project requires effective loopback binding and no exposure beyond the workstation. Inspect the actual listener configuration; do not broaden it to make a remote reviewer connect. Python execution, console execution and arbitrary object invocation must be classified by what they do, not by friendly tool names.

`call_tool` is a dispatcher: allowing that outer name alone does not constrain the inner toolset, function or arguments. A review-only interface must enforce allowed nested operations through a tested catalog/proxy, or use exported artifacts. Log the resolved inner operation and bounded arguments. A prompt saying “read only” is not an enforced restriction.

Epic's MCP includes runtime modules, so disabling auto-start is insufficient. The module split allows packaged applications to contain server functionality. Inspect the selected descriptors and build receipts, then explicitly exclude authoring plugins, `ModelContextProtocol` and `ModelContextProtocolEngine` from delivered Mac/Windows targets using a mechanism qualified against the pinned engine. [Epic module index](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/ModelContextProtocol), [Epic runtime availability](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor#editor-and-runtime-availability)

Prove exclusion through dependency/build receipts and packaged module/binary inspection, including monolithic-link evidence where applicable. Complement that with launching the actual package and observing that no authoring listener starts. A closed port alone does not prove absent code. Runtime game modules must not depend on project editor modules, `UnrealEd`, authoring toolsets or editor Python. Test helpers with special powers stay outside delivered builds as well.

## 7. Qualification and recovery acceptance

The first implementation checkpoint must produce these concrete observations:

1. A minimal native C++ project builds on each target host, or that cell remains explicitly unavailable.
2. The selected editor path identifies the correct project, creates a fixture asset, saves it and reads its properties after restart.
3. The same recipe runs again without duplicate assets; a bystander asset survives.
4. One named real engine test emits a fresh report bound to the run; a deliberately invalid fixture is rejected.
5. An interrupted operation resumes safely, with evidence that the cut occurred inside the named step.
6. Native packages launch independently of the editor/agents; authoring modules are excluded and no bridge is required for play.

Preserve raw subprocess exits and engine reports. Reject missing, stale, zero-case or wrong-run reports even when a tool's text says success. Keep hermetic, engine, package, rendered and human evidence separate. The completed game still needs actual steering/aim/voice/character evaluation; a catalog, scene screenshot or automated bot match cannot establish humanlike play by itself.

When an operation repeatedly fails, isolate the smallest reproducible defect, inspect the actual interface and use the qualified fallback. Preserve failure evidence, update `IMPLEMENTATION-STATE.md` and the residual ledger, then continue independent work. One implementation kickoff can support this correction loop; it cannot remove the need to measure it.
