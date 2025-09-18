### Obligatory
- Run with batch_size = 16: because we are going to smooth the curve of the loss.
- Make another set with proper validation images (not the same as the training module) with nhom 100 and low threshold

### Optional
- Add
# TODO
- Try with and without data_augmentation
- Leon´s demo
- Try other learning rates. 0.00001 seems to be small, why not 0.0001? Maybe something between? Now I have raised from bs 1 to bs 16, so... maybe we could increase the lr


### Export

| TODO | Label | images | n_homo | thrsh    | nms |
| ---- | ----- | ------ | ------ | -------- | --- |
| Done | A     | 1100   | 10     | 0.000075 | 4   |
| Done | B     | 1100   | 100    | 0.015    | 4   |
| TODO | C     | 1100   | 100    | 0.000075 | 4   |

### Train


| TODO  | Date       | ID  | Label | Model              | bs  | lr          | Max.it  | Epochs | Memory | Time   |
| :---- | :--------- | :-- | :---- | ------------------ | --- | ----------- | ------- | ------ | ------ | ------ |
| Done  | 2025-09-16 | 4   | A     | MagicLeap          | 8   | 0.0001      | 50.000  | 363    | 5.1 GB | 4h 16m |
| Done  | 2025-09-16 | 1   | A     | MagicLeap          | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 11m |
| Done  | 2025-09-16 | 2   | B     | MagicLeap          | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 10m |
| Done  | 2025-09-16 | 3   | B     | MagicLeap(retrain) | 1   | 0.00001     | 200.000 | 181    | 2.1 GB | 4h 55m |
| Doing | 2025-09-17 | 5   | A     | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 6   | **B** | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 7   | **A** | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 8   | **C** | Magicleap          | 16  | 0.00001     | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 9   | ?     | Magicleap          | 16  | **0.00005** | 200.000 | 2900   | 8.2 GB |        |
| TODO  |            | 10  | ?     | Magicleap          | 16  | **0.0001**  | 200.000 | 2900   | 8.2 GB |        |

