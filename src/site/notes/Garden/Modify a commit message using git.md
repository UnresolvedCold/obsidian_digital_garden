---
{"dg-publish":true,"permalink":"/garden/modify-a-commit-message-using-git/","tags":["compilation","git"]}
---

# Modify a commit message using git 

Let's say you have a feature branch cut from main branch. 
And you made multiple commits. 
Now you want to modify some of the commits that you made earlier.

```bash
git rebase -i main
```

This will open a window with all your commits. 
If you want to modify a certain commit, just update `pick` to `reword` and continue. 
In the next screen git will ask you to update the commit.

> Caution: This will change your history and make your branch divergent 