//
// Created by Rodrigo on 10/02/2026.
//

#include "Render.h"

#include "EventManager.h"

Render::Render(){
    initialized = false;
    // Inicializar GLFW
    if (!glfwInit())
    {
        std::cerr << "ERROR: Failed to initialize glfw" << std::endl;
        return;
    }
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_COMPAT_PROFILE);
    glfwWindowHint(GLFW_RESIZABLE, GL_FALSE);

    this->window = glfwCreateWindow(800, 600, "Hello Triangle", nullptr, nullptr);
    if (!window)
    {
        std::cerr << "ERROR: Failed to create window" << std::endl;
        glfwTerminate();
        return;
    }
    glfwMakeContextCurrent(window);

    if (!gladLoadGL(glfwGetProcAddress))
    {
        std::cerr << "ERROR: Failed to initialize GLAD" << std::endl;
        glfwTerminate();
        return;
    }

    EventManager::initEventManager(window);
    initialized = true;
}

void Render::setupObject(Object3D *obj) {
    bufferObject_t bo= {0xFFFFFFFF, 0xFFFFFFFF, 0xFFFFFFFF};
    glGenVertexArrays(1, &bo.bufferId);
    glGenBuffers(1, &bo.vertexBufferId);
    glGenBuffers(1, &bo.indexBufferId);

    glBindVertexArray(bo.bufferId);

    // Configurar VBO de vértices
    glBindBuffer(GL_ARRAY_BUFFER, bo.vertexBufferId);
    glBufferData(GL_ARRAY_BUFFER, obj->vertexList.size() * sizeof(vertex_t), obj->vertexList.data(), GL_STATIC_DRAW);

    // Configurar EBO de índices
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bo.indexBufferId);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, obj->indexList.size() * sizeof(unsigned int), obj->indexList.data(), GL_STATIC_DRAW);

    glBindVertexArray(0);

    bufferedObjectList[obj->objectId] = bo;
}

void Render::drawObjects() {
    for (auto& obj : objectList) {
        auto bo = bufferedObjectList[obj->objectId];

        glMatrixMode(GL_MODELVIEW);
        glLoadIdentity();
        matrix4x4f model = obj->computeModelMatrix();
        glLoadMatrixf(model.mat);

        //VAO
        glBindVertexArray(bo.bufferId);
        glBindBuffer(GL_ARRAY_BUFFER, bo.vertexBufferId);
        glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bo.indexBufferId);

        glEnableClientState(GL_VERTEX_ARRAY);
        glEnableClientState(GL_COLOR_ARRAY);
        glVertexPointer(4, GL_FLOAT, sizeof(vertex_t), (void*)offsetof(vertex_t, position));
        glColorPointer(4, GL_FLOAT, sizeof(vertex_t), (void*)offsetof(vertex_t, color));
        
        glDrawElements(GL_TRIANGLES, obj->indexList.size(), GL_UNSIGNED_INT, nullptr);

        glDisableClientState(GL_COLOR_ARRAY);
        glDisableClientState(GL_VERTEX_ARRAY);
        glBindVertexArray(0);
    }
}
void Render::updateObject(double timestep) {
    for (auto& obj : objectList) {
        obj->moveObject(timestep);
    }
}

void Render::mainLoop() {
    if (!initialized || window == nullptr) {
        return;
    }
    double lastTime = glfwGetTime();
    double newTime = 0;
    double deltaTime = 0;

    while (!glfwWindowShouldClose(window))
    {
        EventManager::updateEvents();

        newTime = glfwGetTime();
        deltaTime = newTime - lastTime;
        lastTime = newTime;

        updateObject(deltaTime);

        if (EventManager::keyMap[GLFW_KEY_ESCAPE]) {
            glfwSetWindowShouldClose(window, true);
        }

        glClear(GL_COLOR_BUFFER_BIT);
        drawObjects();
        glfwSwapBuffers(window);
    }

}
#define GLAD_BIN
#include "common.h"
#include "EventManager.h"
#include "Object3D.h"
#include "Render.h"


void drawObject(Object3D& obj) {
    glBegin(GL_TRIANGLES);
    {
        auto model = obj.computeModelMatrix();
        for (auto& i : obj.indexList) {
            auto v = obj.vertexList[i];
            v.position = model * v.position;
            glColor3f(v.color.x, v.color.y, v.color.z);
            glVertex3f(v.position.x, v.position.y, v.position.z);
        }

    }
    glEnd();
}

