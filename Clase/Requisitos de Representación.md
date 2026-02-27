> [!summary]
> Una técnica de representación de sólidos debe equilibrar fidelidad geométrica, ausencia de ambigüedad, validez topológica y eficiencia computacional. Este bloque define los criterios formales que permiten decidir si una representación es útil en CAD, simulación o rendering.

# Requisitos de representación

---

## Objetivo de un modelo geométrico

Un **modelo** es una representación de una entidad real que permite:
- analizar su estructura,
- simular su comportamiento,
- predecir efectos de cambios,
- reducir prototipado físico.

> [!info]
> En informática gráfica, el modelo suele codificar tres capas: **geometría**, **topología** y **atributos**.

---

## Información contenida en un modelo

| Capa | Qué describe | Ejemplos |
|---|---|---|
| Geometría | Forma y distribución espacial | puntos, aristas, polígonos, volúmenes |
| Topología | Conectividad entre componentes | adyacencia vértice-arista-cara |
| Atributos | Datos extra para cálculo/render | material, color, etiquetas físicas |

---

## Compromiso representación–almacenamiento

Toda técnica de modelado responde a dos preguntas:
1. **Cómo** se representa el sólido (primitivas y estructura).
2. **Qué** información se almacena (con o sin redundancia).

> [!important]
> Guardar más datos puede acelerar cómputo, pero incrementa memoria y riesgo de inconsistencia.

---

## Requisitos formales de una técnica

| Requisito | Definición práctica |
|---|---|
| Precisión | Capacidad de aproximar fielmente el objeto real |
| Dominio | Tipos de objetos que puede representar |
| Ausencia de ambigüedad | Una codificación representa un único sólido |
| Unicidad | Un sólido idealmente tiene una codificación canónica |
| Validez | Evita generar objetos imposibles/inconsistentes |
| Cierre | Operaciones sobre sólidos válidos devuelven sólidos válidos |
| Compacidad | Uso razonable de memoria |
| Eficiencia | Operaciones y renderizado con coste aceptable |

---

## Ambigüedad y convenciones

Una descripción puede ser ambigua si no se fija:
- sistema de referencia,
- orientación de ejes,
- convenciones de cara frontal/trasera,
- sentido del eje $z$.

> [!warning]
> La ambigüedad geométrica rompe simulación, colisiones y renderizado consistente.

---

## Etapas del ciclo de vida del modelo

1. **Edición** (creación y modificación)
2. **Almacenamiento**
3. **Uso** (visualización/simulación)

Cada etapa prioriza cosas distintas; por eso no existe una técnica universalmente óptima.

---

## Conexiones con el temario

- [[Técnicas de Representación]]
- [[Adquisición de Modelos 3D]]
- [[Representación de Fronteras (B-Rep)]]
- [[Transformaciones]]
- [[Cauce Gráfico]]

