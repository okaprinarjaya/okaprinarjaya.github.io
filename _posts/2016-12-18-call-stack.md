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
* Saved Previous FP / BP Register

### Stack Pointer (SP) Register
Adalah sebuah register yang berfungsi sebagai penunjuk (pointer) yang selalu menunjuk (pointing) pada tumpukan paling atas. 
Tumpukan-tumpukan diproses menggunakan aturan **LIFO (Last In First Out)**, dan instruksi **POP** untuk mengambil data dari 
tumpukan dan instruksi **PUSH** untuk menambahkan data ke tumpukan.

![Stack Pointer Register](https://github.com/okaprinarjaya/okaprinarjaya.github.io/blob/master/images/call-stack/SP1rsz.png)

### Stack Frame
Adalah tumpukan yang sudah di-kelompok-kelompok-an. Kelompok 1 dengan jumlah sekian tumpukan adalah stack frame milik fungsi 
`main()`, Kelompok 2 dengan jumlah sekian tumpukan adalah stack frame untuk fungsi `tambah()`, Kelompok 3 dengan jumlah sekian 
tumpukan adalah stack frame dari fungsi `kurang()` dan begitu seterusnya.

![Stack Frame](https://github.com/okaprinarjaya/okaprinarjaya.github.io/blob/master/images/call-stack/StackFrameRsz.png)

### Frame Pointer (FP) / Base Pointer (BP) Register
Mirip halnya seperti SP Register, FP / BP Register adalah sebuah register yang berfungsi sebagai penunjuk yang selalu menunjuk pada tumpukkan paling atas dari stack frame sebuah fungsi yang sudah selesai eksekusinya. 

Sederhananya, Jika posisi tunjuk FP / BP Register sudah berubah, itu artinya adalah terbentuk lagi satu stack frame baru. Posisi 
FP / BP Register  menyediakan penunjuk / referer yang stabil untuk mengakses parameter - parameter dan variabel - variabel lokal 
dari fungsi - fungsi yang ada secara ter-isolasi per fungsi. 

### Return Address
Return Address adalah data yang berisikan alamat memory dari instruksi yang harus dieksekusi selanjutnya. Return Address juga 
adalah merupakan data yang disimpan di tumpukkan.

### Saved Previous FP / BP Register Data
Adalah **data** yang posisinya di tumpukan paling atas dari sebuah Stack Frame yang berfungsi sebagai penanda jejak fungsi 
paling akhir yang  sudah selesai dijalankan, dan pada saat yang sama juga menjadi penanda terbentuknya satu Stack Frame baru. 
Isi dari data Saved Previous FP/BP Register ini adalah nilai dari FP/BP Register Stack Frame **sebelumnya** atau Stack Frame 
yang berada tepat di bawah Stack Frame yang paling baru. 

Diperlukan penyimpanan nilai FP/BP Register sebelumnya ke sebuah Stack Frame adalah agar bisa melakukan nested function call. 
Tanpa adanya penyimpanan Previous FP/BP Register ke tumpukan, prosesor tidak dapat mengetahui harus lompat ke instruksi mana 
selanjutnya saat melakukan nested function call. 
