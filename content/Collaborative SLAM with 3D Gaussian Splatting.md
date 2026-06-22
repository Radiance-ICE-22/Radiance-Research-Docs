# A Survey on Collaborative SLAM with 3D Gaussian Splatting

**Authors:** Phuc Nguyen Xuan, Thanh Nguyen Canh, Huu-Hung Nguyen, Nak Young Chong, Xiem HoangVan
**Affiliations:** Le Quy Don Technical University (Vietnam), JAIST (Japan), VNU Hanoi (Vietnam)
**Type:** Survey / arXiv preprint (2025)

> [!url] Links
> Paper: https://arxiv.org/html/2510.23988v1

---

## Core Idea

> [!abstract] What This Survey Covers
> A comprehensive review of **Collaborative Gaussian Splatting SLAM (CoGS-SLAM)**: systems where multiple robots simultaneously build a shared 3DGS map of an unknown environment. The survey taxonomises architectures (centralised vs. distributed), dissects core technical sub-problems (consistency, communication, fusion, optimisation), and maps out open challenges. The field sits at the intersection of multi-robot systems, visual SLAM, and 3DGS.

---

## Why 3DGS for Robotics SLAM

> [!info] 3DGS Advantages Over NeRF for SLAM
> NeRF-based SLAM systems produce high-quality maps but require large MLPs, causing slow training, slow rendering, and difficulty editing the map at runtime. These are all dealbreakers for real-time robotics. 3DGS offers:
>
> - [p] **Real-time rendering** via differentiable tile-based rasterisation — no neural network query per point
> - [p] **Explicit, editable representation** — Gaussians can be directly transformed by rigid-body operations, critical for map correction after loop closure and multi-agent submap merging
> - [p] **High-fidelity reconstruction** comparable to NeRF, with expressive visual, geometric, and semantic information
> - [p] **Metric scale + gravity alignment** possible when paired with a visual-inertial sensor suite, necessary for autonomous exploration

> [!attention] Why Collaborative SLAM Needs 3DGS
> Single-agent SLAM cannot quickly map large-scale environments. Multi-robot teams provide accelerated mapping, shared spatial awareness, and robustness through redundancy. Adding 3DGS as the map representation enables real-time photorealistic digital twins of large environments — something neither sparse point-cloud SLAM nor NeRF-based SLAM can provide together.

---

## Core Technical Sub-problems

> [!info] Four Key Challenges in CoGS-SLAM
>
> **Map Consistency and Fusion**
> Multiple independently generated Gaussian submaps must be merged into a single globally consistent map without geometric distortions or duplicate/conflicting Gaussians.
>
> **Communication Bottlenecks**
> Centralised architectures strain network bandwidth. Distributed systems need protocols to share map data (raw Gaussians, compressed representations, or parameters) efficiently under bandwidth and latency constraints.
>
> **Scalability and Robustness**
> Most systems are validated on small-scale indoor scenes. Extending to large-scale outdoor environments with heterogeneous sensor suites, challenging lighting, and reflective surfaces remains unsolved.
>
> **Global Consistency**
> Trajectory drift from individual agents is amplified in multi-agent settings. Robust intra-robot and inter-robot loop closure mechanisms are critical for long-term accuracy.

### System Architectures

> [!info] Centralised Architecture
> Agents stream local data (keyframes, sensor readings) to a powerful central server. The server handles map fusion, global Pose Graph Optimisation (PGO), and inter-agent loop closure detection. Some systems use bidirectional communication — the server sends optimised results back to refine local estimates.
>
> **Trade-off:** Simple to implement global consistency; single point of failure; bandwidth bottleneck at server.

> [!info] Distributed Architecture
> Robots communicate peer-to-peer to exchange map information, detect inter-robot loop closures, and run distributed PGO (dPGO). Each agent maintains its own submap and resolves updates cooperatively.
>
> **Trade-off:** More scalable and fault-tolerant; harder to achieve global consistency; requires carefully designed distributed optimisation.

### Design Patterns

> [!tip] Shared Gaussian Representations and Semantic Distillation
> Agents maintain a common global Gaussian map template into which local submaps are merged. Semantic feature vectors are attached per-Gaussian (via contrastive learning or feature distillation), enabling semantic-aware fusion and loop closure. Shared representations reduce redundancy and allow gradients to flow from multiple agents' views to a single set of Gaussian parameters.

> [!tip] Inter-Agent Correspondence and Consistency Mechanisms
> Loop closure between agents requires robust place recognition (Bag-of-Words, NetVLAD, or Scan Context for LiDAR) to find where robot A's map overlaps robot B's. False positives (perceptual aliasing) are suppressed with outlier rejection methods such as Pairwise Consistent Measurement (PCM) set maximisation or Graduated Non-Convexity (GNC). Once a valid inter-robot loop closure is detected, the relative transform T_ij in SE(3) is used to align and merge the submaps.

