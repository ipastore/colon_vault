ALguna vez carga los undistorted Sfm?
yo tengo las images? Creo que no las necesitaría? o Si?

No necesito preprocessing: ya van loadeadas. 
#### Notes: errors

**Note1**: I don´t have installed at least in the student machine pycolmap with CUDA support. 
**Note2**: with pycolmap_cpu
```bash
\[11/12/2025 07:41:30 gluefactory INFO] Starting epoch 0
Traceback (most recent call last):
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 736, in <module>
    main_worker(0, conf, output_dir, args)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 667, in main_worker
    training(rank, conf, output_dir, args)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 474, in training
    pred = model(data)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1775, in _wrapped_call_impl
    return self._call_impl(*args, **kwargs)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1786, in _call_impl
    return forward_call(*args, **kwargs)
  File "/home/student/glue-factory-colon/gluefactory/models/base_model.py", line 114, in forward
    return self._forward(data)
  File "/home/student/glue-factory-colon/gluefactory/models/two_view_pipeline.py", line 73, in _forward
    pred0 = self.extract_view(data, "0")
  File "/home/student/glue-factory-colon/gluefactory/models/two_view_pipeline.py", line 67, in extract_view
    pred_i = {**pred_i, **self.extractor(data_i)}
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1775, in _wrapped_call_impl
    return self._call_impl(*args, **kwargs)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1786, in _call_impl
    return forward_call(*args, **kwargs)
  File "/home/student/glue-factory-colon/gluefactory/models/base_model.py", line 114, in forward
    return self._forward(data)
  File "/home/student/glue-factory-colon/gluefactory/models/extractors/sift.py", line 226, in _forward
    p = self.extract_single_image(img)
  File "/home/student/glue-factory-colon/gluefactory/models/extractors/sift.py", line 181, in extract_single_image
    pred["keypoint_scores"],
KeyError: 'keypoint_scores'
```
**Note3: with opencv**
```bash
[11/12/2025 07:35:45 gluefactory INFO] Starting epoch 0
Traceback (most recent call last):
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 736, in <module>
    main_worker(0, conf, output_dir, args)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 667, in main_worker
    training(rank, conf, output_dir, args)
  File "/home/student/glue-factory-colon/gluefactory/train.py", line 474, in training
    pred = model(data)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1775, in _wrapped_call_impl
    return self._call_impl(*args, **kwargs)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1786, in _call_impl
    return forward_call(*args, **kwargs)
  File "/home/student/glue-factory-colon/gluefactory/models/base_model.py", line 114, in forward
    return self._forward(data)
  File "/home/student/glue-factory-colon/gluefactory/models/two_view_pipeline.py", line 73, in _forward
    pred0 = self.extract_view(data, "0")
  File "/home/student/glue-factory-colon/gluefactory/models/two_view_pipeline.py", line 67, in extract_view
    pred_i = {**pred_i, **self.extractor(data_i)}
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1775, in _wrapped_call_impl
    return self._call_impl(*args, **kwargs)
  File "/home/student/anaconda3/envs/gluefactory/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1786, in _call_impl
    return forward_call(*args, **kwargs)
  File "/home/student/glue-factory-colon/gluefactory/models/base_model.py", line 114, in forward
    return self._forward(data)
  File "/home/student/glue-factory-colon/gluefactory/models/extractors/sift.py", line 228, in _forward
    pred = {k: torch.stack([p[k] for p in pred], 0).to(device) for k in pred[0]}
  File "/home/student/glue-factory-colon/gluefactory/models/extractors/sift.py", line 228, in <dictcomp>
    pred = {k: torch.stack([p[k] for p in pred], 0).to(device) for k in pred[0]}
RuntimeError: stack expects each tensor to be equal size, but got [1406, 2] at entry 0 and [1285, 2] at entry 1
```


### Que hay dentro de scene_info?
0 = 'image_paths.npy'
1 = 'depth_paths.npy'
2 = 'intrinsics.npy'
3 = 'poses.npy'
4 =  'overlap_matrix.npy'
5 = 'scale_ratio_matrix.npy'
6 = 'angles.npy'
7 = 'n_points3D.npy'
8 = 'points3D_id_to_2D.npy'
9 = 'points3D_id_to_ndepth.npy'


