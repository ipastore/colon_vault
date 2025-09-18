


## Superglue
*Video*
https://www.youtube.com/watch?v=u7Yo5EtOATQ
https://www.youtube.com/watch?v=95Eysm0IeB0
## Leon´s
*Repo*
Superpoint Endoscopy (updating losses). Poor implementation i guess
https://github.com/LeonBP/SuperPointEndoscopy

*Video*
https://www.youtube.com/watch?v=yFdw3Sfnchw

## Eric-yyjau
https://eric-yyjau.medium.com/what-have-i-learned-from-the-implementation-of-deep-learning-paper-365ee3253a89
## Endoslam
https://github.com/CapsuleEndoscope/EndoSLAM
## C3VD
https://pmc.ncbi.nlm.nih.gov/articles/PMC10591895/#:~:text=,of

# HPatches
https://github.com/hpatches/hpatches-benchmark

# Endovis
https://opencas.dkfz.de/endovis/category/challenges/

# Github List
https://github.com/ericzzj1989/Awesome-Image-Matching

## FM-Bench
https://github.com/JiawangBian/FM-Bench/tree/master

## image-matching-benchmark
https://github.com/ubc-vision/image-matching-benchmark/tree/master
https://github.com/ubc-vision/image-matching-benchmark-baselines

## NICE PDF of slides about metrics
![[Image_Matching_in_2025_no_gif.pdf]]

## Course of CV
https://cw.fel.cvut.cz/b212/courses/mpv/start
### tambien otras slides y videos del profe
https://cmp.felk.cvut.cz/~mishkdmy/
https://www.youtube.com/watch?v=TWWonIHQQXA
# Annotations from SOTA 

## Proxy-preSLAM
**Matching Evaluation in Medical Papers:** The metrics and protocols used in endoscopic matching papers mirror general ones, with a few twists. For example, the superpixel method evaluated **matching precision, recall, and F1** by comparing matched feature pairs to ground truth correspondences from a stereo setup[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=Feature%20matching%20is%20an%20important,the%20vector%20feature%20to%20optimize)[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=matching%20performance,of%20matching%20methods%20will%20discard). They counted a match as correct if the distance between the matched points was below a threshold in the rectified stereo image (essentially an epipolar constraint). Precision = #correct matches / #matches, Recall = #correct / #total true correspondences (they had a known disparity field), and F1 the harmonic mean[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=Feature%20matching%20is%20an%20important,the%20vector%20feature%20to%20optimize). They also defined a **“matching score” = correct matches / total detected features**[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=matching%20performance,of%20matching%20methods%20will%20discard)[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=match%20at%20L820%20matching%20scores,matching%20results%20in%20most%20datasets) – this is slightly unusual (it’s like precision but normalized by detections rather than matches; effectively it penalizes detectors that detect many points that don’t end up matched). They found their method improved this score in all tested endoscope modalities (colon, gastro, etc.)[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=match%20at%20L820%20matching%20scores,matching%20results%20in%20most%20datasets)

