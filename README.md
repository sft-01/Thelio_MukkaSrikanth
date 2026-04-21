# Thelio

Thelio is a small CAD reconstruction workspace for turning reference geometry into editable, scriptable parametric models. It combines exported CAD artifacts such as Fusion 360 JSON, STEP files, and manufacturing outputs with a Python-based modeling workflow built on `build123d`.

The current focus of the repository is reverse engineering and rebuilding sheet-metal style brackets, validating dimensions against source exports, and generating clean downstream deliverables such as STEP, STL, and interactive Plotly previews.

At the moment, the most complete example in the project is `PN_000668_v7.py`, which reconstructs a T-shaped bracket with relief cuts, a drilled tab, and a bottom bent flange. The flange is not an approximation; it is rebuilt from the exact Fusion sketch profile extracted from the exported model data.

## Why This Project Exists

Recreating mechanical parts from source systems is often messy. Native CAD files may be unavailable, locked to a specific toolchain, or difficult to reuse in automation-heavy workflows. Thelio exists to make that process more repeatable.

Instead of treating reference files as the end of the workflow, this project treats them as inputs:

- STEP files provide geometric ground truth.
- Fusion export JSON provides feature, sketch, and parameter metadata.
- Small helper scripts pull out dimensions and sketch geometry.
- `build123d` turns that information into editable Python geometry.

This makes it easier to inspect, validate, version, and regenerate parts without having to remodel everything manually in a CAD UI.

## What’s In The Repository

The repository is intentionally compact and organized around a single working workflow.

| Path | Purpose |
| --- | --- |
| `PN_000668_v7.py` | Main parametric rebuild of the bracket. Builds geometry, exports STEP/STL, and renders a Plotly mesh. |
| `PN_000668_v7_export.json` | Fusion export used to recover dimensions, sketch entities, and feature references for the current model. |
| `PN_000668 v7.step` | Reference STEP geometry for the current part. |
| `PN-000663 v14.step` | Additional reference part. |
| `PN-000664 v2.step` | Additional reference part. |
| `PN-000665 v6.step` | Additional reference part. |
| `PN-000666 v7.step` | Additional reference part. |
| `PN-000666_v7_export.json` | Fusion export for another modeled variant. |
| `PN_000669 v9.step` | Additional reference part. |
| `scratch/extract_params.py` | Utility for extracting named parameters from a Fusion export JSON file. |
| `scratch/summarize_sketches.py` | Utility for listing sketch lines, arcs, and circles from the Fusion export. |
| `scratch/analyze_step.py` | Lightweight STEP inspection script for bounding-box and volume checks. |
| `Results/` | Generated output artifacts and notebook experiments. |

## Core Workflow

Thelio follows a practical reverse-engineering loop:

1. Start from a reference file such as a STEP model or Fusion JSON export.
2. Extract dimensions, sketch entities, and feature relationships from the export.
3. Rebuild the part in Python with `build123d`.
4. Compare key geometry against the source model.
5. Export the regenerated part as STEP and STL.
6. Visualize the result with Plotly for fast inspection.

This workflow is especially useful when a part has to be updated repeatedly, when dimensions must be traceable, or when multiple revisions need to be compared in code rather than by eye alone.

## Current Model: `PN_000668_v7`

The main scripted model rebuilds a T-bracket with these features:

- A rounded rectangular base plate
- A centered vertical tab with rounded top corners
- A through-hole on the tab
- Two lower relief cutouts
- A bottom bent flange reconstructed from the exact Fusion `Sketch2` profile

The flange is an important detail in this project because it was validated directly from exported sketch geometry instead of being modeled from a loose verbal estimate. In the current script:

- The main T profile is created on the `YZ` plane.
- The bottom flange profile is created on the `XZ` plane.
- The flange width is extruded symmetrically to `24 mm`.
- The side profile comes from the exact four-point shape recovered from the Fusion export.

That approach helps preserve both position and true shape, which matters for sheet-metal style parts where a small interpretation error can shift the flange noticeably.

## Quick Start

### Requirements

The project currently assumes a Python environment with:

- `build123d`
- `plotly`

A simple setup using `venv` looks like this:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install build123d plotly
```

### Run The Main Model

```bash
python3 PN_000668_v7.py
```

When the script runs, it will:

- build the part
- print volume and bounding-box information
- export `.stl` and `.step`
- open a Plotly 3D preview

By default, the current script exports files to the Desktop:

- `~/Desktop/PN_000668_v7.stl`
- `~/Desktop/PN_000668_v7.step`

If you want repository-local output instead, adjust the `export_files()` function in `PN_000668_v7.py`.

## Utility Scripts

The `scratch/` folder contains small utilities that support the modeling process.

### Extract Parameters

```bash
python3 scratch/extract_params.py
```

This prints the parameter table from the Fusion export so values such as lengths, radii, and angles can be reused in the scripted model.

### Summarize Sketch Geometry

```bash
python3 scratch/summarize_sketches.py
```

This prints line, arc, and circle definitions from sketches inside the Fusion export. It is particularly helpful when a feature needs to be rebuilt from its exact sketch profile rather than inferred from screenshots or dimensions alone.

### Inspect STEP Geometry

```bash
python3 scratch/analyze_step.py
```

This performs a quick volume and bounding-box check on a STEP file, which is useful for sanity checks during rebuild work.

## Design Philosophy

Thelio is not trying to replace a full CAD system. It is trying to make CAD data more inspectable and reproducible.

The project favors:

- explicit dimensions over hidden assumptions
- source-backed geometry over visual approximation
- small scripts over opaque manual steps
- exportable and reviewable outputs over one-off edits

That means some scripts are intentionally direct and specific to the part under study. This is a working engineering repository, not a generalized CAD framework. The tradeoff is worthwhile because it keeps each reconstruction grounded in the source geometry that produced it.

## Repository Status

This project is currently an active working workspace rather than a polished library. A few things to keep in mind:

- The modeling flow is centered on specific part numbers and revisions.
- There is no packaged CLI or Python module yet.
- Dependencies are not pinned in a `requirements.txt` file yet.
- Some scripts in `scratch/` are exploratory utilities, not hardened tools.

Even so, the repository already captures a useful pattern for CAD reconstruction:

- collect reference artifacts
- interrogate source data
- rebuild the part in code
- validate the result
- export manufacturing-friendly formats

## Roadmap

Reasonable next steps for the project would be:

- add a `requirements.txt` or `pyproject.toml`
- move exports into a configurable output directory
- add a reusable geometry validation script for comparing script output against STEP references
- add screenshots or rendered previews for each modeled part
- standardize naming and revision handling across part files
- expand scripted coverage beyond `PN_000668_v7`

## Contributing

If you are extending this project, the best way to keep it useful is to preserve traceability:

- keep dimensions tied to source exports when possible
- document where a sketch or parameter came from
- avoid replacing exact geometry with approximate geometry unless the approximation is explicit
- keep reference artifacts alongside the scripted rebuild

## License

No license file is currently included in the repository. If this project is intended for public GitHub use, adding a license should be one of the next housekeeping steps.
