# Basketball Multi-Camera CV Tracking

This repository contains the end-to-end computer vision pipeline for multi-camera basketball player and ball tracking, built using YOLOv8, ByteTrack, ResNet-50 Deep Appearance Embeddings, OpenCV, and Pandas.

---

### Progress Status

**Milestone 1–3: Single-Camera Tracking Pipeline**
* Configured YOLOv8s for concurrent detection of players (`class 0`) and the basketball (`class 32`).
* Integrated ByteTrack for persistent local identity tracking across video frames.
* Implemented dynamic Region of Interest (ROI) spatial filters to exclude out-of-bounds bench personnel and background wall murals.
* Generated annotated tracking videos and frame-by-frame coordinate logs (`.csv`) for both camera angles:
  * Front-Left (`FL_full_5min_tracks_v2.csv`)
  * Near-Left (`NL_full_5min_tracks.csv`)

**Milestone 4: Cross-Camera Re-ID & Global Identity Matching**
* Extracted deep visual appearance feature vectors (DeepSORT/ResNet-50 architecture) across player tracklets in both camera views.
* Computed cosine similarity matrices and solved optimal global ID assignment across camera feeds.
* Output unified global tracking dataset (`global_tracks_unified.csv`).

**Milestone 5: Event & Shot Detection**
* In active development: hoop-zone trajectory parsing to detect shot attempts and conversion events.

---

### Cross-Camera Association Architecture

| Stage | Method | Target / Objective |
| :--- | :--- | :--- |
| **Local Tracking** | ByteTrack | High-speed, frame-to-frame trajectory persistence per camera |
| **Feature Extraction** | Pre-trained ResNet-50 (DeepSORT-style) | 2048-d normalized appearance embeddings from sampled player crops |
| **Global Association** | Cosine Similarity + Distance Matrix | Bipartite matching between `FL` and `NL` identity clusters |
| **Schema Merge** | Unified Global ID Mapping | Merged dataset linking local tracks to global persistent entities |

---

### Milestone 4: Cross-Camera Association Sample

| Global ID | FL Local ID | NL Local ID | Appearance Similarity | Status |
| :---: | :---: | :---: | :---: | :---: |
| **1** | Player 2 | Player 5 | **0.892** | Verified Match |
| **2** | Player 3 | Player 7 | **0.864** | Verified Match |
| **3** | Player 4 | Player 4 | **0.831** | Verified Match |
| **4** | Player 6 | Player 9 | **0.785** | Verified Match |
| **5** | Player 8 | Player 12 | **0.758** | Verified Match |

---

### Documented Constraints & Failure Modes

* **High-Speed Motion Blur:** Standard single-frame YOLO detection experiences brief mid-air track dropouts during rapid ball passes due to linear blur and non-rigid deformation.
* **Impact on Shot Detection:** This constraint does not block Milestone 5; projectile velocity decreases significantly at the trajectory apex and rim approach, restoring high-confidence detection where scoring events occur.
* **Jersey Occlusion & Kit Homogeneity:** Players on the same team share identical kit colors; temporal smoothing and high confidence thresholds ($\ge 0.60$) are used to prevent false identity swaps.

---

### Project Structure

```text
├── basketball_cv.ipynb          # End-to-end pipeline: Tracking, Re-ID extraction, and matching
├── outputs/
│   ├── FL_full_5min_tracks_v2.csv   # Production FL tracking log
│   └── global_tracks_sample.csv     # Sample cross-camera unified global tracks
└── README.md                    # Project documentation and milestone tracking
