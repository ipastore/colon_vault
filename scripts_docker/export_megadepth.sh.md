```bash
#!/usr/bin/env bash
set -euo pipefail
  
METHOD="pycolmap-sift-gpu"

MODULE="gluefactory.train"

# TAG="py_colmap+lg_HM_baseline"
TAG="py_colmap+lg_HM_baseline_scale_ori_flash_fp16"

CONFIG_FILE="gluefactory/configs/sift+lightglue_homography.yaml"
# CONFIG_FILE="gluefactory/configs/sift+lightglue_megadepth.yaml"

DATA_PATH="/home/ecs/glue-factory/data/"
OUTPUT_PATH="/home/ecs/glue-factory-colon/outputs"
CONFIGS_PATH="/home/serecsver/glue-factory-colon/gluefactory/configs"
DOCKER_IMAGE="official-gluefactory:cuda11.8"
 
docker run -d --rm --gpus all \
--shm-size=50g \
-v <DATA_PATH>:/workspace/data \
v <OUTPUT_PATH>:/workspace/outputs \
-v <CONFIGS_PATH>:/workspace/gluefactory/configs \
gluefactory-official:cuda11.8 \
/bin/bash -lc "conda run --no-capture-output -n gluefactory python -m gluefactory.scripts.export_megadepth --method ${METHOD} --num_workers 8 2>&1 | tee /workspace/outputs/docker_stdout_stderr.log"
```
