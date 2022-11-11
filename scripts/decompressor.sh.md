---
description: >-
  Bash script of a file decompressor for use on files that have been compressed
  multiple times. The script decompresses me, reads the contents and
  decompresses again until it reaches the last level, and
---

# Decompressor.sh

{% code title="decompressor.sh" overflow="wrap" lineNumbers="true" %}
```bash
#!/bin/bash

name_decompressed=$(7z l content.gzip | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}')
7z x content.gzip > /dev/null 2>&1

while true; do
        7z l $name_decompressed > /dev/null 2>&1
        if [ "$(echo $?)" == "0" ]; then
                decompressed_next=$(7z l $name_decompressed | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}')
                7z x $name_decompressed > /dev/null 2>&1 && name_decompressed=$decompressed_next
        else
                cat $name_decompressed; rm data* 2>/dev/null
                exit 1
        fi
done
```
{% endcode %}

El comando guardado en la variable `name_decompressed` lo que hace es listar en contenido con `7z` de un contenido comprimido, y aplica filtros: `grep` de línea Name, se dirige al último string con `tail`, y con `awk` tomamos el último string.

En el bucle `while` lo que se hace es realizar un listing de la variable, y si el echo del comando anterior tiene estatus correcto es decir, 0, sin error 1, entonces va a guardar este listing en una  nueva variable `decompressed_next` y va a aplicar una extracción con `7z x` enviando errores y el stder al stdin y /dev/null. Luego se pasa el contenido de esta variable a la variable de `name_decompressed`, como un contador, para que tenga ahora un progreso en las proximas ejecuciones.

Si el listing del Name dentro del comprimido da lugar a error, es decir, que NO HAY otros comprimidos, entonces con "else" ponemos que muestre en pantalla con `cat` el nombre de este último comprimido, y que remueva (borre) todos los demás extraídos anteriormente.

NOTA: este script se creó para un desafío de overthewire, y funciona teniendo en cuenta que los archivos comprimidos dentro tienen nombre que comienza con "data".

NOTA 2: podría modificarse para que elimine los demás extraídos de otra forma.
