# mariadb_galera_cluster
Install Galera cluster on mariadb

### Installing MariaDB on All Nodes (3 nodes)
```
apt-get install mariadb-server -y
systemctl start mariadb
mysql_secure_installation
root password: somepass
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```

#### MariaDB Binding
#### sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
```
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
```
#### copy galera.cnf to /etc/mysql/conf.d/

#### stop mariadb on all nodes:
```
systemctl stop mariadb
```
#### create cluster on node1:
````
galera_new_cluster
````

#### start mariadb on all nodes:

```
systemctl start mariadb
```
```
mysql -u root -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```
### Create Remote User and Grant All Permissions
```
mysql -u root -p
create user 'sepehr'@'% 'IDENTIFIED BY 'password';
flush privileges;
grant all privileges on *.* to 'sepehr'@'%';
SHOW GRANTS FOR 'sepehr'@'%';
```
#### Verify Replication
```
mysql -u sepehr -p
CREATE DATABASE clients
USE clients;
CREATE TABLE users (id int, name varchar(20), surname varchar(20));
INSERT INTO users VALUES (1,"sepehr","sy");
SELECT * FROM users;
```
#### on other node :
```
mysql -h galera_ip -u sepehr -p -e
use clients;
SELECT * FROM users;
```
#### Verify LB via Remote Connection:
```
mysql -h vip -P 3306 -u sepehr -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```
####
Galera Manager
Install Galera Manager Via Link bellow.
https://galeracluster.com/library/documentation/gmd-install.html

#### to set url for the domain https://galera.example.com we must set nginx:
#### copy galera.example.com .cnf to /etc/mysql/conf.d/
```
systemctl reload nginx
```
#### on the Galare Manager Server we must:
1: delete defult config from /etc/nginx/site-enabled
2: change server_name of /etc/nginx/sites-enabled/gmd  to 
```
server {
    server_name 172.16.101.53 galera.int.hicolony.com;
 ```





