#### [[Nvidia Containers]]

#### Instalar en Linux sin GUI

```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
```
### Para correr Superpoint

#### Build for DGX
```bash
docker build -t colon_tf113_conda .
enroot import docker://nvcr.io/nvidia/tensorflow:19.03-py3
#Mover a DGX
scp -P 3003 colon_tf113_conda.sqsh ipastore@155.210.134.17:~/storage/docker_images/
```

#### Build for Loca

**Logearse a Nvidia para el container**

```bash
docker login nvcr.io
```

```bash
docker build -t colon_tf113_conda .
```

#### Comando para correr interactivamente la imagen con el mount

```bash
docker run -it --gpus all   -v $(pwd):/ColonSuperPoint   colon_tf113_conda bash
```

##### Dentro para correr el train con python

```bash
python superpoint/experiment.py train ColonExperiments/configs/superpoint_colonoscopy_finetune.yaml superpoint_finetuned_toy --pretrained_model saved_models/sp_v6
```
### Stop all containers and delete all images

```bash
docker ps && \   # List all containers
docker stop $(docker ps -aq) && \     # Stop all
docker rm $(docker ps -aq) && \       # Remove all
docker   $(docker images -q) && \  # Remove all images
```

### Clean all cache for builds

```bash
docker builder prune -a
```


# Erasing old docker image and building a new one for ColonSuperpoinTorch

## Rebuilding the Docker Image After Code Changes

To ensure your Docker image includes the recent changes to `net_parallel()` in model_wrap.py, you'll need to rebuild the image. Here's how to clean up and rebuild everything:

### Step 1: Stop any running containers

```bash
# Stop any containers using the image
docker ps -a | grep colonsp | awk '{print $1}' | xargs -r docker stop
```

### Step 2: Remove the existing image

```bash
# Remove the image
docker rmi colonsp:dev
```

### Step 3: Clean up Docker system (optional but recommended)

```bash
# Remove dangling images, containers, networks and build cache
docker system prune -f
```

### Step 4: Rebuild the image

```bash
# Rebuild for amd64 architecture (for Linux servers)
docker buildx build --platform=linux/amd64 --load -t colonsp:dev .
```

### Step 5: Verify the new image

```bash
# Check that the image was created
docker images | grep colonsp
```

After rebuilding, any new Docker containers will include your updated code. The improved `net_parallel()` method will now properly handle CPU-only environments and prevent the hanging issue you experienced.

# Test ColonSuperpoinTorch

**For export:** 
```bash
docker run --user $(id -u):$(id -g) --gpus all  --rm --shm-size=1g   -v $(pwd)/datasets:/workspace/ColonSuperpoinTorch/datasets   -v $(pwd)/runs:/workspace/ColonSuperpoinTorch/runs   -v $(pwd)/logs:/workspace/ColonSuperpoinTorch/logs   -v $(pwd)/configs:/workspace/ColonSuperpoinTorch/configs   -v $(pwd)/models:/workspace/ColonSuperpoinTorch/models   colonsp:dev   python export.py export_detector_homoAdapt configs/superpoint_colon_export_test.yaml test --outputImg > log.out 2>&1
```
 *For rene (export)*
 
```bash
docker run --user $(id -u):$(id -g) --gpus all --rm --shm-size=1g \
  -v $(pwd)/datasets:/workspace/ColonSuperpoinTorch/datasets \
  -v $(pwd)/runs:/workspace/ColonSuperpoinTorch/runs \
  -v $(pwd)/logs:/workspace/ColonSuperpoinTorch/logs \
  -v $(pwd)/configs:/workspace/ColonSuperpoinTorch/configs \
  -v $(pwd)/models:/workspace/ColonSuperpoinTorch/models \
  colonsp:dev \
  python train4.py train_joint configs/superpoint_colon_train_heatmap_test.yaml test_train 2>&1 | tee -a log.out
 
```
O 
```bash
docker run --user $(id -u):$(id -g) --gpus all --rm --shm-size=1g \
-v $(pwd):/workspace/ColonSuperpoinTorch \
  colonsp \
  python export.py export_detector_homoAdapt configs/superpoint_colon_export_test.yaml test --outputImg 2>&1 | tee -a log.out
```
**For train:** 

```bash
docker run --user $(id -u):$(id -g) --gpus all  --rm --sh
m-size=1g   -v $(pwd)/datasets:/workspace/ColonSuperpoinTorch/datasets   -v $(pwd)/runs:/workspace/ColonSuperpoinTorch/runs  
 -v $(pwd)/logs:/workspace/ColonSuperpoinTorch/logs   -v $(pwd)/configs:/workspace/ColonSuperpoinTorch/configs   -v $(pwd)/mo
dels:/workspace/ColonSuperpoinTorch/models   colonsp:dev   python train4.py train_joint configs/superpoint_colon_train_heatmap_test.yaml test_train  > log.out 2>&1
```

*For rene (train)*

```bash
docker run --user $(id -u):$(id -g) --gpus all  --rm --shm-size=1g \
-v $(pwd):/workspace/ColonSuperpoinTorch \
   colonsp \
   python train4.py train_joint configs/superpoint_colon_train_heatmap_test.yaml test_train  2>&1 | tee -a log.out
```

# Run train in rene

```bash
docker run --user $(id -u):$(id -g) --gpus all  --rm --shm-size=2g \
-v $(pwd):/workspace/ColonSuperpoinTorch \
   colonsp \
   python train4.py train_joint configs/superpoint_colon_train_heatmap.yaml 20_F_RTX3090_bs32 --eval  2>&1 | tee -a log.out
```

# For the docker image of gluefactory

**Para evaluar hpatches**
```bash
docker run --user $(id -u):$(id -g) --gpus all -it --shm-size=8g   -v /home/student/glue-factory-colon:/workspace   -v /media/student/HDD/nacho/glue-factory/data:/me
dia/student/HDD/nacho/glue-factory/data   gluefactory:cuda11.4   bash -c "cd /workspace && conda run -n gluefactory python -m gluefactory.eval.hpatches --conf superpoint+lightglue-official --overwrite"
```

**Para entrenar en megadepth**

```bash
docker run --user $(id -u):$(id -g) --gpus all -it --shm-size=8g   -v /home/student/glue-factory-colon:/workspace   -v /media/student/HDD/nacho/glue-factory/data:/media/student/HDD/nacho/glue-factory/data   gluefactory:cuda11.4   bash -c "cd /workspace && conda run -n gluefactory python -m gluefactory.train sp+lg_megadepth --conf gluefactory/configs/superpoint+lightglue_megadepth.yaml"
```


# Delete all images except endocarto-gui
```bash
docker images -q | xargs -r docker inspect --format '{{.RepoTags}} {{.Id}}' | \
grep -v 'endocarto-gui' | awk '{print $NF}' | xargs -r docker rmi
```


# Sift-lg_megadepth train with output in log.out

```bash
docker run --user $(id -u):$(id -g) --gpus all -it --shm-size=8g \
  -v /home/student/glue-factory-colon:/workspace \
  -v /media/student/HDD/nacho/glue-factory/data:/workspace/data \
  gluefactory:cuda11.4 \
  bash -c "cd /workspace && conda run -n gluefactory python -m gluefactory.train sift+lg_homography --conf gluefactory/configs/sift+lightglue_endomapper_homography.yaml 2>&1 | tee log.out"
```

# Build docker image with user without root
```bash
docker build --build-arg USER_UID=$(id -u) --build-arg USERNAME=dev -t gluefactory:cuda11.4 . 2>&1 | tee log_docker_build.out
```
