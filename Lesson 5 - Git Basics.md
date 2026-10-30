### Version Control
A version controll is a system that records file or a set of files so you can recall on specific versions later.

[Read more about others version control](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)
### Short History
In the early years of developing Linux Kernal, changes to the project have to passed around using email mailing and archive file. In 2002, the project began to use a Distrubuted Version Control called BitKeeper.

In 2005, the relationship between community that developed the Linux Kernel and the company that developed BitKeeper broke down, causing the tool's to revoked from free-of-charge.  This create the Linux development community (esspecially Linus Torvalds) to develop their own tool based on some lession they learnt from BitKeeper. Not long after that (about 10 days), Git was born.

### What is Git?
Git is the distributed version control system. Nearly every developer in the world uses it to manage projects.

The way git work is they think data as a stream of snapshots. Everytime you commit, or save the state of your project, Git takes a picture of what all your files look like at that moment and stores a reference to that snapshot. To be efficient, if files have not changed, Git doesn't store the file again, just a link to the previous identical file it has already stored. 

![[Pasted image 20260830192314.png]]

Git has 3 main states that your file can be in: `modified`, `staged`, and `committed`:

- Modified means that you have changed the file but have not committed it to your database yet.

- Staged means that you have marked a modified file in its current version to go into your next commit snapshot.

- Committed means that the data is safely stored in your local database.

![[Pasted image 20260830195832.png]]

### First-Time Git setup

Git comes with a tool called `git config` that allow you to get and set configuration variables on how Git looks and operates. These can be store in 3 different places:
- `[path]/etc/gitconfig` file: Contains variables applied to every user on the system and all their repositories. To write or read that you can use `--system` when using `git config`
- `~/.gitconfig` or `~/.config/git/config` file: Values specific personally to the user. You can pass the option `--global` to config those file.
- `config` file in the Git directory (`.git/config`): Specific to only that single repository. You can force Git to read from and write to this file using `--local`

#### Setup your identity
The first thing to do is to let Git know who you are, which is username and email. The reason for this is Git uses this infomation for every commit and baked into the commits you start creating:

```
$ git config --global user.name "Your Username"
$ git config --global user.email "youremail@example.com"
```

You only do this once using `--global` and it'll apply to all repository within your user on that system. Most GUI tools will do this when you first run them.

#### Setup Editor
After the identity setup, you can configure the default text editor that will be used when Git needs you to type a message. If not setup, Git uses your system's default editors.

```
$ git config --global core.editor vim
```

#### Your default branch name
By default Git will create a branch called master when you create a new repository with git init. From Git version 2.28 onwards, you can set a different name for the initial branch.

To set main as the default branch name do:

```
$ git config --global init.defaultBranch main
```

#### Checking Your Settings
If you want to check your configuration settings, you can use the `git config --list` command to list all the settings Git can find at that point.

### Git basics
#### Getting a Git Repository
You typically obtain a Git repository in one of two ways:

1. You can take a local directory that is currently not under version control, and turn it into a Git repository.
2. You can clone an existing Git repository from elsewhere.
#### Initializing a Repository in an Existing Directory
From a project directory that is currently not under any version control, you can use `git init` to start controlling it with Git. After that you will get a new directory called `.git` that contain all the necessary repository files

Read more about the structure of `.git` [Here](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain#ch10-git-internals).

The `git status` command shows you the current state of your repo. It will tell you which files are untracked, staged, and committed.

To start the Git version-controlling, you can start using `git add` (also mean staging state) commands to specify the files you want to track, followed by a `git commit`

```
$ git add file.c
$ git commit -m "Initial Commit"
```

Run `git status` to check if those files no longer staged.

And that should be half of Git (atleast so). Half of your workflow as a developer will revolve around 3 simple commands if you're a solo developer. When working with others, you'll need to know about collaborating and storing your work on a remove server. Another stuff is fixing mistakes, rolling back changes, and another advanced topics.