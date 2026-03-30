---

kanban-plugin: board

---

## TODO

- [ ] Hacer una demo de la evolución de los heatmaps según los checkpoints
- [ ] [[entrenamiento_sift_lg]]


## Doing

- [ ] Empezar a tocar el SLAM y ver que tengo que hacer
	[[Superpoint_SLAM]]


## Done

- [ ] Insertar un Scheduler
- [ ] Lanzar entrenamiento: 
	- RTX 3090 bs32
	- DGX bs96
	- Investigar max bs con 2GPUs en DGX
- [ ] Probar con ADamW, basandonos en un modelo que ya funciona
- [ ] papeleo: entregar propuesta TFM
- [ ] Estudiar el paper de Leon y su repo para entender la Loss y como es el entrenamiento en su repo
- [ ] Hacer un export y entrenamiento con todos los datos
- [ ] [[explain_theory]]
- [ ] papeleo TFM
- [ ] COn el export D hacer un export de th bajo y entrenar con th bajo
- [ ] Averiguar si el heatmap está normalizado
- [ ] Hacer informes de tracking avanzados (estan en un .md dentro de la demo)
- [ ] Hacer un export D con más imagenes de training, sepearado del video de val y el video de test
- [ ] [[make_a_proper_demo_superpoint]]
- [ ] [[cropear_videos_para_dataset]]
- [ ] condicionales en mask export vs training
- [ ] [[apply_camera_mask_before_exporting]]
- [ ] Desambiguar camera-mask para hacer mas fina la mascara especular
- [ ] [[experiment_runs]]


## TODO_error_measurement

- [ ] Finish the pulling from upstream IMM to fix sphereglue
- [ ] Fix submodules of image-matching_models: lightglue, roma and gim
- [ ] [[pedido_metricas_monti]]
- [ ] [[tidy_up_repo]]
- [ ] #error_measurement modificar parametros de RANSAC de PYCOLMAP
- [ ] #error_measurement Check the cherality check within the PYCOLMAP function. See if getting any other of the 4 possibilites improve any error.


## TODO_filtering

- [ ] #mask_filtering #FIX Resize preserving the image ratio
- [ ] #mask_filtering Adapt sift-lg and gim-lg to use the dict unction to filter
- [ ] #mask_filtering #FIX Filter further feature maps in RoMa (right now is filtered just the first forward pass of the architecture).
- [ ] #mask_filtering Improve documentation to download project and git architecture.


***

## Archive

- [ ] #error_measurement Create a new script to build a report for a whole sequence? Why?
- [ ] Making the mask bigger to filter any keypoint that is near a specularity
- [ ] #error_measurement I need a way to change easily the thresholds and see the difference. Now I am doing it inside the whole loop.

%% kanban:settings
```
{"kanban-plugin":"board","list-collapse":[null,false,false]}
```
%%