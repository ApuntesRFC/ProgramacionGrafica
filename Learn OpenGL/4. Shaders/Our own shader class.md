> [!summary]  
> Build a **header-only `Shader` class** that loads GLSL from disk, compiles, links, reports errors, and exposes `use()` and `set*()` uniform helpers.

---

### `Shader.h` — header-only implementation

```cpp
#ifndef SHADER_H
#define SHADER_H

#include <glad/glad.h>
#include <string>
#include <fstream>
#include <sstream>
#include <iostream>

class Shader {
public:
    // program ID
    unsigned int ID;

    // ctor: load, compile, link
    Shader(const char* vertexPath, const char* fragmentPath) {
        // 1) read files
        std::string vCode, fCode;
        try {
            std::ifstream vFile(vertexPath), fFile(fragmentPath);
            vFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
            fFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
            std::stringstream vStream, fStream;
            vStream << vFile.rdbuf();
            fStream << fFile.rdbuf();
            vCode = vStream.str();
            fCode = fStream.str();
        } catch (const std::exception& e) {
            std::cerr << "ERROR::SHADER::FILE_READ_FAILED\n" << e.what() << std::endl;
        }
        const char* vSrc = vCode.c_str();
        const char* fSrc = fCode.c_str();

        // 2) compile shaders
        unsigned int vSh = glCreateShader(GL_VERTEX_SHADER);
        glShaderSource(vSh, 1, &vSrc, nullptr);
        glCompileShader(vSh);
        checkShader(vSh, "VERTEX");

        unsigned int fSh = glCreateShader(GL_FRAGMENT_SHADER);
        glShaderSource(fSh, 1, &fSrc, nullptr);
        glCompileShader(fSh);
        checkShader(fSh, "FRAGMENT");

        // 3) link program
        ID = glCreateProgram();
        glAttachShader(ID, vSh);
        glAttachShader(ID, fSh);
        glLinkProgram(ID);
        checkProgram(ID);

        // 4) cleanup shader objects
        glDeleteShader(vSh);
        glDeleteShader(fSh);
    }

    // use/activate
    void use() const { glUseProgram(ID); }

    // uniform helpers
    void setBool (const std::string& name, bool  value) const {
        glUniform1i(glGetUniformLocation(ID, name.c_str()), static_cast<int>(value));
    }
    void setInt  (const std::string& name, int   value) const {
        glUniform1i(glGetUniformLocation(ID, name.c_str()), value);
    }
    void setFloat(const std::string& name, float value) const {
        glUniform1f(glGetUniformLocation(ID, name.c_str()), value);
    }

    // optional convenience (commonly needed)
    void setVec3 (const std::string& name, float x,float y,float z) const {
        glUniform3f(glGetUniformLocation(ID, name.c_str()), x,y,z);
    }
    void setMat4 (const std::string& name, const float* m16) const {
        // m16 points to 16 floats in column-major order
        glUniformMatrix4fv(glGetUniformLocation(ID, name.c_str()), 1, GL_FALSE, m16);
    }

private:
    static void checkShader(unsigned int sh, const char* type) {
        int success = 0; char log[1024];
        glGetShaderiv(sh, GL_COMPILE_STATUS, &success);
        if (!success) {
            glGetShaderInfoLog(sh, 1024, nullptr, log);
            std::cerr << "ERROR::SHADER::" << type << "::COMPILATION_FAILED\n"
                      << log << std::endl;
        }
    }
    static void checkProgram(unsigned int prog) {
        int success = 0; char log[1024];
        glGetProgramiv(prog, GL_LINK_STATUS, &success);
        if (!success) {
            glGetProgramInfoLog(prog, 1024, nullptr, log);
            std::cerr << "ERROR::PROGRAM::LINKING_FAILED\n"
                      << log << std::endl;
        }
    }
};

#endif // SHADER_H
```

---

### Usage

```cpp
// after creating GL context and loading GLAD
Shader shader("shaders/basic.vert", "shaders/basic.frag");

while (!glfwWindowShouldClose(window)) {
    shader.use();
    shader.setFloat("time", (float)glfwGetTime());
    // draw...
}
```

> [!tip]  
> Keep GLSL files on disk (`.vert`, `.frag`). The class reads them, compiles once, and you only call `use()` before draws.