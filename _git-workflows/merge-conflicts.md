---
# title: "Resolving Merge Conflicts"
layout: default
---
When git merges commits from two different branches or remote repositories, it applies the committed changes from both.  In many cases, this works seamlessly, but sometimes it results in _merge conflicts_.  A conflict occurs when the same line(s) in a file were changed in both branches, and git is unsure of which to use.

Git will do several things in this scenario:

1. It will report as output from that command that caused the conflict which file(s) in the repository contain conflicts, and

2. It will mark the conflicted sections of those files using a special format that shows the two versions of the code.

An example of such a marking is:

```
public void PrintSomething() {  
<<<<<<< HEAD
  if(testValue) {
=======
  if(otherTestValue) {
>>>>>>> some_branch
  Console.log("Something...");
}
```

Here, we see two conflicting versions of one line: `if(testValue) {` and `if(otherTestValue){`. Additionally, we see markers delimiting the conflicting sections: `<<<<<<< HEAD`, `=======`, and `>>>>>>> some_branch`.  We need to replace all of the code and delimiters with one final version of the code.  This could be the first option, the second option, or a combination of the two:

```
public void PrintSomething() {  
  if(testValue && otherTestValue) {
  Console.log("Something...");
}
```

We need to do this for all conflicts in all conflicting files.  Once they have all been resolved, we need to commit the changes with the command:

```
$ git commit -a -m "Fixed merge conflicts"
```
