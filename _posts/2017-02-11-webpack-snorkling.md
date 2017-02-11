---
layout: post
title: Webpack Snorkling
---

## Pengantar
Kata "Snorkling" disini saya pakai sebagai kiasan yang berarti mempelajari dan menggunakan webpack hanya sampai pada level dasar dan 
pemula atau cukup tahu permukaan-permukaan nya saja. Tulisan ini akan membahas cara instalasi webpack, sedikit konsep webpack, dan 
penerapan / implementasi dari konsep webpack. Semoga dengan adanya tulisan ini, saya pribadi dapat dengan mudah menerapkan webpack dimana 
saja karena semua tata cara instalasi, konfigurasi, dan penggunaan tercatat disini semua.

## Instalasi Webpack
Sebelum memulai instalasi webpack, pastikan kamu sudah install versi paling fresh Node.js terlebih dahulu. Release Node.js LTS yang paling
baru bisa menjadi pilihan yang ideal dan direkomendasikan. Karena kamu akan menemui banyak masalah dengan versi yang lebih lama seperti 
beberapa fungsionalitas yang hilang atau beberapa package terkait (dependency) yang belum didukung di versi lama.

Pertama, Install webpack dengan menggunakan `npm` (node package manager) dan ketersediaan package dibuat lokal. **Ingat perbedaan ada dua 
jenis ketersediaan package** setelah diinstall yaitu: **lokal** dan **global**. Opsi `--save-dev` membuat ketersediaan package menjadi lokal dan 
sebaliknya opsi `-g` membuat ketersediaan package menjadi global. 

```sh
mkdir bebas-aja-deh
cd bebas-aja-deh/
npm init
```

Output `npm init` . Dari output dibawah, yang wajib diisi adalah isian `name` saja. Pertanyaan isian-isian yang selanjutnya cukup kamu 
enter, enter, enter saja (biarkan kosong).

```sh
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (lalala) ossomm-webpack-oprek
version: (1.0.0)
description:
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
About to write to D:\REACT PROJECT\bebas-aja-deh\package.json:

{
  "name": "ossomm-webpack-oprek",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this ok? (yes)

```

Setelah itu, lalu lanjutkan menjalankan proses instalasi webpack seperti berikut dibawah ini:

```
npm install webpack --save-dev
```

Cara menjalankan package dengan ketersediaan lokal adalah sebagai berikut: `node_modules/.bin/webpack` .

## Sekilas cara pemakaian
Supaya tidak penasaran, setelah instalasi, bagaimana cara memakainya?

### Bundling dari coding yang menggunakan keyword `import`
Buat direktori baru bernama `src` atau `app` atau `apa-aja-deh` di dalam direktori utama project kita yang mungkin kamu beri nama 
`bebas-aja-deh` sehingga menjadi `bebas-aja-deh/src`. 

Buatlah direktori bernama `test1` di dalam direktori `src` sehingga menjadi `src/test1`. Lalu di dalam direktori `src/test1` buatlah 4
file javascript berikut: 

#### `abc.js`
```js
export default function abc()
{
    console.log("ABC")
}
```

#### `def.js`
```js
export default function def()
{
    console.log("DEF")
}
```

#### `ghi.js`
```js
export default function ghi()
{
    console.log("GHI")
}
```

#### `utama.js`
```js
import abc from "./abc"
import def from "./def"
import ghi from "./ghi"

abc()
def()
ghi()
```

Lalu buatlah file bernama `webpack.config.js` di direktori utama project contoh: `bebas-aja-deh/webpack.config.js` dengan isinya seperti
berikut dibawah ini:

```js
const path = require("path")
const webpack= require("webpack")

module.exports = {
    context: path.resolve(__dirname, "./src"),
    entry: "./test1/utama.js",
    output: {
        path: path.resolve(__dirname, "./dist"),
        filename: "bundle-test1.js"
    }
}
```

Selanjutnya, jalankan webpack untuk memulai proses bundling dengan menjalankan perintah 

```sh
node_modules/.bin/webpack
```

Untuk melihat hasilnya, silahkan check di `bebas-aja-deh/dist` terbentuk file bernama `bundle-test1.js` dan direktori `dist` dibuat 
otomatis oleh webpack. Dan kamu bisa coba untuk menjalankan `bundle-test1.js` dengan perintah `node dist/bundle-test1.js`. Dan mari 
lanjut ke contoh selanjutnya.

### Bundling dari `abc.js`, `def.js`, `ghi.js` menjadi `bundle-test2.js`
Mirip seperti pada contoh pemakaian pertama, kamu tinggal buat direktori `test2` di dalam direktori `src`. Lalu, kamu copy 3 file 
yang sudah pernah kamu buat sebelumnya yaitu: `abc.js`, `def.js`, `ghi.js` ke direktori `src/test2`.

Backup file `webpack.config.js` yang di praktek pertama lalu buatlah file `webpack.config.js` yang baru di lokasi yg sama seperti di 
praktek pertama dengan isinya seperti berikut dibawah ini:

```js
const path = require("path")
const webpack= require("webpack")

module.exports = {
    context: path.resolve(__dirname, "./src"),
    entry: {
        bebasapp: [
            "./test2/abc.js",
            "./test2/def.js",
            "./test2/ghi.js"
        ]
    },
    output: {
        path: path.resolve(__dirname, "./dist"),
        filename: "bundle-test2.js"
    }
}
```

Selanjutnya, jalankan webpack untuk memulai proses bundling dengan menjalankan perintah 

```sh
node_modules/.bin/webpack
```

## Konsep dasar Webpack
Webpack adalah sebuah pembundel (bundler) untuk aplikasi-aplikasi javascript. Tentunya kamu tidak asing / pastinya tahu apa 
itu pem-bundel-an (bundling). Sederhananya, asumsikan ada beberapa file javascript yaitu: `abc.js`, `def.js`, `ghi.js`. 3 file 
javascript itu akan digabung menjadi satu file bundle dengan nama `alphabet.js`. 

Tentunya webpack tidak hanya menyediakan / mampu melakukan hal sederhana itu saja. Webpack memiliki kemampuan melakukan pemrosesan-
pemrosesan untuk sebuah file sebelum file tersebut digabungkan ke file bundle nya. Pemrosesan-pemrosesan itu bisa berupa proses compile, 
transformasi, minifikasi, uglify, dan lain-lain sesuai kebutuhan. 

//
