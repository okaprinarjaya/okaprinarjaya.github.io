---
layout: post
title: Webpack Snorkling
---

## Pengantar
Kata "Snorkling" disini saya pakai sebagai kiasan yang berarti mempelajari dan menggunakan webpack hanya sampai pada level dasar dan 
pemula. Tulisan ini akan membahas cara instalasi webpack, sedikit konsep webpack, dan penerapan / implementasi dari konsep webpack. Semoga 
dengan adanya tulisan ini, saya dapat dengan mudah menerapkan webpack dimana saja karena semua tata cara instalasi, konfigurasi, dan 
penggunaan tercatat disini semua.

## Instalasi Webpack
Sebelum memulai instalasi Webpack, pastikan kamu sudah install versi paling fresh Node.js terlebih dahulu. Release Node.js LTS yang paling
baru bisa menjadi pilihan yang ideal dan direkomendasikan. Karena kamu akan menemui banyak masalah dengan versi yang lebih lama seperti 
beberapa fungsionalitas yang hilang atau beberapa package terkait (dependency) yang belum didukung di versi lama.

Pertama, Install webpack dengan menggunakan `npm` (node package manager) dan ketersediaan package dibuat lokal. **Ingat perbedaan ada dua 
jenis ketersediaan package** setelah diinstall yaitu: **lokal** dan **global**. Opsi `--save-dev` membuat ketersediaan package menjadi lokal dan 
sebaliknya opsi `-g` membuat ketersediaan package menjadi global. 

```sh
npm install webpack --save-dev
```

Cara menjalankan package dengan ketersediaan lokal adalah sebagai berikut: `node_modules/.bin/webpack` .

## Konsep dasar Webpack ?
Webpack adalah sebuah pembundel (bundler) untuk aplikasi-aplikasi javascript. Tentunya kamu tidak asing / pastinya tahu apa 
itu pem-bundel-an (bundling). Sederhananya, asumsikan ada beberapa file javascript yaitu: `abc.js`, `def.js`, `ghi.js`. 3 file javascript 
itu akan digabung menjadi satu file bundle dengan nama `alphabet.js`. 

Tentunya webpack tidak hanya menyediakan / mampu melakukan hal sederhana itu saja. Webpack memiliki kemampuan melakukan pemrosesan-
pemrosesan untuk sebuah file sebelum file tersebut digabungkan ke file bundle nya. Pemrosesan-pemrosesan itu bisa berupa proses compile, 
transformasi, minifikasi, uglify, dan lain-lain sesuai kebutuhan.

//
