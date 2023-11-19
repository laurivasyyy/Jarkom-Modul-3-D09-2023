# Daftar Nama Kelompok
| Nama | NRP |
|---------------------------|------------|
| Dafarel Fatih Wirayudha | 5025211120 | 
| Laurivasya Gadhing Syahafidh | 5025211136 | 

# Daftar Isi
- [Pre-requisites](#pre-requisites)
  - [1. Membuat Topologi Baru](#topologi)
  - [2. Network Configuration](#configuration)
  - [3. Instalasi dan Konfigurasi](#install)
    - [DHCP Relay](#dhcp)
    - [DHCP Server](#dhcp-server)
    - [DNS Server](#dns)
    - [Database Server](#database)
    - [Load Balancer](#load-balancer)
    - [Worker PHP](#worker-php)
    - [Worker Laravel](#worker-laravel)
    - [Client](#client)
- [Nomor 0](#nomor-0)
- [Nomor 2-5](#nomor2-5)
- [Nomor 6](#6)
- [Nomor 7](#7)
- [Nomor 8](#8)
- [Nomor 9](#9)
- [Nomor 10](#10)
- [Nomor 11](#11)
- [Nomor 12](#12)
- [Nomor 13](#13)
- [Nomor 14](#14)
- [Nomor 15](#15)
- [Nomor 16](#16)
- [Nomor 17](#17)
- [Nomor 18](#18)
- [Nomor 19](#19)
- [Nomor 20](#20)

# Pre-requisites

## 1. Membuat Topologi Baru
Buat topologi baru sesuai dengan peta topologi jaringan dibawah ini.

> notes : jangan lupa untuk melakukan add new image docker untuk menambahkan debian di GNS3 

[Cara Memasukkan Image Baru ke GNS3](https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/master/Modul-GNS3#memasukkan-image-ubuntu-ke-gns3)

``` js
danielcristh0/debian-buster:1.1
```

![topologi](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/topologi.png)

## 2. Network Configuration
#### **Aura (DHCP Relay)**
```
auto eth0
iface eth0 inet dhcp
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.26.0.0/16

auto eth1
iface eth1 inet static
	address 10.26.1.200
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.26.2.200
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.26.3.200
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.26.4.200
	netmask 255.255.255.0
```

#### **Himmel (DHCP Server)**
```
auto eth0
iface eth0 inet static
	address 10.26.1.1
	netmask 255.255.255.0
	gateway 10.26.1.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Heiter (DNS Server)**
```
auto eth0
iface eth0 inet static
	address 10.26.1.2
	netmask 255.255.255.0
	gateway 10.26.1.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Denken (Database Server)**
```
auto eth0
iface eth0 inet static
	address 10.26.2.1
	netmask 255.255.255.0
	gateway 10.26.2.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Eisen (Load Balancer)**
```
auto eth0
iface eth0 inet static
	address 10.26.2.2
	netmask 255.255.255.0
	gateway 10.26.2.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Lugner(PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.3.1
	netmask 10.26.255.0
	gateway 10.26.3.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Linie (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.3.2
	netmask 10.26.255.0
	gateway 10.26.3.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Lawine (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.3.3
	netmask 10.26.255.0
	gateway 10.26.3.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Frieren (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.4.1
	netmask 10.26.255.0
	gateway 10.26.4.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Flamme (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.4.2
	netmask 10.26.255.0
	gateway 10.26.4.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Fern (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.4.3
	netmask 10.26.255.0
	gateway 10.26.4.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **Revolte, Ritcher, Sein, dan Stark (Client)**
```
auto eth0
iface eth0 inet dhcp
```
## 3. Instalasi dan Konfigurasi
### **DHCP Relay**
- #### Melakukan Instalasi 
    Lakukan beberapa instalasi sebelum melakukan konfigurasi pada Aura yang dijadikan sebagai DHCP Relay. Instalasi yang dilakukan adalah sebagai berikut.

    ``` js
    apt-get update
    apt-get install isc-dhcp-relay -y
    service isc-dhcp-relay start
    ```
- #### Melakukan Konfigurasi pada `isc-dhcp-relay`
    Pada `/etc/default/isc-dhcp-relay` lakukan konfigurasi berikut.
    ``` js
    SERVERS="10.26.1.1"  
    INTERFACES="eth1 eth2 eth3 eth4"
    OPTIONS=
    ```
    > notes : 
    > - Isi dari `INTERFACES=` harus menyesuaikan jumlah interface output yang terhubung dengan client. Pada kasus ini, terdapat 4 interface output yang terhubung dengan client, yaitu `eth1 eth2 eth3 eth4`
    > - `SERVERS=` berisi IP Address dari DHCP Server yang terhubung

- #### Melakukan Konfigurasi IP Forwarding
    Pada `/etc/sysctl.conf` uncomment bagian berikut untuk meneruskan paket dari suatu jaringan ke jaringan lainnya.
    ``` js
    net.ipv4.ip_forward=1
    ```
    ``` js
    service isc-dhcp-relay restart
    ```

### **DHCP Server**
- #### Melakukan Instalasi 
    Pada topologi ini, kita akan menjadikan Himmel sebagai DHCP Server. Oleh sebab itu, kita harus meng-install isc-dhcp-server di Himmel.
    ``` js
    apt-get update
    apt install isc-dhcp-server -y
    ```
- #### Melakukan Konfigurasi pada `/etc/default/isc-dhcp-server`
    Dari topologi yang sudah dibuat interface dari Heiter yang menuju ke switch adalah eth0, maka kita akan memilih interface eth0 untuk diberikan layanan DHCP.

    ```
    echo 'INTERFACESv4="eth0"' > /etc/default/isc-dhcp-server
    ```

- #### Restart Service isc-dhcp-server
    ``` js
    service isc-dhcp-server restart
    ```
### **DNS Server**
- #### Melakukan Instalasi 
    ``` js
    apt-get update
    apt install bind9 -y
    ```
- #### Melakukan Konfigurasi pada `/etc/bind/named.conf.options`
    ```
    echo 'options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;
        listen-on-v6 { any; };
    }; ' >/etc/bind/named.conf.options
    ```

### **Database Server**
``` 
apt-get update
apt-get install mariadb-server -y
service mysql start
```

### **Load Balancer**
```
apt-get update
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y

service nginx start
```

### **Worker PHP**
```
apt-get update
apt-get install nginx -y
apt-get install lynx -y
apt-get install wget -y
apt-get install htop -y
apt-get install unzip -y
apt-get install apache2-utils -y
apt-get install php7.3-fpm php7.3-common php7.3-mysql php7.3-gmp php7.3-curl php7.3-intl php7.3-mbstring php7.3-xmlrpc php7.3-gd php7.3-xml php7.3-cli php7.3-zip -y

service nginx start
service php7.3-fpm start
```
### **Worker Laravel**
```
apt-get update
apt-get install nginx -y
apt-get install lynx -y
apt-get install mariadb-client -y
apt-get install -y lsb-release ca-certificates a   apt-transport-https software-properties-common gnupg2
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
apt-get update
apt-get install php8.0-mbstring php8.0-xml php8.0-cli   php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y

service nginx start
service php8.0-fpm start
```
### **Client**
```
apt update
apt-get install jq -y
apt install lynx -y
apt install htop -y
apt install apache2-utils -y
```

# Nomor 0
Kali ini, kalian diminta untuk melakukan register domain berupa `riegel.canyon.d09.com` untuk worker Laravel dan `granz.channel.d09.com` untuk worker PHP (0) mengarah pada worker yang memiliki IP [IP Prefix].x.1.

#### **a. Frieren (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.4.1
	netmask 10.26.255.0
	gateway 10.26.4.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

#### **b. Lugner(PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.26.3.1
	netmask 10.26.255.0
	gateway 10.26.3.200
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
> notes : lakukan konfigurasi berikut di node Heiter sebagai DNS Server

- 1. #### Melakukan konfigurasi pada `/etc/bind/named.conf.local`
        ```
        echo 'zone "riegel.canyon.d09.com" {
            type master;
            file "/etc/bind/jarkom/riegel.canyon.d09.com";
        };

        zone "granz.channel.d09.com" {
            type master;
            file "/etc/bind/jarkom/granz.channel.d09.com";
        };' > /etc/bind/named.conf.local
        ```
- 2. #### Membuat Direktori
        ``` 
        mkdir /etc/bind/jarkom
        cp /etc/bind/db.local /etc/bind/jarkom/riegel.canyon.d09.com
        cp /etc/bind/db.local /etc/bind/jarkom/granz.channel.d09.com
        ```
- 3. #### Melakukan konfigurasi pada `/etc/bind/jarkom/riegel.canyon.d09.com`
        ```
        echo ';
        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     riegel.canyon.d09.com. root.riegel.canyon.d09.com. (
                                2023111401      ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;
        @       IN      NS      riegel.canyon.d09.com.
        @       IN      A       10.26.3.1     ; IP Lugner
        www     IN      CNAME   riegel.canyon.d09.com.' > /etc/bind/jarkom/riegel.canyon.d09.com
        ```

- 4. #### Melakukan konfigurasi pada `/etc/bind/jarkom/granz.channel.d09.com`
        ```
        echo '
        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     granz.channel.d09.com. root.granz.channel.d09.com. (
                                2023111401      ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;
        @       IN      NS      granz.channel.d09.com.
        @       IN      A       10.26.4.1    ; IP Fern
        www     IN      CNAME   granz.channel.d09.com.' > /etc/bind/jarkom/granz.channel.d09.com
        ```

### Hasil Testing pada Client Ritcher :
![no-1](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%201.png)

# Nomor 2-5
- Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 **(2)**
- Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168 **(3)**
- Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut **(4)**
- Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit **(5)**

| **No** | **Parameter Jaringan**                             | **Keterangan**                                                                                                                                                                                                                                                                                         |
| ------ | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1      | `subnet 'NID'`                                     | karena interface yang dipilih adalah `eth0` dengan IP 10.26.x.x, maka NID subnetnya adalah `10.26.1.0`                                                                                                                                                                                                                                                                                 |
| 2      | `netmask 'Netmask`                                 | Netmask pada subnet dapat dilihat pada konfigurasi network router `255.255.255.0`                                                                                                                                                                                                                                                                                   |
| 3      | `range 'IP_Awal' 'IP_Akhir'`                       | **Sesuaikan dengan soal**                                                                                                                                                                                                                              |
| 4      | `option routers 'Gateway'`                         | IP gateway dari router menuju client sesuai konfigurasi subnet **(cek config aura)**                                                                                                                                                                                                                               |
| 5      | `option broadcast-address 'IP_Broadcast'`          | IP broadcast pada subnet `10.26.x.255`                                                                                                                                                                                                                                                                               |
| 6      | `option domain-name-servers 'DNS_yang_diinginkan'` | IP DNS Server                                                                                                                                                                                                                                         |
| 7      | `default-lease-time 'Waktu'`                       | Lama waktu DHCP server meminjamkan `IP Address` kepada client, dalam satuan detik. **(sesuaikan dengan soal)**                                                                                                                                                                                                     |
| 8      | `max-lease-time 'Waktu'`                           | Waktu maksimal yang di alokasikan untuk peminjaman IP oleh DHCP server ke client dalam satuan detik.  **(sesuaikan dengan soal)**                                                                                                                                                                                |

- #### Sesuaikan Konfigurasi pada `/etc/dhcp/dhcpd.conf` dengan soal, gunakan tabel diatas untuk mempermudah anda dalam memahami setiap parameter jaringan yang ada.

```
echo 'subnet 10.26.1.0 netmask 255.255.255.0 {
}

subnet 10.26.2.0 netmask 255.255.255.0 {
}

subnet 10.26.3.0 netmask 255.255.255.0 {
    range 10.26.3.16 10.26.3.32;
    range 10.26.3.64 10.26.3.80;
    option routers 10.26.3.0;
    option broadcast-address 10.26.3.255;
    option domain-name-servers 10.26.1.3;
    default-lease-time 180;
    max-lease-time 5760;
}

subnet 10.26.4.0 netmask 255.255.255.0 {
    range 10.26.4.12 10.26.4.20;
    range 10.26.4.160 10.26.4.168;
    option routers 10.26.4.0;
    option broadcast-address 10.26.4.255;
    option domain-name-servers 10.26.1.2;
    default-lease-time 720;
    max-lease-time 5760;
}' > /etc/dhcp/dhcpd.conf
```
```
service isc-dhcp-server start
```

### Hasil Testing pada Client Ritcher dan Sein:
![ritcher](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%204%20-%20ritcher.png)
![sein](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%204%20-%20sein.png)

# Nomor 6
Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website [berikut](https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1) dengan menggunakan php 7.3. (6)

- 1. #### Melakukan download dan unzip file di semua worker
        ```
        wget -O '/var/www/granz.channel.d09.com' 'https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1'
        unzip -o /var/www/granz.channel.d09.com -d /var/www/
        rm /var/www/granz.channel.d09.com
        mv /var/www/modul-3 /var/www/granz.channel.d09.com
        ```
- 2. #### Membuat symlink untuk `granz.channel.d09.com`
        ```
        cp /etc/nginx/sites-available/default /etc/nginx/sites-available/granz.channel.d09.com
        ln -s /etc/nginx/sites-available/granz.channel.d09.com /etc/nginx/sites-enabled/
        rm /etc/nginx/sites-enabled/default
        ```
- 3. #### Melakukan konfigurasi pada `/etc/nginx/sites-available/granz.channel.d09.com`
        ```
        echo ' server {

            listen 80;

            root /var/www/granz.channel.d09.com;

            index index.php index.html index.htm;
            server_name _;

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
        }' > /etc/nginx/sites-available/granz.channel.d09.com
        ```
- 4. #### Melakukan testing pada semua worker
        ```
        lynx localhost
        ```

Hasil testing pada worker Fern dan Lawine :

![fern](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%206%20-%20fern.png) 
![lawine](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%206%20-%20lawine.png)

# Nomor 7
Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
- a. Lawine, 4GB, 2vCPU, dan 80 GB SSD.
- b. Linie, 2GB, 2vCPU, dan 50 GB SSD.
- c. Lugner 1GB, 1vCPU, dan 25 GB SSD.

aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. (7)

- 1. #### Edit konfigurasi pada `/etc/bind/jarkom/riegel.canyon.d09.com` dan `/etc/bind/jarkom/granz.channel.d09.com` yang ada pada Heiter (DNS Server) agar mengarahkan domain ke IP Eisen (Load Balancer).
        ```
        echo ';
        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     riegel.canyon.d09.com. root.riegel.canyon.d09.com. (
                                2023111401      ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;
        @       IN      NS      riegel.canyon.d09.com.
        @       IN      A       10.26.2.2    ; IP Load Balancer
        www     IN      CNAME   riegel.canyon.d09.com.' > /etc/bind/jarkom/riegel.canyon.d09.com
        ```
        ```
        echo '
        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     granz.channel.d09.com. root.granz.channel.d09.com. (
                                2023111401      ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;
        @       IN      NS      granz.channel.d09.com.
        @       IN      A       10.26.2.2   ; IP Load Balancer
        www     IN      CNAME   granz.channel.d09.com.' > /etc/bind/jarkom/granz.channel.d09.com
        ```
- 2. #### Melakukan konfigurasi pada node Eisen (Load Balancer).
        ``` 
        cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb

        echo ' upstream worker {
            server 10.26.3.1; # IP Lugner
            server 10.26.3.2; # Ip Linie
            server 10.26.3.3; # IP Lawine
        }

        server {
            listen 80;
            server_name granz.channel.d09.com www.granz.channel.d09.com;

            location / {
            proxy_pass http://worker;
            }
        } ' > /etc/nginx/sites-available/lb

        ln -s /etc/nginx/sites-available/lb /etc/nginx/sites-enabled/
        rm /etc/nginx/sites-enabled/default

        service nginx restart
        ```
- 3. #### Melakukan testing dengan menjalankan perintah berikut di salah satu client.
        ```
        ab -n 1000 -c 100 http://www.granz.channel.d09.com/
        ```

Hasil testing pada node Revolte (client) :
> Request per second : 650.54 [#/sec] (mean)

![no7](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no%207%20-%20revolte.png)

# Nomor 8
Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
a. Nama Algoritma Load Balancer
b. Report hasil testing pada Apache Benchmark
c. Grafik request per second untuk masing masing algoritma. 
d. Analisis

#### Melakukan testing dengan menjalankan perintah berikut di salah satu client.
```
ab -n 200 -c 10 http://www.granz.channel.d09.com/ 
```
- ### Round Robin: Default algoritma untuk load balancer adalah round robin, sehingga tidak perlu menambahkan apa-apa di upstream.
  Hasil:
- ### Least Conn:
  ```
  upstream php {
  	least_conn;
 	server 10.26.3.1; #IP Lugner
 	server 10.26.3.2; #IP Linie
    	server 10.26.3.3; #IP Lawine
	}
  ```
  Hasil:
- ### IP Hash:
  ```
  upstream php {
  	ip_hash;
 	server 10.26.3.1; #IP Lugner
 	server 10.26.3.2; #IP Linie
    	server 10.26.3.3; #IP Lawine
	}
  ```
  Hasil:
- ### Generic Hash:
  ```
  upstream php {
  	hash $request_uri consistent;
 	server 10.26.3.1; #IP Lugner
 	server 10.26.3.2; #IP Linie
    	server 10.26.3.3; #IP Lawine
	}
  ```
  Hasil:
# Nomor 9
Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. 

#### Melakukan testing dengan menjalankan perintah berikut di salah satu client.
```
ab -n 100 -c 10 http://www.granz.channel.d09.com/ 
```
- 1. #### Untuk 3 worker cukup jalankan Load Balancer dan semua worker seperti biasa.
  Hasil:
![no_9_3worker](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_9_3_worker(RPS=2155.92).png)
Didapatkan Request per Second = 2155.92
- 2. #### Untuk 2 worker matikan salah 1 worker dengan ```service nginx stop```.
  Hasil:
![no_9_2worker](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_9_2_worker(LUGNER,LAWINE)_(RPS=1901.21).png)
Didapatkan Request per Second = 1901.21
- 3. #### Untuk 1 worker matikan 2 worker dengan ```service nginx stop```.
  Hasil:
![no_9_1worker](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_9_1_worker(LUGNER)_(RPS=2107.88).png)
Didapatkan Request per Second = 2107.88
# Nomor 10
Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/

- 1. ### Tambahkan command berikut di location pada conf load balancer
  ```
  auth_basic "Administrators Area";
  auth_basic_user_file /etc/nginx/rahasiakita;
  ```
- 2. ### Lakukan command berikut di Load Balancer:
  ```
  mkdir /etc/nginx/rahasisakita
  htpasswd -c -b /etc/nginx/rahasiakita netics ajkd09
  ```
Hasil:

# Nomor 11
Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id.

- 1. ### Tambahkan location baru dengan path /its
  ``` 
  location /its {
        proxy_pass https://www.its.ac.id;
        auth_basic "Administrators Area";
        auth_basic_user_file /etc/nginx/rahasiakita;
  }
  ```
- 2. ### Testing dengan penambahan /its pada url
  ```
  lynx www.granz.channel.d09.com/its
  ```
  Hasil:

# Nomor 12
Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168.

- 1. ### Tambahkan syntax berikut kedalam location di load balancer
  ```
  location / {
    allow 10.26.3.69;
    allow 10.26.3.70;
    allow 10.26.4.167;
    allow 10.26.4.168;
    deny all;
  }
  ```
  Hasil:

# Nomor 13
Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

- 1. ### Tambahkan conf berikut kedalam mysql.conf di denken
  ```
  [client-server]

  !includedir /etc/mysql/conf.d/
  !includedir /etc/mysql/mariadb.conf.d/

  [mysqld]
  skip-networking=0
  skip-bind-address
  ```

- 2. ### Buat script.sql dan tambahkan syntax sql berikut
  ```
  CREATE USER '\''kelompokd09'\''@'\''%'\'' IDENTIFIED BY '\''passwordd09'\''; 
  CREATE USER '\''kelompokd09'\''@'\''localhost'\'' IDENTIFIED BY '\''passwordd09'\''; 
  CREATE DATABASE IF NOT EXISTS dbkelompokd09; 
  GRANT ALL PRIVILEGES ON *.* TO '\''kelompokd09'\''@'\''%'\'';  
  GRANT ALL PRIVILEGES ON *.* TO '\''kelompokd09'\''@'\''localhost'\''; 
  FLUSH PRIVILEGES;
  ```

- 3. ### Jalankan command berikut untuk execute script.sql
  ```
  mysql < script.sql
  ```

# Nomor 14
Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer.

- 1. ### Install package yang dibutuhkan di setiap laravel worker
  ```
  apt-get update
  apt-get install mariadb-client -y
  apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

  curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
  sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

  apt-get update
  apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y

  apt-get install nginx -y

  wget https://getcomposer.org/download/2.0.13/composer.phar
  chmod +x composer.phar
  mv composer.phar /usr/bin/composer

  apt-get install git -y
  ```

- 2. ### Masuk ke folder /var/www dan lakukan git clone dari repository yang diberikan di soal lalu masuk ke folder repository yang sudah di clone
  ```
  cd var/www
  git clone https://github.com/martuafernando/laravel-praktikum-jarkom
  cd laravel-praktikum-jarkom
  ```

- 3. ### Jalankan command berikut
  ```
  composer install
  composer update
  cp .env.example .env
  ```

- 4. ### Tambahkan conf berikut ke .env untuk set up database
  ```
        APP_NAME=Laravel
	APP_ENV=local
	APP_KEY=
	APP_DEBUG=true
	APP_URL=http://localhost
	
	LOG_CHANNEL=stack
	LOG_DEPRECATIONS_CHANNEL=null
	LOG_LEVEL=debug
	
	DB_CONNECTION=mysql
	DB_HOST=10.26.2.1
	DB_PORT=3306
	DB_DATABASE=dbkelompokd09
	DB_USERNAME=kelompokd09
	DB_PASSWORD=passwordd09
	
	BROADCAST_DRIVER=log
	CACHE_DRIVER=file
	FILESYSTEM_DISK=local
	QUEUE_CONNECTION=sync
	SESSION_DRIVER=file
	SESSION_LIFETIME=120
	
	MEMCACHED_HOST=127.0.0.1
	
	REDIS_HOST=127.0.0.1
	REDIS_PASSWORD=null
	REDIS_PORT=6379
	
	MAIL_MAILER=smtp
	MAIL_HOST=mailpit
	MAIL_PORT=1025
	MAIL_USERNAME=null
	MAIL_PASSWORD=null
	MAIL_ENCRYPTION=null
	MAIL_FROM_ADDRESS="hello@example.com"
	MAIL_FROM_NAME="${APP_NAME}"
	
	AWS_ACCESS_KEY_ID=
	AWS_SECRET_ACCESS_KEY=
	AWS_DEFAULT_REGION=us-east-1
	AWS_BUCKET=
	AWS_USE_PATH_STYLE_ENDPOINT=false
	
	PUSHER_APP_ID=
	PUSHER_APP_KEY=
	PUSHER_APP_SECRET=
	PUSHER_HOST=
	PUSHER_PORT=443
	PUSHER_SCHEME=https
	PUSHER_APP_CLUSTER=mt1
	
	VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
	VITE_PUSHER_HOST="${PUSHER_HOST}"
	VITE_PUSHER_PORT="${PUSHER_PORT}"
	VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
	VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
  ```

- 5. ### Jalankan command berikut
  ```
  php artisan migrate:fresh
  php artisan db:seed --class=AiringsTableSeeder

  php artisan key:generate
  php artisan jwt:secret
  ```
- 6. ### Tambahkan conf nginx berikut
  ```
  server {
    listen 80;
    root /var/www/laravel-praktikum-jarkom/public;
    index index.php index.html index.htm;
    server_name _;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }
    location ~ /\.ht {
        deny all;   
    }
    error_log /var/log/nginx/granz_error.log;
    access_log /var/log/nginx/granz_access.log;
  }
  ```
- 7. ### Lalu lakukan testing salah satu worker di client dengan lynx
  ```
  lynx http://10.26.4.1
  ```
  Hasil:

# Nomor 15
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.
POST /auth/register:

- 1. ### Buat file json yang menyimpan data kolom yang diperlukan database
  ```
  {
    "username": "jono",
    "password": "Jokopass"
  }
  ```
- 2. ### Lalu lakukan curl salah satu worker pada client untuk mendapatkan response
  ```
  curl -H "Content-Type:application/json" -X POST -d @register.json http://10.26.4.1:80/api/auth/register
  ```
- 3. ### Testing apache benchmark dengan syntax berikut
  ```
  ab -n 100 -c 10 -p register.json -T application/json http://10.26.4.1/api/auth/register
  ```
  Hasil Response:
  ![no_15_response](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_15_A_response.png)
  Hasil Testing:
  ![no_15_testing](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_15_A_testing.png)

# Nomor 16
POST /auth/login:

- 1. ### Gunakan file json untuk register untuk melakukan login menggunakan curl untuk mendapatkan response
  ```
  curl -H "Content-Type:application/json" -X POST -d @register.json http://10.26.4.1:80/api/auth/login
  ```
- 2. ### Testing apache benchmark dengan syntax berikut
  ```
  ab -n 100 -c 10 -p register.json -T application/json http://10.26.4.1:80/api/auth/login
  ```
  Hasil Response:
  ![no_16_response](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_16_RESPONSE.png)
  Hasil Testing:
  ![no_16_testing](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_16_TESTING.png)

# Nomor 17
GET /me:

- 1. ### Lakukan curl dan simpan response di dalam file txt
  ```
  curl -H "Content-Type:application/json" -X POST -d @register.json http://10.26.4.1:80/api/auth/login > login_output.txt
  ```
- 2. ### Simpan token dari response ke dalam variable
  ```
  token=$(cat login_output.txt | jq -r '.token')
  ```
- 3. ### Lakukan curl dengan syntax berikut untuk mendapatkan response
  ```
  curl -X GET -H "Content-Type:application/json" -H "Authorization:Bearer $token" http://10.26.4.1:80/api/me
  ```
  Hasil Response:
  ![no_17_response](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_17_response.png)
  Hasil Testing:
  ![no_17_testing](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_17_testing.png)
  
# Nomor 18
Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern.

- 1. ### Tambahkan conf upstream di load balancer untuk larvel worker
  ```
  upstream laravel {
 	server 10.26.4.1; #IP Friere
 	server 10.26.4.2; #IP Flame
    server 10.26.4.3; #IP Frern
  }
  ```
- 2. ### Tambahan proxy_bind dengan ip load balancer di location yang mengarah ke upstream laravel
  ```
  location / {
  	proxy_bind 10.26.2.2;
        proxy_pass http://laravel;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
  }
  ```
- 3. ### Lakukan testing apache benchmark. Berikut contoh untuk testing login
  ```
  ab -n 100 -c 10 -p register.json -T application/json http://www.riegel.canyon.d09.com/api/auth/login
  ```
  Hasil:
  
# Nomor 19
Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.

- 1. ### Untuk mengatur conf php fpm bisa menggunakan syntax berikut untuk merubah config di dalam /etc/php/8.0/fpm/pool.d/www.conf
  ```
        echo '[www]
	user = www-data
	group = www-data
	listen = /run/php/php8.0-fpm.sock
	listen.owner = www-data
	listen.group = www-data
	php_admin_value[disable_functions] = exec,passthru,shell_exec,system
	php_admin_flag[allow_url_fopen] = off
	
	; Choose how the process manager will control the number of child processes.
	
	pm = dynamic
	pm.max_children = 5
	pm.start_servers = 2
	pm.min_spare_servers = 1
	pm.max_spare_servers = 3' > /etc/php/8.0/fpm/pool.d/www.conf
  ```
- 2. ### Lalu restart php fpm
  ```
  service php8.0-fpm restart
  ```
- 3. ### Lakukan testing apache benchmark. Berikut contoh untuk testing login
  ```
  ab -n 100 -c 10 -p register.json -T application/json http://www.riegel.canyon.d09.com/api/auth/login
  ```
  
Berikut adalah hasil yang didapatkan dari 3 script pengaturan:
Script default
```
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
```
Hasil: ![no_19_scriptD](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_19_script1.png)
Script 1
```
pm = dynamic
pm.max_children = 15
pm.start_servers = 5
pm.min_spare_servers = 3
pm.max_spare_servers = 10
```
Hasil:  ![no_19_script2](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_19_script2.png)
Script 2
```
pm = dynamic
pm.max_children = 30
pm.start_servers = 7
pm.min_spare_servers = 5
pm.max_spare_servers = 15
```
Hasil: ![no_19_script3](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_19_script3.png)
Script 3     
```
pm = dynamic
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
```
Hasil: ![no_19_script4](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_19_script4.png)

# Nomor 20
Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

- 1. ### Tambahkan ```least_conn``` di upstream laravel pada conf load balancer untuk membuat upstream tersebut menggunakan algoritma least connection
  ```
  upstream laravel {
    least_conn;
    server 10.26.4.1; #IP Friere
    server 10.26.4.2; #IP Flame
    server 10.26.4.3; #IP Frern
  }
  ```

- 2. ### Testing pada apache benchmark
  ```
  ab -n 100 -c 10 -p register.json -T application/json http://www.riegel.canyon.d09.com/api/auth/login
  ```
Hasil dengan script 3: 
Hasil: ![no_20](https://github.com/laurivasyyy/Jarkom-Modul-3-D09-2023/blob/main/src/no_20_dengan_script4.png)
