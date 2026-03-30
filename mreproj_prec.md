### Symmetric Reprojection Error (SRE)

The Symmetric Reprojection Error measures the geometric consistency between two sets of point correspondences using 3D information (depth) and relative camera poses. It quantifies how well a point in one image aligns with its correspondent in another image after being mapped through 3D space.

#### 1. The Geometric Concept
Given a pair of points $(p_0, p_1)$ and their respective depths $(d_0, d_1)$, we define the reprojection operations:
* $\hat{p}_1 = \pi_1(T_{0 \to 1} \cdot \pi_0^{-1}(p_0, d_0))$ : Point 0 projected into Image 1.
* $\hat{p}_0 = \pi_0(T_{1 \to 0} \cdot \pi_1^{-1}(p_1, d_1))$ : Point 1 projected into Image 0.

Where $\pi$ is the projection function and $\pi^{-1}$ is the "unprojection" (back-projection) to 3D space.

#### 2. Error Formulation
The error is defined as the average Euclidean distance between the observed pixel coordinates and the reprojected pixel coordinates in both views:

$$
\text{SRE} = \frac{1}{2} \left( \| \hat{p}_1 - p_1 \|_2 + \| \hat{p}_0 - p_0 \|_2 \right)
$$
$$
d_{\text{SRE}} = \frac{1}{2} \left( d_{0AB} + d_{0BA} \right)
$$

#### 3. Breakdown of the Implementation
* **Depth Sampling**: The function samples `depth0` and `depth1` at the provided pixel coordinates to determine the 3D location of each point.
* **Projection**: The `project()` function performs the coordinate transformation ($P_{cam1} = T_{0 \to 1} \cdot P_{cam0}$) and handles the camera intrinsics to return new pixel coordinates.
* **Symmetry**: By calculating the error in both directions ($0 \to 1$ and $1 \to 0$), the metric becomes robust to errors in a single depth map and ensures that the transformation is consistent from both perspectives.
* **Validity Mask**: The function returns a `valid` boolean mask, which is true only if the point was within the image bounds and had a valid depth in both views.
#### 4. Choice of Distance Metric (L2 Norm)
The function utilizes the **L2 Norm** (Euclidean distance) to calculate the error. For a displacement vector $\Delta p = (\Delta x, \Delta y)$ between a reprojected point and an observed point, the error is calculated as:

$$
\|\Delta p\|_2 = \sqrt{\Delta x^2 + \Delta y^2}
$$

This represents the direct "as-the-crow-flies" pixel distance. Using the L2 norm is standard for reprojection errors as it directly relates to the geometric displacement on the image sensor. In the provided code, this is implemented via `torch.norm(dim=-1)`, which computes the magnitude of the 2D error vector for every point pair.