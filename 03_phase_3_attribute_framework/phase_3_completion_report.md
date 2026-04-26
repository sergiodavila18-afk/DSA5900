# Phase 3 Completion Report
## Taxonomy Engineering, Human Benchmark Construction, and Canonical Attribute Reduction

**Course:** DSA 5900 — Professional Practice (Spring 2026)  
**Project:** Quantitative Evaluation of the Joe's Crab Shack No-Tipping Policy Intervention  
**Phase:** 3 of N — Taxonomy Engineering, Human Benchmark Construction, and Canonical Attribute Reduction  
**Status:** Complete and locked  
**Primary methodology inputs:** `sample.csv` (500-row sponsor benchmark source dataset); exploratory signal corpus (team-generated lexical signal table); literature, sponsor guidance, and project framing  
**Primary deliverables:** `restaurant_review_attribute_lexicon.csv` (97-attribute framework); `manual_attribute_scores.csv` (100 reviews × 646 scored attribute instances); `Attribute_Canonical_Definitions.md` (40-attribute canonical taxonomy)  
**Companion documents:** Phase 1 Completion Report (Experimental Design and Comparison Frame); Phase 2 Completion Report (Extraction Pipeline and Master Dataset Construction); Phase 4 Completion Report (Multi-Model Attribute Detection and Sentiment Scoring Pipeline); Mid-Semester Progress Report; Manual Review Scoring Process report

---

## 1. Executive Summary

Phase 3 of the practicum has concluded successfully. Its purpose was to build the project's measurement system before project-scale automated scoring began. That meant converting a broad sponsor question about tipping, service quality, fairness, and restaurant experience into a disciplined attribute framework, constructing a human-coded benchmark suitable for later evaluation, and consolidating that larger framework into a smaller canonical layer that could support full-corpus execution in Phase 4.

That objective has been met. Phase 3 is no longer a sponsor-guided exploratory NLP exercise, a loose ontology brainstorm, or a partially labeled benchmark spreadsheet. It is a closed and reconciled stage whose finished product is a locked methodology-asset bundle rather than one row-level dataset. That bundle consists of three core artifacts:

| Asset | Role | Final status |
|---|---|---|
| `restaurant_review_attribute_lexicon.csv` | Structured 97-attribute framework across nine categories | Locked as the legacy framework |
| `manual_attribute_scores.csv` | Human-coded benchmark built from a stratified 100-review subset of `sample.csv` | Locked as the benchmark asset |
| `Attribute_Canonical_Definitions.md` | 40-attribute canonical layer with definitions, merge logic, and developer-facing implementation guidance | Locked as the Phase 3 canonical methodology asset |

Phase 3 is therefore the point in the project where the question changed from **"what should this project measure, and how should those measurements be standardized?"** to **"how can those locked standards now be applied at full scale?"** That second question belongs to Phase 4.

The most important methodological fact about Phase 3 is that it did **not** originate from the later Phase 2 Yelp master dataset. Instead, it drew on three parallel input streams:

1. literature, sponsor discussion, and business-problem framing;
2. an exploratory signal corpus used as an early lexical seed layer; and
3. Dr. Kumar's sponsor-provided `sample.csv` dataset, which served as the benchmark source file for the human-coded standard-setting work.

The later Phase 2 master dataset became relevant only as a downstream application target once Phase 3 had already produced the framework, benchmark, and canonical rule assets needed for large-scale scoring.

---

## 2. Phase 3 Mission and Scope

### 2.1 Mission statement

> Build the project's formal measurement system by translating broad restaurant-review language into a locked attribute framework, constructing a reproducible human-coded benchmark for standard-setting, compressing the larger framework into a more stable canonical layer, and packaging the resulting assets so they can be applied at full scale in later automated execution.

### 2.2 In-scope work for Phase 3

Phase 3 was responsible for, and only for, the following classes of work:

1. **Conceptual translation.** Converting sponsor questions and literature-grounded themes into measurable restaurant-review constructs.
2. **Exploratory lexical scaffolding.** Building and using an early signal corpus to bridge abstract concerns such as tipping, fairness, service, price, food quality, and ambiance into observable review-language markers.
3. **Framework development.** Constructing the larger 97-attribute restaurant-review framework and organizing it into coherent categories with locked conceptual definitions and marker logic.
4. **Benchmark construction.** Building a 100-review benchmark subset from `sample.csv` using a disciplined, reproducible stratified sampling design.
5. **Human coding and scoring.** Manually assigning categories, attributes, polarity, broad sentiment bands, and final exact-integer scores to the benchmark review-attribute instances.
6. **Framework refinement.** Using the benchmark construction and review process to sharpen category boundaries, attribute definitions, and conceptual distinctions.
7. **Reduction and consolidation.** Applying a formal KEEP / MERGE / DROP reduction pass to produce a smaller 40-attribute canonical layer from the larger legacy framework.
8. **Methodology packaging.** Producing the rule assets, definitions, canonical mapping logic, and benchmark artifacts needed for later automated full-corpus scoring.

