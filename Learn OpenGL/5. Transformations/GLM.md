> [!summary]  
> **GLM (OpenGL Mathematics)** is a **header-only** C++ math library that mirrors GLSL types and functions. No linking needed. Use it to build **matrices**, **vectors**, and common transforms, then pass them to your shaders as uniforms.

---

### Setup

```cpp
// Core GLM types
#include <glm/glm.hpp>
// Transform helpers: translate, rotate, scale, perspective, lookAt
#include <glm/gtc/matrix_transform.hpp>
// Convert GLM objects to raw pointers for OpenGL
#include <glm/gtc/type_ptr.hpp>
```

> [!info]  
> GLM is column-major by default, matching OpenGL. Use `glm::value_ptr(mat)` with `glUniformMatrix*fv`.

---

### Basic Translation Example

```cpp
glm::vec4 v(1.0f, 0.0f, 0.0f, 1.0f);

glm::mat4 M = glm::mat4(1.0f);                   // identity
M = glm::translate(M, glm::vec3(1.0f, 1.0f, 0)); // T(1,1,0)

glm::vec4 v2 = M * v;                            // (2,1,0,1)
std::cout << v2.x << v2.y << v2.z << '\n';
```

---

### Scale + Rotate (Z axis)

```cpp
glm::mat4 M = glm::mat4(1.0f);
M = glm::rotate(M, glm::radians(90.0f), glm::vec3(0,0,1)); // Rz(90°)
M = glm::scale(M, glm::vec3(0.5f));                        // S(0.5)
```

> [!tip]  
> Transformations apply **right → left**. Writing `rotate` then `scale` yields `M = R · S`.

---

### Vertex Shader: matrix uniform

```glsl
#version 330 core
layout (location=0) in vec3 aPos;
layout (location=1) in vec2 aTexCoord;

uniform mat4 uTransform;

out vec2 vTexCoord;

void main() {
    gl_Position = uTransform * vec4(aPos, 1.0);
    vTexCoord = aTexCoord;
}
```

---

### Sending the Matrix to the Shader

```cpp
// build M with GLM...
GLint loc = glGetUniformLocation(programID, "uTransform");
glUniformMatrix4fv(loc, 1, GL_FALSE, glm::value_ptr(M));
```

- `count = 1` matrix.
    
- `transpose = GL_FALSE` for GLM defaults.
    
- `value_ptr` exposes contiguous float data.
    

---

### Animate: rotate over time and move to bottom-right

```cpp
glm::mat4 M = glm::mat4(1.0f);
M = glm::translate(M, glm::vec3(0.5f, -0.5f, 0.0f));        // T
M = glm::rotate(M, (float)glfwGetTime(), glm::vec3(0,0,1)); // Rz(t)

glUniformMatrix4fv(loc, 1, GL_FALSE, glm::value_ptr(M));
```

> [!note]  
> Even though code shows `translate` then `rotate`, the applied order is **rotate then translate** because of right-to-left multiplication.

---

### Common Pitfalls

> [!warning]
> 
> - Forgetting `glm::mat4(1.0f)` initializes to **identity**; default-constructing leaves elements uninitialized in newer GLM versions.
>     
> - Using degrees directly. Always use `glm::radians(deg)`.
>     
> - Passing `transpose = GL_TRUE` by mistake. Keep `GL_FALSE` with GLM defaults.
>     
> - Non-unit rotation axis. Normalize if not axis-aligned.
>     

---

### Quick Reference

- Identity: `glm::mat4(1.0f)`
    
- Translate: `glm::translate(M, vec3)`
    
- Rotate: `glm::rotate(M, radians(angle), axis)`
    
- Scale: `glm::scale(M, vec3)`
    
- Raw pointer: `glm::value_ptr(M)`