# Feasibility: 3DGS for Streaming Map Reconstruction (LingBot-Map Style)

> [!url] Reference Projects
> LingBot-Map (target benchmark): https://github.com/Robbyant/lingbot-map
> LingBot-Map paper: https://arxiv.org/abs/2604.14141
> StreamGS (closest 3DGS equivalent): https://arxiv.org/abs/2503.06235
> Large-Scale GS SLAM: https://arxiv.org/html/2505.09915

---

## What LingBot-Map Does (The Bar to Match)

> [!abstract] LingBot-Map System Summary
> LingBot-Map is a **feed-forward 3D foundation model** for streaming dense reconstruction. It processes a video stream frame-by-frame in a single forward pass, with no per-scene optimisation loop. Built on top of VGGT and DINOv2, it uses a **Geometric Context Transformer** with paged KV-cache attention to maintain temporal coherence and correct drift over very long sequences.

### Core Capabilities

> [!info] Performance Specifications
> - [f] **~20 FPS** on 518×378 resolution (desktop GPU)
> - [f] Stable over sequences exceeding **10,000 frames**
> - [i] No predetermined camera poses required — pose is estimated internally
> - [i] Paged KV-cache attention for streaming memory management
> - [i] Sky masking support for outdoor scenes
> - [i] Windowed inference mode for sequences exceeding 3,000 frames
> - [i] Output: **dense point cloud only** — no novel-view rendering capability

### What It Does NOT Provide

> [!warning] LingBot-Map Limitations Relevant to This Project
> - [c] Output is a point cloud — **no photorealistic rendering, no novel view synthesis**
> - [c] No semantic labelling of the map
> - [c] No loop closure or global consistency correction built in
> - [c] No explicit surface representation (Gaussians, meshes, surfels)
> - [c] Not designed for collaborative multi-agent mapping
> - [c] Cannot be queried for "what does this location look like from a new viewpoint"

> [!attention] Key Insight
> LingBot-Map solves the **geometry problem** (where things are) but not the **appearance problem** (what things look like). For drone navigation alone, a point cloud may suffice for obstacle avoidance. For the full project goal — real-time *environment rendering* — it is insufficient. 3DGS solves both simultaneously.

---

## What 3DGS Natively Provides and Where It Struggles

### Strengths of 3DGS for Mapping

> [!tip] Why 3DGS Is the Right Representation
> - [p] **Full renderable map** — photorealistic novel-view synthesis from any camera pose, not just a point cloud
> - [p] **Explicit and editable** — Gaussians can be transformed, merged, pruned, and corrected by rigid-body operations; critical for loop closure and submap fusion
> - [p] **Real-time rendering** — tile-based rasteriser runs at hundreds of FPS for navigation and re-localisation queries
> - [p] **Geometric and appearance information in one structure** — position, shape, opacity, and view-dependent colour per primitive
> - [p] **Extendable with semantics** — per-Gaussian feature vectors can carry semantic labels (S2GS, OnlinePG) for object-aware navigation
> - [p] **Metric scale recovery** — when combined with depth or visual-inertial sensors, provides metrically accurate geometry needed for drone navigation

### The Core Tension with Streaming

> [!warning] The Fundamental Problem
> Vanilla 3DGS is an **optimisation-based** method. It runs hundreds of gradient-descent iterations over a fixed image set to minimise a photometric loss. This is structurally incompatible with streaming:
>
> - A single 3DGS scene from scratch takes **6–50 minutes** of training
> - Running 300 optimisation iterations per incoming frame at 20 FPS is computationally impossible on any current hardware
> - Vanilla 3DGS requires all cameras to be known in advance (from COLMAP or similar)
> - Memory grows unboundedly with scene size without explicit management

> [!attention] This Is a Solved (or Largely Solved) Problem in 2025
> The field has attacked all three issues specifically. The following sections document the current state of solutions.

---

## Current Solutions: How the Field Has Closed the Gap

### Category 1 — Feed-Forward 3DGS (Directly Comparable to LingBot-Map)

