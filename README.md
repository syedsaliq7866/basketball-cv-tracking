# Basketball Computer Vision Pipeline: Multi-View Tracking & Fusion[cite: 2]
**Author:** Syed Saliq Alishah[cite: 2]

A multi-camera computer vision pipeline that detects, tracks, and re-identifies basketball players and the ball across four synchronized camera feeds, fusing local track identities into a single set of persistent Global IDs.[cite: 2]

## Overview[cite: 2]
This repository contains the complete codebase and output deliverables for a multi-camera basketball tracking assignment.[cite: 2] The pipeline ingests four synchronized camera angles Front-Left, Near-Left, Front-Right, and Near-Right - and produces a unified, cross-camera tracking dataset by:[cite: 2]
1. Detecting players and the basketball in each view[cite: 2]
2. Assigning local (per-camera) tracking IDs[cite: 2]
3. Fusing those local identities across all four views using appearance-based re-identification[cite: 2]
4. Outputting a master dataset with persistent Global IDs[cite: 2]

## Architecture & Model Choices[cite: 2]
The pipeline is built as a multi-stage CV system:[cite: 2]

| Stage | Model / Method | Purpose |
| :--- | :--- | :--- |
| Object Detection[cite: 2] | YOLOv8s[cite: 2] | Detects class 0 (person) and class 32 (sports ball) across 1080p frames, balancing real-time speed with accuracy[cite: 2] |
| Intra-Camera Tracking[cite: 2] | ByteTrack / DeepSORT[cite: 2] | Performs frame-to-frame tracking within a single camera view, maintaining persistent local track IDs through brief occlusions[cite: 2] |
| Cross-Camera Fusion & Re-ID[cite: 2] | ResNet-50 truncated()[cite: 2] | Extracts 2048-dimensional appearance embeddings from detection crops; a cosine similarity matrix (threshold 0.60) matches identities across the four camera feeds and merges them into a single Global ID[cite: 2] |

## Repository Structure[cite: 2]
```text
basketball-cv-tracking/[cite: 2]
├── basketball_cv.ipynb  #End-to-end pipeline: detection, tracking, and Re-ID f[cite: 2]
├── output/              # Generated tracking outputs / deliverables[cite: 2]
└── README.md[cite: 2]
```

## Setup & Execution[cite: 2]
The entire pipeline is designed to run in a single Google Colab notebook to take advantage of free GPU acceleration.[cite: 2]
1. Clone the repository[cite: 2]
   ```bash
   git clone [https://github.com/syedsaliq7866/basketball-cv-tracking.git](https://github.com/syedsaliq7866/basketball-cv-tracking.git)[cite: 2]
   ```
2. Open the notebook Open basketball_cv.ipynb in Google Colab and set the hardware accelerator to T4 GPU (Runtime Change runtime type T4 GPU).[cite: 2]
3. Mount Google Drive (if your video data is stored there) and install dependencies:[cite: 2]
   ```bash
   pip install ultralytics opencv-python supervision pandas[cite: 2]
   ```
4. Run all cells to execute detection, intra-camera tracking, and cross-camera Re-ID fusion in sequence.[cite: 2]

## Output[cite: 2]
The pipeline produces a master tracking dataset containing, per frame and per camera view:[cite: 2]
* Bounding boxes for detected players and the ball[cite: 2]
* Local track IDs (per camera)[cite: 2]
* Fused Global IDs (consistent across all four camera views)[cite: 2]

Generated artifacts are saved to the output/ directory.[cite: 2]

## Requirements[cite: 2]
* Python 3.8+[cite: 2]
* GPU (T4 or better recommended)[cite: 2]
* ultralytics[cite: 2]
* opencv-python[cite: 2]
* supervision[cite: 2]
* pandas[cite: 2]

## License[cite: 2]
No license specified.[cite: 2] Please contact the repository owner for usage terms.[cite: 2]