### 2.3 Out-of-scope for Phase 3

Phase 3 explicitly did **not** perform any of the following:

- cohort derivation or location selection work from Phase 1;
- project-scale Yelp review extraction or cleaning from Phase 2;
- full-corpus automated scoring across all 16,997 extracted Yelp reviews from Phase 4;
- downstream statistical modeling, causal estimation, or sponsor-facing business inference from Phase 5.

This boundary matters. Phase 3's job was to define and lock the measurement system. It was not to execute that measurement system at full scale.

---

## 3. Design Principles

Six design principles governed every major decision in Phase 3.

### 3.1 Standard-setting before automation

The project did not begin with a gold-standard labeled dataset or a ready-made target variable. Phase 3 therefore treated human standard-setting as a prerequisite for later automation rather than as an optional afterthought.

### 3.2 Multiple input streams are real and must remain distinct

Phase 3 did not inherit cleanly from a single upstream artifact. It combined conceptual framing, an exploratory signal corpus, and a benchmark source dataset. Keeping those streams distinct is necessary for accurate traceability.

### 3.3 Move from raw language to structured measurement

The central task of Phase 3 was not generic sentiment analysis. It was the construction of a structured aspect-level measurement system capable of preserving which part of the restaurant experience a review discussed and how strongly that discussion was valenced.

### 3.4 Benchmark construction must be disciplined, not convenient

The benchmark subset was not selected as an arbitrary 100-row convenience sample. It was designed as a balanced, length-stratified subset to avoid obvious text-length skew and to make the resulting benchmark more useful for later evaluation.

### 3.5 Consolidation is a technical step, not a cosmetic cleanup

The move from the larger legacy framework to the canonical attribute layer was not treated as mere simplification. It was treated as feature pruning and construct consolidation designed to improve downstream interpretability, reduce overlap, and control sparsity.

### 3.6 The completion report describes the final successful phase, not the exploratory residue

This report documents the final as-built Phase 3 system: the input streams that mattered, the benchmark procedure that actually succeeded, the framework that actually governed decisions, and the canonical outputs that later phases inherited. It does not attempt to preserve every exploratory branch that did not survive into the final Phase 3 asset bundle.

---

## 4. Inputs and Parallel Input Streams

Phase 3 had no single upstream data source. It should instead be understood as a methodology phase built from parallel inputs that later converged into one locked asset bundle.

### 4.1 Conceptual framing stream

The first input stream consisted of sponsor discussion, project framing, and relevant background reasoning about restaurant-review language, tipping, service quality, fairness, and customer experience. This stream supplied the business logic that made the attribute system necessary in the first place.

Without this stream, the project would have lacked a defensible answer to the question of what should count as a meaningful unit of restaurant-review experience. This framing pressure is what pushed the project away from one-dimensional global sentiment and toward structured attribute-level analysis.

### 4.2 Exploratory signal corpus

The second input stream was an early **exploratory signal corpus**: an LLM-generated lexical signal table rather than a review dataset and not the final ontology. Its role was upstream and formative.

Functionally, the corpus served as a seed lexicon or early NLP dictionary. It organized tipping-relevant and restaurant-experience language into domains such as service, food, atmosphere, cleanliness, pricing, operations, contextual tipping language, and explicit tipping language. It helped bridge the sponsor's broad research question into concrete textual signals that could plausibly be detected in reviews.

The corpus belongs in the project history for three reasons:

1. it was the first practical bridge from literature and sponsor framing to observable review-language indicators;
2. it seeded the team's thinking about how review text could be decomposed into structured, measurable components; and
3. it is retrospectively mappable into both the larger 97-attribute framework and the later 40-attribute canonical layer.

At the same time, this corpus must be described carefully. It was **not** the benchmark dataset, **not** the extracted Yelp corpus, and **not** the final canonical taxonomy. It was an exploratory lexical scaffold that helped the project move into a formal measurement framework.

### 4.3 Benchmark source dataset

The third input stream was `sample.csv`, a sponsor-provided 500-review restaurant-review dataset used as the benchmark source file for human standard-setting. This is the dataset from which the 100-review benchmark subset was drawn.

