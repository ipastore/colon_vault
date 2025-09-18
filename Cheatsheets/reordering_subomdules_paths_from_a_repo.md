You’ve touched on a really important lesson here 👌 — **submodules are fragile**, and moving them around (`utils/...` → `tools/...`) breaks a lot of internal Git bookkeeping. That’s why you hit so many errors.

Let’s go through how to do it cleanly the next time you want to **reorder or rename submodules**.

---

## 🔹 Why renaming folders breaks things

Each submodule is tracked in 3 places:

1. **`.gitmodules`** → maps the _path_ and _URL_.
2. **`.git/config`** → local config with submodule URLs.
3. **`.git/modules/...`** → actual submodule repo storage (local).
4. The parent repo’s **index** → records the submodule’s path + commit.

If you just `mv utils/image-matching_models tools/image-matching_models`,

- `.gitmodules` still points to `utils/...`.
- `.git/config` still has `utils/...`.    
- `.git/modules/utils/...` still exists, but the pointer inside the new folder is wrong.  
    → Git gets totally confused.
---
## 🔹 Safe way to reorder a submodule

Let’s say you want to move `utils/image-matching_models` → `tools/image-matching_models`.
### Step 1. Deinit the submodule (cleanly)
This tells Git: “forget about this submodule, but keep the files if I want.”

```bash
git submodule deinit -f utils/image-matching_models
```

### Step 2. Remove the old submodule path

```bash
git rm -f utils/image-matching_models
```

This removes the gitlink from the index.

Then cleanup:

```bash
rm -rf .git/modules/utils/image-matching_models
```

### Step 3. Add the submodule back at the new path

```bash
git submodule add https://github.com/ipastore/image-matching-models.git tools/image-matching_models
```

### Step 4. Commit the change

```bash
git add .gitmodules
git commit -m "Move image-matching_models submodule from utils/ to tools/"
```

### Step 5. Sync and init

```bash
git submodule sync --recursive
git submodule update --init --recursive
```

---

## 🔹 If you want to reorder multiple submodules

Repeat the **remove → add at new path** process for each.  
This guarantees Git doesn’t carry stale `.git` pointers around.

---

## 🔹 So, is “remove and reinstall” safer?

✅ Yes. It is the _only clean way_.

- Removing (`git rm -f`, `git submodule deinit`, cleanup `.git/modules/...`)
    
- Then re-adding with `git submodule add` at the new path
    

→ ensures `.gitmodules`, `.git/config`, `.git/modules/...`, and the index all agree.

Trying to “mv” folders manually is guaranteed to leave ghosts like the ones you fought with (`utils/...` pointer).

---

## ✅ Summary

Next time you want to reorder:

1. `git submodule deinit -f oldpath`
    
2. `git rm -f oldpath`
    
3. `rm -rf .git/modules/oldpath`
    
4. `git submodule add <url> newpath`
    
5. Commit + sync + update
    

That way everything is consistent.

---

👉 Do you want me to prepare a **generic script** that takes `oldpath` and `newpath` as arguments and does this whole process for you automatically?