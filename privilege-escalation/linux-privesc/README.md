# Linux PrivEsc

## Enumeration

```bash
whoami #what user am I
pwd #prints the directory where I am
hostname #hostname
uname -a #info of system, kernel, etc
cat /proc/version #kernel version and other info
cat /etc/issue #info about the system
grep VERSION /etc/os-release # kali or operating system version info
join me -v
uname -r
ps -A # running processes
ps axjf #process tree
ps aux #processes for all users
env #display environmental variables
sudo -l # List all commands the user can run as sudo
ls -la #list everything including hidden files and folders with their permissions
id <user> #privilege level overview and group membership
cat /etc/passwd | cut -d “:” -f 1 #show users from passwd file
cat /etc/passwd | grep home #since real users will probably have their folders in the "home" directory

history #command history
ifconfig # show network interfaces, IP
netstat -ano #Displays full TCP and UDP listening connections, does not resolve names, and displays timers; filter with -at or -au to differentiate them
netstat -lt #list of ports in “listening” mode, ready to accept incoming TCP connections

find / -name flag.txt # Find for everything ("/") something with the name flag.txt
find / -type d -name config #look for the directory named config under “/”
find / -type f -perm 0777 #find files with permissions 777 (files readable, writable and executable by all users)
find / -perm a=x #find executable files

#Find writable folders around the world
find / -writable -type d 2>/dev/null
find / -perm -222 -type d 2>/dev/null
find / -perm -o w -type d 2>/dev/null

#Find files with the SUID bit that allows us to execute the file with a higher privilege level than the current user__:

# Find SUID. In the SUID, the executable in question is executed with the privileges of the owner of the file.
find / -perm -u=s -type f 2>/dev/null

# FindGUID. In the GUID, if it is an executable, it runs with the permissions of the group. If it is a directory, returns all new files and directories created to belong to the group.
find / -perm -g=s -type f 2>/dev/null
```
