---
title: "LangSplat: 3D Language Gaussian Splatting"
aliases:
  - "@qinLangSplat3DLanguage"
tags:
  - 3d-representation
draft: true
---

> [!info] Paper Metadata
> - **Citekey:** `@qinLangSplat3DLanguage`
> - **Authors:** Minghan Qin, Wanhua Li, Jiawei Zhou, Haoqian Wang, Hanspeter Pfister
> - **Year:** 2023
> - **DOI/Link:** [10.48550/arXiv.2312.16084](https://doi.org/10.48550/arXiv.2312.16084)

## 📌 One-Sentence Summary
*What is the core contribution of this paper and how does it apply to our 3D Gaussian Splatting edge deployment architecture?*

---

## 📝 Reading Notes & Architecture

![[Pasted image 20260624184125.png]]

> **Multi-view inconsistency** across the rendering is addressed through **[[Multi-view Training  Process]]**. 

### Whats the problem they are trying to solve?

The paper focuses on creating a *3D language field* that allows users to interact with 3D scenes suing **open ended natural language**

Mainly two issues they wanted overcome that are present in the existing methods
1. **Speed** 
- Existing methods lack large 3D annotated datasets, thus going for [[Knowledge Distillation]] from [[Embedding Based Model]] like [[CLIP]]. Plus the 3D modeling is done by [[NeRF]] which extremely time consuming. 

1. **Point ambiguity problem** (imprecise image bounding)
- [[CLIP]]  is **image aligned** ( learns image level semantics) due to that the segmentation result  in imprecisions even when cropped segments are used for training process. Even when we used DINO like models for learned 3D representations, existing models shown imprecisions. 

![[Pasted image 20260624172548.png]]

### Have they solved it?

Yes, astonishingly.  

![[Pasted image 20260624173401.png]]
This sets a new standard in every details. Completely crushing the [[LERF]]. 

>[!quote]
>Notably, LangSplat is extremely efficient, achieving a 199 × speedup compared to LERF at the resolution of 1440 × 1080. We strongly recommend readers to check out our video

### If yes, how, if not why?

The rendering speed is fixed mainly by swapping [[3D Gaussian Splatting]] for [[NeRF]] for the 3D modelling and representation.

They utilized [[SAM]](segment anything model) to segment by patches which eliminates **point ambiguity problem** and imprecise bounding problem. For this to happen, these patches are integrated with [[CLIP]]

>[!warning]
>But this method introduced a new problem **memory constraint** since [[3D Gaussian Splatting]] is notorious when it comes to memory usage.

#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context.?

[[liLangSplatV2Highdimensional3D2025| LangSplatV2]] is released to further improve the original existing pipeline.



## Questions & Answers

Q:  What is a latent space 
A: Latent space is a conceptual map, the lower level of the knowledge representation where the similar things are close by (proximity). Very widely used in generative applications, like generative AI where knowledge synthesis happens.

**A Simple Analogy**

Imagine trying to describe a book.

The **observation space** is the exact sequence of all 100,000 words in the book. If you change a single word, it's technically a different data point.

The **latent space** is describing the book using a few sliders: "Romance (1-10)," "Sci-Fi (1-10)," "Action (1-10)," and "Complexity (1-10)." You have compressed 100,000 words into just four numbers. If two books are close together in this 4D space, they have similar themes, even if their exact words are completely different.
<!--ID: 1780225736401-->

Q: Explain [[qinLangSplat3DLanguage2023|LangSplat]] research work 
A:
It focuses on creating a *3D language field* that allows users to interact with 3D scenes suing **open ended natural language**

Mainly two issues they wanted overcome that are present in the existing methods
1. **Speed** 

2. **Point ambiguity problem** (imprecise image bounding)

the way they approach it is with, below mentioned approaches

![[Pasted image 20260624184125.png]]

The rendering speed is fixed mainly by swapping [[3D Gaussian Splatting]] for [[NeRF]] for the 3D modelling and representation.

They utilized [[SAM]](segment anything model) to segment by patches which eliminates **point ambiguity problem** and imprecise bounding problem. For this to happen, these patches are integrated with [[CLIP]]



- - -

## 🔗 Related to
- [[liLangSplatV2Highdimensional3D2025]]