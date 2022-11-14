# SUID / GUID / Sticky Bit

[Special Permissions: SUID, GUID, and sticky bit](https://linuxhint.com/special-permissions-suid-guid-sticky-bit/)

## SUID

SUID o Set Owner User ID es un indicador de bit de permiso que se aplica a los ejecutables. SUID permite que un usuario alternativo ejecute un ejecutable con los mismos permisos que el propietario del archivo en lugar de los permisos del usuario alternativo.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>SUID permission</p></figcaption></figure>



Add SUID

```bash
chmod u+s file
# forma numérica: cuarto dígito a los permisos normales con número octal siempre 4.
chmod 4766 file

# Queda así:
rwsrw-rw-
```

Delete SUID

```bash
chmod u-s file
chmod 0766 file
```

Add execute option when SUID bit is uppercase 'S':

```bash
chmod +x file
```

.

## GUID

_**SGID es similar a SUID. Con el conjunto de bits SGID, cualquier usuario que ejecute el archivo tendrá los mismos permisos que el propietario del grupo del archivo.**_

Su beneficio está en el manejo del directorio. Cuando se aplica el permiso SGID a un directorio, todos los subdirectorios y archivos creados dentro de este directorio obtendrán la misma propiedad de grupo que el directorio principal (no la propiedad de grupo del usuario que creó los archivos y directorios).

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

💡 GUID (globally unique identifier) se refiere usualmente a una forma más robusta de UUID (universally unique identifier), y parece fuera de lugar respecto de los permisos de usuarios y grupos en particular. Tradicionalmente, sin embargo, alguna documentación de Unix refieren a SGID como bit GUID.

Agregar bit SGID

```bash
chmod g+s file

# forma numérica: cuarto dígito a los permisos normales con número octal siempre 2.
chmod 2766 file 
# Queda así:
rwxrwsrw-
```

Eliminar bit SGID

```bash
chmod g-s file
chmod 0766 file
```



## Sticky Bit

_**El sticky bit funciona en el directorio. Con el sticky bit establecido en un directorio, todos los archivos en el directorio solo pueden ser eliminados o renombrados por los propietarios del archivo o por la raíz.**_

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Esto se usa normalmente en el directorio /tmp que funciona como la papelera de archivos temporales.

```
linuxhandbook:~$ ls -ld /tmp
drwxrwxrwt 1 root root 512 Apr 12 13:24 /tmp
```

Como puede ver, la carpeta /tmp, tiene la letra _**t**_ en el mismo lugar donde esperamos ver **x** o **–** para otros permisos. Esto significa que un usuario (excepto root) no puede eliminar los archivos temporales creados por otros usuarios en el directorio /tmp.

Agregar sticky bit

```bash
chmod +t my_dir
chmod 1766
```

Eliminar:

```bash
chmod -t my_dir
chmod 0766
```

Si el directorio no tiene el permiso de ejecución establecido para todos, establecer un bit fijo hará que se muestre T en lugar de t. Una indicación de que las cosas no son del todo correctas con el bit pegajoso.

[Permisos especiales en Linux: Sticky Bit, SUID y SGID - ochobitshacenunbyte](https://www.ochobitshacenunbyte.com/2019/06/17/permisos-especiales-en-linux-sticky-bit-suid-y-sgid/)

[Special Permissions: SUID, GUID, and sticky bit](https://linuxhint.com/special-permissions-suid-guid-sticky-bit/)



