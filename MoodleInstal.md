---
title: Instalasi Moodle dengan Virtual Server Ubuntu
date: 2020
---
<p align="center">
  <a href="">
    <img src="https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/moodle.png" alt="Moodle" width=300 height=100>
  </a>

  <h3 align="center">Moodle</h3>

  <p align="center">
MOODLE (singkatan dari Modular Object-Oriented Dynamic Learning Environment) adalah paket perangkat lunak yang diproduksi untuk kegiatan belajar berbasis internet dan situs web yang menggunakan prinsip social constructionist pedagogy. MOODLE merupakan salah satu aplikasi dari konsep dan mekanisme belajar mengajar yang memanfaatkan teknologi informasi, yang dikenal dengan konsep pembelajaran elektronik atau e-learning. Moodle dapat digunakan secara bebas sebagai produk sumber terbuka (open source) di bawah lisensi GNU. Moodle dapat diinstal di komputer dan sistem operasi apapun yang bisa menjalankan PHP dan mendukung database SQL. 
  </p>
</p>

# Instalasi Moodle

### Step 1: Install Apache HTTP web server

Akses vm dari host
```
ssh student@localhost -p 2200
```
Set Repositories
```
sudo tee /etc/apt/sources.list << !
deb http://repo.apps.cs.ipb.ac.id/ubuntu bionic          main restricted universe multiverse
deb http://repo.apps.cs.ipb.ac.id/ubuntu bionic-updates  main restricted universe multiverse
deb http://repo.apps.cs.ipb.ac.id/ubuntu bionic-security main restricted universe multiverse
!
```
Update and upgrade sistem repositories
```
sudo apt update -y && sudo apt upgrade -y
```
Install apache2
```
sudo apt install apache2
```
Start Apache2 HTTP server 
```
systemctl start apache2
```
Cek status Apache2 HTTP server
```
systemctl status apache2
```
![db](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/apache.PNG)
--masukkan gambar SS--


### Step 2: Install MySQL and PHP

Install
```
sudo apt install mysql-client mysql-server php libapache2-mod-php
```
Start MySQL database on boot
```
systemctl start mysql
```
Cek status MySQL
```
systemctl status mysql
```
Untuk mengamankan database MySQL sebelum membuat database
```
mysql_secure_installation
```
![db](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installation mysql1.PNG)

![db](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installation mysql2.PNG)

![db](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installation mysql3.PNG)

Ada beberapa pertanyaan yang harus dijawab :
- New passwords :
  Enter new passwords
- Remove anonymous users? [Y/n] :y
- Disalow root login remotely? [Y/n]: y
- Remove test database and access to it?[Y/n]: y
- Reload privilage tables now? [Y/n]:y

