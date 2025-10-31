> [!summary]  
> **Matrix combination** allows multiple transformations — scaling, rotation, translation — to be expressed as a **single matrix** through **matrix multiplication**.  
> Order matters: transformations are applied **right to left**.

---

### 1. Concept

Combining transformations means multiplying their matrices:

$$  
M_{total} = T \cdot R \cdot S  
$$

When this is applied to a vector:

$$  
\vec{v}' = M_{total} \cdot \vec{v}  
$$

- **Scaling (S)** changes size.
    
- **Rotation (R)** changes orientation.
    
- **Translation (T)** changes position.
    

---

### 2. Example: Scale then Translate

We scale by 2 and translate by $(1, 2, 3)$:

#### Translation Matrix

$$  
T =  
\begin{pmatrix}  
1 & 0 & 0 & 1 \  
0 & 1 & 0 & 2 \  
0 & 0 & 1 & 3 \  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

#### Scaling Matrix

$$  
S =  
\begin{pmatrix}  
2 & 0 & 0 & 0 \  
0 & 2 & 0 & 0 \  
0 & 0 & 2 & 0 \  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

#### Combined Matrix

$$  
M = T \cdot S =  
\begin{pmatrix}  
2 & 0 & 0 & 1 \  
0 & 2 & 0 & 2 \  
0 & 0 & 2 & 3 \  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

---

### 3. Applying to a Vector

For $\vec{v} = (x, y, z, 1)$:

$$  
M \cdot \vec{v} =  
\begin{pmatrix}  
2x + 1 \  
2y + 2 \  
2z + 3 \  
1  
\end{pmatrix}  
$$

The result: scaled by 2, then shifted by $(1, 2, 3)$.

---

### 4. Order of Operations

Matrix multiplication is **not commutative**:

$$  
T \cdot S \ne S \cdot T  
$$

Transformations are applied **from right to left**:

|Order|Result|
|---|---|
|`S → T`|Scales first, then translates (correct)|
|`T → S`|Translates first, then scales translation (wrong)|

Recommended order:

> **Scale → Rotate → Translate**

---

> [!info]
> 
> - Combining matrices reduces per-vertex computation — a single multiply applies all transforms.
>     
> - Wrong order can distort motion or scale translation offsets.
>     
> - OpenGL itself has no math utilities; we use **GLM** for this:
>     
>     ```cpp
>     glm::mat4 model = glm::mat4(1.0f);
>     model = glm::translate(model, glm::vec3(1.0f, 2.0f, 3.0f));
>     model = glm::scale(model, glm::vec3(2.0f));
>     ```
>     
> - GLM multiplies matrices in **reverse order of declaration**, matching OpenGL’s column-major convention.

