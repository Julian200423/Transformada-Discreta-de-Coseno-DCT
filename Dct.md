# Transformada Discreta del Coseno Tipo II (DCT-II)



## 1\. Introducción

En el análisis de señales y el procesamiento digital de imágenes, las transformadas lineales cumplen el propósito fundamental de reducir la complejidad de un problema al proyectar los datos hacia un dominio alternativo donde sus

componentes fundamentales resultan más fáciles de manipular. Si bien es frecuente asumir que la Transformada Discreta del Coseno (DCT) es simplemente una aproximación discreta de la Transformada Coseno de Fourier continua (FCT), dicha interpretación es matemáticamente imprecisa. Como demostró Gilbert Strang, la familia completa de transformadas trigonométricas discretas (DCT y DST) emerge de manera natural como las soluciones propias de un sistema físico discretizado: el oscilador armónico simple sometido a distintas combinaciones de condiciones de frontera homogéneas.   El presente informe desarrolla formalmente el origen diferencial y matricial de la DCT Tipo II (DCT-II), establece sus diferencias estructurales frente a otras transformadas ortogonales, demuestra su propiedad de ortonormalidad y expone las factorizaciones de matrices dispersas que permiten su cálculo rápido en bloques bidimensionales de $8 \\times 8$ para la compresión digital

## 2\. Origen Fisico-Mátematico

**Del oscilador Armónico a la Matriz A2.**

Para deducir la DCT-II desde primeros principios, se parte del problema continuo de autovalores asociado al oscilador armónico simple no amortiguado en el dominio espacial $x \\in \[0, \\pi]$:



&#x09;				$$\\frac{d^2u}{dx^2} + \\lambda u = 0 \\quad \\Longleftrightarrow \\quad u'' + \\lambda u = 0$$



Al imponer condiciones de frontera homogéneas de Neumann (derivada nula en ambos extremos, $u'(0) = 0$ y $u'(\\pi) = 0$), las autofunciones del sistema continuo resultan ser los modos naturales de vibración $\\cos(kx)$ con autovalores $\\lambda = k^2$ para $k = 0, 1, 2, \\dots$.   Al trasladar este modelo al dominio discreto sobre una malla equiespaciada de $N$ puntos indexados por $l = 0, 1, \\dots, N-1$, la segunda derivada se aproxima mediante diferencias finitas centrales:

&#x09;			$$-u\_{l-1} + 2u\_l - u\_{l+1} = \\lambda u\_l$$



La distinción entre las ocho variantes de la DCT radica en el punto exacto donde se imponen las condiciones de frontera de simetría (Neumann) o antisimetría (Dirichlet). Específicamente, la DCT-II surge cuando las condiciones de frontera de Neumann se aplican en los puntos medios de la malla (mid-grid) en ambos extremos, es decir, en $l = -1/2$ y $l = N - 1/2$. Esto equivale a imponer una simetría de medio paso (Half-Sample Symmetric o HSHS) dada por $u\_{-1} = u\_0$ en el extremo izquierdo y $u\_N = u\_{N-1}$ en el extremo derecho.



Al sustituir estas condiciones de borde en la ecuación en diferencias para $l = 0$ y $l = N-1$, el sistema de ecuaciones lineales se expresa en forma matricial como un problema de autovalores $A\_2 \\vec{u}\_k = \\lambda\_k \\vec{u}\_k$, donde $A\_2$ es una matriz tridiagonal simétrica de diferencias de segundo orden de orden $N \\times N$:



$$A\_2 = \\begin{pmatrix} 1 \& -1 \& 0 \& \\cdots \& 0 \\\\ -1 \& 2 \& -1 \& \\cdots \& 0 \\\\ 0 \& -1 \& 2 \& \\ddots \& \\vdots \\\\ \\vdots \& \\vdots \& \\ddots \& 2 \& -1 \\\\ 0 \& 0 \& \\cdots \& -1 \& 1 \\end{pmatrix}$$



Resolviendo este sistema algebraico, se verifica que los autovalores de $A\_2$ están dados por $\\lambda\_k = 2 - 2\\cos\\left(\\frac{k\\pi}{N}\\right)$ y que la $l$-ésima componente del $k$-ésimo autovector $\\vec{u}\_k$ corresponde exactamente al núcleo cosenoidal de la DCT-II:  



&#x09;	 $$(u\_k)\_l = \\cos\\left\[\\left(l + \\frac{1}{2}\\right)\\frac{k\\pi}{N}\\right], \\quad l, k = 0, 1, \\dots, N-1$$



A diferencia de la Transformada Discreta de Fourier (DFT), que utiliza senos y cosenos (números complejos),

