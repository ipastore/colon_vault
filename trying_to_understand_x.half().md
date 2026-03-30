That sentence means PyTorch can compute the same attention formula through different implementations, and because floating-point arithmetic is not associative, the result can change slightly depending
  on how operations are grouped and accumulated.

  For example, mathematically:

  $$
  (a+b)+c = a+(b+c)
  $$

  but in floating point those can differ because each step is rounded.

  In attention, there are many such operations:

  - dot products
  - scaling
  - softmax normalization
  - weighted sums

  A fused kernel may combine steps and round at different points than the math backend. So PyTorch warns that the outputs may differ by backend even though the formula is the same.

  The second sentence is more specific:

  > For math backend, all intermediates are kept in torch.float if inputs are in torch.half or torch.bfloat16.

  That means if you call SDPA with fp16 or bf16 inputs and PyTorch falls back to the math backend, internal computations are promoted to float32 for stability. So roughly:

  - input dtype: fp16 or bf16
  - intermediate dtype in math backend: fp32
  - output dtype: usually back to input/output convention

  That is often more stable than doing everything in pure low precision.

  ## Was that the reason they used .half() in branch A?

  Possibly, but if so, it is still not a very good reason.

  If their idea was:

  - “cast to half so fused flash/memory-efficient kernels can run”

  then that makes some historical sense.

  But if the call falls back to the math backend, then:

  - calling with bf16 would also have gotten float32 intermediates
  - calling with fp16 also gets float32 intermediates
  - so .half() is not needed for math-backend stability

  In fact, if the backend falls back to math, forcing bf16 -> fp16 may be worse, because:

  - both get float32 intermediates in math backend
  - but the inputs were already rounded differently before entering the op
  - bf16 and fp16 have different tradeoffs, and bf16 often better preserves range

  So no, that doc excerpt is not a strong justification for .half().

  ## More likely original intent of branch A

  The likely intent was:

  - on CUDA + “flash enabled”
  - force inputs to fp16
  - maximize the chance of dispatching to a fast fused kernel

  That is different from:

  - “make math fallback safer”

  If they were thinking about math fallback, the doc would actually suggest that fallback already handles low precision reasonably by promoting intermediates to float.

  ## So in case branch A hit math backend

  Then this would happen conceptually:

  1. tensors are first cast to fp16 by .half()
  2. SDPA math backend receives fp16 inputs
  3. math backend promotes intermediates to float32
  4. result is returned and cast back to q.dtype

  If q.dtype was originally bf16, then the path is effectively:

  $$
  \text{bf16} \to \text{fp16} \to \text{fp32 intermediates} \to \text{bf16 output}
  $$

  That is an odd path. If you had simply kept bf16, the math backend could have done:

  $$
  \text{bf16} \to \text{fp32 intermediates} \to \text{bf16 output}
  $$

  which is cleaner.

  So if branch A is hitting math backend, .half() is even harder to justify.

  ## Bottom line

  That PyTorch note means:

  - backend choice changes numerical behavior
  not:

  - trying to benefit from math backend float32 intermediates

When `--mixed_precision bfloat16` is enabled in this repo, `data["descriptors0"]` and `data["descriptors1"]` typically arrive in `LightGlue.forward()` as real `fp32` tensors. After `.contiguous()`,
  `desc0` and `desc1` are still `fp32`. At that point, the explicit block `if torch.is_autocast_enabled(): desc0 = desc0.half(); desc1 = desc1.half()` really does save memory, because it converts `fp32 ->
  fp16`. If that cast were removed, autocast would not immediately rewrite those tensors in place; instead, the next eligible ops such as `self.input_proj(desc0)` and `self.input_proj(desc1)` would
  typically run under autocast and produce low-precision outputs, which under `--mixed_precision bfloat16` would generally be `bf16`. From there, inside `SelfBlock.forward()`, `qkv = self.Wqkv(x)` would
  also typically be produced in the autocast dtype, and the derived `q`, `k`, and `v` would therefore also generally be `bf16`. In the generic SDPA branch, those `q/k/v` tensors would be passed to
  `scaled_dot_product_attention` without changing dtype. However, in the flash-requested branch, the code explicitly does `args = [x.half().contiguous() for x in [q, k, v]]`, which forces `q/k/v` from
  `bf16` to `fp16`. That second cast usually does not save memory, because both `bf16` and `fp16` are 16-bit formats; it only changes the numerical behavior and possibly backend eligibility. So the
  important distinction is: the first `.half()` on `desc0/desc1` can genuinely reduce memory if those tensors are still `fp32`, while the second `.half()` on `q/k/v` usually does not reduce memory if they
  were already `bf16`, and instead mainly overrides the intended autocast dtype.