Setting configuration mysql
```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
Tampilan mysql setelah disetting
---masukkan SS---

Masukkan command line berikut di mysqld
```
default_storage_engine = innodb
innodb_file_per_table = 1
innodb_file_format = Barracuda
innodb_large_prefix = 1
```
----masukkan gambar------

Ctrl X untuk save dan exit

Restart MySQL Database untuk melihat perubahan
```
systemctl restart mysql
```

### Step 3: Install Software Package Tambahan

Untuk menginstall moodle , diperlukan software packages tambahan. Untuk menginstall, jalani command dibawah ini:
```
sudo apt install graphviz aspell ghostscript clamav php7.2-pspell php7.2-curl php7.2-gd php7.2-intl php7.2-mysql php7.2-xml php7.2-xmlrpc php7.2-ldap php7.2-zip php7.2-soap php7.2-mbstring
```

### Step 4 : Buat Database pada moodle
Setelah menginstall packages yang dibutuhkan untuk instalasi moodle, kita perlu membuat Moodle Database dan Moodle MySQL server

Masuk ke database
```
sudo mysql -u root -p
```

Buat Database untuk moodle
```
CREATE DATABASE moodledb;
```

Buat database user dengan password
```
CREATE USER  'moodle@localhost'  IDENTIFIED BY  'newpasswordhere';
```
--keterangan : newpasswordhere diisi dengan password yang akan digunakan untuk Database moodle

Atur akses pengguna penuh database 
```
GRANT ALL PRIVILEGES ON *.* TO 'moodle'@'localhost' IDENTIFIED BY 'newpasswordhere' WITH GRANT OPTION;
```
Simpan perubahan dan keluar dari mysqld
```
FLUSH PRIVILEGES;
EXIT;
```
![db](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/db.JPG)

### Step 5: Download Moodle versi terbaru

Download moodle versi 3.8
```
wget https://download.moodle.org/download.php/direct/stable38/moodle-latest-38.tgz
```
--masukkan gambar--

Extract tarballfile
```
tar -zxvf moodle-latest-38.tgz
```
Pindahkan ‘moodle’ file ke web root directory 
```
sudo mv moodle /var/www/html/moodle
```
Kemudian, buat direktori baru di /var/www/html directory
```
sudo mkdir /var/www/html/moodledata
```
Tambahkan permission berikut untuk akses 
```
sudo chown -R www-data:www-data /var/www/html/moodle/
sudo chmod -R 755 /var/www/html/moodle/
sudo chown www-data /var/www/html/moodledata
```

### Step 6 : Konfigurasi Apache2 HTTP server

Membuat host virtual bernama moodle.conf. Untuk membuatnya, kita akan menjalankan
```
sudo nano /etc/apache2/sites-available/moodle.conf
```
Paste kode di bawah ini. ServerName dan ServerAlias diganti dengan nama domain server kita
```
<VirtualHost *:80>
ServerAdmin admin@example.com
DocumentRoot /var/www/html/moodle/
ServerName example.com
ServerAlias www.example.com
 
<Directory /var/www/html/moodle/>
Options +FollowSymlinks
AllowOverride All
Require all granted
</Directory>
 
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
 
