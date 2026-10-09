# FaceID

A local office pilot that recognizes enrolled people, follows their presence, and records descriptions of visible activities throughout the day.

Start with two Hikvision 8 MP network cameras in a 25 m² room with a 3 m ceiling. Frigate will run in a separate Linux virtual machine on HR-SRV, using the existing NVIDIA RTX 5060 Ti if supported GPU access is verified.

- [Live roadmap and session estimates](docs/work/roadmap_261009.md)
- [Agreed requirements, equipment, and acceptance targets](docs/shared/project-brief.md)

**Current state:** documentation only. No virtual machine, camera connection, Frigate installation, model download, or hardware test has been completed for this project.

**Next:** verify HR-SRV's virtualization platform and GPU assignment support before creating the Linux machine. Plan for ten working sessions plus a 48-hour continuous test; allow two to four additional sessions if integration or hardware qualification needs more work.

Recordings live on server storage allocated to the virtual machine. Cameras connect through the wired network, normally using a PoE switch that supplies both power and data. A separate video recorder is not required.
