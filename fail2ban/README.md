# Fail2Ban

<p align="center">
  <img src="https://1.bp.blogspot.com/-qd0HM80Uemw/Xb7xUbp3p9I/AAAAAAAADPc/OgbBEBR-tl0qjMias90hrsqStVR4G9wNwCLcBGAsYHQ/s1600/Fail2Ban.png" alt="Fail2Ban Logo">
</p>

## Daftar isi

- [Fail2Ban](#fail2ban)
  - [Daftar isi](#daftar-isi)
  - [Deskripsi](#deskripsi)
  - [Instalasi Fail2Ban](#instalasi-fail2ban)
    - [Ubuntu atau Debian](#ubuntu-atau-debian)
    - [CentOS atau RHEL](#centos-atau-rhel)
  - [Konfigurasi Fail2Ban](#konfigurasi-fail2ban)
    - [Whitelist IP](#whitelist-ip)
    - [Proteksi Layanan Zimbra](#proteksi-layanan-zimbra)
      - [Zimbra Submission/SMTP](#zimbra-submissionsmtp)
      - [Zimbra Web](#zimbra-web)

## Deskripsi

Fail2Ban adalah sebuah perangkat lunak keamanan open-source yang dirancang untuk melindungi server dari serangan brute force dan jenis serangan lainnya yang dapat mengancam keamanan sistem. Fail2Ban bekerja dengan memantau log sistem untuk mendeteksi upaya login yang gagal atau aktivitas yang mencurigakan lainnya, seperti percobaan untuk mengakses port yang tidak sah.

Ketika aktivitas mencurigakan terdeteksi, Fail2Ban akan mengambil tindakan yang didefinisikan dalam konfigurasinya, seperti memblokir akses IP yang mencurigakan selama jangka waktu tertentu. Tindakan ini dapat membantu mencegah serangan brute force dan melindungi server dari akses yang tidak sah.

Fail2Ban dapat dikonfigurasi untuk bekerja dengan berbagai jenis aplikasi, seperti SSH, Apache, Nginx, dan lain-lain. Fail2Ban juga menyediakan fitur untuk melihat riwayat aktivitas yang terdeteksi dan menyesuaikan konfigurasi untuk mengoptimalkan deteksi dan tindakan.

Dengan menggunakan Fail2Ban, administrator sistem dapat memperkuat keamanan server mereka dan mengurangi risiko terjadinya pelanggaran keamanan.

## Instalasi Fail2Ban

### Ubuntu atau Debian

1. Pastikan sistem Ubuntu atau Debian Anda sudah terupdate ke versi terbaru dengan menjalankan perintah:

```sh
sudo apt update
sudo apt upgrade
```

2. Instal Fail2Ban dengan menjalankan perintah:

```sh
sudo apt install fail2ban
```

### CentOS atau RHEL

1. Pastikan sistem CentOS atau RHEL Anda sudah terupdate ke versi terbaru dengan menjalankan perintah:

```sh
sudo yum update
```

2. Instal Fail2Ban dengan menjalankan perintah:

```sh
sudo yum install epel-release
sudo yum install fail2ban
```

## Konfigurasi Fail2Ban

Pada konfigurasi awal, jalankan perintah berikut untuk membuat salinan file konfigurasi default Fail2Ban :

```sh
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

### Whitelist IP

1. Jalankan perintah berikut untuk memulai konfigurasi whitelist IP :

```sh
sudo nano /etc/fail2ban/jail.local
```

2. Cari kalimat `ignoreip` dengan menekan kombinasi `CTRL` + `W` kemudian ketikkan `ignoreip` lalu `Enter`. Akan muncul output seperti berikut :

```sh
ignoreip = 127.0.0.1/8 ::1
```

3. Tambahkan IP yang akan di whitelist dan diletakkan setelah `::1` seperti contoh berikut :

```sh
ignoreip = 127.0.0.1/8 ::1 192.168.0.0/24 192.168.2.100/32
```

4. Setelah dimasukkan, simpan konfigurasi tersebut dengan menekan kombinasi `CTRL` + `O` lalu tekan `Enter`
5. Kemudian keluar dari editor dengan menekan `CTRL` + `X`
6. Restart layanan **fail2ban** dengan perintah berikut :

- Ubuntu atau Debian:

```sh
sudo service fail2ban restart
```

- CentOS atau RHEL:

```sh
sudo systemctl restart fail2ban
```

### Proteksi Layanan Zimbra

#### Zimbra Submission/SMTP

1. Buat filter baru untuk proteksi Zimbra Submission/SMTP dengan menjalankan perintah berikut :

```sh
sudo nano /etc/fail2ban/filter.d/zimbra-smtp.conf
```

2. Isikan konfigurasi berikut pada file tersebut :

```conf
[Definition]
failregex = postfix\/submission\/smtpd\[\d+\]: warning: .*\[<HOST>\]: SASL \w+ authentication failed: authentication failure$
            postfix\/smtps\/smtpd\[\d+\]: warning: .*\[<HOST>\]: SASL \w+ authentication failed: authentication failure$

ignoreregex =
```

3. Buat konfigurasi baru untuk proteksi Zimbra Submission/SMTP dengan menjalankan perintah berikut :

```sh
sudo nano /etc/fail2ban/jail.d/zimbra-smtp.conf
```

4. Isikan konfigurasi berikut pada file tersebut :

```conf
[zimbra-smtp]
enabled = true
port = smtp,ssmtp,submission,465,587
filter = zimbra-smtp
logpath = /var/log/zimbra.log
maxretry = 3
banaction = route
findtime = 86400
bantime = 86400
action = route
```

5. Setelah dimasukkan, simpan konfigurasi tersebut dengan menekan kombinasi `CTRL` + `O` lalu tekan `Enter`
6. Kemudian keluar dari editor dengan menekan `CTRL` + `X`
7. Restart layanan **fail2ban** dengan perintah berikut :

- Ubuntu atau Debian:

```sh
sudo service fail2ban restart
```

- CentOS atau RHEL:

```sh
sudo systemctl restart fail2ban
```

#### Zimbra Web

1. Buat filter baru untuk proteksi Zimbra Web dengan menjalankan perintah berikut :

```sh
sudo nano /etc/fail2ban/filter.d/zimbra-web.conf
```

2. Isikan konfigurasi berikut pada file tersebut :

```conf
[Definition]
failregex = .*ip=<HOST>;.*authentication failed for .*$

ignoreregex =
```

3. Buat konfigurasi baru untuk proteksi Zimbra Web dengan menjalankan perintah berikut :

```sh
sudo nano /etc/fail2ban/jail.d/zimbra-web.conf
```

4. Isikan konfigurasi berikut pada file tersebut :

```conf
[zimbra-web]
enabled = true
port = 80,443,7071,9071
filter = zimbra-web
logpath = /opt/zimbra/log/mailbox.log
maxretry = 5
banaction = route
findtime = 86400
bantime = 86400
action = route
```

5. Setelah dimasukkan, simpan konfigurasi tersebut dengan menekan kombinasi `CTRL` + `O` lalu tekan `Enter`
6. Kemudian keluar dari editor dengan menekan `CTRL` + `X`
7. Restart layanan **fail2ban** dengan perintah berikut :

- Ubuntu atau Debian:

```sh
sudo service fail2ban restart
```

- CentOS atau RHEL:

```sh
sudo systemctl restart fail2ban
```