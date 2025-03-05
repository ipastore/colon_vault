# **How to Compare Your Deep Learning Matcher to COLMAP Using Metrics from Bonilla et al. (2024)**

To evaluate your **deep learning-based image matcher** against **COLMAP’s matching approach**, we should follow the methodology used in **Bonilla et al. (2024)**. Their evaluation framework consists of **three key metrics**:

---

### **1) Number of Registered Images (`Nimg`)**

- Measures how many image pairs successfully register within a structure-from-motion (SfM) pipeline.
- Since you are evaluating only **one image pair**, this metric isn’t directly applicable yet.
- However, you could **extend** your study to multiple pairs and compute **registration success rate**.

#### **How to Implement:**

- If `pycolmap.estimate_essential_matrix()` returns **None**, the pair **failed registration**.
- Otherwise, count the number of successfully estimated relative poses.

```python
is_registered = result_colmap is not None
```

For multiple image pairs, store results and compute:

```python
Nimg = sum(success_list) / len(success_list) * 100  # Percentage of registered pairs
```

---

### **2) Average Matching Time (`Time`)**

- Measures the average time taken to match a single pair of images.
- Compare how long **COLMAP’s matcher (SIFT)** vs. **your deep learning matcher (SuperPoint-LightGlue, etc.)** takes.

#### **How to Implement:**

Use Python’s `time` module to track execution time.

```python
import time

# Time for deep learning matcher
start_time = time.time()
result_dl = process_image_pairs(img0_path, img1_path, output_dir, model_name, matcher)
dl_time = time.time() - start_time

# Time for COLMAP matching
start_time = time.time()
result_colmap = pycolmap.estimate_essential_matrix(corrected_mkpts0, corrected_mkpts1, camera0, camera1)
colmap_time = time.time() - start_time

print(f"Deep Matcher Time: {dl_time:.4f} sec, COLMAP Time: {colmap_time:.4f} sec")
```

This allows you to compare speed differences between **COLMAP’s SIFT pipeline** and **your deep learning-based matcher**.

---

### **3) Mean Average Accuracy (`mAA`)**

- Measures **how well** estimated relative poses align with the **ground truth (pseudo-GT from COLMAP SfM pipeline)**.
- It thresholds **rotation error** and **translation error**, then **averages over different thresholds**.

#### **How to Compute mAA:**

1. **Extract Rotation & Translation Errors**
    
    - Compute **rotation error** using **geodesic distance** between **COLMAP’s pose and your estimated pose**: rotation error=∥log⁡(RestRcolmapT)∥\text{rotation error} = \|\log(R_{\text{est}} R_{\text{colmap}}^T)\|
    - Compute **translation error** by measuring **angle difference**: translation error=arccos⁡(test⋅tcolmap∥test∥∥tcolmap∥)\text{translation error} = \arccos\left(\frac{t_{\text{est}} \cdot t_{\text{colmap}}}{\|t_{\text{est}}\| \|t_{\text{colmap}}\|}\right)
2. **Threshold the Errors**
    
    - Bonilla et al. use multiple thresholds for **rotation (°)** and **translation (meters)**.
    - If errors fall **below the thresholds**, count as a successful match.
3. **Compute Final `mAA` Score**
    
    - Compute **percentage of image pairs that pass the thresholds**.
    - Average across different thresholds.

#### **Implementation:**

```python
import numpy as np
from scipy.spatial.transform import Rotation as R

def compute_pose_errors(R_est, t_est, R_colmap, t_colmap):
    """
    Compute rotation and translation errors between estimated and COLMAP poses.
    """
    # Compute rotation error (geodesic distance)
    R_diff = R.from_matrix(R_est @ R_colmap.T)
    rot_error = np.linalg.norm(R_diff.as_rotvec(), ord=2)  # Rotation error in radians

    # Compute translation error (angle between unit vectors)
    t_est_norm = t_est / np.linalg.norm(t_est)
    t_colmap_norm = t_colmap / np.linalg.norm(t_colmap)
    trans_error = np.arccos(np.clip(np.dot(t_est_norm, t_colmap_norm), -1.0, 1.0))

    return np.degrees(rot_error), np.degrees(trans_error)  # Convert radians to degrees

# Compute pose errors for this image pair
rot_err, trans_err = compute_pose_errors(R_est, t_est, R_colmap, t_colmap)

# Define Bonilla et al.'s thresholds
rot_thresholds = [0.25, 0.5, 1, 2, 5, 10]  # Degrees
trans_thresholds = [0.025, 0.05, 0.1, 0.2, 0.5, 1]  # Meters

# Check if errors fall below each threshold
successful_pairs = [
    (rot_err < r_thresh) and (trans_err < t_thresh)
    for r_thresh, t_thresh in zip(rot_thresholds, trans_thresholds)
]

# Compute mAA as the mean over thresholds
mAA = np.mean(successful_pairs)
print(f"mAA Score: {mAA:.4f}")
```

