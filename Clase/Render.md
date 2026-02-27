> [!summary]
> La clase `Render` es el **motor gráfico** del proyecto. Se encarga de: inicializar GLFW/GLAD/ventana, subir datos de objetos a la GPU mediante buffers, dibujar todos los objetos registrados, y ejecutar el **game loop** con delta time.
>
> Conceptos clave:
> - Inicialización encapsulada en el constructor
> - Buffer objects (VAO/VBO/EBO) por cada objeto
> - Dibujo con `glDrawElements` (indexed rendering)
> - Game loop con delta time

---

## Render.h — Header

```cpp
#pragma once
#include "Object3D.h"

class Render {
public:
    Render();
    
    typedef struct {
        unsigned int bufferId;        // VAO
        unsigned int vertexBufferId;  // VBO
        unsigned int indexBufferId;   // EBO
    } bufferObject_t;
    
    vector<Object3D*> objectList;
    map<unsigned int, bufferObject_t> bufferedObjectList;
    GLFWwindow* window = nullptr;
    bool initialized = false;
    
    void setupObject(Object3D* obj);
    void drawObjects();
    void updateObject(double timeStep);
    void mainLoop();
};
```

### Miembros importantes

| Miembro | Tipo | Descripción |
|---------|------|-------------|
| `objectList` | `vector<Object3D*>` | Lista de objetos a dibujar cada frame |
| `bufferedObjectList` | `map<uint, bufferObject_t>` | Mapa `objectId → buffers GPU` |
| `window` | `GLFWwindow*` | Ventana de la aplicación |
| `initialized` | `bool` | Flag de inicialización exitosa |

> [!note] `bufferObject_t`
> Agrupa los tres IDs de buffer que OpenGL asigna a cada objeto:
> - `bufferId` → **VAO** (Vertex Array Object)
> - `vertexBufferId` → **VBO** (Vertex Buffer Object)
> - `indexBufferId` → **EBO** (Element Buffer Object)
>
> Ver detalle en: [[Buffers (VAO, VBO, EBO)]]

---

## Constructor — Inicialización

```cpp
Render::Render() {
    initialized = false;
    
    // 1. Inicializar GLFW
    if (!glfwInit()) {
        std::cerr << "ERROR: Failed to initialize glfw" << std::endl;
        return;
    }
    
    // Configurar versión de OpenGL
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_COMPAT_PROFILE);
    glfwWindowHint(GLFW_RESIZABLE, GL_FALSE);

    // 2. Crear ventana
    this->window = glfwCreateWindow(800, 600, "Hello Triangle", nullptr, nullptr);
    if (!window) {
        std::cerr << "ERROR: Failed to create window" << std::endl;
        glfwTerminate();
        return;
    }
    glfwMakeContextCurrent(window);

    // 3. Inicializar GLAD
    if (!gladLoadGL(glfwGetProcAddress)) {
        std::cerr << "ERROR: Failed to initialize GLAD" << std::endl;
        glfwTerminate();
        return;
    }

    // 4. Registrar eventos
    EventManager::initEventManager(window);
    initialized = true;
}
```

> [!info] Diferencia con la Clase 1
> En la [[Introducción a OpenGL]], este código estaba directamente en `main()`. Ahora está encapsulado en el constructor del `Render`, lo que permite:
> - **Reutilizar** la inicialización
> - **Verificar** el estado con `initialized`
> - Potencialmente crear **múltiples ventanas** (múltiples Render)

> [!info] Window Hints de OpenGL
> Antes de crear la ventana, configuramos la **versión de OpenGL** que queremos usar:
> - `GLFW_CONTEXT_VERSION_MAJOR/MINOR` → OpenGL 3.3
> - `GLFW_OPENGL_PROFILE` → `COMPAT_PROFILE` (compatibility profile)
> 
> El **compatibility profile** permite usar funciones legacy (`glBegin/glEnd`, `glPushMatrix`, etc.) junto con shaders modernos. El **core profile** solo permite shaders.
> Ver más: [[Learn OpenGL/1. Theory/Core Profile vs Immediate Mode|Core Profile vs Immediate Mode]]
> Ver también: [[Learn OpenGL/2. Create a Window/Start|Start]]

