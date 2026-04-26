# Phase 1 Completion Report
## Experimental Design and Comparison Frame for the Joe's Crab Shack No-Tipping Pilot Study

**Course:** DSA 5900 — Professional Practice (Spring 2026)
**Project:** Quantitative Evaluation of the Joe's Crab Shack No-Tipping Policy Intervention
**Phase:** 1 of 2 — Experimental Design and Comparison Frame
**Status:** Complete and locked
**Primary deliverable:** `location_manifest_final.csv` (61 active rows)
**Companion document:** *Phase 2 Completion Report — Extraction Pipeline and Master Dataset Construction* (forthcoming)

---

## 1. Executive Summary

Phase 1 of the practicum has concluded successfully. Its purpose was to translate a broad sponsor-level research interest — measuring whether the 2015–2016 Joe's Crab Shack (JCS) no-tipping pilot moved consumer-perceived service quality — into a fully operational quasi-experimental comparison frame that downstream extraction tooling can consume without further design work.

That objective has been met. Phase 1 is no longer a planning phase, a methodology sketch, or a candidate notebook. It is a closed and reconciled stage whose finished product is a single execution-ready file: `location_manifest_final.csv`. That file holds **61 active locations**, distributed as:

| Role | Count | Manifest label |
|---|---|---|
| Treatment (active primary subset of historical pilots) | 13 | `treatment` / `JCS` / `primary_internal` |
| Tier 1 strict internal same-chain controls (non-pilot JCS) | 26 | `internal_control` / `JCS` / `primary_internal` |
| Tier 2 locked external competitor cohort | 22 | `external_competitor` / `non_JCS` / `tier2_official` |
| **Total active rows** | **61** | — |

These counts are now fixed. They are the official Phase 1 outputs and the official inputs to Phase 2.

The strict primary same-chain analytical frame — the comparison architecture that anchors the study's main causal contrast — consists of the **39 unique JCS locations** formed by the 13 treatment stores plus the 26 Tier 1 controls. The 22 Tier 2 competitors form a parallel external benchmark that supports robustness and triangulation rather than the primary contrast.

Phase 1 is therefore the right place in the project lifecycle to draw a hard line. The question "who is in the study?" is closed. The question "how do we extract and assemble the data for the locked study set?" is the next question, and it belongs to Phase 2.

---

## 2. Phase 1 Mission and Scope

### 2.1 Mission statement

> Convert the sponsor's research interest in the JCS no-tipping pilot into a defensible, reproducible, row-level comparison frame that (a) preserves the historically verified treatment footprint, (b) supplies a strict same-chain internal control group derived under one disciplined logic, (c) supplies a locked external competitor cohort selected under hard-gate eligibility rules, and (d) is operationally ready to drive review extraction without further cohort-definition work.

### 2.2 In-scope work for Phase 1

Phase 1 was responsible for, and only for, the following classes of work:

1. **Treatment verification.** Establishing which JCS locations actually participated in the no-tipping pilot, separating the historical treatment universe from the active analytical subset.
2. **Internal same-chain control derivation.** Identifying the universe of non-pilot JCS locations and reducing it to a disciplined Tier 1 control set under a structured matching context.
3. **External competitor selection.** Building, normalizing, and screening a candidate competitor universe down to a Tier 2 cohort under hard-gate eligibility rules.
4. **Manifest reconciliation.** Producing a single row-level operational artifact that consolidates all selected locations with the identity, endpoint, role, and time-window metadata required for downstream execution.
5. **Operational-readiness verification.** Demonstrating that the manifest satisfies identity certainty, endpoint certainty, analytical certainty, and time certainty.

### 2.3 Out-of-scope for Phase 1

Phase 1 explicitly did **not** perform any of the following — these belong to Phase 2:

- Yelp review extraction or scraping execution
- Review-level cleaning, deduplication, or canonicalization
- Attribute lexicon application or NLP scoring
- Pre/post analytical contrasts, modeling, or inference
- Visualization, dashboarding, or executive reporting

This boundary matters. It is what allows Phase 1 to be declared closed and what protects Phase 2 from inheriting unresolved cohort questions.

---

## 3. Design Principles

Five design principles governed every selection decision in Phase 1. They are documented here because Phase 2 will need to honor the same principles when it consumes the manifest.