> [!info] StreamGS (ICCV 2025)
> The most direct 3DGS answer to LingBot-Map. StreamGS processes an **unposed image stream** frame-by-frame in a feed-forward pass, predicting and accumulating per-frame Gaussians into a growing scene representation.
>
> Key properties:
> - [w] **150x faster** than optimisation-based 3DGS methods
> - [w] No predetermined camera poses required
> - [w] Online, generalises to out-of-domain scenes via content-adaptive refinement
> - [w] Cross-frame consistency via pixel correspondences between adjacent frames
> - [f] Output is a **full renderable 3DGS scene**, not just a point cloud
>
> **Relevance:** This is essentially what LingBot-Map does architecturally, but with a 3DGS output instead of a point cloud. For drone mapping this is strictly better — you get geometry AND appearance in real time.

> [!info] EC3R-SLAM (2025)
> A hybrid approach: a feed-forward 3D reconstruction network (similar to LingBot-Map) provides fast pose estimation and initial geometry, which then seeds a 3DGS SLAM back-end for refinement and rendering. Explicitly designed to combine the speed of feed-forward inference with the quality of Gaussian optimisation.

### Category 2 — Incremental Optimisation SLAM (Online But Slower)

> [!info] GS-SLAM Systems (SplaTAM, GS-SLAM, WildGS-SLAM)
> These systems interleave fast tracking steps (pose estimation per frame) with slower mapping steps (Gaussian parameter optimisation over a keyframe window).
>
> - Tracking thread: real-time or near-real-time
> - Mapping thread: 1–5 FPS effective update rate
> - **WildGS-SLAM** (CVPR 2025) extends this to dynamic environments, handling moving objects — relevant for drone operation in populated areas
>
> **Trade-off:** Higher map quality and loop closure support vs. lower overall frame rate.

> [!info] ROS-Integrated 3DGS Systems (2025)
> A ROS-based online system bridges ORB-SLAM (front-end) with a Local Gaussian Splatting Bundle Adjustment back-end. Reduces initialisation time by **10x** compared to offline 3DGS while improving PSNR. Directly deployable on robot platforms via standard ROS middleware.

### Category 3 — Large-Scale and Long-Sequence Scalability

> [!info] LSG-SLAM: Large-Scale Gaussian Splatting SLAM (2025)
> Introduces continuous GS submaps to handle unbounded scenes within a fixed memory budget. Loop closure is detected between submaps using place recognition, and relative poses between looped keyframes are refined using rendering loss and feature warping loss.
>
> Directly addresses the 10,000+ frame scalability requirement.

> [!info] DiskChunGS: Chunk-Based Memory Management (2025)
> Partitions the scene into chunks stored on disk, loading only the active region into GPU memory. Eliminates the GPU memory ceiling for large-scale scenes. Enables 3DGS SLAM across environments that would otherwise overflow VRAM.

> [!info] LatentAM: Latent Gaussian Attention Mapping (2026)
> Integrates dictionary-based feature learning into a local-global 3DGS mapping system with voxel hashing.
>
> - [w] **12–35 FPS** at runtime
> - [w] Scales to environments exceeding **530 metres**
> - [w] Near-real-time operation on standard hardware
>
> This is the closest system to matching LingBot-Map's performance while producing a full Gaussian map.

> [!info] GigaSLAM (2025)
> Targets **multi-kilometre-scale** outdoor sequences. Uses a hierarchical sparse voxel map where Gaussians are decoded by neural networks at multiple levels of detail. Maintains high-fidelity rendering at scale — the most ambitious scalability result in the field.

### Category 4 — Semantic Streaming Maps

> [!info] S2GS: Streaming Semantic Gaussian Splatting (2025)
> Online scene understanding and reconstruction that attaches semantic feature vectors to Gaussians as the map is built. Enables real-time object-aware navigation — each Gaussian knows what object it belongs to. Directly relevant for drone obstacle classification.

> [!info] OnlinePG: Open-Vocabulary Panoptic Mapping with 3DGS (2025)
> Online open-vocabulary panoptic mapping where language-grounded semantic labels are fused into the Gaussian map incrementally. Enables natural-language queries about the map during flight.

---

## Capability Comparison

