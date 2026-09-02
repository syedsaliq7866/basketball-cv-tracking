# Basketball Multi-Camera CV Tracking

This repository contains the end-to-end computer vision pipeline for multi-camera basketball player and ball tracking, built using YOLOv8, ByteTrack, ResNet-50 Deep Appearance Embeddings, OpenCV, and Pandas.

---

### Progress Status

**Milestones 1–3: Single-Camera Tracking Pipeline**
* Configured YOLOv8s for concurrent detection of players (`class 0`) and the basketball (`class 32`).
* Integrated ByteTrack for persistent local identity tracking across video frames.
* Implemented dynamic Region of Interest (ROI) spatial filters to exclude out-of-bounds bench personnel and background murals.
* Generated annotated tracking videos and frame-by-frame coordinate logs (`.csv`) for both camera angles:
  * Front-Left (`FL_full_5min_tracks_v2.csv`) — 8,994 frames
  * Near-Left (`NL_full_5min_tracks.csv`) — 8,993 frames

**Milestone 4: Cross-Camera Re-ID & Global Identity Matching (Completed)**
* Filtered stable player tracklets ($\ge 45$ frames) to drop transient false-positive trajectories.
* Extracted deep visual appearance feature vectors using a truncated ResNet-50 CNN backbone across player tracklet crops in both camera views.
* Evaluated cross-camera associations using a Cosine Distance Matrix and greedy bipartite matching ($\ge 0.60$ similarity threshold).
* Unified 162,291 detection records across Front-Left (`FL`) and Near-Left (`NL`) into **85 persistent Global IDs**.
* Generated master tracking dataset (`global_tracks_unified.csv`).

**Milestone 5: Event & Shot Detection (Next Up)**
* Pending: Hoop-zone ROI calibration and trajectory arc classification (apex verification & downward basket penetration) for shot attempt/conversion logging.

---

### Cross-Camera Association Architecture

| Stage | Method / Component | Target / Objective |
| :--- | :--- | :--- |
| **Local MOT** | ByteTrack | High-speed, frame-to-frame trajectory persistence per camera |
| **Feature Extraction** | Pre-trained ResNet-50 | 2048-dimensional L2-normalized appearance embeddings from player crops |
| **Global Association** | Cosine Metric + Distance Matrix | Pairwise identity matching between `FL` and `NL` identity clusters |
| **Schema Merge** | Unified Schema Integration | Consolidated dataset linking local camera tracks to unified Global IDs |

---

### Milestone 4: Cross-Camera Association Results

| Metric | Result |
| :--- | :--- |
| **Total Detection Records Unified** | 162,291 records |
| **Cameras Integrated** | Front-Left (`FL`), Near-Left (`NL`) |
| **Total Persistent Global IDs** | 85 matched identities |
| **Minimum Tracklet Threshold** | 45 frames (~1.5s persistence) |
| **Appearance Similarity Cutoff** | $\ge 0.60$ Cosine Score |

**Sample Cross-Camera Global Identity Mappings:**

| Global ID | FL Local ID | NL Local ID | Status |
| :---: | :---: | :---: | :---: |
| **01** | Player 303 | Player 619 | Verified Match |
| **02** | Player 1856 | Player 3005 | Verified Match |
| **03** | Player 2793 | Player 3657 | Verified Match |
| **04** | Player 2107 | Player 7734 | Verified Match |
| **05** | Player 819 | Player 2011 | Verified Match |

---

### Documented Constraints & Solutions

* **High-Speed Motion Blur:** Standard single-frame YOLO detection experiences mid-air ball dropouts during rapid passes. Addressed by isolating ball detection from ByteTrack Kalman motion constraints and focusing trajectory checks on the shooting arc.
* **Network FUSE I/O Bottlenecks:** Direct random-access seeking (`cap.set`) across Google Drive FUSE caused significant latency during crop extraction. Resolved by batch-scheduling frame targets and executing a single sequential video stream.
* **Jersey Kit Homogeneity:** Player appearance features within the same team share color profiles; identity stability is maintained by aggregating multi-crop mean vectors and applying a minimum persistence filter ($\ge 45$ frames).

---

### Project Structure

```text
├── basketball_cv.ipynb          # End-to-end pipeline: Tracking, Re-ID extraction, and matching
└── README.md                    # System architecture documentation and milestone tracker
