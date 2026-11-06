---
type: Review
title: WebXR experience plan review
description: The main risks are unclear agency and scene exits, abrupt movement changes, and a prototype scope larger than one testable story journey.
tags: [webxr, storytelling, user-experience]
status: draft
---

<!-- markdownlint-disable MD013 MD025 -->

# WebXR experience plan review

The [working experience plan](webxr-experience-and-prototype-plan.md) has a
compelling map-to-place journey, but a first-time visitor could lose the
story thread or control during several mode changes. The review below is
original UX reasoning about the plan, not an observation from user testing.
Resolve the highest-priority questions before locking the prototype slice.

## Highest-priority gaps

| Area | Potential hole or inconsistency | Decision to make |
| --- | --- | --- |
| User agency | The map suggests users may choose any of three sections, while the story and prototype sequence begin with San Luis. A locked order could make the other colored areas feel broken. | Decide whether all three areas are initially selectable, preview-only, or unlocked in a stated order. |
| Story voice | A filmmaker's first-person introduction and a resident's first-person introduction give the visitor different roles and expectations. The handoff to Ronda's voice could feel abrupt. | Choose the opening narrator, then write the exact handoff from statewide story to Ronda's account. |
| Map selection | "Fly, aim, click" is ambiguous in a headset, and color alone may not identify selectable regions. | Define controller, gaze, and desktop selection; add labels, focus feedback, and a non-color cue. |
| Aerial movement | Free flight, guided descent, ground walking, fixed video, and dream travel all change control rules. A sudden forced camera move may cause discomfort or loss of orientation. | Set speed and comfort rules for each mode, announce changes, and let the user initiate or skip major motion. |
| Required story beats | Free roam lets a visitor enter the house, pipe scene, or fields in an unexpected order, or miss a required POI entirely. | Decide which beats are required, what can play in any order, and how cues recover a missed beat without trapping the user. |
| Exit and recovery | The dream's exit is cinematic, but the plan also promises a direct exit everywhere. It is unclear whether exit means return to the field, aerial map, or browser. | Define pause, back, skip, return-to-map, and leave-WebXR actions for every mode; state where each lands. |
| Prototype scope | Map flight, live data, several splat scenes, interior navigation, fixed video, generative memory, and desktop/headset parity are all in the candidate first journey. | Choose the smallest end-to-end slice that tests the core map-to-place-to-map transition and one meaningful account. |

## Scene and story questions

| Area | Potential hole or inconsistency | Decision to make |
| --- | --- | --- |
| Aerial context | A current-year snowpack layer may sit beside memories or footage from different years. The map might imply a direct causal link that the story has not established. | Name the time period of each dataset and scene; provide a legend and one plain-language explanation of what the layer can and cannot show. |
| Scale and geography | The map is described as Colorado-wide, while watershed and water-rights stories cross state and basin boundaries. The Arkansas section's relationship to Bob Price's Ogallala account remains unclear. | Agree on a map extent and exact locations, labels, and water routes for each section. |
| Local voice | Ronda's voice may begin during free roam while the visitor is looking elsewhere, moving indoors, or trying controls. | Choose whether narration follows location, waits for an action, or pauses when the visitor leaves; provide replay and captions. |
| Interior crossing | The exterior and house interior may be separate captures. A door can feel like a physical opening but behave like a scene load. | Design a visible entry/exit affordance and loading transition; preserve the user's heading and scale across it. |
| Pipe scene | A fixed viewpoint and video overlay interrupt free roam. The visitor may not know why movement stopped, whether the people are present in space, or when control returns. | Pick a media treatment, entry cue, framing, controls, and a clear release back to the ranch. |
| Memory status | A generated childhood dream could be mistaken for a documented event or for Ronda's actual memory. Its trigger and relationship to the pipe/field account are not yet defined. | Co-create and approve its content; signal that it is an expressive interpretation; define its narrative purpose, trigger, and skip. |
| Return to map | A hole, storm, tumbleweed, or water path implies different emotional meanings. A surprise storm could obscure navigation or feel like a disaster event. | Prototype one transition with a clear destination and an ordinary return control alongside it. |
| Whole-story ending | The plan says users can choose another section after returning, but does not say when the experience is complete or what connects the three accounts. | Define chapter completion, replay, an ending after one or all sections, and the final takeaway. |

