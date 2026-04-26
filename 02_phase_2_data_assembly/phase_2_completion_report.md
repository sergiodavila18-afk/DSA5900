# Phase 2 Completion Report
## Extraction Pipeline and Master Dataset Construction for the Joe's Crab Shack No-Tipping Pilot Study

**Course:** DSA 5900 — Professional Practice (Spring 2026)
**Project:** Quantitative Evaluation of the Joe's Crab Shack No-Tipping Policy Intervention
**Phase:** 2 of N — Extraction Pipeline and Master Dataset Construction
**Status:** Complete and locked
**Primary input:** `location_manifest_final.csv` (61 active rows, locked Phase 1 deliverable)
**Primary deliverable:** `consolidated_cleaned_reviews.csv` (16,997 review-level rows × 42 columns)
**Companion documents:** *Phase 1 Completion Report — Experimental Design and Comparison Frame* (preceding); Phase 3 documentation (forthcoming, scope: attribute framework application and modeling)

---

## 1. Executive Summary

Phase 2 of the practicum has concluded successfully. Its purpose was to consume the locked 61-location comparison frame produced in Phase 1 and convert it into a defensible, schema-stable, review-level master analytical dataset suitable for the attribute-application and modeling work that will follow.

That objective has been met. Phase 2 is no longer a scraping experiment, a brittle PAD prototype, or a set of intermediate text dumps. It is a closed and reconciled stage whose finished product is a single execution-ready file: **`consolidated_cleaned_reviews.csv`**. That file holds **16,997 review-level rows × 42 columns**, distributed exactly along the Phase 1 group structure:

| Group (inherited from manifest) | Locations covered | Review rows |
|---|---:|---:|
| Treatment (active primary subset of historical pilots) | 13 / 13 | 1,577 |
| Tier 1 strict internal same-chain controls (non-pilot JCS) | 26 / 26 | 7,003 |
| Tier 2 locked external competitor cohort | 22 / 22 | 8,417 |
| **Total** | **61 / 61** | **16,997** |

Every one of the 61 active manifest locations is represented in the extracted dataset. The 13 / 26 / 22 split from Phase 1 is preserved without erosion. The strict primary same-chain analytical frame — the 39 JCS locations that anchor the project's principal causal contrast — accounts for **8,580 of the 16,997 reviews** (1,577 treatment + 7,003 Tier 1).

Phase 2 also satisfies the master-dataset construction obligation laid out in the practicum's project pipeline. The dataset is review-level, deduplicated by composite key, full-coverage across the cohort, and inherits its analytical identity (group, role, matched pilot, time windows) directly from the Phase 1 manifest. The downstream attribute-application and modeling phase can therefore read one file and begin work without re-resolving cohort questions or re-attaching analytical metadata.

Three honest limitations are documented in Section 9 and should be read alongside the headline results.

---

## 2. Phase 2 Mission and Scope

### 2.1 Mission statement

> Take the locked 61-row Phase 1 comparison frame and produce a single deduplicated, schema-stable, review-level master dataset in which (a) every Phase 1 location is represented, (b) every review row carries the analytical identity (group, role, matched pilot, time windows) of its source location, (c) review text is preserved cleanly enough for downstream attribute and modeling work, and (d) the resulting file can be consumed by the next phase as one source of truth without further extraction or cohort-resolution work.

### 2.2 In-scope work for Phase 2

Phase 2 was responsible for, and only for, the following classes of work:

1. **PAD-driven Yelp extraction.** Building, stabilizing, and operating the Power Automate Desktop (PAD) flow that navigates Yelp business pages for every manifest location and extracts review-card data at the row level.
2. **Schema construction at the review grain.** Designing the row-level schema that turns the page-level Yelp DOM into one review per row with normalized reviewer-level and review-level metadata.
3. **Dual-path resolution.** Resolving Yelp's structural variation (alternate review-text container paths and alternate reaction-layout paths) deterministically rather than through brittle single-selector logic.
4. **Python consolidation and cleanup.** Operating the Python post-processing layer (`parse_reviews.py`, `parse_2.py`) that converts PAD's per-page text exports into canonical review-level rows and stitches the rating / date / text triplet into a coherent review record.
5. **Reconciliation and gap-recovery.** Operating the cross-extraction reconciliation logic (`mismatch.py`) that compares the two extraction paths and recovers reviews captured by one path but missed by the other ("ghost reviews"), producing one consolidated set rather than two competing sets.
6. **Manifest inheritance.** Joining the cleaned review records back against `location_manifest_final.csv` so every review row carries its full analytical identity.
7. **Final consolidation and quality verification.** Producing `consolidated_cleaned_reviews.csv` and verifying that it satisfies coverage, identity, deduplication, and date-integrity standards.

