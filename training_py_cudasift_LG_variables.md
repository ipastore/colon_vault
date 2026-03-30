oob: Out Of the Box
ft: FineTuned

py_colmap_HM: added scale_ori OK
py_colmap_MD: used sparse depth to export and filter invalid features
py_cudasift_HM: added scale_ori OK
py_cudasift_ENDO_HM: DId not multiply scales with scores. Only applied homo to right image, photo augmentation for both

### To train
py_cudasift_ENDO_HM: 
	- a) Homographies to both images
	- b) Multiply scales and scores
py_cudasift_ENDO_3D: 
	- Baseline with homo only right and scores (without * scales)
	- a) and b) if necessary of py_cudasift_ENDO_HM
superpoint_ft_ENDO_HM (x2 esuperpoint + ours)
superpoint_ft_ENDO_3D (x2 esuperpoint + ours)
### To eval
Benchmarks: Megadepth1500, EndomapperDense1500.

end-to-end: roma, master
extractors: 
	SIFT: py_colmap, py_cudasift
     Superpoint: oob, esuperpoint, ours
matchers: 
	NN
	SIFT_LG: oob, ft_ENDO_HOMO, ft_ENDO_3D
	SUPERPOINT_LG: 
	ROMA

roma
master
py_colmap+all_matchers
py_cudasift+all_matchers
