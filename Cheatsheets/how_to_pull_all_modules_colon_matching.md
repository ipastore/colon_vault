```bash
#!/bin/bash

# Main repository
cd /Users/ignaciopastorebenaim/Documents/MGRCV/COLON/colon_matching
git switch main
git pull origin main

# Submodule: image-matching-models
cd utils/image-matching_models
git switch main
git pull origin main

# Sub-submodule: LightGlue
cd matching/third_party/LightGlue
git switch main
git pull origin main

# Sub-submodule: RoMa
cd ../RoMa
git switch main
git pull origin main

echo "All submodules are updated!"
```
