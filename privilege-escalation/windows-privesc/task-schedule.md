# Task schedule

## Tareas programadas (Task schedule)

Si hay tareas programadas que se ejecutan con mayores privilegios y que han perdido algún binario, o tienen modificado un binario ejecutable en su ruta, puede ser vulnerable.

```bash
schtasks /query /fo LIST /v   # Ver tareas programadas, puede dar mucho output
```
