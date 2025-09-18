# Show .git (excluded folder from being seen)

1. Open Preferences: Workspace Settings (JSON).
2. Within settings edit: 
```json
   	  "settings": {
    "files.exclude": {
      "**/.git": false
    }
  }
```

# See differences between branches, etc

1. Download Git Lens extension
2. Now source control has a menu for GITLENS
3. Inside GITLENS there is a button to compare the branches: open the branch and you´ll see it
4. The button are two arrows closing like a square