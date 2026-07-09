---
title: "Feature 3DGS: Supercharging 3D Gaussian Splatting to Enable Distilled Feature Fields"
aliases:
  - "@zhouFeature3DGSSupercharging2024"
tags:
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@zhouFeature3DGSSupercharging2024`
> - **Authors:** Shijie Zhou, Haoran Chang, Sicheng Jiang, Zhiwen Fan, Zehao Zhu, Dejia Xu, Pradyumna Chari, Suya You, Zhangyang Wang, Achuta Kadambi
> - **Year:** 2024
> - **DOI/Link:** [10.48550/arXiv.2312.03203](https://doi.org/10.48550/arXiv.2312.03203)

## 📌 One-Sentence Summary
Foundational work on 3dgs based semantic understanding introduces knowledge distillation and feature embedding into 3DGS Pipeline

---

## 📝 Reading Notes & Architecture

**Feature distillation from 2D Models** has two major limitations.
1. Limited by the rendering speed at training and inference **speed of NeRF** (at the time the paper)
2. **Continuity artifacts** form when rendering the features 

Existing methods for semantic representation for **only store [[Radiance Field]] information** not anything related to intelligence.

>[!warning] 
>3DGS doesn't naively support the semantic learning, it focuses on the visual accuracy.

### Whats the problem they are trying to solve?

Building 3D scene representation that both **semantically intelligent** and **capable of rendering in real time**.  

### Have they solved it?

They largely solved the addressed problem with **no degradation of visual quality**

- **Up to 2.7× faster** feature field distillation and feature rendering over NeRF-style methods.
- An interactive frame rate of **14.55 FPS** during semantic rendering, compared to NeRF-DFF's crawl of **5.38 FPS**.
- **1.7× faster** total inference and segmentation speed when leveraging interactive SAM prompts (by skipping the expensive step of rendering an RGB image first and passing it through SAM's full encoder-decoder).

Semantic Accuracy Gains

- They realized **up to a 23% improvement in mIoU** (mean Intersection-over-Union) for semantic segmentation.


### If yes, how, if not why?

This paper proposes **learning semantic features at each Gaussian through [[Concepts/Knowledge Distillation|Knowledge Distillation]]

![[zhouFeature3DGSSupercharging2024 2026-07-08 19.09.10.excalidraw.svg]]
%%[[zhouFeature3DGSSupercharging2024 2026-07-08 19.09.10.excalidraw.md|🖋 Edit in Excalidraw]]%%

These contributions are done through 4 major clusters

First, the core idea is **storing the semantic features into guassians**. this means This means every Gaussian now stores: position (x), rotation (q), scaling (s), opacity (α), color (c), and its semantic feature (f).

Second, the semantic features are distilled using [[Concepts/Knowledge Distillation|Knowledge Distillation]] into the Guassians using **The Parallel N-Dimensional Gaussian Rasterizer**. 
- Such like the 3DGS, the newly incorporated feature vector is also rasterized in the same pipeline and injected.

Third, the performance optimization is done using **Speed-up Module**

#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context.?

The works still has a **major dependency over the Teacher model** : It cannot exceed the semantic understanding capability of the 2D foundation model that training it. And whatever flaws present in the teacher model is still present in the distilled model (like **coarse boundaries**)

This method suffers from **loss of semantic understanding** addressed by [[zuoFMGSFoundationModel2024]], possibly due to the fact that **it initiated heavily compressed feature vector** that is not enough to capture entire information. 

Here's a snapshot from the ablation studies
![[Pasted image 20260708192641.png]]


## Questions & Answers

Q: What is **Radiance Field** in the 3D graphics jargon?
A: Pure visual and physical parameters of a scene used to render realistic images 

It's a mapping between the coordinates to the color and density information. 

$$L : X,Y,Z,\psi, \phi \rightarrow r,g,b,\sigma$$

Where, $L$ is the [[Radiance Field]] which is a **partial representation** of [[Palenoptic Function]].

**Essentially, [[3D Gaussian Splatting]] solves the problem of approximating the [[Palenoptic Function]]**
***
Q:  What is [[Palenoptic Function]]? 
A: 
The plenoptic function is a **theoretical mathematical model** that describes everything that can be seen from everywhere. Imagine an idealized eye capable of capturing every single ray of light in the universe, from every possible angle, at every moment, across every color.

![[Pasted image 20260706144408.png]]

The idea is this mathematical model  captures the idea of all the sources of light rays going through a certain point in time. For example, in above picture, there are 4 points, actually for two of them since they are inside the pinhole camera, only one straight path to light everything else is set to zero by default. For other two points, captures the full spectrum.

***
Q: What is [[Stereo Vision]]?
A: Extracting 3D **depth information** by comparing two or more views of the same scene
<!--ID: 1783519942787-->


- - -

## 🔗 Related to
- [[qinLangSplat3DLanguage2023]] takes an alternative approach to the same problem by focusing on [[SAM]](segmentation) and distilling information into the 3D splats.

