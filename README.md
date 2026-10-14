# CodePulse

**Java code smell detection and refactoring assistance using rule-based analysis and machine learning.**

CodePulse is a final year software engineering project that helps developers look beyond a working interface and examine the maintainability of the underlying Java code. It focuses on identifying God Class and Feature Envy, presenting evidence, and helping users review supported refactoring proposals.

> **Project status:** This README describes the agreed project scope and planned workflow. A complete application source tree has not been reviewed for this document. Features, installation commands, infrastructure, and performance should be marked as implemented only after verification against the repository.

## Problem

An application can look polished while its internal code becomes difficult to understand, test, and change. Classes with too many responsibilities and methods that rely heavily on other classes can increase maintenance effort. Code smells are warning signs worth investigating; they do not automatically prove that the application is defective.

## Objectives

- Detect God Class and Feature Envy in Java repositories.
- Compare rule-based findings with predictions from trained ML models.
- Show source locations and supporting evidence for each finding.
- Help users prioritize maintenance work.
- Offer recommendations and previews for supported refactoring transformations.
- Preserve the original project while users review proposed changes.

## Target Code Smells

| Smell | Meaning | Analysis unit |
|---|---|---|
| God Class, also called Blob in some datasets | A class takes on too many responsibilities and has problematic cohesion or coupling. Size alone does not establish this smell. | Class |
| Feature Envy | A method relies heavily on another class's data, suggesting that responsibility may be misplaced. | Method |

Large Class, Long Method, and Data Class were considered during research. They are not included in the current two-smell implementation scope described here. Python support is a possible later extension after the Java workflow is complete and validated.

## Planned User Features

| Feature | User outcome |
|---|---|
| Submit a Java project | Provide a public GitHub repository URL or upload a ZIP archive. |
| Start and monitor analysis | Submit an analysis selection and inspect progress or errors. |
| Review findings | View detected smells and their source locations. |
| Inspect evidence | Review measured metrics, rule conditions, and available ML outputs. |
| Compare detectors | See where rule-based and ML findings agree or disagree. |
| Explore dependency and architecture findings | Inspect structural relationships extracted during analysis. |
| Review debt estimates and priorities | Review documented estimates; these are not guaranteed maintenance costs. |
| Export a report | Download an analysis report; supported formats remain to be confirmed. |
| Reopen saved analyses | Retrieve previous analysis results. |
| Review refactoring recommendations | Examine suggested changes for a selected finding. |
| Preview supported refactoring | Inspect a proposed change in a separate project copy. |
| Review validation and quality comparison | Inspect available checks and measured before/after differences. |
| Accept and download a proposal | Accept a specific reviewed proposal version and download its patch or project copy. |

The workflow does not include an automatic GitHub push. Login, administrator roles, and private repository access have not been established in the current requirements.

## Analysis Workflow

1. **Receive project:** retrieve a public repository or extract the submitted ZIP.
2. **Validate input:** identify supported Java source and report input problems.
3. **Parse source:** identify classes, methods, source locations, and relevant structural relationships.
4. **Extract features:** calculate the measurements required by the selected rules and trained models.
5. **Run rules:** evaluate documented, versioned detection conditions.
6. **Run ML inference:** apply the matching trained models and preprocessing to the extracted features.
7. **Assemble results:** preserve each detector's findings and evidence so disagreements remain visible.
8. **Present and save results:** display findings, support report export, and retain analyses for later review.
9. **Assist refactoring:** prepare recommendations, preview supported changes, validate the proposed copy, and compare quality measurements.
10. **Review and download:** record acceptance of the reviewed proposal version before delivering accepted output.

Exact parser libraries, feature definitions, rule thresholds, fusion policy, and refactoring transformations must be documented when implemented. No automatic agreement rule, such as taking the union or intersection of detector outputs, is assumed here.

## Rule-Based and ML Detection

### Rule-based detection

Rules evaluate explicit conditions over code measurements. Each finding should identify the rule version, source location, measured values, and applicable thresholds. Thresholds must be justified and selected using development data, then frozen before final testing.

### Machine learning

The initial training plan uses **Python, pandas, NumPy, and scikit-learn in Google Colab**. Logistic Regression, Decision Tree, and Random Forest are candidate baseline models. Model selection depends on validation results rather than an assumption that ML is always better.

**Python is the training language; Java is the language being analyzed.** Training examples contain measurements extracted from Java code and corresponding smell labels.

God Class and Feature Envy require different analysis units and potentially different feature sets. Separate models are planned unless evaluation supports another approach.

### Why use both?

Rules provide an explainable baseline. ML can learn combinations of features that a simple threshold may miss. Comparing both approaches establishes whether ML provides a measurable benefit on unseen projects. If a rule performs better for a particular smell, the final detector policy should reflect that evidence.

## Dataset Research Resources

These are candidate research resources, not a claim that all have been merged into the final training dataset. Inspect each resource's labels, feature availability, provenance, and reuse conditions before use.

