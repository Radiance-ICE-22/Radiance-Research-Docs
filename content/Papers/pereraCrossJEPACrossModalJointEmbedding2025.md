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

![[Pasted image 20260623180152.png]]

Functionally the model learns the 3D world from the feed it gets from the 2D images (multiple 2D viewpoints simultaneously). Rather than not only relying on 3D  point cloud, the structure is learned from the 2D image embedding. The visual aspects are not learned because the predictor cancels out the visual aspects as noises from the direct feed provided as **latent information**(like color histogram of the image, orientation of the view point from where the image was taken)


### Whats the problem they are trying to solve?
They are trying to solve two major issues present in 3d representation learning.
1. Scarcity of 3D training data
2. Massive computational efficiency 
### Have they solved it?
Their results suggests that are achieved a really good performance across the board better performance in terms
- Higher or similar accuracy of state of the art models 
- Reduced training data
- Reduced GPU time 
### If yes, how, if not why?
The paper focuses on a **multi-modal  JEPA architecture** for 3d representational learning. 

Scarcity of 3D training data is tackled by using [[Knowledge Distillation]]. There's lack of availability of large foundational models for 3D data. Using [[JEPA]] based **encoder weights**(teacher) like DinoV2 they distilled learned high level image semantics of million of 2D images (structural aspects) over 3D point clouds (student)

The computational  inefficiency is tacked by incorporating [[Latent Space ]] embedding with a light weight encoder and streaming mechanism uses cached images from the disk and fusing them for pre-training.  

#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context.?

The points, fixed projection parameters as a current limitation. and **adaptive or learnable projection** as a way to improve the pipeline. 

Further they suggest to integrate richer multi-modal setups like **LiDAR and camera data**.

For different views, incorporating [[Spherical Harmonics]] is a way to go forward as an improvement.

Patch level representations of the image could be beneficial **making the learning objective more specific**

>[!note] Other applications
>[[Autonomous Driving]] shows direct applicability 



## Questions & Answers

Q:  How can [[JEPA]] architecture useful for 3D represntational learning ? 
A: 
[[JEPA]] is grounded in human like learning, learning the structural semantics rather than the fine details.  One notable work [[pereraCrossJEPACrossModalJointEmbedding2025]] suggests a multi-modal training framework based on JEPA which uses [[Knowledge Distillation]] as a primary mean of training 3D point cloud from the information disiled from **foundational encoder model** is promising in that direction.

![[Pasted image 20260623201104.png]]


- - -

## 🔗 Related to
- [[lecunPathAutonomousMachine]]
