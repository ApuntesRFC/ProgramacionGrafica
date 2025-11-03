> [!summary]  
> A **matrix** is a rectangular array of numbers arranged in **rows** and **columns**.  
> It is a fundamental mathematical tool for performing **transformations** (translation, rotation, scaling) in computer graphics.

---

### 1. Definition

A matrix is written as:

$$  
A =  
\begin{pmatrix}  
1 & 2 & 3 \\  
4 & 5 & 6  
\end{pmatrix}  
$$

This example is a **2×3 matrix** (2 rows, 3 columns).

Each element is indexed as $A_{ij}$, where:

- $i$ → row number
    
- $j$ → column number
    

So in this example, $A_{21} = 4$ (row 2, column 1).

---

### 2. Dimensions

Matrices are described by their dimensions **(rows × columns)**:

|Matrix Type|Example|Description|
|---|---|---|
|2×2|$\begin{pmatrix} a & b \ c & d \end{pmatrix}$|Square matrix|
|3×1|$\begin{pmatrix} x \ y \ z \end{pmatrix}$|Column vector|
|1×3|$\begin{pmatrix} x & y & z \end{pmatrix}$|Row vector|

---

### 3. Operations

Matrices support operations similar to numbers and vectors:

- **Addition:** only between matrices of the same dimensions  
    $$  
    A + B =  
    \begin{pmatrix}  
    a_{11}+b_{11} & a_{12}+b_{12} \\  
    a_{21}+b_{21} & a_{22}+b_{22}  
    \end{pmatrix}  
    $$
    
- **Subtraction:** same rule as addition
    
- **Multiplication:** more complex — defined only when the number of **columns of A = rows of B**  
    $$  
    (m \times n) \cdot (n \times p) = (m \times p)  
    $$
    

---

> [!info]
> 
> - **Matrices** are essential for representing and combining **transformations** in 2D and 3D space.
>     
> - A 4×4 matrix is standard in computer graphics for 3D transformations (it supports translation).
>     
> - In GLM:
>     
>     ```cpp
>     glm::mat3 M1;       // 3×3 matrix
>     glm::mat4 model;    // 4×4 model matrix
>     ```
>     
> - Matrices act as **functions** transforming vectors from one coordinate space to another (e.g., model → world → view → clip).
>