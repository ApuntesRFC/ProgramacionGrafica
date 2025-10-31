> [!summary]  
> **Goal:** build **model**, **view**, **projection** and send them per-frame to draw real 3D.  
> Core recipe: enable depth test, compute MVP, upload as uniforms, draw.

---

### Theory (concise)

- **Model**: local → world. Combines scale, rotation, translation.  
    Example: tilt plane on X so it lies like a floor:
    
    ```cpp
    glm::mat4 model(1.0f);
    model = glm::rotate(model, glm::radians(-55.0f), glm::vec3(1,0,0));
    ```
    
- **View**: world → camera. Move scene **opposite** to how you want the camera to move.
    
    ```cpp
    glm::mat4 view(1.0f);
    view = glm::translate(view, glm::vec3(0,0,-3)); // appears as moving camera back
    ```
    
- **Projection**: camera → clip. Use perspective for 3D.
    
    ```cpp
    glm::mat4 projection =
        glm::perspective(glm::radians(45.0f), 800.0f/600.0f, 0.1f, 100.0f);
    ```
    
- **Vertex shader** multiplies **right-to-left**:
    
    ```glsl
    // GLSL 330 core
    layout (location = 0) in vec3 aPos;
    uniform mat4 model, view, projection;
    void main() {
        gl_Position = projection * view * model * vec4(aPos, 1.0);
    }
    ```
    

---

### Easy explanation

- Think “place the object” (**model**), “place the camera” (**view**), “apply perspective” (**projection**).
    
- OpenGL rasterizes only what lands in **NDC** (−1..1). Projection maps your scene into that box.
    

---

### Practice (step-by-step)

1. **Once at init**
    
    ```cpp
    glEnable(GL_DEPTH_TEST);
    ```
    
2. **Each frame**
    
    ```cpp
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    
    glm::mat4 model(1.0f);
    model = glm::rotate(model, glm::radians(-55.0f), glm::vec3(1,0,0));
    
    glm::mat4 view(1.0f);
    view = glm::translate(view, glm::vec3(0,0,-3));
    
    glm::mat4 projection =
        glm::perspective(glm::radians(45.0f), width/(float)height, 0.1f, 100.0f);
    
    shader.use();
    glUniformMatrix4fv(glGetUniformLocation(shader.ID,"model"), 1, GL_FALSE, glm::value_ptr(model));
    glUniformMatrix4fv(glGetUniformLocation(shader.ID,"view"),  1, GL_FALSE, glm::value_ptr(view));
    glUniformMatrix4fv(glGetUniformLocation(shader.ID,"projection"), 1, GL_FALSE, glm::value_ptr(projection));
    
    glBindVertexArray(VAO);
    glDrawElements(GL_TRIANGLES, indexCount, GL_UNSIGNED_INT, 0);
    ```
    
3. **On resize**
    
    ```cpp
    glViewport(0, 0, width, height);
    // and rebuild 'projection' with new aspect = width/height
    ```
    

---

### Example checklist (what you should see)

- Plane is **tilted back** (−55° around X).
    
- Plane is **visible** because the view moved it “away”.
    
- **Perspective** is evident: farther parts look smaller.
    
- No z-fighting between front/back faces because **depth testing** is on.
    

---

> [!info]
> 
> - OpenGL Core 3.3+: use **VAO/VBO**, GLSL `#version 330 core`, no fixed pipeline.
>     
> - **Depth**: always clear depth each frame; default depth func is `GL_LESS`, which is fine here.
>     
> - **Handedness**: world/view are commonly treated as right-handed; after perspective, clip/NDC behaves effectively left-handed in Z due to perspective division. This is expected.
>     
> - **Order matters**: compute `projection * view * model * vec4`. Changing order changes the result.
>     
> - **Stability**: near plane too large (e.g., 10.0) clips close geometry; too small (e.g., 0.0001) hurts depth precision. Typical: `near=0.1`, `far=100.0`.
>     
> - **Performance**: uniforms every frame are fine now; later consider **UBOs** for view/projection shared across objects.
>     
> - **GLM defaults**: column-major matrices, radians APIs. Use `glm::radians()`.
>