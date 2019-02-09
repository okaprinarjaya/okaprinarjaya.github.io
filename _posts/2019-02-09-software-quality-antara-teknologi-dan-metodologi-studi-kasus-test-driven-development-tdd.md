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

Nah, bagaimana penerapannya? **Sulit** kah?, atau **Mudah** kah?, atau **Sulit-sulit-mudah** kah? Menurut saya pribadi,
jawabannya **SULIT**. Karena metodologi itu akan merubah **paradigma**, **pola pikir**, **kebiasaan**, **gaya**, dan banyak
hal lain yang bersifat psikologis yang dipaksa harus diubah. Banyak orang-orang yang **mungkin** akan merasa **kesal**, 
dan **terbebani** dan bahkan **gila**. Hahahahaha... itu yang terakhir saya ber-lebay ria aja kok...#JustKidding.

Tidak ada support service yang bisa ditelpon untuk mendapatkan bantuan menangani masalah. Karena jika ditemukan / menghadapi
suatu masalah saat penerapan suatu metodologi, solusinya dicari dengan duduk bareng dengan semua orang yang terlibat di dalam 
suatu kelompok.

### Implementasi Teknologi

Nah ini, ini merupakan hal yang menyenangkan bagi setiap software engineer. Saya dan **mungkin** banyak teman-teman software
engineer lainnya biasanya **gatel** icip-icip teknologi baru. Aahhh...rasanya kereennnn bangettt bisa menguasai suatu 
teknologi baru. Rasanya cewek-cewek di ruangan semuanya melirik ke kamu memandang kamu betapa kerennya kamu. Hahaha ya,
karena saya seorang pria. 

Hhhmmmm...menguasai? wait..wait..menguasai? Beneran sudah menguasai? Atau hanya sekedar download, install, konfigurasi? 
Ya, memang semudah itu untuk implementasi suatu teknologi. Saya tidak perlu lagi menanyakan **Sulit** kah?, atau **Mudah** 
kah?, atau **Sulit-sulit-mudah** kah?. Apa sulitnya? tinggal download, install, konfigurasi. Lalu, tinggal lempar saja ke 
divisi infrastruktur dan biarkan kawan-kawan di divisi infrastuktur *do their job* sampai puyeng tujuh keliling untuk 
memelihara "mahluk-mahluk" itu. Tidak akan ada **paradigma, pola pikir, kebiasaan, dan gaya** yang akan berubah. Pastinya
semua orang akan nyaman2 aja dan setuju.

Hey! sabarrr..sabarr..saya sama sekali tidak bermaksud menentang niatan teman-teman sejawat / seprofesi dengan niatannya 
berkontribusi untuk meningkatkan kualitas produk dengan cara menerapkan teknologi-teknologi baru. Saya juga sama sangat
antusias dengan penerapan teknologi-teknologi baru. 

## Metodologi: Test Driven Development (TDD)

Ok, di bagian ini dari tulisan ini saya awali dengan dua pertanyaan: `"Mengapa tidak banyak orang yang gatel dengan software quality?"`, `"Mengapa orang-orang lebih banyak yang gatel dengan teknologi baru?"`.

Ya, TDD adalah suatu metodologi. Metdologi pengembangan software yang ber-orientasi test sederhananya saya sebagai seorang 
programmer disetir / diatur-atur oleh suatu kumpulan test cases (unit case) untuk membuat suatu real implementasi. Jadi,
sebelum membuat real implementasinya, saya diwajibkan untuk memulai dengan membuat semua test cases nya terlebih dahulu.

Setahu saya, metodologi ini memang mengesalkan bagi mayoritas software engineer dan bahkan saya pun juga merasa kesal dengan 
metodologi ini. Dulu tapi. Karena TDD memaksa saya untuk menulis test cases terlebih dahulu sebelum membuat real implementasi 
dari suatu modul / komponen dari produk yg saya kembangkan.

OMG! bayangkan! saya dipaksa untuk mengkhayal tanpa ada visual / output apapun. Dipaksa untuk menentukan / mendetermine / 
mendefinisikan apa-apa saja sebenarnya yang saya butuhkan untuk mengimplementasikan requirements dari orang-orang product di 
sana. Aannjayyy capek! pegel! linu! kepala cekot2! pacar marah2! untung saja tidak sampai diputusin. 

### Software Quality dengan TDD (atau metodologi apapun)

