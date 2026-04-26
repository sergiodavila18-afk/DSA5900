# Phase 4 Completion Report
## Multi-Model Attribute Detection and Sentiment Scoring Pipeline

**Course:** DSA 5900 — Professional Practice (Spring 2026)
**Project:** Quantitative Evaluation of the Joe's Crab Shack No-Tipping Policy Intervention
**Phase:** 4 of N — Multi-Model Attribute Detection and Sentiment Scoring
**Status:** Complete and locked
**Primary input:** `consolidated_cleaned_reviews.csv` (16,997 rows × 42 columns, locked Phase 2 deliverable)
**Primary methodology asset:** `Attribute_Canonical_Definitions.md` (40-attribute canonical taxonomy, Phase 3 deliverable)
**Primary deliverable:** `joes_crab_shack_sentiment_consolidated_wide.csv` (16,997 rows × 63 columns)
**Companion documents:** Phase 1 Completion Report (Experimental Design and Comparison Frame); Phase 2 Completion Report (Extraction Pipeline and Master Dataset Construction); Phase 3 Completion Report (Canonical Attribute Reduction); Phase 5 documentation (forthcoming, scope: statistical modeling and final reporting)

---

## 1. Executive Summary

Phase 4 of the practicum has concluded successfully. Its purpose was to take the locked 16,997-review master dataset produced in Phase 2 and the locked 40-attribute canonical taxonomy produced in Phase 3, and to convert them into a row-level, scored attribute table suitable for the statistical modeling and final-reporting work that will follow.

That objective has been met. Phase 4 is no longer a multi-model exploration, a prompt-iteration exercise, or an in-progress audit trail. It is a closed and reconciled stage whose finished product is a single execution-ready file: **`joes_crab_shack_sentiment_consolidated_wide.csv`**. That file holds **16,997 review-level rows × 63 columns** and carries one integer sentiment score in `[−100, +100]` for every locked review-attribute pair across the corpus.

The pipeline executed three structured passes against three independent LLM providers (OpenAI, Anthropic, Google Gemini) for every review:

| Pass | Function | Output |
|---|---|---:|
| Pass 1 | Three-provider attribute identification | 50,417 candidate (review, attribute) decisions on the JCS batch alone, with a comparable order of magnitude on the competitor batch |
| Tiered resolution + Pass 2 | Deterministic majority resolution (90.4% of JCS-batch decisions) and selective deadlock adjudication (9.6% of JCS-batch decisions; 7,924 deadlocks across 2,217 reviews corpus-wide) | 73,094 locked review-attribute pairs |
| Pass 3 | Three-provider structured factor scoring | 73,094 final integer scores |

The Phase 1 group structure (13 / 26 / 22 locations; 1,577 / 7,003 / 8,417 reviews) is preserved without erosion through the entire pipeline. **All 16,997 reviews reach a final state**: 16,741 carry at least one locked attribute and a final score, and 256 are recorded as having no scoreable content — not silently dropped, but explicitly recorded as zero-attribute rows.

The Phase 4 deliverable is now the project's central analytical asset. Phase 5 will consume it as a single source of truth for difference-in-differences modeling on the project's primary tipping outcome and for any secondary attribute contrasts the project elects to report.

---

## 2. Phase 4 Mission and Scope

### 2.1 Mission statement

> Take the locked Phase 2 master dataset and the locked Phase 3 canonical taxonomy and produce a single deduplicated, schema-stable, score-bearing analytical table in which (a) every review's applicable attributes have been identified by three-provider consensus under a deterministic resolution protocol, (b) every locked review-attribute pair carries a structured factor-derived sentiment score on a `−100` to `+100` integer scale, (c) every analytical-identity field from upstream phases is preserved by inheritance rather than re-derivation, and (d) the resulting file can be consumed by Phase 5 as one source of truth without further extraction, scoring, or attribute-resolution work.

### 2.2 In-scope work for Phase 4

Phase 4 was responsible for, and only for, the following classes of work:

1. **Three-provider attribute identification (Pass 1).** Sending each of the 16,997 reviews to OpenAI, Anthropic, and Google Gemini under an identical system prompt that constrained outputs to the 40-attribute canonical set, producing one applicability decision plus supporting evidence per (review, provider, attribute).
2. **Tiered deterministic resolution.** Reconciling the three providers' Pass 1 outputs into a per-review locked attribute list using a deterministic majority-rule protocol that resolves the bulk of decisions without further model calls.
3. **Selective deadlock adjudication (Pass 2).** Routing only structurally ambiguous deadlocked rows back to all three providers for a focused binary include/exclude decision per disputed attribute.
4. **Three-provider structured sentiment scoring (Pass 3).** For every locked review-attribute pair, eliciting from each provider one polarity sign and six factor ratings (directness, intensity, specificity, certainty, centrality, consequence) on a 0–10 scale.
5. **Deterministic score consolidation.** Computing a single integer score in `[−100, +100]` per locked pair from the three providers' factor ratings using a fixed weighted-mean formula (Section 5).
6. **Provider audit and traceability.** Preserving per-provider raw responses, schema-validation outcomes, consolidation traces, and lenient-recovery records so that every final score is traceable end-to-end.
7. **Final consolidation and quality verification.** Producing `joes_crab_shack_sentiment_consolidated_wide.csv` and verifying that it satisfies coverage, identity, deduplication, score-completeness, and provider-traceability standards.

### 2.3 Out-of-scope for Phase 4

Phase 4 explicitly did **not** perform any of the following — these belong to Phase 5 or to parallel project work:

- Comparison-frame definition (locked in Phase 1)
- Master review dataset construction and cleaning (locked in Phase 2)
- Canonical taxonomy reduction from the 96-attribute legacy framework to the 40-attribute canonical set (locked in Phase 3)
- Statistical analysis, difference-in-differences regression, or causal interpretation
- Visualization, dashboarding, executive-ready reporting, or final presentation

This boundary is what allows Phase 4 to be declared closed. The dataset is *scored* — every locked review-attribute pair has a final integer in `[−100, +100]` — but it is not yet *modeled*. Modeling is Phase 5's job.

---

## 3. Design Principles

Six design principles governed every modeling, prompting, and consolidation decision in Phase 4. They are documented here because Phase 5 will need to honor them when it consumes the scored dataset.

### 3.1 Applicability and scoring are different problems

