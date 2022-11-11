# Linux basics

## Privileges

### Chmod

```
u: users
g: group
o: others
```

```
r: read
w: write
x: execute
```

```bash
chmod {who}:{what} {file}

chmod  u+x file   # User add Executable permission

chmod +x file # all exec
```



```
4: read
2: write
1: exec

3: write and write
5: read and exec
6: read and write

7: read, write and exec
```

```bash
chmod 777 file   # All permissions granted = ugo+rwx
chmod 600 file    # Usually ir_rsa keys
```

### Chown

Change ownership

```
chown name filename
chown name foldername
chown -R name:filename /home/name/directoryname
```



## Directory management

### New Temp Dir  - mktemp

```
mktemp -d

>tmp/tmp/whatever
cd {tmp/tmp/whatever}
```

## SSH

Conection

```bash
ssh {user}@{domain} -p {port}

ssh -i id_rsa {user}@{domain} -p {port}
```

Bypass

```bash
# Spawn Bash shell
ssh {user}@{domain} -p {port} bash

# Spawn interactive Shell
ssh {user}@{domain} -p {port} bash --norc
```



