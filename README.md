# Analysis of Sexism and Discrimination in the Spanish Parliament

This repository supports my undergraduate thesis (TFG) on the evolution of sexism and gender-based discrimination in the Spanish Parliament over the past 20 years, specifically analyzing plenary interventions from the years 2003, 2013, and 2023. The project is organized into two phases:

1. **Data Collection and Structuring** – Extraction, cleaning, segmentation, and database population of parliamentary texts.
2. **Analytical Study** – Topic modeling, discursive analysis, and gender-based comparison of intervention dynamics and language.

The data comes from the official archives of *El Congreso de los Diputados*, accessed via the Zenodo repository: https://doi.org/10.5281/zenodo.11195944

---

## Folder Structure

```
TFG/
├── Data extraction/
│   ├── clean-data/
│   └── data_extraction.ipynb
├── Data segmentation/
│   ├── backups/
│   ├── others/
│   ├── SQL/
│   │   └── EER diagram.mwb
│   ├── populate_database.ipynb
│   └── preliminar_segmentation_updated.ipynb
├── Topic modeling/
│   ├── Sankey_diagram.ipynb
│   └── topic_modeling.ipynb
├── DEMO.ipynb
├── README.md
```

> Note: Folders `Local data/` and `Local_csv/` are local and not included in this repository.

---

## Phase 1 – Data Preparation

### Step 1: Extract and Clean Raw Data

**Notebook:** `Data extraction/data_extraction.ipynb`

This notebook downloads and filters the relevant parliamentary transcripts using the `congreso` Python library, focusing exclusively on plenary sessions. After processing:

- A folder named `clean-data/` is created with filtered documents
- The data is ready for segmentation and database integration

Ensure the Zenodo link and the library `congreso` are accessible when running.

### Step 2: Segment Parliamentary Interventions

**Notebook:** `Data segmentation/preliminar_segmentation_updated.ipynb`

This notebook allows segmentation of full-session transcripts into individual speaking interventions.

To segment a document, use:
```python
int_20150120 = extract_interventions(ds_2015[4])
display(int_20150120)
```

This function identifies speakers, timestamps, and extracts gender and term-related metadata per intervention.

### Step 3: Populate the MySQL Database

**Notebook:** `Data segmentation/populate_database.ipynb`

This script structures the data into a normalized MySQL database. So far, metadata for MPs (`diputades`) and legislative terms (`term`) is populated. The rest of the tables follow a relational design that supports querying interventions by gender, term, or topic.

To visualize the database locally, you can use:
```bash
datasette interventions.db
```

---

## Database Schema (MySQL)

The database is designed to store intervention metadata, gender labels, and institutional context. Core tables:

- `document`: Session metadata and full text
- `intervention`: Individual speeches by MPs
- `diputades`: Static data about each MP
- `term` and `termDiputades`: Legislative periods and MP roles

The full schema is in `SQL/EER diagram.mwb`.

**Database access (insert link here)**

---

## Phase 2 – Analytical Study

This phase uses gender-labeled interventions to explore:

- How often women speak (and how long)
- What topics they address
- Whether language use reflects bias or exclusion
- Who speaks after whom, and interruption patterns

---

## Topic Modeling – Gendered Themes in Parliamentary Discourse

**Folder:** `Topic modeling/`

This part contains all scripts for thematic analysis using topic modeling. It is part of **Phase 2** of the TFG and uses a separate virtual environment.

### Environment Setup

Only for topic modeling, a virtual environment is **recommended**:
```bash
cd Topic modeling/
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt  # Optional
jupyter-lab &
```

In Jupyter, switch the kernel to `.venv` (Kernel > Change Kernel).

---

### `topic_modeling.ipynb`

This notebook performs:

- Preprocessing of parliamentary intervention texts
- LDA model training (scikit-learn)
- Evaluation and selection of topic number
- Topic dominance mapping by gender
- Visualizations: word clouds, pie charts, scatterplots

**Required Inputs:**
- `interventions_clean_minimal.csv`
- `frequent_speakers.csv`

See SQL queries below for generating these CSVs.

---

### `Sankey_diagram.ipynb`

Generates Sankey diagrams showing:

- Intervention → Gender → Year
- MPs → Frequency → Gender
- Topics → Gender → Term

Requires CSVs created either manually or via SQL + prior notebooks.

---

## Required SQL Queries

To extract the CSVs for modeling and visualization:

### 1. `interventions_clean_minimal.csv`
```sql
SELECT 
    i.id AS intervention_id,
    i.text,
    i.length,
    i.gender,
    t.position,
    t.grupo,
    t.legislatura,
    d.fecha
FROM intervention i
JOIN termDiputades t ON i.speaker_id = t.id_diputada_term
JOIN document d ON i.document_id = d.id
WHERE i.length > 0;
```

### 2. `frequent_speakers.csv`
```sql
SELECT 
    speaker_id,
    gender,
    COUNT(*) AS n_interventions
FROM intervention
GROUP BY speaker_id, gender;
```

---

## Notes and Recommendations

- All CSV files should be UTF-8 encoded
- Normalize fields (e.g. lowercase text) to avoid mismatches
- Consider excluding or grouping `presidentas de la cámara` depending on the analysis
- This project follows best practices for text preprocessing, SQL normalization, and interpretability in social science analysis