### 3.1 Disciplined derivation over opportunistic inclusion

Every location in the final manifest is present because it survived a structured derivation process from a defined candidate universe. No location was added because it was conveniently nearby, intuitively similar, or qualitatively interesting. This rules-based posture is what makes the comparison frame defensible.

### 3.2 Same-chain primacy, external triangulation

The primary causal contrast is internal: treatment JCS stores against non-pilot JCS controls. This holds brand, format, menu, and corporate operating context constant by construction. The external competitor cohort exists to support robustness and triangulation — not to dilute the primary contrast.

### 3.3 Operational executability as a success criterion

Phase 1 was not considered finished when the design was conceptually elegant. It was considered finished when the design could be handed to an extraction pipeline and executed deterministically. That is why operational-readiness verification (Section 8) is a first-class deliverable.

### 3.4 Documented exclusions over hidden ones

When a row was removed from the cohort, the exclusion is preserved in the structure rather than smoothed away. The most visible example is the intentional `T2_021` gap in the Tier 2 numbering (Section 6.4). The manifest shows the real outcome of the selection process; it does not pretend the process was uncontested.

### 3.5 One source of truth

At the end of Phase 1 there is exactly one operational artifact: `location_manifest_final.csv`. There is no parallel "working" manifest, no unresolved alternate cohort, and no shadow Tier 2 set. Phase 2 reads from one file.

---

## 4. The Treatment Side of the Design

### 4.1 Historical treatment universe — 18 verified pilot locations

The study's historical treatment universe consists of **18 verified JCS pilot locations** that participated in the 2015–2016 no-tipping intervention. This universe is preserved in project documentation because it represents the actual historical footprint of the rollout and the complete set of pilot locations recognized by the project.

This 18-store figure is the historically correct count of the pilot. It is referenced when the study needs to characterize the pilot's real-world scope.

### 4.2 Active primary analytical treatment subset — 13 locations

Within the strict primary same-chain analytical specification, the active treatment set is a **subset of 13 locations** drawn from the 18-store pilot universe. The reduction from 18 to 13 is methodological rather than cosmetic and reflects the disciplined exclusion of pilot stores that could not be retained in the strict same-chain comparison architecture under the project's matching context (Section 5.2).

The two counts therefore coexist with intentionally different roles:

| Construct | Count | Role |
|---|---|---|
| Historical treatment universe | 18 | Documents the real pilot footprint |
| Active primary analytical treatment subset | 13 | Participates in the strict primary contrast |

This distinction is preserved everywhere in the project's written record so that the two figures cannot be confused.

### 4.3 Treatment representation in the manifest

The manifest contains **13 treatment rows**, each carrying:

- a unique `location_id` of the form `PILOT_##`
- `group_type = treatment`
- `chain_type = JCS`
- `analysis_role = primary_internal`
- `include_in_primary_analysis = True`
- a direct `/biz/` Yelp endpoint
- the populated pilot window (`pilot_window_start`, `pilot_window_end`) and primary pre/post window fields

These rows are extraction-ready and self-labeling: any review pulled against them inherits its analytical identity from the manifest row that produced it.

---

## 5. The Internal Same-Chain Comparison Design

### 5.1 Non-pilot JCS candidate universe — 112 locations

The internal comparison design began from the full universe of non-pilot JCS locations operating during the analytical window. That candidate pool consisted of **112 non-pilot JCS locations**.

This is the same-chain control universe from which the strict Tier 1 internal comparison group was derived. Importantly, the final control group was not selected informally or opportunistically; it was reduced from this full non-pilot universe under one structured design.

### 5.2 Matching context

The Tier 1 design was built around a location-level matching context that included the structural and contextual features required to judge cross-store comparability. In final operational terms, the matching context used:

