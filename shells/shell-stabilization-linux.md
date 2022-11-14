# Shell stabilization (Linux)

## Estabilizar shell (Linux)

{% code overflow="wrap" %}
```bash
python -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
^Z
stty raw -echo; fg
#(si no funciona todo, sólo utilizar la primer línea)
```
{% endcode %}

## Otra forma:

{% code overflow="wrap" %}
```bash
# Teniendo un shell reverso tty, lanzamos una pseudo consola
script /dev/null/ -c bash

# Como el shell es muy inestable, y con cualquier Ctrl+C se puede caer, lo que hacemos es ponerla en segundo plano Ctrl+Z
^Z
stty raw -echo; fg
#Luego reiniciamos la configuración de la consola con "reset"
reset
# En tipo de terminal: xterm y damos Enter.
xterm

# Cambiamos las variable: TERM (vale dump) por xterm; y a SHELL=bash:
export TERM=xterm
export SHELL=bash
# Ahora podemos utilizar Ctrl+L y Ctrl+C sin que se dañe el shell.

# Ver las filas y columnas de nuestra consola tty para luego configurar la consola del reverse shell del mismo ancho:
stty size
stty rows <filas> columns <columnas>
# Ej. stty rows 51 columns 1
```
{% endcode %}
