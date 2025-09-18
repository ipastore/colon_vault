## Evaluation metric

Participants are asked to estimate the pose for each image in a set with 𝑁N images. Each camera pose is parameterized with a rotation matrix 𝐑R and a translation vector 𝐓T, from an arbitrary frame of reference.

Submissions are evaluated on the **mean Average Accuracy (mAA)** of the estimated poses. Given a set of cameras, parameterized by their rotation matrices and translation vectors, and the hidden ground truth, we compute the relative error in terms of rotation (𝜖𝑅ϵR, in degrees) and translation (𝜖𝑇ϵT, in meters) for every possible pair of images in 𝑁N, that is, (𝑁2)(N2) pairs.

We then threshold each of this poses by its accuracy in terms of both rotation, and translation. We do this over ten pairs of thresholds: e.g. at 1 degree and 20 cm at the finest level, and 10 degrees and 5 m at the coarsest level. The actual thresholds vary for each dataset, but they look like this:

```
thresholds_r = np.linspace(1, 10, 10)  # In degrees.
thresholds_t = np.geomspace(0.2, 5, 10)  # In meters.
```

We then calculate the percentage of accurate samples (pairs of poses) at every thresholding level, and average the results over all thresholds. This rewards more accurate poses. Note that while you submit 𝑁N, the metric will process all samples in (𝑁2)(N2).

Finally, we compute this metric separately for each scene and then average it to compute its **mAA**. These values are then averaged over datasets, which contain a variable number of scenes, to obtain the final **mAA** metric.