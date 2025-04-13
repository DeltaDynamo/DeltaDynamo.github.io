---
layout: post
title: "Mastering Git: Architecture, Essential Commands and When to Use Them"
slug: "mastering-git-architecture-essential-commands"
date: 2025-04-05
author: Anubhav Srivastava
tags: [git, version control]
---

Git today is probably the most important tool to master as a developer. It doesn't matter what tech stack we use, which side of the application we work on (be it frontend or backend), knowing Git is very important. In this post covers we will try to look at some of the essential Git commands along with their usage scenarios.

---

Git is a free and open source **distributed version control system (DVCS)** designed to track changes in source code efficiently. Let us talk a bit about the underlying architecture behind this awesome tool!

#### Core Components of Git Architecture

<div align="center">
  <img src="{{ '/assets/git-wkf-diagram.webp' | relative_url }}" alt="git-workflow-diagram" style="width:90%">
</div>

##### 1. **Working Directory**
The working directory contains the current state of the project, where developers modify, create, or delete files before adding them to the repository.

##### 2. **Staging Area (Index)**
A temporary storage area where files are prepared before committing. It allows selective commits, meaning developers can stage only specific changes rather than committing all modified files at once.

##### 3. **Local Repository**
A hidden `.git/` directory in the project folder that contains all commits, branches, and configurations. Git stores snapshots of changes here, allowing users to roll back if necessary.

##### 4. **Remote Repository**
A shared repository hosted on a remote server (e.g., GitHub, GitLab, Bitbucket). Developers push and pull changes to/from this repository to collaborate.

#### Git Object Model

Git stores data in the form of **objects** inside the `.git/objects` directory. These objects are immutable and content-addressable, meaning they are referenced by SHA-1 hashes.

##### Main Git Objects:
- **Blob (Binary Large Object):** Represents file contents.
- **Tree:** Represents directories and metadata (i.e., filenames, permissions).
- **Commit:** A snapshot of the repository at a given time, pointing to a tree object and the parent commit.
- **Tag:** A reference to a specific commit, often used for marking releases.

#### Git Workflow

1. User modifies files in the **Working Directory**.
2. Stage changes using `git add` (moves changes to the **Staging Area**).
3. User then commits changes with `git commit` (stores them in the **Local Repository**).
4. Push changes to the **Remote Repository** with `git push` for collaboration.
5. Pull the latest updates using `git pull` to sync with the remote.

#### Git's Distributed Nature

Unlike centralized version control systems, Git maintains a full copy of the repository locally, allowing:
- Faster operations since commits, diffs, and logs are accessed without a remote server.
- Offline work without depending on an internet connection.
- Easy branching and merging without affecting the main repository.

---

<h2 align="center">Git Commands</h2>

### 1. `git init`
##### Scenario: Starting a New Repository
While beginning a new project and to use Git for version control, we initialize a Git repository using `git init`.
```sh
git init
```

**What `git init` Does?**

When we run git init inside a directory, it creates a hidden `.git/` folder, which contains all the metadata for the repository. It sets up the necessary files and structures so that Git can start tracking changes. The `.git/` folder includes:

- **HEAD** – Points to the current branch reference.

- **config** – Repository-specific configuration settings.

- **description** – Used in Gitweb but not commonly modified.

- **hooks/** – Contains scripts that can run at different stages of Git operations (e.g., pre-commit, post-merge).

- **info/** – Contains the exclude file, which works like .gitignore but is specific to this repository.

- **objects/** – Stores all Git objects (commits, trees, blobs).

- **refs/** – Stores references to commits, such as branches and tags.

This structure allows Git to manage and track the project's history efficiently. 

---

### 2. `git clone`
##### Scenario: Copying an Existing Repository
To collaborate on an existing project which might be hosted remotely, in order to clone the repository to your local machine we use this command.
```sh
git clone <repository_url>
```

---

### 3. `git add`
##### Scenario: Staging Changes Before Committing
Before committing changes, to move the modified or newly created files to the staging area.
```sh
git add <file> #Add individual files
git add . #Add all files
```

---

### 4. `git commit`
##### Scenario: Saving Changes with a Message
After staging changes, commit them with a meaningful message. It records the changes in Git history.
```sh
git commit -m "Added feature X"
```
---

### 5. `git status`
##### Scenario: Checking the Current Repository State
To see which files are staged, modified, or untracked.
```sh
git status
```
---

### 6. `git log`
##### Scenario: Viewing Commit History
To see past commits along with author and timestamp details.
```sh
git log
```
---

### 7. `git branch`
##### Scenario: Managing Branches
To create a new branch or list existing branches.
```sh
git branch <branch_name> #Create a new branch
git branch #List all branches
```
---

### 8. `git checkout`
##### Scenario: Switching Between Branches
To move to a different branch.
```sh
git checkout <branch_name>
```

---

### 9. `git merge`
##### Scenario: Merging Changes from Another Branch
To combine changes from another branch into the current branch.
```sh
git merge <branch_name>
```
---

### 10. `git rebase`
##### Scenario: Rewriting Commit History
To update a branch by moving its changes on top of another branch. Keeps a linear project history
```sh
git rebase <branch_name>
```
---

### 11. `git stash`
#### Scenario: Saving Changes Temporarily
To save changes without committing them. Allows temporary storage of changes before switching branches.
```sh
git stash
```

---

### 12. `git reset`
#### Scenario: Undoing Changes
To unstage changes or reset commit history based on selected reset mode.
```sh
git reset <file>
git reset --hard <commit_hash>
```

---

### 13. `git revert`
#### Scenario: Reverting a Commit
To undo a commit by creating a new commit.
```sh
git revert <commit_hash>
```

---

### 14. `git cherry-pick`
#### Scenario: Applying a Specific Commit
To apply a commit from one branch into another.
```sh
git cherry-pick <commit_hash>
```

---

### 15. `git fetch`
#### Scenario: Retrieving Updates Without Merging
To get the latest changes from the remote repository. This command updates local metadata without modifying working files
```sh
git fetch
```

---

### 16. `git pull`
#### Scenario: Getting the Latest Changes from Remote
To fetch and merge changes from the remote repository. This synchronizes local code with the latest remote changes
```sh
git pull
```

---

### 17. `git push`
#### Scenario: Uploading Local Changes to Remote
To send local commits to the remote repository.
```sh
git push origin <branch_name>
```

---

### 18. `git tag`
#### Scenario: Marking Important Points in History
To create a tag for a specific commit. It helps to add labels to significant versions like releases.
```sh
git tag <tag_name>
```

---

### 19. `git bisect`
#### Scenario: Finding a Buggy Commit
To perform a binary search to locate the commit that introduced a bug. It helps to efficiently identify faulty commits in large projects.
```sh
git bisect start
git bisect bad
git bisect good <commit_hash>
```

---

### 20. `git blame`
#### Scenario: Identifying Who Made a Change
To find out who last modified each line in a file. Helps track changes for debugging and code review.
```sh
git blame <file>
```

---

### Conclusion

These are the Git commands I believe are most important and frequently used by developers in their daily workflow. 
While Git offers a wide range of powerful commands beyond what's covered here, this list should well serve as a solid foundation. 
For a deeper dive into more advanced commands and features, I highly recommend checking out the [official Git documentation](https://git-scm.com/docs).


