---
# title: "Pulling from Origin"
layout: default
---
If you have pushed changes from a local repository to your remote _origin_ repository (typically on GitHub), and you want to merge those changes into a different local repository (for example, you made changes in the lab and [pushed them to the origin]({{site.baseurl}}{% link _git-workflows/pushing-changes.md %}), and now you are on your home machine and want those changes), you will need to pull them.

## Step 1 - Pull from the Origin Repository
Since the _origin_ repository was set when you cloned the local repository, all you need to do is push with the command:

```
$ git pull origin master
```

or

```
$ git pull
```

(Which defaults to pulling from the `master` branch of the remote repository named `origin`).

This merges the origin master branch into the local repository.  As with any merge, this may result in _merge conflicts_, which will need to be [resolved]({{site.baseurl}}{% link _git-workflows/merge-conflicts.md %}).
