---
layout: post
title: Membangun Load Balancer Failover yang Otomatis Untuk Web Server
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

Pada diagram diatas, semua request dilewatkan ke mesin load-balancer (LB) yang mana mesin LB lah yang mendistribusikan 
request ke salah satu web server dari 3 web server (election) di belakangnya. 

Nah, pertanyaannya, bagaimana jika mesin LB pada diagram diatas mati / failure? Gawat donk... semua request yang masuk tidak 
dapat didistribusikan ke 3 web server yang tersedia. Oleh karena itu, kita harus menyediakan mekanisme failover pada
level LB.

Solusinya adalah menggunakan 2 mesin LB. 1 mesin sebagai master LB, dan 1 mesin lagi sebagai backup/secondary LB. Dan,
menggunakan floating IP address mekanisme health checking untuk dapat memindahkan rute secara otomatis.

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


Pada diagram diatas, terlihat ditambahkan 1 mesin LB lagi dimana WS LB 1 adalah primary/master LB dan WS LB 2 adalah
secondary/backup LB. Jika suatu saat WS LB 1 failure, maka floating IP secara otomatis dipindahkan ke WS LB 2 daannn tugas 
load balancing diambil alih oleh WS LB 2. 

Masing - masing mesin LB menjalankan mekanisme health checking dimana mesin WS LB 2 "bertanya" ke mesin WS LB 1 dalam
setiap periode waktu tertentu contoh: setiap 2 detik. 

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

### Bahan-bahan

Untuk membangun infrastruktur dengan arsitektur seperti pada diagram diatas yaitu menggunakan 2 load-balancer maka diperlukan
bahan-bahan sebagai berikut:

1. **Floating IP**
   
   Saya menggunakan fitur floating IP dari Digital Ocean (DO). Dan, memang tutorial ini sebenarnya spesifik untuk Digital 
   Ocean. Manual floating IP dari DO bisa dibaca di: [https://www.digitalocean.com/docs/networking/floating-ips/quickstart/](https://www.digitalocean.com/docs/networking/floating-ips/quickstart/)

2. **2 Web Server**

   Minimal 2 Droplet untuk web server yang mana sudah ter-setup web server dan segala macamnya di dalamnya. Kamu dapat mengikuti artikel ini: [http://okaprinarjaya.github.io/setup-php-web-app-stack-di-gnu-linux-centos7/](http://okaprinarjaya.github.io/setup-php-web-app-stack-di-gnu-linux-centos7/). Karena di artikel ini saya tidak memberitahu dan menjelaskan bagaimana cara setup web server.

3. **2 Load Balancer**

   Tentunya 2 droplet untuk 2 load-balancer. 1 untuk primary/master LB, 1 lagi untuk secondary/backup LB. 

4. **HAProxy**

   Untuk implementasi load-balancer. [http://www.haproxy.org/](http://www.haproxy.org/). HAProxy di-setup di semua LB.
   Di artikel ini saya tidak memberitahu dan menjelaskan bagaimana caranya setup HAProxy.

5. **Keepalived**

   Untuk implementasi health checker. [http://www.keepalived.org/](http://www.keepalived.org/). Keepalived di-setup juga di semua LB dan di artikel ini akan diberitahu dan dijelaskan cara setup Keepalived. Karena memang artikel ini fokusnya adalah
   pada software ini.

### Langkah-langkah Instalasi