It is important to name this stream correctly. `sample.csv` is a **benchmark source dataset**, not the exploratory signal corpus and not the later Phase 2 project-scale Yelp corpus. The benchmark and its human-coded scores therefore originated from a parallel methodology track rather than from the later extraction pipeline.

### 4.4 Later downstream alignment to Phase 2

Although Phase 3 did not originate from the later Phase 2 master analytical dataset, Phase 3's outputs were ultimately packaged so they could be applied to it. That downstream alignment matters because the canonical taxonomy, benchmark semantics, and scoring rules were built specifically to be inherited by later large-scale execution.

In other words, Phase 3 came first methodologically even though the final project pipeline places Phase 2 before Phase 3 in the logical data-flow sequence.

---

## 5. The 97-Attribute Framework Development

### 5.1 Why a formal framework was necessary

The project could not answer the sponsor's question with one global sentiment label per review. Restaurant reviews are multi-dimensional. A single review may discuss food quality, attentiveness, fairness, service speed, ambiance, crowding, wait time, gratuity policy, and willingness to return all at once. That meant the project required a formal attribute framework before any meaningful scoring or later causal comparison could occur.

Phase 3 therefore treated framework construction as a first-class deliverable rather than as a side note to model prompting.

### 5.2 The nine-category legacy structure

The larger legacy framework organized the restaurant-review domain into **97 attributes across nine categories**.

| Category | Role in the framework |
|---|---|
| Brand and Digital Experience | Brand expectations, digital-touchpoint and reputation-linked constructs |
| Logistics and Arrival | Arrival, waiting, physical access, and pre-service logistics |
| Environment and Atmosphere | Physical environment, ambiance, noise, crowding, cleanliness, and seating experience |
| Food and Beverage | Dish-level and ingredient-level food and drink experience |
| Service and Staff | Interaction quality, attentiveness, competence, care, and recovery |
| Value and Checkout | Pricing, fee, value, payment, and fairness signals |
| Delivery and Takeout | Off-premise channel constructs |
| Audience and Occasion | Suitability for family, groups, celebrations, and context-specific usage |
| Overall Satisfaction | Cross-cutting evaluative and summary constructs |

This larger framework was the project's first mature measurement system. It was not a casual brainstorm. It supplied the structured target space within which later human scoring, reduction, and automation decisions could occur.

### 5.3 Locked conceptual definitions and markers

Each legacy attribute was paired with a conceptual definition and common markers. The point of this structure was to prevent the framework from degenerating into an uncontrolled label list.

The attribute layer therefore encoded three kinds of information:

1. **conceptual meaning** — what the attribute was supposed to represent;
2. **marker guidance** — examples of review-language cues that could support the attribute; and
3. **sentiment semantics** — how later scoring would preserve both polarity and strength.

This combination is what made the framework usable for both human coding and later prompt-constrained model execution.

### 5.4 Why the legacy framework remained important after consolidation

Even though the project later reduced the legacy framework to a smaller canonical layer, the 97-attribute system remained important. It was the conceptual superset from which the canonical layer was derived. It also preserved the fuller map of the restaurant-review domain that guided both benchmark annotation and later merge logic.

For that reason, the legacy framework remains part of the Phase 3 asset bundle rather than being erased by the later reduction.

---

## 6. Benchmark Construction from `sample.csv`

### 6.1 Purpose of the benchmark

The benchmark existed because the project had no pre-existing gold-standard labeled dataset for this exact task. The benchmark therefore became a necessary methodological deliverable: a human-coded reference set against which later AI outputs could be evaluated and a practical tool for testing whether the framework itself was coherent enough to support consistent annotation.

### 6.2 Source dataset and exclusions

The benchmark source file was `sample.csv`, containing **500 restaurant reviews**. Before selection, reviews under **15 words** and over **400 words** were excluded as outliers to keep the benchmark evaluative, manageable, and less vulnerable to extreme-text artifacts.

### 6.3 Stratified sampling design

A simple random pull was not used. Instead, the team constructed the benchmark through a **five-stratum length-based stratified random sample**.

The final design was:

- compute word count for every review in `sample.csv`;
- exclude reviews under 15 words and over 400 words;
- divide the remaining reviews into five length-based strata using 100-word intervals;
- randomize within each stratum, using original file index as a deterministic tie-breaker for stable reproducibility;
- select **20 reviews from each stratum**;
- assemble the final **100-review benchmark subset**.

This design preserved representation across different review lengths and reduced the risk that the benchmark would be dominated by very short or medium-length reviews.

### 6.4 Review identifiers

Each selected review was assigned a standardized benchmark identifier such as `REV-001`, `REV-002`, and so on, based on its source-file index. This identifier scheme mattered because later annotation, reconciliation, and score auditing all required stable row identity.

