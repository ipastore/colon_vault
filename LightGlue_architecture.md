# LightGlue Architecture

LightGlue is a feature matching framework that leverages transformer-based architectures to match keypoints and descriptors between two images. It utilizes advanced self-attention and cross-attention mechanisms to achieve robust and accurate feature matching.

---

## 🧠 **Key Components:**

### 1. **SelfBlock**
- Implements **self-attention** for a single set of descriptors.
- Computes the relationships between keypoints within the same image.
- Uses **multi-head attention** to enhance feature representation.

### 2. **CrossBlock**
- Implements **cross-attention** between two sets of descriptors.
- Finds correspondences between keypoints from the two input images.
- Facilitates matching by evaluating how well features from one image relate to features from the other.

### 3. **TransformerLayer**
- Combines `SelfBlock` and `CrossBlock` to apply both **self-attention** and **cross-attention**.
- Contains mechanisms to handle **masked attention**, allowing the integration of specular masks.

### 4. **LearnableFourierPositionalEncoding**
- Adds positional encoding to the keypoints.
- Helps maintain spatial relationships between keypoints within the transformer architecture.

### 5. **TokenConfidence**
- Computes **confidence scores** for the tokens (descriptors).
- Aids in early stopping and pruning of low-confidence points during the matching process.

### 6. **MatchAssignment**
- Computes the **assignment matrix** for matching descriptors.
- Utilizes a **log-softmax** approach to determine the best matches between the two sets of keypoints.

### 7. **LightGlue Class**
- The main orchestrator of the matching process.
- Manages the flow of data through all components.
- Supports **attention masking**, which can be used to handle **specular regions** using specular masks.

---

## 🔄 **Workflow:**

1. **Input:** Accepts two sets of **keypoints** and **descriptors** from two images.
2. **Normalization:** Normalizes keypoints to a consistent scale and space.
3. **Positional Encoding:** Applies **LearnableFourierPositionalEncoding** to the keypoints.
4. **Transformer Layers:** Passes data through a series of **TransformerLayers**, each applying **self-attention** and **cross-attention**.
5. **Confidence and Pruning:** Computes **confidence scores** and **prunes** low-confidence points.
6. **Matching:** Generates the **assignment matrix** and **extracts matches** between the images.

---

## 🚀 **Advanced Features:**

- **Attention Masking:** LightGlue supports attention masking within the **TransformerLayer**, allowing integration with **specular masks**.
- **Pruning Mechanism:** The **TokenConfidence** module, combined with **MatchAssignment**, allows for dynamic **point pruning**, improving both speed and accuracy.
- **Support for Specular Masks:** When provided, specular masks are incorporated into the attention mechanisms, enhancing robustness in challenging lighting conditions