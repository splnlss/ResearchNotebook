---
type: Review
title: Independent review of the WebXR testing strategy
description: The testing recommendation now separates draft and canonical checks, scopes replay to implemented behavior, and requires device telemetry and multi-object evidence.
tags: [webxr, quest-2, testing]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# Independent review of the WebXR testing strategy

The [testing recommendation](webxr-testing-strategy.md) incorporates five
evidence-based corrections. Its proposed tools still need a pilot on the pinned
Quest Browser and viewer build.

## Objections and resolutions

1. **Hosted origin:** The reference [deployment guide](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/MILESTONE_4_DEPLOYMENT.md)
   restricts Google/Cesium tokens to the canonical hostname. A random Netlify
   draft can check identity, routing, and static assets; full hosted scene
   checks follow approved promotion or use a separately configured staging
   origin. The recommendation now separates those checks.
2. **Input scope:** The first [Quest acceptance plan](https://github.com/splnlss/3d-story-app/blob/eff39e42d2c7d2cd0b3e0c9b48d8de0437d10d44/docs/WEBXR_QUEST_2_TESTING.md)
   requires thumbsticks to leave the camera still. IWER trials now cover that
   policy, head pose, sessions, and layers; locomotion replay applies only to a
   branch that implements it.
3. **Telemetry:** [OVR Metrics](https://developers.meta.com/horizon/documentation/native/android/ts-ovrmetricstool/)
   Basic supplies app GPU time, FPS, and stale frames. A [Browser `xr.debug`
   trace](https://developers.meta.com/horizon/documentation/web/webxr-perf-tools/)
   supplies CPU frame cost. The [navigation follow-on log](https://github.com/splnlss/3d-story-app/blob/29fe296c8c46e0e0c70476a003ae376d212aab2d/docs/reviews/29-webxr-navigation.md),
   rather than the first headset log, records failed OVR CSV capture. The
   recommendation now attributes each observation to its source.
4. **Scene coverage:** The acceptance plan requires two distinguishable RAD
   objects, an opaque mesh, layer-1/2 markers, sorting, paging, and cleanup.
   The one-RAD fixture cannot pass this gate. The recommendation now lists a
   dedicated multi-object pass and the shared splat budget.
5. **Replay feasibility:** [IWER's native override](https://meta-quest.github.io/immersive-web-emulation-runtime/api/native-webxr-override.html)
   can preserve native rendering and frame timing, but browser hooks may fail
   after session start and synthetic inputs replace native ones. The strategy
   now requires a runtime capability check and an unpatched physical-input
   comparison before drawing performance or comfort conclusions.

## Open questions

- Will the pinned Quest Browser expose enough native IWER override capability
  to run a useful replay?
- Can the next headset run capture both OVR or VrApi performance data and the
  missing multi-object visual evidence?
