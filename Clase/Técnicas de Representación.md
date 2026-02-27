> [!summary]
> Este bloque resume las familias principales para modelar sólidos: alambre, primitivas paramétricas, desplazamientos, B-Rep, partición espacial, CSG y campos escalares. Cada técnica optimiza un equilibrio distinto entre expresividad, validez, coste y rendimiento.

# Técnicas de representación

---

## Panorama general

| Técnica | Idea base | Fortalezas | Debilidades |
|---|---|---|---|
| Alambre | Solo aristas | Simplicidad y rapidez | Ambigüedad, sin interior sólido |
| Primitivas | Catálogo paramétrico | Control y reutilización | Menor libertad para formas orgánicas |
| Desplazamiento | Barrer un perfil | Muy intuitivo | Problemas de cierre en casos complejos |
| B-Rep | Frontera del sólido | Estándar industrial | Gestión topológica compleja |
| Partición espacial | Descomposición del espacio | Consultas/colisión eficientes | Estructuras pesadas |
| CSG | Booleanas entre sólidos | Modelado robusto por operaciones | Árboles complejos al escalar |
| Campos escalares | Isosuperficies | Formas suaves/orgánicas | Extracción de malla costosa |

---

## 1) Modelos de alambre

Representan el objeto como un conjunto de aristas.

> [!warning]
> Son buenos para previsualización, pero insuficientes para sombreado realista, volumen e inferencia física.

---

## 2) Primitivas parametrizables

Se modela con sólidos base (cilindro, caja, engrane, etc.) y parámetros.

Ejemplos de parámetros:
- diámetro,
- grosor,
- número de dientes,
- radio interior.

> [!tip]
> En contextos mecánicos, esta técnica acelera iteración y variantes de diseño.

---

## 3) Representaciones de desplazamiento

Se genera volumen desplazando un perfil.

| Tipo | Definición |
|---|---|
| Extrusión | Traslación de perfil 2D |
| Revolución | Giro del perfil alrededor de un eje |
| Barrido general | Perfil variable sobre trayectoria curva |

También se conocen como **cilindros generalizados**.

---

## 4) B-Rep (visión breve)

Describe el sólido por **vértices, aristas y caras**.

- Admite caras planas o curvas.
- En rendering real-time suele triangularse.
- Requiere controles de validez topológica.

Ver desarrollo completo en [[Representación de Fronteras (B-Rep)]].

---

## 5) Partición espacial, CSG y campos escalares

### Partición espacial
Descompone el espacio en celdas/regiones (útil para consultas espaciales y aceleración).

### CSG
Combina sólidos con operaciones booleanas: unión, intersección y diferencia.

### Campos escalares
Define superficies como isovalores de una función escalar (útil en fluidos, medicina y formas orgánicas).

---

## Conexiones con el temario

- [[Requisitos de Representación]]
- [[Adquisición de Modelos 3D]]
- [[Representación de Fronteras (B-Rep)]]
- [[Cámara y Proyección]]

