> [!summary]  
> Put all **rendering commands** inside the **render loop**. Clear the frame at the start using `glClearColor` + `glClear(GL_COLOR_BUFFER_BIT)` so old pixels don’t persist.

---

### Render loop skeleton

```cpp
// render loop
while (!glfwWindowShouldClose(window)) {
    // 1) input
    processInput(window);

    // 2) render
    // set clear color for this frame
    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    // clear color buffer
    glClear(GL_COLOR_BUFFER_BIT);

    // ... your draw calls here ...

    // 3) present + events
    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

---

### Clearing buffers

```cpp
// set the RGBA color used by future clears (state-setting)
glClearColor(r, g, b, a);

// clear selected buffers (state-using)
glClear(GL_COLOR_BUFFER_BIT /* | GL_DEPTH_BUFFER_BIT | GL_STENCIL_BUFFER_BIT */);
```

|Bit|Purpose|
|---|---|
|`GL_COLOR_BUFFER_BIT`|Clears color pixels.|
|`GL_DEPTH_BUFFER_BIT`|Clears depth values (enable when using depth testing).|
|`GL_STENCIL_BUFFER_BIT`|Clears stencil values.|

> [!note]  
> `glClearColor` sets state. `glClear` reads that state to fill the chosen buffers.

---

### Minimal, compilable context

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

void framebuffer_size_callback(GLFWwindow* w,int width,int height){ glViewport(0,0,width,height); }
void processInput(GLFWwindow* w){ if (glfwGetKey(w, GLFW_KEY_ESCAPE)==GLFW_PRESS) glfwSetWindowShouldClose(w, true); }

int main(){
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR,3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR,3);
    glfwWindowHint(GLFW_OPENGL_PROFILE,GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow* window = glfwCreateWindow(800,600,"Hello Window Clear",nullptr,nullptr);
    if(!window){ std::cout<<"Failed to create GLFW window\n"; glfwTerminate(); return -1; }
    glfwMakeContextCurrent(window);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    if(!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)){ std::cout<<"Failed to initialize GLAD\n"; return -1; }

    while(!glfwWindowShouldClose(window)){
        processInput(window);

        glClearColor(0.2f,0.3f,0.3f,1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glfwSwapBuffers(window);
        glfwPollEvents();
    }
    glfwTerminate();
    return 0;
}
```

> [!tip]  
> When you add 3D, also clear depth each frame:  
> `glEnable(GL_DEPTH_TEST); glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);`