[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/99wpTe72)
| Name           | NRP        | Kelas     |
| ---            | ---        | ----------|
| Sharfina Ardhiyanti Anam | 5025231111 | Jaringan Komputer (c) |
| Muhammad Davin Aulia Risky | 5025231275 | Jaringan Komputer (c) |

## Find your topology here!

- Link: https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing

- Topology distribution for groups: https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?gid=1757558734#gid=1757558734

## Put your topology config image here!




<br>

## Soal 1

> Topologi terdiri dari node Wortel yang berupa DNS Master*. Selain itu, terdapat pula node Pokcoy sebagai DNS Slave*, yang bertugas sebagai cadangan dari node Wortel.
> <br> </br>
> Selanjutnya terdapat node Tomat dan Taoge yang bekerja sebagai Client*, tiga buah Web Server* yaitu Bayam, Buncis, dan Brokoli, serta Mayur sebagai Router*. Buatlah topologi sesuai dengan pembagian topologi [di sini](https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?usp=sharing) dan konfigurasi topologi [di sini](https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing). Pastikan bahwa setiap node dapat terhubung ke Internet.

> _The topology consists of a Wortel node which is a DNS Master*. In addition, there is also a Pokcoy node as a DNS Slave*, which serves as a backup for the Wortel node._
> <br> </br>
> _Furthermore, there are Tomat and Taoge nodes that work as Client*, three Web Servers*, namely Bayam, Buncis, and Brokoli, then finally Mayur as Router*. Make a topology according to the topology division [here](https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?usp=sharing) and the topology configuration [here](https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing). Make sure that each node can connect to the Internet._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/ab9361af-7f83-48c3-afca-fd6b7e10b582)

- Explanation
  
  Menambahkan code di bawah pada network configuration node Mayur agar node lainnya dapat terhubung

  ```
  up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.109.0.0/16
  ```
  
  Menambahkan code di bawah pada file /root/.bashrc di semua node agar terhubung ke internet

  ```
  echo "nameserver 192.168.122.1" > /etc/resolv.conf
  ```

  
  Menambahkan code di bawah pada file /root/.bashrc untuk node DNS Master dan DNS Slave
  ```
  apt-get update
  apt-get install bind9 -y
  ```

  Menambahkan code di bawah pada file /root/.bashrc untuk node client
  ```
  apt-get update
  apt-get install dnsutils  -y
  apt-get install lynx -y
  ```

  Menambahkan code di bawah pada file /root/.bashrc untuk node web server
  ```
  apt-get update
  apt-get install nginx -y
  apt-get install php -y
  apt-get install php-fpm -y
  ```


<br>

## Soal 2

> Tambahkan konfigurasi untuk domain bayam.yyy.com yang mengarah ke IP node Bayam di DNS Master. Dengan cara yang sama, buat konfigurasi domain brokoli.yyy.com yang mengarah ke IP node Brokoli dan domain buncis.yyy.com yang mengarah ke IP node Buncis. Simpan semua konfigurasi dalam folder Jarkom. Selama pengerjaan soal, ubah yyy menjadi kode kelompok masing-masing (contoh: A02).
> <br> </br>
> Jangan lupa update konfigurasi kedua client agar dapat berkomunikasi dengan semua domain tersebut.


