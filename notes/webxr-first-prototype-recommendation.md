---
type: Recommendation
title: First WebXR prototype decisions and playtest
description: Start with a short San Luis map-to-ranch-to-map journey that tests story clarity and control before producing the full documentary sequence.
tags: [webxr, prototype, user-experience]
status: draft
---

<!-- markdownlint-disable MD013 MD025 -->

# First WebXR prototype decisions and playtest

**Recommendation:** Test one complete San Luis Valley journey with temporary
visuals and audio: **Colorado map → Ronda's ranch → one account → map**.
This tests whether visitors understand the place, speaker, and exit controls
before production depends on interior captures, video, live data, or a
generated memory. These are proposed working choices for the first test; the
project owner retains final story and platform decisions.

## Working choices for the first test

| Decision | Recommendation | Reason and remaining choice |
| --- | --- | --- |
| Opening | Keep the owner's map-first idea for the primary test; make a simple person-first variant for comparison. Use clearly labeled scratch narration. | The [UX review](webxr-experience-review.md) found that a map tutorial may delay the human story. The final narrator remains open. |
| Section choice | Show all three areas, but make only San Luis selectable in this slice. Label the other two as future sections; use one direct ranch marker rather than a region-then-ranch double selection. | Visitors should not mistake unfinished sections for broken controls. The full section order remains open. |
| Required beat | Make one Ronda account required and replayable. Use a temporary audio or still-image treatment for the pipe encounter; keep the house, fields, and dream as later tests. | A visitor can complete the path without hunting for an optional cue. The eventual account and media treatment require participant review. |
| Movement | Allow bounded aerial movement and bounded ranch exploration. Let the visitor trigger a short fade or comfortable descent; do not force a long camera flight in the first test. | This isolates orientation and control questions from motion discomfort. The final flight treatment remains open. |
| Exit rules | In every mode, provide **Help**, **Return to map**, and **Leave experience**. The account also has **Pause**, **Replay**, and **Continue**. Return to map preserves the visited marker; leave exits WebXR to the browser page. | Each control has one predictable destination. A cinematic return can be tested later as an additional route. |
| Devices and language | Test the same required path on a desktop browser and one named headset. Include captions and an interface layout that can accept translated text; list Spanish and Ute content as planned work until reviewed assets exist. | The exact headset, input method, and first-release language commitment still need the owner's decision. |
| Map data | Use one clearly labeled static, dated visualization if a verified dataset is available. Otherwise label a mock layer as illustrative and keep it out of documentary claims. | The opening snowpack year, provenance, and interpretation are not yet settled. |

The chapter-level bird's-eye data view from the
[pitch deck](https://docs.google.com/presentation/d/1fXeHrJdvzzpk1lwqnjl6TmKo2fVJabZ0R77XcDEinuA/edit?usp=sharing)
is a separate design step from the statewide map menu. Add it to the next
slice or explicitly decide that the return map will carry that explanation.
The full [experience plan](webxr-experience-and-prototype-plan.md) remains
the source for later scenes and features.

## Paper storyboard for the test

Use these cards to walk a visitor through the path before a 3D build exists.
All spoken lines are placeholders, not Ronda's words or production copy.

| Card | Visitor sees and hears | Available action and result |
| --- | --- | --- |
| Entry | Start page names the project and offers desktop or WebXR entry, controls, captions, and exit. | Start opens the Colorado map. |
| Map | Muted Colorado terrain, three labeled areas, one active San Luis/ranch marker, and a plainly marked illustrative water layer. A temporary narrator introduces the central question. | Select the ranch marker for a destination card; confirm to descend. Help and Leave remain available. |
| Ranch | A bounded ranch view with a visible pipe marker. A temporary guide identifies the place without speaking as Ronda. | Explore, select the pipe account, or Return to map. |
| Account | A fixed frame clearly identified as a temporary stand-in for an approved account. | Pause, Replay, Continue, or Return to map. Continue ends at the ranch; Return to map leaves the chapter. |
| Return | The aerial map shows San Luis as visited and the other sections as future. | Replay San Luis or Leave to the browser page. |

## First playtest script

Use a clickable storyboard or rough 3D scene with temporary geometry and
audio. Do not present placeholder data or invented memories as documentary
material. Recruit a few people who have not read the plan; observe rather
than teach the controls.

1. Start at the browser page. Ask the visitor to enter and say where they
   think they are, who is speaking, and what the project is about.
2. Ask them to find Ronda's ranch from the map. Watch whether the destination
   and selection method are clear without relying only on color.
3. After landing, ask them to find and play the required account. Note
   hesitation, missed cues, overlapping narration, and motion discomfort.
4. Ask them to return to the map and then leave the experience. Record where
   they expect each action to lead and whether the result matches.
5. Show the alternate person-first opening. Ask which version makes the
   story and narrator clearer; record the reason rather than a vote alone.

Record task completion without moderator rescue, wrong turns, time spent
looking for controls, comments about motion or loading, and whether visitors
can distinguish real material from placeholders. Revise the journey before
committing to detailed splat capture or a Marble-generated world.

## Closeout state

- **Prepared:** Working plan, independent UX review, proposed first slice,
  and playtest script.
- **Not yet tested:** No interactive prototype or first-time visitor session
  exists in this notebook.
- **Owner decisions still needed:** Final narrator; exact headset and input;
  which Ronda account may be used; whether the chapter data view belongs on
  return; and the desired language scope for a public prototype.

## Evidence and open questions

This recommendation synthesizes the [experience plan](webxr-experience-and-prototype-plan.md),
its [review](webxr-experience-review.md), and the linked pitch deck. The
prototype sequence and playtest method are original design reasoning.

- Does the map-first opening make the human story clearer after a brief
  introduction, or should the visitor meet Ronda before seeing the map?
- Can the first slice use approved Ronda material, or should the initial
  test use a clearly marked temporary voice?
- What chapter length and device list define the next production slice?
