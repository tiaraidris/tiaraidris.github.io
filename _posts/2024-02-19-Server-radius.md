---
---
# Integrasi Mikrotik dengan FreeRadius

Seperti yang kita ketahui bahwa Routerboard keluaran terbaru untuk jenis Router Indoor seperti hAP series, RB750r Series memiliki kapasitas NAND Storage yang minimalis. 
Hal ini tentu akan sangat berpengaruh bagi kita yang ingin melakukan konfigurasi routerboard dengan mengaktifkan service-service yang mana akan memakan banyak resource dari NAND tersebut. 
Misalnya jika kita ingin menggunakan service hotspot + usermanager dan juga PPP Tunnel. Supaya dapat menggunakan service tersebut kita juga diharuskan menambahkan data-data untuk sistem autentikasi.

Nah, untuk mengatasi permasalahan tersebut kita bisa memanfaatkan sebuah server autentikasi (RADIUS) eksternal. Ada sebuah aplikasi RADIUS Server opensource yang sudah support dengan Mikrotik dan juga paling banyak digunakan, yaitu FreeRadius. 
Kali ini kita akan mencoba bagaimana cara konfigurasi dasar dari Freeradius sehingga bisa diintegrasikan dengan perangkat Mikrotik. 

## Instalasi FreeRadius

Pertama, kita akan melakukan instalasi FreeRadius. Kali ini kita akan menggunakan sebuah PC Server dengan sistem operasi Linux Ubuntu. 
Langkah instalasinya pun cukup mudah, kita tinggal ketikkan pada Terminal Linux sebuah command line seperti berikut:

## sudo apt-get install freeradius freeradius-utils freeradius-mysql

FreeRadius ini bukanlah sebuah aplikasi besar sehingga untuk instalasinya sendiri tidak memerlukan begitu banyak space dari media penyimpanan. Nah, setelah proses instalasi selesai, kita akan melakukan sedikit penyesuain pada FreeRadius. 
Secara dasar kita akan melakukan konfigurasi pada file di FreeRadius, yaitu radiusd.conf, clients.conf, dan users.

## Integrasi Mikrotik dengan FreeRadius

Supaya FreeRadius dapat berintegrasi dengan Mikrotik, maka kita perlu melakukan konfiguasi pada masing-masing perangkat baik pada RADIUS Server (FreeRadius) dan juga RADIUS Client (Mikrotik). 
Kita akan mencoba melakukan konfigurasi seperti pada contoh topologi berikut. Kita akan membangun sebuah service hotspot dengan username dan password tersimpan pada FreeRadius. 