### 6.5 What the benchmark does and does not represent

The benchmark represents a carefully selected, human-coded restaurant-review subset designed for standard-setting and later evaluation. It does **not** represent the full extracted Yelp corpus, and it was not drawn from Joe's Crab Shack review extraction. That distinction must remain explicit whenever the benchmark is used downstream.

---

## 7. Human Coding and Scoring Protocol

### 7.1 Why the annotation workflow was staged

A 97-attribute ontology is too large to annotate cleanly by jumping directly into exact score assignment. Phase 3 therefore used a staged human annotation workflow that progressively narrowed the decision space:

1. broad category assignment;
2. attribute assignment within the applicable categories;
3. polarity direction;
4. broad sentiment band;
5. final exact-integer score.

This staging made the benchmark more manageable and also helped the team refine the ontology itself as disagreements and ambiguities became visible.

### 7.2 Category assignment

The first manual pass operated at the category level. For each of the 100 reviews, the team determined which of the nine broad categories applied.

This step served two purposes. First, it reduced cognitive load by narrowing the candidate attribute space for later annotation. Second, it acted as an early validation layer for the framework itself.

After the manual pass, three AI models were also used to perform the same broad categorization task. These model outputs were not treated as decision-makers. They were used as comparison signals to surface possible false positives, false negatives, and definitional ambiguity. Where disagreements revealed boundary weakness, the category and attribute definitions were clarified. In this way, the category pass functioned both as annotation and as framework refinement.

### 7.3 Attribute assignment

After broad-category locking, the annotation moved to specific attribute assignment. Candidate attributes for each review were limited to those inside the categories already judged applicable, which reduced irrelevant attribute checks and made the process more disciplined.

As in the category stage, three AI models were used as comparison checks after the manual pass. Their outputs were combined into a comparison sheet and used to help identify attributes that needed second-look review. Final attribute decisions remained team-owned human decisions.

### 7.4 Score assignment architecture

Once applicable attributes were locked for a review, scoring proceeded at the **(review, attribute)** level rather than the whole-review level. Each review was therefore expanded conceptually into separate review-attribute instances so that each attribute could be scored on its own evidence.

The scoring workflow had three main phases:

| Phase | Decision | Output |
|---|---|---|
| Phase 1 | Sign assignment | `+1`, `-1`, or `0` |
| Phase 2 | Broad-band assignment | One of 11 locked sentiment subranges |
| Phase 3 | Exact-integer assignment | Final score within the chosen range |

This architecture separated **direction** from **magnitude** and then forced the final score to remain inside the chosen sentiment band. That structure improved consistency and made the benchmark more interpretable than open-ended integer scoring would have been.

### 7.5 The 11-band sentiment scale

The benchmark used a locked `-100` to `+100` scale partitioned into 11 sentiment bands:

| Band | Range |
|---|---|
| Extreme Negative | `-100` to `-81` |
| Very Strong Negative | `-80` to `-61` |
| Strong Negative | `-60` to `-41` |
| Moderate Negative | `-40` to `-21` |
| Slight Negative | `-20` to `-1` |
| Neutral / Mixed / Uncodable | `0` only |
| Slight Positive | `1` to `20` |
| Moderate Positive | `21` to `40` |
| Strong Positive | `41` to `60` |
| Very Strong Positive | `61` to `80` |
| Extreme Positive | `81` to `100` |

### 7.6 Exact-integer selection logic

Once a band had been selected, the final exact integer was chosen by reviewing the full text closely. The principal drivers of within-band placement were:

- linguistic intensity;
- thematic centrality of the attribute to the review;
- repetition or sustained emphasis; and
- explicitness and certainty of the evidence.

This rule set matters because it explains why the benchmark's exact scores are not arbitrary integers. They are constrained by band-level judgment and then positioned within-band using qualitative intensity logic.

---

## 8. Benchmark Output Summary

### 8.1 Final benchmark size

The final benchmark contained:

- **100 reviews**;
- **646 scored attribute instances**;
- a usable spread across the full `-100` to `+100` sentiment scale.

### 8.2 Distributional summary

The benchmark distribution was broad enough to demonstrate that the scoring system was functioning as a real scale rather than collapsing into a narrow center.

| Statistic | Value |
|---|---:|
| Total scored attribute instances | 646 |
| Mean | 25.33 |
| Median | 47 |
| Standard deviation | 50.25 |
| Positive instances | 448 |
| Positive share | 69.3% |
| Negative instances | 143 |
| Negative share | 22.1% |
| Exact zero instances | 55 |
| Zero share | 8.5% |