> _Add a configuration for bayam.yyy.com domain that points to the Bayam node IP in the DNS Master. In the same way, create a brokoli.yyy.com domain configuration that points to the Brokoli node IP and a buncis.yyy.com domain that points to the Buncis node IP. Save all configurations in a folder called Jarkom. For this practicum, substitute yyy with the code of each group (ex: A02).
> <br> </br> 
> Don't forget to update the configuration of both clients so that they can communicate with the domains._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-01 225440](https://github.com/user-attachments/assets/c71c5560-81bd-4c2a-9135-b46a42409acf)
  ![Screenshot 2024-10-01 225542](https://github.com/user-attachments/assets/7778f23c-e59e-459a-9065-9907cdc5222e)


- Explanation

  (Pada WorteldnsMaster)

  `apt-get update`
  `apt-get install bind9 -y`
  
  Edit zone DNS Master dengan command `nano /etc/bind/named.conf.local`
  ```
  zone "C39.com" {
	type master;
	file "/etc/bind/jarkom/C39.com";
  };
  ```

  `cp /etc/bind/db.local /etc/bind/jarkom/C39.com` lalu `nano /etc/bind/jarkom/C39.com`

  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA    C39.com. root.C39.com. (
                        2      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
  ;
  @       IN      NS     C39.com.
  @       IN      A       10.109.1.2	; IP dns master

  bayam	IN	A	10.109.3.2	; IP bayamwebserver
  buncis	IN	A	10.109.3.3	; IP bunciswebserver
  brokoli	IN	A	10.109.3.4	; IP brokoliwebserver
  ```

  Restart kembali bind9 dengan `service bind9 restart`

  (Pada kedua client)
  
  Ubah isi  /etc/resolv.conf dengan `nameserver 10.109.1.2`

  Cek apakah sudah terhubung dengan melakukan ping ketiga domain

<br>

## Soal 3

> Tambahkan domain alias berupa www.bayam.yyy.com pada alamat bayam.yyy.com dan www.brokoli.yyy.com pada alamat brokoli.yyy.com.

> _Add a domain alias in the form of www.bayam.yyy.com to the bayam.yyy.com address and www.brokoli.yyy.com to the brokoli.yyy.com address._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/48adb1d1-3a95-44cc-9b14-0a9a978ce181)
  ![image](https://github.com/user-attachments/assets/9cdd796d-57bf-4e96-ad65-b6b40a49f73e)


- Explanation

  Menambahkan konfigurasi sebagai berikut pada DNS Master
  ```
  www.bayam	IN CNAME	bayam.C39.com.
  www.buncis	IN CNAME	buncis.C39.com.
  www.brokoli	IN CNAME	brokoli.C39.com.
  ```

  `service bind9 restart` dan lakukan ping kepada ketiga domain alias tadi

<br>

## Soal 4

> Tambahkan record reverse domain untuk domain brokoli.yyy.com dan buncis.yyy.com.

> _Add a reverse domain record for brokoli.yyy.com and buncis.yyy.com domains._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/ef3af531-3e9d-46d7-a670-a850b48e6306)


- Explanation

  (Pada WorteldnsMaster)
  
  `nano /etc/bind/named.conf.local`

  Menambahkan konfigurasi reverse dari 3 byte awal IP buncis dan brokoli, yakni 10.109.3
  ```
  zone "3.109.10.in-addr.arpa" {
  	type master;
  	file "/etc/bind/jarkom/3.109.10.in-addr.arpa";
  };
  ```

  `cp /etc/bind/db.local /etc/bind/jarkom/3.109.10.in-addr.arpa` lalu `nano /etc/bind/jarkom/3.109.10.in-addr.arpa`

  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA    C39.com. root.C39.com. (
                        2      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

  @	IN NS	C39.com.
  3	IN PTR	buncis.C39.com.
  4	IN PTR	brokoli.C39.com.
  ```
  `service bind9 restart`

  (Pada kedua client)
  
  `nano /etc/resolv.conf` untuk mengganti nameserver menjadi 192.168.122.1 agar bisa menginstall package dnsutils. Lalu jalankan command `apt-get update` `apt-get install dnsutils`

  Kembalikan nameserver agar tersambung dengan DNS Master, yakni 10.109.1.2 dan jalankan command
  `host -t PTR 10.109.3.3` dan `host -t PTR 10.109.3.3` untuk mengecek hasilnya

<br>

note: Mulai dari nomor 5 code akan dijalankan menggunakan file bash sh

## Soal 5

> Ubah record SOA dari domain bayam.yyy.com sesuai dengan ketentuan berikut:
> - Lama waktu server slave menunggu untuk mengecek salinan baru server master adalah sebesar 2 jam.
> - Field yang mengatur revisi file zona ini diubah menjadi tanggal awal praktikum (format YYYYMMDD) kemudian diikuti dengan nomor kelompok (contoh untuk kelompok A02 maka nomornya 02).
> - Lamanya waktu server harus menunggu untuk meminta pembaruan lagi dari nameserver master yang tidak responsif sebesar 30 menit.
> - Lama waktu nama domain di-cache secara lokal sebelum kadaluarsa dan kembali ke nameserver otoritatif untuk informasi terbaru sebesar 12 jam.
> - Jika server slave tidak mendapatkan respons dari server master dalam waktu 2 minggu, server tersebut harus berhenti merespons kueri untuk zona tersebut.

> _Change the SOA record of the bayam.yyy.com domain according to the following conditions:_
> - The length of time the slave server waits to check for a new revision of the master server is 2 hours.
> - The field that regulates the revision of this zone file is changed to the start date of the practicum (YYYYMMDD format) then followed by the group number (ex: for A02 the group number would be 02).
> - The length of time the server has to wait to request another update from an unresponsive master nameserver is 30 minutes.
> - The length of time a domain name is cached locally before it expires and returns to an authoritative nameserver for up-to-date information is 12 hours.
> - If the slave server does not get a response from the master server within 2 weeks, it must stop responding to queries for that zone.

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/0c3bb81a-ccc4-4804-aee9-f842f1716359)


- Explanation

  (Pada WorteldnsMaster)
  
  Membuat zona untuk bayam.C39.com 
  ```
  cat <<EOL > /etc/bind/named.conf.local
  zone "C39.com" {
    type master;
    file "/etc/bind/jarkom/C39.com";
  };

  zone "3.109.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.109.10.in-addr.arpa";
  };

  zone "bayam.C39.com" {
    type master;
    file "/etc/bind/jarkom/bayam.C39.com";
  };
  EOL
  ```

  Agar tidak memiliki 2 SOA record yang berbeda, maka hapus milik bayam.C39.com pada file /etc/bind/jarkom/C39.com

    `cp /etc/bind/db.local /etc/bind/jarkom/bayam.C39.com` untuk menyalin template zone file

  Buat zone file untuk bayam.C39.com dengan SOA sesuai ketentuan soal
  ```
  cat <<EOL > /etc/bind/jarkom/bayam.C39.com
  ;
  ; BIND data file for local loopback interface
  ;
  \$TTL    604800
  @       IN      SOA    C39.com. root.C39.com. (
                     2024100139         ; Serial (tanggal prak)
                           7200         ; Refresh (2 jam)
                           1800         ; Retry (30 mnt)
                        1209600         ; Expire (2 minggu)
                          43200 )       ; Negative Cache TTL (12 jam)

  @       IN      NS     C39.com.       ; NS dns master
  @       IN      A       10.109.3.2    ; IP bayamwebserver
  www IN    CNAME	  bayam.C39.com.
  EOL
  ```

  `service bind9 restart` untuk merestart kembali bind9

  (Pada kedua client)

  ```
  ping bayam.C39.com -c 5
  ping www.bayam.C39.com -c 5
  ```
  Untuk memeriksa apakah konfigurasi sudah benar
  


<br>

## Soal 6

> Untuk menangani request yang berlebih dari client ke ketiga alamat yang tadi dibuat, konfigurasikan node Pokcoy sebagai DNS Slave yang bekerja untuk DNS Master Wortel.

> _To handle excess requests from the client to the three addresses created, configure the Pokcoy node as the DNS Slave that works for Wortel DNS Master._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/bb679cc2-5caa-4eef-9c38-814145f2c5b2)


