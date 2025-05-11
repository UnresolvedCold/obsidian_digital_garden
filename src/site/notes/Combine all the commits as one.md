---
{"dg-publish":true,"permalink":"/combine-all-the-commits-as-one/","tags":["insight"]}
---

#git #insight 
I was working on a feature branch which had hundreds of commits and it was time to finally rebase it to main branch.
As usual, I ran `git rebase origin/main` and there were conflicts. 
I spent some time cleared all the conflicts continued rebasing. 
I was presented with more conflicts and more conflicts. 
Each and every commit on the main branch had conflicts with each and every commit of my feature branch. 
One option was to merge the changes and resolve conflicts during merge but this will result in non-linear commit tree. 

Finally, I found a way.
Merge all the commit of my active feature branch and resolve all the rebase conflicts at once.
For this I tried the first way, squash all the commits.
```
# First start an interactive rebase 
git rebase -i develop

# This will open all the commits in a vim editor
# Replace all the pick to squash except the first one
# 2,$s/^pick/squash/
```

And I realised, there are conflicts within the active branch itself. 
And I had no intention of keeping all my commits history for this branch.
Instead of squashing, I did a soft reset of all the commits in the feature branch and created a single commit of all my changes. 

```
# Undo all the commits and keep your changes
git reset --soft $(git merge-base your-branch develop)

# Create a new commit with all the changes
git commit -m "Merged commit"
```

And finally there was just one conflict to be resolved. It took me around 10-15 mins to go through each changes and I was finally relieved. 
