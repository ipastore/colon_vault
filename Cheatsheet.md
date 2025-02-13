#### Unlock git

```bash
rm -f /Users/ignaciopastorebenaim/Documents/MGRCV/COLON/colon_matching/.git/index.lock
```
#### Git: remotes and submodules
```bash
git remote -v

git remote add upstream https://github.com/ORIGINAL-OWNER/REPO.git

git fetch upstream

git submodule add https://github.com/alexstoken/image-matching-models.git utils/image-matching_models

git submodule update --init --recursive

git add .gitmodules utils/image-matching_models 
git commit -m "Added correct submodule reference" 
git push origin main
```

Pull and push for original repo
```bash
git pull origin main
git add . 
git commit -m "Your commit message" 
git push origin main
```

Pulling & Pushing in the Submodule
```bash
cd utils/image-matching_models
git pull origin main
git add .
git commit -m "Updated image-matching models"
git push origin main
```

```bash
git submodule status
```

#### ✅ **Pull updates from the original upstream repo (`alexstoken`)**

If the upstream repo (`alexstoken/image-matching-models`) has updates you want:

```bash
git fetch upstream
git merge upstream/main  # or rebase: git rebase upstream/main
```

#### ✅ **Push the updates to your fork (`origin`)**

```bash
git push origin main
```

#### ✅ **Move back to the main repo**

```bash
cd ../..
```

#### ✅ **Update the submodule reference in your main repo**

If the submodule (`image-matching_models`) was updated, your main repo (`colon_matching`) needs to track the new commit:

```bash
git add utils/image-matching_models
git commit -m "Updated submodule reference"
git push origin main
```

---

### **3️⃣ Cloning the Repo & Submodules on a New Machine**

#### ✅ **Clone your main repository (including submodules)**

```bash
git clone --recurse-submodules https://github.com/ipastore/colon_matching.git
```

_(This ensures the submodule is downloaded too.)_

#### ✅ **If you already cloned it without submodules**

```bash
git submodule update --init --recursive
```

#### ✅ **To pull the latest updates for the submodule**

```bash
git submodule update --remote --merge
```

---

### **🔹 Summary Table: Git Commands for Each Repository**

| Action                                         | **Main Repo (`colon_matching`)**                                           | **Submodule (`image-matching_models`)**                  |
| ---------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Pull latest changes**                        | `git pull origin main`                                                     | `cd utils/image-matching_models && git pull origin main` |
| **Pull from upstream (original repo of fork)** | _Not applicable_                                                           | `git fetch upstream && git merge upstream/main`          |
| **Push changes**                               | `git push origin main`                                                     | `git push origin main`                                   |
| **Update submodule reference in main repo**    | `git add utils/image-matching_models && git commit -m "Updated submodule"` | _Not needed_                                             |
| **Clone repo with submodules**                 | `git clone --recurse-submodules <repo-url>`                                | _Handled automatically_                                  |
| **Manually update submodules**                 | `git submodule update --init --recursive`                                  | `git submodule update --remote --merge`                  |
Since you've pulled the commit that added the submodule on your **MacOS machine**, but the folder is **empty**, you need to **initialize and update the submodule** manually.

---

## **✅ Fix: Initialize & Update the Submodule on MacOS**

Run the following inside your `colon_matching` repository:

```bash
git submodule update --init --recursive
```

This will **download the contents** of the submodule (`image-matching_models`) inside the empty folder.

If you want to **always fetch submodules automatically** when pulling from the main repo, use:

```bash
git pull --recurse-submodules
```

---
## Step by Step guide to install repo

Prerequisites: 
- install conda
Steps:
```bash
conda create --name colon_matching python=3.10

conda activate colon_matching

#git clone --recursive https://github.com/alexstoken/image-matching-models
git clone --recurse-submodules https://github.com/ipastore/colon_matching.git

cd utils/image-matching-models

pip install .

pip install .[all] 
OR (OH-MyZSH)
pip install '.[all]'

conda install ipykernel

conda install pandas

git remote set-url origin https://github.com/ipastore/image-matching_models.git 

git remote add upstream https://github.com/alexstoken/image-matching-models.git
```

For Ubuntu:
Steps:
```bash
sudo apt update
sudo apt install build-essential gcc g++ -y

g++ --version

conda activate colon_matching

conda install -c conda-forge cxx-compiler

```
And continue as in macos?

git lfs install
git lfs track "*.pth"