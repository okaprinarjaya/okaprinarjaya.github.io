---
layout: post
title: Call Stack
---

## Pengantar
Tulisan ini adalah tulisan yang merupakan tulisan dengan kategori computer science. Call Stack merupakan salah satu bagian 
konsep dari sekian banyak bagian-bagian konsep yang tersusun menjadi satu dalam topik bagaimana sebuah software berjalan 
(running) di komputer. Tujuan utama tulisan ini adalah untuk pengingat (catatan) untuk diri sendiri.

**Call** identik dengan pemanggilan sebuah fungsi. **Stack** adalah sebuah area di memori (RAM) yang isinya susunan / tumpukan 
yang digunakan dalam proses pemanggilan (eksekusi) fungsi. Sebuah fungsi terdiri dari parameter-parameter, variabel-variabel 
lokal, dan nilai kembalian (return value). Parameter-parameter, variabel-variabel dan nilai kembalian itulah yang disimpan di stack.

## Bagian - bagian Call Stack
Belajar tentang Call Stack, maka muncul bagian - bagian berikut:

* Stack Pointer (SP) Register
* Stack Frame
* Frame Pointer (FP) / Base Pointer (BP) Register
* Return Address
* Previous Saved FP / BP Register

### Stack Pointer (SP) Register
Adalah sebuah register yang berfungsi sebagai penunjuk (pointer) yang selalu menunjuk (pointing) pada tumpukan paling atas. 
Tumpukan-tumpukan diproses menggunakan aturan **LIFO (Last In First Out)**, dan instruksi **POP** untuk mengambil data dari 
tumpukan dan instruksi **PUSH** untuk menambahkan data ke tumpukan.

### Stack Frame
Adalah tumpukan yang sudah di-kelompok-kelompok-an. Kelompok 1 dengan jumlah sekian tumpukan adalah stack frame milik fungsi 
`main()`, Kelompok 2 dengan jumlah sekian tumpukan adalah stack frame untuk fungsi `tambah()`, Kelompok 3 dengan jumlah sekian 
tumpukan adalah stack frame dari fungsi `kurang()` dan begitu seterusnya.

### Frame Pointer (FP) / Base Pointer (BP) Register
Mirip halnya seperti SP Register, FP Register adalah sebuah register yang berfungsi sebagai penunjuk yang selalu menunjuk pada 
lokasi yang permanen / tetap (fixed) dari stack frame sebuah fungsi yang sedang mendapatkan gilirannya berjalan / aktif. 

Sederhananya, Posisi Stack Frame paling atas adalah Stack Frame dari fungsi yang sedang (current) berjalan / aktif. Frame 
Pointer menunjuk pada tumpukan paling atas sebuah Stack Frame. Frame pointer menyediakan penunjuk / referer yang stabil untuk 
mengakses parameter - parameter dan variabel - variabel lokal dari fungsi - fungsi yang ada secara ter-isolasi per fungsi. 

### Return Address
Return Address adalah data tumpukan yang berisikan alamat memory dari instruksi yang harus dieksekusi selanjutnya. Return Address juga adalah merupakan data yang disimpan di tumpukkan.

### Previous Saved FP / BP Register
Seringkali disebut Base Pointer Register adalah penanda jejak satu fungsi sudah selesai dijalankan dan pada saat yang sama juga 
menjadi penanda terbentuknya satu Stack Frame. Isi dari BP Register ini adalah copy dari nilai SP Register. Lalu Nilai dari BP 
Register di-PUSH / disimpan ke tumpukkan. 

Diperlukan penyimpanan nilai BP Register ke sebuah Stack Frame adalah agar bisa melakukan nested function call. Tanpa adanya 
penyimpanan BP Register ke tumpukan, prosesor tidak dapat mengetahui harus lompat ke instruksi mana selanjutnya saat melakukan 
nested function call. 
