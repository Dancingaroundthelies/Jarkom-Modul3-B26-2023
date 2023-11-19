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
>Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit.
>
Kita perlu menggunakan bantuan fungsi default-lease-time dan max-lease-team dimana satuannya adalah detik.
Karena pada switch3 dapat meminjamkan IP selama 3 Menit dan Switch4 dapat meminjamkan IP selama 12 Menit. Sehingga pada Switch3 membutuhkan waktu 180 s dan Switch4 membutuhkan waktu 720 s dan untuk max-lease-time nya adalah 96 menit dimana akan menjadi 5760 s
Selanjutnya kita perlu menambahkan beberapa konfigurasi baru untuk mengatur leasing time pada switch3 dan switch4 sesuai dengan aturan soal. Kita dapat menjalankan command berikut pada DHCP Server
### Script
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
dhcpd --version
#konfigurasi DHCP Server
echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server
echo '
option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;
default-lease-time 600;
max-lease-time 7200;
ddns-update-style none;
subnet 192.191.1.0 netmask 255.255.255.0 {
}
subnet 192.191.2.0 netmask 255.255.255.0 {
}
subnet 192.191.3.0 netmask 255.255.255.0 {
range 192.191.3.16 192.191.3.32;
range 192.191.3.64 192.191.3.80;
option routers 192.191.3.0;
option broadcast-address 192.191.3.255;
option domain-name-servers 192.191.1.2;
default-lease-time 180;
max-lease-time 5760;
}
subnet 192.191.4.0 netmask 255.255.255.0 {
range 192.191.4.12 192.191.4.20;
range 192.191.4.160 192.191.4.168;
option routers 192.191.4.0;
option broadcast-address 192.191.4.255;
option domain-name-servers 192.191.1.2;
default-lease-time 720;
max-lease-time 5760;
}
host Frieren {
hardware ethernet 5a:e5:f2:d5:0c:6c;
fixed-address 192.191.4.1;
option domain-name-servers 192.168.122.1;
}
host Flamme {
hardware ethernet 82:c8:45:f8:b2:8c;
fixed-address 192.191.4.2;
option domain-name-servers 192.168.122.1;
}
host Fern {
hardware ethernet c2:7d:25:f6:9c:6b;
fixed-address 192.191.4.3;
option domain-name-servers 192.168.122.1;
}
host Heiter {
hardware ethernet e2:d9:00:d3:26:10;
fixed-address 192.191.1.2;
option domain-name-servers 192.168.122.1;
option routers 192.191.1.0;
}
host Denken {
hardware ethernet 4a:21:d0:ae:30:69;
fixed-address 192.191.2.1;
option domain-name-servers 192.168.122.1;
}
host Eisen {
hardware ethernet 6a:66:23:bc:a7:af;
fixed-address 192.191.2.2;
option domain-name-servers 192.168.122.1;
}
host Lawine {
hardware ethernet 32:46:2b:95:89:c1;
fixed-address 192.191.3.1;
option domain-name-servers 192.168.122.1;
}
host Linie {
hardware ethernet 3e:66:51:2a:0a:ff;
fixed-address 192.191.3.2;
option domain-name-servers 192.168.122.1;
}
host Lugner {
hardware ethernet ca:e5:52:ee:64:96;
fixed-address 192.191.3.3;
option domain-name-servers 192.168.122.1;
}' > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
```

### Result
![image](https://github.com/Dancingaroundthelies/Jarkom-Modul3-B26-2023/assets/116566988/a7dcb24e-228d-4477-a588-a221afdb2162)


## Soal 6
>Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3. (6)
>

```
apt-get update && apt install nginx php php-fpm -y
apt-get install wget -y
apt-get install unzip -y
apt-get install htop -y
service php7.3-fpm start
php -v
service nginx start
mkdir /var/www/jarkom
cp -v -r /root/modul-3/* /var/www/jarkom/
touch /etc/nginx/sites-available/jarkom

echo '
server {

 	listen 80;

 	root /var/www/jarkom;

 	index index.php index.html index.htm;
 	server_name 192.191.3.1;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	    include snippets/fastcgi-php.conf;
 	    fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
    }

    location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
}' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default

service php7.3-fpm restart
service nginx restart
```

### Result
Jalanin Perintah `lynx localhost` pada masing-masing worker dan hasilnya akan sebagai berikut:
![lynx no 6](https://github.com/Dancingaroundthelies/Jarkom-Modul3-B26-2023/assets/116566988/e5bc4c67-2865-4858-8e13-81fa4491ff1f)


## Soal 7
>Kepala suku dari Bredt Region memberikan resource server sebagai berikut: Lawine, 4GB, 2vCPU, dan 80 GB SSD. Linie, 2GB, 2vCPU, dan 50 GB SSD. Lugner 1GB, 1vCPU, dan 25 GB SSD. Aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. (7)
>

Sebelum mengerjakan perlu untuk melakukan setup terlebih dahulu. Setelah melakukan konfigurasi diatas, sekarang lakukan konfigurasi `Load Balancing` pada node `Eisen` sebagai berikut

### Script
```
apt-get update && apt install nginx php php-fpm -y
apt-get install bind9 nginx -y
nginx -t
apt-get install apache2-utils -y
apt-get install htop -y
touch /etc/nginx/sites-available/lb-jarkom


echo '
# Default menggunakan Round Robin
upstream myweb  {
    server 192.191.3.1; #IP Lawine
    server 192.191.3.2; #IP Linie
    server 192.191.3.3; #IP Lugnar
}


server {
    listen 80;
    server_name granz.channel.b26.com;


    location / {
    proxy_pass http://myweb;
    }
}' > /etc/nginx/sites-available/lb-jarkom


ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled




echo '
#Default menggunakan Round Robin
upstream backend  {
server 192.191.3.1; #IP Lawine
server 192.191.3.2; #IP Linie
server 192.191.3.3; #IP Lugnar
}


server {


listen 80;


server_name granz.channel.b26.com;


        location / {
            proxy_pass http://backend;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;


            auth_basic "Administrator'\''s Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
        }


        location ~ /\.ht {
            deny all;


        }
location ~ /its {
        proxy_pass https://www.its.ac.id;
        proxy_set_header Host www.its.ac.id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }


error_log /var/log/nginx/lb_error.log;
access_log /var/log/nginx/lb_access.log;


}' > /etc/nginx/sites-available/lb-jarkom


echo '
#Default menggunakan Round Robin
upstream backend  {
server 192.191.3.1; #IP Lawine
server 192.191.3.2; #IP Linie
server 192.191.3.3; #IP Lugnar
}


server {


listen 80;


server_name granz.channel.b26.com;


        location / {
            proxy_pass http://backend;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;


            auth_basic "Administrator'\''s Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
        }


        location ~ /\.ht {
            deny all;
        }


        location /its {
            proxy_pass https://www.its.ac.id;
        }


error_log /var/log/nginx/lb_error.log;
access_log /var/log/nginx/lb_access.log;


}' > /etc/nginx/sites-available/lb-jarkom


echo '
#Default menggunakan Round Robin
upstream backend  {
server 192.191.3.1; #IP Lawine
server 192.191.3.2; #IP Linie
server 192.191.3.3; #IP Lugnar
}


server {


listen 80;


server_name granz.channel.b26.com;


        location / {
            proxy_pass http://backend;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;


            auth_basic "Administrator'\''s Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
        }


        location ~ /\.ht {
            deny all;
        }


        location /its {
            proxy_pass https://www.its.ac.id;
        }


error_log /var/log/nginx/lb_error.log;
access_log /var/log/nginx/lb_access.log;


}' > /etc/nginx/sites-available/lb-jarkom


service nginx restart

```
Sebelum melakukan setup soal 7. Buka kembali Node `DNS Server` dan arahkan `domain` tersebut pada `IP Load Balancer Eisen`
```
#Default menggunakan Round Robin
upstream backend  {
server 192.191.3.1; #IP Lawine
server 192.191.3.2; #IP Linie
server 192.191.3.3; #IP Lugnar
}

server {

listen 80;

server_name granz.channel.b26.com;

        location / {
            proxy_pass http://backend;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;

            auth_basic "Administrator's Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
        }

        location ~ /\.ht {
            deny all;
        }

        location /its {
            proxy_pass https://www.its.ac.id;
        }

error_log /var/log/nginx/lb_error.log;
access_log /var/log/nginx/lb_access.log;

}


```

### Result
Jalankan perintah berikut pada client `Sein`
```
ab -n 1000 -c 100 http://granz.channel.b26.com/
```
Dan akan menampilkan seperti ini.

![image](https://github.com/Dancingaroundthelies/Jarkom-Modul3-B26-2023/assets/116566988/942fb2d4-feb6-48ae-a677-666e4bbeeaed)



### Soal 8
>Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut: 1. Nama Algoritma Load Balancer; 2. Report hasil testing pada Apache Benchmark; 3.Grafik request per second untuk masing masing algoritma; 4. Analisis
>

Sebelum mengerjakan perlu untuk melakukan setup terlebih dahulu. Selebihnya untuk konfigurasinya sama dengan soal 7. Kemudian tinggal panggil `htop` pada node `Lawine`

### Result
![htop no8](https://github.com/Dancingaroundthelies/Jarkom-Modul3-B26-2023/assets/116566988/2bafd3f7-828c-4e63-92e7-49bc087e8b5d)


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
