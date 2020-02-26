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

