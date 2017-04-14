## Instalasi Maven

1. Download Maven di https://maven.apache.org/download.cgi . Saya menggunakan versi terbaru versi 3.5.0

2. Extract / Unpack / Unzip file archive maven yang sudah kamu download di `C:\` . Setelah berhasil ter-extract maka terbentuk direktori bernama `apache-maven-3.5.0`

3. Tambahkan string `C:\apache-maven-3.5.0\bin` ke system environment variable, ke variable `Path` di kolom System variables. Dengan cara sebagai berikut
    * Buka Windows Explorer, klik kanan pada icon Computer, pilih klik Properties, terbuka sebuah window lalu klik pada menu `Advanced system setting`
    * Muncul window kecil baru yang sudah aktif di tab `Advanced` lalu klik tombol `Environment Variables` yang berada di paling bawah 
    * Muncul window kecil baru, lalu pada box `System variables` double klik pada variable dengan nama `Path`
    * Muncul window kecil lalu tambahkan string baru di paling akhir string pada field `Variable value`
    * String yg ditambahkan adalah string `;C:\apache-maven-3.5.0\bin`. Perhatikan! harus ada titik koma (semicolon) terlebih dahulu sebelum menambahkan string baru apapun.

4. Test instalasi maven apakah maven sudah bisa berjalan dengan baik dengan cara: Buka windows command prompt (cmd) **yang baru**, lalu ketik perintah `mvn -version`
    
## Instalasi Eclipse IDE

// 
