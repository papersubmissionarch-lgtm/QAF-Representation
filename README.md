# BIM-Based Representation of Façade-Related Qualitative Architectural Features

## Overview

This repository contains the research materials supporting the BIM-based computational representation of four façade-related Qualitative Architectural Feature (QAF) constructs:

- Regularity
- Symmetry-based balance
- Diversity
- Complexity

The repository accompanies the manuscript:

**“Automated BIM-Based Evaluation of Façade Composition: Representing Regularity, Symmetry, Diversity, and Complexity as Computational Reference Metrics.”**

The study represents the four theoretical QAF constructs as computable proxy metrics.

The implementation is provided as a research proof-of-concept developed in Autodesk Revit and Dynamo. It is intended to support transparency, computational verification, and reproducibility of the procedures described in the manuscript.

## Repository Contents

### Dynamo Scripts

| File | Description |
|---|---|
| `Regularity Representation Script.dyn` | Computes the regularity metric from directional nearest-neighbour centre-to-centre distances between façade window instances. |
| `Symmetry-Based Balance Representation Script.dyn` | Generates candidate vertical symmetry axes, detects mirrored component correspondences, selects the principal symmetry axis, and computes the symmetry-based balance metric. |
| `Diversity Representation Script.dyn` | Classifies façade components into element types, calculates their relative frequencies, and computes the normalised diversity metric using perplexity. |
| `Complexity Representation Script.dyn` | Decomposes the façade into primary massing, secondary horizontal and vertical groups, and individual component-type groups to compute the normalised complexity metric. |

### Revit Models

| File | Description |
|---|---|
| `QAF Components.rvt` | Library of prototype façade components with standardised parameters required for QAF rule execution. |
| `QAF Benchmark and Realworld Cases.rvt` | Revit model containing the hypothetical benchmark façades and real-world façade cases used for script verification and external comparison. |

### Supporting Files

| File | Description |
|---|---|
| `Questionnaire Responses (Group 1).xlsx` | Anonymised respondent-level data for Group 1, who evaluated the façade cases using 3D isometric representations. The file contains respondents’ professional characteristics, QAF ratings for all benchmark and real-world façade cases, summary statistics, BIM-generated values, rank-based comparison results, and value-difference metrics. Responses excluded during screening are retained in a separately identified section. |
| `Questionnaire Responses (Group 2).xlsx` | Anonymised respondent-level data for Group 2, who evaluated the façade cases using 2D elevation representations. The file contains respondents’ professional characteristics, QAF ratings for all benchmark and real-world façade cases, summary statistics, BIM-generated values, rank-based comparison results, and value-difference metrics. Responses excluded during screening are retained in a separately identified section. |

## Software Requirements

The implementation was developed and tested using:

- Autodesk Revit 2021
- Dynamo for Revit included with Autodesk Revit 2021

The Dynamo graphs use standard Dynamo and Revit nodes only.

No Python nodes or external Dynamo packages are required.

Because Revit model files are version-dependent, users should open the supplied `.rvt` files using Revit 2021 or a later compatible version.

## Model Preparation Requirements

The scripts operate on explicitly modelled and semantically classified façade components.

### Analysis View

The façade to be evaluated must be isolated within the dedicated Revit view:

`QAFs Representation`

Only elements visible within this view should contribute to the evaluation. When applying the scripts to another model, create a view with the same name and configure its visibility settings to isolate the target façade and its associated components.

### Component Classification

The shared type parameter `TypeOfComponent` is used as a consistent classification key across native and extended Revit modelling approaches.

The prototype implementation uses the following values:

- `Window`
- `Door`
- `CurtainWall`
- `FrenchWindow`
- `Balcony`
- `Canopy`
- `VerticalSolidExtrusion`
- `HorizontalSolidExtrusion`

These values should be preserved exactly when the scripts are applied to other models.

### Required Parameters

Depending on the QAF procedure, the scripts use the following model parameters:

- `TypeOfComponent`
- `Symmetrical`
- Decomposition-level parameters for complexity evaluation
- `GroupDepth`
- `GroupWidth`
- `Level` or `Reference Level`
- Component width and height parameters where applicable

The prototype components supplied in `QAF Components.rvt` already contain the required custom parameters.

Native and derived geometric information—including element IDs, centroids, bounding boxes, areas, and volumes—is queried or calculated automatically by the Dynamo scripts and does not require manual entry.

Parameter names used by the Dynamo graphs should not be renamed unless the corresponding parameter-retrieval nodes are also updated.

## Running the Scripts

### Using the Supplied Evaluation Model

1. Open `QAF Benchmark and Realworld Cases.rvt` in Autodesk Revit.
2. Open the Revit view named `QAFs Representation`.
3. Confirm that only the façade and components intended for evaluation are visible in the view.
4. Launch Dynamo from the Revit `Manage` tab.
5. Open the Dynamo graph corresponding to the required QAF metric.
6. Review the user-adjustable inputs and confirm that the default tolerance and threshold values are appropriate.
7. Run the Dynamo graph.
8. Review the numerical output and any graphical verification geometry generated in the Revit viewport.

### Using the Scripts with Another Revit Model

1. Create or identify the façade components required by the selected QAF procedure.
2. Bind the required shared parameters to the relevant Revit categories.
3. Assign the appropriate `TypeOfComponent` value to each component type.
4. Assign the required level, symmetry, and decomposition parameters.
5. Create a dedicated view named `QAFs Representation`.
6. Isolate the target façade and its associated components in this view.
7. Open and run the relevant Dynamo script.
8. Check intermediate graphical outputs before interpreting the final metric value.