- **Storefront topology** — store form and trade-area setting (e.g., free-standing pad, mall-attached, lifestyle-center, urban storefront), used to prevent treating physically and contextually different store formats as interchangeable.
- **Historical review volume** — the baseline engagement signal a store carried prior to any treatment window, used to align stores with comparable Yelp review ecosystems and prevent matching very-high-visibility stores to very-low-visibility ones.
- **Socioeconomic context** — location-level socioeconomic descriptors of the surrounding consumer environment, used to preserve some alignment in trade-area composition rather than relying solely on brand identity and geography.
- **Region and market-type context** — geographic and market-class alignment, used to keep regional consumer expectations and competitive landscapes broadly comparable.
- **Row-level confidence and operational identity** — `location_confidence` and identity fields that ensured each candidate had a verifiable storefront and an unambiguous Yelp endpoint before it could be considered for inclusion.

These covariates defined the feature space within which non-pilot JCS locations were assessed as strict internal controls. They are documented here because Phase 2's analytical inheritance — that is, what each Tier 1 row "is" when its reviews are interpreted — derives from this matching context.

### 5.3 Final Tier 1 control set — 26 locations

After the matching context was applied to the 112-location non-pilot JCS universe, the strict internal same-chain design resolved into **26 unique non-pilot JCS controls**. These 26 locations are the finalized Tier 1 control group and appear in the manifest as the internal same-chain comparison rows with:

- `location_id` values in the Tier 1 internal-control series
- `group_type = internal_control`
- `chain_type = JCS`
- `analysis_role = primary_internal`
- `include_in_primary_analysis = True`
- a populated `matched_pilot_id` that ties each control to its analytically paired pilot store
- a direct `/biz/` Yelp endpoint
- populated pilot and primary pre/post window fields aligned to the matched pilot's timeline

These rows are not generic same-chain stores. They are the locations that survived the strict internal comparison logic and were retained as the official internal controls of the primary study design.

### 5.4 Strict primary same-chain analytical frame — 39 locations

The strict primary same-chain analytical structure is therefore:

> **13 treatment locations + 26 Tier 1 internal controls = 39-location strict same-chain analytical frame.**

This 39-location structure is the main primary analytical comparison set for the study and the principal carrier of the project's causal contrast.

---

## 6. The External Competitor Design

### 6.1 Competitor candidate universe

The external competitor side of Phase 1 was built from a merged research pool of candidate restaurants tied to the pilot markets. That pool was collected, normalized, deduplicated, and audited so the competitor side of the study would be assembled as rule-based as the internal side.

The result was an audited external candidate universe from which the final competitor cohort was drawn under hard-gate screening logic (Section 6.2).

### 6.2 Tier 2 hard-gate screening logic

The external competitor design used a hard-gate logic to determine which candidate restaurants qualified as valid Tier 2 controls. Each candidate had to satisfy all of the following gate criteria:

| Gate | Requirement |
|---|---|
| **Concept suitability** | Casual-dining, sit-down restaurant concept comparable in customer experience to the JCS format. |
| **Service model** | Full-service, server-mediated dining model in which tipping is a meaningful customer behavior. |
| **Historical operational relevance** | Operating presence during the analytical window so that pre/post review series are populated on both sides of the timeline. |
| **Review footprint sufficiency** | Yelp review volume sufficient to support meaningful pre/post comparisons rather than zero-review or near-zero-review noise. |
| **Geographic proximity to the relevant pilot market** | Located in or adjacent to the trade area of a pilot or paired control, so that competitor reviews share macro-environmental signal with the JCS rows. |

These were applied as hard gates: a candidate that failed any one gate was excluded. The cohort was not assembled as "restaurants nearby" or "restaurants that seemed similar." It was assembled by applying a consistent screening structure across the audited candidate universe.

### 6.3 Final Tier 2 competitor cohort — 22 locations

The final official Tier 2 external competitor set consists of **22 locked competitor rows** in the active manifest, carrying:

- `location_id` values in the `T2_###` series
- `group_type = external_competitor`
- `chain_type = non_JCS`
- `analysis_role = tier2_official`
- a direct `/biz/` Yelp endpoint
- populated pilot and primary pre/post window fields aligned to the appropriate pilot timeline so the external cohort's review series are anchored to the same analytical clock as the internal frame

These 22 locations are the competitor rows that survived the hard-gate logic and are the **single official external competitor cohort going forward**. There is no second active Tier 2 set in the final Phase 1 product. The competitor side is locked as one official set.

### 6.4 The intentional `T2_021` gap

