---
type: Recommendation
title: A repeatable WebXR testing strategy for Quest 2
description: Keep physical Quest testing, add desktop WebXR input replay and hosted HTTPS checks, and measure frame cost with device telemetry rather than video cadence.
tags: [webxr, quest-2, testing, performance]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# A repeatable WebXR testing strategy for Quest 2

Use automated desktop checks and a repeatable physical Quest run for each
development candidate. At release, check the same build over hosted HTTPS.
The current USB `adb reverse` loop is a supported, useful
local transport. The improvement is a tighter test protocol and device
telemetry, with wireless Quest tools and input replay added where they save
time. This recommendation is based on [Meta's Browser guidance](https://developers.meta.com/horizon/documentation/web/browser-remote-debugging/),
the [supplied workflow](../raw/webxr-prototype-workflow.md), and the reference
project's [Quest acceptance plan](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/WEBXR_QUEST_2_TESTING.md).

## What the current approach proves

USB forwarding to `http://localhost:55000` lets Quest Browser run the Mac build
and local Supabase after forwarding both ports. Mac Chrome can inspect the live
Quest tab. The reference project's [headset review log](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/reviews/webxr-quest-2.md)
reports that this path exposed a splat visibility bug and severe frame pacing
that desktop tests had not established. It also records a sleeping-headset
sample. The [navigation follow-on review](https://github.com/splnlss/3d-story-app/blob/29fe296c8c46e0e0c70476a003ae376d212aab2d/docs/reviews/29-webxr-navigation.md)
records an unsuccessful OVR Metrics CSV capture. These runs show why a named
build, awake headset, and second telemetry path matter. The first log also
records a usable `ovrgpuprofiler` GPU utilization sample.

The loop's limits are specific: manual controller and head motions are hard to
repeat; a local origin does not exercise deployed Auth callbacks, browser keys,
or asset CORS; and a recording's frame cadence is not the headset's rendered
frame rate. The [project acceptance plan](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/WEBXR_QUEST_2_TESTING.md)
already separates desktop checks from local and hosted headset checks.

## Alternatives and tradeoffs

- **Desktop unit tests and Playwright:** Fastest repeatable check for session
  lifecycle, unsupported browsers, controller policy, navigation math, and exit
  cleanup. The project already has these. They cannot establish headset stereo,
  comfort, or Quest GPU cost, as its acceptance plan states.
- **IWER desktop emulation:** [Meta recommends IWER with a physical headset](https://developers.meta.com/horizon/documentation/iwsdk/guides/02-testing-experience/).
  Its [standalone runtime](https://meta-quest.github.io/immersive-web-emulation-runtime/getting-started.html)
  can simulate XR poses and inputs without moving this custom Next.js/Three
  viewer to IWSDK. A development-only proof of concept is warranted for
  head pose, session, and controller-policy regressions. The first release's
  acceptance plan requires thumbsticks to leave the camera still. Emulated
  timing is not Quest timing.
- **Wireless Quest Browser via MQDH:** [Meta calls ADB over Wi-Fi the preferred
  Browser debugging connection](https://developers.meta.com/horizon/documentation/web/browser-mqdh/).
  MQDH can cast the device and send a URL from the Mac, reducing cable and URL
  entry friction. It still runs on real Quest hardware. Keep USB available for
  initial authorization and when Wi-Fi is unreliable; local API access still
  needs a working route.
- **Meta VR CLI:** [Meta's device commands](https://developers.meta.com/horizon/essentials/metavr-devices-and-apps/)
  can check device health, wake it, and prepare a test session; its [performance
  commands](https://developers.meta.com/horizon/essentials/metavr-performance/)
  can capture Perfetto traces. This could reduce missed asleep-headset runs and
  help diagnose slow frames. Pilot its trace against Quest Browser before using
  it as the app's performance record.
- **IWER native override and action playback:** [IWER can replay tracking and
  controller input inside a native WebXR session](https://meta-quest.github.io/immersive-web-emulation-runtime/api/native-webxr-override.html),
  retaining the browser's stereo rendering and frame timing. This is a strong
  later candidate for repeatable pose and scene-load runs, and for locomotion
  if the tested branch implements it. It requires app integration, a user
  gesture to start XR, and a runtime capability check on the pinned Quest
  Browser: the [native override documentation](https://meta-quest.github.io/immersive-web-emulation-runtime/api/native-webxr-override.html)
  warns that some browser hooks can fail after session start. Synthetic input
  replaces native input, so compare timing with an unpatched session at a
  matched pose and keep a real-input pass for comfort and controller behavior.
- **Hosted HTTPS check:** A draft can check deployed routing, build identity,
  and static assets. The reference app's [production token policy](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/MILESTONE_4_DEPLOYMENT.md)
  authorizes Google/Cesium only on its canonical host, so full-scene Quest
  testing there occurs after approved promotion. A separate staging site could
  test the full scene before production if it has its own authorized origins
  and service configuration. [Meta's WebXR guide](https://developers.meta.com/horizon/documentation/web/port-vr-xr/)
  allows HTTP localhost for development and requires HTTPS for hosted WebXR.

A full IWSDK migration is an architectural option for a new app, since Meta
[recommends it for new 3D web work](https://developers.meta.com/horizon/documentation/web/3d-web/).
The existing custom viewer can test IWER independently; no source found shows
that an IWSDK migration by itself fixes its Spark frame cost.

## Recommended test protocol

1. **Freeze the candidate:** Record app and viewer commits, installed package
   versions, asset URLs/checksums, scene/camera pose, Quest OS/Browser version,
   and local ports. Use a clean production build for the timed headset pass;
   confirm `/api/health` or build identity before entering VR.
2. **Run fast checks:** Keep the current unit, precision, and Playwright gates.
   Trial IWER in a development-only harness for controller input policy,
   session failure/reentry, head pose, and layer visibility. Keep a real-headset
   pass for all
   visual and performance claims.
3. **Run the local Quest pass:** Start with USB `adb reverse` for app and local
  Supabase. Try MQDH Wi-Fi, casting, and URL launch when it makes repeated
  headset runs easier. Confirm the headset is worn, the XR session is visible,
  both ports work, and the exact build and assets loaded. Use the same pose,
  scene, and asset warm-up for A/B/A comparisons. A Meta VR CLI health check
  may help catch a sleeping or disconnected device before timing starts.
4. **Measure without video recording:** Use [OVR Metrics Tool's basic HUD or
   CSV](https://developers.meta.com/horizon/documentation/unity/ts-ovrmetricstool/)
   for FPS, app GPU time, and stale frames. Record thermal state when available.
   Get browser CPU frame cost from a [Chrome `xr.debug` trace](https://developers.meta.com/horizon/documentation/web/webxr-perf-tools/).
   If OVR CSV fails, collect [VrApi logcat stats](https://developers.meta.com/horizon/documentation/native/android/ts-logcat-stats/)
   as another device-level record.
   Capture WebXR callback intervals and Spark paging counters as separate app
   diagnostics. At the default 90 Hz Quest 2 Browser rate, the frame budget is
   about 11.1 ms; the reported session target is not achieved FPS.
5. **Diagnose the bottleneck:** Compare a fixed scene with splats hidden, globe
   hidden, and normal layers, one change at a time. If the GPU dominates, test
   render scale and splat LoD separately; if CPU dominates, use the Chrome
   trace. [Meta's profiling workflow](https://developers.meta.com/horizon/documentation/web/webxr-perf-workflow/)
   describes these isolations. The project's successful `ovrgpuprofiler` sample
   makes [Meta's render-stage trace](https://developers.meta.com/horizon/documentation/web/webxr-perf-tools/)
   a direct follow-up for GPU investigation. Keep visual quality and both-eye
   checks beside the timings.
6. **Run the multi-object acceptance scene:** Prepare a second distinguishable
   RAD LoD object and an opaque Three mesh with surfaces before and behind
   splats. Check layer-1 and layer-2 opaque markers in both eyes, the shared
   500,000-splat Spark budget, cross-object sorting, depth, page churn, and
   cleanup after exit. The tracked one-RAD fixture cannot satisfy the
   [project's acceptance gate](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/WEBXR_QUEST_2_TESTING.md).
7. **Verify the release route:** On the reference app's approved Netlify draft,
   check the commit, health route, login redirect, and static assets. After
   approved promotion, repeat entry, controls, globe/splat assets, Auth,
   stereo, and a short Quest performance sample on the canonical HTTPS host.
   A separately authorized staging site can move that full-scene check ahead
   of production. The local result cannot establish hosted configuration.

For the first improvement, prioritize step 4's reliable device telemetry and
step 7's same-commit hosted check. Trial desktop IWER next. Add native action
replay when repeated input and pose testing costs more time than integration.
This order is an inference from the project's recorded failure modes and the
capabilities in the linked tool documentation.

The [independent review](webxr-testing-strategy-review.md) records the
project-specific corrections included in this recommendation.
The next combined globe, tile, and RAD headset run is tracked in
[Quest 2 acceptance issue #168](https://github.com/splnlss/3d-story-app/issues/168).

## Open questions

- Can MQDH's Wi-Fi connection keep the app and local Supabase routes stable on
  the test network, or should USB remain the routine local transport?
- Can the current Next.js viewer accept development-only IWER without changing
  its native XR path or build output?
- Which FPS/frame-time and visual-quality thresholds will the owner accept for
  the initial Quest 2 scene, given the measured baseline is still slow?
- Will the next run produce OVR Metrics CSV, VrApi stats, or both for the exact
  candidate build and a continuously worn headset?
