# Tutorial Setup VPS

Dalam tutorial ini digunakan OS Ubuntu 14.04 64-bit pada VPS Hostinger. Untuk OS di _local machine_ yang digunakan dalam pembuatan tutorial ini adalah Linux Mint 18. Ada beberapa tahap dalam proses setup VPS, yaitu :

1. Setup SSH di local machine
2. Login ke VPS melalui SSH sebagai root
3. Buat user baru
4. Berikan hak akses sebagai root ke user baru
5. Tambahkan _Public Key Authentication_
6. Disable _Password Authentication_
7. Disable login sebagai root
8. Install MariaDB
9. Buat database dan user baru
10. Install Caddy web server
11. Menjalankan Caddy di background
12. Menjalankan Caddy saat startup
13. Setting firewall

### Setup SSH di _local machine_

_Local machine_ adalah komputer yang kita gunakan. Sebelum dapat menggunakan SSH, kita harus menginstall SSH tersebut sesuai dengan OS yang kita gunakan di komputer kita. 

Untuk keamanan VPS, biasanya SSH akan memutus koneksi dari _local machine_ ke VPS secara otomatis jika tidak ada aktifitas selama beberapa menit. Hal ini memang berguna, tapi cukup mengganggu saat kita sedang men-_setup_ VPS. Untuk mencegah SSH _disconnect_ secara otomatis, pada _local machine_ tambahkan ke baris akhir di file `/etc/ssh/ssh_config` perintah sebagai berikut:

```bash
ServerAliveInterval 60
```

### Login ke VPS melalui SSH

Buka terminal, lalu masukkan perintah:

```bash
ssh root@<ip-vps>
```

Setelah itu akan muncul peringatan mengenai keotentikan _host_. Setujui pesan _warning_ tersebut, setelah itu masukkan password untuk root. Jika password benar, maka kita akan masuk ke dalam VPS tersebut sebagai root.

Setelah masuk, lakukan update sistem dengan perintah `apt update` dilanjutkan dengan `apt upgrade`.

### Buat user baru

Untuk keperluan setup selanjutnya, kita akan membuat user baru. Sebagai contoh, di sini dibuat user baru dengan nama `radhi`. Perintah yang digunakan adalah:

```bash
adduser radhi
```

Setelah itu, OS akan meminta kita memasukkan password dan data untuk user baru tersebut:

```bash
Adding user `radhi' ...
Adding new group `radhi' (1000) ...
Adding new user `radhi' (1000) with group `radhi' ...
Creating home directory `/home/radhi' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
Changing the user information for radhi
Enter the new value, or press ENTER for the default
	Full Name []: Radhi Fadlillah
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
```

Kadang saat menggunakan perintah `adduser` akan muncul pesan _warning_ mengenai locale seperti berikut :

```bash
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_PAPER = "id_ID.UTF-8",
	LC_ADDRESS = "id_ID.UTF-8",
	LC_MONETARY = "id_ID.UTF-8",
	LC_NUMERIC = "id_ID.UTF-8",
	LC_TELEPHONE = "id_ID.UTF-8",
	LC_IDENTIFICATION = "id_ID.UTF-8",
	LC_MEASUREMENT = "id_ID.UTF-8",
	LC_NAME = "id_ID.UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
```

Peringatan ini muncul karena ada data locale yang tidak terdaftar di OS tersebut. Untuk mengatasinya, digunakan perintah `locale-gen` untuk meng-_generate_ locale yang diinginkan. Pada pesan _warning_ di atas, ada dua locale yang digunakan, yaitu `id_ID` dan `en_US`, sehingga perintahnya adalah:

```bash
locale-gen en_US en_US.UTF-8 id_ID id_ID.UTF-8
```

Jika sukses, akan muncul pesan _complete_ seperti ini:

```bash
Generating locales...
  en_US.ISO-8859-1... done
  en_US.UTF-8... done
  id_ID.ISO-8859-1... done
  id_ID.UTF-8... done
Generation complete.
```

Setelah itu lakukan rekonfigurasi locale dengan perintah:

```bash
dpkg-reconfigure locales
```

### Berikan hak akses sebagai root ke user baru

