> [!summary]  
> The **identity matrix** is the foundational transformation in linear algebra and OpenGL.  
> It leaves any vector **unchanged** and acts as the **neutral element** of matrix multiplication.

---

### 1. Definition

A **4×4 identity matrix** looks like:

$$  
I =  
\begin{pmatrix}  
1 & 0 & 0 & 0 \\  
0 & 1 & 0 & 0 \\  
0 & 0 & 1 & 0 \\  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

All diagonal elements are **1**, all others are **0**.

---

### 2. Effect on a Vector

Multiplying $I$ by any 4D vector leaves it **unchanged**:

 $$  
I \cdot  
\begin{pmatrix}  
1 \\ 2 \\ 3 \\ 4  
\end{pmatrix}

\begin{pmatrix}  
1 \\ 2 \\ 3 \\ 4  
\end{pmatrix}  
$$

This is because each row of $I$ only multiplies one vector component by 1, and all others by 0.

---

### 3. Properties

- **Neutral element:**  
    $$  
    I·A = A·I = A  
    $$
    
- **Used as a base:**  
    Transformations (translation, rotation, scaling) often start from the identity matrix, then modified.
    
- **Invertible:**  
    The inverse of $I$ is itself ($I^{-1} = I$).
    

---

> [!info]
> 
> - OpenGL uses **4×4 identity matrices** for homogeneous coordinates $(x, y, z, 1)$ to handle translations.
>     
> - Starting every transformation from the identity ensures predictable results.
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat4 I = glm::mat4(1.0f); // creates the 4x4 identity matrix
>     glm::vec4 v(1.0f, 2.0f, 3.0f, 1.0f);
>     glm::vec4 result = I * v; // unchanged
>     ```
>