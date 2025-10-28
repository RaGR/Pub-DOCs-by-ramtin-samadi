https://itfix.org.uk/taming-the-terminal-advanced-linux-command-line-tips-tricks-and-productivity-hacks-for-power-users/
https://blog.devops.dev/linux-series-2-advanced-linux-command-line-techniques-47a609bf52be
https://tryhackme.com/r/module/linux-fundamentals


### Advanced Linux Concepts for DevOps and Python Backend Development

**1. Shell Scripting:**

Shell scripting is essential for automating tasks in Linux. Advanced scripting involves using regular expressions, loops, conditionals, and functions. For instance, to automate server setup, you might write a script that installs dependencies, configures users, and sets up services. Here's a simple example:

```bash
#!/bin/bash
# Install nginx
sudo apt-get update
sudo apt-get install nginx -y
# Start nginx service
sudo systemctl start nginx
sudo systemctl enable nginx
echo "Nginx installed and started."
```

This script automates the installation and startup of nginx, a common task in server management.

**2. Systemd and Service Management:**

Systemd is used for managing system services. To create a custom systemd service for a Python Flask app, you can create a service file:

```ini
[Unit]
Description=My Flask App
After=network.target

[Service]
User=youruser
WorkingDirectory=/path/to/app
ExecStart=/usr/bin/python3 /path/to/app/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Save this as `/etc/systemd/system/myflaskapp.service`, then enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myflaskapp.service
sudo systemctl start myflaskapp.service
```

This ensures your Flask app runs on system startup and restarts on failure.

**3. Networking:**

Mastering networking tools like `iptables` for firewall management is crucial. For example, to allow HTTP traffic:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

For troubleshooting, `tcpdump` captures network traffic:

```bash
sudo tcpdump -i eth0 port 80
```

Setting up an SSH tunnel:

```bash
ssh -L 8080:localhost:80 user@remote-server
```

This forwards local port 8080 to port 80 on the remote server.

**4. File Systems and Storage:**

LVM (Logical Volume Management) allows dynamic disk management. To create a logical volume:

```bash
sudo pvcreate /dev/sdb1
sudo vgcreate myvg /dev/sdb1
sudo lvcreate -l 100%FREE -n mylv myvg
sudo mkfs.ext4 /dev/myvg/mylv
```

For shared storage, set up NFS:

```bash
sudo apt-get install nfs-kernel-server
sudo echo "/path/to/share *(rw,sync,no_subtree_check)" >> /etc/exports
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

**5. Environment Management for Python:**

Use `virtualenv` for isolated Python environments:

```bash
python3 -m venv myenv
source myenv/bin/activate
```

For process management, use `supervisord`:

```ini
[program:myapp]
command=/path/to/myapp.py
autostart=true
autorestart=true
```

**6. Automation with Ansible:**

Ansible automates configuration management. A simple playbook to install nginx:

```yaml
- name: Install nginx
  hosts: all
  become: yes
  tasks:
    - name: Install nginx
      package:
        name: nginx
        state: present
```

Run with:

```bash
ansible-playbook -i inventory install_nginx.yml
```

**7. Containerization with Docker:**

Docker containers isolate apps. A Dockerfile for a Flask app:

```Dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

Build and run:

```bash
docker build -t myflaskapp .
docker run -p 5000:5000 myflaskapp
```

**8. Orchestration with Kubernetes:**

Kubernetes manages containerized apps. A simple deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myflaskapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myflaskapp
  template:
    metadata:
      labels:
        app: myflaskapp
    spec:
      containers:
      - name: myflaskapp
        image: myflaskapp:latest
        ports:
        - containerPort: 5000
```

Apply with:

```bash
kubectl apply -f deployment.yaml
```

**9. CI/CD Pipelines:**

Set up a CI/CD pipeline with GitHub Actions:

```yaml
name: CI/CD Pipeline

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.8'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      - name: Run tests
        run: pytest
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to server
        run: |
          ssh user@server 'sudo systemctl restart myflaskapp.service'
```

**10. Infrastructure as Code with Terraform:**

Terraform manages cloud infrastructure. Example for AWS EC2 instance:

```hcl
provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
}
```

Apply with:

```bash
terraform init
terraform apply
```

**11. Monitoring with Prometheus and Grafana:**

Set up Prometheus to monitor your apps. Configuration in `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: 'myapp'
    static_configs:
      - targets: ['localhost:5000']
```

Grafana visualizes metrics from Prometheus, helping you monitor app performance.

**12. Security Hardening:**

Use SELinux to enhance security:

```bash
sudo setenforce 1
sudo semanage permissive -a httpd_t
```

This sets SELinux in enforcing mode and allows HTTPD to operate securely.

**13. Cloud Integration with AWS:**

Deploy a Python app on AWS using EC2:

```bash
aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 1 --instance-type t2.micro --key-name MyKeyPair
```

Configure security groups and IAM roles for enhanced security and access control.

By mastering these advanced Linux concepts, you'll be well-equipped to handle complex tasks in DevOps and Python backend development, ensuring efficient, scalable, and secure systems.