### 8.3 Band-level distribution

The strongest concentration occurred in the **Strong Positive** band, but the benchmark still showed meaningful mass in negative and neutral regions.

| Sentiment band | Count |
|---|---:|
| Extreme Positive | 75 |
| Very Strong Positive | 34 |
| Strong Positive | 237 |
| Moderate Positive | 33 |
| Slight Positive | 69 |
| Neutral / Mixed | 55 |
| Slight Negative | 24 |
| Moderate Negative | 9 |
| Strong Negative | 59 |
| Very Strong Negative | 33 |
| Extreme Negative | 18 |

### 8.4 What this benchmark summary shows

The benchmark did not collapse into a single weakly positive cluster. It used the full sign structure and a wide range of magnitude bands, which is important because later AI evaluation would have been much less informative if the human baseline occupied only a small central range.

---

## 9. Reduction and Consolidation to the Canonical Layer

### 9.1 Why reduction became necessary

The larger framework was conceptually rich but too large and too overlapping to serve as the final production ontology without further governance. Phase 3 therefore included a dedicated reduction and consolidation pass whose purpose was to improve interpretability, reduce redundancy, control sparsity, and prepare the attribute layer for downstream full-corpus scoring and modeling.

### 9.2 Decision framework

The reduction logic used three explicit recommendation classes:

| Recommendation | Implementation meaning |
|---|---|
| **KEEP** | Retain as a standalone canonical attribute |
| **MERGE** | Do not retain standalone; map into a broader surviving canonical attribute |
| **DROP** | Remove from the canonical layer and do not expose as a production attribute |

This logic should be understood as methodology governance rather than cosmetic cleanup.

### 9.3 Reduction criteria

The reduction pass optimized for a hybrid of empirical support and construct validity. The criteria documented in the reduction materials included:

- **corpus prevalence** — whether the attribute appeared often enough to justify occupying its own feature slot;
- **scored frequency** — whether it generated enough benchmark-scored support to remain analytically useful;
- **association with overall satisfaction** — whether it seemed meaningfully connected to the project's evaluative outcome space;
- **marker overlap and semantic redundancy** — whether it was too similar to nearby constructs;
- **domain fit** — whether it belonged centrally enough to the Joe's Crab Shack dine-in study context; and
- **granularity control** — whether it was too narrow to survive as its own standalone construct.

### 9.4 Numerical outcome of the reduction

The raw recommendation counts from the reduction materials were:

| Outcome | Count |
|---|---:|
| KEEP | 39 |
| MERGE | 19 |
| DROP | 38 |

The implemented result was a **40-attribute canonical layer**. In practical terms, the reduction materials functioned as a KEEP / MERGE / DROP governance pass over the legacy framework, while the final canonical output preserved the service-recovery umbrella needed for downstream use. The important project-level result is the locked 40-attribute canonical layer, not the intermediate worksheet arithmetic by itself.

### 9.5 Category-level pattern

The reduction was not uniform. Some domains survived much more strongly than others.

| Category | KEEP | MERGE | DROP |
|---|---:|---:|---:|
| Ambiance | 10 | 6 | 3 |
| Food | 9 | 4 | 12 |
| Service | 10 | 4 | 7 |
| Logistics | 3 | 0 | 10 |
| Pricing | 3 | 4 | 3 |
| Research / meta constructs | 4 | 1 | 3 |

This pattern matters because it reveals the shape of the final ontology. Service and ambiance survived relatively strongly. Food remained important but was pruned heavily through consolidation. Logistics was reduced aggressively. Pricing was compressed into a smaller but still meaningful core. Research or meta constructs survived only where they carried unusual value for the project's interpretive goals.

### 9.6 Main consolidation logic

The canonical layer preserved latent constructs while collapsing unnecessary surface distinctions. The dominant patterns included:

| Legacy attribute | Canonical target |
|---|---|
| Beverage Quality | Bar Drinks |
| Bill Shock | Price Fairness |
| Brand Reputation | Hype vs. Reality |
| Date-Night Vibe | Ambiance |
| Desserts | Flavor Profile |
| Emotional Comfort | Ambiance |
| Fee Transparency | Price Fairness |
| Food Freshness | Flavor Profile |
| Issue Recovery | Conflict Resolution |
| Lighting | Ambiance |
| Portion Consistency | Portion Size |
| Privacy | Ambiance |
| Recommendations | Attentiveness / Competence |
| Restrooms | Cleanliness |
| Seating Comfort | Ambiance |
| Sincerity | Conflict Resolution |
| Surcharges | Price Fairness |
| Value Predictability | Price Fairness |

