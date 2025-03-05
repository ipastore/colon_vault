---

kanban-plugin: board

---

## TODO_filtering

- [ ] #mask_filtering Unit tests
- [ ] #mask_filtering Integration tests (try resize, logger, etc)
- [ ] #mask_filtering #FIX Resize preserving the image ratio
- [ ] #mask_filtering Adapt sift-lg and gim-lg to use the dict unction to filter
- [ ] #mask_filtering #FIX Filter further feature maps in RoMa (right now is filtered just the first forward pass of the architecture).
- [ ] #mask_filtering Improve documentation to download project and git architecture.
- [ ] #mask_filtering #BUG [[killed_bug]]


## TODO_error_measurement

- [ ] #error_measurement Error measurement for all selected keframes. ^qif38a
- [ ] #error_measurement tune hyperparameters for other (than sp-lg) models and and compare within one submap


## Doing

- [ ] #error_measurement #matcher Choose sp-lg. Ablation study


## Done

- [ ] #COLMAP Extract Camera poses for selected keyframes
- [ ] #error_measurement Select one pair of image and measure the error in the camear pose.
- [ ] #error_measurement #matcher Preprocessing of feats to insert in COLMAP
- [ ] #COLMAP Choose pair of keyframes
- [ ] #error_measurement #matcher Keypoint plotting
- [ ] #COLMAP Download COLMAP in UBUNTU
- [ ] #error_measurement get the E matrix. Then get R and t




%% kanban:settings
```
{"kanban-plugin":"board","list-collapse":[false]}
```
%%