La DCT emplea únicamente funciones reales basadas en cosenos.

Para un bloque de $N \\times N$ píxeles $f(x,y)$:

$$
F(u,v) = \\alpha(u),\\alpha(v) \\sum\_{x=0}^{N-1} \\sum\_{y=0}^{N-1} f(x,y),
\\cos!\\left\[\\frac{(2x+1)u\\pi}{2N}\\right]
\\cos!\\left\[\\frac{(2y+1)v\\pi}{2N}\\right]
$$

con

$$
\\alpha(u) =
\\begin{cases}
\\sqrt{\\dfrac{1}{N}} \& u = 0 \\\[2mm]
\\sqrt{\\dfrac{2}{N}} \& u > 0
\\end{cases}
$$

* $u$: frecuencia horizontal, $v$: frecuencia vertical.
* $F(0,0)$ es el coeficiente DC (promedio del bloque).
* Mientras más lejos de $(0,0)$, mayor la frecuencia (detalle fino).

## 2\. Separabilidad

El kernel es el producto de dos cosenos 1D, por lo que la DCT 2D equivale a dos DCT 1D: primero sobre filas y luego sobre columnas. En forma matricial:

$$
F = C , f , C^{T}, \\qquad f = C^{T} , F , C
$$

donde

$$
C\_{u,x} = \\alpha(u)\\cos!\\left\[\\frac{(2x+1)u\\pi}{2N}\\right]
$$

$C$ es ortogonal ($C C^{T} = I$), por eso la inversa es la transpuesta. El costo baja de $O(N^4)$ a $O(N^3)$ o menos con algoritmos rápidos.

## 3\. Imágenes base

Cada coeficiente $F(u,v)$ pondera una imagen base, producto de un coseno horizontal y uno vertical. Para $8 \\times 8$ hay 64 patrones, y el bloque original es la suma ponderada de ellos:

$$
f(x,y) = \\sum\_{u=0}^{N-1}\\sum\_{v=0}^{N-1} \\alpha(u)\\alpha(v),F(u,v),
\\cos!\\left\[\\frac{(2x+1)u\\pi}{2N}\\right]
\\cos!\\left\[\\frac{(2y+1)v\\pi}{2N}\\right]
$$

## 4\. DCT-II

Se utilizan para la implementación eficiente de transformadas ortogonales solapadas y en bancos de filtros modulados en coseno de reconstrucción.  Las transformadas que usamos para comprimir imágenes y audio (como la DCT en el formato JPEG o MP3) no son fórmulas inventadas al azar, sino la versión digital y discreta de las vibraciones naturales de un sistema físico (como una cuerda de guitarra o un sistema de masas y resortes).

$$

En física , un oscilador armonico como un pendulo o cuerda se describe mediante una ecución diferencia que relaciona la posición con su segunda derivada:

$$

$\\frac{d^2y}{dx^2} = -\\lambda y$

$$

Las funciones senos y cosenos son autofunciones. Es decir, son funciones propias donde el sistema no cambia su forma, sino solo su amplitud.  Por ejemplo, si se deriva dos veces a un seno o coseno, obtienes la misma función multiplica por una constante.($-\\lambda$)

$$
C = \\frac{1}{\\sqrt{2}}
\\begin{pmatrix} 1 \& 1 \\ 1 \& -1 \\end{pmatrix},
\\qquad
f = \\begin{pmatrix} a \& b \\ c \& d \\end{pmatrix}
$$

$$
\\begin{aligned}
F(0,0) \&= \\tfrac{1}{2}(a + b + c + d) \\
F(0,1) \&= \\tfrac{1}{2}(a - b + c - d) \\quad \\text{(variación horizontal)} \\
F(1,0) \&= \\tfrac{1}{2}(a + b - c - d) \\quad \\text{(variación vertical)} \\
F(1,1) \&= \\tfrac{1}{2}(a - b - c + d) \\quad \\text{(diagonal)}
\\end{aligned}
$$

Con

$$
f = \\begin{pmatrix} 52 \& 55 \\ 61 \& 59 \\end{pmatrix}
$$

se obtiene

$$
F = \\begin{pmatrix} 113.5 \& -0.5 \\ -6.5 \& -2.5 \\end{pmatrix}
$$

La energía se concentra en $F(0,0)$, lo que se aprovecha para comprimir.

## 5\. Aplicaciones

* **JPEG:** bloques $8 \\times 8$, se resta 128, DCT 2D, cuantización (se descartan altas frecuencias) y codificación.
* **pHash:** imagen a $32 \\times 32$ en grises, DCT 2D, se toma el bloque $8 \\times 8$ de bajas frecuencias, se compara con la mediana y se obtiene un hash de 64 bits.

