# Basketball Computer Vision Pipeline: Multi-View Tracking & Fusion
**Author:** Syed Saliq Alishah

## Project Overview
This repository contains the complete codebase and output deliverables for the multi-camera basketball tracking assignment. The pipeline is designed to process four synchronized camera angles (Front-Left, Near-Left, Front-Right, Near-Right), detect players and the basketball, assign local tracking IDs, and fuse these identities across all views into a master dataset using persistent Global IDs.

## Architecture & Model Choices
The system relies on a multi-stage computer vision pipeline:
* **Object Detection (YOLOv8s):** Chosen for its optimal balance of real-time inference speed and accuracy. It successfully detects `class 0` (person) and `class 32` (sports ball) across the 1080p frames.
* **Intra-Camera Tracking (ByteTrack / DeepSORT):** Handles localized frame-to-frame tracking within single camera views, assigning persistent local track IDs while handling brief occlusions.
* **Cross-Camera Fusion & Re-ID (ResNet-50):** A truncated ResNet-50 model extracts 2048-dimensional appearance embeddings from bounding box crops. A cosine similarity matrix (threshold: `0.60`) is used to match and fuse identities across the four independent camera feeds into a single Global ID.

## Setup & Execution
The entire pipeline is consolidated into a single Google Colab environment to utilize GPU acceleration.
1. Clone this repository to your local machine or Google Drive.
2. Open `basketball_cv.ipynb` in Google Colab (ensure Hardware Accelerator is set to T4 GPU).
3. Mount Google Drive and install the required dependencies:
   ```bash
   pip install ultralytics opencv-python supervision pandas
