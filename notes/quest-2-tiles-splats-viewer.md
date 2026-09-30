---
type: Recommendation
title: Rendering 3D Tiles and Gaussian splats together on Quest 2
description:
  Keep the Three.js tile and Spark RAD pipeline, repair XR camera and depth
  integration, and spend a measured shared headset budget before evaluating
  newer splat tile formats.
tags: [webxr, quest-2, 3d-tiles, gaussian-splats]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# Rendering 3D Tiles and Gaussian splats together on Quest 2

**Recommendation (29 September 2026):** Keep the viewer's Three.js,
`3d-tiles-renderer`, and Spark stack for the next Quest 2 experiment. Render the
globe, a _small selected_ set of custom mesh tiles, and paged RAD splats in one
XR scene with correct eye-camera selection and depth. Give tiles and splats
separate quality controls but one measured GPU, memory, and network budget.
Start with substantially fewer than 500,000 visible splats; raise detail only in
the viewed region or story subject when frame time and page residency allow.
No tested combination of all three content types has reached the headset's
target frame time.

An [independent Claude review](quest-2-tiles-splats-viewer-review.md) checked
the evidence and corrected earlier claims about the combined Quest run,
unreleased viewer branch, and plugin migration.

## What the current viewer and headset tests establish

- The _unreleased_
  [viewer branch](https://github.com/splnlss/3d-story-viewer/tree/133a70be4704f9ea4adb4e260a9392138a4428c5)
  uses Three 0.186.0, Spark 2.2.0, and `3d-tiles-renderer` 0.5.2. In VR,
  [WebXRSession](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/WebXRSession.js)
  hides every custom `TilesLayer`, leaves the separate Google globe, sets
  `lodSplatCount=500,000`, framebuffer scale 0.8, and foveation 0.75. It omits
  the app's measured `lodRenderScale=4` VR setting. Its
  [WebGLRenderer](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/index.js)
  enables `antialias: true`; Spark
  [recommends false](https://sparkjs.dev/docs/performance/) to avoid
  multisampling overhead. [Spark's WebXR default](https://sparkjs.dev/docs/spark-renderer/)
  is already 500,000–750,000 splats; the explicit 500,000 setting is not a
  Quest 2 performance finding. The Quest tests below used the app's own
  [XR session implementation](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/src/components/app/viewer/webxr-session.ts)
  with [published viewer 0.8.1](https://github.com/splnlss/3d-story-viewer/tree/34ad7cc),
  which has no XR bridge. They do not benchmark
  the unreleased viewer branch.
- The viewer carries a 477-line
  [Spark ECEF backport](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/patches/@sparkjsdev+spark+2.2.0.patch)
  and an [exact module-hash check](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/scripts/check-spark-backport.mjs)
  run during development and builds. Its camera-relative ECEF behavior is a
  local patch; upgrading Spark or migrating renderer needs a precision and
  packaging regression test.
- The [app's Quest 2 review](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
  **did briefly test all three together**: hiding 15 initially visible custom
  tile layers while retaining Google globe and 500,000 splats reduced median
  XR callback gap from about 311 ms to 187 ms. Later, with custom tiles hidden,
  gaps were 96.5 ms with Google plus roughly 500,000 splats, 100.3 ms with
  Google off, and 18.6 ms with Google on but essentially no splats.
  `lodRenderScale=4` selected roughly 278,000–299,000 splats and improved
  paired samples, yet median gaps remained roughly 50–96 ms. These short,
  nonstationary samples show both custom tiles and splats matter, with splat
  work dominating the later isolated comparison. Callback gaps are not GPU
  frame times or proof of achieved frame rate. Hidden tile layers also retained
  cached content: one snapshot counted 571 shared items, about 123 MB; retained
  GPU memory was not measured.
- A separate
  [September 2026 Quest 2 Spark prototype](https://github.com/splnlss/threejs-webxr-worldmodels-test/blob/1140d3a5b3c1a84ba976a8fb10c15e155fcf8804/docs/quest-sparkjs-handoff.md)
  also did **not** load Google 3D Tiles. For one 432 MiB RAD scene, its chosen
  quality preset used 196,608 selected splats, 8,388,608 paged splats, two
  fetchers, `maxPixelRadius=256`, spherical harmonics degree 0, and XR
  framebuffer scale 0.9. The 8M page pool gave better coverage
  but used roughly 1 GiB browser renderer PSS in one 262K/8M sample before XR;
  full scale 1.0
  stuttered, 0.8 was too soft, and 0.9 was the preferred visual compromise. This
  preset is a _splat-only starting point_, not a safe joint memory or
  performance budget.
- In the unreleased viewer, the
  [tile layer](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/ContentLayer/TilesLayer.js)
  registers the desktop camera and resolution once; the
  [globe manager](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/GlobeManager.js)
  re-registers that camera and its desktop resolution on each update. The
  [XR bridge](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/XRRenderBridge.js)
  updates Three's XR camera for rendering, but does not switch tile selection to
  the XR `ArrayCamera`. Select _both_ globe and custom tiles from the headset's
  stereo camera and current eye viewport during XR, then restore the desktop
  camera after XR. Avoid registering both cameras at once. The globe's `update()`
  currently restores the desktop registration every frame, so the change must
  be made inside that code path. Also bind the tile
  [Scheduler](https://github.com/NASA-AMMOS/3DTilesRendererJS/blob/master/CHANGELOG.md)
  to the XR session for asynchronous tile work. The
  [plugin's VR guide](https://github.com/WilliamLiu-1997/3D-Tiles-RendererJS-3DGS-Plugin/blob/v0.3.0/docs/webxr.md)
  and
  [tile API](https://github.com/NASA-AMMOS/3DTilesRendererJS/blob/master/src/three/renderer/API.md)
  document this pattern. This is an integration issue inferred from code; it
  needs an actual headset check.
- The unreleased viewer's
  [layer render loop](https://github.com/splnlss/3d-story-viewer/blob/133a70be4704f9ea4adb4e260a9392138a4428c5/src/viewer/index.js)
  issues one full stereo render per defined render-order index, even if those
  layers are hidden, plus a UI pass when needed. It calls `clearDepth()` between
  passes. The Google globe and splats both occupy layer 0 and can depth-test
  together; a tile on a higher render-order layer always draws over splats
  after depth is cleared, regardless of its geometry. Put opaque globe and
  custom tiles in a shared depth-writing scene pass with depth-tested splats;
  test front/inside/behind occlusion in both eyes. The repeated passes may also
  defeat [Meta's final-eye-buffer foveation](https://developers.meta.com/horizon/documentation/web/webxr-ffr/)
  if other render targets intervene; verify it on device.
- Quest GPU samples in the [app review](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
  and [separate prototype](https://github.com/splnlss/threejs-webxr-worldmodels-test/blob/1140d3a5b3c1a84ba976a8fb10c15e155fcf8804/docs/quest-sparkjs-handoff.md)
  reported about 88–89% GPU utilization/93–94% shader activity and about
  80–83% shader busy in distinct runs; one prototype sample attributed roughly
  75–80% of shader time to fragments. These are snapshots, but they prioritize
  fragment footprint and redundant passes over speculative sorting changes.

## Recent approaches, 29 May–29 September 2026

- [Spark 2.2.0, 11 September](https://github.com/sparkjsdev/spark/blob/main/CHANGELOG.md)
  improved sorting, fixed stale LoD fetches, and offered experimental faster
  traversal. The viewer already pins it. Reported speedups depend on the
  configuration and are not Quest 2 results.
- [3D Tiles Renderer 0.5.2, 24 August; 0.5.3, 18 September](https://github.com/NASA-AMMOS/3DTilesRendererJS/blob/master/CHANGELOG.md)
  added host-specific download queues, then reduced traversal work and exposed
  `stats.refused` for full caches. The viewer has 0.5.2. A 0.5.3 trial can
  improve diagnosis, but does not repair stereo selection or splat draw cost.
  All tilesets share caches and queues by default since 0.5.0.
- The [3D Tiles RendererJS 3DGS plugin 0.3, September](https://github.com/WilliamLiu-1997/3D-Tiles-RendererJS-3DGS-Plugin/blob/v0.3.0/README.md)
  streams glTF/SPZ splats as explicit or implicit tiles and accounts for tile
  disposal and bytes. **Current 0.3 uses Gaussian-Splat-Lite 1.x, not Spark**;
  the app owns one scene-level Gaussian renderer. It is a strong candidate for
  _many geographically separate captures_ and unified spatial scheduling. Its
  globe+splat demo has no Quest 2 benchmark. Adopting it requires a renderer
  migration; only the _tiled SPZ content_ needs conversion. GSL can display the
  existing RAD alongside plugin-loaded SPZ tiles in the same scene in principle;
  that combination has not been measured on Quest 2.
- [Gaussian-Splat-Lite, created August](https://github.com/WilliamLiu-1997/Gaussian-Splat-Lite)
  offers RAD/SOG streaming, WebGL2 or WebGPU, splat depth output, and stochastic
  rendering. Its [renderer guide](https://github.com/WilliamLiu-1997/Gaussian-Splat-Lite/blob/v1.0.0/docs/GaussianSplatRenderer.md#webxr)
  says automatic movement-triggered stochastic mode is disabled in XR, though
  manual mode is possible. Test it if depth or sorting remains a bottleneck;
  published Quest 2 plus 3D Tiles performance evidence is absent.
- [PlayCanvas SuperSplat, June](https://blog.playcanvas.com/new-in-supersplat-webgpu-and-streaming-bring-huge-performance-wins/)
  uses coarse-first streamed SOG and a device splat budget; its WebGPU path
  culls, projects, and sorts on the GPU. Published speedups are desktop and
  iPhone results, not Quest 2 results. Its viewer [enabled WebXR on WebGPU in
  July](https://github.com/playcanvas/supersplat-viewer/releases). Meta's April
  [Browser 146.0 note](https://developers.meta.com/horizon/downloads/package/browser/146.0/)
  introduced WebGPU in WebXR as experimental; August [Browser 150.1](https://developers.meta.com/horizon/release-notes/?search_key=browser)
  still calls WebGPU foveation experimental. The app's Quest test reported
  Browser 31.3, so verify actual device support before any WebGPU trial. Use
  WebGL2 for the Quest 2 delivery baseline.
- [CesiumJS Gaussian splat tiles](https://cesium.com/blog/2026/04/27/3d-gaussian-splats-lod/)
  show an interoperable geospatial path: a 3D Tiles hierarchy carries
  compressed glTF splats with terrain and meshes. The April launch and most
  splat fixes predate this window; the [August 1.144 changelog](https://github.com/CesiumGS/cesium/blob/main/CHANGES.md)
  records a SPZ extension buffer-view fix. CesiumJS would be an engine
  migration; no cited source establishes Quest 2 WebXR support for this viewer.
- [luma.gl/splats](https://luma.gl/docs/api-reference/splats), listed in the
  [September 9.4 release](https://luma.gl/docs/whats-new), explores glTF
  splats, 3D Tiles, foveated hierarchy traversal, and mixed mesh scenes. Its
  GPU ordering, hierarchy, and storage-backed features require WebGPU; its
  WebGL2 fallback uses a more limited prepared-renderer path. The API is
  experimental and has no cited Quest 2 combined benchmark.
- September's [MoQSplat](https://arxiv.org/abs/2609.18624) and July's
  [SplatStream](https://arxiv.org/abs/2607.25971) research prioritize spatial
  or perceptually important splats for progressive transmission. They target
  bandwidth and startup detail rather than proving low Quest 2 draw cost or
  integration with the present 3D Tiles viewer.

The new projects use two complementary methods: **spatial hierarchy** determines
which distant tiles or splat chunks exist in memory, and **screen-space LoD plus
foveation** limits how much of that resident data is drawn per eye. Compression
and small chunk requests improve loading; they do not remove transparent
overdraw, sorting, or decoded GPU memory cost. This is a synthesis of the cited
project designs and [Spark's cost model](https://sparkjs.dev/docs/performance/).

If standalone Quest 2 cannot meet the chosen visual threshold, [NVIDIA
CloudXR.js](https://docs.omniverse.nvidia.com/xr/omniverse-spatial-docs/latest/clients/meta/01-overview.html)
supports Quest 2 Browser by streaming server-rendered stereo frames. That
offloads tile and splat drawing, but requires a GPU server and low-latency
network path; it is a different delivery architecture, not a faster local
loader. The cited CloudXR documentation predates this four-month search window.

## Practical Quest 2 experiment

1. **Correct the render path before setting budgets.** Use the headset
   `ArrayCamera` for globe and custom tile traversal and stereo viewport
   resolution each frame; call tile updates after XR pose/camera update and
   before rendering, and call `Scheduler.setXRSession()` on entry/exit. Change
   `GlobeManager.update()` itself so it does not re-register the desktop camera.
   Use one shared Spark renderer. Collapse redundant full stereo passes; their
   count currently depends on the highest defined render order even when
   layers are hidden. Preserve depth between opaque globe/tiles and splats;
   isolate intentional overlays. The XR camera uses near 0.25 m and far 100,000
   km; test depth precision and reduce the range or use a supported depth
   strategy if z-fighting occurs. The
   [Spark renderer documentation](https://sparkjs.dev/docs/spark-renderer/)
   says splats depth-test but do not write depth by default; transparent globe
   tiles need explicit compositing tests. Repeat the old custom-tiles-on/off
   headset comparison immediately after this repair.
2. **Make a low-cost measured baseline.** Carry over the app's tested
   `lodRenderScale=4` setting, then A/B `antialias: false`, `maxStdDev=sqrt(5)`,
   `maxPixelRadius` and `splat.maxSh=0` with headset images and timings. The
   [app review](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
   found 512→64/32 pixel-radius caps reduced obscuring large splats and gave a
   small desktop GPU gain; Quest performance and quality remain untested. Spark
   [documents](https://sparkjs.dev/docs/performance/) the smaller Gaussian
   footprint for VR. Trial the separate prototype's 196,608 visible
   splats with a _smaller initial page pool than 8M_ once tiles are enabled;
   determine pool size from actual coverage and memory, not by copying a
   splat-only preset. Maintain a coarse RAD overview so there are no holes
   during page fetch. Measure page residency and churn, failed ranges, tile
   cache bytes/refusals, shader cost, and browser renderer memory together.
3. **Spend detail where it is seen.** Keep the total splat count capped; use
   Spark's
   [cone foveation and per-mesh `lodScale`](https://sparkjs.dev/docs/lod-getting-started/)
   to refine the view direction while coarsening the periphery and behind-head
   content. Per-mesh `lodScale` can favor a story subject only if it is a
   separate `SplatMesh`; the current Ronda capture is one mesh. Raise
   `errorTarget` for background tilesets and lower it on a separately managed
   focus tileset, using measured tile counts and texture memory. Expose those
   values first: the viewer hardcodes 20 in `TilesLayer` and resets the globe's
   target to 20 every update. A single tileset needs custom selection logic for
   region-specific error targets. Maintain a local origin/transform for the
   splat and georeferenced tile data. The viewer's patched Spark
   camera-relative accumulation helps global precision, while 16-byte compact
   splats require local coordinates for fine
   detail;
   [extended storage doubles bytes per splat](https://sparkjs.dev/docs/lod-getting-started/#handling-huge-coordinates).
4. **Preserve center sharpness only after fragment cost falls.** Test 0.8, 0.9,
   and 1.0 XR framebuffer scale at identical camera paths. Meta's
   [fixed foveated rendering](https://developers.meta.com/horizon/documentation/web/webxr-ffr/)
   reduces peripheral fragment work on the final eye buffer; verify that it is
   active with `ovrgpuprofiler -t`, especially if render targets or passes
   change. Prefer more real nearby splat/tile detail and a sharp center over
   globally increasing resolution: framebuffer scaling cannot reconstruct absent
   capture detail. `focalAdjustment=1.5` yielded little visible gain in the
   [Quest 2 prototype](https://github.com/splnlss/threejs-webxr-worldmodels-test/blob/1140d3a5b3c1a84ba976a8fb10c15e155fcf8804/docs/quest-sparkjs-handoff.md).
5. **Gate on the combined scene, then consider new formats.** Repeat the same
   path for globe only, RAD only, custom tiles only, globe+RAD, and all three.
   Record p50/p95 XR callback intervals _and_ CPU/GPU frame time, dropped
   frames/black bands, splat selection, page pool residency, tile cache/queues,
   and memory. Meta documents
   [90 Hz default on Quest 2](https://developers.meta.com/horizon/documentation/web/webxr-frames/)
   (about 11.1 ms per application frame) and
   [OVR Metrics, GPU stage tracing, and `xr.debug` CPU traces](https://developers.meta.com/horizon/documentation/web/webxr-perf-tools/).
   A lower supported target rate can be tested if 90 Hz is unattainable; it is
   not a substitute for diagnosing sustained overload. If the same captures must
   span large areas, first compare Gaussian-Splat-Lite and Spark on the same RAD
   in an isolated spike. Then compare a small converted SPZ 3DGS tileset using
   plugin 0.3 plus Gaussian-Splat-Lite against GSL paged RAD at matched visual
   quality. Measure cold load, head-turn latency, renderer memory, and depth
   behavior. GSL's optional splat depth draw adds work, so measure it separately.

## Open questions

- After correct XR tile selection and depth composition, can a representative
  custom tileset fit a Quest 2 frame? Existing tests show severe combined
  slowdown and high GPU activity, but not the corrected path's final headroom.
- Does disabling MSAA reduce Quest 2 cost materially in this viewer, and does it
  harm opaque tile edges or text? Spark's guidance supports a trial, not a
  guaranteed win for the whole scene.
- Can the Ronda capture be re-tiled as SPZ without losing useful RAD spatial LoD
  or requiring excessive tile churn? What conversion path preserves its
  georeferencing and opacity?
- Is the current black edge band due chiefly to missed frames, page churn, or
  scene coverage? Spark's `sortRadial` is already enabled by default, so simply
  enabling radial sorting is not an available fix. Confirm with headset traces.
- Does the 0.25 m–100,000 km XR depth range cause z-fighting where local splats
  overlap terrain or custom tiles? Test both eyes at the house, horizon, and
  near-camera geometry before changing the depth strategy.
- What final visual threshold is acceptable for the story's focus objects? The
  human owner should choose this after headset A/B images and comfort checks.
