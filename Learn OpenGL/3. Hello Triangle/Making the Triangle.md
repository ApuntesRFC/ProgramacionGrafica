> [!summary]  
> Draw with **`glDrawArrays`** using the active **shader program**, the bound **VAO** (which references your VBOs), and a chosen **primitive**. For one triangle: 3 vertices, starting at index 0.

---

### Minimal draw call

```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

---

### Parameters

|Param|Meaning|For the triangle|
|---|---|---|
|`mode`|Primitive type|`GL_TRIANGLES`|
|`first`|Start index in the enabled vertex arrays|`0`|
|`count`|Number of vertices to draw|`3`|

> [!note]  
> `count` is **vertex count**, not triangle count.

---

### Where it sits in the frame

```cpp
while (!glfwWindowShouldClose(window)) {
    processInput(window);

    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

    glUseProgram(shaderProgram);
    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

---

### Preconditions checklist

> [!important]
> 
> - VAO created and bound when calling `glVertexAttribPointer`.
>     
> - Attribute 0 enabled and matches shader: `layout(location=0) in vec3 aPos;`
>     
> - VBO contains 3 vertices (9 floats) and is uploaded with `glBufferData`.
>     
> - Shaders compiled and program linked with **no errors**.
>     
> - Correct context version: **OpenGL 3.3 Core**.
>     

---

### Common pitfalls

> [!warning]
> 
> - No VAO bound → nothing renders in Core Profile.
>     
> - Wrong `stride`/`offset` in `glVertexAttribPointer` → garbled geometry.
>     
> - Forgetting `glUseProgram` or `glBindVertexArray` each frame → no draw.
>