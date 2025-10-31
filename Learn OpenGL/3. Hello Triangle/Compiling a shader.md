> [!summary]  
> To use a shader in OpenGL, you must **compile it at runtime** from its source code.  
> The process includes:
> 
> 1. Creating a shader object.
>     
> 2. Attaching the source code.
>     
> 3. Compiling the shader.
>     
> 4. Checking for errors.
>     

---

### Step 1 — Store the Shader Source

```cpp
const char* vertexShaderSource = 
"#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
```

> [!note]  
> Shader source code is written as a **C string** for simplicity here.  
> Later you can load shader code from external `.vert` and `.frag` files.

---

### Step 2 — Create a Shader Object

```cpp
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
```

|Function|Description|
|---|---|
|`glCreateShader(type)`|Creates an empty shader object of the given type. Returns its **ID**.|

> [!info]  
> Shader types include:
> 
> - `GL_VERTEX_SHADER`
>     
> - `GL_FRAGMENT_SHADER`
>     
> - `GL_GEOMETRY_SHADER` (optional)
>     

---

### Step 3 — Attach Source Code and Compile

```cpp
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);
```

|Parameter|Description|
|---|---|
|`vertexShader`|The shader object ID.|
|`1`|Number of source strings (here only one).|
|`&vertexShaderSource`|Pointer to the source string.|
|`NULL`|Use full string (no length array).|

> [!note]  
> `glShaderSource` uploads the source to OpenGL,  
> and `glCompileShader` tells OpenGL to compile it into GPU bytecode.

---

### Step 4 — Check for Compilation Errors

```cpp
int success;
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);

if (!success) {
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
              << infoLog << std::endl;
}
```

|Function|Purpose|
|---|---|
|`glGetShaderiv(shader, pname, &params)`|Retrieves compile status or other properties.|
|`glGetShaderInfoLog(shader, bufSize, NULL, log)`|Fetches error messages if compilation failed.|

> [!important]
> 
> - Always check for compile errors — even small typos in GLSL will cause failure.
>     
> - The **info log** provides driver-specific error messages.
>     

---

### Step 5 — What Happens Internally

> [!info]
> 
> 1. You call `glCreateShader` → OpenGL allocates a shader object on the GPU.
>     
> 2. `glShaderSource` uploads the GLSL text.
>     
> 3. `glCompileShader` compiles it into GPU machine code.
>     
> 4. If successful, you can attach it to a **shader program** (next step).
>     

---

### Full Minimal Example

```cpp
const char* vertexShaderSource = 
"#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";

unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);

int success;
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
if (!success) {
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
              << infoLog << std::endl;
}
```

---

### Key Takeaways

> [!important]
> 
> - Store shader source as text (in memory or files).
>     
> - Compile it at runtime with `glCompileShader`.
>     
> - Always check compilation logs for errors.
>     
> - A compiled shader isn’t usable yet — it must be **linked** into a shader program (next step).
>