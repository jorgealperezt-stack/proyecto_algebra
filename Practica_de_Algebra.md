# Práctica de Álgebra - SVM con base teórico‑algebraica


**Estudiante:** Jorge Alberto Pérez Tortolero
**DNI:** 54705536J
**Asignatura:** 02GIAR_10_A_2025-26_Álgebra Lineal
**fecha de entrega:** 19 de enero de 2026

---

## Parte A: Subespacios y Proyecciones

### A1. Los cuatro subespacios fundamentales
Para empezar analizamos la matriz de datos $X \in \mathbb{R}^{n \times d}$ (donde $n$ son las muestras y $d$ las características):

1.  **$Im(X)$:** Espacio de columnas. En clasificación, representa el espacio generado por las caracteristicas de los datos.
2.  **$Ker(X)$:** Son los vectores $v$ tales que $Xv = 0$. Si hay un vector aquí, significa que hay redundancia en las características.
3.  **$Im(X^\top)$:** Aquí se presentan las filas de nuestros datos. **Importante:** El vector de pesos $w$ del SVM se encuentra en este espacio (o en $\mathbb{R}^d$), ya que $w$ actúa sobre las muestras $x$.
4.  **$Ker(X^\top)$:** Es el complemento ortogonal del espacio de columnas. En regresión, aquí es donde muestra el "error" que no podemos explicar linealmente.

**Relación con clasificación lineal:** Nuestro hiperplano está definido por un vector $w$. Para que $w$ clasifique bien, debe tener una proyección no nula sobre las direcciones donde varían los datos ($Im(X^\top)$). Si $w$ estuviera en $Ker(X)$, el producto $Xw$ sería 0 para todos los puntos.

### A2. Proyección vs. Margen
Comparamos dos ideas clave:
* **Proyección ($\hat{y} = Py$):** Busca minimizar la distancia vertical de los puntos a la recta. Intenta "pasar por el medio" de los datos para explicarlos.
* **Maximizar Margen ($\gamma = 1/||w||_2$):** El SVM busca la via más ancha posible que separe las clases. Mientras la proyección busca minimizar el error de representar los datos fielmente, el SVM busca maximizar la capacidad de generalización o separación, ignorando la distribución de la clase.

**Conclusión:** La proyección comprime la información en un subespacio, mientras que el SVM busca una dirección en el subespacio que garantice la máxima seguridad geométrica.

---

## Parte B: Hiperplano y Margen en Conjunto A

### B1. Cálculo del separador
Buscamos un $w$ proporcional a $(1,1)$, mejor dicho, $w = (a, a)$ y $b=0$.
Usamos la condición canónica en el punto $x_1 = (2, 2)$ que es de la Clase +1 ($y_1 = +1$).

La condición es:
$$y_i(w \cdot x_i + b) \geq 1$$

Sustituyendo para $x_1$:
$$1 \cdot ((a, a) \cdot (2, 2) + 0) = 1$$
$$2a + 2a = 1 \implies 4a = 1 \implies a = 0.25$$

Por lo tanto, nuestro vector de pesos es:
$$w = (0.25, 0.25)$$

**Comprobamos con x_4 ($x_4 = (-2, -2), y=-1$):**
$$(-1) \cdot ((0.25, 0.25) \cdot (-2, -2)) = (-1) \cdot (-0.5 - 0.5) = (-1)(-1) = 1$$
Se cumple la igualdad ($\ge 1$).

### B2. Margen y Distancias
Primero calculamos la norma de $w$:
$$||w||_2 = \sqrt{0.25^2 + 0.25^2} = \sqrt{0.0625 + 0.0625} = \sqrt{0.125} \approx 0.3536$$

El margen geométrico $\gamma$ es:
$$\gamma = \frac{1}{||w||_2} = \frac{1}{\sqrt{0.125}} = \sqrt{8} \approx 2.828$$

Calculamos las distancias de los otros puntos al hiperplano: $dist = \frac{|w \cdot x|}{||w||}$:

* **Para $x_2(2,3)$:**
    $$w \cdot x_2 = 0.25(2) + 0.25(3) = 0.5 + 0.75 = 1.25$$
    $$dist = \frac{1.25}{0.3536} \approx 3.535$$
