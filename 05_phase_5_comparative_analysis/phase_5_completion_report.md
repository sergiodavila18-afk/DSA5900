# Phase 5 — Nicholas Jacob’s Quantitative Analysis
## Consolidated, Enhanced, and Conflict-Resolved Report

**Project:** Joe’s Crab Shack Pilot Tipping Analysis  
**Course:** DSA 5900 — Professional Practice (Spring 2026)  
**Team:** Sergio Davila, Nicholas Jacob, Deborah Rufus  
**Primary reporting window:** April 10, 2026 through April 24, 2026  
**Compilation basis:** Consolidated from existing project records, with **Phase5_Nicholas_Work_Report.md treated as the controlling source wherever documentary conflicts arise**  
**Prepared for:** Internal project documentation, final-report drafting support, and presentation synthesis

---

## 1. Purpose of This Report

This document is a consolidated, expanded, and clarified record of Nicholas Jacob’s Phase 5 contributions to the Joe’s Crab Shack no-tipping practicum project. Its purpose is to replace fragmentation across GroupMe messages, commit notes, notebooks, figures, and short written outputs with one internally coherent account of what Nicholas analyzed, how he analyzed it, what he found, what files he produced, what remains incompletely documented, and how this work fits into the project’s overall pipeline.

This is not a critique memo, a speculative reconstruction, or a methodological redesign. It is a disciplined synthesis of the surviving record. Where project artifacts disagree in wording, framing, or implementation detail, this report resolves those conflicts in favor of **Phase5_Nicholas_Work_Report.md**, per the governing assumption for this version.

---

## 2. Governing Source Rule and Consolidation Logic

### 2.1 Controlling-source rule

When project documents conflict, **Phase5_Nicholas_Work_Report.md controls** for purposes of this report.

### 2.2 Supporting-source role

Other project documents are used only to:

- provide stage context,
- clarify upstream handoffs,
- explain what data Nicholas had available,
- identify the timing and sequencing of his work,
- confirm output artifacts,
- and position his results within the larger project architecture.

### 2.3 Scope boundary

This report documents **Nicholas’s Phase 5 activity**, not the full practicum. Earlier phases are summarized only where they define his inputs, constraints, available variables, or downstream significance.

### 2.4 Evidence standard used in this report

This report distinguishes between four kinds of statements:

1. **Directly documented facts** from Nicholas’s own commits, write-up, or chat messages.
2. **Project-context facts** inherited from upstream phase reports.
3. **Conflict-resolved assertions** accepted because the controlling Phase 5 work report governs.
4. **Bounded interpretive summaries** that synthesize multiple records but do not claim undocumented code-level certainty.

---

## 3. Phase 5 in the Full Project Pipeline

Phase 5 is the project’s post-processing analytical stage: the point at which the extracted review corpus, already structured into a review-level analytical table, is used for descriptive analysis, hypothesis testing, sentiment quantification, and preliminary machine-learning classification.

Within the broader project architecture, Phase 5 comes after the completion of the comparison frame, extraction pipeline, and large-scale scoring infrastructure. In practical terms, this means Nicholas’s work began after the project had already:

- locked the treatment and comparison frame,
- assembled the review-level master dataset,
- established the broader no-tipping study context,
- and moved toward a structured attribute-based framework for later scoring and modeling.

However, under the governing source rule for this report, Nicholas’s actual Phase 5 execution during the documented reporting window is treated as having been performed on the **consolidated review-level CSV**, not on the later canonical wide-format attribute-scored table. This distinction matters because it defines both the value and the limit of his contribution: Nicholas completed a genuine quantitative analysis of the assembled review corpus, but not yet a full exploitation of the richer attribute-wide representation layer.

---

## 4. Immediate Upstream Inputs Available to Nicholas

According to the controlling Phase 5 record, Nicholas’s work sat on top of four already completed upstream accomplishments:

- historical identification and mapping of the Joe’s Crab Shack no-tipping pilot,
- Yelp extraction and scraping infrastructure,
- a consolidated cleaned review-level dataset with location and group metadata,
- and a later wide-format LLM-scored attribute dataset available elsewhere in the broader project pipeline.

Even though the wider project had begun moving toward a structured aspect-based analytical framework, Nicholas’s documented Phase 5 execution relied on the **consolidated cleaned review CSV** rather than the later attribute-wide table.

---

## 5. Primary Dataset Nicholas Actually Used

### 5.1 Controlling input file

