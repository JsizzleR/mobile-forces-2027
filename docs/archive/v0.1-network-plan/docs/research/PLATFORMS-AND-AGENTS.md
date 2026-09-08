# Platforms, agent workflow, assets, and feasibility research

Research date: 2026-09-07, America/New_York. This is a design assessment, not an engine installation, benchmark, legal clearance, or successful build. The product requirements live in [SPEC.md](../SPEC.md); research alternatives do not override its selected defaults.

## 1. Mac and Windows recommendation

Support **native Apple Silicon macOS and Windows x64 clients from the first playable milestone**. Use the same gameplay implementation, map, networking protocol, content manifest, and acceptance scenarios. Allow platform-specific rendering quality and input glyphs. Defer Intel Mac, consoles, Linux desktop clients, and mobile devices. “Mobile” in the original title refers to the game, not a requirement to build for phones.

Mac is a manageable constraint for the proposed compact, stylized game. It becomes a much larger obstacle if the project depends on Windows-only binary plugins, hardware ray tracing, the highest-end rendering demonstrations, or proving Windows compatibility without a Windows machine. This is an engineering assessment; actual performance remains unmeasured.

Epic's freshly retrieved Mac platform page lists a UE 5.8 minimum of macOS 14.5 and Xcode 26.0, recommends Xcode 26.1.1, and explicitly warns against Xcode 26.4. Earlier search snippets returned an older Xcode minimum; the opened page takes precedence. It lists 16 GB minimum / 32 GB recommended memory. Hardware-ray-traced Lumen and MegaLights are unavailable on Mac; Nanite and Virtual Shadow Maps have beta support on M2 and later. Prefer the version table and the installed engine's SDK checks where introductory tables differ. [Epic Mac development requirements](https://dev.epicgames.com/documentation/unreal-engine/macos-development-requirements-for-unreal-engine?lang=en-US)

The selected rendering baseline is an original project decision: conventional meshes with LODs, modest materials, conventional shadowing, fixed daylight, no required Nanite, no required hardware ray tracing, no required Lumen, no MegaLights. Establish a working medium preset first. Add an optional higher-quality preset only after both platforms pass. Software Lumen is an experiment, not a prerequisite. Windows should run this same baseline before receiving optional visual upgrades.

### Observed local environment

Read-only shell inspection found:

| Observation | Evidence / limit | Consequence |
|---|---|---|
| Apple Silicon M-series laptop, 12 CPU cores, 16 GPU cores, 24 GB RAM | `system_profiler SPHardwareDataType SPDisplaysDataType -detailLevel mini` | Plausible editor/playtest host; compile and multi-client concurrency must be constrained |
| macOS 26.6.2, build 25G83 | `sw_vers` | Newer than listed minimum does not establish compatibility with a particular Xcode / engine combination |
| Limited space available on the workspace volume | `df -h`; point-in-time, APFS availability may change | Insufficient planning headroom for engine, caches, native project builds, and package archives |
| `xcodebuild -version` failed because active developer directory is Command Line Tools | Tool output, not an Unreal compile attempt | Full compatible Xcode and its license/SDK initialization are prerequisites |
| No `/Applications/Xcode.app`, `/Applications/Epic Games Launcher.app`, or `~/Epic Games` | Exact-path inspection only | Standard-location installation absent; no claim that all volumes/custom paths were searched |
| Project directory was empty; no `.git` or `.uproject` | Workspace directory listing | Deliverable starts as documentation; there is no game implementation to preserve or validate yet |
| No callable Unreal MCP or Fable tool in this session | Available-tool metadata inspection | Design the integrations; do not report live tool or Fable validation |
| Separate Windows hardware unknown | User selected Mac + Windows, without identifying another host | Provision and validate an actual Windows native build/playtest runner before declaring dual-platform acceptance |

Reserve **at least 150 GiB free for a Launcher-based editor/project work area**, and plan **300–500 GiB per source-engine build work area**. These are conservative project budgeting allowances, not Epic-published installation sizes or measured requirements; component selection and caches change actual use. An external fast SSD is a reasonable capacity option, subject to a small compile/cook/read-write performance test. Do not delete existing user files to create space. Start editor/compiler work with bounded parallelism and record swap/memory pressure.

### Native build matrix

| Runner | Required first slice | Deferred |
|---|---|---|
| Apple Silicon Mac | Native editor build, content generation smoke, Mac Development package, Mac Shipping smoke, cross-OS client tests, local host/bot match | Intel/universal compatibility promise, App Store release |
| Windows x64 | Native editor build, Windows Development package, Windows Shipping smoke, dedicated Development server from matching source engine, cross-OS tests | Console/GDK targets |
| Linux x64 | No first-slice dependency | Dedicated server port after Windows server acceptance; same authoritative game code |

Use Epic's supported Visual Studio/toolchain matrix at setup time and record the actual compiler/SDK in the lock. A generic “latest Visual Studio” instruction is insufficient. [Epic Visual Studio setup](https://dev.epicgames.com/documentation/en-us/unreal-engine/setting-up-visual-studio-development-environment-for-cplusplus-projects-in-unreal-engine)

A Mac editor session is not evidence of a Windows executable, and a Windows compile is not evidence that a Mac package launches. Development package tests also do not establish public distribution readiness. Public Mac downloads require a separate signing/notarization and clean-machine Gatekeeper test; never include signing keys in the project. [Apple notarization documentation](https://developer.apple.com/documentation/security/notarizing-macos-software-before-distribution)

## 2. Astra and Fable roles

Use the user's available **Astra** as the implementation/integration lead. Official OpenAI guidance identifies the API model as `gpt-6-astra`; tool-calling API integrations use Responses. There is no need to build an API orchestration service just to create this game: use the existing coding-agent harness, local files, shell, editor tools, and its supported subagents. [OpenAI Astra guidance](https://developers.openai.com/api/docs/guides/latest-model)

“Fable” means the user's chosen Fable reviewer. This session did not resolve a public model ID, vendor, CLI executable, authentication scheme, or callable Fable integration. The specification deliberately supplies a portable review prompt rather than inventing `fable` commands or assuming Fable is another product with the same name. The user intends to request that review after this specification is complete.

Proposed operating roles:

| Role | Work | Write ownership | Evidence returned |
|---|---|---|---|
| Astra integrator | Resolve spec decisions, manage dependencies, integrate, run gates, maintain progress | Main project/config/module boundaries; final integration | Build manifest, gate reports, artifact paths, unresolved defects |
| Astra gameplay worker | Infantry, weapons, objective pure rules | Assigned C++ files only | Changed files, assumptions, targeted test output |
| Astra vehicle worker | Transport, seats, ownership, network probe | Assigned vehicle files only | Two-client seat and physics traces, failure cases |
| Astra test/content worker | Scenario harness or content recipes, depending on current gate | Assigned test/scripts files; binary assets only with editor lease | Reproducibility diff, test reports, screenshots |
| Fable reviewer | Adversarial architecture/spec review, later independent implementation audit | Review document only unless implementation explicitly assigned | Severity, requirement, reproduction, concrete change, residual uncertainty |
| User | Product taste, access/setup requiring human credentials, actual playtest experience | Product decisions | Feel/readability assessment, machine-specific launch confirmation |

The ownership rules are proposed for this project. Official Codex documentation supports parallel subagents and advises caution with concurrent writes; it does not make Unreal binary asset edits mergeable. [OpenAI subagent documentation](https://learn.chatgpt.com/docs/agent-configuration/subagents)

Use small, bounded work packages and a single editor operator. A compile and a file-edit agent may overlap only when the compile reads an immutable snapshot. No worker gets to rename shared reflected types independently. Do not let two agents save the same map, asset, config, or editor session concurrently. Parallelism helps research, isolated C++ work, and log review; it cannot bypass sequential engine compilation, content loading, or acceptance dependencies.

## 3. Agent transport and local execution

The preferred route is an agent running on the same machine as Unreal, connected to Epic's native experimental MCP. Use local filesystem and command-line tools for source and builds; use MCP for inspecting and changing editor state. The detailed comparison is in [UNREAL-AUTOMATION.md](UNREAL-AUTOMATION.md).

Codex supports HTTP MCP configuration and tool filtering. Discovery must precede configuration of individual tool names. A generic dispatch tool may expose many operations behind one name; an allowlist of the outer dispatcher does not constrain those operations. The project should scope actual loaded toolsets and enforce mutation policy inside custom tools. [OpenAI MCP documentation](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)

Do not assume a hosted chat agent can connect to a laptop's `127.0.0.1`. A remote workflow should run a local agent on the Unreal host, or use a reviewed authenticated access mechanism. The initial specification needs neither a public MCP listener nor a new remote-execution service. Keep the editor bridge out of runtime gameplay dependencies.

## 4. Asset acquisition and reproducibility

The first implementation must build without paid assets, private model APIs, or an artist finishing a vehicle rig on demand. Use simple project-authored graybox meshes, materials, UI, and sound placeholders. Epic templates/samples may be used after confirming their availability and applicable terms in the selected engine. Content recipes must report any dependency that was not acquired.

After the graybox passes, curate a **single visual family** of licensed modular industrial environment assets, one compatible character/animation set, one weapon family, one transport asset, and a small audio set. Do not compose a random marketplace collage. Each candidate must be evaluated for source access, Mac support, skeletal rig, LODs, collision, materials, texture memory, and packaged references before purchase. No live prices are assumed; the acquisition budget starts at zero and purchases are a later user decision.

Fab's Standard License summary allows incorporation in projects and collaboration sharing, but not standalone asset redistribution; other listings may use different licenses. Record the exact license and acquisition evidence for each asset rather than assuming that free assets are unrestricted. [Fab EULA](https://www.fab.com/eula)

Recommended future manifest fields: stable asset ID; publisher; source URL; exact license/version; acquisition date; receipt/evidence location outside public source; permitted distribution; attribution; installed package version; file hash; Unreal path; skeleton/socket contract; platform restrictions; replacement placeholder; review status. Store proprietary assets privately when required. Do not include proprietary engine source or full original-game manuals/assets in this documentation repository.

Use Git for text and Git LFS for binary Unreal assets and authored source media if Git is adopted. LFS stores pointers in Git and actual objects separately; a fresh checkout must fetch those objects before the editor opens. [Git LFS](https://git-lfs.com/) Pin asset versions, verify missing LFS objects, and preserve stable paths. Introduce Perforce only if multi-person binary asset work actually demands it; adopting a new source-control platform is not a first-slice requirement.

## 5. Rights and AI data boundaries

The project is an original spiritual successor. The working directory name is not a cleared product title. Use new branding, setting, characters, map layouts, UI, sounds, weapon/vehicle art, and code; do not rip or redistribute Mobile Forces content. A close mechanical comparison is a design reference, not evidence of permission to use protected content or branding.

Epic currently describes the standard game license as royalty-based after the applicable threshold, with a standard 5% rate on qualifying lifetime revenue above $1 million and exceptions. Recheck the actual agreement and release notification obligations before commercialization. This spec is not a calculation of royalties owed. [Unreal licensing overview](https://www.unrealengine.com/license)

The current Unreal EULA restricts using Licensed Technology as AI training input or prompt input where the program trains on that data. Before providing licensed engine source/assets to either agent service, confirm the applicable service's data treatment and rights; ordinary agent use is not automatically prohibited by this clause. Do not train models on engine content. Keep public reports focused on project code and minimal diagnostic excerpts. [Unreal EULA, section 6(e)](https://www.unrealengine.com/eula/unreal)

## 6. What “one shot” can mean here

The realistic objective is **one sufficiently explicit kickoff followed by an autonomous sequence of code, compile, content generation, testing, and corrections**, with durable checkpoints. It is not one untested batch of generated C++/Blueprints or a promise of a commercially polished game in one response.

The first outcome is a complete, inspectable, cross-platform 4v4 vertical slice. Public internet service, anti-cheat operations, final art, full controller support, platform storefront work, and content scale are separate milestones. Preserve the vision in the roadmap without making those dependencies for the initial closed playtest.

Planning estimates, not guarantees or vendor benchmarks:

| Work | Rough allowance | Main uncertainty |
|---|---|---|
| Machine preparation and matching engine builds | 1–3 working days plus downloads/compilation | Storage, toolchain compatibility, Windows access, source-engine build |
| Connectivity and vehicle/seat feasibility spikes | 2–5 working days | Chaos prediction and driver possession across native clients |
| Gameplay-complete graybox slice | 1–3 weeks of supervised iteration | Objective/bot correctness and packaging integration |
| Presentable vertical slice and repeatable cross-OS tests | Additional 2–6 weeks | Art/animation quality, gameplay feel, driver latency, performance |
| Commercial release | Re-estimate after playtests | Audience, content volume, online operations, assets, QA and support |

Agents may shorten source-writing time substantially; no evidence from this session supports a particular acceleration factor. Track actual wall time, machine cost, agent cost, human intervention count, and failed gate count. Use measured cost per accepted gate to estimate subsequent work. If no spending ceiling was supplied, do not invent permission for paid APIs, assets, or cloud runners.

## 7. Evidence classification

- **Verified here:** the cited pages were retrieved; limited local machine facts were inspected; this specification package was authored.
- **Publisher-documented:** engine/model/tool feature descriptions and licenses; these are not independent runtime certification.
- **Design proposal:** architecture choices, performance targets, time/storage allowances, gameplay rules, budgets, process, file layout, custom tools and tests.
- **Unverified:** UE 5.8.2 compile on either user machine, exact Mac MCP operation, vehicle net quality, native crossplay, any build/performance number, Fable review, final rights clearance, shipping/notarization.

The implementation run must replace unknowns with artifacts or report them as blockers. It must never convert a source citation or generated screenshot into proof of a packaged multiplayer game.