The supplied scripts assume the modelling and classification conventions described above. Application to models using different categories, parameter names, units, or modelling strategies may require modification of the element-selection and parameter-retrieval nodes.

## Computational Outputs

### Regularity

The regularity script outputs a normalised regularity value:

`R%`

The value is derived from the metric Shannon entropy of directional nearest-neighbour centre-to-centre distances between façade window instances. Higher values indicate greater consistency in the spacing relationships represented by the adopted metric.

### Symmetry-Based Balance

The symmetry-based balance script outputs:

`SBB%`

The value represents the total area of elements contributing to symmetry around the selected principal vertical axis divided by the total area of the evaluated façade elements.

The script also generates graphical outputs showing candidate axes, the selected principal axis, and symmetry-contributing elements.

### Diversity

The diversity script outputs:

`D%`

The raw diversity value is calculated using perplexity based on the relative frequencies of façade component types. It is normalised using the maximum raw diversity value observed in the pooled calibration dataset:

`PPmax = 20.07`

### Complexity

The complexity script outputs:

`C%`

The raw complexity score aggregates non-redundant compositional groups across the implemented hierarchical decomposition levels. It is normalised using the maximum raw complexity value observed in the pooled calibration dataset:

`Cmax = 68`

The diversity and complexity normalisation baselines are context-calibrated reference values rather than universal upper limits and may require recalibration when the procedures are applied to substantially different façade datasets.

## Numerical Tolerances and Thresholds

The prototype implementation uses the following numerical settings:

| Procedure | Setting | Baseline value |
|---|---|---:|
| Symmetry-based balance | Candidate-axis merging tolerance | 0.15 m |
| Symmetry-based balance | On-axis positional tolerance | 0.15 m |
| Symmetry-based balance | Mirrored-element matching tolerance | 0.35 m |
| Regularity | Directional-distance rounding increment | 0.10 m |
| Complexity | `GroupDepth` threshold | 1.00 m |
| Complexity | `GroupWidth` threshold | 19.00 m |

The symmetry and regularity settings accommodate minor geometric inaccuracies in the prototype models. The complexity thresholds were selected according to the dimensional characteristics of the benchmark and real-world façade cases used in the study.

These values should be reviewed and, where necessary, recalibrated before the scripts are applied to façade models with substantially different scales, modelling accuracy, or compositional configurations.

## Verification and External-Comparison Data

The supplied Revit model contains:

- 15 hypothetical benchmark façades designed to test controlled variations in the individual QAF metrics.
- 4 real-world façade cases containing interacting compositional features.

The scripts were internally verified by comparing their outputs with manually calculated reference values based on the same computational procedures.

The external-comparison dataset contains architects’ visual assessments of the same façade cases under two representation modalities:

- Group 1: 3D isometric views
- Group 2: 2D elevations

The shared data files distinguish between:

- Raw anonymised responses
- Screening decisions
- Retained analysis responses
- Case-level median ratings
- BIM-generated metric values
- Rank-based comparison statistics
- Value-difference metrics

The external comparison examined correspondence between BIM-derived metrics and architects’ perceptual ratings. It should not be interpreted as validating one source as a ground truth against the other.

## Data Anonymisation

All publicly shared respondent data have been anonymised.

Direct identifying information, including names, email addresses, contact details, institutional identifiers, and original submission timestamps, has been removed. Respondents are represented using randomly assigned anonymous identifiers that cannot be linked to the original questionnaire records through the files provided in this repository.

Potential indirect identifiers have been removed or generalised where necessary to reduce re-identification risk. The datasets retain only the variables required to reproduce the screening procedure, descriptive respondent characteristics, and statistical comparisons reported in the manuscript.

## Reproducibility Notes

The repository is intended to enable inspection and reproduction of the computational procedures reported in the associated manuscript.

Exact results may depend on:

- Revit and Dynamo versions
- Model units
- Component geometry
- Parameter naming
- Category assignment
- View visibility settings
- Numerical tolerances
- Dynamo rounding behaviour

Users should first reproduce the results using the supplied Revit cases before adapting the scripts to independent models.

The repository does not currently provide:

- Automatic recognition of arbitrary façade components
- Automatic semantic classification of unprepared BIM models
- An integrated Revit add-in
- A real-time user-oriented feedback dashboard
- Validated design recommendations
- Universal context-independent QAF thresholds

## Citation

This repository accompanies a manuscript currently under anonymous peer review.

Citation details will be added following publication.

Until publication, the repository may be cited as:

> Anonymous authors. BIM-Based Representation of Façade-Related Qualitative Architectural Features: Dynamo Scripts, Revit Models, and External-Comparison Data. Research repository, 2026.

## Licence

The materials are currently provided for anonymous peer review, methodological transparency, and research reproducibility.

Formal licensing terms for the Dynamo scripts, Revit models, documentation, and anonymised datasets will be specified upon publication.

Users should not attempt to re-identify questionnaire respondents or combine the shared data with external information for that purpose.

## Repository Status

This repository contains the version of the research materials associated with the manuscript submitted for peer review.

Updates may be introduced to correct documentation, improve file organisation, or respond to peer-review comments. A versioned archival release will be created for the final accepted manuscript.
