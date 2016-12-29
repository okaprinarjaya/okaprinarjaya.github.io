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

![Stack Pointer Register](http://res.cloudinary.com/okaprinarjaya/image/upload/v1482073533/okadiary/call-stack/SP1rsz.png)

### Stack Frame
Adalah tumpukan yang sudah di-kelompok-kelompok-an. Kelompok 1 dengan jumlah sekian tumpukan adalah stack frame milik fungsi 
`main()`, Kelompok 2 dengan jumlah sekian tumpukan adalah stack frame untuk fungsi `tambah()`, Kelompok 3 dengan jumlah sekian 
tumpukan adalah stack frame dari fungsi `kurang()` dan begitu seterusnya.

![Stack Frame](http://res.cloudinary.com/okaprinarjaya/image/upload/v1482073533/okadiary/call-stack/StackFrameRsz.png)

### Frame Pointer (FP) / Base Pointer (BP) Register
Mirip halnya seperti SP Register, FP / BP Register adalah sebuah register yang berfungsi sebagai penunjuk yang selalu menunjuk pada tumpukkan paling atas dari stack frame sebuah fungsi yang sudah selesai eksekusinya. 

Sederhananya, Jika posisi tunjuk FP / BP Register sudah berubah, itu artinya adalah terbentuk lagi satu stack frame baru. Posisi 
FP / BP Register  menyediakan penunjuk / referer yang stabil untuk mengakses parameter - parameter dan variabel - variabel lokal 
dari fungsi - fungsi yang ada secara ter-isolasi per fungsi. 

![Frame Pointer Register](http://res.cloudinary.com/okaprinarjaya/image/upload/v1482073532/okadiary/call-stack/FPRsz1.png)

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

## Studi Kasus / Praktek
Disini saya akan tunjukkan bagaimana proses call stack terlihat secara langsung melalui sebuah tool bernama GNU Debugger (`gdb`).
Apa yang akan saya debug adalah sebuah program super kecil dan sederhana yang hanya menjumlahkan 2 angka. Pastikan `gdb` dan 
paket-paket essential untuk development sudah terinstall di komputer kamu. Asumsi saya adalah kamu menggunakan OS GNU/Linux.

Oiya..dan kamu harus sedikit paham membaca set instruksi-instruksi (instructions set) dari processor arsitektur x86 atau x86_64. 
Atau lebih sering dikenal dengan bahasa pemrograman Assembly.

### Program Sederhana

Tulis dan simpan source code program sederhana berikut dengan nama bebas. Contoh: `just-add.c`

```c
#include <stdio.h>

int add(int a, int b)
{
    int result = a + b;
    return result;
}

int main(int argc, char *argv[])
{
    int answer;
    answer = add(40, 2);

    return 0;
}

```
Lalu, lanjutkan dengan meng-compile source code program diatas dengan perintah `gcc -Wall -g -o just-add just-add.c` Jika kamu 
berhasil meng-compile, lalu coba jalankan program dengan perintah `./just-add` dan hasilnya memang kosong / tidak ada output 
apapun. 

### Mulai Debugging
Setelah membuat program sederhana, mari lanjutkan dengan debugging. Disinilah kita akan mengetahui bagaimana proses call stack 
berjalan.

Jalankan perintah `gdb just-add` maka akan muncul output seperti ini:

```text
GNU gdb (Ubuntu 7.7.1-0ubuntu5~14.04.2) 7.7.1
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from just-add...done.
(gdb) _
```
Lanjutkan menjalankan perintah-perintah selanjutnya sampai muncul code assembly dari program C sederhana tadi diatas. 

```text
(gdb) break *main
Breakpoint 1 at 0x400507: file addadd.c, line 19.
(gdb) display $rsp
(gdb) display $rbp
(gdb) run
Starting program: /home/okaprinarjaya/ASSEMBLY/just-add 

Breakpoint 1, main (argc=0, argv=0x400530 <__libc_csu_init>) at addadd.c:19
19	{
2: $rbp = (void *) 0x0
1: $rsp = (void *) 0x7fffffffdfa8
(gdb) disas
Dump of assembler code for function main:
=> 0x0000000000400507 <+0>:	push   %rbp
   0x0000000000400508 <+1>:	mov    %rsp,%rbp
   0x000000000040050b <+4>:	sub    $0x20,%rsp
   0x000000000040050f <+8>:	mov    %edi,-0x14(%rbp)
   0x0000000000400512 <+11>:	mov    %rsi,-0x20(%rbp)
   0x0000000000400516 <+15>:	mov    $0x2,%esi
   0x000000000040051b <+20>:	mov    $0x28,%edi
   0x0000000000400520 <+25>:	callq  0x4004ed <add>
   0x0000000000400525 <+30>:	mov    %eax,-0x4(%rbp)
   0x0000000000400528 <+33>:	mov    $0x0,%eax
   0x000000000040052d <+38>:	leaveq 
   0x000000000040052e <+39>:	retq   
End of assembler dump.
(gdb) 

```
Supaya tidak terlalu panjang, saya tidak akan melanjutkan menunjukkan menjalankan perintah-perintah dari `gdb` menjadi satu 
disini. Saya pisahkan perintah-perintah langkah demi langkah debuggingnya ke file terpisah. Disini saya akan cukupkan sampai
memunculkan assembly saja. File perintah-perintah gdb bisa dilihat disini.

Baik, mari kita lanjutkan. Dari perintah `disas` dari program `gdb` maka muncullah assembly (instructions set) dari program 
sederhana kita diatas tadi. Kita akan eksekusi satu baris instruksi demi satu baris instruksi dengan perintah `stepi`.

Berikut adalah susunan stack sebelum instruksi `push %rbp` dijalankan. Atau disebut sebagai "Program Prologue". Program 
prologue meng-inisiasi tumpukkan dan mengisi tumpukkan dengan data-data: Jumlah argumen, parameter-parameter fungsi untuk 
fungsi `main()` sebelum fungsi `main()` dijalankan dan return address.

![Stack Init](http://res.cloudinary.com/okaprinarjaya/image/upload/v1482869707/okadiary/call-stack/cs1.png)
<br />*Gambar 1*

<hr />

```asm
push   %rbp
mov    %rsp, %rbp
```

Dengan dijalankannya instruksi diatas, makan susunan stack menjadi seperti gambar 2 berikut: 

![Stack](http://res.cloudinary.com/okaprinarjaya/image/upload/v1482869707/okadiary/call-stack/cs2.png)
<br >*Gambar 2*

Dan pada gambar 2 dengan dijalankan instruksi PUSH RBP ke stack, maka terbentuklah satu stack frame baru. Seperti penjelasan 
sebelumnya diatas, RBP adalah FP (Frame Pointer). 

<hr />

```asm
sub    $0x20, %rsp
```
![Stack](http://res.cloudinary.com/okaprinarjaya/image/upload/v1483036507/okadiary/call-stack/cs3.png)
<br />*Gambar 3*

<hr />

```asm
mov    %edi, -0x14(%rbp)
mov    %rsi, -0x20(%rbp)
mov    $0x2, %esi
mov    $0x28,%edi
callq  0x4004ed
```
![Stack](http://res.cloudinary.com/okaprinarjaya/image/upload/v1483037077/okadiary/call-stack/cs4.png)
<br />*Gambar 4*

Instruksi `callq` mem-PUSH return address ke tumpukan lalu berlanjut melompat ke dan menjalankan instruksi selanjutnya yaitu
label `add` . 

Pada file kumpulan perintah-perintah, anda akan diminta untuk menjalankan perintah `stepi` di program berulangkali sampai 
mencapai instruksi `callq  0x4004ed` . Jika instruksi `callq  0x4004ed` sudah dijalankan maka kamu udah berada di dalam urutan 
instruksi-instruksi untuk label / function `add()` . Untuk melihat instruksi-instruksiny jalankan perintah `disas` dengan 
outputnya seperti berikut:

```text
Dump of assembler code for function add:
=> 0x00000000004004ed <+0>:	push   %rbp
   0x00000000004004ee <+1>:	mov    %rsp,%rbp
   0x00000000004004f1 <+4>:	mov    %edi,-0x14(%rbp)
   0x00000000004004f4 <+7>:	mov    %esi,-0x18(%rbp)
   0x00000000004004f7 <+10>:	mov    -0x18(%rbp),%eax
   0x00000000004004fa <+13>:	mov    -0x14(%rbp),%edx
   0x00000000004004fd <+16>:	add    %edx,%eax
   0x00000000004004ff <+18>:	mov    %eax,-0x4(%rbp)
   0x0000000000400502 <+21>:	mov    -0x4(%rbp),%eax
   0x0000000000400505 <+24>:	pop    %rbp
   0x0000000000400506 <+25>:	retq   
End of assembler dump.
```
<hr />

```asm
push   %rbp
mov    %rsp, %rbp
```
![Stack](http://res.cloudinary.com/okaprinarjaya/image/upload/v1483038105/okadiary/call-stack/cs5.png)
<br />*Gambar 5*

<hr />

```asm
mov    %edi, -0x14(%rbp)
mov    %esi, -0x18(%rbp)
mov    -0x18(%rbp), %eax
mov    -0x14(%rbp),%edx
add    %edx,%eax
```
![Stack](http://res.cloudinary.com/okaprinarjaya/image/upload/v1483038629/okadiary/call-stack/cs6.png)
<br />*Gambar 6*

<hr />
