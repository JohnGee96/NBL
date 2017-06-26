#Git Nota Bene List

## Git Controls
  - list files in git folder
    
        git ls-files
    
  - Create an empty "orphan" branch with a separate file tree

        // rename the master branch
        git branch -m master old_master
        git checkout --orphan master
        // remove any cached file in the git folder
        git rm --cached -r .
        git add ./README.md
        git commit -m "blank master"
        // then push
      
   - Undo a git add

        git reset filename.txt