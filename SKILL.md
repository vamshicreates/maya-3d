---
name: maya-3d
description: Create, edit, and verify Autodesk Maya 3D projects from reference images or briefs, delivering native editable scenes and project assets on Windows or macOS.
---

# Maya 3D production

Use this skill when the requested deliverable is a Maya scene, model, material setup, animation, lighting setup, or render. It does not run AutoCAD. An image is visual evidence, not a complete 3D specification: infer hidden surfaces and dimensions conservatively and record consequential assumptions.

## Start each job

1. Inspect every supplied reference image at original resolution where possible. Record its pixel width, pixel height, and aspect ratio; then look for dimension labels, scale bars, technical drawing annotations, known-size objects, or dimensions stated by the user. Identify the main forms, silhouette, relative scale, materials, camera angle, lighting, text/logos, and any requested animation or output format. Preserve distinctive design details. Ask only for a missing choice that materially changes the model; otherwise choose a plausible interpretation and proceed.
2. Check the current OS, installed Maya version, available `mayapy`, render plug-ins, and project output location. Run `python scripts/maya_runner.py --probe` from this skill folder, or provide `--mayapy` explicitly. Maya must be installed and licensed to execute and verify Maya code. Do not claim a scene was tested in Maya if it was only syntax checked.
3. Read [references/image-to-scene.md](references/image-to-scene.md) for image-based work and [references/dimensions-and-scale.md](references/dimensions-and-scale.md) whenever an image, drawing, measurement, or Three.js handoff is involved. Read [references/platform-and-rendering.md](references/platform-and-rendering.md) when choosing execution, dependencies, rendering, or interchange formats. Check Autodesk documentation for the installed Maya version before using an unfamiliar or version-specific command.
4. Read [references/threejs-review.md](references/threejs-review.md) when exporting a web-review asset, inspecting part coordination, comparing dimensions, or responding to a Three.js annotation.

## Build the deliverable

- Work in a dedicated Maya project. Keep `scenes/`, `sourceimages/`, `images/`, and the generating Python job together. Preserve the user's originals; copy reference images into `sourceimages/` when the scene depends on them. Use project-relative asset paths where Maya supports them.
- Create `dimension_spec.json` from `assets/dimension_spec_template.json` before modeling. Treat pixel dimensions as camera and aspect-ratio evidence only. Use explicit real-world measurements as authoritative; mark estimates and their confidence. Never silently interpret pixels as centimeters, inches, or meters.
- Write a reproducible Python job exposing `build(project_dir: pathlib.Path) -> pathlib.Path`. Generate actual editable Maya geometry and scene nodes using `maya.cmds` (or the Maya Python API when needed). Use descriptive transform names and groups for major components. Prefer procedural parameters for repeated details. Do not substitute a flat image plane for the requested 3D model.
- Set Maya linear units deliberately and drive major dimensions, curve control points, profiles, offsets, attachment anchors, and symmetry from the dimension specification. Avoid arbitrary literals for a measured component. When exporting glTF/GLB for Three.js, convert linear measurements to meters and retain semantic node names.
- Save the native source as `.ma` or `.mb` before any interchange export. `.ma` is a useful default for inspectable procedural scenes; `.mb` is appropriate when scene size or features warrant it. Export FBX, USD, OBJ, or Alembic only when the user asks or the downstream pipeline needs it, and check what each format preserves.
- For Three.js review, export a self-contained GLB with semantic node names and meter scale. Copy the GLB and its `dimension_spec.json` into the deliverable project; use `viewer/` in this skill to inspect the hierarchy, visibility, wireframe, hard edges, bounding dimensions, required parts, and reference-image alignment. The viewer is a QA surface, while the native Maya scene remains the editable source of truth.
- Create materials, UVs/textures, lights, and cameras to the requested finish. Render a preview when Maya and the necessary renderer are available. Match the reference from at least the principal camera; inspect another view for depth, intersections, and unfinished backsides.
- Keep all generated files in the chosen project; avoid modifying the user's Maya preferences, global scripts, plug-in directories, or existing scenes. Never install Maya, plug-ins, or packages without a concrete need and authorization.

## Execute and verify

Run `python scripts/maya_runner.py --job PATH_TO_JOB.py --project PATH_TO_PROJECT` with normal Python. The launcher finds the installed `mayapy`, initializes Maya, calls `build(project_dir)`, checks the saved native scene, reopens it, and writes `maya_report.json`. See `assets/scene_job_template.py` for the job interface. On Windows, use `py -3` or `python` if needed. For Maya GUI use, load the job in a Python Script Editor tab and call its `build(Path(...))`; do not initialize `maya.standalone` inside the GUI.

Inspect the saved scene and any render rather than treating a successful script exit as visual approval. Check geometry counts, missing textures, relative paths, camera framing, materials, scale, render settings, and any warnings in `maya_report.json`. Compare measured scene extents and named anchor distances against `dimension_spec.json` within the declared tolerances. If the current machine lacks Maya, still deliver the source job and project assets, run ordinary Python syntax checks, and state precisely that native scene creation and render verification remain pending on a Maya machine.

When Three.js review is requested, run `npm install` once in `viewer/`, then `npm run dev` for interactive review or `npm run build` for a static bundle. Load the exported GLB and dimension specification, inspect every required part from multiple angles, and send geometry corrections back to the named Maya generator rather than editing the GLB as the master asset.

## Hand-off

Return links to the Maya project root, `dimension_spec.json`, native scene, generating script, preview, and report when each exists. Briefly state the Maya version used, authoritative dimensions, estimated dimensions and confidence, what the reference did not reveal, and any remaining technical limitation. Never describe a script or preview image alone as a completed Maya project.
