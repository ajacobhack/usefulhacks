# Token spoofing (Potato)



## Suplantación de Token

En las versiones de Windows anteriores a Server 2019 y 10 (versión 1809), estas cuentas de servicio se ven afectadas por una vulnerabilidad interna de MITM.

las cuentas de servicio con privilegios más altos se verán obligadas a autenticarse en un puerto local en el que escuchamos. Una vez que la cuenta de servicio intenta autenticarse, esta solicitud se modifica para negociar un token de seguridad para la cuenta "NT AUTHORITY\SYSTEM". El token de seguridad obtenido puede ser utilizado por el usuario que tengamos en un proceso llamado "suplantación". Aunque ha dado lugar a varias hazañas, los derechos de suplantación de identidad no eran una vulnerabilidad.

En las versiones de Windows posteriores a Server 2019 y Windows 10 (versión 1809), los derechos de suplantación ahora sí estaban restringidos.

Las vulnerabilidades de suplantación de tokens, verá que existen varios exploits diferentes. Estos tienen nombres caprichosos como Hot Potato, Rotten Potato, Lonely Potato, Juicy Potato, etc. Podrá decidir qué "Potato" se adapta mejor a sus necesidades según la versión del sistema de destino. Si bien algunas de estas vulnerabilidades se ejecutarán en el sistema de destino, otras pueden requerir que configure un servidor falso en la misma red.El primero de estos exploits de patatas fue "Hot Potato".

```bash
# Procedimiento
(completar)
```
