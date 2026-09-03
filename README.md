# 🏀 Basketball Computer Vision Pipeline

## Multi-View Tracking & Cross-Camera Identity Fusion

**Author:** Syed Saliq Alishah

A multi-camera computer vision pipeline for **detecting, tracking, and re-identifying basketball players and the ball across four synchronized camera feeds**. The system combines intra-camera tracking with appearance-based cross-camera Re-ID to produce persistent **Global IDs** across multiple viewpoints.

---

## 📌 Overview

This repository contains the complete implementation and output deliverables for a **multi-camera basketball tracking system**.

The pipeline processes four synchronized camera views:

* **Front-Left (FL)**
* **Near-Left (NL)**
* **Front-Right (FR)**
* **Near-Right (NR)**

The system performs the following operations:

1. 🎯 **Object Detection** — Detects basketball players and the ball in each camera view.
2. 🆔 **Intra-Camera Tracking** — Assigns persistent local tracking IDs within each camera.
3. 🔄 **Cross-Camera Re-Identification** — Extracts appearance embeddings and matches identities across camera views.
4. 🌐 **Global Identity Fusion** — Merges local track IDs into persistent Global IDs.
5. 📊 **Dataset Generation** — Produces a unified tracking dataset containing detections, local IDs, and Global IDs.

### Pipeline Flow

```text
                    ┌─────────────────────┐
                    │  4 Synchronized     │
                    │    Camera Feeds     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   YOLOv8s Detection │
                    │  Player + Ball      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Intra-Camera        │
                    │ Tracking            │
                    │ ByteTrack/DeepSORT  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Appearance Feature  │
                    │ Extraction          │
                    │ ResNet-50           │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Cosine Similarity   │
                    │ Cross-Camera Re-ID  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Global ID Fusion    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Unified Tracking    │
                    │ Dataset             │
                    └─────────────────────┘
```

---

## 🏗️ Architecture & Model Choices

| Stage                     | Model / Method                | Purpose                                                                                                                             |
| :------------------------ | :---------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| **Object Detection**      | YOLOv8s                       | Detects players (`class 0`) and the basketball (`class 32`) in 1080p frames while balancing detection accuracy and inference speed. |
| **Intra-Camera Tracking** | ByteTrack / DeepSORT          | Maintains persistent local track IDs within each camera and handles short-term occlusions.                                          |
| **Feature Extraction**    | ResNet-50                     | Extracts 2048-dimensional appearance embeddings from player detection crops.                                                        |
| **Cross-Camera Re-ID**    | Cosine Similarity             | Compares appearance embeddings across camera views to identify the same player.                                                     |
| **Global ID Fusion**      | Similarity Threshold = `0.60` | Matches local identities and merges them into persistent Global IDs across the four camera feeds.                                   |

---

## 📂 Repository Structure

```text
basketball-cv-tracking/
│
├── basketball_cv.ipynb
│   └── End-to-end pipeline for detection, tracking, Re-ID, and fusion
│
├── output/
│   └── Generated tracking outputs and final deliverables
│
└── README.md
```

---

## ⚙️ Setup & Execution

The pipeline is designed to run in **Google Colab** and can take advantage of a GPU such as the NVIDIA T4.

### 1. Clone the Repository

```bash
git clone https://github.com/syedsaliq7866/basketball-cv-tracking.git
cd basketball-cv-tracking
```

### 2. Open the Notebook

Open:

```text
basketball_cv.ipynb
```

in **Google Colab**.

For GPU acceleration, select:

```text
Runtime → Change runtime type → T4 GPU
```

### 3. Install Dependencies

Run the following inside the Colab notebook:

```bash
pip install ultralytics opencv-python supervision pandas
```

If your video data is stored in Google Drive, mount Google Drive before running the pipeline.

### 4. Run the Pipeline

Execute the notebook cells sequentially or use:

```text
Runtime → Run all
```

The notebook will perform:

```text
Detection
   ↓
Intra-Camera Tracking
   ↓
Appearance Embedding Extraction
   ↓
Cross-Camera Re-ID
   ↓
Global ID Fusion
   ↓
Output Generation
```

---

## 📊 Output

The pipeline generates a **master tracking dataset** containing information for each detected object across every frame and camera view.

The output includes:

* 📍 Bounding boxes for players and the basketball
* 🎥 Camera/view identifier
* 🆔 Local track ID for each camera
* 🌐 Fused Global ID across camera views
* 🕐 Frame-level tracking information

Generated artifacts are stored in:

```text
output/
```