> [!tip] Asynchronous Fusion and Pose Optimisation
> Agents build local Gaussian submaps independently and asynchronously. Map fusion is triggered by successful inter-robot loop closures. A joint loss minimises tracking error, mapping error, loop closure constraints, and fusion consistency simultaneously. Pose refinement is jointly optimised with Gaussian parameter refinement after submap merging.

> [!tip] Compression-Oriented Efficiency for Real-Time Scalability
> Raw Gaussian clouds are expensive to transmit. Methods include: pruning low-opacity Gaussians before transmission, quantising Gaussian parameters, transmitting only new keyframes and their associated Gaussians rather than the full map, and shared SH bases that reduce per-Gaussian storage. Compression enables operation on constrained bandwidth links typical of UAV or UGV swarm scenarios.

---

## Single-Robot GS-SLAM (Foundations)

> [!info] How Single-Robot GS-SLAM Works
> Pioneering systems: **SplaTAM**, **GS-SLAM**, **LoopSplat**. Each follows a classic SLAM paradigm adapted for Gaussians:
>
> **Tracking module:** Minimises a photometric + depth loss to estimate the 6-DoF camera pose per frame via gradient descent through the differentiable 3DGS renderer.
>
> **Mapping module:** Seeds new Gaussians from the incoming depth map, then jointly optimises Gaussian parameters and a local window of keyframe poses to minimise a mapping loss (colour + depth + regularisation).
>
> **Loop closure:** When a loop is detected, PGO is run over the keyframe pose graph, and the Gaussian map is deformed/transformed accordingly to correct accumulated drift.

---

## Applications

> [!done] Validated Use Cases
> - [w] **UAV / UGV collaborative exploration** — multi-agent teams map unknown outdoor environments with real-time 3DGS rendering
> - [w] **Autonomous driving** — large-scale urban scene reconstruction from multi-vehicle data
> - [w] **Digital twins** — photorealistic live-updating maps for infrastructure monitoring
> - [w] **Industrial automation** — multi-robot workcell mapping for navigation and manipulation
> - [w] **Environmental monitoring** — aerial agents mapping disaster zones or natural environments
> - [w] **Virtual reality** — real-time, navigable 3DGS scenes from collaborative capture

### Benchmarks and Metrics

> [!info] Key Evaluation Datasets
> Indoor RGB-D: Replica, TUM RGB-D, ScanNet
> Outdoor / large-scale: KITTI, nuScenes
> Multi-robot / collaborative: custom sequences from the surveyed papers (no standard multi-robot 3DGS benchmark exists yet — a noted gap)

> [!info] Key Evaluation Metrics
> **Tracking accuracy:** ATE (Absolute Trajectory Error), RPE (Relative Pose Error)
> **Mapping quality:** PSNR, SSIM, LPIPS (rendering quality); depth L1 error (geometric accuracy)
> **Efficiency:** Training/update time per keyframe, total map memory, communication bandwidth consumed
> **Semantic quality:** mIoU on segmented Gaussian scenes (where applicable)

---

## Open Challenges and Future Directions

> [!warning] Unresolved Problems
> - [c] **No standard multi-robot 3DGS benchmark** — most systems are evaluated on re-purposed single-robot datasets with simulated multi-agent splits
> - [c] **Scalability to large outdoor scenes** — validated almost exclusively on small-scale indoor environments
> - [c] **Heterogeneous sensor suites** — combining RGB-D cameras, LiDAR, and event cameras from different robot types in a single Gaussian map is unsolved
> - [c] **Bandwidth constraints at scale** — raw Gaussian clouds are large; transmitting them over real radio links with latency and packet loss is not addressed
> - [c] **Dynamic environments** — moving objects (people, vehicles) corrupt the static Gaussian map; no robust CoGS-SLAM handles dynamics reliably
> - [c] **Sim-to-Real gap** — methods developed and evaluated in simulation (e.g. Replica, synthetic sequences) often fail in real-world conditions (motion blur, varying illumination, lens distortion)

> [!question] Key Future Research Directions

- [I] **Lifelong mapping** — continuous updating of the Gaussian map as the environment changes over time, including adding, removing, and modifying Gaussians without full re-training
- [I] **Semantic association and mapping** — coupling semantic segmentation with the Gaussian representation to enable object-level loop closure and more robust inter-agent data association
- [I] **Multi-modal robustness** — fusing RGB-D, LiDAR, event cameras, and IMUs within a single coherent CoGS-SLAM framework to handle diverse robot platforms
- [I] **Bridging Sim2Real** — designing training and evaluation protocols that transfer to real-world noise, lighting variation, and motion blur common in drone/UGV operation
- [I] **Bandwidth-aware Gaussian compression** — learned or structured compression of Gaussian clouds for transmission over constrained links (relevant for swarm drone operations)
- [I] **Active exploration with 3DGS** — using the current Gaussian map to plan next-best-views for collaborative agents, closing the loop between reconstruction quality and navigation policy
- [I] **Integration with SLAM front-ends beyond RGB-D** — making CoGS-SLAM work with monocular cameras only, which is the most common sensor on lightweight UAVs

