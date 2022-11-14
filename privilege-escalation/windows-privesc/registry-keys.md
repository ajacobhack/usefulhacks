# Registry Keys

## **Claves de registro (Registry keys)**

L**as claves** de registro que posiblemente contengan contraseñas se pueden consultar con los siguientes comandos.

```powershell
reg query HKCU /f password /t REG_SZ /s

reg query HKLM /f password /t REG_SZ /s
```