* **Para $x_3(3,2)$:** Simétrico a $x_2$, misma distancia $\approx 3.535$.
* **Para $x_5(-3,-2)$:**
    $$w \cdot x_5 = 0.25(-3) + 0.25(-2) = -0.75 - 0.5 = -1.25$$
    $$dist = \frac{|-1.25|}{0.3536} \approx 3.535$$

**Candidatos a Vectores Soporte:**
Los vectores soporte son los que están *exactamente* en el margen (distancia mínima).
* $x_1$ y $x_4$ tienen distancia geométrica $\approx 2.828$ (cumplen la ecuación con $=1$). **Estos son los soportes.**
* $x_2, x_3, x_5, x_6$ están más lejos ($> 2.828$), por lo que no definen el margen.

### B3. Normal Unitario y Confianza
Sea $n = \frac{w}{||w||}$.
El valor $y_i(n \cdot x_i)$ nos dice cuántas veces "cabe" el margen hasta el punto.
Para $x_1$, el valor es exactamente $\gamma$ (el borde de la carretera).
Para $x_2$, el valor es mayor que $\gamma$.
**Razonamiento:** Cuanto mayor sea este valor respecto a $\gamma$, un valor mayor implica que el punto está más alejado del hiperplano que los vectores soporte, su clasificación es más robusta ante posibles pertubaciones.

---

## Parte C: Matriz de Gram y Separabilidad

### C1. Matriz de Gram de A
Calculamos los productos escalares $x_i \cdot x_j$. Notamos que los puntos de clase +1 están en el primer cuadrante (positivos) y los de clase -1 en el tercero (negativos).

Ejemplos:
* $x_1 \cdot x_1 = 2(2)+2(2) = 8$
* $x_1 \cdot x_4 = 2(-2)+2(-2) = -8$ (Entre clases)
* $x_1 \cdot x_2 = 2(2)+2(3) = 10$ (Intra clase)

La matriz aproximada (simplificada conceptualmente por bloques):

$$
G_A = \begin{pmatrix}
 8 & 10 & 10 & -8 & -10 & -10 \\
10 & 13 & 12 & -10 & -12 & -13 \\
10 & 12 & 13 & -10 & -13 & -12 \\
-8 & -10 & -10 & 8 & 10 & 10 \\
-10 & -12 & -13 & 10 & 13 & 12 \\
-10 & -13 & -12 & 10 & 12 & 13
\end{pmatrix}
$$

**Bloques:**
* **Intra-clase (Bloques 1-3 y 4-6):** Valores muy positivos.
* **Entre-clase (Intersección):** Valores muy negativos.

### C2. Interpretación
Que los productos intra-clase sean positivos significa que los vectores apuntan en la misma dirección (ángulo agudo). Que los productos entre clases sean negativos significa que apuntan en direcciones opuestas (ángulo obtuso).
Valores intraclase-positivos y entre-clase negativos indican que los vectores de la misma clase forman ángulos agudos entre sí y obtuso con la clase opuesta, facilitando la separación lineal. Si hubiera productos positivos grandes entre clases, significaría que las clases están mezcladas o solapadas, dificultando la separación lineal.

---

## Parte D: Primal, Dual y Vectores Soporte

### D1. El vector w como combinación lineal
En el problema dual de Lagrange, la condición de optimalidad (KKT) nos dice que el vector de pesos óptimo es una combinación lineal de los datos de entrenamiento:
$$w = \sum \alpha_i y_i x_i$$
La clave es que $\alpha_i$ (el multiplicador de Lagrange) representa la "fuerza" o importancia del punto $x_i$ para definir la frontera.
* Si un punto está lejos y bien clasificado (seguro), no necesitamos preocuparnos por él, así que su $\alpha_i = 0$.
* Si un punto está justo en el margen (es difícil), es un **Vector Soporte**, y tendrá $\alpha_i > 0$. Solo estos puntos "sostienen" el hiperplano.

### D2. Identificación en Conjunto A
Mirando nuestros cálculos de la Parte B:
* **Clase +1:** El punto $x_1(2,2)$ satisface la ecuación canónica exactamente igual a 1. Es un vector soporte ($\alpha_1 > 0$). Los puntos $x_2$ y $x_3$ dan $>1$, así que sus $\alpha$ son 0.
* **Clase -1:** El punto $x_4(-2,-2)$ satisface la ecuación exactamente. Es un vector soporte ($\alpha_4 > 0$).

