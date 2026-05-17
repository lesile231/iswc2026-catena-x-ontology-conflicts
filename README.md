# iswc2026-catena-x-ontology-conflicts

Research paper submitted to the 4th International Workshop on Semantic Industrial Information Modelling (SemIIM 2026), co-located with ISWC 2026.

## Paper

**Detecting Cross-Use-Case Inconsistencies in Industrial Dataspace Ontologies: An Empirical Study of 123 Catena-X Aspect Models**

We analyze 123 SAMM-based aspect models from the Catena-X automotive dataspace, identifying 249 cross-model property overlaps including 40 structural conflicts. We classify these conflicts into three categories — naming collisions, regulatory-driven divergence, and non-regulatory coordination failures — and validate the regulatory cases against EU regulation source texts.

## Key Findings

- 40 structural conflicts identified across 123 Catena-X Aspect Models
- 6 of 40 (15%) validated as regulatory-driven against EU regulation source texts
- 6 of 13 conflicts involving regulatory models (46%) show regulatory influence on the structural divergence
- The majority of conflicts (~65%) are non-regulatory and stem from Working Group coordination gaps

See `validation/METHODOLOGY.md` for the full classification process and `validation/validation_regulatory_cases.csv` for case-by-case rationale.

## Structure

```
paper/                  LaTeX source, bibliography, compiled PDF
sldt-semantic-models/   (submodule) Catena-X corpus + analysis pipeline
  analysis/
    overlap_detection.py      Property overlap detection (RDFLib)
    statistical_analysis.py   Statistical summary generation
    results/                  Generated analysis outputs (CSV, TXT)
validation/             Classification and validation methodology
  METHODOLOGY.md              Process description + AI assistance disclosure
  CLASSIFICATION_RUBRIC.md    Formal rubric (C1.1-C1.3, C2.1-C2.4, C3.1-C3.3)
  classification_all_40.csv   All 40 CONFLICT properties with classification
  validation_regulatory_cases.csv  C2.3b validation of all 13 initially-classified regulatory cases
```

## AI Assistance Disclosure

The initial classification of 40 CONFLICT properties was performed with AI assistance (Claude, Anthropic). All 13 regulatory-driven classifications were subsequently validated by human reading of the EU regulation source texts (Battery Regulation 2023/1542, ESPR 2024/1781, PACT Framework). Of these, 10 were directly validated, 1 was spot-checked (ratedSpeed — confirmed ESPR contains no "speed" data field requirement), and 2 were inferred from validated cases with identical patterns. See `validation/METHODOLOGY.md` for details.

## Reproducing the Analysis

### 1. Setup

The `sldt-semantic-models/` directory is a git submodule containing the Catena-X corpus. The `--recurse-submodules` flag ensures it is cloned along with this repository.

```bash
git clone --recurse-submodules https://github.com/lesile231/iswc2026-catena-x-ontology-conflicts.git
pip install rdflib
```

### 2. Run the overlap detection pipeline

```bash
# Input:  sldt-semantic-models/io.catenax.*/
# Output: sldt-semantic-models/analysis/results/
python sldt-semantic-models/analysis/overlap_detection.py
```

Expected outputs in `analysis/results/`:
- `01_overlaps_summary.csv` — 249 property overlaps with overlap class
- `02_overlaps_detail.txt` — per-property model list with Characteristics and dataTypes
- `03_model_pair_matrix.csv` — pairwise model overlap counts

### 3. Run statistical analysis

```bash
# Input:  analysis/results/01_overlaps_summary.csv
# Output: analysis/results/04_statistics.txt
python sldt-semantic-models/analysis/statistical_analysis.py
```

### 4. Validation (manual)

The regulatory validation in `validation/` was performed by human reading of regulation texts, not by automated scripts. See `validation/METHODOLOGY.md` for the process.

## Author

Sangil Lee — University of Liverpool / IBCT