The Tier 2 numbering in the manifest runs `T2_001` through `T2_020`, then jumps to `T2_022` and `T2_023`. The `T2_021` slot is intentionally empty.

That gap is **not an error**. It records a row that was eliminated during final reconciliation and would have been retained had it survived the hard-gate screen. Preserving the gap (rather than renumbering downstream rows or fabricating a replacement) is a deliberate choice consistent with Design Principle 3.4: documented exclusions over hidden ones. The manifest documents the real outcome of the selection process and does not smooth away real distinctions.

Any downstream tooling, analyst, or reviewer who notices the gap should treat it as expected behavior, not as data loss.

---

## 7. The Final Active Groups in Phase 1

In final implemented form, the active Phase 1 groups are fixed at the following counts. These should now be treated as locked.

| Construct | Count | Manifest representation |
|---|---|---|
| Active extraction-ready manifest | **61 rows** | `location_manifest_final.csv` |
| Active treatment group | **13 locations** | `group_type = treatment` |
| Active Tier 1 internal same-chain controls | **26 locations** | `group_type = internal_control`, `chain_type = JCS` |
| Active Tier 2 external competitor cohort | **22 locations** | `group_type = external_competitor`, `chain_type = non_JCS` |
| Strict primary same-chain analytical frame | **39 unique locations** | `analysis_role = primary_internal` (13 treatment + 26 Tier 1) |
| Historical treatment universe | **18 verified pilot locations** | Documented; superset of the active 13 |
| Non-pilot JCS candidate universe used for internal-control derivation | **112 locations** | Documented; superset of the 26 |

---

## 8. Operational Readiness of the Manifest

The finalized manifest is operationally ready in the strict execution sense — meaning Phase 2 can read it deterministically and start work without further cohort-definition repair. This readiness rests on four certainties.

### 8.1 Identity certainty

Every row has a unique identity. The manifest contains **zero duplicate `location_id` values** and exactly one operational row per included location. Each row's identity is stable and self-describing through the combination of `location_id`, `restaurant_name`, `address`, `city`, `state`, and `zip`.

### 8.2 Endpoint certainty

Every row has a direct Yelp business-page URL of the form `https://www.yelp.com/biz/<slug>`. There are **zero missing `base_url` values** and **zero Yelp search-result URLs** in the manifest. This makes browser navigation deterministic and prevents misidentification during extraction; the extractor never has to disambiguate between candidate businesses returned by a search query.

### 8.3 Analytical certainty

Every row carries explicit analytical role information through `group_type`, `chain_type`, `analysis_role`, `include_in_primary_analysis`, `include_in_secondary_analysis`, and (for controls) `matched_pilot_id`. This means extracted review rows in Phase 2 can inherit their analytical identity directly from their source manifest row without an additional join, lookup, or rule-application step.

### 8.4 Time certainty

The required analytical window fields are populated on every row: `pilot_window_start`, `pilot_window_end`, `primary_pre_start`, `primary_pre_end`, `primary_post_start`, and `primary_post_end`. This means the output of Phase 2 extraction can be anchored immediately to the study's time structure and used to build the master analytical dataset, rather than being left as an unlabeled review dump that needs a second pass to attach time labels.

### 8.5 Operational-readiness summary

| Certainty | Verification | Status |
|---|---|---|
| Identity | 0 duplicate `location_id` values; one row per location | ✓ |
| Endpoint | 0 missing URLs; 0 search URLs; all `/biz/` direct endpoints | ✓ |
| Analytical | All role and inclusion flags populated; controls carry `matched_pilot_id` | ✓ |
| Time | All pilot and primary pre/post window fields populated | ✓ |

The manifest has crossed the threshold from planning artifact to execution artifact. The Phase 2 extraction pipeline can consume it directly without resolving ambiguity, choosing among alternatives, or repairing missing identity information at runtime.

---

## 9. Why the 61-Row Manifest Is the Correct Phase 1 Endpoint

Phase 1's success criterion was never symmetry, theoretical fullness, or idealized counts. The success criterion was a final design that could be translated into extraction without ambiguity.

