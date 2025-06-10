---
{"dg-publish":true,"permalink":"/garden/git-worktree/","tags":["insight","git"]}
---

# Git Worktree

I have the main branch which is `develop` checkout in the main repository.
And I have added multiple working trees, 3 for feature development, 1 for reviews, 1 for refactoring and 1 for my experiments. 

```bash
❯ git worktree list
/Users/shubham.kumar/Projects/GreyOrange/mvts/vrp-obts-rec  f3d4d139 [develop]
/Users/shubham.kumar/Projects/GreyOrange/mvts/experiments   f51d0c4a [experiments]
/Users/shubham.kumar/Projects/GreyOrange/mvts/feature1      d5e909aa [GM-227928-changes]
/Users/shubham.kumar/Projects/GreyOrange/mvts/feature2      30b9f1d4 [GM-220725-more-changes]
/Users/shubham.kumar/Projects/GreyOrange/mvts/feature3      10247d47 [GM-223556]
/Users/shubham.kumar/Projects/GreyOrange/mvts/refactor      f5a1b0ff [refactorbulk]
/Users/shubham.kumar/Projects/GreyOrange/mvts/review        f3d4d139 [review]

```

## Rename worktree
I tend to rename my feature worktrees so that I can easily identify the feature I'm working with.
There isn't any worktree rename command, you can use move command instead.

```bash
git worktree move ../feature1 GithubActionCommitMsg
```

## Add new worktree
```bash
git worktree add ../<worktreename> <branchname>
```

## Remove a worktree
```
git worktree remove <worktreename>
```

## Call worktree from root directory
I do not want to get inside the main repo or any other features repo to call the git command. 
For this, I have created a bash function that works as an alias to git and checks if I'm in the mvts repo then call worktree from vrp-obts-rec directory.

> This is written in `.zshrc` as I use zsh, you can write this in `.bashrc` if you use bash or other rc files of your respective terminal

```bash
function git() {
  if [[ "$PWD" == "/Users/shubham.kumar/Projects/GreyOrange/mvts" ]]; then
    command git --git-dir=vrp-obts-rec/.git "$@"
  else
    command git "$@"
  fi
}
```