I need the SIFT parameters for each point in the 3d maps

# Conversation with Copilot 12-11-25 to elaborate plan
## Plan for Adapting Two-View Pipeline for Sparse SLAM Training

Based on your presentation needs and the codebase analysis, here's a comprehensive list:

---

### **a. Dataset Implementation**
**File:** `gluefactory/datasets/endomapper.py`

**Key differences from MegaDepth:**
- ✅ Load COLMAP sparse reconstruction (`cameras.txt`, `images.txt`, `points3D.txt`)
- ✅ Parse 3D point tracks instead of dense depth maps
- ✅ Handle multiple sub-maps per sequence (e.g., `3D_maps/0/`, `3D_maps/1/`, etc.)
- ✅ Camera trajectory loading (`cam_traj_map_XXX.txt`)
- ✅ Pair selection based on:
  - Co-visibility (shared 3D points)
  - Baseline (camera distance)
  - View overlap percentage
- ✅ Handle variable image resolutions (colonoscopy frames)
- ✅ Scene-specific train/val splits (`Seq_XXX_a` pattern)

---

### **b. Ground Truth Matcher**
**File:** `gluefactory/models/matchers/sparse_matcher.py`

**Changes from `depth_matcher.py`:**
- ✅ **Remove depth-based parameters:**
  - `th_positive`, `th_negative`, `th_epi`, `th_consistency`
- ✅ **Track-based matching (threshold-free):**
  - Direct lookup from COLMAP 3D point observations
  - Bidirectional match verification
- ✅ **Required data keys:**
  ```python
  required_data_keys = [
      "view0", "view1",
      "keypoints0", "keypoints1",
      "tracks0", "tracks1",  # 2D→3D associations
      "point3D_ids0", "point3D_ids1"
  ]
  ```
- ✅ **Handle unmatched keypoints:**
  - Mark as non-matches (for negative mining)
  - Visibility-based filtering

---

### **c. New Filters for Sparse Maps**

**File:** `gluefactory/models/filters/` (new filters needed)

#### **c.1. Minimum Track Length Filter**
**File:** `filters/min_track_length.py`
- Filter out 3D points observed in < N images
- **Reason:** Short tracks = unreliable triangulation
- **Parameter:** `min_observations: 3` (default)

#### **c.2. Reprojection Error Filter**
**File:** `filters/reprojection_error.py`
- Filter matches with high reprojection error from COLMAP
- **Reason:** COLMAP stores per-observation error
- **Parameter:** `max_error: 2.0` pixels

#### **c.3. Minimum Co-visibility Filter**
**File:** `filters/min_covisibility.py`
- Require minimum number of shared 3D points between image pairs
- **Reason:** Ensure sufficient overlap for training
- **Parameter:** `min_shared_points: 50`

#### **c.4. Depth Range Filter**
**File:** `filters/depth_range.py`
- Filter 3D points too close/far from camera
- **Reason:** Handle colonoscopy's limited depth range
- **Parameters:**
  - `min_depth: 0.1` meters
  - `max_depth: 10.0` meters

#### **c.5. Spatial Distribution Filter**
**File:** `filters/spatial_distribution.py`
- Ensure matches are well-distributed across image
- **Reason:** Avoid clustering in textured regions
- **Method:** Grid-based sampling or entropy-based selection

---

### **d. Additional Components to Add**

#### **d.1. COLMAP Data Parsers**
**File:** `gluefactory/datasets/colmap_utils.py`

**Functions needed:**
```python
def read_cameras_binary(path) -> Dict[int, Camera]
def read_images_binary(path) -> Dict[int, Image]
def read_points3D_binary(path) -> Dict[int, Point3D]
def read_cameras_text(path) -> Dict[int, Camera]
def read_images_text(path) -> Dict[int, Image]
def read_points3D_text(path) -> Dict[int, Point3D]
def build_image_pair_graph(images, points3D, min_covisibility)
```

#### **d.2. Pair Selection Strategy**
**File:** `gluefactory/datasets/endomapper_pairs.py`