### 2.3 Out-of-scope for Phase 2

Phase 2 explicitly did **not** perform any of the following — these belong to Phase 3:

- Application of the 97-attribute restaurant-review framework
- Sentiment polarity assignment, broad-band bucketing, or integer scoring
- LLM-based attribute applicability detection
- Human-coded benchmark scoring on extracted reviews
- Pre/post analytical contrasts, modeling, or causal estimation
- Visualization, dashboarding, or executive-ready reporting

This boundary is what allows Phase 2 to be declared closed. The dataset is analytically *describable* — every row knows what location it came from, what group it represents, what time window it falls in, and what its star rating is — but it is not yet *scored*. Scoring is Phase 3's job.

---

## 3. Design Principles

Five design principles governed every extraction and cleaning decision in Phase 2. They are documented here because Phase 3 will need to honor the same principles when it consumes the master dataset.

### 3.1 Treat structural variation as real, not as noise

Yelp's review cards do not render identically across reviews. The same field can appear in two different DOM paths depending on layout. The pipeline's response is to extract both paths and resolve them deterministically, rather than to flatten everything through a single broad selector that would silently corrupt the output.

### 3.2 Raw extraction and normalization are different stages

The PAD `Main` flow performs raw acquisition. The PAD `ProcessData` sub-flow performs row-level normalization. The Python layer performs cross-source reconciliation and final consolidation. These stages are deliberately separated, with persisted artifacts at each boundary, so that any one stage can be re-run without contaminating the others.

### 3.3 Review-level grain, end to end

Every cleaning decision is anchored on the principle that **one row equals one Yelp review tied to exactly one manifest location**. Page-level text, blob-level reviewer metadata, and aggregated reaction strings are all rejected as analytically wrong even when technically extractable.

### 3.4 Manifest inheritance over re-derivation

Analytical identity is not re-derived inside the extraction pipeline. It is **inherited from `location_manifest_final.csv` by join**. This is what guarantees that Phase 2 cannot accidentally re-open the cohort decisions made in Phase 1.

### 3.5 Documented limitations over silent degradation

Where a Yelp DOM region is structurally unstable (notably elite badges, reaction counts on the consolidated path, and the `seafood_review_count` field), the column is preserved in the schema but its limitations are documented in Section 9. The pipeline does not fabricate values to make the schema look complete.

---

## 4. The PAD Extraction Architecture (`Main` Flow)

### 4.1 Why the architecture changed during Phase 2

The first PAD design navigated page by page and extracted separate lists for ratings, dates, review text, and reactions, then wrote those lists to separate per-page text files. That design was workable for early experimentation but had four structural weaknesses: it produced fragmented per-field output rather than one row-based table, it made row alignment fragile because each field was extracted independently, it produced UTF-16 artifacts when downstream tools opened the files under the wrong encoding, and it encouraged overly broad selectors — especially for review text — that swallowed reaction labels and footer prompts into the review body.

The replacement design solved this by anchoring extraction on the repeated review-card `<li>` elements under Yelp's reviews list and treating the whole review card as one row with multiple columns. This is the design that produced the dataset documented here.

### 4.2 The three nested control layers

The PAD `Main` flow operates in three nested layers:

| Layer | Concern | Inputs | Outputs |
|---|---|---|---|
| **A. Browser / session setup** | Stable Microsoft Edge attachment with attach-or-launch fallback logic | None | A live browser context |
| **B. Location loop** | Iterate over `config\urls.csv` (LocationId, Address, BaseUrl) one row at a time | Location config CSV | Per-location navigation context |
| **C. Page / review extraction loop** | Within each location, walk pagination and extract one row per review card | A live business page | A row appended to `RunningTable` with location metadata + extracted review fields |

