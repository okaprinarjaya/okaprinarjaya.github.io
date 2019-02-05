---
layout: post
title: ProxySQL Sebagai MySQL Database Load-balancer, Connection-pooling, dan Query Routing
---

Semua instalasi dan konfigurasi dilakukan di lingkungan sistem operasi GNU/Linux CentOS 7. Database yang saya gunakan 
adalah MariaDB v.10.x terbaru dan sudah meng-aktifkan dan mengkonfigurasi fitur Galera Database Clustering. Tutorial ini 
tidak membahas cara install dan konfigurasi MariaDB Clustering. Jadi, untuk dapat mengikuti tutorial ini, kamu sudah harus
memiliki mesin-mesin yang sudah ter-install MariaDB, terkonfigurasi dan sudah ter-cluster dengan baik.

## Instalasi ProxySQL

Tulisan ini menyadur cara instalasi di [https://github.com/sysown/proxysql/wiki](https://github.com/sysown/proxysql/wiki). 
Jadi, jika kamu tidak menggunakan GNU/Linux CentOS 7 maka di link manual itu ada cara instalasi untuk distro GNU/Linux yang 
lainnya.

<!--more-->

Berikut langkah - langkah instalasi ProxySQL:

1. Buatlah file bernama `proxysql.repo` di `/etc/yum.repos.d/` dengan isinya seperti berikut ini:

    ```ini
    [proxysql_repo]
    name= ProxySQL YUM repository
    baseurl=http://repo.proxysql.com/ProxySQL/proxysql-2.0.x/centos/\$releasever
    gpgcheck=1
    gpgkey=http://repo.proxysql.com/ProxySQL/repo_pub_key
    ```

2. Download dan install ProxySQL dengan perintah: `yum install proxysql`

3. Aktifkan service ProxySQL dengan perintah: `systemctl start proxysql` dan `systemctl enable proxysql`

4. Login ke ProxySQL administration interface dengan perintah: `mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '`

Jika kamu belum memiliki MySQL client (bukan MySQL server), maka bisa langsung install dengan perintah berikut 
`yum install mysql`. Perintah tersebut menginstall HANYA mysql client bukan MySQL server.

## Konfigurasi monitoring

Konfigurasi yang paling mendasar adalah konfigurasi fungsi monitoring. ProxySQL akan melakukan tindakan health-check terhadap
database nodes yang ada di belakangnya (backend servers). Untuk dapat meng-aktifkan fungsi monitoring ini, kamu harus 
membuat user baru bernama `monitor` di sisi masing-masing database server (bukan di sisi ProxySQL nya).

Berikut langkah - langkah konfigurasi fungsi monitoring:

### Membuat user `monitor` di sisi masing-masing database server

Login ke salah satu node di cluster database server kamu, lalu buatlah satu user baru bernama `monitor` dengan perintah 
berikut: 

```sql
CREATE USER 'monitor'@'%' IDENTIFIED BY 'passwordKmuYangPalingKeceh';

GRANT SELECT on sys.* to 'monitor'@'%';

FLUSH PRIVILEGES;
```

Karena kamu sudah menggunakan database server yang sudah ter-cluster, maka user `monitor` yang baru tersebut juga dibuat dan 
tersedia di semua database server lainnya secara otomatis.

### Konfigurasi di sisi ProxySQL

Di mesin ProxySQL, login ke ProxySQL administration interface dengan perintah berikut:

```sh
mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

Lalu mendaftarkan user `monitor` dengan perintah - perintah berikut ini:

```
Admin> UPDATE global_variables SET variable_value='monitor' WHERE variable_name='mysql-monitor_username';

Admin> UPDATE global_variables SET variable_value='passwordKmuYangPalingKeceh' WHERE variable_name='mysql-monitor_password';

Admin> UPDATE global_variables SET variable_value='2000' WHERE variable_name IN ('mysql-monitor_connect_interval','mysql-monitor_ping_interval','mysql-monitor_read_only_interval');
```

Perubahan - perubahan yang terjadi di table `global_variables` HANYA aktif saat kamu sudah menjalankan perintah berikut ini:

```
Admin> LOAD MYSQL VARIABLES TO RUNTIME;

Admin> SAVE MYSQL VARIABLES TO DISK;
```

Jadi, pastikan setiap melakukan perubahan pada table `global_variables` jangan lupa untuk menjalankan 2 perintah diatas.

### Mendaftarkan semua database server di ProxySQL

Setelah proses mendaftarkan user monitor beres, lalu lanjutkan dengan mendaftar semua database server yang akan berada di 
belakang (backend servers) ProxySQL nantinya. Ikuti langkah-langkah berikut ini untuk menambahkan backend servers:

```
Admin> INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'IP_ADDR_DB_NODE-1',3306);