The controlling report identifies Nicholas’s primary Phase 5 input as:

`consolidated_cleaned_reviews.csv`

### 5.2 Dataset shape

The controlling report states that this dataset contained:

- **16,997 reviews**
- **42 columns**

### 5.3 Column structure

The reported schema included location identity, analytical grouping, time-window fields, reviewer-level metadata, review-level text and rating fields, provenance columns, and a composite review key. The controlling report lists the following columns:

`run_id`, `location_id`, `name`, `address`, `city`, `state`, `zip`, `group_type`, `analysis_role`, `matched_pilot_id`, `source_url`, `pre_start`, `pre_end`, `post_start`, `post_end`, `reviewer_name`, `reviewer_location`, `elite`, `is_elite`, `review_count`, `friend_count`, `photo_count_user`, `reviewer_seafood_review_count`, `rating`, `review_date`, `review_text`, `helpful`, `thanks`, `love`, `ohno`, `review_photos`, `source_file`, `source_stem`, `chain_type`, `include_in_primary_analysis`, `include_in_secondary_analysis`, `pilot_window_start`, `pilot_window_end`, `location_confidence`, `notes`, `selection_status`, `review_key`.

### 5.4 Group structure used in Nicholas’s analysis

The controlling record states that Nicholas treated the dataset as divided into three analytical groups:

| Group | Review count | Description |
|---|---:|---|
| `external_competitor` | 8,417 | Similar-market competitor reviews |
| `internal_control` | 7,003 | Joe’s Crab Shack reviews outside the pilot window |
| `treatment` | 1,577 | Joe’s Crab Shack reviews in the pilot treatment condition |

Total reviews: **16,997**.

This three-group structure governed Nicholas’s reported summaries, significance tests, sentiment comparisons, and tipping-mention analysis.

---

## 6. Analytical Tools and Working Environment

Nicholas’s documented Phase 5 environment consisted of a lightweight but standard applied-analytics stack:

| Layer | Tooling |
|---|---|
| Notebook environment | Google Colab |
| Data handling | `pandas` |
| Visualization | `matplotlib`, `wordcloud` |
| Sentiment | `TextBlob` |
| Statistics | `scipy.stats`, `statsmodels.stats.proportion.proportions_ztest` |
| Classification | decision tree and random forest methods |
| Reporting | LaTeX |
| Version control | GitHub |

This tooling profile is important because it frames the kind of analysis Nicholas actually completed. His Phase 5 work was not an embedding-heavy NLP workflow, not a canonical attribute-aware predictive package, and not a custom causal-modeling framework. It was a classical applied-analytics pass on the review corpus using lexicon-based sentiment, straightforward frequentist testing, descriptive visualization, and early tree-based supervised modeling.

---

## 7. High-Level Summary of What Nicholas Did

Across the reporting window, Nicholas completed five major classes of work:

### 7.1 Corpus inspection and structural familiarization

He loaded the consolidated review table, checked shape and columns, converted date fields, and inspected representative rows.

### 7.2 Lexical exploration

He generated a corpus-wide word cloud, computed high-frequency n-grams from length 2 through length 7, and investigated a repeated-token anomaly caused by an extreme outlier review.

### 7.3 Sentiment scoring and sentiment distribution analysis

He computed TextBlob sentiment polarity for each review, reduced those scores into Positive / Negative / Neutral classes, visualized the distribution, and summarized sentiment by analytical group.

### 7.4 Group comparison and hypothesis testing

He compared treatment, internal control, and external competitor groups on mean ratings, mean sentiment, and tipping-mention frequency using classical significance testing.

### 7.5 Early supervised machine learning

He implemented a decision tree and later a random forest classifier, explored missing-value behavior, added class weighting and cross-validation, and documented the result that tipping-related information ranked as the strongest feature in early treatment-classification work.

---

## 8. Detailed Technical Record of the Work Performed

## 8.1 Data loading and inspection

Nicholas began by loading the review-level CSV into a Colab notebook, parsing `review_date` as a datetime, printing a DataFrame head, checking the full column list, and confirming the dataset shape as `(16997, 42)`. He also displayed a narrower review-level slice including location identity, reviewer fields, rating, date, and review text. That sample display later appeared in his write-up.

This opening step matters because it shows that his analysis was grounded in the cleaned downstream artifact as delivered and not in raw extraction dumps.

## 8.2 Word cloud generation

