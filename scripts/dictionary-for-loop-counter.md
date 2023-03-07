---
description: Generate a counter of values with "for" and build a dictionary
---

# Dictionary ("for" loop counter)

El contador `for` genera un ciclo que va desde 0000 a 9999, pero siempre de a 4 cifras. Esto puede modificarse a gusto.

Con echo volcamos en pantalla. Podemos redirigir luego el resultado a un archivo txt para confirmar un diccionario.

```bash
#!/bin/bash

for i in {0000..9999}; do
        echo "$i"
done
```

```bash
./script.sh > diccionary.txt
```

Código para generar txt de puertos para Fuzzing de localhost para la verificación de vulnerabilidad  CSRF:

```bash
for i in {1..65535}; do; echo "$i"; done > ports.txt
```

Payload 1 caracter, de a-z y de 0-9:

{% code overflow="wrap" %}
```bash
for i in {a..z}; do echo "$i"; done > az09.txt && for u in {0..9}; do echo "$u"; done >> az09.txt
```
{% endcode %}
