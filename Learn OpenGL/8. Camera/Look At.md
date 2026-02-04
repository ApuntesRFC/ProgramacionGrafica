> [!summary]  
> The **LookAt matrix** builds a **view matrix** from a camera's position, target, and up vector.  
> GLM provides `glm::lookAt()` to create this matrix automatically.

---

### 1. LookAt Matrix Theory

A great property of matrices: if you define a coordinate space using **3 perpendicular axes** plus a **translation vector**, you can transform any vector to that coordinate space by multiplying it with this matrix.

This is exactly what the **LookAt matrix** does.

Given our camera vectors:

$$
\text{LookAt} = 
\begin{bmatrix}
R_x & R_y & R_z & 0 \\
U_x & U_y & U_z & 0 \\
D_x & D_y & D_z & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix}
1 & 0 & 0 & -P_x \\
0 & 1 & 0 & -P_y \\
0 & 0 & 1 & -P_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

Where:

- $R$ = **right** vector
    
- $U$ = **up** vector
    
- $D$ = **direction** vector
    
- $P$ = **camera position** vector
    

The rotation (left matrix) and translation (right matrix) are **inverted** (transposed and negated) because we want to rotate and translate the world in the **opposite direction** of where we want the camera to move.

---

### 2. Using GLM's LookAt

GLM does all this work for us. We only need to specify:

- A **camera position**
    
- A **target position**
    
- An **up vector** in world space
    

```cpp
glm::mat4 view;
view = glm::lookAt(
    glm::vec3(0.0f, 0.0f, 3.0f),  // camera position
    glm::vec3(0.0f, 0.0f, 0.0f),  // target position
    glm::vec3(0.0f, 1.0f, 0.0f)   // up vector
);
```

This creates a view matrix identical to the one from the previous chapter.

---

### 3. Example: Rotating Camera Around Scene

Before diving into user input, let's make the camera rotate around the scene automatically.

We'll use **trigonometry** to create $x$ and $z$ coordinates that represent a point on a circle:

```cpp
const float radius = 10.0f;
float camX = sin(glfwGetTime()) * radius;
float camZ = cos(glfwGetTime()) * radius;

glm::mat4 view = glm::lookAt(
    glm::vec3(camX, 0.0f, camZ),
    glm::vec3(0.0f, 0.0f, 0.0f),
    glm::vec3(0.0f, 1.0f, 0.0f)
);
```

By re-calculating the $x$ and $z$ coordinates over time, we traverse all points on a circle, making the camera rotate around the scene.

---

> [!info]
> 
> - The LookAt matrix **looks at a given target**  it creates a view matrix that orients the camera toward that point.
>     
> - Feel free to experiment with the **radius** and **position/direction** parameters to understand how LookAt works.
>     
> - Source code reference: `/src/1.getting_started/7.1.camera_circle/`
