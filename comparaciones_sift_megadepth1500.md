# Rootsift

Rootsift normalization makes a difference for pycolmap but not for py_cudasift

# Lowering LG th
We get more recallm less precision, poorer computation of camera pose. Do we care about getting more precise matches? Which LG threshold are we using in Endocartoscopoe SLAM

# Bests
1) Roma
2) Superpoint official
3) Sifts oficial (both, opencv and pycolmap)
4) PyCudasift_finetuned

# Offset in py_cudasift
Similar

# Channel
Green channel is the worst for megadepth

# Is Py_Cudasift worst with finetuned?
No Py_Cudasift is similiar with official and finetude. But it is worst than Py_colmap_official


# Chosen
- Roma
- Py_colmap_official
- Opencv_official
- Py_Cudasift_official
- Py_Cudasift_finetuned (grayscale_root_th0.1)