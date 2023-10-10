# Jarkom-Modul-2-E10-2023

## Identitas Kelompok E10 (Jaringan Komputer E)
| Name                 | NRP        |
| ---                  | ---        |
| Mashita Dewi         | 5025211036 |
| Mavaldi Rizqy Hazdi  | 5025211086 |

## Soal Nomor 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologinya!

### Jawaban Nomor 1

#### Topologi:

<img width="406" alt="Screenshot 2023-10-09 221547" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/897ac9b1-9a52-4dc7-91c9-5129d38b1317">

#### Konfigurasi :
- Pandudewanata
  ```ruby
  auto eth0
  iface eth0 inet dhcp

  auto eth1
  iface eth1 inet static
  	address 192.211.1.1
  	netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
  	address 192.211.2.1
  	netmask 255.255.255.0

  auto eth3
  iface eth3 inet static
  	address 192.211.3.1
  	netmask 255.255.255.0
   ```
  
- Nakula
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.1.2
  	netmask 255.255.255.0
  	gateway 192.211.1.1
  ```

- Sadewa
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.1.3
  	netmask 255.255.255.0
  	gateway 192.211.1.1
   ```

- Yudhistira
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.2.2
  	netmask 255.255.255.0
  	gateway 192.211.2.1
  ```

- Werkudara
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.2.3
  	netmask 255.255.255.0
  	gateway 192.211.2.1
  ```
  
- Prabukusuma
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.3.2
  	netmask 255.255.255.0
  	gateway 192.211.3.1
  ```

- Abimanyu
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.3.3
  	netmask 255.255.255.0
  	gateway 192.211.3.1
   ```
  
