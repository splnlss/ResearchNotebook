---
type: Review
title: Independent review of WebXR prototype setup
description: The setup note is usable after clarifying production hosting, R2 CORS and URLs, remote database safeguards, and asset provenance.
tags: [webxr, project-setup, r2]
status: draft
---

<!-- markdownlint-disable-next-line MD025 -->
# Independent review of WebXR prototype setup

The [setup note](webxr-prototype-setup.md) incorporates the following
qualifications. This review checked the supplied workflow against the examined
`3d-story-app` checkout and provider documentation; no Quest run was performed.

## Objections and resolutions

1. **Hosting choice:** Git-linked versus unlinked hosting is a choice for a new
   prototype. The existing [3d-story-app deployment](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/MILESTONE_4_DEPLOYMENT.md)
   requires an unlinked Netlify site and its release helper. The setup note now
   separates these cases.
2. **Remote database change:** A dry run alone does not complete the supplied
   workflow. It also calls for a remote backup and review/approval before
   `supabase db push`. The setup note now includes those steps.
3. **R2 access:** Exact origins are one restricted CORS policy. Public read-only
   buckets may use `*`, as the reference [local stack guide](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/docs/LOCAL_FULL_STACK.md)
   explains. The note now states the actual requirement: permit the requesting
   origin and required headers/methods.
4. **R2 production use:** [Cloudflare](https://developers.cloudflare.com/r2/buckets/public-buckets/)
   recommends `r2.dev` for development, but the reference app's [current
   layers](https://github.com/splnlss/3d-story-app/blob/83c04499c364f8c6dcc05d5a1dec0a5b2eb901bf/src/assets/mapLayers.ts)
   still use it. The note presents a custom domain as a future production task.
5. **Asset location:** The Ronda RAD demo has a Colorado placement anchor, but
   its filename does not establish the capture location. The note now records
   source provenance and display site separately, keeping the Colorado scope
   for displayed prototype sites.

## Open questions

- Which new prototype, if any, will use this guide and which release route will
  it choose?
- When will the current app's `r2.dev` URLs and stale viewer-version instructions
  be updated?
