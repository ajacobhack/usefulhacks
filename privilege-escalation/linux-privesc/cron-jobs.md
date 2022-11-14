# Cron jobs



## Cron Jobs: se ejecutar con privilegios root

```bash
# Ver cron jobs
cat /etc/crontab
# Buscar algún trabajo cron y prestar atención desde dónde se ejecuta o ejecuta otras cosas.
# Armar un exploit para que se ejecute en lugar del archivo que ejcuta el cron job. Tener un netcat para recibir el shell.
```

Por ejemplo un sh que ejecuta el antivirus en una tarea cron, puede poner allí un reverse shell:

![https://i.imgur.com/SHknR87.png](https://i.imgur.com/SHknR87.png)

La conexión de shell inversa entrante tiene privilegios de root:

![https://i.imgur.com/EBCue17.png](https://i.imgur.com/EBCue17.png)
