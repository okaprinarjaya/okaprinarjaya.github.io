---
layout: post
title: Setup PHP Web App Stack di GNU/Linux CentOS 7
---

## Web Server

Ada 2 pilihan populer untuk web server di lingkungan GNU/Linux yaitu: 

1. Apache HTTPD 
2. NGINX

### Instalasi Apache HTTPD

Langkah - langkah instalasi Apache HTTPD:

1. sudo yum install httpd
2. sudo systemctl start httpd
3. sudo systemctl enable httpd

### Instalasi NGINX

Langkah - langkah instalasi NGINX:

1. Buat file bernama nginx.repo di /etc/yum.repos.d/

2. Isi file nya berikut ini:
    ```text
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/7/$basearch/
    gpgcheck=0
    enabled=1
    ```

3. sudo yum install nginx -y

4. systemctl start nginx

5. systemctl enable nginx

