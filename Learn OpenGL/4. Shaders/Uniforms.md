> [!summary]  
> **Uniforms** are global shader variables that stay constant for all vertices or fragments of a single draw call.  
> You set them from your **CPU application**, and they keep their value until updated.

---

### What uniforms are

> [!info]
> 
> - Global per **shader program** (shared by all shader stages).
>     
> - Used for **dynamic parameters** like time, colors, or transformation matrices.
>     
> - Do **not** require vertex attributes.
>     
> - Persist until you explicitly change or reset them.
>     

---

### Declaring a uniform in GLSL

```glsl
#version 330 core

out vec4 FragColor;
uniform vec4 ourColor; // value set from CPU side

void main()
{
    FragColor = ourColor;
}
```

> [!note]  
> The uniform `ourColor` can be modified from your OpenGL code.  
> If it’s unused, the GLSL compiler optimizes it away — causing `glGetUniformLocation` to return `-1`.

---

### CPU side: setting the uniform

```cpp
float timeValue = glfwGetTime();                // running time in seconds
float greenValue = sin(timeValue) / 2.0f + 0.5f; // oscillates between 0 and 1

int colorLoc = glGetUniformLocation(shaderProgram, "ourColor"); // get location
glUseProgram(shaderProgram);                                      // activate shader
glUniform4f(colorLoc, 0.0f, greenValue, 0.0f, 1.0f);              // set color
```

|Step|Function|Description|
|---|---|---|
|1|`glGetUniformLocation`|Get uniform index in the linked shader.|
|2|`glUseProgram`|Activate the program before updating.|
|3|`glUniform*`|Send the data (float, int, matrix, etc.).|

---

### Type suffixes for `glUniform`

|Function|Expected type|Example|
|---|---|---|
|`glUniform1f`|`float`|brightness|
|`glUniform1i`|`int`|texture unit index|
|`glUniform4f`|four floats|RGBA color|
|`glUniformMatrix4fv`|matrix of floats|transform matrices|
|`glUniform3fv`|vector array|direction vectors|

> [!tip]  
> `glUniform4f(loc, r, g, b, a)`  
> or `glUniform4fv(loc, 1, &color[0])` both work — choose whichever matches your data layout.

---

### Updating every frame

Put your uniform update logic inside the **render loop** to animate it:

```cpp
while (!glfwWindowShouldClose(window)) {
    processInput(window);

    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

    glUseProgram(shaderProgram);

    float timeValue = glfwGetTime();
    float greenValue = sin(timeValue) / 2.0f + 0.5f;
    int colorLoc = glGetUniformLocation(shaderProgram, "ourColor");
    glUniform4f(colorLoc, 0.0f, greenValue, 0.0f, 1.0f);

    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

> [!example]  
> The result is a triangle that smoothly oscillates between **black and green** over time.

---

### Common uniform pitfalls

> [!warning]
> 
> - If `glGetUniformLocation` returns `-1`, the variable might be **optimized away** (not used in shader).
>     
> - Must call `glUseProgram` **before** setting uniforms.
>     
> - Uniform updates affect **only the currently active program**.
>     

---

### When to use uniforms

> [!important]  
> Use **uniforms** for:
> 
> - Values that are the same for all vertices/fragments in one draw (e.g., time, light color, camera position).
>     
> - Configuration that changes **per frame**, not **per vertex**.
>     
> 
> Use **vertex attributes** instead if you need a **different value per vertex**.