The resulting dataset can be used for further analysis, visualization, player trajectory analysis, and multi-camera sports analytics.

---

# ⚠️ Known Limitations & Failure Modes

During evaluation of the four-camera fusion pipeline, several limitations were identified.

## 1. View-Angle Appearance Shift

### Problem

The Re-ID system performed relatively well when matching cameras on the **same side of the court**, for example:

```text
Front-Left  ↔ Near-Left
Front-Right ↔ Near-Right
```

However, matching identities across the center line was more challenging.

Players appear from substantially different orientations between the left and right camera groups. Consequently, one camera may primarily capture the **front of a jersey**, while another captures the **back**.

This creates a significant appearance shift, causing the ResNet-50 embeddings to fall below the configured:

```text
Cosine Similarity Threshold = 0.60
```

As a result, the same physical player can occasionally receive different Global IDs across the two court hemispheres.

### Proposed Improvement

Implement a **multi-view appearance gallery** for every Global ID.

For example:

```text
Global ID 12
├── Front Appearance Embedding
├── Back Appearance Embedding
└── Side Appearance Embedding
```

Another potential solution is to incorporate **jersey-number OCR**, providing a semantic identity signal that is less sensitive to viewing direction.

---

## 2. Static False Positives — Wall Murals

### Problem

The YOLOv8s detector occasionally identified **painted basketball-player murals on gym walls** as class `0` (`person`) detections.

Because these objects are static, tracking algorithms such as ByteTrack or DeepSORT can assign them persistent tracking IDs.

This results in false player tracks.

### Proposed Improvement

Implement stricter **Region of Interest (ROI) filtering** to restrict valid detections to the playable hardwood area.

For example:

```text
┌──────────────────────────────────┐
│          WALL / MURALS           │
│        ❌ Ignore detections       │
├──────────────────────────────────┤
│                                  │
│          🏀 PLAYING COURT        │
│          ✓ Valid detections      │
│                                  │
└──────────────────────────────────┘
```

An additional improvement would be to use **motion/velocity filtering** to remove detections that remain completely stationary over time.

---

## 3. Re-ID Under Occlusion

### Problem

During close physical plays, particularly around the basket, players frequently overlap.

This can result in detection crops containing visual information from **multiple players**, which degrades the quality of the extracted appearance embedding.

Consequently, the Re-ID system may temporarily:

* Reduce matching confidence
* Fail to match an identity
* Produce an identity switch

### Proposed Improvement

Potential improvements include:

* Improved person segmentation before embedding extraction
* Higher-quality detection crops
* Temporal feature aggregation
* Occlusion-aware Re-ID models
* Multi-frame embedding averaging
* Pose-based feature extraction

---

# 📦 Requirements

### Software

* Python **3.8+**
* Google Colab (recommended)
* CUDA-compatible GPU (recommended)

### Python Dependencies

```text
ultralytics
opencv-python
supervision
pandas
```

Install them with:

```bash
pip install ultralytics opencv-python supervision pandas
```

### Hardware

A GPU is recommended for practical inference speed.

```text
Recommended:
NVIDIA T4 GPU or better
```

The pipeline can also run on CPU, although inference will be significantly slower.

---

# 🚀 Future Improvements

The current implementation provides a functional multi-camera tracking and fusion pipeline. Several improvements could further increase robustness and identity consistency:

* [ ] Multi-view Re-ID gallery per Global ID
* [ ] Jersey-number OCR
* [ ] Stronger person Re-ID model
* [ ] Improved occlusion handling
* [ ] Court-aware ROI filtering
* [ ] Motion-based false-positive filtering
* [ ] Temporal embedding aggregation
* [ ] Camera calibration and geometric constraints
* [ ] Homography-based cross-camera localization
* [ ] Automated evaluation metrics such as IDF1, MOTA, and HOTA

---

# 📈 Key Takeaways

This project demonstrates an end-to-end approach to **multi-camera sports tracking**, combining:

```text
Object Detection
       +
Intra-Camera Tracking
       +
Appearance-Based Re-ID
       +
Cross-Camera Identity Fusion
       =
Global Multi-Camera Tracking
```

The system highlights both the capabilities and challenges of deploying computer vision for real-world multi-view sports analytics, particularly around **viewpoint changes, occlusion, and false detections**.

---

# 📄 License

No license has been specified for this repository.

Please contact the repository owner for usage and distribution permissions.

---

## 👤 Author

**Syed Saliq Alishah**

GitHub: [@syedsaliq7866](https://github.com/syedsaliq7866)
