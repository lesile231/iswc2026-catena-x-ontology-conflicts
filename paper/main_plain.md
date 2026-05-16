# Detecting Cross-Use-Case Inconsistencies in Industrial Dataspace Ontologies: An Empirical Study of 123 Catena-X Aspect Models

**Sangil Lee**
Department of Computer Science, University of Liverpool, Liverpool, UK
ORCID: 0009-0001-8320-9728 | sgslee7@liverpool.ac.uk

*SemIIM 2026: 4th International Workshop on Semantic Industrial Information Modelling, co-located with ISWC 2026, October 25--29, 2026, Bari, Italy*

---

## Abstract

Catena-X, a European data space initiative for the automotive industry, defines 123 Aspect Models using the Semantic Aspect Meta Model (SAMM) to standardize data exchange across use cases such as Traceability, Product Carbon Footprint (PCF), Digital Product Passport (DPP), and Quality Management. However, as these models were developed independently by different working groups, semantically equivalent data properties---such as part identification, manufacturing information, and material composition---are often defined with incompatible types, structures, or naming conventions across use case boundaries. This fragmentation creates concrete interoperability barriers for organizations that must implement multiple use cases simultaneously.

We present a systematic methodology for automatically detecting and classifying cross-use-case property overlaps across the entire Catena-X Aspect Model corpus. Analyzing 123 models (2,217 properties), we identify 249 overlapping property names, of which 40 exhibit type-level conflicts. We introduce a three-tier classification---IDENTICAL, COMPATIBLE, and CONFLICT---and provide in-depth case studies of critical conflicts, including the Battery Pass vs. Digital Product Passport model pair where EU regulatory divergence (Battery Regulation 2023/1542 vs. ESPR 2024/1781) manifests as incompatible ontological structures. Our findings provide the first quantified evidence of a theoretically expected pattern: regulatory divergence creating structural fault lines in modular industrial ontologies. We discuss implications for Catena-X governance and for the broader landscape of multi-jurisdictional data ecosystems.

**Keywords:** Catena-X, SAMM, Aspect Models, Ontology Modularization, Semantic Interoperability, Digital Product Passport, Regulatory Ontology, Automotive Data Ecosystems

---

## 1. Introduction

The European automotive industry is undergoing a transformation toward shared data ecosystems, driven by regulatory frameworks such as the EU Battery Regulation 2023/1542 [1] and the Ecodesign for Sustainable Products Regulation (ESPR) 2024/1781 [2]. These regulations mandate digital traceability and product passports across the supply chain, creating an unprecedented need for semantic interoperability among thousands of organizations exchanging standardized data about parts, materials, and manufacturing processes.

Catena-X, the European automotive data space initiative [3], addresses this need through a federated architecture grounded in 123 standardized *Aspect Models* defined using the Semantic Aspect Meta Model (SAMM) [4, 5]. Each Aspect Model represents a logical view of data exchanged for a specific use case---Traceability, Digital Product Passport (DPP), Quality Management, Product Carbon Footprint (PCF)---developed independently by domain working groups within the Catena-X ecosystem.

This independent development creates a hidden interoperability challenge: semantically equivalent concepts---part identification, manufacturing information, material composition---are often modeled with incompatible types or structures across use case boundaries. The challenge intensifies when different use cases must satisfy different external regulatory requirements: a property named `materials` answers the question "What hazardous substances are present?" under the Battery Regulation, but answers "What is the recycled content?" under ESPR. While the Catena-X standard CX-0143 [6] explicitly recommends reuse of generic DPP modules, no automated mechanism currently verifies cross-model consistency, and the community has identified this as an open governance challenge [7].

This raises the empirical question: *How prevalent are property-level inconsistencies across the Catena-X Aspect Model corpus, and to what extent does regulatory divergence drive these inconsistencies?*

We address this question through a systematic empirical analysis of all 123 Catena-X Aspect Models (2,217 properties in 279 Turtle files), classifying cross-use-case overlaps into three structural tiers---IDENTICAL, COMPATIBLE, and CONFLICT---and conducting in-depth case studies of the most critical conflicts.

This paper makes three contributions:

1. The first systematic empirical analysis of cross-use-case property overlap across 123 Catena-X Aspect Models, comprising 2,217 properties and 249 cross-model overlaps. To our knowledge, this is the first quantitative measurement of inter-model consistency in an industrial dataspace ontology at this scale.
2. A three-tier overlap taxonomy (IDENTICAL, COMPATIBLE, CONFLICT) with an automated detection pipeline, grounded in established modular ontology theory [8, 9] but specialized for SAMM's two-level type structure (Characteristic + dataType).
3. In-depth case studies---including the Battery Pass and Digital Product Passport pair---revealing that regulatory-driven structural divergence accounts for at least half of the true conflicts in our validated sample. Grounded in modular ontology theory and our empirical findings, we argue that regulatory-driven structural divergence constitutes a systematic challenge for modular ontologies operating under heterogeneous regulatory regimes, with implications beyond Catena-X.

---

## 2. Background

SAMM [4] is a meta-model built on RDF/Turtle that defines the structural primitives for Catena-X Aspect Models. Its core concepts are: **Aspect** (the top-level model), **Property** (a named data attribute), **Characteristic** (the type contract of a Property, e.g., `Text`, `Timestamp`, or a custom type), **Entity** (a structured complex type referenced by a Characteristic), and **Trait** (a Characteristic with added constraints such as regex patterns or value ranges) [5].

A key design feature of SAMM is its *two-level type system*: a Property's effective data type is determined by its Characteristic, which either maps to an `xsd` primitive (e.g., `xsd:string`, `xsd:dateTime`) or references a complex Entity definition with its own nested Properties. This two-level indirection---Property → Characteristic → dataType---means that two properties sharing the same name may agree on the Characteristic class but diverge on the underlying dataType, or vice versa. This distinction is central to our three-tier overlap classification (Section 5.2).

SAMM models may reference definitions from other models via namespace imports, enabling reuse of shared Characteristics and Entities across use cases. However, imports are version-pinned: a model importing `v5.0.0` of another model's namespace will not automatically receive changes introduced in `v6.0.0` or later, creating potential compatibility drift when the imported model evolves independently.

The Catena-X model corpus [3] is maintained in a public GitHub repository organized as a flat namespace: each model resides in a directory named `io.catenax.{domain}.{model_name}`, with versioned subdirectories containing Turtle files and a `metadata.json` file indicating status (`release`, `draft`, or `deprecated`). As of our analysis, the repository contains 123 models (109 release, 14 draft/other) spanning use case domains including Traceability, PCF, DPP, Quality Management, Supply Chain Management, and Circular Economy. Twenty shared modules (`io.catenax.shared.*`) provide reusable definitions, though their adoption varies across use cases. Each domain is governed by an independent working group, and no automated mechanism enforces cross-domain property consistency.

---

## 3. Related Work

**Modular ontology design.** The challenge of maintaining consistency across modular ontologies is well-established. Shimizu et al. [10] formalize Modular Ontology Modeling (MOM) with patterns for module composition, while Stuckenschmidt et al. [9] address knowledge modularization theory. Oh and Ahn [8] propose cohesion and coupling metrics for ontology modules, and Keet [11] provides a comprehensive treatment of ontology engineering principles including modularization. These works establish theoretical foundations but do not empirically analyze large-scale industrial ontology corpora for cross-module consistency.

**Industrial semantic modeling.** Recent workshop contributions have addressed semantic modeling for industrial data exchange, both through integration architectures [12, 13] and domain-specific vocabularies [14, 15, 16]. However, each operates within a single domain and does not examine cross-domain consistency within a shared ecosystem---the central focus of our work.

**DPP ontologies and Catena-X.** The Digital Product Passport has motivated ontology network design. Jansen et al. [17] design an ontology network for DPPs using modular ontology patterns, proposing a top-level structure that connects product, process, and regulatory concepts. Deich et al. [18] address the complementary problem of connecting general DPP frameworks with domain-specific information sources, demonstrating mappings between DPP ontologies and building information models. Both works focus on *designing* coherent ontology structures but do not empirically measure whether existing deployed models actually achieve consistency. Staebler et al. [19] come closest to our concern by identifying "ontological dissonance" in ecosystem platforms using network graph analysis, though their approach operates at the model level rather than the property level and does not quantify type-level conflicts. Ishihara and Matsutsuka [20] compare data space implementations between Japan and Europe, confirming that cross-border interoperability challenges mirror the cross-use-case challenges we examine within a single ecosystem.

