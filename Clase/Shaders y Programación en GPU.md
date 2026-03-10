> [!summary]
> Los **shaders** son programas que se ejecutan en GPU y permiten controlar etapas del cauce gráfico.  
> En esta asignatura se usa **GLSL** y, de forma práctica, trabajaremos sobre todo con **Vertex Shader** y **Fragment Shader**.
>
> Objetivos del tema:
> - Entender qué es un programa de shaders y cómo se compila/linka.
> - Pasar datos CPU→GPU mediante `attribute`/`in` y `uniform`.
> - Comprobar errores de compilación y linkado.
> - Integrar matrices `model/view/projection` (incluida cámara) en shaders.

---

## 1) Contexto: del pipeline fijo al programable

En OpenGL moderno, varias fases del cauce gráfico se pueden programar con shaders:

- **Vertex Shader**: transforma vértices.
- **Fragment Shader**: calcula color final por fragmento.
- (Opcionales) Geometry/Tessellation shaders.

> [!info]
> En tu proyecto actual ya hay cámara y transformaciones, pero el render está en estilo **legacy/fixed pipeline** (`glMatrixMode`, `glVertexPointer`, `glColorPointer`). Este tema prepara la migración a pipeline programable.

Ver también: [[Cauce Gráfico]] · [[Cámara y Proyección]] · [[Transformaciones]]

---

## 2) Lenguajes de shaders

Los más comunes:

- **GLSL** (OpenGL/OpenGL ES/WebGL/Vulkan)
- HLSL (Direct3D)
- Cg (histórico)

En esta asignatura: **GLSL**.

---

## 3) Ciclo de vida de un shader program

1. Crear shader (`glCreateShader`)
2. Cargar código (`glShaderSource`)
3. Compilar (`glCompileShader`)
4. Crear programa (`glCreateProgram`)
5. Adjuntar shaders (`glAttachShader`)
6. Linkar (`glLinkProgram`)
7. Activar (`glUseProgram`)

```cpp
GLuint programID = glCreateProgram();

GLuint vs = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vs, 1, &vertexCode, nullptr);
glCompileShader(vs);

GLuint fs = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fs, 1, &fragmentCode, nullptr);
glCompileShader(fs);

glAttachShader(programID, vs);
glAttachShader(programID, fs);
glLinkProgram(programID);

glUseProgram(programID);

// Tras linkar, los objetos shader se pueden liberar
glDeleteShader(vs);
glDeleteShader(fs);
```

---

## 4) Comprobación de errores (imprescindible)

### Error de compilación de shader

```cpp
GLint ok = GL_FALSE;
glGetShaderiv(shaderID, GL_COMPILE_STATUS, &ok);
if (ok != GL_TRUE) {
    GLchar log[1024];
    GLsizei len = 0;
    glGetShaderInfoLog(shaderID, 1024, &len, log);
    std::cout << "Shader compile error:\n" << log << "\n";
}
```

### Error de linkado de programa

```cpp
GLint linked = GL_FALSE;
glGetProgramiv(programID, GL_LINK_STATUS, &linked);
if (linked != GL_TRUE) {
    GLchar log[1024];
    GLsizei len = 0;
    glGetProgramInfoLog(programID, 1024, &len, log);
    std::cout << "Program link error:\n" << log << "\n";
}
```

> [!warning]
> No revisar logs es uno de los errores más comunes: el programa “no dibuja nada” y parece fallo de cámara/buffers cuando en realidad es compilación GLSL.

---

## 5) Vertex Shader (transformaciones)

El vertex shader transforma del espacio de modelo a clip space:

$$
\text{gl\_Position} = P \cdot V \cdot M \cdot \begin{pmatrix}x\\y\\z\\1\end{pmatrix}
$$

Ejemplo adaptado al motor (`vertex_t` con posición/color en `vec4`):

```glsl
#version 330 core
layout(location = 0) in vec4 aPos;
layout(location = 1) in vec4 aColor;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

out vec4 vColor;

void main()
{
    gl_Position = projection * view * model * aPos;
    vColor = aColor;
}
```

---

## 6) Fragment Shader (color por fragmento)

```glsl
#version 330 core
in vec4 vColor;
out vec4 FragColor;

void main()
{
    FragColor = vColor;
}
```

> [!note]
> Los valores que salen del vertex shader (`out`) llegan interpolados al fragment shader (`in`).

---

## 7) Atributos y uniforms

### Atributos (por vértice)

- Se leen desde VBO/VAO.
- Se declaran con `layout(location = X) in ...` en GLSL moderno.

```cpp
// vertex_t { vec4float position; vec4float color; }

// Posición (location = 0)
glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, sizeof(vertex_t),
    (void*)offsetof(vertex_t, position));
glEnableVertexAttribArray(0);

// Color (location = 1)
glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, sizeof(vertex_t),
    (void*)offsetof(vertex_t, color));
glEnableVertexAttribArray(1);
```

### Uniforms (constantes por draw call)

