### Package Managers
Package managers install, update and remove software the way your distributions can track and maintain.
![[./images/package_repository.png]]
#### Why Package Managers?
- Help install dependencies.
- Easier to install things.
#### Core commands

| Task              | Command                     |
| ----------------- | --------------------------- |
| Refresh metadata  | `sudo dnf check-update`     |
| Upgrade system    | `sudo dnf upgrade`          |
| Install package   | `sudo dnf isntall pkg-name` |
| Remove package    | `sudo dnf remove pkg-name`  |
| Search package    | `dnf search pkg-name`       |
| Show package info | `dnf info pkg-name`:        |
You can also install local package file called ".rpm" on fedora if the official repository doesn't have it.
```
sudo dnf install ./package.rpm
```

### Permissions
```
drwxr-xr-x. 1 devops devops 24 Jun 10:38 Downloads
```

1. The first character indicates whether this is a file or a directory (`-` is a file, `d` is a directory)
2. The next 9-characters are permission settings: `rwr-xr-x`
3. Next to permissions is owner of the file/folder which is `devops`
4. Next to owner is group owner and also name `devops`
#### How do you read file permissions?
```
rw-r--r–x
```

Each 3-character is a set of permissions:
- `rw-`: User
- `r--`: Group
- `r-x`: Others

Three Basic Permissions
![[./images/bacsic_permissions.png]]

Ownership and Permissions Group
![[Pasted image 20260628214727.png]]
####  Change permissions
We will use `chmod` command to change permission in Linux

| Operators | Definition                                  |
| --------- | ------------------------------------------- |
| +         | Add permissions                             |
| -         | Remove permissions                          |
| =         | Set the permissions to the specified values |

| Reference | Class     |
| --------- | --------- |
| u         | user      |
| g         | group     |
| o         | others    |
| a         | All there |
Example: To Change File Permission in Linux

If you want to give "execute" permission to the world ("other") for file "xyz.txt", you will start by typing. 
```
chmod o
```

Now you would type a '+' to say that you are "adding" permission. 
```
chmod o+
```

Then you would type an 'x' to say that you are adding "execute" permission. 
```
chmod o+x
```

Finally, specify which file you are changing.  
```
chmod o+x xyz.txt
```