### Flujo de inicialización

```mermaid
graph TD
    A[Render::Render] --> B{glfwInit?}
    B -->|Falla| X[return sin initialized]
    B -->|OK| B2["Configurar hints<br/>(OpenGL 3.3 Compat)"]
    B2 --> C[Crear ventana]
    C --> D{¿Ventana OK?}
    D -->|Falla| X
    D -->|OK| E[gladLoadGL]
    E --> F{¿GLAD OK?}
    F -->|Falla| X
    F -->|OK| G[initEventManager]
    G --> H[initialized = true ✓]
```

> [!warning] Manejo de errores
> El constructor usa `return` temprano en lugar de excepciones. El código que usa `Render` debe verificar `initialized` antes de continuar. El `mainLoop()` comprueba esto al inicio.

---

## setupObject — Subir datos a la GPU

```cpp
void Render::setupObject(Object3D *obj) {
    bufferObject_t bo = {0xFFFFFFFF, 0xFFFFFFFF, 0xFFFFFFFF};
    
    // 1. Generar buffers
    glGenVertexArrays(1, &bo.bufferId);       // Crear VAO
    glGenBuffers(1, &bo.indexBufferId);        // Crear EBO
    glGenBuffers(1, &bo.vertexBufferId);       // Crear VBO

    // 2. Bind VAO (todo lo que sigue queda "grabado" en este VAO)
    glBindVertexArray(bo.bufferId);

    // 3. Subir vértices al VBO
    glBindBuffer(GL_ARRAY_BUFFER, bo.vertexBufferId);
    glBufferData(GL_ARRAY_BUFFER, 
                 obj->vertexList.size() * sizeof(vertex_t), 
                 obj->vertexList.data(), 
                 GL_STATIC_DRAW);

    // 4. Subir índices al EBO
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bo.indexBufferId);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, 
                 sizeof(unsigned int) * obj->indexList.size(), 
                 obj->indexList.data(), 
                 GL_STATIC_DRAW);

    // 5. Guardar la asociación objectId → buffers
    bufferedObjectList[obj->objectId] = bo;
}
```

### Paso a paso

| Paso | Función OpenGL | Qué hace |
|------|----------------|----------|
| 1 | `glGenVertexArrays` / `glGenBuffers` | Pide a OpenGL que reserve IDs para buffers |
| 2 | `glBindVertexArray` | Activa el VAO: todo lo que se configure después queda asociado a él |
| 3 | `glBindBuffer` + `glBufferData` | Copia los vértices de RAM → VRAM (memoria de la GPU) |
| 4 | `glBindBuffer` + `glBufferData` | Copia los índices de RAM → VRAM |
| 5 | `bufferedObjectList[id] = bo` | Guarda los IDs para usarlos al dibujar |

> [!important] `GL_STATIC_DRAW`
> Le indica a OpenGL que estos datos **no van a cambiar** frecuentemente. Esto permite a la GPU optimizar el almacenamiento.
>
> | Hint | Uso típico |
> |------|-----------|
> | `GL_STATIC_DRAW` | Datos que se suben una vez y no cambian |
> | `GL_DYNAMIC_DRAW` | Datos que cambian con frecuencia |
> | `GL_STREAM_DRAW` | Datos que cambian cada frame |

> [!tip] ¿Por qué `0xFFFFFFFF` como valor inicial?
> Es el valor máximo de un `unsigned int`. Se usa como "valor inválido" para detectar si un buffer no se generó correctamente (OpenGL nunca asigna este ID).

Ver explicación detallada de los buffers en: [[Buffers (VAO, VBO, EBO)]]

---

## drawObjects — Dibujar cada frame

