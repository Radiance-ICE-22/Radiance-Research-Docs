---
title: "SOUS VIDE: Cooking Visual Drone Navigation Policies in a Gaussian Splatting Vacuum"
aliases:
  - "@lowSOUSVIDECooking2025"
tags:
  - literature
  - slam
  - edge-computing
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@lowSOUSVIDECooking2025`
> - **Authors:** JunEn Low, Maximilian Adang, Javier Yu, Keiko Nagami, Mac Schwager
> - **Year:** 2025
> - **DOI/Link:** [10.48550/arXiv.2412.16346](https://doi.org/10.48550/arXiv.2412.16346)

## 📌 One-Sentence Summary
A [[VisuoMotor]] policy creation platform which uses 3DGS as a simulation environment to close the gap between sim-to-real.

---

## 📝 Reading Notes & Architecture


### Whats the problem they are trying to solve?
Creating a zero shot [[VisuoMotor]] using human like agility and collision avoidance require large amount of visual and state data, making  behavior cloning from human pilot demonstration impractical. Most of the existed simulation had a gap in sim-to-real world. Using 3DGS, they have proposed a better simulation.
### Have they solved it?
Yes.
### If yes, how, if not why?
Their simulator FIGS couples a computationally simple drone dynamics model with a 3DGS model. FIGS can simulate drone flights producing photorealistic images at up to 130fps. They use FIGS to generate 100k-300k image/state-action pairs with randomized over dynamic parameters and spatial disturbances (Like wind pushing the drone.)  which is taken from a expert MPC.
Then they distill this expert MPC into an end-to-end visuomotor policy with a lightweight neural architecture which is called SV-Net.
![[Pasted image 20260707111758.png]]
#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context?
- Multi objective task gives inconsistent performances. They are suggesting that  sophisticated objective encodings could solve the problem.
- Currently the platform is for single real life environment. Future works can be done to include multiple environments in FiGS to enable generalist skills such as general collision avoidance and scene agnostic navigation.
- They has a plan to augmenting the simulation policies with semantic goal understanding. Such that the goals can be given by a human operator in the form of natural language commands.


## Questions & Answers

Q: 
A: 
- - -

## 🔗 Related to
- This project could utilize semantic understanding frameworks like [[SAM]], could be worthwhile to check into [[zhouFeature3DGSSupercharging2024]] or relevant if it's feasible.