- Explanation

  (Pada WorteldnsMaster)

  Tambahkan konfigurasi zona `C39.com` dan `bayam.C39.com` pada file `/etc/bind/named.conf.local` sebagai berikut

  ```
  zone "C39.com" {
  	type master;
  	notify yes;
  	also-notify { 10.109.2.2; };
  	allow-transfer { 10.109.2.2; };
  	file "/etc/bind/jarkom/C39.com";
  };
  
  zone "bayam.C39.com" {
  	type master;
  	notify yes;
  	also-notify { 10.109.2.2; };
  	allow-transfer { 10.109.2.2; };
  	file "/etc/bind/jarkom/bayam.C39.com";
  };
  ```
  `service bind9 restart` untuk merestart kembali bind9

  (Pada PakcoyDNSSlave)

  Tambahkan pula konfigurasi zona `C39.com` dan `bayam.C39.com` pada file `/etc/bind/named.conf.local` sebagai berikut

  ```
  zone "C39.com" {
  	type slave;
  	masters { 10.109.1.2; };
  	file "var/lib/bind/C39.com";
  };
  
  zone "k1.vitamin.brokoli.C39.com"{ 
        type master;
        file "/etc/bind/k1/k1.vitamin.brokoli.C39.com";
  };
  ```

  `service bind9 restart` untuk merestart kembali bind9

  Lalu kembali ke WorteldnsMaster, menjalankan command `service bind9 stop` untuk memeriksa kinerja DNS slave

  (Pada kedua client)

  Tambahkan `nameserver 10.109.2.2` pada file `/etc/resolv.conf` dan lakukan `ping C39.com -c 3` untuk memereiksa apakah DNS slave dapat bekerja dengan baik
  
<br>

## Soal 7

> Karena membutuhkan tempat untuk menyimpan resep brokoli, buatlah subdomain berupa vitamin.brokoli.yyy.com dengan alias www.vitamin.brokoli.yyy.com dengan mendelegasikannya dari Wortel ke Pokcoy dengan alamat IP menuju Brokoli yang diatur di folder Vitamin.

> _Since we need a place to store Brokoli recipes, create a subdomain in the form of vitamin.brokoli.yyy.com with an alias of www.vitamin.brokoli.yyy.com by delegating it from Wortel to Pokcoy with an ip to the Brokoli node in a folder called Vitamin._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/8775f7fc-3676-49a4-8fb9-6d8c4d238abb)


