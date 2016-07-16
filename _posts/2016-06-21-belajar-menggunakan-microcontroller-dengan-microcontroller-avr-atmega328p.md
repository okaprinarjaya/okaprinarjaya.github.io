---
layout: post
title: Belajar Menggunakan Microcontroller dengan Microcontroller AVR ATMega328P
---

Untuk dapat menggunakan sebuah microcontroller atau sering disingkat dengan MCU, kita wajib dan harus mengetahui karakteristik microcontroller (MCU) yang akan kita gunakan. Mulai dari Electricity requirements, ketersediaan I/O, I/O Pins (Kaki-kaki IC), I/O Registers, dan bit settings dari masing-masing I/O register. dan fitur-fitur lainnya yang informasinya dapat kita ketahui melalui membaca datasheet.

## Secuil tentang AVR
AVR adalah nama arsitektur microcontroller  yang diciptakan oleh dua orang mahasiswa Norwegian Institute of Technology (NTH). 
Mengapa sebuah arsitektur microcontroller dinamakan AVR ? berdasarkan informasi dari Wikipedia, https://en.wikipedia.org/wi/Atmel_AVR AVR adalah singkatan dari nama dua orang mahasiswa NTH yang menciptakan arsitektur AVR. AVR berarti **Alf (Egil Bogen) and Vegard (Wollan)'s RISC processor**. Arsitektur AVR juga mengimplementasikan arsitektur RISC (Reduced Instruction Set Computer) dan modified Harvard Architecture.

## ATMega328P
Microcontroller ATMega328P adalah produk microcontroller dari Atmel http://www.atmel.com yang menggunakan arsitektur AVR. 
ATMega32P adalah microcontroller 8 bit yang sudah diintegrasikan dengan 32Kb ISP Flash memory dan banyak lagi fitur-fitur inti yang lainnya. 

Berikut tampilan packaging dari microcontroller ATMega328p :
![MCU Packaging](http://res.cloudinary.com/okaprinarjaya/image/upload/c_scale,w_800/v1466608123/okadiary/ATMEGA328P-PU.jpg)

## Registers
Register adalah suatu lokasi / tempat di dalam MCU atau Prosesor yang berfungsi sebagai penyimpan nilai-nilai dan memiliki. Ada banyak register di dalam MCU dan masing-masing register memiliki memory address yang **unik** dan **statik**. Dan masing-masing register juga punya "pelanggan" nya masing-masing. Oleh karena itulah mengapa ada yang namanya **I/O Registers**, yang mana merupakan sekumpulan register yang bertugas menampung nilai dan mengirim nilai dari dan ke I/O yg bersangkutan. 

Tentu saja ada juga register yang "pelanggan" nya adalah si programmer (kita) itu sendiri, disebut dengan **General Purpose Register**. Kita bisa menyimpan nilai apa saja di dalam General Purpose Register dan melakukan proses-proses selanjutnya terhadap nilai yang tersimpan di dalam register tersebut menggunakan bagian-bagian prosesor yg lain, contoh: ALU (Arithmetic Logic Unit) .

Tidak hanya MCU yang memiliki register, prosesor (Intel, AMD, dan lain-lain) pun juga memiliki register. Tapi jika sudah berbicara di level prosesor, tentunya jumlah register yang adalam prosesor jumlahnya amat sangat banyak.
