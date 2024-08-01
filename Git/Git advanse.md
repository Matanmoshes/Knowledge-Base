----
![[Pasted image 20240801155322.png]]
## Git Installation
---

``apt install git -y

``ssh-key gen

``ssh-keygen -t rsa -b 4096 -C "user email"

``cat ~/.ssh/id_rsa.pub

``git config --global credential.helper store

``git clone <Repo URL>

>[!Note]
>Password for credential helper is a Personal Token that needs to be generated on the GitHub account, under Profile > Settings > Developer Settings > Personal Tokens

## Merging
---

Merging commits between branches:
``git merge 

``git config pull.rebase false

Merge to main to local
``git merge main

``Pull/Push:

``git pull origin main

``git commit -m "database"

``git add .

``git push

``git pull

## Branch
---

Create branch and checkout to it:
``git chackout -b <New branch>

 Create new branch: 
``git branch <New branch name>

Move to branch:
``git checkout <branch name> 

See branches in local and remote:
``git branch -a

Change branch name:
``git branch -m <old name> <new name>

## Reset/Restore
---
![[Pasted image 20240801155351.png]]

Resets staging area:
``git reset

Deletes staged files from branch/repo (current file): 
``git reset --hard HEAD

Deletes staged files from branch/repo (parent file):
``git reset --hard HEAD~1

Cancel commit:
``git reset --soft HEAD

## Other commands
---

Check current status (e.g. changes, branch, etc):
``git status 

Clone remote repo to local  workspace:
``git clone

Create git project:
``git init 

Shows commit changes flow:
``git log --online --graph

Show changes between commits:
``git diff 

Sync local and remote repositories (e.g. branches):
``git fetch

Abort merge:
``git merge --abort 

``git remote

``git revision

Show parent branch
``git show HEAD ~<Branch place in NUM>

``git reset