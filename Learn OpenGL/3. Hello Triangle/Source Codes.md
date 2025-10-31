
# Hello Triangle

```c++
#include <glad/glad.h>  
#include <GLFW/glfw3.h>  
#include <iostream>  
  
  
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
  
  
    /*--------------------------CREATE THE VERTICES--------------------------*/  
    float vertices[]={  
        -0.5, -0.5f, 0.0f,  
        0.5f, -0.5f, 0.0f,  
        0.0f,  0.5f, 0.0f  
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
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);  
    glEnableVertexAttribArray(0);  
  
  
    /*---------------------------UNBIND FOR SAFETY---------------------------*/  
    glBindBuffer(GL_ARRAY_BUFFER, 0);  
    glBindVertexArray(0);  
  
    /*----------------------SET UP THE VERTEX SHADER-------------------------*/  
    const char* vertexShaderSource = "#version 330 core\n"  
                             "layout (location = 0) in vec3 aPos;\n"  
                             "void main()\n"  
                             "{\n"  
                             "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"  
                             "}\0";  
    unsigned int vertexShader = glCreateShader(GL_VERTEX_SHADER);  
    glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);  
    glCompileShader(vertexShader);  
    int success;  
  
    glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;  
    }  
  
  
    /*---------------------SET UP THE FRAGMENT SHADER-------------------------*/  
    const char* fragmentShaderSource = "#version 330 core\n"  
                                   "out vec4 FragColor;\n"  
                                   "void main()\n"  
                                   "{\n"  
                                   "   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"  
                                   "}\0";  
    unsigned int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);  
    glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);  
    glCompileShader(fragmentShader);  
    success = 0;  
    glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;  
    }  
  
  
    /*-------------------LINK THE SHADERS INTO A PROGRAM-----------------------*/  
    unsigned int shaderProgram = glCreateProgram();  
    glAttachShader(shaderProgram, vertexShader);  
    glAttachShader(shaderProgram, fragmentShader);  
    glLinkProgram(shaderProgram);  
  
    glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;  
    }  
    glDeleteShader(vertexShader);  
    glDeleteShader(fragmentShader);  
  
    /*--------------------------------REPEATING LOOP-------------------------*/  
    while (!glfwWindowShouldClose(window)) {  
        processInput(window);  
  
        glClearColor(0.3f, 0.5f, 0.7f, 1.0f);  
        glClear(GL_COLOR_BUFFER_BIT);  
  
        glUseProgram(shaderProgram);  
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

# Hello Square 
``
```c++
#include <glad/glad.h>  
#include <GLFW/glfw3.h>  
#include <iostream>  
  
  
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
  
  
    /*--------------------------CREATE THE VERTICES--------------------------*/  
    float vertices[]={  
        -0.5, -0.5f, 0.0f,  
        0.5f, -0.5f, 0.0f,  
        0.5f,  0.5f, 0.0f,  
        -0.5f, 0.5f, 0.0f  
    };  
  
    unsigned int indices[] = {  
        0, 1, 2,  
        2, 3, 0  
    };  
  
  
    /*---------------------------CREATE A VAO--------------------------------*/  
    unsigned int VAO;  
    glGenVertexArrays(1, &VAO);  
    glBindVertexArray(VAO);  
  
  
    /*-----------------------------CREATE A VBO------------------------------*/  
    unsigned int VBO;  
    glGenBuffers(1, &VBO);  
    glBindBuffer(GL_ARRAY_BUFFER, VBO);  
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);  
  
  
    /*---------------------------CREATE A EBO--------------------------------*/  
    unsigned int EBO;  
    glGenBuffers(1, &EBO);  
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);  
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);  
  
  
    /*--------CONFIGURATIONS OF THE VBO AND ATTRIBUTES------------------------*/  
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);  
    glEnableVertexAttribArray(0);  
  
  
    /*---------------------------UNBIND FOR SAFETY---------------------------*/  
    glBindVertexArray(0);  
  
    /*----------------------SET UP THE VERTEX SHADER-------------------------*/  
    const char* vertexShaderSource = "#version 330 core\n"  
                             "layout (location = 0) in vec3 aPos;\n"  
                             "void main()\n"  
                             "{\n"  
                             "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"  
                             "}\0";  
    unsigned int vertexShader = glCreateShader(GL_VERTEX_SHADER);  
    glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);  
    glCompileShader(vertexShader);  
    int success;  
  
    glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;  
    }  
  
  
    /*---------------------SET UP THE FRAGMENT SHADER-------------------------*/  
    const char* fragmentShaderSource = "#version 330 core\n"  
                                   "out vec4 FragColor;\n"  
                                   "void main()\n"  
                                   "{\n"  
                                   "   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"  
                                   "}\0";  
    unsigned int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);  
    glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);  
    glCompileShader(fragmentShader);  
    success = 0;  
    glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;  
    }  
  
  
    /*-------------------LINK THE SHADERS INTO A PROGRAM-----------------------*/  
    unsigned int shaderProgram = glCreateProgram();  
    glAttachShader(shaderProgram, vertexShader);  
    glAttachShader(shaderProgram, fragmentShader);  
    glLinkProgram(shaderProgram);  
  
    glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);  
    if (!success) {  
        char infoLog[512];  
        glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);  
        std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;  
    }  
    glDeleteShader(vertexShader);  
    glDeleteShader(fragmentShader);  
  
    /*--------------------------------REPEATING LOOP-------------------------*/  
    while (!glfwWindowShouldClose(window)) {  
        processInput(window);  
  
        glClearColor(0.3f, 0.5f, 0.7f, 1.0f);  
        glClear(GL_COLOR_BUFFER_BIT);  
  
        glUseProgram(shaderProgram);  
        glBindVertexArray(VAO);  
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);  
  
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