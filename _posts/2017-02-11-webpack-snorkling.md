---
layout: post
title: Webpack Snorkling
---

Daftar Isi
==========

* [Pengantar](#pengantar)
* [Instalasi webpack](#instalasi-webpack)
* [Sekilas cara pemakaian](#sekilas-cara-pemakaian)
    * [1. Bundling dari coding yang menggunakan keyword import](#1-bundling-dari-coding-yang-menggunakan-keyword-import)
    * [2. Bundling dari banyak file terpisah-pisah menjadi bundle-test2.js](#2-bundling-dari-banyak-file-terpisah-pisah-menjadi-bundle-test2-js)
* [Konsep dasar webpack](#konsep-dasar-webpack)
    * [Context dan Entry](#context-dan-entry)
        * [Context](#context)
        * [Entry](#entry)
    * [Output](#output)
    * [Loaders](#loaders)
    * [Plugins](#plugins)
* [Zoom-IN sedikit untuk Entry - Output, dan Loaders - Plugins](#zoom-in-sedikit-untuk-entry-output-dan-loaders-plugins)
    * [Entry - Output Zoom-IN](#entry-output-zoom-in)
    * [Loaders - Plugins Zoom-IN](#loaders-plugins-zoom-in)
        * [Studi Kasus - Men-transpile (transformation compile) LESS menjadi CSS](#studi-kasus-men-transpile-transformation-compile-less-menjadi-css)
        * [Studi Kasus - Meminifikasi hasil bundling](#studi-kasus-meminifikasi-hasil-bundling)
* [Kesimpulan](#kesimpulan)

## Pengantar
Kata "Snorkling" disini saya pakai sebagai kiasan yang berarti mempelajari dan menggunakan webpack hanya sampai pada level dasar dan 
pemula atau cukup tahu permukaan-permukaan nya saja. Tulisan ini akan membahas cara instalasi webpack, sedikit konsep webpack, dan 
penerapan / implementasi dari konsep webpack. Semoga dengan adanya tulisan ini, saya pribadi dapat dengan mudah menerapkan webpack 
dimana saja karena semua tata cara instalasi, konfigurasi, dan penggunaan tercatat disini semua.

## Instalasi webpack
Sebelum memulai instalasi webpack, pastikan kamu sudah install versi paling fresh Node.js terlebih dahulu. Release Node.js LTS yang 
palingbaru bisa menjadi pilihan yang ideal dan direkomendasikan. Karena kamu akan menemui banyak masalah dengan versi yang lebih lama 
seperti beberapa fungsionalitas yang hilang atau beberapa package terkait (dependency) yang belum didukung di versi lama.

Pertama, Install webpack dengan menggunakan `npm` (node package manager) dan ketersediaan package dibuat lokal. **Ingat perbedaan ada dua 
jenis ketersediaan package** setelah diinstall yaitu: **lokal** dan **global**. Opsi `--save-dev` membuat ketersediaan package menjadi 
lokal dan sebaliknya opsi `-g` membuat ketersediaan package menjadi global. 

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

### 1. Bundling dari coding yang menggunakan keyword `import`
Buat direktori baru bernama `src` atau `app` atau `apa-aja-deh` di dalam direktori utama project kita yang mungkin kamu beri nama 
`bebas-aja-deh` sehingga menjadi `bebas-aja-deh/src`. 

Buatlah direktori bernama `test1` di dalam direktori `src` sehingga menjadi `src/test1`. Lalu di dalam direktori `src/test1` buatlah 4
file javascript berikut: 

`abc.js`

```js
export default function abc()
{
    console.log("ABC")
}
```

`def.js`

```js
export default function def()
{
    console.log("DEF")
}
```

`ghi.js`

```js
export default function ghi()
{
    console.log("GHI")
}
```

`utama.js`

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

### 2. Bundling dari banyak file terpisah-pisah menjadi `bundle-test2.js`
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

## Konsep dasar webpack
Webpack adalah sebuah pembundel (bundler) untuk aplikasi-aplikasi javascript modern. Tentunya kamu tidak asing / pastinya tahu apa 
itu pem-bundel-an (bundling). Sederhananya, asumsikan ada beberapa file javascript yaitu: `abc.js`, `def.js`, `ghi.js`. 3 file 
javascript itu akan digabung menjadi satu file bundle dengan nama `alphabet.js`. Selain menghandle bundling file-file javascript, 
webpack juga dapat membundling file-file css.

Tentunya webpack tidak hanya menyediakan / mampu melakukan hal sederhana itu saja. Webpack memiliki kemampuan melakukan pemrosesan-
pemrosesan untuk sebuah file sebelum file tersebut digabungkan ke file bundle nya. Pemrosesan-pemrosesan itu bisa berupa proses compile, 
transformasi, minifikasi, uglify, dan lain-lain sesuai kebutuhan. Contoh: mengcompile file-file `less`, atau `sass`, atau `scss` menjadi 
file css terlebih dahulu baru digabung menjadi satu file atau beberapa file. 

Ada 4 konsep dari webpack yang wajib kamu pahami, yaitu:

* Context dan Entry
* Output
* Loaders
* Plugins

### Context dan Entry

#### Context
Menentukan absolute path string ke direktori yang memuat file-file yang akan di-bundle. Memberitahu webpack untuk mulai dari mana 
mencari file-file yg akan diproses. 

#### Entry
Adalah sebuah configuration object yang menentukan file-file mana saja yang harus diproses.

### Output
Adalah sebuah configuration object yang terdiri dari: `output.path` dan `output.filename`. `output.path` yang menentukan absolute path 
string ke direktori mana output bundle file disimpan. `output.filename` yang menentukan nama output bundle file.

### Loaders
Loader dapat juga dilihat sebagai sebuah preprocess yaitu melakukan proses-proses lain *sebelum* masuk ke proses bundling. Contohnya 
melakukan proses compile `less`, `sass`, `scss` menjadi css. Contohnya lagi, men-transformasi atau sering dikenal sebagai transpiler 
(transformation compiler) file-file source code ES6 JSX menjadi javascript ES5 / ES4 sesuai target versi ES yg dibutuhkan.

### Plugins
Plugins adalah proses-proses yang terjadi pasca proses bundling. Contoh: asumsikan ada 2 yaitu file `page-a.less`, `page-b.less`. 2 file
tersebut sudah dicompile menjadi css di loaders, lalu dibundle dan hasil bundle masih tersimpan di memory (belum menjadi file fisik). 
Naahh plugins bertugas untuk memproses bundled files yg ada di buffer. Apa saja proses yg bisa dilakukan? Ada beberapa yaitu: 
Minifikasi, Uglify, dan lain-lain.

## Zoom-IN sedikit untuk Entry - Output, dan Loaders - Plugins
Kita perlu tahu sedikit saja lebih lanjut bagaimana pemanfaatan Entry - Output, Loaders - Plugins supaya kita bisa 
mengimplementasikan konsep-konsep dari webpack tersebut. Oiya, by the way kok *Context* nggak dimasukin untuk dibahas ya? Hemmm... 
menurut saya pribadi, Context sepertinya kerjaannya sudah cukup segitu aja. Saya sudah bolak-balik documentation dari webpack dan memang 
belum ada pembahasan lebih lanjut untuk pemanfaatan dari Context.

### Entry - Output Zoom-IN
Lho? Entry dan Output kan dua hal yang berbeda, kenapa penjelasannya dijadikan satu ? Hemmm...namanya juga snorkling, jadi saya bahas 
seperlunya saja dan yang sudah merupakan common-usage pada real development case. Jadi untuk uncommon usage nya kamu bisa baca-baca 
sendiri di https://webpack.js.org/configuration/output/ .

Mari lihat kembali contoh dasar pemanfaatan Entry - Output yang sudah dijelaskan sebelumnya diatas. 

```js
entry: "./test1/utama.js",
output: {
    path: path.resolve(__dirname, "./dist"),
    filename: "bundle-test1.js"
}
```

dan

```js
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
```

Nah, ada lagi variasi penggunaan dari Entry - Output. Apa saja? 

```js
entry: {
    app_satu: "./app1.js",
    app_dua: "./app2.js",
    app_tiga: "./app3.js"
},
output: {
    path: path.resolve(__dirname, "./dist"),
    filename: "[name].appbundle.js"
}
```

Code diatas akan menghasilkan 3 file bundle di direktori `dist/` seperti berikut: `dist/app_satu.appbundle.js`, 
`dist/app_dua.appbundle.js`, dan `dist/app_tiga.appbundle.js`.

```js
entry: {
    app_satu: ["./abc.js", "./def.js", "./ghi.js"],
    app_dua: ["./jkl.js", "mno.js", "./pqr.js"]
},
output: {
    path: path.resolve(__dirname, "./dist"),
    filename: "[name].appbundle.js"
}
```

Mirip seperti yg sebelumnya, code diatas akan menghasilkan 2 file bundle di direktori `dist/` yaitu: (1) `app_satu.appbundle.js` dimana 
merupakan bundle penggabungan dari `abc.js`, `def.js`, dan `ghi.js`. (2) `app_dua.appbundle.js` dimana merupakan bundle penggabungan 
dari `jkl.js`, `mno.js`, `pqr.js`.

### Loaders - Plugins Zoom-IN

#### Studi Kasus - Men-transpile (transformation compile) LESS menjadi CSS
Untuk pemanfaatan Loaders - Plugins, Langsung saja dengan contoh kasus yang real. Untuk dapat menjalankan contoh kasus ini, maka kamu 
perlu install beberapa library tambahan. Langsung saja diinstall seperti berikut:

```sh
npm install css-loader --save-dev
npm install https://github.com/webpack-contrib/extract-text-webpack-plugin/tarball/v2.0.0-rc.3 --save-dev
npm install less-loader less --save-dev
```

Pastikan semua library tambahan diatas terinstall dengan baik. Jika sudah terinstall, maka mari kita buat beberapa file LESS di struktur
direktori kita. 

Masih menggunakan direktori project kita yang sebelumnya, buatlah direktori bernama `less` di `bebas-aja-deh/src/` sehingga menjadi 
`bebas-aja-deh/src/less`. Buatlah beberapa file less di `bebas-aja-deh/src/less/` misalkan seperti berikut ini:

`lesscelana.less`

```css
@bloody: #D70F22;
@textWhite: #FFFFFF;
@base: 960px;

body {
    background: @bloody;
    color: @textWhite;
}

#main {
    .box {
        width: @base / 3;

        h3 {
            color: @textWhite;
        }
    }
}
```

silahkan berkreasi sendiri untuk beberap file less lainnya seperti: `celanagoloh.less` , dan `ngeless_wae_koe.less` dimana isinya 
terserah anda yg penting sesuai aturan LESS.

Setelah kamu selesai menyiapkan beberapa file LESS maka lanjutkan dengan membuat file `webpack.config.js` dengan isinya seperti berikut
ini:

```js
const path = require("path")
const webpack = require("webpack")
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
    context: path.resolve(__dirname, "./src"),
    entry: {
        cssone: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"],
        csstwo: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"],
        cssthree: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"]
    },
    output: {
        path: path.resolve(__dirname, "./dist/css-bundle"),
        filename: "[name].unused.please-delete.js"
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                use: ExtractTextPlugin.extract(["css-loader", "less-loader"])
            }
        ]
    },
    plugins: [
        new ExtractTextPlugin("[name].styles.css")
    ]
}
```

Lanjut jalankan proses bundling dengan perintah: `node_modules/.bin/webpack` Yaayyy! hasilnya kamu bisa lihat di `dist/css-bundle` ada 3
file CSS dimana masing-masing dari 3 file CSS itu merupakan penggabungan dari beberapa file. Cool! Do you have any idea with this?

#### Studi Kasus - Meminifikasi hasil bundling
Untuk dapat meminifikasi hasil bundling, maka kamu perlu install webpack plugin bernama `optimize-css-assets-webpack-plugin` dan seperti 
biasa install dengan cara `npm install optimize-css-assets-webpack-plugin --save-dev`. Dan ubah file `webpack.config.js` menjadi seperti
berikut dibawah ini:

```js
const path = require("path")
const webpack = require("webpack")
const ExtractTextPlugin = require("extract-text-webpack-plugin");
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
    context: path.resolve(__dirname, "./src"),
    entry: {
        cssone: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"],
        csstwo: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"],
        cssthree: ["./less/lesscelana.less", "./less/celanagoloh.less", "./less/ngeless_wae_koe.less"]
    },
    output: {
        path: path.resolve(__dirname, "./dist/css-bundle"),
        filename: "[name].unused.please-delete.js"
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                use: ExtractTextPlugin.extract(["css-loader", "less-loader"])
            }
        ]
    },
    plugins: [
        new ExtractTextPlugin("[name].styles.css"),
        new OptimizeCssAssetsPlugin({
            cssProcessor: require("cssnano"),
            cssProcessorOptions: { discardComments: {removeAll: true } },
            canPrint: true
        })
    ]
}
```

## Menyusun React Development Environment dengan Webpack 2, dan Babel

Berikut ini adalah cara menyusun / merangkai development environment untuk React yang paling minimal / basic. Kenapa kamu harus tahu 
bagaimana rangkaian paling minimal dari sebuah environment? Karena supaya environment yg kamu rangkai tidak bloated, dan tidak berisi
hal-hal yang tidak terpakai yg membuat environment kamu kotor dan membebani peformance. Berikut langkah demi langkah yang harus diikuti:

Buatlah direktori project yang baru bernama bebas apa saja sesuai kebutuhan kamu. Contoh: `MyOssommReact`. Dari command line masuk ke 
direktori project yang baru saja kamu buat itu lalu ketikkan perintah-perintah berikut:

```sh
npm init
npm install --save webpack webpack-dev-server
npm install --save babel-core babel-loader
npm install --save babel-preset-es2015
npm install --save react react-dom
npm install --save babel-preset-react
```

Setelah semua packages diatas terinstall, maka lanjutkan dengan membuat file `webpack.config.js` di dalam direktori project kamu. Contoh
nama direktori kamu adalah `MyOssommReact` sehingga menjadi `MyOssommReact/webpack.config.js`. `Berikut isi file webpack.config.js`:

```js
const path = require('path')
const webpack = require('webpack')

module.exports = {
    context: path.resolve(__dirname, './src'),
    entry: ['./app.js'],
    output: {
        path: path.resolve(__dirname, './public'),
        filename: 'bundle-dist.js',
        publicPath: '/public'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                query: {
                    presets: ['es2015', 'react']
                }
            }
        ]
    },
    devServer: {
        contentBase: path.resolve(__dirname, './public'),
        publicPath: '/public'
    }
}
```

Dari susunan konfigurasi webpack diatas, maka diasumsikan struktur direktori project sudah seperti berikut:

```
MyOssommReact/
    src/
        app.js
    public/
        index.html
    webpack.config.js
```

Jika struktur direktori project kamu belum seperti itu, maka buatlah segera seperti itu. Jika struktur direktori kamu sudah seperti itu,
maka berikut adalah file-file yang harus kamu tulis:

`public/index.html`

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Awesome React</title>
        <meta charset="utf-8">
    </head>
    <body>
        <div id="react-app"></div>
        <script type="text/javascript" src="/public/bundle-dist.js" charset="utf-8"></script>
    </body>
</html>
```

`src/app.js`

```js
import React from 'react'
import { render } from 'react-dom'

const DescriptionOssomm = () => (
    <p>Hey boss, friends, and ex. You are not as ossomm as my app!</p>
)

const WhateverLah = ({propsAsParamLalalaApaAja}) => {
    return (
        <p>I am {propsAsParamLalalaApaAja} at all</p>
    )
}

class App extends React.Component
{
    render()
    {
        return (
            <div>
                <h1>This is my ossomm app!</h1>
                <DescriptionOssomm />
                <WhateverLah propsAsParamLalalaApaAja={"too handsome"} />
            </div>
        )
    }
}

render(
    <App />,
    document.getElementById('react-app')
)
```

Setelah semuaaaaa langkah-langkah diatas kamu lakukan dan semuaaaa file-file yang dibutuhkan sudah kamu buat, maka kamu bisa jalankan 
perintah berikut: 

```sh
node_modules/.bin/webpack-dev-server
```

Lalu akses app kamu di browser dengan alamat http://localhost:8080 .

## Kesimpulan
Pekerjaan memilah-milah file assets bukan merupakan pekerjaan yang ringan karena banyak file yang harus kita pilah. Webpack dapat 
membantu kita memilah-milah file assets dengan mudah dan cepat.

Baik, berakhir sudah tulisan tentang webpack ini. Semoga tulisan ini bermanfaat bagi saya sendiri, karena jika saya lupa bagaimana 
implementasi webpack, maka saya tinggal buka tulisan ini saja hehehe.
