# NIO Student Hackathon — Dataset README

## Overview

This repository contains the dataset and example files for the NIO Student Hackathon. The task is to develop a Python-based workflow that takes a JPEG image of a mesh hole and reverse-engineers the corresponding Nastran (`.nas`) bulk data file as accurately as possible.

---

## The Task

### Objective

Participants are provided with a dataset of 100 .jpeg images representing finite element (FE) meshes.

The goal is to develop a Python-based solution that takes a .jpeg image as input and outputs a corresponding .nas file that details the underlying mesh geometry.

No prior experience with .nas files is expected. Full .nas format details are provided in this pack.


### Required Submission

Each team must submit:
- Generated output of the 100 .nas files corresponding to the provided .jpegs
- The full, reproducible, well-documented code used to generate the .nas files
- A concise one-page report that summarises the approach, methods, and tools used (including AI/LLMs, if applicable).

---

## Repository Structure

```
/
├── README.md                   # This file
├── images/                     # 100 input JPEG images (competition dataset)
│   ├── hole_001_normalised.jpg
│   ├── hole_002_normalised.jpg
│   ├── ...
│   └── hole_100_normalised.jpg
└── examples/                   # 4 worked examples with ground-truth .nas files
    ├── hole_101_flat.jpg
    ├── hole_101_flat.nas
    ├── hole_102_flat.jpg
    └── hole_102_flat.nas
```

---

## Input Images

### Format

All input images are JPEG files rendered against a black background. Each image depicts a small section of a finite element mesh, cropped from a larger vehicle CAD model. Mesh elements appear as **yellow or grey filled polygons** separated by dark grid lines.

### Coordinate System

Each image includes two **magenta (purple) reference lines** — one along the X-axis and one along the Y-axis — that define a consistent coordinate system across all images:

- **Origin (0, 0):** bottom-left corner, where the two magenta lines meet
- **X-axis:** horizontal magenta line running along the bottom
- **Y-axis:** vertical magenta line running up the left side
- **Axis extent:** both axes span **0 to 100 units** (as labelled `D:100.00` and `D:75.00` in the images — note the displayed label reflects the full canvas dimension, not a scaling factor)

Use these reference lines to establish the real-world coordinate system when extracting node positions from the image.

### Example Images

| File | Description |
|---|---|
| `hole_001_normalised.jpg` | Two circular mesh holes (grey), shown with shared elements between them |
| `hole_002_normalised.jpg` | Single circular mesh hole (yellow) |
| `hole_003_normalised.jpg` | Two circular mesh holes (grey), no red boundary highlight |
| `hole_101_flat.jpg` | Example — circular hole with irregular outer boundary (yellow) |
| `hole_102_flat.jpg` | Example — rectangular hole with irregular outer boundary (yellow) |

---

## Output Format: Nastran Bulk Data (`.nas`)

Each output file must follow the Nastran free-field or fixed-field bulk data format. The file is structured in two sections:

### 1. `GRID` — Node Definitions

Each line defines a single node (point in space) by its ID and coordinates:

```
GRID      <node_id>        <x>      <y>      <z>
```

- `node_id`: unique integer identifier for the node
- `x`, `y`: coordinates in the mesh coordinate system (derived from the image)
- `z`: always `0.` for flat (2D) meshes

**Example:**
```
GRID          94        -2.26620-3.790010.
GRID          95        2.18410 -4.665410.
GRID          96        5.64320 -1.731810.
```

### 2. Element Definitions — `CQUAD4` and `CTRIA3`

Each line defines a single mesh element (a filled polygon) by referencing its corner node IDs:

#### Quadrilateral element (4 nodes):
```
CQUAD4    <elem_id>  <prop_id>  <n1>  <n2>  <n3>  <n4>
```

#### Triangular element (3 nodes):
```
CTRIA3    <elem_id>  <prop_id>  <n1>  <n2>  <n3>
```

- `elem_id`: unique integer identifier for the element
- `prop_id`: property set ID (always `101` in this dataset)
- `n1`–`n4`: node IDs defining the corners of the element, listed in order

**Example:**
```
CQUAD4        65     101     126     104     101     125
CQUAD4        66     101     113     133     157     140
CTRIA3        87     101     103     165     124
```

### Full File Structure

```
BEGIN BULK
GRID      ...
GRID      ...
...
CQUAD4    ...
CQUAD4    ...
CTRIA3    ...
...
ENDDATA
```

The file begins with `BEGIN BULK` and ends with `ENDDATA`. GRID entries should precede element entries.

---

## Example Files

The `examples/` folder contains **4 files** (2 image–NAS pairs) with ground-truth outputs. These are provided to:

- Help you understand the expected output format.
- Serve as test cases to validate your pipeline.

| Image | NAS File | Hole Shape |
|---|---|---|
| `hole_101_flat.jpg` | `hole_101_flat.nas` | Circular hole, irregular outer boundary |
| `hole_102_flat.jpg` | `hole_102_flat.nas` | Rectangular hole, irregular outer boundary |

You are encouraged to use these examples to test and calibrate your solution before running it over the full 100-image competition dataset.

---

## Tips for Getting Started
There is a lot of information in this pack, which will be overwhelming at first. We suggest starting with the simple solutions first, and as you become more familiar with the example images and .nas files, the task will become more clear. A few suggestions on where to start:

- The magenta axis lines are your primary reference for mapping pixel coordinates to real-world mesh coordinates. Identify where they intersect to locate the origin and define your coordinate system.
- Most elements in this dataset are `CQUAD4` (quadrilateral). `CTRIA3` (triangular) elements appear occasionally, typically at irregular boundaries. Focus on understanding quads first.
- The `z` coordinate is always `0.` — this is a flat 2D mesh projected into the XY plane.
- The `prop_id` field in all element definitions is always `101`.
- Node and element IDs do not need to start from any specific value, but must be unique within the file.
- Use of LLMs and AI tools is permitted and encouraged — see the Terms & Conditions for details.

---

## Contact

For questions about the competition or this dataset, contact the event organiser at: [jay.flynn.o@nio.io](mailto:jay.flynn.o@nio.io)