int main(int argc, char** argv)
{
    auto r = new Render();
    auto obj = new Object3D();
    r->setupObject(obj);
    r->objectList.push_back(obj);
    r->mainLoop();

    glfwTerminate();
    return 0;
}
//
// Created by Rodrigo on 03/02/2026.
//

#include "EventManager.h"

// Incluimos solo lo necesario para no reintroducir la implementación de GLAD
#include <GLFW/glfw3.h>

GLFWwindow* EventManager::window = nullptr;
std::map<int,bool> EventManager::keyMap;

void EventManager::initEventManager(GLFWwindow* win) {
    glfwSetKeyCallback(win, keyboardManager);
    //glfwSetMouseButtonCallback(win, mouseButtonManager);
    EventManager::window = win;
}
void EventManager::keyboardManager(GLFWwindow* win, int key, int scancode, int action, int mods) {
    switch (action) {
        case GLFW_PRESS: {
            EventManager::keyMap[key] = true;
        } break;
        case GLFW_RELEASE: {
            EventManager::keyMap[key] = false;
        } break;
        default: {
        } break;
    }
}

void EventManager::updateEvents() {
    glfwPollEvents();

}
#include "libMath.h"
#include <cmath>

vec4float make_vector4f(float x, float y, float z, float w) {
    vec4float result;
    result.x = x;
    result.y = y;
    result.z = z;
    result.w = w;
    return result;
}

vec4float normalize(vec4float v) {
    float len = sqrt(v.x * v.x + v.y * v.y + v.z * v.z);
    vec4float result;
    result.x = v.x / len;
    result.y = v.y / len;
    result.z = v.z / len;
    result.w = v.w;
    return result;
}

float dot(vec4float v1, vec4float v2) {
    return v1.x * v2.x + v1.y * v2.y + v1.z * v2.z;
}

vec4float cross(vec4float v1, vec4float v2) {
    vec4float result;
    result.x = v1.y * v2.z - v1.z * v2.y;
    result.y = v1.z * v2.x - v1.x * v2.z;
    result.z = v1.x * v2.y - v1.y * v2.x;
    result.w = 0.0f;
    return result;
}

matrix4x4f make_identityf() {
    matrix4x4f result;
    for (int i = 0; i < 16; i++) {
        result.mat[i] = 0.0f;
    }
    result.mat[0] = 1.0f;
    result.mat[5] = 1.0f;
    result.mat[10] = 1.0f;
    result.mat[15] = 1.0f;
    return result;
}

matrix4x4f make_translate(float X, float Y, float Z) {
    matrix4x4f result = make_identityf();
    result.mat[12] = X;
    result.mat[13] = Y;
    result.mat[14] = Z;
    return result;
}

matrix4x4f make_rotate(float angleX, float angleY, float angleZ) {
    float radX = angleX * PI / 180.0f;
    float radY = angleY * PI / 180.0f;
    float radZ = angleZ * PI / 180.0f;

    matrix4x4f rotX = make_identityf();
    rotX.mat[5] = cos(radX);
    rotX.mat[6] = -sin(radX);
    rotX.mat[9] = sin(radX);
    rotX.mat[10] = cos(radX);

    matrix4x4f rotY = make_identityf();
    rotY.mat[0] = cos(radY);
    rotY.mat[2] = sin(radY);
    rotY.mat[8] = -sin(radY);
    rotY.mat[10] = cos(radY);

    matrix4x4f rotZ = make_identityf();
    rotZ.mat[0] = cos(radZ);
    rotZ.mat[1] = -sin(radZ);
    rotZ.mat[4] = sin(radZ);
    rotZ.mat[5] = cos(radZ);

    matrix4x4f temp = rotZ * rotY;
    return temp * rotX;
}

matrix4x4f make_scale(float X, float Y, float Z) {
    matrix4x4f result = make_identityf();
    result.mat[0] = X;
    result.mat[5] = Y;
    result.mat[10] = Z;
    return result;
}

matrix4x4f transpose(matrix4x4f m) {
    matrix4x4f result;
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            result.mat[i * 4 + j] = m.mat[j * 4 + i];
        }
    }
    return result;
}

