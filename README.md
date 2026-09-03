# 🏀 Basketball Computer Vision Pipeline

### Multi-View Tracking & Cross-Camera Identity Fusion

**Author:** Syed Saliq Alishah

A multi-camera computer vision pipeline that detects, tracks, and re-identifies basketball players and the ball across **four synchronized camera feeds**, producing persistent **Global IDs** across views.

## 🔍 Overview

The pipeline performs:

1. **Object Detection** — YOLOv8s detects players (`class 0`) and the basketball (`class 32`).
2. **Intra-Camera Tracking** — ByteTrack / DeepSORT assigns persistent local track IDs.
3. **Cross-Camera Re-ID** — ResNet-50 extracts 2048-dimensional appearance embeddings.
4. **Identity Fusion** — Cosine similarity (`threshold = 0.60`) matches local tracks across cameras and generates Global IDs.

### Camera Views

```text
Front-Left → Near-Left
Front-Right → Near-Right
```

## 🏗️ Architecture

| Stage              | Method               | Purpose                 |
| ------------------ | -------------------- | ----------------------- |
| Detection          | YOLOv8s              | Player & ball detection |
| Tracking           | ByteTrack / DeepSORT | Local tracking IDs      |
| Feature Extraction | ResNet-50            | Appearance embeddings   |
| Re-ID              | Cosine Similarity    | Cross-camera matching   |
| Fusion             | Global IDs           | Unified identities      |

## 📂 Repository Structure

```text
basketball-cv-tracking/
├── basketball_cv.ipynb
├── output/
└── README.md
```

## ⚙️ Setup

Clone the repository:

```bash
git clone https://github.com/syedsaliq7866/basketball-cv-tracking.git
cd basketball-cv-tracking
```

Open `basketball_cv.ipynb` in **Google Colab**, enable a **T4 GPU**, and install:

```bash
pip install ultralytics opencv-python supervision pandas
```

Run all notebook cells to execute the complete pipeline.

## 📊 Output

The pipeline generates a master tracking dataset containing:

* Bounding boxes
* Camera IDs
* Local Track IDs
* Global IDs
* Frame-level tracking information

Outputs are saved in:

```text
output/
```

## ⚠️ Known Limitations

* **Viewpoint changes:** Front/back jersey appearances can reduce cross-camera Re-ID accuracy.
* **Static false positives:** Wall murals may be detected as players.
* **Occlusion:** Overlapping players can degrade embeddings and cause temporary ID switches.

### Potential Improvements

* Multi-view Re-ID galleries
* Jersey-number OCR
* Stronger Re-ID models
* Improved ROI filtering
* Occlusion-aware tracking
* Temporal embedding aggregation

## 📦 Requirements

* Python 3.8+
* NVIDIA T4 GPU or better recommended
* YOLOv8 / Ultralytics
* OpenCV
* Supervision
* Pandas

## 📄 License

No license specified. Please contact the repository owner for usage permissions.

## 👤 Author

**Syed Saliq Alishah**

GitHub: https://github.com/syedsaliq7866