**Gap.** Existing work addresses either inter-ontology alignment (across different ontologies) or domain-specific modeling (within a single use case). What remains unexamined is *intra-ecosystem cross-use-case consistency*---whether independently developed models within the same data space define overlapping concepts consistently. Our work fills this gap with the first corpus-wide empirical analysis of property-level overlaps across Catena-X Aspect Models.

---

## 4. Methodology

### 4.1 Data Collection

We cloned the Catena-X Semantic Models repository [3] and selected the latest release version of each model. When no release version existed, we used the latest available version regardless of status. This yielded 123 models (109 release, 14 draft/other) containing 279 Turtle files.

### 4.2 Property Extraction

We built a Python pipeline using RDFLib [21] that parses each model's Turtle files and extracts all SAMM Property instances. The pipeline handles both SAMM 2.0.0 and 2.1.0 namespace URIs, which coexist in the corpus. For each property, we extract: `local_name` (the fragment identifier), `preferredName`, `description`, `characteristic` (the Characteristic class name), `dataType` (the resolved `xsd` type or Entity name), and `exampleValue`. Properties imported from external namespaces (e.g., `io.catenax.shared.*`) are excluded from the defining model's count to avoid double-counting shared definitions.

### 4.3 Overlap Detection and Classification

We group all extracted properties by `local_name` across models. Properties appearing in two or more models constitute an overlap. Each overlap is classified into one of three tiers:

- **IDENTICAL**: same Characteristic class *and* same `xsd` dataType across all models.
- **COMPATIBLE**: same dataType but differently named Characteristics.
- **CONFLICT**: different dataTypes or structurally incompatible Entity definitions.

### 4.4 Manual Validation

To assess the semantic significance of detected conflicts, we manually examined the top 10 CONFLICT properties (ranked by affected model count). For each, we compared `preferredName`, `description`, and Entity structure across models to distinguish *true conflicts* (same real-world concept, incompatible types) from *coincidental name collisions* (unrelated concepts sharing a generic name) and *domain-justified divergences* (intentionally different types satisfying distinct external standards).

---

## 5. Findings

### 5.1 Quantitative Overview

We applied the extraction pipeline described in Section 4 to the 123 Catena-X Aspect Models (latest release version per model), parsing 279 Turtle files containing a total of 2,217 SAMM Property definitions. Table 1 summarizes the corpus-level statistics.

**Table 1.** Corpus-level statistics of the Catena-X Aspect Model analysis.

| Metric | Value |
|--------|-------|
| Aspect Models analyzed | 123 |
|   of which *release* | 109 |
|   of which *draft/other* | 14 |
| Shared modules (`io.catenax.shared.*`) | 20 |
| Turtle files parsed | 279 |
| Properties extracted | 2,217 |
| Unique property names | 1,759 |
| Overlapping property names (≥2 models) | 249 (14.2%) |
|   IDENTICAL | 114 |
|   COMPATIBLE | 95 |
|   CONFLICT | 40 |

Of the 1,759 unique property names, 249 (14.2%) appear in two or more models. While the majority of these overlaps are structurally consistent---114 IDENTICAL (same Characteristic and dataType) and 95 COMPATIBLE (same dataType, different Characteristic name)---40 property names exhibit type-level conflicts where the same name maps to different `xsd` dataTypes or structurally incompatible Entity definitions across models.

Table 2 lists the most severe conflicts ranked by the number of affected models. The property `value` appears in 13 models with four distinct dataTypes (`string`, `double`, `float`, `integer`), while `partTypeInformation` spans 7 models with two incompatible Entity structures.

**Table 2.** Top CONFLICT properties by number of affected models.

