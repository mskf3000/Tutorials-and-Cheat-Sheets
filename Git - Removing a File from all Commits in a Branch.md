# Cheat Sheet - Git - Removing a File from all Commits in a Branch

By Jack Szwergold, September 15, 2015

#### How to remove a file from every commit in a branch.

This will effectively rewrite every commit to remove the `markdown/things/index.md`.

    git filter-branch --tree-filter 'rm -f markdown/things/index.md' HEAD

Now push that commit back to GitHub.

    git push -f

***

*Cheat Sheet - Git - Removing a File from all Commits in a Branch (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
