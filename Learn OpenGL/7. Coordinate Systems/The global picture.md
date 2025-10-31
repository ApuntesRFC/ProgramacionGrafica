> [!summary]  
> Transforming vertices from **local space** to **screen space** is done step-by-step using three key matrices: **model**, **view**, and **projection**.  
> Each stage converts coordinates into a space where certain operations are simpler or more meaningful.

---

### 1. The Full Transformation Pipeline

Each vertex passes through five coordinate spaces:

|Step|Name|Purpose|Transformation|
|---|---|---|---|
|1|**Local (Object) Space**|Vertex positions relative to the object’s own origin.|—|
|2|**World Space**|Object placed into the global scene.|`model` matrix|
|3|**View (Eye) Space**|Scene transformed to the camera’s point of view.|`view` matrix|
|4|**Clip Space**|Vertices projected to the canonical cube (−1 → +1).|`projection` matrix|
|5|**Screen Space**|Final pixel coordinates after viewport transform.|`glViewport`|

---

### 2. Transformation Sequence

Combined mathematically:  
$$  
\text{ClipCoords} = P \cdot V \cdot M \cdot \text{LocalCoords}  
$$

Then, after the GPU performs **perspective division**:  
$$  
\text{NDC} = \frac{\text{ClipCoords.xyz}}{\text{ClipCoords.w}}  
$$

Finally, **viewport transform** converts NDC to pixel coordinates.

---

### 3. Summary of Each Space

#### a. Local Space

- Coordinates as originally modeled (e.g., a cube centered at origin).
    
- Local origin $(0,0,0)$ is the object’s pivot point.
    
- Used for modeling and animation.
    

#### b. World Space

- After applying the **model matrix**, the object is positioned, rotated, and scaled within the scene.
    
- All objects share the same world origin.
    

#### c. View Space

- Transformed so the camera is at the origin, facing the −Z direction.
    
- Created using the **view matrix**, often via `glm::lookAt`.
    

#### d. Clip Space

- Output of the **projection matrix** (perspective or orthographic).
    
- Defines what part of the scene is visible (inside the clip volume).
    
- Anything outside (x, y, z ∉ [−w, w]) is clipped.
    

#### e. Screen Space

- GPU maps NDC [−1, 1] → viewport (in pixels).
    
- Final result sent to the rasterizer → fragments → pixels.
    

---

### 4. Why Step-by-Step?

> [!info]
> 
> - Each transformation isolates a specific concern:
>     
>     - **Model** → individual object manipulation.
>         
>     - **View** → camera and scene positioning.
>         
>     - **Projection** → visible volume and perspective.
>         
> - Though we could merge them into a single matrix, separating them increases **flexibility** and **readability**.
>     
> - The combined MVP matrix used in shaders is:
>     
>     ```cpp
>     glm::mat4 mvp = projection * view * model;
>     gl_Position = mvp * vec4(aPos, 1.0);
>     ```
>