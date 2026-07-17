---
title: "SOUS VIDE: Cooking Visual Drone Navigation Policies in a Gaussian Splatting Vacuum"
aliases:
  - "@lowSOUSVIDECooking2025"
tags:
draft: false
---

> [!info] Paper Metadata
> - **Citekey:** `@lowSOUSVIDECooking2025`
> - **Authors:** JunEn Low, Maximilian Adang, Javier Yu, Keiko Nagami, Mac Schwager
> - **Year:** 2025
> - **DOI/Link:** [10.48550/arXiv.2412.16346](https://doi.org/10.48550/arXiv.2412.16346)

## 📌 One-Sentence Summary
A [[VisuoMotor]] policy creation platform which uses 3DGS as a simulation environment to close the gap between sim-to-real with minimal human curation.

---

## 📝 Reading Notes & Architecture


### Whats the problem they are trying to solve?
Creating a zero shot [[VisuoMotor]] using human like agility and collision avoidance **require large amount of visual and state data**, making  behavior cloning from human pilot demonstration impractical. 

Most of the existed simulation based policy generations had a **gap in sim-to-real world**. Using 3DGS, they have proposed a better simulation to **train policies for navigation with minimal human curation**.
### Have they solved it?
Yes.

Their solution allows making a zero-shot sim-to-real transfer using video clips with only minimal tuning of easily measurable parameters.

### If yes, how, if not why?
First core idea is creating the expert dataset (privileged data : under very controlled simulation environment) using their simulator **FIGS(Flying in Gaussian Splats)** couples a computationally simple drone dynamics model with a [[3DGS]] model. FIGS can simulate drone flights producing photo-realistic images at up to 130fps. They use FIGS to generate 100k-300k image/state-action pairs with randomized over dynamic parameters and spatial disturbances (Like wind pushing the drone.)  which is taken from a expert MPC.


Then they distill this expert MPC into an end-to-end visuomotor policy with a lightweight neural architecture which is called SV-Net.


To summarize,
1) **Flying in Gaussian Splats (FiGS):** A simulator coupling GSplat scene models with drone dynamics for efficient and photorealistic visual-inertial data generation.
2) **Scalable Visuomotor Policy Generation:** We use FiGS  to generate large synthetic datasets to train visuomotor policies that transfer zero-shot to real-world flight.
3) **SV-Net:** An onboard policy that fuses image and observable states to infer thrust and body rates while continuously adapting to changing flight conditions.
![[Pasted image 20260707111758.png]]
#### if yes, can it be further improved, OR can you use the same solution in different contexts , or in applications of the same context?
- Multi objective task gives inconsistent performances. They are suggesting that  sophisticated objective encodings could solve the problem.
- Currently the platform is for single real life environment. Future works can be done to include multiple environments in FiGS to enable generalist skills such as general collision avoidance and scene agnostic navigation.
- They has a plan to augmenting the simulation policies with semantic goal understanding. Such that the goals can be given by a human operator in the form of natural language commands.


## Questions & Answers

Q: What is [[VisuoMotor]]?
A: VisuoMotor policy is a end to end neural network that translates the **visual data**(image) into **low-level motor commands** 
- **Visuo** : Visual inputs (images or lidar data)
- **Motor**: Physical actuation commands
![[Pasted image 20260717161536.png]]
***

Q: What is [[MPC(Model Predictive Control)]]
A: 
It's a type of **feedback controller** works with **optimization** at heart. It optimizes the control sequences for a **short time window** at each step in the process. Thus acting as a **fully online control system** which takes a massive computational power.

![[Pasted image 20260717162133.png]]

***
Q: What is [[RMA(Rapid Motor Adaptation)]]?
A: 
A pre-trained alternative to **online parameter estimation** to handle uncertainty. 
The idea is to train an **encoder** that takes in a **sensing history and produces a latent vector that captures runtime operation conditions**. This is known for having impressive robustness.

![[Pasted image 20260717175129.png]]
- - -

## 🔗 Related to
- This project could utilize semantic understanding frameworks like [[SAM]], could be worthwhile to check into [[zhouFeature3DGSSupercharging2024]] or relevant if it's feasible.