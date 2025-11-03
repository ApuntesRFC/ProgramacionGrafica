> [!summary]  
> The **length** (or **magnitude**) of a vector is found using the **Pythagorean theorem**.  
> A **unit vector** is a normalized vector — same direction, length = 1.

---

### 1. Vector Length

For a vector  
$$  
\vec{v} =  
\begin{pmatrix}  
x \\ y  
\end{pmatrix}  
\quad \text{or} \quad  
\begin{pmatrix}  
x \\ y \\ z  
\end{pmatrix}  
$$

the **magnitude** is:

#### 2D:

$$  
||\vec{v}|| = \sqrt{x^2 + y^2}  
$$

#### 3D:

$$  
||\vec{v}|| = \sqrt{x^2 + y^2 + z^2}  
$$

---

### 2. Example

For  
$$  
\vec{v} = (4, 2)  
$$

we get:

$$  
||\vec{v}|| = \sqrt{4^2 + 2^2} = \sqrt{16 + 4} = \sqrt{20} \approx 4.47  
$$

---

### 3. Normalization (Unit Vector)

To create a **unit vector** $\hat{n}$ pointing in the same direction as $\vec{v}$:

$$  
\hat{n} = \frac{\vec{v}}{||\vec{v}||}  
$$

So if $\vec{v} = (4, 2)$:

$$  
\hat{n} = \frac{1}{4.47} (4, 2) = (0.894, 0.447)  
$$

Now:  
$$  
||\hat{n}|| = 1  
$$

---

> [!info]
>  - **Unit vectors** describe _direction only_ — magnitude is removed.
>
> - They are used extensively in lighting, physics, and movement systems.
  >  
 >- Normalization ensures stability in computations (e.g., dot and cross products).
>     
 >- In GLM:
>     
  >  ```cpp
>    glm::vec3 v(4.0f, 2.0f, 0.0f);
>     float len = glm::length(v);       // 4.47
>     glm::vec3 n = glm::normalize(v);  // (0.894, 0.447, 0)
>     




