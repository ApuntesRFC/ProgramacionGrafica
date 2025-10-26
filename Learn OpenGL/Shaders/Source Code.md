
## Shader Class ##

[[Reading from file#Class skeleton (header-only)|Shader Header Class]]

## Main Class

```c++
#include <glad/glad.h>  
#include <GLFW/glfw3.h>  
#include <iostream>  
#include "Shader.h"  
  
void framebuffer_size_callback(GLFWwindow* window, int width, int height);  
void processInput(GLFWwindow* window);  
  
  
int main() {  
  
    /*-------------------------Initialize GLFW------------------------------*/  
    if (!glfwInit()) {  
        std::cout << "Failed to initialize GLFW" << std::endl;  
    }  
  
  
    /*-----------------------SET THE CURRENT CONTEXT------------------------*/  
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);  
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);  
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);  
// MacOS compatibility  
#ifdef __APPLE__  
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);  
#endif  
  
  
    /*----------------------CREATE A WINDOW---------------------------------*/  
    GLFWwindow* window = glfwCreateWindow(800, 600, "OpenGL Test", nullptr, nullptr);  
    if (window == nullptr) {  
        std::cout << "Failed to create GLFW window" << std::endl;  
        glfwTerminate();  
        return -1;  
    }  
  
  
    /*----------------------USING THE WINDOW---------------------------------*/  
    glfwMakeContextCurrent(window);  
  
  
    /*-------------------------LOADING GLAD----------------------------------*/  
    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {  
        std::cout << "Failed to initialize GLAD" << std::endl;  
        return -1;  
    }  
  
  
    /*---------------------SETTING THE VIEWPORT-----------------------------*/  
    glViewport(0, 0, 800, 600);  
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);  
  
  
    /*----------------------HOW MANY VERTEX ATTRIBS--------------------------*/  
    int n_attribs;  
    glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &n_attribs);  
    std::cout << "Maximum number of vertex attributes supported: " << n_attribs << std::endl;  
  
    /*--------------------------CREATE THE VERTICES--------------------------*/  
    float vertices[] = {  
        // positions         // colors (RGB)  
        0.5f, -0.5f, 0.0f,   1.0f, 0.0f, 0.0f,  
       -0.5f, -0.5f, 0.0f,   0.0f, 1.0f, 0.0f,  
        0.0f,   0.5f, 0.0f,   0.0f, 0.0f, 1.0f  
   };  
  
  
    /*-----------------------------CREATE A VBO------------------------------*/  
    unsigned int VBO;  
    glGenBuffers(1, &VBO);  
    glBindBuffer(GL_ARRAY_BUFFER, VBO);  
  
  
    /*---------------------------CREATE A VAO--------------------------------*/  
    unsigned int VAO;  
    glGenVertexArrays(1, &VAO);  
    glBindVertexArray(VAO);  
  
  
    /*--------CONFIGURATIONS OF THE VBO AND ATTRIBUTES------------------------*/  
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);  
  
    const GLsizei stride = 6 * sizeof(float); // 3 position + 3 color  
  
    // Position attribute    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, stride, (void*)0);  
    glEnableVertexAttribArray(0);  
  
    // Color attribute (3 components: RGB)  
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, stride, (void*)(3 * sizeof(GLfloat)));  
    glEnableVertexAttribArray(1);  
  
    /*---------------------------UNBIND FOR SAFETY---------------------------*/  
    glBindBuffer(GL_ARRAY_BUFFER, 0);  
    glBindVertexArray(0);  
  
    /*---------------------------------USE SHADERS.H-------------------------*/  
    // Compare to Hello Triangle Code    Shader shaderProgram("../src/Shaders/basic.vert", "../src/Shaders/basic.frag");  
  
    /*--------------------------------REPEATING LOOP-------------------------*/  
    while (!glfwWindowShouldClose(window)) {  
        processInput(window);  
  
        glClearColor(0.3f, 0.5f, 0.7f, 1.0f);  
        glClear(GL_COLOR_BUFFER_BIT);  
  
        shaderProgram.use();  
        glBindVertexArray(VAO);  
        glDrawArrays(GL_TRIANGLES, 0, 3);  
  
        glfwSwapBuffers(window);  
        glfwPollEvents();  
    }  
  
    glfwTerminate();  
  
  
    return 0;  
}  
  
void processInput(GLFWwindow* window) {  
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {  
        glfwSetWindowShouldClose(window, true);  
    }  
}  
  
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {  
    glViewport(0, 0, width, height);  
}
```

## Vector shader

```glsl
#version 330 core  
layout (location = 0) in vec3 aPos;    // position  
layout (location = 1) in vec3 aColor;  // color  
out vec3 ourColor;                     // to fragment shader  
  
void main() {  
    gl_Position = vec4(aPos, 1.0);  
    ourColor = aColor;  
}
```

## Fragment shader

```glsl
#version 330 core  
in vec3 ourColor;  
out vec4 FragColor;  
  
void main() {  
    FragColor = vec4(ourColor, 1.0);  
}
```