| Property | Models | DataTypes | Example types |
|----------|--------|-----------|---------------|
| `value` | 13 | 4 | string, double, float, integer |
| `quantity` | 8 | 2 | Quantity (entity), float |
| `partTypeInformation` | 7 | 2 | PartTypeInformationEntity, PartTypeEntity |
| `key` | 7 | 2 | anyURI, string |
| `version` | 6 | 3 | VersionEntity, string, nonNegativeInteger |
| `weight` | 5 | 2 | {value,unit} entity vs. scalar double/float |
| `childItems` | 5 | 2 | ChildData, ChildItemEntity |
| `type` | 5 | 3 | CertificateTypeEntity, TypeEntity, string |
| `changedAt` | 5 | 2 | dateTime, dateTimeStamp |

**Overlap distribution.** The distribution of overlapping properties exhibits a strong long tail: 155 of 249 overlapping properties (62.2%) are shared by exactly 2 models, while only 25 (10.0%) appear in 5 or more models (Figure 1). The mean model count per overlapping property is 2.84 (median 2, σ = 1.72). Notably, the proportion of CONFLICT classifications increases with model count: among properties shared by 2 models, 11.0% are CONFLICT (17/155), while among properties shared by 5+ models, 24.0% are CONFLICT (6/25). This suggests that widely reused property names face greater type divergence pressure as more working groups independently define them.

*Figure 1. Distribution of overlapping properties by number of models sharing the same property name, broken down by overlap classification.*

**Conflict type breakdown.** Of the 40 CONFLICT properties, 15 (37.5%) involve incompatible Entity definitions, 12 (30.0%) involve `xsd` dataType disagreements, and 13 (32.5%) are *mixed* conflicts where some models use a primitive type while others use a complex Entity---the most severe form of structural incompatibility (e.g., `quantity` is a scalar `float` in some models but a structured `Quantity` entity with `value` and `unit` fields in others).

**Cross-domain conflict analysis.** To understand whether conflicts arise from independent modeling by different teams or from divergence within a single domain, we mapped each model to one of nine use-case domains (Traceability, DPP, PCF, Quality, Supply Chain, Circular Economy, Manufacturing, Shared, and Other) and classified each conflict as cross-domain or within-domain. Of the 40 CONFLICT properties, 32 (80.0%) involve models from *different* domains, while only 8 (20.0%) are within-domain conflicts---5 of which occur within the DPP domain (between Battery Pass, Electric Drive Passport, and Transmission Pass sub-models). The domain pairs with the most conflicts are Circular Economy--Other (9), Circular Economy--Shared (7), and DPP--Other (5), suggesting that domains with the least alignment to shared vocabularies are most prone to property conflicts.

### 5.2 Overlap Classification and Severity

Our three-tier classification captures structurally distinct overlap patterns. We define each tier and illustrate with examples from the corpus:

**IDENTICAL.** The same property local name maps to the same Characteristic class and the same `xsd` dataType across all models in which it appears. For example, `description` uses the `Text` Characteristic with `xsd:string` in all 8 models that define it. These overlaps represent successful de facto standardization, even when models do not explicitly import a shared definition.

**COMPATIBLE.** The property shares the same dataType across models but uses differently named Characteristics. For instance, `catenaXId` appears in 16 models, always representing a Catena-X UUID identifier, but through Characteristics named `UuidV4Trait` (13 models) or `CatenaXIdTrait` (3 models). Notably, even this identifier---required by the Industry Core KIT [22] and arguably the most fundamental shared concept in the ecosystem---exhibits Characteristic divergence. While data-level interoperability is preserved, schema-level tooling cannot automatically recognize these as equivalent without additional mapping.

**CONFLICT.** The property name maps to different dataTypes or structurally incompatible Entity definitions across models. We further distinguish two sub-categories through manual validation:

- **True conflict:** Properties that represent the same real-world concept but are modeled with incompatible types. Example: `weight` represents physical mass in all five models, but three use a complex `{value, unit}` entity via `MassCharacteristic`, while two use a scalar `xsd:double`/`float` with a fixed kilogram unit. A second critical true conflict is `partTypeInformation`, which appears in 7 models with two incompatible Entity definitions: `PartTypeInformationEntity` (used by 6 Traceability/Planning models) includes `partClassification`, while DPP's `PartTypeEntity` omits it to align with ESPR 2024/1781 [2], creating a structurally incompatible simplification.
- **Domain-justified divergence:** Properties where the name collision is coincidental or where different types are intentionally chosen to satisfy distinct domain standards. Example: `version` uses `xsd:nonNegativeInteger` in the PCF model (mandated by WBCSD/PACT [23]), `xsd:string` with SemVer constraint in the message header, and a complex `VersionEntity` in engineering master data. These represent three genuinely different concepts that happen to share a name.

