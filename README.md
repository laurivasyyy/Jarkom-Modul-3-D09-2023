# Daftar Nama Kelompok
| Nama | NRP |
|---------------------------|------------|
| Dafarel Fatih Wirayudha | 5025211120 | 
| Laurivasya Gadhing Syahafidh | 5025211136 | 

# Daftar Isi
- [Pre-requisites](#pre-requisites)
  - [1. Membuat Topologi Baru](#topologi)
  - [2. Network Configuration](#config)
  - [3. Instalasi dan Konfigurasi](#install)
    - [DHCP Relay](#dhcp)
    - [DHCP Server](#dhcp-server)
    - [DNS Server](#dns)
    - [Database Server](#database)
    - [Load Balancer](#load-balancer)
    - [Worker PHP](#worker-php)
    - [Worker Laravel](#worker-laravel)
    - [Client](#client)
- [Nomor 0](#0)
- [Nomor 1](#1)
- [Nomor 2-5](#2-5)
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
- #### Melakukan Konfigurasi pada `/etc/dhcp/dhcpd.conf`
    ```
    subnet 'NID' netmask 'Netmask' {
        range 'IP_Awal' 'IP_Akhir';
        range 'IP_Awal' 'IP_Akhir';
        option routers 'iP_Gateway';
        option broadcast-address 'IP_Broadcast';
        option domain-name-servers 'IP-DNS-SERVER';
        default-lease-time 'Waktu';
        max-lease-time 'Waktu';
    }
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
Kali ini, kalian diminta untuk melakukan register domain berupa `riegel.canyon.d09.com` untuk worker Laravel dan `granz.channel.d09.com` untuk worker PHP (0) mengarah pada worker yang memiliki IP [10.26].x.1.

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


