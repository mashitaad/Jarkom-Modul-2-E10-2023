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
  @               IN      A       192.211.3.5 ; IP Arjuna
  www             IN      CNAME   arjuna.e10.com.
  ' > /etc/bind/jarkom/arjuna.e10.com
  ```

- Restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Melakukan ping `ping www.arjuna.e10.com -c 5` dan melakukan perintah berikut `host -t CNAME www.arjuna.e10.com`

  <img width="540" alt="Screenshot 2023-10-11 124208" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/0794a8a2-8245-4375-95ef-552f84f8f4f9">

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
  @               IN      A       192.211.3.4 ; IP Abimanyu
  www             IN      CNAME   abimanyu.e10.com.
  ' > /etc/bind/jarkom/abimanyu.e10.com
  ```

- Restart bind9 dengan perintah
  ```ruby
  service bind9 restart
  ```

- Testing
  Melakukan ping `ping www.abimanyu.e10.com -c 5` dan menjalankan perintah berikut `host -t CNAME www.abimanyu.e10.com`

  <img width="539" alt="Screenshot 2023-10-11 124531" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/7b060a8a-e504-414a-b5d1-3a95377b8f3c">
  
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

  <img width="488" alt="Screenshot 2023-10-11 124636" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/7c72446f-1da6-43dd-8fc6-177df992458a">

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

- Kemudian restart bind9 dengan perintah
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
  @               IN      A       192.211.2.2	; IP Yudhistira
  www             IN      CNAME   abimanyu.e10.com.
  baratayuda	IN 	A	192.211.3.3	; IP Abimanyu
  www.baratayuda	IN 	CNAME 	baratayuda.abimanyu.e10.com.
  ns1		IN	A	192.211.2.3 	; IP Werkudara	
  @		IN	AAAA	::1
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

#### Konfigurasi pada Server Arjuna
- Konfigurasi pada file `/etc/nginx/sites-available/lb-jarkom` seperti dibawah:
  ```ruby
  echo '
   # Default menggunakan Round Robin
  upstream myweb {
      server 192.168.3.2;  	#IP Prabakusuma
      server 192.168.3.3;    #IP Abimanyu
      server 192.168.3.4;   	#IP Wisanggeni
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
  - Lakukan perintah `curl arjuna.e10.com` pada Server Nakula. Pada GNS3 saya ketika saya mengetik perintah tersebut yang muncul adalah sebagai berikut:
  
    <img width="330" alt="Screenshot 2023-10-10 210001" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/5561e864-0e25-43b9-bc56-1f2a2dc59719">

  - Saya telah mencari pada google bahwa hal tersebut merupakan kesalahan dari php, sehingga saya menjalankan perintah `service php7.0-fpm start` dan `service php7.0-fpm restart` pada          seluruh Server Worker
    
    <img width="561" alt="Screenshot 2023-10-10 210123" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/5d4100a5-51c0-4575-86de-0711cae337f7">
    <img width="575" alt="Screenshot 2023-10-10 210030" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/7684410a-63b6-4a30-92ac-3bd7933711ea">
    <img width="566" alt="Screenshot 2023-10-10 210100" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/0293d1b3-5a26-4c6e-9832-c645fd3fb015">

  - Sehingga, ketika saya menjalankan perintah `curl arjuna.e10.com` pada Server Nakula lagi hasilnya sebagai berikut:
  
    <img width="256" alt="Screenshot 2023-10-10 210137" src="https://github.com/mashitaad/Jarkom-Modul-2-E10-2023/assets/87978863/d1975b17-ecc5-4a19-960e-a067d7fadc51">


## Soal Nomor 11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

### Jawaban Nomor 11
Untuk mengerjakan soal ini, diperlukan beberapa konfigurasi. Salah satunya adalah Yudhistira dimana kita akan mengubah IP yang awalnya ke Werkudara menuju Abimanyu. Lalu diperlukan juga untuk menggunakan ServerAlias agar bisa menggunakan www nantinya.
- Masukkan script `no11.sh` ini ke Yudhistira
```ruby
# 11
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e10.com. root.abimanyu.e10.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.e10.com.
@       IN      A       192.211.3.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.e10.com.
parikesit IN    A       192.211.3.3     ; IP Abimanyu
ns1     IN      A       192.211.2.3     ; IP Werkudara
baratayuda IN   NS      ns1' > /etc/bind/jarkom/abimanyu.e10.com

service bind9 restart
```
- Masukkan script `no11.sh` ke dalam abimanyu
```ruby
apt-get update
apt-get install dnsutils -y
apt-get install lynx -y
apt-get install apache2 -y
apt-get install libapache2-mod-php7.0 -y
service apache2 start
apt-get install wget -y
apt-get install unzip -y
apt-get install php -y
echo -e "\n\nPHP Version:"
php -v

a2enmod rewrite

