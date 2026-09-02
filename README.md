# Basketball Multi-Camera CV Tracking

An end-to-end computer vision pipeline for multi-camera basketball player tracking, cross-camera identity association, and automated shot event detection built with YOLOv8, ByteTrack, ResNet-50 appearance embeddings, and OpenCV.

---

### Milestone Progress Tracker

* **Milestones 1–3: Single-Camera Tracking Pipeline (Completed)**
  * Concurrent detection of court players (`class 0`) and the basketball (`class 32`) via YOLOv8s.
  * Multi-object tracking with ByteTrack leveraging Kalman filter state estimation.
  * Spatial ROI boundary masking to filter bench personnel and out-of-bounds movement.
  * Processed 8,994 frames for Front-Left (`FL`) and 8,993 frames for Near-Left (`NL`).

* **Milestone 4: Cross-Camera Re-ID & Identity Matching (Completed)**
  * Extracted 2048-dimensional appearance feature vectors using a truncated ResNet-50 CNN backbone.
  * Applied cosine similarity and greedy bipartite matching ($\ge 0.60$ threshold) across player tracklets ($\ge 45$ frames).
  * Unified 162,291 detection records across `FL` and `NL` into **85 persistent Global IDs**.
  * Generated master dataset: `global_tracks_unified.csv`.

* **Milestone 5: Event & Shot Detection (Completed)**
  * Solved high-speed motion blur and ball dropouts using a localized Hoop Region of Interest (ROI) approach ($915 \le X \le 975$, $235 \le Y \le 290$).
  * Monitored parabolic trajectory arcs (ascent, apex verification, and downward penetration through the rim plane).
  * Analyzed 8,994 frames (~30 FPS) on the primary half-court hoop, successfully identifying 5 verified shot events.
  * Generated event log: `FL_shot_events.csv`.

---

### Milestone 5: Detected Shot Events Log (Far Basket)

| Shot ID | Frame | Timestamp (s) | Apex Y (px) | Event Classification |
| :---: | :---: | :---: | :---: | :---: |
| **01** | 2,856 | 95.30s | 209 | Made Basket |
| **02** | 3,503 | 116.88s | 138 | Shot Attempt |
| **03** | 5,786 | 193.06s | 214 | Shot Attempt |
| **04** | 6,197 | 206.77s | 220 | Shot Attempt |
| **05** | 7,725 | 257.76s | 183 | Made Basket |

> **Note on Event Frequency:** The detector monitors the far hoop in the Front-Left camera view. Across a 5-minute full-court game, possessions alternate between hoops, yielding roughly ~2.5 minutes of active offensive play on this basket. The 5 detected attempts represent the shots taken within this half-court target zone.

---

### System Architecture & Pipeline Design

| Component | Target Objective | Strategy / Model |
| :--- | :--- | :--- |
| **Detection** | Player & Ball Localization | YOLOv8s ($320\text{ px}$ to $1280\text{ px}$ inference) |
| **MOT Tracking** | Single-Camera Persistence | ByteTrack (Kalman Filter + Spatial IoU) |
| **Re-ID / Global ID** | Multi-Camera Fusion | ResNet-50 truncated backbone + Cosine similarity |
| **Shot Detection** | Event Logging | Parabolic trajectory evaluation within calibrated hoop zone |

---

### Documented Constraints & Mitigations

* **Motion Blur & Ball Dropouts:** Full-court tracking frequently drops the basketball during rapid passes and shots. Solved by isolating high-resolution inference to the hoop zone and lowering the detection threshold (`conf=0.05`) within the localized ROI.
* **Network I/O Bottlenecks:** Eliminated random frame seeking over Google Drive FUSE by performing sequential frame passes with trajectory buffering.
* **Rebound Multi-Counting:** Added a 75-frame (2.5s) cooldown window immediately after a shot trigger to prevent ball rebounds and putback scrambles from causing duplicate detections.

---

### Project Structure

```text
├── basketball_cv.ipynb          # Full pipeline: Detection, Tracking, Re-ID, and Shot Detection
└── README.md                    # Architecture documentation, milestone logs, and metrics
