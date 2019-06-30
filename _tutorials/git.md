---
name: Git
tags: [Version Control]
style: fill
color: primary
description: Tutorial which covers git fundamentals.
---

# Git

## Git Areas

>"Git has three main states that your files can reside in: committed, modified, and staged."

All files can be found on your working tree area and you can see them by running `git status`. Git will start tracking files when they are staged or added to the staging area, and to do so, you need to run `git add <file or directory name>`.

Now if you track new files or make changes on the already tracked ones, you need to somehow move them to your local repository. The git command `git commit -m "message"` takes all changes in the Staging Area, wraps them together and puts them in your Local Repository. 

## Essential git commands every developer should know

1. `git init`: creates an empty git repo, which is a _.git_ directory with subdirectories for objects. _HEAD_ is also created to point to the _HEAD_ of the _master_ branch.
2. `git add <file or directory name>`: adds files to the staging area. To commit changes, firstly files need to be added to staging.
3. `git commit -m "message"`: moves tracked files from staging to your local repository. 
4. `git status`: non tracked and modified files will be displayed.

## .gitignore

>"A gitignore file specifies intentionally untracked files that Git should ignore. Files already tracked by Git are not affected"

You can specify patterns to ignore a particular folder or set of files.

.gitignore file template
{% gist 11f72e06a93ac0bc08da0059cf49f033 .gitignore %}