The methodological point is that Phase 3 did not simply delete constructs. It compressed semantically overlapping constructs into stronger umbrellas.

### 9.7 Why attributes were dropped

Recurring drop themes included:

- too sparse;
- too noisy;
- too weakly distinct;
- too channel-specific; and
- too poorly aligned with the dine-in tipping-regime study context.

This is why many logistics-heavy, delivery-heavy, or low-density constructs did not survive into the canonical layer.

---

## 10. The Final Canonical Taxonomy

### 10.1 Purpose of the canonical layer

The canonical layer is the authoritative Phase 3 ontology inherited by later automated scoring. It exists to provide a smaller, cleaner, and more stable measurement layer than the original legacy framework while preserving the constructs most important to the study.

### 10.2 Final 40 canonical attributes by domain

| Domain | Count | Canonical attributes |
|---|---:|---|
| Ambiance / setting | 10 | Air Quality; Ambiance; Cleanliness; Crowd Density; Family-Friendliness; Group-Friendliness; Noise Level; Outdoor Dining; Special Occasions; Table Placement |
| Food and beverage | 9 | Appetizers; Bar Drinks; Chef Creativity; Flavor Profile; Ingredient Quality; Portion Size; Seasonal Menu; Signature Dishes; Temperature and Doneness |
| Service and staff | 11 | Attentiveness; Competence; Conflict Resolution; Customization; Host Stand; Manager Presence; Order Accuracy; Perceived Care; Perceived Respect; Service Speed; Staff Coordination |
| Pricing and fairness | 3 | Happy Hour; Perceived Fairness; Price Fairness |
| Logistics | 3 | Location; Parking; Wait Time |
| Research / meta constructs | 4 | Brand Consistency; Hype vs. Reality; Tipping; Trustworthiness |

### 10.3 Developer-facing semantic contract

The canonical taxonomy is not just a list of names. It is a semantic contract consisting of:

- the final canonical attribute inventory;
- developer-facing definitions for each canonical attribute;
- merge logic from legacy labels into canonical targets;
- instructions to keep canonical and legacy layers separate;
- guidance to expose only canonical attributes in downstream prompting; and
- explicit treatment of merged attributes as aliases rather than surviving production fields.

That contract is why `Attribute_Canonical_Definitions.md` functions as a methodology asset rather than merely as documentation.

### 10.4 Why this layer is the authoritative one

Later phases inherit the canonical layer because it is the first attribute system that is simultaneously:

- conceptually grounded,
- benchmarked,
- reduced for overlap and sparsity,
- stable enough for full-corpus execution, and
- packaged with developer-facing definitions and merge rules.

That is why Phase 4 treats the canonical layer as locked rather than as a prompt-time suggestion.

---

## 11. Phase 3 Authoritative Outputs

Phase 3 produced a methodology-asset bundle rather than one flat dataset.

### 11.1 `restaurant_review_attribute_lexicon.csv`

This artifact is the legacy framework file. It captures the 97-attribute system across nine categories and preserves the larger conceptual space that the project originally built.

**What it is for:**

- preserving the larger aspect inventory;
- documenting the pre-reduction attribute layer;
- maintaining the conceptual lineage that later flowed into the canonical layer.

### 11.2 `manual_attribute_scores.csv`

This artifact is the human-coded benchmark produced from the stratified 100-review subset of `sample.csv`.

**What it is for:**

- benchmark comparison against later AI outputs;
- preserving the human reference judgments used in standard-setting;
- documenting the scored review-attribute instances that supported framework refinement and later reduction.

### 11.3 `Attribute_Canonical_Definitions.md`

This artifact is the final canonical taxonomy document. It defines the canonical attribute layer, explains merge logic, provides developer implementation guidance, and serves as the rule asset inherited by later automation.

**What it is for:**

- serving as the authoritative Phase 3 methodology asset;
- defining the 40 canonical attributes;
- governing downstream prompt exposure and schema interpretation;
- preserving merge and alias logic from legacy labels.

### 11.4 Supporting explanatory companions

The Phase 3 asset bundle is also supported by companion explanatory materials, including:

- the Manual Review Scoring Process report;
- the Mid-Semester Progress Report;
- explanatory canonical-set summaries;
- and any project-side data dictionaries or developer notes that preserve mapping logic.

These are not replacements for the three core Phase 3 deliverables, but they do make the Phase 3 methodology auditable and reconstructable.

---

## 12. Operational Readiness of the Phase 3 Deliverables

### 12.1 Benchmark certainty

The benchmark selection logic is fixed and reproducible:

- benchmark source file identified;
- exclusion rules identified;
- strata construction identified;
- 20-per-stratum sampling rule identified;
- review identifier logic identified;
- final benchmark size identified.

