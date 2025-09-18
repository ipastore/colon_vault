# Scaling and then computing diff is the same as scaling to unit vector and compute diff

Aquí va una **comparación matemática** entre tu nueva función (que escala t_est y luego divide por ‖t_colmap‖) y la vieja que computa la diferencia entre **vectores unitarios**. Veremos que en realidad ambas **producen el mismo valor**.

---

## Función nueva

```python
def translation_error_realative_to_t_colmap(t_colmap, t_est):
    scale_factor = np.linalg.norm(t_colmap) / np.linalg.norm(t_est)
    t_est_scaled = t_est * scale_factor
    err = np.linalg.norm(t_colmap - t_est_scaled) / np.linalg.norm(t_colmap)
    return err
```
![[Screenshot 2025-03-31 at 15.53.49.png]]


---

## Función vieja (unitaria)

```python
def translation_error_unit(t_colmap, t_est):
    t_colmap_unit = t_colmap / np.linalg.norm(t_colmap)
    t_est_unit    = t_est    / np.linalg.norm(t_est)
    err = np.linalg.norm(t_colmap_unit - t_est_unit)
    return err
```
![[Screenshot 2025-03-31 at 15.53.55.png]]
---

## Demostración de equivalencia

![[Screenshot 2025-03-31 at 15.54.01.png]]

Es decir, **tu función que escala la traslación estimada y luego divide por la norma de t_colmap produce el mismo valor** que la función que simplemente computa la distancia entre los vectores **unitarios** (uno de COLMAP y el otro de tu estimación). Ambas devuelven un valor entre 0 y 2 (donde 0 indica direcciones idénticas y 2 indica direcciones opuestas, en 3D).

---

## Ejemplo Numérico

Tomemos un caso sencillo:

![[Screenshot 2025-03-31 at 15.54.21.png]]

---

## Resumen

- Tu **función nueva** y la **vieja** (la que mide la diferencia entre vectores unitarios) son matemáticamente equivalentes: ambas miden **error de dirección** (ignorando la escala).
    
- Si la dirección estimada es opuesta (180°), la salida puede llegar hasta 2. Si es igual (0°), la salida es 0.
    
- El hecho de que en una uses “normalizo y resto” y en la otra “resto tras escalar y normalizo al final” termina siendo la **misma operación**, como demuestra la álgebra.

## 3 ways but really similar
A grandes rasgos, **sí**: todas esas variantes (escalar y dividir por la norma, medir similitud de coseno, medir ángulo en grados, o medir la distancia entre vectores unitarios de 0 a 2) **están midiendo la misma magnitud geométrica**: la **discrepancia de dirección** entre $t_{\mathrm{colmap}}$ y $t_{\mathrm{est}}$. La diferencia es sólo en **cómo** expresas el resultado numérico (rango distinto, interpretación distinta). Abajo te muestro un ejemplo claro.

---

# Tres formas de medir el “error de dirección”**

### 1) **Distancia 0..2 (o “error relativo tras escalar”)**

$\text{err}_{\mathrm{0..2}} = \frac{\|\mathbf{t}_c - \alpha\,\mathbf{t}_e\|}{\|\mathbf{t}_c\|}, \quad \alpha = \frac{\|\mathbf{t}_c\|}{\|\mathbf{t}_e\|}.$

Esto, como vimos, equivale a medir $\|\mathbf{u}_c - \mathbf{u}_e\|$ si $\mathbf{u}_x$ son vectores unitarios. Su rango es [0, 2].

### 2) **Cosine similarity** (rango [-1,\,1])

$\mathbf{u}_c \cdot \mathbf{u}_e = \cos(\theta).$

• 1 → misma dirección (0°).

• -1 → direcciones opuestas (180°).

• 0 → ortogonales (90°).

### 3) **Ángulo en grados** (rango (0, 180))

$\theta = \arccos(\mathbf{u}_c \cdot \mathbf{u}_e).$

• 0° → misma dirección,

• 180° → direcciones opuestas.

**Las tres expresiones miden la misma propiedad** (el desalineamiento de la dirección) pero transforman el resultado a rangos distintos.

---

## Implementacion en python