Nicholas then performed a broad lexical pass by concatenating all non-null review text, converting it to lowercase, stripping non-alphabetic characters, removing stopwords, and rendering a word cloud. The most visually dominant terms included:

- food
- good
- service
- place
- one
- server
- table
- ordered
- restaurant
- time
- waitress
- crab
- shrimp

This step did not produce inferential findings, but it served as a quick corpus-orientation device showing that the dominant lexical surface of the dataset centered on food, service, and the dining experience as a whole.

## 8.3 N-gram analysis

Nicholas built an n-gram frequency pass and computed the top five n-grams for `n = 2` through `n = 7`. The most frequent shorter n-grams were generic review-language patterns such as:

- `it was`
- `and the`
- `of the`
- `the food`
- `the food was`
- `the service was`
- `joes crab shack`

At longer n-gram lengths, more interpretable review phrases appeared, including:

- `my husband and i`
- `my wife and i`
- `we will definitely be back`
- `i will not be going back`
- `made sure we had everything we needed`

This pass exposed one major lexical anomaly: repeated runs of the word **very**.

## 8.4 Outlier investigation: the repeated “very” review

Nicholas followed up by tracing the repeated-token anomaly to a single long negative review containing **87 uses of the word “very.”** According to the consolidated record, that review described cascading dissatisfaction, including service problems and generalized frustration.

This was not a major substantive finding, but it is methodologically useful because it shows Nicholas checked whether corpus-frequency artifacts were being driven by one aberrant document rather than a genuine broader language pattern.

## 8.5 TextBlob sentiment scoring

Nicholas next operationalized review sentiment using **TextBlob polarity**. For each review, he computed a continuous polarity score and then reduced that score into a three-level categorical sentiment variable:

- Positive: polarity > 0
- Negative: polarity < 0
- Neutral: polarity == 0

He visualized sentiment two ways:

- a histogram of continuous polarity,
- and a pie chart of categorical sentiment counts.

The reported sentiment-category counts were:

| Sentiment category | Count |
|---|---:|
| Positive | 13,565 |
| Negative | 3,310 |
| Neutral | 122 |

This implies a strongly positive skew in the corpus overall, even before group comparison begins.

## 8.6 Group-level descriptive rating summaries

Nicholas then computed group means, standard deviations, and counts for `rating`:

| Group | Mean rating | Std. dev. | Count |
|---|---:|---:|---:|
| External competitor | 3.5048 | 1.4665 | 8,417 |
| Internal control | 2.8960 | 1.5723 | 7,003 |
| Treatment | 2.5777 | 1.4989 | 1,577 |

This immediately established the dominant descriptive pattern of the analysis: treatment reviews had the lowest average star rating of the three groups, internal controls were intermediate, and external competitors were highest.

## 8.7 Group-level descriptive sentiment summaries

He ran the same grouped summary for TextBlob polarity:

| Group | Mean polarity | Std. dev. | Count |
|---|---:|---:|---:|
| External competitor | 0.2635 | 0.2490 | 8,417 |
| Internal control | 0.1836 | 0.2834 | 7,003 |
| Treatment | 0.1373 | 0.2550 | 1,577 |

This mirrored the ratings pattern: competitor reviews were most positive, internal controls were lower, and treatment reviews were lowest.

## 8.8 Rating by sentiment category within each group

Nicholas also crossed `group_type` with `sentiment_category` and examined rating means within those strata. The surviving report indicates that even within positive-sentiment reviews, the average rating still stepped down from competitors to internal controls to treatment.

That matters because it suggests the treatment disadvantage was not merely a trivial artifact of having more plainly negative reviews overall. Some of the gap persisted even within the reviews that TextBlob still classified as positive.

## 8.9 Inferential testing: sentiment differences across groups

Nicholas formally tested group sentiment differences.

### One-way ANOVA on TextBlob polarity

- **F = 257.62**
- **p ≈ 6.0 × 10⁻¹¹¹**

Conclusion: the three groups do not share the same mean sentiment.

### Pairwise t-test: treatment vs internal control

- **t = −5.96**
- **p ≈ 2.6 × 10⁻⁹**

Conclusion: treatment reviews are significantly less positive than internal-control reviews.

### Pairwise t-test: external competitor vs internal control

- **t = 18.63**
- **p ≈ 1.2 × 10⁻⁷⁶**

Conclusion: external competitors are significantly more positive than internal controls.

## 8.10 Inferential testing: rating differences across groups

