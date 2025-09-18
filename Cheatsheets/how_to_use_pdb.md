### How to use pdb

``` bash
# Navigation
n              # Step to next line (step over)
s              # Step into function (like F11)
r              # Run until the current function returns
c              # Continue until next breakpoint or end
q              # Quit debugging

# Inspection
p checkpoint   # Print the value of checkpoint variable
pp config      # Pretty print the config dictionary
p os.path.exists(checkpoint)  # Check if checkpoint exists
p 'CUDA_VISIBLE_DEVICES' in os.environ  # Check environment variable

# Setting breakpoints
b experiment.get_num_gpus   # Set breakpoint at function
b experiment.py:62          # Set breakpoint at line 62 in experiment.py

# Listing code
l               # List source code around current line
ll              # List entire function source
```