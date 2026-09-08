# Platforms and agent workflow: offline Mac and Windows

Research revision v0.2, 2026-09-07. [SPEC.md](../SPEC.md), [BOT-CAST.md](../BOT-CAST.md), [BUILD-RUNBOOK.md](../BUILD-RUNBOOK.md) and [DAY1-ENGINEERING.md](../DAY1-ENGINEERING.md) define the implementation contract. This revision updates documentary research; native builds, performance, local prerequisites and Fable integration remain unverified.

## 1. Keep Mac and Windows from the start

Target native **Apple Silicon macOS and Windows x64** using the same offline simulation, seven named profiles, four vehicles, Fort Crossing, Holdout and CTF. Both platforms need their own compiled, cooked and rendered evidence. Intel Mac, Windows ARM64, Linux desktop, consoles and mobile devices are separate future decisions.

Mac is a manageable engineering constraint for a compact game with modest rendering and local bot controllers. The serious hurdles would be a required Windows-only plugin/inference runtime, unavailable Mac-compatible vehicle tooling, or a visual target depending on unavailable rendering features. The proposed baseline avoids those dependencies; actual performance must still be measured.

Epic's current UE 5.8 Mac table specifies macOS 14.5 minimum, Xcode 26.0 minimum and Xcode 26.1.1 recommended. It explicitly marks **Xcode 26.4 incompatible**. The 5.8.2 hotfix's `UE-377426` known-issue reply gives the broader warning: compilation can fail with **Xcode 26.4 or newer**, and recommends the 26.1 line. Start with the recommended compatible version; a newer toolchain requires evidence that this issue is resolved for the selected engine. The platform page lists 16 GB minimum and 32 GB recommended RAM. Hardware-ray-traced Lumen and MegaLights are unavailable on Mac; Nanite/Virtual Shadow Maps have beta support on M2+. Use the version-specific table plus installed-engine SDK validation where introductory wording conflicts. [Epic Mac requirements](https://dev.epicgames.com/documentation/unreal-engine/macos-development-requirements-for-unreal-engine?lang=en-US), [5.8.2 hotfix and known issue](https://forums.unrealengine.com/t/5-8-2-hotfix-released/2746335)

Use conventional LODs, restrained materials/lighting/shadows and a shared medium preset first. Optional quality improvements must retain that supported baseline. Compare recorded output resolution and internal rendering scale, not just the preset name. Profile a rendered eight-combatant match with vehicles and effects; headless bot throughput does not predict gameplay frame time.

On Windows, Epic's UE 5.8 table lists VS 2022 17.14+ and VS 2026 18.0+, recommending VS 2026 for general development. The current recommended MSVC is 14.50 and Windows SDK 10.0.26100 or newer. Record the exact accepted combination rather than installing an unspecified latest toolchain. [Epic Visual Studio setup](https://dev.epicgames.com/documentation/en-us/unreal-engine/setting-up-visual-studio-development-environment-for-cplusplus-projects-in-unreal-engine)

## 2. Earlier local observations and resource planning

The earlier research pass recorded macOS 26.x, Apple Silicon M-series, 24 GB RAM and limited free workspace storage. It found Command Line Tools selected instead of a working full Xcode installation and did not find Unreal/Xcode at the standard paths it inspected. These are **earlier observations, not refreshed facts**; custom installations were not comprehensively excluded. Run the proposed doctor before implementation. The project now contains this documentation package, so the earlier empty-directory observation is no longer current.

24 GB is below Epic's recommended memory but above its listed minimum. Limit concurrent compilation/editor/cooking jobs, record memory pressure, and keep performance runs isolated from heavy build work. The observed free space would provide poor headroom for an engine/project/caches/packages workspace.

Use **150 GiB free as an initial planning allowance**, then replace it with observed installation and growth figures. This is a conservative project allowance, not an Epic installation requirement. The offline scope does not require a source-engine build; remove the former mandatory source-build storage allocation. A fast external SSD is a possible workspace location after a small read/write, compile and cook qualification; do not delete user files automatically.

| Native host | Required evidence |
|---|---|
| Apple Silicon Mac | Editor C++ build, automation/content round trip, Development package, Shipping smoke, rendered match/input/audio/performance |
| Windows x64 | The corresponding Windows editor, package, rendered and performance checks |
| Each target using a learned policy | Actual packaged model/runtime availability, output schema and measured execution budget |

A Windows machine has not been identified or qualified in this package. Source work can continue while that prerequisite is open, but dual-platform completion cannot. Training infrastructure, if eventually needed, is a separately justified experiment; it is not a third shipping target.

## 3. Astra, Fable and Unreal have different jobs

Use the user's selected Astra for implementation/integration and Fable for independent design and implementation review. Official OpenAI documentation identifies Astra as `gpt-6-astra`; an API implementation using tools would use Responses. This project can use the existing coding-agent harness instead of building a new API orchestration service. [Official OpenAI Astra guidance](https://developers.openai.com/api/docs/guides/latest-model)

Fable means the user's chosen model/reviewer. Its exact executable, model ID, account and available integration have not been resolved here; the review packet must remain portable. Do not invent a Fable command or claim a review that did not occur.

| Role | Bounded responsibility |
|---|---|
| Astra integrator | Maintain canonical contracts, integrate changes, run native gates and record evidence/residuals |
| Gameplay worker | Shared combat/action/mode rules in assigned source files |
| Vehicle worker | Human handling, rigs, tire damage, seats and lifecycle; then bot driving adapter |
| Bot/evaluation worker | Profile behavior, observation boundary, scenario traces and held-out evaluation |
| Editor operator | Sole mutating owner of the current editor/content operation |
| Fable reviewer | Challenge assumptions, bypasses, identity and acceptance claims; return dispositions and reproductions |
| User | Judge vehicle feel, the remembered players' identities and the complete experience |

Parallel workers need disjoint ownership or immutable worktree snapshots. Reflected type renames, map saves, binary assets, generation and cooking require coordination. DAY1 specifies leases, interruption recovery and final-artifact revalidation. Neither model has special authority to weaken a gameplay requirement because its first implementation was difficult.

Epic's native experimental MCP is the preferred editor integration, subject to its real Mac/Windows spike and qualified fallback. Epic documents local editor execution and serial game-thread tool invocation. Keep source/build operations in ordinary tools and editor operations in MCP/Python; ship neither agent connection nor editor bridge as a gameplay dependency. [Epic Unreal MCP](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor), [automation comparison](UNREAL-AUTOMATION.md)

## 4. Assets, reproducibility and delivery

Start with project-authored graybox content and available licensed sample material. The user has required four vehicle types, so evaluate all four rigs, seat layouts and damaged-tire behavior before investing in final art. Curate a coherent environment/character/weapon/audio family after physical qualification. Cosmetic placeholders are acceptable at the specified graybox milestone; absent behavior is not.

Record each asset's stable ID, source, exact license, acquisition evidence, version/hash, Unreal path, platform/rig restrictions and replacement placeholder. Free availability does not establish unrestricted redistribution. Original branding, map geometry and game assets remain project requirements; reference mechanics and player handles do not authorize copying the original game's media.

Choose a binary-source-control workflow and prove a fresh checkout obtains actual assets, then regenerate/validate content from the recorded recipes. Preserve source media and license evidence; exclude caches and raw bulk verification output from ordinary commits. Native Development testing and Shipping smoke are required; public signing, notarization and storefront delivery are distinct later qualifications.

“One shot” means one clear implementation kickoff followed by autonomous building, measurement, correction and resumable checkpoints. The pivot removes online infrastructure work and adds substantial vehicle/character evaluation. It does not establish a calendar estimate or an acceleration factor. Measure time and cost per accepted milestone; preserve every unavailable platform or subjective evaluation as an explicit obligation rather than hiding it behind a green build.
