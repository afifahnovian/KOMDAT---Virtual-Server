---
title: Instalasi Moodle dengan Virtual Server Ubuntu
date: 2020
---



# Instalasi Moodle
```bash
```
### Step 1: Install Apache HTTP web server
```bash
#akses vm dari host
ssh student@localhost -p 2200

#update and upgrade sistem repositories
sudo apt update -y && sudo apt upgrade -y

#install apache2
sudo apt install apache2

#start Apache2 HTTP server 
systemctl start apache2

#Cek status Apache2 HTTP server
systemctl status apache2

--masukkan gambar SS--

```
### Step 2: Install MySQL and PHP
```bash
#Install
sudo apt install mysql-client mysql-server php libapache2-mod-php

#Start MySQL database on boot
systemctl start mysql

#Cek status MySQL
systemctl status mysql

#Untuk mengamankan database MySQL sebelum membuat database
mysql_secure_installation

---masukkan SS ---

ada beberapa pertanyaan yang harus dijawab :
- New passwords :
  Enter new passwords
- Remove anonymous users? [Y/n] :y
- Disalow root login remotely? [Y/n]: y
- Remove test database and access to it?[Y/n]: y
- Reload privilage tables now? [Y/n]:y

#setting configuration mysql
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

tampilan mysql setelah disetting
---masukkan SS---

#masukkan command line berikut di mysqld

default_storage_engine = innodb
innodb_file_per_table = 1
innodb_file_format = Barracuda
innodb_large_prefix = 1

----masukkan gambar------

Ctrl X untuk save dan exit

#restart MySQL Database untuk melihat perubahan
systemctl restart mysql
```

### Step 3: Install Software Package Tambahan
```bash
#Untuk menginstall moodle , diperlukan software packages tambahan. Untuk menginstall, jalani command dibawah ini:

sudo apt install graphviz aspell ghostscript clamav php7.2-pspell php7.2-curl php7.2-gd php7.2-intl php7.2-mysql php7.2-xml php7.2-xmlrpc php7.2-ldap php7.2-zip php7.2-soap php7.2-mbstring
```

### Step 4 : Buat Database pada moodle
Setelah menginstall packages yang dibutuhkan untuk instalasi moodle, kita perlu membuat Moodle Database dan Moodle MySQL server

```bash
#Masuk ke database
sudo mysql -u root -p

#Buat Database untuk moodle
CREATE DATABASE moodledb;

#Buat database user dengan password
CREATE USER  'moodle@localhost'  IDENTIFIED BY  'newpasswordhere';

--keterangan : newpasswordhere diisi dengan password yang akan digunakan untuk Database moodle

#Atur akses pengguna penuh database 
GRANT ALL PRIVILEGES ON *.* TO 'moodle'@'localhost' IDENTIFIED BY 'newpasswordhere' WITH GRANT OPTION;

#Simpan perubahan dan keluar dari mysqld
FLUSH PRIVILEGES;
EXIT;

```
### Step 5: Download Moodle versi terbaru
```bash
#Download moodle versi 3.8
wget https://download.moodle.org/download.php/direct/stable38/moodle-latest-38.tgz

--masukkan gambar--

#Extract tarballfile
tar -zxvf moodle-latest-38.tgz

#pindahkan ‘moodle’ file ke web root directory 
sudo mv moodle /var/www/html/moodle

#Kemudian, buat direktori baru di /var/www/html directory
sudo mkdir /var/www/html/moodledata

#tambahkan permission berikut untuk akses 
sudo chown -R www-data:www-data /var/www/html/moodle/
sudo chmod -R 755 /var/www/html/moodle/
sudo chown www-data /var/www/html/moodledata

```
### Step 6 : Configure Apache2 HTTP server



