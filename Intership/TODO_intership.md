---

kanban-plugin: board

---

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
{"kanban-plugin":"board","list-collapse":[]}
```
%%