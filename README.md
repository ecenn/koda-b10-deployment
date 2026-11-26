# Minitask: Linux - File Permission & Ownership

**Scenario**: You are a system administrator preparing a shared project workspace on a Linux server. There are two users and one group.
Users consists of ***koda*** and ***dako***, both users included in ***devteam*** group, and the project directory is ***/srv/projectX***.
<br>

## Task

1. Users and group setup
```console
$cd /srv
$sudo adduser koda
$sudo adduser dako
$sudo addgroup devteam
$sudo usermod -aG devteam koda
$sudo usermod -aG devteam dako

$cat /etc/passwd
# koda:x:1001:1001:,,,:/home/koda:/bin/bash
# dako:x:1002:1002:,,,:/home/dako:/bin/bash

$cat /etc/group:
# devteam:x:1003:koda,dako
```
<br>

2. Make *koda* the owner and *devteam* the group owner of */srv/projectX*.
```console
$sudo usermod -aG sudo koda
$su koda
$mkdir projectX
$sudo chown koda:devteam projectX

$ll
# drwxr-xr-x  2 koda devteam 4096 Nov 26 15:11 projectX/
```
<br>

3. Restricting access (using numeric mode)
    - Owner have full access
    - Group have read and execute access
    - Others have no access
```console
$chmod 750 projectX

$ll
# drwxr-x---  2 koda devteam 4096 Nov 26 15:11 projectX/
```
<br>

4. As user *koda*, create the following structure:

<img width="164" height="143" alt="image" src="https://github.com/user-attachments/assets/2b31838e-b235-483e-9dde-00c10b18e61a" />

```console
$cd projectX
$touch README.md
$mkdir src
$mkdir data
$touch app.sh
$mv app.sh src
$touch input.txt
$mv input.txt data
```
<br>

5. Fixing permissions (using symbolic mode): The script app.sh must be executable by owner and group, but others can't.
```console
$cd /projectX/src
$chmod ug+x,o-x app.sh

$ll
# -rwxrwxr-- 1 koda koda       0 Nov 26 15:18 app.sh*
```
<br>

6. Protecting sensitive data
    - The file data/input.txt contains sensitive information
    - Set permissions so that only the owner can read and write access, and no one else has any access
```console
$cd /projectX/data
$chmod 600 input.txt

$ll
# -rw------- 1 koda koda       0 Nov 26 15:18 input.txt
```
<br>

7. Allow group collaboration
    - Devteam members able to read and write files in src and access to the directory
    - Use permissions recursively (-R) to the src directory
```console
$cd /srv/projectX
$chown -R :devteam src

$ll
# drwxrwxr-x 2 koda devteam 4096 Nov 26 15:19 src/

$cd src
$ll
# -rwxrwxr-- 1 koda devteam    0 Nov 26 15:18 app.sh*
```
<br>

8. Change ownership (using a single command): Change ownership of all files and directories under ***/srv/projectX*** so the owner is ***koda*** and the group is ***devteam***
```console
$cd /srv
$chown -R koda:devteam projectX

$cd projectX
$ll
# drwxr-x--- 4 koda devteam 4096 Nov 26 15:19 ./
# drwxr-xr-x 3 root root    4096 Nov 26 15:11 ../
# -rw-rw-r-- 1 koda devteam    0 Nov 26 15:17 README.md
# drwxrwxr-x 2 koda devteam 4096 Nov 26 15:19 data/
# drwxrwxr-x 2 koda devteam 4096 Nov 26 15:19 src/
```
<br>

9. Prevent accidental deletion: Files inside srv/projectX **cannot be deleted** by users outside devteam but devteam members can still create and delete files
```console
$cd /srv
$chmod -R ug+w,o-w projectX

$cd projectX
$ll
# drwxrwx--- 4 koda devteam 4096 Nov 26 15:19 ./
# drwxr-xr-x 3 root root    4096 Nov 26 15:11 ../
# -rw-rw-r-- 1 koda devteam    0 Nov 26 15:17 README.md
# drwxrwxr-x 2 koda devteam 4096 Nov 26 15:19 data/
# drwxrwxr-x 2 koda devteam 4096 Nov 26 15:19 src/
```
<br>

10. Make README read-only
```console
$cd /srv/projectX
$chmod 644 README.md

$ll
# -rw-r--r-- 1 koda devteam    0 Nov 26 15:17 README.md
```
<br>

11. Resignation and project handover
    - Transfer all ownership to *dako*
    - Group remains *devteam*
```console
$cd /srv
$sudo chown -R dako projectX
$sudo usermoda -aG sudo dako
$sudo deluser koda sudo

$cd /srv/projectX
$ll
# drwxrwx--- 4 dako devteam 4096 Nov 26 15:19 ./
# drwxr-xr-x 3 root root    4096 Nov 26 15:11 ../
# -rw-r--r-- 1 dako devteam    0 Nov 26 15:17 README.md
# drwxrwxr-x 2 dako devteam 4096 Nov 26 15:19 data/
# drwxrwxr-x 2 dako devteam 4096 Nov 26 15:19 src/

$cd /srv/projectX/src
$ll
# drwxrwxr-x 2 dako devteam 4096 Nov 26 15:19 ./
# drwxrwx--- 4 dako devteam 4096 Nov 26 15:19 ../
# -rwxrwxr-- 1 dako devteam    0 Nov 26 15:18 app.sh*

$cd /srv/projectX/data
$ll
# drwxrwxr-x 2 dako devteam 4096 Nov 26 15:19 ./
# drwxrwx--- 4 dako devteam 4096 Nov 26 15:19 ../
# -rw--w---- 1 dako devteam    0 Nov 26 15:18 input.txt
```
