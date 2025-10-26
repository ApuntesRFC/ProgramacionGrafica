> [!summary]  
> **Vectors** in GLSL are containers that hold 1–4 components of the same base type (`float`, `int`, `bool`, etc.).  
> They are heavily used for **positions**, **colors**, **directions**, and **texture coordinates**.

---

### Vector Types Overview

|Type Prefix|Meaning|Example|
|---|---|---|
|`vecn`|Vector of _n_ floats|`vec2`, `vec3`, `vec4`|
|`bvecn`|Vector of _n_ booleans|`bvec3`|
|`ivecn`|Vector of _n_ integers|`ivec4`|
|`uvecn`|Vector of _n_ unsigned ints|`uvec2`|
|`dvecn`|Vector of _n_ doubles|`dvec3`|

> [!note]  
> The **most common** vector type in OpenGL shaders is `vec3`, used for positions, colors, and normals.

---

### Accessing Components

```glsl
vec4 position = vec4(1.0, 2.0, 3.0, 1.0);

float x = position.x;  // first component
float y = position.y;  // second component
float z = position.z;  // third component
float w = position.w;  // fourth component
```

> [!info]  
> Component naming depends on context:
> 
> - Geometry → `.x, .y, .z, .w`
>     
> - Color → `.r, .g, .b, .a`
>     
> - Texture → `.s, .t, .p, .q`
>     

All three sets of names refer to the same underlying components.

---

### Swizzling (reordering / duplicating components)

> [!tip]  
> Swizzling lets you **rearrange or replicate** components to form new vectors.

```glsl
vec2 v1 = vec2(0.5, 0.7);
vec4 v2 = v1.xyxx;         // (0.5, 0.7, 0.5, 0.5)
vec3 v3 = v2.zyw;          // (0.5, 0.5, 0.5)
vec4 v4 = v1.xxxx + v3.yxzy;
```

Rules:

- You can use **up to 4 components**.
    
- You can only access existing components (e.g., no `.z` in `vec2`).
    
- Result type depends on how many letters you use.
    

---

### Vector Constructors

Vectors can be built from:

- **Scalars**
    
- **Other vectors**
    
- **Combinations of both**
    

```glsl
vec2 vA = vec2(0.5, 0.7);
vec4 vB = vec4(vA, 0.0, 0.0);     // (0.5, 0.7, 0.0, 0.0)
vec4 vC = vec4(vB.xyz, 1.0);      // (0.5, 0.7, 0.0, 1.0)
```

> [!example]  
> Combine scalars and vectors freely as long as total components match the target vector size.

---

### Practical Usage

|Use Case|Typical Vector Type|Example|
|---|---|---|
|Vertex position|`vec3`|`layout(location = 0) in vec3 aPos;`|
|Vertex color|`vec3` / `vec4`|`in vec4 aColor;`|
|Texture coordinates|`vec2`|`in vec2 aTexCoord;`|
|Normal direction|`vec3`|`in vec3 aNormal;`|

---

### Key Takeaways

> [!important]
> 
> - Vectors are **core data types** in GLSL for spatial and color math.
>     
> - Access components via `.xyzw`, `.rgba`, or `.stpq`.
>     
> - Use **swizzling** for rearranging or replicating components.
>     
> - Build new vectors via **constructors** or **mixed arguments**.
>     
> - GLSL’s vector syntax is compact and optimized for GPU parallelism.
>