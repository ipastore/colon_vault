

### empr_gt_pos_null_10_lg1e-2_lr5e-5_ov0010_nobin

full training with middle lr
still overfitting due to lr5e-5

### empr_gt_pos_null_10_lg1e-2_lr1e-6_ov0010_nobin
expecting less overfit

less overfit, but still overfitting

### empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_nobin
lowering lg, trying to be lucky

still overfitting. 

### empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_nobin_2048
no difference with more max features


### empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_5bin
expecting less overfitting

quite good until 1M. It improves vs without bins. 

## empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0007_4bin
this couldbe the best comination of bins and overlap to avoid overfitting

dont know why but the pos loss is still exploding

# Conclusion
with lr 1e-06 we can avoid overfitting but we are training in small steps. Still no clear if the overfitting is coming from the nobin or from the overlap. 

Try to set the pairs were we want to see clear improvement and not changing