Distinguishing true conflicts from domain-justified divergences is essential because the corrective action differs: true conflicts demand harmonization or shared module reuse, while domain-justified divergences indicate that property names should be qualified with domain-specific prefixes (e.g., `footprintVersion`, `messageVersion`) rather than left ambiguous.

Of the 40 CONFLICT properties, our manual examination of the top 10 (by model count) identified approximately 6 as true conflicts and 4 as domain-justified divergences (where same-named properties intentionally use different types to satisfy distinct domain standards).

**Theoretical grounding.** Our classification draws on Oh and Ahn's [8] cohesion-coupling tension in modular ontologies, specializing it for SAMM's two-level type system: where they measure cohesion within a single module, we measure *consistency across modules* sharing a property name. The IDENTICAL/COMPATIBLE/CONFLICT distinction parallels Stuckenschmidt et al.'s [9] structural vs. semantic compatibility, applied at the property level.

### 5.3 Case Study: Battery Pass and Digital Product Passport

We selected the Battery Pass (BP, v6.1.0) and Digital Product Passport (DPP, v7.0.0) model pair for in-depth analysis because (1) they share 9 property names, making them the highest-overlap cross-domain pair in the corpus; (2) both implement EU regulatory requirements, making their relationship a concrete test of standard compliance; and (3) the Catena-X standard CX-0143 [6] explicitly positions BP as a domain-specific extension of the generic DPP framework. This pair represents a critical instance where structural divergence coincides with regulatory divergence. Of the approximately 6 true conflicts identified among the top 10 most-affected CONFLICT properties (Section 5.2), at least 3 coincide with differences in EU regulatory requirements (Battery Regulation 2023/1542 [1], ESPR 2024/1781 [2]). These span two distinct case patterns: `materials` and `characteristics` in the BP--DPP pair, plus `partTypeInformation` across Traceability, Planning, and DPP models. Together, they account for at least half of the true conflicts in this validated subset.

**Import relationship.** Battery Pass v6.1.0 imports the DPP namespace at version 5.0.0, but the current DPP release is v7.0.0---two major versions ahead. Properties added or modified in DPP v6.0.0 and v7.0.0 are invisible to Battery Pass consumers, creating a silent compatibility drift.

**Shared property classification.** Table 3 classifies the 9 shared properties. Seven are IDENTICAL (both models use the same Characteristic and Entity name) and two exhibit CONFLICT: `characteristics` and `materials`.

**Table 3.** Shared properties between Battery Pass (v6.1.0) and Digital Product Passport (v7.0.0).

| Property | BP type | DPP type | Class |
|----------|---------|----------|-------|
| operation | OperationEntity | OperationEntity | IDENT |
| sustainability | SustainabilityEntity | SustainabilityEntity | IDENT |
| physicalDimension | PhysDimensionEntity | PhysDimensionEntity | IDENT |
| specVersion | Text | Text | IDENT |
| sources | SourceList | SourceList | IDENT |
| carbonFootprint | FootprintList | FootprintList | IDENT |
| identification | IdentificationEntity | IdentificationEntity | IDENT |
| **characteristics** | **CharacteristicsEntity** | **ProductCharEntity** | **CONFL** |
| **materials** | **ChemicalMaterialEntity** | **MaterialEntity** | **CONFL** |

**Conflict analysis: `characteristics`.** Battery Pass defines `characteristics` as an entity with two properties: `warranty` and `physicalDimension`. DPP defines it with five properties: `lifespan`, `physicalDimension`, `physicalState`, `generalPerformanceClass`, and `otherCharacteristics`. While `physicalDimension` appears in both, the surrounding structure is incompatible---a system consuming DPP data cannot parse BP's `characteristics` without transformation, and vice versa.

