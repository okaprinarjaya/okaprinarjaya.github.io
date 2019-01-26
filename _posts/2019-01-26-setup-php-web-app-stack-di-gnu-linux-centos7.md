---
layout: post
title: Setup PHP Web App Stack di GNU/Linux CentOS 7
---

## Web Server

Ada 2 pilihan populer untuk web server di lingkungan GNU/Linux yaitu: 

1. Apache HTTPD 
2. NGINX

### Instalasi Apache HTTPD

Langkah - langkah instalasi:

1. sudo yum install httpd
2. sudo systemctl start httpd
3. sudo systemctl enable httpd

<!--more-->

### Instalasi NGINX

Langkah - langkah instalasi:

1. Buat file bernama nginx.repo di `/etc/yum.repos.d/`

2. Isi file nya berikut ini:
    ```text
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/7/$basearch/
    gpgcheck=0
    enabled=1
    ```

3. `yum install nginx -y`

4. `systemctl start nginx`

5. `systemctl enable nginx`

6. Jika butuh menjalankan PHP dengan web server NGINX maka ikuti bagian "Instalasi PHP 5.6 dan PHP FPM"

### Instalasi PHP 5.6 dan PHP FPM

Langkah - langkah instalasi: 

1. `yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm`

2. `yum --disablerepo=remi-safe update`

3. `yum-config-manager --enable remi-php56`

4. `yum install php php-fpm php-devel php-pear php-mcrypt php-cli php-curl php-mysql php-ldap php-zip php-fileinfo -y`

5. `vim /etc/php.ini`
    
    Ubah nilai dari variabel `cgi.fix_pathinfo` menjadi bernilai 0 (noL). `cgi.fix_pathinfo=0`

6. **Konfigurasi PHP FPM**
    
    `vim /etc/php-fpm.d/www.conf`, lalu edit baris-baris berikut:
        
        * `user = nginx`
        
        * `group = nginx`

7. Konfigurasi NGINX untuk mengkonsumsi PHP-FPM sebagai fastcgi di /etc/nginx/conf.d/default.conf

8. `systemctl restart nginx`

9. `systemctl start php-fpm`

10. `systemctl enable php-fpm`
