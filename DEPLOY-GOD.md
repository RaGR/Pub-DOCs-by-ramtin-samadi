Deploying a complex Django project with Redis, Celery, MySQL, InfluxDB, and Nginx on an Ubuntu server involves several steps. Below is a comprehensive guide to help you through the process:


```bash

# list all installed python vertions:
ls /usr/bin/python*

# not recomended - uninstalling python will dammage system files:
# try setting bash command to execute desired .ver or specify in your commands
# like -> 'python3.10 -m venv ... (.etc)'
apt-get purge --auto-remove python3.12

# these will enable multiple versions of python available:
sudo apt-get update
sudo apt-get install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update


sudo apt-get install -y python3.10 python3.10-venv python3.10-dev

# to make python3 execute version 3.10:
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1

# to make python command exeute python 3.10:

sudo ln -s /usr/bin/python3.10 /usr/bin/python


```


### **1. Prepare the Ubuntu Server**
1. **Update the system**:
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```



2. **Install essential system packages**:
   ```bash
   sudo apt-get install -y python3 python3-pip python3-venv python3-dev libmysqlclient-dev build-essential nginx unixodbc-dev
   ```

   
3. C dependent python pakages require libffi. so mind installing that via command provided on your system:
   ```
   sudo apt-get install libffi-dev
   ```

4. some packages need this packge-config as well - so mind installing and updating it as well on your ubuntu server:

   ```bash
   sudo apt-get install pkg-config
   ```

5. **Install Redis** (required for Channels and Celery):
   ```bash
   sudo apt-get install -y redis-server
   sudo systemctl start redis
   sudo systemctl enable redis
   ```

6. **Install MySQL** (if not already installed):
   ```bash
   sudo apt-get install -y mysql-server
   sudo mysql_secure_installation
   ```

7. **Install InfluxDB** (for logs):
   ```bash
   sudo apt-get install -y influxdb
   sudo systemctl start influxdb
   sudo systemctl enable influxdb
   ```

---

### **2. Install Microsoft SQL Server ODBC Drivers and Tools**
1. **Add Microsoft's repository and install ODBC drivers**:
   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
   curl https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list
   sudo apt-get update
   sudo ACCEPT_EULA=Y apt-get install -y msodbcsql18
   sudo ACCEPT_EULA=Y apt-get install -y mssql-tools18
   ```

2. **Add `mssql-tools` to your PATH**:
   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Install `unixodbc-dev`** (if not already installed):
   ```bash
   sudo apt-get install -y unixodbc-dev
   ```

---

### **3. Set Up Python Virtual Environment**
1. **Create a virtual environment**:
   ```bash
   python3 -m venv /path/to/your/venv
   ```

2. **Activate the virtual environment**:
   ```bash
   source /path/to/your/venv/bin/activate
   ```

---

### **4. Install Project Dependencies**
1. **Install the project using `pip`** (if you have a `setup.py` or `setup.cfg`):
   ```bash
   pip install .
   ```

2. **Alternatively, install from `requirements.txt`** (if available):
   ```bash
   pip install -r requirements.txt
   ```

3. **Install Hypercorn** (ASGI server):
   ```bash
   pip install hypercorn
   ```

---

### **5. Configure Environment Variables**
1. **Create a `.env` file**:
   ```bash
   touch /path/to/your/project/.env
   ```

2. **Fill in the `.env` file** with necessary environment variables. Example:
   ```plaintext
   DJANGO_SECRET_KEY=your_secret_key
   DB_NAME=your_db_name
   DB_USER=your_db_user
   DB_PASSWORD=your_db_password
   DB_HOST=your_db_host
   DB_PORT=your_db_port
   REDIS_URL=redis://your_redis_host:6379/0
   INFLUXDB_HOST=your_influxdb_host
   INFLUXDB_PORT=your_influxdb_port
   INFLUXDB_USER=your_influxdb_user
   INFLUXDB_PASSWORD=your_influxdb_password
   MSSQL_DSN=your_mssql_dsn
   ```

   or you can use a json file to fill in information in there. then you can write a .py script that converts data from json file to .env file
   ```bash
   python3 env_creator.py
   ```