vec4float make_quaternion(float x, float y, float z, float angle) {
    vec4float axis = make_vector4f(x, y, z, 0.0f);
    axis = normalize(axis);

    float halfAngle = angle / 2.0f;
    float sinHalfAngle = sin(halfAngle);
    float cosHalfAngle = cos(halfAngle);

    vec4float q;
    q.x = axis.x * sinHalfAngle;
    q.y = axis.y * sinHalfAngle;
    q.z = axis.z * sinHalfAngle;
    q.w = cosHalfAngle;
    return q;
}

matrix4x4f make_rotate_quaternion(vec4float q) {
    matrix4x4f result;
    float xx = q.x * q.x;
    float yy = q.y * q.y;
    float zz = q.z * q.z;
    float xy = q.x * q.y;
    float xz = q.x * q.z;
    float yz = q.y * q.z;
    float wx = q.w * q.x;
    float wy = q.w * q.y;
    float wz = q.w * q.z;

    result.mat[0] = 1.0f - 2.0f * (yy + zz);
    result.mat[1] = 2.0f * (xy - wz);
    result.mat[2] = 2.0f * (xz + wy);
    result.mat[3] = 0.0f;

    result.mat[4] = 2.0f * (xy + wz);
    result.mat[5] = 1.0f - 2.0f * (xx + zz);
    result.mat[6] = 2.0f * (yz - wx);
    result.mat[7] = 0.0f;

    result.mat[8] = 2.0f * (xz - wy);
    result.mat[9] = 2.0f * (yz + wx);
    result.mat[10] = 1.0f - 2.0f * (xx + yy);
    result.mat[11] = 0.0f;

    result.mat[12] = 0.0f;
    result.mat[13] = 0.0f;
    result.mat[14] = 0.0f;
    result.mat[15] = 1.0f;

    return result;
}

