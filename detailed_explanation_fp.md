A floating-point number is represented approximately as

$$
  x = (-1)^s \cdot m \cdot 2^e
$$
  
  where:
  - s is the sign bit
  - e is the exponent
  - m is the significand, often called the mantissa in practice
  
  The key idea is:
  - the exponent sets the scale
  - the mantissa sets the detail within that scale

###  Normalized binary floating point

  For normal numbers, the stored representation is effectively

$$
  x = (-1)^s \cdot (1.f)_2 \cdot 2^{E - \text{bias}}
$$


  where:

  - E is the stored exponent field
  - bias is a fixed offset
  - (1.f)_2 means binary 1.xxx..., with the fractional bits coming from the mantissa field

  Example in binary:

$$
  1.101_2 \cdot 2^3 = 1101_2 = 13
$$
  

  So:

  - mantissa 1.101_2 gives the precise shape
  - exponent 3 shifts the binary point, scaling the number

### What exponent does

  If you keep the same mantissa and change the exponent:

  $1.25 \cdot 2^0 = 1.25$
  $1.25 \cdot 2^4 = 20$
  $1.25 \cdot 2^{-3} = 0.15625$


  So exponent controls dynamic range:
  - larger exponent -> much larger number
  - smaller exponent -> much smaller number

  Each increment of exponent multiplies by 2.

  If you have k exponent bits, you can represent many powers of two. More exponent bits means wider range.

###  What mantissa does
  The mantissa controls resolution between powers of two.

  Suppose exponent is fixed at 2, so numbers are around scale 2^2 = 4.

  If mantissa is:
  $1.000_2 \cdot 2^2 = 4$
  $1.001_2 \cdot 2^2 = 4.5$
  $1.010_2 \cdot 2^2 = 5$
  

  More mantissa bits means more representable values between 4 and 8.

  So mantissa controls precision:
  - more mantissa bits -> finer spacing between nearby numbers
  - fewer mantissa bits -> coarser rounding
### Spacing mathematically
  
  For numbers near $x \approx 2^e$ the gap between adjacent representable floating-point numbers is roughly $\Delta x \approx 2^{e-p}$

  where p is the number of precision bits in the significand.

  This is crucial:
  - as numbers get larger, spacing gets larger
  - with fewer mantissa bits, spacing is larger everywhere

  So floating point has relative precision, not uniform absolute precision.
  FP16 vs BF16

	  fp16:
	  - 5 exponent bits
	  - 10 fraction bits
	  - effective precision is about 11 significand bits including the hidden leading 1
	
	  bf16:
	  - 8 exponent bits
	  - 7 fraction bits
	  - effective precision is about 8 significand bits

  So:
	  Range:
	  - bf16 has much larger exponent range
	  - it can represent much larger and much smaller numbers
	
	  Precision:
	  - fp16 has more mantissa bits
	  - it distinguishes nearby values better

  Why bigger exponent helps stability

  If an intermediate value in a neural network becomes large, you need enough exponent range to represent it without overflow.

  If not, you get:
  - inf from overflow
  - 0 from underflow
  - unstable downstream operations

  Because bf16 has the same exponent size as fp32, it has a range much closer to fp32, which is why it is usually safer.

  Why bigger mantissa helps precision

  If two numbers are close:
  a = 1.234
  b = 1.235

  a format with more mantissa bits is more likely to preserve the difference after rounding.

  This matters for:
  - accumulation
  - small gradient updates
  - comparing similar logits

  But in deep learning, overflow/underflow is often a bigger practical problem than tiny rounding differences, which is why bf16 is often preferred.

### Attention-specific intuition

  In attention, logits are
  $$L_{ij} = \frac{q_i \cdot k_j}{\sqrt{d}}$$

  A dot product sums many products. That can create large magnitudes. Large exponent range helps keep those logits representable.

  Then softmax uses exponentials:
$$
  \text{softmax}(L_i) = \frac{e^{L_i}}{\sum_j e^{L_j}}
$$

  Exponentials are very sensitive to large positive or negative inputs, so range matters a lot. That is one reason bf16 tends to be safer than fp16 for attention-heavy models.