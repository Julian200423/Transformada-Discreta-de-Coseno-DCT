# DCT bidimensional (2D)

## 1. Definición

Para un bloque de $N \times N$ píxeles $f(x,y)$:

$$
F(u,v) = \alpha(u)\,\alpha(v) \sum_{x=0}^{N-1} \sum_{y=0}^{N-1} f(x,y)\,
\cos\!\left[\frac{(2x+1)u\pi}{2N}\right]
\cos\!\left[\frac{(2y+1)v\pi}{2N}\right]
$$

con

$$
\alpha(u) =
\begin{cases}
\sqrt{\dfrac{1}{N}} & u = 0 \\[2mm]
\sqrt{\dfrac{2}{N}} & u > 0
\end{cases}
$$

- $u$: frecuencia horizontal, $v$: frecuencia vertical.
- $F(0,0)$ es el coeficiente DC (promedio del bloque).
- Mientras más lejos de $(0,0)$, mayor la frecuencia (detalle fino).

## 2. Separabilidad

El kernel es el producto de dos cosenos 1D, por lo que la DCT 2D equivale a dos DCT 1D: primero sobre filas y luego sobre columnas. En forma matricial:

$$
F = C \, f \, C^{T}, \qquad f = C^{T} \, F \, C
$$

donde

$$
C_{u,x} = \alpha(u)\cos\!\left[\frac{(2x+1)u\pi}{2N}\right]
$$

$C$ es ortogonal ($C C^{T} = I$), por eso la inversa es la transpuesta. El costo baja de $O(N^4)$ a $O(N^3)$ o menos con algoritmos rápidos.

## 3. Imágenes base

Cada coeficiente $F(u,v)$ pondera una imagen base, producto de un coseno horizontal y uno vertical. Para $8 \times 8$ hay 64 patrones, y el bloque original es la suma ponderada de ellos:

$$
f(x,y) = \sum_{u=0}^{N-1}\sum_{v=0}^{N-1} \alpha(u)\alpha(v)\,F(u,v)\,
\cos\!\left[\frac{(2x+1)u\pi}{2N}\right]
\cos\!\left[\frac{(2y+1)v\pi}{2N}\right]
$$

## 4. Ejemplo mínimo ($N=2$)

$$
C = \frac{1}{\sqrt{2}}
\begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix},
\qquad
f = \begin{pmatrix} a & b \\ c & d \end{pmatrix}
$$

$$
\begin{aligned}
F(0,0) &= \tfrac{1}{2}(a + b + c + d) \\
F(0,1) &= \tfrac{1}{2}(a - b + c - d) \quad \text{(variación horizontal)} \\
F(1,0) &= \tfrac{1}{2}(a + b - c - d) \quad \text{(variación vertical)} \\
F(1,1) &= \tfrac{1}{2}(a - b - c + d) \quad \text{(diagonal)}
\end{aligned}
$$

Con

$$
f = \begin{pmatrix} 52 & 55 \\ 61 & 59 \end{pmatrix}
$$

se obtiene

$$
F = \begin{pmatrix} 113.5 & -0.5 \\ -6.5 & -2.5 \end{pmatrix}
$$

La energía se concentra en $F(0,0)$, lo que se aprovecha para comprimir.

## 5. Aplicaciones

- **JPEG:** bloques $8 \times 8$, se resta 128, DCT 2D, cuantización (se descartan altas frecuencias) y codificación.
- **pHash:** imagen a $32 \times 32$ en grises, DCT 2D, se toma el bloque $8 \times 8$ de bajas frecuencias, se compara con la mediana y se obtiene un hash de 64 bits.
