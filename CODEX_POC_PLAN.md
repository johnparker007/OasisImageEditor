# OasisImageEditor — Codex POC plan

The immediate goal is to determine whether two perspective photographs can be converted into a geometrically accurate front-on composite foundation that materially outperforms whole-image generative reconstruction for overlay accuracy.

## Phase 1 — Bootstrap

Build a minimal Python package and CLI.

Requirements:
- Python project using `pyproject.toml`.
- Dependencies: OpenCV, NumPy, Pillow and matplotlib where useful.
- Package under `src/oasis_image_editor/`.
- Tests under `tests/`.
- `sample_data/` documented as the local location for source photographs; do not require committing source images.
- `outputs/` for diagnostics, ignored except for a placeholder if desired.
- CLI entry point.
- README with setup and example commands.
- No web UI and no generative AI.

Acceptance criteria:
- Package installs in editable mode.
- CLI help works.
- Unit tests pass.

## Phase 2 — Rectify one photograph

Given one photograph of approximately planar artwork, produce a front-on orthographic image.

Implement:
- Image loading and validation.
- Optional reduced-resolution analysis while retaining full-resolution final processing.
- Strong-edge/line and rectangular-structure analysis suitable for proposing the artwork plane.
- Automatic four-corner estimation with a confidence score.
- Ordered corners and homography calculation.
- Full-resolution perspective warp and sensible canvas dimensions.
- Diagnostic image showing proposed corners/edges.
- JSON or text diagnostics containing source corners, destination corners, homography and confidence.
- Manual fallback accepting four explicit source coordinates when automatic confidence is inadequate.

Rules:
- Never guess silently when confidence is low.
- The automatic detector may return a structured failure.

Tests:
- Corner ordering.
- Homography recovery from synthetic quadrilaterals.
- Perspective warp dimensions.

## Phase 3 — Register two rectified photographs

Align two observations of the same fixed artwork into a common coordinate system.

Implement:
- Feature detection/descriptors (prefer SIFT when available; provide a sensible fallback if needed).
- Descriptor matching and filtering.
- Robust transform estimation using RANSAC.
- Treat moving/non-planar/window content as outliers through robust estimation; do not attempt to align reel symbols behind the glass.
- Prefer a homography or similarly globally explainable transform.
- Warp the moving image into the reference coordinate system.

Diagnostics:
- Feature-match image.
- Inlier/outlier visualization.
- Aligned A and B.
- Absolute difference image.
- Edge disagreement image.
- Machine-readable metrics containing detected features, candidate matches, inlier count/ratio, transform and median reprojection error.

Tests:
- Synthetic known transforms with distractor/outlier features where practical.
- Transform/reprojection metric helpers.

## Phase 4 — Near-pixel refinement

Refine the robust feature-based alignment deterministically.

Implement:
1. Coarse rectification.
2. Feature/RANSAC registration.
3. Intensity-based refinement using OpenCV ECC or another justified constrained method.

Constraints:
- Do not introduce unconstrained local warping.
- Use a transformation model that cannot freely distort individual artwork elements.
- If ECC fails or reduces the measured alignment quality, retain the pre-refinement transform and report the failure.

Diagnostics:
- Before/after overlays.
- Before/after absolute difference.
- Before/after edge disagreement.
- ECC convergence/correlation score.
- Quantitative comparison showing whether refinement improved alignment.

Acceptance target:
For suitable source photography, fixed printed edges should visually overlap closely enough that one- or two-pixel disagreements are obvious in the edge diagnostic. Do not claim a fixed pixel tolerance until it has been measured on real samples.

## POC checkpoint

STOP after Phase 4 and evaluate real source images before implementing:
- exposure/quality maps;
- multi-image fusion;
- reflection removal;
- dynamic-window semantic detection;
- generative restoration;
- application UI.

The checkpoint should answer:

> Can deterministic geometry recover a stable common artwork coordinate system from the source photographs, and how large is the residual positional error?

## Suggested first Codex task

Read `AGENTS.md` and this plan. Implement Phases 1 and 2 only. Run the tests. Do not proceed to Phase 3 until Phase 2 is working and its diagnostics can be inspected on a real photograph.

## Suggested second Codex task

Read `AGENTS.md` and `CODEX_POC_PLAN.md`. Assuming Phase 2 is complete, implement Phase 3. Run all tests. Do not implement Phase 4. Report the registration metrics and diagnostic files produced by the CLI.

## Suggested third Codex task

Read `AGENTS.md` and `CODEX_POC_PLAN.md`. Implement Phase 4 only. Preserve the Phase 3 result whenever refinement fails or worsens alignment. Run all tests and report before/after metrics and diagnostics.
