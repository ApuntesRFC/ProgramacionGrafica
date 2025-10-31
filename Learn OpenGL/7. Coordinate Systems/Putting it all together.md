> [!summary]  
> The complete coordinate transformation pipeline in OpenGL converts **local vertices** into **screen-space pixels** through a sequence of matrix operations.

---

### 1. Transformation Chain

Each vertex passes through **three main transformation matrices**:

$$  
V_{clip} = M_{projection} \times M_{view} \times M_{model} \times V_{local}  
$$

- **Model Matrix ($M_{model}$)** → Moves from _local/object_ space to _world_ space.
    
- **View Matrix ($M_{view}$)** → Moves from _world_ space to _camera/view_ space.
    
- **Projection Matrix ($M_{projection}$)** → Moves from _view_ space to _clip_ space.
    

> Matrix multiplication is read **right to left** — the model transform happens first.

---

### 2. After the Vertex Shader

1. **Clip-space output** → Sent from the vertex shader (`gl_Position`).
    
2. **Perspective Division** → Performed automatically by OpenGL:  
    $$  
    (x, y, z) ; \div ; w  
    $$  
    Converts clip coordinates → **Normalized Device Coordinates (NDC)** in [-1, 1].
    
3. **Viewport Transform** → Maps NDC to **screen coordinates** using `glViewport`.
    

---

### 3. Visualization

|Stage|Space|Matrix Used|Example Purpose|
|---|---|---|---|
|Local → World|Model|Position object in scene|Rotate/scale/translate cube|
|World → View|View|Simulate camera|Move scene opposite to camera|
|View → Clip|Projection|Apply perspective/orthographic|Define visible volume|
|Clip → NDC|Perspective Division|—|Normalize coordinates|
|NDC → Screen|Viewport Transform|—|Convert to pixel positions|

---

> [!info]
> 
> - The **vertex shader** must output coordinates in **clip space**.
>     
> - **Perspective division** and **viewport mapping** are handled **automatically** by OpenGL.
>     
> - Together, these transformations form the foundation of the **3D rendering pipeline**.
>