Also see [[L2_distance]]

Let's break it down with your example matrices:

**Matrix A**  
```python
A = [[1, 2, 3],
     [4, 5, 6]]
```
**Matrix B**  
```python
B = [[a, b, c],
     [d, e, f]]
```

---

### 1. **Dot Product (between vectors)**

- The dot product is for two vectors of the same length.
- For example, between `[1, 2, 3]` and `[a, b, c]`:
  ```python
  dot = 1*a + 2*b + 3*c
  ```
- In numpy: `np.dot([1,2,3], [a,b,c])`

---

### 2. **Matrix Multiplication**

- For two matrices, use `np.dot(A, B)` or `A @ B` (if shapes are compatible).
- With your shapes (both 2x3), you **cannot** multiply directly (need A: 2x3, B: 3xN).
- If you transpose B:
  ```python
  np.dot(A, B.T)
  ```
  This gives a 2x2 matrix:
  ```python
  [[1*a + 2*b + 3*c, 1*d + 2*e + 3*f],
   [4*a + 5*b + 6*c, 4*d + 5*e + 6*f]]
  ```

---

### 3. **Cosine Similarity & Cosine Distance**

- **Cosine similarity** between two vectors x and y:
  ```python
  cos_sim = (x · y) / (||x|| * ||y||)
  ```
  Where `·` is the dot product, and `||x||` is the L2 norm.

- **Cosine distance** is:
  ```python
  cos_dist = 1 - cos_sim
  ```

- In descriptor matching, you often normalize vectors to unit length, so the dot product directly gives cosine similarity.

---

### 4. **Summary Table**

| Operation             | What it does                         | Example (A, B)      |
| --------------------- | ------------------------------------ | ------------------- |
| Dot product           | Scalar between two vectors           | `[1,2,3] · [a,b,c]` |
| Matrix multiplication | Combines rows of A with columns of B | `np.dot(A, B.T)`    |
| Cosine similarity     | Dot product of normalized vectors    | `np.dot(x)`         |
| Cosine distance       | `1 - cosine similarity`              | `1 - np.dot(x)`     |

---

**In descriptor matching:**  
- You normalize descriptors (columns of A and B).
- Compute dot products between all pairs (matrix multiplication).
- Use the result as cosine similarity.
- Convert to cosine distance if needed.

**So:**  
- **Cosine similarity** is the dot product of normalized vectors.
- **Cosine distance** is `1 - cosine similarity`.