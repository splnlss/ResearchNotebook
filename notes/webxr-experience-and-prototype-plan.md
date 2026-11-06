---
type: Solution
title: WebXR experience, story, and prototype plan
description: A working plan for defining the story arc, scene flow, points of interest, movement rules, and features required for a WebXR prototype.
tags: [webxr, storytelling, prototype]
status: draft
---

<!-- markdownlint-disable MD013 MD025 -->

# WebXR experience, story, and prototype plan

**Source presentation:** [Dust and the Great Divide pitch deck](https://docs.google.com/presentation/d/1fXeHrJdvzzpk1lwqnjl6TmKo2fVJabZ0R77XcDEinuA/edit?usp=sharing).

**UX review:** [Open questions and independent review](webxr-experience-review.md).

**First slice:** [Prototype recommendation and playtest](webxr-first-prototype-recommendation.md).

**Current direction:** Begin above Colorado in a flyable map, choose one of
three geographic sections, descend into a locally explorable story world,
then return to the map. The San Luis Valley is the first sequence described
in detail. The deck supplies the documentary premise; the sequence below
records the project owner's working ideas, with alternatives left open.

## Review of the source presentation

- **Story:** The central question is who gets water when there is not enough.
  The deck follows water from snowpack through rivers, acequias, fields, and
  aquifers, connecting water scarcity to rights, power, and community life.
- **People and places:** Eric Whyte at Ute Mountain, Ronda Lobato in the San
  Luis Valley, and Bob Price above the Ogallala Aquifer anchor the three
  strands. Location images include Ronda's ranch and interior, Cleave
  Simpson's farm and barn, and Ute Mountain Ute Ranch.
- **Experience:** The deck proposes free exploration in ground-level spaces,
  intimate memory sequences co-created with participants, and bird's-eye
  visualizations of snowpack, aquifer depletion, infrastructure, and rights.
  Spatial voice and gaze-responsive overlays are named interaction ideas.
- **Access and integrity:** Browser delivery across desktop, laptop, and
  headsets is central to the pitch. It also calls for journalistic integrity,
  participant collaboration, and Spanish and Ute language accessibility.
- **Missing design decisions:** The deck does not specify the scene order,
  exact POIs, transition triggers, allowed actions in memory sequences,
  entry and exit flow, or the first prototype chapter. The named locations
  do not yet identify an Ogallala scene capture. These need decisions before
  the feature list can become a build estimate.

The slide describing Cleave Simpson calls him a congressman, while the
location caption calls him a state senator. Confirm his role before using
either title in the experience.

## Three geographic sections

The experience is organized by area. The aerial map is the hub between
sections; the order after the first visit is still open.

| Section | Water connection | Ground-level story | Current detail |
| --- | --- | --- | --- |
| Arkansas River watershed | Arkansas River system | Participant, locations, and POIs to assign | Outline only |
| San Luis Valley | Rio Grande watershed and local water system | Ronda Lobato's ranch, home, pipe, and fields | First walkthrough below |
| Ute Mountain Ute Reservation | Dolores Project water in the Colorado River basin | Eric Whyte and the tribal farm/ranch | Outline only |

The [Bureau of Reclamation's Dolores Project description](https://www.usbr.gov/projects/index.php?id=453)
connects the reservation's project water to the Dolores River and the upper
Colorado basin. Map the exact water route before labeling it in the aerial
visualization. The deck also names Bob Price and the Ogallala Aquifer; decide
how that account fits the Arkansas section rather than assigning it by
assumption.

## Working entry and San Luis Valley sequence

This is a proposed path, not a locked script. Every change of movement mode
needs an understandable cue and a way to leave or recover.

| Step | View and user action | Narrative and POI | Transition or exit |
| --- | --- | --- | --- |
| 0. Enter | Launch in browser, then enter WebXR or desktop view above Colorado | Brief flight controls and a visible way back to the browser | Begin in aerial flying mode |
| 1. Orient | Fly around a mostly black-and-white Colorado map; selected areas appear in color | Opening voice introduces the project; a dated snowpack layer or another water visualization provides context | Colored area markers invite selection |
| 2. Choose San Luis | Aim at or click the San Luis Valley marker, then Ronda's ranch POI | Show the destination name and the Rio Grande connection | A guided flight and descent lands at a safe ranch spawn |
| 3. Ranch exterior | Explore freely within a clear boundary | Ronda's voice introduces herself and the ranch; local details can be inspected | House entrance moves indoors; return control remains available |
| 4. Ranch interior | Explore or inspect a smaller space | Ronda introduces family and place history; define exact objects and recordings | A clear doorway or action returns outdoors |
| 5. Pipe encounter | Arrive at a set viewing position | Ronda and Joseph discuss possible issues at the pipe; a video overlay is one candidate treatment | Video can be paused or replayed; a visible action releases the user back to exploration |
| 6. Fields | Explore freely and find story cues | Show the conditions and stakes through place, voice, and selected POIs | A discoverable cue enters the personal sequence |
| 7. Memory or dream | Enter a guided, expressive world inspired by one of Ronda's childhood ideas | A time-based transformation or World Labs Marble-generated Gaussian splat is a candidate; the memory's content requires co-creation and approval | Provide an explicit exit, with a narrative transition back above Colorado |
| 8. Return | Reappear over the map with spatial orientation intact | Link the local account to the larger water system; other colored sections remain available | Choose another area, replay, or leave the experience |

**Opening narrator:** Two possibilities are the filmmaker explaining in the
first person why they made the project, or a person from one of the places
introducing the story. Choose a voice and point of view before writing the
opening. The snowpack year, dataset, and interpretation also need selection.

**Return transition options:** Follow water out of the local scene; move
through a portal or opening; or use a storm and tumbleweed to carry the user
back to the aerial map. Test one for clarity, comfort, and fit with the
documentary tone. The user also needs a direct exit control at every step.

**World setup hypothesis:** Keep the statewide map and any 3D tiles in the
aerial layer. On landing, load an isolated Gaussian splat scene for walking
and local interaction; unload it on return. Confirm whether this matches the
intended use of splats overhead, and test loading time, scale, collision,
visual continuity, and device performance before adopting it.

## Decisions to make first

| Decision | Working question | Current answer |
| --- | --- | --- |
| Experience promise | What should the user feel, learn, or change by the end? | Understand the human stakes of water allocation; exact takeaway open |
| Audience and session | Who is this for, and how long is one visit? | Community and classroom access are named; duration open |
| Platform | Which headsets, controllers, hands, and browser modes must work? | Desktop, laptop, and headset access intended; exact support matrix open |
| Story premise | Who is the user in the world, and what is at stake? | Witness following water and meeting people affected by scarcity; user role to confirm |
| Agency | Can the user change the outcome, the order of events, or only the pace? | User flies to a section and explores locally; later section order and choices open |
| Prototype slice | Which complete sequence proves the experience works? | Aerial map to Ronda's ranch, indoor/outdoor POIs, one guided moment, and return to map |

## Story structure

Write a one-sentence premise and a one-sentence ending before expanding
individual scenes. Give each beat a user action as well as a plot event.

| Beat | Plot question | User experience to define |
| --- | --- | --- |
| Arrival | Why is the user here now? | First image, orientation, role, and invitation to act |
| Discovery | What can the user uncover? | First meaningful point of interest (POI) and feedback |
| Complication | What changes the user's understanding or goal? | New scene, obstacle, or changed interaction |
| Revelation | What is the central payoff? | Inspect or reveal a connection between a person and the water system |
| Resolution | What does the user take away? | Ending, reflection, replay, and exit |

**Premise:** Follow water through three Western communities to understand
the people and competing claims behind the question, "Who gets water when
there isn't enough?" This paraphrases the deck's proposed story.

**User role and goal:** The deck places the user as a visitor and witness,
exploring places and hearing firsthand accounts. Decide whether the
experience gives them any further role.

**Ending and possible variants:** To decide.

**Prototype slice:** Candidate: aerial entry, San Luis selection, Ronda's
ranch exterior and interior, pipe encounter, fields, one memory sequence,
and return to the aerial map. Decide which parts are essential for the first
playtest and which can use temporary media.

Map each plotline before assigning it to scenes. The three participant
strands need distinct information and payoffs even if only one is prototyped.

| Plotline | Setup | Development and linked POIs | Payoff | Required or optional? |
| --- | --- | --- | --- | --- |
| Water across the West | Snowpack and the question of allocation | Move across watersheds; connect local experience to system views | Return to the central question with human stakes | Required |
| Eric Whyte / Ute Mountain | The ranch and its water allocation | Explore the ranch, hear Eric, reveal the rights context | Decide what understanding closes this chapter | Chapter proposed in deck |
| Ronda Lobato / San Luis Valley | Ranch and acequia tradition | Explore local spaces, hear Ronda, show cooperative water governance | Decide what understanding closes this chapter | Chapter proposed in deck |
| Bob Price / Ogallala | Fields over a declining aquifer | Explore land, hear Bob, show aquifer and land-use change | Decide what understanding closes this chapter | Chapter proposed in deck |

If choices create branches, record the choice point, changed scenes or
dialogue, and where paths rejoin or end. Keep a complete route through the
prototype even when optional POIs are missed.

## Journey and scene map

The detailed San Luis path above is the first scene map. Use the same scene
card below for the Arkansas and Ute Mountain sections once their people,
locations, and transitions are chosen. The general mode pattern remains:
aerial orientation, ground exploration, a guided account, and map return.

| Step | Scene and narrative purpose | Entry and orientation | Movement and allowed actions | POIs and trigger | Exit and next state |
| --- | --- | --- | --- | --- | --- |
| 0 | Launch and onboarding: establish expectations | Browser page; explain controls, audio, comfort, and session goal | Start, adjust settings, or leave | Start control | Enter XR or desktop view |
| 1 | Aerial map: establish the geography and story | Start above Colorado with a visible flight cue | Fly, inspect data layer, and select a colored area | Region marker and opening voice | Fly to the chosen POI and descend |
| 2 | Ground level: establish one person's place | Safe spawn; visible first cue | Free exploration within a defined area; inspect and approach POIs | Place detail and participant voice | Explicit cue enters personal sequence |
| 3 | Guided account or memory: hear that person's perspective | Transition from a linked ground-level POI | Look, listen, pause, repeat, and advance | Voice, video, or expressive imagery | Clear completion or exit returns to the map |
| 4 | Aerial return: connect local and regional scale | Rise with an orientation cue | Fly, inspect layers, and select another area | Water and rights overlays with source context | Choose next section or ending |
| 5 | Final scene: connect the strands | Preserve what the user has seen | Inspect recap or final account; no outcome-changing action assumed | Closing question and routes to context | Show ending state and exit options |
| 6 | End and return | Summary or closing image | Replay, return to menu, or leave XR | Replay and exit controls | Return to browser page safely |

For each scene, fill out this compact scene card:

- **Scene ID, name, and beat:**
- **What the user knows on entry / should know on exit:**
- **Space and setup:** scale, lighting, sound, spawn point, boundaries, and landmarks.
- **Movement:** free roam, bounded roam, teleport between anchors, or fixed viewpoint; why.
- **Allowed actions:** inspect, select, manipulate, converse, wait, or move on.
- **POIs:** IDs, locations, cues, interaction, story information, and completion state.
- **Entry:** trigger, transition, orientation cue, and any state carried in.
- **Exit:** required event, user control, transition, and state carried out.
- **Recovery:** what happens if a cue is missed, the user leaves XR, or the scene is revisited.

## Movement and agency rules

The aerial map permits flight; ranch exterior and fields permit bounded free
roam; the pipe encounter uses a fixed viewpoint; the memory is guided. Each
mode needs an obvious action and exit even when the user cannot walk.

| Rule | Define before implementation |
| --- | --- |
| Aerial flight | Speed, altitude limits, rotation, comfort controls, map bounds, and how colored destinations are selected |
| Free roam | Play boundary, locomotion method, collision, recentering, and how the next POI is found |
| Bounded roam | Boundary cue, reachable POIs, and what happens at the edge |
| Guided or fixed view | What the user can inspect or choose, how they advance, and how they pause, repeat, or leave content |
| Every scene | Whether actions are optional or required, how progress is signaled, and how the user exits |

## POI inventory

A POI should have a reason to exist in the plot and an unmistakable response
to user input. Keep the inventory tied to scene IDs.

| POI ID / scene | Story purpose and information revealed | Cue and discoverability | User action | Feedback and resulting state | Required? |
| --- | --- | --- | --- | --- | --- |
| SLV map marker / aerial | Identify Ronda's ranch within the valley | Color against monochrome map; label or voice cue | Aim and select | Preview location; begin guided descent | Yes |
| House / ranch exterior | Meet Ronda and locate family history | Ronda's voice and visible entrance | Approach or select doorway | Load interior at a safe spawn | Likely |
| Pipe / ranch exterior | Hear Ronda and Joseph discuss an issue | Location cue and clear viewing point | Approach and activate | Lock viewpoint for video or other account, then release | To decide |
| Field cue / ranch fields | Connect visible conditions to Ronda's perspective | Land feature, voice, or object cue | Explore and select | Enter approved memory sequence | To decide |
| Return cue / memory | Close personal moment and regain regional context | Visible and accessible exit | Select or follow narrative cue | Transition to aerial map with orientation restored | Yes |

For optional POIs, decide whether they add context, change later dialogue, or
unlock a different route. Define a fallback for every required POI that the
user might overlook.

## Prototype feature inventory

**Core** means needed to demonstrate a complete journey. **Conditional**
becomes required if the corresponding story or platform choice is selected.
Track ownership and implementation detail after the prototype slice is set.

| Feature | Scope | Done when |
| --- | --- | --- |
| Launch page and XR entry | Core | User sees requirements and controls, starts the experience, and gets a clear unsupported-device message. |
| Desktop and laptop mode | Core | The same essential story path can be completed in a browser with mouse and keyboard. |
| First-use orientation | Core | User can identify how to move, select, pause, and exit before the first required action. |
| Aerial Colorado map and flight | Core | User can orient, fly within comfortable bounds, and identify the three colored sections. |
| Map data layer | Core | One dated, sourced water layer can be read over the map without obscuring destinations. |
| Map POI and guided descent | Core | A user can select Ronda's ranch, understand the destination, and arrive at a safe local spawn. |
| Scene loading and transitions | Core | The chosen scene sequence loads, provides arrival cues, and advances only on defined triggers. |
| Overview-to-local world switch | Core | Aerial map assets and the isolated ranch world exchange without a broken frame, lost progress, or incorrect scale. |
| Scene layout and collision | Core | Each scene has a safe spawn, readable landmarks, reachable required POIs, and no accidental path out. |
| Movement and comfort | Core | Each scene's chosen locomotion works; recentering and a comfort option are available where movement calls for them. |
| Gaze, controller, or hand input | Core | The chosen primary input can activate every required POI; targets provide hover and activation feedback. |
| POI interaction system | Core | Required POIs can be found, activated, marked complete, and prevented from replaying incorrectly. |
| Story state and sequencing | Core | Progress flags determine available actions, scene transitions, and the ending consistently. |
| Three viewpoint modes | Core | A chosen chapter connects ground-level exploration, a memory sequence, and a bird's-eye water view without losing orientation. |
| Place capture and rendering | Core | The chosen location loads at usable quality on target devices and preserves enough detail for its story POIs. |
| Ranch interior and exterior navigation | Core | User can enter the home, return outdoors, and reach the pipe and fields with clear boundaries. |
| Fixed-view account | Core | The pipe moment presents Ronda and Joseph in an understandable frame and returns control afterward. |
| Participant account and memory sequence | Core | A participant's approved account is presented with the intended expressive treatment, captions, and clear pause or replay controls. |
| Generated memory feasibility | Conditional | If Marble is chosen, a small approved test scene loads in the target browser, meets comfort and performance needs, and has a clear exit. |
| Local-to-map return | Core | Completing or leaving a local chapter returns the user to an oriented aerial state with other sections available. |
| Water-system visualization | Core | The bird's-eye view connects at least one verified data layer to the place and explains its source, date, and meaning. |
| Narrative delivery | Core | Essential dialogue or exposition is understandable in the selected mix of audio, captions, and visual cues. |
| Pause, help, and exit | Core | User can pause, review controls, leave XR, and return to a stable browser state from any scene. |
| Interruption recovery | Core | Leaving XR, losing focus, or reentering does not strand the user or silently skip a required beat. |
| Completion and replay | Core | The ending is recognizable and offers a deliberate replay or exit choice. |
| Branches and consequences | Conditional | If choices alter the story, each selected branch has defined triggers, state changes, and a reachable ending. |
| Persistence across visits | Conditional | If return visits matter, progress restores to a defined scene and can be reset. |
| Spatial audio and voice | Core | Participant voice is locatable and intelligible; levels are controllable and captions cover essential content. |
| Spanish and Ute language support | Conditional | If included in the first slice, translated interface and story assets are reviewed with the relevant speakers and work across chosen modes. |
| Content and participant review | Core | The participant account, depiction, and documentary claims used in the slice have a recorded review path before public use. |
| Accessibility options | Core | Text is legible, required information is available beyond a single sensory cue, and motion or audio can be adjusted as needed for the chosen design. |
| Performance and device check | Core | The target devices sustain an agreed frame-rate and loading budget through the complete slice. |
| Basic playtest capture | Core | Observers can record where users hesitate, miss POIs, feel discomfort, or fail to reach the ending. |

## Plan completion check

This page is ready to become a build plan when it contains a named target
device, a premise and ending, a complete scene map, a movement rule and
entry/exit trigger for each scene, a POI inventory, and a prototype slice.
Then turn the feature inventory into tasks with owners, dependencies, and
acceptance checks tied to that slice.

## Open questions

- Which story and locations fill the Arkansas River section, and how does
  Bob Price's Ogallala account connect to it?
- What exact water route and map label should represent the Ute Mountain
  Ute section?
- Which devices and input methods define the first prototype?
- How long should a chapter and the full visit be?
- Who narrates the opening, and is that narration personal or place-led?
- Which snowpack year or other dataset opens the experience?
- Which San Luis POIs are required before the memory, and what exact action
  releases the user from the pipe video and dream?
- Which return transition best fits the story and user comfort?
- Does the aerial layer contain splats as well as map tiles, or only map
  data, with splats reserved for isolated ground-level worlds?
- Which data layers, languages, and participant approvals are needed in the
  first prototype slice?
