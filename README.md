# Minitask: Linux - File Permission & Ownership

**Scenario**: Me as a system administrator preparing a shared project workspace on a Linux server. There are two users and one group.
Users consists of ***koda*** and ***dako***, both users included in ***devteam*** group, and the project directory is ***/srv/projectX***.

1. Users and group setup
```console
$cd /srv
$sudo adduser koda
$sudo adduser dako
$sudo addgroup devteam
$sudo usermod -aG devteam koda
$sudo usermod -aG devteam dako
```

To check using ```$cat /etc/passwd``` and ```$cat /etc/group```
```console
$cat /etc/passwd

# koda:x:1001:1001:,,,:/home/koda:/bin/bash
# dako:x:1002:1002:,,,:/home/dako:/bin/bash

$cat /etc/group

# devteam:x:1003:koda,dako
```

2. Make *koda* the owner and *devteam* the group owner of */srv/projectX*.
```console
$sudo usermod -aG sudo koda
$su koda
$mkdir projectX
$sudo chown koda:devteam projectX
```

To check, using ```$ll``` inside */srv* directory
```console
$ll

# drwxr-xr-x  2 koda devteam 4096 Nov 26 15:11 projectX/
```