```cpp
void Render::drawObjects() {
    for (auto& obj : objectList) {
        // 1. Recuperar buffers de este objeto
        auto bo = bufferedObjectList[obj->objectId];
        
        // 2. Cargar matriz de modelo (legacy OpenGL)
        glMatrixMode(GL_MODELVIEW);
        glLoadIdentity();
        matrix4x4f model = obj->computeModelMatrix();
        glLoadMatrixf(model.mat);
        
        // 3. Bind de buffers
        glBindVertexArray(bo.bufferId);
        glBindBuffer(GL_ARRAY_BUFFER, bo.vertexBufferId);
        glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bo.indexBufferId);

        // 4. Configurar atributos de vértice
        glEnableClientState(GL_VERTEX_ARRAY);
        glEnableClientState(GL_COLOR_ARRAY);
        glVertexPointer(4, GL_FLOAT, sizeof(vertex_t), 
                       (void*)offsetof(vertex_t, position));
        glColorPointer(4, GL_FLOAT, sizeof(vertex_t), 
                      (void*)offsetof(vertex_t, color));
        
        // 5. Dibujar
        glDrawElements(GL_TRIANGLES, obj->indexList.size(), 
                      GL_UNSIGNED_INT, nullptr);
        
        // 6. Limpiar estado
        glDisableClientState(GL_COLOR_ARRAY);
        glDisableClientState(GL_VERTEX_ARRAY);
        glBindVertexArray(0);
    }
}
```

### Configuración de matrices (Legacy OpenGL)

```cpp
glMatrixMode(GL_MODELVIEW);
glLoadIdentity();
matrix4x4f model = obj->computeModelMatrix();
glLoadMatrixf(model.mat);
```

| Función | Descripción |
|----------|-------------|
| `glMatrixMode(GL_MODELVIEW)` | Selecciona la **matriz modelview** como activa |
| `glLoadIdentity()` | Resetea la matriz a la **identidad** |
| `glLoadMatrixf(model.mat)` | Carga nuestra matriz de modelo en OpenGL |

> [!note] GL_MODELVIEW vs GL_PROJECTION
> OpenGL legacy mantiene dos pilas de matrices:
> - `GL_MODELVIEW` → transformaciones del objeto y cámara
> - `GL_PROJECTION` → proyección (perspectiva/ortográfica)
> 
> Ver más: [[Computer Graphics/3. OpenGL Geometry/3.3 Projection and Viewing/Modelview Transformation|Modelview Transformation]]

### Desglose de `glVertexPointer` y `glColorPointer`

```cpp
glVertexPointer(4, GL_FLOAT, sizeof(vertex_t), (void*)offsetof(vertex_t, position));
glColorPointer(4, GL_FLOAT, sizeof(vertex_t), (void*)offsetof(vertex_t, color));
```

| Parámetro | `glVertexPointer` | `glColorPointer` | Significado |
|-----------|-------------------|------------------|-------------|
| `size` | `4` | `4` | 4 componentes (x,y,z,w / r,g,b,a) |
| `type` | `GL_FLOAT` | `GL_FLOAT` | Cada componente es un float |
| `stride` | `sizeof(vertex_t)` | `sizeof(vertex_t)` | Distancia entre vértices (32 bytes) |
| `pointer` | `offsetof(vertex_t, position)` | `offsetof(vertex_t, color)` | Offset dentro del struct (0 y 16 bytes) |

> [!info] ¿Qué es el stride?
> El **stride** le dice a OpenGL cuántos bytes saltar para llegar al siguiente vértice. Como nuestro `vertex_t` tiene position (16 bytes) + color (16 bytes) = 32 bytes, OpenGL sabe que cada 32 bytes empieza un nuevo vértice.
>
> ```
> Memoria del VBO:
> [pos0][col0][pos1][col1][pos2][col2]...
>  ←─ stride ─→
> 
> glVertexPointer apunta a pos0, pos1, pos2... (offset 0)
> glColorPointer apunta a col0, col1, col2... (offset 16)
> ```
>
> Ver más: [[Learn OpenGL/3. Hello Triangle/Linking Vertex Attributes|Linking Vertex Attributes]]

### `glDrawElements` — Dibujo indexado

```cpp
glDrawElements(GL_TRIANGLES, obj->indexList.size(), GL_UNSIGNED_INT, nullptr);
```

