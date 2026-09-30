---
type: Review
title: Independent review of Quest 2 tiles and splats viewer research
description: Claude found an overlooked combined headset run, a code-version mix-up, and several render-path and format claims that the viewer recommendation now corrects.
tags: [webxr, quest-2, 3d-tiles, gaussian-splats]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# Independent review of Quest 2 tiles and splats viewer research

**Finding:** Claude's independent review of the
[viewer recommendation](quest-2-tiles-splats-viewer.md) found that the first
draft overstated what was untested and underplayed existing Quest GPU evidence.
The note now distinguishes measured app behavior from the unreleased viewer
branch and sequences renderer and format experiments separately. Performance
acceptance remains open until a corrected joint scene is measured on Quest 2.

Review source: Claude Opus 5.1, Conductor session
`35eaec95-b15a-4571-a620-96a4d2efdf5b`, 29 September 2026. Claude read the
current note, checked linked primary sources, and inspected the app and viewer
branches. The following is a processed account, not a transcript.

## Objections and disposition

1. **Combined Quest evidence was omitted — corrected.** The
   [app's Quest review](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
   briefly compared 15 custom tile layers on versus hidden with the Google
   globe and 500,000 splats: median XR callback gaps fell from about 311 ms to
   187 ms when hidden. Both were slow. Later 96.5/100.3/18.6 ms comparisons
   had custom tiles hidden. The recommendation now reports both experiments
   and keeps their short, nonstationary measurement limits visible.
2. **Two XR implementations were conflated — corrected.** The app's
   [XR implementation](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/src/components/app/viewer/webxr-session.ts)
   ran against [published viewer 0.8.1](https://github.com/splnlss/3d-story-viewer/tree/34ad7cc);
   the later
   [viewer branch](https://github.com/splnlss/3d-story-viewer/tree/133a70be4704f9ea4adb4e260a9392138a4428c5)
   owns a new XR bridge. The branch omits the app's tested `lodRenderScale=4`.
   No cited Quest benchmark covers that branch, so the note now calls it an
   unreleased implementation and proposes carrying the measured setting over.
3. **Tile XR scheduling and per-frame camera resets were missing — corrected.**
   The [3D Tiles VR guide](https://github.com/WilliamLiu-1997/3D-Tiles-RendererJS-3DGS-Plugin/blob/v0.3.0/docs/webxr.md)
   swaps to the headset `ArrayCamera`, sets its eye resolution, and binds
   `Scheduler.setXRSession()`. The viewer's
   [GlobeManager](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/GlobeManager.js)
   re-registers the desktop camera and `errorTarget=20` each update. The note
   now names the required changes inside that update path and the hardcoded
   quality controls.
4. **Depth and repeated passes were understated — corrected.** The
   [viewer render loop](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/index.js)
   issues a stereo render for every defined render-order index, even with
   hidden layers, and clears depth between them. Higher-layer custom tiles
   therefore cannot depth-occlude layer-0 splats correctly; the layer-0 globe
   and splats can test against the same depth buffer. The note now calls for a
   shared depth pass, measures the pass count, and flags the 0.25 m–100,000 km
   XR depth range. Meta says render-target switching can disable
   [fixed foveation](https://developers.meta.com/horizon/documentation/web/webxr-ffr/),
   so that needs a headset trace rather than an assumption.
5. **The plugin migration and source dates needed correction — corrected.**
   [Plugin 0.3](https://github.com/WilliamLiu-1997/3D-Tiles-RendererJS-3DGS-Plugin/blob/v0.3.0/README.md)
   uses Gaussian-Splat-Lite, which can load RAD directly. Only tiled SPZ
   requires asset conversion. The recommended comparison now runs GSL versus
   Spark on the same RAD, then tests tiling under GSL. Cesium's relevant
   [1.144 SPZ fix](https://github.com/CesiumGS/cesium/blob/main/CHANGES.md)
   was in August; older Cesium splat fixes and the April launch are explicitly
   outside the four-month focus.
6. **Local Spark changes and better GPU levers were missing — corrected.** The
   viewer's [Spark patch](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/patches/@sparkjsdev+spark+2.2.0.patch)
   is hash-checked and affects ECEF precision and migration cost. The
   [app review](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
   reports a desktop `maxPixelRadius` trial; the [Quest prototype](https://github.com/splnlss/threejs-webxr-worldmodels-test/blob/1140d3a5b3c1a84ba976a8fb10c15e155fcf8804/docs/quest-sparkjs-handoff.md)
   uses spherical harmonics degree 0. Their GPU samples showed high shader
   activity, with one fragment-heavy sample. The note now prioritizes those
   levers while preserving the difference between desktop and headset results.
7. **Several qualifications were tightened — corrected.** Spark's 500,000
   WebXR splats are within its [default range](https://sparkjs.dev/docs/spark-renderer/),
   not a Quest 2 acceptance budget. `sortRadial` is already default, so merely
   enabling it cannot fix the observed black band. The 1 GiB renderer-memory
   sample came from a 262K/8M preset before XR. The PlayCanvas WebGPU/XR and
   Meta Browser dates are now separated from the app's Browser 31.3 test.
   The Gaussian-Splat-Lite [renderer guide](https://github.com/WilliamLiu-1997/Gaussian-Splat-Lite/blob/v1.0.0/docs/GaussianSplatRenderer.md#webxr)
   directly supports the note about automatic stochastic mode being disabled
   in XR.

## Open questions

- Does the corrected shared-depth, stereo tile path meet a useful Quest 2 frame
  target at any acceptable image quality? No reviewed source demonstrates it.
- Which combination of splat footprint, tile error target, page pool, FFR, and
  framebuffer scale keeps the center legible without holes or black bands?
- Does the patched ECEF path survive a later Spark or GSL migration with correct
  georeferencing and near/far depth precision?