The 61-row manifest is the correct endpoint because it contains only those rows that survived final reconciliation and are authorized to enter execution. Phase 1 concluded not by preserving every originally imagined row, but by preserving every valid active row needed for the next stage. The 18-store historical pilot footprint and the 112-store non-pilot universe are not lost — they remain documented as the upstream universes from which the active 13 and 26, respectively, were derived. They simply do not enter the operational artifact, because they are not what Phase 2 is asked to extract from.

This is why Phase 1 is closed: the artifact does what Phase 1 was scoped to produce.

---

## 10. Methodological Position at the End of Phase 1

At this point, Phase 1 has resolved the study's comparison architecture in a sufficiently disciplined way that the project is no longer engaged in cohort-definition work. The phase has completed:

- Treatment verification
- Treatment-subset operationalization
- Same-chain internal control derivation
- External competitor selection
- Row-level manifest reconciliation
- Execution-readiness preparation

The project has therefore moved past the question *"who is in the study?"* and into the question *"how do we extract and assemble the data for the locked study set?"* That second question is the entire subject of Phase 2.

---

## 11. Final Phase 1 Deliverable

The final deliverable of Phase 1 is a single locked, execution-ready, row-level comparison frame consisting of:

- a historically verified treatment universe (18 stores, documented)
- an active primary treatment subset (13 stores, in the manifest)
- a strict same-chain internal control set (26 stores, in the manifest)
- a locked external competitor set (22 stores, in the manifest)
- one unified operational manifest with 61 active rows
- complete extraction endpoints
- complete group-role metadata
- complete window metadata
- row-level readiness for downstream extraction

That deliverable is `location_manifest_final.csv`.

---

## 12. State of the Project at the End of Phase 1

The project is now in the position it needed to reach before Phase 2 could begin. Phase 1 is effectively done and concluded successfully because:

- the comparison frame is locked
- the treatment side is locked
- the Tier 1 side is locked
- the Tier 2 side is locked
- the active manifest is locked
- a single definitive file is available for downstream consumption

The project is now ready to move into Phase 2 as an execution and data-assembly problem, not a cohort-definition problem.

---

## 13. Handoff to Phase 2

This section is intentionally brief. A full Phase 2 narrative will appear in the companion document, *Phase 2 Completion Report — Extraction Pipeline and Master Dataset Construction*. The purpose of this section is only to mark the contract between the two phases.

### 13.1 What Phase 2 inherits from Phase 1

- The 61-row active manifest in `location_manifest_final.csv` as its single execution input.
- The locked treatment / Tier 1 / Tier 2 group structure, which Phase 2 must preserve in all extracted and derived datasets.
- The strict primary same-chain analytical frame of 39 JCS locations as the principal carrier of the causal contrast.
- The matching context (storefront topology, historical review volume, socioeconomic context, region/market-type context, row-level confidence) as the analytical interpretation lens for each Tier 1 control.
- The hard-gate Tier 2 logic as the analytical interpretation lens for each external competitor.
- The pilot and primary pre/post analytical windows as the time skeleton for all extracted reviews.
- The intentional `T2_021` gap, which Phase 2 must preserve rather than backfill.

### 13.2 What Phase 2 is responsible for

- Executing review extraction against every row of the manifest.
- Producing a clean, deduplicated, schema-stable review-level master dataset that inherits row-level identity, group role, and time-window metadata from the manifest.
- Applying the project's attribute lexicon and NLP scoring framework to the extracted reviews.
- Producing the analytical pre/post and treatment-vs-control structures used in the final analysis.

### 13.3 What Phase 2 is *not* permitted to do

- Re-open the cohort. Phase 2 may not add, remove, or reclassify locations; any such pressure must be raised back to a documented Phase 1 amendment rather than handled silently in the pipeline.
- Re-derive the matching context or the Tier 2 gates. Phase 2 inherits these as fixed.
- Treat the `T2_021` gap as data loss or as a row to be filled.

---

## 14. Document Control

| Field | Value |
|---|---|
| Document | Phase 1 Completion Report |
| Phase status | Closed |
| Primary artifact | `location_manifest_final.csv` |
| Active row count (locked) | 61 |
| Treatment / Tier 1 / Tier 2 split (locked) | 13 / 26 / 22 |
| Strict primary same-chain analytical frame (locked) | 39 |
| Companion document | Phase 2 Completion Report (forthcoming) |

---

*End of Phase 1 Completion Report.*
