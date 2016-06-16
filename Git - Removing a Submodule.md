## Git - Removing a Submodule

By Jack Szwergold

### Removing a submodule from a git repository.

In this example, let’s remove the submodule `wordpress`. First, check the submodule status from the repository:

    git submodule status

Now, `deinit` the submodule from GIT:

	git submodule deinit wordpress

Now remove the actual `wordpress` directory:

	git rm -rf wordpress

And finally—if you have no need for any submodules in your repository—remove the `.gitmodules` file from the root of the repository:

	git rm .gitmodules

***

*Git - Removing a Submodule (c) by Jack Szwergold; written on September 15, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
