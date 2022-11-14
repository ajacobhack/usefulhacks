# Windows PrivEsc



## Procedimiento

1. Enumere los privilegios del usuario actual y los recursos a los que puede acceder.
2. Si el software antivirus lo permite, ejecute un script de enumeración automatizado como:

winPEAS, PowerUp.ps1, Windows Exploit Suggester.

1. Si no encuentra nada, verifique manualmente:

Lista de verificación

[PayloadsAllTheThings/Windows - Privilege Escalation.md at master · swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)

## Enumeración

{% code overflow="wrap" %}
```powershell
whoami /priv  #Privilegios del usuario actual
net users     #Lista de usuarios
net user <username>  #Enumere los detalles de un usuario p. ej net user Administrator
net user Administrator

qwinsta  #Otros usuarios iniciaron sesión simultáneamente. El `query session`comando se puede usar de la misma manera

net localgroup  # Enumera Grupos de usuarios definidos en el sistema
net localgroup <groupname>  #Enumere los miembros de un grupo específico, por ejemplo, `net localgroup Administrators`)
net localgroup Administrators

systeminfo | findstr /B /C:"OS Name" /C:"OS Version"  #info del sistema
hostname   #nombre del host equipo

findstr /si password *.txt
#Desglose de comandos:
#`findstr`: busca patrones de texto en archivos.
#`/si`: busca en el directorio actual y en todos los subdirectorios (s), ignora las diferencias entre mayúsculas y minúsculas (i)
#`password`: El comando buscará la cadena "contraseña" en los archivos
#- `.txt`: La búsqueda cubrirá archivos que tengan una extensión .txt
#La cadena y la extensión del archivo se pueden cambiar según sus necesidades y el entorno de destino, pero ".txt", ".xml", ".ini", "*.config" y ".xls" suelen ser un buen lugar para comienzo.
findstr /si unattend.xml

#Enumera actualizaciones instaladas:
wmic qfe get Caption,Description,HotFixID,InstalledOn 

#Ver todas las conexiones de red, sin dns, con id de proceso
netstat -ano

#Tareas programadas
schtasks /query /fo LIST /v

driverquery #Ver drivers

#Antivirus detection
sc query windefend #busca un servicio llamado "windefend" y devolverá su estado actual.
sc queryex type=service #el output es abrumadior, pero se podría encontrar el AV

wmic product get name,version,vendor  #ver software vulnerbale, pero puede que no figuren todos u otroas versiones 32 bits.
wmic service list brief | findstr  "Running" #verifica servicios en ejecución para entender mejor el sistema
sc qc <RemoteMouseService> # Ve detalles de un servicio
```
{% endcode %}

## Tools automatizadas

### _<mark style="color:yellow;">**WinPEAS**</mark>_

_<mark style="color:yellow;"></mark>_[PEASS-ng/winPEAS at master · carlospolop/PEASS-ng](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)

**WinPEAS** es un script desarrollado para enumerar el sistema de destino para descubrir rutas de escalada de privilegios. Puede encontrar más información sobre winPEAS y descargar el ejecutable precompilado o un script .bat. Tenga en cuenta que Windows Defender detecta y desactiva winPEAS. WinPEAS ejecutará comandos similares a los enumerados en la tarea anterior e imprimirá su salida. La salida de winPEAS puede ser larga y, a veces, difícil de leer. Es por eso que sería una buena práctica redirigir siempre la salida a un archivo, como se muestra a continuación:

`winpeas.exe > outputfile.txt`

WinPEAS se puede descargar [aquí](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)

—Una vez obtenidos provilegios de admin podemos ejecutarlo

### _<mark style="color:yellow;">**PowerUp**</mark>_

[PowerSploit/Privesc at master · PowerShellMafia/PowerSploit](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)

PowerUp es un script de PowerShell que busca la escalada de privilegios comunes en el sistema de destino. Puede ejecutarlo con la `Invoke-AllChecks`opción que realizará todas las comprobaciones posibles en el sistema de destino o utilizarlo para realizar comprobaciones específicas (p. ej., la `Get-UnquotedService`opción de buscar únicamente posibles vulnerabilidades en la ruta del servicio sin comillas).

PowerUp se puede descargar [aquí](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) .

**Recordatorio** : para ejecutar PowerUp en el sistema de destino, es posible que deba omitir las restricciones de la política de ejecución. Para lograr esto, puede iniciar PowerShell usando el siguiente comando.

Ejecución de PowerUp.ps1 en el sistema de destino

```powershell
powershell.exe -nop -exec bypass
Import-Module .\\PowerUp.ps1
Invoke-AllChecks
```

```
C:\\Users\\user\\Desktop>powershell.exe -nop -exec bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\\Users\\user\\Desktop> Import-Module .\\PowerUp.ps1
PS C:\\Users\\user\\Desktop> Invoke-AllChecks

[*] Running Invoke-AllChecks

[*] Checking if user is in a local group with administrative privileges...
```

### _<mark style="color:yellow;">**Windows Exploit Suggester**</mark>_

_<mark style="color:yellow;"></mark>_[https://github.com/AonCyberLabs/Windows-Exploit-Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)

Algunos scripts que sugieren vulnerabilidades (por ejemplo, winPEAS) requerirán que los cargue en el sistema de destino y los ejecute allí. Esto puede hacer que el software antivirus los detecte y elimine. Para evitar hacer ruido innecesario que pueda llamar la atención, es posible que prefiera utilizar Windows Exploit Suggester, que se ejecutará en su máquina atacante (por ejemplo, Kali o TryHackMe AttackBox).

Windows Exploit Suggester es un script de Python que se puede encontrar y descargar [aquí](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)

Una vez instalado, y antes de usarlo, escribe el `windows-exploit-suggester.py –update` comando para actualizar la base de datos. La secuencia de comandos se referirá a la base de datos que crea para verificar si faltan parches que pueden resultar en una vulnerabilidad que puede usar para elevar sus privilegios en el sistema de destino.

Para usar el script, deberá ejecutar el `systeminfo` comando en el sistema de destino. No olvide dirigir la salida a un archivo .txt que deberá mover a su máquina atacante.

Una vez hecho esto, [windows-exploit-suggester.py](http://windows-exploit-suggester.py) se puede ejecutar de la siguiente manera;

`windows-exploit-suggester.py --database 2021-09-21-mssb.xls --systeminfo sysinfo_output.txt`

Una versión más reciente de Windows Exploit Suggester está disponible [aquí](https://github.com/bitsadmin/wesng) . Dependiendo de la versión del sistema de destino, el uso de la versión más nueva podría ser más eficiente.

### _<mark style="color:yellow;">**MSF-Metasploit**</mark>_

Si ya tiene un shell de Meterpreter en el sistema de destino, puede usar el `multi/recon/local_exploit_suggester`módulo para enumerar las vulnerabilidades que pueden afectar el sistema de destino y permitirle elevar sus privilegios en el sistema de destino.

****