Agar user baru yang dibuat dapat melakukan perintah administratif seperti menginstall aplikasi, user baru tersebut harus diberi hak akses sebagai root. Perintah yang digunakan adalah:

```bash
usermod -aG sudo radhi
```

Sekarang user `radhi` sudah dapat menggunakan perintah `sudo`.

### Tambahkan _Public Key Authentication_

Pertama-tama, logout dari SSH. Setelah itu, pada _local machine_ buat SSH _key pair_ dengan perintah:

```bash
ssh-keygen
```

Misalkan nama user di _local machine_ adalah `localuser`, perintah `ssh-keygen` di atas akan menampilkan pesan sebagai berikut:

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/localuser/.ssh/id_rsa):
```

Klik enter untuk menyimpan SSH _key pair_ di lokasi default. Selanjutnya kita akan diminta memasukkan password untuk mengamankan kunci SSH yang dibuat. Di sini bebas ingin memasukkan password atau tidak, akan tetapi dalam tutorial ini kita tidak akan memasukkan password.

Setelah proses ini selesai, akan dihasilkan file _private key_ `id_rsa` dan _public key_ `id_rsa.pub` di folder `.ssh` pada _home directory_ milik `localuser`.

Selanjutnya, kita harus mengirimkan _public key_ yang telah dibuat ke user baru pada VPS yang kita miliki. Untuk melakukannya dapat digunakan perintah `ssh-copy-id`. Karena user baru yang telah dibuat tadi namanya adalah `radhi`, maka perintah lengkapnya adalah:

```bash
ssh-copy-id radhi@<ip-vps>
```

Dalam proses pengiriman, kita akan diminta untuk memasukkan password `radhi` pada VPS yang dituju. Setelah selesai, akan muncul pesan sukses seperti berikut:

```bash
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh radhi@<ip-vps>"
and check to make sure that only the key(s) you wanted were added.
```

Sekarang login kembali ke VPS melalui SSH dengan perintah `ssh radhi@<ip-vps>` tanpa perlu memasukkan password.

### Disable _Password Authentication_ dan login sebagai root

Untuk mencegah serangan _brute force_, direkomendasikan untuk mematikan fitur login SSH menggunakan password. Selain itu, login sebagai root juga umumnya tidak direkomendasikan, sebab umumnya saat melakukan _brute force_ bot secara otomatis akan mencoba menggunakan user `root`.

Caranya, setelah login di VPS sebagai user yang baru (dalam tutorial ini adalah `radhi`), edit SSH _daemon configuration_ menggunakan `nano` (atau text editor lainnya) dengan perintah:

```bash
sudo nano /etc/ssh/sshd_config
```

Cari baris `PasswordAuthentication`:

```bash
#PasswordAuthentication yes
```

Hapus simbol `#` di awal, lalu ubah nilainya menjadi `no`, sehingga menjadi:

```bash
PasswordAuthentication no
```

Selanjutnya, pada baris akhir file tambahkan baris berikut:

```bash
AllowUsers user1 user2 user3
```

di mana `user1`, `user2` dan `user3` adalah nama user yang diizinkan untuk login.

Setelah itu, reload SSH _daemon_ dengan perintah:

```bash
sudo systemctl reload sshd
```

Sekarang login tidak bisa lagi menggunakan password, tapi harus menggunakan _public key_.

### Install MariaDB

