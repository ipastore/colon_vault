# Notes

So, it was huge success. However, not without drawbacks. The main drawback is that training code was never released. That is why people tried independently re-implement SuperPoint, as well as present new, supposedly better versions of it.

There are two alternatives for the original SuperPoint
https://github.com/rpautrat/SuperPoint

https://github.com/eric-yyjau/pytorch-superpoint

Source:
https://ducha-aiki.github.io/wide-baseline-stereo-blog/posts/2023-01-04-unsuperpoint-family.html

# COPILOT: Step-by-Step Guide: Fine-tuning 

## Adapting to fish eye

``` python
import cv2
import numpy as np

def undistort_fisheye(img_fish, K, D, balance=0.0, dim2=None, dim3=None):
    # img_fish: input fish-eye image
    # K, D: calibration parameters
    DIM1 = img_fish.shape[:2][::-1]  # (width, height)
    if not dim2:
        dim2 = DIM1
    if not dim3:
        dim3 = DIM1
    new_K = cv2.fisheye.estimateNewCameraMatrixForUndistortRectify(
        K, D, DIM1, np.eye(3), balance=balance)
    map1, map2 = cv2.fisheye.initUndistortRectifyMap(
        K, D, np.eye(3), new_K, dim3, cv2.CV_16SC2)
    undistorted = cv2.remap(img_fish, map1, map2, interpolation=cv2.INTER_LINEAR, borderMode=cv2.BORDER_CONSTANT)
    return undistorted, new_K

def distort_to_fisheye(img_rect, K, D, orig_shape):
    # Back-project rectilinear image to fish-eye domain
    # This is a bit more advanced; you need to map each pixel in the output fish-eye image
    # to its corresponding location in the rectilinear (undistorted) image using cv2.fisheye.projectPoints
    # For small batches, you can use cv2.remap with reverse maps
    h, w = orig_shape[:2]
    map1, map2 = cv2.fisheye.initUndistortRectifyMap(
        K, D, np.eye(3), K, (w, h), cv2.CV_16SC2)
    # Now invert the mapping: for each pixel in the fish-eye, find where it comes from in the rectilinear
    # You can use cv2.remap with the inverted map
    fisheye_warped = cv2.remap(img_rect, map1, map2, interpolation=cv2.INTER_LINEAR, borderMode=cv2.BORDER_CONSTANT)
    return fisheye_warped

def fish_eye_homographic_adaptation(img_fish, K, D, **homography_params):
    # 1. Undistort
    img_rect, new_K = undistort_fisheye(img_fish, K, D)
    # 2. Apply planar homography (in rectilinear space)
    H = sample_homography(img_rect.shape, **homography_params)
    img_rect_warp = cv2.warpPerspective(img_rect, H, (img_rect.shape[1], img_rect.shape[0]))
    # 3. Redistort back
    img_fish_warp = distort_to_fisheye(img_rect_warp, K, D, img_fish.shape)
    return img_fish, img_fish_warp, H
    ```

