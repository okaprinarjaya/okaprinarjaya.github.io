---
layout: post
title: Software Quality, Antara Teknologi dan Metodologi. Studi kasus, Test Driven Development (TDD)
---

## Awalan

Berbicara tentang teknology dan metodologi memang merupakan 2 hal yang sangat berbeda. Dan 2 hal ini sangat erat kaitannya 
sebagai fondasi untuk menuju yang namanya software quality. Pemilihan technology dan methodology yang tepat, pastinya 
mempengaruhi seberapa berkualitas software yang kita bangun.

Dalam pandangan awam saya, tanpa survey, dan tanpa data dan karena saya seseorang yang dengan literasi jongkok, jadi saya 
bermodalkan kondisi sedang mabuk untuk menulis artikel ini. Di sebelah saya ada satu kerat teh botol. Ya mabuk teh botol. 

Di Indonesia, khususnya di perusahaan-perusahaan berbasis dan memang teknologi sebagai tulang punggungnya, para lord nya 
(orang-orang di level management teratas) sudah mulai melirik, mempertimbangkan mengimplementasikan suatu methodology 
tertentu pada proses pengembangan produk-produk mereka. Dengan harapan dapat meningkatkan kualitas produk.

Teknologi? Tidak usah ditanya lagi, orang-orang kita sudah sejak lama dan sudah banyak yang jago dalam hal implementasi 
teknolog-teknologi terbaru. Mmmmmmmm...ternyata kualitas produk yang dihasilkan pun dinilai masih kurang OK. Walaupun sudah 
menggunakan teknolog-teknologi baru sekalipun. Ya..saya akui memang, OK atau kurang OK itu relatif. Dan, apakah implementasi
suatu methodology baru akan menjamin meningkatnya kualitas produk? Ya! Tentu saja! Tentu saja tidak bisa dipastikan juga 
apakah akan meningkat atau malah menurun. Hahahahaha...

Tapi, ingat metodologi juga amat sangat **PENTING** sama halnya seperti teknologi dan malah **lebih** berpengaruh dalam 
peningkatan kualitas dibandingkan teknologi.

<!--more-->

## Teknologi

Dalam sudut pandang awam saya pribadi, teknologi itu adalah alat yang bersifat teknis untuk mengimplementasikan suatu tujuan
/ rancangan / ide. Biasanya bentuknya berupa perangkat keras atau perangkat lunak dan ataupun kombinasi / gabungan dari 
perangkat keras dan lunak.

Suatu alat ada cara pakainya, ada buku manualnya, ada langkah-langkah penggunaannya yang dinomerin dari nomer `1`... `x`. 
Saat apa yang kita ikuti dalam buku manual tidak ber-efek, maka tinggal telpon support service vendor dari alat tersebut.

## Metodologi

Dalam sudut pandang awam saya pribadi, metodologi itu adalah suatu kerangka kerja dimana bentuknya bukan suatu alat fisik. 
Tidak ada bentuknya, metodologi itu isinya prinsip-prinsip, values, dan lebih bersifat visi-misi. Metodologi tidak dapat
bergerak / berjalan dengan sendirinya seperti hal nya teknologi yang bisa kita tinggal pencet tombol "On" lalu, bisa kita
tinggalkan, lalu teknologi itu akan bekerja dengan sendirinya.

Tidak, metodologi tidak bisa seperti teknologi. Metodologi **HANYA** bisa bergerak / berjalan oleh atau melalui orang / 
seseorang / "peoples" gtu bahasa zimbabwe nya. Metodologi itu tertanam di dalam diri seseorang itu. Jadi orang itulah yang
menyebabkanbuat suatu metodologi itu dapat bergerak / berjalan terus.

## Teknologi dan Metodologi

Saya adalah seorang software engineer. Jika ditanya oleh seseorang lain, "metodologi apa yang kamu terapkan dalam 
pengembangan perangkat lunak kamu?". Ya, saya dan **mungkin** kebanyakan teman-teman software engineer lain juga akan menjawab
"ennggg...mmmmm........... ngg nganuu keramm eh sekram ehh SCRUM kali yaaa". Hahahaha ya mungkin seperti itu.

Tapi, jika saya ditanya "teknologi apa yang kamu gunakan dalam pengembangan perangkat lunak kamu?". Ya, tanpa 
"ang..ing..eng..", biasanya saya dan **mungkin** juga teman-teman software engineer lainnya akan menjawab 
"saya menggunakan PHP, MySQL, Java, Golang, PostgreSQL, MongoDB, Cassandra, Kafka, RabbitMQ, Redis, Load Balancer, Linux, 
Windows, Vim, Jetbrains, ReactJs, Kotlin, Apache, NginX, ..................... jadi ampe lupa nanyain pacar udah maem / mandi 
blomm". Hahahaha ya mungkin seperti itu.

### Implementasi Metodologi

Kapan dan dimana suatu metodologi diterapkan? Menurut saya pribadi, suatu metodologi diterapkan di suatu kelompok orang yang 
dimana di dalam kelompok itu jumlah orang-orang nya sudah sangat banyak. Menurut saya, jarang ada untuk implementasi 
suatu metodologi di personal / single performer. Ya walaupun ada, mungkin saja orang itu sudah kebiasaan bekerja dng suatu
metodologi itu karena sebelumnya berasal dari suatu kelompok yang menerapkan suatu metodologi itu juga sebelumnya.

Nah, bagaimana penerapannya? **Sulit** kah?, atau **Mudah** kah?, atau *Sulit-sulit-mudah* kah? Menurut saya pribadi,
jawabannya **SULIT**. Karena metodologi itu akan merubah **paradigma**, **pola pikir**, **kebiasaan**, **gaya**, dan banyak
hal lain yang bersifat psikologis yang dipaksa harus diubah. Banyak orang-orang akan merasa **kesal**, **terbebani**, 



