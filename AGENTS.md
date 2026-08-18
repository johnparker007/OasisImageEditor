# OasisImageEditor agent guidance

## Purpose
Build a proof-of-concept pipeline that reconstructs flat printed fruit-machine glass artwork from multiple perspective photographs while preserving the source geometry.

## Non-negotiable principles
1. Source photography is authoritative.
2. Fixed artwork geometry must be preserved rather than regenerated.
3. Prefer deterministic computer-vision operations over generative reconstruction.
4. Never silently alter artwork geometry to make an image look better.
5. Produce diagnostic outputs at each major processing stage.
6. Preserve intermediate transformations and metrics so results can be audited.
7. Regions that cannot be confidently reconstructed should be marked uncertain rather than invented.
8. Transparent apertures may reveal moving/non-planar content behind the glass; treat that content as an outlier for planar registration.
9. Do not add generative-AI restoration until deterministic registration has been evaluated.

## POC scope
Implement phases 1-4 first:
- Python/OpenCV project foundation and CLI.
- Perspective rectification of a single photograph, with automatic estimation and manual-corner fallback.
- Robust registration of two rectified photographs using fixed printed artwork.
- Near-pixel alignment refinement using a constrained deterministic method such as ECC.

Stop before quality fusion, reflection removal, UI work, or generative restoration unless explicitly requested.

## Engineering expectations
- Use Python, OpenCV, NumPy, Pillow and matplotlib where useful.
- Keep geometry and I/O code modular and testable.
- Prefer globally explainable transforms (homography/affine) over unconstrained deformation.
- Work at reduced resolution for feature analysis when useful, but apply final transforms at full resolution.
- Automatic detection must fail clearly when confidence is inadequate; do not fabricate corners or transforms.
- Write synthetic tests for corner ordering, homography recovery and registration.
- Emit useful numerical metrics: match counts, inliers, reprojection error, ECC score where applicable.
- Emit diagnostic images that make geometric errors easy to inspect.
- Keep sample photographs out of git unless explicitly added; document where users should place them.
