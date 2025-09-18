# 🚀 **Pushing and Pulling Changes in the LightGlue Sub-Submodule**

This guide provides a **step-by-step workflow** to **push and pull changes** in the **LightGlue sub-submodule** of the **colon_matching project**, ensuring **consistency** and **avoiding detached HEAD states**.

---

## 🧠 **Pushing Changes to LightGlue Sub-Submodule**

### 1. **Navigate to the Sub-Submodule**

```bash
cd ~/colon_matching/utils/image-matching_models/matching/third_party/LightGlue
```

### 2. **Ensure You Are on the Correct Branch**

```bash
git checkout feature/lightglue_mask
```

### 3. **Stage, Commit, and Push Changes**

```bash
git add .
git commit -m "Feature: Update LightGlue with new functionality"
git push origin feature/lightglue_mask
```

### 4. **Verify the Remote is Correct**

```bash
git remote set-url origin https://github.com/ipastore/LightGlue.git
```

---

## ✅ **Push the Changes to the Submodule**

### 1. **Navigate to the Submodule (`image-matching-models`)**

```bash
cd ~/colon_matching/utils/image-matching_models
```

### 2. **Commit and Push the Updated Submodule Reference**

```bash
git checkout feature/mask
git add matching/third_party/LightGlue
git commit -m "Update LightGlue submodule to latest feature/lightglue_mask"
git push origin feature/mask
```

---

## 🚀 **Push to the Main Repository (`colon_matching`)**

### 1. **Navigate to the Main Repository**

```bash
cd ~/colon_matching
```

### 2. **Commit and Push the Updated Submodule Reference**

```bash
git checkout feature/mask
git add utils/image-matching_models
git commit -m "Update image-matching-models submodule to feature/mask"
git push origin feature/mask
```

---

## 🔄 **Pulling Changes in LightGlue Sub-Submodule**

### 1. **Pull Changes from the Main Repository**

```bash
cd ~/colon_matching
git checkout feature/mask
git pull origin feature/mask
```

### 2. **Pull Changes from the Submodule (`image-matching-models`)**

```bash
cd utils/image-matching_models
git checkout feature/mask
git pull origin feature/mask
```

### 3. **Pull Changes from the Sub-Submodule (`LightGlue`)**

```bash
cd matching/third_party/LightGlue
git checkout feature/lightglue_mask
git pull origin feature/lightglue_mask
```

---

## 🎯 **Final Steps: Verify and Test**

### 1. **Verify Submodule Status**

```bash
cd ~/colon_matching
git submodule status --recursive
```

### 2. **Test the Code**

```bash
python run_models.py
```

Ensure the **code runs smoothly**, reflecting **all recent changes**.

---

## 💡 **Pro Tip: Automate with a Bash Script**

```bash
#!/bin/bash

# Pull from the main repository
cd ~/colon_matching
git checkout feature/mask
git pull origin feature/mask

# Pull from the submodule
cd utils/image-matching_models
git checkout feature/mask
git pull origin feature/mask

# Pull from the sub-submodule
cd matching/third_party/LightGlue
git checkout feature/lightglue_mask
git pull origin feature/lightglue_mask

echo "All repositories are up-to-date!"
```

---

## 🚦 **Summary**
- **Always use feature branches** for **submodules** and **sub-submodules**.
- **Avoid detached HEAD states** by setting **upstream branches**.
- **Manually pull and push** in **specific submodules** to **maintain control**.
- **Verify submodule status** and **test the code** regularly.

By following this guide, you can **efficiently manage changes** in the **LightGlue sub-submodule** while maintaining **repository stability** and **development efficiency**.


# 🧠 **Summary: Git Submodules and Sub-Submodules Management**

Managing **Git submodules** and **sub-submodules** involves handling nested repositories within a main project. The workflow includes **cloning**, **updating**, **committing**, **pushing**, and **pulling** across **main repositories**, **submodules**, and **sub-submodules**, while avoiding common pitfalls like **detached HEAD states**, **untracked files**, and **permission issues**.

---

## 🚦 **Common Scenarios and Solutions**

### 🆕 **1. Cloning a Repository with Submodules**

```bash
# Clone the main repository with all submodules
git clone --recurse-submodules <repository_url>

# Initialize and update submodules (if already cloned)
git submodule update --init --recursive
```

---

### 🔄 **2. Pulling Changes in Main Repository and Submodules**

```bash
# Pull changes in the main repository
cd ~/colon_matching
git pull origin main

NOOOO:
git submodule update --init --remote --recursive NOOOO
```

#### 💡 **Avoid Updating All Submodules**
```bash
# Only update specific submodules
git submodule update --remote matching/third_party/LightGlue
```

---

### 🚀 **3. Pushing Changes in Submodules and Sub-Submodules**

#### 🧬 **For Submodules:**
```bash
cd utils/image-matching_models

# Create a feature branch
git checkout -b feature/mask

# Stage, commit, and push changes
git add .
git commit -m "Update submodule with feature/mask changes"
git push origin feature/mask
```

#### 🧬 **For Sub-Submodules:**
```bash
cd matching/third_party/LightGlue

# Make sure the remote points to your fork
git remote set-url origin https://github.com/ipastore/LightGlue.git

# Create and push the feature branch
git checkout -b feature/lightglue_mask
git push origin feature/lightglue_mask
```

---

### 🛠️ **4. Handling Detached HEAD in Submodules**

```bash
# Create a new branch from a detached HEAD state
git checkout -b feature/branch_name

# Push and set upstream
git push --set-upstream origin feature/branch_name
```

---

### 🧹 **5. Cleaning Up Untracked Files and Directories**

```bash
# Forcefully clean up untracked files
cd matching/third_party/Steerers
sudo rm -rf DeDoDe

git clean -fdx
git reset --hard
```

---

### 🔗 **6. Resetting Submodules to Correct Commits**

```bash
# Reset all submodules to the expected commit
cd ~/colon_matching/utils/image-matching_models
git submodule update --init --recursive --force
```

---

### 🧠 **7. Best Practices for Submodule Management**

- Always **create feature branches** for **submodules** and **sub-submodules**.
- Avoid **detached HEAD states** by **setting upstream branches**.
- Use **HTTPS instead of SSH** if **permission issues arise**.
- **Commit submodule pointers** in the **parent repository** after updating submodules.
- **Avoid automatic updates** of **read-only submodules** by using **specific submodule updates**.

---

### 🎯 **8. Useful Git Commands for Submodules**

```bash
# Add and commit submodule updates
git add utils/image-matching_models

git commit -m "Update submodule reference"
git push origin feature/mask

# Check the status of all submodules
git submodule status --recursive

# Clean up local git environment
git fetch --prune
git remote prune origin
```

---

## 🚦 **Summary**
- Use **feature branches** in all **repositories**.
- **Avoid detached HEAD states** by **setting upstream branches**.
- Use **forceful cleanup** for **persistent untracked files**.
- When **pulling updates**, specify **exact submodules** if needed.
- Maintain a **clean git status** by regularly **committing changes**.


By following these **best practices** and **commands**, you can **efficiently manage nested submodules** and **avoid common pitfalls** in **complex Git repositories**.

