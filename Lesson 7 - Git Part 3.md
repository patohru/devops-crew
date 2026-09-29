#### Reset
The main benefits of using git is ability to undo changes. While there are many different ways to do this, but we will be focus on `git reset`.

The `git reset` command can be used to undo the last commit or any changes in the index (staged but not committed changes)

```
$ git reset --soft COMMITHASH
```

The `--soft` option is useful if you just want to go back to previous commit without discard the changes. Committed changes will be uncommitted and staged, while uncommitted changes will remain staged or unstaged as before

```
$ git reset --hard COMMITHASH
```

The `--hard` will makes your working directory to match the specified commit exactly, meaning it will discarding any local changes. However if you used this command to undo committing a file, you would lost the file for good.

#### Remote
As we know, Git is a distributed version control system. We can have "remotes", which are just external repos with mostly the same as local Git repo history.

First create a sample repo
```
$ mkdir <sample-repo>
```

On sample repo add new remote
```
$ git remote add <name> <uri>
```

Here we name it as `origin` and `uri` is the path to the original project (You need to use `../project-name` instead of absolute path like `/home/user/...`).

Before we do something else, check what this repo have in `.git/objects`
```
$ find .git/objects
```
Since it empty we will only see 3 entries.

Next to bring the remote of original repo info into our sample repo, we have to `fetch` it.

```
$ git fetch
```
This will downloads the objects information needed to complete the fetched branches histories. Then run the `find .git/objects` again.

But that's only the metadata from remote original repo doesn't mean we have all of the files. To check, run the `git log` inside sample repo. You should see that you don't have any commits.

However, you can log the remote repo by using this
```
$ git log <remote>/<branch>
```

With that we can use the `git merge` to merge the remote repo into our local sample one.

```
$ git merge <remove>/<branch>
```