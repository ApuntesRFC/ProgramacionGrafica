> [!summary]  
> A **vector** is mathematically equivalent to an **N×1 matrix**, and can therefore participate in **matrix multiplication**.  
> This relationship allows us to apply **transformations** (translation, rotation, scaling, etc.) to vectors using matrices.

---

### 1. Vector as a Matrix

A 3D vector:

$$  
\vec{v} =  
\begin{pmatrix}  
x \\  
y \\  
z  
\end{pmatrix}  
$$

is a **3×1 matrix**.

A transformation matrix (e.g., 3×3) can then multiply this vector:

$$  
\text{Matrix}_{(3×3)} · \text{Vector}_{(3×1)} = \text{Vector}_{(3×1)}  
$$

---

### 2. Why It Matters

By storing transformations (like rotation, scaling, projection) inside matrices,  
we can apply them to any vector simply via **matrix–vector multiplication**.

$$  
\vec{v}' = M · \vec{v}  
$$

Here:

- $M$ = transformation matrix
    
- $\vec{v}$ = original vector
    
- $\vec{v}'$ = transformed vector (new position, color, etc.)
    

---

### 3. Example (2D)

Given:

$$  
M =  
\begin{pmatrix}  
2 & 0 \\  
0 & 3  
\end{pmatrix},  
\quad  
\vec{v} =  
\begin{pmatrix}  
1 \\  
1  
\end{pmatrix}  
$$

Then:

 $$  
M·\vec{v} =  
\begin{pmatrix}  
2·1 + 0·1 \\  
0·1 + 3·1  
\end{pmatrix}

\begin{pmatrix}  
2 \\  
3  
\end{pmatrix}  
$$

This scales the vector by 2 in **x** and 3 in **y**.

---

> [!info]
> 
> - Treating vectors as matrices enables a **unified mathematical framework** for all transformations.
>     
> - In 3D graphics, we use **4×4 matrices** and **homogeneous coordinates** $(x, y, z, 1)$ to include translation.
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat3 transform = glm::mat3(2.0f, 0.0f, 0.0f,
>                                     0.0f, 3.0f, 0.0f,
>                                     0.0f, 0.0f, 1.0f);
>     glm::vec3 v(1.0f, 1.0f, 1.0f);
>     glm::vec3 result = transform * v; // (2, 3, 1)
>     ```
>