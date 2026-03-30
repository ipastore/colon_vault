# Experimentos
Se corrieron 5 nuevos experimentos: 

- empr_gt_pos_null_10_lg0.01_lr5e-5_ov0007_NoKFCull_**300**
https://empr-gt-pos-null-10-lg0-01-lr5e-5-ov0007-nokfcull-300.loca.lt

- empr_gt_pos_null_10_lg0.01_lr5e-5_ov0007_NoKFCull_**100**
https://empr-gt-pos-null-10-lg0-01-lr5e-5-ov0007-nokfcull-100.loca.lt

- empr_gt_pos_null_10_lg0.01_**lr1e-6**_ov0007_NoKFCull_300
https://empr-gt-pos-null-10-lg0-01-lr1e-6-ov0007-nokfcull-300.loca.lt

- empr_gt_pos_null_10_lg0.01_lr5e-5_ov0007_NoKFCull_300_**adamw**
 https://empr-gt-pos-null-10-lg0-01-lr5e-5-ov0007-nokfcull-300-adamw.loca.lt
 
- empr_gt_pos_null_10_lg0.01_lr5e-5_ov0007_NoKFCull_300_**nockpt**
https://empr-gt-pos-null-10-lg0-01-lr5e-5-ov0007-nokfcull-300-nockpt.loca.lt

*Password: 155.210.155.93*
## Naming
***gt_pos*:** Positive matches del mapa SLAM
***null*:** No se utiliza repesca de positivos con ningún threshold
***10*:** hard negatives con un threshold de 10 píxeles
***lg0.01*:** threshold de lightglue a 0.01
***lr1e-6*:** learning rate 1e-6)
***ov0007*:** usando imágenes con overlap desde 0.0 a 0.7. Además se usaron 4 bines (0.0 - 0.175); (0.175 - 0.350) ; (0.350 - 0.525) ; (0.525 - 0.7) 
***NoKFCull*:** utilizando mapas del EndoCartoscope sin KeyFrame Culling. Por ende tenemos 1 KeyFrame cada 5 frames, resultando en más pares de imágenes.
**300:** por cada epoch se carga un nuevo DataLoader que samplea por lo menos 300 pares de imágnes por mapa. Si el mapa no tiene suficiente imágenes, se descarta. 
***adamw***: Se utilizó adam con weight decay en vez de adam.
***nockpt:*** training from scartch vs fine-tuninig.

> [!NOTE]
> En este caso la epoch no está definida como las itearaciones para ver todo el dataset, sino las iteraciones necesarias para cubrir el size del DataLoader. Así las epochs de **100** son más cortas que la de **300**.
> 

---

# Observaciones

## 300 (base)
Al samplear 300 pares de imágenes por mapa por cada epoch se puede apreciar que en las primeras iteraciones mejora la loss y las métricas del val dataset. Con el correr de las iteraciones diverge la loss positiva, observando overfitting. 

## 100
Lo mismo que **300** pero con curvas más ruidosas. Al tener un subset menor de datos, las curvas cambian más de epoch a epoch. 

## nockpt
- Al hacer un entrenamiento from scracth, se confirma que la red aprende rápido. Sin embargo no llega a las métricas de un fine-tuning con pesos iniciales. Esto es por falta de datos, ya que se ve que tiende a overfitear. Quizás con más datos se puede entrenar sin homografías y sin los pesos de megadepth.
  
  "LightGlue follows the supervised training setup of SuperGlue. We first pre-train the model with synthetic homographies sampled from 1M images [48]. Such augmentations provide full and noise-free supervision but require careful tuning. LightGlue is then fine-tuned with the MegaDepth dataset [36], which includes 1M crowd-sourced images depicting 196 tourism landmarks, with camera calibration and poses recovered by SfM and dense depth by multi-view stereo. Because large models easily overfit to such distinctive scenes, the pre-training is critical to the generalization of the model but was omitted in recent follow-ups [7, 62]." (4. Details Matters, Lightglue paper)

## lr1e-6
En este caso no se nota overfitting y además hay una leve mejora de las métricas en val. Sin embargo, a través de la inspección de las imágenes se nota que la red aprende poco.

## adamw
No se observan diferencias con **300**


# Próximos entrenamientos

1. Se propone bajar el threshold de lightglue de 0.01 a 0.001 con el fin de poder recuperar más matches en zonas de bajo overlap.
2. Probar learning rate de 1e-05
3. Probar learning rate de 5e-06
4. Probar con overal de 0.1 a 0.7 y 3 bines (como Megadepth)
5. Probar sin filtro de overlap y 5 bines. 