# Terminal Notes

## General

`open .` Open current folder in file explorer

## git

[Interactive Git cheatsheet](https://ndpsoftware.com/git-cheatsheet.html)

###

`git config --global core.editor "code --wait"` Configure git globally to use VS Code to write commit messages

`git commit` - Opens up IDE to write a commit message. First line: your subject line, < 50 characters, no need for end punctuation. Starting a new line will allow you to write a more detailed commit message

`git commit -m "<Commit message here>"`

### Branching

`git branch` See all current branches. Checked out one will have `*`

`git branch <branch_name>` Make new branch e.g. `git branch DEV`

`git checkout <branch_name>` - Checkout existing branch

`git checkout -b <branch_name>` - Make new branch and checkout in one command

`git merge <branch_name>` - Take changes from `<branch_name>` and add them to the branch that you're currently on

`git branch -d <branch_name>` Delete branch if it has already been merged into main (it's good to delete branches when you're done with them)

`git branch -D <branch_name>` Delete branch if it hasn't already been merged into main

`git push --delete origin <branch_name>` Delete branch from the remote repo on Github