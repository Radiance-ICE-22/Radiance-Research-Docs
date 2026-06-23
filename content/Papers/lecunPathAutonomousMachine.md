---
title: A Path Towards Autonomous Machine Intelligence Version 0.9.2, 2022-06-27
aliases:
  - "@lecunPathAutonomousMachine"
tags:
  - literature
draft: true
---

> [!info] Paper Metadata
> - **Citekey:** `@lecunPathAutonomousMachine`
> - **Authors:** Yann LeCun
> - **Year:** Error: `format` can only be applied to dates. Tried for format object
> - **DOI/Link:** [](https://doi.org/)

## 📌 One-Sentence Summary
This positional paper discusses about the ways in which the future of AI takes turns and overcome the traditional limitations of generative AI.

---

## 📝 Reading Notes & Architecture

Deep learning is notoriously data hungry, and the way animals and humans learn is **not how generative AI tends to learn it seems**.

The papers promises to address these 4 gaps seen in current state of AI*

![[Drawing 2026-06-01 16.34.15.excalidraw.svg]]
%%[[Drawing 2026-06-01 16.34.15.excalidraw.md|🖋 Edit in Excalidraw]]%%

>[!note] The focus of this paper is to bring **self-supervised learning** with **world models** and **intrinsic motivation**.
>- **Self-supervised learning** : The same way LLMs were able to overcome the training bottleneck.
>- **World Models** : The model of the physical world that the model can experiment with, and predict the consequences of its actions.
>- **Intrinsic Motivation**: The ability for an intelligent to drive its own exploration (maybe through multiple abstraction levels)

The way JEPA addresses this
1. An architecture where all the modules are differentiable and most of them are trainable.
2. **Non-Generative** architecture for predictive world models that learn a **hierarchical representation**.
3. **Non-Contrastive**  self supervised learning.
4. H-JEPA to be used as a basis for predictive world models to work under uncertainty. 

Here's a world model exhibiting the nature of how an animal might learn

![[lecunPathAutonomousMachine 2026-06-02 11.40.25.excalidraw.svg]]
%%[[lecunPathAutonomousMachine 2026-06-02 11.40.25.excalidraw.md|🖋 Edit in Excalidraw]]%%

When we say that humans learn in **layers of abstraction** where the concept of higher levels seem to develop on top of concept of low levels. 

![[lecunPathAutonomousMachine 2026-06-02 11.46.52.excalidraw.svg]]
%%[[lecunPathAutonomousMachine 2026-06-02 11.46.52.excalidraw.md|🖋 Edit in Excalidraw]]%%

This comes from the *courtesy of Emmanuel Dupoux*
![[Pasted image 20260602140759.png]]

## Questions & Answers

Q: What's the reason behind JEPA?
A: Generative AI relies on massive amount of data and yet still not capable of predicting the consequences of its own action. JEPA explores the ways animals and humans learns mainly having a **world model** to simulate and interact with. Plus, LLMs and main stream AI works by gradient loss which can only work on differentiable models.

![[lecunPathAutonomousMachine 2026-06-02 11.40.25.excalidraw.svg]]

JEPA architecture is created in order to have agents that can think and behave like humans. It's a way of training (framework) training the models in a way to match how humans learn.
<!--ID: 1780390148605-->


- - -

## 🔗 Related to
- [[Project Architecture Master Note]]