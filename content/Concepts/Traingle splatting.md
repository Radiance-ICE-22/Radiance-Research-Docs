## Triangle Primitives

Our primitives are **3D triangles** $T_{3D}$, each defined by:
- Three vertices $v_i \in \mathbb{R}^3$
- A color $c$
- A smoothness parameter $\sigma$
- An opacity value $o$

The three vertices can move freely during optimization.

---

## Projection to Image Space

To render a triangle, each vertex $v_i$ is projected onto the image plane using a **pinhole camera model**.

The projection uses:
- The intrinsic camera matrix $K$ <---- Physical properties of the camera
- Camera pose defined by:
	- Rotation matrix $R$
	- Translation vector $t$

The projection equation is:

$$
q_i = K (R v_i + t)
$$

where:
- $q_i \in \mathbb{R}^2$
- The projected vertices $q_i$ form a 2D triangle $T_{2D}$ in image space

---

## Soft Rasterization

Instead of rendering triangles as fully opaque, their influence is weighted smoothly using a **window function** $I(p)$.

The function maps each pixel position $p$ to a value in the range:

$$
I(p) \in [0, 1]
$$

The choice of this function is critical for rendering quality.

---

## Pixel Color Computation

Once the triangles are projected:

- The color of each image pixel $p$ is computed by accumulating contributions from **all overlapping triangles**
- Triangles are processed in **depth order**
- The window function value $I(p)$ is treated as the **opacity** during blending

## Window Functions: Prior Work vs Ours

We visualize the window functions of prior works [8, 14] (bottom) and the window function introduced in our paper (top), shown in both **1D (left)** and **2D (right)**.

The window functions are compared while varying the smoothness control parameter $\sigma$.

- As $\sigma$ decreases, both methods can approximate the window function of a triangle.
- As $\sigma$ increases, the support of the prior window function (Eq. (2)) exceeds the footprint of the triangle.
- This makes it unsuitable for rasterization workloads.

In the limit:
- The prior window function becomes **globally supported**
- Its value converges to $0.5$ everywhere
- Every triangle contributes to the color of every pixel in the image

---

## A New Window Function

We now describe the definition of the window function $I$, which is one of the core contributions of this work.

---

## Signed Distance Field (SDF) of a Triangle

We define the **signed distance field** $\phi$ of a 2D triangle in image space as:

$$
\phi(p) = \max_{i \in \{1,2,3\}} L_i(p)
$$

where each edge function $L_i(p)$ is given by:

$$
L_i(p) = n_i \cdot p + d_i
$$

- $n_i$ are **unit normals** of the triangle edges pointing **outside** the triangle
- $d_i$ are offsets such that the triangle is represented by the **zero-level set** of $\phi$

The signed distance field $\phi$ has the following properties:
- $\phi(p) > 0$ outside the triangle
- $\phi(p) < 0$ inside the triangle
- $\phi(p) = 0$ on the triangle boundary

---

## Triangle Incenter

Let $s \in \mathbb{R}^2$ be the **incenter** of the projected triangle $T_{2D}$.

- The incenter is the point inside the triangle where the signed distance field $\phi$ is **minimum**
- This corresponds to the most negative value of $\phi$

---

## Definition of the New Window Function

Using the signed distance field, we define the window function $I(p)$ as:

$$
I(p) = \text{ReLU} \left( \frac{\phi(p)}{\phi(s)} \right)^{\sigma}
$$

This definition satisfies:

- $I(p) = 1$ at the triangle incenter
- $I(p) = 0$ at the triangle boundary
- $I(p) = 0$ outside the triangle

---

## Interpretation of the Formulation

- $\phi(p)$ is negative inside the triangle
- $\phi(s)$ is the smallest (most negative) value of $\phi$
- The ratio $\phi(p) / \phi(s)$ is:
  - Positive inside the triangle
  - Equal to $1$ at the incenter
  - Equal to $0$ at the boundary

The parameter $\sigma > 0$ controls the **smoothness** of the window function.

---

## Key Properties of the Window Function

This formulation has three important properties:

1. There exists a point inside the triangle (the incenter) where the window function reaches its maximum value of $1$
2. The window function is zero at the boundary and outside the triangle, so its support tightly fits the triangle
3. A single parameter $\sigma$ allows easy control of smoothness

---

## Behavior as $\sigma$ Changes

- As $\sigma \to 0$, the window function converges to the hard window function of a triangle
- For moderate $\sigma$, the window function transitions smoothly from zero at the boundary to one at the center
- As $\sigma \to \infty$, the window function becomes a **delta function** centered at the incenter

Figure 3 illustrates these behaviors for different values of $\sigma$.
