# Linux Enumeration and Privilege Escalation

### Interesting commands

```bash
luCstlog
/etc/hosts
who
finger
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

update -o APT::Update::Pre-Invoke::=/bin/sh

```

### Linux capabilities

|Value|Description|
| --- | --------- |
|  =  |	This value sets the specified capability for the executable, but does not grant any privileges. This can be useful if we want to clear a previously set capability for the executable.|
| +ep |	This value grants the effective and permitted privileges for the specified capability to the executable. This allows the executable to perform the actions that the capability allows but does not allow it to perform any actions that are not allowed by the capability.|
| +ei | This value grants sufficient and inheritable privileges for the specified capability to the executable. This allows the executable to perform the actions that the capability allows and child processes spawned by the executable to inherit the capability and perform the same actions. |
| +p  |	This value grants the permitted privileges for the specified capability to the executable. This allows the executable to perform the actions that the capability allows but does not allow it to perform any actions that are not allowed by the capability. This can be useful if we want to grant the capability to the executable but prevent it from inheriting the capability or allowing child processes to inherit it.|

### Linux capabilities and opportunities for privesc

|Capability |Description|
| --------- | --------- |
|CAP_SETUID | 	Allows a process to set its effective user ID, which can be used to gain the privileges of another user, including the root user.|
|CAP_SETGID |	Allows to set its effective group ID, which can be used to gain the privileges of another group, including the root group.|
|CAP_SYS_ADMIN | 	This capability provides a broad range of administrative privileges, including the ability to perform many actions reserved for the root user, such as modifying system settings and mounting and unmounting file systems.|
