# Jarkom-Modul-3-B26-2023

Anggota Kelompok 

|   Nama                             | NRP      |
| ------                             | ------   |
|  Nur Azizah                        |5025211252|
|  Rayhan Almer Kusumah              |5025211115|
|  Faiz Haq Noviandra Ciptadi Putra  |5025211132|

## Topologi
![topology prak 3](https://github.com/Dancingaroundthelies/Jarkom-Modul3-B26-2023/assets/116566988/d6bacd42-2e27-4c21-94d2-fbbc2dd45767)

## Config
Aura
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.191.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.191.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.191.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.191.4.0
	netmask 255.255.255.0
```
Himmel
```
auto eth0
iface eth0 inet static
	address 192.191.1.1
	netmask 255.255.255.0
	gateway 192.191.1.0
```
Heiter
```
auto eth0
iface eth0 inet dhcp
hwaddress ether e2:d9:00:d3:26:10
```
Denken
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 4a:21:d0:ae:30:69
```
Eisen
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 6a:66:23:bc:a7:af
```
Frieren
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 5a:e5:f2:d5:0c:6c
```
Flamme
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 82:c8:45:f8:b2:8c
```
Fern
```
auto eth0
iface eth0 inet dhcp
hwaddress ether c2:7d:25:f6:9c:6b
```
Lawine
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 32:46:2b:95:89:c1
```
Linie
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 3e:66:51:2a:0a:ff
```
Lugner
```
auto eth0
iface eth0 inet dhcp
hwaddress ether ca:e5:52:ee:64:96
```

## Soal 1
>Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.
>
Pertama kita perlu mempersiapkan konfigurasi topologi dan setup seperti aturan diatas. Selanjutnya untuk kebutuhan testing, kita perlu menambahkan register domain berupa `riegel.canyon.b26.com` untuk worker Laravel dan granz.channel.b26.com untuk worker PHP yang mengarah pada worker dengan IP `192.191.x.1`. Karena pada konfirgurasi topologi sebelumnya seluruh worker sudah menggunakan DHCP, maka kita perlu modifikasi sedikit pada node `Lugner` dan `Fern` seperti dibawah ini
- **Lugner (PHP Worker)**
  ```
  auto eth0
  iface eth0 inet static
	address 192.191.3.1
	netmask 255.255.255.0
	gateway 192.191.3.0
  ```
- **Fern (Laravel Worker)**
  ```
  auto eth0
  iface eth0 inet static
	address 192.191.4.1
	netmask 255.255.255.0
	gateway 192.191.4.0
  ```
Selanjutnya pada DNS Server (Heiter), kita perlu menjalankan command dibawah ini
### Script
```sh
echo 'zone "riegel.canyon.b26.com" {
    type master;
    file "/etc/bind/sites/riegel.canyon.b26.com";
};

zone "granz.channel.b26.com" {
    type master;
    file "/etc/bind/sites/granz.channel.b26.com";
};

zone "1.191.192.in-addr.arpa" {
    type master;
    file "/etc/bind/sites/1.191.192.in-addr.arpa";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/sites
cp /etc/bind/db.local /etc/bind/sites/riegel.canyon.b26.com
cp /etc/bind/db.local /etc/bind/sites/granz.channel.b26.com
cp /etc/bind/db.local /etc/bind/sites/1.191.192.in-addr.arpa

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.b26.com. root.riegel.canyon.b26.com. (
                        2023111401      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      riegel.canyon.b26.com.
@       IN      A       192.191.4.1     ; IP Fern
www     IN      CNAME   riegel.canyon.b26.com.' > /etc/bind/sites/riegel.canyon.b26.com

echo '
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.b26.com. root.granz.channel.b26.com. (
                        2023111401      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      granz.channel.b26.com.
@       IN      A       192.191.3.1     ; IP Lugner
www     IN      CNAME   granz.channel.b26.com.' > /etc/bind/sites/granz.channel.b26.com

echo 'options {
      directory "/var/cache/bind";

      forwarders {
              192.168.122.1;
      };

      // dnssec-validation auto;
      allow-query{any;};
      auth-nxdomain no;    # conform to RFC1035
      listen-on-v6 { any; };
}; ' >/etc/bind/named.conf.options

service bind9 start
```
## Soal 2
>Semua CLIENT harus menggunakan konfigurasi dari DHCP Server. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80
>
Sebelum mengerjakan perlu untuk melakukan setup untuk DHCP Server terlebih dahulu. Selanjutnya kita perlu menjalankan command dibawah ini pada DHCP Server

### Script
```sh
echo 'subnet 192.191.1.0 netmask 255.255.255.0 {
}

subnet 192.191.2.0 netmask 255.255.255.0 {
}

subnet 192.191.3.0 netmask 255.255.255.0 {
    range 192.191.3.16 192.191.3.32;
    range 192.191.3.64 192.191.3.80;
    option routers 192.191.3.0;
}' > /etc/dhcp/dhcpd.conf
```

## Soal 3
>Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168
>
Selanjutnya kita perlu menambahkan beberapa konfigurasi baru untuk switch4 dengan menjalankan command dibawah ini

### Script
```sh
echo 'subnet 192.191.1.0 netmask 255.255.255.0 {
}

