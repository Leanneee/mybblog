---
title: Git with a bad story or clean turnips correctly
permalink: / en / clean-your-dirty-git-history /
categories: ['Ruby', 'Tutorials']
tags: ['ruby', 'git']

---
When working in a team it is important to keep your projects clean, without unnecessary waste. And now, after the next git rm, git commit, you understand that your project is clean, but still there is a sediment in the form of the history of deleted files. It seems you have removed all unnecessary, and the project did not weigh less. At first, this does not seem scary and does not even get on your nerves, but over time, you realize that this is already impossible and start searching for these unfortunate files.
<! - more ->
To solve this problem at the moment there are 2 ways:

  * git filter-branch
  * BFG Repo-Cleaner

The first method is good because everything can be solved using git, and the second is good with simple and advanced settings.
The documentation for `filter-branch` can be read [here] [1]. I will tell you how to use BFG Repo-Cleaner for our task.



So, go to [off site] [2] and download the latest version of the program.

Take the `jar` file and throw in the home directory, or on the desktop.

Next we need to download our repository with a bad story.

We do it as usual, but with the key — myrror

```bash
$ git clone --mirror git@yourdomain.com:mydirtyrepo
```
In fact, we create a mirror copy of the repository from our server.

Let's get down to “cleansing” our repository.

If we know the names of the malicious files from which we need to permanently get rid of, we run the following command:

`` bash
$ java -jar bfg.jar --delete-files filename mydirtyrepo.git
`` `

where filename is the name of the file, information about which needs to be completely erased.

If you want to get rid of all the files that were ever in your project and weighed, for example, more than 20MB, then this is also quite simple:

```bash
$ bfg --strip-blobs-bigger-than 20M  mydirtyrepo.git
```

In addition, BFG Repo-Cleaner can delete folders, files by mask, as well as replace the contents of files in the entire history.

After everything unnecessary is deleted, it’s time to post the changes and push everything to the server.
To do this, we execute only 3 commands in the console:

`` bash
$ cd some-big-repo.git
$ git reflog expire --expire = now --all && git gc --prune = now --aggressive
$ git push
`` `

That's all. Keep your story clean and refrain from accidental promptings to push something extra.
As they say, check seven times, commit once.

 [1]: http://git-scm.com/docs/git-filter-branch
 [2]: https://rtyley.github.io/bfg-repo-cleaner/