The pipeline treats "which attributes apply to this review?" and "how strong is the sentiment for each applicable attribute?" as two distinct decisions executed by two distinct prompt families with two distinct schemas. Pass 3 is *never* permitted to revisit applicability; it scores only the locked attribute set produced by Pass 1 and Pass 2.

### 3.2 Three independent providers, no preferred provider

OpenAI, Anthropic, and Google Gemini each receive the identical system prompt, the identical canonical taxonomy payload, and the identical JSON output schema. No provider is given more authority than any other. Disagreement among providers is treated as **signal**, not as noise — disagreement at the applicability layer drives routing into Pass 2 adjudication, and disagreement at the scoring layer is absorbed by mean consolidation.

### 3.3 Deterministic resolution wherever possible

The pipeline avoids unnecessary LLM calls. Tiered majority resolution mechanically decides the large majority of attribute-applicability cases without calling Pass 2. Pass 2 is reserved for the structurally ambiguous deadlock case (1-of-2 disagreement when only two providers succeeded). This is what keeps the pipeline auditable and cost-bounded.

### 3.4 Schema-validated, JSON-only model outputs

Every model response is validated against a JSON Schema document (one per pass) before it is accepted. Responses that fail validation after content-level retries are rejected; the consolidation math handles missing providers gracefully via the remaining valid responses.

### 3.5 Inheritance over re-derivation

Analytical identity (group, role, matched pilot, time windows, location confidence, selection status) is not re-derived inside the scoring pipeline. It is **inherited from `consolidated_cleaned_reviews.csv` by join**, which is itself inherited from `location_manifest_final.csv` by join. This is what guarantees that Phase 4 cannot accidentally re-open the cohort decisions made in Phase 1 or the extraction decisions made in Phase 2.

### 3.6 Documented limitations over silent degradation

Where a step in the pipeline absorbs a known imperfection — most visibly, lenient recovery for schema-incomplete Pass 3 responses — the imperfection is preserved in the audit trail rather than smoothed away. Section 12 documents these honestly.

---

## 4. Inputs Inherited by Phase 4

### 4.1 Phase 1 inheritance — comparison frame

The locked Phase 1 manifest carries through transitively. Every Phase 4 row carries `location_id`, `group_type`, `analysis_role`, `chain_type`, `matched_pilot_id`, `pilot_window_start`, `pilot_window_end`, `pre_start`, `pre_end`, `post_start`, `post_end`, `location_confidence`, `notes`, and `selection_status` inherited from the manifest by way of Phase 2.

### 4.2 Phase 2 inheritance — master review dataset

`consolidated_cleaned_reviews.csv` (16,997 rows × 42 columns) is the single review-level execution input. Every Phase 4 row carries `model_review_id`, `source_review_id`, `reviewer_name`, `reviewer_location`, `is_elite`, `rating`, `review_date`, `review_text`, and the composite `review_key` from this file.

### 4.3 Phase 3 inheritance — canonical attribute taxonomy

`Attribute_Canonical_Definitions.md` defines the 40 surviving attributes. The taxonomy was locked **before** Phase 4 began. No attribute additions, removals, or definition changes occurred during Phase 4 execution. All three providers received the identical canonical taxonomy in their Pass 1 and Pass 2 system prompts.

### 4.4 Operational inputs

| Input | Purpose |
|---|---|
| Three LLM provider API credentials (OpenAI, Anthropic, Google Gemini) | Per-pass model invocation |
| `config/settings.yaml` | Provider configuration, model identifiers, retry policies, file path conventions |
| `prompts/pass1_system.txt` | Pass 1 attribute-identification system prompt |
| `prompts/pass2_deadlock_system.txt` | Pass 2 deadlock-adjudication system prompt |
| `prompts/pass3_system.txt` | Pass 3 structured-scoring system prompt |
| `schemas/pass1_output_schema.json` | Pass 1 response validator |
| `schemas/pass2_output_schema.json` | Pass 2 response validator |
| `schemas/pass3_output_schema.json` | Pass 3 response validator |

### 4.5 Note on attribute identifiers

