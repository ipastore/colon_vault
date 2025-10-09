### Obligatory
- Run with batch_size = 16: because we are going to smooth the curve of the loss.
- Make another set with proper validation images (not the same as the training module) with nhom 100 and low threshold

### Optional
- Add
# TODO
- Try with and without data_augmentation
- Leon´s demo
- Try other learning rates. 0.00001 seems to be small, why not 0.0001? Maybe something between? Now I have raised from bs 1 to bs 16, so... maybe we could increase the lr


## Export

| TODO | Label | images | n_homo | thrsh    | nms |
| ---- | ----- | ------ | ------ | -------- | --- |
| Done | A     | 1100   | 10     | 0.000075 | 4   |
| Done | B     | 1100   | 100    | 0.015    | 4   |
| TODO | C     | 1100   | 100    | 0.000075 | 4   |
###  Dudas de export
- Which model should I use. MagicLeap  (Superpoint_pretrained), SuperpointNet or SuperpointNet_gauss2

- I want to filter the features that fall withing the camera mask (it´s a fixed binary mask). Where I should do it in experiment.py or Colon.py

- How is number of homograpgies modifying the amount and quality of features (is my repo using sum or aggregatiob) which is it more robust? 

- LEt´s say I want around 300-400 robust, precise and distributed matches in a 960x1344 image between suerpoint features, how should I set the confidence_threshold for the exporting the labels for a 240x320 image? How many features should I decide it´s oka yot set that threshold. Is there any difference on modifying this threshold, in the amoun of quality of supeorpoint detector and descriptor after training.

- The same question as before but with the nms threshold.
### Train


| TODO  | Date       | ID  | Label | Model              | bs  | lr          | Max.it  | Epochs | Memory | Time   |
| :---- | :--------- | :-- | :---- | ------------------ | --- | ----------- | ------- | ------ | ------ | ------ |
| Done  | 2025-09-16 | 4   | A     | MagicLeap          | 8   | 0.0001      | 50.000  | 363    | 5.1 GB | 4h 16m |
| Done  | 2025-09-16 | 1   | A     | MagicLeap          | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 11m |
| Done  | 2025-09-16 | 2   | B     | MagicLeap          | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 10m |
| Done  | 2025-09-16 | 3   | B     | MagicLeap(retrain) | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 55m |
| Doing | 2025-09-17 | 5   | A     | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 6   | **B** | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 8   | **C** | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 9   | ?     | Magicleap          | 16  | **0.00005** | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 10  | ?     | Magicleap          | 16  | **0.0001**  | 200.000 | 2900   | 8.2 GB |        |

# 19-05-25

HIce el experiment runs. Hay una conclusion por ahi. Creo que estaba haciendo los export con la máscara ya. 

Debería implementar el guardado de la máscara. DONE
Al final había hecho bien lo de la máscara. Deberá volver a correr los experiment de export.

Cuidado con la máscara porque el downsize hace que las zonas en negro esten. mas grandes, modificar si se usa la specular mask.

Esta testeado que con el yaml podes manejar si se hace la mascara o no (tanto la specular como la camera_mascara)