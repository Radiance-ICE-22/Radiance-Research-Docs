---
title: "FMGS: Foundation Model Embedded 3D Gaussian Splatting for Holistic 3D Scene Understanding"
aliases:
  - "@zuoFMGSFoundationModel2024"
tags:
  - 3d-representation
  - foundation-models
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@zuoFMGSFoundationModel2024`
> - **Authors:** Xingxing Zuo, Pouya Samangouei, Yunwen Zhou, Yan Di, Mingyang Li
> - **Year:** 2024
> - **DOI/Link:** [10.48550/arXiv.2401.01970](https://doi.org/10.48550/arXiv.2401.01970)

## 📌 One-Sentence Summary

The idea is to distill the information from [[Foundation Models]](2D) like [[DINO]] and [[CLIP]] and embed the information into splat using efficient methods like [[Multi-resolution Hash Encoding]].

---

## 📝 Reading Notes & Architecture

![[Pasted image 20260626080650.png]]

Here, the geometric aspects and languages aspects are separated by [[Multi-resolution Hash Encoding]] these spatial aspects are queries through MHS and fed into a **MLP(multi layer perceptron)** to extract language meaning.

![[Pasted image 20260626084419.png]]


### Whats the problem they are trying to solve?
Making the representation and the reconstruction of 3D vision language models. Particularly these problems encountered  for 3DGS approaches.

- **Excessive memory consumption**
This is one of the limitation with [[qinLangSplat3DLanguage2023|@qinLangSplat3DLanguage]], and other 3DGS based approaches since by nature the approach is notorious in terms of memory consumption. (also GPU usage)
- **Multi-view inconsistency**
Notably highlighted issue. **Addressed that [[CLIP]] embeddings are not multi-view consistent**. Thus, for 3D representation,  there's a need to minimize the [[CLIP]] embeddings across the moving views because different view of the same object has to result in similar embeddings.
- **Pixel misalignment problem**: [[CLIP]] is not designed to be fine grained, thus existing methods utilizing [[CLIP]] struggle to detect sharp objects.

### Have they solved it?
Yes

>[!quote]
>Our results demonstrate remarkable multi-view semantic consistency, facilitating diverse downstream tasks, beating state of the-art methods by **10.2 percent** on open-vocabulary language-based object detection, despite that we are **851×** faster for inference.

### If yes, how, if not why?

For excessive memory consumption, the straightforward approach which is **attaching a learnable feature vector** tends to be causing massive memory consumption because each render contains millions of guassians. Utilizing [[Multi-resolution Hash Encoding]] significantly reduces the number of parameters to be optimized. 

The multi-view inconsistency is addressed mainly through [[Multi-view Training  Process]], specially utlizing [[Multi-resolution Hash Encoding]] over raw 2D points, the embeddings are forced to remain invariant.  

Pixel alignment loss is addressed by [[DINO]] feature maps.

#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context?

**Limitations :** FMGS depends on high quality calibrated input images and its **performance is heavily subjected to the underlying foundation model being used**  

**Improvements**: To enhance the performance of the segmentation tasks, it's advised to use embedded a image segmentation foundation model like [[SAM]] that would help to overcome the original challenges presented above [[#Whats the problem they are trying to solve?]]


## Questions & Answers

Q: Explain FMGS (Foundation Model embedded Gaussian Splatting)
A: 
The idea is to distill the information from [[Foundation Models]](2D) like [[DINO]] and [[CLIP]] and embed the information into splat using efficient methods like [[Multi-resolution Hash Encoding]].

![[Pasted image 20260626080650.png]]

The key challenges it tackle are

- Excessive memory consumption
- Multi-view inconsistency
- Pixel misalignment problem
- 
The way it's done

For excessive memory consumption, the straightforward approach which is **attaching a learnable feature vector** tends to be causing massive memory consumption because each render contains millions of guassians. Utilizing [[Multi-resolution Hash Encoding]] significantly reduces the number of parameters to be optimized. 

The multi-view inconsistency is addressed mainly through [[Multi-view Training  Process]], specially utlizing [[Multi-resolution Hash Encoding]] over raw 2D points, the embeddings are forced to remain invariant.  

Q: What's the difference between FMGS and LangSplat?
A:
Memory efficiency;

**LangSplat : Auto-encoders**  - It compresses [[CLIP]] features into a 3D latent representation and it's attached to every Gaussian as a learnable vector.
**FMGS: Multi-resolution Hash Encoding** - 3D mean position of Gaussian is used to query the MHE grid. and language aspects are handled by MLP. This preserves the original capabilities of foundation models.

Precise object boundaries

**LangSplat:** - Segmentation is done through [[SAM]] and a hierarchical [[CLIP]] is incorporated for object boundaries. 
**FMGS:** - [[DINO]] feature regularization applied over extracted [[CLIP]] features. This does not use any foundation model for segmentation (which is a possible enhancement) 



- - -

## 🔗 Related to
- [[qinLangSplat3DLanguage2023|@qinLangSplat3DLanguage]]