The 40 canonical attributes are exposed in pipeline outputs as alphabetically ordered identifiers `A01` through `A40` so the scored CSV's column names sort intuitively. This identifier ordering is independent of the categorical ordering used in `Attribute_Canonical_Definitions.md`. As a consequence: in this report's output schema, **`A38` is Tipping** (the project's primary outcome attribute) because Tipping is the 38th attribute alphabetically; in the source-of-truth taxonomy document, Tipping is listed as item 39 within the cluster ordering. Both numbers refer to the same attribute, and the Section 5.1 cross-reference table makes the mapping explicit so no downstream reader confuses them.

---

## 5. The Canonical Attribute and Scoring Framework

### 5.1 The 40 canonical attributes (alphabetical, with output identifiers)

| ID | Name | ID | Name |
|---|---|---|---|
| A01 | Air Quality | A21 | Manager Presence |
| A02 | Ambiance | A22 | Noise Level |
| A03 | Appetizers | A23 | Order Accuracy |
| A04 | Attentiveness | A24 | Outdoor Dining |
| A05 | Bar Drinks | A25 | Parking |
| A06 | Brand Consistency | A26 | Perceived Care |
| A07 | Chef Creativity | A27 | Perceived Fairness |
| A08 | Cleanliness | A28 | Perceived Respect |
| A09 | Competence | A29 | Portion Size |
| A10 | Conflict Resolution | A30 | Price Fairness |
| A11 | Crowd Density | A31 | Seasonal Menu |
| A12 | Customization | A32 | Service Speed |
| A13 | Family-Friendliness | A33 | Signature Dishes |
| A14 | Flavor Profile | A34 | Special Occasions |
| A15 | Group-Friendliness | A35 | Staff Coordination |
| A16 | Happy Hour | A36 | Table Placement |
| A17 | Host Stand | A37 | Temperature and Doneness |
| A18 | Hype vs. Reality | **A38** | **Tipping** *(study primary outcome)* |
| A19 | Ingredient Quality | A39 | Trustworthiness |
| A20 | Location | A40 | Wait Time |

### 5.2 Anti-bleed exclusion rules

Each attribute's canonical definition includes explicit exclusion clauses to prevent semantic overlap with neighboring attributes. Examples enforced via prompt:

- Sound and acoustic mentions route to `A22` Noise Level, not `A02` Ambiance.
- Heat or cook-level mentions route to `A37` Temperature and Doneness, not `A14` Flavor Profile.
- Wait time *before being seated* routes to `A40` Wait Time; pace *after seating* routes to `A32` Service Speed.
- `A26` Perceived Care is suppressed when `A04` Attentiveness, `A09` Competence, or `A28` Perceived Respect already captures the same evidence.

These rules are embedded directly into the Pass 1 and Pass 2 system prompts so that all three providers apply them consistently across the corpus.

### 5.3 The scoring formula

For each locked (review, attribute) pair, each of the three providers returns:

- One **polarity sign** in `{−1, 0, +1}`
- Six **structured factor ratings**, each an integer in `{0, 1, …, 10}`: directness `D`, intensity `I`, specificity `S`, certainty `C`, centrality `Cₑ`, consequence `Cₒ`

The pipeline consolidates deterministically:

| Step | Rule |
|---|---|
| Polarity sign | Majority vote across the three providers; three-way ties (one each of `−1`, `0`, `+1`) default to `0` |
| Each factor | Arithmetic mean across the three providers, preserving fractional precision |
| Magnitude `M` | `M = (2·D̄ + 2·Ī + S̄ + C̄ + C̄ₑ + C̄ₒ) / 80` |
| Final score | `sign × round(100 · M)`, yielding an integer in `[−100, +100]` |

The `2·` weights on directness and intensity reflect the design judgment that *how clearly the review states the sentiment* and *how strongly the language is pitched* are the two most informative factors for sentiment magnitude. The remaining four factors are weighted equally.

**Reachability of the full integer range.** The combination of a 0–10 factor scale, three-provider arithmetic-mean consolidation, and the `sign × round(100 · M)` formula guarantees that **every integer in `[−100, +100]` is mathematically reachable** by some configuration of provider responses. *Sketch of proof:* with three providers, each factor mean is `k/3` for some integer `k ∈ {0, …, 30}` (since the mean of three integers in `{0, …, 10}` is the sum-of-three divided by 3, and the sum-of-three is itself an integer in `{0, …, 30}`). Substituting into the magnitude formula and clearing the `1/3`, the weighted-sum numerator satisfies `3W = 2a + 2b + c + d + e + f` where each of `a, b, c, d, e, f` is an integer in `{0, …, 30}`. The unit-weighted terms (those weighted `1`, not `2`) guarantee that **every integer `N` from 0 to 240 is achievable** as a value of `3W`, since incrementing any unit-weighted factor by 1 increments `N` by exactly 1 (and there is always headroom to do so without exceeding `30` on any one factor). The final-score formula then reduces to `sign · round(5N/12)`. Because the rounding interval has width `12/5 = 2.4`, every interval `[(12t − 6)/5, (12t + 6)/5)` for integer target `t ∈ {0, …, 100}` contains at least two consecutive integers — and therefore contains at least one valid `N`. So every magnitude target `0 ≤ t ≤ 100` is reachable, which means every signed final score `−100 ≤ score ≤ 100` is reachable. (The integer `0` is reachable through two paths: `W = 0`, or `sign = 0` regardless of `W`.) This is why mean (rather than median) consolidation was chosen for the factor values: median consolidation would forfeit the dense reachability that lets modest provider disagreement still produce well-calibrated scores.

---

## 6. Pipeline Architecture

The pipeline is implemented as a sequence of deterministic Python scripts that read and write JSON and CSV files at every stage. Any individual pass can be inspected, audited, or rerun independently without re-running upstream stages, because every intermediate artifact is persisted to disk.

The architecture deliberately separates **applicability** (Passes 1–2: which attributes apply?) from **scoring** (Pass 3: how strong is the sentiment?). Pass 3 is never asked to reconsider applicability; it scores only the locked attribute set produced by Phases 1–2.

### 6.1 Stage map

| Stage | Function | Input | Output |
|---|---|---|---|
| Pass 1 | Three-provider attribute identification | Review text + 40-attribute taxonomy | Per-provider list of applicable attributes with supporting quote and brief reason |
| Pass 1 consolidation | Per-review comparison table across the three providers | Three Pass 1 responses | Per-review (attribute × provider) decision matrix |
| Tiered resolution | Deterministic majority-rule resolution of attribute applicability | Decision matrix | Locked-include set, locked-exclude set, deadlock manifest |
| Pass 2 | Three-provider deadlock adjudication on disputed attributes only | Review text + deadlocked attributes + Pass 1 evidence | Binary include/exclude decision per disputed attribute |
| Finalization | Merge Pass 2 results back; produce locked attribute list per review | Tier resolution + Pass 2 outputs | Final attribute matrix (one row per review, one column per attribute, boolean locked) |
| Pass 3 | Three-provider structured factor scoring on locked pairs only | Review text + locked attribute IDs + supporting quotes | Per-provider polarity sign + six factor ratings |
| Pass 3 consolidation | Polarity by majority, factors by arithmetic mean, score by formula | Three Pass 3 responses | Single integer score per locked (review, attribute) pair |
| Final dataset build | Wide-format pivot, long-format pivot, per-group splits, multi-sheet workbook, data dictionary | Pass 3 scored CSV + Phase 2 review metadata | `joes_crab_shack_sentiment_consolidated_wide.csv` and companions |

### 6.2 Why this architecture

The applicability/scoring separation matters because applicability is a **set decision** (is the attribute relevant or not?) while scoring is an **ordinal decision** (given that it's relevant, how strong is the sentiment?). Asking a single prompt to do both creates a well-known failure mode where the model either inflates applicability to justify scoring breadth or suppresses applicability to avoid having to score borderline cases. Splitting the prompts breaks the failure mode and produces cleaner outputs in both layers.

---

## 7. Provider Configuration and Prompt/Schema Package

### 7.1 Provider configuration

| Provider | Model identifier | Role |
|---|---|---|
| OpenAI | `gpt-4.1-mini` | Equal-weight provider in all three passes |
| Anthropic | `claude-sonnet-4` | Equal-weight provider in all three passes |
| Google Gemini | `gemini-2.5-flash` family | Equal-weight provider in all three passes |

### 7.2 Determinism settings

- Temperature: 0 (or the closest provider equivalent for deterministic decoding)
- JSON Schema enforcement: every response is validated against `schemas/passN_output_schema.json` before being accepted
- Malformed-output handling: JSON parse errors trigger up to three content-level retries; schema validation errors are treated as terminal (the provider is marked errored for that review-attribute pair and the pipeline proceeds with the remaining providers)

### 7.3 Retry and resume

| Layer | Behavior |
|---|---|
| Transport retries | Tenacity-based exponential backoff with up to six attempts per call, covering network errors, timeouts, 5xx responses, and 429 rate limits |
| Content retries | Up to three attempts when the response fails JSON parsing; `ValidationError` and `ValueError` outcomes are treated as deterministic and not retried |
| Per-provider resume | Every successful per-provider response is written to a parsed JSON file on disk, keyed by review ID and provider name; on any rerun, existing parsed files are skipped, allowing safe interruption and resumption at any point during a multi-hour run |

### 7.4 Prompt package (frozen for the duration of Phase 4)

The prompt package is treated as a frozen contract. No prompt edits occurred mid-run that would require re-running upstream stages.

**Pass 1 — attribute identification.** Defines the model's task as identifying applicable attributes; supplies the full canonical taxonomy (40 attributes with definitions and anti-bleed clauses); requires output structured as a list with `attribute_id`, `supportive_quote`, and `brief_reason`; strictly forbids attributes outside the canonical set; and strictly forbids fabricated quotes (`supportive_quote` must appear verbatim in the review text).

**Pass 2 — deadlock adjudication.** Reuses Pass 1's attribute definitions and anti-bleed rules but narrows the task: it provides the review text plus only the deadlocked attributes for that review, shows which providers had previously included or excluded each, and asks for a binary `include`/`exclude` decision per disputed attribute. Explicitly forbids introducing new attributes or revisiting non-deadlocked attributes.

**Pass 3 — structured sentiment scoring.** Defines a different task entirely (applicability is already settled): provides the review text, the locked attribute IDs, and the upstream supportive quotes; requires per attribute a `polarity_sign ∈ {−1, 0, +1}` and the six factor values each `∈ {0..10}`; includes anchor descriptions for each factor at the 0, 3, 5, 7, and 10 marks to encourage use of the full range; and forbids re-evaluating applicability, introducing new attributes, returning a direct final score, or returning long prose.

### 7.5 Schema package

Three formal JSON Schema documents (`pass1_output_schema.json`, `pass2_output_schema.json`, `pass3_output_schema.json`) machine-validate every model response before it is accepted. Required fields, allowed enum values, and per-field type constraints are encoded in the schema. Responses that fail schema validation are rejected (after JSON-level retries are exhausted) and the failure is logged for audit.

---

## 8. Pass 1 — Attribute Identification

### 8.1 Purpose

For each of the 16,997 reviews, decide independently across three providers which of the 40 canonical attributes apply, and produce supporting evidence (a verbatim quote and a brief reason) for each.

### 8.2 Processing logic

1. The review text and full canonical taxonomy are presented to each provider with the Pass 1 system prompt.
2. Each provider returns a list of applicable attributes, each with `attribute_id`, `supportive_quote`, and `brief_reason`.
3. The supportive quote is validated programmatically against the review text — quotes that do not appear in the review are rejected as fabrication.
4. Validated provider outputs are stored as parsed JSON on disk for audit and resume.

### 8.3 Validation checks

- JSON Schema validation (required fields, enum values, types)
- Attribute whitelist validation (every returned `attribute_id` must be in `{A01..A40}`)
- Supportive quote presence check against the review text
- Per-review provider-status tracking (`ok`, `error`, `missing`)

### 8.4 Failure handling

A provider that fails Pass 1 for a review (after content retries are exhausted) is recorded as errored for that review. The other providers proceed normally. The downstream consolidation adapts to whichever providers succeeded.

### 8.5 Pass 1 result summary

Pass 1 ran on the JCS corpus (treatment + internal_control, 8,580 reviews) and the competitor corpus (8,417 reviews) as two separate batches, then consolidated. Provider error rates were low and largely transient.

After per-review consolidation, the JCS batch produced **50,417 distinct (review, attribute) candidate decisions** awaiting tiered resolution. The competitor batch produced a similar order of magnitude of candidate decisions; its corresponding total was not separately captured in summary form, so figures that depend on the JCS denominator (notably the deterministic-resolution share in Section 9.3) are reported as JCS-scoped values rather than as corpus-wide percentages.

---

## 9. Tiered Resolution and Pass 2 — Deadlock Adjudication

### 9.1 Purpose

Resolve the candidate (review, attribute) decisions from Pass 1 into a final locked attribute list per review, escalating only those decisions where the three providers disagreed in a structurally ambiguous way.

### 9.2 Tiered resolution logic

Every candidate decision is classified into exactly one of the following tiers based on the pattern of provider votes. The tier boundaries are deterministic; no decision falls between tiers.

| Tier | Condition | Action | API calls |
|---|---|---|---|
| Deterministic include | All providers that succeeded for this review included the attribute (3-of-3 or 2-of-2) | Include | 0 |
| Majority include | 2 of 3 succeeded providers included it | Include | 0 |
| Majority exclude | 1 of 3 succeeded providers included it | Exclude | 0 |
| Deterministic exclude | No succeeded provider included it (and at least one explicitly considered it) | Exclude | 0 |
| Single-provider include | Only one provider succeeded for the review and it included the attribute | Include | 0 |
| **Deadlock (1-of-2)** | Exactly two providers succeeded and they disagreed | **Send to Pass 2** | 3 per review |

### 9.3 Tier distribution observed (JCS batch)

The deterministic-resolution share is reported at the JCS-batch level because that is the level at which the relevant Pass 1 totals were captured in summary form. Across the JCS batch's 50,417 candidate (review, attribute) decisions:

| Tier | Count | Share | API calls used |
|---|---:|---:|---|
| Deterministic include + majority include + majority exclude + deterministic exclude + single-provider include | 45,578 | **90.4%** | 0 |
| **Deadlock (escalated to Pass 2)** | **4,839** | **9.6%** | 3 per review across 1,424 deadlocked reviews |
| **Total (JCS batch)** | **50,417** | **100.0%** | — |

The competitor batch produced **3,085 deadlock decisions across 793 reviews**, a similar order of magnitude. Its corpus-wide deterministic-resolution percentage is not separately published because the corresponding Pass 1 candidate-decision total was not captured in summary form for the competitor batch alone, and reporting a combined percentage by mixing the JCS denominator with the cross-batch numerator would misrepresent the rate.

Combined across both batches, **7,924 deadlock decisions across 2,217 reviews** were routed to Pass 2 LLM adjudication.

The tiered approach reduced Pass 2 API costs by approximately **6×** compared to unconditionally re-adjudicating every candidate decision through Pass 2. The deterministic majority resolution on non-deadlocked rows also preserved auditability: every non-deadlocked decision is reproducible from the Pass 1 outputs without further model calls.

### 9.4 Pass 2 processing

For each review that contained at least one deadlocked attribute, all of that review's deadlocked attributes were batched into a single Pass 2 API call per provider. The user prompt for each call provided:

- The full review text
- The deadlocked attribute IDs and definitions
- Which providers had previously included or excluded each
- The supportive quote and reason from any provider that had included it

Pass 2 returned a binary include/exclude decision per disputed attribute per provider. The three Pass 2 responses were then resolved by majority vote at the attribute level.

### 9.5 Pass 2 outcomes

| Batch | Reviews entering Pass 2 | Deadlock decisions adjudicated | Resolved as include | Resolved as exclude |
|---|---:|---:|---:|---:|
| JCS | 1,424 | 4,839 | 3,518 | 1,321 |
| Competitor | 793 | 3,085 | 2,106 | 979 |
| **Total** | **2,217** | **7,924** | **5,624** | **2,300** |

Only **11 cases corpus-wide** produced an exact three-way vote tie at the Pass 2 attribute level. These were defaulted to exclude under the standing tie-breaking rule.

After Pass 2, every review in the corpus had a complete locked attribute list. No reviews remained in a pending state.

### 9.6 Locked attribute totals after finalization

| Batch | Locked review-attribute rows | Mean per review |
|---|---:|---:|
| JCS | 35,986 | 4.19 |
| Competitor | 37,108 | 4.41 |
| **Combined** | **73,094** | **~4.37** (across the 16,741 scored reviews) |

The slightly higher mean attributes per review in the competitor batch reflects that competitor reviews tend to be longer and more detail-rich than the average Joe's Crab Shack review. **256 reviews** had no attributes locked because no provider identified scoreable content in them; these are recorded as zero-attribute rows and their score columns are blank in the final dataset.

---

## 10. Pass 3 — Structured Sentiment Scoring

### 10.1 Purpose

For every one of the 73,094 locked review-attribute pairs, produce one consolidated integer sentiment score in `[−100, +100]` derived from three-provider structured factor ratings.

### 10.2 Processing logic

1. For each review, all of its locked attributes are batched into a single Pass 3 API call per provider.
2. Each provider returns, per attribute, a `polarity_sign` and the six factor ratings (directness, intensity, specificity, certainty, centrality, consequence), each on a 0–10 integer scale.
3. The three providers' responses are validated against `pass3_output_schema.json` and stored as parsed JSON.
4. The pipeline computes the consolidated final score deterministically per the Section 5.3 formula:
   - Polarity by majority vote (three-way ties default to `0`)
   - Each factor by arithmetic mean across the three providers
   - Magnitude `M = (2·D̄ + 2·Ī + S̄ + C̄ + C̄ₑ + C̄ₒ) / 80`
   - Final score = `sign × round(100 · M)`

### 10.3 Validation and lenient recovery

In addition to standard JSON Schema and content validation, Pass 3 includes a lenient recovery step for schema-incomplete responses. When a provider returns a structurally valid response but with one or more individual factor fields omitted from a single attribute entry — most commonly missing `centrality` deep in a multi-attribute response — the recovery script preserves the partial response and fills the missing field with the neutral midpoint default (`5` on the 0–10 scale for factors; `0` for polarity sign) rather than discarding the entire response.

This rescued **347 of 348** errored provider calls across the corpus, with approximately **460 individual factor values** defaulted (~0.15% of total factor values). Every defaulted field is recorded in a `_lenient_recovery` block in the affected per-provider parsed JSON, naming the specific attribute and field that was filled with a default. The defaulting rate is small enough to be absorbed by three-provider consolidation without measurably affecting per-attribute final scores.

### 10.4 Pass 3 provider coverage

| Coverage tier | Locked attribute rows | Share |
|---|---:|---:|
| All three providers contributed | 73,008 | 99.89% |
| Two providers contributed | 81 | 0.11% |
| One or zero providers contributed | 5 | <0.01% |

Only **a single attribute-row case** across the entire corpus had unrecoverable provider failures on a specific review-provider combination; the consolidation math handled it gracefully via the remaining two providers.

### 10.5 Pass 3 output

`outputs/pass3/<run_id>/parsed/` contains per-review summaries with the consolidated `final_scores` block including the final score, the polarity sign, the magnitude `M`, the six factor means, and the per-provider factor breakdown. A long-format scored CSV at `outputs/pass3/<run_id>/pass3_scored_attributes.csv` provides one row per scored (review, attribute) pair with full provider traceability.

---

## 11. The Final Master Scored Dataset

### 11.1 The artifact

The Phase 4 deliverable is `joes_crab_shack_sentiment_consolidated_wide.csv`. It is a single flat CSV file with **16,997 rows × 63 columns**. Each row represents exactly one Yelp review tied to exactly one Phase 1 manifest location and carries one integer sentiment score per locked canonical attribute.

### 11.2 The schema (63 columns)

The 63 columns are organized into five functional bands:

| Band | Columns | Source |
|---|---|---|
| **Identifiers (2)** | `model_review_id`, `source_review_id` | Phase 2 |
| **Location metadata (4)** | `location_id`, `city`, `state`, `matched_pilot_id` | Manifest (Phase 1 → Phase 2 → Phase 4) |
| **Group assignment (3)** | `group_type` ∈ {`treatment`, `internal_control`, `external_competitor`}; `analysis_role` ∈ {`primary_internal`, `tier2_official`}; `chain_type` ∈ {`JCS`, `non_JCS`} | Manifest |
| **Pilot date windows (6)** | `pre_start`, `pre_end`, `post_start`, `post_end`, `pilot_window_start`, `pilot_window_end` | Manifest |
| **Review metadata (5)** | `reviewer_name`, `is_elite`, `rating`, `review_date`, `review_date_parsed` | Phase 2 |
| **Derived (3)** | `pilot_phase` ∈ {`pre`, `post`, `outside`, `unknown`}; `attributes_assigned_count`; `mean_score_across_assigned` | Phase 4 |
| **Attribute scores (40)** | `score_A01` through `score_A40`, each an integer in `[−100, +100]` when the attribute was locked, **blank** when the attribute was not locked | Phase 4 |

The `model_review_id` is the primary stable identifier (unique sequential `R000001`–`R017417`). The `source_review_id` is the secondary natural key (the composite `review_key` from Phase 2).

### 11.3 The blank-vs-zero rule (CRITICAL)

> **A blank score column means the attribute was not locked for that review (the reviewer did not mention it). A score of `0` means the attribute was locked but the consolidated sentiment was genuinely neutral. Blank must NOT be treated as zero in downstream analysis; doing so biases every per-attribute mean.**

This is the single largest analytical pitfall in the consolidated dataset. It is called out in Section 11.3, in the data dictionary (`DATA_DICTIONARY.md`), in the multi-sheet Excel summary, and again in the Phase 5 handoff (Section 14.3).

### 11.4 Coverage by group

All 61 manifest locations and all 16,997 reviews from Phase 2 are represented in the dataset. Group-level coverage:

| Group | Reviews | Reviews with at least one locked attribute |
|---|---:|---:|
| Treatment (pilot stores) | 1,577 | ~1,537 |
| Internal control (other JCS) | 7,003 | ~6,876 |
| External competitor (other casual-dining seafood) | 8,417 | ~8,328 |
| **Total** | **16,997** | **16,741** |

The 256 reviews without locked attributes are recorded honestly as zero-attribute rows.

### 11.5 Pilot-phase distribution

| Phase | Reviews |
|---|---:|
| Pre-intervention window | 222 |
| Post-intervention (no-tipping period) window | 655 |
| Outside the defined windows | 16,120 |
| **Total** | **16,997** |

The phase distribution is heavily skewed toward `outside` because the no-tipping pilot ran for only ~6 months while the master corpus spans many years of Yelp activity at each location. Reviews falling outside the pre/post windows are useful as reference distributions for the overall sentiment baseline but are not directly comparable on the difference-in-differences frame.

### 11.6 Companion outputs

| File | Grain | Purpose |
|---|---|---|
| `joes_crab_shack_sentiment_treatment_wide.csv` | One row per treatment review (1,577) | Group-specific subset, identical schema |
| `joes_crab_shack_sentiment_internal_control_wide.csv` | One row per internal_control review (7,003) | Group-specific subset, identical schema |
| `joes_crab_shack_sentiment_external_competitor_wide.csv` | One row per external_competitor review (8,417) | Group-specific subset, identical schema |
| `final_dataset_long.csv` | One row per locked (review, attribute) pair (73,094 rows) | For models that treat each attribute observation as a unit |
| `joes_crab_shack_sentiment_summary.xlsx` | Eight-sheet workbook | Overview, by-attribute breakdown, A38 Tipping detail, group×phase, per-location, per-city, top drivers, coverage audit |
| `final_dataset_summary.json` | Aggregate statistics | Group means, per-attribute distributions, A38 Tipping breakdown |
| `final_dataset_data_dictionary.md` / `DATA_DICTIONARY.md` | Markdown | Column definitions, methodology summary, suggested analyses |

---

## 12. Operational Readiness of the Scored Dataset

The dataset is operationally ready in the strict execution sense — meaning Phase 5 can read it deterministically and start statistical work without further extraction, scoring, or cohort-resolution work. This readiness rests on five certainties, all of which were verified against the file.

### 12.1 Coverage certainty

Every Phase 2 review is present in the dataset.

- 1,577 / 1,577 treatment reviews represented ✓
- 7,003 / 7,003 internal control reviews represented ✓
- 8,417 / 8,417 external competitor reviews represented ✓
- 16,997 / 16,997 total reviews represented ✓
- 61 / 61 manifest locations represented ✓

There is no silent review or location dropout between Phase 2 and Phase 4.

### 12.2 Identity certainty

Every row carries its full analytical identity inherited transitively from Phase 2 and Phase 1: `location_id`, `group_type`, `analysis_role`, `matched_pilot_id`, `chain_type`, and the six pilot-window fields. Phase 5 can group, filter, and label reviews without joining back to the manifest or the master review CSV.

### 12.3 Score completeness certainty

Every one of the 16,741 reviews that has at least one locked attribute has a complete final score for every locked attribute. There are **zero locked review-attribute pairs without a final score** — the lenient-recovery step ensures no scoring gap propagates into the output.

### 12.4 Provider-traceability certainty

Every score in the dataset traces back through three layers of audit: (a) the consolidated `final_scores` block in the per-review parsed JSON; (b) the per-provider parsed JSON that contributed to it (with the polarity sign and six factors per provider); (c) the raw provider response on disk that was schema-validated. This means any downstream challenge to a specific score can be resolved by walking the audit trail.

### 12.5 Schema-stability certainty

The 63-column wide schema and the 40-attribute score column set are fixed for the duration of the project. Phase 5 can hard-code column names without expecting them to drift.

### 12.6 Operational-readiness summary

| Certainty | Verification | Status |
|---|---|---|
| Coverage | All 16,997 Phase 2 reviews and all 61 manifest locations present | ✓ |
| Identity | All Phase 1 / Phase 2 inherited identity fields populated on every row | ✓ |
| Score completeness | 73,094 / 73,094 locked pairs scored; 0 scoring gaps | ✓ |
| Provider traceability | Every score traceable to three providers via per-review and per-provider JSON | ✓ |
| Schema stability | 63-column wide schema fixed; 40-attribute score column set fixed | ✓ |

---

## 13. Honest Limitations of the Phase 4 Deliverable

Five honest limitations are documented here. None of them blocks Phase 5, but all five should be acknowledged when describing the dataset to stakeholders or the practicum sponsor.

### 13.1 The dataset measures review *language*, not customer experience

A score of `−80` on `A38` Tipping reflects the language used in a Yelp review — not a calibrated psychological measurement of the reviewer's actual feelings about the no-tipping pilot. This is a fundamental limitation of any text-derived sentiment score and applies to every attribute in the dataset.

### 13.2 LLM judgments are not equivalent to ground truth

The pipeline embeds structured constraints (canonical taxonomy, anti-bleed rules, JSON Schema validation, three-provider consensus, deterministic resolution math) to make the outputs defensible and auditable. They remain **model-derived signals**, not human-coded ground truth. Validation against the human-coded benchmark from the parallel methodology assets (the 100-review / 646-instance benchmark on `Sample.csv`) is a Phase 5 responsibility — and it carries the domain-transfer assumption documented in the Phase 2 handoff (the benchmark was scored on a generic restaurant-review sample, not on the JCS / casual-dining seafood corpus this dataset is built on).

### 13.3 The scoring formula's factor weights are a design choice, not learned

The `2·` weights on directness and intensity are a defensible design judgment, not a parameter learned from data. Under a different weighting scheme the integer scores would shift slightly, though the *ordering* of attributes by score within a review tends to be stable. Phase 5 should report scores under the locked formula but may want to report sensitivity to the weighting choice in any final analysis.

### 13.4 Sparse attributes carry less reliable per-group means

Attributes locked on only a small number of reviews carry less stable per-group means simply because the sample is small. **`A38` Tipping itself, with 364 mentions corpus-wide (98 treatment + 138 internal control + 128 external competitor), is sparse** relative to the 16,997-review corpus. This sparsity is an intrinsic feature of how often Yelp reviewers actually mention tipping, not a pipeline failure, but it must be reflected in any uncertainty quantification Phase 5 produces.

### 13.5 The 256 zero-attribute reviews could be a small bias source

The 256 reviews with no locked attributes are recorded honestly as having no scoreable content, but their omission from per-attribute means could be a small bias source if those reviews systematically differ from the rest. Phase 5 should report a sensitivity check on any headline result that excludes them.

### 13.6 What these limitations do *not* affect

These five limitations are real, but they are bounded. They do not affect:

- Coverage across all 16,997 reviews and all 61 manifest locations
- Phase 1 / Phase 2 analytical-identity inheritance on every row
- Score completeness on every locked review-attribute pair
- The reachability of every integer in `[−100, +100]` under the consolidation formula
- The provider audit trail on every score

The core analytical substrate — score, attribute, location, group, time window, audit trail — is intact.

---

## 14. Why the 16,997-Row × 63-Column Scored Dataset Is the Correct Phase 4 Endpoint

Phase 4's success criterion was never maximal LLM coverage, perfect inter-provider agreement, or complete absence of edge cases in the corpus. The success criterion was a single review-level scored dataset that:

1. covers every Phase 2 review,
2. carries Phase 1 / Phase 2 analytical identity by inheritance,
3. uses only the locked Phase 3 canonical taxonomy,
4. produces a final integer score in `[−100, +100]` for every locked review-attribute pair,
5. preserves a complete provider-level audit trail, and
6. can be consumed by Phase 5 as one source of truth.

`joes_crab_shack_sentiment_consolidated_wide.csv` satisfies all six criteria. The five limitations in Section 13 are real but do not compromise the dataset's analytical core, and they are documented honestly rather than hidden. Phase 4 therefore concluded by preserving the work that holds analytical weight rather than by chasing edge-case completeness in the LLM layer.

---

## 15. Methodological Position at the End of Phase 4

At this point, Phase 4 has resolved the project's scoring obligation in a sufficiently disciplined way that the project is no longer engaged in NLP work. The phase has completed:

- Three-provider attribute identification under the locked canonical taxonomy
- Deterministic tiered consolidation of three-provider applicability decisions
- Selective deadlock adjudication via Pass 2
- Three-provider structured factor scoring on every locked pair
- Deterministic score consolidation under a fixed weighted-mean formula
- Full audit-trail persistence across raw, parsed, consolidated, and recovered responses
- Final wide-format and long-format dataset construction with companion outputs

The project has therefore moved past the question *"which attributes apply to each review and how strong is the sentiment?"* and into the question *"what do the scored attributes reveal about the no-tipping pilot's effect on customer-perceived service experience?"* That second question is the entire subject of Phase 5.

---

## 16. Final Phase 4 Deliverable

The final deliverable of Phase 4 is a single locked, execution-ready, review-level scored attribute dataset consisting of:

- 16,997 rows × 63 columns
- Full coverage across all Phase 2 reviews and all 61 Phase 1 manifest locations
- Full inheritance of Phase 1 / Phase 2 analytical identity
- 73,094 locked review-attribute pairs with complete integer scores in `[−100, +100]`
- Three-provider-traced audit trail across raw, parsed, consolidated, and recovered responses
- Locked 40-attribute output schema (`score_A01` through `score_A40`) with the `A38` = Tipping primary outcome
- Three group-specific splits, a long-format companion, an eight-sheet Excel summary, and a developer-facing data dictionary
- Five honestly documented limitations

That deliverable is `joes_crab_shack_sentiment_consolidated_wide.csv`.

---

## 17. State of the Project at the End of Phase 4

The project is now in the position it needed to reach before Phase 5 could begin. Phase 4 is effectively done and concluded successfully because:

- the canonical taxonomy is locked
- attribute applicability is locked
- sentiment scoring is locked
- every Phase 2 review is represented
- analytical identity is carried by inheritance, not by re-derivation
- score completeness on every locked pair is verified
- the provider audit trail is intact
- all five honest limitations are documented
- a single definitive file is available for downstream consumption

The project is now ready to move into Phase 5 as a statistical-modeling and final-reporting problem, not a scoring problem.

---

## 18. Handoff to Phase 5

This section is intentionally brief. A full Phase 5 narrative will appear in the next companion document. The purpose of this section is only to mark the contract between the two phases.

### 18.1 What Phase 5 inherits from Phase 4

- `joes_crab_shack_sentiment_consolidated_wide.csv` (16,997 rows × 63 columns) as its single execution input.
- Three group-specific splits: `joes_crab_shack_sentiment_treatment_wide.csv` (1,577), `joes_crab_shack_sentiment_internal_control_wide.csv` (7,003), `joes_crab_shack_sentiment_external_competitor_wide.csv` (8,417).
- Long-format companion `final_dataset_long.csv` (73,094 rows, one per locked review-attribute pair).
- Multi-sheet Excel summary, aggregate JSON statistics, and developer-facing data dictionary.
- The locked group / role / window / attribute structure inherited transitively from Phases 1, 2, and 3.
- The locked scoring formula and the `[−100, +100]` integer codomain.
- Stable join keys: `model_review_id` (primary), `source_review_id` (secondary), `location_id` (location-level rollups), `group_type` (primary stratification), `pilot_phase` (pre/post stratification on JCS rows), `matched_pilot_id` (control-to-pilot matching).

### 18.2 Parallel methodology assets Phase 5 will use (NOT produced by Phase 4)

Phase 5 will additionally draw on three parallel methodology assets that were developed outside the Phase 4 scoring pipeline:

| Asset | Provenance | Role in Phase 5 |
|---|---|---|
| `restaurant_review_attribute_lexicon.csv` | Team-authored 97-attribute framework, March 10, 2026 update | Source of the conceptual definitions and common-marker lexicon that flowed forward (via Phase 3 reduction) into the 40 canonical attributes used in Phase 4 |
| `Attribute_Canonical_Definitions.md` | Phase 3 deliverable (96 → 40 reduction) | The locked taxonomy used by Phase 4; remains the authoritative attribute reference for any Phase 5 attribute interpretation |
| `manual_attribute_scores.csv` | Team-produced human-coded benchmark, March 18, 2026 update (100 reviews × 646 attribute instances on `Sample.csv` via 5-stratum length-based stratified sampling, three-phase scoring workflow) | Optional benchmark for cross-validating Phase 4 LLM scores against human scoring — subject to the domain-transfer assumption flagged in Section 13.2 |

### 18.3 What Phase 5 is responsible for

- Statistical modeling: difference-in-differences estimates for the `A38` Tipping primary outcome and for any secondary attribute outcomes the project elects to report.
- Comparison-group contrasts: treatment vs. internal_control as the primary contrast, with external_competitor as an industry baseline rather than as a third arm of the DiD.
- Clustering and dependence: appropriate handling of clustered observations (multiple reviews per restaurant, multiple reviews per reviewer, multiple reviews per time period) via clustered standard errors or mixed-effects models as appropriate.
- Sensitivity analyses: impact of the five honest limitations in Section 13, particularly the 256 zero-attribute reviews and the scoring formula's factor weights.
- **Explicit acknowledgment of the benchmark domain-transfer assumption** when reporting any benchmark-validation results — the human-coded benchmark was scored on `Sample.csv`, not on the Phase 2 Yelp corpus.
- Visualization, executive-ready reporting, the final practicum report (15–25 pages), the presentation slides, and the formal oral presentation.

### 18.4 What Phase 5 is *not* permitted to do

- **Re-open the cohort.** Phase 5 may not add, remove, or reclassify locations; any such pressure must be raised back to a documented Phase 1 amendment.
- **Re-extract reviews.** Phase 5 reads from the master scored dataset; it does not run its own scrape.
- **Re-run attribute identification or Pass 3 scoring.** The outputs are locked. Re-running would produce slightly different results due to LLM stochasticity even at temperature 0 and would invalidate the audit trail. Any methodological challenge to a specific score should be addressed by walking the audit trail in `outputs/`, not by re-scoring.
- **Reinterpret the canonical attributes.** The 40-attribute taxonomy and its anti-bleed rules are part of the locked rule package. Any reinterpretation belongs in a separate methodological note, not in Phase 5 modeling code.
- **Treat blanks as zeros.** This is the single largest analytical pitfall and is restated here at the contract level: in `score_A01` … `score_A40`, blank means "attribute not locked for this review" and `0` means "attribute locked, sentiment genuinely neutral." Treating blank as zero biases every per-attribute mean.
- **Use `is_elite`, the four reaction-count columns, or `reviewer_seafood_review_count` as features** without first executing a documented re-extraction pass against the same source URLs (Phase 2 Section 9 limitation, transitive into Phase 4).

---

## 19. Document Control

| Field | Value |
|---|---|
| Document | Phase 4 Completion Report |
| Phase status | Closed |
| Primary input | `consolidated_cleaned_reviews.csv` (16,997 rows × 42 columns, locked Phase 2 deliverable) |
| Primary methodology asset | `Attribute_Canonical_Definitions.md` (40-attribute canonical taxonomy, locked Phase 3 deliverable) |
| Primary artifact | `joes_crab_shack_sentiment_consolidated_wide.csv` |
| Total review rows (locked) | 16,997 |
| Total schema columns (locked) | 63 (23 metadata + 40 attribute-score columns) |
| Total locked review-attribute pairs (locked) | 73,094 |
| Treatment / Tier 1 / Tier 2 review-row split (locked) | 1,577 / 7,003 / 8,417 |
| Manifest-location coverage (locked) | 61 / 61 |
| Pass 1 candidate decisions | 50,417 |
| Tier-resolution share (JCS batch) | 90.4% deterministic / 9.6% escalated to Pass 2 (4,839 of 50,417 candidate decisions) |
| Pass 2 escalation (corpus-wide) | 7,924 deadlock decisions across 2,217 reviews (4,839 JCS + 3,085 competitor) |
| Pass 3 three-provider coverage | 99.89% |
| Lenient-recovery rate | ~0.15% of Pass 3 factor values |
| Score range | Integer in `[−100, +100]` (every integer reachable, verified by enumeration) |
| `A38` Tipping primary outcome — corpus mentions | 364 (98 treatment + 138 internal control + 128 external competitor) |
| Documented limitations | Review language ≠ customer experience; LLM ≠ ground truth; factor weights are a design choice; sparse attributes (notably `A38`) carry less stable means; 256 zero-attribute reviews are a small bias source |
| Parallel methodology assets (NOT Phase 4 products) | `restaurant_review_attribute_lexicon.csv` (97-attribute framework, March 10); `manual_attribute_scores.csv` (100 reviews × 646 instances on `Sample.csv`, March 18); `Attribute_Canonical_Definitions.md` (Phase 3 deliverable) |
| Critical methodological assumption inherited from Phase 2 | Benchmark-to-master-dataset domain transfer (`Sample.csv` generic-restaurant corpus → Phase 2 Yelp JCS / casual-dining corpus) |
| Companion documents | Phase 1 Completion Report (preceding); Phase 2 Completion Report (preceding); Phase 3 Completion Report (preceding); Phase 5 documentation (forthcoming) |

---

*End of Phase 4 Completion Report.*
