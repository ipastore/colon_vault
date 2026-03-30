up-to-scale is valid only for pixel reprojection, not for mixing quantities with different units.

  
$$
  u_2 \sim K,(R,X_1 + t), \quad X_1 = d,K^{-1}u_1
$$
  

  If you scale geometry by (s):

  - $d \rightarrow s,d$
  - $X_1 \rightarrow s,X_1$
  - then you also need $(t \rightarrow s,t$)

  to keep the same pixels ($u_2$).

  So you can combine translation and depth, but only when both are in the same scale.

  Your stats show exactly this:

  - z_mean ~ 34.55 vs depth_mean ~ 0.041
  - ratio median z/depth ~ 833
  - after multiplying depth by ~833, error drops a lot

  That means LD depth and COLMAP tvec are not in identical units yet (or not perfectly).

  Why this can happen even if LD was retrained with COLMAP sparse depth:

  - COLMAP monocular reconstructions have arbitrary scale (often different per map/sequence).
  - Sparse supervision may not fully lock absolute scale.
  - Possible definition mismatch (z-depth vs ray-distance depth).
  - Small intrinsics/crop/mask mismatches and outliers.

  Why PLYs can still “look aligned”:

  - Visual alignment tolerates 10–20% scale error.
  - Viewer/registration may apply implicit similarity alignment.
  - Shape can match while metric scale is still off.

  Practical rule for your pipeline:

  1. Estimate one robust scale (s) per map/sequence (median z/depth is good).
  2. Use depth_aligned = s * depth when using COLMAP poses.
  3. For reprojection/warping, ensure depth and tvec share the same scale convention.

## Solution?


  Sea $X_w^i \in \mathbb{R}^3$ un punto 3D de COLMAP visible en el frame (a o b).
  Con la pose cámara-mundo ($T_{w\to a}=(R_a,t_a)$), su profundidad geométrica en cámara es:

  
$$
  Z_a^i = \big(R_a X_w^i + t_a\big)_z,
  \qquad
  Z_b^i = \big(R_b X_w^i + t_b\big)_z
  
$$

  donde (($\cdot)_z$) es la tercera componente (eje óptico).

  Sea $d_a^i$ y $d_b^i$ la profundidad predicha por la red en el mismo píxel, y ($v_a^i, v_b^i\in{0,1}$) sus máscaras de validez.

  Definimos conjuntos válidos:
$$

  \mathcal{G}_a={, i:\ v_a^i=1,\ d_a^i>0,\ Z_a^i>\varepsilon,\ \mathrm{isfinite}(d_a^i),\ \mathrm{isfinite}(Z_a^i),}

$$

  


  Para cada frame, calculamos razones COLMAP/profundidad:

$$
  r_a^i=\frac{Z_a^i}{d_a^i},\quad i\in\mathcal{G}_a
$$
$$
  r_b^i=\frac{Z_b^i}{d_b^i},\quad i\in\mathcal{G}_b
$$
  
  Estimador robusto de escala (mediana):

  
$$
  s_a=\operatorname{median}_{i\in\mathcal{G}_a}(r_a^i),
  \qquad
  s_b=\operatorname{median}{i\in\mathcal{G}_b}(r_b^i)
$$
  

  Luego alineamos la profundidad de red a la escala de COLMAP:

  
$$
  \hat d_a^i = s_a, d_a^i,\qquad
  \hat d_b^i = s_b, d_b^i
  
$$

  Así, ($\hat d$) queda en la misma escala que (Z) (y por tanto consistente con (t) de COLMAP).