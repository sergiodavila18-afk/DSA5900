```mermaid
flowchart TD

    MAN["Phase 1 Locked Location Manifest<br/>(location_manifest_final.csv;<br/>61 active rows = 13 Treatment + 26 Tier 1 + 22 Tier 2)"]:::data

    subgraph P2["Phase 2"]
        direction TB

        EXT["PAD Yelp Extraction<br/>(execute row-level review extraction<br/>across all manifest locations)"]:::proc

        PROC["Processing, Reconciliation, and Validation<br/>(clean, normalize, deduplicate,<br/>and recover missing reviews)"]:::proc

        LABEL["Manifest Inheritance and Analytical Labeling<br/>(attach group, role, matched pilot,<br/>and policy-window metadata)"]:::proc

        EXT --> PROC --> LABEL
    end

    MASTER["Master analytical dataset<br/>(consolidated_cleaned_reviews.csv;<br/>16,997 reviews × 42 columns;<br/>single downstream source of truth)"]:::output

    MAN --> EXT
    LABEL --> MASTER

    classDef proc fill:#EEF4FF,stroke:#3B6FB6,stroke-width:1.2px,color:#111;
    classDef data fill:#F8FAFC,stroke:#5B6573,stroke-width:1.2px,color:#111;
    classDef output fill:#EAFBF0,stroke:#2E8B57,stroke-width:1.4px,color:#111;

    class MAN data;
    class EXT,PROC,LABEL proc;
    class MASTER output;
```mermaid
