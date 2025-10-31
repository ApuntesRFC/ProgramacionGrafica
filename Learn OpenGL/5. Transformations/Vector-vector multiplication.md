> [!summary]  
> **Vector multiplication** is defined in two distinct ways:
> 
> - **Dot Product ( $\vec{v} \cdot \vec{k}$ )** → scalar measuring **alignment**.
>     
> - **Cross Product ( $\vec{v} \times \vec{k}$ )** → vector **perpendicular** to both inputs.
>     

---

### 1. Dot Product

#### Formula

$$  
\vec{v} \cdot \vec{k} = ||\vec{v}|| , ||\vec{k}|| \cos(\theta)  
$$

where $\theta$ is the angle between $\vec{v}$ and $\vec{k}$.

If both vectors are **unit vectors**, then:  
$$  
\hat{v} \cdot \hat{k} = \cos(\theta)  
$$

#### Interpretation

- $= 1$ → vectors point in the same direction.
    
- $= 0$ → vectors are **orthogonal** (perpendicular).
    
- $= -1$ → vectors point in opposite directions.
    

#### Example

# $$  
\begin{pmatrix}  
0.6 \ -0.8 \ 0  
\end{pmatrix}  
\cdot  
\begin{pmatrix}  
0 \ 1 \ 0  
\end{pmatrix}

(0.6)(0) + (-0.8)(1) + (0)(0) = -0.8  
$$

To find the angle:  
$$  
\theta = \cos^{-1}(-0.8) \approx 143.1^\circ  
$$

---

### 2. Cross Product

#### Formula

$$  
\vec{v} \times \vec{k} =  
\begin{pmatrix}  
v_y k_z - v_z k_y \  
v_z k_x - v_x k_z \  
v_x k_y - v_y k_x  
\end{pmatrix}  
$$

The result is a **vector perpendicular** to both $\vec{v}$ and $\vec{k}$.

#### Magnitude

$$  
||\vec{v} \times \vec{k}|| = ||\vec{v}|| , ||\vec{k}|| \sin(\theta)  
$$

#### Example

For $\vec{A} = (1,0,0)$ and $\vec{B} = (0,1,0)$:  
$$  
\vec{A} \times \vec{B} = (0,0,1)  
$$

The result points along the **z-axis**, orthogonal to both inputs.

---

> [!info]
> 
> - **Dot product** → measures _how similar_ two directions are.
>     
> - **Cross product** → gives a _new orthogonal direction_ (used for normals, rotation axes).
>     
> - If vectors are parallel, the cross product becomes **(0, 0, 0)**.
>     
> - **Right-hand rule:** curling your right-hand fingers from $\vec{v}$ to $\vec{k}$, your thumb points in the direction of $\vec{v} \times \vec{k}$.
>     
> - In GLM:
>     
>     ```cpp
>     float d = glm::dot(v, k);       // scalar
>     glm::vec3 c = glm::cross(v, k); // perpendicular vector
>     ```