matrix4x4f inverse(matrix4x4f m) {
    matrix4x4f result;
    float det;

    result.mat[0] = m.mat[5] * m.mat[10] * m.mat[15] - m.mat[5] * m.mat[11] * m.mat[14] - m.mat[9] * m.mat[6] * m.mat[15] + m.mat[9] * m.mat[7] * m.mat[14] + m.mat[13] * m.mat[6] * m.mat[11] - m.mat[13] * m.mat[7] * m.mat[10];
    result.mat[4] = -m.mat[4] * m.mat[10] * m.mat[15] + m.mat[4] * m.mat[11] * m.mat[14] + m.mat[8] * m.mat[6] * m.mat[15] - m.mat[8] * m.mat[7] * m.mat[14] - m.mat[12] * m.mat[6] * m.mat[11] + m.mat[12] * m.mat[7] * m.mat[10];
    result.mat[8] = m.mat[4] * m.mat[9] * m.mat[15] - m.mat[4] * m.mat[11] * m.mat[13] - m.mat[8] * m.mat[5] * m.mat[15] + m.mat[8] * m.mat[7] * m.mat[13] + m.mat[12] * m.mat[5] * m.mat[11] - m.mat[12] * m.mat[7] * m.mat[9];
    result.mat[12] = -m.mat[4] * m.mat[9] * m.mat[14] + m.mat[4] * m.mat[10] * m.mat[13] + m.mat[8] * m.mat[5] * m.mat[14] - m.mat[8] * m.mat[6] * m.mat[13] - m.mat[12] * m.mat[5] * m.mat[10] + m.mat[12] * m.mat[6] * m.mat[9];

    result.mat[1] = -m.mat[1] * m.mat[10] * m.mat[15] + m.mat[1] * m.mat[11] * m.mat[14] + m.mat[9] * m.mat[2] * m.mat[15] - m.mat[9] * m.mat[3] * m.mat[14] - m.mat[13] * m.mat[2] * m.mat[11] + m.mat[13] * m.mat[3] * m.mat[10];
    result.mat[5] = m.mat[0] * m.mat[10] * m.mat[15] - m.mat[0] * m.mat[11] * m.mat[14] - m.mat[8] * m.mat[2] * m.mat[15] + m.mat[8] * m.mat[3] * m.mat[14] + m.mat[12] * m.mat[2] * m.mat[11] - m.mat[12] * m.mat[3] * m.mat[10];
    result.mat[9] = -m.mat[0] * m.mat[9] * m.mat[15] + m.mat[0] * m.mat[11] * m.mat[13] + m.mat[8] * m.mat[1] * m.mat[15] - m.mat[8] * m.mat[3] * m.mat[13] - m.mat[12] * m.mat[1] * m.mat[11] + m.mat[12] * m.mat[3] * m.mat[9];
    result.mat[13] = m.mat[0] * m.mat[9] * m.mat[14] - m.mat[0] * m.mat[10] * m.mat[13] - m.mat[8] * m.mat[1] * m.mat[14] + m.mat[8] * m.mat[2] * m.mat[13] + m.mat[12] * m.mat[1] * m.mat[10] - m.mat[12] * m.mat[2] * m.mat[9];

    result.mat[2] = m.mat[1] * m.mat[6] * m.mat[15] - m.mat[1] * m.mat[7] * m.mat[14] - m.mat[5] * m.mat[2] * m.mat[15] + m.mat[5] * m.mat[3] * m.mat[14] + m.mat[13] * m.mat[2] * m.mat[7] - m.mat[13] * m.mat[3] * m.mat[6];
    result.mat[6] = -m.mat[0] * m.mat[6] * m.mat[15] + m.mat[0] * m.mat[7] * m.mat[14] + m.mat[4] * m.mat[2] * m.mat[15] - m.mat[4] * m.mat[3] * m.mat[14] - m.mat[12] * m.mat[2] * m.mat[7] + m.mat[12] * m.mat[3] * m.mat[6];
    result.mat[10] = m.mat[0] * m.mat[5] * m.mat[15] - m.mat[0] * m.mat[7] * m.mat[13] - m.mat[4] * m.mat[1] * m.mat[15] + m.mat[4] * m.mat[3] * m.mat[13] + m.mat[12] * m.mat[1] * m.mat[7] - m.mat[12] * m.mat[3] * m.mat[5];
    result.mat[14] = -m.mat[0] * m.mat[5] * m.mat[14] + m.mat[0] * m.mat[6] * m.mat[13] + m.mat[4] * m.mat[1] * m.mat[14] - m.mat[4] * m.mat[2] * m.mat[13] - m.mat[12] * m.mat[1] * m.mat[6] + m.mat[12] * m.mat[2] * m.mat[5];

    result.mat[3] = -m.mat[1] * m.mat[6] * m.mat[11] + m.mat[1] * m.mat[7] * m.mat[10] + m.mat[5] * m.mat[2] * m.mat[11] - m.mat[5] * m.mat[3] * m.mat[10] - m.mat[9] * m.mat[2] * m.mat[7] + m.mat[9] * m.mat[3] * m.mat[6];
    result.mat[7] = m.mat[0] * m.mat[6] * m.mat[11] - m.mat[0] * m.mat[7] * m.mat[10] - m.mat[4] * m.mat[2] * m.mat[11] + m.mat[4] * m.mat[3] * m.mat[10] + m.mat[8] * m.mat[2] * m.mat[7] - m.mat[8] * m.mat[3] * m.mat[6];
    result.mat[11] = -m.mat[0] * m.mat[5] * m.mat[11] + m.mat[0] * m.mat[7] * m.mat[9] + m.mat[4] * m.mat[1] * m.mat[11] - m.mat[4] * m.mat[3] * m.mat[9] - m.mat[8] * m.mat[1] * m.mat[7] + m.mat[8] * m.mat[3] * m.mat[5];
    result.mat[15] = m.mat[0] * m.mat[5] * m.mat[10] - m.mat[0] * m.mat[6] * m.mat[9] - m.mat[4] * m.mat[1] * m.mat[10] + m.mat[4] * m.mat[2] * m.mat[9] + m.mat[8] * m.mat[1] * m.mat[6] - m.mat[8] * m.mat[2] * m.mat[5];

    det = m.mat[0] * result.mat[0] + m.mat[1] * result.mat[4] + m.mat[2] * result.mat[8] + m.mat[3] * result.mat[12];

    // Verificar que el determinante no sea cero (matriz singular)
    if (det == 0.0f) {
        // Retornar matriz identidad si no se puede invertir
        return make_identityf();
    }

    for (int i = 0; i < 16; i++)
        result.mat[i] = result.mat[i] / det;

    return result;
}