wget -O '/var/www/abimanyu.e10.com' 'https://drive.usercontent.google.com/download?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc'
unzip -o /var/www/abimanyu.e10.com -d /var/www/
mv /var/www/abimanyu.yyy.com /var/www/abimanyu.e10
rm /var/www/abimanyu.e10.com
rm -rf /var/www/abimanyu.e10/abimanyu.yyy.com

cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.e10.com.conf

rm /etc/apache2/sites-available/000-default.conf

echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/abimanyu.e10

  ServerName abimanyu.e10.com
  ServerAlias www.abimanyu.e10.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.e10.com.conf

a2ensite abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```
lynx abimanyu.e10.com
```
![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/11.png?raw=true)

## Soal Nomor 12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

### jawaban Nomor 12
Disini kita menggunakana bantuan Directory yang melakukan rewrite Indexes. Agar dapat melakukan `Alias`
- Masukkan script `no12.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/abimanyu.e10
  ServerName abimanyu.e10.com
  ServerAlias www.abimanyu.e10.com

  <Directory /var/www/abimanyu.e10/index.php/home>
          Options +Indexes
  </Directory>

  Alias "/home" "/var/www/abimanyu.e10/index.php/home"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```ruby
lynx abimanyu.e10.com/home
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/12.png?raw=true)

## Soal Nomor 13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

### Jawaban Nomor 13
Disini kita hanya memerlukan setup ServerName dan ServerAlias.
- Masukkan script `no13.sh` ke dalam abimanyu
```ruby
wget -O '/var/www/parikesit.abimanyu.e10.com' 'https://drive.usercontent.google.com/download?id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS'
unzip -o /var/www/parikesit.abimanyu.e10.com -d /var/www/
mv /var/www/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.e10
rm /var/www/parikesit.abimanyu.e10.com
rm -rf /var/www/parikesit.abimanyu.yyy.com
mkdir /var/www/parikesit.abimanyu.e10/secret

echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e10
  ServerName parikesit.abimanyu.e10.com
  ServerAlias www.parikesit.abimanyu.e10.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e10.com.conf

a2ensite parikesit.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```
lynx parikesit.abimanyu.e10.com
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/13.png?raw=true)

## Soal Nomor 14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

### Jawaban Nomor 14
Karena kita mau mengizinkan public agar dapat melakukan directory listing kita menggunakan Options +Indexes. Sedangkan agar suatu folder tidak dapat di akses, kita dapat menggunakan Option -Indexes.
- Masukkan script `no14.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e10
  ServerName parikesit.abimanyu.e10.com
  ServerAlias www.parikesit.abimanyu.e10.com

  <Directory /var/www/parikesit.abimanyu.e10/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e10/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.e10/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.e10/secret"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```
