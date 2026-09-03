# Basketball Computer Vision Pipeline: Multi-View Tracking & Fusion
**Author:** Syed Saliq Alishah

A multi-camera computer vision pipeline that detects, tracks, and re-identifies basketball players and the ball across four synchronized camera feeds, fusing local track identities into a single set of persistent Global IDs.

## Overview
This repository contains the complete codebase and output deliverables for a multi-camera basketball tracking assignment. The pipeline ingests four synchronized camera angles (Front-Left, Near-Left, Front-Right, and Near-Right) and produces a unified, cross-camera tracking dataset by:
1. Detecting players and the basketball in each view
2. Assigning local (per-camera) tracking IDs
3. Fusing those local identities across all four views using appearance-based re-identification
4. Outputting a master dataset with persistent Global IDs

## Architecture & Model Choices
The pipeline is built as a multi-stage CV system:

| Stage | Model / Method | Purpose |
| :--- | :--- | :--- |
| **Object Detection** | YOLOv8s | Detects class `0` (person) and class `32` (sports ball) across 1080p frames, balancing real-time speed with accuracy. |
| **Intra-Camera Tracking** | ByteTrack / DeepSORT | Performs frame-to-frame tracking within a single camera view, maintaining persistent local track IDs through brief occlusions. |
| **Cross-Camera Fusion & Re-ID** | ResNet-50 truncated() | Extracts 2048-dimensional appearance embeddings from detection crops; a cosine similarity matrix (threshold `0.60`) matches identities across the four camera feeds and merges them into a single Global ID. |

## Repository Structure
```text
basketball-cv-tracking/
├── basketball_cv.ipynb     # End-to-end pipeline: detection, tracking, and Re-ID fusion
├── output/                 # Generated tracking outputs / deliverables
└── README.md
