---
title: "GSplatLoc: Grounding Keypoint Descriptors into 3D Gaussian Splatting for Improved Visual Localization"
aliases:
  - "@sidorovGSplatLocGroundingKeypoint2024"
tags:
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@sidorovGSplatLocGroundingKeypoint2024`
> - **Authors:** Gennady Sidorov, Malik Mohrat, Denis Gridusov, Ruslan Rakhimov, Sergey Kolyubin
> - **Year:** 2024
> - **DOI/Link:** [](https://doi.org/)

## 📌 One-Sentence Summary
The paper introduces **GSplatLoc**, a two-stage visual localization method that use XFeat for deep feature extractor (it is robust in extacting reliable and distinctive features) , these features are mebedded into the splats, then localization is achieved by pose refinement of guessing by comparing the guess rendered view and the photo taken.

---

## 📝 Reading Notes & Architecture


### Whats the problem they are trying to solve?
They are trying to give a solution for the challenges faced by previous methods for mobile and humanoid robots for navigation. There were some trade-off which the paper tries to overcome.
- **Image Retrieval & Absolute Pose Regression (APR):** Efficient, but struggle with scalability, lack geometric precision, and generalize poorly.
- **Scene Coordinate Regression (SCR):** High accuracy indoors, but demands extensive optimization, requires high-quality 3D training data, and struggles to scale up to complex, dynamic outdoor scenarios
- **NeRF-Based Neural Render Pose (NRP) estimation:** Capable of high-accuracy test-time optimization by comparing rendered views to query images, but severely limited by **slow inference speeds, long training times, and high computational overhead**.

### Have they solved it?
**Yes, within the scope of their evaluation targets.** The authors introduced **GSplatLoc**, a framework that outperforms previous neural rendering-based (NRP) approaches in both accuracy and speed, while requiring only a single RGB modality.

### If yes, how, if not why?
They developed a unified, two-stage framework that tightly couples keypoints feature extraction with 3DGS.
1. **Modeling stage: ** Creation of 3DGS,. They put 2D descriptors got from XFeat to each splat.
2. **Test stage** (Coarse to fine localization)
	- **Coarse phase**: When given a query image, they match its 2D XFeat descriptors with the 3D feature embeddings embedded in the Gaussian cloud via cosine similarity. Using a Perspective-n-Point (PnP) solver inside a RANSAC loop, they quickly compute an initial coarse pose.
	- **Refinement phase:** They use the fast, differentiable rasterization of 3DGS to project a "visual reference image" and depth map from that coarse pose. They optimize the camera pose by minimizing an **RGB warping loss** (photometric error) between the query image and the rendered reference.
![[Pasted image 20260702161419.png]]
#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context?
There are some enhancement the author is suggesting.
- **Eliminating floaters**: future work must focus on removing floaters (detached, semi-transparent noise Gaussians) from the 3DGS scene. floaters corrupt depth map estimation during the photometric warping phase.


## Questions & Answers

Q: 
A: 
- - -

## 🔗 Related to
- [[Project Architecture Master Note]]