# Introduction to Git / GitHub

# What is Git?

Git is a distributed version control system (dvcs). A version control system is an application that allows programmers to manage changes to their projects over time. Here, the word distributed means that there is no central server where a project's source code and history live.

When cloning a Git project (making a copy on your local machine), you get a copy of the repository in it's current state and all of it's history going back to the very first commit.

Having the entire project stored locally on your computer makes for extremely fast operations on the repository because it doesn't have to communicate with a central server.


# Why Should I Use Git?

If you are the only one working on your project, you may ask yourself, "Why even bother with version control?". You may have a project that you have been working on where you take regular backups at points where your code has changed significantly.

One day you decide to start working on a new feature. You implement the feature and take a backup of the project. The next day you decide that the feature you just implemented isn't going to work out, so you remove it. You continue working on the project for several weeks, taking regular backups along the way. At some point you decide that the project is in a stable state and decide to remove the old backups that you created. Later, you remember the feature that you had worked on weeks previously and decide to give it a second chance. The problem, however, is that you've already removed the old backups from the project. Even if you had kept them, would you even be able to remember which backup it was that contained the feature that you had built and then abandoned?

This problem can be exponentially exacerbated when more people work on the project. It is inevitable that, at some point, someone will delete or overwrite a file that you may have been working on. Maybe you've been sending copies of the code back and forth to each other via email and you can use that to go and find an old backup of the codebase that has the feature you just lost. Best of luck.

These scenarios, and many others like them, are exactly the types of problems that using a version control system help us to solve or avoid altogether.


# Enter Git

