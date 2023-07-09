---
title: Install DVWA with httpd
date: 2023-07-09
draft: false
tags: [cybersecurity, lab-setup]
---

## Installing DVWA with VirtualHost httpd

This note will guide you to installing DVWA with httpd on centos 7/8

<!--more-->

1. install httpd (Apache2)

```bash
sudo yum install httpd
sudo systemctl enable httpd
sudo systemctl start httpd
```

2. open firewall status to run service http, https

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
```

3. disable welcome.conf

```bash
sudo mv welcome.conf welcome.conf.back
```

4. download dvwa all file to /var/www/html/dvwa/

```bash
cd /var/www/
sudo git clone https://github.com/digininja/DVWA.git
sudo mv DVWA dvwa
```

4.2 rename config file

```bash
cd /var/www/dvwa/config
sudo mv config.inc.php.dist config.inc.php
```

4.3 set permission 

```bash
sudo chown -R $USER.$USER /var/www/dvwa/
sudo chmod -R 755 /var/www/
sudo chmod -R 777 /var/www/dvwa/hackable/
```

3. install mariadb, secure installation, setup database, and allow to remote database

```bash
yum install mariadb-server
sudo systemctl enable mariadb
sudo systemctl start mariadb
sudo mysql_secure_installation
```

3.2 Setting up database

```sql
mysql -u root -p

mysql> create database dvwa;
Query OK, 1 row affected (0.00 sec)

mysql> create user dvwa@localhost identified by 'p@ssw0rd';
Query OK, 0 rows affected (0.01 sec)

mysql> grant all on dvwa.* to dvwa@localhost;
Query OK, 0 rows affected (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

4. install dependecies (php, php-mysqli, php-gd)

```bash
sudo yum install php php-mysqli php-gd
```

5. setting up Virtual Host httpd

```bash
sudo mkdir /etc/httpd/sites-available /etc/httpd/sites-enabled
```

5.1 Include sites-enabled config

```bash
vim /etc/httpd/conf/httpd.conf
---
IncludeOptional sites-enabled/*.conf
```

5.2 Create file configuration

```bash
vim /etc/httpd/sites-available/dvwa.conf
---
<VitualHost *:80>
	DocumentRoot /var/www/dvwa
	DirectoryIndex index.php
</VirtualHost>
```

5.3 enable file configuration

```bash
ln -s /etc/httpd/sites-available/dvwa.conf /etc/httpd/sites-enabled/dvwa.conf
```

6. Adjusting Apache Policies Universally

```bash
sudo setsebool -P httpd_unified 1
```

7. restart apache2 and check

```bash
sudo systemctl restart httpd
curl -I localhost
```

7.1 if you see PHPSESSIONID and 302 HTTP CODE dvwa work, if you dont see you should review your step

8. Setting up dvwa php configuration

```bash
vim /etc/php.ini
---
allow_url_include = On
```

8.1 restart apache2

```bash
sudo systemctl restart httpd
```

9. Setting up dvwa, go to browser http://<you_ip_dvwa>/setup.php

9.1 fixing error if you see

9.2 create and reset database

10. Done!!