---
title: "CrossJEPA: Cross-Modal Joint-Embedding Predictive Architecture for Efficient 3D Representation Learning from 2D Images"
aliases:
  - "@pereraCrossJEPACrossModalJointEmbedding2025"
tags:
  - literature
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@pereraCrossJEPACrossModalJointEmbedding2025`
> - **Authors:** Avishka Perera, Kumal Hewagamage, Saeedha Nazar, Kavishka Abeywardana, Hasitha Gallella, Ranga Rodrigo, Mohamed Afham
> - **Year:** 2025
> - **DOI/Link:** [10.48550/arXiv.2511.18424](https://doi.org/10.48550/arXiv.2511.18424)

## 📌 One-Sentence Summary
*What is the core contribution of this paper and how does it apply to our 3D Gaussian Splatting edge deployment architecture?*

---

## 📝 Reading Notes & Architecture

As of my understanding, they utilized JEPA method to learn the 3D world model from a cross-modal learning approach (2D images and 3D point  cloud).  They utilized a **knowledge distillation approach**
- Teacher Model : Frozen Image Encoder
- Student Model: 3D Point Encoder

Here, Teacher and Student co learning does not take place. They utilized JEPA's native loss minimization as a mean for knowledge distillation. Important difference is, JEPA uses masking whereas these people incorporated knowledge distillation.  

Functionally the model learns the 3D world from the feed it gets from the 2D images (multiple 2D viewpoints simultaneously). Rather than not only relying on 3D  point cloud, the structure is learned from the 2D image embedding. The visual aspects are not learned because the predictor cancels out the visual aspects as noises from the direct feed provided as **latent information**(like color histogram of the image, orientation of the view point from where the image was taken)

## Questions & Answers

Q: 
A: 
- - -

## 🔗 Related to
- [[Project Architecture Master Note]]