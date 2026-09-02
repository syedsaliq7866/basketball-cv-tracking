Step 1: Copy this Template
Basketball Multi-Camera CV Tracking
This repository contains the computer vision pipeline for multi-camera basketball player and ball tracking, built using YOLOv8 and ByteTrack.

Current Progress
✅ Milestone 1-3: Single-Camera Tracking (Completed)

Configured YOLOv8 for player (class 0) and sports ball (class 32) detection.

Implemented ByteTrack for persistent frame-to-frame local ID assignment.

Applied spatial ROI (Region of Interest) filters to dynamically exclude sideline bench players and out-of-bounds wall murals.

Generated structured tracking logs (CSV) for the full 5-minute FL (Front-Left) camera view.

Known Constraints (Iterative Documentation)
High-Speed Motion Blur: Standard YOLO detection drops the basketball mid-air during high-velocity passes due to extreme motion blur. This is an expected constraint and will not block Milestone 5 (Shot Detection), as the ball regains shape and is reliably detected at the apex of its trajectory near the hoop.

Next Steps
🔄 Process the Near-Left (NL) camera view.

🔄 Milestone 4: Cross-Camera Appearance Matching (Assigning Global IDs across FL and NL).

🔄 Milestone 5: Trajectory-based Shot Detection.
