# Jarkom-Modul-1-B26-2023

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
Pertama kita perlu mempersiapkan konfigurasi topologi dan setup seperti aturan diatas. Selanjutnya untuk kebutuhan testing, kita perlu menambahkan register domain berupa `riegel.canyon.b26.com` untuk worker Laravel dan granz.channel.b26.com untuk worker PHP yang mengarah pada worker dengan IP `192.191.x.1`. Karena pada konfirgurasi topologi sebelumnya seluruh worker sudah menggunakan DHCP, maka kita perlu modifikasi sedikit pada node `Lugner` dan `Fern` seperti dibawah ini

## Soal 2
