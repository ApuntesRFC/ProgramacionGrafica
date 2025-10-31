> [!summary]  
> When the render loop finishes, you must call **`glfwTerminate()`** to properly free all GLFW-allocated resources and close the application cleanly.  
> Seeing a **plain black window** means everything is working as expected.

---

### Step — Cleanup and Exit

```cpp
// Render loop
while (!glfwWindowShouldClose(window)) {
    glfwSwapBuffers(window);
    glfwPollEvents();
}

// Clean up and close
glfwTerminate();
return 0;
```

---

### Explanation

- `glfwTerminate()`:
    
    - Frees all resources allocated by GLFW (window, context, event handlers, etc.).
        
    - Should be called **once**, after the render loop.
        
    - Prevents memory leaks or dangling OS handles.
        

> [!note]  
> A **black screen** means the OpenGL context is running correctly; you simply haven’t drawn anything yet.

---

### Common Issues to Check

> [!warning]  
> If the program doesn’t compile or run:
> 
> - Ensure **GLFW** and **GLAD** are correctly **linked** in CMake.
>     
> - Include the correct **header directories** (`glad/include`, `GLFW/include`).
>     
> - Call `glfwMakeContextCurrent(window)` **before** `gladLoadGLLoader`.
>     
> - Request **OpenGL 3.3 Core Profile** with `glfwWindowHint`.
>     
> - On **macOS**, add:
>     
>     ```cpp
>     glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
>     ```
>     

---

### Expected Output

> [!info]  
> If you see an empty black window that stays open until you close it manually — **you did everything right**.  
> The setup is complete and ready for rendering in the next steps.