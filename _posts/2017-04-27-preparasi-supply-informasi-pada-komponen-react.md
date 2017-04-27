---
layout: post
title: Preparasi supply informasi pada komponen React
---

## Pengantar

Setiap komponen pada suatu aplikasi berbasis React pastinya ketergantungan dengan berbagai jenis informasi pada saat komponen itu mulai 
diaktifkan (mounted). Informasi-informasi itu dapat berupa informasi wording, informasi styling, informasi bahasa, informasi daftar menu, 
dan informasi berupa function yang nantinya bisa dipanggil dan banyak lagi jenis informasi yang dapat di-supply ke sebuah komponen.

Nah, Informasi-informasi itu akan disiapkan (preparasi) dan di-supply ke sebuah component melalui function-function yang kita definisikan
sendiri. Function-function preparasi itu akan menjadi / bersifat function umum yang bisa dimanfaatkan untuk preparasi supply informasi ke 
semua komponen, tidak hanya komponen tertentu saja. Dan biasanya informasi-informasi itu disimpan sebagai konfigurasi yang disimpan dalam 
sebuah file konfigurasi atau object state aplikasi React kita.