## Build and content questions

- Which ground scenes already have usable captures, audio, and permission to
  appear in a test? The [pitch deck](https://docs.google.com/presentation/d/1fXeHrJdvzzpk1lwqnjl6TmKo2fVJabZ0R77XcDEinuA/edit?usp=sharing)
  shows several locations but does not define a production-ready asset list.
- Can the aerial map, descent, and isolated ground splat preserve a believable
  position and scale without a long load or a visible jump? This is an
  implementation hypothesis to test, not an established capability.
- Does the first prototype need live or dated water data, or would one
  clearly labeled static visualization test the story more directly?
- What accessibility baseline applies across flight, walking, video, and
  dream scenes: seated use, motion comfort, captions, language, and an input
  path for each required action?

## Additions from an independent Claude review

Claude reviewed the plan and deck independently, then compared this note.
These objections add specificity to the decisions above. A map-first opening
is the owner's current direction; its difference from the deck's more linear,
person-led rationale is a design choice to test, not an error to erase.

| Area | Further question or test |
| --- | --- |
| Narrative geography | The [USGS map of Colorado's major basins](https://pubs.usgs.gov/of/1982/0426/report.pdf) shows the Arkansas, Rio Grande, and Colorado systems separately. Is the story following one body of water, or comparing three places facing a shared question? Do not imply that water flows from one section to the next. |
| First minute | The current map opening asks visitors to learn flight, read a data layer, and choose a destination before meeting anyone. Compare a quick map-first mockup with a person-first opening, then ask viewers who is speaking and what the story is about. |
| Aerial view's job | The map is simultaneously an introduction, flight space, region menu, and science visualization. Decide which function has priority at each moment. The deck also proposes a bird's-eye data view between chapters; the San Luis walkthrough does not yet specify a chapter-level visualization. |
| Selection and progress | San Luis region selection and ranch POI selection imply an intermediate state that is not designed. Define that state and show available, visited, and current sections without relying on color alone. |
| Plan consistency | The house, pipe, and field POIs are marked "Likely" or "To decide," while matching scenes and features are "Core." Mark required beats first, then make the feature inventory reflect them. |
| Visitor's social role | Ronda speaks directly to the visitor in one scene, while the pipe encounter has Ronda and Joseph speaking to each other. Decide whether the visitor is a welcomed guest or an observer, and introduce Joseph and the change in address. |
| Documentary register | A real capture, a data model, a recorded account, and a generated memory should each be recognizable as such. Define visual and verbal cues, dates, sources, and participant review before mixing them in one seamless scene. |
| Asset timing | Check that ranch capture season supports the narrated conditions. Confirm the opening snowpack dataset exists, covers the chosen year and geography, and has permission for use before writing it into the script. |
| Web interruption | A refresh or slow download can return a visitor to the beginning. Test on a slower connection and decide whether the first prototype needs chapter resume, a meaningful loading moment, or both. |
| Access scope | The deck makes browser reach and Spanish/Ute language access part of its purpose, while the plan leaves exact devices open and languages conditional. Name the devices and languages promised by the prototype separately from the full project. |
| Ending and impact | The pitch deck aims to channel visitor interest toward education or action. Define whether the ending offers further context, community resources, a discussion prompt, or another concrete next step. |
| Participant placement | The [Colorado General Assembly](https://www.leg.colorado.gov/legislators/cleave-simpson) identifies Cleave Simpson as a state senator, and the [Rio Grande Water Conservation District](https://www.rgwcd.org/staff) lists him as its general manager. His role and San Luis location need to be reconciled with the deck's placement of him in Bob Price's Ogallala account. |

For an inexpensive first test, Claude suggested a rough prototype with
temporary geometry and audio that asks whether visitors know where they are,
who is speaking, and how to leave after each mode change. This can guide
which captures and effects deserve production work. Language support and
resume may be phased, but their intended audience and chapter length must be
stated before marking them optional.

## Open questions

- Which one user action proves that exploring the ranch adds meaning beyond
  watching a linear film?
- Where can a visitor go if they have only two minutes, miss a POI, or leave
  WebXR mid-chapter?
- Which mode changes require a user-initiated confirmation, and which can
  happen automatically without confusion?