- Explanation

  (Pada WorteldnsMaster)

  Untuk membuat delegasi, pada file `/etc/bind/jarkom/C39.com` tambahkan konfigurasi sebagai berikut

  ```
  # ns1 IN A 10.109.2.2
  # vitamin.brokoli IN NS ns1
  ```

  Tambahkan zone `brokoli.C39.com` pada file `/etc/bind/named.conf.local`

  ```
  zone "brokoli.C39.com" {
  	type master;
  	file "/etc/bind/jarkom/brokoli.C39.com";
  };
  ```

  `cp /etc/bind/db.local /etc/bind/jarkom/brokoli.C39.com` untuk menyalin tempale zone file lalu edit file tersebut

  ```
  ;
  ; BIND data file for local loopback interface
  ;
  \$TTL    604800
  @       IN      SOA   brokoli.C39.com. root.brokoli.C39.com. (
                                2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

  @       IN      NS      C39.com.
  @       IN      A       10.109.1.2    ; IP dns master
  www     IN      CNAME	brokoli.C39.com.
  ns1     IN      A       10.109.2.2
  vitamin IN      NS      ns1

  ```
  
  Pada file `/etc/bind/named.conf.options` comment `dnssec-validation auto;` dan tambahkan `allow-query{any;};`

  `service bind9 restart` untuk merestart kembali bind9

  (Pada PakcoyDNSSlave)

  Pada file `/etc/bind/named.conf.options` comment `dnssec-validation auto;` dan tambahkan `allow-query{any;};`

  Edit zone pada `/etc/bind/named.conf.local` sebagai berikut
  ```
  zone "C39.com" {
  	type slave;
  	masters { 10.109.1.2; };
  	file "var/lib/bind/C39.com";
  };

  zone "vitamin.brokoli.C39.com"{ 
        type master;
        file "/etc/bind/Vitamin/vitamin.brokoli.C39.com";
  };
  ```

  ```
  mkdir /etc/bind/Vitamin
  cp /etc/bind/db.local /etc/bind/Vitamin/vitamin.brokoli.C39.com
  ```
  Buat konfigurasi file `/etc/bind/db.local /etc/bind/Vitamin/vitamin.brokoli.C39.com` sebagai berikut
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  \$TTL    604800
  @       IN      SOA     vitamin.brokoli.C39.com. root.vitamin.brokoli.C39.com. (
                                2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      vitamin.brokoli.C39.com.
  @       IN      A       10.109.3.4    ; IP of BrokoliWebServer
  vitamin IN      A       10.109.3.4

  www     IN      CNAME   vitamin.brokoli.C39.com.
  ```
  
  `service bind9 restart` untuk merestart kembali bind9

  (Pada Kedua client)

  Uji konfigurasi dengan melakukan ping
  ```
  ping vitamin.brokoli.C39.com -c 3
  ping www.vitamin.brokoli.C39.com -c 3
  ```
<br>

## Soal 8

> Buatlah subdomain khusus untuk kandungan brokoli dengan akses k1.vitamin.brokoli.yyy.com dengan alias www.k1.vitamin.brokoli.yyy.com yang mengarah ke IP brokoli dan diatur di folder k1.  

> _Create a special subdomain for Brokoli content called k1.vitamin.brokoli.yyy.com with an alias called www.k1.vitamin.brokoli.yyy.com that point to Brokoli node and are organized in a folder called k1._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/48128975-7510-4954-8b6f-186d5c6f42f5)



- Explanation

  (Pada PakcoyDNSSlave)

  Tambhakan zone k1 pada file `/etc/bind/named.conf.local`
  ```
  zone "k1.vitamin.brokoli.C39.com"{ 
        type master;
        file "/etc/bind/k1/k1.vitamin.brokoli.C39.com";
  };
  ```
  
  `mkdir -p /etc/bind/k1` untuk membuat direktori k1
  
  `cp /etc/bind/db.local /etc/bind/k1/k1.vitamin.brokoli.C39.com` untuk menyalin template zone file dan buat konfigurasi file sebagai berikut
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  \$TTL    604800
  @       IN      SOA     k1.vitamin.brokoli.C39.com. root.k1.vitamin.brokoli.C39.com. (
                                2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      vitamin.brokoli.C39.com.
  @       IN      A       10.109.3.4    ; IP of BrokoliWebServer
  
  www     IN      CNAME   k1.vitamin.brokoli.C39.com.
  ```
  `service bind9 restart` untuk merestart kembali bind9
  
  (Pada Kedua client)

  Uji konfigurasi dengan melakukan ping
  ```
  ping k1.vitamin.brokoli.C39.com -c 3
  ping www.k1.vitamin.brokoli.C39.com -c 3
  ```

<br>

## Soal 9

> Bayam, Brokoli, dan Buncis masing-masing berfungsi sebagai web server nginx yang menyajikan resep khusus untuk jenis sayuran yang mereka tangani. Untuk mengaktifkan web server pada masing-masing worker, lakukan deployment website menggunakan sumber yang tersedia di sayur_webserver_nginx. Tambahkan konfigurasi untuk log error ke file /var/log/nginx/error.log dan log access ke file /var/log/nginx/access.log.

> _Bayam, Brokoli, and Buncis each function as nginx web servers that serve special recipes for the type of vegetables they handle. To activate the web server on each worker, do the deployment using the resources available in sayur_webserver_nginx. Add configuration for error log to the file /var/log/nginx/error.log and access log to the file /var/log/nginx/access.log._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/0213b2ae-be40-4b80-b714-cc627f5d58ae)
  ![image](https://github.com/user-attachments/assets/15de0dfd-c2e1-43e0-ba80-3f3e79506d5c)


- Explanation

  (Pada ketiga web server)

  Jalankan command `service php7.2-fpm start`

  Salin isi dari file index pada `sayur_webserver_nginx` ke `/var/www/html/index.php`. Sesuaikan switch case dengan nama topoplogy yang telah dibuat, yakni BayamWebServer, BuncisWebServer, BrokoliWebServer.

  Salin isi dari resepx pada `sayur_webserver_nginx` ke `/var/www/html/resep_x.php`, dimana x sesuai nomornya masing-masing.

  Edit konfigurasi pada file `/etc/nginx/sites-available/default`. uncomment bagian server dan sesuaikan `server_name` sesuai dengan web server masing-masing. Lalu tambahkan konfigurasi yang dibutuhkan serta ketentuan yang diminta soal sebagai berikut
  ```
  server {
  	listen 80;
  	server_name bayam.C39.com;

  	root /var/www/html;
  	index index.php;

  	location / {
  		try_files $uri $uri/ =404;
  	}

    	# location ~ \.php$ {
    	#     include snippets/fastcgi-php.conf;
    	#     fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
    	#     fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    	#     include fastcgi_params;
    	# }

    	error_log /var/log/nginx/error.log;
    	access_log /var/log/nginx/access.log;
  }
  ```
  `service nginx restart` untuk merestart kembali nginx
  
  (Pada Kedua client)

  Uji setiap domain
  ```
  lynx bayam.C39.com
  lynx buncis.C39.com
  lynx brokoli.C39.com
  ```

<br>

## Soal 10

> Pada masing masing worker nginx, akan terdapat beberapa hal yang perlu diperbaiki pada resource yang diberikan untuk bisa menampilkan resep saat halaman dimuat. Analisis kesalahan yang ada di resource melalui file /var/log/nginx/error.log dan perbaiki hingga halaman bisa menampilkan resep sesuai dengan worker nya.

> _On each nginx worker, there will be several things that need to be fixed in the resources provided to be able to display recipes when the page is loaded. Analyze the errors in the resource through the /var/log/nginx/error.log file and fix it until the page can display recipes according to its worker._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/f79229cd-33e8-4003-bbfa-49c6b823a02c)

  ![image](https://github.com/user-attachments/assets/7ba4bcdb-ef1b-4efd-b189-168c23860be4)

  ![image](https://github.com/user-attachments/assets/02a1e377-f779-4046-94f4-a01043b12d45)
  
  ![image](https://github.com/user-attachments/assets/6a4ea657-cc99-48e5-a914-b14c30bb67aa)
  
  ![image](https://github.com/user-attachments/assets/e2b35f9a-fe1a-4343-8351-a5fedd3d73c1)


- Explanation

  Terlihat bahwa ada kesalahan pada bagian nama webserver. Ada kesalahan pada nama file php yang seharusnya resep_n.php dengan n nantinya diganti dengan 1, 2, dan 3. Setelah diperbaiki, akan tampak resep yang tepat untuk tiap nama. Sekian

<br>

  
## Problems

Perbedaan link source antara soal yang ada di github dan  gdocs, yakni pada nomor 12, sempat menyebabakan kebingungan. Selain itu, terdapat beberapa soal yang dapat diinterpretasikan dengan cara berbeda, seperti pada nomor 2, sehingga memungkinkan variasi dalam penulisan kode. Namun, seiring pengerjaan nomor-nomor berikutnya, pemahaman semakin jelas sehingga perlu dilakukan beberapa penyesuaian pada kode yang telah dibuat.

## Revisions (if any)

## Soal 9

> Bayam, Brokoli, dan Buncis masing-masing berfungsi sebagai web server nginx yang menyajikan resep khusus untuk jenis sayuran yang mereka tangani. Untuk mengaktifkan web server pada masing-masing worker, lakukan deployment website menggunakan sumber yang tersedia di sayur_webserver_nginx. Tambahkan konfigurasi untuk log error ke file /var/log/nginx/error.log dan log access ke file /var/log/nginx/access.log.

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/7f936826-4014-48df-9859-883d937256f2)

  ![image](https://github.com/user-attachments/assets/51f89e8f-b8fc-45a8-8d3c-4fd0ee87f610)

  ![image](https://github.com/user-attachments/assets/1ea9e0d3-bf5e-4fa8-9cc1-abfa4f09fff5)

  - Explanation

  Nomor 9 seharusnya adalah versi yang belum diperbaiki, baik nama file resep dan hostname pada switch case. Sehingga nantinya akan menampilkan hasil seperti gambar di atas.
  
<br>

## Soal 11

> Setelah website berhasil dideploy pada masing-masing worker (Bayam, Brokoli, dan Buncis) dan halaman dapat menampilkan resep sayuran yang sesuai,  buatlah custom access log ke file /var/log/nginx/access.log di masing-masing web server worker menggunakan format log tertentu seperti di bawah:
> - Tanggal dan waktu akses dalam format standar log.
Nama worker yang sedang dilayani (misalnya: Bayam, Brokoli, atau Buncis).
> - Alamat IP klien yang mengakses website.
> - Metode HTTP dan URI yang diakses oleh klien.
> - Status respons HTTP yang diberikan oleh server.
> - Jumlah byte yang dikirimkan dalam respons.
> - Waktu yang dihabiskan oleh server untuk menangani permintaan.
> <br> </br>
> Contoh format log yang sesuai: 
[01/Oct/2024:11:30:45 +0000] Jarkom Node Bayam Access from 192.168.1.15 using method "GET /resep/bayam HTTP/1.1" returned stat


> _After successfully deploying the website on each worker (Bayam, Brokoli, and Buncis) and ensuring the pages display the appropriate vegetable recipes, create a custom access log file at /var/log/nginx/access.log on each web server worker using a specific log format as described below:_
> - _Access date and time in standard log format._
> - _Name of the worker serving the request (e.g., Bayam, Brokoli, or Buncis)._
> - _Client IP address accessing the website._
> - _HTTP method and URI accessed by the client._
> - _HTTP response status provided by the server.__
> - _Number of bytes sent in the response.
> - _Time taken by the server to handle the request._
> <br> </br>
> _Example of the appropriate log format:
[01/Oct/2024:11:30:45 +0000] Jarkom Node Bayam Access from 192.168.1.15 using method "GET /resep/bayam HTTP/1.1" returned status 200 with 2567 bytes sent in 0.038 seconds_


**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/fb054278-306f-4302-a3dd-b90cfd82281a)

  ![image](https://github.com/user-attachments/assets/50802cb5-2e0d-4fc9-a586-c52f2f9d580a)

  ![image](https://github.com/user-attachments/assets/33684e43-8b9c-48b4-9266-d5c13002f408)


- Explanation

  Pada file `/etc/nginx/sites-available/default` tambahkan format log custom dan perbarui pengaturan acces_log sebagai berikut
  ```
  cat << 'EOF' > /etc/nginx/sites-available/default

  log_format custom '[$time_local] Jarkom Node $hostname Access from $remote_addr using method "$request_method $uri $server_protocol" returned status $status with $body_bytes_sent bytes sent in $request_time seconds';

  server {
    listen 80;
    server_name bayam.C39.com;

    root /var/www/html;
    index index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    access_log /var/log/nginx/access.log custom;
    error_log /var/log/nginx/error.log;
  }
  EOF
  ```

`service nginx restart` untuk merestart kembali nginx

Lalu client dapat mengakses web dan nantinya hasil log dapat di lihat di file `/var/log/nginx/access.log` masing-masing web server

<br>

## Soal 12

> Informasi vitamin pada sayur brokoli akan ditampilkan pada subdomain vitamin.brokoli.yyy.com di node brokoli, buatlah DocumentRoot yang disimpan pada /var/www/vitamin.brokoli.yyy. Konfigurasikan webserver dengan nama server vitamin.brokoli.yyy.com dan server alias www.vitamin.brokoli.yyy.com. Lakukan konfigurasi Apache Web Server pada Brokoli dengan menggunakan sumber yang tersedia di [sini](https://docs.google.com/uc?export=download&id=1QbGkKXo3jt4c68AdVAkl1hD4LolTUPg2).

> _For information on vitamins in brokoli will be displayed on the vitamin.brokoli.yyy.com subdomain on the brokoli node, create a DocumentRoot stored in /var/www/vitamin.brokoli.yyy. Configure the web server with the server name vitamin.brokoli.yyy.com and server alias www.vitamin.brokoli.yyy.com. Configure the Apache Web Server on Brokoli using [this resource](https://docs.google.com/uc?export=download&id=1QbGkKXo3jt4c68AdVAkl1hD4LolTUPg2)._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/975f51c2-bbe9-40fd-af5c-42677942a38c)

- Explanation

  Buat folder dengan command `mkdir -p /var/www/vitamin.brokoli.C39`

  Download file dari source yang diberikan dan simpan ke dalam `/var/www/vitamin.brokoli.C39/` dengan command sebagai berikut
  ```
  wget "https://docs.google.com/uc?export=download&id=1QbGkKXo3jt4c68AdVAkl1hD4LolTUPg2" -O /var/www/vitamin.brokoli.C39.zip
  unzip /var/www/vitamin.brokoli.C39.zip -d /var/www/vitamin.brokoli.C39
  mv /var/www/vitamin.brokoli.C39/vitamin.brokoli.yyy.com/* /var/www/vitamin.brokoli.C39/
  ```

  Pada `/etc/apache2/ports.conf` ubah menjadi `Listen 8080` agar tidak konflik dengan nginx yang berada di port 80

  Pada `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` buat konfigurasi sebagai berikut
  ```
  <VirtualHost *:8080>
    ServerAdmin webmaster@vitamin.brokoli.C39.com
    ServerName vitamin.brokoli.C39.com
    ServerAlias www.vitamin.brokoli.C39.com

    DocumentRoot /var/www/vitamin.brokoli.C39

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```

  Aktifkan konfigurasi dengan `a2ensite vitamin.brokoli.C39.conf`

  ```
  service apache2 restart
  service apache2 reload
  ```

  Lalu cek hasilnya dengan mengkases `lynx  vitamin.brokoli.C39.com:8080` pada client
  
<br>

## Soal 13

> Pada subdomain vitamin.brokoli.yyy.com, terdapat subfolder /nutrisi yang menyediakan informasi tentang berbagai vitamin dalam brokoli, seperti Vitamin A, C, dan K. Aktifkan directory listing untuk folder /nutrisi, dan buatlah rewrite rule di Apache untuk memperbaiki URL agar halaman seperti www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a.php dapat diakses hanya dengan www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a. Pastikan setiap halaman vitamin dapat diakses langsung melalui url yang telah disederhanakan.

> _On the vitamin.brokoli.yyy.com subdomain, there is a /nutrisi subfolder that provides information about various vitamins in brokoli, such as Vitamin A, C, and K. Activate directory listing for the /nutrisi folder, and create a rewrite rule in Apache to fix the URL so that pages like www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a.php can be accessed only with www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a. Make sure each vitamin page can be accessed directly through the simplified url._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-09 022504](https://github.com/user-attachments/assets/3b3433c0-925a-4c82-934a-76b4b31e151d)

- Explanation

  Jalankan command berikut
  ```
  a2enmod rewrite
  service apache2 restart
  ```

  Buat konfigurasi pada file `/var/www/vitamin.brokoli.C39/nutrisi/.htaccess` agar bisa diakses tanpa .php
  ```
  cat << 'EOF' >  /var/www/vitamin.brokoli.C39/nutrisi/.htaccess
    RewriteEngine On
    
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^([^\.]+)$ $1.php [NC,L]
  EOF
  ```

  Pada file `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` tambahkan konfigurasi berikut
  ```
  <Directory /var/www/vitamin.brokoli.C39/nutrisi>
        Options +Indexes +FollowSymLinks -Multiviews
        AllowOverride All
  </Directory>
  ```

  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx  vitamin.brokoli.C39.com:8080/nutrisi/vitamin_a` pada client
  
<br>

## Soal 14

> Tambahkan alias untuk folder /public/images/ pada subdomain www.vitamin.brokoli.yyy.com agar folder tersebut dapat diakses langsung melalui url www.vitamin.brokoli.yyy.com/img.

> _Add an alias for the /public/images/ folder on the www.vitamin.brokoli.yyy.com subdomain so that the folder can be accessed directly through the url www.vitamin.brokoli.yyy.com/img._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-11 135910](https://github.com/user-attachments/assets/f8d9d054-3426-4721-8626-d71c2dc2c46c)


- Explanation

  Pada file `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` tambahkan listing dan alias sebagai berikut
  ```
  <Directory /var/www/vitamin.brokoli.C39/public/images>
        Options +Indexes
  </Directory>

  Alias /img /var/www/vitamin.brokoli.C39/public/images/
  ```

  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx  vitamin.brokoli.C39.com:8080/img` pada client
<br>

## Soal 15

> Karena terdapat resep rahasia di file /secret/recipe_secret.txt pada subdomain www.vitamin.brokoli.yyy.com, konfigurasikan folder /secret agar tidak dapat diakses oleh pengguna (dengan menampilkan 403 Forbidden).

> _Because there is a secret recipe in the /secret/recipe_secret.txt file on the www.vitamin.brokoli.yyy.com subdomain, configure the /secret folder so that it cannot be accessed by users (by displaying 403 Forbidden)._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-10 135841](https://github.com/user-attachments/assets/156884c4-bd25-4f67-bbbe-fc563935d29e)


- Explanation

  Pada file `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` tambahkan konfigurasi berikut
  ```
  <Directory /var/www/vitamin.brokoli.C39/secret>
        Require all denied
  </Directory>
  ```

  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx  vitamin.brokoli.C39.com:8080/secret` pada client
  
<br>

## Soal 16

> Karena dinilai terlalu panjang coba ubah konfigurasi www.vitamin.brokoli.yyy.com/public/js menjadi www.vitamin.brokoli.yyy.com/js

> _Since it is considered too long, change the configuration from www.vitamin.brokoli.yyy.com/public/js to www.vitamin.brokoli.yyy.com/js._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-10 135930](https://github.com/user-attachments/assets/ea394b80-0232-46d4-b4e2-098efcd1aee2)


- Explanation

  Pada file `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` tambahkan alias pada folder `/public/js` dengan code sebagai berikut
  ```
  Alias /js /var/www/vitamin.brokoli.C39/public/js
  ```

  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx  vitamin.brokoli.C39.com:8080/js` pada client
  
<br>

## Soal 17

> Supaya Web kita aman terkendali maka ubah konfigurasi www.k1.vitamin.brokoli.yyy.com menjadi hanya bisa di akses oleh port 9696 dan 8888

> _To keep our web secure, configure www.k1.vitamin.brokoli.yyy.com to only be accessible through ports 9696 and 8888._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-11 131154](https://github.com/user-attachments/assets/a5e3a9ec-a4fd-46ae-b0be-e406bfdeb25d)


- Explanation

  Download file dari link resource yang diberikan dengan command sebagai berikut
  ```
  wget 'https://docs.google.com/uc?export=download&id=1SRnelY4XrtmhJg_Ly1nUJo1Jf91SnmtB' -O '/var/www/k1.vitamin.brokoli.C39.zip'
  unzip /var/www/k1.vitamin.brokoli.C39.zip -d /var/www/
  rm /var/www/k1.vitamin.brokoli.C39.zip
  mv /var/www/k1.vitamin.brokoli.yyy.com /var/www/k1.vitamin.brokoli.C39
  rm -rf /var/www/k1.vitamin.brokoli.yyy.com
  ```

  Lalu tambahkan konfigurasi pada file `/etc/apache2/sites-enabled/k1.vitamin.brokoli.C39.conf` agar hanya bisa diakses oleh port 9696 dan 8888
  ```
  <VirtualHost *:8888>
        ServerName k1.vitamin.brokoli.C39.com
        ServerAlias www.k1.vitamin.brokoli.C39.com
        DocumentRoot /var/www/k1.vitamin.brokoli.C39

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>

  <VirtualHost *:9696>
        ServerName k1.vitamin.brokoli.C39.com
        ServerAlias www.k1.vitamin.brokoli.C39.com
        DocumentRoot /var/www/k1.vitamin.brokoli.C39

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```

  Pada file `/etc/apache2/ports.conf` tambahkan konfigurasi berikut
  ```
  Listen 8888
  Listen 9696
  ```

  Jalankan command berikut
  ```
  a2ensite /etc/apache2/sites-enabled/k1.vitamin.brokoli.C39.conf
  service apache2 restart
  ```

  Lalu cek hasilnya dengan mengkases `lynx  k1.vitamin.brokoli.C39.com:8888` atau `lynx  k1.vitamin.brokoli.C39.com:9696` pada client

<br>

## Soal 18

> Lanjutkan dari nomor sebelumnya buatlah autentikasi dengan username “Seblak” dan password “sehatyyy” dengan yyy adalah kode kelompok. Letakkan Document Root pada /var/www/k1.vitamin.brokoli.yyy.

> _Continuing from the previous point, create authentication with the username “Seblak” and the password “sehatyyy” where yyy is the group code. Set the Document Root to /var/www/k1.vitamin.brokoli.yyy._

**Answer:**

- Screenshot

  ![Screenshot 2024-10-11 194032](https://github.com/user-attachments/assets/0f6539f4-83cd-4010-8f30-a0ba761a8c7f)
  ![Screenshot 2024-10-11 194046](https://github.com/user-attachments/assets/4656f90e-31bd-48af-962b-dfd5a1f476d1)
  ![Screenshot 2024-10-11 194058](https://github.com/user-attachments/assets/dd6a3dde-fd70-477a-9629-179535def3de)
  ![Screenshot 2024-10-11 194107](https://github.com/user-attachments/assets/d072818d-b4b1-4b37-af93-cf4b8b44bd86)


- Explanation

  Atur username dan password dengan command
  ```
  htpasswd -c -b /etc/apache2/.htpasswd Seblak sehatC39
  ```

  Pada file `/etc/apache2/sites-enabled/k1.vitamin.brokoli.C39.conf` bagian dalam `<VirtualHost *:8888>` dan `<VirtualHost *:9696>` tambahkan konfigurasi berikut
  ```
  <Directory /var/www/k1.vitamin.brokoli.C39>
            AuthType Basic
            AuthName "Restricted"
            AuthUserFile /etc/apache2/.htpasswd
            Require valid-user 
  </Directory>
  ```

  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx  k1.vitamin.brokoli.C39.com:8888` atau `lynx  k1.vitamin.brokoli.C39.com:9696` pada client. Lalu masukkan username dan password sesuai yang ditentukan.


<br>

## Soal 19

> Konfigurasikan agar setiap kali IP Brokoli diakses dengan lynx, secara otomatis akan dialihkan ke www.brokoli.yyy.com (alias).

> _Configure it so that every time Brokoli's IP is accessed using lynx, it is automatically redirected to www.brokoli.yyy.com (alias)._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/90496f8c-3bd9-4647-90b3-1e8314138e7a)
  ![Screenshot 2024-10-11 195037](https://github.com/user-attachments/assets/b1aee37f-edc4-4d97-b54e-ec756f9cccbf)


- Explanation

  Pada file `/etc/nginx/sites-available/default` tambahkan konfigurasi berikut untuk mengarahkan setiap permintaan ke server dengan IP 10.109.3.4 ke domain www.brokoli.C39.com dengan pengalihan permanen (301).
  ```
  server {
    listen 80;
    server_name 10.109.3.4; #IP brokoliwebserver
    return 301 http://www.brokoli.C39.com;
  }
  ```
  
  `service apache2 restart`

  Lalu cek hasilnya dengan mengkases `lynx (IP brokoli)` atau `lynx  10.109.3.4` pada client
  
<br>

## Soal 20

> Karena jumlah pengunjung website www.vitamin.brokoli.yyy.com semakin meningkat dan terdapat banyak gambar random, ubah permintaan gambar yang mengandung substring "vitamin" agar diarahkan ke file vitamin.png.

> _Since the number of visitors to www.vitamin.brokoli.yyy.com is increasing and there are many random images, redirect image requests that contain the substring "vitamin" to the file vitamin.png._

**Answer:**

- Screenshot

  ![image](https://github.com/user-attachments/assets/46232458-c728-4a19-a86b-ee9d85ff9f01)
  ![image](https://github.com/user-attachments/assets/47a17a6a-bff7-4bf5-a6ab-005a5ca96ceb)


- Explanation

  Pada file `/etc/apache2/sites-available/vitamin.brokoli.C39.conf` tambahkan konfigurasi berikut agar permintaan yang mengandung substring "vitamin" diarahkan ke file vitamin.png.
  ```
  RewriteEngine On

    RewriteCond %{REQUEST_URI} .vitamin. [NC]
    RewriteRule ^.*$ /public/images/vitamin.png [L]
  ```

  `service apache2 restart`

  Lalu cek hasilnya pada client dengan mengkases `lynx vitamin.brokoli.C39.com:8080/(string apa saja yang mengandung vitamin)`, misalnya `lynx vitamin.brokoli.C39.com:8080/tes-vitamin.jpg`. Maka akan diarahkan ke file vitamin.png.

<br>
