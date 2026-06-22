# Splat and Replace: 3D Reconstruction with Repetitive Elements

**Authors:** Nicolás Violante, Andreas Meuleman, Alban Gauthier, Fredo Durand, Thibault Groueix, George Drettakis
**Affiliations:** Inria & Université Côte d'Azur, Adobe, MIT
**Venue:** SIGGRAPH 2025

> [!url] Links
> Paper: https://arxiv.org/pdf/2506.06462v1
> Code: https://repo-sam.inria.fr/nerphys/splat-and-replace

---

## Core Idea

> [!abstract] Key Insight
> Standard 3DGS reconstructions degrade in occluded or poorly captured regions because each object is optimised only from the views that directly see it. Real environments are **full of repetitive elements** — chairs, windows, pillars. Every instance of the same object is essentially the same geometry seen under slightly different lighting. By fusing all instances into a single shared Gaussian template, every instance donates information to every other, filling in occluded and low-coverage regions across the board.

---

## What Was Achieved

### Stage 1 — 3D Instance Segmentation

> [!info] Segmentation Pipeline
> A per-Gaussian feature vector is added to the scene and trained with **contrastive learning**, using 2D masks from GroundingDINO + SAM-HQ. User effort is minimal: one text query (e.g. "chair") and one click per instance in a single training view.
>
> Key improvements over prior contrastive 3DGS segmentation:
> - L1 regularisation on Gaussian **opacity** and **scale** discourages large, low-opacity Gaussians that straddle object boundaries and receive conflicting gradients
> - A **space-carving post-processing** pass removes residual Gaussians left at instance borders after contrastive training

> [!attention] Why Standard Contrastive Segmentation Fails on 3DGS
> Three specific failure modes identified: (1) low-opacity sub-surface Gaussians receive too little gradient because they are occluded; (2) large cross-boundary Gaussians receive conflicting signals; (3) border-straddling Gaussians are not assigned to any instance and persist as artefacts. All three are addressed by the opacity/scale regularisation and space-carving combination.

### Stage 2 — Registration via Novel View Synthesis

> [!info] Why 3D Point-Cloud Registration Fails Here
> Gaussian primitives are not constrained to lie on geometric surfaces. Density varies strongly with capture coverage. FPFH on raw Gaussians fails with **50° mean angular error**. The solution is to use the radiance field itself.

> [!tip] NVS-Assisted Registration Pipeline
> 1. Render **25 synthetic views** of each instance from a virtual sphere (5 azimuths x 5 elevations)
> 2. Fast-match all view pairs with XFeat; shortlist top **10 candidate pairs**
> 3. Run **MASt3R** (transformer-based dense matcher) on the 10 candidates
> 4. Lift 2D matches to 3D using depth rendered by 3DGS
> 5. Solve with **PnP-RANSAC** for the rigid instance-to-template transformation
> 6. Refine coarse pose with ICP, then jointly during fine-tuning

### Stage 3 — Shared Representation and Optimisation

> [!info] Shared Template
> All instances are transformed into the template coordinate frame and their Gaussians are **unioned** into a single shared representation. All geometric parameters (position, scale, rotation, opacity) are shared across instances.
>
> Appearance variation from per-instance illumination is modelled by decomposing each Gaussian's SH coefficients:
>
> **c = 0.8 × c_shared + 0.2 × c_offset**
>
> An L1 penalty on c_offset keeps it small, ensuring the shared component carries the dominant signal. During fine-tuning, gradients flow from every instance's training views back through the shared template.

> [!done] Quantitative Results
> **Synthetic Scenes (vs. 3DGS\*):**
> - [w] PSNR: **27.62** vs. 24.41 (+3.2 dB)
> - [w] SSIM: **0.897** vs. 0.843
> - [w] LPIPS: **0.163** vs. 0.236
> - [f] KID improves **4x** on synthetic, **2x** on real scenes
>
> **Real Scenes (vs. 3DGS\*):**
> - [w] PSNR: **+1.59 dB** overall, **+2.72 dB** in masked (instance) regions
> - [w] Average **+1.28 dB** PSNR in masked regions on ScanNet++ / DL3DV
>
> **Registration (vs. FPFH on raw Gaussians):**
> - [w] Mean angular error: **0.49°** vs. **50.58°**
>
> **Computational Cost:**
> - [i] Base 3DGS: 22 min | Contrastive features: 21 min (reducible to 4 min) | Segmentation: 19 sec | Registration: 2 min | Fine-tuning: 51 min (reducible to 29 min)

---

## Novelty

Key technical contributions:

- [k] **Repetitions as a reconstruction prior** — uses multi-view information already latent in the scene; requires no external generative model or per-class priors. Nerfbusters and Bayes' Rays can only remove floaters, not fill missing geometry.
- [k] **NVS-assisted instance registration** — rendering virtual views cleanly separates the geometric ambiguity of Gaussians from the matching problem; the first method to use the radiance field itself as a pre-processing step for 3D registration.
- [k] **SH offset decomposition** — factorises SH coefficients into a shared base and a lightweight per-instance offset. Faster than a per-instance MLP (1.5x speedup) with equivalent PSNR, and explicitly disentangles shared geometry from illumination-specific appearance.
- [k] **Segmentation improvements for 3DGS** — opacity/scale L1 regularisation plus space carving achieves clean instance boundaries without manual cleanup, addressing three specific failure modes of contrastive 3DGS segmentation.
- [k] **Zoom-in generalisation** — close-up details from one instance propagate through the shared template to a distant instance, enabling accurate close-up renders of objects never directly seen up-close in training.

---

## Limitations

