# Unreal Engine automation decision and execution contract

Research date: **2026-09-07**. Status: **research and proposed specification; no Unreal installation, plugin compilation, editor connection, game build, or runtime validation was performed for this document**.

This document supports the modern Mobile Forces project specification. Its platform assumption is native Windows and Apple Silicon Mac clients from the first playable, initially 4v4, with a Windows dedicated server. It does not define game design or authorize implementation. Fable's independent review remains a subsequent user-run step.

## 1. Decision

Use **Unreal Engine 5.8.2 as the provisional pinned baseline**, Epic's **native Unreal MCP** for editor discovery and controlled interactive work, checked-in editor Python/C++ tools for repeatable content production, and UBT/UAT plus test harnesses for authoritative build and acceptance evidence. UE 5.8.2 is the latest released hotfix found in the official sources checked on the research date; this is a bounded finding, not a claim about future releases.

Do not choose a historical community MCP bridge merely because a 2025 tutorial calls it “Unreal MCP.” Epic now supplies a server in the engine. Conversely, the official server is Experimental and cannot make an entire multiplayer game dependable by itself. The implementation should remain buildable and recoverable when every MCP client is disconnected.

The strongest community fallback investigated is **David Lyon's `db-lyon/ue-mcp`**, subject to a version-pinned evaluation on both operating systems. It has useful workflow and lifecycle facilities, but adds a Node dependency tree and a compiled editor bridge. Adopt it only for a measured capability gap. Do not enable multiple overlapping editor bridges by default.

The practical meaning of a successful “one shot” is **one complete implementation brief driving a resumable build-and-test sequence**, with bounded repair loops. It is not a promise that a single model response or an uninterrupted chain of editor mutations yields a finished game.

## 2. Current engine evidence

