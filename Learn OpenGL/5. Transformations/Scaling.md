> [!summary]  
> **Scaling** changes the **size** (magnitude) of a vector or object along each axis without altering its direction.  
> In matrix form, it’s done by modifying the **diagonal values** of the identity matrix.

---

### 1. Concept

Scaling a vector $\vec{v} = (x, y, z)$ by $(S_1, S_2, S_3)$ gives:

$$  
\vec{v}' = (S_1x,\ S_2y,\ S_3z)  
$$

- **Uniform scale:** all $S_i$ equal
    
- **Non-uniform scale:** different $S_i$ values per axis
    

Example:  
If $\vec{v} = (3, 2, 0)$ and scaling vector $\vec{s} = (0.5, 2, 1)$:

$$  
\vec{v}' = (1.5, 4, 0)  
$$

---

### 2. Scaling Matrix (4×4)

The scaling transformation matrix is:

$$  
S =  
\begin{pmatrix}  
S_1 & 0 & 0 & 0 \  
0 & S_2 & 0 & 0 \  
0 & 0 & S_3 & 0 \  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

Applied to a vector $(x, y, z, 1)$:

# $$  
S \cdot  
\begin{pmatrix}  
x \ y \ z \ 1  
\end{pmatrix}

\begin{pmatrix}  
S_1x \ S_2y \ S_3z \ 1  
\end{pmatrix}  
$$

---

### 3. Example

$$  
S =  
\begin{pmatrix}  
0.5 & 0 & 0 & 0 \  
0 & 2 & 0 & 0 \  
0 & 0 & 1 & 0 \  
0 & 0 & 0 & 1  
\end{pmatrix},  
\quad  
\vec{v} =  
\begin{pmatrix}  
3 \ 2 \ 0 \ 1  
\end{pmatrix}  
$$

Then:

$$  
S \cdot \vec{v} =  
\begin{pmatrix}  
1.5 \ 4 \ 0 \ 1  
\end{pmatrix}  
$$

---

> [!info]
> 
> - Scaling affects **object size**, not **position** or **rotation**.
>     
> - Non-uniform scaling can distort shapes (e.g., stretch along one axis).
>     
> - The **w-component** (last element) remains **1** — used later for perspective division.
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat4 model = glm::mat4(1.0f);
>     model = glm::scale(model, glm::vec3(0.5f, 2.0f, 1.0f));
>     ```
>