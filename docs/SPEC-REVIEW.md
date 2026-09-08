# Specification review and evidence boundary

Updated 2026-09-08 for v0.2. This records independent research/specification review performed during preparation. It is **not a Fable review or gameplay validation**. The user plans to run Fable separately using [FABLE-REVIEW.md](FABLE-REVIEW.md).

## Accepted findings and corrections

| Finding | Correction in the current package | Future evidence affected |
|---|---|---|
| Old network/map/one-vehicle assumptions conflicted with subsequent user decisions | Replaced active scope with offline exact seven-bot 4v4, Fort Crossing/Holdout/CTF and all four vehicles; marked v0.1 archive superseded | Complete roster, map/modes and native package inventory |
| Grenade trajectory-search language survived the user's expert-thrower correction | Specified practiced motor execution through normal charge/aim/release/drop, fair target estimates and no corrected projectiles/perfect runtime futures | Throw accuracy, information access and legal-control tests |
| Driving discovery was deferred despite hotlap's required identity | Supported-family discovery/generalization on held-out geometry is mandatory; arbitrary-any-geometry mastery remains a research stretch | Held-out traversal and passenger/human reproduction |
| Optional learning could be mistaken for either a mandatory dependency or an excuse for weak fallback bots | Authored and learned controllers face the same specialty/identity/fairness gates; weak fallback only preserves operation | Native policy availability and actual skill qualification |
| Planning forecasts could accidentally clone hidden actors or future inputs | Limited forecasting inputs to known static terrain and observed/memory proxies; privileged evaluator truth stays separate | Same-run positive/negative observation counterfactuals |
| Generic four-wheel language conflicted with the truck | Six physical truck wheels, five seats, propulsion disabled at four failed tires; bike has two real wheels and one seat | Rig/contact/tire/cook cases for every family |
| Mac documentation and hotfix wording differed | Added the stronger UE5.8.2 UE-377426 warning for Xcode26.4 or newer, with compatible earlier-toolchain qualification | Exact native toolchain reports and builds |
| Phase order could assess lattice before laser mines existed | M3 creates sniper/mines before those characters are assessed; M4 integrates completed mechanics into both modes | Milestone prerequisite and case selection |
| Warmup reset could leave old grenades, mines, holds and bot records alive | Active transition creates a clean gameplay generation and resets transients, seats, spawns and observations | Boundary-time explosion and stale-job/reset cases |
| CTF lacked complete range/sight/capture interaction rules | Shared 1.5m/LOS/held-input contract; pickup0.3s, return1s, capture0.3s with own flag continuously Home | Exact interaction and cancellation cases |
| Equal-time expiry and action ordering was incomplete | Defined continuous Holdout scoring, regulation expiry, lifecycle/lease expiry and interaction priority | Before/equal/after deadline and oversize-frame cases |
| Empty vehicle affiliation, damage credit and replacement ownership were conflated | Separate original base slot, observable combat affiliation and last-driver credit, with fresh replacement clearing history | Bailout, empty beam crossings, ramming and stolen-vehicle replacement |
| Friendly-fire defaults could prevent the requested abandon-and-blow-up loop | Empty vehicles/tires can be damaged by either side; occupied friendly vehicles and players retain the selected protection | Destroy own abandoned vehicle and verify fresh base replacement |
| Bullet target conversion and post-falloff damage intervals were ambiguous | Explicit chassis/tire body-damage rule, no overlapping double hit, and minimum-damage plateaus to inclusive hard ranges | Hit classification, ammo/damage conservation and boundary ranges |
| “Disable driving” could imply freezing disabled vehicles | Consistently specify powered-propulsion disablement with physical coasting, slopes and remaining steering behavior | Disabled downhill and mid-turn physics |
| Generated content and final art were too easy to conflate | Added repeatable original rig/source/import/cook pipeline and explicit placeholder-to-final criteria | Semantic generation, rig inspection, native cook and presentation |

These are document-level corrections. Their implementation must still be exercised by the named acceptance cases. Review findings are not test receipts.

## Fable review of 2026-09-08

The independent Fable specification review is recorded in [FABLE-REVIEW-RESULT.md](FABLE-REVIEW-RESULT.md), with input hashes, a Codex (`gpt-6-astra`) cross-check, and the owner's dispositions. Verdict: viable after listed corrections. Three findings were accepted and applied on 2026-09-08 (see [DECISIONS.md](../DECISIONS.md)):

| Finding | Decision | Applied in |
|---|---|---|
| FR-01 CTF lease shorter than any on-foot carry | D-001: CTF is vehicle-required; lease stays 90 s | SPEC §11, §12; OBJ-07, UX-06 |
| FR-02 Drop release point and vehicle immunity unspecified | D-002: release point must clear thrower and vehicle hulls; exemptions end before flight | SPEC §7; GRE-02 |
| FR-03 Fairness counterfactual had no executable contract | D-003: decision-side queries obey the observation boundary; decision step repeatable with reset state | SPEC §12; BOT-03; ENG-011 |

FR-20 was withdrawn by the reviewer. On the owner's instruction the remaining findings (FR-04 to FR-19, FR-21 to FR-35) were all accepted and applied as D-004 to D-018, with Codex re-verification of each batch recorded in the result file. Every accepted change still needs its named fixture to run; none is verified.

## Rejected and unresolved findings

No reviewer finding was rejected merely to preserve scope or avoid work. Broader alternatives such as a runtime LLM controller, whole-Lyra adoption, a mandatory learned policy, or an additional editor bridge were not selected; their measured adoption triggers and tradeoffs remain in the research appendices. This is architecture selection, not evidence that every alternative was implemented and compared experimentally.

The following remain **unverified implementation obligations**, not unresolved wording that silently licenses an incomplete game: native UE/MCP/toolchain compatibility; four physical handling rigs including the bike; expert throw/stunt performance; full cast fairness/recognition; required voice/assets; both native packages; rendered performance; frozen held-out human studies; and actual Fable review. Every obligation must acquire its own implementation/evidence record during the runbook.

Numeric physics/combat/timing/performance seeds are deliberate design hypotheses. Human review may recommend changes; record accepted changes and rerun affected evidence. No claim is made that the selected numbers reproduce the original executable or already feel good.

## Documentation checks

The final preparation check validates local Markdown file/anchor references, fenced-block balance, exact cast spellings, active-scope consistency and the acceptance-ID inventory. It does not execute the future Unreal game or the proposed ENG harness. The acceptance document defines **90 product cases plus 16 engineering obligations**; their current gameplay execution status is unrun.

Executed documentation check on September 8: **159 local references and 10 anchors resolved; 90 unique product IDs and all 16 engineering IDs matched; no unbalanced fences, noncanonical cast spellings or stale active gate/map scope were found.** The package contains 16 active Markdown files and nine historical archived files, with 71 unique external source links in active documents. External-link counting is an inventory check, not a fresh HTTP availability test of every page. No Git repository or game build was created.

All substantive sources are linked near their associated claims in the research documents. Reference-source provenance is recorded in [DAY1-ENGINEERING.md](DAY1-ENGINEERING.md), including the inspected revision and read-only boundary. The adaptation is self-contained; another implementer does not need that checkout.