That gives the benchmark enough structural certainty to function as a genuine methodology asset.

### 12.2 Taxonomy certainty

The larger framework and the final canonical layer are both fixed enough for downstream use:

- the legacy framework exists as a structured 97-attribute inventory;
- the canonical layer exists as a final 40-attribute system;
- the canonical definitions are written in developer-facing form rather than only informally;
- merge logic is explicit enough to support downstream alias handling.

### 12.3 Reduction certainty

The reduction pass is operationally meaningful because it does not merely state that a reduction occurred. It preserves:

- explicit KEEP / MERGE / DROP logic;
- numeric reduction counts;
- category-level survival patterns; and
- concrete merge examples that show how constructs were compressed.

### 12.4 Downstream readiness summary

Phase 3's outputs are operationally ready for downstream execution because later phases do not need to re-invent the measurement system. They can inherit:

- the canonical attribute inventory,
- benchmark semantics,
- merge logic,
- score meaning,
- and developer-facing definitions.

That is the standard of readiness Phase 3 had to reach before project-scale scoring could begin.

---

## 13. Honest Limitations of the Phase 3 Deliverable

### 13.1 The benchmark was not scored on the later Yelp master dataset

The benchmark was built from `sample.csv`, a generic restaurant-review dataset, not from the later Joe's Crab Shack / control / competitor Yelp corpus. That creates a domain-transfer assumption whenever later phases use the benchmark to evaluate automation applied to the extracted Yelp reviews.

### 13.2 The benchmark is a disciplined sample, not an exhaustive gold standard for the whole project domain

A 100-review / 646-instance benchmark is strong enough to support standard-setting and later evaluation, but it is not exhaustive. Sparse or rare constructs remain less densely benchmarked than common ones.

### 13.3 The reduction is a governance decision, not a learned optimization procedure

The move from 97 attributes to the final canonical layer is technically motivated, but it is still a human-governed feature-selection and construct-consolidation step rather than a model-learned optimum.

### 13.4 The exploratory signal corpus is upstream support, not evidence or ground truth

The corpus helped seed the framework but should not be confused with benchmark evidence, extracted review data, or the final ontology.

### 13.5 The larger framework still matters even after reduction

Because the canonical layer was derived from a larger framework, downstream readers must avoid pretending the 40-attribute layer appeared in isolation. The legacy framework continues to matter for conceptual lineage, merge logic, and historical traceability.

### 13.6 What these limitations do *not* affect

These limitations are real, but they do **not** undermine the core achievement of Phase 3. Phase 3 still succeeded in creating:

- a formal attribute framework,
- a reproducible human benchmark,
- a technically motivated reduction pass, and
- a locked canonical taxonomy suitable for downstream execution.

---

## 14. Why the Phase 3 Methodology Asset Bundle Is the Correct Endpoint

Phase 3's success criterion was never the production of one giant analytical CSV. Its success criterion was a locked standard-setting bundle that:

1. defines the restaurant-review measurement space,
2. preserves a human-coded benchmark,
3. compresses the larger legacy framework into a smaller canonical layer,
4. documents the final semantic and merge contract for later automation, and
5. allows downstream project-scale scoring to proceed without reopening the ontology.

The final Phase 3 outputs satisfy all five criteria. The limitations in Section 13 are real but do not compromise the phase's methodological core. Phase 3 therefore concluded by preserving the work that holds standard-setting weight rather than by pretending it was already a full-corpus execution stage.

---

## 15. Methodological Position at the End of Phase 3

At this point, the project had resolved the question of how restaurant-review language should be represented, scored, and standardized. Phase 3 completed:

- construction of the larger 97-attribute restaurant-review framework;
- benchmark sampling from `sample.csv` under a reproducible stratified design;
- staged human coding of benchmark reviews and attribute instances;
- use of comparison-model disagreement as a refinement aid rather than as final authority;
- construction of a 646-instance scored benchmark;
- feature-pruning and construct-consolidation into a 40-attribute canonical layer;
- packaging of canonical definitions, merge logic, and developer-facing implementation guidance.

The project had therefore moved past the question **"what should count as a measurable restaurant-review construct, and how should it be scored?"** and into the question **"how can the locked measurement system now be applied at full scale to the actual project corpus?"** That second question is the subject of later project execution.

---

## 16. Final Phase 3 Deliverable

The final deliverable of Phase 3 is a locked methodology-asset bundle consisting of:

