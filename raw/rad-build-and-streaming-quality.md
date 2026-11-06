# RAD build and streaming quality source capture

A supplied handoff explains how to build a high-quality RAD file, choose viewer budgets, and diagnose streaming gaps.

Source: User-provided attachment `rad-build-and-streaming-quality.md` (no public source link supplied). Relative links in the captured text refer to the source prototype.

## Captured text

# Building and viewing large RAD splats

This is a starting point for comparing a detailed `.rad` scene with a `.sog` scene made from the **same capture**. A RAD file contains a prebuilt level-of-detail (LoD) tree; Spark streams its pages and chooses which splats to render. Build quality, resident page capacity, visible splat budget, and source-capture coverage are separate limits.

## Build the RAD

Run this command **in the SparkJS repository**, which provides the `build-lod` script (this prototype does not):

```bash
npm run build-lod -- scene.sog --quality --gsplat --rad
# output: scene-lod.rad
```

- `--quality` selects the higher-quality `bhatt-lod` builder. Its standard base is 1.75; keep that starting value before experimenting with `--bhatt-lod=<base>`.
- `--gsplat` selects the higher-precision splat encoding. The current Spark CLI defaults to both quality mode and gsplat, but explicit flags make the choice clear. Avoid `--quick` and `--csplat` when comparing maximum source fidelity.
- Keep the full input capture. `--min-box`, `--max-box`, and `--within-dist` deliberately remove splats outside the selected region.
- Leave spherical harmonics (SH) at the source's available degree unless size or render cost requires a lower `--max-sh`. SH changes view-dependent color, not spatial coverage. This prototype renders RAD at SH degree 0 under Quest presets, regardless of higher SH retained in the file.
- A single `.rad` is the simplest deployment. `--rad-chunked` creates a `.rad` header plus `.radc` files; serve **every** generated chunk at the expected relative path. Chunking changes delivery, not image resolution.

The builder cannot create detail missing from its source, and these flags do not force all RAD pages to stay loaded in a browser. See the [Spark LoD guide](https://sparkjs.dev/docs/lod-getting-started/) and [current build-lod CLI source](https://github.com/sparkjsdev/spark/blob/main/rust/build-lod/src/main.rs). Check `npm run build-lod` in the Spark checkout for the exact options of the version used to build an asset.

## Start with the viewer budget

The current values below come from [`src/quality.ts`](../src/quality.ts) and [`src/gaussianSplatLoader.ts`](../src/gaussianSplatLoader.ts). `lodSplatCount` limits selected visible detail; `maxPagedSplats` controls the resident streaming pool. Raising either can increase GPU work or memory use. Spark documents both in its [renderer options](https://sparkjs.dev/docs/spark-renderer/).

| Target | Starting setting | Evidence and limit |
| --- | --- | --- |
| Desktop with ample GPU memory | Try `?quality=default` to use Spark's platform splat-count and page-pool defaults; measure frame rate and memory. | The prototype's normal desktop preset is 524,288 visible / 1,048,576 paged splats. `default` removes those explicit limits, so it is a diagnostic trial, not a proven setting for every desktop. |
| Quest 2 | `?quality=quest2`: 196,608 visible / 8,388,608 paged splats, two fetchers, 0.9 XR framebuffer scale. | Measured on Quest 2 with the Ronda RAD. Keep one heavy scene tab open. |
| Quest 3/3S | `?quality=quest3`: 327,680 visible / 12,582,912 paged splats, three fetchers, full XR framebuffer scale. | An unverified starting point; measure on the actual headset. |

The [Quest observations](quest-sparkjs-handoff.md#what-the-quest-2-tests-showed) found that larger page pools improved Ronda coverage considerably, while higher visible-splat budgets could cause stutter. No build setting alone resolved those runtime limits. LoD streaming continuously replaces pages near the viewer; it does not aim to hold a large scene's complete finest-resolution data in memory.

## Diagnose persistent missing areas

1. Compare the same capture from the same viewpoint. Stop moving long enough for page requests to settle. A different source capture can have different missing imagery.
2. Add `?metrics=1` (or `&metrics=1` when the URL already has options), then inspect `window.__sparkStats()` in DevTools. A full `residentPages` pool plus remaining `wantedPages` suggests page-replacement pressure; it does **not** prove the cause of every visible hole. The reported range-request counters are limited to the prototype's Ronda proxy route.
3. Check the browser Network panel for failed RAD requests. A single RAD server must support HTTP byte ranges (normally HTTP 206 responses). For chunked RAD, verify that every referenced `.radc` is reachable. If requests succeed and the pool has space, compare the source splat and RAD at the same pose before changing builder settings.

The loader creates RAD meshes with `paged: true`; it does not build a second browser LoD tree. See the [loader source](../src/gaussianSplatLoader.ts) and [Quest diagnostic notes](quest-sparkjs-handoff.md#rad-loading-and-rendering-choices).