---

### **How to Compare COLMAP vs. Deep Matcher**

1. **Run both matchers and extract relative poses**:
    
    ```python
    # COLMAP pose estimation
    result_colmap = pycolmap.estimate_essential_matrix(corrected_mkpts0, corrected_mkpts1, camera0, camera1)
    R_colmap, t_colmap = result_colmap["R"], result_colmap["t"]
    
    # Deep learning matcher pose estimation
    result_dl = process_image_pairs(img0_path, img1_path, output_dir, model_name, matcher)
    R_dl, t_dl = estimate_relative_pose(mkpts0, mkpts1, camera0.calibration_matrix())
    ```
    
2. **Compute errors for both methods**:
    
    ```python
    rot_err_colmap, trans_err_colmap = compute_pose_errors(R_colmap, t_colmap, R_colmap, t_colmap)  # Self-check
    rot_err_dl, trans_err_dl = compute_pose_errors(R_dl, t_dl, R_colmap, t_colmap)
    
    print(f"COLMAP Errors -> Rotation: {rot_err_colmap:.4f}°, Translation: {trans_err_colmap:.4f}°")
    print(f"Deep Matcher Errors -> Rotation: {rot_err_dl:.4f}°, Translation: {trans_err_dl:.4f}°")
    ```
    
3. **Compute `mAA` for both methods**:
    
    ```python
    mAA_colmap = np.mean([rot_err_colmap < r and trans_err_colmap < t for r, t in zip(rot_thresholds, trans_thresholds)])
    mAA_dl = np.mean([rot_err_dl < r and trans_err_dl < t for r, t in zip(rot_thresholds, trans_thresholds)])
    
    print(f"COLMAP mAA: {mAA_colmap:.4f}, Deep Matcher mAA: {mAA_dl:.4f}")
    ```
    

---

### **Final Summary**

|Metric|COLMAP (SIFT)|Deep Matcher (SuperPoint-LightGlue, etc.)|
|---|---|---|
|`Nimg` (Success Rate)|X%|Y%|
|`Time` (per pair)|T_colmap sec|T_dl sec|
|`Rotation Error` (°)|R_err_colmap|R_err_dl|
|`Translation Error` (°)|T_err_colmap|T_err_dl|
|`mAA` Score|mAA_colmap|mAA_dl|

---

### **Conclusion**

🚀 **This workflow directly replicates how Bonilla et al. evaluated matchers.**  
✅ **Compares COLMAP (SIFT) vs. deep learning matchers fairly.**  
✅ **Computes metrics used in their evaluation (Nimg, Time, mAA).**  
✅ **Ready to extend to more image pairs for a full evaluation!**

Would you like help automating this across multiple image pairs? 🔥


# Second sketch
Below is essentially the **same solution** for computing rotation/translation error and mAA, **but** with additional detail about **scaling/normalizing** your estimated translation vectors. This addresses the **arbitrary reference frame** issue:

---

## **1) When (and How) to Handle Scale?**

If you are working with a **single scene**, **single camera** (same intrinsics), and you are certain that:

- Both **ground-truth** (COLMAP-based) and **estimated** poses use **the same coordinate system**,
- The scale factor from the SfM pipeline is **fixed** (e.g., it’s a calibrated scenario with known intrinsics/focal length),

then you **do not** need to manually scale `t_est`. You can directly compare `(R, t)` from both solutions.

**However**, if you used a method (e.g., pure 2-view geometry or an uncalibrated pipeline) that produces **scale-ambiguous** results, you might see that:

- `||t_est||` is inconsistent with `||t_colmap||`.

In such a case, you have at least three options:

1. **Scale `t_est`** so that `||t_est||` matches `||t_colmap||`.
    
    - This effectively says: “We accept the orientation of `t_est` but forcibly fix its norm to match the ground truth.”
    - This might artificially remove some “scale error” from your results, but if your problem is purely relative pose, it’s common to allow that.
2. **Ignore the scale** and measure only **direction error** in the translation.
    
    - For example, compare normalized translations `t_est / ||t_est||` vs. `t_colmap / ||t_colmap||`.
3. **Include a custom scale factor** if partial ground truth is known or if you have a known baseline.
    

> **In your case** (same scene, same camera, presumably a calibrated scenario):  
> **No** manual scaling is typically needed. Both `t_colmap` and `t_est` should already be in the same metric scale if the pipeline is consistent.

---

## **2) Rotation Error in Degrees**