Hi [@YanShuo1992](https://github.com/YanShuo1992) ,

Thanks for your message.  
To generate keypoints for training, I suggest directly using the pre-trained network for homography adaptation ([https://github.com/eric-yyjau/pytorch-superpoint/tree/master/logs/magicpoint_synth20/checkpoints](https://github.com/eric-yyjau/pytorch-superpoint/tree/master/logs/magicpoint_synth20/checkpoints)).  
For the parameters, you may refer to this one ([https://github.com/eric-yyjau/pytorch-superpoint/blob/master/logs/superpoint_coco_heat2_0/config.yml](https://github.com/eric-yyjau/pytorch-superpoint/blob/master/logs/superpoint_coco_heat2_0/config.yml))

I didn't change the learning rate. Yeah, image size should be 240x320.  
Best,

https://github.com/eric-yyjau/pytorch-superpoint/issues/40

 SuperPoint was trained on images 240x320. I recommend training on a similar size if possible
 https://github.com/rpautrat/SuperPoint/issues/313

# More notes (conclusions)

Start from weihgts of MSCOCO
FIne-tune the weights with HA in Colonoscopies

Note that MagicPoint and SuperPoint only work on images with dimensions divisible by 8 and the user is responsible for resizing them to a valid dimension.

In rpautat: 
- The config file reflects that all homographic adaptation is done before training, not dynamically during each epoch or batch.
  
  Primero se hace HA y luego se entran. El not implemented de comentario está bien, indica que es offline, no lo hace online el labeling con HA

For large changes of illumination

``` yaml
photometric:
    enable: true
    primitives: [
        'random_brightness', 'random_contrast', 'additive_speckle_noise',
        'additive_gaussian_noise', 'additive_shade', 'motion_blur'
    ]
    params:
        random_brightness: {max_abs_change: 80}  # try up to 80 or 100
        random_contrast: {strength_range: [0.2, 2.5]}  # wider range
        additive_gaussian_noise: {stddev_range: [0, 25]}
        additive_speckle_noise: {prob_range: [0, 0.01]}
        additive_shade:
            transparency_range: [-0.8, 0.8]
            kernel_size_range: [80, 200]
        motion_blur: {max_kernel_size: 7}
```

| Approach                              | Speed      | Quality | Engineering effort |
| ------------------------------------- | ---------- | ------- | ------------------ |
| Planar Homography (original)          | Fast (GPU) | Good    | Low                |
| Fisheye via OpenCV (on-the-fly)       | Slow (CPU) | Best    | Medium             |
| Fisheye with precomputed/cached warps | Medium     | Best    | Medium             |
| Fisheye with custom GPU remap kernel  | Fast (GPU) | Best    | High               |

### **In short:**

- **The labels after HA are 2D heatmaps, not just keypoint coordinates.**
- **You need to remap (warp) these heatmaps to the distorted domain for training on distorted images.**

The output of the training for genererating the labels is a heatmap. I should 

## For distorting the heatmap(labels)

``` python
import numpy as np
import cv2
import matplotlib.pyplot as plt

def warp_heatmap_to_distorted(undistorted_heatmap, K, D, original_shape):
    """
    Warps a heatmap from undistorted to distorted image domain.

    Args:
        undistorted_heatmap: 2D numpy array (H', W') from undistorted image.
        K: Camera matrix for the distorted image (3x3).
        D: Distortion coefficients for the distorted image (1x4 or 1x5).
        original_shape: (H, W) of the distorted image.

    Returns:
        warped_heatmap: 2D numpy array (H, W), corresponding to the distorted domain.
    """
    H, W = original_shape
    map1, map2 = cv2.initUndistortRectifyMap(
        cameraMatrix=K, distCoeffs=D, R=np.eye(3), newCameraMatrix=K,
        size=(W, H), m1type=cv2.CV_32FC1
    )
    # Inverse mapping: for each pixel in distorted image, find where it came from in undistorted
    # So we need to invert the undistortion map
    # We'll create a grid of distorted image coords, undistort them, and sample from the undistorted heatmap

    # Generate pixel grid in distorted image
    grid_x, grid_y = np.meshgrid(np.arange(W), np.arange(H))
    distorted_points = np.stack([grid_x, grid_y], axis=-1).reshape(-1, 1, 2).astype(np.float32)

    # Map them to undistorted normalized coordinates
    undistorted_points = cv2.undistortPoints(distorted_points, K, D, P=K).reshape(H, W, 2)

    # Use the undistorted points to sample from the undistorted heatmap
    map_x = undistorted_points[..., 0]
    map_y = undistorted_points[..., 1]
    warped_heatmap = cv2.remap(
        undistorted_heatmap.astype(np.float32), map_x, map_y, interpolation=cv2.INTER_LINEAR, borderMode=cv2.BORDER_CONSTANT, borderValue=0
    )
    return warped_heatmap

def visualize_heatmap_and_keypoints(undistorted_heatmap, warped_heatmap, K, D, keypoints_undistorted):
    """
    Visualizes the undistorted and distorted heatmaps, and overlays keypoints.

    Args:
        undistorted_heatmap: 2D numpy array (H', W')
        warped_heatmap: 2D numpy array (H, W)
        K, D: Camera matrix and distortion coefficients
        keypoints_undistorted: Nx2 array of keypoints in undistorted domain
    """
    # Map undistorted keypoints to distorted image
    pts = np.array(keypoints_undistorted, dtype=np.float32).reshape(-1, 1, 2)
    pts_distorted = cv2.projectPoints(pts, np.zeros(3), np.zeros(3), K, D)[0].reshape(-1, 2)

    plt.figure(figsize=(12, 6))
    plt.subplot(1, 2, 1)
    plt.title("Undistorted Heatmap")
    plt.imshow(undistorted_heatmap, cmap='hot')
    if keypoints_undistorted is not None:
        plt.scatter(keypoints_undistorted[:, 0], keypoints_undistorted[:, 1], c='cyan', s=10, label='Undistorted keypoints')
    plt.legend()

    plt.subplot(1, 2, 2)
    plt.title("Heatmap warped to Distorted Domain")
    plt.imshow(warped_heatmap, cmap='hot')
    if keypoints_undistorted is not None:
        plt.scatter(pts_distorted[:, 0], pts_distorted[:, 1], c='lime', s=10, label='Distorted keypoints')
    plt.legend()
    plt.show()

### === Example Usage ===

### Suppose you have the following (replace with your values):
K = np.array([[300, 0, 320],
              [0, 300, 240],
              [0,   0,   1]], dtype=np.float32)  # Example camera intrinsic matrix
D = np.array([-0.2, 0.03, 0, 0], dtype=np.float32)  # Example distortion coefficients
original_shape = (480, 640)  # Original distorted image size

### Simulate an undistorted heatmap and some keypoints
undistorted_heatmap = np.zeros((480, 640), dtype=np.float32)
keypoints_undistorted = np.array([[200, 150], [400, 350], [300, 220]])  # Example keypoints
for x, y in keypoints_undistorted:
    cv2.circle(undistorted_heatmap, (int(x), int(y)), 5, 1, -1)

### Warp the heatmap to distorted domain
warped_heatmap = warp_heatmap_to_distorted(undistorted_heatmap, K, D, original_shape)

### Visualize
visualize_heatmap_and_keypoints(undistorted_heatmap, warped_heatmap, K, D, keypoints_undistorted)
```

## For undistorting images offline

``` python

import os
import cv2
import numpy as np

def undistort_images(
    input_dir,
    output_dir,
    K,
    D,
    new_K=None,
    crop=False,
    ext_list=[".jpg", ".jpeg", ".png", ".bmp"]
):
    """
    Undistorts all images in input_dir and saves them to output_dir.

    Args:
        input_dir (str): Directory with distorted images.
        output_dir (str): Directory to save undistorted images.
        K (np.ndarray): Camera matrix (3x3).
        D (np.ndarray): Distortion coefficients (1x4 or 1x5).
        new_K (np.ndarray or None): New camera matrix (if None, uses K).
        crop (bool): If True, crops to remove black borders.
        ext_list (list): File extensions to process.
    """
    os.makedirs(output_dir, exist_ok=True)
    files = [f for f in os.listdir(input_dir) if os.path.splitext(f)[1].lower() in ext_list]

    for fname in files:
        img_path = os.path.join(input_dir, fname)
        img = cv2.imread(img_path)
        if img is None:
            print(f"Warning: failed to load {img_path}")
            continue
        h, w = img.shape[:2]
        if new_K is None:
            new_K, roi = cv2.getOptimalNewCameraMatrix(K, D, (w, h), 1, (w, h))
        undistorted = cv2.undistort(img, K, D, None, new_K)
        if crop:
            x, y, w_c, h_c = roi
            undistorted = undistorted[y:y+h_c, x:x+w_c]
        out_path = os.path.join(output_dir, fname)
        cv2.imwrite(out_path, undistorted)
        print(f"Saved: {out_path}")

if __name__ == "__main__":
    # Example usage:
    # Define your intrinsics and distortion coefficients (replace with your actual values)
    K = np.array([[300, 0, 320],
                  [0, 300, 240],
                  [0,   0,   1]], dtype=np.float32)
    D = np.array([-0.2, 0.03, 0, 0], dtype=np.float32)  # or shape (5,)

    input_dir = "path/to/input_images"
    output_dir = "path/to/undistorted_images"
    undistort_images(input_dir, output_dir, K, D)
    ```

hese transformations are composed together with an initial root center crop to help avoid bordering artifacts. This process is shown in Figure 6.

Source: SuperPoint papaer. Should I crop the images?


"On a held-out set of images from MS-COCO, we obtain a repeatability score of .67 without any Homographic Adaptation, a repeatability boost of 21% when performing Nh = 100 transforms, and a repeatability boost of 22% when Nh = 1000, thus the added benefit of using more than 100 homographies is minimal. For a more detailed analysis and discussion of this experiment see Appendix C."

- Number of homographies: 100 

"The images are sized to a resolution of 240 × 320 and converted to grayscale."

- Should resize and covert

"The labels are generated using Homographic Adaptation with Nh = 100, as motivated by our results from Section 5.2. We repeat the Homographic Adaptation a second time, using the resulting model trained from the first round of Homographic Adaptation."

- To generate labels repeat two HA with nH=100

"The joint training of SuperPoint is also done on 240×320 grayscale COCO images."

"The joint training of SuperPoint is also done on 240×320 grayscale COCO images. For each training example, a homography is randomly sampled. It is sampled from a more restrictive set of homographies than during Homographic Adaptation to better model the target application of pairwise matching (e.g., we avoid sampling extreme in-plane rotations as they are rarely seen in HPatches). The image and corresponding pseudo-ground truth are transformed by the homography to create the needed inputs and labels. The descriptor size used in all experiments is D = 256. We use a weighting term of λd = 250 to keep the descriptor learning balanced. The descriptor hinge loss uses a positive margin mp = 1 and negative margin mn = 0.2. We use a factor of λ = 0.0001 to balance the two losses"

"All training is done using PyTorch [19] with mini-batch sizes of 32 and the ADAM solver with default parameters of lr = 0.001 and β = (0.9, 0.999). We also use standard data augmentation techniques such as random Gaussian noise, motion blur, brightness level changes to improve the network’s robustness to lighting and viewpoint changes."

"Applying larger amounts of NMS helps ensure that the points are evenly distributed in the image, useful for certain applications such as ORBSLAM [17],"

- Increase NMS

"All training is done using PyTorch [19] with mini-batch sizes of 32 and the ADAM solver with default parameters of lr = 0.001 and β = (0.9, 0.999). We also use standard data augmentation techniques such as random Gaussian noise, motion blur, brightness level changes to improve the network’s robustness to lighting and viewpoint changes."


http://github.com/LeonBP/SuperPointEndoscopy
Paper de UNIZAR para entrenar superpoint


# Prompt to generate plan
So from now on in this chat I want to focus to make an exhaustive prompt in .txt (give me a codeblcok to copy and paste) to feed another LLM with may plan. I want to include the following steps, and if you imagine some other steps, you can also add them.

Objetective: 
Use rpautrat(tensorflow) repo to fine-tune SuperPoint starting with the weights having been trained in MSCOCO already. My dataset are colonoscopy images with fish-eye distortion whre I also should apply some masks to avoid the black edges and specularities (see images attached).

I want the following features: 

- Run the training as it is, without masks and without fish eye distortion. Just feed the images and see.
- Visualization tool: I want to see the output images of the pseudo label truth. To Check if the first option is enough (without masks and distortion). 
  - Optionally add distortion model: Where I think I should (offline) undistort the image apply the random homographies, and then remap the heatmap to the original distort image. I would also like to apply the visualization of the labels in this case. 
  - Optionally pass a mask for image where I will no consider those keypoints. For example for specularities or edges in the black part. I think that for this I should filter the keypoints detected of the base detector and then the loss to ingore invalid regions. Only use descriptor pairs where **both locations are in the valid region** (e.g., no black corners or specular blobs).




Steps: 

0. Prepare dataset.
   rename files with parent folder and saved into the unfiltered folder. 
   Make a script to filter the images for: when the camera is facing directly the wall of the colon with the light and everything is red and there is low texture. Also when the physician takes a screenshot and it´s seen outside the mask (low left corner)
1. Generate mask with script that I already have in Python. 

2. FIlter the keypoints for label and the descriptor loss with the mask
3. Add the feature to distort
4. Make the visualization tool

RODRIGUEZ: 

- Utilizar 500 imágenes para testear. 
- Utilizar el correspondence graph de COLMAP para elegir las imágenes como primer filtro y ya está.
- Revisar si las proyecciones son pinhole: NO. Todos los trabajos utilizan una homografía con pinhole. 
- Me dio la opción de recortar la imagen. ESTO LO APLICAN CON EL PARAMETRO patch_ratio.
- Escribir a Monti y Morlana, en copia a Ruben y a Rodriguez consultando:
  
  "- SuperPoint trabaja con homografias. ¿Hay que desdistorsionar la imágen para para entrenar superpoint?
	-Tanto NR SLAM como CUDASIFT SLAM sacan los features en la imagen distorsionada. Deberiamos seguir este plan?"
	"Las funciones de reproyeccion en kanala las tenemos (Se utilizan en LightDepth). Quiza esto sea suficiente?"
  
  
  
  