**Conflict analysis: `materials`.** The divergence in `materials` reveals a deeper semantic conflict rooted in regulatory differences. Battery Pass, governed by the Battery Regulation (EU) 2023/1542 [1], models materials as chemical composition: `activeMaterials`, `hazardousSubstance`, and `materialComposition` (3 properties). DPP, governed by the Ecodesign for Sustainable Products Regulation (ESPR) (EU) 2024/1781 [2], models materials from a circular economy perspective: `recycled`, `materialType`, `materialOrigin`, `substancesOfConcernInformation`, and 7 additional properties (10+ total). The same property name `materials` answers fundamentally different regulatory questions: "What hazardous substances does this battery contain?" vs. "What is the recycled content percentage of this product?"

**Regulatory roots of ontological divergence.** The structural conflicts between BP and DPP align with fundamental differences in their governing regulations. The Battery Regulation (EU) 2023/1542 targets a specific product category (batteries and waste batteries) and mandates detailed chemical composition disclosure, including critical raw materials and hazardous substances. The ESPR (EU) 2024/1781, by contrast, is a horizontal framework regulation applicable to all products placed on the EU market, emphasizing circular economy metrics such as recycled content, durability, and repairability. Although both regulations require digital product passports and share a 2027 implementation timeline, they address different policy objectives---chemical safety and raw material traceability for batteries vs. circular economy metrics for all products---creating divergent data requirements. Within Catena-X, these regulations are implemented by separate working groups (Battery Passport WG and DPP WG) with independent development cycles, as evidenced by the version gap between BP's import of DPP v5.0.0 and the current DPP v7.0.0 release. This organizational separation correlates with the regulatory separation, though we note that establishing a causal link would require comparative analysis across multiple regulatory pairs and ecosystems.

**Broader implications.** The BP--DPP case instantiates a general theoretical concern: when modules answer to different external normative sources, the module boundary becomes a structural fault line rather than a clean interface. Empirical verification in other ecosystems remains future work.

### 5.4 Limitations

We identify three methodological limitations, each suggesting a direction for future work.

**L1: Name-based detection.** Our pipeline relies on property local name matching, which introduces both false positives and false negatives. Generic property names such as `value` (13 models) and `key` (7 models) represent coincidental name collisions---distinct concepts that happen to share a name across unrelated domains. For example, `value` denotes a monetary amount in `customs_information`, a measurement result in `certificate_of_analysis`, and a key-value pair value in `serial_part`. Of the 40 CONFLICT properties, we estimate that approximately 10--15 fall into this category, distinct from the domain-justified divergences identified in Section 5.2 where the name collision is intentional. Conversely, semantically equivalent properties with different names are invisible to our pipeline. For example, `manufacturerPartId` (defined locally in 4 models) and `partId` could represent the same concept but would not be detected as overlapping.

**L2: Cross-version analysis.** Our pipeline analyzes only the latest release version of each model. However, cross-model imports may reference older versions, as demonstrated by the Battery Pass importing DPP v5.0.0 while v7.0.0 is current (Section 5.3). A comprehensive analysis should compare all version pairs referenced by cross-model imports to identify version-induced compatibility gaps.

**L3: Semantic similarity.** Our detection is purely syntactic (property local name matching). NLP-based semantic similarity analysis on `preferredName` and `description` fields could detect additional overlaps among differently named but semantically equivalent properties (e.g., `productionDate` and `date` labeled "Production Date" in `serial_part`). Quantifying these false negatives would provide a more complete picture of the true overlap landscape.

**Implications.** These limitations suggest that the 40 CONFLICT figure represents a lower bound for true interoperability issues (undetected semantic overlaps) while simultaneously overstating severity (coincidental name collisions). A more precise estimate would require NLP-based description similarity and multi-rater validation.

---

## 6. Discussion

### 6.1 Governance Implications for Catena-X

Our findings suggest four concrete recommendations for the Catena-X model governance process:

1. **Domain-prefixed naming** for generic property names. Properties like `version` and `value` should carry domain prefixes (e.g., `footprintVersion`, `passportVersion`) to avoid coincidental name collisions.
2. **Mandatory shared Characteristic reuse** for common concepts. The 20 existing shared modules (`io.catenax.shared.*`) are underutilized---for example, `weight` is defined with three incompatible patterns across five models despite a shared physical dimension module being available.
3. **Cross-model consistency checks** integrated into the CI/CD pipeline. Our detection pipeline could be adapted as a GitHub Actions check that flags new CONFLICT properties before merge.
4. **Version dependency tracking** for cross-model imports. The Battery Pass importing DPP v5.0.0 while v7.0.0 is current (Section 5.3) illustrates the need for automated dependency staleness alerts.

