# Thelio: CAD Reconstruction Workspace

**Thelio** is a streamlined environment designed to transform static reference geometry into dynamic, scriptable parametric models. By leveraging the `build123d` Python library, it integrates exported CAD data—such as Fusion 360 JSON and STEP files—into a code-based modeling pipeline.

Currently, the project focuses on the high-fidelity reverse engineering of sheet-metal brackets. It prioritizes dimensional validation against source data and produces clean outputs in STEP and STL formats, alongside interactive Plotly visualizations.

The primary demonstration, `PN_000668_v7.py`, reconstructs a complex T-bracket. Rather than using approximations, it utilizes exact sketch profiles recovered from Fusion 360 exports to rebuild features like bent flanges with surgical precision.

---

## Project Motivation

Traditional CAD interoperability is often fragmented. Native files are frequently locked to specific software or difficult to use in automated pipelines. Thelio treats these "final" exports as raw inputs for a repeatable reconstruction process:

*   **STEP Files:** Serve as the geometric ground truth.
*   **Fusion JSON:** Provides the underlying feature logic, sketch entities, and parameters.
*   **`build123d`:** Acts as the engine that converts this data into versionable, editable Python code.

This approach allows for easier inspection, better version control, and rapid regeneration without manual UI-based remodeling.

---

## Repository Overview

The workspace is organized around a focused reconstruction workflow:

| Component | Description |
| :--- | :--- |
| **`PN_000668_v7.py`** | The core script for rebuilding the T-bracket, including export and visualization logic. |
| **`*_export.json`** | Metadata from Fusion 360 used to recover dimensions and sketch definitions. |
| **`*.step`** | Reference geometry used for volumetric and dimensional benchmarking. |
| **`scratch/`** | Helper utilities for extracting parameters, summarizing sketches, and analyzing STEP volumes. |
| **`Results/`** | A directory for generated artifacts, logs, and experimental notebooks. |

---

## The Workflow

Thelio implements a practical loop for engineering reconstruction:
1.  **Ingest:** Import reference STEP or JSON metadata.
2.  **Extract:** Programmatically retrieve dimensions and sketch relationships.
3.  **Model:** Re-create the part using `build123d`.
4.  **Verify:** Benchmark the new geometry against the source reference.
5.  **Deliver:** Export validated STEP/STL files and inspect via Plotly.

---

## Highlighted Model: `PN_000668_v7`

The featured T-bracket reconstruction includes a rounded base, a central vertical tab with mounting holes, and relief cutouts. A key differentiator is the **bottom bent flange**, which is rebuilt using exact point data from the source Fusion `Sketch2` rather than a visual estimate. This ensures that the flange position and profile remain perfectly aligned with the original design intent.

---

## Getting Started

### Prerequisites
The environment requires Python with the following dependencies:
*   `build123d`
*   `plotly`

```bash
# Recommended setup
python3 -m venv .venv
source .venv/bin/activate
pip install build123d plotly
```

### Execution
Run the primary reconstruction script:
```bash
python3 PN_000668_v7.py
```
This script will generate the part, display physical properties (volume/bounds), save `.stl` and `.step` files to your Desktop, and launch a 3D preview.

---

## Design Philosophy

Thelio is a specialized engineering workspace, not a general-purpose CAD replacement. It adheres to several core principles:
*   **Explicit over Implicit:** All dimensions should be traceable.
*   **Data-Driven:** Favor source-backed geometry over approximations.
*   **Reproducible:** Use scripts to eliminate the "black box" of manual UI edits.

---

## Roadmap & Contributions

While currently an active "working" repository, future updates will focus on:
*   Standardizing dependencies (`requirements.txt`).
*   Implementing a formal geometry validation suite.
*   Expanding coverage to additional part numbers.

**Contributing:** To maintain the project's integrity, please ensure all new models remain traceable to their source artifacts and document the origin of all parameters and sketches.
