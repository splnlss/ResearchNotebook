---
type: Recommendation
title: Diagnose blown-out Gaussian splat colors in WebXR
description: When splat colors change only after entering VR, compare the XR output path before changing the asset or blaming headset performance.
tags: [gaussian-splatting, webxr, color-management, vr]
status: draft
---

# Diagnose blown-out Gaussian splat colors in WebXR

**Recommended direction:** Keep the current splat assets unchanged. Test color in the standalone headset and over Air Link with a fixed scene and a neutral reference object, then correct the path that actually changes the image.

## Observed in this case

- The owner reports that a streamed SOG scene in Babylon.js and a RAD scene in a Three.js viewer look correct before **Enter VR**, but pink or blown out in the headset.
- The owner reports that the scene looks better over Air Link. No paired captures, headset model, browser versions, or render settings have been provided, so the cause remains unconfirmed.

## Evidence and interpretation

- WebXR presents an XR framebuffer through a compositor. Its specification says the compositor treats the pixels as sRGB and warns that an extra gamma conversion would make them too bright. This makes an XR color-path mismatch a plausible cause, not a confirmed diagnosis. [WebXR Device API](https://www.w3.org/TR/webxr/)
- Three.js documents that a double sRGB conversion can make the whole scene lighter; custom shaders and postprocessing need correct output conversion. [Three.js color management](https://threejs.org/manual/pages/color-management.html)
- Air Link runs through a Windows PC, and Meta says appearance and performance over Link can differ from standalone headset rendering. Therefore, improvement over Air Link does not by itself prove inadequate headset hardware. [Meta Link guidance](https://developers.meta.com/vr/documentation/unity/unity-link/)

## Next test

1. Use the same splat, URL, starting view, and viewer settings for desktop preview, standalone Quest WebXR, and PC WebXR over Air Link. Record headset, browser, renderer, and software versions.
2. Put an unlit neutral gray or white object beside the splat. Capture both the PC or browser mirror and the headset view after entering VR. A headset capture or recording is needed to assess what the wearer actually sees.
3. If the reference object also changes, compare XR output color space, tone mapping, exposure, and gamma or postprocessing passes. As a diagnostic, set exposure to 1 and temporarily disable extra grading; check that the final color is encoded once.
4. If only the splat changes, inspect its XR shader path, per-eye view-dependent color, opacity blending, sort order, and LOD. If only the headset view changes while the mirror stays correct, investigate the browser or headset compositor.
5. Change one setting at a time and repeat on the standalone headset. Preserve the original assets so desktop appearance remains a valid reference.

## Close-out condition

Record the first setting or code change that removes the color shift, then repeat the same view in desktop preview and standalone WebXR. Keep this page in `draft` until those results and a headset capture are available. Air Link alone is a comparison, not a substitute for the standalone check.

## Open questions

- Which headset, browser, graphics backend, and viewer versions produced each result?
- Does a neutral object also blow out, or only the splat?
- Is the headset capture different from the VR mirror, and does the pink cast vary by viewing angle?
- Does the same asset reproduce the problem on another headset or browser?