### 6.2 Impact on Multi-Use-Case Implementers

The 40 type-level conflicts have practical consequences for organizations implementing multiple Catena-X use cases. Each conflict requires manual data mapping or transformation logic, and the effort multiplies with the number of use cases adopted. For example, an organization implementing Traceability, DPP, and PCF simultaneously must build a scalar-to-entity adapter for `quantity` between PCF (scalar `float`) and Traceability (structured `Quantity` entity with `value` and `unit` fields). These mapping layers accumulate silently and become a maintenance burden when upstream models release new versions.

This burden falls disproportionately on SMEs---Tier 2/3 automotive suppliers who lack dedicated semantic engineering teams and rely on off-the-shelf connectors that assume structural consistency across models. The 80% cross-domain conflict rate (Section 5.1) suggests that this assumption is systematically violated.

### 6.3 Generalizability

We distinguish two dimensions of generalizability: methodological and empirical.

**Methodology.** Our pipeline is applicable to any modular ontology ecosystem with typed property definitions on an RDF-based meta-model; adapting it requires only replacing SAMM-specific namespace resolution. Emerging data space initiatives such as Manufacturing-X and Ouranos [20] adopt similar architectures and are likely candidates for analogous analysis.

**Empirical findings.** While our empirical evidence is drawn from a single ecosystem, the underlying mechanism we identify---regulatory divergence creating structural fault lines at module boundaries---is theoretically applicable to any modular ontology operating under heterogeneous regulation. Healthcare data spaces such as the European Health Data Space (EHDS), which must reconcile cross-border privacy frameworks and national clinical coding standards, or financial data spaces navigating tensions between Basel III and national reporting standards, face structurally analogous conditions. The Catena-X case provides empirical evidence that this mechanism manifests concretely at the property level, even when a shared governance body (Catena-X e.V.) coordinates model development. Verification in other ecosystems such as Manufacturing-X and Ouranos is a natural next step.

---

## 7. Conclusion and Future Work

We presented the first systematic empirical analysis of cross-use-case property overlap across 123 Catena-X Aspect Models. Analyzing 2,217 properties, we identified 249 overlapping property names, of which 40 exhibit type-level conflicts. Our three-tier taxonomy---IDENTICAL, COMPATIBLE, and CONFLICT---provides a principled framework for classifying overlap severity, and our automated pipeline enables reproducible detection across evolving model corpora. Through in-depth case studies of the Battery Pass and Digital Product Passport model pair, we presented quantified evidence that regulatory-driven structural divergence---where EU regulations manifest as incompatible ontological structures---accounts for a substantial portion of the most severe conflicts in this corpus. We argue that the underlying mechanism---regulatory divergence creating structural fault lines at module boundaries---constitutes a systematic challenge for any modular ontology operating under heterogeneous regulation, warranting verification across other data space ecosystems.

Future work includes: (1) longitudinal tracking of property overlap evolution across model versions; (2) NLP-based semantic similarity analysis to detect false negatives (semantically equivalent properties with different names); (3) extension of the pipeline to other SAMM-based ecosystems such as Manufacturing-X; (4) cross-version import dependency analysis to identify compatibility gaps between referenced and current model versions; and (5) integration of automated consistency checks into the Catena-X CI/CD governance pipeline.

---

## Declaration on Generative AI

During the preparation of this work, the author(s) used Claude (Anthropic) in order to: code assistance for the RDFLib-based analysis pipeline and literature review support. After using these tools/services, the author(s) reviewed and edited the content as needed and take(s) full responsibility for the publication's content.

---

## References

[1] European Parliament and Council, Regulation (EU) 2023/1542 -- batteries and waste batteries, 2023.

[2] European Parliament and Council, Regulation (EU) 2024/1781 -- ecodesign for sustainable products (ESPR), 2024.

