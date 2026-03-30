Let:

  - $x_i \in \mathbb{R}^D$ be the descriptor of keypoint $i$
  - $D$ be the full embedding dimension
  - $h$ be the number of heads
  - $d = D/h$ be the per-head dimension

  From the input descriptors, LightGlue computes for each head:

  $$
  q_i = W_Q x_i,\quad k_i = W_K x_i,\quad v_i = W_V x_i
  $$

  with $q_i, k_i, v_i \in \mathbb{R}^d$ for one head.

  For a real keypoint $i$, attention computes scores against all other tokens $j$:

  $$
  s_{ij} = \frac{q_i^\top k_j}{\sqrt{d}}
  $$

  and attention weights:

  $$
  \alpha_{ij} = \frac{\exp(s_{ij})}{\sum_k \exp(s_{ik})}
  $$

  Then the attention message for token $i$ is:

  $$
  m_i = \sum_k \alpha_{ij} v_j
  $$

  and the transformer block outputs a new representation:

  $$
  x_i' = x_i + \mathrm{FFN}([x_i, m_i])
  $$

  That is what “updated descriptor” means: the layer takes input descriptor $x_i$ and returns a new descriptor $x_i'$.

  Now split the tokens into:

  - real tokens: $\mathcal{R}$
  - padded/synthetic tokens: $\mathcal{P}$

  If there is no padding contamination, then for a real token $i \in \mathcal{R}$:

  $$
  m_i^{\text{clean}} = \sum_{j \in \mathcal{R}} \alpha_{ij}^{\text{clean}} v_j
  $$

  with

  $$
  \alpha_{ij}^{\text{clean}} =
  \frac{\exp(s_{ij})}{\sum_{k \in \mathcal{R}} \exp(s_{ik})}
  $$

  If padded tokens are also present and unmasked, then:

  $$
  m_i^{\text{cont}} = \sum_{j \in \mathcal{R} \cup \mathcal{P}} \alpha_{ij}^{\text{cont}} v_j
  $$

  with

  $$
  \alpha_{ij}^{\text{cont}} =
  \frac{\exp(s_{ij})}{\sum_{k \in \mathcal{R} \cup \mathcal{P}} \exp(s_{ik})}
  $$

  So:

  $$
  m_i^{\text{cont}} =
  \sum_{j \in \mathcal{R}} \alpha_{ij}^{\text{cont}} v_j
  +
  \sum_{j \in \mathcal{P}} \alpha_{ij}^{\text{cont}} v_j
  $$

  This differs from the clean case in two ways:

  1. There is an extra term from padded tokens:

  $$
  \sum_{j \in \mathcal{P}} \alpha_{ij}^{\text{cont}} v_j
  $$

  2. Even the weights on real tokens change, because the denominator changed:

  $$
  \sum_{k \in \mathcal{R}} \exp(s_{ik})
  \quad\to\quad
  \sum_{k \in \mathcal{R} \cup \mathcal{P}} \exp(s_{ik})
  $$

  Therefore, in general:

  $$
  m_i^{\text{clean}} \neq m_i^{\text{cont}}
  $$

  and so the output descriptor also changes:

  $$
  x_i'^{\text{clean}} \neq x_i'^{\text{cont}}
  $$

  That is the contaminated-forward argument.

  Now for the matching loss.

  LightGlue later computes similarities between refined descriptors from image 0 and image 1:

  $$
  \mathrm{sim}_{ij}
  $$

  Then it applies row-wise and column-wise log-softmax. For a real keypoint $i$ in image 0 and a real match candidate $j$ in image 1, the row-wise probability is:

  $$
  p_{ij} =
  \frac{\exp(\mathrm{sim}{ij})}{\sum_k \exp(\mathrm{sim}{ik})}
  $$

  If padded candidates are present in the columns and unmasked, then:

  $$
  p_{ij}^{\text{cont}} =
  \frac{\exp(\mathrm{sim}_{ij})}
  {\sum{k \in \mathcal{R}} \exp(\mathrm{sim}{ik}) + \sum{k \in \mathcal{P}} \exp(\mathrm{sim}_{ik})}
  $$

  Without padded candidates:

  $$
  p_{ij}^{\text{clean}} =
  \frac{\exp(\mathrm{sim}_{ij})}
  {\sum{k \in \mathcal{R}} \exp(\mathrm{sim}_{ik})}
  $$

  Hence in general:

  $$
  p_{ij}^{\text{cont}} \neq p_{ij}^{\text{clean}}
  $$

  and usually

  $$
  p_{ij}^{\text{cont}} \le p_{ij}^{\text{clean}}
  $$

  because the contaminated denominator is larger.

  So even if the loss is only applied to real GT pairs, the supervised probability itself is different, because padded tokens still enter:

  - the attention computation
  - the softmax normalization of the matching scores

  That is why:

  - clean GT + contaminated forward
    is not the same as
  - clean GT + clean forward
