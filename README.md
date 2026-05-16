# iswc2026-catena-x-ontology-conflicts

Research paper submitted to the 4th International Workshop on Semantic Industrial Information Modelling (SemIIM 2025), co-located with ISWC 2026.

## Paper

**Detecting Cross-Use-Case Inconsistencies in Industrial Dataspace Ontologies: An Empirical Study of 123 Catena-X Aspect Models**

We analyze 123 SAMM-based aspect models from the Catena-X automotive dataspace, identifying 249 cross-model property overlaps (40 structural conflicts) and demonstrating that regulatory-driven divergence constitutes a systematic source of ontological inconsistency.

## Structure

```
paper/              LaTeX source, bibliography, compiled PDF
sldt-semantic-models/
  analysis/         RDFLib-based overlap detection pipeline
    results/        Generated analysis outputs (CSV, TXT)
```

## Reproducing the Analysis

```bash
# Clone the Catena-X corpus into sldt-semantic-models/
git clone https://github.com/eclipse-tractusx/sldt-semantic-models temp
mv temp/io.catenax.* sldt-semantic-models/
rm -rf temp

# Run pipeline
pip install rdflib
python sldt-semantic-models/analysis/overlap_detection.py
python sldt-semantic-models/analysis/statistical_analysis.py
```

## Author

Sangil Lee — University of Liverpool / IBCT
