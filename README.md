# NIO Student Hackathon — Dataset README

Thank you for registering for the event.

The NIO Student Hackathon is a technical data challenge inviting students across UK universities to solve a real-world engineering problem at the intersection of automotive engineering, computer vision, and AI-driven quality assurance.

Participants will work in teams to develop an automated Python solution to an open problem, contributing towards research into scalable quality assurance processes within advanced engineering simulation pipelines.

The winning team will receive a cash prize of **£4,000** split evenly among team members.

This repository contains the dataset and example files for the NIO Student Hackathon.

---

## Key Links

- [Terms and Conditions](https://nio.jp.feishu.cn/docx/D1Q0dlw3KoOpoKxGRuQjgi53puf)
- [Data Privacy Notice](https://nio.jp.feishu.cn/docx/ZqHndQhMCoSWpcx8xbtjeVSGpJh?from=from_parent_docx)
---

## Not Yet Registered? 
If you have not yet registered for the event and wish to do so, please fill in the registration form found here: 
- Link: https://forms.office.com/Pages/ResponsePage.aspx?id=ly8b6j9Cs0q_bUWjbAnONHz2I4aOuwtCtg7vuPMHdqFUMzhHM1MwMkdCVldNOTE4OURWREwzVEQ2VC4u 
---

## Background

- NIO uses an iterative CAD refinement process that includes automated quality checks before simulation (e.g. crash testing)
- A key quality check ensures bolt/fixing holes are correctly meshed, currently handled by bespoke computer vision scripts
- As part of a wider research project, we aim to develop an improved qulaity assurance method using CNNs or Multi-modal LLMs
- The main challenge in delivering these improved solutions is insufficient labelled training and testing data, particularly for failure cases
- This hackathon aims to develop methods to address this challenge, by contributing towards the first step in a larger research project towards a synthetic data generation pipeline
- This event is also part of a wider talent scouting initiative for NIO's grad schemes, industry funded projects, and other academic collaborations
- Further information on the project background and context can be found in the pre-registration pack

---

## The Task

### Objective

Participants are provided with a dataset of **100 `.jpeg` images** representing finite element (FE) meshes.

The goal is to develop a **Python-based solution** that takes a `.jpeg` image as input and outputs a corresponding `.nas` file that details the underlying mesh geometry.

> No prior experience with `.nas` files is expected. Full `.nas` format details are provided in this pack.

---

## The Data

### Repository Structure

The GitHub repository contains:

- 100 `.jpeg` images representing FE meshes
- 2 example images representing FE meshes with corresponding `.nas` files
- Competition Brief
- Pre-Registration Pack

Use the example images and `.nas` files to familiarise yourself with the `.nas` format. A simplified example of how a basic element is constructed using a `.nas` file is included in the competition brief. Further information on how the coordinate system is defined is also included in the project brief.

---

## Required Submission

Each team's **Chief Engineer** must submit:

1. **Generated output of the 100 `.nas` files** corresponding to the provided `.jpeg` images
   - `.nas` files must follow the example format
   - Each `.nas` file must have an identical name to the image it represents
   - Each `.nas` file must have the correct `File Ref. No.`

2. **The full code** used to generate the `.nas` files
   - The provided `.nas` files **must** be reproducible using this code
   - Code should be executed by running a `main.py` file
   - Code should be well-documented throughout, with clear instructions on how to reproduce the `.nas` files
   - A clear, well-formatted `README.md` file should be included

3. **A concise, well-presented one-page report** summarising the approach, methods, and tools used (including AI/LLMs, if applicable)
   - `.docx` or `.pdf` format
   - please explicitly state which all AI models used throughout your research, development, testing, etc. i.e. Claude Code, Chat GPT, Perplexity, Antigravity, etc. 

> ⚠️ **Omitting any of these 3 key requirements may result in your submission being rejected.**

### Submission Instructions

- The Chief Engineer must email a `.zip` file to [jay.flynn.o@nio.io](mailto:jay.flynn.o@nio.io) before **23:59 on Friday 17th April**
- Late submissions will not be considered
- Teams are permitted a maximum of **1 submission** — resubmissions will not be considered
- The submission should follow the suggested format outlined in the GitHub repository
- If the file is too large to share via email, a download link may be accepted — please liaise with [jay.flynn.o@nio.io](mailto:jay.flynn.o@nio.io) well ahead of the deadline to ensure accessibility

> Teams must abide by the [Terms & Conditions](https://nio.jp.feishu.cn/docx/D1Q0dlw3KoOpoKxGRuQjgi53puf). If **any** team member is found to be in breach of any term, the entire team risks disqualification.

---

## Scoring

Provided all T&Cs and submission requirements are met, teams are primarily scored on the **accuracy of the 100 `.nas` files submitted**.

Each `.nas` file is scored against the original ground truth `.nas` that was used to produce the image, and is scored **out of 100** as follows:

| # | Condition |
|---|-----------|
| 1 | The format must match the provided example format |
| 2 | The `BEGIN BULK` and `ENDDATA` strings must be included |
| 3 | The total number of `GRID` rows must exactly match the ground truth |
| 4 | The total number of `CTRIA3` and `CQUAD4` elements must exactly match the ground truth |
| 5 | For each element, the `GRID` coordinates defining the geometry must exactly match the ground truth |
| 6 | If conditions 1–5 are met, the score is calculated by measuring the sum of average distance from each `GRID` coordinate to its respective ground truth coordinate |
| 7 | If conditions 1–5 are **not** met for any given `.nas`, that file will score **0** |
| 8 | The total score for a team is the **average score across all 100 `.nas` files** |
| 9 | Any ties will be decided by assessing the quality of the code documentation and the one-page report |

> ⚠️ **Important:** Scoring is only performed if all T&Cs and submission requirements are met. Be sure to provide all documents with clear code documentation and a clear, concise one-page report.

---

## `.nas` Output Format: Nastran Bulk Data (`.nas`)

Each output file must follow the format below, structured in two sections:

### 1. GRID — Node Definitions

Each line defines a single node (point in space) by its ID and coordinates:

```
GRID      <node_id>        <x>      <y>      <z>
```

- `node_id`: unique integer identifier for the node. Scoring uses closest-neighbour matching, so variance in `node_id` between your submission and the ground truth does not matter. We suggest starting from `1`.
- `x`, `y`: coordinates in the mesh coordinate system (derived from the image)
- `z`: always `0.` for flat (2D) meshes

**Example:**
```
GRID          94        -2.26620-3.790010.
GRID          95        2.18410 -4.665410.
GRID          96        5.64320 -1.731810.
```

### 2. Element Definitions — `CQUAD4` and `CTRIA3`

Each line defines the boundary of a mesh element by referencing its corner node IDs.

**Quadrilateral element (4 nodes):**
```
CQUAD4    <elem_id>  <file_id>  <n1>  <n2>  <n3>  <n4>
```

**Triangular element (3 nodes):**
```
CTRIA3    <elem_id>  <file_id>  <n1>  <n2>  <n3>
```

- `elem_id`: unique integer identifier for the element
- `file_id`: file ID used to reference the mesh file
- `n1–n4`: node IDs defining the corners of the element. Node IDs **must** be listed in order, correctly tracing the outer boundary of the element (clockwise or anti-clockwise)

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

> The file begins with `BEGIN BULK` and ends with `ENDDATA`. `GRID` entries should precede element entries.

---

## Example Files

The `Example .nas format/` folder contains 4 files (2 image–NAS pairs) with ground-truth outputs. These are provided to:

- Help you understand the expected output format
- Serve as test cases to validate your pipeline

| Image | NAS File | Hole Shape |
|-------|----------|------------|
| `hole_101_flat.jpg` | `hole_101_flat.nas` | Circular hole, irregular outer boundary |
| `hole_102_flat.jpg` | `hole_102_flat.nas` | Rectangular hole, irregular outer boundary |

You are encouraged to use these examples to test and calibrate your solution before running it over the full 100-image competition dataset.

---

## Tips for Getting Started

There is a lot of information in this pack, which may be overwhelming at first. We suggest starting with simple solutions and building familiarity with the example images and `.nas` files gradually.

- **Coordinate system:** The magenta axis lines are your primary reference for mapping pixel coordinates to real-world mesh coordinates. Identify where they intersect to locate the origin and define your coordinate system.
- **Element types:** Most elements in this dataset are `CQUAD4` (quadrilateral). `CTRIA3` (triangular) elements appear occasionally, typically at irregular boundaries. Focus on understanding quads first.
- **Z coordinate:** Always `0.` — this is a flat 2D mesh projected into the XY plane.
- **prop_id:** The `prop_id` field in all element definitions is always `101`.
- **Node/element IDs:** Do not need to start from any specific value, but must be unique within the file.
- **AI tools:** Use of LLMs and AI tools is permitted and encouraged — see the [Terms & Conditions](https://nio.jp.feishu.cn/docx/D1Q0dlw3KoOpoKxGRuQjgi53puf) for details.
