Mysql
-----


### Install

```
sudo apt-get install mysql-server mysql-client

mysql -V

sudo mysql_secure_installation
```

### Check status
```
sudo systemctl status mysql

service mysql status
```

### Login as root

In ubuntu systems running MySQL 5.7 (and later), the root user is authenticated by the `auth_socket` plugin by default.

The `auth_socket` plugin authenticates users that connect from the `localhost` through the Unix socket file. This means that you can't authenticate as root by providing password.

To log in to the MySQL server as the root user type:
```
sudo mysql
```
You will be presented with the MySQL shell.

### To login as root from an external program (2 options)

#### 1 - Change the authentication method from `auth_socket` to `mysql_native_password`

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql-native-password BY 'very-strong-password';
mysql> FLUSH PRIVILEGES;
```

#### 2 - Create a new administrative user with access to all databases (recommended option)

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'administrator'@'localhost' IDENTIFIED BY 'very-strong-password';
```

### Start, Stop, Restart 
```
sudo service mysql start

sudo service mysql stop

sudo service mysql restart
```

### List and Create Databases
```
mysql> SHOW DATABASES;

mysql> SHOW DATABASES LIKE 'pattern';
```

```
CREATE DATABASE [IF NOT EXISTS] database-name;
```

In MySQL, the schema is the synonym for the database. Creating a new schema also means creating a new database.

#### Access database
```
USE database-name;
```
