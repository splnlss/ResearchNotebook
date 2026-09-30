---
type: Solution
title: WebXR Colorado prototype setup and review
description: A reproducible Quest 2 prototype needs a pinned app checkout, per-service configuration, verified asset access, headset measurements, and a run manifest.
tags: [webxr, project-setup, quest-2, geospatial]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# WebXR Colorado prototype setup and review

The [supplied workflow](../raw/webxr-prototype-workflow.md) is a sound setup
outline for this Colorado prototype family. It is not a runnable configuration
until a particular prototype records its repository, ports, service identities,
asset inventory, viewer versions, and release route. Headset performance and
asset availability still need direct checks.

## Technical specification

- **Checkout:** Record repository, branch, commit, pinned runtime and package
  manager, app port, and local stack commands. Use that repository's instructions.
- **Quest loop:** Enable Developer Mode and USB debugging; require `adb devices`
  to show `device`. Reverse the app port and any local API port used by Quest
  Browser. Open `http://localhost:<port>` in Quest Browser and inspect it from
  Mac Chrome.
- **Performance:** Test stereo, head motion, comfort, and frame rate on Quest 2.
  Log CPU/GPU frame time, FPS/dropped frames, memory or thermal state, and visible
  asset load at a repeatable pose. Quest 2 Browser's default 90 Hz WebXR session
  gives about 11.1 ms per frame; read the actual session rate when profiling.
- **Data:** Choose shared Supabase for shared content and accounts, or a separate
  project for isolation. Use a browser publishable/anon key with tested RLS.
  Apply ordered migrations locally, inspect a remote dry run, and regenerate
  app types where needed. A database clone carries Auth user records but needs
  separate Storage objects and service settings. Back up the linked remote and
  follow its review/approval process before `supabase db push`.
- **Assets:** Inventory every R2 host and exact object path. Configure CORS on
  each bucket to allow the app origin and loader requests; a restricted policy
  lists exact origins, while public read-only assets may allow `*`. Verify
  ranged RAD requests return `206` and expose needed range headers. Record
  format, source capture site, displayed Colorado site, checksum or version,
  and transform. Plan a custom domain for production R2 traffic.
- **Viewer:** Place one camera and scene in WGS84 ECEF space; stream globe and
  custom `tileset.json` layers through a 3D Tiles renderer and Gaussian
  captures through Spark. Pin viewer, Three, Spark, and tiles-renderer versions;
  verify both-eye visibility, depth, paging, and layer behavior on the headset.
- **Service access:** Confirm the Git remote and GitHub login. Configure a
  restricted browser key for Google Map Tiles and a read-scoped Cesium ion
  token for the assets used. Check Cloudflare account/bucket access. If using
  Doppler, verify its directory-scoped project and config; give the app only
  the variables it needs. Browser-visible tokens remain visible in the build.
- **Release:** For a new prototype, decide whether Netlify is Git-connected or
  a separate, unlinked site. Set public browser values before the build. For an
  unlinked site, inspect a draft and publish that exact successful deploy.
  Follow the repository's deployment helper for an existing guarded site.
  Record site and deploy IDs.
- **Geospatial data:** Limit this prototype family to Colorado sites. Store
  longitude then latitude in WGS84 degrees, ellipsoidal altitude in meters,
  ECEF anchors in meters, and local ENU offsets in meters. Keep source datum,
  capture provenance, anchor, and placement transform distinct.

The numerical contract from the reference project is under 2 cm of **added
software displacement** through transport, with at most 1 mm WGS84 conversion
error and under 5 mm error for a 1 cm ENU adjustment across the tested 0–50 km
range. These are test targets, not survey or visual accuracy claims.

## Reference checkout details

The examined `3d-story-app` checkout pins Node `22.23.2`, pnpm `10.12.1`,
Next.js `15.5.24`, and `@splnlss/3d-story-viewer` `0.8.1` in its
[package manifest](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/package.json).
Its [local stack guide](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/LOCAL_FULL_STACK.md)
uses app port `55000` and Supabase API port `54321`, so Quest testing of that
checkout needs both mappings when the browser calls the local API. Its
[geospatial contract](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/GEOSPATIAL_PRECISION.md)
defines the numerical targets above. These values are a snapshot of that
checkout, not defaults for every prototype.

For that checkout, the local stack guide starts with `pnpm install` and
`pnpm db:start`. Apply migrations with `pnpm db:migrate` on an existing local
database; use `pnpm db:reset` only for a new or deliberately replaced local
database. Then run `pnpm db:env`, `pnpm dev`, and `pnpm stack:check`. The app's
browser token values must be present in its ignored local environment or
selected Doppler config before the viewer can load its providers.

The existing production Netlify site is [required to stay unlinked from
Git](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/MILESTONE_4_DEPLOYMENT.md)
and uses a guarded release helper. Its [layer definitions](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/src/assets/mapLayers.ts)
still point at `r2.dev` hosts, so moving production assets to a custom domain is
a future readiness task rather than a description of its current state. The
Ronda demo's filename does not establish its capture location; record source
provenance separately from its Colorado placement anchor.

## Review findings

- The workflow's main device loop agrees with [Meta's Quest Browser debugging
  guide](https://developers.meta.com/horizon/documentation/web/browser-remote-debugging/).
  Meta's [WebXR frame-rate guide](https://developers.meta.com/horizon/documentation/web/webxr-frames/)
  supports the Quest 2 default and 11.1 ms budget; a measured session can use
  another rate.
- [Cloudflare's public bucket guide](https://developers.cloudflare.com/r2/buckets/public-buckets/)
  confirms that `r2.dev` is rate-limited and intended for development.
  [Its CORS guide](https://developers.cloudflare.com/r2/buckets/cors/) confirms
  exact matching for restricted origins; it also permits `*`. The public base
  URL in the workflow does not establish that every object or sidecar exists.
- [Supabase's restore guide](https://supabase.com/docs/guides/platform/clone-project)
  confirms that restoring to a new project copies database and Auth user data,
  while Storage objects, Auth settings, and API keys need separate work. It is
  a paid-plan feature with physical-backup requirements, so a clean project and
  approved fixtures may be the simpler prototype path.
- [Netlify's CLI guide](https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/)
  distinguishes draft from production deploys. Its [deploy management guide](https://docs.netlify.com/deploy/manage-deploys/manage-deploys-overview/)
  confirms that publishing a successful existing deploy does not rebuild it.
- The reference checkout's [README](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/README.md)
  and local stack guide name viewer `0.5.7`, while its package manifest pins
  `0.8.1`. Record the installed manifest/lockfile version in each run, and
  resolve the stale instructions before using them for setup.

The [independent review](webxr-prototype-setup-review.md) records the
project-specific qualifications incorporated above.
The [testing strategy](webxr-testing-strategy.md) compares faster emulation,
physical Quest checks, and hosted verification.

## Prototype run record

For each run, record repository/branch/commit; tool and viewer versions;
Quest/Browser versions; app and API ports; Supabase project and sharing choice;
Netlify site/deploy ID; displayed Colorado site and WGS84 anchor; asset source
sites, URLs, and checksums; camera pose; visual findings and CPU/GPU/FPS
readings; and rollback or teardown steps. Keep captures outside tracked assets
unless selected for publication.

## Open questions

- Which repository branch, Colorado site, and asset manifest define the first
  prototype to set up from this reusable guide?
- Will a new prototype share Supabase content and accounts, and use a Git-linked
  or unlinked Netlify site?
- Have the Quest Browser load, cross-origin assets, and numerical targets been
  verified on the exact commit and device version recorded for the run?