The first step to using Git is to download and install it. If you are working on a Linux system, odds are that you already have Git. If not, however, it is typically available via your package manager by the name of `git` or `git-core`. If you are working on a Mac or Windows system, you can download Git from [git-scm](https://www.git-scm.com).

While GUI applications for working with Git abound, the most efficient way of working with Git is from the command line. If you aren't familiar with, or are intimidated by the command line, don't worry, this will be a gentle introduction.

If the CLI isn't your thing, or you are interested in working with Git in a GUI environment, check out some of these desktop clients:

- [GitKraken](https://www.gitkraken.com/) - *Linux, Mac, Windows*
- [Sourcetree](https://www.sourcetreeapp.com/) - *Mac, Windows*
- [GitHub Desktop](https://desktop.github.com/) - *Mac, Windows*
- [Tortoise Git](https://tortoisegit.org/download/) - *Windows*


# Getting Started

Once you have installed Git, the first thing you should do is add your name and email to Git's global configuration file. This information will be added to each commit that you make and make it easier to figure out who has created a particular commit when viewing the log.

```bash
$ git config --global user.name  "<first> <last>"
$ git config --global user.email "<email address>"
```

After configuring your name and email, use the terminal to navigate to, or create, the directory where you would like to store you Git repositories. This can be in your home folder, documents, or wherever you like.


## Create a new repository

Once you are inside of the folder where you will store your repositories, create a folder for your new repository and enter it, then create a new Git repository using the `git init` command.

```bash
$ mkdir my-project
$ cd my-project

$ git init
# Initialized empty Git repository in /Users/brycejech/Documents/GitHub/my-project/.git/
```

The `git init` command from above will initialize a new repository in the current directory. You can optionally specify the directory in which to create the new repository; when not specified, git uses the current working directory. After running the `git init` command, use the `ls -a` command to see the contents of the directory. You should see a new `.git` folder.

```bash
$ ls -a
# .	..	.git
```

The .git folder contains all of the information about your repository, it's history, object database, settings, and much more. Diving into this folder is outside of the scope of this talk, but if you would like to read more you can do so [here](http://gitready.com/advanced/2009/03/23/whats-inside-your-git-directory.html).

## Git Workflow

Before we dive in, it's important to learn about the different states that files can be in when working with Git. These states are `committed`, `modified`, and `staged`.

- `committed` means that the changes have been safely added to your local database.
- `modified` means that a file has been changed, but not yet added to the staging area.
- `staged` means that you have marked a modified file in its current state to go into your next commit.

These states are related to the three main areas of a Git project. The .git directory, the working tree, and the staging area (or index).

- The .git directory, as described earlier, is where Git stores the object database and all of the metadata about your project. This is the most important part of Git, without this folder, you don't have a Git project.
- The working tree is a checkout of one version of the project. In our case, this is a checkout of the `master` branch. This is your working directory where your projects current state lives.
- The staging area, or, more accurately, the index, is a file located in your .git directory. It stores information about what will go into your next commit.

The most basic Git workflow is as follows:

1. You make some changes to files in your working tree.
2. Using the `git add` command, you selectively stage the changes you want to be a part of your next commit. It is important to note that Git adds *only* those changes to the staging area.
3. You make a commit which takes the files as they are in the staging area and stores a snapshot to your Git directory.


## Git Status

Before we start making commits, let's use the `git status` command to view the current state of the repository.

```bash
$ git status

# On branch master

# No commits yet

# nothing to commit (create/copy files and use "git add" to track)
```

Git reports back to us that we are currently on the `master` branch (more on branches later), we haven't made any commits yet, and that we haven't `add`ed any files to commit yet.


## Making Commits

Now that we're all set up, let's create a file so that we can make our first commit.

```bash
$ echo 'Today I am learning about Git!' > index.txt
```

This will create a file named `index.txt` with the contents `Today I am learning about Git!`.

Let's do another status check.

```bash
$ git status
# On branch master

# No commits yet

# Untracked files:
#   (use "git add <file>..." to include in what will be committed)

# 	index.txt

# nothing added to commit but untracked files present (use "git add" to track)
```

Here, Git is telling us that we have an untracked file, `index.txt` and that we need to `add` it to the staging area before we can commit it.

Let's `add` the file to the staging area and do another status check.

```bash
$ git add index.txt

$ git status
# On branch master

# No commits yet

# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)

# 	new file:   index.txt
```

Git now reports that the file has been added to staging and will be part of our next commit.

*Here, we've specified a single file with the `git add` command. Note that you can specify multiple files or directories when using `add`.*

It's also important to note that the `add` command adds your changes to the staging **as they are**. Any further modifications to the file(s) will not be added to the next commit unless you explicitly add them back in. You could, in some cases, have the same file appear in the untracked changes section as well as the changes to be committed section. If it helps, think of the `add` command like saying, "add precisely these changes to the next commit", rather than adding a file or folder to the next commit.

Now, let's make our first commit and do yet another status check.

```bash
$ git commit -m 'add index.txt'
# [master (root-commit) 3b58485] add index.txt
#  1 file changed, 1 insertion(+)
#  create mode 100644 index.txt

$ git status
# On branch master
# nothing to commit, working tree clean
```

When using the `git commit` command above, note that we've given it an `-m` option. Here, `-m` stands for message. The string of text passed to the command after the `-m` flag will become our commit message.

All of your commits should not only have messages, but the messages should describe the change that occurred in that particular commit. Giving good, descriptive commit messages will save you lots of debugging time in the future. It will make it much easier to track down commits that may have introduced bugs or that you are otherwise trying to locate.

Commit messages should be short and concise. If you need to specify more explanatory text, you can pass multiple `-m` flags. The first message passed will be the commit message itself, and any additional messages passed will appear on separate lines when viewing the `git log` for the project.

When making the commit above, Git reports some information about the commit back to us. It gives us the branch name that the commit was made on, the hash, the commit message, and some data about what was changed.

After making the commit, when doing another status check, Git reports back that we are on our `master` branch, that there is nothing to commit, and that our working tree is clean.

### Bypassing the Staging Area

The staging area is very helpful in that allows us to carefully craft out commit messages and include changes/files selectively. However, sometimes it may be a bit more complex than we need for our workflow.

We can by bypass the staging area by including the `-a` flag with the `git commit` command. Using the `-a` flag will stage all changes to files that were already being tracked before making the commit.

```bash
$ git commit -a -m '<your commit message>'
```

Take special care when using the `-a` option however, as files that have not previously been `add`ed are not included.

## Basic Branching

Git allows us to create what are called branches. A branch is simply a symbolic reference to a single commit. By default, a Git repository will have a single branch called `master`.

A branch is similar to a tag, which is used for versioning, in that both are references to a single commit. However, branches differ in that they are designed to be updated, while a tag is usually not updated once it's been created.

When a branch is checked out, the working tree is updated to reflect the state of the repository for that commit. When making a commit on a branch, the reference, or, more appropriately, the pointer for the branch is updated to point to the result of that commit.

Branches are central to the development workflow of a Git project. They are used to isolate code while working on new features or bug fixes. Creating new branches allows us to work on a specific task independently, without affecting the main branch.

Once development on a branch is complete, it's changes are then merged into some other branch, such as `master`. Sometimes, changes in the branches being merged are incompatible, this is known as a merge conflict. We won't cover merge conflicts right now, just be aware that this can happen and that they must be manually resolved.

To view the branches for a repository, use the `git branch` command.

```bash
$ git branch
# * master
```

This repository only has a single branch, `master`, which is also the currently checked-out branch, denoted by the `*` character.

Let's create a new branch and switch to it using the `git checkout` command. To create a new branch, use the `git branch <branch name>` command.

```bash
$ git branch test-branch
$ git checkout test-branch
# Switched to branch 'test-branch'
```

Here, we've created a new branch called `test-branch` and switched to it. Let's see what the output of `git branch` is now.

```bash
$ git branch
# master
# * test-branch
```

Now we can see that we have 2 branches, `master` and `test-branch`, and that `test-branch` is the currently checked-out branch as denoted by the `*`.

Git also provides us a shortcut to create a new branch and switch to it in a single command, `git checkout -b <new branch name>`.

It's important to note that in most cases Git will not allow you to switch branches if you have any changes that have not been committed in your current branch. Before switching branches, you must either commit or stash your changes.

Let's create a new commit on the test-branch.

```bash
$ echo 'This file only exists in the test branch' > test.txt

$ git add test.txt
$ git commit -m 'add test.txt'
# [test-branch be55380] add test.txt
#  1 file changed, 1 insertion(+)
#  create mode 100644 test.txt

$ ls
# index.txt   test.txt
```

We've created a new file and committed it to our test branch. To illustrate that `git checkout` updates our working tree as described previously, let's switch back to our master branch and see what we have.

```bash
$ git checkout master
# Switched to branch 'master'

$ ls
# index.txt
```

As you can see, the `test.txt` file from our `test-branch` is gone.

In order to get the changes on our `test-branch` into our master branch, we will have to `merge` them in. We'll cover merging in more detail later.

Let's delete the `test-branch` for now, we'll talk more about branching and merging later on.

```bash
$ git branch -D test-branch
# Deleted branch test-branch (was 6921b4d).
```

Git tells us that we've deleted the branch and gave us the abbreviated hash that it used to point to.

Note that we specified the `-D` option to delete the branch. Normally, when deleting a branch the `-d` (lower case) option is used. However, since we chose not to merge in the changes from `test-branch` we have to "force delete" it with the capital `-D` option. Be careful when using the `-D` option as Git **will not warn you** before deleting the branch.


## Removing Files

To remove a file from a Git project, you have to explicitly remove it from your staging area and then commit. To do this, you can use the `git rm <file>` command. This will remove the file(s) from your filesystem and stage it's removal for the next commit.

```bash
$ echo 'I'\''am about to be removed!' > remove.txt
$ git add remove.txt
$ git commit -m 'add remove.txt'

# [master d7fa17a] add remove.txt
#  1 file changed, 1 insertion(+)
#  create mode 100644 remove.txt

$ git rm remove.txt
# rm 'remove.txt'

$ git commit -m 'rm remove.txt'
```

Note that the `git rm <file>` command is equivalent to removing the file and `add`ing it's removal to the staging area like so:

```bash
$ rm remove.txt
$ git add remove.txt
$ git commit -m 'rm remove.txt'
```

So, the `git rm` command just does a couple extra steps for us.

There may also be scenarios where you want to remove a file from the staging area, but keep it on your hard drive. You can pass it the `--cached` option to accomplish this.

```bash
$ git rm --cached remove.txt
$ git commit -m 'rm remove.txt'
```

The above would remove the file from staging and commit it's removal, but would not touch the file in your working tree. This is particularly useful for scenarios where you forgot to add a file to your .gitignore and accidentally staged it.

## Moving Files

While Git doesn't actually store any metadata about file movement, it is good at figuring out if a file has been moved after the fact.

Git comes with a `mv` command for moving, or renaming, files. Similarly to the `git rm` command, it does a couple operations for us. `git mv <from> <to>` will move the `<from>` file to the `<to>` file, and stage the changes.

```bash
$ echo 'I'\''m about to be moved!' > move.txt
$ git add move.txt
$ git commit -m 'add move.txt'
# [master c98471a] add move.txt
#  1 file changed, 1 insertion(+)
#  create mode 100644 move.txt

$ git mv move.txt moved.txt

$ git status

# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)

# 	  renamed:    move.txt -> moved.txt

$ git commit -m 'rename move.txt to moved.txt'
```

As you can see from the output in the changes to be committed section, Git has detected that the `move.txt` file has been renamed to `moved.txt`.

Note that the `git mv move.txt moved.txt` command from above is equivalent to renaming the file, staging the removal, and adding the new file

```bash
$ mv move.txt moved.txt
$ git rm move.txt
$ git add moved.txt
```

## Viewing Project History

Git comes with a `log` command for viewing the commit history of a project. By default, it will list out the commit history of the current branch in the order of newest to oldest commits. With no other options given, `git log` will list out the hash, author name and email, the date, and the commit message.

```bash
$ git log

# commit d92aeae2f7009b83492b3fa1dc73a00dfcd421e6 (HEAD -> master)
# Author: Bryce Jech <bryce@brycejech.com>
# Date:   Wed May 30 18:30:02 2018 -0500

#     rm remove.txt

# commit b77ba1ab0c902a19fa4b8230de3336898a646cbb
# Author: Bryce Jech <bryce@brycejech.com>
# Date:   Wed May 30 18:28:58 2018 -0500

#     rename move.txt to moved.txt

# commit c98471adc170b577ad4b09ad5ecd0e9d3c9b530e
# Author: Bryce Jech <bryce@brycejech.com>
# Date:   Tue May 29 21:27:53 2018 -0500

#     add move.txt

# commit d7fa17aca47baa467c1c7f89b953f5bbbe3ae66e
# Author: Bryce Jech <bryce@brycejech.com>
# Date:   Tue May 29 19:12:24 2018 -0500

#     add remove.txt

# commit a12c95926132e75db8ae220adc74c590232093f8
# Author: Bryce Jech <bryce@brycejech.com>
# Date:   Mon May 28 21:02:40 2018 -0500

#     add index.txt
```

`git log` pretty useful by itself, but this command comes with tons of options that make it very easy to find what you are looking for.

If you want to view less information about each commit, you can use the `--oneline` flag, which will output the abbreviated hash and the commit message. If you want to view single line output with the full hash you can use the `--pretty=oneline` option.

If these formatting flags aren't enough, you can pass Git your own custom formatter with the `--pretty=format:"<your format>"` option. There are tons of formatting options available for this one, check out [git-scm](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History) for all options.

You can limit the output by passing a `-<Number>` option. For example, if you only wanted to get the 3 most recent commits, the command would be as follows: `git log -3`.

The `-p` or `--patch` option will output the differences, or patch output, introduced by each commit.

The `--grep <pattern>` flag is useful for searching commit messages for a matched RegEx pattern.


# Uh-Oh

Ok, at this point we've learned just enough to be dangerous. Time to learn a little about how to recover from mistakes.

### I left a file out of my last commit
```bash
$ git add <file>
$ git commit --amend
# Follow prompt to update commit message if needed

# Do the same, but replay the last commit message (and other metadata)
$ git commit --amend -C HEAD

# Same, but just leave the commit message
$ git commit --amend --no-edit
```

### I need to update my last commit message
```bash
$ git commit --amend
# Follow prompt to update last commit message

# Shorter still
$ git commit --amend -m 'your new message'
```

### I've screwed up a file and want it back to it's last committed state
```bash
$ git checkout -- <file>
```

### I've lost a commit and need to get it back
```bash
$ git reflog
# will show everything you've done across branches with a
# HEAD@{index} for each move, find the index you want and...

$ git checkout HEAD@{index}

# This will put you in a detached head state, may want to create
# a tag or branch at this point

# If you are just wanting to go backwards...
$ git reset HEAD@{index}
```

### I've accidentally staged a file and want to unstage it
```bash
$ git reset HEAD <file>
```

### I need to undo the last commit but leave my changes
```bash
$ git reset --soft HEAD~
# Note that this will preserve your staging area.
# If you want to also reset the staging area just leave
# off the --soft flag (aka --mixed, the default)
```

### I meant to make that last commit on a new branch
```bash
$ git branch new-branch
$ git reset --hard HEAD~
$ git checkout new-branch
```

More examples at [Oh, Shit, Git!](http://ohshitgit.com)


# Working with Remotes

Now that we've learned some basics, it's time to start working with remote repositories. A remote, in Git terminology, is a reference to a version of your project that is hosted on a network location or somewhere on the internet, such as GitHub. Working with others on shared projects, such as an open source project, involves pushing and pulling data to and from these remote repositories.

A project may have any number of remote repositories. When dealing with open source projects on GitHub that are shared among collaborators, a local project will typically have at least one or two remote repositories configured.

Before we can really start working with remotes, we need to move out of our local repository. I've created a sandbox repository for us to play around with. Let's clone it. *Be sure to switch to your git repo directory before cloning*

```bash
$ cd ~/Documents/GitHub
$ git clone https://github.com/brycejech/git-sandbox.git
# Cloning into 'git-sandbox'...
# remote: Counting objects: 9, done.
# remote: Compressing objects: 100% (7/7), done.
# remote: Total 9 (delta 0), reused 0 (delta 0), pack-reused 0
# Unpacking objects: 100% (9/9), done.

# switch to our new repository's directory
$ cd git-sandbox
```

## Viewing Remote Repositories

To view the remote repositories that are configured for your project, use the `git remote` command.

```bash
$ git remote
# origin
```

If you have cloned a repository from somewhere else, you will almost certainly have a remote named `origin`. This is the default name given to a server that Git has cloned a repository from.

To also view the URLs that Git will use to read and write to and from a remote repository, use the `-v` option.

```bash
$ git remote -v
# origin	https://github.com/brycejech/git-sandbox.git (fetch)
# origin	https://github.com/brycejech/git-sandbox.git (push)
```


## Pushing to Remotes

After you have committed changes to your local repository, the next step is to push those changes up to your remote repository. To accomplish this, Git provides the `git push <remote> <branch>` command.

Let's make a commit and push it up to our `origin`.

```bash
$ cd sandbox
$ echo 'demonstrating our first push' > first-push.txt

$ git add first-push.txt
$ git commit -m 'git push is easy!'
# [master c0c077e] git push is easy!
#  1 file changed, 1 insertion(+)
#  create mode 100644 sandbox/first-push.txt

$ git push origin master
# Counting objects: 4, done.
# Delta compression using up to 8 threads.
# Compressing objects: 100% (3/3), done.
# Writing objects: 100% (4/4), 372 bytes | 372.00 KiB/s, done.
# Total 4 (delta 1), reused 0 (delta 0)
# remote: Resolving deltas: 100% (1/1), completed with 1 local object.
# To https://github.com/brycejech/git-sandbox.git
#    3dab12d..c0c077e  master -> master
```

In the above commands, we created a new file, committed it, and pushed it up to our origin repository on the master branch. Upon making the push, Git gives us some output about what happened.

### Git Credential Helper

Going back to the protocol discussion from earlier, using the http protocol does have one significant downside to consider, you must authenticate each time you `push` over http. This can be a real pain if you are frequently pushing data to one of your remote repositories.

Luckily, Git provides the ability to store your credentials a couple of different ways via the credential storage. Storage options are available using the `credential.helper` configuration key.

The `credential.helper` setting accepts the value `cache` and `store`. If you are on a Mac, you can also use the `osxkeychain`.

#### Examples

- `git config --global credential.helper cache`
    - Stores credentials for 15 minutes (default)


- `git config --global credential.helper 'cache --timeout=1800'`
    - Stores credentials for 30 minutes (1800 seconds)


- `git config --global credential.helper store`
    - Stores credentials indefinitely. **Caution** stores passwords in a plaintext file


- `git config --global credential.helper osxkeychain`
    - MacOS only. Stores credentials in encrypted format permanently in the osxkeychain

By default, Git does not consider the "path" component of an http URL to be worth matching via external helpers. This means that credentials stored for one repository will be used for any other repository. If you are having issues with credentials and multiple accounts, you may want to tell Git to use the full http path: `git config --global credential.useHttpPath true`


## Adding Remotes

The `git clone` command implicitly adds the `origin` remote for us. To explicitly add your own remotes, use the `git remote add <alias> <url>` command.

For example, if you had forked a copy of the FreeCodeCampOKC repository fccokc_web (more on forks later) and cloned a local copy of your fork, you may want to also configure an `upstream` remote to stay up to date with changes made to the original repository you forked from. In this scenario, your command would look something like this:

```bash
$ git remote add upstream https://github.com/FreeCodeCampOKC/fccokc_web.git
```

*Note that the above repository is not the same project we are working on currently. The above example is used simply to illustrate the use of the `git remote add` command.*

It is worth noting that Git and GitHub support a variety of protocols. In the above example we've specified the `http` protocol. Http is the most widely used protocol for working with remote repositories on GitHub. Git and GitHub also support the `git` and `ssh` protocols, and Git supports a `local` protocol as well.

Learn more about remote protocols on [git-scm](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)


## Fecthing and Merging Changes from a Remote

When working with remotes, Git will not allow us to `push` our changes if the remote branch has gotten ahead of our local branch. This means that any new commits that have occurred on the remote branch must be merged into our local branch before we can `push`.

The `git fetch <remote>` command tells Git to go and get data from a remote repository. You can optionally fetch data on a specific branch by specifying the branch you want to fetch after the remote name.

Fetch is used when we want to get updates from a remote repository/branch so that we can update a local branch with any changes on the remote.

`fetch` will **not** make any changes to your working tree. Once any new commits have been fetched from the remote, we merge them in to our working tree using the `git merge <remote>/<branch>` command.

```bash
$ git fetch origin

# Make sure we're on the right branch
$ git checkout master

# Merge changes on origin/master into our working tree
$ get merge origin/master
```

Git also provides a shortcut to `fetch` and `merge` simultaneously with the `git pull <remote>/<branch>` command.

```bash
$ git checkout master
$ git pull origin/master
```


# Exercise: Distributed Git

Now that we know how to work with remotes. Let's work through a more distributed workflow involving collaboration between a shared repository on GitHub. This workflow is how many open source libraries are maintained.

## Create a Fork on GitHub

We are first going to create a fork. In the context of GitHub, a fork is a remote repository that you own that is a copy of a remote repository owned by someone else. Your fork is automatically linked to the repository that it was copied from. We are going to create a fork of a repository on GitHub, then clone our remote fork down to our local machine.

Our GitHub hosted fork that we clone is what will be known as our `origin`, the original repository that we forked from is what is commonly called an `upstream` repository.

Go to <https://github.com/BryceJech/talks> and click the `Fork` button in the upper right-hand corner of the page. This will take you to your own GitHub account where the fork will be created. The URL you are taken to will look something like this: `https://github.com/<username>/talks`.

Note that under the repository name, there will be a reference to the original repository that the project was forked from.

## Clone Your Fork

Once on your fork's page, click the green `Clone or download` button and copy the url to your clipboard. The URL should look like this: `https://github.com/<username>/talks.git`.

Now, go back to your terminal, navigate to the folder you've created for holding your repositories, and clone the repository. Then, navigate to the repositories directory.

```bash
$ cd ~/Documents/GitHub

$ git clone https://github.com/OctoBryce/talks.git
# Cloning into 'talks'...
# remote: Counting objects: 114, done.
# remote: Compressing objects: 100% (72/72), done.
# remote: Total 114 (delta 38), reused 93 (delta 20), pack-reused 0
# Receiving objects: 100% (114/114), 5.37 MiB | 100.00 KiB/s, done.
# Resolving deltas: 100% (38/38), done.

$ cd talks
```

The reason for creating a fork is that we do not have write access to the original repository. Creating a fork allows us to have a copy of a repository that we can write to. This writeable repository that we own is where we will make our pull request from.

## Add an `upstream` Remote

Git has already configured an `origin` remote that refers to our fork on GitHub. Let's also create an `upstream` remote that refers to the original repository that we forked from. If needed, we'll use this `upstream` remote to fetch and merge any changes on the original repository.

```bash
$ git remote add upstream https://github.com/brycejech/talks.git

$ git remote -v
# origin	https://github.com/OctoBryce/talks.git (fetch)
# origin	https://github.com/OctoBryce/talks.git (push)
# upstream	https://github.com/brycejech/talks.git (fetch)
# upstream	https://github.com/brycejech/talks.git (push)
```

If the `upstream` repository get's ahead of our fork, we will have to merge in any changes from the upstream before we can have a pull request accepted. If this happens, use the `git fetch upstream` and `git merge upstream/master` commands from earlier. Then push the merge commit to your fork. Once you are caught up, your pull request should be able to fast-forward and will be more likely to be accepted.

## Create a Branch

In order to keep things tidy, let's make a branch. Normally, we'd make a branch to work on a feature or fix a bug. Here, we're going to create a branch called `survey` that we'll use to fill out a questionnaire.

```bash
$ git checkout -b survey
# Switched to a new branch 'survey'
```


## Complete the Survey

Now that our branch is created, navigate to the survey directory inside of the Intro-to-Git directory.

```bash
$ cd Intro-to-Git/survey
```

Then, copy the TEMPLATE.md file to a new filename. Name the file something that will not collide with other respondents, such as your username, and give it a `.md` file extension. For example, if your username is PinkUnicorn, name the file PinkUnicorn.md

```bash
$ cp TEMPLATE.md <your username>.md
```

Open your newly created markdown file and fill out the survey questions.

The first few survey questions are checkboxes, which, in markdown, look like this `- [ ] <question>`. In order to indicate a positive response (checked box), replace the space inside the square brackets with an x like this: `- [x] <question>`. The last few questions are free response.

Next, add the new file to your staging area, commit the change, and push it to your `origin` repository.

```bash
$ git add <new file>.md
$ git commit -m 'Complete survey'

$ git push origin survey
```

Since the `survey` branch did not exist on the `origin` repository, Git may have created it for you.


## Create a Pull Request

Now that we've pushed our change up to our `origin` repository, we need to create a pull request to have the change brought in to the original repository.

Go to your forks page on GitHub, switch to the `survey` branch, and click the `Create pull request` button.

On the pull request page, make sure that the base fork is set to the original repository that we originally forked (the upstream repo in our local project), that you are committing to the master branch, that the head fork is set to the fork you created, and that the compare branch is set to the survey branch that you created.

Then, give your pull request a descriptive title and press the `Create pull request` button.

That's it! Your pull request has been created!!

## Clean up

Once your pull request has been merged in, you can go ahead and delete the `survey` branch on GitHub as well as your local repository.

To delete the local branch, use the `git branch -D <branch name>` command from earlier.

```bash
$ git branch -D survey
# Deleted branch survey (was d4ed485).
```

To delete the remote branch, use the `git push -d origin <branch name>` command.

```bash
$ git push -d origin survey
# To https://github.com/OctoBryce/talks.git
# - [deleted]         survey
```

Alternatively, you can delete the remote branch like this.
```
$ git push origin :survey
```

# Final Thoughts

I hope that you learned a lot about working with Git today. Now, go out and practice, practice, practice!!!
