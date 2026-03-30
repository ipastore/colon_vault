# SuperPoint: Mathematical Foundation and Loss Functions

## 1. Introduction

SuperPoint is a self-supervised deep learning framework for joint interest point detection and description. This document provides a rigorous mathematical foundation for the SuperPoint architecture, training methodology, and loss functions based on the official implementation analysis.

## 2. Mathematical Notation and Definitions

### 2.1 Basic Definitions

Let $\mathcal{I} \in \mathbb{R}^{H \times W}$ denote a grayscale input image of height $H$ and width $W$.

**Homographic Transformation**: A homography $\mathbf{H} \in \mathbb{R}^{3 \times 3}$ is a projective transformation that maps points from one plane to another:
$$\mathbf{p}' = \mathbf{H}\mathbf{p}$$
where $\mathbf{p} = [x, y, 1]^T$ and $\mathbf{p}' = [x', y', 1]^T$ are homogeneous coordinates.

**Grid-based Detection**: SuperPoint divides the input image into a regular grid of cells with size $s = 8$ pixels. For an image of size $H \times W$, this creates a detection grid of size $\frac{H}{s} \times \frac{W}{s}$.

### 2.2 Network Architecture

The SuperPoint network $f_\theta$ consists of three main components:

1. **Shared Encoder**: $f_{enc}: \mathbb{R}^{H \times W} \rightarrow \mathbb{R}^{\frac{H}{s} \times \frac{W}{s} \times d}$
2. **Detector Head**: $f_{det}: \mathbb{R}^{\frac{H}{s} \times \frac{W}{s} \times d} \rightarrow \mathbb{R}^{\frac{H}{s} \times \frac{W}{s} \times (s^2 + 1)}$
3. **Descriptor Head**: $f_{desc}: \mathbb{R}^{\frac{H}{s} \times \frac{W}{s} \times d} \rightarrow \mathbb{R}^{H \times W \times D}$

where $d$ is the feature dimension and $D = 256$ is the descriptor dimension.

## 3. Detector Loss Function

### 3.1 Keypoint Representation

For each cell $(i,j)$ in the detection grid, the detector head outputs a probability distribution over $s^2 + 1$ possible locations (64 possible sub-pixel positions plus a "dustbin" channel for no keypoint).

Let $\mathbf{Y}^{det} \in \mathbb{R}^{\frac{H}{s} \times \frac{W}{s} \times (s^2 + 1)}$ be the detector output, where:
$$\mathbf{Y}^{det}_{i,j,k} = \text{probability of keypoint at position } k \text{ in cell } (i,j)$$

### 3.2 Ground Truth Encoding

Ground truth keypoints are encoded into the same grid structure. For a keypoint at pixel location $(x, y)$:
- Cell location: $(i, j) = (\lfloor y/s \rfloor, \lfloor x/s \rfloor)$
- Sub-pixel position: $k = (y \bmod s) \cdot s + (x \bmod s)$

Let $\mathbf{L}^{det} \in \mathbb{R}^{\frac{H}{s} \times \frac{W}{s}}$ be the ground truth labels, where $\mathbf{L}^{det}_{i,j} \in \{0, 1, ..., s^2\}$ indicates the true keypoint position in cell $(i,j)$ (with $s^2$ representing the dustbin).

### 3.3 Detector Loss Formulation

The detector loss is computed as a sparse softmax cross-entropy loss:

$$\mathcal{L}_{det} = -\frac{1}{|\mathcal{V}|} \sum_{(i,j) \in \mathcal{V}} \log\left(\frac{\exp(\mathbf{Y}^{det}_{i,j,\mathbf{L}^{det}_{i,j}})}{\sum_{k=0}^{s^2} \exp(\mathbf{Y}^{det}_{i,j,k})}\right)$$

where $\mathcal{V}$ represents the set of valid cells (excluding border regions where transformations might be undefined).

## 4. Descriptor Loss Function

### 4.1 Descriptor Representation

The descriptor head produces dense descriptors $\mathbf{D} \in \mathbb{R}^{H \times W \times D}$ that are L2-normalized:
$$\mathbf{D}_{x,y} = \frac{\mathbf{D}_{x,y}}{||\mathbf{D}_{x,y}||_2}$$

For detected keypoints $\mathcal{P} = \{(x_1, y_1), ..., (x_N, y_N)\}$, we extract descriptors $\mathbf{d}_i = \mathbf{D}_{x_i, y_i} \in \mathbb{R}^D$.

### 4.2 Correspondence Establishment

Given a homography $\mathbf{H}$ between images $\mathcal{I}$ and $\mathcal{I}'$, correspondences are established as follows:

For a keypoint $\mathbf{p}_i = (x_i, y_i)$ in $\mathcal{I}$, its corresponding location in $\mathcal{I}'$ is:
$$\mathbf{p}'_i = \mathbf{H}\mathbf{p}_i$$

A correspondence is valid if:
1. $\mathbf{p}'_i$ lies within the image boundaries of $\mathcal{I}'$
2. Both $\mathbf{p}_i$ and $\mathbf{p}'_i$ are detected as keypoints

### 4.3 Descriptor Similarity

The similarity between descriptors is computed using the dot product:
$$s_{i,j} = \mathbf{d}_i^T \mathbf{d}'_j$$

