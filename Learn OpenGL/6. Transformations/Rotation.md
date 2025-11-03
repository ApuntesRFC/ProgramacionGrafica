> [!summary]  
> **Rotation** transforms a vector’s direction around an axis by a given **angle** θ.  
> Rotations in 3D use **sine** and **cosine** to compute new coordinates, and are represented by **4×4 matrices** in OpenGL.

---

### 1. Basic Concept

- A **rotation** changes the direction of a vector while keeping its length (magnitude) constant.
    
- Defined by:
    
    - An **angle** θ (in degrees or radians)
        
    - A **rotation axis** (e.g., X, Y, or Z)
        

Conversion:  
$$  
\text{radians} = \text{degrees} \times \frac{\pi}{180}  
$$

---

### 2. Rotation Matrices (4×4)

#### Around X-axis:

$$  
R_x(\theta) =  
\begin{pmatrix}  
1 & 0 & 0 & 0 \\  
0 & \cos\theta & -\sin\theta & 0 \\  
0 & \sin\theta & \cos\theta & 0 \\ 
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

#### Around Y-axis:

$$  
R_y(\theta) =  
\begin{pmatrix}  
\cos\theta & 0 & \sin\theta & 0 \\  
0 & 1 & 0 & 0 \\  
-\sin\theta & 0 & \cos\theta & 0 \\  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

#### Around Z-axis:

$$  
R_z(\theta) =  
\begin{pmatrix}  
\cos\theta & -\sin\theta & 0 & 0 \\  
\sin\theta & \cos\theta & 0 & 0 \\  
0 & 0 & 1 & 0 \\  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

Each one rotates the object around its respective axis by the angle θ.

---

### 3. Arbitrary Axis Rotation

To rotate around an arbitrary unit axis $(R_x, R_y, R_z)$:

$$  
R(\theta) =  
\begin{pmatrix}  
\cos\theta + R_x^2(1-\cos\theta) & R_xR_y(1-\cos\theta) - R_z\sin\theta & R_xR_z(1-\cos\theta) + R_y\sin\theta & 0 \\  
R_yR_x(1-\cos\theta) + R_z\sin\theta & \cos\theta + R_y^2(1-\cos\theta) & R_yR_z(1-\cos\theta) - R_x\sin\theta & 0 \\  
R_zR_x(1-\cos\theta) - R_y\sin\theta & R_zR_y(1-\cos\theta) + R_x\sin\theta & \cos\theta + R_z^2(1-\cos\theta) & 0 \\  
0 & 0 & 0 & 1  
\end{pmatrix}  
$$

This allows precise rotation around any direction in space.

---

### 4. Gimbal Lock

When combining sequential rotations (e.g., X → Y → Z), **two rotation axes can align**, causing the loss of one degree of freedom — this is **Gimbal Lock**.  
To avoid it, use **quaternions**, a more robust representation for rotation.

---

> [!info]
> 
> - Rotation matrices are **orthogonal**, meaning $R^{-1} = R^T$.
>     
> - Use radians in OpenGL and GLM (not degrees).
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat4 model = glm::mat4(1.0f);
>     model = glm::rotate(model, glm::radians(45.0f), glm::vec3(0.0f, 0.0f, 1.0f)); // rotate 45° around Z
>     ```
>     
> - **Quaternion rotations** completely avoid Gimbal Lock and are computationally more stable.
>