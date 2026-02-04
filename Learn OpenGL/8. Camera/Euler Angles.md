> [!summary]  
> **Euler angles** are three values (pitch, yaw, roll) that can represent any rotation in 3D.  
> For our camera, we use **yaw** and **pitch** to compute a direction vector via trigonometry.

---

### 1. The Three Euler Angles

Defined by **Leonhard Euler** in the 1700s, the three Euler angles are:

- **Pitch**  how much we're looking up or down
    
- **Yaw**  how much we're looking left or right
    
- **Roll**  how much we roll (mostly used in space-flight cameras)
    

For our camera system, we only care about **yaw** and **pitch** (we won't discuss roll).

---

### 2. Converting Angles to Direction Vector

Given **pitch** and **yaw** values, we can convert them into a **3D direction vector**.

#### Basic Trigonometry Review

For a right triangle with hypotenuse of length 1:

- Adjacent side length = $\cos(\theta)$
    
- Opposite side length = $\sin(\theta)$
    

---

### 3. Computing Direction from Yaw

Looking from a **top perspective** (down the $y$-axis):

- The $x$ component relates to $\cos(\text{yaw})$
    
- The $z$ component relates to $\sin(\text{yaw})$
    

```cpp
glm::vec3 direction;
direction.x = cos(glm::radians(yaw)); // convert to radians first
direction.z = sin(glm::radians(yaw));
```

---

### 4. Adding Pitch

From the side view, the **$y$ component** equals $\sin(\text{pitch})$:

```cpp
direction.y = sin(glm::radians(pitch));
```

However, the **pitch** also affects the $x$ and $z$ components by $\cos(\text{pitch})$.

---

### 5. Final Direction Formula

Combining **yaw** and **pitch**:

```cpp
direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
direction.y = sin(glm::radians(pitch));
direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
```

This gives us the **3-dimensional direction vector** for looking around.

---

### 6. Default Yaw Value

The scene is set up so everything's positioned in the direction of the **negative $z$-axis**.

However, at $\theta = 0$, the camera points toward the **positive $x$-axis**.

To make the camera point toward the **negative $z$-axis** by default:

```cpp
yaw = -90.0f;
```

---

> [!info]
> 
> - Positive degrees rotate **counter-clockwise**, so we use **$-90$** for a clockwise rotation.
>     
> - This formula converts Euler angles into a direction vector we can use for the camera's front vector.
>     
> - The next section shows how to obtain and modify these yaw and pitch values using mouse input.
