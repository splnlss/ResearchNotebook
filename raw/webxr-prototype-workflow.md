# WebXR prototype workflow source capture

A supplied guide describes Quest 2 development, services, geospatial conventions, deployment, and prototype records.

Source: User-provided attachment `WEBXR_PROTOTYPE_WORKFLOW.md` (no public source link supplied).

## Captured text

# WebXR prototype workflow: Mac, Quest 2, Colorado assets

This guide is reusable across repositories. Replace `<port>`, `<project-ref>`, `<site-id>`, and branch names with the values for the prototype. Check that repository's own instructions before running its build, database, or deployment commands. The Colorado scope and accuracy targets below are shared conventions for this family of prototypes.

## 1. Edit and test on a USB-connected Quest 2

1. On the Mac, check out the intended branch, pull its latest changes, install the repo's pinned tools, and start its local app and any local database. Keep one known app port for a test run. Note the exact Git commit and confirm the app's health page or build identifier if it has one.
2. Enable Developer Mode on Quest, connect it to the Mac with a USB data cable, put on the headset, and accept **Allow USB debugging**. Bluetooth pairing is unnecessary. `adb devices` must show `device`, rather than `unauthorized`.
3. Map the Mac's app port into Quest and open the URL **in Quest Browser**:

   ```bash
   adb devices
   adb reverse tcp:<port> tcp:<port>
   adb reverse --list
   # In Quest Browser: http://localhost:<port>
   ```

   If the browser must call a database running on the Mac, reverse that API port too. Use the exact origin configured in the app and in the asset bucket's CORS policy. When another local server owns the usual port, choose a different free port and change the reverse mapping and CORS origin together. [Meta's Browser debugging guide](https://developers.meta.com/horizon/documentation/web/browser-remote-debugging/) documents this setup.

4. Edit on the Mac, let the dev server rebuild, then reload the Quest tab. In Mac Chrome, open `chrome://inspect/#devices` to inspect the Quest Browser tab's console, network requests, WebGL state, and JavaScript. A desktop browser or emulator is useful for quick checks; verify stereo, head motion, comfort, and frame rate on the physical headset.
5. For visual evidence, use Quest's **Sharing → Record Video**, or record from the Mac:

   ```bash
   adb shell screenrecord --time-limit 60 /sdcard/quest-prototype.mp4
   adb pull /sdcard/quest-prototype.mp4 ./quest-prototype.mp4
   ```

   The command runs until its limit or `Ctrl+C`. A recording can change performance and shows a captured view, not what both eyes perceived; stop recording for timed performance runs. See [Meta's capture guide](https://developers.meta.com/horizon/documentation/native/android/mobile-testing-capture/) and [Android's ADB guide](https://developer.android.com/tools/adb#screenrecord).

6. For performance, enable the [OVR Metrics Tool](https://developers.meta.com/horizon/documentation/web/webxr-perf-tools/) and log app CPU/GPU frame time, FPS/dropped frames, memory or thermal state, visible splats, and asset paging. At Quest 2's default 90 Hz WebXR rate, the frame budget is about **11.1 ms**; missed frames can cause black side bands during head turns. Test one change at a time at the same camera pose and asset load, preferably A/B/A. Measure while the headset is awake and worn; use separate head-turn passes for visual judgment. [Meta's frame-rate guide](https://developers.meta.com/horizon/documentation/web/webxr-frames/) explains the budget.

## 2. Supabase: connect, create, duplicate, change

Decide whether two frontends should **share content and accounts**. If yes, point both at the same Supabase project; a second Netlify site does not require a second database. Create a separate Supabase project when data, Auth users, or destructive experiments need isolation.

| Need                                  | Approach                                                                                                                                                                                                                                                                                                              |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Clean prototype                       | Create a new project in the Supabase Dashboard, apply the prototype's ordered migrations, and import only approved fixture content.                                                                                                                                                                                   |
| Copy existing data and Auth users     | Use Supabase's [Restore to a new project](https://supabase.com/docs/guides/platform/clone-project) when available. It copies the database, but Storage objects, Edge Functions, Auth settings, API keys, and some other settings need separate configuration. Review copied jobs and webhooks before using the clone. |
| Another frontend for the same content | Reuse the existing project's URL and browser key. Configure any new Auth redirect hostname if that frontend uses email or OAuth callbacks.                                                                                                                                                                            |

To connect a local repo to a **chosen** remote project, use the [Supabase CLI workflow](https://supabase.com/docs/guides/local-development/cli-workflows):

```bash
supabase login
supabase projects list
supabase link --project-ref <project-ref>
supabase migration list
```

Put the project's URL and **publishable key** (or legacy anon key required by the app) in ignored local environment files and the hosting site's browser environment variables. Never put a secret/service-role key in a browser bundle or an ordinary hosted frontend; those keys bypass Row Level Security. Check the RLS policies with anonymous and signed-in users. Supabase's [API key guide](https://supabase.com/docs/guides/getting-started/api-keys) describes the current key types.

Make schema changes as new, ordered migration files. Test them against the local stack and its policy tests, then inspect the linked remote, back it up, and dry-run before applying an approved remote change:

```bash
supabase migration new <change-name>
supabase start                 # local stack
supabase migration up --local  # apply pending migrations locally
supabase db push --dry-run     # preview the linked remote change
supabase db push               # remote mutation: run only after review/approval
```

Regenerate database types if the app uses them. Do not edit an already-applied migration, run `db reset --linked` on production, or seed production with development fixtures. Supabase's [migration guide](https://supabase.com/docs/guides/deployment/database-migrations) explains the migration history. For passwordless email, reset links, or OAuth on a new hostname, add that exact origin/callback in [Auth redirect URLs](https://supabase.com/docs/guides/auth/redirect-urls/) and test it.

## 3. Load shared files from Cloudflare R2

The main shared base for Gaussian splat assets in these prototypes is:

```text
https://pub-dd92ae5131ec49f1bbd411b51a858249.r2.dev
```

Append the **exact object path** from the Cloudflare R2 bucket inventory or an asset manifest. For example, one Ronda splat is `https://pub-dd92ae5131ec49f1bbd411b51a858249.r2.dev/20260820_RondaLobato.rad`. Other R2 buckets may serve 3D Tiles, meshes, images, or additional splats. Inventory every asset host used by a scene; the main bin is not a complete asset list. A public `r2.dev` URL can serve an object without Cloudflare credentials; it does **not** list bucket contents at its root. Cloudflare says these development URLs are rate-limited and intended for testing. [Public bucket guide](https://developers.cloudflare.com/r2/buckets/public-buckets/).

Give the full URL to the viewer's matching loader: `.rad`/`.sog`/`.spz`/`.ply` to the splat loader, or `tileset.json` to the 3D Tiles loader. A paged RAD may fetch byte ranges or relative sidecar files; a tileset may fetch many child tiles and textures. Every fetched object needs to exist and be readable. For **each underlying bucket**, configure its [CORS policy](https://developers.cloudflare.com/r2/buckets/cors/) to allow the app's exact origin (including port), `GET`/`HEAD`, and any `Range` header used by paging. Expose `Content-Range` and `Accept-Ranges` when the loader reads them. A policy on the main Gaussian bucket does not cover a separate tileset or image bucket. A URL opening directly in a tab does not prove CORS works inside the app. For a paged RAD, verify a ranged request returns `206` and inspect the Quest Browser network panel.

Record each asset's path, format, version or checksum, source, Colorado site, and placement transform. Use a new object name for a changed asset so old prototypes remain reproducible.

To manage the bucket, sign in to the correct Cloudflare account in **R2 Object Storage**. For CLI work, [Wrangler's R2 guide](https://developers.cloudflare.com/r2/get-started/cli/) uses `wrangler login` and `wrangler r2 object put <bucket>/<object-path> --file <local-file>`. A public asset URL is for reading; it does not grant permission to upload. Check the target bucket and object path before writing, and use a new path for a revised asset. Bulk transfers can use R2's S3-compatible API with a bucket-scoped credential kept outside the repo.

## 4. Publish a repository as a new Netlify project

For a new Git-connected prototype, choose **Add new project → Import an existing project** in Netlify, authorize the Git provider, choose the repository and production branch, confirm framework build settings, set the environment variables, and deploy. A push to the chosen production branch then triggers a new deploy; preview/branch behavior depends on the site's settings. For a Next.js app, Netlify currently suggests `next build` and `.next`, but use the repo's tested configuration. [Add new project](https://docs.netlify.com/manage/projects/add-new-project/) · [Next.js settings](https://docs.netlify.com/snippets/frameworks/nextjs-config-values/).

For a **new** prototype that must stay **unlinked from Git**, create a separate blank Netlify project and upload a draft from the reviewed checkout:

```bash
netlify login
netlify sites:create --name <prototype-site-name>
netlify deploy --site <site-id>  # draft URL; does not replace the live site
```

Inspect the draft URL, `/api/health` if present, login/anonymous behavior, globe, asset network requests, and Quest Browser VR. Then publish that **exact successful deploy** through its Netlify Deploys page when it is approved; Netlify's **Publish Deploy** action does not rebuild it. `netlify deploy --prod` is a separate production upload, so do not assume it is the tested draft. [CLI deploy guide](https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/) · [Manage deploys](https://docs.netlify.com/deploy/manage-deploys/manage-deploys-overview/).

Use that raw CLI recipe only for a newly created site whose release process you control. For an existing guarded or production site, follow its repository's audited deployment helper and approval process; raw `netlify deploy` commands may skip commit, environment, smoke-test, and publication checks.

Configure the new site's Supabase URL/browser key, viewer-provider tokens, and any other required values **before the build**; framework public variables are embedded in client output. Bind the deploy to the intended Netlify site ID and Git commit. Use separate sites for desktop and headset when they need separate release schedules; they can still use the same Supabase project. Never copy a database secret into Netlify's browser build. Check site-specific repo rules for approval before creating a site, changing its variables, or publishing.

## 5. Connect the other services

| Service                                                                                                 | Connection and check                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [GitHub](https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git)      | Run `gh auth login` on the development machine, then `gh auth status`. Confirm `git remote -v` points to the intended repository before pulling or opening a PR. Netlify's Git import asks for a separate GitHub app authorization.                                                                                                                                                                    |
| [Meta developer tools](https://developers.meta.com/horizon/documentation/web/browser-remote-debugging/) | Enable Developer Mode for the Quest account and headset, install Android Platform Tools on the Mac, approve USB debugging inside the headset, and confirm `adb devices` shows `device`. A Meta app-store submission is unnecessary for testing a web URL in Quest Browser.                                                                                                                             |
| [Google Map Tiles API](https://developers.google.com/maps/documentation/tile/get-api-key)               | In the intended Google Cloud project, enable billing and the Map Tiles API, create an API key, restrict it to the required API and supported application origins, and test both localhost and the deployed site. Monitor its [usage and quota](https://developers.google.com/maps/documentation/tile/usage-and-billing). Browser keys are exposed to viewers, so do not treat them as private secrets. |
| [Cesium ion](https://cesium.com/learn/ion/cesium-ion-access-tokens/)                                    | Sign in to ion, create an application token with only the needed read scope/assets and allowed site URLs, and test the exact local and deployed origins. Record which ion assets the token may access.                                                                                                                                                                                                 |
| [Cloudflare R2](https://developers.cloudflare.com/r2/get-started/cli/)                                  | Select the right Cloudflare account and bucket; use Dashboard or Wrangler for object management. The public URL and CORS setup are in section 3. A separate bucket-scoped R2 API credential is needed only for S3-compatible upload tools.                                                                                                                                                             |

Use [Doppler](https://docs.doppler.com/docs/cli) when a prototype shares configuration across machines or deployments. Sign in, then associate the **current directory** with the intended Doppler project and development config; verify the selection before running the app:

```bash
doppler login
doppler setup --project <doppler-project> --config <dev-config>
doppler configure --scope "$(pwd)"
doppler run --project <doppler-project> --config <dev-config> \
  --only-secrets NEXT_PUBLIC_GOOGLE_MAPS_TOKEN,NEXT_PUBLIC_CESIUM_TOKEN \
  -- <start-command>
```

The last command injects only those two values into its child process. Replace the names with the prototype's actual viewer variables; omit a provider the app does not use. Doppler's [project/config setup](https://docs.doppler.com/docs/secrets-setup-guide) is directory scoped, so check it again after cloning into another folder. Keep separate development and deployment configs, pass a CI service token only to the job that needs it, and do not print or export the full secret set into logs or tracked files. A browser build embeds its public variables even if Doppler supplied them; keep server-only credentials out of that build. Netlify's build environment is configured separately in section 4, and Supabase's project connection is in section 2.

## 6. Geospatial contract for Colorado prototypes

Only add **Colorado sites** to this prototype family. Check a new site's source coordinates against a Colorado boundary and retain the source datum and capture provenance. A location that looks close on a map is not enough to validate a transform.

| Field               | Shared convention                                                                            |
| ------------------- | -------------------------------------------------------------------------------------------- |
| Longitude, latitude | WGS84 geodetic decimal degrees, in that order                                                |
| Altitude            | WGS84 **ellipsoidal** height in meters; convert surveyed/geoid or terrain heights explicitly |
| World position      | WGS84 Earth-centered, Earth-fixed (ECEF), meters, double precision for anchors               |
| Local movement      | East-north-up (ENU) meters relative to a declared WGS84 anchor                               |
| Splat rotation      | Model-local XYZ degrees; scale is dimensionless                                              |
| Camera orientation  | Pitch, yaw, roll in degrees; record the camera pose used for visual comparisons              |

The numerical transport target is **under 2 cm added displacement** through forms, APIs, storage, and viewer. Preserve each JavaScript number exactly at pure data boundaries; WGS84 conversion error should be at most 1 mm, and a 1 cm ENU adjustment should add less than 5 mm numerical error over the tested 0–50 km range. These targets describe software arithmetic, **not** source survey accuracy, terrain accuracy, or rendered pixel accuracy. Never round coordinates, heights, angles, offsets, or transforms before saving. Zero is valid. Validate canonical geospatial objects at persistence and viewer boundaries, and keep the anchor, model transform, and any display locator distinct.

## 7. How the viewer combines the layers

A viewer built on [Three.js](https://threejs.org/docs/) maintains one camera and scene in an Earth-centered frame. Layer definitions tell it which content to load and where to place it. A typical stack uses [3d-tiles-renderer](https://github.com/NASA-AMMOS/3DTilesRendererJS) to stream the Google photorealistic globe and custom [OGC 3D Tiles](https://www.ogc.org/standards/3dtiles/) from `tileset.json`, and [Spark](https://sparkjs.dev/docs/) to draw Gaussian splats in the same Three scene. Google's globe comes from the [Map Tiles API](https://developers.google.com/maps/documentation/tile/overview) and needs its own restricted browser token.

| Content                            | Viewer input                                                      | Rendering path                                                                                                                                                                                                                                         |
| ---------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Globe, photogrammetry, tiled LiDAR | `tileset.json`                                                    | 3D Tiles hierarchy selects and streams visible tiles through `3d-tiles-renderer`. Here, LiDAR is **already converted to 3D Tiles**; raw LAS/LAZ is not a direct layer URL.                                                                             |
| Gaussian captures                  | `.rad`, `.sog`, `.spz`, or `.ply` URL plus WGS84 anchor/transform | Spark's `SplatMesh` loads and sorts splats; `.rad` can carry prebuilt LoD and stream pages. Multiple splat objects should share one Spark renderer and a measured scene-wide budget. [Spark LoD guide](https://sparkjs.dev/docs/lod-getting-started/). |
| Models and annotations             | Georeferenced mesh URL (for example GLB), GeoJSON, or app data    | Ordinary Three meshes and overlays use the same camera and depth/layer ordering.                                                                                                                                                                       |

The viewer library is an integration layer, not the source of the assets: it manages content-layer visibility, camera/geospatial placement, and render passes; the loaders fetch from Google, Cesium, R2, or another provider. The [`@splnlss/3d-story-viewer` package](https://www.npmjs.com/package/@splnlss/3d-story-viewer) is one implementation used by this prototype family. Pin and record its version alongside Three, Spark, and the 3D Tiles renderer. In VR, verify both eyes, depth, and splat visibility on the headset; hiding a layer visually does not by itself prove its loader stopped updating. Choose LoD, framebuffer scale, and antialiasing from measured Quest results rather than a desktop preview.

## 8. Small record for each prototype

Keep a short manifest or run log with: repository/branch/commit; Netlify site and deploy ID; Supabase project reference and whether it is shared or isolated; Colorado site and WGS84 anchor; asset URLs and checksums; viewer/library versions; Quest and Browser versions; test port; camera pose; visual notes; CPU/GPU/FPS readings; and the rollback or teardown plan. Store captures outside tracked assets unless they are deliberately approved for the repository. This makes a new prototype reproducible and prevents it from silently using another prototype's database, deployment, or asset version.
