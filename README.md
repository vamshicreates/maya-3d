# Maya 3D Codex Skill

A reusable Codex skill for creating, editing, and checking Autodesk Maya projects from reference images or written briefs. It guides Codex through image analysis, procedural Maya Python scene construction, project organization, materials, cameras, preview rendering, and handoff of an editable native scene.

## What it provides

- Image-to-scene workflow with explicit handling of hidden geometry and scale assumptions
- Dimension-aware modeling that separates pixel resolution from physical measurements
- A reusable `dimension_spec.json` template for units, anchors, tolerances, and measurement provenance
- An integrated Three.js GLB reviewer with part visibility, selection, wireframe, edge overlays, reference-image comparison, and dimension checks
- Cross-platform `mayapy` discovery for Windows and macOS
- A reusable Maya scene-job template
- Native `.ma` or `.mb` scene verification by reopening the generated scene
- Checks for renderable meshes and missing or external image assets
- Guidance for Arnold licensing, Maya projects, and common interchange formats

Autodesk Maya must be installed and licensed on the machine that creates or verifies a Maya scene. This repository does not install Maya or a Maya plug-in.

## Install

Copy this repository to your Codex skills directory:

- macOS: `${CODEX_HOME:-$HOME/.codex}/skills/maya-3d`
- Windows: `%CODEX_HOME%\skills\maya-3d`, or `%USERPROFILE%\.codex\skills\maya-3d` when `CODEX_HOME` is unset

Start a new Codex chat and invoke the skill with `$maya-3d`, attaching a reference image or describing the Maya project you want.

See [INSTALL.md](INSTALL.md) for platform notes.

## Check the Maya connection

From the repository directory, run:

```bash
python scripts/maya_runner.py --probe
```

To generate and validate the included sample scene:

```bash
python scripts/maya_runner.py \
  --job assets/scene_job_template.py \
  --project ./sample-project
```

On Windows, use `py -3` or `python` according to your Python installation. If Maya is installed in a custom location, pass `--mayapy PATH` or set `MAYA_PYTHON_EXECUTABLE`.

## Repository layout

```text
maya-3d/
├── SKILL.md
├── INSTALL.md
├── agents/openai.yaml
├── assets/dimension_spec_template.json
├── assets/scene_job_template.py
├── references/
│   ├── dimensions-and-scale.md
│   ├── image-to-scene.md
│   ├── platform-and-rendering.md
│   └── threejs-review.md
├── viewer/
│   ├── index.html
│   ├── package.json
│   └── src/
└── scripts/maya_runner.py
```

## Three.js review application

The repository includes a browser review tool powered by the official Three.js package. From `viewer/`, run:

```bash
npm install
npm run dev
```

Load a self-contained GLB, the project's `dimension_spec.json`, and an optional reference image. The viewer reports world-space dimensions in meters, missing required parts, and expected-versus-actual size differences while providing interactive model inspection. Maya remains the authoritative editable scene.

## Validation status

The Python files and skill metadata have been checked. Native Maya scene and render validation require an installed Maya environment with a valid license.