```python
import numpy as np
from scipy.spatial.transform import Rotation as R

def rotation_error_deg(R_colmap: np.ndarray, R_est: np.ndarray) -> float:
    """
    Compute rotation error in degrees between two 3x3 rotation matrices.
    If R_est == R_colmap, result is ~0 degrees.
    """
    assert R_colmap.shape == (3, 3), "R_colmap must be 3x3"
    assert R_est.shape == (3, 3),    "R_est must be 3x3"

    # If colmap and est are identical, R_diff ~ identity => angle=0
    R_diff = R.from_matrix(R_colmap @ R_est.T)
    angle_rad = R_diff.magnitude()
    return np.degrees(angle_rad)
```

---

## **3) Translation Error in Meters**

```python
def translation_error_m(t_colmap: np.ndarray, t_est: np.ndarray) -> float:
    """
    Compute translation error in meters between two translation vectors.
    Both must be in the same coordinate scale. 
    """
    assert t_colmap.shape == (3,), "t_colmap must be shape (3,)"
    assert t_est.shape == (3,),    "t_est must be shape (3,)"

    return np.linalg.norm(t_colmap - t_est)
```

 **Scaling** (Optional):

 ```python
 # If your pipeline is scale-ambiguous and you want to force norm equality:
 scale_factor = np.linalg.norm(t_colmap) / (1e-9 + np.linalg.norm(t_est))
 t_est_scaled = t_est * scale_factor
 # Then compute error:
 err_m = np.linalg.norm(t_colmap - t_est_scaled)
 ```

But again, if it’s the **same camera/scene** in a calibrated setup, you typically do **not** need this.

---

## **4) Calculate mAA for Multiple Thresholds**

The question’s metric thresholds might look like:

```python
thresholds_r = np.linspace(1, 10, 10)    # e.g. 1 -> 10 deg
thresholds_t = np.geomspace(0.2, 5, 10) # e.g. 0.2 -> 5 m
```

We define:

```python
def compute_mAA(rot_errors_deg, trans_errors_m, thresholds_r, thresholds_t):
    """
    Compute mean Average Accuracy (mAA) given rotation & translation errors
    at multiple threshold pairs.

    - rot_errors_deg: [rot_err_1, rot_err_2, ...]
    - trans_errors_m: [trans_err_1, trans_err_2, ...]
    - thresholds_r:   e.g. [1,2,3,...,10] (deg)
    - thresholds_t:   e.g. [0.2, 0.35, 0.6, 1, 2, 5] (meters)

    Return:
        float: average of the fraction of pairs that pass each threshold pair
    """
    assert len(rot_errors_deg) == len(trans_errors_m), "Mismatch in array lengths"
    n_pairs = len(rot_errors_deg)
    if n_pairs == 0:
        return 0.0

    accuracies = []
    for r_thresh, t_thresh in zip(thresholds_r, thresholds_t):
        n_accurate = 0
        for r_err, t_err in zip(rot_errors_deg, trans_errors_m):
            if (r_err < r_thresh) and (t_err < t_thresh):
                n_accurate += 1
        # fraction of pairs that pass this threshold
        acc = n_accurate / n_pairs
        accuracies.append(acc)

    # The final mAA is the average accuracy across threshold pairs
    return np.mean(accuracies)
```

### **Example Usage**

```python
if __name__ == "__main__":
    # Ground truth (from COLMAP)
    R01_colmap = ...
    t01_colmap = ...

    # Estimated
    R01_est = ...
    t01_est = ...

    # (A) If needed, scale t01_est to match norm(t01_colmap):
    # scale_factor = np.linalg.norm(t01_colmap) / (1e-9 + np.linalg.norm(t01_est))
    # t01_est *= scale_factor

    # 1) rotation error
    rot_err = rotation_error_deg(R01_colmap, R01_est)
    # 2) translation error
    trans_err = translation_error_m(t01_colmap, t01_est)

    # In a multi-pair scenario, store in lists
    rot_errs = [rot_err]
    trans_errs = [trans_err]

    # 3) mAA
    thresholds_r = np.linspace(1, 10, 10)
    thresholds_t = np.geomspace(0.2, 5, 10)
    my_mAA = compute_mAA(rot_errs, trans_errs, thresholds_r, thresholds_t)

    print(f"Rotation error (deg): {rot_err:.3f}")
    print(f"Translation error (m): {trans_err:.3f}")
    print(f"mAA: {my_mAA:.3f}")
```

---

### **Summary**

- If **both** ground truth and estimated poses are in **the same scale** (same camera, same scene, no uncalibrated steps), **no extra normalization** is required.
- **If scale is ambiguous** (e.g., pure 2-view geometry or uncalibrated pipeline), consider **matching the norms** or ignoring scale.
- With these three functions—**rotation_error_deg**, **translation_error_m**, and **compute_mAA**—you replicate the question’s metric.