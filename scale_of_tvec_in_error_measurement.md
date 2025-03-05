Below is a more **detailed explanation** of what it means to compare **pairwise relative poses** in a single submap, how **scales** can differ across pairs, and whether you need to **manually scale** your results.

---

## **1) COLMAP’s Scale within One Submap**

1. **Single Submap**: You mentioned you have a “submap” produced by COLMAP. Internally, COLMAP will pick a certain scale (often arbitrary) but **consistent** throughout that entire reconstruction.
2. **Pairwise Poses from COLMAP**: For **any** pair (i,j)(i, j) of images, COLMAP can give you the relative pose (Rij,tij)(R_{ij}, t_{ij}). Because it’s from the **same** reconstruction, the translations are all in the same scale as the submap. That means ∥tij∥\|t_{ij}\| is consistent relative to ∥tkl∥\|t_{kl}\| for any other pair (k,l)(k,l).

In other words, **all** pairs in the **COLMAP submap** share a single consistent scale.

---

## **2) Custom Matching for Each Pair**

Now, if you’re **re-estimating** the pose for every pair (i,j)(i, j) **by yourself** (e.g., using `estimate_essential_matrix()` for each pair separately), each result is:

- **An essential matrix** => yields rotation RijestR_{ij}^\text{est} and translation direction tijestt_{ij}^\text{est}. The magnitude of tijestt_{ij}^\text{est} is **inherently ambiguous** in a 2-view approach.
- This means for each pair, you only get the direction of t\mathbf{t} (plus sign + scale ambiguities).

Thus, **each pair** can come out with a different scale if you just do essential-matrix-based decomposition. If you never do a **multi-view** or **bundle adjustment** step to unify them, then you effectively have **one random scale per pair**.

---

## **3) Comparing Pairwise Poses: The Scale Problem**

When you compare your per-pair (Rijest,tijest)(R_{ij}^\text{est}, t_{ij}^\text{est}) with **COLMAP’s** (Rijcolmap,tijcolmap)(R_{ij}^\text{colmap}, t_{ij}^\text{colmap}), you’ll see:

∥tijest∥may not match∥tijcolmap∥.\|t_{ij}^\text{est}\| \quad \text{may not match} \quad \|t_{ij}^\text{colmap}\|.

But **COLMAP** has a definite scale for ∥tijcolmap∥\|t_{ij}^\text{colmap}\|. If you do **nothing**, your translation error

∥  tijcolmap−tijest∥  \|\;t_{ij}^\text{colmap} - t_{ij}^\text{est}\|\;

can be huge, simply because ∥tijest∥\|t_{ij}^\text{est}\| is not in the same ballpark.

### **Do I Need to Scale for Each Pair?**

- **Yes**, if you want a direct “meter-by-meter” comparison for each pair.
- Typically, you either:
    1. **Scale** each tijestt_{ij}^\text{est} so that ∥tijest∥\|t_{ij}^\text{est}\| matches ∥tijcolmap∥\|t_{ij}^\text{colmap}\|. Then measure the error.
    2. Or **ignore scale** and only measure direction error, e.g. ∠(tijcolmap,tijest)\angle (t_{ij}^\text{colmap}, t_{ij}^\text{est}).

Since your question states you want an **mAA** that uses “translation error in meters,” you do have to pick a scale for your custom pairwise matches.

---

## **4) Best Practice for Pairwise Pose Comparison**

If you’re only doing **two-view geometry** for each pair (no multi-view refinement):

1. **For each pair** (i,j)(i,j):
    1. Estimate (Rijest,tijest)(R_{ij}^\text{est}, t_{ij}^\text{est}).
    2. Compute **best scale** so that ∥tijest∥\|t_{ij}^\text{est}\| = ∥tijcolmap∥\|t_{ij}^\text{colmap}\|. scale=∥tijcolmap∥∥tijest∥.\text{scale} = \frac{\|t_{ij}^\text{colmap}\|}{ \|t_{ij}^\text{est}\| }.
    3. Evaluate translation error=∥  tijcolmap−(scale×tijest)⏟scaled tij∥.\text{translation error} = \big\|\;t_{ij}^\text{colmap} - \underbrace{\bigl(\text{scale}\times t_{ij}^\text{est}\bigr)}_{\text{scaled } t_{ij}} \big\|.
2. Then collect these errors for all pairs, threshold them for the mAA calculation, etc.

If you do **not** scale, each pair might be off by a large factor, artificially giving you a big translation error.

> **Note**: By scaling each pair to match the ground-truth’s norm, you are ignoring any “true” scale estimation error that a multi-view pipeline would otherwise fix. But if your approach is strictly 2-view, you basically have no single “global scale.”

---

## **5) If You Had a Multi-View Pipeline**

If you run a **custom** multi-view pipeline (like your own version of incremental SfM) with **all** images in that submap, you’d end up with a single consistent scale for **all** pairs. Then you can compare those pairwise poses to COLMAP’s submap scale. But that is a more advanced scenario.

---

## **Conclusion**

- **Yes**, within **one** COLMAP submap, **all** pairs have a consistent scale.
- Your **custom** matching (if purely pairwise essential matrix) **does not** produce a single scale for all pairs.
- Therefore, **for each pair** you should either:
    - **Manually scale** tijestt_{ij}^\text{est} to match ∥tijcolmap∥\|t_{ij}^\text{colmap}\|, then measure the error in meters,
    - **or** measure only a direction-based translation error (angle).

That is **why** if you want “translation error in meters” as the metric, you typically do the pairwise scale alignment. This ensures an apples-to-apples comparison for each pair’s (R, t) against the scale chosen by COLMAP in your submap.