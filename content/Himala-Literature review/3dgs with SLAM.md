> [!note] [3DGS-LSR](https://arxiv.org/pdf/2507.05661) : Large-scale relocation for autonomus driving basedon 3DGS
> - **Problem**: 
> 	- For precise localization, when using methods like GNSS (Global navigation satellite system) there can be signal occulusion and multi-path effects, due to obstruction from the environment due to signal reflecting of surfaces. 
> 	- The trajectories and positions obtained from IPUs, LiDAR and camera can accumulate errors over time. 
> 	- And to store those point cloud data it takes relatively huge amount of storage for **higher accuracy**.
> - **Solution**: 
> 	- Using a large scale 3dgs for re-localization using single monocular camera input. They use SuperPoint and SuperGlue for feature extraction and mapping.  
> 	![[Pasted image 20260607094558.png]]
> 	- And then adjust the current position and orientation of the viewpoint is adjusted by checking with the 3dgs model.
> 	- The pose of the target image is computed by PnP algorithm and a resultant relatively accurate pose comes. From that rgb and depth images are rendered under that pose and an iterative refinement method is used to determine the final refined pose of the target image.
> - **Experimental evaluation**:
> 	- For their evaluation they used KITTI dataset real world driving scenerio.
> 	- ==For evaluation they used a single image provided in the images. (Might be biased because of that.)==
> 	- **Implemented on** : el Core i9-13620H CPU, 16 GB of RAM, and an NVIDIA RTX 4090 GPU with 24 GB of graphics memory.
> 	- Used python and PyTorch.
> 	- They have reported errors less than 10cm and 1 degree.
> 	![[Pasted image 20260621133122.png]]
> 	![[Pasted image 20260621133244.png]]
> - **Conclusion and discussion**:
> 	- ==The test data set is a unidirectional path, therefore range of viewpoints usable of the 3dgs is limited. Therefore if there is a significant deviation of the angle in the iterative process, it will result in a significant degradation of the quality of the 3dgs rendered maps, which can affect the convergence and the. ==
> 	- For future research they suggested future explore how to make 3dgs maps rendered with high fidelity from a more free viewing angle in the case of limited data acquisition and ==how to improve the sensitivity of the repositioning iteration process to angular errors.==
> 	


> [!note] SplatLoc

---
# External resources
- [**SuperPoint**](https://github.com/rpautrat/SuperPoint)---> Basically a alternative to SIFT and ORB which uses CNN instead to **extract feature points**.
- [**SuperGlue**](https://github.com/magicleap/supergluepretrainednetwork)---> Deep Neural network mechanism to accurate real-time **feature matching**.
- [OmniRe](https://ziyc.github.io/omnire/)----> 