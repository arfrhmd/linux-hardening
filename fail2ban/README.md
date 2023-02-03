## Konfigurasi Fail2Ban

### Whitelist IP

1. Masuk ke dalam server menggunakan SSH atau aplikasi PuTTY

```sh
ssh username@yourserver.com -p 22
```

2. Masuk sebagai user `root`

```sh
su -
```

3. Jalankan perintah berikut untuk memulai konfigurasi whitelist IP :

```sh
nano /etc/fail2ban/jail.local
```

4. Cari kalimat `ignoreip` dengan menekan kombinasi `CTRL` + `W` kemudian ketikkan `ignoreip` lalu `Enter`. Akan muncul output seperti berikut :

```sh
ignoreip = 127.0.0.1/8 ::1
```

5. Tambahkan IP yang akan di whitelist dan diletakkan setelah `::1` seperti contoh berikut :

```sh
ignoreip = 127.0.0.1/8 ::1 192.168.0.0/24 192.168.2.100/32
```

6. Setelah dimasukkan, simpan konfigurasi tersebut dengan menekan kombinasi `CTRL` + `O` lalu tekan `Enter`
7. Kemudian keluar dari editor dengan menekan `CTRL` + `X`
8. Restart layanan **fail2ban** dengan mengetik :

```sh
service fail2ban restart
```
