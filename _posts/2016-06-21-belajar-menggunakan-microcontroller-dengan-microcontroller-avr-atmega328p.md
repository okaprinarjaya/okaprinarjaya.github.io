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
Register adalah suatu lokasi / tempat di dalam MCU atau Prosesor yang berfungsi sebagai penyimpan nilai-nilai. Ada banyak register di dalam MCU dan masing-masing register memiliki memory address yang **unik** dan **statik**. Dan masing-masing register juga punya "pelanggan" nya masing-masing. Oleh karena itulah mengapa ada yang namanya **I/O Registers**, yang mana merupakan sekumpulan register yang bertugas menampung nilai dan mengirim nilai dari dan ke I/O yg bersangkutan. 

Tentu saja ada juga register yang "pelanggan" nya adalah si programmer (kita) itu sendiri, disebut dengan **General Purpose Register**. Kita bisa menyimpan nilai apa saja di dalam General Purpose Register dan melakukan proses-proses selanjutnya terhadap nilai yang tersimpan di dalam register tersebut menggunakan bagian-bagian prosesor yg lain, contoh: ALU (Arithmetic Logic Unit).Tidak hanya MCU yang memiliki register, prosesor (Intel, AMD, dan lain-lain) pun juga memiliki register.

### Jenis - jenis Register

#### I/O Registers
Adalah register-register yang menampung nilai dan mengirim nilai dari dan ke peripheral yang bersangkutan dalam rangka "menggerakkan" peripheral yang bersangkutan (Timers, USART, SPI, TWI, ADC, dan lain-lain). Berdasarkan datasheet di Figure 8.3  ATMega328P memiliki **64 I/O Registers** dengan alamat memori mulai dari **0x0020** s/d **0x005F**

#### General Purpose (GP) Registers
Adalah register-register yang sifatnya bebas, dapat digunakan untuk menyimpan data untuk keperluan apa saja. Dan di arsitektur AVR beberapa operasi seperti operasi aritmatika, dan pembandingan hanya bisa dilakukan di GP Registers. Berdasarkan datasheet di Figure 8.3 ATMega328P memiliki **32 General Purpose Registers** dengan alamat memori mulai dari **0x0000** s/d **0x001F**. 32 GP Register diberi nama **R0** s/d **R31**.

#### Extended I/O Registers
Jumlahnya ada **160** dan berikut penjelasan dalam bahasa inggris:
As the AVRs grew and more peripherals were added, in and out could not serve enough I/O locations. Additional I/O registers are placed above the I/O registers, but are treated like normal SRAM.
