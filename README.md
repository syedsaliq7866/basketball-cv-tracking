### Known Limitations & Failure Modes

As part of the final evaluation, several failure modes were identified during the 4-camera fusion process:

1. **View-Angle Appearance Shift (Left-to-Right Court Split):** 
   While the ResNet-50 Re-ID pipeline successfully linked identities between cameras on the same side of the court (e.g., matching FL to NL, and FR to NR), it struggled to fuse identities across the center line. Because players face opposite directions relative to the left vs. right cameras, the visual features extracted (front of jersey vs. back of jersey) fell below the `0.60` cosine similarity threshold, resulting in split Global IDs across the hemispheres.
   * *Proposed Improvement:* Implement a multi-view gallery for each Global ID that stores both front and back embedding centroids, or integrate jersey number OCR to provide a hard semantic link.

2. **Static False Positives (Wall Murals):**
   The YOLOv8s detector confidently identified painted murals of basketball players on the gym walls as active class `0` (person) detections. Because these murals do not move, ByteTrack and DeepSORT easily assigned them stable tracking IDs.
   * *Proposed Improvement:* Tighten the coordinate-based Region of Interest (ROI) exclusion masks to strictly encompass the hardwood floor, or introduce a minimum velocity threshold to drop static bounding boxes.

3. **Re-ID Under Occlusion:**
   During tight physical plays under the rim, bounding boxes frequently overlap, causing the cropped image tensors to contain visual features of two distinct jerseys. This briefly degrades the accuracy of the ResNet-50 embedding, sometimes causing an identity switch if a player breaks out of the cluster.
