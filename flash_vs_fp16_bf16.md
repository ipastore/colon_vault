The most likely reason is not “FlashAttention is inherently unstable”, but your code path changes dtype when flash is enabled.

  In lightglue.py:113, the flash branch does this:

  args = [x.half().contiguous() for x in [q, k, v]]

  So if you were training in bf16, enabling flash here changes

  $$
  \text{bf16} \to \text{fp16}
  $$

  before attention.

  That is a major numerical change. fp16 has less dynamic range than bf16, so large attention logits are more likely to overflow or become badly scaled.

  ## Where flash is influencing the computation

  For attention, the risky quantity is

  $$
  s_{ij} = \frac{q_i^\top k_j}{\sqrt{d}}
  $$

  Then attention does

  $$
  \alpha_{ij} = \frac{\exp(s_{ij})}{\sum_k \exp(s_{ik})}
  $$

  and

  $$
  m_i = \sum_j \alpha_{ij} v_j
  $$

  If q, k, v are cast from bf16 to fp16, the main effects are:

  - smaller dynamic range for the dot products $q_i^\top k_j$
  - larger chance of overflow/underflow in intermediate values
  - worse behavior when synthetic padded tokens produce unusually large similarities

  So the likely chain is:

  1. padded/synthetic tokens create some large or noisy logits
  2. flash branch forces fp16
  3. fp16 is less forgiving than bf16
  4. softmax/log-softmax gets sharper or numerically worse
  5. loss spikes, then gradients spike

  ## Why training can be stable in bf16 without flash

  Because then you are probably not converting to fp16.

  If flash is disabled, Attention.forward:120 takes:

  args = [x.contiguous() for x in [q, k, v]]
  v = F.scaled_dot_product_attention(*args, attn_mask=mask)

  So bf16 stays bf16.

  That alone can explain better stability.

  ## Why the non-flash branches may not explode

  There are two different “else” cases.

  ### 1. Attention.forward non-flash SDPA path

  If FLASH_AVAILABLE is true but flash is not enabled, it still uses SDPA:

  v = F.scaled_dot_product_attention(*args, attn_mask=mask)

  without .half().

  So:

  - with bf16, it stays bf16
  - PyTorch can choose math or memory-efficient backend
  - those kernels may accumulate differently and be more stable than your forced-fp16 flash path

  ### 2. CrossBlock.forward manual path

  If self.flash is None, it uses explicit matmul/einsum and softmax:

  sim = torch.einsum("bhid, bhjd -> bhij", qk0, qk1)
  attn01 = F.softmax(sim, dim=-1)

  Why can this be more stable?

  - again, it does not force bf16 -> fp16
  - some ops may internally promote or behave more conservatively depending on autocast/backend
  - it avoids the exact fused-kernel numerical path of flash
  - in your repo, this path also makes the scaling explicit:

    qk0, qk1 = qk0 * self.scale**0.5, qk1 * self.scale**0.5
    so the logits are formed as
    $$
    (qk0 \sqrt{\text{scale}})(qk1 \sqrt{\text{scale}})^\top
    $$
    which is equivalent to dividing by $\sqrt{d}$, but done in a different computational order

  Same math, different numerics.

  ## Important conclusion

  If you observe:

  - bf16 + no flash: stable
  - flash enabled: unstable

  the first suspect is not flash itself
  but this line:

  x.half()

  Because it changes the experiment from:

  - bf16 attention

  to:

  - fp16 attention

  That is not an apples-to-apples comparison.

  ## What would be the proper comparison

  You should compare:

  1. bf16 + SDPA without forced cast
  2. bf16 + flash-capable SDPA without forced cast
  3. fp16 + SDPA
  4. fp16 + flash

  Right now your “flash” path is really:

  - “flash attempt + forced fp16”

  not just “flash”.

  ## Short answer

  Why is bf16 without flash more stable?

  - because in your code it remains bf16

  Where is flash influencing things?

  - mainly through the branch that forces q/k/v to fp16
  - and secondarily through different fused-kernel numerics

  Why do the other branches not explode?

  - because they do not force bf16 -> fp16
  - and their numerical behavior is usually more forgiving for your padded/noisy logits

  If you want, I can now tell you exactly how to modify the Attention.forward() branch so you can test “flash with preserved bf16” instead of “flash with forced fp16”.