| Resource | Relevant smell coverage | Source |
|---|---|---|
| MLCQ | Blob/God Class, Feature Envy; also Long Method and Data Class | [Zenodo](https://zenodo.org/records/3666840) |
| Fontana / ESSeRE benchmark | God Class, Feature Envy; also Long Method and Data Class | [University resource page](https://essere.disco.unimib.it/machine-learning-for-code-smell-detection/) |
| Crowdsmelling | God Class, Feature Envy, Long Method | [Zenodo](https://zenodo.org/records/6555241) |
| DeepSmellDetection | Feature Envy, Large Class, Long Method, and Misplaced Class; Large Class is not automatically equivalent to God Class | [GitHub](https://github.com/liuhuigmail/DeepSmellDetection) |
| Škipina prepared MLCQ subset | Feature Envy and Data Class | [GitHub](https://github.com/milica-skipina/ML-code-smell-detection) |
| ImprovMLCQ | Derived MLCQ resource covering Blob/God Class, Feature Envy, Long Method, and Data Class | [Zenodo](https://zenodo.org/records/14834187) |
| SmellyCode++ | God Class, Feature Envy, Long Method, and Data Class | [Research paper](https://www.nature.com/articles/s41597-025-05465-z) and [repository](https://github.com/nawafalomari/code_smell_detection_dataset) |

### Dataset handling

- Count CSV records with a CSV parser, not physical text lines. Code cells may contain embedded newlines.
- Distinguish annotation rows from unique code artifacts.
- Preserve project identity, artifact identity, source version, and label provenance where available.
- Inspect conflicting labels and repeated annotations before choosing an aggregation policy.
- Check overlap between original resources, derived subsets, and combined corpora.
- Do not treat the sum of resource counts as a count of unique training examples.
- Keep duplicate or related artifacts out of different evaluation splits.
- Do not equate different smell names or metric definitions without supporting evidence.

## Training and Evaluation Plan

1. Load and inspect the selected dataset, columns, labels, and missing values.
2. Confirm that the label represents the intended smell and analysis unit.
3. Establish reproducible Java feature extraction matching the training measurements.
4. Deduplicate artifacts and resolve annotation policy while preserving provenance.
5. Split by project into training, validation, and held-out testing groups. If project identifiers are missing, disclose the resulting evaluation limitation.
6. Fit preprocessing only on training data. Apply imbalance handling only within training folds.
7. Train candidate models and tune rules using training and validation data.
8. Choose thresholds and model settings before evaluating the held-out test set.
9. Report positive-class precision, recall, F1, PR-AUC, MCC, and the confusion matrix, together with class counts.
10. Compare rules and ML on the same held-out examples. Avoid reporting accuracy alone.
11. Save the selected model with its preprocessing, feature order, label mapping, decision threshold, dependency versions, and evaluation record.
12. Integrate inference into CodePulse and confirm that application feature extraction matches training.

No final CodePulse accuracy or superiority claim is made in this README. Any results added later must identify the dataset, split method, sample counts, and evaluation limitations.

## Refactoring Workflow

Refactoring assistance depends on supported transformations. A recommendation is not a guarantee that an automatic transformation is safe.

- Generate the proposed change in a separate copy.
- Show the change and the affected source locations.
- Run available validation checks and label each as passed, failed, unavailable, or skipped.
- Compare documented quality measurements before and after the change.
- Require review and acceptance of the specific proposal version.
- Deliver an accepted patch or project copy without automatically modifying the upstream repository.

Improved metrics alone do not prove preservation of program behavior. Validation limitations must remain visible to the user.

## Architecture and Storage

The intended responsibilities are:

| Responsibility | Purpose |
|---|---|
| User interface | Project submission, progress, results, reports, and proposal review. |
| Application processing | Input handling, Java parsing, feature extraction, rules, ML inference, and refactoring assistance. |
| Persistence | Saved analyses and implementation-defined project artifacts. |

Saved analysis storage is required by the history workflow. The actual backend framework, database, API endpoints, storage format, and source-copy retention policy are not yet verified. Proposed UML classes or DFD processes are design artifacts and should not be described as confirmed source-code components.

## Getting Started

### ML training environment

1. Open [Google Colab](https://colab.research.google.com/).
2. Create a notebook named `CodePulse_GodClass_Training.ipynb`.
3. Use the default CPU runtime for initial metric-based baseline experiments.
4. Run the environment check below.
5. Upload one selected dataset and inspect its schema before training.

```python
import sys
import pandas as pd
import numpy as np
import sklearn

print("Python:", sys.version.split()[0])
print("pandas:", pd.__version__)
print("NumPy:", np.__version__)
print("scikit-learn:", sklearn.__version__)
```

Record these versions with the experiment. Save notebooks, datasets, and model artifacts outside the temporary runtime.

### Application installation

Repository-specific installation and launch instructions must be added after the application's dependency manifests and entry points are confirmed. No unverified build command, server command, directory layout, or repository URL is supplied here.

## Current Scope and Limitations

- Java is the first analysis language.
- God Class and Feature Envy are the current target smells.
- Dataset labels may contain annotator disagreement or tool bias.
- Metrics must be defined consistently between training and application inference.
- Incomplete type resolution can affect dependency-based findings, particularly Feature Envy.
- Predictions identify suspected smells, not proven bugs.
- Debt estimates need a documented method and cannot be presented as guaranteed time or cost.
- Refactoring coverage and validation depend on supported transformations and available checks.
- Cross-language transfer to Python requires separate evaluation; Java performance does not establish Python performance.

## Roadmap

- [ ] Inspect and select the initial God Class training dataset.
- [ ] Establish reproducible feature extraction and project-level evaluation splits.
- [ ] Train and compare baseline models and rules for God Class.
- [ ] Train and compare baseline models and rules for Feature Envy.
- [ ] Integrate Java project submission and hybrid analysis.
- [ ] Implement findings, evidence, detector comparison, saved analyses, and export.
- [ ] Implement supported refactoring previews and validation.
- [ ] Evaluate the end-to-end workflow on unseen Java projects.
- [ ] Consider Python support after completing the Java scope.

These items are planning milestones. Update their completion status using repository evidence.

## Academic Project

**Project:** CodePulse  
**Purpose:** Final year project in software engineering  
**Focus:** Explainable Java code smell detection, empirical comparison of rules and ML, and reviewable refactoring assistance.

## License and Attribution

A project license has not been specified. Add the chosen license to the repository before describing reuse permissions. Dataset and third-party component licenses apply separately; cite the resources used and comply with their terms.