Karena dalam tutorial ini digunakan OS Ubuntu 14.04, dan OS tersebut terdaftar di [repositori MariaDB](https://downloads.mariadb.org/mariadb/repositories/), maka kita akan menggunakan MariaDB versi stabil terbaru, yaitu versi 10.1.

Pertama-tama, buka halaman repositori tersebut, lalu pilih OS, versi MariaDB dan lokasi _mirror_ yang diinginkan. Setelah itu ikuti tutorial yang ada di bagian bawah halaman tersebut. Sebagai contoh, di sini digunakan OS Ubuntu 14.04 LTS, MariaDB versi 10.1 dan _mirror_ di `DigitalOcean` sehingga perintah untuk menyimpan repositori MariaDB menjadi sebagai berikut:

```bash
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db
sudo add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://sfo1.mirrors.digitalocean.com/mariadb/repo/10.1/ubuntu trusty main'
```

Setelah repositori berhasil disimpan, update _cache_ dan install MariaDB.

```bash
sudo apt update
sudo apt install mariadb-server -y
```

Saat proses installasi, kita mungkin akan diminta untuk memasukkan password untuk root. Jika tidak, maka password root secara default adalah kosong (tidak ada password).

Selanjutnya, untuk mengamankan MariaDB, gunakan perintah:

```bash
sudo mysql_secure_installation
```

Di sini kita akan diminta untuk menjalankan beberapa proses, di antaranya mengatur password root dan lain-lain:

```bash
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
ERROR 1008 (HY000) at line 1: Can't drop database 'test'; database doesn't exist
 ... Failed!  Not critical, keep moving...
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

### Buat database dan user baru

Sebagai contoh, di sini akan dibuat database dengan nama `yulia_server`. Perintahnya adalah:

```SQL
CREATE DATABASE yulia_server;
```

Untuk melihat hasil penambahan, gunakan perintah:

```SQL
SHOW DATABASES;
```

Selanjutnya, sebagai contoh dibuat user baru dengan nama `server` dan password `password` yang memiliki hak untuk melakukan `SELECT`, `INSERT`, `UPDATE` dan `DELETE` pada database `yulia_server` yang telah dibuat. Perintahnya adalah:

```SQL
CREATE USER 'server'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT, INSERT, UPDATE, DELETE ON yulia_server.* TO 'server'@'localhost';
```

Dengan perintah di atas, kita telah membuat user baru dengan nama `server` yang dapat mengakses dan memanipulasi data dalam database `yulia_server`, tetapi tidak dapat mengubah struktur database tersebut.

### Install Caddy web server

Caddy adalah web server seperti Apache, nginx dan lighttpd, tapi dengan fitur, tujuan dan kelebihan yang berbeda. Salah satu tujuan Caddy adalah untuk mempermudah proses _development_, _deployment_ dan _hosting_ sehingga setiap orang, baik developer, blogger atau desainer, dapat meng-_hosting_ websitenya secara mudah. Selain itu, Caddy adalah web server yang pertama dan satu-satunya yang secara otomatis mengaktifkan HTTPS pada website yang dia _serve_.

Caddy dikembangkan menggunakan bahasa pemrograman Go, sehingga hanya terdiri atas satu buah file _executable_ yang bisa diletakkan di mana saja. Untuk menginstall Caddy, pertama-tama buka halaman [download Caddy](https://caddyserver.com/download). Pilih jenis server, _middleware_ dan DNS _provider_ yang diinginkan. Setelah itu, klik kanan pada OS yang kita gunakan, lalu klik `copy URL`. 

Di sini dipilih Caddy untuk Linux 64-bit, sehigga URL nya adalah `https://caddyserver.com/download/build?os=linux&arch=amd64&features=`.

SSH kembali ke VPS yang digunakan. Sebagai contoh, di sini Caddy akan diinstall di `~/Caddy`. Perintah yang digunakan adalah:

```bash
mkdir Caddy
cd Caddy
wget "https://caddyserver.com/download/build?os=linux&arch=amd64&features=" -O caddy.tar.gz
tar xvf caddy.tar.gz
rm caddy.tar.gz
```

Setelah semua perintah di atas dijalankan, aplikasi Caddy akan tersimpan di direktori `~/Caddy`. Saat dilihat menggunakan perintah `ll` mestinya didapat output sebagai berikut:

```bash
total 14928
drwxrwxr-x 3 radhi radhi     4096 Oct 18 03:27 ./
drwxr-xr-x 7 radhi radhi     4096 Oct 18 03:24 ../
-rwxrwxr-x 1 radhi radhi 15224588 Sep 28 15:13 caddy*
-rw-rw-r-- 1 radhi radhi    13218 Sep 28 15:07 CHANGES.txt
drwxrwxr-x 6 radhi radhi     4096 Sep 28 15:07 init/
-rw-rw-r-- 1 radhi radhi    25261 Sep 28 15:07 LICENSES.txt
-rw-rw-r-- 1 radhi radhi      994 Sep 28 15:07 README.txt
```

Agar Caddy dapat mengakses port 80 (HTTP) dan 443 (HTTPS), kita dapat menggunakan perintah `setcap`. Caranya, masuk ke folder `~/Caddy` tadi, lalu ketikkan perintah:

```bash
sudo setcap cap_net_bind_service=+ep ./caddy
```

Sekarang kita dapat membuat `Caddyfile`, mengaktifkan HTTPS dan lain-lain dengan mengikuti [dokumentasi](https://caddyserver.com/docs) Caddy.

### Menjalankan Caddy di background

Ada beberapa cara agar Caddy dapat dijalankan saat boot. Yang pertama adalah dengan menjadikan Caddy sebagai `service` pada Upstart, systemd atau SysVinit. Yang kedua dan yang palih mudah adalah dengan menggunakan `nohup &`. Pertama-tama, `cd` ke `HOME`, lalu jalankan perintah berikut ini:

```bash
cd Caddy && nohup ./caddy >> ~/caddy.log 2>&1 &
```

Saat diperiksa menggunakan perintah `top`, dapat dilihat Caddy sudah berjalan bersama proses yang lain:

```bash
top - 05:08:58 up 1 day, 17:58,  2 users,  load average: 0,08, 0,03, 0,05
Tasks:  30 total,   1 running,  29 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,0 us,  0,0 sy,  0,0 ni,100,0 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
KiB Mem:   1048576 total,   761320 used,   287256 free,        0 buffers
KiB Swap:   262144 total,        0 used,   262144 free.   688740 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                          
    1 root      20   0   34824   3556   2188 S   0,0  0,3   0:03.41 systemd                                          
    2 root      20   0       0      0      0 S   0,0  0,0   0:00.00 kthreadd/108247                                  
    3 root      20   0       0      0      0 S   0,0  0,0   0:00.00 khelper/108247                                   
   70 root      20   0   32748   1692   1228 S   0,0  0,2   0:00.00 systemd-udevd                                    
  114 root      20   0   31440   2436   2160 S   0,0  0,2   0:00.48 systemd-journal                                  
  261 root      20   0   59588   3132   2444 S   0,0  0,3   0:00.01 sshd                                             
  263 root      20   0   25996   1168    936 S   0,0  0,1   0:00.11 cron                                             
  264 syslog    20   0  184136   2236   1528 S   0,0  0,2   0:00.06 rsyslogd                                         
  268 systemd+  20   0   27272   1488   1252 S   0,0  0,1   0:03.09 systemd-resolve                                  
  300 root      20   0   91684   1388    416 S   0,0  0,1   0:00.00 saslauthd                                        
  301 root      20   0   91684   1016     44 S   0,0  0,1   0:00.00 saslauthd                                        
  302 root      20   0   12768    888    736 S   0,0  0,1   0:00.00 agetty                                           
  303 root      20   0   12768    892    740 S   0,0  0,1   0:00.00 agetty                                           
  412 root      20   0   25368   1704   1392 S   0,0  0,2   0:00.35 master                                           
  416 postfix   20   0   27484   1604   1316 S   0,0  0,2   0:00.07 qmgr                                             
 5136 postfix   20   0   40244   2952   2364 S   0,0  0,3   0:00.02 tlsmgr                                           
10628 root      20   0   89160   3964   3084 S   0,0  0,4   0:00.00 sshd                                             
10639 radhi     20   0   89160   1772    856 S   0,0  0,2   0:00.07 sshd                                             
10640 radhi     20   0   20056   2196   1664 S   0,0  0,2   0:00.02 bash                                             
12965 root      20   0   18128   1768   1336 S   0,0  0,2   0:00.00 mysqld_safe                                      
12966 root      20   0   20104    840    656 S   0,0  0,1   0:00.00 logger                                           
13119 mysql     20   0  650772  57724   8168 S   0,0  5,5   0:04.05 mysqld                                           
13120 root      20   0   20104    972    776 S   0,0  0,1   0:00.00 logger                                           
13337 root      20   0   89160   3964   3084 S   0,0  0,4   0:00.00 sshd                                             
13348 radhi     20   0   89160   1776    856 S   0,0  0,2   0:00.02 sshd                                             
13349 radhi     20   0   20048   2192   1664 S   0,0  0,2   0:00.00 bash                                             
13364 postfix   20   0   27436   1564   1280 S   0,0  0,1   0:00.00 pickup                                           
13389 radhi     20   0   20048    876    348 S   0,0  0,1   0:00.00 bash                                             
13390 radhi     20   0   12628   5056   3588 S   0,0  0,5   0:00.00 caddy
```

### Menjalankan Caddy saat startup

Jika pada langkah sebelumnya kita telah membuat Caddy sebagai `service`, maka Caddy secara otomatis akan berjalan saat startup. Karena tadi kita menggunakan `nohup`, maka kita harus menggunakan teknik lain agar Caddy dapat berjalan saat startup. Ada beberapa cara yang dapat digunakan, seperti menambah script di `/etc/init.d/`, meletakkan perintah di `/etc/rc.local/` atau menggunakan `cron @reboot`. Di sini akan digunakan `cron @reboot`. Caranya, ketikkan perintah:

```bash
crontab -e
```

Jika ini pertama kalinya kita menggunakan perintah `crontab` dan di OS yang kita gunakan terdapat lebih dari satu text editor, kita akan diminta untuk memilih salah satu text editor:

```bash
no crontab for radhi - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.basic

Choose 1-3 [2]: 2
```

Di sini dipilih `nano` sebagai text editor default. Setelah itu, pada di jendela editor yang muncul, tambahkan di baris paling akhir perintah berikut:

```bash
@reboot cd ~/Caddy && nohup ./caddy >> ~/caddy.log 2>&1 &
```

Sintaks `@reboot` berarti perintah di baris tersebut dijalankan setiap kali startup. Alamat `~/Caddy` adalah alamat tempat kita menyimpan Caddy tadi.

### Setting firewall

Firewall adalah suatu keharusan untuk server. Ada beberapa aplikasi firewall yang tersedia untuk Linux, di antaranya adalah iptables, CSF dan UFW. Di sini digunakan UFW, sebab dari ketiga firewall yang telah disebutkan, UFW lah yang paling mudah dan simpel.

UFW atau _Uncomplicated Firewall_ adalah _front-end_ dari iptables. Tujuan utamanya adalah untuk menjadikan proses manajemen firewall sesimpel mungkin dengan _interface_ yang mudah untuk digunakan. UFW memiliki _support_ yang baik dan sangat populer di komunitas Linux.

Untuk menggunakan UFW, pertama-tama install terlebih dahulu:

```bash
sudo apt install ufw
```

Untuk melihat status apakah UFW berjalan atau tidak, digunakan perintah:

```bash
sudo ufw status
```

Umumnya VPS saat ini sudah dikonfigurasi untuk mendukung IPv6, sehingga UFW juga harus diatur agar dapat menangani IPv6. Caranya, buka file konfigurasi UFW dengan perintah:

```bash
sudo nano /etc/default/ufw
```

Lalu cari baris `IPV6` dan set ke `yes`:

```bash
IPV6=yes
```

Simpan, lalu keluar dari file konfigurasi tersebut.

Setelah itu, untuk mempermudah setting firewall selanjutnya, kita dapat membuat aturan default. Aturan yang pertama, secara default server harus selalu dapat mengirimkan data ke luar (ke pengguna). Perintahnya adalah:

```bash
sudo ufw default allow outgoing
```

Yang kedua, secara default tidak mengizinkan pihak luar untuk masuk ke dalam server tersebut:

```bash
sudo ufw default deny incoming
```

Selanjutnya kita perlu memberi izin agar beberapa jenis koneksi dapat dilakukan ke VPS. Ada tiga jenis koneksi yang umumnya harus diizinkan, yaitu `SSH`, `HTTP` dan `HTTPS`:

```bash
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
```

Untuk menghapus salah satu aturan UFW digunakan perintah `ufw delete`. Sebagai contoh, misalkan ingin menghapus aturan `allow ssh`, perintahnya adalah:

```bash
sudo ufw delete allow ssh
```

Untuk mengaktifkan UFW, gunakan perintah:

```bash
sudo ufw enable
```

dan untuk mematikan UFW digunakan perintah:

```bash
sudo ufw disable
```

Jika ingin mereset seluruh aturan UFW, gunakan perintah:

```bash
sudo ufw reset
```