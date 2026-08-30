### Text Editors
When using Linux based OS, most of the time you spent will be on the terminals, and using GUI text editors like VS Code, Sublime, etc can be quite slow and time consuming in few cases.
#### Which editors should you start with?
- **Nano**: easiest and beginner friendly with key hints.
- **Vim**: powerful and fast for long-term once learned.

#### Nano
Open a file:
```
nano /path/to/file
```
Common actions:
- Save: `Ctrl + O`
- Exit: `Ctrl + X`
- Search: `Ctrl + W`
- Cut line: `Ctrl + K`
- Paste line: `Ctrl + U`

#### Vim
Open a file:
```
vim /path/to/file
```
Core mode model:
- **Normal mode**: navigation and command
- **Insert mode**: typing text
- **Command mode**: save, quit, and file commands
Starter actions:
- Enter insert mode: `i`
- Save and quit: `:wq`
- Quit without saving: `:q!`
- Delete line: `dd`
- Undo: `u`
Navigation and search basics:
- Move around: `hjkl`
- Move by words: `w`, `b`
- Search forward: `/text`
- Repeat search: `n`
- Replace in file `:%s/old/new/g`
If you want to learn more, check out:
```
vimtutor
```
or
https://github.com/patohru/TheNeovim
https://www.youtube.com/watch?v=X6AR2RMB5tE&list=PLm323Lc7iSW_wuxqmKx_xxNtJC_hJbQ7R

#### File system structure
![[images/filesystem_structure.png]]

1. **/ (Root)**
At the top of every Linux file system is the root directory represented by a forward slash /. It’s the base point, and no directory exists above it. 

2. **/bin**
The /bin directory contains essential commands and binaries needed by all users, including cp, ls, ssh, and kill. These commands are universally available across user types.

3. **/boot**
This directory stores all files required for booting the system. It includes the GRUB bootloader configuration and essential kernel files that are loaded during startup. 
- Kernel initrd, vmlinux, grub files are located under /boot
- Example: initrd.img-2.6.32-24-generic, vmlinuz-2.6.32-24-generic

4. **/dev**
Device files in Linux are stored in the /dev directory. These are special files that act as interfaces between hardware and software. Device files are of two types: block devices (e.g., hard drives) and character devices (e.g., microphones and speakers). Examples include /dev/sda1 for disk partitions.
- These include terminal devices, USB, or any device attached to the system
- Example: /dev/tty1, /dev/usbmon0
4. **/etc**
Short for "Editable Text Configuration," /etc contains configuration files for system applications, users, services, and tools or it contains the Host-specific system-wide configuration files.
5. **/home**
Home directories for all users to store their personal files, containing saved files, personal settings, etc
6. **/lib**
Applications require shared libraries to run, which are stored in /lib. These include dynamic libraries needed during runtime.
7. **/media**
Devices like USBs, CDs, and pen drives are mounted under /media.
8. **/mnt**
When external drives are connected, they are temporarily mounted in /mnt. This is where their contents become accessible to the system.
9. **/opt**
Third-party software and packages not part of the default system installation are stored in /opt. It includes their configuration and data files.
10. **/sbin**
This directory holds administrative binaries like iptables, firewall management tools, fsck, init, route etc. These binaries are primarily for system administrators and typically require root privileges to execute.
11. **/srv**
Site-specific data served by this system, such as data and scripts for web servers, data offered by FTP servers, and repositories for version control systems.
12. **/tmp**
Programs create temporary files during execution, and these are stored in /tmp. These files are deleted automatically after the program finishes or when the system is restarted.
13. **/usr**
Secondary hierarchy for read-only user data; contains the majority of (multi-)user utilities and applications. 
14. **/proc**
The /proc directory provides detailed information about system processes. Each process is assigned a unique ID and represented as a directory inside /proc. For example, /proc/meminfo gives real-time data about memory usage including total, free, buffer, and cache statistics.