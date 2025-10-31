> [!summary]  
> **Translation** moves a vector or object by adding a **displacement vector** $(T_x, T_y, T_z)$ to its position.  
> In a 4×4 matrix, translation values are stored in the **last column**.

---

### 1. Concept

Translation adds an offset to position:

$$  
\vec{v}' = \vec{v} + \vec{T}  
$$

So,  
$$  
(x, y, z) + (T_x, T_y, T_z) = (x + T_x, y + T_y, z + T_z)  
$$

---

### 2. Translation Matrix

In homogeneous coordinates, translation is defined as:

$$  
T =  
\begin{pmatrix}  
1 & 0 & 0 & T_x \  
0 & 1 & 0 & T_y \  
0 & 0 & 1 & T_z \  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

Applied to a vector $(x, y, z, 1)$:

# $$  
T \cdot  
\begin{pmatrix}  
x \ y \ z \ 1  
\end{pmatrix}

\begin{pmatrix}  
x + T_x \  
y + T_y \  
z + T_z \  
1  
\end{pmatrix}  
$$

---

### 3. Example

If  
$$  
\vec{v} =  
\begin{pmatrix}  
1 \ 2 \ 3 \ 1  
\end{pmatrix},  
\quad  
\vec{T} =  
\begin{pmatrix}  
4 \ -2 \ 1  
\end{pmatrix}  
$$

Then:

$$  
T \cdot \vec{v} =  
\begin{pmatrix}  
5 \ 0 \ 4 \ 1  
\end{pmatrix}  
$$

The object moved +4 on X, –2 on Y, and +1 on Z.

---

### 4. Homogeneous Coordinates

Homogeneous form $(x, y, z, w)$ enables translation using matrix multiplication.

- When $w = 1$: it represents a **position** (can be translated).
    
- When $w = 0$: it represents a **direction vector** (not affected by translation).
    

This distinction is key in 3D graphics, where directions (like normals) should not move.

---

> [!info]
> 
> - Translation needs a **4×4 matrix**; a 3×3 matrix can’t represent position shifts.
>     
> - Homogeneous coordinates make 3D transformations (rotation, scale, translation) **consistent**.
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat4 model = glm::mat4(1.0f);
>     model = glm::translate(model, glm::vec3(4.0f, -2.0f, 1.0f));
>     ```
>