- Wisanggeni
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.3.4
  	netmask 255.255.255.0
  	gateway 192.211.3.1
  ```

- Arjuna
  ```ruby
  auto eth0
  iface eth0 inet static
  	address 192.211.3.5
  	netmask 255.255.255.0
  	gateway 192.211.3.1
   ```

#### Initial Script
Pada initial project, kami mengubah `root/.bashrc` masing-masing node sehingga saat dijalankan akan langsung melakukan command berikut ini
- Pandudewanata
  ```ruby
  # ~/.bashrc: executed by bash(1) for non-login shells.
  # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
  # for examples
  ...
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.211.0.0/16
  ```

- Yudhistira sebagai DNS Master & Werkudara sebagai DNS Slave
  ```ruby
  # ~/.bashrc: executed by bash(1) for non-login shells.
  # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
  # for examples
  ...
  echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  
  apt-get update
  apt-get install bind9 -y
  ```

## Soal Nomor 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Jawaban Nomor 2
- Melakukan perintah `nano .bashrc` dan mengisikan konfigurasi arjuna.e10.com seperti berikut:
  ```ruby
  echo '
  zone "arjuna.e10.com" {
  	type master;
  	file "/etc/bind/jarkom/arjuna.e10.com";
  };
  ' > /etc/bind/named.conf.local
  ```

- Buat folder jarkom di dalam /etc/bind
  ```ruby
  mkdir /etc/bind/jarkom
  ```

- Copykan file db.local pada path /etc/bind ke dalam folder jarkom yang baru saja dibuat dan ubah namanya menjadi arjuna.e10.com
  ```ruby
  cp /etc/bind/db.local /etc/bind/jarkom/arjuna.e10.com
  ```
- Kemudian buka file arjuna.e10.com dan edit IP Yudhistira masing-masing kelompok:
  ```ruby
  echo '
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800  
  @       IN      SOA     arjuna.e10.com. root.arjuna.e10.com. (
                          2022100601      ; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      arjuna.e10.com.
  @               IN      A       192.211.2.2 ; IP Yudhistira
  www             IN      CNAME   arjuna.e10.com.
  ' > /etc/bind/jarkom/arjuna.e10.com
  ```

- Restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Melakuakn ping `ping www.arjuna.e10.com -c 5` dan melakukan perintah berikut `host -t CNAME www.arjuna.e10.com`
  <img width="554" alt="Screenshot 2023-10-10 021910" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/946d2d1a-2d0f-4a6d-894a-368b2c8775f3">
  <img width="365" alt="Screenshot 2023-10-10 021931" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/daa6559e-a8c4-4103-bd7d-887bbbf057b8">

NOTE: SEMUA KONFIGURASI SAYA LETAKKAN DI .BASHRC 

## Soal Nomor 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

### Jawaban Nomor 3
- Melakukan perintah `nano .bashrc` dan mengisikan konfigurasi abimanyu.e10.com seperti berikut:
  ```ruby
  echo '
  zone "abimanyu.e10.com" {
  	type master;
  	file "/etc/bind/jarkom/abimanyu.e10.com";
  };
  ' > /etc/bind/named.conf.local
  ```

- Masih di dalam file .bashrc, tambahkan konfigurasi ini:
  ```ruby
  echo '
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800  
  @       IN      SOA     abimanyu.e10.com. root.abimanyu.e10.com. (
                          20231009        ; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      abimanyu.e10.com.
  @               IN      A       192.211.2.2 ; IP Yudhistira
  www             IN      CNAME   abimanyu.e10.com.
  ' > /etc/bind/jarkom/abimanyu.e10.com
  ```

- Restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Melakukan ping `ping www.abimanyu.e10.com -c 5` dan menjalankan perintah berikut `host -t CNAME www.abimanyu.e10.com`
  <img width="541" alt="Screenshot 2023-10-10 022001" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/ed931381-caa8-498c-be45-06c8c31175d4">
  <img width="397" alt="Screenshot 2023-10-10 022017" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/b31a7ea9-6bf2-45ad-8d41-5c4f17b0cfa1">
  
## Soal Nomor 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

### Jawaban Nomor 4
- Melakukan perintah `nano .bashrc` dan mengisikan konfigurasi abimanyu.e10.com seperti berikut:
  ```ruby
  echo '
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800  
  @       IN      SOA     abimanyu.e10.com. root.abimanyu.e10.com. (
                          20231010        ; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      abimanyu.e10.com.
  @               IN      A       192.211.2.2 ; IP Yudhistira
  www             IN      CNAME   abimanyu.e10.com.
  parikesit 	IN 	A	192.211.3.3 ; IP Abimanyu
  @		IN	AAAA	::1
  ' > /etc/bind/jarkom/abimanyu.e10.com
  ```

- Testing
  `ping parikesit.abimanyu.e10.com`
  <img width="508" alt="Screenshot 2023-10-10 022056" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/fd47689a-f8b5-434f-a3c1-8d45121aca30">

## Soal Nomor 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

### Jawaban Nomor 5
- Tambahkan konfigurasi berikut ke dalam file `named.conf.local`. Tambahkan reverse dari 3 byte awal dari IP yang ingin dilakukan Reverse DNS. Karena di contoh saya   menggunakan IP 192.211.2 untuk IP dari records, maka reversenya adalah 2.211.192
  ```ruby
  echo '
  zone "2.211.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.211.192.in-addr.arpa";
  };
  ' > /etc/bind/named.conf.local
  ```

- Copykan file db.local pada path /etc/bind ke dalam folder jarkom yang baru saja dibuat dan ubah namanya menjadi 2.211.192.in-addr.arpa
  ```ruby
  cp /etc/bind/db.local /etc/bind/jarkom/2.211.192.in-addr.arpa
  ```

- Edit file 2.211.192.in-addr.arpa menjadi seperti di bawah ini
  ```ruby
  echo ' 
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800  
  @       IN      SOA     abimanyu.e10.com. root.abimanyu.e10.com. (
                          20231009        ; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  2.211.192.in-addr.arpa. IN      NS      abimanyu.e10.com.
  2               	IN      PTR     abimanyu.e10.com.
  ' > /etc/bind/jarkom/2.211.192.in-addr.arpa
  ```

- Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Saya melakukan testing di Sadewa dengan memasukkan konfigurasi berikut ke `.bashrc`-nya
  ```ruby
  apt-get update
  apt-get install dnsutils
  host -t PTR 192.211.2.2
  ```
  <img width="380" alt="Screenshot 2023-10-10 022115" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/5f72f1a9-2e69-4835-a8d3-0f66ca98a7c1">

## Soal Nomor 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Jawaban Nomor 6
#### Konfigurasi pada Server Yudhistira
- Edit file `/etc/bind/named.conf.local` seperti dibawah:
  ```ruby
  echo '
  zone "abimanyu.e10.com" {
      type master;
      notify yes;
      also-notify { 192.211.2.3; }; 
      allow-transfer { 192.211.2.3; };
      file "/etc/bind/jarkom/abimanyu.e10.com";
  };
  ' > /etc/bind/named.conf.local
  ```

- - Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

#### Konfigurasi pada Server Werkudara
- Update package lists dengan menjalankan command:
  ```ruby
  apt-get update
  ```

- Setalah melakukan update silahkan install aplikasi bind9 pada Werkudara dengan perintah:
  ```ruby
  apt-get install bind9 -y
  ```

- Tambahkan konfirgurasi di file `/etc/bind/named.conf.local` pada Werkudara seperti di bawah:
  ```ruby
  echo '
  zone "abimanyu.e10.com" {
      type slave;
      masters { 192.211.2.2; };
      file "/var/lib/bind/abimanyu.e10.com";
  };
  ' > /etc/bind/named.conf.local
  ```
  
- Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Pada server Yudhistira silahkan matikan service bind9 dengan
  ```ruby
  service bind9 stop
  ```
  Pada client Nakula pastikan pengaturan nameserver mengarah ke IP Yudhistira dan IP Werkudara
  ```
  echo '
  nameserver 192.168.2.2; #IP Yudhistira
  nameserver 192.168.2.3; #IP Werkudara
  ' > /etc/resolv.conf
  ```
  Lakukan ping ke abimanyu.e10.com (`ping abimanyu.e10.com -c 5`) pada client Nakula. Jika ping berhasil maka konfigurasi DNS slave telah berhasil akan seperti ini:
  <img width="446" alt="Screenshot 2023-10-10 113152" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/565b35f5-4d2a-43f4-b5ab-a8d327a76d1b">

## Soal Nomor 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

### Jawaban Nomor 7
#### Konfigurasi di Server Yudhistira
- Ganti konfigurasi abimanyu.e10.com dengan ini:
  ```ruby
  echo ' 
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     abimanyu.e10.com. root.abimanyu.e10.com. (
                          20231010	; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      abimanyu.e10.com.
  @               IN      A       192.211.2.2			; IP Yudhistira
  www             IN      CNAME   abimanyu.e10.com.
  baratayuda	IN 	A	192.211.3.3			; IP Abimanyu	
  www.baratayuda  IN      CNAME   baratayuda.abimanyu.e10.com. 
  ns1	IN 	A	A	192.211.2.3			; IP Werkudara
  operation	IN 	NS 	ns1
  ' > /etc/bind/jarkom/abimanyu.e10.com
  ```

- Kemudian edit file `/etc/bind/named.conf.options` lakukan comment `dnssec-validation auto;` dan tambahkan baris berikut pada `/etc/bind/named.conf.options`:
  ```ruby
  allow-query{any;};
  ```

- Tambahkan konfigurasi ini pads `/etc/bind/named.conf.local`:
  ```ruby
  echo '
  zone "abimanyu.e10.com" {
          type master;
          notify yes;
          also-notify { 192.211.2.3; };  Masukan IP Werkudara tanpa tanda petik
          allow-transfer { 192.211.2.3; }; // Masukan IP Werkudara tanpa tanda petik
          file "/etc/bind/jarkom/abimanyu.e10.com";
  };
  
  zone "2.211.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.211.192.in-addr.arpa";
  };
  ' > /etc/bind/named.conf.local
  ```

- Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

#### Konfigurasi pada Server Werkudara
- Edit file `/etc/bind/named.conf.options` lakukan comment `dnssec-validation auto;` dan tambahkan baris berikut pada `/etc/bind/named.conf.options`:
  ```ruby
  allow-query{any;};
  ```

- Tambahkan konfigurasi ini pads `/etc/bind/named.conf.local`:
  ```ruby
  echo '
  zone "abimanyu.e10.com" {
      type slave;
      masters { 192.211.2.2; }; // IP Yudhistira
      file "/var/lib/bind/abimanyu.e10.com";
  };  
    
  zone "baratayuda.abimanyu.e10.com"{  
          type master;
          file "/etc/bind/baratayuda/baratayuda.abimanyu.e10.com";
  };  
  ' > /etc/bind/named.conf.local
  ```

- Buat folder `baratayuda` sesuai instruksi soal:
  ```ruby
  mkdir /etc/bind/baratayuda
  ```

- Isi konfigurasi dibawah pada file `/etc/bind/baratayuda/baratayuda.abimanyu.e10.com`:
  ```ruby
  echo ' 
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     baratayuda.abimanyu.e10.com. root.baratayuda.abimanyu.e10.com. (
                          2023101010	; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      baratayuda.abimanyu.e10.com.
  @               IN      A       192.211.3.3	; IP Abimanyu
  www             IN      CNAME   baratayuda.abimanyu.e10.com.
  ' > /etc/bind/baratayuda/baratayuda.abimanyu.e10.com
  ```

- Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Lakukan `ping baratayuda.abimanyu.e10.com` di Server Sadewa seperti berikut:
  <img width="503" alt="Screenshot 2023-10-10 132333" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/ae7f6b72-b9dc-4fdb-a23d-c4d20ec0211a">

## Soal Nomor 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

### Jawaban Nomor 8
- Pada Server Werkudara ganti konfigurasi `/etc/bind/baratayuda/baratayuda.abimanyu.e10.com` seperti dibawah:
  ```ruby
  echo '
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     baratayuda.abimanyu.e10.com. root.baratayuda.abimanyu.e10.com. (
                                2         ; Serial
                           604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                           604800 )       ; Negative Cache TTL
  ;
  @               IN      NS		baratayuda.abimanyu.e10.com.
  @               IN      A		192.211.3.3			; IP Abimanyu
  www             IN      CNAME		baratayuda.abimanyu.e10.com.
  rjp		IN	A		192.211.3.3			; IP Abimanyu
  www.rjp		IN	CNAME		rjp.baratayuda.abimanyu.e10.com.
  ' > /etc/bind/baratayuda/baratayuda.abimanyu.e10.com
  ```

- Kemudian restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Lakukan `ping rjp.baratayuda.abimanyu.e10.com -c 3` untuk menjalankan testing:
  <img width="527" alt="Screenshot 2023-10-10 135023" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/ae78e2b8-037c-42b2-872c-29b3ab464a7a">

## Soal Nomor 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Jawaban Nomor 9
#### Arjuna (DNS Server)
- instal Bind9 dan Nginx di Arjuna:
  ```ruby
  apt-get update
  apt-get install bind9 nginx
  ```

- Pada topologi kali ini kami akan menggunakan jarkom.site sebagai domain utama seperti di Modul Web Server.
- Mengisi konfigurasi pada `named.conf.local`
  ```ruby
  echo '
   //
   // Do any local configuration here
   //
  
   // Consider adding the 1918 zones here, if they are not used in your
   // organization
   //include "/etc/bind/zones.rfc1918";
  
  
   zone "jarkom.site" {
   		type master;
   		file "/etc/bind/jarkom/jarkom.site";
   };
  
  zone "2.211.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.211.192.in-addr.arpa";
  };
  ' > /etc/bind/named.conf.local
  ```

- Mengisi konfigurasi file `jarkom.site`
  ```ruby
  echo '
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     jarkom.site. root.jarkom.site. (
   			2         ; Serial
   			604800    ; Refresh
   			86400     ; Retry
   			2419200   ; Expire
   			604800 )  ; Negative Cache TTL
  ;
  @       IN      NS      jarkom.site.
  @       IN      A       192.211.3.5
  ' > jarkom.site
  ```

- Mengisi konfigurasi `/etc/bind/jarkom/2.211.192.in-addr.arpa`
  ```ruby
  echo '
   ;
   ; BIND data file for local loopback interface
   ;
   $TTL    604800
   @       IN      SOA     jarkom.site. root.jarkom.site. (
   				2         ; Serial
   				604800    ; Refresh
   				86400     ; Retry
   				2419200   ; Expire
   				604800 )  ; Negative Cache TTL
   ;
   2.211.192.in-addr.arpa.         IN      NS      jarkom.site.
   2                               IN      PTR     jarkom.site.
  ' > /etc/bind/jarkom/2.211.192.in-addr.arpa
  ```

- Testing
  Kami melakukan pengujian (`ping jarkom.site -c 5`) untuk domain yang telah dibuat, dari Server Nakula:
  <img width="497" alt="Screenshot 2023-10-10 144738" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/bdc0f945-11b9-4692-9ab2-d0fac8b78542">

#### Konfigurasi pada Server Prabakusuma (Worker)
- Melakukan penginstallan lalu setup Nginx dan PHP
  ```ruby
  apt-get update && apt install nginx php php-fpm -y
  ```

- Membuat direktori baru
  ```ruby
  mkdir /var/www/jarkom
  ```

- Membuat file `index.php` di dalam folder `/jarkom` dan mengisikan konfigurasi berikut: 
  ```ruby
  echo '
  <?php
   echo "Halo, Kamu berada di Prabukusuma";
  ?>
  ' > /var/www/jarkom/index.php
  ```

- Kemudian kami akan melakukan konfigurasi pada Nginx, pertama masuk ke direktori `/etc/nginx/sites-available` lalu buat file baru dengan nama `jarkom` dan mengisikan konfigurasi berikut:
  ```ruby
  echo '
   server {
  
   	listen 8001;
  
   	root /var/www/jarkom/;
  
   	index index.php index.html index.htm;
   	server_name _;
  
   	location / {
   			try_files $uri $uri/ /index.php?$query_string;
   	}
  
   	# pass PHP scripts to FastCGI server
   	location ~ \.php$ {
   	include snippets/fastcgi-php.conf;
   	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
   	}
  
   location ~ /\.ht {
   			deny all;
   	}
  
   	error_log /var/log/nginx/jarkom_error.log;
   	access_log /var/log/nginx/jarkom_access.log;
   }
  ' > /etc/nginx/sites-available/jarkom
  ```

- Kemudian, kami membuat `symlink`
  ```ruby
  ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
  ```

- Kemudian restart nginx dengan perintah
  ```ruby
  service nginx restart
  ```

- Testing
  jika mengecek apakah konfigurasi yang dibuat sudah benar atau belum, bisa mengunakan perintah berikut
  ```ruby
  nginx -t
  ```
  <img width="481" alt="Screenshot 2023-10-10 145726" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/0b149c0c-fe90-4d5e-8001-708a03749568">

#### Konfigurasi pada Server Abimanyu (Worker)
- Melakukan penginstallan lalu setup Nginx dan PHP
  ```ruby
  apt-get update && apt install nginx php php-fpm -y
  ```

- Membuat direktori baru
  ```ruby
  mkdir /var/www/jarkom
  ```

- Membuat file `index.php` di dalam folder `/jarkom` dan mengisikan konfigurasi berikut: 
  ```ruby
  echo '
  <?php
   echo "Halo, Kamu berada di Abimanyu";
  ?>
  ' > /var/www/jarkom/index.php
  ```

- Kemudian kami akan melakukan konfigurasi pada Nginx, pertama masuk ke direktori `/etc/nginx/sites-available` lalu buat file baru dengan nama `jarkom` dan mengisikan konfigurasi berikut:
  ```ruby
  echo '
   server {
  
   	listen 8002;
  
   	root /var/www/jarkom/;
  
   	index index.php index.html index.htm;
   	server_name _;
  
   	location / {
   			try_files $uri $uri/ /index.php?$query_string;
   	}
  
   	# pass PHP scripts to FastCGI server
   	location ~ \.php$ {
   	include snippets/fastcgi-php.conf;
   	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
   	}
  
   location ~ /\.ht {
   			deny all;
   	}
  
   	error_log /var/log/nginx/jarkom_error.log;
   	access_log /var/log/nginx/jarkom_access.log;
   }
  ' > /etc/nginx/sites-available/jarkom
  ```

- Kemudian, kami membuat `symlink`
  ```ruby
  ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
  ```

- Kemudian restart nginx dengan perintah
  ```ruby
  service nginx restart
  ```

- Testing
  jika mengecek apakah konfigurasi yang dibuat sudah benar atau belum, bisa mengunakan perintah berikut
  ```ruby
  nginx -t
  ```
  <img width="495" alt="Screenshot 2023-10-10 145713" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/1e1259a3-e62a-426c-8ce2-39581c3a631e">

#### Konfigurasi pada Server Wisanggeni (Worker)
- Melakukan penginstallan lalu setup Nginx dan PHP
  ```ruby
  apt-get update && apt install nginx php php-fpm -y
  ```

- Membuat direktori baru
  ```ruby
  mkdir /var/www/jarkom
  ```

- Membuat file `index.php` di dalam folder `/jarkom` dan mengisikan konfigurasi berikut: 
  ```ruby
  echo '
  <?php
   echo "Halo, Kamu berada di Wisanggeni";
  ?>
  ' > /var/www/jarkom/index.php
  ```

- Kemudian kami akan melakukan konfigurasi pada Nginx, pertama masuk ke direktori `/etc/nginx/sites-available` lalu buat file baru dengan nama `jarkom` dan mengisikan konfigurasi berikut:
  ```ruby
  echo '
   server {
  
   	listen 8003;
  
   	root /var/www/jarkom/;
  
   	index index.php index.html index.htm;
   	server_name _;
  
   	location / {
   			try_files $uri $uri/ /index.php?$query_string;
   	}
  
   	# pass PHP scripts to FastCGI server
   	location ~ \.php$ {
   	include snippets/fastcgi-php.conf;
   	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
   	}
  
   location ~ /\.ht {
   			deny all;
   	}
  
   	error_log /var/log/nginx/jarkom_error.log;
   	access_log /var/log/nginx/jarkom_access.log;
   }
  ' > /etc/nginx/sites-available/jarkom
  ```

- Kemudian, kami membuat `symlink`
  ```ruby
  ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
  ```

- Kemudian restart nginx dengan perintah
  ```ruby
  service nginx restart
  ```
  
- Testing
  jika mengecek apakah konfigurasi yang dibuat sudah benar atau belum, bisa mengunakan perintah berikut
  ```ruby
  nginx -t
  ```
  <img width="540" alt="Screenshot 2023-10-10 145131" src="https://github.com/mashitaad/Jarkom-Modul-1-E10-2023/assets/87978863/d42ba27a-c906-4a93-ac27-1a91aeea97dd">

## Soal Nomor 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

### Jawaban Nomor 10
#### Konfigurasi pada Server Arjuna
- Konfigurasi pada file `/etc/nginx/sites-available/lb-jarkom` seperti dibawah:
  ```ruby
  echo '
   # Default menggunakan Round Robin
  upstream myweb {
      server 192.168.3.2:8001;  	#IP Prabakusuma
      server 192.168.3.3:8002;    #IP Abimanyu
      server 192.168.3.4:8003;   	#IP Wisanggeni
  }
  
  server {
      listen 80;
      server_name arjuna.e10.com www.arjuna.e10.com;
  
      location / {
          proxy_pass http://myweb;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
      }
  }
  ' > /etc/nginx/sites-available/lb-jarkom
  ```

- Lalu, kami membuat symlink
  ```ruby
  ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled
  ```

#### Konfigurasi pada Server Yudhistira
- Ubah konfigurasi `/etc/bind/jarkom/arjuna.e10.com` agar mengarah ke Server Arjuna
  ```ruby
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800  
  @       IN      SOA     arjuna.e10.com. root.arjuna.e10.com. (
                          2022100601      ; Serial
                          604800          ; Refresh
                          86400           ; Retry
                          2419200         ; Expire
                          604800 )        ; Negative Cache TTL
  ;
  @               IN      NS      arjuna.e10.com.
  @               IN      A       192.211.3.5 ; IP Arjuna
  www             IN      CNAME   arjuna.e10.com.
```

- Testing
  Lakukan perintah `curl arjuna.e10.com` pada Server Nakula. Pada GNS3 saya ketika saya mengetik perintah tersebut yang muncul adalah sebagai berikut:
  <img width="330" alt="Screenshot 2023-10-10 210001" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/5561e864-0e25-43b9-bc56-1f2a2dc59719">

  Saya telah mencari pada google bahwa hal tersebut merupakan kesalahan dari php, sehingga saya menjalankan perintah `service php7.0-fpm start` dan `service php7.0-fpm restart` pada seluruh    Server Worker
  <img width="561" alt="Screenshot 2023-10-10 210123" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/5d4100a5-51c0-4575-86de-0711cae337f7">
  <img width="575" alt="Screenshot 2023-10-10 210030" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/7684410a-63b6-4a30-92ac-3bd7933711ea">
  <img width="566" alt="Screenshot 2023-10-10 210100" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/0293d1b3-5a26-4c6e-9832-c645fd3fb015">

  Sehingga, ketika saya menjalankan perintah `curl arjuna.e10.com` pada Server Nakula lagi hasilnya sebagai berikut:
  <img width="256" alt="Screenshot 2023-10-10 210137" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/d1975b17-ecc5-4a19-960e-a067d7fadc51">
