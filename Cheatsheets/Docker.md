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


