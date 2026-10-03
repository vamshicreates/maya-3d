# Three.js review workflow

Three.js is installed in `viewer/` as a pinned npm dependency from the official `mrdoob/three.js` project. It is a rendering and inspection layer, not a replacement for Maya's native modeling tools or scene format.

## Prepare the review artifact

1. Save the editable `.ma` or `.mb` scene first.
2. Export a self-contained `.glb` using a verified Maya exporter or conversion path. Preserve semantic transform and mesh names. Convert Maya centimeters to glTF meters and avoid a compensating scale on an unnamed wrapper node.
3. Validate the GLB with the Khronos glTF Validator when available.
4. Complete `dimension_spec.json`, including overall size, tolerance, and `requiredParts`. Required part strings must exactly match exported node names.

## Run the reviewer

From this skill's `viewer/` folder:

```bash
npm install
npm run dev
```

Open the displayed local URL, then load a GLB by file or URL. Load the matching dimension specification and optional reference image.

The reviewer provides:

- orbit, pan, zoom, fit-to-model, and auto-rotation;
- semantic scene-part listing with visibility controls;
- click selection and bounding-box highlighting;
- wireframe and hard-edge overlays;
- GLB world-space width, height, and depth in meters;
- expected-versus-actual overall dimension differences;
- missing required-part detection by semantic node name;
- reference-image overlay with adjustable opacity.

## Interpret results

- A missing named part is a semantic modeling failure. Add or repair its Maya generator, rebuild the native scene, and export a new GLB.
- A correct node with the wrong bound is a scale, axis, or exporter problem. Verify Maya units, exporter conversion, root transforms, and glTF meter scale.
- A visible gap between parts may be an anchor or curve-endpoint error. Check the declared attachment anchors in `dimension_spec.json` and the generating Maya curve or profile.
- A silhouette mismatch with correct dimensions is commonly a camera calibration, curvature, or part-shape problem. Match reference aspect and field of view before changing geometry.
- Wireframe and edge views help reveal insufficient bevel segments, unexpected triangulation, disconnected shells, and missing support loops. Confirm topology in Maya before delivery.

Do not use browser-side object transforms as the authoritative correction. Record the affected semantic node and intended change, then update the procedural Maya source so the native scene and later exports remain reproducible.

## Sources

- [Official Three.js repository](https://github.com/mrdoob/three.js)
- [Three.js GLTFLoader](https://threejs.org/docs/pages/GLTFLoader.html)
- [Three.js OrbitControls](https://threejs.org/docs/pages/OrbitControls.html)
- [Khronos glTF Validator](https://github.com/KhronosGroup/glTF-Validator)