Nicholas ran equivalent tests on star ratings.

### One-way ANOVA on rating

- **F = 441.16**
- **p ≈ 1.6 × 10⁻¹⁸⁷**

Conclusion: at least one group mean differs materially.

### Pairwise t-test: treatment vs internal control

- **t = −7.33**
- **p ≈ 2.6 × 10⁻¹³**

Conclusion: treatment ratings are significantly lower than internal-control ratings.

Taken together, the rating and sentiment tests support the same first-pass descriptive picture: treatment reviews look worse than internal controls, and Joe’s Crab Shack outside the pilot still trails the external competitor benchmark.

---

## 9. Tipping-Mention Analysis

## 9.1 How Nicholas defined tipping mention

Under the controlling report, Nicholas created a binary flag for whether review text contained the substring `tip`, case-insensitive. That is a deliberately broad detector. It would capture expressions like `tip`, `tipping`, `tipped`, and `tips`, but it could also capture irrelevant strings. In other words, this was analytically useful as a coarse first-pass trigger, not a precision-engineered no-tipping-policy detector.

## 9.2 Corpus-wide tipping-mention rate

Nicholas counted **770 tipping mentions** out of **16,997 reviews**, for an overall rate of **4.530%**.

## 9.3 Tipping-mention rates by group

He then counted tipping mentions by group:

| Group | Mentions | Total reviews | Proportion |
|---|---:|---:|---:|
| External competitor | 365 | 8,417 | 4.34% |
| Internal control | 291 | 7,003 | 4.16% |
| Treatment | 148 | 1,577 | 9.39% |

The treatment group’s tipping-mention rate was therefore more than double the rate observed in either comparison group.

## 9.4 Two-proportion test

Nicholas formally tested the internal-control vs treatment tipping-mention difference using a two-proportion z-test:

- **Z = −8.5152**
- **p < 0.0001**

Conclusion: treatment reviews mention tipping at a significantly higher rate than internal-control reviews.

This is one of the most important substantive results in the Phase 5 record, because it indicates that the no-tipping intervention changed what customers talked about, at least at the level of topic salience.

---

## 10. Conditional Comparisons Given a Tipping Mention

Nicholas then asked a more refined question: once a review mentions tipping at all, do treatment and internal-control reviews still differ?

## 10.1 Rating by group and tipping mention

| Group | Mentions tip | Mean rating | Std. dev. | Count |
|---|---|---:|---:|---:|
| External competitor | False | 3.5339 | 1.4585 | 8,052 |
| External competitor | True | 2.8630 | 1.4983 | 365 |
| Internal control | False | 2.9267 | 1.5724 | 6,712 |
| Internal control | True | 2.1890 | 1.3978 | 291 |
| Treatment | False | 2.6193 | 1.5107 | 1,429 |
| Treatment | True | 2.1757 | 1.3182 | 148 |

## 10.2 Sentiment by group and tipping mention

| Group | Mentions tip | Mean polarity | Std. dev. | Count |
|---|---|---:|---:|---:|
| External competitor | False | 0.2674 | 0.2513 | 8,052 |
| External competitor | True | 0.1785 | 0.1734 | 365 |
| Internal control | False | 0.1875 | 0.2860 | 6,712 |
| Internal control | True | 0.0932 | 0.1941 | 291 |
| Treatment | False | 0.1426 | 0.2588 | 1,429 |
| Treatment | True | 0.0868 | 0.2095 | 148 |

The immediate descriptive takeaway is that, in **every group**, reviews that mention tipping are more negative on average than reviews that do not mention tipping.

## 10.3 Conditional null results

Nicholas then restricted the dataset to only the tipping-mention subset and ran two treatment-vs-internal-control t-tests.

### Rating, treatment-with-tip vs internal-control-with-tip

- **t = −0.0962**
- **p = 0.9234**

### Sentiment polarity, treatment-with-tip vs internal-control-with-tip

- **t = −0.3185**
- **p = 0.7502**

In both cases, Nicholas failed to reject the null of equal means.

This is one of the most intellectually important results in the Phase 5 record because it complicates a simple causal story. The pilot is associated with a much higher frequency of tipping discussion, and tipping-mention reviews are more negative overall, but among the subset of reviews that do talk about tipping, treatment and internal control are not distinguishable on rating or TextBlob polarity.

