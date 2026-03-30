Buenas, 

Aquí les dejo los nuevos experimentos donde he modificando el algoritmo para hacer matching con brute force. Hay implementadas 4 variantes:

- **(1) BF-Bidireccional** *("bi")*: Chequea bidireccionalidad de los matches, no se aplica ningún otro filtro. En consecuencia, este es el matching más flexible (más cantidad de matches) para todos los casos.
- **(2) BF-NN-Bidireccional** *("bi_nn")*: a (1) se le suma un filtro donde el descriptor de la primera imagen 1 (d1) debe tener una distancia euclidiana (L2) al descriptor de la imagen 2 (d2) menor a un threshold.
	Como escribió Monti: 
	
	a. d1 y d2 ortogonales, no tienen nada que ver, cos=0, correlación = 0 %, distancia=1.41
    
	b. d1 y d2 formando cos=0.8, correlación 80 %, distancia = 0.63, ángulo +-37 deg
    
	c. d1 y d2 formando cos=0.9, correlación 90 %, distancia = 0.45, ángulo +-26 deg
    
	d. d1 y d2 colineales, son el mismo vector, cos=1, correlación 100 %, distancia 0, ángulo 0 deg
    
	e. d1 y d2 van en direcciones opuestas, cos=-1, correlación -100 %, distancia 2, ángulo +-180 deg

- **(3) BF-DR-Bidireccional** *("bi_ratio")*: a (1) se le suma otro filtro. Primer se calculan las distancias euclidianas del mejor match (D1) y segundo mejor match (D2). Si D1/D2 < Th_NDDR se acepta el match. Por ejemplo si Th_NDDR 0.7, ente caso quiere decir que  D1 es un 30 % menor que el D2.

- (4) **BF-NNDR-Bidireccional** *("bi_ratio_nn-thresh")*: a (1) se le suman los filtros de (2) y (3). En consecuencia este es el algoritmo más restrictivo.

Para cada variante probé: 
- *nn-thresh:* 0,7; 0,8; 0,9; 1
- *ratio-thresh:* 0,7, 0,8, 0,9

Luego de una inspección visual general decidí compartir los resultados con nn-thresh = 0,9 y ratio-thresh = 0,9. Esto quiere decir que la distancia entre d1 y d2 es menor a 0,9 (cos = 0.595, correlacion 59,5%, ángulo +-53deg ) y que la diferencia entre el mejor y segundo mejor (D1/D2) match es de por lo menos un 10%. 


[[escrito_bf]]

# Emparejamiento Brute Force

A continuación dejo los experimentos y variantes implementadas para el matching de Brute Forece con las siguientes variantes: **BF-Bidireccional**, **BF-NN-Bidireccional** (umbral **TH_D**) y **BF-NNDR-Bidireccional** (umbrales **TH_D** y **TH_NNDR**). Además, incluyo una variante **solo-ratio** (TH_NNDR sin TH_D) para análisis comparativo. 

---
### Nota:  Relación distancia L2 y distancia coseno

Sean \($d_1, d_2 \in \mathbb{R}^n$\) con $(\|d_1\|_2=\|d_2\|_2=1)$.

- **Coseno / correlación:** $(c_{12}=d_1^\top d_2 \in [-1,1]).$
- **Distancia euclídea:** 
$$
D(d_1,d_2)=\sqrt{2(1-c_{12})} \in [0,2].
$$
  - Ortogonales: $(c=0 \Rightarrow D=\sqrt{2}\approx 1.41)$.
  - $c=0.8 \Rightarrow D\approx 0.63$  ; $ángulo \approx +/-37^\circ$.
  - $c=0.9 \Rightarrow D\approx 0.45$  ; $ángulo \approx +/-26^\circ$.
  - Colineales:  $c=1 \Rightarrow D = 0$  ; $ángulo = 0^\circ$.
  - Opuestos:  $c= -1 \Rightarrow D = 2$  ; $ángulo = 180^\circ$.

---
## Variantes Implementadas

### (1) **BF-Bidireccional** *(alias: `bi`)*
Emparejamiento BF con **verificación bidireccional**. No se aplican filtros adicionales. Al ser la variante más laxa se puede utilizar como baseline.

---
### (2) **BF-NN-Bidireccional** *(alias: `bi_nn`)*
A **(1)** se le agrega un filtro. 
Para cada punto de la **imagen 1**, buscar en la **imagen 2** su vecino más cercano con distancia $(D_1)$. Siendo $\mathbf{TH\_D}$ el threshold de distancia se acepta el match si $D_1 < \mathbf{TH\_D}$.

---
### (3) **BF-NNDR-solo** *(alias: `bi_ratio`)*
A **(1)** se le agrega otro filtro.
Para cada punto en la **imagen 1**, obtener $(D_1)$ (mejor) y $(D_2)$ (segundo mejor). Siendo $\mathbf{TH\_NNDR}$ el threshold de ratio se acepta el match si $(\dfrac{D_1}{D_2} < \mathbf{TH\_{NNDR}})$

---
### (4) **BF-NNDR-Bidireccional** *(alias: `bi_ratio_nn-thresh`)*

A **(1)** se le suman los filtros de **(2)** y **(3)**. En consecuencia esta es la variante más estricta

---
## Elección de thresholds
Para todas las variantes probé los siguientes thresholds: 
  - $\mathbf{TH\_D} \in \{0.7,\,0.8,\,0.9,\,1.0\}$.
  - $\mathbf{TH\_{NNDR}} \in \{0.7,\,0.8,\,0.9\}$.

Luego de una inspección visual decidí compartir los resultados de $\mathbf{TH\_D}=0.9$ y $\mathbf{TH\_{NNDR}}=0.9$.

$$
 TH_D=0.9 \Rightarrow D_1<0.9 \Rightarrow c=0.595 \Rightarrow   ángulo \approx +/-53^\circ.
  $$
  $$
  TH_{NNDR}=0.9 \Rightarrow \dfrac{D_1}{D_2}<0.9
$$
---