where $\mathbf{d}_i$ and $\mathbf{d}'_j$ are L2-normalized descriptors from images $\mathcal{I}$ and $\mathcal{I}'$ respectively.

### 4.4 Descriptor Loss Formulation

The descriptor loss encourages high similarity for corresponding keypoints and low similarity for non-corresponding ones:

$$\mathcal{L}_{desc} = \frac{1}{|\mathcal{C}|} \sum_{(i,j) \in \mathcal{C}} L_{pos}(s_{i,j}) + \frac{\lambda_d}{|\mathcal{N}|} \sum_{(i,k) \in \mathcal{N}} L_{neg}(s_{i,k})$$

where:
- $\mathcal{C}$ is the set of positive pairs (corresponding keypoints)
- $\mathcal{N}$ is the set of negative pairs (non-corresponding keypoints)
- $\lambda_d = 250$ is the negative margin weight

The positive and negative loss terms are defined as:
$$L_{pos}(s) = \max(0, m_p - s)$$
$$L_{neg}(s) = \max(0, s - m_n)$$

where $m_p = 1$ is the positive margin and $m_n = 0.2$ is the negative margin.

## 5. Complete Training Objective

### 5.1 Self-Supervised Training Setup

SuperPoint is trained using pairs of images $(\mathcal{I}, \mathcal{I}')$ where $\mathcal{I}' = \mathcal{H}(\mathcal{I})$ is a homographically warped version of $\mathcal{I}$.

The complete training objective combines both losses:

$$\mathcal{L}_{total} = \mathcal{L}_{det} + \lambda_{desc} \cdot \mathcal{L}_{desc}$$

where $\lambda_{desc} = 0.05$ balances the detector and descriptor losses.

### 5.2 Multi-Scale Training Objective

During training, the total loss is scaled by $\lambda_{loss} = 10000$ for numerical stability:

$$\mathcal{L}_{final} = \lambda_{loss} \cdot \mathcal{L}_{total}$$

### 5.3 Optimization

The network parameters $\theta$ are optimized using the Adam optimizer:

$$\theta^{(t+1)} = \theta^{(t)} - \alpha \nabla_\theta \mathcal{L}_{final}$$

where $\alpha = 0.0001$ is the learning rate.

## 6. Implementation Details

### 6.1 Homographic Adaptation

During inference and pseudo-label generation, homographic adaptation is applied:

$$\hat{\mathcal{L}}_{det} = \frac{1}{N_{homo}} \sum_{k=1}^{N_{homo}} f_{det}(\mathcal{H}_k(\mathcal{I}))$$

where $N_{homo}$ is the number of random homographies applied (typically 100 for pseudo-label generation).

### 6.2 Non-Maximum Suppression

Final keypoint detection applies NMS with radius $r_{nms} = 4$ pixels:

$$\mathcal{P}_{final} = \text{NMS}(\mathcal{P}, r_{nms})$$

### 6.3 Keypoint Filtering

Only the top-K keypoints (typically $K = 1000$) are retained based on detection confidence:

$$\mathcal{P}_{top-K} = \text{TopK}(\mathcal{P}_{final}, K)$$

## 7. Mathematical Properties

### 7.1 Translation Invariance

The grid-based detection scheme provides translation invariance up to the grid resolution:
$$f_{det}(\mathcal{T}_{\mathbf{t}}(\mathcal{I})) \approx \mathcal{T}_{\mathbf{t}/s}(f_{det}(\mathcal{I}))$$

where $\mathcal{T}_{\mathbf{t}}$ represents translation by vector $\mathbf{t}$.

### 7.2 Descriptor Invariance

The descriptor loss encourages invariance to homographic transformations:
$$||\mathbf{d}_i - \mathbf{d}'_j||_2 \rightarrow 0 \text{ for corresponding points}$$

### 7.3 Convergence Properties

The combined loss function is bounded below and satisfies:
$$\mathcal{L}_{total} \geq 0$$

with equality achieved when all keypoints are correctly detected and all descriptors are perfectly matched.

## 8. Endoscopic SLAM Adaptations

For endoscopic applications, the following modifications are particularly relevant:

### 8.1 Specular Reflection Handling

The dustbin channel helps handle specular reflections common in endoscopic imagery:
$$P(\text{dustbin}) \uparrow \text{ for specular regions}$$

### 8.2 Fish-eye Distortion

The homographic adaptation can be extended to handle fish-eye distortions common in endoscopic cameras by incorporating distortion models in the transformation pipeline.

## 9. Conclusion

This mathematical foundation provides the theoretical basis for understanding SuperPoint's self-supervised learning approach. The combination of detector and descriptor losses, enabled by homographic adaptation, allows the network to learn robust feature detection and description without manual annotations.

The key mathematical insight is that geometric consistency (enforced through homographic correspondences) provides sufficient supervision signal for learning both detection and description tasks simultaneously.

---

**Key Parameters Summary:**
- Grid size: $s = 8$ pixels
- Descriptor dimension: $D = 256$
- Positive margin: $m_p = 1$
- Negative margin: $m_n = 0.2$
- Descriptor loss weight: $\lambda_{desc} = 0.05$
- Negative pair weight: $\lambda_d = 250$
- Learning rate: $\alpha = 0.0001$
- Loss scaling: $\lambda_{loss} = 10000$