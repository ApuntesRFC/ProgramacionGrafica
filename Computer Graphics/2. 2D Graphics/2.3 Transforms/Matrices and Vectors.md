> [!summary]
Transformations in computer graphics are represented using **matrices**, and points or directions are represented as **vectors**.  
Matrix–vector multiplication applies transformations like translation, rotation, or scaling to geometric data.

---

### Matrices and Vectors

> [!info]
> - A **matrix** is a 2D array of numbers.  
> - A **vector** is a 1D array of numbers.  
> - **Linear algebra** provides the mathematical tools for working with them and is fundamental in computer graphics.

---

### Dimensions

A matrix has **N rows** and **M columns**, written as *N×M*.

> [!example]
> - A 2×2 matrix acts on 2D vectors.  
> - A 3×3 matrix acts on 3D vectors (or 2D affine transformations).  
> - Most transformation matrices are **square** (*N = M*).

If:
$$A, B \in \mathbb{R}^{N\times N}$$
then:
$$C = AB \in \mathbb{R}^{N\times N}$$

If $A$ is $N×N$ and $v$ is a vector with $N$ components,  
then $A v$ is a new vector — the result of transforming $v$.

---

### Linear Transformations

> [!note]
> A **linear transformation** maps vectors to other vectors using a matrix:
> $$
> T(v) = A v
> $$
> It always preserves the origin and straight lines.

> [!example]
> - **Rotation** and **scaling** are linear transformations.  
> - **Translation** is *not* linear (it moves the origin).

---

### Associativity of Transformations

Matrix multiplication is **associative**:
$$
(AB)v = A(Bv)
$$

> [!tip]
> You can first apply $B$ to $v$, then apply $A$ — or compute the combined matrix $AB$ once and apply it directly.  
> The result is identical.

---

### Example: Rotation and Scaling

Let:
$$
R_d = 
\begin{bmatrix}
\cos d & -\sin d \\
\sin d & \cos d
\end{bmatrix},
\qquad
S_{a,b} =
\begin{bmatrix}
a & 0 \\
0 & b
\end{bmatrix}
$$

Applying both:
$$
R_d(S_{a,b}v) = (R_d S_{a,b})v
$$

> [!note]
> The computer can **precompute** the combined matrix $R_d S_{a,b}$  
> and apply it once to every vertex — efficient for thousands of points.

---

### Translation and Homogeneous Coordinates

Translation cannot be expressed as a 2×2 linear transform.  
To include translation, we use **homogeneous coordinates**:

$$
v =
\begin{bmatrix}
x \\ y \\ 1
\end{bmatrix}
$$

Then all **affine transformations** (translation, rotation, scaling) fit in a single **3×3 matrix**:

---

### Affine Transformation Matrices (2D)

> [!important]
> - Each affine transformation can be expressed as:

#### Translation by $(e, f)$
$$
T_{e,f} =
\begin{bmatrix}
1 & 0 & e \\
0 & 1 & f \\
0 & 0 & 1
\end{bmatrix}
$$

#### Scaling by $(a, d)$
$$
S_{a,d} =
\begin{bmatrix}
a & 0 & 0 \\
0 & d & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

#### Rotation by angle $r$
$$
R_r =
\begin{bmatrix}
\cos r & -\sin r & 0 \\
\sin r & \cos r & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

> [!tip]
> Multiplying $(x, y, 1)$ by one of these matrices gives $(x', y', 1)$ —  
> you can ignore the last 1 after the transformation.

---

### Combining Transformations

> [!info]
> Multiple transformations can be **chained** by multiplying their matrices:
> $$
> M_{\text{total}} = T_{e,f} \cdot R_r \cdot S_{a,d}
> $$
> The resulting single matrix represents the **current transformation** —  
> equivalent to performing each operation in sequence.

> [!note]
> - The **order** matters: matrix multiplication is **not commutative**.  
> - Transformations written last in code are **applied first geometrically**.

---

### Key Idea

- All geometric transformations (rotation, scaling, translation) can be represented by **matrix multiplication**.  
- Transform sequences are **combined** into a single matrix to optimize performance.  
- In most graphics APIs, the **current transformation matrix (CTM)** stores this accumulated transform and applies it to all subsequent drawing operations.

