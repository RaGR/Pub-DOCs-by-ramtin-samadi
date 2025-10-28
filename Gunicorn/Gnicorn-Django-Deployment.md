Gunicorn DOC:
https://docs.gunicorn.org/en/latest/deploy.html

so system-md is the firts procces that lunches into unix base OSs and the last one to terminate when shuting down.

i was wondering how to make multiple scripts happen at once in a single terminal without opening a new one. and here is your advanced profational answer:


# WHAT IS A SERVICE IN UNIX BASED SYSTEMS:

A Linux service is a **background process** that runs continuously to perform specific tasks. Services can start automatically at boot time and run without user intervention. Examples include web servers, database servers, and network services.

yes, so basicaly its a magic wood if you manage to create them and manage them and configure currectly

this is where the magic happens:
```bash
/etc/systemd/system/
```
unix GODs got your back and when you navigate into this directory you can see all other services too - you can nano them to see how real peopple wrote well services

i recomend:

```bash
ls -la | grep .service
```

here we can list and view all files containig .service in this magic dir, looks something like this:
![image](https://github.com/user-attachments/assets/ee9000c9-427b-4922-9fab-c225d99b4ba2)

inside of a service file looks something like this(Don't worrie, everything in Linux is eather a file or a dir so nothing wont go wrong):

```sh
[Unit]
Description=OpenBSD Secure Shell server
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target auditd.service
ConditionPathExists=!/etc/ssh/sshd_not_to_be_run

[Service]
EnvironmentFile=-/etc/default/ssh
ExecStartPre=/usr/sbin/sshd -t
ExecStart=/usr/sbin/sshd -D $SSHD_OPTS
ExecReload=/usr/sbin/sshd -t
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartPreventExitStatus=255
Type=notify
RuntimeDirectory=sshd
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
Alias=sshd.service
```



back to the topic if you install GUNICORN and type in terminall:
(myproject = name of your Django project)
```bash
gunicorn myproject.wsgi
```
now python GODs will aid you and with out python manage.py runserver 0.0.0.0:8000 the gunicorn will communicate with your Django project and answer client user requests when they happen.




```bash
sudo systemctl daemon-reload
```

```bash
sudo systemctl start gunicorn.socket
sudo systemctl enable gunicorn.socket
```





# UNICORN SOCKET COnF:
/etc/systemd/system/gunicorn.socket

```bash
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock
SocketUser=deploy
SocketGroup=www-data
SocketMode=0660

[Install]
WantedBy=sockets.target

```


# UNICORN SERVICE COnf:
/etc/systemd/system/gunicorn.service
```bash
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=deploy
Group=www-data
WorkingDirectory= /home/vboxuser/Django DRF todolist/mysite01
ExecStart= /home/vboxuser/Django DRF todolist/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          mysite01.wsgi:application

[Install]
WantedBy=multi-user.target
```

curl to check connection:
```bash
-u www-data curl --unix-socket /run/gunicorn.sock http
```