3. **Run the `env_gen` script** (if available) to generate environment variables from a JSON file:
   ```bash
   python /path/to/your/project/env_gen.py
   ```

---

### **6. Set Up MySQL Database**
1. **Log in to MySQL**:
   ```bash
   sudo mysql -u root -p
   ```

2. **Create a database and user**:
   ```sql
   CREATE DATABASE your_db_name;
   CREATE USER 'your_db_user'@'localhost' IDENTIFIED BY 'your_db_password';
   GRANT ALL PRIVILEGES ON your_db_name.* TO 'your_db_user'@'localhost';
   FLUSH PRIVILEGES;
   ```

3. **Run Django migrations**:
   ```bash
   python manage.py migrate
   ```

---

### **7. Set Up InfluxDB**
0. make sure you have the influxDB client installed:
   ```
   sudo apt install influxdb-client
   ```
2. **Log in to InfluxDB**:
   ```bash
   influx
   ```

3. **Create a database and user**:
   ```sql
   CREATE DATABASE your_influxdb_name;
   CREATE USER your_influxdb_user WITH PASSWORD 'your_influxdb_password' WITH ALL PRIVILEGES;
   ```

---

### **8. Configure Nginx**
1. **Use the project's Nginx configuration script** (if available):
   ```bash
   python /path/to/your/project/nginx.py
   ```

2. **Alternatively, manually configure Nginx**:
   - Create an Nginx configuration file:
     ```bash
     sudo nano /etc/nginx/sites-available/your_project
     ```
   - Example configuration for ASGI and WebSockets:
     ```nginx
     server {
         listen 80;
         server_name your_domain_or_ip;

         location / {
             proxy_pass http://127.0.0.1:8000;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }

         location /ws/ {
             proxy_pass http://127.0.0.1:8000;
             proxy_http_version 1.1;
             proxy_set_header Upgrade $http_upgrade;
             proxy_set_header Connection "upgrade";
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }

         location /static/ {
             alias /path/to/your/project/static/;
         }

         location /media/ {
             alias /path/to/your/project/media/;
         }
     }
     ```
   - Enable the configuration:
     ```bash
     sudo ln -s /etc/nginx/sites-available/your_project /etc/nginx/sites-enabled/
     ```
   - Test and restart Nginx:
     ```bash
     sudo nginx -t
     sudo systemctl restart nginx
     ```

---

### **9. Run the Project with Hypercorn**
1. **Run Hypercorn** (ASGI server):
   ```bash
   hypercorn your_project.asgi:application --bind 127.0.0.1:8000
   ```

2. **Use the project's `run.sh` script** (if available):
   ```bash
   bash /path/to/your/project/run.sh
   ```

---

### **10. Set Up Celery**
1. **Create a Celery service file**:
   ```bash
   sudo nano /etc/systemd/system/celery.service
   ```

2. **Example configuration**:
   ```ini
   [Unit]
   Description=Celery Service
   After=network.target

   [Service]
   User=your_user
   Group=your_group
   WorkingDirectory=/path/to/your/project
   ExecStart=/path/to/your/venv/bin/celery -A your_project worker --loglevel=info
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

3. **Start and enable Celery**:
   ```bash
   sudo systemctl start celery
   sudo systemctl enable celery
   ```

---

### **11. Monitor and Maintain**
1. **Check logs** for Nginx, Hypercorn, and Celery.
2. **Set up monitoring** tools like `supervisord` for process management.

---

# Celery as a service
```bash
[Unit]
Description=Celery Service
After=network.target

[Service]
Type=forking
User=your_user
Group=your_group
WorkingDirectory=/path/to/your/django/project
ExecStart=/path/to/your/virtualenv/bin/celery -A your_project worker --loglevel=info
Restart=always

[Install]
WantedBy=multi-user.target
```bash

```bash
sudo systemctl enable celery.service
sudo systemctl start celery.service
```

By following these steps, you should be able to deploy your ASGI-based Django project with Channels, WebSockets, and Hypercorn on an Ubuntu server, including support for Microsoft SQL Server.