![Capture JPG-radius1](https://github.com/tiaraidris/taca/assets/156057283/467e7e15-da63-447f-8dee-c14e10a8544c)

Pertama, kita akan mengedit pada file radiusd.conf. Dengan menggunakan Terminal Linux ketikkan command line berikut:

## sudo nano /etc/freeradius/radiusd.conf

Kemudian kita pastikan untuk line dengan script "$INCLUDE clients.conf" tidak terdapat tanda "#", yang artinya script tersebut telah aktif. Tetapi jika masih ada tanda "#" didepannya maka script tersebut belum aktif.

![Capture JPG-radius2](https://github.com/tiaraidris/taca/assets/156057283/8bea9c0f-ada6-4325-9e83-4a502f0de8cb)

Selanjutanya kita akan konfigurasi pada file clients.conf. Dengan file ini kita akan menentukan perangkat RADIUS Client yang akan terkoneksi ke FreeRadius. Kita ketikkan sebuah command line pada Terminal linux seperti 
berikut.
## sudo nano /etc/freeradius/clients.conf

Pada file tersebut kita akan tambahkan sebuah script yang berupa alamt IP Address dari RADIUS Client (Mikrotik) dan juga Secret yang akan digunakan oleh RADIUS Client terkoneksi ke FreeRadius. Kedua jenis parameter tersebut yang utama. 
Kita juga bisa menambahkan parameter yang lain namun hanya optional saja. Nah, seperti topologi diatas kita isikan IP Address dari Mikrotik yaitu 172.16.1.25 dan secret yaitu coba12345. Kita bisa menambahkan pada baris paling bawah dengan 
contoh format penulisan seperti tampilan berikut.

![Capture JPG-radius3](https://github.com/tiaraidris/taca/assets/156057283/daa6633e-3e66-44c2-b966-23770e356413)

Langkah selanjutnya kita akan menambahakan akun berupa username dan password yang digunakan untuk login hotspot. Kita tambahkan kombinasi user dan password tersebut pada file Users. Kita ketikkan pada Terminal Linux sebuah command line seperti berikut.

## sudo nano /etc/freeradius/users

Kemudian kita tambahkan pada file tersebut sebuah script seperti pada tampilan dibawah ini. Kita akan mencoba menambhakan username=coba dan password=12345, dan yang kedua username=test dan password=test123

![Capture JPG4](https://github.com/tiaraidris/taca/assets/156057283/3db45e79-7f1d-4c04-a93d-d4420f079477)

Jika terdapat keterangan Access-Accept maka Freeradius telah berjalan dengan baik namun jika terdapat keterangan Access-Reject maka hal itu menandakan terdapat setingan yang kurang tepat.

Kita juga bisa melakukan debug dengan menggunakan perintah freeradius -X. Nah, sampai sini konfigurasi freeradius sudah selesai.

## Konfigurasi Mikrotik

Setelah kita menyelesaikan setting FreeRadius, langkah selanjutnya kita akan melakukan konfigurasi disisi Router Mikrotik. Untuk konfigurasi hotspot pada mikrotik sendiri bisa kita lihat pada artikel sebelumnya disini.

Setelah service hotspot sudah kita tambahkan pada router mikrotik, kita akan melakukan integrasi supaya router bisa mengambil data username dan password login hotspot yang berada di RADIUS Server eksternal. Langkah-langkahnya cukup mudah.
Pertama kita pilih menu Radius -> Klik Add [+], kemudian akan muncul tampilan seperti berikut.

![Capture JPG5](https://github.com/tiaraidris/taca/assets/156057283/c57683a7-7e17-4460-9a28-d11819324162)

Karena kita akan membuat service hotspot maka kita pilih pada paremeter 'Service' yaitu hotspot. Dan jangan lupa untuk menentukan IP Address dari perangkat RADIUS Server 172.16.1.100 dan juga secret untuk RADIUS Client seperti yang kita tambahkan pada aplikasi freeradius diatas, yaitu coba12345.

Selanjutnya pada menu hotspot, di bagian server profile kita tentukan juga untuk menggunakan data dari RADIUS Server.

![Capture JPG6](https://github.com/tiaraidris/taca/assets/156057283/c8fbbca5-7608-4386-81f7-34302a82e8b2)

Setelah semua langkah-langkah diatas sudah kita lakukan, maka kita akan mencoba untuk menggunakan service hotspot tersebut dengan username dan password yang terdapat pada database dari FreeRadius. Kita akan menggunakan salah satu akun yang telah kita buat sebelumnya, 
misal kita memakai username=test dan password=test123. Dan apabila kita sudah berhasil login, kita bisa monitoring perangkat client pada tab 'Active'.

Apabila kita lihat maka pada client tersebut terdapat sebuah tanda/flag 'R' (Radius) yang mengindikasikan bahwa client tersebut menggunakan akun dari RADIUS Server.

![Capture JPG7](https://github.com/tiaraidris/taca/assets/156057283/50bd25fa-eaa0-4aa9-9ec6-8211db5cb761)

Nah, demikianlah bagaimana cara konfigurasi dasar untuk mengintegrasikan Mikrotik dengan FreeRadius. Untuk yang lebih advance lagi, sehingga kita bisa lebih mudah dari segi management user, kita bisa melakukan kombinasi dengan MySQL Server (sebagai penyimpanan database yang lebih fleksibel).

![Capture JPG8](https://github.com/tiaraidris/taca/assets/156057283/47706bac-aa76-4efc-9c1b-4bc501d0d79d)

            Parsing Database Freeradius ke MySQL Server

Dan juga bisa dikombinasi dengan aplikasi-aplikasi lain untuk management user seperti Access Manager, DaloRadius, phpRADmin, dll.
