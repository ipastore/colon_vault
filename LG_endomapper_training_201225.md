20# Tamaño y sampling de Dataset

### Source Data

Se tomó la mitad de las short_seqs para train+val. Luego se separó una secuencia completa para val, tratando de mantener un 10% del tamaño para val.
### Variables que afectan el tamaño del dataset

**min y max Overlap**: Seleccionan el mínimo y máximo permitido para samplear un par de imágenes. En megadepth por default vienen 0.1 y 0.7. 
**train_num_per_scene**: Determina el número de pares de imágenes que se tomarán para el dataset por escena
**num_bins:** Seleccionan bandas para enforzar número de imágenes por bandas de overlap. Por ejemplo si se elige num_bins = 3 y train_num_per_scene= 300, se repartirán 100 imágenes por bins de 0-1 a 0-3; 0-3 a 0.5; 0.5 a 0.7. 
#### Selección de variables
- *min_overlap*: 0.1
- *max_overlap*:0.7
- *train_num_per_scene*: null
- *num_overlap_bins*: null

De esta manera se seleccionan todas las imágenes de ente 0.1 y 0.7 de overlap sin techo de números de escena. Se tomó esta decisión porque al seleccionar num_overlap_bins = 3, descartaba mapas pequeños perdiendo pares de imágenes.

### Final dataset size

- Train: size=33492, batch_size=32, iter_per_epoch=1046.62
- Val: size=5368, batch_size=32,  iter_per_epoch=167.75
---
# Runs

*Naming:* gt_pos_X_Y
- gt_pos: using positives from map
- X: threshold for selecting new positive matches as ground truth
- Y: threshold for selecting hard negatives

Se corrieron 4 entrenamientos: 
1. gt_pos_3_10: Es un entrenamiento que ya había largado
2. gt_pos_3_10_no_ckpt: Con el fin de ver una comparación de full training vs fine-tuning.
3. gt_pos_null_10: Este es el training que queríamos ver, positivos del mapa y hard negatives con un threshold
4. gt_pos_null_5: Igual que 3 para ver la diferencia bajando el negative_threshold.


---
# Tensorboard links
*Password for all: 155.210.155.93*

- gt_pos_3_10:  https://gt-pos-3-10.loca.lt/
- gt_pos_3_10_no_ckpt: https://gt-pos-3-10-no-ckpt.loca.lt/
- gt_pos_null_10: https://gt-pos-null-10.loca.lt/
- gt_pos_null_5: https://gt-pos-null-5.loca.lt/


> [!NOTA]
> Por cada experimento se adjuntan screenshots con las curvas de train y val, junto con imágenes de muestra. 

> [!RECOMENDACIÓN]
> También se pueden visualizar y bajar todas las imágenes a través del tensorboard, de esta manera podemos ver la totalidad del entrenamiento.

---
# Metrics
- precision: TP / pred_matches
		“When the model says it found a match, how often is it right?”
- recall: TP / gt_pos 
		“How many true matches did we find?”
- accuracy: (TP + TN) / (gt_pos + gt_neg) 
		“How often the match decision is correct, including correct no_match”
- average_precision: AUC precision vs recall with sorted matching_scores
		“Do high-confidence matches tend to be correct?”

---
# Observaciones

- Epoch0 es la epoch inicial, epoch1 es la segunda epoch.
- Líneas Color verde = TP, color rojo = FP. 
- Solamente se plotean las líneas donde gt_matches0 es positivo o negativo (se quitan los ignorados).  
- Como en resultados anteriores se ve overfitting pero en este caso desde el comienzo. Las curvas de loss de train bajan y las de val divergen.
- Sin embargo, visualmente, tras comparar algunos casos se ve que hay una leve mejora en las epochs0 hasta 6. Aproximadamente a partir de la epoch6 las comparaciones con LightGlue Out-Of-the-Box empiezan arrojar peores resultados.
- Hay una tendencia a mejorar más fácilmente en los pares de imágenes con más overlap, ergo con más gt_pos.
- Al momento de evaluar gt_pos_3_10 vs gt_pos_3_10_no_ckpt (full training vs fine-tuning) me llama la atención que rápidamente en las primeras epochs, el entrenamiento es capaz de igualar performance en imágenes de bajo overlap. Para imágenes de mayor overlap no llega a los resultados de fine-tuning nunca. 

