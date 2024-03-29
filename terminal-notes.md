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

## git merge and git rebase
The two utilites that integrate changes from one branch to another.

Merge is a forward moving change record.
- creates a merge commit
- non destructive operation (existing branches not changed in any way)
- can pollute your feature branch history if `main` is very active


Rebase has powerful history rewriting features.
- moves the ENTIRE feature branch to begin on the tip of the `main` branch, effectively incorporating all the new commits in `main`.
- this re-writes the project history by creating brand new commits for each commit in the original branch
- gives you a much cleaner, linear project history
- requires a lot of care; can potentially be catastrophic for your collaboration workflow
- also (less importantly) loses the context provided by a merge commit, you can't see when upstream changes were incorporated into the feature

## Output to the terminal with Node

Use `node --version` to see if you have Node in your directory and what ver.

To run any console.logs, use `node [filenamehere]` e.g. `node script.js`

To make a [custom console instance](https://stackoverflow.com/questions/63780704/how-do-i-use-console-table-but-to-stderr): 

    const { Console } = require("console");
    const myConsole = new Console(process.stderr);

Perhaps this is for working with errors in more detail.

### Monitoring a file for changes (watch)

From [this article](https://stackoverflow.com/questions/9724139/is-there-a-way-change-a-node-js-while-its-running)

- returns a Watcher object used for tracking any changes in the file.

Syntax:

    fs.watch( filename[, options][, listener] )
    fs.watchFile(filename[, options], listener)

    let fs = require('fs');
    let file = 'file.js';

    let script;
    function loadScript() {
    if (script) {
        if (typeof script.teardown === 'function') {
        script.teardown();
        }
        delete require.cache[file];
    }

    script = require(file);
    }

    fs.watch(file, function(event, filename) {
    if (event !== 'change') return;
    loadScript();
    });

    loadScript();

## http-server

`http-server` - start the server

`http-server --cors` - start the server with CORS enabled (to allow fetching)

`watch-http-server` - additional npm package that allows http-server to watch for changes.

`watch-http-server -c-1`

## NPM

`npm uninstall [package-name]`

`npm list` show all packages installed in current directory