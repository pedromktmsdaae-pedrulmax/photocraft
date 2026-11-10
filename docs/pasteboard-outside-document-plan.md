# Outside-document pasteboard: implementation plan

Goal: show, select, and move loose layers outside document bounds in the editor without changing the exported image bounds.

## Verified code paths

- `crates/ui-egui/src/canvas/artboard_ui.rs`: `pasteboard_quads` currently limits its geometry to `doc.bounds()`.
- `crates/ui-egui/src/canvas.rs`: main canvas draws the document image using `xf.doc_rect(doc.bounds())`; `draw_beyond_canvas` exists for crop-tool previews and should be investigated for reuse.
- `crates/ui-egui/src/canvas/gpu_canvas.rs`: GPU render regions are bounded by `doc.bounds()`.

## Implementation considerations

1. Define an editor-only viewport region outside document bounds, based on visible loose-layer bounds, without changing the document's size or export region.
2. Render those loose layers beyond the document edge in CPU and GPU paths, or use a shared editor overlay path; avoid rendering the document background outside its bounds.
3. Allow pointer hit-testing, selection, dragging, and transforms in that region.
4. Keep artboard-specific clipping and export boundaries unchanged.
5. Handle zoom, rotation, large coordinates, hidden layers, and performance.

## Acceptance tests

- A visible loose image moved fully outside the document remains visible and selectable on the gray pasteboard.
- Dragging the image back into the artboard works.
- A hidden layer stays hidden; a layer clipped to an artboard retains its clipping semantics.
- Exports contain only the intended artboard/document region.
- CPU and GPU display paths agree.

This document records investigation and acceptance criteria; no functional code changes are implemented yet.
