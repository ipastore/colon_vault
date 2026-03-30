```yaml

  
"data_dir" : "Endomapper_CUDASIFT_NOV25"
"depth_subpath": inside each folder map
"image_subpath": not used (just loading the image_name)
"info_dir": ??
"train_spli": train_seqs.txt
"train_num_per_scene": 500
"val_split_": val_seqs.txt
"train_num_per_scene": 500,
"val_pairs": None
"test_split": test_seqs.txt
"test_num_per_scene": None,
"test_pairs": None,
"views": 2,
"min_overlap": 0.3, # To implement in the future
"max_overlap": 1.0, # To implement in the future
"num_overlap_bins": 1, # To implement in the future
"sort_by_overlap": False, #To implement in the future
"triplet_enforce_overlap": False, # Just going to use 2 views
"read_depth": True,  # Always true
"read_image": True, # Always true?
"grayscale": False, # Always false?
"preprocessing": ImagePreprocessor.default_conf,
"p_rotate": 0.0, # probability to rotate image by +/- 90°
"reseed": False,
"seed": 0,
"load_features": {
"do": True,   # Always strue since we are loading always cached features form the txts
**CacheLoader.default_conf,
"collate": False,
},
}
```

Al final pude guardar los npzs con la info necesaria. Lo que tengo que hacer es debugear el script y ver como va funcionando. Además la overlap matrix es toda 0. Por que?
Además hay scores negativos, en principio se filtra con el valor absoluto, revisarß