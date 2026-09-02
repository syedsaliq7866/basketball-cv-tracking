# Basketball Multi-Camera CV Tracking

This repository contains the end-to-end computer vision pipeline for multi-camera basketball player and ball tracking, built using YOLOv8, ByteTrack, OpenCV, and Pandas.

---

### Progress Status

**Milestone 1–3: Single-Camera Tracking Pipeline**

* Configured YOLOv8s for concurrent detection of players (`class 0`) and the basketball (`class 32`).
* Integrated ByteTrack for persistent local identity tracking across video frames.
* Implemented dynamic Region of Interest (ROI) spatial filters to exclude out-of-bounds bench players and background murals.
* Generated annotated tracking videos and frame-by-frame coordinate logs (`.csv`) for both camera angles:
* Front-Left (`FL_full_5min_tracks_v2.csv`)
* Near-Left (`NL_full_5min_tracks.csv`)



**Milestone 4: Cross-Camera Global ID Matching**

* In active development: mapping local tracklet identities across `FL` and `NL` camera views using timestamp synchronization and spatial/appearance association into a unified global ID schema.

**Milestone 5: Event & Shot Detection**

* Pending: rim-zone trajectory parsing to detect shot attempts and conversions.

---

### Pipeline Architecture

| Stage | Component | Description |
| --- | --- | --- |
| **Detection** | YOLOv8s | Frame-by-frame object inference on scaled frames ($960\times960$) |
| **Tracking** | ByteTrack | Multi-object tracking associating high and low confidence detections |
| **Filtering** | Spatial ROIs | Rejection of sideline personnel and high-contrast mural boundaries |
| **Output** | CSV Logs & Video | Normalized bounding box coordinates, timestamps, and confidence metrics |

---

### Documented Constraints & Failure Modes

* **High-Speed Motion Blur:** Standard single-frame YOLO detection experiences mid-air track dropouts during rapid ball passes due to linear blur and non-rigid deformation.
* **Impact on Shot Detection:** This constraint does not block Milestone 5; projectile velocity decreases significantly at the trajectory apex and rim approach, restoring high-confidence detection where event scoring occurs.

---

### Project Structure

```text
├── basketball_cv.ipynb         # Main Colab execution pipeline (Setup, FL, NL tracking)
├── README.md                   # System documentation and milestone tracker

```
