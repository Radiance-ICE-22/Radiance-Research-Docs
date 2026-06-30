---
title: 3DGS-LSR：Large-Scale Relocation for Autonomous Driving Based on 3D Gaussian Splatting
aliases:
  - "@lu3DGSLSRLargeScaleRelocationAutonomous"
tags:
  - literature
  - slam
  - edge-computing
draft: true
---

> [!info] Paper Metadata
> - **Citekey:** `@lu3DGSLSRLargeScaleRelocationAutonomous`
> - **Authors:** Haitao Lu, Haijier Chen, Haoze Liu, Shoujian Zhang, Bo Xu, Ziao Liu
> - **Year:** Error: `format` can only be applied to dates. Tried for format object
> - **DOI/Link:** [](https://doi.org/)

## 📌 One-Sentence Summary
**3DGS-LSR** introduces a large-scale re-localization framework for autonomous driving that leverages a single monocular camera alongside SuperPoint, SuperGlue, and the PnP algorithm to iteratively refine vehicle poses against a 3D Gaussian Splatting model, significantly reducing storage overhead compared to traditional point clouds while achieving sub-10cm accuracy.

---

## 📝 Reading Notes & Architecture


### Whats the problem they are trying to solve?
- **Signal Occlusion & Multi-path Effects:** Standard localization methods like GNSS (Global Navigation Satellite Systems) suffer from accuracy drops in urban environments due to signals reflecting off surfaces or being blocked by obstructions.
    
- **Accumulated Drift:** Trajectory and positioning data sourced from IMUs, LiDAR, and cameras naturally accumulate drift errors over time.
    
- **Storage Bottlenecks:** Storing high-accuracy, dense point cloud data for localization requires immense storage capacity, making edge deployment difficult.
### Have they solved it?
**Yes, within specific experimental constraints.** The authors achieved high-precision re-localization, reporting errors of **less than 10cm** and **less than 1 degree** using the real-world KITTI driving dataset.
### If yes, how, if not why?
The architecture solves the problem through a multi-stage vision and rendering pipeline:
![[Pasted image 20260607094558.png]]
1. **Feature Extraction & Matching:** They utilize **SuperPoint** (a CNN-based alternative to SIFT/ORB) to extract key feature points from a single monocular camera image, and **SuperGlue** (a deep neural network) for real-time feature matching.
    
2. **Initial Pose Estimation:** The initial pose (position and orientation) of the target image is computed using the **Perspective-n-Point (PnP) algorithm** against the 3DGS model.
    
3. **Iterative Render & Refinement:** Using the estimated pose, the 3DGS model renders corresponding RGB and depth images. These rendered views undergo an iterative refinement process to lock down the final, highly accurate vehicle pose.

![[Pasted image 20260621133244.png]]

> **Implementation Hardware:** The system was validated using Python and PyTorch on an Intel Core i9-13620H CPU, 16 GB of RAM, and an NVIDIA RTX 4090 GPU (24 GB VRAM).
#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context.?
**Yes, there are several areas open for improvement and adaptation:**

- **Address Evaluation Bias:** The paper evaluated its pipeline using a single image from the dataset, which might introduce bias. Re-testing across continuous, un-sequenced frames is needed to prove real-world robust deployment.
    
- **Overcome Unidirectional/Angular Constraints:** Because the KITTI dataset follows a strict unidirectional path, the 3DGS model's usable viewpoints are limited. If the vehicle experiences a significant angular deviation, the quality of the rendered map degrades sharply, ruining optimization convergence.
    
- **Future Research Paths:** * Developing methods to render high-fidelity 3DGS maps from completely free, unconstrained viewing angles despite limited data collection.
    
    - Improving the optimization sensitivity of the repositioning iteration loop so it handles large angular errors better without failing to converge.
        
- **Contextual Deployment:** This framework is highly relevant to edge deployment since 3DGS offers a more compact storage footprint than raw LiDAR point clouds. The same pipeline could be ported to warehouse robotics, drone navigation, or localized AR systems where storage is premium and camera-based re-localization is mandatory.
#### if no, what can you learn from it? can the lessons learnt be applied in any way? may be re-experiment with tuning/re-adjusting the methods/changing the parameters etc.


### Evaluation
![[Pasted image 20260621133122.png]]
## Questions & Answers
**Q**: What is the problem statement?
**A**: They need high accuracy localization for autonomous driving.

**Q**: Can you explain why 3DGS is need for this project?
**A**: Their claim is that 3DGS provide high accuracy localization grater than the classical algorithms. 

**Q**: On which part exactly does 3DGS improve the application, before this work, how it is handled?
**A**: Before classical algorithms like RANSAC and PNP was used for localization, there claim is accuracy is not enough for autonomous driving. Those classical algorithms only use keypoints for localization. But when using 3DGS, the estimation is re-rendered and compare to finalize a perfectly fitting pose.

**Q**: What advantage does 3DGS bring?
**A**: High accuracy localization..

- - -

## 🔗 Related to
- [[Project Architecture Master Note]]