---

## Parte E: Kernels

### E1. Kernel Polinomial en A
Fórmula: $K_p(x, z) = (x \cdot z + 1)^2$.
Comparamos producto escalar vs Kernel para un par de clases opuestas ($x_1$ vs $x_4$):
* Producto escalar (lineal): $x_1 \cdot x_4 = -8$. (Indica oposición).
* Kernel: $K_p(x_1, x_4) = (-8 + 1)^2 = (-7)^2 = 49$.

**Análisis:**El kernel proyecta los datos a un espacio de mayor dimensión donde la similitud se mide diferente. Al elevar al cuadrado, el kernel transforma el producto escalar negativo en una alta similitud positiva en el espacio de características, lo cuál no es necesario dado que los datos son separables linealmente. Sin embargo, en este caso simple separable, el kernel polinomial de grado 2 podría estar complicando innecesariamente la geometría simple que ya teníamos con el producto escalar negativo.

### E2. Kernel RBF en Conjunto B (Casi separable)
Fórmula: $K_r(x, z) = \exp(-\frac{||x-z||^2}{2})$.
Tenemos Clase +1: $u_1(1,2)$, Clase -1: $v_1(0.5, 0.5)$ y $v_2(2,2)$.

* **Distancia $u_1$ a $v_1$:**
    $||u_1 - v_1||^2 = (1-0.5)^2 + (2-0.5)^2 = 0.25 + 2.25 = 2.5$.
    $K_r(u_1, v_1) = e^{-1.25} \approx 0.286$.

* **Distancia $u_1$ a $v_2$:**
    $||u_1 - v_2||^2 = (1-2)^2 + (2-2)^2 = 1 + 0 = 1$.
    $K_r(u_1, v_2) = e^{-0.5} \approx 0.606$.

**Comentario:**
El RBF mide proximidad local. $u_1$ tiene una "afinidad" (kernel) más alta con $v_2$ (0.606) que con $v_1$ (0.286) simplemente porque está más cerca geométricamente, aunque sean de distinta clase.
Sin embargo, el RBF se basa en la distancia euclidea. Aunque u_1 y v_1 son de clases distintas , el kernel da un valor bajo. Con v_2 , aunque es de otra clase, la similitud es mayor por cercanía geométrica. El RBF permite generar fronteras de decisión no lineales que se ajustan, algo imposible para un separador lineal rígido. En el Conjunto B, el RBF permitiría aislar las clases creando una frontera curva y ajustada, cosa que un plano rígido no puede hacer bien si los datos están intercalados.

---

## Parte F: Soft-margin y Ruido

### F1. Punto ruidoso $x_7$
Nuevo punto $x_7 = (0.2, 0.2)$ con etiqueta $y_7 = -1$ (debería ser negativo).
Usamos nuestro $w=(0.25, 0.25)$:
$$w \cdot x_7 + b = 0.25(0.2) + 0.25(0.2) = 0.05 + 0.05 = +0.1$$

**Evaluación:**
* El clasificador predice signo **positivo** ($+0.1$).
* La etiqueta real es **negativa** ($-1$).
* **Conclusión:** El punto x7 tiene etiqueta -1 pero el modelo predice +0.1. Es un error de clasificación y viola el margen.

### F2. Efecto del parámetro C
El parámetro $C$ penaliza la suma de las variables de holgura ($\sum \xi_i$). Un $C$ muy alto forzará al modelo a intentar clasificar bien x_7, probablemente reduciendo el margen global y arriesgando overfitting. Un  $C$ bajo permitirá que x_7 sea mal clasificado a cambio de mantener un ||w||pequeño (margen amplio) para el resto de datos.

1.  **Si aumentamos C mucho (C $\to \infty$):**
    El algoritmo intentará a toda costa clasificar bien a $x_7$. Para hacerlo, tendrá que rotar o mover el hiperplano drásticamente, probablemente reduciendo mucho el margen $\gamma$ para los demás puntos. El modelo se vuelve muy sensible al ruido (overfitting).
2.  **Si C es bajo:**
    El algoritmo quiere mantener un margen ancho para la mayoría de los datos, aceptando que $x_7$ es un error. La norma $||w||_2$ se mantiene pequeña (margen grande).