</VirtualHost>
```
Klik **ctrl+x** lalu **Y** dan **enter**

Selanjutnya, aktifkan file host virtual Apache dengan menjalankannya
```
sudo a2enmod rewrite
sudo a2ensite moodle.conf
```
Untuk melakukan perubahan, muat ulang server web Apache
```
systemctl restart apache2
```

### Step 7 : Setting up Moodle melalui web browser

Setelah melakukan restart pada Apache web server, selanjutnya buka browser dan akses link berikut dengan didahului alamat server IP anda seperti berikut :
```
http://your-server-IP/moodle/install.php
```
Pada kesempatan kali ini, kami melakukan akses dengan menggunakan link berikut **localhost:8000/moodle/install.php**

Link tersebut akan melakukan redirect ke laman web seperti dibawah ini yang akan meminta anda untuk memilih bahasa yang akan digunakan. Pilih bahasa yang anda kehendaki lalu klik *Next*.
![installmoodle1](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle1.PNG)

Sebelum menentukan direktori data, jika anda belum mebuat sebelumnya, dapat dilakukan dengan membuat direktori baru untuk data moodle pada home directory anda dengan step-step seperti dibawah ini:
* Buat direktori moodle baru pada home
* Ubah permission menjadi **777**. Ini berguna untuk membuat user, groups, dan other dapat melakukan operasi *read, write*, dan *execute*.
![installmoodle2](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle2.PNG)

Setelah itu, anda akan diteruskan ke laman dibawah ini dimana anda akan melakukan verifikasi ***path*** ke direktori moodle dan penyimpanan data. Jika sudah sesuai, silahkan klik *Next*
![installmoodle3](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle3.PNG)

Pada bagian selanjutnya, pilih Improved MySQL(Native/mysqli) sebagai tipe *Database type* lalu klik *Next*
![installmoodle4](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle4.PNG)

Kemudian, lengkapi rincian tentang Database Host, Database Name, Database User dan Database Password sesuai dengan data yang dibuat sebelumnya. Untuk Database Port dan Unix Socket nya dibiarkan kosong. Klik *Next*
![installmoodle5](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle5.PNG)

Pada bagian berikutnya, instalasi akan melakukan beberapa pemeriksaan server.
![installmoodle8](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle8.PNG)

Jika semua requirements sudah terpenuhi, klik *Continue*
![installmoodle7](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/installmoodle7.PNG)

Pada bagian terakhir, lakukan pengisian data diri untuk membuat akun moodle seperti full name, nick name, city, email, username, password, etc. 
Setelah data sudah terisi dan sesuai, akun anda berhasil dibuat dan akan menampilkan seperti gambar di bawah ini.
![moodle web](https://github.com/afifahnovian/KOMDAT---Virtual-Server/blob/master/moodleweb.JPG)

Moodle sudah siap digunakan. 

### Kelebihan MOODLE
1. Sistem jaringan dan juga keamanan yang dapat disetting sendiri.
2. Ruang akses yang dapat dibatasi dengan jaringan yang dibuat. Karena masing masing user memiliki hak akses yang berbeda
3. System pembelajaran yang dapat disesuaikan dengan kebutuhan user.
4. Kelengkapan fitur meliputi fitur untuk komunikasi (chatting, messaging, atau forum)
	- Fitur untuk pembuatan dan administrasi materi pembelajaran
	- Fitur untuk melacak dan mengikuti perkembangan proses pembelajaran (tracking data) dengan user interface yang mudah dipahami
	- Fitur untuk perluasan fitur (ekstensibilitas plugin) yang fleksibel dengan dukungan fasilitas dokumentasi API (guideline, dan template untuk programming).
5. Proses custumizng yang relative mudah dan banyak.
6. Tersedia secara gratis
	Sebagai perangkat-lunak open source (di bawah lisensi GNU Public License), MOODLE memberikan kebebasan untuk mengkopi, menggunakan, dan memodifikasinya

### Kekurangan MOODLE 
1. Membutuhkan pemahaman yang lebih tentang system yang ada pada moodle
2. Perlunya tenaga ahli yang bertugas sebagai administrator untuk memaintenance dan juga membangun system e-learningnya
3. Memerlukan hardware yang lumayan bagus dan juga harus mengeluarkan biaya yang sedikit lebih.

# Contoh platform Learning Management System lainnya

### Google Classroom
Google Clasroom merupakan sebuah kelas virtual yang menyediakan tempat bagi siswa untuk dapat berinteraksi, berkomunikasi, berdiskusi, dan bekerja sama dalam sebuah kelompok pada sebuah sistem daring. Google Classroom merupakah sebuah LMS karena memungkinkan siswa untuk berpartisipasi secar individual dalam sebuah pembelajaran. Google Classroom dapat digunakan juga oleh guru yang dapat melakukan penentuan deadline waktu pengumpulan tugas, memantau, mereview dan memberikan feedback untuk tugas-tugas para siswa. Siswa sendiri memiliki fitur untuk langsung terhubung melalui Google Drive. Beberpa keunggulan dari Google Classroom adalah :
- Gratis dan mudah
- Sistem yan terintegrasi (Youtube, Gmail, Drive, Google Docs)
- Kemudahan dalam penugasan
- Fitur assessment (Quiz dan semacamnya)
- Forum diskusi

### Schoology
Schoology merupakan salah satu LMS dengan memasukkan sistem pembelajaran dalam jejaring sosial. Fitur menarik yang terdapat dalam Schoology antara lain kelas mata pelajaran, kelompok belajar, maupun sumber belajar. Beberapa keunggulan Schoology sebagai LMS adalah :
- Penggunaan alat sosial seperti ruang diskusi, posting blog, dan pengumuman
- Sistem untuk penilaian dan kuis yang memiliki batas waktu
- Penyimpanan berbasis Cloud
- Notifikasi melalui email dan SMS bagi para siswa
- Pemantauan kehadiran berbasis web
