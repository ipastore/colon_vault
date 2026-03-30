Let the model parameters be $\theta$, and suppose each sample $x_i$ in a batch has loss

  $$
  \ell_i(\theta).
  $$

  If a GPU processes a local batch of size $B$, the usual batch loss is the mean:

  $$
  L(\theta) = \frac{1}{B}\sum_{i=1}^{B} \ell_i(\theta).
  $$

  Then the gradient on that GPU is

  $$
  \nabla_\theta L(\theta)
  = \nabla_\theta \left(\frac{1}{B}\sum_{i=1}^{B} \ell_i(\theta)\right)
  = \frac{1}{B}\sum_{i=1}^{B} \nabla_\theta \ell_i(\theta).
  $$

  So the batch gradient is just the average of the per-sample gradients.

  Now suppose you have 2 GPUs in DDP.

  - GPU 1 sees local batch $\mathcal{B}_1$ of size $B$
  - GPU 2 sees local batch $\mathcal{B}_2$ of size $B$

  Each GPU computes its own local loss:

  $$
  L_1(\theta) = \frac{1}{B}\sum_{i \in \mathcal{B}_1} \ell_i(\theta)
  $$

  $$
  L_2(\theta) = \frac{1}{B}\sum_{i \in \mathcal{B}_2} \ell_i(\theta)
  $$

  and local gradients:

  $$
  g_1 = \nabla_\theta L_1(\theta)
  = \frac{1}{B}\sum_{i \in \mathcal{B}1} \nabla\theta \ell_i(\theta)
  $$

  $$
  g_2 = \nabla_\theta L_2(\theta)
  = \frac{1}{B}\sum_{i \in \mathcal{B}2} \nabla\theta \ell_i(\theta).
  $$

  DDP performs an all-reduce sum and then divides by the number of processes, so the final gradient used for the optimizer step is:

  $$
  g = \frac{g_1 + g_2}{2}.
  $$

  Substituting the definitions:

  $$
  g = \frac{1}{2}\left(
  \frac{1}{B}\sum_{i \in \mathcal{B}1} \nabla\theta \ell_i(\theta)
  +
  \frac{1}{B}\sum_{i \in \mathcal{B}2} \nabla\theta \ell_i(\theta)
  \right)
  $$

  $$
  g = \frac{1}{2B}\left(
  \sum_{i \in \mathcal{B}1} \nabla\theta \ell_i(\theta)
  +
  \sum_{i \in \mathcal{B}2} \nabla\theta \ell_i(\theta)
  \right)
  $$

  $$
  g = \frac{1}{2B}\sum_{i \in \mathcal{B}_1 \cup \mathcal{B}2} \nabla\theta \ell_i(\theta).
  $$

  So this is exactly the gradient of the mean loss over the global batch of size $2B$:

  $$
  L_{\text{global}}(\theta)
  = \frac{1}{2B}\sum_{i \in \mathcal{B}_1 \cup \mathcal{B}_2} \ell_i(\theta).
  $$

  That is why DDP usually behaves like training with a larger batch.

  ## Why averaging can stabilize training

  Suppose one GPU gets an unusually bad local batch and produces a large gradient spike:

  $$
  g_1 = g_{\text{true}} + \varepsilon_1
  $$

  $$
  g_2 = g_{\text{true}} + \varepsilon_2
  $$

  Then the averaged gradient is:

  $$
  g = \frac{g_1 + g_2}{2}
  = g_{\text{true}} + \frac{\varepsilon_1 + \varepsilon_2}{2}.
  $$

  If the noise terms $\varepsilon_1$ and $\varepsilon_2$ are partly independent, then averaging reduces variance.

  In general:

  $$
  \mathrm{Var}\left(\frac{g_1 + g_2}{2}\right)
  = \frac{1}{4}\left(\mathrm{Var}(g_1) + \mathrm{Var}(g_2)\right)
  $$

  and if both have the same variance $\sigma^2$ and are independent:

  $$
  \mathrm{Var}(g) = \frac{\sigma^2}{2}.
  $$

  So gradient noise is reduced by averaging.

  That means:

  - extreme local spikes may be softened
  - instability may become less visible
  - training can appear more stable on multiple GPUs

  ## Important caveat

  If both GPUs get similarly pathological gradients, then averaging does not help much.

  For example, if:

  $$
  g_1 \approx g_2 \approx \text{huge bad gradient},
  $$

  then:

  $$
  g = \frac{g_1 + g_2}{2}
  $$

  is still huge.

  So DDP averaging reduces random noise, but it does not fix a systematic bug.

  ## In your case

  If padded-feature contamination causes occasional bad samples, then on a single GPU:

  $$
  g_{\text{single}} = \frac{1}{B}\sum_{i=1}^{B} \nabla_\theta \ell_i
  $$

  may fluctuate a lot depending on whether that batch contains several problematic pairs.

  With 2 GPUs, the update becomes:

  $$
  g_{\text{ddp}} = \frac{1}{2}(g_1 + g_2),
  $$

  which is the gradient over a larger effective batch and therefore usually less noisy.

  So yes, mathematically, DDP can make the training look more stable because it averages local gradients over more samples.