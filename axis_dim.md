**It is the same: axis and dim** 

Looking at desc1 with shape (128, 1691):

128 rows = descriptor dimension (e.g., each SIFT descriptor is a 128-D vector)
1691 columns = number of keypoints (each column is one descriptor)
****

**What axis=0 does (the current code):**

```python
desc1_norm = np.linalg.norm(desc1, axis=0, keepdims=True)
```

Collapses down the rows (dimension 0).
You get one norm per column → shape (1, 1691).
This computes the L2 norm of each 128-D descriptor vector separately.
Correct for normalizing descriptors when they're stored column-wise.
****
**What axis=1 would do:**

```python
desc1_norm = np.linalg.norm(desc1, axis=1, keepdims=True)
```

****
**Collapses across the columns (dimension 1).**
You get one norm per row → shape (128, 1).
This would compute the norm across all 1691 keypoints for each of the 128 descriptor dimensions.
Wrong — you'd be treating each dimension as a separate entity instead of treating each descriptor as a unit.
****
**In plain English:**

axis=0 says: "Give me the length of each column vector (each descriptor)." ✅
axis=1 says: "Give me the length of each row vector (each dimension across all descriptors)." ❌
Since you want to normalize each descriptor independently, you need axis=0.


****
for 
```python
np.argmin(x,axis=0)
```

In NumPy, for a 2D array:

axis=0 means "operate down the rows" (i.e., across columns).
axis=1 means "operate across the columns" (i.e., within each row).
So, for np.argmin(dmat, axis=1):

You get the index of the minimum value across columns for each row.
For np.argmin(dmat, axis=0):

You get the index of the minimum value down the rows for each column.
Summary:

axis=0 → operate down rows (across columns)
axis=1 → operate across columns (within each row)

In other words:

idx = np.argmin(dmat, axis=1)

means:  
**For every row, search over the columns and return the index of the column with the smallest value.**

So, for each descriptor in image 1 (each row), you find the index of the closest descriptor in image 2 (the column with the least distance).

