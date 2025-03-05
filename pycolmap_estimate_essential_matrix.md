### **How COLMAP Handles Fisheye Distortion for Relative Pose Estimation**

From the **C++ implementation of COLMAP's OpenCV Fisheye Camera Model and Essential Matrix Estimation**, we can now confirm **exactly how COLMAP handles fisheye distortion when computing the essential matrix**.

---

## **Key Takeaways from the COLMAP Source Code**

1. **COLMAP DOES NOT Undistort Keypoints Explicitly Before Essential Matrix Estimation**
    
    - Instead, COLMAP **works with distorted keypoints** in image space and **applies the fisheye model directly within the solver**.
    - The function `Camera::CamFromImg()` converts **image-space coordinates into normalized camera coordinates**, but **keeps fisheye distortion in mind**.
    - This is done in **PyEstimateAndDecomposeEssentialMatrix()**, where each keypoint is converted using:
        
        ```cpp
        if (const std::optional<Eigen::Vector2d> cam_point1 =
                camera1.CamFromImg(points2D1[point2D_idx]);
        ```
        
        This function **does NOT undistort the point**, it just **maps it to the camera model**.
2. **The Essential Matrix Estimation Happens in Fisheye Normalized Space**
    
    - The function `ImgFromCam()`:
        
        ```cpp
        bool OpenCVFisheyeCameraModel::ImgFromCam(
            const T* params, T u, T v, T w, T* x, T* y)
        ```
        
        - First, it **applies the fisheye projection model**.
        - Then it **applies distortion** via `Distortion(&params[4], uu, vv, &duu, &dvv);`.
        - Then it **maps it back to image coordinates**.
    - The reverse transformation happens in `CamFromImg()`:
        
        ```cpp
        bool OpenCVFisheyeCameraModel::CamFromImg(
            const double* params, double x, double y, double* u, double* v)
        ```
        
        - It **maps distorted image coordinates back to normalized space**.
        - Uses `IterativeUndistortion()` to refine the mapping.
3. **LO-RANSAC is Applied to Filter Inliers**
    
    - In `PyEstimateAndDecomposeEssentialMatrix()`, after converting image-space keypoints into **camera-space coordinates**, COLMAP runs **LO-RANSAC**:
        
        ```cpp
        LORANSAC<EssentialMatrixFivePointEstimator, EssentialMatrixFivePointEstimator>
            ransac(ransac_options);
        const auto report = ransac.Estimate(cam_points1, cam_points2);
        ```
        
    - This means: ✅ **COLMAP internally filters out outlier matches before computing EE**.  
        ✅ **You do NOT need to filter matches manually before calling `estimate_essential_matrix()`**.
4. **Pose Decomposition Uses a Cheirality Check**
    
    - If RANSAC succeeds, the function **extracts the correct (R, t) pair** using:
        
        ```cpp
        PoseFromEssentialMatrix(report.model,
                                inlier_cam_points1,
                                inlier_cam_points2,
                                &cam2_from_cam1,
                                &inlier_points3D);
        ```
        
    - This ensures that the **correct transformation** is chosen (eliminating the ambiguity in the essential matrix decomposition).

---

## **What This Means for Your Custom Matcher Integration**

### ✅ **You should provide the raw, distorted keypoints to `estimate_essential_matrix()`**

- **Do not manually undistort the keypoints before calling `estimate_essential_matrix()`**.
- **COLMAP already accounts for fisheye distortion inside `CamFromImg()`** before estimating EE.

### ✅ **You do NOT need to filter matches beforehand**

- COLMAP applies **LO-RANSAC internally**, so it will automatically filter outliers before computing EE.

### ✅ **Your Correct Approach**

```python
result = pycolmap.estimate_essential_matrix(
    points2D1=mkpts0,  # Raw distorted keypoints
    points2D2=mkpts1,  # Raw distorted keypoints
    camera1=camera1,
    camera2=camera2
)
```

---

## **Final Answer to Your Questions**

### **1) Does `estimate_essential_matrix()` take fisheye distortion into account?**

✅ **Yes, COLMAP automatically handles fisheye distortion inside the camera model.**

- It **does not undistort keypoints**, but instead **computes epipolar constraints in distorted space using the fisheye projection model**.

### **2) Does `estimate_essential_matrix()` use inliers or just matches?**

✅ **It uses inliers, not raw matches.**

- It applies **LO-RANSAC** to remove incorrect matches before estimating EE.
- You **do not need to pre-filter your matches**—COLMAP will handle it internally.

🚀 **Now you can confidently compare your deep learning matcher against COLMAP's pose estimation!** 🚀