**Strategies:**
- **Co-visibility Graph:** Select pairs with shared 3D points
- **Temporal Pairs:** Consecutive frames from colonoscopy
- **Loop Closure Pairs:** Distant frames with overlap
- **Hierarchical Sampling:** Balance easy/hard pairs

#### **d.3. Visualization Tools**
**File:** `gluefactory/visualization/visualize_sparse.py`

**Add visualizations for:**
- 3D point cloud overlay on image pairs
- Track length heatmap
- Co-visibility graph
- Reprojection error distribution

---

### **e. Configuration Files**

#### **e.1. Dataset Config**
**File:** `gluefactory/configs/data/endomapper.yaml`
```yaml
name: endomapper
root_path: /media/student/HDD/nacho/glue-factory/data/Endomapper_CUDASIFT_OCT25/
sequences:
  train: [Seq_001_a, Seq_002_a, ...]
  val: [Seq_001_b, Seq_002_b, ...]
preprocessing:
  resize: 512
  side: long
  square_pad: True
pair_selection:
  min_covisibility: 50
  max_baseline: 0.3  # meters
  temporal_window: 10  # frames
filters:
  min_track_length: 3
  max_reprojection_error: 2.0
  min_depth: 0.1
  max_depth: 10.0
```

#### **e.2. Training Config**
**File:** `gluefactory/configs/sift+lightglue_endomapper.yaml`
```yaml
data:
  name: endomapper
  # ... (rest from data/endomapper.yaml)
model:
  name: two_view_pipeline
  extractor:
    name: extractors.sift
    backend: pycolmap_cpu
    max_num_keypoints: 2048
  matcher:
    name: matchers.lightglue
  ground_truth:
    name: matchers.sparse_matcher  # NEW!
```

---

### **f. Data Loader Modifications**

#### **f.1. Handle Multiple Sub-maps**
**File:** `gluefactory/datasets/endomapper.py`
- Load all sub-maps (`3D_maps/{0,1,2,3}/`)
- Merge or handle separately based on camera trajectory

#### **f.2. Camera Model Support**
- Endomapper likely uses **OPENCV** or **RADIAL** camera models
- Ensure distortion handling in reprojection

---

### **g. Testing & Validation**

#### **g.1. Unit Tests**
**File:** `tests/test_endomapper_dataset.py`
```python
def test_load_sequence()
def test_parse_colmap_files()
def test_build_covisibility_graph()
def test_track_based_matching()
```

#### **g.2. Integration Tests**
**File:** `tests/test_sparse_training.py`
```python
def test_endomapper_dataloader()
def test_sparse_matcher_forward()
def test_training_loop_one_epoch()
```

---

### **h. Documentation**

#### **h.1. Dataset README**
**File:** `docs/ENDOMAPPER.md`
- Dataset structure explanation
- Download instructions
- Preprocessing steps

#### **h.2. Training Guide**
**File:** `docs/TRAINING_SPARSE.md`
- Differences from dense training
- Hyperparameter recommendations
- Expected metrics/performance

---

## Summary Checklist

### **Core Implementation:**
- [ ] a. `datasets/endomapper.py` - COLMAP-based dataloader
- [ ] b. `matchers/sparse_matcher.py` - Track-based GT matcher
- [ ] c. New filters (5 filters listed above)

### **Supporting Infrastructure:**
- [ ] d.1. `datasets/colmap_utils.py` - COLMAP parsers
- [ ] d.2. `datasets/endomapper_pairs.py` - Pair selection
- [ ] d.3. `visualization/visualize_sparse.py` - Debug tools
- [ ] e.1. `configs/data/endomapper.yaml`
- [ ] e.2. `configs/sift+lightglue_endomapper.yaml`

### **Quality Assurance:**
- [ ] g.1. Unit tests for dataset/matcher
- [ ] g.2. Integration tests for training
- [ ] h. Documentation (README + training guide)

---

**Would you like me to start implementing any of these components? I recommend starting with:**
1. `colmap_utils.py` (foundation for everything)
2. `sparse_matcher.py` (core logic)
3. `endomapper.py` (dataset loader)

Let me know which one to prioritize! 🚀