> [!table] LingBot-Map vs. 3DGS Ecosystem
>
> | Dimension | LingBot-Map | Vanilla 3DGS | Best Current 3DGS (2025) |
> |---|---|---|---|
> | Inference speed | ~20 FPS | 0.0001 FPS (offline) | 12–35 FPS (LatentAM, StreamGS) |
> | Long sequences (10k+ frames) | Yes (paged KV cache) | No | Yes (DiskChunGS, LSG-SLAM) |
> | No prior poses required | Yes | No (needs COLMAP) | Yes (StreamGS, EC3R-SLAM) |
> | Novel view synthesis | No | Yes | Yes |
> | Photorealistic rendering | No | Yes | Yes |
> | Semantic labels on map | No | No | Yes (S2GS, OnlinePG) |
> | Loop closure / drift correction | No | No | Yes (LSG-SLAM, LoopSplat) |
> | Multi-agent / collaborative | No | No | Yes (CoGS-SLAM systems) |
> | Dynamic object handling | No | No | Yes (WildGS-SLAM) |
> | Onboard drone hardware | Yes (inference only) | No | Partial (active research gap) |
> | Memory management | Paged KV cache | Unbounded | Chunking / submaps |
> | Output format | Point cloud | Gaussian scene | Gaussian scene |

---

## Remaining Challenges

> [!warning] Unsolved Problems for the Drone Use Case

- [c] **Onboard compute constraints** — StreamGS and LatentAM run well on desktop GPUs. Jetson-class (drone-grade) hardware is roughly 5–10x slower. No 2025 system has demonstrated full-stack 3DGS streaming mapping on an onboard drone computer at usable frame rates.
- [c] **Initialisation fragility** — most online 3DGS SLAM systems require a short stationary period or known starting conditions. Drones in flight rarely provide this.
- [c] **Motion blur** — fast drone movement causes motion blur in camera frames that degrades both Gaussian initialisation and pose estimation. LingBot-Map has the same weakness.
- [c] **Variable lighting** — outdoor drone operation encounters rapid lighting changes (clouds, shadows, specular ground surfaces) that destabilise both appearance and geometry optimisation.
- [c] **No unified benchmark** — there is no standard multi-drone 3DGS streaming mapping benchmark. Performance claims from different systems are hard to compare directly.
- [c] **Sim2Real gap** — most systems are validated on TUM, Replica, or ScanNet (controlled indoor RGB-D). Real drone footage has more noise, wider baselines, and less overlap.

---

## Feasibility Verdict

> [!done] Overall Assessment: Feasible, and Strictly Better Than Point Clouds

- [w] **Geometry coverage:** 3DGS streaming systems (StreamGS, LatentAM, LSG-SLAM) now match or exceed LingBot-Map's ability to handle long sequences and unposed streams
- [w] **Output richness:** 3DGS produces a renderable map — photorealistic novel views, surface normals, depth — not just a sparse point cloud. For real-time environment *rendering* this is the correct choice.
- [w] **Semantic and collaborative extensions:** Already demonstrated in 2025 systems. A 3DGS map can be annotated with semantics and shared between agents; a point cloud cannot.
- [!] **The only remaining gap is onboard hardware speed.** Desktop-class performance is now competitive with LingBot-Map. The open research problem is shrinking the compute budget to fit a drone payload.
- [i] LingBot-Map's feed-forward architecture is actually a *component model* for how to approach this — StreamGS proves a feed-forward 3DGS network can process streaming inputs at similar speeds, just with richer output.

---

## Recommended Architecture for the Project

> [!tip] Proposed Streaming 3DGS Pipeline for Drone Mapping

The following architecture synthesises the best of each approach for the drone/SLAM real-time rendering goal:

**Stage 1 — Front-End (Fast Pose + Depth)**
Use a lightweight feed-forward model (LingBot-Map style or a VIO front-end) for per-frame pose estimation and initial depth. This runs at full frame rate and does not need to be 3DGS-aware.

**Stage 2 — Gaussian Seeding**
Seed new Gaussians from the incoming depth map at the estimated pose. Apply opacity and scale regularisation from the start (as in Splat and Replace) to keep the Gaussian cloud clean.

**Stage 3 — Incremental Optimisation (Keyframe Window)**
Run Gaussian parameter optimisation over a sliding window of recent keyframes. This is the mapping thread and runs asynchronously at a lower rate (1–5 FPS) while tracking continues at full speed.

