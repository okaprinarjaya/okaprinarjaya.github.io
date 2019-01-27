---
layout: post
title: Membangun Load Balancer Failover yang Otomatis Untuk Web Server di GNU/Linux CentOS 7
---

## Pengantar

Untuk menuju suatu situasi ketersediaan yang tinggi (High Availability / HA) pada service kamu, maka kamu mulai menggunakan 
lebih dari 1 web server dan database server. Ambil contoh untuk web server. Sebagai langkah awal, Kamu mungkin mulai 
menggunakan 2 atau 3 web server. Yang mana semua request didistribusikan ke 2 atau 3 web server tersebut melalui mesin 
load balancer (LB).

```text
                                              +-----------------+
                                              |                 |
                                              |                 |
                                         +----> Web Server 1    |
                                         |    |                 |
                                         |    |                 |
                                         |    +-----------------+
                                         |
                 +-----------------+     |    +-----------------+
  Request        |                 |     |    |                 |
+----------------> Load Balancer   +---------->                 |
                 |                 |     |    | Web Server 2    |
                 +-----------------+     |    |                 |
                                         |    |                 |
                                         |    +-----------------+
                                         |
                                         |    +------------------+
                                         |    |                  |
                                         |    |                  |
                                         +----> Web Server3      |
                                              |                  |
                                              |                  |
                                              +------------------+
```

<!--more-->

Pada diagram ke-1 diatas, semua request dilewatkan ke mesin load-balancer (LB) yang mana mesin LB lah yang mendistribusikan 
request ke salah satu web server dari 3 web server (election) di belakangnya. 

Nah, pertanyaannya, **bagaimana jika mesin LB pada diagram diatas mati / failure?** Gawat donk... semua request yang masuk tidak 
dapat didistribusikan ke 3 web server yang tersedia. Oleh karena itu, kita harus menyediakan mekanisme failover pada
level LB.

Solusinya adalah menggunakan 2 mesin LB. 1 mesin sebagai master LB, dan 1 mesin lagi sebagai backup/secondary LB. Dan,
menggunakan floating IP address dan mekanisme health checking untuk dapat memindahkan rute secara otomatis.

## Mulai membangun

```text
                             +-----------------+
                             |                 |
                             |    WS LB 1      |                        +----------+
                             |                 |                        |          |
                         +---> +-------------+ |                        |          |
                         |   | |Health Check | +----------+-----------> |   WS 1   |
                         |   | +-----------^-+ |          |             |          |
                         |   |             |   |          |             +----------+
                         |   +-----------------+          |
                         |                 |              |             +----------+
                         |                 |              |             |          |
 Request   +-----------+ |                 |              +-----------> |          |
+--------> |Floating IP+-+                 |              |             |   WS 2   |
           +-----------+ |                 |              |             |          |
                         |                 |              |             +----------+
                         |   +-----------------+          |
                         |   |             |   |          |
                         |   |             |   |          |             +----------+
                         |   |    WS LB 2  |   |          |             |          |
                         |   |             |   |          |             |          |
                         +---> +-----------v-+ |          +-----------> |   WS 3   |
                             | |Health Check | |                        |          |
                             | +-------------+ |                        +----------+
                             |                 |
                             +-----------------+
```


Pada diagram ke-2 diatas, terlihat ditambahkan 1 mesin LB lagi dimana WS LB 1 adalah primary/master LB dan WS LB 2 adalah
secondary/backup LB. Jika suatu saat WS LB 1 failure, maka floating IP secara otomatis dipindahkan ke WS LB 2 daannn tugas 
load balancing diambil alih oleh WS LB 2. 

Mesin LB yang berstatus secondary / backup menjalankan mekanisme health checking dimana mesin WS LB 2 "bertanya" ke mesin WS 
LB 1 dalam setiap periode waktu tertentu contoh: setiap 2 detik. Mekanisme health checking diimplementasikan oleh software 
bernama **Keepalived**.

```text
                             +-----------------+
                             |                 |
                             |    WS LB 1      |                        +----------+
                             |                 |                        |          |
                         +---> +-------------+ |                        |          |
                         |   | |Health Check | |           +----------> |   WS 1   |
                         |   | +-----------^-+ |           |            |          |
                         |   |             |   |           |            +----------+
                         |   +-----------------+           |
                         |                 |               |            +----------+
                         |                 |               |            |          |
 Request   +-----------+ |                 |               |            |          |
+--------> |Floating IP+-+                 |               +----------> |   WS 2   |
           +-----------+ |                 |               |            |          |
                         |                 |               |            +----------+
                         |   +-----------------+           |
                         |   |             |   |           |
                         |   |             |   |           |            +----------+
                         |   |    WS LB 2  |   |           +----------> |          |
                         |   |             |   |           |            |          |
                         +---> +-----------v-+ +-----------+            |   WS 3   |
                             | |Health Check | |                        |          |
                             | +-------------+ |                        +----------+
                             |                 |
                             +-----------------+
```

