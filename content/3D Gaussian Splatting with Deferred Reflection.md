# 3D Gaussian Splatting with Deferred Reflection

**Authors:** Keyang Ye, Qiming Hou, Kun Zhou (Zhejiang University)
**Venue:** SIGGRAPH 2024

> [!url] Links
> Paper: https://arxiv.org/pdf/2404.18454
> Code: https://github.com/gapszju/3DGS-DR

---

## What Was Achieved

> [!abstract] Overview
> Introduces a **deferred shading pipeline** on top of 3D Gaussian Splatting to render high-quality specular reflections at near-vanilla 3DGS speeds. The key insight is moving reflection evaluation from per-Gaussian to per-pixel, which both improves quality and enables a novel normal-propagation training strategy.

### Core Pipeline

> [!info] Two-Pass Rendering
> **Pass 1 — Gaussian Splatting Pass**
> Splats per-Gaussian base colour, normal (shortest ellipsoid axis), and reflection strength into screen-space maps.
>
> **Pass 2 — Deferred Reflection Pass**
> Queries a learned environment map using the per-pixel reflection direction, then blends base and reflection colours weighted by the per-pixel reflection strength map.

> [!tip] Training Innovations
> **Normal Propagation**
> Gaussians with high reflection strength (already converged normals) are periodically scaled up to overlap neighbours, injecting correct normal gradients into nearby Gaussians via shared pixels. Correct normals gradually spread across the entire reflective surface.
>
> **Color Sabotage**
> Adds ±10% noise to non-reflective Gaussians' colours during propagation steps. Prevents diffuse overfitting from blocking reflective surface discovery.
>
> **View-Independent Bootstrap**
> First few thousand iterations train with SH order 0 and reflection disabled, providing a stable diffuse base before reflection optimisation begins.

> [!attention] Why Deferred Beats Forward Shading
> Per-Gaussian (forward) shading produces independent normal gradients — Gaussians cannot influence each other's normals. Deferred shading creates a gradient path: **image loss → blended pixel normal → individual Gaussian normal**, letting correctly-converged Gaussians propagate their normals to neighbours.

### Quantitative Results

> [!done] Performance vs. Baselines (Shiny Blender + Glossy Synthetic + Real)
> - [w] Consistently best or tied-best PSNR/SSIM/LPIPS across all synthetic scenes vs. Ref-NeRF, NPC, vanilla 3DGS, GaussianShader, ENVIDR
> - [w] Normal reconstruction MAE° of **4.871°**, on par with ENVIDR (4.618°) and far better than GaussianShader (22.31°)
> - [w] Best environment map LPIPS (**0.511**) among all compared methods
> - [f] Rendering: **251 FPS** (synthetic) / **80 FPS** (real), nearly identical to vanilla 3DGS (277 / 84 FPS)
> - [f] Training: **~16 min** (synthetic) / **~47 min** (real) vs. hours for NeRF-based methods
> - [u] Uses far fewer Gaussians — e.g. **27k vs. 199k** for the ball scene compared to vanilla 3DGS

---

## Novelty

> [!table] Contributions
>
> | Contribution | Why It Matters |
> |---|---|
> | **Per-pixel deferred reflection** | Prior work computed reflection per-Gaussian, yielding sparse env-map samples and Gaussian-boundary artefacts. Per-pixel gives orders-of-magnitude more samples per frame at negligible extra cost. |
> | **Normal propagation via inflation** | Breaks the chicken-and-egg problem: correct normals need good gradients, good gradients need correct normals. Seeds from the few Gaussians that converge early. |
> | **Colour sabotage** | Prevents base SH colours from explaining away reflection and stalling normal propagation. Simple but critical. |
> | **Gradient channel via blended normals** | Only possible with deferred shading. Forward shading gives each Gaussian independent gradients that cannot influence neighbours. |
> | **No full inverse rendering** | Deliberately avoids ill-posed geometry-lighting-material decomposition, achieving better novel-view quality than full decomposition methods while remaining real-time. |

Key technical contributions:

- [k] Per-pixel reflection evaluation in a Gaussian rasteriser
- [k] Normal propagation as a training-time mechanism for gradient sharing
- [k] Colour sabotage to prevent premature diffuse convergence
- [k] Two-stage training (view-independent bootstrap then reflection)

---

## Limitations

> [!warning] Known Constraints
> - [c] **Single reflection layer per pixel** — inherits the classic deferred shading constraint; transparent/multilayer objects (e.g. car windows) are handled inconsistently
> - [c] **Concave geometry** — normal propagation is less efficient on concave surfaces (e.g. the bell scene); training converges but takes considerably longer
> - [c] **No roughness / glossy BRDF** — only mirror (sharp) reflection is modelled; rough, anisotropic, and layered materials fall back to base SH colours
> - [c] **No relighting or material editing** — scene decomposition is not complete enough for downstream editing tasks
> - [c] **Background interference** — requires a foreground sphere mask for real scenes to prevent background Gaussians from being mistakenly treated as reflective

---

## Future Improvements

> [!question] Open Directions

- [I] **Roughness / glossy materials** — extend shading model with a physically-based roughness parameter, generalising from mirror to glossy BRDFs
- [I] **Screen-space reflections (SSR)** — replace or augment the environment map query with SSR for inter-object reflections not captured by a static env map
- [I] **Hardware ray tracing** — integrate differentiable ray tracing for higher-fidelity multi-bounce reflections
- [I] **Multi-layer / transparent surfaces** — address the single-layer constraint to handle glass, water, and layered materials correctly
- [I] **Improved normal propagation for concave geometry** — better optimisation scheduling or seed selection to accelerate convergence in concave/interior regions
- [I] **Dynamic scenes** — current method is static; extending to dynamic or deformable scenes with correct reflection updates is an open problem
- [I] **Anisotropic / layered materials** — brushed metal, fabric, car paint require more expressive BRDF models beyond the current scalar reflection strength
- [I] **Global illumination** — indirect lighting (caustics, inter-reflections) is not modelled; coupling with a differentiable GI estimator could significantly improve realism

---

## Relation to Research Direction

> [!note] Connection to Human-in-the-Loop 3DGS
> Connects to the broader goal of improving 3DGS initialisation and handling challenging surface phenomena. The **normal propagation mechanism** is directly relevant to any 3DGS extension that needs reliable per-Gaussian normals — including human-in-the-loop pipelines where high-confidence Gaussians are selected as seeds to guide harder regions.

---

## Tags

`#gaussian-splatting` `#novel-view-synthesis` `#reflections` `#deferred-shading` `#real-time-rendering` `#SIGGRAPH2024` `#3DGS`