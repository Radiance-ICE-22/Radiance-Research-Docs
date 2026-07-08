---
title: "Feature 3DGS: Supercharging 3D Gaussian Splatting to Enable Distilled Feature Fields"
aliases:
  - "@zhouFeature3DGSSupercharging2024"
tags:
  - literature
  - slam
  - edge-computing
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@zhouFeature3DGSSupercharging2024`
> - **Authors:** Shijie Zhou, Haoran Chang, Sicheng Jiang, Zhiwen Fan, Zehao Zhu, Dejia Xu, Pradyumna Chari, Suya You, Zhangyang Wang, Achuta Kadambi
> - **Year:** 2024
> - **DOI/Link:** [10.48550/arXiv.2312.03203](https://doi.org/10.48550/arXiv.2312.03203)

## 📌 One-Sentence Summary
*What is the core contribution of this paper and how does it apply to our 3D Gaussian Splatting edge deployment architecture?*

---

## 📝 Reading Notes & Architecture

**Feature distillation from 2D Models** has two major limitations.
1. Limited by the rendering speed at training and inference **speed of NeRF** (at the time the paper)
2. **Continuity artifacts** rendering the feature quality 

### Whats the problem they are trying to solve?

### Have they solved it?

### If yes, how, if not why?

#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context.?

#### if no, what can you learn from it? can the lessons learnt be applied in any way? may be re-experiment with tuning/re-adjusting the methods/changing the parameters etc.


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
- - -

## 🔗 Related to
- [[qinLangSplat3DLanguage]] takes an alternative approach to the same problem by focusing on 