```cpp
GLint mLoc = glGetUniformLocation(programID, "model");
GLint vLoc = glGetUniformLocation(programID, "view");
GLint pLoc = glGetUniformLocation(programID, "projection");

matrix4x4f model = obj->computeModelMatrix();
matrix4x4f view  = cam->computeViewMatrix();
matrix4x4f proj  = cam->computeProjectionMatrix(100.0f, 0.1f, PI / 4.0f, 4.0f / 3.0f);

// Igual que en el pipeline legacy de tu motor: transponer antes de enviar a OpenGL
matrix4x4f modelT = transpose(model);
matrix4x4f viewT  = transpose(view);
matrix4x4f projT  = transpose(proj);

glUniformMatrix4fv(mLoc, 1, GL_FALSE, modelT.mat);
glUniformMatrix4fv(vLoc, 1, GL_FALSE, viewT.mat);
glUniformMatrix4fv(pLoc, 1, GL_FALSE, projT.mat);
```

> [!tip]
> Si `glGetUniformLocation` devuelve `-1`, revisa nombre/uso real en shader (GLSL optimiza variables no usadas).

---

## 8) Lectura de código de shader desde fichero

```cpp
std::string readTextFile(const std::string& file)
{
    std::ifstream f(file);
    if (!f.is_open()) {
        std::cout << "ERROR: fichero no encontrado: " << file << "\n";
        return {};
    }
    return std::string(std::istreambuf_iterator<char>(f), {});
}
```

Recomendación: mantener `vertex.glsl` y `fragment.glsl` separados del código C++.

---

## 9) Ejemplo práctico (migrando `drawObjects` a shaders)

Base conceptual: mantener tu flujo actual (VAO/VBO/EBO, cámara y matrices) y sustituir la parte legacy por `glUseProgram` + atributos/uniforms.

```cpp
void Render::drawObjectsShader(GLuint programID)
{
    matrix4x4f proj = cam->computeProjectionMatrix(100.0f, 0.1f, PI / 4.0f, 4.0f / 3.0f);
    matrix4x4f view = cam->computeViewMatrix();
    matrix4x4f projT = transpose(proj);
    matrix4x4f viewT = transpose(view);

    GLint mLoc = glGetUniformLocation(programID, "model");
    GLint vLoc = glGetUniformLocation(programID, "view");
    GLint pLoc = glGetUniformLocation(programID, "projection");

    glUseProgram(programID);
    glUniformMatrix4fv(vLoc, 1, GL_FALSE, viewT.mat);
    glUniformMatrix4fv(pLoc, 1, GL_FALSE, projT.mat);

    for (auto& obj : objectList) {
        auto bo = bufferedObjectList[obj->objectId];

        matrix4x4f model = obj->computeModelMatrix();
        matrix4x4f modelT = transpose(model);
        glUniformMatrix4fv(mLoc, 1, GL_FALSE, modelT.mat);

        glBindVertexArray(bo.bufferId);
        glBindBuffer(GL_ARRAY_BUFFER, bo.vertexBufferId);
        glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bo.indexBufferId);

        glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, sizeof(vertex_t), (void*)offsetof(vertex_t, position));
        glEnableVertexAttribArray(0);

        glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, sizeof(vertex_t), (void*)offsetof(vertex_t, color));
        glEnableVertexAttribArray(1);

        glDrawElements(GL_TRIANGLES, obj->indexList.size(), GL_UNSIGNED_INT, nullptr);

        glDisableVertexAttribArray(1);
        glDisableVertexAttribArray(0);
        glBindVertexArray(0);
    }
}
```

> [!note]
> En esta versión ya no se usan `glMatrixMode`, `glLoadMatrixf`, `glVertexPointer` ni `glColorPointer`.

---

## 10) Adaptación al motor (estado actual)

### Lo que ya tienes

- Cámara y proyección (tema previo).
- Bucle de render y delta time.
- Buffers VAO/VBO/EBO.

### Lo que falta para “pasar a shaders”

1. Crear clase/struct `GLProgram` (compilar, linkar, `use()`).
2. Sustituir llamadas legacy:
   - `glMatrixMode`, `glLoadMatrixf` → uniforms `model/view/projection`.
   - `glEnableClientState`/`glVertexPointer` → `glVertexAttribPointer` + `glEnableVertexAttribArray`.
3. Añadir al menos un par `vertex+fragment` shaders.
4. En cada frame:
   - calcular `view` con cámara,
   - calcular `projection`,
   - enviar uniforms antes de `glDrawElements`.

---

## 11) Checklist de estudio (examen/práctica)

- [ ] Diferenciar **shader object** vs **program object**.
- [ ] Saber flujo: create → source → compile → attach → link → use.
- [ ] Saber leer y explicar un log de compilación/linkado.
- [ ] Entender `in/out/uniform` y su frecuencia de cambio.
- [ ] Recordar orden de matrices: `projection * view * model`.
- [ ] Explicar por qué un fragment shader recibe datos interpolados.

---

## Conexiones con tus apuntes

- [[Cauce Gráfico]]
- [[Render]]
- [[Buffers (VAO, VBO, EBO)]]
- [[Cámara y Proyección]]
- [[Game Loop y Delta Time]]
- [[Learn OpenGL/4. Shaders/GLSL|Learn OpenGL - GLSL]]
- [[Learn OpenGL/4. Shaders/Uniforms|Learn OpenGL - Uniforms]]
- [[Learn OpenGL/4. Shaders/Ins and outs|Learn OpenGL - In/Out]]