## VO/SLAM
In VO works (e.g., EndoSLAM paper by Gadre et al.), standard **trajectory RMSE (ATE)** and **RPE** were used, comparing to the external tracker ground truth[sciencedirect.com](https://www.sciencedirect.com/science/article/abs/pii/S1361841521001043#:~:text=In%20total%2C%2035%20sub,). Additionally, because the colon is deformable, some works evaluate **local consistency**: e.g., reconstruct a small section and compare to a CT-derived model or to an EM sensor reading at a point. Another medical-specific evaluation is **tracking duration** – e.g., **percent of frames where tracking was successfully maintained**. In a live endoscopy, losing track for >1 s might be considered a failure. EndoSLAM’s visual odometry was evaluated on how long it could continuously track before needing a reset, on those pig sequences.

# Tensorflow vs Pytorch implementation
Got it — proceeding with your updated instruction and keeping your browser control in mind.

## **SuperPoint Training Repos – TensorFlow vs. PyTorch:** 
The original SuperPoint code by MagicLeap (Paul-Edouard Sarlin and Daniel DeTone) was TensorFlow and was not fully open-sourced (weights only). However, Renaud Pautrat (rpautrat) released a TensorFlow implementation (as part of his bachelor thesis code) in 2018. That code faithfully reproduces the paper’s training pipeline and was used in later research. It outputs intermediate training metrics and supports MagicPoint pre-training. It’s under Apache 2.0 and was referenced by many. Downsides: It relies on TF1, which is now outdated; setting it up (with CUDA etc.) is non-trivial in 2025. Also, it’s not actively maintained (no major updates in last 3 years). On the other hand, the **PyTorch reimplementation by Eric** (eric-yyjau/pytorch-superpoint) has become popular. It supports training on COCO or KITTI out of the box[github.com](https://github.com/eric-yyjau/pytorch-superpoint#:~:text=GitHub%20github,Tensorboard%3A%20log%20files), logs to TensorBoard, and is easier to extend (we can add our custom dataset). It outputs models that are easy to integrate with other PyTorch code (like LightGlue). It’s MIT licensed and fairly well documented. Community feedback indicates it reaches similar repeatability as original (within a few points). Moreover, PyTorch allows exporting the model to ONNX or TorchScript for deployment – something we might need for embedding into a C++ SLAM. TensorFlow models can be converted too, but again the ecosystem has shifted to PyTorch for research.

One consideration: Does the PyTorch version achieve the same performance as the TF one? Users have reported that eric-yyjau’s model achieves ~0.65 Homography Accuracy AUC on HPatches (similar to paper) after proper training. We thus trust it. Additionally, since we might fine-tune on a small dataset, PyTorch’s dynamic updates and easier debugging are pluses. The PyTorch repo is active (last commit mid-2023, with bug fixes). For example, it added support for multi-GPU and corrected a few loss function details. In contrast, rpautrat’s TF code has open issues that likely won’t be resolved (like compatibility with TF2).

**Logging & Evaluation features:** The PyTorch superpoint includes TensorBoard logging of loss and example outputs (warped pair correspondence visualization). It also has evaluation scripts for HPatches. This is convenient to monitor our training progress and ensure we don’t overfit. The TF code also had some evaluation but might require more manual work to adapt to endo.

**Export and Efficiency:** Post-training, we’ll want to use the model in C++ (for real-time pipeline). PyTorch models can be exported to ONNX and run via TensorRT, achieving maybe >300 fps for 640×480 images on an NVIDIA Xavier (some have done this for SuperPoint). There are projects like **torch2trt** that simplify that. The TensorFlow model could in principle be converted to a TF-Lite or TensorRT as well, but given TF1 it’s more cumbersome. So PyTorch route simplifies deployment.

**Community & Maintenance:** The PyTorch version’s author and others in community have provided Q&A, and forks exist that added features (like superpoint with different backbone, etc.). The TF version is static. Considering our timeline and need to possibly tweak things, PyTorch is clearly better.

Therefore, we will **use the PyTorch SuperPoint implementation** for training and inference. We will verify that it achieves the performance reported by rpautrat’s (we can run a quick test on HPatches or on a small synthetic test).

## BIS: Due-Diligence Report: Using **eric-yyjau/pytorch-superpoint** vs **rpautrat/SuperPoint**

### Executive summary (one page)

- **Bottom line:** If your pipeline is **PyTorch-first** (LightGlue, glue-factory, HLOC, etc.), **eric-yyjau/pytorch-superpoint** is a pragmatic choice because it provides a **full SuperPoint training pipeline in PyTorch** (MagicPoint pretrain, HA exports, SuperPoint train, HPatches eval) with ready configs for **COCO/KITTI** and TensorBoard logging. The repo is **MIT-licensed** and has active community usage (ongoing issues and forks) even if commit volume is modest. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **Trade-off vs rpautrat/SuperPoint:** Pautrat’s repo is **more popular and thoroughly documented**, but **training is in TensorFlow**; a **PyTorch file exists for inference/conversion** (“superpoint_pytorch.py”) rather than a full PyTorch training stack. If you want end-to-end PyTorch training without maintaining TF, Eric’s repo remains the cleaner starting point. ([GitHub](https://github.com/rpautrat/SuperPoint "GitHub - rpautrat/SuperPoint: Efficient neural feature detector and descriptor"))
    
- **LightGlue (and glue-factory) compatibility:**
    
    - **Inference:** LightGlue is trained with SuperPoint and is compatible with any features; using Eric’s SuperPoint at inference is straightforward. ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"), [GitHub](https://github.com/cvg/LightGlue?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed (ICCV ..."))
        
    - **Fine-tuning LightGlue:** glue-factory includes **its own (open) SuperPoint** and **“official” configs**; **you can train LightGlue on _your own_ local features** (i.e., precomputed keypoints+descriptors from Eric’s model). You **do not** need to “plug Eric’s weights” into glue-factory’s SuperPoint to fine-tune LightGlue. If you _do_ want to use glue-factory’s SuperPoint module, you’ll likely need a **checkpoint key-mapping/conversion** because state_dict names differ. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
        
- **Endorsement evidence for Eric’s repo:**
    
    - **Activity & adoption:** 890★/190 forks; open issues through **2024–2025**; used in external projects and cited in academic work. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"), [dl.acm.org](https://dl.acm.org/doi/10.1007/978-3-031-43907-0_56?utm_source=chatgpt.com "Tracking Adaptation to Improve SuperPoint for 3D ..."))
        
    - **Features:** Full PyTorch training, COCO/KITTI support, HA export, HPatches evaluation, pretrained checkpoints, explicit results table. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
        
- **Risks:** Requirements are dated (e.g., Python 3.6, torch ≥1.1 stated), some “known problems” and TODOs in README; commit frequency is low. These are mitigable (pin modern env, patch scripts), and the codebase is small/understandable. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    

---

### Evidence pack & comparison

#### Repository health & community signals

- **eric-yyjau/pytorch-superpoint**
    
    - **License:** MIT. **Stars/Forks:** 890 / 190 (as displayed). **Issues/PRs:** 55 / 4. **Recent issues in 2024–2025** (e.g., #120 “few features”, #118 CUDA arch issue); indicates users still train & debug with recent CUDA/GPUs. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
        
    - **Training pipeline & docs:** End-to-end PyTorch: Synthetic Shapes → HA export on COCO/KITTI → SuperPoint training → HPatches evaluation, with **train_tutorial.md** and **evaluation.py**; explicitly reports HPatches metrics and includes **pretrained COCO/KITTI** models. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
        
    - **External endorsements/usage:**
        
        - Referenced by academic work (e.g., **Tracking Adaptation to Improve SuperPoint**, MICCAI/LNCS 2023) and third-party repos. ([dl.acm.org](https://dl.acm.org/doi/10.1007/978-3-031-43907-0_56?utm_source=chatgpt.com "Tracking Adaptation to Improve SuperPoint for 3D ..."), [GitHub](https://github.com/PH8411/image-matching?utm_source=chatgpt.com "PH8411/image-matching: Superpoint and Superglue ..."))
            
        - Discussed/linked in forums (TI Edge AI), clones/mirrors, and derivative PyTorch ports. ([e2e.ti.com](https://e2e.ti.com/support/processors-group/processors/f/processors-forum/1132630/tda4vm-training-model-on-edgeai-torchvision?utm_source=chatgpt.com "TDA4VM: training model on edgeai-torchvision - TI E2E"), [gitee.com](https://gitee.com/hanyunlong12/pytorch-superpoint?skip_mobile=true&utm_source=chatgpt.com "韩云龙12/pytorch-superpoint"), [GitHub](https://github.com/shaofengzeng/SuperPoint-Pytorch?utm_source=chatgpt.com "Pytorch Implementation of rpautrat/SuperPoint"))
            
- **rpautrat/SuperPoint**
    
    - **License:** MIT. **Stars/Forks:** 2.2k / 453. **Commits:** 283. **Issues/PRs:** 44 / 2. Strong adoption, well-maintained historically. ([GitHub](https://github.com/rpautrat/SuperPoint "GitHub - rpautrat/SuperPoint: Efficient neural feature detector and descriptor"))
        
    - **Framework:** **TensorFlow training** with a **PyTorch conversion/inference file** (`superpoint_pytorch.py`) and a notebook (`convert_to_pytorch.ipynb`). If you need _training in PyTorch_, you still need to port TF training or switch repos. ([GitHub](https://github.com/rpautrat/SuperPoint "GitHub - rpautrat/SuperPoint: Efficient neural feature detector and descriptor"))
        

**Takeaway:** If you need **training** in PyTorch, Eric’s repo avoids TensorFlow. If you only need **inference** or are fine with **TF training**, Pautrat’s repo is a solid baseline with wider community visibility.

---

#### Features relevant to your pipeline (PyTorch + LightGlue + glue-factory)

- **LightGlue** (ICCV’23): trained with **SuperPoint** (and SIFT) and **compatible with any features**; fine-tuning often uses MegaDepth pairs. For endoscopy, you can fine-tune LightGlue on your colon data **provided you supply matched keypoints+descriptors** (no need to swap SuperPoint code inside glue-factory). ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"))
    
- **glue-factory** (ETH-CVG training library):
    
    - Ships an **Apache-2.0 “open” SuperPoint** implementation and “official” configs; supports **training LightGlue with _your own_ local features**. This means you can **export SuperPoint features with Eric’s code** and feed them to glue-factory for LightGlue fine-tuning/eval. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
        
    - Because glue-factory’s SuperPoint modules and checkpoints follow **their** config & naming, **you generally can’t just drop Eric’s `.pth` into glue-factory** without a small **key-mapping/conversion**. (This is a tooling detail, not a conceptual blocker.) ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
        

**Answer to your concrete concern:** You’re right: **you can’t “just plug” Eric’s weights into glue-factory’s SuperPoint**. But you **can** fine-tune LightGlue **using features extracted by Eric’s model** (export to disk → glue-factory “use your own features”). That requires **no weight import**, just consistent feature format. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))

---

### What “robust enough” means here

- **Maturity:** Eric’s repo has been used since 2019 by researchers and practitioners; README lists HPatches results and provides pretrained models. Community issues show **active use on modern GPUs (2024–2025)**. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **Scope:** It covers **the full SuperPoint recipe** (MagicPoint, HA, SuperPoint, evaluation) in **PyTorch only**, which aligns with your **all-PyTorch** SLAM stack (LightGlue, RoMa, etc.). ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **License:** MIT (green-light for academic/industrial). ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **Known limitations** (transparent): dated environment notes (Python 3.6, Torch ≥1.1 tested), “known problems” around COCO export resolution (240×320 vs 480×640), and a modest number of commits. These are tractable; you can modernize env and tweak configs. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    

---

### Compatibility details (LightGlue & glue-factory)

- **Inference path (simple):**
    
    1. Use Eric’s SuperPoint to detect+describe (256-D descriptors).
        
    2. Feed keypoints/descriptors to **LightGlue** with the **“superpoint”** feature setting. (LightGlue explicitly supports SuperPoint features.) ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"))
        
- **Fine-tuning LightGlue (without touching SuperPoint weights):**
    
    - With **glue-factory**, use the “train with your own local features” capability and point it at **exported SuperPoint features** computed by Eric’s model on your colonoscopy pairs/sequences. No weight port needed. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
        
- **If you insist on using glue-factory’s SuperPoint module with Eric’s weights:**
    
    - Write a **checkpoint converter** to remap `state_dict` keys (e.g., encoder/decoder blocks, det/desc heads, NMS params), verify descriptor normalization, and match glue-factory config (open vs official). This is normal housekeeping when mixing repos. The README highlights separate **“open SuperPoint”** vs **“official”** configs, underscoring why key-mapping may be required. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
        

---

### Head-to-head snapshot (keywords only)

|Repo|Framework|Training|Inference|License|Stars/Forks|Notes|
|---|---|---|---|---|---|---|
|**eric-yyjau/pytorch-superpoint**|**PyTorch**|**Yes** (MagicPoint→HA→SuperPoint)|Yes|**MIT**|**890 / 190**|COCO/KITTI, HPatches eval, active issues 2024–2025. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))|
|**rpautrat/SuperPoint**|**TensorFlow** (+PyTorch **conversion/inference**)|**TF**|**PyTorch inference file**|**MIT**|**2.2k / 453**|Broad adoption; TF training scripts; PyTorch file `superpoint_pytorch.py`. ([GitHub](https://github.com/rpautrat/SuperPoint "GitHub - rpautrat/SuperPoint: Efficient neural feature detector and descriptor"))|
|**glue-factory**|PyTorch|**Yes** (LG, GlueStick)|Yes|Apache-2.0|954 / 124|Has **open SuperPoint**, supports **your own features**; separate configs. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))|

_(Numbers shown as displayed on the page when accessed; GitHub may fluctuate.)_

---

### Risks & mitigations

- **Aging environment in Eric’s README** (PyTorch 1.3 / Python 3.6): Pin a modern environment (e.g., PyTorch 2.3+, Python 3.10/3.11), fix minor API calls, and vendor any small utils. The codebase is small; these bumps are routine. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **COCO export resolution note (240×320)**: If you want HQ colonoscopy, change export configs and retrain; HA operates at your chosen resolution. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **Weight-format mismatch with glue-factory modules**: Avoid by training LG with **exported features**; or write a short weight converter if you must reuse their SuperPoint class. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    

---

### Why this decision is defensible for `colon_matching`

1. **All-PyTorch workflow:** SLAM front-end, LightGlue, RoMa/LoFTR variants, and glue-factory trainers are PyTorch-centric. Eric’s repo keeps training/inference under one framework. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    
2. **Feature parity for your needs:** It implements MagicPoint→SuperPoint with HA and reports standard HPatches metrics. That’s exactly what you need to train on domain data and export features for downstream matchers/VO. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
3. **Community signal:** Ongoing usage (fresh issues), academic citation/usage, and forks across years support robustness. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint/issues "GitHub · Where software is built"), [dl.acm.org](https://dl.acm.org/doi/10.1007/978-3-031-43907-0_56?utm_source=chatgpt.com "Tracking Adaptation to Improve SuperPoint for 3D ..."))
    
4. **Licensing clarity:** MIT (repo) + Apache-2.0 (glue-factory) are permissive for research and potential tech transfer. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    

---

### Action plan for you (concrete and safe)

**A. Keep Eric’s repo as the SuperPoint trainer/extractor**

- Modernize env (PyTorch ≥2.1, Python ≥3.10), run the pipeline on a small colonoscopy subset, and validate HPatches eval still runs. Use the repo’s **export.py** to dump **keypoints+descriptors** per frame/pair, respecting your **specularity masks**. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    

**B. Train or fine-tune LightGlue using glue-factory on your exported features**

- Use glue-factory’s **“train with your own local features”** capability; start with the **homography pretrain** configs, then fine-tune on your colon data. No SuperPoint weight import is required. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    

**C. (Optional) If you want to unify under glue-factory’s SuperPoint module**

- Write a light **state_dict key converter** to map Eric’s naming to glue-factory’s “open SuperPoint” or “official” configs; test descriptor normalization (L2) and NMS radius/thresholds for parity. The need for a converter is expected given glue-factory’s own SuperPoint variants. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    

---

### Clarifications, myths, and what’s _not_ true

- **“We need 3D GT to fine-tune LightGlue”** – Not strictly. LightGlue pretrains on **synthetic homographies** and can be fine-tuned on datasets like **MegaDepth**. For domain adaptation, you can train using image pairs with geometric supervision proxies (homographies/pseudo-GT) or SfM-derived correspondences; 3D GT helps but isn’t mandatory to get gains. ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"))
    
- **“We must import Eric’s weights into glue-factory to use our SP model”** – No. You can **export features** with Eric’s model and **train LightGlue** on those features in glue-factory. Weight import is optional and only needed if you insist on instantiating glue-factory’s SuperPoint module with Eric’s checkpoint. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    

---

### Sources (representative)

- **Eric’s PyTorch SuperPoint:** README (features, metrics, license, env), issues (2024–2025), file tree. ([GitHub](https://github.com/eric-yyjau/pytorch-superpoint "GitHub - eric-yyjau/pytorch-superpoint: Superpoint Implemented in PyTorch: https://arxiv.org/abs/1712.07629"))
    
- **Pautrat’s SuperPoint (TF + PyTorch conversion/inference):** README, files, metrics, license. ([GitHub](https://github.com/rpautrat/SuperPoint "GitHub - rpautrat/SuperPoint: Efficient neural feature detector and descriptor"))
    
- **LightGlue paper & repo:** ICCV’23 paper (training compat with SuperPoint/SIFT; any features), GitHub. ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"), [GitHub](https://github.com/cvg/LightGlue?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed (ICCV ..."))
    
- **glue-factory:** README (open SuperPoint; “train with your own features”; configs & benchmarks). ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))
    
- **External usages/mentions of Eric’s repo:** TI Edge-AI forum thread; academic citation (SuperPoint Tracking Adaptation, 2023); derivative/clone repos. ([e2e.ti.com](https://e2e.ti.com/support/processors-group/processors/f/processors-forum/1132630/tda4vm-training-model-on-edgeai-torchvision?utm_source=chatgpt.com "TDA4VM: training model on edgeai-torchvision - TI E2E"), [dl.acm.org](https://dl.acm.org/doi/10.1007/978-3-031-43907-0_56?utm_source=chatgpt.com "Tracking Adaptation to Improve SuperPoint for 3D ..."), [GitHub](https://github.com/shaofengzeng/SuperPoint-Pytorch?utm_source=chatgpt.com "Pytorch Implementation of rpautrat/SuperPoint"))
    

---

### Final recommendation

Stick with **eric-yyjau/pytorch-superpoint** as your **training/extraction** backbone to keep everything in **PyTorch**, and pair it with **LightGlue** trained via **glue-factory** using **your exported features**. This combination maximizes practicality (no TF), stays aligned with the modern PyTorch ecosystem, and avoids weight-format headaches while still giving you a clean path to **fine-tune matchers on colonoscopy data**. If later you want a single training codebase, you can either (1) convert Eric’s weights to glue-factory’s SuperPoint variant or (2) migrate fully to glue-factory’s “open SuperPoint” once their detector training docs mature. ([GitHub](https://github.com/cvg/glue-factory "GitHub - cvg/glue-factory: Training library for local feature detection and matching"))

If you want, I can sketch the exact **export schema** (npz/HDF5 layout) for Eric’s features and the **glue-factory** dataloader fields it expects so you can plug them in with zero ambiguity.

## Do I have to retrain LightGlue?

>**TL;DR;**
	No, it can be just enough. And if not, it could be retrained with homographies.


Short answer: **you don’t have to retrain LightGlue when you fine-tune SuperPoint**, and you definitely **don’t need 3D ground truth to use LightGlue**. Retraining LightGlue can help, but it’s optional and depends on how far your new SuperPoint descriptors drift from the pretrained distribution.

Here’s the nuance:

- **What LightGlue needs to train (vs. to use):**
    
    - The official recipe _trains_ LightGlue in two stages:
        
        1. **Pre-train on synthetic homography pairs** (planar warps) — this needs **no 3D GT** at all.
            
        2. **Fine-tune on SfM datasets (e.g., MegaDepth)** where **ground-truth correspondences** are derived from known camera poses/depth (via low reprojection error). This part _does_ rely on 3D poses/depth from SfM, not necessarily “perfect” GT sensors. ([GitHub](https://github.com/cvg/glue-factory?utm_source=chatgpt.com "cvg/glue-factory: Training library for local feature detection ..."), [huggingface.co](https://huggingface.co/ETH-CVG/lightglue_superpoint?utm_source=chatgpt.com "ETH-CVG/lightglue_superpoint"), [ar5iv](https://ar5iv.labs.arxiv.org/html/2306.13643?utm_source=chatgpt.com "[2306.13643] LightGlue: Local Feature Matching at Light Speed"), [Microsoft](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/09/LightGlue-Local-Feature-Matching-at-Light-Speed.pdf?utm_source=chatgpt.com "arXiv:2306.13643v1 [cs.CV] 23 Jun 2023"))
            
    - For **inference**, you just feed keypoints+descriptors; **no GT is required**. Pretrained LightGlue weights for **SuperPoint** and **DISK** are provided by the authors. ([GitHub](https://github.com/cvg/LightGlue?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed (ICCV ..."))
        
- **If you fine-tune SuperPoint for colonoscopy:**  
    LightGlue is conditioned on the feature type (it has separate weights “for SuperPoint”), but it’s **not domain-specific**. In practice, the **pretrained LightGlue-for-SuperPoint** model usually still works after you adapt SuperPoint, because the descriptor dimensionality/semantics remain similar. If you observe a drop (lower inlier ratio/mAA), _then_ consider LightGlue fine-tuning. ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"))
    
- **Do you need 3D GT to (re)train LightGlue?**
    
    - **No, not strictly.** You can **(re)train only with homographies** on your own endoscopy frames (planar-ish local patches), which requires **zero 3D**. The authors’ training library (“glue-factory”) ships a homography config for **SuperPoint+LightGlue**. Expect gains mainly in **short-baseline / locally planar** regimes. ([GitHub](https://github.com/cvg/glue-factory?utm_source=chatgpt.com "cvg/glue-factory: Training library for local feature detection ..."))
        
    - **For best wide-baseline/3D robustness**, a **fine-tune with SfM-derived correspondences** (poses+depth) is recommended. That’s where “3D ground truth” comes in—but it can be **pseudo-GT from SfM**, not external sensors. ([ar5iv](https://ar5iv.labs.arxiv.org/html/2306.13643?utm_source=chatgpt.com "[2306.13643] LightGlue: Local Feature Matching at Light Speed"), [Microsoft](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/09/LightGlue-Local-Feature-Matching-at-Light-Speed.pdf?utm_source=chatgpt.com "arXiv:2306.13643v1 [cs.CV] 23 Jun 2023"))
        
- **When to stick with NN (your team’s proposal):**
    
    - **Speed/VRAM**: NN (+ ratio test, cross-check, and geometric verification like RANSAC/AdaLAM) is lighter than any learned matcher and is robust in SLAM loops where you already do pose filtering. LightGlue is designed as a learned “soft-RANSAC” that **cuts false matches and ups inlier rates** but still costs extra compute/memory (albeit far less than SuperGlue). If you’re resource-constrained or your domain is heavily repetitive with specularities (where masking + RANSAC already removes most outliers), **SuperPoint+NN is a solid baseline**. ([openaccess.thecvf.com](https://openaccess.thecvf.com/content/ICCV2023/papers/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.pdf?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed"))
        
- **Practical options for you (no 3D GT available):**
    
    1. **Use off-the-shelf LightGlue-for-SuperPoint** (no retraining) and compare vs. **NN** inside your SLAM pipeline (inlier ratio, track length, ATE/RPE). ([GitHub](https://github.com/cvg/LightGlue?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed (ICCV ..."))
        
    2. If LightGlue lags on colonoscopy, **homography-only fine-tune** on your frames via **glue-factory**; this needs no 3D and is a one-line config to start. ([GitHub](https://github.com/cvg/glue-factory?utm_source=chatgpt.com "cvg/glue-factory: Training library for local feature detection ..."))
        
    3. Later, if you can build **pseudo-GT** (e.g., SfM on curated subsequences or a simulator/phantom), do a **short fine-tune** on those correspondences for the last bit of robustness. ([ar5iv](https://ar5iv.labs.arxiv.org/html/2306.13643?utm_source=chatgpt.com "[2306.13643] LightGlue: Local Feature Matching at Light Speed"), [Microsoft](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/09/LightGlue-Local-Feature-Matching-at-Light-Speed.pdf?utm_source=chatgpt.com "arXiv:2306.13643v1 [cs.CV] 23 Jun 2023"))
        

**Bottom line:** Your colleagues’ suggestion (“use NN because we don’t have 3D GT”) is **reasonable but not strictly necessary**. You can **use LightGlue without retraining**, or **retrain it without 3D** using homographies. Reserve LightGlue fine-tuning with SfM-style correspondences for when you can assemble some pseudo-GT and need extra accuracy. ([GitHub](https://github.com/cvg/LightGlue?utm_source=chatgpt.com "LightGlue: Local Feature Matching at Light Speed (ICCV ..."), [huggingface.co](https://huggingface.co/ETH-CVG/lightglue_superpoint?utm_source=chatgpt.com "ETH-CVG/lightglue_superpoint"), [ar5iv](https://ar5iv.labs.arxiv.org/html/2306.13643?utm_source=chatgpt.com "[2306.13643] LightGlue: Local Feature Matching at Light Speed"), [Microsoft](https://www.microsoft.com/en-us/research/wp-content/uploads/2023/09/LightGlue-Local-Feature-Matching-at-Light-Speed.pdf?utm_source=chatgpt.com "arXiv:2306.13643v1 [cs.CV] 23 Jun 2023"))

## If i use eric´s repo I can´t just plug and play to fine-tune lightglue.

Great! You’re correct: if the architecture differs, it’s **best to treat Eric’s SuperPoint as a feature extractor** and use its output (keypoints + descriptors) as the input for LightGlue fine-tuning, rather than trying to plug weights into glue-factory directly.

Here’s a **step-by-step recipe** to use keypoints/descriptors from Eric’s repo to fine-tune LightGlue with glue-factory:

---

### **Workflow: Use Eric’s SuperPoint to Fine-Tune LightGlue in glue-factory**

#### **1. Fine-Tune/Extract Features with Eric’s SuperPoint**
- Fine-tune Eric’s SuperPoint on your colon images (or other domain).
- Use Eric’s repo to extract **keypoints and descriptors** for all images/pairs you want to use for training LightGlue.

#### **2. Prepare a Feature Dataset for LightGlue**
- **For each image pair** (A, B):
   - Run inference with Eric’s SuperPoint to get:
     - `keypoints_A`, `descriptors_A` for image A
     - `keypoints_B`, `descriptors_B` for image B
   - Save them in a format readable by glue-factory (e.g., `.npz` files, or a Python dict).

- **You must also record ground-truth correspondences** (if available, e.g., from homographies, manual annotation, or synthetic warps).

##### **Example Saved Data Format**
```python
{
    "image_A_path": "A.jpg",
    "image_B_path": "B.jpg",
    "keypoints_A": np.ndarray(N_A, 2),
    "descriptors_A": np.ndarray(N_A, D),
    "keypoints_B": np.ndarray(N_B, 2),
    "descriptors_B": np.ndarray(N_B, D),
    "matches": np.ndarray(M, 2)  # optional, indices of matches A/B
}
```

#### **3. Adapt LightGlue Training in glue-factory**
- Modify or create a custom dataset loader in glue-factory that reads your **precomputed keypoints/descriptors** instead of running SuperPoint inference.
- The loader should yield batches of:
   - descriptors_A, keypoints_A, descriptors_B, keypoints_B, [matches]
- Configure LightGlue to use the descriptor dimension (e.g., D=256 if you use default SuperPoint).

#### **4. Fine-Tune LightGlue**
- Use glue-factory’s matcher training scripts (`train_matcher.py` or similar).
- Point the extractor to your dataset of features (not the SuperPoint model).
- Train LightGlue as usual, but using your precomputed features.

##### **Pseudo-code for Custom Dataset Loader**
```python name=custom_feature_dataset.py
import numpy as np
import torch

class PrecomputedFeaturePairDataset(torch.utils.data.Dataset):
    def __init__(self, feature_pair_files):
        self.files = feature_pair_files

    def __getitem__(self, idx):
        data = np.load(self.files[idx])
        return {
            "keypoints_A": torch.tensor(data["keypoints_A"], dtype=torch.float32),
            "descriptors_A": torch.tensor(data["descriptors_A"], dtype=torch.float32),
            "keypoints_B": torch.tensor(data["keypoints_B"], dtype=torch.float32),
            "descriptors_B": torch.tensor(data["descriptors_B"], dtype=torch.float32),
            "matches": torch.tensor(data["matches"], dtype=torch.long) if "matches" in data else None,
        }

    def __len__(self):
        return len(self.files)
```

##### **Pseudo-code for Training**
```python name=train_lightglue_with_features.py
from custom_feature_dataset import PrecomputedFeaturePairDataset
from gluefactory.models.matchers.lightglue import LightGlue

dataset = PrecomputedFeaturePairDataset(list_of_npz_files)
dataloader = torch.utils.data.DataLoader(dataset, batch_size=8, shuffle=True)

matcher = LightGlue(descriptor_dim=256)  # or 128 if you used that

optimizer = torch.optim.Adam(matcher.parameters(), lr=1e-4)

for batch in dataloader:
    # Your batch has descriptors/keypoints
    out = matcher(batch["descriptors_A"], batch["keypoints_A"],
                  batch["descriptors_B"], batch["keypoints_B"])
    # Compute loss using batch["matches"] or built-in matcher objectives
    loss = compute_lightglue_loss(out, batch["matches"])
    loss.backward()
    optimizer.step()
```

---

#### **Summary**

- **Don’t port weights:** Extract features from Eric’s SuperPoint and save them.
- **Build a dataset of keypoints/descriptors/matches.**
- **Modify glue-factory’s matcher training to use your saved features.**
- **Fine-tune LightGlue with this custom loader and your features!**

---

**Let me know if you want example file formats or a full training script for glue-factory. This workflow is robust and avoids the architecture mismatch issue.**



# Metrics of matching (proxy)
## Repeatability
Not. We could Report **HA-repeatability curves** during training (AUC @ 1/3/5 px) and compare domains (240×320 vs HQ colonoscopy crops) but we had to make an entire script.

Given two images and a set of detected keypoints in each, repeatability is the % of keypoints from image A that have a corresponding keypoint in image B within a tolerance (usually 3 px or 5 px when projected into one image by homography or known pose)[arxiv.org](https://arxiv.org/abs/2203.04302#:~:text=SuperPoint%20based%20models%20achieve%20significantly,Our%20adapted%20model). High repeatability means the detector finds the same physical points despite viewpoint change. In endoscopy, repeatability is tricky since the scene isn’t static; we will compute it on sequences or image pairs known to have small deformation (e.g., EndoSLAM static phantom or consecutive frames of real data where motion is small). We can also report a _masked repeatability_ where we exclude regions that undergo obvious change (specular or moving tissue).

## **Homography/AP Accuracy (HA@pix):** 
NOT.Not strictly planar, fish-eye 

For planar scenes or when a homography relates the images, one can compute the fraction of point correspondences that are below X pixels error when transformed by the estimated homography. HPatches uses AUC of this for thresholds 1,3,5 px[arxiv.org](https://arxiv.org/abs/2203.04302#:~:text=SuperPoint%20based%20models%20achieve%20significantly,Our%20adapted%20model). We can simulate a homography scenario by printing a calibration grid, filming it with an endoscope and moving it. But since we have direct 3D evaluation ability, we might not do separate homography tests. Still, it’s a metric to consider for any flat phantom surfaces.


## **Localization Error (Keypoint accuracy):** 

YES. The average pixel error between the detected keypoint location and the true location (usually measured when ground truth correspondence is known or on synthetic data with known warps). This is important if one cares about precise localization for triangulation. Learned methods often achieve sub-pixel localization (SuperPoint does through softargmax). We might measure this on SCARED stereo pairs: e.g., detect a point in left image and see how close its epipolar match in right image is to the ground-truth epipolar line. However, in our context, this is secondary; slight localization error (1–2 px) is usually absorbed by RANSAC.

## **Matching Precision & Recall:** 

NOT. Similar to repetability. They are performed with the synthetic wrapping. 

When ground-truth correspondences are known (via stereo or simulation), we can classify each tentative match as True/False. **Precision** = correct_matches / total_matches, **Recall** = correct_matches / total_true_correspondences (those that should have been matched). We often see **Matching Score** defined as correct_matches / min(n_kp1, n_kp2)[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=matching%20performance,of%20matching%20methods%20will%20discard), which is essentially a recall normalized by number of detections. We will calculate precision and recall at perhaps a 3 px threshold for a match to be considered correct (or using an epipolar distance threshold e.g. 1 px). In the superpixel paper, they got precision ~92%[pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC9045917/#:~:text=Feature%20matching%20is%20an%20important,the%20vector%20feature%20to%20optimize) – that’s very high, indicating few outliers, likely because they used their strong spatial filter. Learned matchers like SuperGlue similarly can reach 95% precision on easy sequences (few mismatches). But on wide baseline, precision might drop. Both metrics are useful: we want high precision (outliers are bad for pose estimation) and decent recall (enough matches to constrain pose). Sometimes these are combined into **F1-score** (harmonic mean). We’ll include that as well.

## **Essential/Fundamental Matrix Accuracy:** 

Good IDEA. Read again about plotting the recall vs threshold to het the curve and cupmpute the area = mAA.

When evaluating feature matching for pose, one robust way is to see if the correct fundamental matrix can be estimated. We define an error for an estimated pose (R, t) vs ground truth (R<sub>gt</sub>, t<sub>gt</sub>): either use angular deviation of rotation and translation or measure the Sampson error of all inlier correspondences. The IMC challenge’s mAA metric wraps this into a single number[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=mAA%2C%20we%20%EF%AC%81rst%20calculate%20the,rotation%20and%20translation)[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=threshold,registered%20are%20set%20to%20identity). We will adopt the **rotation and translation threshold** approach: e.g., measure % of image pairs whose estimated pose is within 5° and 5 mm of ground truth. That percentage is analogous to recall of correct registration. We can plot recall vs threshold to get a curve and compute the area = mAA[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=F%20or%20the%20mAA%20computations%2C,0.25%2C0.5%2C1%2C2%2C5%2C10%5D%20de). If needed, we’ll separate rotation and translation components to diagnose if a method struggles more with one (especially since monocular VO has scale ambiguity – we might align scale before computing translation error to be fair). For colonoscopy, scale is arbitrary (we can align to one frame’s depth for evaluation).

## mAA + Nimg

As explained earlier. PLus we are going to inform the AA per threshold.

## **Inlier Ratio:** 
Yes. 

For each image pair, after running RANSAC, we compute inliers/total matches. This tells us how “clean” the match set was. E.g., a robust pipeline might have 80% inliers on most pairs, whereas a weaker might have 30%. We’ll average this over all pairs (or frames). This metric doesn’t directly translate to accuracy but is a good indicator of match quality and efficiency of the matcher in rejecting outliers. We might call it **Matching Inlier Ratio** and give mean and median. Notably, SuperGlue often yields inlier ratios ~0.7–0.8 on ScanNet (as reported by Sarlin), versus 0.3 for SIFT. In endoscopy, where many points might violate one rigid model (due to deformation), even a perfect method might max out at, say, 0.5 inliers (half the points might come from static background, half from moving tissue that one model cannot fit). So we’ll interpret inlier ratio carefully and perhaps measure it in a region-masked way.

## **Timing and Memory:** 

Yes. We will log the **average runtime per frame (or per image pair)** for each method on a given hardware. E.g., SuperPoint extraction time, matching time, total. We also note if a method uses GPU or not. Memory usage in matching mainly concerns GPU VRAM for big transformer models, but for fairness we can list peak memory. E.g., LoFTR’s attention on 8× downscaled feature map of a 1024×1024 image can use ~2 GB. LightGlue uses <0.5 GB typically. This might not be a strict metric but rather reported for completeness in results tables.

## **Image Coverage:** 

Maybe. Look to Leon´s paper:
 what percentage of the image area has at least one feature tracked (this indicates if the method covers the field of view well or clusters features in one region). For a thorough exploration like colonoscopy, broad coverage is desir
 
## **Region-wise feature count:** 

It´s a detail. We could see if we have enough data to test in different parts of the colon.

using EndoMapper’s anatomical annotations, we can report, say, average number of features in the cecum vs transverse colon (the textures differ, with the rectum having more blood vessels, etc.), to ensure the method isn’t biased to one type of texture.

## **Cross-patient pairs (for place recognition)**: 
Nice idea. But we are already using BoW.

optionally, an image from one patient’s colon vs an image of a similar-looking area from another patient. A generic matcher might erroneously match them due to similar texture, but a good system should ideally not (this is more for false positive analysis in loop closure).

# Metrics of SLAM/VO
All the CUDASIFTs. But I also copy and paste to stud/remember.

- **Absolute Trajectory Error (ATE):** Measures the discrepancy between the estimated camera trajectory and ground truth trajectory, after aligning them (usually with a similarity transform if scale is free). We’ll compute ATE as the root mean square of Euclidean distances between estimated and true camera centers, after aligning via SE(3) (or SE(3)+scale if needed). For EndoSLAM, since ground truth is metric, we can directly align scale. For simulated sequences, metric scale is known. ATE gives an overall performance (in mm or cm).
    
- **Relative Pose Error (RPE):** We’ll compute the error in frame-to-frame motion. For each time step Δ (like 1 frame apart or 5 frames apart), RPE_translation = ‖ (Δ pose_est) - (Δ pose_gt) ‖, and RPE_rotation = angle between (ΔR_est * ΔR_gt^T). We might average these over the sequence. TUM RGB-D benchmark uses a similar notion but integrated into drift per second or per meter. For colonoscopy, perhaps drift per meter traveled (but since we don’t have a clear “distance traveled” in deforming space, we can approximate by insertion length).
    
- **Scale Drift:** For monocular VO, scale can drift over time (the estimated map scaling changes). If ground truth scale is known, we can measure scale error at intervals. This may not be key for our learned feature benchmark unless we compare methods that inherently recover scale differently (not likely, since none recover scale unless using known size references). Still, if we have loop closures, we can measure final scale drift closed vs open loop.
    
- **Tracking Loss Events:** As mentioned, count how many times the method lost track (e.g., insufficient inliers to continue). Also measure the **distance or time traveled between lost tracks** (longer = more robust tracking).
    
- **Loop Closure Recall/Precision:** If we incorporate a loop detection mechanism (perhaps outside pure matching), we could measure how many known loop closures were detected (recall) and how many reported loop closures were true vs false (precision). Since our focus is on low-level matching, we might not implement a full loop closure module initially, but we can simulate one by attempting to match non-consecutive frames at certain intervals or based on image retrieval and see if they align.
    
- **Map Quality:** If a method produces a 3D point cloud map (like ORB-SLAM does via bundle adjustment), we can measure that map’s accuracy. For example, for EndoSLAM’s phantom or C3VD’s CT model, we could take the reconstructed sparse points and compute average distance to the ground truth surface (after alignment). This is a direct way to evaluate the _mapping_ aspect, not just tracking. It’s a bit beyond just matching, but it closes the loop on how good matches yield good 3D structure.
    
# Baselines to include

Superpoint (E, pretrained, fine-tuned) - NN/LG
RoMA (i am still not filtering all the keypoints on specularities)
SIFT - NN
ORB - NN

Take into account that I should run different hyperparameters to find the sweet spot of each method. Maybe this idea [[Sota Supplementary#**Essential/Fundamental Matrix Accuracy **]]


# Selection of images
From CUDASFIT?
from lightdepth?


# Lighgt glue vs NN as matcher

**LightGlue vs Nearest Neighbor in SLAM:** This addresses Q3 more directly now. As discussed, LightGlue provides more robust matching especially in difficult cases. But it is a learned matcher that consumes some compute. On a modern GPU the cost is small (20 ms), but on CPU it would be too slow. If we ever aim to run on CPU (maybe not, but in some settings possibly), NN matching with good outlier rejection (AdaLAM) might be the fallback. There’s also an approach of doing coarse initial matching via NN, then using LightGlue’s attention on only those matches to refine (not sure if implemented, but could). We will likely integrate LightGlue into our system for all inter-keyframe matching (since keyframes by definition may have larger baseline) and possibly skip it for consecutive frame tracking (if that proves stable). The VRAM usage of LightGlue is quite low; Mismatched ran it on 12MP images with ~2k features fine on 48GB A6000[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=LoFTR%20,285), so on our small images and <1k features it’s negligible (<1GB).

**NEAREST-NEIGHBOR vs. LIGHTGLUE speed:** The pointer network in LightGlue scales roughly linearly with matches because it prunes aggressively. The original LightGlue paper shows matching 2000 features takes ~16 ms on an RTX3090, compared to ~12 ms for brute-force nearest neighbor + ratio test for 2000 features[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=May%202025%20%C2%B7%20Journal%20of,Imaging)[researchgate.net](https://www.researchgate.net/publication/383529999_Mismatched_Evaluating_the_Limits_of_Image_Matching_Approaches_and_Benchmarks#:~:text=extreme%20viewpoint%20and%20rotational%20variations%2C,limited%20environments). So LightGlue was only slightly slower, and delivered better outlier filtering. On CPU or without GPU, brute-force matching might be faster because LightGlue’s transformers would then run on CPU slowly. But we plan to use GPU.

**One more factor: memory** – If we have to conserve memory (maybe running on embedded with limited GPU mem along with other tasks), LightGlue has advantage over SuperGlue: it uses local attention and does not instantiate full N×N attention matrices. For example, with 1000 features each in two images (total 2000), SuperGlue’s memory is O(N^2) = 4 million pairwise comparisons stored, whereas LightGlue’s is maybe O(N * 40) after pruning (just conceptual). So LightGlue can match high feature counts without blowing up memory. This means if we decide to push resolution and have many features, LightGlue will handle it more gracefully than global NN matching (which becomes heavy in compute) or SuperGlue (heavy in memory).

**Conclusion on Q3:** For SLAM, when using a GPU, **SuperPoint+LightGlue is preferred** except possibly for immediate frame-to-frame tracking where the motion is small enough that a cheap nearest-neighbor + descriptor distance check might suffice (and we already do have geometric check via RANSAC). We will likely still default to LightGlue even frame-to-frame because it gives an extra safety margin (especially if frame blur or rotation introduced false matches, LightGlue might filter those better). The ~10 ms difference isn’t huge given camera framerate (~30 FPS, budget ~33ms per frame). If we absolutely needed to optimize, we could test the difference: perhaps we find that using LightGlue yields, say, 5% more inliers on frame-to-frame when lighting changes occur (like auto-exposure jumps). That may be worth the time.

# Datasets