Admin> INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'IP_ADDR_DB_NODE-2',3306);

Admin> INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'IP_ADDR_DB_NODE-3',3306);
```

Check apakah semua server sudah berhasil didaftarkan dan masuk ke table `mysql_servers`. Berikut caranya:

```
Admin> SELECT hostgroup_id, hostname, port, status FROM mysql_servers;
```

Jika semua server sudah terdaftar, maka sekarang check apakah ProxySQL berhasil berkomunikasi dengan semua database server
yang ada di belakangnya. Berikut caranya:

```
Admin> SELECT * FROM monitor.mysql_server_connect_log ORDER BY time_start_us DESC LIMIT 10;
```

dan

```
Admin> SELECT * FROM monitor.mysql_server_ping_log ORDER BY time_start_us DESC LIMIT 10;
```

dari hasil output 2 perintah diatas, perhatikan pada kolom `connect_error` dan `ping_error`. Jika semua kolom tersebut 
bernilai `NULL` maka itu berarti ProxySQL berhasil berkomunikasi dengan semua database server yang ada di belakangnya dengan
lancar.

Satu hal yang sangat penting untuk dicatat, monitoring dengan connect dan ping dilakukan berdasarkan isi dari table 
`mysql_servers` TANPA perlu data di dalam table `mysql_servers` di-LOAD terlebih dahulu ke RUNTIME. Memang sengaja didesain
seperti itu, karena dengan tanpa perlu LOAD terlebih dahulu ke RUNTIME, kita dapat melakukan basic health checks dulu 
ke node / backend server yg baru yg ingin kita tambahkan sebelum benar-benar menambahkannya ke production.

Jika ProxySQL sudah berhasil dan lancar berkomunikasi dengan backend servers, lalu jalankan perintah berikut ini untuk 
mengaktifkan (LOAD) monitoring ke semua backend servers yg terdaftar secara permanen ke RUNTIME. Berikut perintahnya:

```
Admin> LOAD MYSQL SERVERS TO RUNTIME;

Admin> SAVE MYSQL SERVERS TO DISK;
```

Dan, selesai sudah bagian konfigurasi paling mendasar ini. Bagian ini adalah fondasi untuk bagian-bagian selanjutnya. Jadi
pastikan semua berfungsi dengan baik di bagian ini. Ok, mariii kita lanjutkan ke bagian-bagian selanjutnya!

## ProxySQL Sebagai Load-balancer

Di system ProxySQL, table yang berperan sebagai fungsi load-balancer adalah table `mysql_servers` dan table `mysql_users`. 
Table `mysql_servers` adalah table referensi untuk mendistribusikan traffic dan table `mysql_users` adalah table yang 
menyimpan data-data authentication untuk digunakan login oleh ProxySQL ke salah satu backend servers sesuai informasi yang
didapat dari table `mysql_servers`.

### Menambahkan operational users di ProxySQL

Operational user yang dimaksud adalah user yang digunakan untuk login ke semua backend servers. Dimana operational users ini
akan melakukan semua kegiatan-kegiatan operasional seperti: INSERT, SELECT, DELETE, UPDATE, CREATE VIEW, CREATE TRIGGER,
CREATE STORED PROCEDURE, dan lain-lain yang bersifat operasional data.

Contoh: di dalam table `mysql_users` terdapat baris data dengan kolom `username` berisikan nilai `asoygeboy`, dan kolom 
`password` berisikan nilai `xxx`. Maka nanti di aplikasi kita, setting database connection nya diarahkan ke 
IP ProxySQL, Port: 6033, dengan username: `asoygeboy` dan password: `xxx`.

Berikut caranya untuk menambahkan operational users:

```
Admin> INSERT INTO mysql_users(username,password,default_hostgroup) VALUES ('asoygeboy','xxx',1);
```

Field-field `username`, `password`, `default_hostgroup` adalah field-field yang paling penting untuk harus diisi. Penting 
untuk dicatat dan dipahami, field `default_hostgroup` adalah field yang berperan sebagai penentu destinasi hostgroup default 
yang akan dikirimkan traffic *JIKA* tidak ada *query rules / query routing* yang cocok. 

Lalu, load configuration runtime untuk membuat konfigurasi ini aktif dan tetap ada aktif saat mesin restart. Berikut caranya:

```
Admin> LOAD MYSQL USERS TO RUNTIME;

Admin> SAVE MYSQL USERS TO DISK;
```