- a **97-attribute restaurant-review framework** across nine categories;
- a **100-review stratified benchmark** drawn from `sample.csv`;
- **646 scored benchmark attribute instances** under a disciplined three-step scoring workflow;
- a **40-attribute canonical layer** produced through formal reduction and consolidation;
- explicit **KEEP / MERGE / DROP** governance logic;
- developer-facing **canonical definitions**, merge handling, and implementation guidance;
- one clearly documented methodological limitation: the benchmark was not drawn from the later Yelp master dataset.

That deliverable is carried primarily by:

- `restaurant_review_attribute_lexicon.csv`,
- `manual_attribute_scores.csv`, and
- `Attribute_Canonical_Definitions.md`.

---

## 17. State of the Project at the End of Phase 3

At the end of Phase 3, the project was in the position it needed to reach before project-scale automation could begin because:

- the attribute framework was formalized;
- the benchmark existed and was human-coded;
- the score semantics were explicit;
- the canonical layer was locked;
- merge logic and semantic boundaries were documented;
- the exploratory corpus had been absorbed into a more rigorous framework-driven methodology;
- later phases no longer needed to invent the ontology while executing the full project corpus.

The project was therefore ready to move into large-scale attribute detection and scoring as an execution problem rather than as a measurement-design problem.

---

## 18. Handoff to Phase 4

This section is intentionally brief. A full Phase 4 narrative appears in the next companion document. The purpose of this section is only to mark the contract between the two phases.

### 18.1 What Phase 4 inherits from Phase 3

- `Attribute_Canonical_Definitions.md` as the authoritative canonical taxonomy.
- The locked 40-attribute layer and its developer-facing definitions.
- Explicit merge logic from legacy attributes to canonical targets.
- The scored benchmark in `manual_attribute_scores.csv` as an optional human reference asset for later evaluation.
- The larger legacy framework in `restaurant_review_attribute_lexicon.csv` as conceptual lineage and supporting methodology context.
- The locked score semantics inherited from the benchmark workflow: polarity, band logic, and integer score meaning.

### 18.2 Parallel assets Phase 4 will combine with Phase 3 outputs

Phase 4 will combine the locked Phase 3 methodology assets with the later project-scale extraction outputs from Phase 2:

- `consolidated_cleaned_reviews.csv` as the full extracted Yelp corpus;
- the fixed group / role / window structure inherited from Phase 1 and Phase 2;
- the canonical attribute layer and benchmark semantics inherited from Phase 3.

### 18.3 What Phase 4 is responsible for

- applying the locked canonical taxonomy to every row of the project-scale review dataset;
- executing full-corpus attribute detection;
- assigning sentiment scores to locked review-attribute pairs;
- preserving an audit trail for model outputs and consolidations;
- producing a modeling-ready scored dataset.

### 18.4 What Phase 4 is *not* permitted to do

- **Re-open the taxonomy.** Phase 4 may not add, remove, merge, or reinterpret canonical attributes during execution.
- **Treat the benchmark as if it came from the extracted Yelp corpus.** Any evaluation use must acknowledge the benchmark's source in `sample.csv`.
- **Erase legacy-to-canonical traceability.** Merged attributes must remain interpretable through explicit mapping logic rather than silent renaming.
- **Collapse blanks into zeros.** Later numerical outputs must preserve the distinction between attribute absence and genuine neutral sentiment.

---

## 19. Document Control

| Field | Value |
|---|---|
| Document | Phase 3 Completion Report |
| Phase status | Closed |
| Primary methodology inputs | `sample.csv`; exploratory signal corpus; literature, sponsor guidance, and conceptual framing |
| Primary deliverables | `restaurant_review_attribute_lexicon.csv`; `manual_attribute_scores.csv`; `Attribute_Canonical_Definitions.md` |
| Benchmark source dataset size | 500 reviews |
| Benchmark final sampled size | 100 reviews |
| Benchmark scored instances | 646 |
| Legacy framework size | 97 attributes across 9 categories |
| Reduction governance counts | 39 KEEP / 19 MERGE / 38 DROP |
| Final canonical taxonomy size | 40 attributes |
| Benchmark scoring scale | Integer `[-100, +100]` via sign → band → exact-integer workflow |
| Benchmark summary statistics | Mean 25.33; median 47; standard deviation 50.25 |
| Positive / negative / zero benchmark share | 448 / 143 / 55 instances |
| Critical methodological assumption | Benchmark-to-master-dataset domain transfer (`sample.csv` generic restaurant corpus → later extracted Yelp project corpus) |
| Companion documents | Phase 1 Completion Report; Phase 2 Completion Report; Phase 4 Completion Report; Mid-Semester Progress Report; Manual Review Scoring Process report |

---

*End of Phase 3 Completion Report.*
