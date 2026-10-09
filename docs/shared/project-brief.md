# FaceID office pilot — project brief

**Recorded:** 2026-10-09 · **Status:** agreed direction; equipment compatibility and performance unverified.

## What we are building

The priorities, in order, are:

1. Recognize enrolled people's faces.
2. Recognize them during both daytime and nighttime operation.
3. Detect and follow people within the cameras' views.
4. Describe visible activities and save a useful history throughout the day.

For a question such as “What did Amir do all day?”, the system must read saved observations and summaries. It must not analyze the day's recordings again to construct the answer. Video remains available separately for human review.

Descriptions cover observable actions such as walking, sitting, standing, and leaving the visible area. They must distinguish an observation from an uncertain interpretation. A person leaving one camera's view does not by itself prove that they left the room. Identity must remain unknown when evidence is insufficient; matching the same person between cameras needs its own test.

## Equipment and room

| Item | Current decision or verification needed |
| --- | --- |
| Room | 25 m², ceiling 3 m; the earlier 54 m² figure is superseded. |
| Cameras | Buy two Hikvision 8 MP IP cameras: network cameras with infrared night vision, microphones, and motion detection. Exact models and lenses remain unselected. |
| Placement | Two room corners with overlapping coverage; no dedicated entrance camera. Choose actual mounting height and angle after checking face visibility. |
| Network | Wired Ethernet. Confirm PoE support, a compatible power-supplying switch or injectors, and cabling before purchase. Cameras do not normally plug directly into the server. |
| Existing compute | HR-SRV and an NVIDIA RTX 5060 Ti. Confirm whether the card has 8 GB or 16 GB of memory, its availability, and the server's remaining CPU, RAM, and disk capacity. |
| Deployment | Separate Linux virtual machine on HR-SRV, subject to supported GPU assignment. No server changes are part of this documentation PR. |
| Storage | Continuous recordings on server-backed storage assigned to the Linux machine; storage capacity and retention must be settled before recording begins. |
| Nighttime | Infrared is required camera hardware. Modest visible lighting is acceptable for the pilot; recognition must be tested under the actual nighttime conditions. |
| Expansion | Later qualification at four and six cameras. Two-camera success does not establish six-camera capacity. |

The expected purchase is the cameras. Confirm existing network power, cables, and disk capacity first; do not assume those accessories are already available or purchase them automatically.

## How the parts fit together

**Cameras → wired network → Linux virtual machine → Frigate → saved observations and summaries → questions about the day.**

Frigate records the streams and provides person detection, tracking, and face recognition. Its official local AI integrations are the first choice for activity descriptions. A small additional service may be needed to preserve an ongoing history and answer from that history; establish the exact gap before building it.

An 8 MP recording stream does not automatically give face recognition an 8 MP image. Frigate recognizes faces from the stream used for detection, so that stream must preserve enough detail at the far side of the room. Select cameras with configurable streams and verify their supported resolutions before purchase. Prefer standard H.264 video and compatible audio settings according to the official camera guide.

### GPU and virtual machine gate

First identify HR-SRV's actual host operating system and virtualization platform. The Hyper-V setup documented by AI Assistant 1C is background information, not proof that HR-SRV has that configuration.

If HR-SRV uses Hyper-V, do not assume that GPU partitioning can share the RTX 5060 Ti: it is absent from Microsoft's published supported GPU list. Whole-device assignment is a separate route requiring host, device, and driver support; it also makes the assigned GPU unavailable to the host while assigned. Verify support with the official host and GPU guidance before choosing the installation route. Do not use unofficial GPU-sharing scripts.

Frigate recommends direct installation on suitable Linux hardware rather than a virtual machine for easiest hardware access. The requested VM remains our target, with demonstrated GPU access as a prerequisite. If that prerequisite fails, record the blocker and agree on a supported alternative before changing the host or buying hardware.

### Model installation

| Purpose | Planned approach |
| --- | --- |
| Person detection | Select a free model supported by the installed Frigate NVIDIA detector; record the exact weights, download source, license, and measured memory use. No Frigate+ subscription assumed. |
| Face recognition | Start with Frigate's large ArcFace option once GPU acceleration works. Frigate downloads its required face models when enabled; retain the model cache. |
| Activity descriptions | Qualify an official local provider such as Ollama with a small compatible vision model; Qwen3-VL 4B is a candidate, not a promised performance result. Download this separately. |
| Summaries and questions | Reuse a compatible local model where practical; verify that it can answer from stored text without requesting video or images. |

Confirm the licenses of the actual model weights separately from the software license. Record pinned software versions, model identifiers, file sizes, runtime GPU memory use, and successful offline operation after downloads. “Runs locally” does not mean every model is bundled with Frigate or automatically licensed for every future use.

## Pilot targets and checks

These are initial test targets, not measured results or promises of perfect recognition:

- **Recognition:** test each enrolled participant in both camera views, near and far, moving and stationary, by day and under the chosen nighttime lighting. Start with at least 20 usable face encounters per person per lighting condition, balanced across cameras. Target at least 90% correct recognition of usable encounters, with no wrong-person labels in this test. Report obscured or unusable faces separately rather than hiding them in the success rate.
- **Unknown people:** use at least 20 encounters by non-enrolled test participants; none should receive an enrolled person's name. This small pilot is not a statistical accuracy guarantee.
- **Nighttime:** test infrared-only footage separately from the accepted illuminated setup. Do not describe infrared-only recognition as passed if only the illuminated setup works.
- **Live history:** initially sample active people about every 30 seconds and target saved observations within 60 seconds. Preserve stationary-person coverage, timestamps, camera, known or unknown identity, uncertainty, and source event references. Save each observation rather than overwriting the whole history with the latest description.
- **Summaries:** begin with 15-minute summaries and one daily summary, using Asia/Tashkent for displayed day boundaries. Show observation gaps and avoid counting overlapping camera views as two people.
- **Questions:** demonstrate a whole-day answer using stored text with image/video retrieval disabled for the answering step. Include times and references back to observations. Insufficient evidence should produce an incomplete answer, not an invented activity.
- **Reliability:** run both cameras continuously for 48 hours. Check restarts, camera disconnection/reconnection, local model failure, recording playback, storage cleanup, and recovery of saved history. Measure processing delays, dropped frames, and GPU memory use.
- **Capacity:** benchmark four and six streams separately using clearly labelled recorded test streams if more cameras are unavailable. Report this as a load estimate, not a real-world multi-camera recognition test.

Before live recording, set a storage budget, recording/history retention periods, access accounts, and who is enrolled. Keep credentials, face libraries, model files, recordings, and private test evidence outside Git. Microphone hardware is included; audio retention is a separate configuration decision. Conversation transcription, speaker recognition, and automatic door/light control are outside this pilot.

## Official references

Reviewed 2026-10-09; re-check against the exact software version used during installation.

- [Frigate installation and supported images](https://docs.frigate.video/frigate/installation/)
- [Camera stream setup](https://docs.frigate.video/frigate/camera_setup/)
- [Object detectors](https://docs.frigate.video/configuration/object_detectors/)
- [Face recognition and model downloads](https://docs.frigate.video/configuration/face_recognition/)
- [Local generative AI configuration](https://docs.frigate.video/configuration/genai/genai_config/)
- [Object description behavior](https://docs.frigate.video/configuration/genai/genai_objects/)
- [Review summaries](https://docs.frigate.video/configuration/genai/genai_review/)
- [Microsoft supported GPU partitioning hardware](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/gpu-partitioning)
- [Microsoft GPU assignment planning](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server)