Pada diagram ke-3 diatas, divisualisasikan bahwa WS LB 1 sedang failure, maka semua request dialihkan secara otomatis ke WS LB 2. 

Untuk diagram yang lebih bagus visualnya, dan lebih detail cara kerjanya, kamu bisa lihat di sini: [https://assets.digitalocean.com/articles/high_availability/ha-diagram-animated.gif](https://assets.digitalocean.com/articles/high_availability/ha-diagram-animated.gif)

### Bahan-bahan

Untuk membangun infrastruktur dengan arsitektur seperti pada diagram diatas yaitu menggunakan 2 load-balancer maka diperlukan
bahan-bahan sebagai berikut:

1. **Floating IP**
   
   Saya menggunakan fitur floating IP dari Digital Ocean (DO). Dan, memang tutorial ini sebenarnya spesifik untuk Digital 
   Ocean. Manual floating IP dari DO bisa dibaca di: [https://www.digitalocean.com/docs/networking/floating-ips/quickstart/](https://www.digitalocean.com/docs/networking/floating-ips/quickstart/)

2. **Keepalived**

   Untuk implementasi health checker. [http://www.keepalived.org/](http://www.keepalived.org/). Keepalived di-setup juga di semua LB dan di artikel ini akan diberitahu dan dijelaskan cara setup Keepalived. Karena memang artikel ini fokusnya adalah
   pada software ini.

3. **2 Web Server**

   Minimal 2 Droplet untuk web server yang mana sudah ter-setup web server dan segala macamnya di dalamnya. Kamu dapat mengikuti artikel ini: [http://okaprinarjaya.github.io/setup-php-web-app-stack-di-gnu-linux-centos7/](http://okaprinarjaya.github.io/setup-php-web-app-stack-di-gnu-linux-centos7/). Karena di artikel ini saya tidak memberitahu dan menjelaskan bagaimana cara setup web server.

4. **2 Load Balancer**

   Tentunya 2 droplet untuk 2 load-balancer. 1 untuk primary/master LB, 1 lagi untuk secondary/backup LB. 

5. **HAProxy**

   Untuk implementasi load-balancer. [http://www.haproxy.org/](http://www.haproxy.org/). HAProxy di-setup di semua LB.
   Di artikel ini saya tidak memberitahu dan menjelaskan bagaimana caranya setup HAProxy.

### Langkah-langkah Instalasi 

1. Pastikan mesin-mesin web server dan load-balancer sudah ter-setup lengkap terisi software-software yang sesuai dengan peruntukkannya.

2. Install paket **keepalived** melalui `yum` package manager di semua mesin LB. `yum install keepalived -y`

3. Buat user baru dengan perintah: `sudo useradd -g users -M keepalived_script`

4. Setelah instalasi `keepalived` selesai dan berhasil, maka akan tersedia file konfigurasinya di `/etc/keepalived/keepalived.conf`.

#### Assign Floating IP di LB 1 / primary / master LB

Caranya cukup mudah, ikuti saja langkah-langkah yang di artikel dari Digital Ocean berikut ini: [https://www.digitalocean.com/docs/networking/floating-ips/quickstart/](https://www.digitalocean.com/docs/networking/floating-ips/quickstart/).

Dan pastikan floating IP address kamu assign ke LB 1 / primary / master LB, jangan sampai lupa! Setelah berhasil assign kamu 
akan mendapatkan 1 static public IP yang ke depannya IP address ini tidak akan pernah berubah. Floating IP address inilah
yang akan menjadi pointing dari alamat domain kamu di setting DNS. Contoh: di DNS kamu, kamu setting 
mycoolservice.com --> mapping ke Floating IP address yang didapat dari proses assign floating IP ke LB 1.

Untuk test floating IP, kamu bisa melakukan test dengan cara mengakses floating IP melalui web browser 
`http://floating_ip_yang_kamu_dapat`. Dan, floating IP kamu menghubungi / call IP address LB 1 kamu. Lalu muncullah 
halaman website kamu. Seharusnya.

#### Download script python otomatisasi assign floating IP

Download script python bernama `assign-ip.py` di `http://do.co/assign-ip` dengan cara berikut:

```
cd /usr/local/bin
curl -LO http://do.co/assign-ip
```

script python `assign-ip` harus ditaruh di `/usr/local/bin`.

Jadi, konsepnya cukup sederhana. Script python ini akan meng-otomatisasi proses assign floating IP ke sebuah droplet. Assign 
floating IP secara manual cukup dilakukan sekali saja seperti yang sudah dijelaskan sebelumnya diatas. 

Jadi, jika LB 1 failure, maka dijalankan script python `assign-ip` yang akan meng-assign floating IP ke LB 2 dan floating IP 
di LB 1 dicabut. Jika LB 1 sudah aktif kembali, maka dijalankan lagi script python untuk meng-assign floating IP ke LB 1 dan 
floating IP di LB 2 dicabut.


#### Buat dan Dapatkan DigitalOcean API Token

Ikut langkah-langkah yang sudah ada di artikel DigitalOcean berikut ini: [https://www.digitalocean.com/docs/api/create-personal-access-token/](https://www.digitalocean.com/docs/api/create-personal-access-token/)

#### Konfigurasi Keepalived

**Konfigurasi di LB 1 / primary / master LB**

Buka file konfigurasi di `/etc/keepalived/keepalived.conf` di mesin **LB 1**. Kosongkan isi file konfigurasi default dengan 
perintah: `echo > keepalived.conf`. Biasanya mengosongkan file harus sebagai `root` dengan perintah `sudo su`. Lalu Isikan 
baris-baris konfigurasi berikut ini:

```text
global_defs {
    enable_script_security
}

vrrp_script chk_haproxy {
    script "/usr/sbin/pidof haproxy"
    interval 2
}

vrrp_instance VI_1 {
    interface eth0
    state MASTER
    priority 200

    virtual_router_id 33
    unicast_src_ip IP_ADDR_LB1
    unicast_peer {
        IP_ADDR_LB2
    }
    
    authentication {
        auth_type PASS
        auth_pass isi_passwd_bebas_maks_8_karakter
    }
    
    track_script {
        chk_haproxy
    }

    notify_master /etc/keepalived/master.sh
}
```

password dari `auth_pass` nantinya di LB2 **HARUS SAMA** dengan password di LB1.

**Konfigurasi di LB 2 / secondary / backup LB**

Buka file konfigurasi di `/etc/keepalived/keepalived.conf` di mesin **LB 2**. Kosongkan isi file konfigurasi default dengan 
perintah: `echo > keepalived.conf`. Biasanya mengosongkan file harus sebagai `root` dengan perintah `sudo su`. Lalu Isikan 
baris-baris konfigurasi berikut ini:

```text
global_defs {
    enable_script_security
}

vrrp_script chk_haproxy {
    script "/usr/sbin/pidof haproxy"
    interval 2
}

vrrp_instance VI_1 {
    interface eth0
    state BACKUP
    priority 100

    virtual_router_id 33
    unicast_src_ip IP_ADDR_LB2
    unicast_peer {
        IP_ADDR_LB1
    }
    
    authentication {
        auth_type PASS
        auth_pass isi_passwd_bebas_maks_8_karakter
    }
    
    track_script {
        chk_haproxy
    }

    notify_master /etc/keepalived/master.sh
}
```

Ingat! isi password dari `auth_pass` di LB2 ini **HARUS SAMA** dengan password di LB1.

**Membuat shell script master.sh**

Buatlah file `master.sh` di semua mesin LB (LB 1 dan LB 2). Lalu isikan dengan baris-baris perintah berikut:

```sh
#!/bin/bash
export DO_TOKEN='DigitalOcean_API_Token_Put_Here'
IP='FLOATING_IP_ADDR'
ID=$(curl -s http://169.254.169.254/metadata/v1/id)
HAS_FLOATING_IP=$(curl -s http://169.254.169.254/metadata/v1/floating_ip/ipv4/active)

if [ $HAS_FLOATING_IP = "false" ]; then
    n=0
    while [ $n -lt 10 ]
    do
        python /usr/local/bin/assign-ip $IP $ID && break
        n=$((n+1))
        sleep 3
    done
fi
```

shell script `master.sh` di mesin LB 1 dan mesin LB 2 isinya sama.

Lalu, jangan lupa berikan permission executable dengan perintah `chmod +x master.sh`.

**Starting Keepalived**

```
systemctl start keepalived
systemctl enable keepalived
```

Lakukan perintah diatas di semua mesin LB. 

