> [!summary]  
> OpenGL requires all final vertex positions to lie within **Normalized Device Coordinates (NDC)** — each component (x, y, z) must be between **−1 and 1**.  
> To reach NDC, vertices are transformed step-by-step through several **coordinate spaces**, each simplifying specific operations.

---

### 1. The Coordinate Transformation Pipeline

Each vertex moves through **five spaces** before reaching the screen:

|Stage|Name|Purpose|
|---|---|---|
|1|**Local (Object) Space**|Vertex positions as modeled — relative to the object’s origin.|
|2|**World Space**|Object placed in the global scene using the **model matrix**.|
|3|**View (Eye) Space**|Scene viewed from the camera’s point of view using the **view matrix**.|
|4|**Clip Space**|Coordinates transformed by the **projection matrix** (perspective or orthographic).|
|5|**Screen Space**|Final 2D pixel positions after the GPU performs **perspective division** and **viewport transform**.|

---

### 2. Why Multiple Spaces?

Each coordinate space isolates a transformation’s purpose:

1. **Model Matrix (`M`)** → moves object from _local_ to _world space_.
    
2. **View Matrix (`V`)** → positions camera and converts world to _view space_.
    
3. **Projection Matrix (`P`)** → maps 3D view to _clip space_ (visible cube −1→1).
    

The total transformation:  
$$  
\text{ClipCoords} = P \cdot V \cdot M \cdot \text{LocalCoords}  
$$

---

### 3. Normalized Device Coordinates (NDC)

After the projection step, the vertex shader outputs:  
$$
\text{gl\_Position} = (x, y, z, w) 
$$

The GPU then divides by `w` to get:  
$$  
(x', y', z') = \left( \frac{x}{w}, \frac{y}{w}, \frac{z}{w} \right)  
$$  
where each component must be within **[−1, 1]** to be visible.

---

### 4. From Clip Space to Screen Space

Finally, NDC are mapped to screen pixels via the **viewport transform**:  
$$  
x_{screen} = \frac{(x_{ndc} + 1)}{2} \times \text{width}  
$$  
$$  
y_{screen} = \frac{(y_{ndc} + 1)}{2} \times \text{height}  
$$

---

> [!info]
> 
> - The three matrices `model`, `view`, and `projection` are combined as:
>     
>     ```cpp
>     glm::mat4 MVP = projection * view * model;
>     gl_Position = MVP * vec4(aPos, 1.0);
>     ```
>     
> - Order matters: OpenGL uses **column-major** matrices, so transformations apply **right-to-left**.
>     
> - Each space enables a different logical step:
>     
>     - _Model_ → shape of object.
>         
>     - _World_ → position in scene.
>         
>     - _View_ → camera perspective.
>         
>     - _Projection_ → map visible region to screen cube.
>