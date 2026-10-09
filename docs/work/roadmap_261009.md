# FaceID roadmap

**Created** 2026-10-09 · **Last updated** 2026-10-09

## What this is

The one live list of unfinished work for the two-camera office pilot. Requirements and technical context live in the [project brief](../shared/project-brief.md). This roadmap follows the short-item format used by RoleBench and AI Assistant 1C.

**Estimate:** ten sequential working sessions, plus a 48-hour continuous test. A session is one focused work package, not a promised number of hours or calendar days. Allow two to four additional sessions if supported GPU access, camera positioning, or the activity-history integration needs more work. Hardware delivery and approved maintenance windows are separate waiting time. Re-estimate after Session 1; an unsupported GPU/host combination is a blocker, not merely another day's work.

**Milestones:** recording after Session 4; measured day/night recognition after Session 6; saved activity history and questions after Session 9; accepted pilot after Session 10. These are planned outcomes, not completed capabilities.

## Rules for maintaining this roadmap

1. Keep one live roadmap in this folder. An item has a title, three to five lines, and one Action line.
2. Keep reasoning in the closing PR and test results in dated verification records. Do not commit private footage or face data.
3. Close an item with its completion date and PR number, replacing its description with that record. Update the roadmap in the same commit as the work.
4. Re-check reality before starting each session. A blocked or partially tested item stays open.
5. Archive monthly; next review is 2026-11-09. Move this roadmap to the archive and carry only open items into its dated successor.
6. Work sequentially. Before implementation, prepare the session's plan and agree on any shared-server maintenance needed.

## Phase 1 — Prove the server foundation

### 1 · Verify HR-SRV and the GPU route — 1 session
Identify the actual host, virtualization platform, RTX 5060 Ti memory, and available CPU, RAM, network, and storage.
Verify an officially supported route to give a Linux VM GPU access; check its effect on existing services and host GPU use.
Confirm exact camera requirements, the current Frigate feature coverage, retention budget, and model license checks.
**Action:** record a go/no-go decision, resource allocation, and revised estimate before creating the VM.

### 2 · Create the separate Linux virtual machine — 1 session
Create the isolated Linux VM on HR-SRV using the supported route established in Session 1.
Install the supported NVIDIA driver, Docker, and NVIDIA container tooling; allocate persistent data storage.
Prove GPU access inside both Linux and a container, and verify it survives a VM restart without disrupting other services.
**Action:** save the installation and recovery record; proceed only after real GPU access passes.

### 3 · Install Frigate and its baseline models — 1 session
Install a pinned supported Frigate NVIDIA image, enable authentication, and configure persistent recordings and model caches.
Install a supported person detector and enable the large face-recognition model downloads; record exact versions and licenses.
Verify GPU decoding, detection, and face-model acceleration separately; confirm restart and offline model loading.
**Action:** demonstrate a working Frigate installation with test footage and measured resource use.

## Phase 2 — Connect cameras and qualify recognition

### 4 · Connect both cameras and record continuously — 1 session
Install the two selected Hikvision 8 MP cameras in the room corners and connect them through the wired network.
Configure recording and detection streams, enough face detail, time synchronization, and the agreed microphone setting.
Verify live views, continuous recording, playback, coverage, disk usage, and retention cleanup on server storage.
**Action:** demonstrate both cameras recording together and record the final placement and stream settings.

### 5 · Enroll people and test daytime recognition — 1 session
Enroll the agreed participants with clear face samples and test known and unknown people across both views.
Measure recognition, wrong names, missed people, stationary tracking, and overlapping-camera observations against the brief.
Keep uncertain identities unknown; distinguish tracking within one view from verified identity across cameras.
**Action:** record the daytime results and resolve placement or detection-stream problems before nighttime qualification.

### 6 · Qualify nighttime operation — 1 session
Repeat the recognition tests in infrared-only mode and under the accepted nighttime lighting.
Check motion blur, reflections, backlighting, distant faces, occlusion, and simultaneous people.
Record which lighting conditions pass; do not claim infrared-only recognition from an illuminated test.
**Action:** establish the tested day/night operating conditions, with failed cases left visible.

## Phase 3 — Save activities and answer from history

### 7 · Install and qualify local activity descriptions — 1 session
Download a compatible local vision model and connect it through Frigate's official local-provider integration.
Start with the small-model candidate in the brief; measure memory and description delays alongside both live cameras.
Test active and stationary people; confirm whether native descriptions update during long events and whether earlier descriptions remain available.
**Action:** demonstrate useful ongoing descriptions and record the exact gap, if any, requiring an additional service.

### 8 · Preserve the live activity history — 1 session
Use native persistent history if it meets the requirement; otherwise implement the smallest agreed service through official interfaces.
Save timestamped observations, identity uncertainty, source references, and gaps without losing earlier descriptions.
Test restart recovery, duplicate events, identity corrections, and overlapping-camera observations against the brief's timing targets.
**Action:** demonstrate a continuous, recoverable history for a person present throughout a long event.

### 9 · Add periodic summaries and questions — 1 session
Create periodic and daily summaries from saved observations, using the pilot schedule in the brief.
Answer person-and-time questions from saved records, retaining uncertainty and source references.
Verify “What did Amir do all day?” with video/image retrieval disabled for the answering step; include empty and interrupted days.
**Action:** complete a founder walkthrough of the saved timeline, summaries, and logs-only answers.

## Phase 4 — Accept the pilot and measure expansion

### 10 · Continuous operation, recovery, and handover — 1 session plus 48 hours elapsed
Run both cameras continuously for 48 hours and record resource use, delays, footage availability, and history completeness.
Test camera outages, VM/service restarts, model failures, storage cleanup, and recovery without silent gaps.
Measure four- and six-stream load separately; distinguish replay estimates from real-camera validation.
**Action:** record passed and failed acceptance checks, operating instructions, backup/recovery steps, and the next expansion decision.

## Current next action

Start Session 1 with a read-only inventory of HR-SRV. No installation or hardware validation has yet been performed for FaceID.