> [!warning] Known Constraints
> - [c] **Background not improved** — the method only refines shared instances; everything outside remains standard 3DGS
> - [c] **Requires user interaction** — one text query and one click per instance; fully automatic repetition detection is left for future work
> - [c] **Strong illumination variation** — pronounced specular highlights produce appearance differences between instances that the SH offset model cannot fully absorb
> - [c] **Repetition count and diversity matters** — benefit is greatest when instances are numerous and collectively provide wide angular coverage; two instances from the same viewpoint offer marginal improvement
> - [c] **Symmetric and textureless objects** — multiple valid rigid transforms exist; the method finds one valid alignment, sufficient for fine-tuning but correspondence is appearance-driven rather than geometry-grounded
> - [c] **Additional training time** — full pipeline adds ~70 min on top of base 3DGS (reducible to ~33 min with minimal quality loss)

---

## Future Improvements

> [!question] Open Directions

- [I] **Memory-efficient shared representation** — if N instances share one compact template, Gaussian count scales sub-linearly; key challenge is compact per-instance appearance encoding
- [I] **Shared BRDF for inverse rendering** — extend shared parameters to BRDF attributes (roughness, albedo) for multi-illumination constraints and better material/lighting disentanglement
- [I] **Automatic instance detection** — replace user click with an automatic detector (e.g. from GroundingDINO outputs) for a fully unsupervised pipeline
- [I] **Stronger appearance handling** — replace SH offset with a lightweight scene-specific network or diffusion-based appearance model to handle stronger specular variations
- [I] **Faster base reconstruction** — Taming 3DGS explicitly cited as a way to reduce contrastive training and fine-tuning cost via a more compact initial Gaussian set
- [I] **Dynamic scenes** — repeated moving elements (rearranged chairs, similar human poses) break the rigid template assumption; an open problem
- [I] **Integration with human-in-the-loop initialisation** — segmentation and shared-template ideas are directly applicable to pipelines where users select high-confidence seed regions to guide optimisation

---

## Relation to Research Direction

> [!note] Connection to Human-in-the-Loop 3DGS
> Highly relevant to the human-in-the-loop 3DGS initialisation direction. The core insight — that high-quality Gaussian subsets can seed and improve lower-quality ones — parallels using user-selected confident regions as initialisation seeds. The contrastive segmentation pipeline, space-carving post-processing, and NVS-assisted registration are directly reusable components. The SH offset decomposition is also a clean model for handling appearance variation without the cost of per-region MLPs.

---

## Glossary of Key 3DGS Terms

> [!info] 3D Gaussian Splatting (3DGS)
> A scene representation that models the radiance field as a set of 3D Gaussian primitives, each defined by a position (mean), a covariance (encoded as scale and rotation), an opacity, and view-dependent colour via SH coefficients. Rendered by projecting and alpha-compositing Gaussians onto the image plane at real-time frame rates.

> [!info] Gaussian Primitive
> A single 3D Gaussian in the scene, characterised by its centre, ellipsoidal shape (3D covariance), opacity, and per-degree SH colour coefficients.

> [!info] Spherical Harmonics (SH)
> Orthogonal basis functions on the sphere used to represent view-dependent colour per Gaussian. Degree 0 is constant (fully diffuse); degrees 1–3 add increasing angular frequency and capture directionality and mild specularity.

> [!info] SH Offset
> A per-instance additive correction to the shared SH coefficients, modelling the illumination difference between a given instance and the template. Penalised with L1 to remain small.

> [!info] Densification
> The 3DGS training procedure that splits or clones Gaussians in under-reconstructed regions (high image gradient error) or over-large Gaussians. Controls total Gaussian count over training.

> [!info] Opacity and Scale Regularisation
> L1 penalties on per-Gaussian opacity and scale, discouraging semi-transparent or oversized Gaussians that straddle object boundaries and receive conflicting gradients during contrastive segmentation training.

> [!info] Contrastive Learning (in 3DGS)
> Training per-Gaussian feature vectors such that, when rendered to image space, features from the same 2D instance mask are pulled together (cosine similarity maximised) and features from different masks are pushed apart. Enables click-based 3D segmentation.

> [!info] Floater
> A Gaussian primitive appearing in mid-air with no physical surface behind it, produced by 3DGS to explain training-view appearance but causing artefacts in novel views.

> [!info] Space Carving
> Post-processing that removes Gaussians whose projected centres fall inside an instance mask in every training view — meaning they unambiguously belong to the instance and should not remain in the background representation.

> [!info] Novel View Synthesis (NVS)
> Rendering a scene from a camera pose not present in the training set. The central task 3DGS is optimised for.

> [!info] PnP-RANSAC (Perspective-n-Point with RANSAC)
> A solver that estimates a camera pose or rigid object transformation from 2D-3D point correspondences, using RANSAC to robustly discard outlier matches.

> [!info] ICP (Iterative Closest Point)
> A classical algorithm that refines alignment between two point clouds by iteratively finding nearest-neighbour pairs and computing the optimal rigid transformation. Used here to refine coarse registration before shared optimisation.

> [!info] MASt3R
> A transformer-based dense 2D image matcher producing pixel-level correspondences robust to large viewpoint changes. Used here to find reliable matches between rendered virtual views of different instances.

> [!info] KID (Kernel Inception Distance)
> Measures distributional similarity between two sets of images using Inception network features. Used here to evaluate the overall perceptual realism of rendered test views.

> [!info] LPIPS (Learned Perceptual Image Patch Similarity)
> A perceptual image quality metric based on deep features that correlates better with human judgement than pixel-level metrics like PSNR or SSIM.

> [!info] Template
> The single shared Gaussian representation into which all registered instances are merged. Lives in one canonical coordinate frame; all instances are replaced with it after shared optimisation.

---

## Tags

#gaussian-splatting #novel-view-synthesis #repetitive-elements #3D-segmentation #instance-registration #shared-representation #SIGGRAPH2025 #3DGS`