# LangSplat: 3D Language Gaussian Splatting

**Authors:** Minghan Qin, Wanhua Li, Jiawei Zhou, Haoqian Wang, Hanspeter Pfister
**Affiliations:** Tsinghua University, Harvard University
**Venue:** CVPR 2024 Highlight

> [!url] Links
> Paper: https://arxiv.org/abs/2312.16084
> Project: https://langsplat.github.io
> Code: https://github.com/minghanqin/LangSplat
> Follow-ups: [LangSplat V2](https://langsplat-v2.github.io/) · [4D LangSplat](https://4d-langsplat.github.io/)

---

## Core Idea

> [!abstract] What LangSplat Does
> LangSplat attaches **CLIP language feature vectors** to each Gaussian primitive in a 3DGS scene, creating a queryable **3D language field**. Given any natural-language query ("the red mug", "something to sit on"), the system returns precise 3D locations and masks directly from the Gaussian representation — with no NeRF ray marching. It is **199x faster** than the previous state-of-the-art (LERF) at 1440×1080 resolution.

---

## What Was Achieved

### Core Pipeline

> [!info] Five-Step Training Process
> **Step 1 — Base 3DGS**
> Train a standard 3DGS scene from a set of posed images. This produces the geometry and appearance foundation.
>
> **Step 2 — Hierarchical SAM Mask Generation**
> For every training image, run SAM (Segment Anything Model) at three granularity levels (object, part, sub-part) to produce a set of precise 2D instance masks. This replaces the expensive multi-scale querying used by LERF.
>
> **Step 3 — CLIP Feature Extraction per Region**
> For each SAM mask, crop the image region and extract a CLIP embedding. Each pixel is assigned the embedding of the smallest SAM region that contains it, at each of the three scales.
>
> **Step 4 — Scene-Specific Language Autoencoder**
> Train a small autoencoder on the collected CLIP features for this scene. The encoder compresses 512-dim CLIP vectors to a low-dimensional latent (e.g. 3-dim). This drastically reduces the per-Gaussian memory footprint compared to storing raw CLIP embeddings.
>
> **Step 5 — Per-Gaussian Language Feature Training**
> Add a learnable low-dimensional language feature vector to each Gaussian. Train these features via tile-based splatting: render the latent features to each training view and minimise the loss against the projected CLIP latents from Step 4.

> [!tip] Why the Autoencoder Is Critical
> CLIP embeddings are 512-dimensional. Storing one per Gaussian on a scene with hundreds of thousands of primitives is prohibitive. The scene-specific autoencoder compresses to 3 dimensions — a 170x reduction — while preserving the discriminability of features within that particular scene. This is what makes the memory cost tractable.

### Querying the Language Field

> [!info] Open-Vocabulary Query at Runtime
> 1. Encode the text query with CLIP: `f_text = CLIP_encode("the red mug")`
> 2. Decode every Gaussian's latent feature back to CLIP space via the learned decoder
> 3. Compute cosine relevancy score between each Gaussian's decoded feature and `f_text`
> 4. Threshold scores to produce a 3D relevancy map or segmentation mask
> 5. Render the relevancy map via tile-based splatting for 2D visualisation

### Hierarchical Semantics

> [!tip] Why SAM Fixes the Blurry Boundary Problem
> LERF queries the language field at multiple scales at render time and uses DINO regularisation to get crisp boundaries — expensive and still imprecise. LangSplat moves the multi-scale reasoning to training time: SAM provides three levels of precise, instance-level masks that give each Gaussian a clean, boundary-respecting semantic label from the start. No post-processing or test-time scale search needed.

### Quantitative Results

> [!done] Performance vs. LERF
> - [f] **199x faster** than LERF at 1440×1080 resolution
> - [w] Significantly higher open-vocabulary 3D object localisation accuracy (IoU)
> - [w] Sharper and more precise 3D semantic segmentation boundaries
> - [w] Language features already exhibit precise object boundaries before any text query is issued
> - [i] Evaluated on open-vocabulary 3D object localisation and 3D semantic segmentation benchmarks

---

## Novelty

- [k] **Per-Gaussian language features** — the first method to attach CLIP embeddings directly to 3D Gaussians, enabling tile-based rendering of language fields at the same speed as colour rendering
- [k] **Scene-specific language autoencoder** — compresses CLIP from 512-dim to ~3-dim per scene, making per-Gaussian storage feasible without losing inter-object discriminability
- [k] **SAM-driven hierarchical semantics** — replaces expensive test-time multi-scale querying with train-time multi-scale mask generation, producing clean boundaries without DINO regularisation
- [k] **Unified rendering pipeline** — language features are splatted through exactly the same tile-based rasteriser as colour; no additional rendering infrastructure needed

---

## Limitations

> [!warning] Known Constraints
> - [c] **Offline only** — requires a complete, static multi-view capture and a full 3DGS training pass before language features can be trained. Not applicable to streaming or online mapping scenarios without extension.
> - [c] **Scene-specific autoencoder** — the autoencoder is retrained per scene and encodes only the vocabulary of objects present in that scene. Querying for objects not in the training set degrades gracefully but not perfectly.
> - [c] **Static scenes** — the base paper handles only static environments. 4D LangSplat (follow-up) extends to dynamic scenes.
> - [c] **Depends on SAM quality** — poor SAM masks (thin objects, highly reflective surfaces, cluttered backgrounds) produce noisy language features. Outdoor drone scenes with sky, vegetation, and motion blur are challenging for SAM.
> - [c] **No online update** — adding new objects or updating existing labels requires retraining from scratch or fine-tuning the language features over new keyframes.
> - [c] **CLIP vocabulary limits** — queries outside CLIP's training distribution (rare objects, domain-specific terminology) produce unreliable relevancy scores.

---

## Future Improvements

> [!question] Open Directions

- [I] **Online / streaming language fields** — incrementally training per-Gaussian language features as new keyframes arrive, without full retraining (directly relevant to the drone mapping pipeline)
- [I] **Universal autoencoder** — a single pretrained autoencoder that generalises across scenes, removing the need for per-scene retraining
- [I] **LLM-grounded queries** — connecting LangSplat to an LLM for compositional queries ("find me a seat near a window that is not occupied")
- [I] **Active querying for exploration** — using language relevancy scores from a partial map to guide a drone's next-best-view: "fly toward regions where the query score for 'landing zone' is high but uncertain"
- [I] **Multi-agent language map sharing** — sharing language-annotated Gaussian submaps between drones so one agent's query resolution improves other agents' maps

---

## Relation to Research Direction

> [!note] Connection to Drone Navigation and SLAM
> LangSplat is the **semantic layer** that converts a 3DGS map from a purely geometric/appearance structure into a **queryable spatial knowledge base**. This is highly relevant to the project for three reasons:
>
> - [!] **Natural language obstacle avoidance** — a drone can query "where are people / vehicles / water bodies?" in real time and get precise 3D masks without a separate object detector
> - [!] **Semantic initialisation hook** — the SAM + CLIP pipeline LangSplat uses to label training regions is essentially the same mechanism a human-in-the-loop could use to annotate reflective or repeating regions before 3DGS training begins. The annotation tool is already built.
> - [k] **Foundation for the streaming semantic map** — S2GS and OnlinePG (referenced in the feasibility document) both build directly on LangSplat's per-Gaussian feature vector pattern, adapting it to online/streaming settings. LangSplat is the offline baseline to beat.
>
> The critical gap for the project: LangSplat is offline. Making it work incrementally in a streaming 3DGS mapping pipeline — particularly with the universal autoencoder needed to avoid per-scene retraining — is an open problem and a potential research contribution.

---

## Glossary

> [!info] CLIP (Contrastive Language-Image Pretraining)
> A foundation model trained on 400 million image-text pairs that maps both images and text into a shared embedding space. Cosine similarity between a text embedding and an image region embedding measures semantic relevance — the backbone of open-vocabulary recognition.

> [!info] LERF (Language Embedded Radiance Fields)
> The predecessor to LangSplat. Embeds CLIP features into a NeRF by distilling language information into the implicit neural field. Requires expensive ray marching and multi-scale test-time querying, making it ~199x slower than LangSplat at high resolution.

> [!info] SAM (Segment Anything Model)
> Meta's large-scale segmentation model that produces precise instance masks for any object in an image given a prompt (point, box, or automatic). LangSplat uses it to generate three levels of granularity masks per training image, providing clean semantic boundaries without manual annotation.

> [!info] Open-Vocabulary Querying
> The ability to retrieve objects by arbitrary natural-language descriptions not seen during training, as opposed to closed-set classifiers that only recognise a fixed category list. Enabled by CLIP's zero-shot generalisation.

> [!info] Relevancy Score
> The cosine similarity between a text query's CLIP embedding and a Gaussian's decoded language feature. High scores indicate the Gaussian likely belongs to the queried object. Thresholding scores produces a 3D object mask.

> [!info] Language Autoencoder
> In LangSplat, a small MLP encoder-decoder trained on the CLIP features present in a specific scene. Compresses 512-dim CLIP vectors to a scene-specific latent (~3-dim) that preserves inter-object distances within that scene. Enables per-Gaussian storage of language information at tractable memory cost.

> [!info] 4D LangSplat
> The dynamic extension of LangSplat that handles moving objects and changing scenes, built on a 4D (space + time) Gaussian representation. Relevant for drone mapping of non-static environments.

---

## Tags

`#gaussian-splatting` `#language-field` `#CLIP` `#SAM` `#open-vocabulary` `#semantic-mapping` `#CVPR2024` `#3DGS` `#drone-navigation` `#scene-understanding`