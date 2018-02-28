---
layout: post
title:  "Using Git-SVN in an SVN Organization"
subtitle: "Subverting Subversion with Private Branches"
date:   2018-02-28 15:45:00 +0100
categories: projects
tags:
- git
- svn
- git-svn
- workflow
---


Git has many advantages over SVN, which are better documented elsewhere, but git's killer feature is cheap private branches. This becomes invaluable when working on multiple tickets, or when you want to quickly compare two versions of the code.
I've been doing this for a little more than a year and a half. This section will get you through setting up Git-SVN, and will give an overview of the workflow I've found to work under these conditions.

# Limitations

Eclipse, or any other tool for that matter, doesn't work well with [git-svn][git-svn]. That's why committing should be done from the command line - because the actual command is different: [`git svn dcommit`][dcommit].

# Getting Started

In the console, cd to a temp directory. Run the following command:

```
git svn init -T svn://<your SVN branch url>
```

This will fetch each commit from SVN and re-commit it to your local Git repo. It will take a lot of time; for comparison, it's taken me more than half a day on our 10-year-old codebase.
An alternative is to clone from an existing git-svn repo (if a colleague of yours has one), and then configure it so it pushes to the correct repository with the correct credentials.
I haven't explored that option personally, but it might be worth pursuing while waiting for the above command to complete.

Import as a new project into your IDE when done.

It's important to keep two projects at once at the beginning to make sure you can quickly undo anything that goes wrong.


# Workflow

Your master branch should only be used to communicate with svn. Do not develop features to this branch directly, or you risk a massive headache when a merge issue occurs.

Feature branches are branches named after their respective ~~Jira~~ `$(bug tracking)` tickets. For example, ticket `PROJ-100` becomes branch `proj/100`. Adding a slash `/` in the branch name causes that branch to
show hierarchically. This helps me with SourceForge which collapses the hierarchy and removes some clutter.

In order to push to SVN:

1. Create and switch to a new feature branch:
```
git checkout -b <branch name>
```
2. Commit your work
```
git commit -m "This could be done from your IDE"
```
3. Checkout the Master branch and pull recent changes from SVN:
```
git checkout master
git svn rebase
```
4. Go back to your feature branch, and sync it up with master
```
git checkout <branch name>
git rebase master
```
5. Test your feature with the new changes imported from master
6. Switch back to master and bring it up to speed with your feature branch
```
git checkout master
git merge <branch name>
```
7. Commit changes (use `--dry-run` if you're scared)
```
git svn dcommit
```	

A popular git workflow is to switch to master and merge the feature branch directly. This will merge the feature branch with master in a single commit and *lose all change history* - presumably your branches will have more than one change committed to them, so that history will be lost. Doing rebase-then-merge preserves commit history after the branch is merged back to master.

When git commits to SVN all your pending commits will look like they were made just _now_, rather than when they were actually committed. This is due to the centralized nature of SVN.

[dcommit]: https://git-scm.com/docs/git-svn#git-svn-emdcommitem
[git-svn]: https://git-scm.com/docs/git-svn
