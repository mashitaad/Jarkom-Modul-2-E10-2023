# Jarkom-Modul-2-E10-2023

## Identitas Kelompok E10 (Jaringan Komputer E)
| Name                 | NRP        |
| ---                  | ---        |
| Mavaldi Rizqy Hazdi  | 5025211086 |
| Mashita Dewi         | 5025211036 |

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
