1. Primero forward_masked en LightGlue
  2. Después filtrar padded features en la GT

  No son redundantes. Arreglan dos problemas distintos.

####  Por qué 1 primero

  Si los puntos sintéticos entran al forward, contaminan las activaciones internas de LightGlue:

  - self-attention
  - cross-attention
  - descriptors refinados
  - logits de matching

  Eso significa que el problema aparece antes de la loss. Aunque luego filtres en la GT, ya dejaste que tokens falsos modifiquen las representaciones de los reales.

  Matemáticamente, si el attention hace algo como
  $$
  \mathrm{Attn}(Q,K,V)=\mathrm{softmax}\left(\frac{QK^\top}{\sqrt{d}}\right)V
  $$

  y metes tokens padded en $K,V$, entonces los tokens reales atienden también a basura. Luego el output ya sale perturbado. Filtrar solo en la loss no deshace eso.

  Por eso, si quieres corrección de modelo, la solución estructural es:
  - los padded tokens no deben participar en atención
  - eso implica masked_forward

#### Por qué 2 también hace falta

  Aunque arregles el forward, si la GT sigue incluyendo padded keypoints:

  - generas positivos/negativos/ignorados sobre puntos falsos
  - alteras la distribución de la loss
  - puedes crear logits extremos inútiles
  - en BF16/FP16 eso puede amplificar inestabilidad numérica

  O sea:
  - 1 protege el modelo
  - 2 protege la supervisión

  Si solo haces 1:
  - el forward será más limpio
  - pero la loss todavía puede estar mal definida sobre tokens artificiales

  Si solo haces 2:
  - la loss mejora
  - pero el forward sigue mezclando tokens falsos con reales

  Entonces, cuál es más importante para tu caso de exploding gradients

  Por lo que describes:
  - “explosiona con flash_attention”
  - “el problema aparece cuando padded_features entran en la loss”

  mi diagnóstico es:
  - hay un componente de supervisión incorrecta en GT
  - pero también hay un componente de contaminación del forward

  Si tengo que priorizar por impacto técnico:
  - más correcto conceptualmente: 1 + 2
  - más probable de darte alivio rápido a la explosión: 2
  - más necesario para que el modelo no procese basura: 1

#### Recomendación práctica

  Hazlo en dos pasos:
  Paso A
  Filtra padded points en GT generation.

  - Es el cambio más directo para cortar la fuente de losses absurdas.
  - Además no te rompe FlashAttention en el forward.

  Paso B
  Añade masked_forward para LightGlue, pero solo si aceptas que:
  - con mask != None, en PyTorch 2.0 no usarás flash kernel
  - probablemente perderás parte de la ventaja de throughput

  Eso te deja dos modos razonables:
  - Modo estable de entrenamiento:
      - filtrar padded en GT
      - no usar padded tokens en GT
      - idealmente también masked_forward
  - Modo rápido:
      - no usar masked_forward
      - pero entonces obligatorio filtrar padded en GT
      - y mejor aún evitar padding sintético siempre que puedas

## Que pasa si solo aplico la GT limpia?

[[masked_forward_vs_gt_masking]]

## Por que sin flash debería ser más estable?
[[flash_vs_fp16_bf16]]