---

## Relation to Research Direction

> [!note] Connection to Real-Time Drone/SLAM Rendering
> This survey is **the primary reference** for the expanded project goal of real-time environment rendering for drone navigation and SLAM. Key takeaways for the research direction:
>
> - [!] **3DGS is already the preferred map representation for real-time SLAM** — explicit, editable, and fast to render. The project is building on the right foundation.
> - [!] **No standard benchmark for multi-robot / drone 3DGS-SLAM exists** — this is an opportunity to contribute evaluation infrastructure.
> - [k] The human-in-the-loop initialisation idea maps directly to the **seeding problem** in GS-SLAM: where and how Gaussians are initialised from depth maps is a known quality bottleneck. Human or semantic priors at init could accelerate convergence in the mapping module.
> - [k] The survey identifies **reflective surfaces and illumination variation** as unresolved robustness issues, directly overlapping with the reflection-aware Gaussian unit planned for the project.
> - [k] **Compression-oriented efficiency** is critical for onboard drone compute. The SH offset decomposition pattern (from Splat and Replace) and regularised opacity/scale patterns (from both reviewed papers) are directly applicable to reducing the Gaussian count before transmission.
> - [I] A promising niche: combining human-seeded initialisation + reflection handling + collaborative map sharing in a single drone-targeted pipeline — none of the surveyed systems address all three.

---

## Glossary of Key Terms

> [!info] SLAM (Simultaneous Localisation and Mapping)
> The problem of an autonomous agent building a map of an unknown environment while simultaneously estimating its own position within that map. Combines a front-end (sensor processing, tracking) and a back-end (map optimisation, loop closure).

> [!info] CoSLAM / CoGS-SLAM
> Collaborative SLAM: the multi-robot extension of SLAM where N agents jointly build a single globally consistent map. CoGS-SLAM specifically uses 3DGS as the map representation.

> [!info] Pose Graph Optimisation (PGO)
> A back-end SLAM technique that represents robot poses as graph nodes and sensor constraints (odometry, loop closures) as edges, then minimises a least-squares objective over the graph to find globally consistent trajectories. Corrects accumulated drift.

> [!info] Loop Closure
> The event where a robot (or agent) recognises a previously visited location. Intra-robot: re-visiting its own past trajectory. Inter-robot: visiting a region already mapped by another agent. Detecting loop closures provides non-sequential constraints that correct drift in PGO.

> [!info] Submap
> A local Gaussian map built by a single agent covering only the region it has explored. Submaps are eventually fused into a global map after inter-robot loop closures provide the relative transformation between coordinate frames.

> [!info] Keyframe
> A selected subset of input frames whose poses and associated Gaussian parameters are retained in the map. Not every frame is a keyframe; selection criteria typically include sufficient motion or appearance change from the previous keyframe.

> [!info] SE(3)
> The Special Euclidean group in 3D: the space of rigid-body transformations (rotation + translation). All robot poses and inter-agent alignment transforms live in SE(3).

> [!info] Distributed PGO (dPGO)
> A variant of Pose Graph Optimisation where no central server exists. Each agent solves part of the optimisation locally and iteratively communicates updates to its neighbours until the global solution converges.

> [!info] Perceptual Aliasing
> When visually similar but geometrically distinct locations produce the same descriptor, causing false-positive loop closure detections. A key failure mode in place recognition for SLAM.

> [!info] Tile-Based Rasteriser
> The rendering engine in 3DGS. The screen is divided into tiles; Gaussians are sorted by depth within each tile and alpha-blended in parallel on the GPU. This enables real-time rendering at high resolution.

> [!info] ATE (Absolute Trajectory Error)
> A trajectory accuracy metric that measures the root-mean-square of the Euclidean distance between estimated and ground-truth poses after aligning the two trajectories with a single rigid transform. The standard metric for SLAM localisation accuracy.

> [!info] RPE (Relative Pose Error)
> Measures local consistency of a trajectory by comparing relative transformations between consecutive poses. Complements ATE by capturing drift over short segments rather than global alignment error.

> [!info] UAV / UGV
> Unmanned Aerial Vehicle (drone) and Unmanned Ground Vehicle (wheeled/tracked robot). The two primary platforms for which CoGS-SLAM is being developed for real-world autonomous navigation.

> [!info] Sim2Real Gap
> The performance difference between a system evaluated in simulation (clean, controlled, with perfect ground truth) versus deployed on real hardware (noisy sensors, motion blur, variable lighting, physical imperfections). A critical unsolved problem for all drone/robot SLAM systems.

---

## Tags

`#gaussian-splatting` `#SLAM` `#collaborative-SLAM` `#multi-robot` `#drone-navigation` `#real-time-rendering` `#survey` `#3DGS` `#UAV` `#loop-closure` `#pose-graph`