That means the most defensible reading is narrower than “the pilot uniquely worsened tipping sentiment.” What the documented evidence more safely supports is that the pilot increased the **frequency** of tipping discussion while the **conditional valence** of tipping-discussing reviews did not differ detectably between treatment and internal control.

---

## 11. Machine-Learning Work Completed by Nicholas

## 11.1 General character of the ML work

Nicholas’s machine-learning work was preliminary but real. It was not a fully specified predictive package with complete model cards, but it did extend beyond descriptive statistics and null-hypothesis testing into supervised classification.

## 11.2 Decision-tree stage

On April 20, Nicholas began a decision-tree analysis and explicitly noted that missing values materially affected results. This is important because it explains his methodological movement toward tree-based methods: they are comparatively forgiving of missingness and therefore attractive for a dataset with uneven reviewer-level metadata.

## 11.3 Random-forest stage

On April 21, Nicholas documented that he had implemented a random forest. Later that same day, he reported the key result that **tipping was the most important predictor in the study sample**.

## 11.4 Added null analysis

On April 22, Nicholas added further null analysis. This indicates that he did not treat missingness as a background nuisance only; he recognized it as a factor that might affect splits, importance rankings, and classifier behavior.

## 11.5 Added class weighting and cross-validation

Also on April 22, he documented that he had added both:

- **cross-validation**
- **weighting to the classifier**

This matters because the treatment class constituted only **1,577 of 16,997 reviews**, or roughly **9.3%** of the sample, so class imbalance was a real concern. Weighting suggests an attempt to avoid trivial majority-class behavior, and cross-validation suggests an attempt to examine whether the observed structure was stable rather than an artifact of one split.

## 11.6 Histograms and supporting visuals

He also added histograms on April 22, indicating that the classifier work was accompanied by at least some visual inspection of distributions or output behavior.

## 11.7 Boundary on what is actually documented

The controlling Phase 5 record is explicit that the exact details of the machine-learning implementation were **not fully documented** in the reviewed notebook or LaTeX write-up. Specifically, the following are not fully reported there:

- exact target coding,
- complete feature set,
- full preprocessing chain,
- hyperparameters,
- fold count,
- final evaluation metrics,
- confusion matrix,
- AUC or related diagnostics,
- and fold-level variance in feature importance.

So the correct characterization is that Nicholas completed documented supervised tree-based modeling, but the surviving record captures the existence and broad result of that modeling much more clearly than it captures the full technical specification.

---

## 12. Chronological Record of Nicholas’s Phase 5 Activity

The surviving documentary trail supports the following chronological progression.

### April 13, 2026

- Created working notebook in Colab.
- Began exploratory quick analysis.
- Investigated the repeated `very` anomaly and traced it to one extreme review.

### April 15–16, 2026

- Added tip-mention logic.
- Ran early tipping-discussion comparisons.
- Reported first “fail to reject” result on the tipping-discussing subset.
- Looked at the proportion of reviews discussing tipping.

### April 20, 2026

- Began tree-based modeling.
- Documented that missing values materially affected results.

### April 21, 2026

- Added a random forest method.
- Reported that tipping was the most important predictor in the study sample.

### April 22, 2026

- Added null analysis.
- Added histograms.
- Added cross-validation and class weighting to the classifier.

### April 23, 2026

- Added simple hypothesis testing.
- Looked at the proportion of “no opinion” or analogous null/empty structure.

### April 24, 2026

- Wrote up quick-analysis findings into the `Statistic_on_Scraped_Data` report.
- Committed or merged the reporting materials into the repository.

This chronology aligns closely with the visible evolution in both GroupMe and the commit notes.

---

## 13. Nicholas’s Documented Findings as of April 24, 2026

The controlling report, together with the quick-analysis PDF, supports the following findings.

### 13.1 Treatment ratings are lower

Treatment-group mean rating was approximately **2.58**, compared with **2.90** for internal control and **3.50** for external competitors.

### 13.2 Treatment sentiment is lower

Treatment-group mean TextBlob polarity was approximately **0.137**, compared with **0.184** for internal control and **0.264** for external competitors.

### 13.3 Joe’s Crab Shack trails the external benchmark even outside treatment

Internal control underperformed external competitors on both rating and sentiment, implying a broader baseline disadvantage for the chain independent of the no-tipping pilot.

### 13.4 Tipping is discussed much more often in treatment reviews

The treatment group’s tipping-mention rate was **9.39%**, compared with **4.16%** in internal control and **4.34%** in external competitors.

