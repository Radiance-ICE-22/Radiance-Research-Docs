- A 3D file is nothing but a list of vertices stored in a file. (Like pixels in image files)
- We choose primitive traingle since it will be guaranteed to be on a plane.
- **Rendering** -----> Method of producing a 2D image based on the 3D data file using various algorithms.
- **Ray casting** -----> Rendering technique that project virtual "rays" into a 3D scene to determine pixel colors.
- **Ray tracing** ------> Simulates how light behaves in the real world. (Reflections, refractions etc)


| World space | Screen space |
| ----------- | ------------ |
| (x,y,z)     | (x,y)        |
|             |              |
![[Pasted image 20260116205554.png]]
![[Pasted image 20260116210033.png]]
- Here when consider the same object at different `z`'s they cross through different points in screen.
![[Pasted image 20260117163419.png]]

- https://youtu.be/eoXn6nwV694
---
# Camera projection and parameters
- The process is can be summarized as ,
```lua
x = P.X
```
- Here,
	- x ----> 2D pixel coordinates on the image plane
	- P -----> The camera projection matrix (Encapsulate **intrinsic** and **extrinsic** parameters)
	- X ------> 3D world coordinates in homogeneous form
- **Extrinsic transformation** ----> Position the camera relative to the scene by ==rotating== and ==translating== the 3D points.
- **Intrinsic transformation** -----> Maps these transformed points onto the 2D image plane using the camera's internal properties.
- 3D ---> 2D,
	1. **World coordinate system**: Defines the 3D scene using a global reference frame.
	2. **Camera coordinate system**: Positions the camera as the origin, where the world is transformed relative to it.
	3. **Image plane**: Involves a 2D plane where the camera projects the 3D points, capturing the scene.
	4. **Pixel coordinate system**: Represents the digital image in discrete pixel values, linking image plane coordinates to actual pixels.
## Intrinsic parameters (The camera's internal properties)
- **Focal length (fx,fy)** Represents the distance from the lens to the image sensor, dictating the scale of projection.
- **Principal point (Cx,Cy​)**: ==The center of the image plane== where the optical axis intersects.
- **Aspect ratio and skew (s)**: Corrects any deviation from orthogonality between image axes.
- **Distortion parameters**: Address lens distortions, such as barrel distortion (curving outward) or pincushion distortion (curving inward).
- Intrinsic matrix K,
![[Pasted image 20260117170736.png]]
- Can be found by 
## Extrinsic parameters (Locating the camera in space)
- Define the relative position and the orientation of the camera.
	- **Rotation (**R**): A 3×3 orthogonal matrix representing the camera’s orientation.
	- **Translation (**t**): A 1×3 column vector that specifies the camera’s displacement from the origin.
- Combined,
```lua
[R|t]
```
- Determining parameters like focal length, principal point, and lens distortion. Calibration involves capturing images of a known pattern (e.g., a checkerboard) and applying algorithms like ==Zhang’s method== to compute the camera matrix.

- https://www.e-consystems.com/blog/camera/technology/a-comprehensive-guide-to-understand-camera-projection-and-parameters/
---
![[Pasted image 20260117163722.png]]
![[Pasted image 20260117163909.png]]
- $u_{camera}$ ----> Point in the camera coordinate system
- $a_{world}$ ---> Point in the world coordinate system
- Why? 
	- Since the world we built will have its own coordinate system, we need to know it respective to the camera coordinate system if we want to recreate the photo for the training pipeline.

![[Pasted image 20260117172636.png]]
# 3D to 2D projection in Gaussian splatting
- For each and every Splat we'll do the world space to camera space transformation.
![[Pasted image 20260117172947.png]]
- Affine transfomation for a single point `a`,
![[Pasted image 20260117172959.png]]
- Do the same for the splat,
![[Pasted image 20260117173040.png]]
- ![[Pasted image 20260117173101.png]] is a 3D point in camera coordinates.
- ![[Pasted image 20260117173116.png]] covariance matrix after affine 

- ==After this transformation the camera projection is not applied immediately. Instead there is an intermediate step calle **Ray space** transformation.== (Represented as $m(u)$) Its still 3D to 3D mapping.

![[Pasted image 20260117205845.png]]

---
https://leeyngdo.github.io/blog/computer-graphics/2024-04-09-gaussian-splatting/?utm_source=chatgpt.com

![[Pasted image 20260125104137.png]]

---
## Ray-Space Integration & Gaussian Splatting (What We Discussed)

1. Pixel = Ray
   In a pinhole camera model, each image pixel corresponds to exactly one camera ray.
   So “per-pixel” processing is mathematically equivalent to “per-ray” integration.

2. Naive Ray-Centric View (Expensive)
   Conceptually, volumetric rendering would:
     - Take one ray
     - Find all Gaussians intersecting that ray
     - Integrate contributions along the ray
   This leads to ray × samples × Gaussians complexity.

3. Key Optimization: Splat-Centric Reordering
   Gaussian Splatting inverts the loop:
     - Instead of ray → find splats
     - Do splat → find rays (pixels)
   This reordering is the core performance trick.

4. Visibility & Culling (Before Integration)
   For each 3D Gaussian (splat):
     - Transform its mean to camera space
       → if z ≤ 0, it is behind the camera and discarded
     - Project mean + covariance to the image plane
       → produces a 2D elliptical footprint
     - If the ellipse does not overlap the screen, discard
   The remaining splats are the “visible splats”.

5. What the 2D Ellipse Means
   The projected ellipse represents all rays that pass close enough
   to the Gaussian to have non-negligible contribution.
   Pixel inside ellipse ⇔ ray intersects Gaussian support.
   Thus, projection implicitly performs ray–Gaussian intersection tests.

6. No Per-Ray Gaussian Transforms
   Gaussians are NOT transformed into ray space for every ray.
   Each Gaussian is projected once, then reused across many pixels (rays).

7. Where Ray Integration Happens
   The line integral of a 3D Gaussian along a ray has a closed-form solution.
   After projection, this appears as:
     - Evaluating a 2D Gaussian weight per pixel
     - Combined with a depth-dependent opacity term
   This implicitly performs ray-space integration without sampling.

8. Bulk Processing, Per-Pixel Integration
   All visible splats are processed in bulk:
     - Often binned into screen tiles for efficiency
   For each pixel (ray):
     - Only splats whose ellipses cover that pixel are considered
     - These splats are depth-sorted (front-to-back)
     - Contributions are alpha-composited
     - Early termination occurs when opacity saturates

9. Occlusion Handling
   Occlusion is not solved by ray casting.
   It is resolved by depth ordering + alpha compositing during splatting.

10. Final Mental Model
    Visibility → projection → binning → per-pixel (per-ray) integration

    The 2D splat footprint IS the set of rays that see the Gaussian.
    Gaussian Splatting replaces ray marching with analytic, batched
    per-pixel integration of projected Gaussians.
