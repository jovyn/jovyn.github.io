---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Git Tutorial"
subtitle: "Basic usage of Git for day to day tasks."
summary: "Basic usage of Git for day to day tasks."
authors: []
tags: []
categories: []
date: 2020-07-1T15:51:19+05:30
lastmod: 2020-07-1T15:51:19+05:30
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

**Credits -** These are my notes from Corey Schafer's Youtube series -  [Git Tutorials - by Corey Schafer.](https://www.youtube.com/playlist?list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx)

### **Fundamentals**


Git is a Distributed version control. ...Git has a local and remote repositories.
* ``` git  --version ```
* ``` git help <verb>  OR  git <verb> --help ```

Set Configs values :
* ``` git config --global user.name "Jon Doe" ```
* ``` git config --global user.email "jondoe@example.com" ```
* ``` git config --list ```   - all values will be listed


### **Scenario 1 : Existing Project Local** 

* ``` cd  /project_dir ```
* ```  git init ```    // Initializes a git repo .. creates a .git/ dir
* ```  git status ```   // Check status
* ```  touch .gitignore  ```  // .txt file to add files dirs to be ignored by git.
* ```  git add -A  ``` // add all files to staging area.
    * Eg: ```   git add .gitignore ```  // add .gitignore to the repo
* ```  git commit -m "<message>"  ``` // make changes to the local repo
* ```  git reset  ``` // remove files from staging
* ```  git log   ``` // log 

### **Scenario 2 : Remote Project Repo** 

* ``` git clone <url> <location_to_clone> ```  //clone remote repo to local system
* ``` git remote -v ``` //  view info about the remote repo
* ``` git branch -a ``` // list all branches in the repo.

**After making changes to the code, commit the changes locally before pushing**

* ``` git diff ``` // shows changes 
* ``` git status ``` // shows the modified files
* ``` git add -A ``` // add all files to staging 
* ``` git status ``` // shows files are ready to be committed
* ``` git commit -m "<message>" ```  // commit changes locally
* ``` git pull origin master  ``` // pull latest code (in case there are more people working on the repo)
* ``` git push origin master  ``` // push changes to master branch on remote.

**Note:Instead of working on the master branch, its recommended to create a branch of the feature to work on.** 

* ``` git branch -a ```  // list branches
* ``` git branch <branch_name> ``` // Create new branch
* ``` git checkout <branch_name> ``` // switch to <branch_name>
* ``` <make changes to the code> ```
* ``` git commit -m "<message>" ``` // commit changes to <branch_name>
* ``` git push -u origin <branch_name> ``` // push changes to <branch_name> on remote repo
 
**Now we need to Merge the changes in the custom branch to the master & then delete the custom branch.**

* ``` git checkout master ```  // switch to master branch
* ``` git  pull origin master ``` // pull the latest code
* ``` git branch  --merged  ``` // lists the branches that have been merged so far
* ``` git merge <branch_name> ``` // Merge changes from <branch_name> to master.
* ``` git push origin master ``` // push the merged changes to master.
* ``` git branch --merged ``` // check if the changes from <branch_name> have been merged.
* ``` git branch -d <branch_name> ``` // Deletes branch locally
* ``` git push origin --delete <branch_name> ``` // delete branch from remote repo.


### **Undoing Bad commits**

* ``` git status ``` 
* ```git diff ``` //see the code changes made

**1. In case we want to change the commit message** 

* ``` git commit -m "Wrong message"  ``` // user enter a wrong commit message
* ``` git log  ``` // check the message in logs
* ``` git commit --amend -m "Correct message" ``` // Amends the commit message
* ``` git log  ``` // double-check in logs

**2. In case of a committed file (Accidentally made changes to wrong branch)**
   
*Eg.  Changes committed to master instead of feature branch*

* ``` git log --stat ``` // git files changed within the commit
* ``` git checkout master ``` // switch to master.
* ``` git log ```// copy 5-6 chars of the commit hash
* ``` git checkout feature ``` // switch to feature branch
* ``` git cherry-pick <copied commit hash> ``` // copied above commit hash
* ``` git log ``` // commit from master is now in feature branch 
* ``` git checkout master ``` // go back to master branch
* ``` git reset ```


### **Git Reset**

1. Git Reset Soft:
* ``` git reset --soft <commit hash_str> ```
In this case the ``` 'git log'  ``` will no longer have the commit but in the 'git status' the changes will be as it is in the staging. So we wont lose any work.

1.   Git Reset (Default/Mixed) :
* ```  git reset <commit hash_str> ```
In this case the ``` 'git log' ``` will no longer have the commit just as ``` git --soft ``` but in this case the changes will move out of the staging area to the working area.

1. Git Reset  Hard:
* ``` git reset --hard <commit hash_Str> ```
In this case  modifications will be completely reverted. Except for the un-tracked files. 

 
1. To get rid of untracked files and directories :
* ```  git clean -df ```  // -d for dirs; f for force

**In case you run '' git reset --hard"  by mistake:**
* ```  git reflog ``` // shows commits in the order of when we last referenced them.
* ```  git checkout <commit hash_str> ``` // Checkout to the commit hash obtained from above reflog.
* ```  git branch backup ``` //create a backup of the previous rollback.                

*Git Garbage collector run once in 30 days and above retrieval will  work prior to the garbage collector running.* 


### Git Revert:  
Git revert un-does the changes and adds additional commits of the changes.
* ```  git revert <commit hash_str> ```

 
Git Stash :  [pending ] 



*P.S: Originally written on my [blogger](https://hacktheripper.blogspot.com/2020/07/notes-git-basics-git-commands.html)*  




 