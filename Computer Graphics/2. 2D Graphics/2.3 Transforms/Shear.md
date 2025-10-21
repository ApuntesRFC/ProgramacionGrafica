> [!summary]
A **shearing (or skew) transformation** tilts an object so its sides become slanted while keeping lines parallel.  
It changes shape but not area, and can be expressed as a combination of **rotations and scalings**.

---

### Definition

A shear keeps one axis fixed and shifts the other proportionally to its position along the first.

#### Horizontal Shear
$$
\begin{cases}
x' = x + b \cdot y \\
y' = y
\end{cases}
$$
> [!note]
> - $b$ = **horizontal shear factor**  
> - Positive $b$ tilts the top of the shape **right**.  
> - Negative $b$ tilts it **left**.

#### Vertical Shear
$$
\begin{cases}
x' = x \\
y' = y + c \cdot x
\end{cases}
$$
> [!note]
> - $c$ = **vertical shear factor**  
> - Positive $c$ tilts the right side **up**.  
> - Negative $c$ tilts it **down**.

---

### Geometric Effect

> [!info]
> - Horizontal shear → the x-axis stays fixed, but other horizontal lines shift.  
> - Vertical shear → the y-axis stays fixed, but other vertical lines shift.  
> - Parallelism is preserved, but **angles and lengths are not**.

> [!example]
> A rectangle becomes a **parallelogram**, keeping its base length but slanting its sides.

---

### Matrix Representation

Horizontal shear:
$$
\begin{bmatrix}
x' \\
y'
\end{bmatrix}
=
\begin{bmatrix}
1 & b \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\
y
\end{bmatrix}
$$

Vertical shear:
$$
\begin{bmatrix}
x' \\
y'
\end{bmatrix}
=
\begin{bmatrix}
1 & 0 \\
c & 1
\end{bmatrix}
\begin{bmatrix}
x \\
y
\end{bmatrix}
$$

---

### Shear vs. Skew

> [!note]
> - “**Shear**” uses a **linear factor** ($b$ or $c$).  
> - “**Skew**” often refers to the same effect, but defined using an **angle** $\theta$:  
>   > $\tan(\theta)$ = shear factor.

---

### Relation to Rotations and Scalings

A shear can be **decomposed** into a combination of **rotations and non-uniform scalings**:

> [!info]
> - It can be achieved by:
>   1. **Rotate** the shape by a small angle $r$  
>   2. **Scale** differently along the rotated axes  
>   3. **Rotate back** by $-r$

Mathematically, this means:
$$
\text{Shear} = R(-r) \cdot S(s_x, s_y) \cdot R(r)
$$
for appropriate values of $r$, $s_x$, and $s_y$ that reproduce the desired shear factor.

> [!tip]
> In practice, this equivalence is rarely used directly — shearing is implemented with its own matrix form, since it’s more efficient.

---

### Key Properties

| Property | Preserved? |
|-----------|-------------|
| Parallel lines | ✔ |
| Angles | ✖ |
| Lengths | ✖ |
| Area | ✔ (for small shears) |

> [!important]
> Shearing distorts shapes **without rotation or scaling their area**, making it useful for effects like italic text, perspective simulation, or slanted surfaces.