``` python
import numpy as np

def cosine_similarity(t_colmap: np.ndarray, t_est: np.ndarray) -> float:
    """
    Retorna el dot product de los vectores unitarios en el rango [-1,1].
    1  => direcciones idénticas
    -1 => direcciones opuestas
    0  => ortogonales
    """
    # Normalizamos ambos vectores
    norm_c = np.linalg.norm(t_colmap) + 1e-9
    norm_e = np.linalg.norm(t_est)    + 1e-9

    u_c = t_colmap / norm_c
    u_e = t_est    / norm_e
    
    return np.dot(u_c, u_e)

def angle_between(t_colmap: np.ndarray, t_est: np.ndarray) -> float:
    """
    Retorna el ángulo en grados, entre 0 y 180.
    0   => misma dirección
    180 => direcciones opuestas
    """
    cos_sim = cosine_similarity(t_colmap, t_est)
    cos_sim = np.clip(cos_sim, -1.0, 1.0)
    angle_rads = np.arccos(cos_sim)
    return np.degrees(angle_rads)

def error_relative_0_to_2(t_colmap: np.ndarray, t_est: np.ndarray) -> float:
    """
    Equivalente a escalar t_est a la misma magnitud que t_colmap
    y dividir norma de la resta por la norma de t_colmap.
    """
    scale_factor = np.linalg.norm(t_colmap) / (np.linalg.norm(t_est) + 1e-9)
    t_est_scaled = t_est * scale_factor
    return np.linalg.norm(t_colmap - t_est_scaled) / (np.linalg.norm(t_colmap) + 1e-9)
```


---

## Ejemplo Numérico Comparativo
  
Tomemos 3 ejemplos de vectores:

``` python
import numpy as np

# Ejemplos:
t_c1 = np.array([1, 0, 0], dtype=float)
t_e1 = np.array([1, 0, 0], dtype=float)  # misma dirección

t_c2 = np.array([1, 0, 0], dtype=float)
t_e2 = np.array([0, 1, 0], dtype=float)  # ortogonales

t_c3 = np.array([1, 0, 0], dtype=float)
t_e3 = np.array([-1, 0, 0], dtype=float) # opuestas
```

**Caso 1: direcciones iguales**

• Cosine similarity = 1

• Ángulo = 0°

• Distancia 0..2 = 0
  
**Caso 2: vectores ortogonales**
``` python
cos = cosine_similarity(t_c2, t_e2)  # 0
angle = angle_between(t_c2, t_e2)    # 90 grados
err_0to2 = error_relative_0_to_2(t_c2, t_e2)  # ~1.4142
```

• Cosine similarity = 0

• Ángulo = 90°

• Distancia 0..2 = √2 ≈ 1.4142

  
**Caso 3: opuestos**

``` python
cos = cosine_similarity(t_c3, t_e3)  # -1
angle = angle_between(t_c3, t_e3)    # 180 grados
err_0to2 = error_relative_0_to_2(t_c3, t_e3)  # 2
```

• Cosine similarity = -1

• Ángulo = 180°

• Distancia 0..2 = 2

---

**4) Conclusión**

  
Los tres enfoques:

1. **Cosine Similarity** => [-1, 1]

2. **Ángulo en grados** => [0, 180]

3. **Distancia 0..2** entre vectores unitarios (o escala “COLMAP”) => [0, 2]


**miden la misma desviación de dirección**. Tienen distintas interpretaciones y distintos rangos de salida, pero están estrechamente relacionadas matemáticamente:
  
$\|\mathbf{u} - \mathbf{v}\| = \sqrt{2 - 2(\mathbf{u}\cdot\mathbf{v})} = \sqrt{2 - 2\cos(\theta)}.$

• Cuando $\mathbf{u} \cdot \mathbf{v}=1$, la distancia es 0, ángulo 0°.

• Cuando $\mathbf{u} \cdot \mathbf{v}=-1$, la distancia es 2, ángulo 180°.

• Cuando $\mathbf{u} \cdot \mathbf{v}=0$, la distancia es √2, ángulo 90°.

Así que en efecto:

**“Escalar y dividir”** (dando un resultado 0..2),

**“Cosine similarity”** (dando -1..1)

y **“Ángulo en grados”** (0..180)

**son maneras distintas** de reportar cuánto se desalinean dos vectores.