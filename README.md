Basketball Computer Vision Pipeline: Multi-View
Tracking & Fusion
Author: Syed Saliq Alishah
A multi-camera computer vision pipeline that detects, tracks, and re-identifies basketball
players and the ball across four synchronized camera feeds, fusing local track identities into
a single set of persistent Global IDs.
 Overview
This repository contains the complete codebase and output deliverables for a multi-camera
basketball tracking assignment. The pipeline ingests four synchronized camera angles —
Front-Left, Near-Left, Front-Right, and Near-Right — and produces a unified, crosscamera tracking dataset by:
1. Detecting players and the basketball in each view
2. Assigning local (per-camera) tracking IDs
3. Fusing those local identities across all four views using appearance-based reidentification
4. Outputting a master dataset with persistent Global IDs
 Architecture & Model Choices
The pipeline is built as a multi-stage CV system:
Stage Model /
Method Purpose
Object
Detection YOLOv8s
Detects class 0 (person) and class 32 (sports ball) across
1080p frames, balancing real-time speed with accuracy
IntraCamera
ByteTrack /
DeepSORT
Performs frame-to-frame tracking within a single camera view,
maintaining persistent local track IDs through brief occlusions
Tracking
CrossCamera
Fusion &
Re-ID
ResNet-50
(truncated)
Extracts 2048-dimensional appearance embeddings from
detection crops; a cosine similarity matrix (threshold 0.60 )
matches identities across the four camera feeds and merges
them into a single Global ID
 Repository Structure
 Setup & Execution
The entire pipeline is designed to run in a single Google Colab notebook to take advantage
of free GPU acceleration.
1. Clone the repository
git clone https://github.com/syedsaliq7866/basketball-cv-tracking.git
2. Open the notebook Open basketball_cv.ipynb in Google Colab and set the hardware
accelerator to T4 GPU ( Runtime → Change runtime type → T4 GPU ).
3. Mount Google Drive (if your video data is stored there) and install dependencies:
pip install ultralytics opencv-python supervision pandas
4. Run all cells to execute detection, intra-camera tracking, and cross-camera Re-ID
fusion in sequence.
 Output
The pipeline produces a master tracking dataset containing, per frame and per camera
view:
basketball-cv-tracking/
├── basketball_cv.ipynb # End-to-end pipeline: detection, tracking, and Re-ID fusion
├── output/ # Generated tracking outputs / deliverables
└── README.md
Bounding boxes for detected players and the ball
Local track IDs (per camera)
Fused Global IDs (consistent across all four camera views)
Generated artifacts are saved to the output/ directory.
 Requirements
Python 3.8+
GPU (T4 or better recommended)
ultralytics
opencv-python
supervision
pandas
 License
No license specified. Please contact the repository owner for usage terms.