| Finding | Evidence and date | Implication |
|---|---|---|
| UE 5.8 is released, with integrated MCP functionality | Epic's [release announcement](https://forums.unrealengine.com/t/unreal-engine-5-8-released/2729274), dated June 17, 2026 | Evaluate the native engine feature before third-party substitutes. |
| UE 5.8.2 is released | Epic's [5.8.2 hotfix announcement](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335), August 25, 2026; [hotfix index](https://forums.unrealengine.com/tag/hotfix/11407) checked September 7 | Start qualification at 5.8.2; record the installed changelist and build identifier. |
| Mac compiler incompatibility is documented | The hotfix's known-issue reply identifies `UE-377426`: compilation can fail with Xcode 26.4 or newer. It recommends the 26.1 line | “Install the latest Xcode” is an incorrect bootstrap instruction for this baseline. |
| Current Mac toolchain and renderer constraints | Exact requirements and their primary source are consolidated in [platform research](PLATFORMS-AND-AGENTS.md#1-mac-and-windows-recommendation) | Provision and record the supported compiler/SDK combination before generating project code. |

Use the version-specific platform requirements linked above and the selected rendering baseline in the main specification. These constrain provisioning and visual features; they do not establish that the whole game must be Windows-only.

**Mac availability boundary:** Epic's MCP guide includes Unix setup and identifies no Windows-only restriction, but the public plugin API index does not establish a complete Mac binary/support matrix. Exact UE 5.8.2 Mac plugin availability must therefore pass the local preflight below. Do not state that native MCP was tested on this Mac. If that feature fails on Mac, continue native Mac development through editor Python and commandlets while resolving MCP separately.

## 3. Evidence terminology

| Label | Meaning in this document |
|---|---|
| Official documentation | Epic describes the capability or requirement; not a local test result. |
| Maintainer claim | A repository owner or product publisher describes its own capability. |
| Code inspection | Source text or a package manifest was read; neither compiled nor executed. |
| Maintenance evidence | A dated commit/release page was inspected; activity alone does not prove quality. |
| Proposal | A project-specific decision or acceptance requirement introduced here. |
| Unverified | Not established by the inspected source or not exercised on the intended host. |

GitHub search pages, raw files, and documentation can represent different cached revisions. During research, `db-lyon/ue-mcp`'s commit page showed a September 6 release commit for 1.3.5, while its raw `main/package.json` returned 1.3.8-beta.2. That discrepancy is recorded rather than silently reconciled. A future installer must resolve one immutable commit and inspect the files at that commit. The inspected moving-branch URLs below are evidence pointers, not dependency locks.

## 4. What Epic supplies now

Epic documents an Experimental HTTP/SSE server at `http://127.0.0.1:8000/mcp`, without authentication. `ModelContextProtocol` is its plugin identifier. Enable Unreal MCP plus `AllToolsets`; `ToolsetRegistry` supplies discovery. Default tool search exposes `list_toolsets`, `describe_toolset`, and `call_tool`. Calls execute serially on the game thread; clients must avoid overlapping invocations. New reflected functions require restart. Runtime server modules exist, so packaged builds need explicit exclusion. [Epic Unreal MCP guide](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor)

The public [plugin index](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/ModelContextProtocol) identifies `ModelContextProtocol`, `ModelContextProtocolEngine`, and `ModelContextProtocolEditor`, and lists Engine Asset Definitions as a dependency. This index is not the full selected-toolset dependency graph. Record the installed `.uplugin` descriptors and enabled plugins during preflight; the local engine snapshot is the authority for that build.

For a custom project toolset, use `UToolsetDefinition` with static reflected methods marked `AICallable`; its documented header is `ToolsetRegistry/ToolsetDefinition.h`. The [API reference](https://dev.epicgames.com/documentation/unreal-engine/API/Plugins/ToolsetRegistry/UToolsetDefinition) also exposes `AIIgnore` and toolset versioning. This is a smaller extension point than maintaining an independent transport when only one project operation is missing.

Keep tool search enabled. Discover the installed schemas and save a catalog for the run. Never fabricate tool names from a video, assume every toolset is enabled, or hard-code a marketing tool count as a compatibility check. A catalog proves discoverability; each required operation still needs a smoke test.

### Native connection recipe to qualify

In a disposable qualification project, enable Unreal MCP and All Toolsets, then restart. Execute these documented console commands as appropriate:

```text
ModelContextProtocol.StartServer 8000
ModelContextProtocol.GenerateClientConfig Codex
ModelContextProtocol.RefreshTools
ModelContextProtocol.StopServer
```

Start the agent from the project root. Existing Codex TOML is not overwritten by the generator: inspect and merge the server entry without deleting unrelated configuration. These commands and the config caveat come from the [official setup guide](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor). Client integration availability must be checked in the actual Astra/Fable host; model names do not imply network access to this workstation.

## 5. Comparison of community MCP options

All four projects below are independently maintained community software; none is Epic's native server. Tool totals are publisher descriptions at the inspected snapshot, not coverage measurements or comparable denominators.

| Option / publisher | Architecture and declared range | Maintenance and license evidence | Disposition |
|---|---|---|---|
| [`db-lyon/ue-mcp`](https://github.com/db-lyon/ue-mcp), David Lyon | Node MCP process plus C++ editor WebSocket bridge. README declares Windows 5.4–5.8, Linux/macOS 5.6+, and native Epic toolset integration on 5.8+ | [Commit history](https://github.com/db-lyon/ue-mcp/commits/main/) shows September 6, 2026 activity, including `a74a624` for release 1.3.5. [Package manifest](https://raw.githubusercontent.com/db-lyon/ue-mcp/main/package.json) identifies David Lyon and MIT | Best optional community candidate in this review; qualify an immutable release on both OSs. |
| [`chongdashu/unreal-mcp`](https://github.com/chongdashu/unreal-mcp), Chong Dashu | Python/FastMCP to a bundled C++ TCP bridge, declared UE 5.5+, basic actors, Blueprint authoring and graph manipulation | [Commit history](https://github.com/chongdashu/unreal-mcp/commits/main/) most recent displayed main commit is April 22, 2025, `4e5f00d`. README says MIT and explicitly discourages production use | Useful historical reference; weak default for a 5.8.2 project. Latest-engine compatibility is unverified. |
| [`flopperam/unreal-engine-mcp`](https://github.com/flopperam/unreal-engine-mcp), Flopperam; visible maintainers include `chris-gong` and `rthomas24` | Public local Python/C++ bridge is distinct from hosted Flop MCP/FlopAI. README's agent range is 5.5–5.7 | [Commit history](https://github.com/flopperam/unreal-engine-mcp/commits/main/) shows June 26, 2026 acquisition-banner edits; earlier visible code includes January 14 Mac include fixes. README says MIT for local repo | Do not equate hosted demos with the local code. Prefer Epic for baseline. |
| [`sam-david/unreal-mcp`](https://github.com/sam-david/unreal-mcp), Sam David | TypeScript MCP, Python remote execution, Remote Control, optional C++ bridge and UAT subprocesses. Claims 127 tools/16 subsystems; beta validation statement names UE 5.6 | [Commit history](https://github.com/sam-david/unreal-mcp/commits/main) shows March 28, 2026 latest displayed commit `8b88ec3`. [Manifest](https://raw.githubusercontent.com/sam-david/unreal-mcp/main/package.json) says version 0.1.0, MIT, Node >=18 | Breadth is interesting; exact 5.8.2/Mac support and execution safety remain unproven. Not preferred. |

### 5.1 David Lyon: concrete strengths and limits

The [configuration document](https://github.com/db-lyon/ue-mcp/blob/main/docs/configuration.md) describes per-project ports, connection identity records, bridge deployment, and an `execute_python` escape hatch. It also describes setup that may install hooks or offer GitHub OAuth. Those optional external workflows are not required for this game and should stay disabled unless separately wanted. This avoids confusing editor automation with public issue submission or account integration.

Code inspection of [`BridgeServer.cpp`](https://raw.githubusercontent.com/db-lyon/ue-mcp/main/plugin/ue_mcp_bridge/Source/UE_MCP_Bridge/Private/BridgeServer.cpp) found `PLATFORM_MAC` socket branches, an explicit game-thread executor, request-size bounds, connection shutdown handling, and rejection of WebSocket upgrades carrying an Origin header. These are substantive mechanisms, not a claim of complete security or tested Mac support.

[`EditorHandlers.cpp`](https://github.com/db-lyon/ue-mcp/blob/main/plugin/ue_mcp_bridge/Source/UE_MCP_Bridge/Private/Handlers/EditorHandlers.cpp) registers execution of Python and Python files; the handler passes supplied code to the embedded interpreter. This is code execution with editor-process access. A narrow tool name is not a sandbox. Its source also exposes runtime world inspection and test operations, but their correctness for this game's multiplayer scenarios must be proved independently.

The [README](https://github.com/db-lyon/ue-mcp) describes guards, idempotency conventions and rollback. Treat these as workflow features, not a guarantee that saved binary assets or unknown concurrent user edits can always be restored. The inspected raw manifest requires Node >=20, while the viewed README says >=18: select the manifest from the actual pinned release, not a blended dependency recipe.

A maintainer-filed [protocol/lifecycle issue](https://github.com/db-lyon/ue-mcp/issues/821), opened August 4 and marked closed through #852, records past framing, thread-lifetime, origin and negotiation defects. The currently inspected bridge contains related repairs. This supports active maintenance and the need to pin versions; it does not establish that older releases are safe or that all remaining paths were audited here.

### 5.2 Chong Dashu: narrower and older

The inspected [`UnrealMCPBridge.cpp`](https://raw.githubusercontent.com/chongdashu/unreal-mcp/main/MCPGameProject/Plugins/UnrealMCP/Source/UnrealMCP/Private/UnrealMCPBridge.cpp) binds `127.0.0.1:55557`, dispatches named command groups and posts work to `ENamedThreads::GameThread`, then waits on a future. No authentication logic was visible in that file. It does not by itself establish behavior under shutdown, stalled editor ticks, or concurrent clients.

This snapshot supplies basic construction operations; adding many small Blueprint nodes is still a long, stateful editing transaction. For this project, reusable C++ behavior plus data assets is easier to diff, rebuild and review than an improvised graph assembled over dozens of remote calls. That is a project architecture judgment, not a defect claim about all Blueprint workflows.

### 5.3 Flopperam: distinguish product from repository

The [README](https://github.com/flopperam/unreal-engine-mcp/blob/main/README.md) states that Flopperam was acquired by Aura and that hosted Flop MCP shares no code with its local Python server. The hosted route uses an API key and a separately installed plugin. Its broader toolset and autonomy claims cannot be inferred from the public repository's license or code. Hosted product availability, pricing, service behavior and UE 5.8.2 compatibility were not tested.

The local [`EpicUnrealMCPBridge.cpp`](https://raw.githubusercontent.com/flopperam/unreal-engine-mcp/main/UnrealMCP/Source/UnrealMCP/Private/EpicUnrealMCPBridge.cpp) source uses a loopback TCP endpoint and game-thread dispatch. Despite the filename prefix, this is Flopperam repository code, not Epic's official MCP plugin. Visible command groups cover actors, Blueprints, materials and graph edits; they do not demonstrate a packaged multiplayer acceptance harness.

A hosted dependency would require evaluating project-data handling and obtaining continued service access. There is no identified capability here necessary enough to justify that dependency in the first playable.

### 5.4 Sam David: broad surface with transport caveats

The [README](https://github.com/sam-david/unreal-mcp) recommends enabling remote Python execution and changing multicast binding to `0.0.0.0`; its troubleshooting also suggests disabling a firewall. These instructions expand network exposure. Do not copy them into the project's default setup. The same README notes an optional C++ plugin for graph nodes, so “no mandatory plugin” should not become “all features require no C++ compilation.”

The [manifest inspected](https://raw.githubusercontent.com/sam-david/unreal-mcp/main/package.json) depends on `unreal-remote-execution`, the MCP SDK and WebSockets, supporting the described external bridge architecture. Attempts to fetch individual transport source files were unsuccessful through the research browser; their authentication and process-spawn implementation was not verified. No runtime safety conclusion follows from package presence or the tool-count table.

## 6. Native automation without MCP

| Mechanism | Good use here | Boundary and evidence |
|---|---|---|
| C++ source and build targets | Combat rules, movement, vehicle/seat state, replication, deterministic test fixtures | Reviewable project source; compile through UBT. Source compilation remains necessary even if an editor agent generated assets. |
| Editor Python | Import/reimport, data assets, material instances, blockout generation, audits and export of semantic manifests | Epic documents `PythonScriptPlugin`, an embedded interpreter, full-editor `-ExecutePythonScript` and headless `-run=pythonscript -script=...`. Python is editor tooling, not packaged gameplay logic. [Python guide](https://dev.epicgames.com/documentation/en-us/unreal-engine/scripting-the-unreal-editor-using-python) |
| C++ editor module/commandlet | Operations missing from Python; explicit asset generators and validators | Keep editor dependencies out of runtime modules. Add only when the project has a concrete gap. |
| UAT `BuildCookRun` | Repeatable build, cook, stage, package and archive | Packaging is a separate gate from an editor play session. [Epic build operations](https://dev.epicgames.com/documentation/en-us/unreal-engine/build-operations-cooking-packaging-deploying-and-running-projects-in-unreal-engine) |
| Automation/Functional Testing | Rules, map content and engine-integrated scenarios | The framework supports unit, feature, content stress and screenshot testing; a smoke flag is a speed promise, not comprehensive coverage. [Epic test framework](https://dev.epicgames.com/documentation/en-us/unreal-engine/automation-test-framework-in-unreal-engine) |
| Gauntlet plus a project scenario harness | Start packaged server/client processes; collect results and terminate orphaned processes | Gauntlet manages sessions; it does not invent gameplay assertions. [Gauntlet overview](https://dev.epicgames.com/documentation/unreal-engine/gauntlet-automation-framework-overview-in-unreal-engine?lang=en-US) |
| Remote Control HTTP | A narrow, existing exposed-property or function workflow if needed | Epic's quick start uses `WebControl.StartServer` and port 30010. This is a different API from native MCP and is not required by the proposed baseline. [Remote Control quick start](https://dev.epicgames.com/documentation/unreal-engine/remote-control-quick-start-for-unreal-engine?lang=en-US) |
| Visible editor interaction | Examine composition, animation, input feel, audio and presentation | Screenshots and runtime recordings are essential evidence; fragile coordinate clicking should not carry reproducible asset generation. |

Python commandlets do not automatically load the desired level. Scripts must select the map explicitly. Avoid launching Python through early `ExecCmds` startup injection; Epic warns it can run before the editor is ready. Full-editor script execution and commandlet execution are different execution environments and need separate qualification. [Python guide](https://dev.epicgames.com/documentation/en-us/unreal-engine/scripting-the-unreal-editor-using-python)

## 7. Proposed repository contract

The following are **deliverables to implement later**, not files claimed to exist already. [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md#2-durable-control-files) owns the canonical paths; use one control system:

```text
PROJECT-LOCK.json                    # Engine/toolchain/content and capability identity
IMPLEMENTATION-STATE.md              # Current gate, owners, jobs and recovery state
DECISIONS.md                         # Accepted deviations and their evidence
ContentRecipe/assets.json            # Content provenance, stable IDs and ownership
ContentRecipe/quarry_exchange.json   # Map-generation inputs
Scripts/doctor.py                    # Read-only prerequisite and lock validation
Scripts/build.py                     # Native build/cook/package wrapper
Scripts/generate_content.py          # Editor entry; calls reusable authoring helpers
Scripts/validate_content.py          # Asset/reference and semantic validation
Scripts/run_scenarios.py             # Supervised native test processes
Scripts/collect_evidence.py          # Report verification and evidence index
Build/AgentTools/                    # Custom editor-only tool sources if needed
Saved/Verification/<run-id>/         # Machine-generated evidence, not source
docs/evidence/<gate-id>.md           # Reviewed gate result and artifact pointers
```

Use the game/module identifiers selected by the main specification. Project-owned scripting should use a neutral prefix, not impersonate an engine API. Centralize paths in configuration instead of embedding an absolute home path or one Windows drive letter in generated code. Keep catalog snapshots and generated semantic reports under the run's verification directory; reference their hashes from the lock/evidence index. Do not create another toolchain lock or competing content manifest.

### `PROJECT-LOCK.json`

Record:

1. Engine semantic version, changelist, build ID, installation origin, and optional source commit.
2. Host OS version and architecture; target OS/architecture; compiler, SDK and build-tool versions.
3. Project template/sample identity and exact content version, if used.
4. Every enabled plugin, version, installation origin, architecture support, and editor/runtime category.
5. Native MCP endpoint and catalog hash; optional community repository commit/package-lock hash.
6. Relevant scalability settings and content-generation seeds.

The verifier must fail on unresolved placeholders or a version mismatch. A `.uproject` association of `5.8` does not alone pin hotfix, changelist or plugin binary compatibility. Treat an engine upgrade as a separate qualification run, not an automatic repair attempt.

### Wrapper behavior

Every wrapper must accept explicit project, engine, target and output paths; print a reproducible argument vector; spawn subprocesses without shell interpolation; preserve full logs; expose exit status; and enforce a timeout with child-process cleanup. It must check required tools before editing content. A failure must leave a report with stage, error, last completed checkpoint and recovery instruction.

Command shapes for qualification, with installation-specific executable paths resolved by preflight:

```text
<Editor-Cmd> <Project.uproject> -run=pythonscript -script=<absolute-script.py> -unattended
<Editor-Cmd> <Project.uproject> -ExecutePythonScript=<absolute-script.py>
<RunUAT> BuildCookRun -project=<Project.uproject> -platform=<Win64|Mac> -clientconfig=Development -build -cook -stage -pak -archive
```

These are argument templates, not copy/paste commands or validated invocations. The wrapper must supply correct quoting, output directory, map/cook policy and platform options after verifying the installed UAT help. Server packaging must use the actual dedicated-server target; substituting a client package with `-server` at runtime does not prove the intended server target was built. The Mac wrapper must resolve `RunUAT.sh` and the actual Mac editor executable; it must not invoke a Windows `.exe` path copied from documentation.

## 8. Preflight before game implementation

Preflight is the first execution milestone, not a reason to drop Mac from scope.

| Check | Required evidence | Failure policy |
|---|---|---|
| Disk and memory | Free-space snapshot, engine/content/cache/build budget, memory-pressure baseline | Provide adequate workspace storage before installation; do not begin a large source build on an unbudgeted disk. |
| Native compiler | Exact Xcode/toolchain+SDK on Mac; supported MSVC+SDK on Windows; one minimal C++ compile | Install/configure prerequisites before game source. Command Line Tools alone are not accepted as a completed Xcode setup. |
| Engine startup | Fresh C++ project opens, exits and reopens with a clean log | Resolve engine/bootstrap defect in this empty test project. |
| Native MCP | Inspect installed descriptors, start the server, initialize, discover toolsets, capture schemas on both OSs | Fall back to editor scripting if MCP fails; preserve error and versions. |
| Correct project binding | Read project path, engine identity and map, compare to expected values | Stop before mutation on mismatch. Unique ports alone are insufficient identity evidence. |
| Binary persistence | Create a qualification asset, save, close editor, reopen, inspect expected values | A tool success response without persistence fails qualification. |
| Script repeatability | Run the same content generator twice, compare semantic outputs | Duplicate assets, name suffixes or unexpected dirty packages fail qualification. |
| Native packaging | Package a minimal native Win64 client and arm64 Mac client, launch each outside editor | Do not count editor startup or cross-compiled artifacts as native runtime evidence. |
| Network baseline | Both platform clients connect to the Windows server and exchange a minimal replicated state | Mac support remains an incomplete gate until exercised. |

The research coordinator reported an M-series host with 24 GB RAM, limited disk available, and no complete Xcode/UE install at typical locations. Those are session observations from the parent investigation, not probes performed by this document's author. Recheck them at execution time. They indicate provisioning and workload-budget constraints; they do not establish that the Mac hardware is incapable of the scoped game.

## 9. Editor ownership and agent collaboration

Assign three logical responsibilities without assuming a particular product API:

| Responsibility | Work | Boundary |
|---|---|---|
| Astra implementation lead | C++ architecture, runtime code, build wrappers, integration decisions | Delegates disjoint text-source work; owns integration and task state. |
| Editor operator | Execute approved content manifests and inspect the current editor | Exactly one process/agent owns mutations of one editor at a time. |
| Fable reviewer | Adversarial review of the spec, later source, evidence, omissions and acceptance claims | Receives the repository/evidence bundle; does not require a second simultaneous editor writer. |

If Fable runs in a hosted environment, `127.0.0.1` refers to that environment, not the Mac. Give the reviewer artifacts or run a local compatible client on the workstation. Do not expose the unauthenticated editor endpoint to make a cloud agent reach it. MCP connects clients to tools; it does not provide remote desktop, shared filesystem, game networking, or automatic model orchestration.

Parallel work should primarily occur in text source, with explicit file/module ownership. Binary `.uasset` and `.umap` files require an owner or lock. Separate editor instances in separate checkouts can author disjoint content only when dependency and integration rules are explicit. Two agents must never save different in-memory versions of the same asset.

Proposed editor lease fields: `run_id`, `agent_id`, canonical project path, editor PID, process-start identity, engine build ID, current world, expiry and last heartbeat. A stale lease is investigated against the actual process before takeover; a recycled PID does not establish ownership. If a client disconnects, inspect state before releasing the lease or replaying a mutation.

## 10. Reproducible asset authoring

The content generator must consume a checked-in manifest containing stable logical IDs, package paths, class IDs, mesh/material references, transforms and data values. Save the manifest revision with the output report.

Required algorithm:

1. Validate the entire manifest and dependency availability before mutation.
2. Compute the intended change set and reject collisions with assets outside the generator's ownership.
3. Load/create packages by exact path; find placed actors by stable project ID, not a generated display label.
4. Update owned objects in place. Re-running the same manifest must not produce `_2` actors, extra components, duplicate bindings or new material assets.
5. Build dependent assets in topological order; distinguish asset creation, Blueprint compile and runtime instantiation.
6. Save only intended packages; collect failures individually.
7. Export semantic state, then reopen in a fresh editor process and validate it again.

“Deterministic” means a defined semantic result for the same inputs and engine build. Unreal package bytes, GUIDs, derived data, physics playback and GPU images may not be byte-identical. Compare named properties, counts, references, ownership and invariant outcomes where byte identity is not a meaningful oracle.

Use generated blockout geometry and data-driven setup to achieve playability before optional asset acquisition. External assets must carry provenance, license and checksum in `ContentRecipe/assets.json`. Do not replace missing production content with an untracked download or a silently different asset.

## 11. Proposed custom tool contract

Add project tools only where existing editor APIs leave a repeatable gap. Prefer one tool invocation that executes a known script/manifest to dozens of ad hoc node mutations. Suggested logical operations, whose actual engine schemas must be implemented and discovered:

| Proposed operation | Input | Postcondition |
|---|---|---|
| Inspect project | Expected canonical root | Engine/project identity, loaded map, PIE state, dirty packages and enabled plugin versions returned |
| Plan content changes | Manifest path and hash | Complete change list and ownership conflicts; no mutation |
| Apply content manifest | Manifest hash, operation ID, expected predecessor revision | Owned assets updated; per-package result; no undeclared deletion |
| Validate content | Map/package scope and expected manifest hash | Typed invariant failures, dependency errors and semantic snapshot |
| Capture runtime evidence | Scenario ID, client/world identifier and camera/view mode | Timestamped screenshot/video reference and scenario context |
| Report operation | Operation ID | `not_started`, `running`, `succeeded`, `failed` or `unknown`; never infer cancellation from timeout |

Mutation replies should contain operation ID, before/after revision, changed packages, warnings, elapsed time and result paths. A JSON success envelope is only transport success until a separately read postcondition confirms the operation.

For asynchronous work, the tool should start a bounded job, return its ID and let the caller poll. Avoid blocking the game thread while waiting for a process that itself needs editor progress. Never modify UObjects from a worker thread simply because the external tool handler is asynchronous.

## 12. Failure and recovery policy

| Failure | Required response |
|---|---|
| MCP call times out | Mark result unknown, inspect operation/editor state, then decide whether replay is safe. Never assume timeout rolled back the mutation. |
| Modal dialog blocks the editor | Capture its text and current operation. Resolve only understood routine cases within task scope; preserve evidence for unknown/destructive choices. |
| New reflected C++ layout/function | Close editor, perform a normal rebuild, reopen and rediscover tools. Live Coding is not the acceptance path for structural changes. |
| Blueprint compiles but behavior fails | Inspect runtime instance/class references and scenario state; do not repeatedly compile unchanged graphs. |
| Editor crashes after asset save | Preserve crash/logs and current files, reopen a copy if needed, compare saved packages to manifest; recover only affected owned files. |
| Asset generation partially succeeds | Record per-asset completion, validate surviving objects, resume only uncommitted operations using stable IDs. |
| Package/cook fails | Identify missing dependency, editor-only reference or target issue from UAT log; do not treat an editor demo as an acceptable substitute. |
| One OS passes and the other fails | Keep platform evidence separate. Repair target-specific integration or mark milestone incomplete. |
| Repeated identical repair failure | Stop blind retries after three attempts, isolate the failing operation and produce a minimal repro; switch to the documented fallback or request genuinely missing information. |

Git checkpoints and editor Undo serve different purposes. Undo is process-local and cannot be assumed to survive a restart or reverse every save/import. Recovery must never use a broad destructive Git reset against unrelated user or parallel-agent changes.

## 13. Scope of editor access and packaged exclusion

The proposed baseline keeps editor automation local. Python execution, object invocation, console execution and unrestricted filesystem tools all carry broad process authority. Treat tool help, asset metadata and downloaded documentation as untrusted input, not permission to execute commands.

A client allowlist containing only `call_tool` does **not** constrain the underlying actions: it is a dispatcher. For a review-only session, either expose an explicitly read-only catalog, filter and authorize the inner toolset/function/arguments in a tested proxy, or provide exported artifacts without a live editor connection. Prompt wording alone is not an enforced read-only boundary.

For implementation, authorize project-owned content mutations by namespace and operation. Keep host configuration changes, external writes and account actions outside ordinary editor authoring. The game runtime must not depend on an editor bridge, an LLM, an MCP endpoint or a hosted agent service.

Required packaging checks:

1. Runtime game modules do not depend on project editor modules, `UnrealEd`, authoring toolsets or Python scripting.
2. Selected client/server targets explicitly exclude authoring plugins and the native MCP runtime/server modules using a mechanism qualified for the pinned engine. Merely leaving auto-start off is insufficient.
3. Cooked dependencies contain no unintended editor tool assets or references.
4. Launch each native client and the dedicated server; inspect process listeners and logs. The server exposes only the intended game-network endpoints, and no MCP/Remote Control authoring port is listening.
5. Repeat with a clean package, outside the editor and development MCP session. Record the artifact hash tested.

The fact that Epic names an editor integration does not imply all its modules are editor-only; verify actual build receipts and runtime behavior.

## 14. Acceptance evidence must prove the game

Automation acceptance should progress through increasingly realistic environments:

1. Text-source and manifest validation: schema errors, platform guards, missing paths and unresolved placeholders.
2. Native compile on both OSs: headers, reflection, module dependencies and plugin compatibility.
3. Asset validation after fresh editor reopen: references, class inheritance, collision, materials, input mapping and map configuration.
4. Engine-integrated tests: match rules, weapon state, seats, death/respawn and authority boundaries.
5. Multi-process local packaged test: Windows dedicated server and native clients; late join, disconnect and map/match reset.
6. Cross-platform session: Mac and Windows participants in the same match, including vehicle occupancy and replicated combat/objective transitions.
7. Impaired-network scenarios: delayed/lost packets and reconnection, with explicit tolerances defined by the game specification.
8. Rendered, audible playthrough on each client platform: input, readability, frame pacing, animation and feedback.

The initial 4v4 proof must exercise eight player slots using a defined mixture of real clients and validated test-controlled clients/bots; the report must distinguish them. A two-client replicated actor smoke test does not prove an eight-player game. Conversely, running eight rendered clients on a 24 GB Mac is not a prerequisite: distribute the scenario appropriately and measure the native Mac client itself.

Reports must record engine/build/tool versions, exact source revision, scenario revision, target, hardware, participant topology, random seed, timestamps, exit statuses and artifact paths. Store machine-readable assertions separately from logs and captures. The report verifier must fail when required results are missing, stale, skipped or generated by a different build.

Visual evidence must identify the actual client/world/camera; editor screenshots of a level do not prove first-person runtime visibility. Headless or `NullRHI` runs cannot establish visual correctness, audio perception, GPU cost or input feel. Keep performance thresholds in the main spec and measure on both native targets.

## 15. Selection gates and fallback order

1. **Epic native MCP works on both OSs and required operations pass:** use it with checked-in generators and native build/test wrappers.
2. **Native MCP has a missing operation, but Python/C++ can implement it:** add a small project authoring operation; retain the same transport and scripts.
3. **Native MCP is unreliable on one OS:** use native editor Python/commandlets there; keep both game platforms in acceptance. Document the missing convenience feature separately.
4. **A substantial editor capability remains blocked:** run a time-bounded `db-lyon/ue-mcp` qualification in an isolated project at one immutable release, with the same persistence, repeatability, packaging and security checks. Keep it only if it measurably resolves the gap.
5. **No automation path satisfies the operation:** use a short documented editor procedure and capture its outputs for reproducibility; do not let the agent invent an unsupported API or hide a manual dependency.

No tool choice earns an exception from native packaging, cross-platform multiplayer or content persistence evidence. The recommended architecture keeps the core game and its build recipe independent of whichever MCP implementation wins the qualification.

## 16. Open research-to-execution questions

- Exact available Windows build/test hardware and its supported compiler/SDK.
- Storage provision for the Mac engine, project, derived data and packaged outputs.
- Native UE 5.8.2 MCP descriptor/module availability on the actual Mac installation.
- Exact installed native tool catalog and which tools are needed after the game architecture is selected.
- Whether the selected agent hosts support local HTTP MCP directly; any necessary adapter must be pinned and local.
- Whether the chosen optional content/plugins supply compatible Mac binaries/source and suitable licenses.
- Exact supported engine-source/installed-build path for the Windows dedicated-server target.
- All local build, execution, latency, crash recovery and performance results: currently unverified.

These are qualification tasks with explicit outputs. They should be completed before promising an unattended game-implementation run; they are not grounds for leaving the requested detailed specification vague.
