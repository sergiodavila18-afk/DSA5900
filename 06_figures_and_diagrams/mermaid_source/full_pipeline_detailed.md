```mermaid
flowchart TD

    %% =========================
    %% EXTERNAL INPUTS
    %% =========================
    T18["Historical treatment universe<br/>(18 verified JCS pilot stores with<br/>documented no-tipping windows)"]:::data
    NP["Non-pilot JCS candidate universe<br/>(112 same-chain locations operating in the<br/>analytical window)"]:::data
    EC["External competitor candidate universe<br/>(merged, normalized, audited candidate pool<br/>across the pilot markets)"]:::data

    C["Exploratory signal corpus<br/>(early LLM-assisted lexical signal table:<br/>tipping, service, price, food, fairness,<br/>ambiance, and related review cues)"]:::data
    LIT["Literature, sponsor guidance,<br/>and conceptual framing"]:::data
    S500["Sponsor benchmark source dataset<br/>(sample.csv, 500 reviews)"]:::data
    MODELSET["Model set<br/>(ChatGPT, Anthropic Claude, Google Gemini)"]:::data

    %% =========================
    %% PHASE 1
    %% =========================
    subgraph P1["Phase 1"]
        direction TB

        T13["Active primary treatment subset<br/>(retain 13 pilot stores in the strict<br/>same-chain analytical design;<br/>5 pilots excluded from the main analysis)"]:::proc

        subgraph MATCH["Tier 1 internal same-chain design"]
            direction TB

            V1["Storefront topology<br/>(store form and trade-area setting)"]:::proc
            V2["Historical review volume<br/>(baseline Yelp visibility and review ecosystem)"]:::proc
            V3["Socioeconomic context<br/>(trade-area composition and local environment)"]:::proc
            V4["Region and market-type context<br/>(geographic and market-class alignment)"]:::proc
            V5["Row-level confidence and identity<br/>(verifiable storefront and unambiguous Yelp endpoint)"]:::proc

            M1["Structured matching context<br/>(disciplined same-chain control derivation<br/>under one consistent feature space)"]:::proc

            V1 --> M1
            V2 --> M1
            V3 --> M1
            V4 --> M1
            V5 --> M1
        end

        C1["Tier 1 strict internal controls<br/>(26 non-pilot JCS locations;<br/>group_type = internal_control,<br/>analysis_role = primary_internal,<br/>matched_pilot_id populated)"]:::proc

        subgraph COMP["Tier 2 external competitor design"]
            direction TB

            E1["Normalize and deduplicate<br/>(merge reports, resolve address conflicts,<br/>and confirm storefront identity)"]:::proc

            E2["Hard-gate screening<br/>(concept suitability, full-service model,<br/>historical operational relevance,<br/>review-footprint sufficiency,<br/>and geographic proximity)"]:::proc

            E2X["Documented exclusions<br/>(record failed-gate candidates and<br/>preserve exclusion logic explicitly)"]:::note

            E7["Locked Tier 2 cohort<br/>(22 official competitor rows;<br/>single audited external benchmark set)"]:::proc

            E1 --> E2
            E2 -- "pass all gates" --> E7
            E2 -. "any gate fail" .-> E2X
        end

        FRAME1["Locked comparison frame<br/>(13 treatment + 26 Tier 1 + 22 Tier 2;<br/>39-location strict same-chain core plus<br/>parallel external benchmark)"]:::proc

        T13 --> MATCH --> C1 --> FRAME1
        E7 --> FRAME1
    end

    MAN["Locked location manifest<br/>(location_manifest_final.csv;<br/>61 active rows with direct /biz/ endpoints,<br/>group_type, chain_type, analysis_role,<br/>matched_pilot_id, and populated windows)"]:::output

    MANQ["Operational-readiness verification<br/>(0 duplicate location_id values;<br/>0 missing URLs; all rows carry role logic,<br/>matched-pilot logic where applicable,<br/>and populated time-window fields;<br/>intentional T2_021 gap preserved)"]:::note

    %% =========================
    %% PHASE 2
    %% =========================
    subgraph P2["Phase 2"]
        direction TB

        EXT["PAD Main-flow extraction<br/>(row-anchored Yelp review-card extraction<br/>across all 61 manifest locations;<br/>browser setup → location loop → page loop → review rows)"]:::proc

        RAW["Raw extraction artifact<br/>(RunningTable / ExtractedData.csv;<br/>one row per extracted review card<br/>with location-level source context)"]:::data

        DUAL["Dual-path resolution and row normalization<br/>(resolve alternate review-text paths and<br/>alternate reaction-layout paths deterministically,<br/>rather than through one brittle selector)"]:::proc

        PY["Python consolidation and cleanup<br/>(parse_reviews.py and parse_2.py convert PAD output<br/>into canonical review-level rows and stitch<br/>rating, date, and text into coherent records)"]:::proc

        RECON["Cross-extraction reconciliation and gap recovery<br/>(mismatch.py compares extraction paths and recovers<br/>reviews captured on one path but missed on the other,<br/>producing one consolidated set rather than two rivals)"]:::proc

        INHERIT["Manifest inheritance and analytical labeling<br/>(join cleaned review rows back to the manifest so every row<br/>carries location_id, group_type, analysis_role,<br/>matched_pilot_id, and policy-window fields)"]:::proc

        VERIFY["Final consolidation and quality verification<br/>(coverage, identity, review_key deduplication,<br/>date integrity, and operational-readiness checks)"]:::proc

        QA2["Documented limitations and run notes<br/>(is_elite unusable in this release;<br/>reaction counts empty; reviewer_seafood_review_count unpopulated)"]:::note

        EXT --> RAW --> DUAL --> PY --> RECON --> INHERIT --> VERIFY
        VERIFY -. "preserve known field limitations" .-> QA2
    end

    MASTER["Master analytical dataset<br/>(consolidated_cleaned_reviews.csv;<br/>16,997 review-level rows × 42 columns;<br/>13 / 26 / 22 coverage preserved;<br/>single execution input for later phases)"]:::output

    %% =========================
    %% PHASE 3
    %% =========================
    subgraph P3["Phase 3"]
        direction TB

        TAX97["97-attribute framework development<br/>(expand exploratory review signals into a structured<br/>aspect framework across 9 categories with<br/>working definitions and boundary logic)"]:::proc

        subgraph BENCH["Benchmark construction"]
            direction TB

            SAMP["Stratified benchmark sampling<br/>(exclude extreme outliers;<br/>create balanced 100-review subset<br/>from sample.csv by review-length strata)"]:::proc

            CAT["Category assignment<br/>(manually label applicable top-level categories;<br/>use model overlays only as diagnostic checks<br/>to clarify category boundaries)"]:::proc

            ATTR["Attribute assignment<br/>(apply the 97-attribute framework review by review;<br/>use comparison overlays to refine borderline cases;<br/>final decisions remain human-locked)"]:::proc

            SCORE["Three-stage human sentiment scoring<br/>(sign assignment, broad-band placement,<br/>and exact-integer scoring at the<br/>review-attribute level)"]:::proc

            SAMP --> CAT --> ATTR --> SCORE
        end

        REDUCE["Taxonomy reduction and consolidation<br/>(39 KEEP, 19 MERGE, 38 DROP;<br/>reduce dimensionality, sparsity, redundancy,<br/>and weak-fit constructs using corpus prevalence,<br/>scored frequency, overall-satisfaction association,<br/>marker overlap, domain fit, and construct validity)"]:::proc

        MERGE["Canonical merge mapping and survivor logic<br/>(resolve aliases and umbrella survivors,<br/>compress overlapping constructs,<br/>and preserve only production-valid attribute IDs)"]:::proc

        ALIGN["Production-facing packaging and alignment<br/>(prepare canonical definitions, benchmark assets,<br/>and scoring-rule infrastructure for later application<br/>to the Phase 2 master dataset)"]:::proc

        TAX97 --> BENCH
        TAX97 --> REDUCE
        REDUCE --> MERGE
    end

    GOLD["Human-coded benchmark assets<br/>(100 reviews and 646 scored attribute instances;<br/>benchmark for later AI evaluation)"]:::output

    CANON["Final canonical taxonomy<br/>(40-attribute layer after feature selection,<br/>construct compression, and merge interpretation)"]:::output

    PKG["Frozen rule package<br/>(canonical 40-attribute set, definitions,<br/>prompt files, schemas, scoring logic,<br/>and benchmark-linked evaluation basis)"]:::output

    %% =========================
    %% PHASE 4
    %% =========================
    subgraph P4["Phase 4"]
        direction TB

        ORCH["Runtime setup and pass orchestration<br/>(Pass 1 runs on every review; Pass 2 runs only on deadlocks;<br/>Pass 3 runs once per review, batched across that review's locked attributes)"]:::proc

        subgraph PASS1["Pass 1"]
            direction LR
            P1A["ChatGPT<br/>(return only included attributes<br/>with supportive quote and brief reason)"]:::llm
            P1B["Anthropic Claude<br/>(return only included attributes<br/>with supportive quote and brief reason)"]:::llm
            P1C["Google Gemini<br/>(return only included attributes<br/>with supportive quote and brief reason)"]:::llm
        end

        CONS1["Pass 1 consolidation<br/>(build per-review comparison table of candidate attributes;<br/>preserve each provider's include / omit decision,<br/>supportive quote, and brief reason)"]:::proc

        TIER["Tiered majority-rule resolution<br/>(resolve deterministic cases without Pass 2 API calls:<br/>unanimous include / exclude, 2-of-3 majority, etc.;<br/>about 90.4% of attribute rows resolved here)"]:::proc

        subgraph PASS2["Pass 2"]
            direction LR
            P2A["ChatGPT<br/>(reconsider only deadlocked 1-of-2 cases<br/>using review text + Pass 1 comparison context)"]:::llm
            P2B["Anthropic Claude<br/>(reconsider only deadlocked 1-of-2 cases<br/>using review text + Pass 1 comparison context)"]:::llm
            P2C["Google Gemini<br/>(reconsider only deadlocked 1-of-2 cases<br/>using review text + Pass 1 comparison context)"]:::llm
        end

        CONS2["Pass 2 consolidation and attribute lock<br/>(adjudicate only the remaining deadlocks;<br/>about 9.6% of attribute rows reach Pass 2;<br/>produce final locked attribute set per review)"]:::proc

        P3PACK["Pass 3 review packet assembly<br/>(for each review, batch all locked attributes into one scoring call<br/>per provider; output returns one score object per locked attribute)"]:::proc

        subgraph PASS3["Pass 3"]
            direction LR
            P3A["ChatGPT<br/>(for each locked review-attribute pair, return:<br/>polarity sign + factor ratings for<br/>directness, intensity, specificity,<br/>certainty, centrality, consequence<br/>on a 0-10 scale)"]:::llm
            P3B["Anthropic Claude<br/>(for each locked review-attribute pair, return:<br/>polarity sign + factor ratings for<br/>directness, intensity, specificity,<br/>certainty, centrality, consequence<br/>on a 0-10 scale)"]:::llm
            P3C["Google Gemini<br/>(for each locked review-attribute pair, return:<br/>polarity sign + factor ratings for<br/>directness, intensity, specificity,<br/>certainty, centrality, consequence<br/>on a 0-10 scale)"]:::llm
        end

        CONS3["Pass 3 consolidation and final score computation<br/>(polarity sign = majority vote across 3 providers;<br/>factor values = mean across 3 providers;<br/>0-10 factor scale chosen so full -100 to 100 range is reachable;<br/>final score = sign × round(100 × (2D + 2I + S + C + Ce + Co) / 80))"]:::proc

        ORCH --> PASS1
        P1A --> CONS1
        P1B --> CONS1
        P1C --> CONS1

        CONS1 --> TIER
        TIER --> PASS2
        P2A --> CONS2
        P2B --> CONS2
        P2C --> CONS2

        TIER -. "deterministically resolved rows skip Pass 2" .-> CONS2
        CONS2 --> P3PACK --> PASS3

        P3A --> CONS3
        P3B --> CONS3
        P3C --> CONS3
    end

    WB["Validated scored-attribute database<br/>(locked attributes, consolidated factors,<br/>final scalar scores, and retained provider traces)"]:::output

    M4["Modeling-ready table / panel<br/>(join review metadata + validated scored attributes +<br/>cohort identity + regime labels into one clean handoff file)"]:::output

    AUDIT4["Run log and audit tables<br/>(review IDs, provider outputs by pass,<br/>comparison tables, deterministic resolutions,<br/>factor math, and file lineage)"]:::note

    %% =========================
    %% PHASE 5
    %% =========================
    subgraph P5["Phase 5"]
        direction TB

        LOAD["Data loading and inspection<br/>(load CSV in Colab, parse dates,<br/>confirm shape and columns,<br/>inspect sample review records)"]:::proc

        subgraph LEX["Lexical exploration"]
            direction TB

            WC["Word cloud generation<br/>(corpus-wide orientation to dominant<br/>review-language surface)"]:::proc

            NGRAM["N-gram analysis<br/>(top n-grams from length 2 through 7<br/>to identify recurring lexical patterns)"]:::proc

            OUTLIER["Outlier review check<br/>(trace repeated-token anomaly<br/>to one extreme negative review)"]:::proc

            WC --> NGRAM --> OUTLIER
        end

        SENT5["TextBlob sentiment scoring<br/>(continuous polarity + three-level sentiment category:<br/>positive, negative, neutral)"]:::proc

        G5["Executed analytical grouping<br/>(treatment = 1,577;<br/>internal_control = 7,003;<br/>external_competitor = 8,417)"]:::cohort

        GROUPSUM["Grouped descriptive summaries<br/>(mean rating and mean polarity by group,<br/>plus rating-by-sentiment-category comparisons)"]:::proc

        INF["Inferential testing<br/>(ANOVA and pairwise t-tests on rating and sentiment<br/>across treatment, internal control,<br/>and external competitor groups)"]:::proc

        TIPFLAG["Tipping-mention detector<br/>(binary flag from review_text<br/>using case-insensitive substring search for 'tip')"]:::proc

        TIPTEST["Tipping-mention analysis<br/>(groupwise tipping-discussion rates,<br/>two-proportion z-test, and conditional<br/>treatment-vs-control comparisons within<br/>the tipping-mention subset)"]:::proc

        ML5["Preliminary supervised classification<br/>(decision tree and random forest;<br/>null analysis, class weighting,<br/>and cross-validation added later)"]:::proc

        SYN5["Findings synthesis<br/>(treatment underperforms on rating and sentiment;<br/>tipping is discussed much more often in treatment;<br/>tipping-discussing reviews are more negative in every group;<br/>conditional treatment-vs-control gap disappears within the<br/>tipping-mention subset; tipping is highly predictive in early ML)"]:::proc

        LIMITS5["Documented Phase 5 boundaries<br/>(TextBlob-based sentiment only;<br/>coarse tipping detection; no difference-in-differences;<br/>classifier diagnostics incomplete;<br/>canonical 40-attribute layer not yet integrated)"]:::note

        LOAD --> LEX --> SENT5 --> GROUPSUM --> INF
        INF --> TIPFLAG --> TIPTEST --> ML5 --> SYN5
        SYN5 -. "preserve scope limits" .-> LIMITS5
    end

    FIND5["Phase 5 empirical findings package<br/>(descriptive patterns, inferential results,<br/>tipping-salience evidence, and preliminary<br/>treatment-classification signals)"]:::output

    REC5["Strategic recommendations<br/>(sponsor-facing interpretation and action logic<br/>grounded in the executed Phase 5 evidence)"]:::output

    %% =========================
    %% CROSS-PHASE HANDOFFS
    %% =========================
    T18 --> T13
    T18 --> COMP
    NP --> V1
    NP --> V2
    NP --> V3
    NP --> V4
    NP --> V5
    EC --> E1

    FRAME1 --> MAN
    MAN -. "execution-ready Phase 2 handoff" .-> MANQ

    MAN --> EXT
    MAN --> INHERIT
    VERIFY --> MASTER

    C --> TAX97
    LIT --> TAX97
    S500 --> SAMP
    SCORE --> GOLD
    GOLD --> REDUCE
    MERGE --> CANON
    CANON --> ALIGN
    GOLD --> ALIGN
    MASTER --> ALIGN
    ALIGN --> PKG

    MASTER --> ORCH
    PKG --> ORCH
    MODELSET --> ORCH

    CONS3 --> WB --> M4
    MASTER -. "merge source review metadata" .-> M4

    ORCH -. "log run state" .-> AUDIT4
    CONS1 -. "log Pass 1 comparison table" .-> AUDIT4
    TIER -. "log deterministic resolutions" .-> AUDIT4
    CONS2 -. "log locked attributes" .-> AUDIT4
    CONS3 -. "log factor math and final scores" .-> AUDIT4
    WB -. "retain final provider traces" .-> AUDIT4

    MASTER --> LOAD
    MASTER -. "group labels define executed grouping" .-> G5
    G5 --> GROUPSUM
    G5 --> INF
    G5 --> TIPTEST
    M4 -. "available upstream but not used in executed analysis" .-> LIMITS5

    INF --> FIND5
    TIPTEST --> FIND5
    ML5 --> FIND5
    SYN5 --> FIND5
    FIND5 --> REC5

    %% =========================
    %% STYLING
    %% =========================
    classDef llm fill:#F6E9FF,stroke:#7B3FC6,stroke-width:1.4px,color:#111;
    classDef proc fill:#EEF4FF,stroke:#3B6FB6,stroke-width:1.2px,color:#111;
    classDef data fill:#F8FAFC,stroke:#5B6573,stroke-width:1.2px,color:#111;
    classDef cohort fill:#FFF8E1,stroke:#FFB300,stroke-width:1.5px,color:#111;
    classDef output fill:#EAFBF0,stroke:#2E8B57,stroke-width:1.4px,color:#111;
    classDef note fill:#F5F5F5,stroke:#757575,stroke-width:1px,color:#111,stroke-dasharray: 4 4;

    class T18,NP,EC,C,LIT,S500,MODELSET,RAW data;
    class T13,V1,V2,V3,V4,V5,M1,C1,E1,E2,E7,FRAME1,EXT,DUAL,PY,RECON,INHERIT,VERIFY,TAX97,SAMP,CAT,ATTR,SCORE,REDUCE,MERGE,ALIGN,ORCH,CONS1,TIER,CONS2,P3PACK,CONS3,LOAD,WC,NGRAM,OUTLIER,SENT5,GROUPSUM,INF,TIPFLAG,TIPTEST,ML5,SYN5 proc;
    class G5 cohort;
    class MAN,MASTER,GOLD,CANON,PKG,WB,M4,FIND5,REC5 output;
    class E2X,MANQ,QA2,AUDIT4,LIMITS5 note;
    class P1A,P1B,P1C,P2A,P2B,P2C,P3A,P3B,P3C llm;
```