subnet 192.191.2.0 netmask 255.255.255.0 {
}

subnet 192.191.3.0 netmask 255.255.255.0 {
    range 192.191.3.16 192.191.3.32;
    range 192.191.3.64 192.191.3.80;
    option routers 192.191.3.0;
}

subnet 192.191.4.0 netmask 255.255.255.0 {
    range 192.191.4.12 192.191.4.20;
    range 192.191.4.160 192.191.4.168;
    option routers 192.191.4.0;
} ' > /etc/dhcp/dhcpd.conf
```


## Soal 4
>Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut
>
kita akan menambahkan beberapa konfigurasi seperti `option broadcast-address` dan `option domain-name-server` agar dapat DNS yang telah disiapkan sebelumnya dapat digunakan

### Script
```sh
subnet 192.191.3.0 netmask 255.255.255.0 {
    ...
    option broadcast-address 192.191.3.255;
    option domain-name-servers 192.191.1.2;
    ...
}

subnet 192.191.4.0 netmask 255.255.255.0 {
    option broadcast-address 192.191.4.255;
    option domain-name-servers 192.191.1.2;
} 
```
Lalu gunakan `shell` script sebagai berikut
```sh
echo 'subnet 192.191.1.0 netmask 255.255.255.0 {
}

subnet 192.191.2.0 netmask 255.255.255.0 {
}

subnet 192.191.3.0 netmask 255.255.255.0 {
    range 192.191.3.16 192.191.3.32;
    range 192.191.3.64 192.191.3.80;
    option routers 192.191.3.0;
    option broadcast-address 192.191.3.255;
    option domain-name-servers 192.191.1.2;
}

subnet 192.191.4.0 netmask 255.255.255.0 {
    range 192.191.4.12 192.191.4.20;
    range 192.191.4.160 192.191.4.168;
    option routers 192.191.4.0;
    option broadcast-address 192.191.4.255;
    option domain-name-servers 192.191.1.2;
} ' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server start
```

Selanjutnya kita perlu untuk melakukan setup untuk DHCP Relay terlebih dahulu. Selanjutnya kita perlu menjalankan command dibawah ini pada DHCP Relay
```sh
echo '# Defaults for isc-dhcp-relay initscript
# sourced by /etc/init.d/isc-dhcp-relay
# installed at /etc/default/isc-dhcp-relay by the maintainer scripts

#
# This is a POSIX shell fragment
#

# What servers should the DHCP relay forward requests to?
SERVERS="192.191.1.1"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3 eth4"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""' > /etc/default/isc-dhcp-relay

service isc-dhcp-relay start 
```
Lalu pada file `/etc/sysctl.conf` lakukan uncommented pada `net.ipv4.ip_forward=1`  

Terakhir jangan lupa untuk restart seluruh client agar dapat melakukan leasing IP dari DHCP Server

## Soal 5

## Soal 6

## Soal 7

## Soal 8

## Soal 9
> Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. (9)
>
Sebelum mengerjakan perlu untuk melakukan setup terlebih dahulu. Setelah melakukan setup pada node `Eisen` sekarang lakukan testing pada load balancer yang telah dibuat sebelumnya. Yang menjadi pembeda adalah kita harus melakukan testing menggunakan `1 worker`, `2 worker`, dan `3 worker`.
### Script
Jalankan command berikut pada client `Revolte`
```
ab -n 100 -c 10 http://www.granz.channel.b26.com/ 
```


## Soal 10
> Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/
>
Sebelum mengerjakan perlu untuk melakukan setup terlebih dahulu. Setelah itu, lakukan beberapa konfigurasi sebagai berikut
### Script
```
mkdir /etc/nginx/rahasisakita
htpasswd -c /etc/nginx/rahasisakita/htpasswd netics
```
Lalu, masukkan passwordnya `ajkb26` <br>
Jika sudah memasukkan `password` dan `re-type password`. Sekarang bisa dicoba dengan menambahkan command berikut pada setup nginx.
```
auth_basic "Restricted Content";
auth_basic_user_file /etc/nginx/rahasisakita/htpasswd;
```


## Soal 11
> Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. (11) hint: (proxy_pass)
>
Sebelum mengerjakan perlu untuk melakukan setup terlebih dahulu. Setelah itu, lakukan beberapa konfigurasi tambahan pada nginx sebagai berikut
### Script
```
location ~ /its {
    proxy_pass https://www.its.ac.id;
    proxy_set_header Host www.its.ac.id;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```
Berikut adalah full scriptnya
```
echo 'upstream worker {
    server 192.191.3.1;
    server 192.191.3.2;
    server 192.191.3.3;
}

server {
    listen 80;
    server_name granz.channel.b26.com www.granz.channel.b26.com;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
    }

    location ~ /its {
        proxy_pass https://www.its.ac.id;
        proxy_set_header Host www.its.ac.id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}' > /etc/nginx/sites-available/lb_php
```
Maksudnya adalah ketika kita melakukan akses pada endpoint yang mengandung `/its` akan diarahkan oleh `proxy_pass` menuju `https://www.its.ac.id`. Jadi ketika melakukan testing pada client `Revolte` dengan menggunakan perintah berikut
```
lynx www.granz.channel.b26.com/its
```
