### Git Internals
Git have it own way to store and manage commits, specifically hashing. Git uses commit message, author's name and email, date and time, previous commit hashes to hash a new one, meaning there are low change of getting the same hash. ([SHA-1](https://en.wikipedia.org/wiki/SHA-1))

#### The Plumbing
Most of the git command you use everyday are called "Porcelain" which is user friendly and easy to use, while "Plumbing" is a low-level machinery that hard to use directly but stable.

#### All Of It Are Just Files Down There
The data git store are all inside hidden directory `.git`. That includes all the commits, branches, tags and other objects.

Git is made up of `objects` that are stored in the `.git/objects` directory.

Get your last 10 commits
```
$ git log -n 10
```

See what inside `.git/objects`
```
$ ls -l .git/objects
```

Find directory that match the first 2 characters of your commit hash the use this command to see (Replace XX with your 2 characters).
```
$ ls -al .git/objects/XX
```

Try to `cat` and see the contents of the commit objects file.
```
$ cat <path-to-file>
```

Well it's really a mess there.

Now try again with `xxd` command for "better" view
```
$ xxd <path-to-file>
```

That seem "better" but still unreadable for normal human. Luckily Git has a built-in plumbing command to view contents of a commit called `cat-file`
```
$ git cat-file -p <hash>
```

#### Tree and Blobs
Two notable thing to know before reading are `tree` and `blob`:
- `tree`: git's way of storing a directory
- `blob`: git's way of storing a file

After all that we can actually read what inside an object file.
```
tree f559ddbe0d716fefe5ed5d154da0eb9315ca132c
parent b09dc949edab7c1679f141fd5c0547e1c5fc891c
author your-name <example123@gmail.com> 1789295413 +0700
committer your-name <example123@gmail.com> 1789295413 +0700

A: add commit
```

Here we can see:
- The `tree` object
- The `parent` (This appear when there're more than one commit)
- The `author`
- The `committer`
- The commit message

### Branching
A Git Branch allows you to keep track of different changes separately without changing the main one.

For example, imagine you have a very big project and you want to experiment with changing a new font. Instead of touching the entire project directly which might have a chance to make it broke, you can create a new branch called `font` and work on that branch. When you're done, if you like the changes, you can merge the `font` branch back in the main one. If you don't like it, you can simply delete the `font` branch and go back to the main one.

#### How does it work?
A branch is just a named `pointer` to a specific commit. When you create a new branch, you are creating a new pointer to that specific commit. The commit that the branch point to is called the tip of the branch or head of the branch

#### Three Ways to Create a Branch
```
$ git branch my_new_branch
```
This command will create a new branch without switching to it.

```
$ git switch -c my_new_branch
```
The `switch` command allow you to switch to another branch, but here using `-c` will create a new one then switch to it.

```
$ git checkout -b my_new_branch
```
This command actually similar to `switch`, it create and then switch, but `checkout` is a legacy way for this but it have more purpose than managing branches.

### Merge
Imagine you have two branches, each have their own unique commits:
```
A - B - C    main
   \
    D - E    other_branch
```

If you merge `other_branch` into main, Git combines both branches by creating a new commit that has both commits history as parents. As diagram below, `F` is a merge commit that has `C` and `E` as parents. `F` brings all the changes from `D` and `E` back into the `main` branch
```
A - B - C - F    main
   \     /
    D - E        other_branch
```

### Rebase
A **Rebase** is a way we merge a branch into our branch without a `Merge Commit`.

For example, We have a branch called `feature-1` that need to be updated with the latest commit from `main`. Normally we'd use `merge` to bring those changes into our branch. But for `Rebase`, it simply replays the commits from `feature-1` on top of `main`.

#### Warning
You should never rebase a public branch (like main) onto anything else. Other developers have it checked out, and if you change its history, you'll cause a lot of problems for them.