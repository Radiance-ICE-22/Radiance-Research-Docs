---
title: "VINS-Mono: A Robust and Versatile Monocular Visual-Inertial State Estimator"
aliases:
  - "@qinVINSMonoRobustVersatile2018"
tags:
  - literature
  - slam
  - edge-computing
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@qinVINSMonoRobustVersatile2018`
> - **Authors:** Tong Qin, Peiliang Li, Shaojie Shen
> - **Year:** 2018
> - **DOI/Link:** [10.1109/TRO.2018.2853729](https://doi.org/10.1109/TRO.2018.2853729)

## 📌 One-Sentence Summary
*What is the core contribution of this paper and how does it apply to our 3D Gaussian Splatting edge deployment architecture?*

---

## 📝 Reading Notes & Architecture



## Questions & Answers

Q: What is the limitations of **monocular cameras** that limits the use of them in real world robotics and navigation applications?

A: They don't have **metric scale**, meaning **it's fundamentally incapable of determining real world physical distances**

They track movements and maps the environments by *how features change between frames*. They don't know whether they are focusing on proximal object or a far away one. 
<!--ID: 1776131179616-->
- - -

Q: How does VINS(Visual INertial System) overcomes the problem that traditional monocular cameras have in term of robotics navigation and applications? 

A: VINS integrates **low cost IMU**, uses *acceleration excitation* as a metric, and fuses it with the visual information to accurately estimate the physical distances

Thus making it **observable**.
<!--ID: 1776445613612-->

- - -

Q:Why we might need **VINS-MONO((Visual INertial System monocular camera)** to reduce the bottleneck of initial training bottleneck?
A: The major bottleneck is creating the SFM via Colmap or something, one approach is using SLAM process to entirely bypass the training phase. But SLAM is not that accurate, thus a robust estimation is required to accurately re-create the 3d representation. VINS-MONO is known to be very accurate since it uses sensor fusion with Monocular camera data to create highly accurate baseline for estimation.
<!--ID: 1776445613621-->


- - -

## 🔗 Related to
- [[Project Architecture Master Note]]