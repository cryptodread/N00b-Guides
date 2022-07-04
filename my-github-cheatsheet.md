# Github is hard to use. This guide helped me when I could not remember what commands to use in different scenarios.

## How to remove a submodule and replace 
    Delete the relevant section from the .gitmodules file.
    Stage the .gitmodules changes git add .gitmodules
    Delete the relevant section from .git/config.
    Run git rm --cached path_to_submodule (no trailing slash).
    Run rm -rf .git/modules/path_to_submodule (no trailing slash).
    Commit git commit -m "Removed submodule "
    Delete the now untracked submodule files rm -rf path_to_submodule

## How to clean up a repo and start fresh:


## How to update the remote submodule 
git submodule update --remote

## How to delete a tag from a release 
git push --delete origin [version]

