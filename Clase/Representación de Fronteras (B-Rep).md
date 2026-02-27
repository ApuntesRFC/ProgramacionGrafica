> [!summary]
> B-Rep representa un sólido a través de su frontera (caras, aristas y vértices). Es una técnica central en CAD y modelado industrial porque separa claramente geometría y topología, aunque exige controles estrictos de validez.

# Representación de fronteras (B-Rep)

---

## Idea central de B-Rep

Un sólido se define por:
- **Vértices** (puntos),
- **Aristas** (conexiones),
- **Caras** (superficies que delimitan el interior).

> [!info]
> B-Rep no es solo geometría; también codifica **topología** (quién conecta con quién).

---

## Restricciones comunes

- Uso de caras planas o superficies curvas parametrizadas.
- Frecuente triangulación para renderizado eficiente.
- En muchos sistemas, requisito de malla **2-manifold**.

### Criterios de validez típicos

| Regla | Intuición |
|---|---|
| Cada arista pertenece a 2 caras | Evita huecos/no-manifold |
| Caras no se interpenetran | Evita autocolisiones topológicas |
| Conectividad coherente | Permite navegación robusta de malla |

---

## Euler para poliedros cerrados

Para sólidos topológicamente equivalentes a una esfera (género 0):

$$V + C = A + 2$$

donde:
- $V$ = vértices,
- $C$ = caras,
- $A$ = aristas.

> [!important]
> Esta relación ayuda a detectar errores estructurales en mallas cerradas simples.

---

## Almacenamiento: explícito vs por índices

### Explícito
Cada cara guarda coordenadas de sus vértices.

- Simple de entender.
- Duplica datos y aumenta memoria.

### Por índices
Lista global de vértices + caras como índices.

- Evita duplicación.
- Más eficiente para GPU.
- Requiere gestión de adyacencias.

> [!tip]
> Este esquema conecta directamente con buffers de índices en rendering.
> Ver: [[Buffers (VAO, VBO, EBO)]].

---

## Caras curvas y aproximación poligonal

Aproximar superficies curvas con polígonos implica:
- pérdida de precisión geométrica,
- mayor memoria al subir resolución,
- mejor compatibilidad con pipelines de rasterización.

---

## Operaciones que se benefician de B-Rep

- Cálculo de envolventes convexas,
- cajas límite y volúmenes contenedores,
- consultas de adyacencia,
- navegación de superficie para edición.

---

## Conexiones con el temario

- [[Técnicas de Representación]]
- [[Requisitos de Representación]]
- [[Cámara y Proyección]]
- [[Cauce Gráfico]]

