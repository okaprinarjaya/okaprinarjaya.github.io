---
layout: post
title: Dasar Penggunaan Maven di Eclipse IDE
---

## Instalasi Maven di Windows

1. Download Maven di https://maven.apache.org/download.cgi .Saya menggunakan versi terbaru versi 3.5.0

2. Extract / Unpack / Unzip file archive maven yang sudah kamu download di `C:\`. Setelah berhasil ter-extract maka terbentuk direktori bernama `apache-maven-3.5.0`

3. Tambahkan string `C:\apache-maven-3.5.0\bin` ke system environment variable, ke variable `Path` di kolom System variables. Dengan cara sebagai berikut
    * Buka Windows Explorer, klik kanan pada icon Computer, pilih klik Properties, terbuka sebuah window lalu klik pada menu `Advanced system setting`
    * Muncul window kecil baru yang sudah aktif di tab `Advanced` lalu klik tombol `Environment Variables` yang berada di paling bawah 
    * Muncul window kecil baru, lalu pada box `System variables` double klik pada variable dengan nama `Path`
    * Muncul window kecil lalu tambahkan string baru di paling akhir string pada field `Variable value`
    * String yg ditambahkan adalah string `;C:\apache-maven-3.5.0\bin`. Perhatikan! harus ada titik koma (semicolon) terlebih dahulu sebelum menambahkan string baru apapun.

4. Test instalasi maven apakah maven sudah bisa berjalan dengan baik dengan cara: Buka windows command prompt (cmd) **yang baru**, lalu ketik perintah `mvn -version`

<!--more-->

## Instalasi Maven di GNU/Linux Ubuntu

1. Download Maven di https://maven.apache.org/download.cgi .Saya menggunakan versi terbaru versi 3.5.0

2. Extract file archive `apache-maven-3.5.0-bin.tar.gz` ke `/opt/` dengan perintah `tar -xzvf apache-maven-3.5.0-bin.tar.gz -C /opt/`

3. Edit / buka file `/etc/environment` lalu tambahkan string `:/opt/apache-maven-3.5.0/bin` di bagian baris `PATH`. Sehingga 
menjadi seperti ini: `PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/opt/apache-maven-3.5.0/bin"`

4. Lanjutkan jalankan perintah `source /etc/environment`

5. Untuk test apakah maven berhasil terinstall, jalankan perintah: `mvn -version`. JIKA perintah tersebut tidak ditemukan, maka 
restart komputer kamu.
    
## Instalasi Eclipse IDE

1. Download Eclipse IDE di https://www.eclipse.org/downloads/eclipse-packages/ .Saya menggunakan Eclipse IDE for Java EE Developers 64 
bit. Kamu bisa download Eclipse IDE for Java Developers jika kamu tidak membutuhkan pengembangan aplikasi berbasis web menggunakan Java

2. Extract / Unpack / Unzip file archive Eclipse IDE yang sudah kamu download di `C:\` lalu akan terbentuk direktori baru dengan nama `eclipse`

3. Masuk ke direktori `C:\eclipse` melalui windows explorer, lalu double klik pada `eclipse.exe` untuk mulai menjalan Eclipse IDE

## Aktifkan Maven repository index di Eclipse IDE

Mengapa kita harus meng-aktifkan opsi ini? Jawabannya, agar kita dapat dengan mudah meng-install semuaaaa kebutuhan library. Berikut caranya:

1. Buka Eclipse IDE
2. Pada menu bar klik `Window` --> klik `Preferences` lalu muncul window baru
3. Pada kolom sebelah kiri, klik menu `Maven` lalu pada kolom sebelah kanan, centang / aktifkan `Download repository index updates on startup`
4. Klik Apply, klik Ok, tutup Eclipse IDE lalu buka lagi. Pada saat Eclipse dibuka lagi, maka Eclipse akan secara otomatis melakukan 
download informasi library-library di repository. Dan proses download repository index lumayan memakan waktu yang lama. Jadi jika terasa 
sangat lama, maka tunggu saja itu wajar

## Latihan membuat project berbasis Maven

Pastikan proses download repository index sudah selesai. Jika proses download repository index sudah selesai, maka mari kita latihan 
membuat satu project berbasis Maven.


