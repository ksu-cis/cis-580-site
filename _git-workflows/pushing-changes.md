---
# title: "Pushing Changes"
layout: default
---
To make your changes in a local repository available to other local repositories, or to turn in your homework, you will need to push those changes to the _origin_ remote repository (which is typically your GithHub repository).

## Step 1 - Checking the Status
Before you do so, you will want to make sure that you have [committed any changes]({{site.baseurl}}{% link _git-workflows/committing-changes.md %}) to your local repository.  An easy way to do this is the command:

```
$ git status
```

Which will list any uncommitted changes.  If there are none, you are ready for step 2.

## Step 2 - Pulling Changes from Origin
If there are new commits on the _origin_ repository (either because you made changes in another local repository and pushed them, or a team member did so), you want to make sure that you merge them into your code first, so that any merge conflicts occur locally and can be fixed there.  This can be done with the command:

```
$ git pull origin master
```

If there are any merge conflicts, the output from this command will tell you what files they are in.  If there are, you'll need to resolve them (step 3).  If not, you can skip to step 4.

## Step 3 - Fix Merge Conflicts
If there were merge conflicts in step 2, you'll need to [resolve them]({{site.baseurl}}{% link _git-workflows/merge-conflicts.md %}) before proceeding to step 4.

## Step 4 - Pushing your Changes
You can now push your changes to the remote _origin_ repository with the command:

```
$ git push origin master
```
