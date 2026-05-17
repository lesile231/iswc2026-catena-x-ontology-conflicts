# Validation Methodology

## Overview

This document describes the methodology used to classify and validate the 40 CONFLICT properties identified in the Catena-X aspect model corpus.

## AI Assistance Disclosure

The initial classification of all 40 CONFLICT properties into three categories (Naming Collision, Regulatory-driven, Non-regulatory) was performed with AI assistance (Claude, Anthropic). The AI-assisted classification served as a **starting hypothesis** and was subsequently validated through manual verification against regulation source texts.

Specifically:
- **AI-assisted (initial):** Reading property descriptions, comparing semantic intent, proposing category assignments for all 40 properties.
- **Human-verified (validation):** Direct reading of EU regulation texts (Battery Regulation 2023/1542, ESPR 2024/1781, PACT Framework Technical Specification) to verify whether specific articles/annexes justify the structural choices in each model. All C2.3b assessments in `validation_regulatory_cases.csv` are based on human reading of regulation originals.

## Classification Rubric

See `CLASSIFICATION_RUBRIC.md` for the formal rubric with criteria C1.1–C1.3, C2.1–C2.4, C3.1–C3.3.

The critical criterion is **C2.3b**: "A specific article, annex, or section of the regulation justifies that model's structural choice."

## Validation Process

### Step 1: Initial Classification (AI-assisted)

All 40 CONFLICT properties were classified using the rubric:
- Compare `description` and `preferredName` across models
- Determine: same concept or different concept?
- If same concept: is at least one model a regulatory model?
- If yes: can the structural difference be traced to a specific regulatory provision?

Result: `classification_all_40.csv`

### Step 2: Regulatory Case Validation (Human-verified)

For each property initially classified as Regulatory-driven, we verified C2.3b against the regulation source texts:

| Regulation | Source | Sections Examined |
|-----------|--------|-------------------|
| Battery Regulation 2023/1542 | EUR-Lex official text | ANNEX XIII (battery passport data), ANNEX VII (SoH parameters) |
| ESPR 2024/1781 | EUR-Lex official text | Art 5–11, Art 27, Annex I (product parameters), Annex III (DPP data elements) |
| PACT Framework | WBCSD Technical Specification v3 | Section 4 (ProductFootprint data model) |

For each case, we assessed:
1. Does the regulation mention this data field explicitly?
2. Does the regulation specify the data structure (fields, types, conditions)?
3. Does the regulation justify why the model's structure differs from the conflicting model?

Result: `validation_regulatory_cases.csv`

### Step 3: Reclassification

Based on validation results, cases were reclassified:
- **C2.3b clear:** Maintained as Regulatory-driven
- **C2.3b partial:** Maintained as Regulatory-driven (regulation-induced via delegation gap)
- **C2.3b failed:** Reclassified to Non-regulatory or Naming Collision

## Key Finding: EU Regulation Specification Types

The validation revealed three types of EU regulation regarding data structure specification:

| Type | Description | C2.3b Result | Examples |
|------|-------------|-------------|---------|
| Field-specifying | Regulation directly dictates data fields and types | Clear pass | ESPR Art 27(6) for manufacturer; PACT §4 for version |
| Parameter-enumerating | Regulation lists domain-specific parameters without schema | Clear pass (entity separation) | Battery Reg ANNEX XIII for characteristics |
| Framework-delegating | Regulation defines WHAT to report, delegates HOW to delegated acts | Partial at best | ESPR Annex I, III for most requirements |

## Regulatory Model List

| Model | Regulation | Key Provisions |
|-------|-----------|----------------|
| io.catenax.generic.digital_product_passport | ESPR 2024/1781 | Art 9–11, Annex I, III |
| io.catenax.battery.battery_pass | Battery Regulation 2023/1542 | ANNEX VI, VII, VIII, XIII |
| io.catenax.pcf | WBCSD PACT Framework v3 | Technical Spec §4 |
| io.catenax.transmission.transmission_pass | ESPR 2024/1781 | Annex I (product-type) |
| io.catenax.electric_drive.electric_drive_passport | ESPR 2024/1781 | Annex I (product-type) |