This three-layer structure preserves analytical identity at the review level (because each row is stamped with its source location's identifiers) while operating efficiently at the page and location level.

### 4.3 The `Main` flow stage map

The `Main` flow's 39 actions resolve into eight functional stages:

| Stage | Action range | Function |
|---|---|---|
| A. Initialization | 1–3 | Define `CleanReviewTable` and `RunningTable` schema contracts up front |
| B. Execution gating | 4 | Branch on `ButtonPressed`: run scraper or reuse prior output |
| C. Environment setup | 5–18 | Read location config, set up Edge browser context with attach/launch fallbacks |
| D. Core extraction loop | 19–31 | Locations → pages → review rows; append `[LocationName, Address, extracted fields]` to `RunningTable` |
| E. Raw dataset persistence | 32 | Write `ExtractedData.csv` (Stage 1 dataset artifact) |
| F. Reuse branch | 33–35 | Allow skipping extraction and reading prior `ExtractedData.csv` for reproducible re-runs |
| G. Processing pipeline call | 36 | Hand `RunningTable` to the `ProcessData` sub-flow |
| H. Final dataset production | 37–39 | Write `ProcessedData.csv`, deduplicate, overwrite |

The raw-versus-cleaned separation in Stages E and H is intentional and is what makes the extraction reproducible.

### 4.4 Why the row-anchor shift was the central improvement

The single most important architectural change in Phase 2 was moving from "extract ratings; extract dates; extract text; extract reactions; hope they line up" to "extract one row per review, with multiple columns tied to that same review card." Once the row anchor was correct, the pipeline could reason about `RunningTable` as a relational object and the rest of the extraction logic — schema enforcement, dual-path resolution, deduplication — became straightforward to layer on top.

---

## 5. The `ProcessData` Normalization Layer (Sub-Flow)

### 5.1 What `ProcessData` is for

The `ProcessData` sub-flow consumes `RunningTable` and produces `CleanReviewTable`. It is a deterministic, row-by-row transformation pipeline that converts raw DOM-dependent fields into analysis-ready typed fields. It is not a scraper. It is a schema-enforcing transformation layer.

### 5.2 The three transformation layers within `ProcessData`

`ProcessData`'s 94 actions resolve into three functional layers:

| Layer | Action range | Function |
|---|---|---|
| **Layer 1 — Raw ingestion** | 2–29 | Read each raw column from `CurrentRawRow` into a typed staging variable. No logic, only assignment. |
| **Layer 2 — Cleaning, validation, fallback** | 30–87 | Initialize defaults, type-cast where source is non-empty, resolve dual-path A/B selectors, normalize the elite flag to boolean. |
| **Layer 3 — Final assembly** | 88–94 | Consolidate final field names, parse and re-serialize the date as `yyyy-MM-dd`, and append the row to `CleanReviewTable`. |

### 5.3 The dual-path A/B resolution rule

The single most important behavior in `ProcessData` is the dual-path resolution for review text and for each reaction count. The rule is:

> **Prefer path A. If path A is empty, fall back to path B. If both are empty, default the field (empty string for text, `0` for reactions).**

This is implemented identically for `ReviewTextA` / `ReviewTextB` (actions 54–59) and for each of `helpful`, `thanks`, `love`, and `ohno` reaction counts (actions 60–83). This pattern is what allows the pipeline to absorb Yelp's structural inconsistency without either dropping reviews or contaminating the text column.

### 5.4 The most important normalization rules

| Concern | Rule | Implementation |
|---|---|---|
| Review text resolution | Use the populated alternate raw text source; normalize whitespace; reject reaction text and footer prompts | Actions 15–18, 54–59 |
| Reaction count resolution | Prefer A, fall back to B, default to `0` | Actions 60–83 |
| Elite flag normalization | Convert raw badge text to a boolean | Actions 30–33 |
| Numeric conversion | Cast reviewer counts, rating stars, reaction counts, and review-photo counts to numbers before append, not after | Actions 36, 40, 44, 48, 52, 62/64, 68/70, 74/76, 80/82, 86 |
| Date normalization | Parse to datetime, then re-serialize as `yyyy-MM-dd` | Actions 91–92 |
| Duplicate prevention | Use a row-level key during processing rather than relying solely on a post-hoc deduplication pass | Carried through to the Python consolidation layer |

### 5.5 Known issues in `ProcessData` itself

The verbatim Phase 2 notes record three known issues inside `ProcessData` that the pipeline absorbed without losing data integrity:

- **Action 90** assigns `FinalReviewerLocation` a second time, redundantly. No effect on output, but flagged for cleanup.
- **Actions 91–92** carry a small inconsistency: action 92 references `ReviewDate` rather than the `FinalReviewDate` that action 91 produced. Date integrity in the final CSV is intact (verified — see Section 8.4), but this is a code-level cleanup item.
- **Empty-string defaults for numeric fields** (e.g., friend, review, photo counts) introduce a downstream type-inconsistency risk. The Python consolidation layer absorbs this by treating empty strings and `NaN` interchangeably.

These are documented for code-review purposes and do not compromise the final dataset.

---

## 6. The Python Consolidation Layer

The Python layer is what converted PAD's per-page exports into the single `consolidated_cleaned_reviews.csv` file. It comprises three scripts, each with a focused responsibility.

### 6.1 `parse_reviews.py` — text-anchored extractor

`parse_reviews.py` is the single-file fallback path. It walks `raw_pages/` recursively for `.txt` files, opens each one with a UTF-16-then-UTF-8 fallback (necessary because PAD writes UTF-16 by default and downstream tools often assume UTF-8), and uses three regex anchors to reconstruct review records from the raw page text:

| Anchor | Pattern | Role |
|---|---|---|
| **Date pattern** | `^(Jan|Feb|...|Dec)\s\d{1,2},\s\d{4}$` | Marks the start of a review record |
| **Name pattern** | `^[A-Z][A-Za-z\-\'\s]*\s+[A-Z]\.$` | Walks back up to 5 lines from a date to find the reviewer name |
| **Stop pattern** | `^(Helpful|Thanks|Love this|Oh no)[\s\d]*$` | Marks the end of the review body |

Each review record is tagged with `Location_ID` (extracted from the filename via `_page_` split), `Reviewer_Name`, `Review_Date`, `Review_Text`, and a derived `Policy_Period` (`Pre-Policy (Tipping)` / `Active Policy (No-Tipping)` / `Post-Policy (Tipping Returned)`) bucketed against the policy window of `2015-11-01` through `2016-05-04`. Output: `Cleaned_Yelp_Reviews.csv`.

### 6.2 `parse_2.py` — three-file stitcher

`parse_2.py` is the structured-extraction path. It assumes the PAD flow has written three parallel per-page files for each location: a ratings file (`<loc>_page_<n>.txt`), a dates file (`<loc>_Date_page_<n>.txt`), and a text file (`<loc>_Text_page_<n>.txt`). For each ratings file it reconstructs the matching dates and texts files, reads all three under the same UTF-16-then-UTF-8 fallback, and stitches them index-by-index up to `min(len(ratings), len(dates), len(texts))`. Each stitched record is cleaned by a footer regex (`Helpful\s*\d*.*?What do you think\?`) and a `^\d+\sphotos?` photo-label stripper. Each row carries `Location_ID`, `Review_Date`, `Rating`, `Review_Text`, and the derived `Policy_Period`. Output: `Final_Master_Dataset.csv`.

### 6.3 `mismatch.py` — ghost-review reconciliation

`mismatch.py` resolves the disagreement between the two extraction paths. It loads both `Cleaned_Yelp_Reviews.csv` (from the text-anchored extractor) and `Final_Master_Dataset.csv` (from the three-file stitcher), normalizes every review text to a punctuation-and-case-stripped key via `re.sub(r'[^a-z0-9]', '', str(text).lower())`, and performs a substring-containment check on the first 50 normalized characters of each original review against all reviews from the same `Location_ID` in the structured dataset. Any original review that cannot be located by substring containment in the structured set is flagged as a "ghost review" and written to `Ghost_Reviews_Identified.csv`. This is what allowed Phase 2 to recover reviews captured only by the text-anchored fallback and to consolidate everything into one final set.

### 6.4 The consolidation strategy

The final consolidation layer joins the consolidated review records against `location_manifest_final.csv` on `location_id` and inherits every analytical-identity field directly from the manifest. This is the step that promotes a per-source-file review record into a fully-typed master-dataset row carrying its group, role, matched pilot, time windows, location confidence, and selection status without any re-derivation.

---

## 7. The Final Master Dataset

### 7.1 The artifact

The Phase 2 deliverable is `consolidated_cleaned_reviews.csv`. It is a single flat CSV file with **16,997 rows** and **42 columns**. Each row represents exactly one Yelp review tied to exactly one Phase 1 manifest location.

### 7.2 The schema (42 columns)

The 42 columns are organized into five functional bands:

| Band | Columns | Source |
|---|---|---|
| **Run / provenance** | `run_id`, `source_file`, `source_stem` | Pipeline metadata |
| **Location identity (inherited from manifest)** | `location_id`, `name`, `address`, `city`, `state`, `zip`, `source_url` | `location_manifest_final.csv` |
| **Analytical identity (inherited from manifest)** | `group_type`, `analysis_role`, `matched_pilot_id`, `chain_type`, `include_in_primary_analysis`, `include_in_secondary_analysis`, `pilot_window_start`, `pilot_window_end`, `pre_start`, `pre_end`, `post_start`, `post_end`, `location_confidence`, `notes`, `selection_status` | `location_manifest_final.csv` |
| **Reviewer-level fields** | `reviewer_name`, `reviewer_location`, `elite`, `is_elite`, `review_count`, `friend_count`, `photo_count_user`, `reviewer_seafood_review_count` | Extracted (with caveats — see Section 9) |
| **Review-level fields** | `rating`, `review_date`, `review_text`, `helpful`, `thanks`, `love`, `ohno`, `review_photos`, `review_key` | Extracted |

The `review_key` field is a composite key of the form `{location_id}|{lowercased reviewer_name}|{review_date}|{first portion of normalized review_text}`. It is the primary mechanism for review-level deduplication in the consolidation layer.

### 7.3 Coverage by group

All 61 manifest locations are present in the dataset. The group-level coverage and review counts are:

| Group | Locations covered (extracted / manifest) | Review rows |
|---|---|---:|
| Treatment | 13 / 13 | 1,577 |
| Internal control (Tier 1) | 26 / 26 | 7,003 |
| External competitor (Tier 2) | 22 / 22 | 8,417 |
| **Total** | **61 / 61** | **16,997** |

The strict primary same-chain analytical frame (treatment + Tier 1) accounts for **8,580 reviews**. The Tier 2 external benchmark accounts for **8,417 reviews**.

### 7.4 Date coverage

Review dates span **2005-07-08 through 2026-04-10**, giving the dataset wide pre-policy, active-policy, and post-policy coverage. Bucketing every row against the policy window (`2015-11-01` to `2016-05-04`):

| Policy period | Treatment | Internal control | External competitor | Total |
|---|---:|---:|---:|---:|
| Pre-Policy (Tipping) | 891 | 3,049 | 2,472 | 6,412 |
| Active Policy (No-Tipping) | 148 | 407 | 322 | 877 |
| Post-Policy (Tipping Returned) | 538 | 3,547 | 5,623 | 9,708 |
| **Total** | **1,577** | **7,003** | **8,417** | **16,997** |

The active-policy window is — as expected for a six-month historical pilot — the smallest of the three buckets but is populated for all three groups.

### 7.5 Rating distribution

The rating field is fully populated and spans the full 1–5 range with no missing values:

| Rating | Count | Share |
|---|---:|---:|
| 1 | 3,998 | 23.5% |
| 2 | 2,366 | 13.9% |
| 3 | 2,355 | 13.9% |
| 4 | 3,339 | 19.6% |
| 5 | 4,939 | 29.1% |
| **Total** | **16,997** | **100.0%** |

The distribution is the expected Yelp J-shape (heavy on 1s and 5s, lighter in the middle), which is consistent with sincere extraction rather than systematic distortion.

---

## 8. Operational Readiness of the Master Dataset

The dataset is operationally ready in the strict execution sense — meaning the next phase can read it deterministically and start work without further extraction, joining, or repair. This readiness rests on four certainties, all of which were verified directly against the file.

### 8.1 Coverage certainty

Every Phase 1 manifest location is present in the dataset.

- 13 / 13 treatment locations represented ✓
- 26 / 26 Tier 1 internal control locations represented ✓
- 22 / 22 Tier 2 external competitor locations represented ✓
- 61 / 61 total active manifest locations represented ✓

There is no silent location dropout between Phase 1 and Phase 2.

### 8.2 Identity certainty

Every row carries its full analytical identity inherited from the manifest by join: `location_id`, `group_type`, `analysis_role`, `matched_pilot_id`, `chain_type`, `include_in_primary_analysis`, `include_in_secondary_analysis`, `pilot_window_start`, `pilot_window_end`, `pre_start`, `pre_end`, `post_start`, `post_end`, `location_confidence`, `notes`, and `selection_status`. This means Phase 3 can group, filter, and label reviews without joining back to the manifest.

### 8.3 Deduplication certainty

The dataset contains **16,997 unique `review_key` values across 16,997 rows**, meaning **zero duplicate review rows**. The composite-key strategy described in Section 7.2 was effective.

### 8.4 Date integrity

All 16,997 rows have a parseable `review_date`. There are **zero `NaT` (unparseable) date values** in the file. This is what allows the policy-period bucketing in Section 7.4 to be performed with no row loss.

### 8.5 Operational-readiness summary

| Certainty | Verification | Status |
|---|---|---|
| Coverage | All 61 manifest locations present in extracted dataset | ✓ |
| Identity | All 14 manifest-inherited identity fields populated on every row | ✓ |
| Deduplication | 0 duplicate `review_key` values across 16,997 rows | ✓ |
| Date integrity | 0 `NaT` values across 16,997 rows; full pre/active/post coverage | ✓ |

---

## 9. Honest Limitations of the Phase 2 Deliverable

Three honest limitations are documented here. None of them blocks Phase 3, but all three should be acknowledged when describing the dataset to stakeholders or the practicum sponsor.

### 9.1 Elite flag is uniformly `False`

The `is_elite` column is `False` for all 16,997 rows in the consolidated CSV. The verbatim Phase 2 notes flagged elite-badge rendering as one of the structurally inconsistent regions of the Yelp DOM (badges sometimes rendered as labeled text, sometimes as numeric strings such as `Elite 26`, sometimes embedded in a flattened blob). The PAD `ProcessData` flow normalizes elite to a boolean through actions 30–33, but the underlying badge field appears to have been empty or unrecognizable during the consolidated pass that produced this CSV.

> **Implication for Phase 3:** Do not use `is_elite` as a covariate or stratifier without first re-extracting elite status in a targeted second pass. Treat the column as present-but-unpopulated.

### 9.2 Reaction counts (`helpful`, `thanks`, `love`, `ohno`) are empty

All four reaction-count columns are empty across all 16,997 rows in the consolidated CSV. The PAD `ProcessData` sub-flow has the full A/B fallback logic for these fields (Section 5.3), and the verbatim notes describe the dual-path resolution as working correctly during interactive testing. The empty state in the consolidated CSV indicates that the dual-path reaction extraction did not survive into the final consolidation, most likely because the Python consolidation layer drew from an extraction artifact that did not include the reaction columns.

> **Implication for Phase 3:** Reaction counts are not available as covariates or as engagement signals in this version of the master dataset. If they become analytically necessary, they would require a targeted re-extraction pass against the same source URLs.

### 9.3 `reviewer_seafood_review_count` is unpopulated

The `reviewer_seafood_review_count` field is unpopulated across the dataset. This matches the explicit warning in the verbatim notes that the seafood-review-count field is structurally unstable in the Yelp DOM. It is preserved in the schema for forward compatibility but should not be treated as a usable feature in this release.

> **Implication for Phase 3:** Do not use this field. It is reserved.

### 9.4 What these limitations do *not* affect

These three limitations are real, but they are bounded. They do not affect:

- The presence or correctness of `location_id`, `group_type`, `analysis_role`, or any manifest-inherited identity field
- The completeness of `review_text` (zero null rows)
- The integrity of `review_date` (zero `NaT` rows)
- The presence of `rating` (fully populated, full 1–5 range)
- Coverage across all 61 manifest locations
- The 1,577 / 7,003 / 8,417 split

The core analytical substrate — text, rating, date, location, group — is intact.

---

## 10. Why the 16,997-Row Consolidated CSV Is the Correct Phase 2 Endpoint

Phase 2's success criterion was never maximal Yelp coverage, perfect reaction-count fidelity, or completeness of every reviewer-level metadata field. The success criterion was a single review-level dataset that:

1. covers every Phase 1 manifest location,
2. carries Phase 1 analytical identity by inheritance,
3. is deduplicated at the review level,
4. has clean, parseable dates and full review text, and
5. can be consumed by the next phase as one source of truth.

`consolidated_cleaned_reviews.csv` satisfies all five criteria. The three limitations in Section 9 are real but do not compromise the dataset's analytical core, and they are documented honestly rather than hidden. Phase 2 therefore concluded by preserving the work that holds analytical weight rather than by chasing extraction completeness for fields that the pipeline had already surfaced as structurally unstable.

---

## 11. Methodological Position at the End of Phase 2

At this point, Phase 2 has resolved the project's master-dataset construction obligation in a sufficiently disciplined way that the project is no longer engaged in extraction work. The phase has completed:

- PAD extraction architecture (Main flow + ProcessData sub-flow)
- Row-anchored review-card extraction
- Dual-path A/B resolution for review text and reactions
- Schema enforcement at the review grain
- UTF-16 / UTF-8 encoding handling for PAD outputs
- Dual-path Python extraction (text-anchored and three-file structured)
- Cross-extraction reconciliation via ghost-review detection
- Manifest inheritance by join
- Review-level deduplication via composite key
- Final consolidation and quality verification

The project has therefore moved past the question *"how do we extract and assemble the data for the locked study set?"* and into the question *"how do we score and analyze the assembled data?"* That second question is the entire subject of Phase 3.

---

## 12. Final Phase 2 Deliverable

The final deliverable of Phase 2 is a single locked, execution-ready, review-level master dataset consisting of:

- 16,997 deduplicated review rows
- 42 columns spanning provenance, location identity, analytical identity, reviewer-level fields, and review-level fields
- Full coverage across all 61 Phase 1 manifest locations
- Full inheritance of Phase 1 analytical identity (group, role, matched pilot, time windows, selection status)
- Clean review text (zero nulls) and parseable dates (zero `NaT`)
- A composite `review_key` for downstream join safety
- Three honestly documented limitations (elite, reactions, seafood count)

That deliverable is `consolidated_cleaned_reviews.csv`.

---

## 13. State of the Project at the End of Phase 2

The project is now in the position it needed to reach before Phase 3 could begin. Phase 2 is effectively done and concluded successfully because:

- the master dataset is locked
- every Phase 1 location is represented
- the 13 / 26 / 22 group structure is preserved
- analytical identity is carried by inheritance, not by re-derivation
- review-level deduplication is verified
- date integrity is verified
- all three structural limitations are honestly documented
- a single definitive file is available for downstream consumption

The project is now ready to move into Phase 3 as a scoring, modeling, and inference problem, not a data-construction problem.

---

## 14. Handoff to Phase 3

This section is intentionally brief. A full Phase 3 narrative will appear in the next companion document. The purpose of this section is only to mark the contract between the two phases.

### 14.1 What Phase 3 inherits from Phase 2

- `consolidated_cleaned_reviews.csv` (16,997 rows × 42 columns) as its single execution input.
- The locked group / role / window structure for every review, inherited transitively from Phase 1.
- A composite `review_key` that downstream joins must preserve, not regenerate.
- Clean, populated, full-coverage `review_text`, `rating`, and `review_date` columns.
- The pre/active/post policy-period bucketing as a fixed time skeleton.

### 14.2 Parallel methodology assets Phase 3 will use (NOT produced by Phase 2)

Phase 3 will additionally draw on two methodology assets that were developed **in parallel with** Phase 2 rather than produced by it. These assets were built on Dr. Kumar's provided `Sample.csv` (a 500-row generic restaurant-review sample used as an NLP development sandbox), not on the Yelp extract this phase produced. They are listed here — and deliberately not in Section 14.1 — so the traceability of Phase 3's inputs is unambiguous and so that no downstream reader mistakes them for Phase 2 deliverables.

| Asset | Provenance | Contents |
|---|---|---|
| `restaurant_review_attribute_lexicon.csv` | Team-authored attribute framework delivered to Dr. Kumar in the March 10, 2026 update | 97 attributes organized into 9 categories (Brand and Digital Experience; Logistics and Arrival; Environment and Atmosphere; Food and Beverage; Service and Staff; Value and Checkout; Audience and Occasion; Delivery and Takeout; Overall Satisfaction). Each attribute carries a locked conceptual definition and a set of common markers. The framework encodes a `-100` to `+100` sentiment scale and an 11-band sentiment mapping (Extreme Negative through Extreme Positive). |
| `manual_attribute_scores.csv` | Team-produced human-coded benchmark delivered to Dr. Kumar in the March 18, 2026 update | **100 reviews** drawn from `Sample.csv` using a **5-stratum length-based stratified sampling design** (100-word intervals; reviews under 15 words and over 400 words excluded as outliers; file-index tie-breaker for reproducibility; 20 reviews per stratum), yielding **646 attribute instances** scored through a disciplined three-phase workflow: sign assignment (`+1` / `-1` / `0`) → broad-band assignment (one of 11 locked subranges) → exact-integer assignment within the chosen band. |

Neither asset is a Phase 2 deliverable, neither is a product of the Yelp extraction pipeline, and neither was scored on the extracted Yelp corpus. Phase 3 therefore inherits `consolidated_cleaned_reviews.csv` from Phase 2 and the two assets above from parallel project work — three independent inputs, two origins.

### 14.3 What Phase 3 is responsible for

- Applying the project's 97-attribute restaurant-review framework to every review row.
- Producing attribute-level applicability flags, sentiment polarities, broad-band buckets, and integer scores.
- Running the multi-model attribute-and-scoring pipeline against the master dataset.
- Validating model output against the human-coded benchmark.
- **Explicitly acknowledging the benchmark's domain-transfer assumption.** The benchmark in `manual_attribute_scores.csv` was scored on `Sample.csv` — Dr. Kumar's generic restaurant-review sample — not on Yelp reviews of JCS locations or their casual-dining internal controls and external competitors. Any validation of Phase 3 model output against the benchmark therefore rests on the assumption that `Sample.csv` and the Phase 2 Yelp corpus represent restaurant-review language comparably enough for benchmark-derived performance signals to transfer. This assumption is plausible (both corpora are English-language restaurant reviews from U.S. diners evaluating sit-down restaurant experiences) but it is **not self-evident**, and it must be stated explicitly in Phase 3 reporting rather than absorbed silently into model-validation metrics. If sensitivity analysis suggests the assumption is weak in practice, Phase 3 should schedule a supplementary human-coded benchmark pass drawn from `consolidated_cleaned_reviews.csv` itself rather than continue to rely on cross-domain validation alone.
- Building the modeling-ready table and executing the interpretable machine-learning stage.
- Producing the executive-ready analytical contrasts and final reporting.

### 14.4 What Phase 3 is *not* permitted to do

- Re-open the cohort. Phase 3 may not add, remove, or reclassify locations; any such pressure must be raised back to a documented Phase 1 amendment.
- Re-extract reviews. Phase 3 reads from the master dataset; it does not run its own scrape.
- Use `is_elite`, the four reaction-count columns, or `reviewer_seafood_review_count` as features without first executing a documented re-extraction pass against the same source URLs (Section 9).
- Silently drop manifest locations during analysis. Coverage by group must be reported even when a group is excluded from a particular contrast.

---

## 15. Document Control

| Field | Value |
|---|---|
| Document | Phase 2 Completion Report |
| Phase status | Closed |
| Primary input | `location_manifest_final.csv` (61 active rows, locked Phase 1 deliverable) |
| Primary artifact | `consolidated_cleaned_reviews.csv` |
| Total review rows (locked) | 16,997 |
| Total schema columns (locked) | 42 |
| Treatment / Tier 1 / Tier 2 review-row split (locked) | 1,577 / 7,003 / 8,417 |
| Manifest-location coverage (locked) | 61 / 61 |
| Strict primary same-chain analytical review pool (locked) | 8,580 reviews across 39 locations |
| Documented limitations | `is_elite` uniformly False; reaction counts empty; `reviewer_seafood_review_count` unpopulated |
| Parallel methodology assets (NOT Phase 2 products) | `restaurant_review_attribute_lexicon.csv` — 97 attributes × 9 categories, locked definitions, `-100` to `+100` scale, 11-band sentiment mapping; `manual_attribute_scores.csv` — 100 reviews × 646 attribute instances, 5-stratum length-based stratified sample of `Sample.csv`, three-phase scoring workflow |
| Critical methodological assumption for Phase 3 | Benchmark-to-master-dataset domain transfer (`Sample.csv` generic-restaurant corpus → Phase 2 Yelp JCS / casual-dining corpus) |
| Companion documents | Phase 1 Completion Report (preceding); Phase 3 documentation (forthcoming) |

---

*End of Phase 2 Completion Report.*
