# Git - Dealing With Tags

By Jack Szwergold, September 15, 2015

***

#### Simple introduction to how tags work in git.

List all local tags.

    git tag -l

Delete a local tag.

    git tag -d 1.0.6

Delete a remote tag.

    git push origin :refs/tags/1.0.6

Create a tag.

    git tag -a 1.0.6 -m "Verison 1.0.6: Adjusting to do cool things."

Push tags to origin.

    git push --tags origin

Checkout a specific tag:

    git checkout tags/1.0.6

***

*Git - Dealing With Tags (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*