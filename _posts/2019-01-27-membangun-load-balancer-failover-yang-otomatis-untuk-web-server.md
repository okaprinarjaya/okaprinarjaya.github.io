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