[3] Eclipse Tractus-X, Semantic layer digital twin -- semantic models, 2024. https://github.com/eclipse-tractusx/sldt-semantic-models

[4] Eclipse ESMF, Semantic Aspect Meta Model (SAMM) specification 2.1.0, 2024. https://eclipse-esmf.github.io/samm-specification/

[5] Catena-X Automotive Network e.V., CX-0003 SAMM Aspect Meta Model, Standard CX-0003, 2024. https://catenax-ev.github.io/docs/standards/CX-0003-SAMMSemanticAspectMetaModel

[6] Catena-X Automotive Network e.V., CX-0143 Use Case Circular Economy -- Digital Product Passport Standard, Standard CX-0143, 2024.

[7] Catena-X Automotive Network, Harmonization of Catena-X aspect models (issue #720), 2024. https://github.com/eclipse-tractusx/sldt-semantic-models/issues/720

[8] S. Oh, J. Ahn, Ontology module metrics, in: Proceedings of the IEEE International Conference on e-Business Engineering (ICEBE 2009), IEEE, 2009, pp. 11--18.

[9] H. Stuckenschmidt, C. Parent, S. Spaccapietra, Modular ontologies: Concepts, theories and techniques for knowledge modularization, in: Lecture Notes in Computer Science, Springer, 2009.

[10] C. Shimizu, K. Hammar, P. Hitzler, Modular ontology modeling, Semantic Web 14 (2023) 459--489.

[11] C. M. Keet, An introduction to ontology engineering, Computing Series, College Publications, 2023.

[12] I. Grangel-Gonzalez, M. Rickart, O. Rudolph, R. Dias, Towards a knowledge graph-based data mesh for smart manufacturing, in: Proceedings of SemIIM 2023, co-located with ISWC 2023, CEUR-WS.org, 2023.

[13] M. Milicic Brandt, F. Psarommatis, C. Simon, A. Waaler, B. Zhou, Towards standardised product data exchange with information modelling framework at Siemens Energy, in: Proceedings of SemIIM 2023, co-located with ISWC 2023, CEUR-WS.org, 2023.

[14] E. Tsalapati, M. Koubarakis, SHM: A light-weight, mid-level ontology for reliable system health monitoring, in: Proceedings of SemIIM 2023, co-located with ISWC 2023, CEUR-WS.org, 2023.

[15] M. Yahya, A. Breathnach, F. Khan, I. Abaspur, R. Ranganathan, Towards semantic modeling of camera from image quality testing perspective: Valeo vision systems case, in: Proceedings of SemIIM 2023, co-located with ISWC 2023, CEUR-WS.org, 2023.

[16] H. Tan, R. Kebede, A. Moscati, P. Johansson, Semantic interoperability using ontologies and standards for building product properties, in: Proceedings of LDAC 2024, CEUR-WS.org, 2024, pp. 23--35.

[17] M. Jansen, E. Blomqvist, R. Keskisaerkka, H. Li, M. Lindecrantz, K. Wannerberg, A. Pomp, T. Meisen, H. Berg, Designing an ontology network for digital product passports, in: The Semantic Web: ESWC 2024 Satellite Events, LNCS 15345, Springer, 2024.

[18] J. Deich, L. Loeb, A. Meins-Becker, T. Meisen, A. Pomp, Towards connecting general DPP ontology frameworks with domain-specific information sources, in: Proceedings of KG4S 2025, co-located with ESWC 2025, CEUR-WS.org, 2025, pp. 1--12.

[19] M. Staebler, F. Koester, C. Schlueter-Langdon, Towards solving ontological dissonance using network graphs, in: Proceedings of AMCIS 2023, 2023.

[20] S. Ishihara, T. Matsutsuka, Towards interoperable data spaces: Comparative analysis of data space implementations between Japan and Europe, 2025. arXiv:2501.15738.

[21] RDFLib Team, RDFLib: A Python library for working with RDF, 2024. https://rdflib.readthedocs.io/

[22] Catena-X Automotive Network e.V., Industry Core KIT, Technical Report, 2024.

[23] WBCSD/PACT, Pathfinder Framework: Guidance for the Accounting and Exchange of Product Life Cycle Emissions, Technical Guidance, 2024.
