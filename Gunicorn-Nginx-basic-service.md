# gunicorn:

## gunicorn.service:

```bash
cd /etc/systemd/system
nano /etc/systemd/system/gunicorn.service
```
```bash
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=vboxuser
Group=vboxuser
WorkingDirectory=/home/vboxuser/DjangoDRFtodolist/mysite01
ExecStart=/home/vboxuser/DjangoDRFtodolist/venv/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 mysite01.wsgi:application

[Install]
WantedBy=multi-user.target
```


## gunicorn.socket:

```bash
cd /etc/systemd/system
nano /etc/systemd/system/gunicorn.ssocket
```

```bash
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock
SocketUser=vboxuser
SocketGroup=vboxuser
SocketMode=0660

[Install]
WantedBy=sockets.target
```


# NginX:

## conf:
```bash
cd /etc/nginx
nano nginx.conf
```

```bahs
worker_processes 1;

user vboxuser; # SAME USER AS GUNICORN CONFIG IN /etc/systemd/system/gunicorn.service
error_log  /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
  worker_connections 1024; # Increase if you have lots of clients
  accept_mutex off; # Set to 'on' if nginx worker_processes > 1
}

http {
  include mime.types;
  default_type application/octet-stream;
  access_log /var/log/nginx/access.log combined;
  sendfile on;

  upstream app_server {
    # Use TCP configuration for Gunicorn running on port 8000
    server 192.168.56.101:8000 fail_timeout=0;
  }

  server {
    # DEFAULT - TO ENSHURE TRAFIC
    listen 80 default_server;
    return 444;
  }

  server {
    listen 80;
    client_max_body_size 4G;

    server_name 192.168.56.101;

    keepalive_timeout 5;

    root /home/vboxuser/DjangoDRFtodolist/mysite01/rest_framework;

    location / {
      # Checks for static file, if not found proxy to app
      try_files $uri @proxy_to_app;
    }

#    location /v1 {
#       proxy_pass http://127.0.0.1:8000;
#        proxy_set_header Host $host;
#        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#    }

#    location /v2 {
#        proxy_pass http://127.0.0.1:8001;
#        proxy_set_header Host $host;
#        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#    }
```

## sites:
```bash
cd /etc/nginx/sites-available
nano default
```

```bash

server {
        listen 80;
        server_name 192.168.56.101;
        access_log  /var/log/nginx/example.log;

        location /v1 {
                proxy_pass http://0.0.0.0:8000;
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

        location /v2 {                                                                               │
                proxy_pass http://0.0.0.0:8001;                                                    │
                proxy_set_header Host $host;                                                         │
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;                         │
    }
 }

```
