
# ssh
```bash
vim ~/.ssh/config # add remote
cat ~/.ssh/id_ed25519.pub | ssh -p 22 REPLACE_WITH_ALIAS@REPLACE_WITHIP \
  'umask 077; mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys' # add key to remote
```

# gluefactory official repo

```bash
git clone https://github.com/cvg/glue-factory.git
```

**Pasar**
- Dockerfile
- .dockerignore
- 0001-Fix-sift-training.patch
- scripts (folder)

# Check arch

```bash
nvidia-smi                          # driver version + GPU name
nvidia-smi --query-gpu=name,compute_cap --format=csv   # compute capability
nvcc --version                      # CUDA toolkit version
```
[https://developer.nvidia.com/cuda/gpus](https://developer.nvidia.com/cuda/gpus)


# Docker

## Init

**Check for docker group**
```bash
groups
```

If not included

```bash
sudo usermod -aG docker {user}
```

Exit and reconnect. If persistent ssh

```bash
ssh -O exit -o ControlPath=~/.ssh/cm-{user}@{ip}:22 {user/name}
```
or killall

```bash
rm ~/.ssh/cm-*
```

## Check CUDA_IMAGE and CUDA_LIBRARIES

```bash
nvidia-smi --query-gpu=name,compute_cap --format=csv   
nvcc --version 
```


**For 5090**
Check CUDA compute capability:  https://developer.nvidia.com/cuda/gpus
	CUDASIFT_CUDA_ARCHS=120 
FROM:https://developer.nvidia.com/cuda-toolkit-archive
https://docs.nvidia.com/cuda/archive/12.8.0/cuda-features-archive/index.html
	CUDATOOLKIT_VERSION=12.8 
FROM: https://hub.docker.com/r/nvidia/cuda 
	CUDA_IMAGE(choose the cuddn-devel)

FROM: https://pytorch.org/get-started/previous-versions/ 
Check latest pytorch version with CUDA support and select
	PYYTORCH_VERSION=2.7.0 
	TORCHVISION_VERSION=0.22.0

or follow

| **Compute Capability** | **Architecture**         | **First CUDA Version** | **Common GPUs**                  |
| ---------------------- | ------------------------ | ---------------------- | -------------------------------- |
| **sm_100 / sm_120**    | **Blackwell**            | **CUDA 12.8**          | RTX 5090, B200                   |
| **sm_90**              | **Hopper**               | **CUDA 11.8**          | H100, H200                       |
| **sm_89**              | **Ada Lovelace**         | **CUDA 11.8**          | RTX 40-series, L40               |
| **sm_87**              | **Ampere (Jetson)**      | **CUDA 11.4**          | Jetson AGX Orin                  |
| **sm_86**              | **Ampere (Consumer)**    | **CUDA 11.1**          | RTX 30-series (except 3090/A100) |
| **sm_80**              | **Ampere (Data Center)** | **CUDA 11.0**          | A100, RTX 3090                   |
| **sm_75**              | **Turing**               | **CUDA 10.0**          | RTX 20-series, GTX 1660          |
| **sm_70 / sm_72**      | **Volta**                | **CUDA 9.0**           | Titan V, V100, Xavier            |
| **sm_60 / sm_61**      | **Pascal**               | **CUDA 8.0**           | GTX 10-series, P100              |
| **sm_50 / sm_52**      | **Maxwell**              | **CUDA 6.0**           | GTX 900-series, Titan X          |
| **sm_35 / sm_37**      | **Kepler (Update)**      | **CUDA 5.0**           | GTX 780, K40, K80                |
| **sm_30**              | **Kepler**               | **CUDA 4.2**           | GTX 600-series                   |


| CTK Version | Driver Range for Minor Version Compatibility |       |
| ----------- | -------------------------------------------- | ----- |
|             | Min                                          | Max   |
| 13.x        | >= 580                                       | N/A   |
| 12.x        | >= 525                                       | < 580 |
| 11.x        | >= 450                                       | < 525 |
## Build 

> [!NOTE] If installing glue-factory official repo (not fork) apply patch to fix sift training: git apply {patch}
> 

Inside glue-factory
```bash
chmod +x ./scripts/build_docker.sh
```

Change build ARGS for building the image in [[build.docerk.sh]]
## Run

[[train.sh]]

## Troubleshooting

## libpng error: IDAT: CRC error
There was an error movign the files with cp, mv or rsync. 

### For Homography dataset
Use the tool under tools/check_corrupted_images.py

## Config details
If training in homographies: ADD **scale_ori: true**
If pytorch>2.0; **flash: true**
to avoid OOMs: **val_batch_size**: 1

## Export details
Add --export_sparse_depth for exporting megadepth to pad properly the vector of invalid depth previously

Then

Add keys in config
data_keys: \["keypoints", "keypoint_scores", "descriptors", "oris", "scales", "depth_keypoints", "valid_depth_keypoints"]