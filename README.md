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

## Soal 1
Pertama kita perlu mempersiapkan konfigurasi topologi dan setup seperti aturan diatas. Selanjutnya untuk kebutuhan testing, kita perlu menambahkan register domain berupa riegel.canyon.b26.com untuk worker Laravel dan granz.channel.b26.com untuk worker PHP yang mengarah pada worker dengan IP 192.191.x.1. Karena pada konfirgurasi topologi sebelumnya seluruh worker sudah menggunakan DHCP, maka kita perlu modifikasi sedikit pada node `Lugner` dan `Fern` seperti dibawah ini
