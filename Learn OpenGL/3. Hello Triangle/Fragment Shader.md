> [!summary]  
> Write a **fragment shader** that outputs a color, compile it, then **link** it with the vertex shader into a **shader program**. Activate the program with `glUseProgram` and delete individual shader objects after linking.

---

### Fragment Shader — GLSL source

```glsl
#version 330 core
out vec4 FragColor;

void main()
{
    // RGBA in [0.0, 1.0]
    FragColor = vec4(1.0, 0.5, 0.2, 1.0); // orange, opaque
}
```

> [!info]  
> Colors use **RGBA** with components in **[0, 1]**.  
> Example: red 1.0 + green 1.0 → yellow.

---

### Compile the Fragment Shader

```cpp
const char* fragmentShaderSource =
"#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"    FragColor = vec4(1.0, 0.5, 0.2, 1.0);\n"
"}\n";

unsigned int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, nullptr);
glCompileShader(fragmentShader);

// check compile errors
int success;
char infoLog[512];
glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
if (!success) {
    glGetShaderInfoLog(fragmentShader, 512, nullptr, infoLog);
    std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n"
              << infoLog << std::endl;
}
```

---

### Link Shaders into a Program

```cpp
unsigned int shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);   // previously compiled
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);

// check link errors
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
if (!success) {
    glGetProgramInfoLog(shaderProgram, 512, nullptr, infoLog);
    std::cout << "ERROR::PROGRAM::LINKING_FAILED\n"
              << infoLog << std::endl;
}

// shaders no longer needed after linking
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

Activate once before drawing:

```cpp
glUseProgram(shaderProgram);
```

---

### What linking does

> [!note]  
> Linking connects **stage outputs ↔ inputs**. Mismatched types or missing varyings cause **link errors**.

---

### Next step

> [!important]  
> Tell OpenGL how your vertex buffer **maps to attribute locations** in the vertex shader (e.g., `layout(location=0) in vec3 aPos;`). This is done with **vertex attribute pointers** and a **VAO**.