## Git - Duplicating or Renaming a Repository

By Jack Szwergold

### How to duplicate and rename a repository.

First step is to create a new repo on GitHub.

Make a bare clone of the source repository.

    git clone --bare --recursive git@github.com:JackSzwergold/Memories.git

Go into that directory.

    cd Memories.git

Now push (via mirror) into the new repo.

    git push --mirror git@github.com:JackSzwergold/Writing.git

If any `git clone` copies s of the repo exist somewhere, change the origin URL this way.

    git remote set-url origin git@github.com:JackSzwergold/Writing.git

***

*Git - Duplicating or Renaming a Repository (c) by Jack Szwergold; written on September 15, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*