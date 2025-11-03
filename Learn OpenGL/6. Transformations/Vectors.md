> [!summary]  
> **Vectors** represent **direction and magnitude**. They’re fundamental for describing motion, orientation, and transformation in 2D/3D space. Unlike points, vectors don’t have a fixed position — only direction and length.

---

### 1. Definition

A **vector** $\vec{v}$ is an ordered set of components representing direction and magnitude.

$$  
\vec{v} =  
\begin{pmatrix}  
x \\  
y \\  
z  
\end{pmatrix}  
$$

- **2D vector:** $(x, y)$ → direction in a plane.
    
- **3D vector:** $(x, y, z)$ → direction in 3D space.
    
- **Magnitude (length):**  
    $$  
    |\vec{v}| = \sqrt{x^2 + y^2 + z^2}  
    $$
    

> [!info]  
> Vectors are independent of position — only their _orientation and scale_ matter.  
> Two equal arrows starting at different points represent the same vector.

---

### 2. Position vectors

If you treat the vector’s **tail** as the origin $(0,0,0)$, then the **head** represents a point in space.  
Example:

$$  
\vec{p} = (3, 5)  
$$

→ represents a point **at** (3,5) **from** the origin (0,0).

Thus, **a point** can be represented as **a vector from the origin to that point**.

---

### 3. Basic operations

#### Addition

$$  
\vec{a} + \vec{b} =  
\begin{pmatrix}  
a_x + b_x \  
a_y + b_y \  
a_z + b_z  
\end{pmatrix}  
$$

> Visually: place the tail of **b** at the head of **a**; the resulting vector goes from the start of **a** to the end of **b**.

#### Subtraction

$$  
\vec{a} - \vec{b} =  
\begin{pmatrix}  
a_x - b_x \\  
a_y - b_y \\  
a_z - b_z  
\end{pmatrix}  
$$

> Represents the vector _from b to a_.

#### Scalar multiplication

$$  
k \vec{v} =  
\begin{pmatrix}  
k x \\  
k y \\ 
k z  
\end{pmatrix}  
$$

> Multiplies the vector’s length by _k_.  
> If _k < 0_, it flips direction.

---

### 4. Normalization

To get a **unit vector** (length = 1):

$$  
\hat{v} = \frac{\vec{v}}{|\vec{v}|}  
$$

Used heavily in lighting, direction, and transformation calculations.

---

### 5. Visual intuition

- $\vec{v} = (1, 0)$ → points right.
    
- $\vec{v} = (0, 1)$ → points up.
    
- $\vec{v} = (-1, 0)$ → points left.
    
- $\vec{v} = (3, 5)$ → points to (3,5) from origin.
    

> [!tip]  
> In OpenGL, most vector operations are handled using **GLM** (`glm::vec2`, `glm::vec3`, etc.), which supports all these operations directly.

---

### Information Addendum
>[!info]
>- The **origin** of a vector doesn’t affect its properties; only direction and magnitude matter.
>     
> - In computer graphics, **positions** are treated as **vectors from origin** to the point.
>     
> - You’ll soon use these for **translation**, **rotation**, and **scaling** through **matrix transformations**.
>
