This line converts **cosine similarity** to **Euclidean (L2) distance** for unit-normalized vectors.

Also see [[cosine_distance]]
### Math explanation

If **u** and **v** are unit vectors,  
- Cosine similarity: `cos_sim = u · v`  
- Euclidean distance:  
  
$$
  ||u - v||^2 = ||u||^2 + ||v||^2 - 2(u · v)
              = 1 + 1 - 2(u · v)
              = 2 - 2(u · v)
$$

So,  
  ```
  Euclidean distance = sqrt(2 - 2 * cos_sim)
  ```

### In code

```python
dmat = np.dot(desc1.T, desc2)  # cosine similarity matrix
dmat = np.sqrt(2 - 2 * np.clip(dmat, -1, 1))  # Euclidean distance matrix
```

**Summary:**  
- This formula gives the L2 distance between every pair of normalized descriptors using their cosine similarity.  
- `np.clip` keeps values in [-1, 1] for numerical stability.