lynx parikesit.abimanyu.e10.com/public
lynx parikesit.abimanyu.e10.com/secret
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/14.1.png?raw=true)
![2](https://github.com/mavaldi/image-jarkomasdasd/blob/main/14.2.png?raw=true)

## Soal Nomor 15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

### Jawaban Nomor 15
Untuk page html error. Kita mendapatkan dari file resources yang telah diberikan, untuk detailnya ada pada folder parikesit.abimanyu.e10.com/public/error/. Disana terdapat 2 file yaitu 403.html dan 404.html. Kita juga menggunakan ErrorDocument yang berfungsi melakukan redirect terhadap file yang diinginkan ketika mendapatkan masalah ketika mengakses domain yang telah ada sebelumnya.
- Masukkan script `no15.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e10
  ServerName parikesit.abimanyu.e10.com
  ServerAlias www.parikesit.abimanyu.e10.com

  <Directory /var/www/parikesit.abimanyu.e10/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e10/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.e10/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.e10/secret"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```
lynx parikesit.abimanyu.e10.com/publicc
lynx parikesit.abimanyu.e10.com/secret
```
![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/15.1.png?raw=true)
![2](https://github.com/mavaldi/image-jarkomasdasd/blob/main/15.2.png?raw=true)

## Soal Nomor 16
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 

### Jawaban Nomor 16
Disini kita hanya perlu menggunakan Alias "/js" "/var/www/parikesit.abimanyu.e10/public/js" untuk mengubah virtual host agar file tersebut menjadi lebih singkat. Disini kami juga menggunakan ServerName dan ServerAlias agar virtual host dapat berjalan.
- Masukkan script `no16.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e10
  ServerName parikesit.abimanyu.e10.com
  ServerAlias www.parikesit.abimanyu.e10.com

  <Directory /var/www/parikesit.abimanyu.e10/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e10/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.e10/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.e10/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.e10/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `Nakula`
```
lynx parikesit.abimanyu.e10.com/js
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/16.png?raw=true)

## Soal Nomor 17
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

### Jawaban Nomor 17
Untuk melakukan kustomisasi pada port tertentu. Kita hanya perlu mengubah file ports.conf dengan menambahkan Listen 14000 dan Listen 14400. Kita juga perlu mengubah <VirtualHost *:14000 *:14400>
- Masukkan script `no17.sh` ke dalam abimanyu
```ruby
wget -O '/var/www/rjp.baratayuda.abimanyu.e10.com' 'https://drive.usercontent.google.com/download?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6'
unzip -o /var/www/rjp.baratayuda.abimanyu.e10.com -d /var/www/
mv /var/www/rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.e10
rm /var/www/rjp.baratayuda.abimanyu.e10.com
rm -rf /var/www/rjp.baratayuda.abimanyu.yyy.com

echo -e '<VirtualHost *:14000>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.e10
  ServerName rjp.baratayuda.abimanyu.e10.com
  ServerAlias www.rjp.baratayuda.abimanyu.e10.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.e10.com.conf

echo -e '<VirtualHost *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.e10
  ServerName rjp.baratayuda.abimanyu.e10.com
  ServerAlias www.rjp.baratayuda.abimanyu.e10.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.e10.com.conf

a2ensite rjp.baratayuda.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `nakula`
```
lynx rjp.baratayuda.abimanyu.e10.com:14000
lynx rjp.baratayuda.abimanyu.e10.com:14400
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/Screenshot%202023-10-17%20195208.png?raw=true)

## Soal Nomor 18
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

### Jawaban Nomor 18
Untuk melakukan autentikasi pada sebuah server, diperlukan AuthType dan Require Valid-User. Lalu untuk AuthUserFile sendiri adalah tempat yang ingin kita gunakan untuk melakukan write. Sedangkan untuk AuthName adalah content-type Autentikasi pada apache2
- Masukkan script `no18.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.e10
  ServerName rjp.baratayuda.abimanyu.e10.com
  ServerAlias www.rjp.baratayuda.abimanyu.e10.com

  <Directory /var/www/rjp.baratayuda.abimanyu.e10>
          AuthType Basic
          AuthName "Restricted Content"
          AuthUserFile /etc/apache2/.htpasswd
          Require valid-user
  </Directory>

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.e10.com.conf

htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudae10

a2ensite rjp.baratayuda.abimanyu.e10.com.conf

service apache2 restart
```
- jalankan pada client `nakula`
```
lynx rjp.baratayuda.abimanyu.e10.com:14000
lynx rjp.baratayuda.abimanyu.e10.com:14400
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/Screenshot%202023-10-17%20195257.png?raw=true)
![2](https://github.com/mavaldi/image-jarkomasdasd/blob/main/Screenshot%202023-10-17%20195304.png?raw=true)

## Soal Nomor 19
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

### jawaban Nomor 19
Agar ketika kita mengakses IP dari abimanyu dapat otomatis dialihkan ke www.abimanyu.e10.com. Kita perlu menggunakan file Redirect yang akan mengarahkan kepada file yang kita inginkan. Disini saya memasukkan ke dalam file konfigurasi 000-default.conf karena merupakan default dari suatu service apache.
- Masukkan script `no19.sh` ke dalam abimanyu
```ruby
echo -e '<VirtualHost *:80>
    ServerAdmin webmaster@abimanyu.e10.com
    DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Redirect / http://www.abimanyu.e10.com/
</VirtualHost>' > /etc/apache2/sites-available/000-default.conf

apache2ctl configtest

service apache2 restart
```
- jalankan pada client `nakula`
```
lynx 192.211.3.3
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/19.png?raw=true)

## Soal Nomor 20
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

### Jawaban Nomor 20
- Abimanyu Jangan lupa untuk menjalankan perintah berikut agar dapat melakukan rewrite modul
```ruby
a2enmod rewrite
```
- Lalu jalankan perintah tersebut untuk melakukan rewrite terhdap directory parikesit.abimanyu.e10
```ruby
echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.e10.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.e10/.htaccess
```

- Ubah konfigurasi dan gunakan AllowOverride All untuk mengkonfigurasi nya dengan .htaccess sebelumnya.
```ruby
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e10

  ServerName parikesit.abimanyu.e10.com
  ServerAlias www.parikesit.abimanyu.e10.com

  <Directory /var/www/parikesit.abimanyu.e10/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e10/secret>
          Options -Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e10>
          Options +FollowSymLinks -Multiviews
          AllowOverride All
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.e10/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.e10/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.e10/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e10.com.conf

service apache2 restart
```
- Jalankan pada client `nakula`
```
lynx parikesit.abimanyu.e10.com/public/images/not-abimanyu.png
lynx parikesit.abimanyu.e10.com/public/images/abimanyu-student.jpg
lynx parikesit.abimanyu.e10.com/public/images/abimanyu.png
lynx parikesit.abimanyu.e10.com/public/images/notabimanyujustmuseum.177013
```

![1](https://github.com/mavaldi/image-jarkomasdasd/blob/main/20.png?raw=true)
