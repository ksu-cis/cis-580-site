---
layout: page
# title: "Create a Release"
---
When you are ready to turn in an assignment, you will need to create a _release tag_.  A _tag_ is nothing more than a specially named commit, and a _release_ is a special tag created on GitHub to mark a specific version of the software.

## Step 1 - Make sure _All_ your code is on GitHub
Since releases are created on GitHub, it is important to make sure you've [committed your changes]({{site.baseurl}}{% link _git-workflows/committing-changes.md %}) and [pushed them to origin]({{site.baseurl}}{% link _git-workflows/pushing-changes.md %}) before you create the release.  You can check that all changes have been committed and pushed with the command:

```
$ git status
```

If you see these messages:

```
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

Then you are good to go.  On the other hand, if you get the message `Your branch is # commits ahead of 'origin/master'`, then you need to [push to master]({{site.baseurl}}{% link _git-workflows/pushing-changes.md %}), and if any files are listed as uncommitted, you first need to [commit them]({{site.baseurl}}{% link _git-workflows/committing-changes.md %}).

## Step 2 - Navigate to the Releases on GitHub
Next, open your repository on Github.  Towards the top of the page you should see a tab named "releases".  Click it.

![Releases Link]({{site.baseurl}}/img/release.png)

This will load the releases page for your repository.  On it you will see a button "Draft New Release".  Click it.

![Draft New Release Button]({{site.baseurl}}/img/draft.png)

## Step 3 - Complete the Release Form
You will need to fill out the release form, specifically the version and title, and then click the "Publish Release" button.

![New Release Form]({{site.baseurl}}/img/form.png)

Releases use [_semantic versioning_](https://semver.org/), a numbering system that uses three numbers separated by periods (i.e. version 3.4.2).  The first number is the _major_ version - a change in this number indicates a major change in the associated software, i.e. a redesigned interface, a change in what methods are available, etc.  The second number is the _minor_ version.  It indicates small feature additions to the software.  Finally, the third number is the _patch_ version, and this one indicates a change that is typically a bug fix or security fix.  Each number _rolls over_ like the seconds and minutes on a clock when the next version number is increased, i.e. you would go from version 2.7.23 to 3.0.0, or 4.3.12 to 4.4.0.  For this project, each milestone should be treated as a minor release, and each new project as a major one.  

Releases also get a human-readable name.  For this class, you should use the assignment name as the release name, i.e. Menu Milestone 1 for your first release.

## Step 4 - Submit your Release URL on K-State Online
Once you have finished creating the release, GitHub should take you to the release page.  You can also navigate there by clicking the "releases" tab on your GitHub repository's landing page and then clicking the specific release.  Copy the URL of this page; it is what you will submit on K-State Online.

![Release URL]({{site.baseurl}}/img/release-url.png)
