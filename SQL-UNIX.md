DOC from the good guys (Ubuntu Family):

https://ubuntu.com/server/docs/install-and-configure-a-mysql-server

# MYSQL INSTALATION AND CONFIGURATION

```bash
sudo apt install mysql-server

alter:
sudo apt-get install python3.12.3-dev 
sudo apt-get install mysql-client
sudo apt-get install libmysqlclient-dev
sudo apt-get install libssl-dev
apt-get install pkg-config

sudo service mysql status
sudo ss -tap | grep mysql
sudo service mysql restart
sudo service mysql status
sudo journalctl -u mysql
sudo systemctl restart mysql.service

mysql -u root -p
```

# MySQL Command Line Client
```SQL
show databases;
use DB_Name;
show tables;

SELECT * FROM Table_Name;
CREATE DATABASE db_name;
GRANT ALL PRIVILEGES ON *.* TO 'db_user'@'localhost' IDENTIFIED BY 'P@s$w0rd123!';
``` 





```bash
# Access MySQL shell
sudo mysql

# Check the password policy
SHOW VARIABLES LIKE 'validate_password%';

# Lower the password policy (optional)
SET GLOBAL validate_password.policy = LOW;
SET GLOBAL validate_password.length = 6;

# Set a password for the root user
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
FLUSH PRIVILEGES;

# Exit MySQL shell
exit

# Restart MySQL
sudo systemctl restart mysql

# Test the new password
mysql -u root -p
```