Di tulisan saya ini tidak mau menjelaskan panjang lebar apa itu TDD karena sudah banyak sekali artikel-artikel yang jauh lebih
berkualitas untuk menjelaskan secara detail tentang TDD. Tujuan saya di tulisan ini adalah mengajak kawan-kawan semuanya
untuk mulai "gatel" dikit (dikit aja) juga dengan yg namanya software quality melalui penerapan TDD. Karena saya pun 
baru-baru aja "gatel" dng metodologi ini. Hahahahaha....

Sebagai contoh sederhana, kita ambil contoh Kafka. di infrastruktur kita belum ada yang namanya teknologi [https://kafka.apache.org/](Kafka). 
Situasinya di infrastruktur kita sudah ada [https://www.rabbitmq.com/](RabbitMQ). **Disclaimer: ini sebagai contoh saja. Maafkan jika pemahaman saya terhadap RabbitMQ dan Kafka kurang benar atau malah sama sekali gak benar**.
Suatu hari terdengarlah kabar ke para tech-big-boss di perusahaan tempat kita bekerja. Mereka mendengar ada suatu teknologi 
baru yang bernama Kafka yang katanya bisa membantu meningkatkan kualitas produk dari sisi blablablablablablablabla. Brosurnya
Kafka menjanjikan blablablablablablabla beuhh maniss banget kalimat-kalimat dalam brosurnya. Dan, tech-big-bossess pun "gatel"
/ tertarik untuk mencoba implementasi si Kafka ini. Dalam benak mereka dengan penerapan teknologi Kafka ini berharap kualitas
produk bisa meningkat dari sisi speed of delivery, reliability of service, dan lain-lain seperti yang dibilang di brosur.

Selain tech-big-bosses, VP, tech lead beserta serdadu-serdadunya pun setuju dan excited. Mereka semua meyakini, jika 
dianalogikan seperti agama, mereka semua "mengimani" nya. Meyakini dan "mengimani" nya bahwa ini teknologi Kafka dapat 
membantu mereka untuk meningkatkan kualitas produk. Mereka ngoprekin teknologi dengan penuh antusias, dan semangat.

Tapi, apakah setelah penerapan teknologi Kafka itu kualitas produk benar meningkat? apakah kemudahan development benar menjadi
lebih mudah? apakah reliability service kita benar meningkat? **TAPI** meningkat atau pun tidak meningkat, biasanya saya 
tidak peduli (karena saya juga bagian / termasuk salah satu dari mereka). Saya akan terus semangat ngoprekin itu teknologi
dengan harapan jika saya resign dari current company, di perusahaan baru gaji saya meningkat drastis oleh karena punya
pengalaman yang cukup lama di teknologi Kafka. Ya! itu wajar, saya setuju. Tidak ada yang salah dengan itu.

Naaahhhh, pertanyaannya, mengapa kita tidak pernah begitu excited / meyakini / "mengimani" dengan penerapan metodologi TDD?
Di tulisan ini saya sama sekali tidak membela TDD, saya bukan pejuang TDD. Saya tidak menentang metodologi-metodologi yang
lainnya. Apapun itu metodologinya, silahkan, saya dukung 100%. Mau TDD, mau BDD, mau DDD, mau kombinasi TDD + BDD, BDD + DDD
atau apapun itu, silahkan. Yang penting itu bukan FDD (Fear Driven Develomnet) Hahahahahaha... Anggap saja TDD itu layaknya
seperti kita memilih Kafka. Ya karena banyak ada teknologi lain yang sama fungsinya seperti Kafka. Tapi kenapa kita pilih
Kafka? Yaa karena ada pilihan yang harus dipilih, ya tinggal dipilih. Kita pilih Kafka, done! Kita pilih TDD done! bukankah
begitu?

Concern saya adalah mari yuk bareng-bareng kita mulai "gatel" dengan salah satu dari sekian banyak metodologi itu. Mulai 
meyakininya, "mengimani" nya seperti hal nya antusiasme kita terhadap teknologi. Ikuti dengan antusias semua 
prinsip-prinsipnya, dan values nya. Temukan pola-polanya, diskusi duduk bareng untuk setiap masalah yang ditemui karena 
setahu tidak ada customer service yang bisa dihubungi jika ada masalah dalam proses suatu penerapan metodologi.

### Tolok ukur kesuksesan implementasi metodologi TDD (atau metodologi apapun)