| Parámetro | Valor | Significado |
|-----------|-------|-------------|
| `mode` | `GL_TRIANGLES` | Cada 3 índices forman un triángulo |
| `count` | `indexList.size()` | Número total de índices (6) |
| `type` | `GL_UNSIGNED_INT` | Tipo de cada índice |
| `indices` | `nullptr` | Los índices ya están en el EBO vinculado |

> [!important] `glDrawElements` vs `glDrawArrays`
> - `glDrawArrays` → dibuja vértices en orden secuencial
> - `glDrawElements` → dibuja usando **índices** (permite reutilizar vértices)
>
> Usamos `glDrawElements` porque nuestro cuadrado comparte vértices entre triángulos.
> Ver más: [[Learn OpenGL/3. Hello Triangle/Element Buffer Objects|Element Buffer Objects]]

---

### Client State Arrays

```cpp
glEnableClientState(GL_VERTEX_ARRAY);
glEnableClientState(GL_COLOR_ARRAY);
// ... configurar punteros y dibujar ...
glDisableClientState(GL_COLOR_ARRAY);
glDisableClientState(GL_VERTEX_ARRAY);
```

> [!warning] API Legacy (Compatibility Profile)
> `glEnableClientState` / `glDisableClientState` son parte del **pipeline fijo** de OpenGL. Activan/desactivan arrays de atributos:
> - `GL_VERTEX_ARRAY` → posiciones
> - `GL_COLOR_ARRAY` → colores
> - `GL_NORMAL_ARRAY` → normales (para iluminación)
> - `GL_TEXTURE_COORD_ARRAY` → coordenadas de textura
>
> En OpenGL moderno (Core Profile) esto se reemplaza con:
> - `glEnableVertexAttribArray` / `glDisableVertexAttribArray`
> - **Shaders** con atributos de entrada personalizados
> 
> Ver más: [[Learn OpenGL/3. Hello Triangle/Linking Vertex Attributes|Linking Vertex Attributes]]
> Ver también: [[Learn OpenGL/1. Theory/Core Profile vs Immediate Mode|Core Profile vs Immediate Mode]]

---

## updateObject — Actualizar lógica

```cpp
void Render::updateObject(double timestep) {
    for (auto& obj : objectList) {
        obj->moveObject(timestep);
    }
}
```

Recorre todos los objetos y les pasa el **delta time** para que actualicen su posición. Ver: [[Movimiento de Objetos]]

---

## mainLoop — El bucle principal

```cpp
void Render::mainLoop() {
    if (!initialized || window == nullptr) {
        return;
    }
    
    double lastTime = 0;
    double newTime = glfwGetTime();
    double deltaTime = newTime - lastTime;

    while (!glfwWindowShouldClose(window))
    {
        // 1. Calcular delta time
        newTime = glfwGetTime();
        deltaTime = newTime - lastTime;
        lastTime = newTime;

        // 2. Actualizar lógica
        updateObject(deltaTime);
        EventManager::updateEvents();

        // 3. Comprobar ESC
        if (EventManager::keyMap[GLFW_KEY_ESCAPE]) {
            glfwSetWindowShouldClose(window, true);
        }

        // 4. Renderizar
        glClear(GL_COLOR_BUFFER_BIT);
        drawObjects();

        // 5. Intercambiar buffers
        glfwSwapBuffers(window);
    }
}
```

Ver explicación detallada del bucle en: [[Game Loop y Delta Time]]

---

## Pintado — Limpieza de buffers

Antes de dibujar cada frame, es necesario **limpiar** los buffers de la GPU:

### `glClearColor`

```cpp
void glClearColor(GLclampf red, GLclampf green, GLclampf blue, GLclampf alpha);
```

Define el **color** con el que se limpiará el color buffer. Normalmente se llama una vez al inicio y se limpia antes de pintar cada frame.

### `glClear`

```cpp
void glClear(GLbitfield mask);
```

Limpia el buffer pasado como parámetro. Se pueden **combinar** con el operador `|`:

| Flag | Buffer | Descripción |
|------|--------|-------------|
| `GL_COLOR_BUFFER_BIT` | Color / backbuffer | Los píxeles de color del frame anterior |
| `GL_DEPTH_BUFFER_BIT` | Profundidad (Z-buffer) | Los valores de profundidad de cada píxel |
| `GL_STENCIL_BUFFER_BIT` | Stencil / recorte | Máscara de recorte (no lo utilizamos) |

```cpp
// Uso típico: limpiar color y profundidad
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

> [!info] ¿Por qué limpiar el depth buffer?
> Sin limpiar el depth buffer, los valores de profundidad del frame anterior persistirían y los objetos podrían quedar ocultos incorrectamente.
> Ver más: [[Learn OpenGL/2. Create a Window/Rendering|Rendering]]
> Ver también: [[Learn OpenGL/7. Coordinate Systems/Z-Buffer|Z-Buffer]]

---

## Modos de pintado

Cuando se realiza el pintado de elementos, OpenGL tiene dos modos:

| Modo | Descripción | Perfil |
|------|-------------|--------|
| **Flujo de pintado fijo** | Las operaciones de transformación de vértices y rasterizado de fragmentos está **preestablecida** (no modificable) | Compatibility Profile |
| **Shaders** | Codificamos la transformación en un **vertex shader** y el rasterizado en un **fragment shader** | Core Profile |

El flujo de pintado fijo se utiliza únicamente en el **perfil de compatibilidad**, así que nos limitaremos a renderizado con shaders.

```mermaid
graph LR
    subgraph "Pipeline Fijo (Legacy)"
        A1["Vértices"] --> B1["Transformación fija"]
        B1 --> C1["Rasterizado fijo"]
        C1 --> D1["Píxeles"]
    end
    
    subgraph "Pipeline Programable (Shaders)"
        A2["Vértices"] --> B2["Vertex Shader<br/>(programable)"]
        B2 --> C2["Fragment Shader<br/>(programable)"]
        C2 --> D2["Píxeles"]
    end
```

> [!important] Core Profile vs Compatibility Profile
> - **Compatibility Profile**: incluye las funciones legacy (`glBegin`, `glEnd`, `glVertex`, `glPushMatrix`...). Nuestro código actual usa algunas de estas.
> - **Core Profile**: solo funciones modernas. Requiere shaders obligatoriamente.
>
> Ver más: [[Learn OpenGL/1. Theory/Core Profile vs Immediate Mode|Core Profile vs Immediate Mode]]
> Ver también: [[Learn OpenGL/4. Shaders/GLSL|GLSL]]
> Ver también: [[Learn OpenGL/3. Hello Triangle/Vertex Shader|Vertex Shader]]
> Ver también: [[Learn OpenGL/3. Hello Triangle/Fragment Shader|Fragment Shader]]
> Ver también: [[Cauce Gráfico]] — El pipeline completo

---

## Flujo completo de un frame

```mermaid
graph TD
    A[Inicio del frame] --> B[Calcular deltaTime]
    B --> C[updateObject - mover objetos]
    C --> D[updateEvents - procesar input]
    D --> E{¿ESC presionado?}
    E -->|Sí| F[Cerrar ventana]
    E -->|No| G[glClear - limpiar pantalla]
    G --> H[drawObjects - dibujar todo]
    H --> I[glfwSwapBuffers - mostrar frame]
    I --> A
```

---

## Véase también

- [[Arquitectura del Motor]] — Visión general del proyecto
- [[Object3D]] — Los objetos que el Render dibuja
- [[Buffers (VAO, VBO, EBO)]] — Detalle de los buffer objects
- [[Game Loop y Delta Time]] — El patrón del bucle principal
- [[Movimiento de Objetos]] — Cómo se mueven los objetos
- [[Cauce Gráfico]] — El pipeline gráfico completo
- [[Transformaciones]] — Matrices y coordenadas homogéneas
- [[Learn OpenGL/1. Theory/Core Profile vs Immediate Mode|Core Profile vs Immediate Mode]]
- [[Learn OpenGL/1. Theory/State Machine|State Machine]]
- [[Learn OpenGL/2. Create a Window/Rendering|Rendering]]