### 13.5 Tipping-discussing reviews are more negative in every group

Where tipping is mentioned, both mean ratings and mean sentiment are lower across all three analytical groups.

### 13.6 Conditional on tipping being mentioned, treatment and internal control converge

The two conditional t-tests fail to reject equality between treatment and internal control on both rating and sentiment within the tipping-mention subset.

### 13.7 Tipping is the most important feature in early treatment-classification work

This is the headline ML result Nicholas reported from his early tree-based modeling.

---

## 14. Files Nicholas Produced or Modified During Phase 5

The consolidated record attributes the following files and artifacts to Nicholas’s Phase 5 work:

| File | Purpose |
|---|---|
| `QuickAnalysis.ipynb` | Main Colab notebook for descriptive statistics and inferential tests |
| `Statistic_on_Scraped_Data.tex` | LaTeX source for the short write-up |
| `Statistic_on_Scraped_Data.pdf` | Compiled Phase 5 write-up |
| `.aux`, `.out`, `_synctex.gz` artifacts | Standard LaTeX build outputs |
| `wordcloud.png` or equivalent output | Word cloud visualization |
| `senthist.png` | Sentiment histogram |
| `sentpie.png` | Sentiment pie chart |
| Tree/forest classifier code | Present in GitHub but not fully represented in the preserved write-up |

This file set matters because it shows Nicholas did not merely run notebook cells informally. He produced a partial reporting package with figures and LaTeX output and committed that work to version control.

---

## 15. GitHub Activity During the Reporting Window

The documented Nicholas-attributed commit history for the period is:

| Date | Commit message |
|---|---|
| 2026-04-13 | Created using Colab |
| 2026-04-13 | explored the very. Only one review with 87 very... |
| 2026-04-15 | Added some things about if they mentioned tip. First fail to reject! |
| 2026-04-16 | Looked at proportion discussing tipping |
| 2026-04-20 | Just a start. NaN’s make a big difference... |
| 2026-04-21 | Did a random forest method |
| 2026-04-21 | Got another decent result! Tipping is most important predictor in the study sample |
| 2026-04-22 | Added some null analysis |
| 2026-04-22 | histograms added |
| 2026-04-22 | Added cross validation and weighting to the classifier |
| 2026-04-23 | Simple hypothesis test added. |
| 2026-04-23 | looked at the proportion of no opinion |
| 2026-04-24 | added report from quick analysis .py |
| 2026-04-24 | Merge branch 'main' of https://github.com/nurfnick/Jacob_OUDSA5900 |
| 2026-04-24 | I didn’t change much but saved what ever it was I did... |

This sequence aligns well with the substantive evolution visible in the report itself: descriptive review exploration first, tipping-specific testing next, classifier development next, and reporting last.

---

## 16. What Nicholas Had Not Yet Completed by the End of the Reporting Window

The controlling report identifies several open surfaces in Nicholas’s Phase 5 work.

### 16.1 The canonical 40-attribute wide table was not yet integrated

Under the controlling source rule, Nicholas’s active analysis still operated on the consolidated review-level CSV rather than the later canonical attribute-wide table.

### 16.2 The classifier target was group membership rather than a business outcome

His tree/forest work was framed around treatment vs non-treatment classification, not yet around a direct business-outcome target such as rating prediction with treatment status as a predictor.

### 16.3 Difference-in-differences was not implemented

The notebook’s contrasts were between-group rather than a fully explicit pre/post causal interaction design.

### 16.4 Tipping mention was only coarsely detected

The tipping flag was broad and not precision-audited for policy-specific language.

### 16.5 Sentiment remained TextBlob-based

The richer attribute-scored sentiment layer had not yet replaced or been compared directly with the general-domain TextBlob signal.

### 16.6 Classifier diagnostics were incompletely reported

The record does not include a full diagnostic table for the decision tree or random forest.

### 16.7 Reviewer-metadata weighting or stratification was not documented

Fields such as `is_elite`, `review_count`, `friend_count`, and `photo_count_user` were present in the data but not documented as active weighting or stratification inputs.

### 16.8 Explicit no-tipping-policy mentions were not isolated from generic tipping references

The analysis did not yet distinguish general mention of tipping from narrower policy-specific language such as “no tip line,” “service included,” or “tip-free.”

---

## 17. Methodological Characterization of Nicholas’s Phase 5 Contribution

The most accurate description of Nicholas’s Phase 5 work is:

> He completed a first-pass quantitative review analysis of the consolidated Yelp corpus using descriptive statistics, lexicon-based sentiment, proportion and mean tests, and early supervised tree-based classification, all centered on coarse tipping-mention detection rather than on the project’s later canonical attribute-wide scoring layer.

That characterization is specific enough to be accurate and broad enough to capture the real scope of the work.

It would be inaccurate to describe his Phase 5 work during this window as a fully realized attribute-aware machine-learning phase. It would also be inaccurate to reduce it to mere basic EDA. The work sits between those two extremes: substantially more developed than simple exploratory tabulation, but not yet the full mature modeling layer originally envisioned in the broader project architecture.

---

## 18. Best Defensible Interpretation of Nicholas’s Results

If the results are read together rather than in isolation, the most defensible synthesis is:

1. the treatment group underperformed both comparison groups on rating and TextBlob sentiment,
2. tipping was discussed much more often during treatment,
3. reviews that mention tipping are more negative in every group,
4. but once the analysis is restricted to the tipping-mention subset, treatment and internal control do not differ detectably on rating or sentiment,
5. and tipping-related information is highly useful in early treatment-classification work.

That combined reading is stronger than a simple “no-tipping made everything worse” claim and more faithful to the internal tensions in the actual results.

It suggests that the intervention changed what customers discussed and made tipping more salient in treatment reviews, but it does not by itself prove that treatment caused a uniquely more negative tipping-related reaction once tipping is already on the table as a topic of discussion.

---

## 19. Relationship to the Broader Project Architecture

Within the broader practicum, Nicholas’s Phase 5 work occupies a transitional role.

The project’s earlier design evolved away from simple topic-modeling ambitions and toward a structured aspect-based framework with a manually scored benchmark, locked taxonomy, and later canonical attribute layer. In that context, Nicholas’s Phase 5 work should be understood as a **parallel empirical pass on the consolidated review corpus**, not yet the full culmination of the attribute-scoring program.

That is why one of the most important facts in the controlling report is not a statistical number at all. It is the statement that Nicholas had **not yet integrated the 40-attribute LLM-scored wide table**. That single fact defines both the value and the limit of his Phase 5 contribution.

Its value is that it produced clear descriptive, inferential, and preliminary ML findings quickly from the assembled corpus. Its limit is that it did so without yet exploiting the richer representation layer the rest of the project had been building toward.

---

## 20. Practical Uses of This Report

This consolidated report can serve four immediate functions.

### 20.1 Source-of-truth function

It provides one coherent internal record of Nicholas’s Phase 5 work without requiring cross-reference among scattered notes.

### 20.2 Final-report drafting function

It can be used to write the Nicholas / Phase 5 sections of the final report in a way that is explicit about both results and boundaries.

### 20.3 Presentation-prep function

It offers a concise but detailed basis for explaining what was actually done in Phase 5 during oral questioning.

### 20.4 Scope-control function

It helps prevent overstatement by distinguishing between:

- what Nicholas actually analyzed,
- what he had available but did not yet integrate,
- and what remained open at the end of the reporting window.

---

## 21. Final Consolidated Conclusion

Nicholas Jacob’s documented Phase 5 contribution was a real and nontrivial quantitative analysis of the consolidated Joe’s Crab Shack review corpus. Using the 16,997-row review-level dataset, he conducted corpus inspection, lexical exploration, TextBlob sentiment scoring, groupwise descriptive analysis, ANOVA and t-test comparisons, tipping-mention proportion testing, conditional null tests, and early supervised tree-based classification. His main substantive findings were that treatment reviews scored lower on both rating and sentiment, that tipping was mentioned much more often in treatment reviews, that tipping-mention reviews were more negative in every group, that the treatment-vs-internal-control gap disappeared within the tipping-mention subset, and that tipping-related information ranked as the most important feature in early classification work.

At the same time, Phase 5 remained a first-pass analytical layer rather than the project’s final attribute-aware modeling stage. Nicholas’s documented work did not yet incorporate the 40-attribute LLM-scored wide table, did not yet isolate explicit no-tipping-policy mentions from generic tipping references, and did not yet provide a fully specified machine-learning diagnostic package. The most accurate overall judgment, therefore, is that Phase 5 produced a substantive empirical foundation and a credible preliminary modeling pass, but it did not yet exhaust the richer structured analytical framework that the larger project had been building toward.

---

*End of consolidated enhanced Phase 5 report.*
