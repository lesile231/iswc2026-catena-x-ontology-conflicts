# Classification Rubric for CONFLICT Properties

A property in the CONFLICT class (same name, different dataType/Characteristic across models) is classified into ONE of three categories.

---

## Category 1: Naming Collision

**ALL** of the following must hold:

- **C1.1**: The `description` fields in the two models refer to **genuinely different concepts** (not the same concept with different wording).
- **C1.2**: Unifying the property would **not resolve a real interoperability issue** — the use cases are truly separate and never need to exchange this data.
- **C1.3**: Beyond the property name, there is **no shared semantic intent** (different preferredName phrasing, different parent Entities, different use-case domains).

**Example**: `value` — model A uses it for "price of an item" (float), model B for "identifier string" (string). These are entirely different concepts that happen to share the name "value."

**Decision test**: "If two systems exchanged this property, would a type mismatch cause a real problem?" → If **no** (because they'd never exchange it), then Naming Collision.

---

## Category 2: True Conflict — Regulatory-driven

**ALL** of the following must hold:

- **C2.1**: The two models refer to the **same or closely adjacent concept** (confirmed by comparing `description`, `preferredName`, and parent Entity context).
- **C2.2**: A **structural difference** exists (different xsd dataType, different Characteristic, or incompatible Entity composition).
- **C2.3**: The structural difference is **traceable to an external regulation's specific requirement**:
  - **C2.3a**: At least one model is a **regulatory model** (see list below).
  - **C2.3b**: A **specific article, annex, or section** of the regulation justifies that model's structural choice. This must be cited.
- **C2.4**: Resolving the difference would require **regulatory mediation or cross-standard coordination** — a simple type mapping is insufficient.

### Regulatory models (as of 2024 corpus)

| Model | Regulation | Key provisions |
|-------|-----------|----------------|
| io.catenax.generic.digital_product_passport | ESPR 2024/1781 | Annex I, III |
| io.catenax.battery.battery_pass | Battery Regulation 2023/1542 | Annex VI, VII, VIII, XIII |
| io.catenax.pcf | WBCSD PACT Framework v3.0 | Technical Specs §4 |
| io.catenax.transmission.transmission_pass | ESPR 2024/1781 | Annex I (product-type) |
| io.catenax.electric_drive.electric_drive_passport | ESPR 2024/1781 | Annex I (product-type) |

### Sub-mechanisms

- **A. Regulation vs non-regulation**: Only one model is regulatory. The regulatory model requires a structured Entity; the non-regulatory model uses a simple type.
- **B. Cross-regulation divergence**: Both models are regulatory, but under **different regulations** (e.g., Battery Regulation vs ESPR).
- **C. Intra-regulation product-type divergence**: Both models are under the **same regulation** (ESPR) but for **different product types**, leading to different structural interpretations of the same requirement.

---

## Category 3: True Conflict — Non-regulatory

**ALL** of the following must hold:

- **C3.1**: The two models refer to the **same or closely adjacent concept** (same test as C2.1).
- **C3.2**: A **structural difference** exists (same test as C2.2).
- **C3.3**: The structural difference is **NOT traceable to regulatory requirements**. Root causes include:
  - Working group modeling convention differences
  - Model version evolution / drift
  - Implementation legacy (older model not updated)
  - Lack of cross-WG coordination on shared property names

---

## Edge Cases / Ambiguous

If a property does not clearly fit any category, mark it as **FLAG** with a note explaining the ambiguity. Flagged cases are prioritized in validation review.

Common ambiguities:
- Model is in the regulatory model list, but the specific property's structure is NOT driven by the regulation (coincidental involvement).
- Description wording suggests different concepts, but the underlying data would still conflict in a cross-model query.

---

## Validation Procedure

For each CONFLICT property:

1. Read `description` and `preferredName` from all involved models (source: `02_overlaps_detail.txt`).
2. Determine: same concept or different concept? → If different → **Category 1**.
3. If same concept: is at least one model a regulatory model? → If no → **Category 3**.
4. If yes: can the structural difference be traced to a specific regulatory provision? → Cite the article/annex.
   - If yes → **Category 2** (assign sub-mechanism A/B/C).
   - If no (regulatory model involved but not the cause) → **Category 3**.
5. Record classification with evidence in the validation spreadsheet.

---

## Output

- This rubric: `analysis/CLASSIFICATION_RUBRIC.md`
- Classification results: `analysis/validation_targets.csv`
- Per-case evidence notes: recorded in validation spreadsheet
