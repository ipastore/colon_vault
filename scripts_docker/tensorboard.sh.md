
Si tensorflow:latest da error usar tensorflow:2.13.0
```bash
#!/usr/bin/env bash
set -euo pipefail
  

TRAIN_PATH="${1:-/home/ecs/glue-factory-colon/outputs/training}"
PORT="${2:-8008}"

echo "TRAIN_PATH: ${TRAIN_PATH}"
echo "PORT: ${PORT}"

docker rm -f tensorboard 2>/dev/null || true

docker run \
-d \
-p "${PORT}":"${PORT}" \
-v "${TRAIN_PATH}:/workspace/outputs/training" \
--name tensorboard \
tensorflow/tensorflow:2.13.0 \
tensorboard \
--logdir=/workspace/outputs/training/ \
--host=0.0.0.0 \
--port="${PORT}"
```
