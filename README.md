# Zabbix Monitoring Software

Zabbix adalah platform perangkat lunak open-source yang digunakan untuk pemantauan jaringan, server, aplikasi, layanan, dan perangkat keras dalam suatu infrastruktur TI. Zabbix memungkinkan pengguna untuk mengumpulkan data dari berbagai perangkat, menganalisis performa sistem, dan mengirim notifikasi jika ada anomali atau masalah.

## Tech Stack
- [Zabbix](https://www.zabbix.com/download?zabbix=7.0&os_distribution=debian&os_version=12&components=server_frontend_agent&db=mysql&ws=apache) - Main Packages for monitoring server
- [Debian](https://www.debian.org/releases/bookworm/debian-installer/) - Main Operating System that are used in this project
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) - For running virtual machine
- [MySQL](https://www.mysql.com/) - Database that are used in this project
- [Apache](https://httpd.apache.org/download.cgi) - Web Server that are used in this project

## Installation Guide
This is installation guide for installing Zabbix on Debian 12 (Bookworm) 

### Prerequisite
ambil package zabbix dengan melakukan perintah berikut 
```
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-2+debian12_all.deb
```

install pakcage yang sudah diambil dengan melaksanakan perintah berikut
```
dpkg -i zabbix-release_7.0-2+debian12_all.deb
```

ambil package mysql dengan melakukan perintah berikut
```
wget https://dev.mysql.com/get/mysql-apt-config_0.8.32-1_all.deb
```

install package mysql yang sudah diambil dengan melaksanakan perintah berikut
```
apt install ./mysql-apt-config_*_all.deb
```

> biarkan dalam kondisi default dan pilih *ok*

lakukan *dpkg-reconfigure* untuk me-reconfigure package mysql yang sudha kita install sebelumnya
```
dpkg-reconfigure mysql-apt-config
```

update repository dengan perintah berikut
```
apt update
```

install zabbix server, frontend, agent dan database yang digunakan (MySQL/PostgreSQL)
```
apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent mysql-server mysql-client
```

### MySQL SetUp
start dan enable MySQL dengan melakukan perintah
```
systemctl enable --now mysql
```

login ke MySQL dengan melakukan perintah
```
mysql -u root -p
...
Enter password: password
```

konfigurasi database sebagai berikut
```
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
SET GLOBAL log_bin_trust_function_creators = 1;
QUIT;
```

import database schema
```
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
...
Enter password: password
```
Disable log_bin_trust_function_creators option setelah import database schema
```
mysql -u root -p
...
Enter password: password
```
```
SET GLOBAL log_bin_trust_function_creators = 0;
QUIT;
```

### Config Databse for Zabbix
edit file ***/etc/zabbix/zabbix_server.conf***
>```
>DBPassword=password
>```

### Start and Enable Zabbix Server and Agent
jalankan dan enable server dan agent zabbix
```
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```
Open http://localhost for accessing the zabbix management console
