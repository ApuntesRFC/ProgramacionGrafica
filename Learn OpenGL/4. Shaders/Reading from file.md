> [!summary]  
> Implement a **header-only `Shader` class** that loads GLSL from disk, compiles, links, reports errors, and exposes `use()` + uniform setters. Use it once, then call `use()` per draw.

---

### Class skeleton (header-only)

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
    unsigned int ID;

    Shader(const char* vertexPath, const char* fragmentPath) {
        // 1) Read files
        std::string vertexCode, fragmentCode;
        std::ifstream vFile, fFile;
        vFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
        fFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
        try {
            vFile.open(vertexPath);
            fFile.open(fragmentPath);
            std::stringstream vStream, fStream;
            vStream << vFile.rdbuf();
            fStream << fFile.rdbuf();
            vFile.close();
            fFile.close();
            vertexCode   = vStream.str();
            fragmentCode = fStream.str();
        } catch (std::ifstream::failure&) {
            std::cout << "ERROR::SHADER::FILE_NOT_SUCCESSFULLY_READ\n";
        }
        const char* vSrc = vertexCode.c_str();
        const char* fSrc = fragmentCode.c_str();

        // 2) Compile shaders
        unsigned int vSh = glCreateShader(GL_VERTEX_SHADER);
        glShaderSource(vSh, 1, &vSrc, nullptr);
        glCompileShader(vSh);
        checkShader(vSh, "VERTEX");

        unsigned int fSh = glCreateShader(GL_FRAGMENT_SHADER);
        glShaderSource(fSh, 1, &fSrc, nullptr);
        glCompileShader(fSh);
        checkShader(fSh, "FRAGMENT");

        // 3) Link program
        ID = glCreateProgram();
        glAttachShader(ID, vSh);
        glAttachShader(ID, fSh);
        glLinkProgram(ID);
        checkProgram(ID);

        glDeleteShader(vSh);
        glDeleteShader(fSh);
    }

    void use() const { glUseProgram(ID); }

    // Uniform helpers
    void setBool (const std::string& name, bool  v) const { glUniform1i(loc(name), (int)v); }
    void setInt  (const std::string& name, int   v) const { glUniform1i(loc(name), v); }
    void setFloat(const std::string& name, float v) const { glUniform1f(loc(name), v); }

private:
    int loc(const std::string& name) const { return glGetUniformLocation(ID, name.c_str()); }

    static void checkShader(unsigned int sh, const char* tag) {
        int ok; char log[512];
        glGetShaderiv(sh, GL_COMPILE_STATUS, &ok);
        if (!ok) {
            glGetShaderInfoLog(sh, 512, nullptr, log);
            std::cout << "ERROR::SHADER::" << tag << "::COMPILATION_FAILED\n" << log << '\n';
        }
    }
    static void checkProgram(unsigned int prog) {
        int ok; char log[512];
        glGetProgramiv(prog, GL_LINK_STATUS, &ok);
        if (!ok) {
            glGetProgramInfoLog(prog, 512, nullptr, log);
            std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << log << '\n';
        }
    }
};

#endif
```

---

### Usage

```cpp
Shader shader("shaders/basic.vs", "shaders/basic.fs");

while (!glfwWindowShouldClose(window)) {
    shader.use();
    shader.setFloat("uTime", (float)glfwGetTime());
    // draw...
}
```

---

### Notes and pitfalls

> [!warning]
> 
> - Call after GL context + GLAD are initialized.
>     
> - If a uniform is unused, the compiler may **opt it out** → `glGetUniformLocation` returns `-1`.
>     
> - GLSL files must match your target (`#version 330 core`).
>     
> - Check compile/link logs to debug shader errors.
>