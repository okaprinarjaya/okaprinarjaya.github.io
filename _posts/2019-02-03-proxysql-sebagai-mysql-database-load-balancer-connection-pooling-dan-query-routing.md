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

## ProxySQL sebagai Connection-pooling

Pada dasarnya saat kamu menyelesaikan setup proxysql sebagai load-balancer, fitur connection pooling sudah aktif secara 
otomatis. Dibuktikan dengan field bernama `max_connections` di table `mysql_servers` yang memiliki nilai default `1000`. 
Yang artinya adalah total jumlah koneksi yang diterima oleh masing-masing backend adalah terbatas maksimal 1000 koneksi.

### Mengontrol jumlah koneksi yang bersiaga (connection idle)

Jumlah koneksi yang bersiaga (idle) ditentukan oleh variabel `mysql-free_connections_pct` di table `global_variables`. 

```
Admin> SELECT * FROM global_variables WHERE variable_name LIKE 'mysql-free_connections_pct';
+----------------------------+----------------+
| variable_name              | variable_value |
+----------------------------+----------------+
| mysql-free_connections_pct | 10             |
+----------------------------+----------------+
1 row in set (0.00 sec)
```

Rumus yang digunakan untuk menentukan berapa jumlah koneksi yang akan bersiaga adalah sebagai berikut: 
`(mysql-free_connections_pct * max_connections) / 100`. Contoh: Jika `mysql-free_connections_pct = 10`, dan 
`max_connections = 1000`, maka `(10 * 1000) / 100 = 100 koneksi siaga`.

Jika kamu ada merubah nilai dari variable `mysql-free_connections_pct` atau variabel-variabel lain apapun di table 
`global_variables` maka kamu jangan lupa untuk jalankan perintah berikut:

```
Admin> LOAD MYSQL VARIABLES TO RUNTIME;

Admin> SAVE MYSQL VARIABLES TO DISK;
```

### Workflow Connection-pooling

* Sebuah sesi memerlukan sebuah koneksi ke server, maka bertanya ke connection-pool.

* Jika ada koneksi di kumpulan koneksi untuk backend itu, koneksi itu digunakan, jika tidak, koneksi baru dibuat.

* Ketika suatu sesi membebaskan koneksi, maka object koneksi dikirim kembali ke Hostgroup Manager. Jika Hostgroup Manager 
  menentukan bahwa object koneksi aman untuk dibagikan dan connection-pool tidak penuh, maka object koneksi ditempatksan di 
  connection-pool.

* Jika suatu object koneksi dinilai tidak aman untuk dibagi, contoh: karena koneksi tersebut memuat session variables, 
  temporary tables. Maka Hostgroup manager tidak menaruh object koneksi ke pool, tapi akan menghancurkan object koneksi 
  tersebut.

## Query Routing / Query Rules

Query routing yang saya maksud adalah fitur proxysql untuk menentukan traffic query yg cocok dengan suatu akan dikirim ke 
mesin-mesin yang dengan hostgroup yg sesuai dengan pola tersebut.

Table yang berperan untuk melakukan query routing adalah table `mysql_query_rules`. Ada 6 utama field di table ini yang 
harus diisi yaitu: `rule_id`, `active`, `username`, `match_digest`, `destination_hostgroup`, `apply`.

Sebagai contoh kasus, saya butuh semua traffic query yang berisikan kata-kata `SELECT` diarahkan ke mesin-mesin yang 
hostgroup id `2`. Untuk memastikan, lihat lagi field `hostgroup_id` di table `mysql_servers`.

Untuk menyelesaikan contoh kasus diatas, maka kita input regex rule ke table `mysql_query_rules` dengan cara seperti berikut
ini: 

```
Admin> INSERT INTO mysql_query_rules (rule_id,active,username,match_digest,destination_hostgroup,apply) VALUES (1,1,'asoygeboy','SELECT',2,1);
```

Karena informasi yang disimpan di field `match_digest` adalah berupa string regex, maka PASTIKAN regex kamu sudah valid dan 
sesuai dengan yang dibutuhkan. Jika pola regex sudah valid dan sesuai maka jalankan perintah:

```
Admin> LOAD MYSQL QUERY RULES TO RUNTIME;
```

Dengan aktifnya query rules yang baru saja kita definisikan diatas, maka semua traffic query yg berupa SELECT akan diarahkan 
ke hostrgroup id `2`.

### Membuktikan hasil Query Routing / Rules

Di sisi saya, saya melakukan test di database saya sendiri dengan nama database dan table yang saya susun sendiri. Jadi 
tentunya hasil output tidak sama di sisi kamu.

Log semua traffic query disimpan di table `stats_mysql_query_digest`. 

```
Admin> SELECT hostgroup, sum_time, count_star, digest_text FROM stats_mysql_query_digest WHERE digest_text LIKE '%tbl_satu%' ORDER BY sum_time DESC;
+-----------+----------+------------+-----------------------------------------------------------------+
| hostgroup | sum_time | count_star | digest_text                                                     |
+-----------+----------+------------+-----------------------------------------------------------------+
| 1         | 28825    | 2          | INSERT INTO tbl_satu VALUES (?, ?)                              |
| 1         | 12280    | 2          | INSERT INTO `tbl_satu` (`id`, `tbl_satu_content`) VALUES (?, ?) |
| 2         | 3424     | 4          | SELECT * FROM tbl_satu                                          |
| 2         | 2095     | 3          | SELECT * FROM `tbl_satu` LIMIT ?,?                              |
| 1         | 2054     | 1          | SHOW FULL COLUMNS FROM `tbl_satu` FROM `cooldb`                 |
| 1         | 1014     | 2          | SHOW CREATE TABLE `tbl_satu`                                    |
+-----------+----------+------------+-----------------------------------------------------------------+
```

Perhatikan pada query `SELECT * FROM tbl_satu` dan `SELECT * FROM `tbl_satu` LIMIT ?,?`. Traffic query sudah berhasil 
diarahkan ke hostgroup id `2`. Dan semua traffic query lainnya yang tidak sesuai dengan pola diarahkan ke hostgroup id `1`