**Stage 4 — Memory Management**
Use chunk-based or submap-based memory management (DiskChunGS / LSG-SLAM pattern) to handle long sequences without VRAM overflow. Inactive chunks are offloaded to CPU/disk.

**Stage 5 — Loop Closure and Global Consistency**
Detect inter-keyframe loop closures using place recognition. Run PGO and deform/transform the affected Gaussian submaps. This is the back-end thread and runs asynchronously.

**Optional — Human-in-the-Loop Seeding**
At initialisation, a user marks reflective surfaces and repeating texture regions. These annotations pre-populate those regions with specialised Gaussian primitives (reflection-aware units, shared-template units from Splat and Replace) before the incremental optimisation begins — directly reducing the number of optimisation iterations needed to converge.

> [!note] Connection to Project Research Direction
> The human-in-the-loop seeding step is exactly where the project's core novelty lives. No existing streaming 3DGS system includes semantic priors at initialisation. Adding this would reduce the mapping thread's optimisation burden — shrinking convergence time — which is the primary remaining barrier to onboard drone deployment. The project could position this specifically as: *human-annotated semantic priors for accelerated streaming 3DGS initialisation, validated on drone navigation sequences*.

---

## Key Papers to Track

- [k] [StreamGS — Feed-forward 3DGS for unposed streams](https://arxiv.org/abs/2503.06235)
- [k] [EC3R-SLAM — Feed-forward + 3DGS hybrid](https://arxiv.org/html/2510.02080v1)
- [k] [LatentAM — Real-time large-scale Gaussian mapping (12–35 FPS)](https://arxiv.org/pdf/2602.12314)
- [k] [LSG-SLAM — Large-scale GS SLAM with submaps](https://arxiv.org/html/2505.09915)
- [k] [DiskChunGS — Chunk-based memory for large-scale 3DGS](https://arxiv.org/pdf/2511.23030)
- [k] [S2GS — Streaming semantic Gaussian splatting](https://arxiv.org/pdf/2603.14232)
- [k] [WildGS-SLAM — Dynamic environments (CVPR 2025)](https://openaccess.thecvf.com/content/CVPR2025/papers/Zheng_WildGS-SLAM_Monocular_Gaussian_Splatting_SLAM_in_Dynamic_Environments_CVPR_2025_paper.pdf)
- [k] [ROS-based online 3DGS with ORB-SLAM front-end](https://pmc.ncbi.nlm.nih.gov/articles/PMC12252524/)

---

## Glossary

> [!info] Feed-Forward Model
> A neural network that processes input in a single forward pass with no iterative loop. Fast at inference time but requires large-scale pretraining to generalise. LingBot-Map and StreamGS are both feed-forward.

> [!info] Optimisation-Based 3DGS
> The original 3DGS training procedure: initialise Gaussians from a point cloud, then iteratively minimise a photometric loss over many input images. Produces high-quality results but is slow and requires all images upfront.

> [!info] Submap
> A local Gaussian map covering only the region explored so far by one agent (or one time window). Multiple submaps are later merged into a global map after loop closure provides the relative transform between them.

> [!info] Paged KV Cache
> LingBot-Map's mechanism for handling long sequences. The transformer's key-value attention memory is stored in pages that can be swapped like virtual memory, preventing attention cost from growing quadratically with sequence length.

> [!info] Voxel Hashing
> A spatial data structure that stores scene information in a hash map keyed by 3D voxel indices. Enables O(1) lookup for the active voxel at any position, and easy activation/deactivation of scene regions as the camera moves through large spaces.

> [!info] Place Recognition
> The task of determining whether a current sensor reading matches a previously visited location. Typically done by comparing compact descriptors (Bag-of-Words, NetVLAD) of keyframes. The trigger for loop closure in SLAM.

> [!info] Visual-Inertial Odometry (VIO)
> Combining a camera with an IMU (Inertial Measurement Unit) for pose estimation. The IMU provides high-rate motion estimates; the camera corrects drift. Essential for metric-scale pose estimation on drones without GPS.

---

## Tags

#gaussian-splatting #feasibility #streaming-reconstruction #drone-navigation #SLAM #real-time-rendering #feed-forward #online-mapping #3DGS #LingBot-Map`