```python
pip install memory_profiler

from memory_profiler import profile

@profile
def process_submap(submap):
    # Your submap processing code
    pass

python -m memory_profiler error_measurement.py

# Start memory monitoring
mprof run error_measurement.py

# Generate a plot
mprof plot
```
