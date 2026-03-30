
## lg1e-2 vs lg1e-3

Cuando se baja el th de lg para entrenar, entrena mejor. Se nota que puede agarrar más matches positivos y así seguir empujando la loss. En nuestro caso, como estamos overfiteando, overfitea más todavía, pero quizás si podríamos encontrar una zona de no overfitting, debería dar mejor la val.

## lr5e-06 vs lr 1e-06 vs lr 1e-05

Todavía hay overfitting con lr5e-06 y lr1e-05. Se decide utilizar lr 1e-06

## ov 0007 vs 0107

En 0107 cae más la loss y hay más overfitting pero quizás porque hay casos más fáciles también.



## Nuevos runes

16006: empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_**nobin**
16007: empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_nobin_**2048**
16008: empr_gt_pos_null_10_lg1e-3_lr1e-6_ov0010_**5bin**
16009: empr_gt_pos_null_10_**lg1e-2**_lr1e-6_ov0010_nobin