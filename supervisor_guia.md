[TOC]

Instalar supervisor
---
```
sudo pip install supervisor
```

Crear archivos de configuración
---
###supervisord.conf
```
sudo mkdir /etc/supervisord 
sudo touch /etc/supervisord/supervisord.conf
sudo subl /etc/supervisord/supervisord.conf
```

En **supervisord.conf** metemos:
```no-highlight
[unix_http_server]
file=/tmp/supervisor.sock   ; (the path to the socket file)
 
[supervisord]
logfile=/var/log/supervisord/main.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10           ; (num of main logfile rotation backups;default 10)
loglevel=info                ; (log level;default info; others: debug,warn,trace)
pidfile=/tmp/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=false               ; (start in foreground if true;default false)
minfds=1024                  ; (min. avail startup file descriptors;default 1024)
minprocs=200                 ; (min. avail process descriptors;default 200)
childlogdir=/var/log/supervisord            ; ('AUTO' child log dir, default $TEMP)
 
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
 
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
 
[include]
files = /etc/supervisord/conf.d/*.conf
```

###scandal-processor.conf
```
sudo mkdir /etc/supervisord/conf.d/
sudo touch /etc/supervisord/conf.d/scandal-processor.conf
sudo subl /etc/supervisord/conf.d/scandal-processor.conf
```
En **scandal-processor.conf** metemos:
```
[program:scandal-processor]
command=/usr/bin/python /home/ubuntu/scandaloh/manage.py scandal_processor --start --settings=settings.test_server
environment=PYTHONPATH=/home/ubuntu/scandaloh/
directory=/home/ubuntu/scandaloh/
numprocs=1
stdout_logfile=/home/ubuntu/scandaloh/logs/supervisor/scandal_processor.log
stderr_logfile=/home/ubuntu/scandaloh/logs/supervisor/scandal_processor.log
autostart=true
autorestart=true
startsecs=10
stopwaitsecs = 10
priority=998
```
 
Si no especificamos ```user=yomac```, entonces se lanzará como root.

###Carpetas para los logs
```stdout_logfile``` y ```stderr_logfile``` apuntarán a un archivo que puede que no tengan su carpeta creada. Creamos las carpetas:
```
sudo mkdir /var/log/supervisord
sudo mkdir /path/to/app/logs/supervisor
``` 
 
Ejecutar supervisor
---
```
supervisord -c /etc/supervisord/supervisord.conf
```
 
###Ver que está corriendo:
```
ps aux | grep scandal_processor
``` 

###Ver salida stdout/stderr del scandal_processor:

Forma antigua (sin supervisor):

    tail -f logs/scandal_processor\@dev.log

Forma nueva:

    tail -f logs/supervisor/scandal_processor.log 
 
Detener supervisor:
    
    supervisorctl -c /etc/supervisord/supervisord.conf shutdown

