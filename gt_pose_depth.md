# Master's Lecture: Ground-Truth Match Generation from Pose and Depth

Also see this conversation with the ChatGPT [link](https://chatgpt.com/share/691cb083-81d8-8013-b8a9-e74137f1b738)
And this [video](https://www.youtube.com/watch?v=LBFiKtUBHc0)

---
## 📚 **The Big Picture**

**Goal:** Given two images with keypoints, camera poses, and depth maps, determine:

- ✅ **Positive matches**: "These keypoints correspond!"
- ❌ **Negative matches**: "These keypoints definitely don't match!"
- ⚠️ **Ignored points**: "We can't tell, skip these in training"

**Why?** To train a neural network (like LightGlue) to learn what makes good feature correspondences.

---
### **Step 2: Sample Depth at Keypoints**
```python
d0, valid0 = sample_depth(kp0, depth0)
d1, valid1 = sample_depth(kp1, depth1)
```
**Colloquially:** "For each keypoint, look up its depth value from the sparse depth map. Mark keypoints as 'valid' if they have depth, 'invalid' if the depth map is empty there."

---
### **Step 3: Reproject Keypoints to Other View**
```python
kp0_1, visible0 = project(kp0, d0, depth1, camera0, camera1, T_0to1, valid0, ccth=cc_th)
kp1_0, visible1 = project(kp1, d1, depth0, camera1, camera0, T_1to0, valid1, ccth=cc_th)
```
**Colloquially:** "Take each keypoint in image 0, convert it to 3D using its depth, transform it to camera 1's coordinate system, and project it back to 2D. Check if it's still visible (not behind the camera, inside image bounds). Do the same in reverse for image 1 → image 0."

---
### **Step 4: Build Pairwise Distance Matrix**
```python
dist0 = torch.sum((kp0_1.unsqueeze(-2) - kp1.unsqueeze(-3)) ** 2, -1)
dist1 = torch.sum((kp0.unsqueeze(-2) - kp1_0.unsqueeze(-3)) ** 2, -1)
dist = torch.max(dist0, dist1)
dist = torch.where(mask_visible, dist, inf)
```

**Colloquially:** "For EVERY combination of keypoint i in image 0 and keypoint j in image 1, compute how far apart they are when reprojected. Use the MAXIMUM of forward and backward errors (symmetric distance). If either point is invisible, set distance to infinity."

**Pen & Paper:**
Draw a grid (matrix):
```
kp1[0] kp1[1] kp1[2]
kp0[0] 50 6100 20000
kp0[1] 4100 60 5000
kp0[2] inf inf inf (invisible)
```
Each cell = "How far is the reprojected kp0[i] from kp1[j]?"
**The key insight:** Small distances = likely correspondences!
### **Step 5: Find Nearest Neighbors**

```python
min0 = dist.min(-1).indices # For each kp0, find closest kp1
min1 = dist.min(-2).indices # For each kp1, find closest kp0
```

**Colloquially:** "For each keypoint in image 0, find its closest buddy in image 1. Then do the reverse: for each keypoint in image 1, find its closest buddy in image 0."
**Pen & Paper:**
From the matrix above:
- Row 0 minimum is at column 0 (dist=50) → `min0[0] = 0`
- Row 1 minimum is at column 1 (dist=60) → `min0[1] = 1`
- Column 0 minimum is at row 0 → `min1[0] = 0`
- Column 1 minimum is at row 1 → `min1[1] = 1`
Draw arrows: kp0[0] ↔ kp1[0] and kp0[1] ↔ kp1[1]
  
---
### **Step 6: Mutual Nearest Neighbor Check**
```python

ismin0.scatter_(-1, min0.unsqueeze(-1), value=1)
ismin1.scatter_(-2, min1.unsqueeze(-2), value=1)
positive = ismin0 & ismin1 & (dist < pos_th**2)
```

**Colloquially:** "A match is only valid if BOTH agree: kp0[i] thinks kp1[j] is its best match AND kp1[j] thinks kp0[i] is its best match. Also, the distance must be small (< 3 pixels by default)."

**Pen & Paper:**
Create two boolean matrices:
```
ismin0: ismin1:
[1, 0, 0] [1, 0, 0]
[0, 1, 0] [0, 1, 0]
```

AND them together:
```
positive = ismin0 & ismin1 & (dist < 9)
= [1, 0, 0] (only if dist[0,0] < 9)
[0, 1, 0] (only if dist[1,1] < 9)
```
Circle the cells that are `1` → these are **positive matches**!

---
### **Step 7: Identify Hard Negatives**

```python
negative0 = (dist0.min(-1).values > neg_th**2) & valid0
negative1 = (dist1.min(-2).values > neg_th**2) & valid1
```
**Colloquially:** "If a keypoint's closest match is FAR AWAY (> 5 pixels), it's a clear negative. But only label it if the keypoint has valid depth (otherwise we can't trust the reprojection)."
**Pen & Paper:**
Look at row 0 minimum: 50 pixels
- Is 50 > 25 (neg_th²)? **YES** → kp0[0] is a hard negative
- Does kp0[0] have valid depth? **YES** → Mark as **UNMATCHED (-1)**
  
These are valuable for training: "Teach the network that these DON'T match!"

---
### **Step 8: Assign Labels**

```python
m0 = torch.where(positive.any(-1), min0, ignore)
m1 = torch.where(positive.any(-2), min1, ignore)
m0 = torch.where(negative0, unmatched, m0)
m1 = torch.where(negative1, unmatched, m1)
```

**Colloquially:** "For each keypoint, assign a label:
- If it has ANY positive match → store the match index (0, 1, 2, ...)
- Else if it's a hard negative → label as **UNMATCHED (-1)**
- Else → label as **IGNORE (-2)** (can't determine, skip in training)"
  
**Pen & Paper:**
Create a list for image 0:

```
kp0[0]: has positive match at kp1[0] → m0[0] = 0
kp0[1]: no positive, but negative → m0[1] = -1 (UNMATCHED)
kp0[2]: no depth, can't tell → m0[2] = -2 (IGNORE)
```

---
### **Step 9: Epipolar Geometry Refinement (Optional)**

```python
F = K1^-T @ E @ K0^-1
epi_dist = sym_epipolar_distance_all(kp0, kp1, F)

```

**Colloquially:** "Use the **fundamental matrix** (encodes the geometric relationship between cameras) to compute epipolar distances. This is a fallback for points without depth: if a point is FAR from where geometry says it should be, mark it as UNMATCHED."
**Pen & Paper:**
1. Draw image 0 with a keypoint
2. Draw image 1 with an **epipolar line** (where that keypoint MUST appear geometrically)
3. If detected keypoints in image 1 are far from this line → they can't match!
4. Label them as UNMATCHED even without depth

**The magic:** Epipolar geometry works even when depth is missing (sparse maps)!

---
### **Step 10: Refine IGNORE → UNMATCHED**

```python
if epi_th is not None:
exclude0 = epi_dist.min(-1).values > neg_th
m0 = torch.where((~valid0) & exclude0, -1, m0)
```

**Colloquially:** "For points we previously IGNORED (no depth), check if they're also far from the epipolar line. If yes, downgrade them from IGNORE to UNMATCHED. Now they're useful negatives for training!"
 
**Pen & Paper:**
```
Before: kp0[2] = -2 (IGNORE, no depth)
	Check epipolar: kp0[2] is 10 pixels from epipolar line (> neg_th=5)
After: kp0[2] = -1 (UNMATCHED, useful negative!)
```

---
## **Step 11: Package Results**

```python
return {
"assignment": positive, # [B, N, M] boolean matrix
"matches0": m0, # [B, N] indices or labels
"matches1": m1, # [B, M] indices or labels
"matching_scores0": (m0 > -1).float(), # 1.0 if matched/unmatched, 0.0 if ignored
...
}
```

**Colloquially:** "Bundle everything into a dictionary for the training loop. Include the binary match matrix, labeled indices, scores, depths, reprojections, and visibility masks."

**Pen & Paper:**
Draw a summary table:
```
kp0[0]: Match to kp1[0], score=1.0, depth=2.5m ✓
kp0[1]: UNMATCHED, score=1.0, depth=3.1m ✗
kp0[2]: IGNORE, score=0.0, no depth ⚠️

```
---
