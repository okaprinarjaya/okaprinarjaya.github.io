---
layout: post
title: Membuat react web apps sederhana yang isomorphic
---

## Apa itu javascript web apps yang isomorphic?

Javascript web apps yang isomorphic adalah suatu web apps berbasis javascript yang selain bisa berjalan di sisi 
client (browser) juga bisa berjalan di sisi server. Semua proses dapat dieksekusi di sisi server atau client. 
Lalu, Apa yang menyebabkan Isomorphic yang merupakan sebuah kata sifat ini dijadikan sebagai sifat baru dari 
sebuah javascript web apps? 

NodeJs. Ya, Sejak lahirnya teknologi yang bernama [NodeJs](https://nodejs.org/en/) javascript mulai bisa 
dijalankan di sisi server. Dengan mengkombinasikan salah satu platform javascript UI framework dengan NodeJs, 
maka kita dapat mewujudkan isomorphic web apps. Contoh, mengkombinasikan ReactJs dengan NodeJs, atau AngularJs 
dengan NodeJs.
<!--more-->

## Implementasi isomorphic React app secara cepat dan sekedar jadi (quick and dirty)

Supaya kamu cepat dapat bayangan implementasinya maka mari kita membuat react app yang amat sangat super 
sederhana secara cepat dan sekedarnya. Ikut langkah-langkah berikut ini:

### 1. Install semua dependency

Saya rasa saya tidak perlu lagi menyampaikan cara bagaimana meng-inisiasi react app directory. Langsung saja saya 
sampaikan library-library apa saja yang perlu di-install. Berikut:

```
npm install express react react-dom babel-core babel-loader babel-register babel-preset-es2015 babel-preset-react babel-preset-stage-2
npm install webpack
```

### 2. Tulis code

#### Kode untuk sisi server
---

#### `.babelrc`

```json
{
  "presets": [
    "react",
    "es2015",
    "stage-2"
  ]
}

```

#### `render-webserver.js`

```javascript
require('babel-register')({
  ignore: /\/(build|node_modules)\//
});

require('./appserve');
```

#### `appserve.js`

```javascript
import React from 'react';
import {renderToString} from 'react-dom/server';
import express from 'express';

import App from './App';

const app = express();

app.use(express.static('public'));

app.get('/', (request, response) => {
  const uiRendered = renderToString(<App/>);

  var HTML = '<!DOCTYPE html>';
  HTML += '<html>';
  HTML += '<head>';
  HTML += '<title>My Super simple isomorphic react app</title>';
  HTML += '<meta charset="utf-8">';
  HTML += '</head>';
  HTML += '<body>';
  HTML += `<div id="react-app">${uiRendered}</div>`;
  HTML += '<script type="text/javascript" src="/bundle-app.js" charset="utf-8"></script>';
  HTML += '</body>';
  HTML += '</html>';

  response.send(HTML);
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log('http://localhost:' + PORT);
});
```

#### Kode untuk sisi client (hal-hal terkait webpack)
---

#### `webpack.config.js`

```javascript
const path = require('path');

const APP_BASE = path.normalize(path.join(__dirname, '.'));
const BUILD_DIR = path.join(APP_BASE, 'public');

module.exports = {
  entry: './App',
  output: {
    path: BUILD_DIR,
    filename: 'bundle-app.js'
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react', 'stage-2']
        }
      }
    ]
  }
};
```

#### Kode yang untuk kedua sisi (sisi server dan client)
---

#### `App.js`

```javascript
import React, {Component} from 'react';
import ReactDOM from 'react-dom';

class App extends Component {
  handleClick() {
    alert('Haaalloowwwww');
  }

  render() {
    return (
      <div>
        <p>App component</p>
        <button type="button" onClick={this.handleClick}>Click me!</button>
      </div>
    );
  }
}

export default App;

if (typeof window !== 'undefined' && window.document && window.document.createElement) {
  window.onload = () => {
    ReactDOM.render(<App/>, document.getElementById('react-app'));
  }
}
```

#### Kode untuk pembuktian suatu react app tidak bersifat isomorphic
---

Tulis lalu simpan kode HTML script ini di root directory dengan nama`app.html`. Tentunya ini juga kode untuk 
sisi client. Berikut:

```
<!DOCTYPE html>
<html>
    <head>
        <title>My Super simple NOT isomorphic react app</title>
        <meta charset="utf-8">
    </head>
    <body>
        <div id="react-app"></div>
        <script type="text/javascript" src="public/bundle-app.js" charset="utf-8"></script>
    </body>
</html>
```

Jalankan dengan perintah-perintah berikut:

```text
// Jika di Unix like environment (GNU/Linux, Macintosh)
node_modules/.bin/webpack

// Jika di windows
node_modules\.bin\webpack

node render-webserver.js
```

Akses react app kamu di `http://localhost:3000`

Bagaimana? sudah terbayang kasarnya (draft)? Perlu kamu ketahui, perintah `node_modules/.bin/webpack` menghasilkan
file bundle javascript yang ditempatkan di `public/` bernama `bundle-app.js`. 
File javascript bundle `bundle-app.js` inilah yang meng-handle proses rendering UI di sisi client.

Dan, perintah `node render-webserver.js` mengaktifkan webserver yang bertugas menerima request HTTP dan merender 
UI di sisi server layaknya aplikasi web berbasis PHP Wordpress, dan lain-lain. Rendering UI di sisi server ini
diwujudkan oleh teknologi Nodejs dengan framework bernama `express`.

Untuk membuktikan suatu react app **SUDAH bersifat isomorphic** adalah dengan melakukan *View source* pada browser.
Klik kanan lalu pilih `View page source` atau tekan kombinasi tombol `CTRL + U`. Buktinya adalah, kamu dapat
melihat element `<div id="react-app"></div>` di dalamnnya **LANGSUNG** terisi element-element yang seperti 
tertulis di component `App.js`. Dan, tombol **Click me!** berfungsi dengan baik.

Lalu, untuk membuktikan bahwa suatu react app **TIDAK bersifat isomorphic**, maka kamu bisa langsung test dengan
cara membuka langsung file `app.html`. Lakukan view page source, maka kamu melihat element
`<div id="react-app"></div>` di dalamnya **TIDAK** terisi element-element apapun. Kenapa?, Karena element-element
didalamnya dirender di sisi client / browser. Walaupun buttom **Click me!** berfungsi dengan baik.

Nah, semoga kamu dapat memahami dan punya bayangan bagaimana itu react app yang isomorphic. Mari kita lanjut
ke pembuatan react app yang isomorphic yang lebih sedikit serius agar dapat sebagai fondasi membangun react app
yang skala besar. Untuk itu diperlukan penyusunan aplikasi yg berpola cukup baik dan dengan tambahan penggunaan
tools dan library pendukung yang lain. 

## Mulai membangun Isomorphic React web apps yang sedikit serius

Untuk mulai membangun React web apps yang isomorphic yang sedikit serius dan dengan pola yang sedikit teratur 
juga, berbeda dengan implementasi quick and dirty tadi, maka berikut ini adalah kebutuhan minimal untuk 
library-library apa saja yang harus diinstall diluar dari library-library yg berfungsi sebagai transpiler dan 
bundler. Berikut:

**Kebutuhan Wajib**
1. react
2. react-dom
3. react-router
4. express

`react`, dan `react-dom` tentunya adalah library inti untuk membangun web apps berbasis React (belum isomorphic). 
Dan library-library yang membuat suatu react web apps menjadi bersifat isomorphic adalah: `express`, 
`react-router`, method `react-dom/server/renderToString()`

**Kebutuhan Opsional**
1. ejs
2. webpack-isomorphic-tools

Pada kebutuhan opsional ini, library `ejs` adalah untuk templating, menempelkan hasil `renderToString()` ke 
layout view (bukan component). Dan, `webpack-isomorphic-tools` adalah library untuk membantu kita mengkonsumsi 
output assets dari webpack secara mudah. Karena output nama file dari assets yang diproses di webpack adalah 
sangat dinamis terutama saat kita memanfaatkan fitur [Code Splitting](https://webpack.js.org/guides/code-splitting/)

### Bagaimana server side rendering bekerja?

```
  1                      2                               3
                         +---------------+               +------------------------+
  client                 |               |               |                        |
  HTTP Request           | express       |  url path     |                        |
  GET /galleries         | app router    |  /galleries   |     React Router       |
+--------------------->  |               | +-----------> |                        |
                         |               |               | if match() then        |
                         +---------------+               | +--------------------+ |
                                                         | |                    | |
                                                         | |                    | |
                                                         | | react-dom/server/  | |
                                                         | | renderToString()   | |
                                                         | |                    | |
                                                         | |                    | |
                                                         | |                    | |
                                                         | |                    | |
                                                         | +--------------------+ |
                                                         |                        |
                                                         +------------------------+

```

Dapat kamu lihat pada ascii diagram diatas, ada dua kali proses passing informasi url path ke dua system router 
yg berbeda. Yang pertama informasi url path yg datang dari client request melalui browser dimana url path itu 
di-passing ke express app router. Selanjutnya yang kedua, dari express app router informasi url path di-passing 
ke react router dimana sebagai informasi untuk membuat keputusan component mana yang akan dirender.

### Mulai persiapan project dan instalasi semua packages yang dibutuhkan

Struktur direktori kita akan seperti berikut dibawah ini:

```
isomorphic-react
|_ src/
    |_ components/
      |_ com1
      |_ com2
      |_ comX
|_ assets
```

Dan, berikut ini adalah package-package library yang wajib kamu install terlebih dahulu yaitu:

1. Package-package library transpiler dan bundler
   * babel-core
   * babel-loader
   * babel-register
   * babel-preset-es2015
   * babel-preset-react
   * babel-preset-stage-2
   * babel-preset-react-hmre
   * webpack
   * webpack-dev-server
   * webpack-dev-middleware
   * webpack-hot-middleware

2. Package-package library react app
   * react
   * react-dom
   * react-router@^3.0.5
   * express

## Mulai coding!

Ok! Mari kita mulai menulis code untuk mewujudkan react web apps yg isomorphic. Code-code yang akan kita tulis 
dibagi menjadi beberapa bagian yaitu:

1. Webpack configuration
2. Code Fondasi isomorphic
3. Code Router
4. Code components

### Webpack configuration 
---

#### [APP/webpack.config.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/webpack.config.js)

### Code Fondasi isomorphic
---

#### 1. [APP/src/hot-bundles-webserver.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/src/hot-bundles-webserver.js)

Ini adalah express app yang berfungsi sebagai webserver yang melayani (serve) file bundle javascript, css, dan 
file chunking javascript dari react apps kita. Seringkali disebut dengan istilah *Hot Server*. 

Sekalinya react apps javascript bundles kita di-load dari server, maka selanjutnya SEMUA state / perubahan pada 
user interface akan berkoordinasi dengan file bundle javascript. Inilah yang mengimplementasikan konsep client 
side rendering dari sifat isomorphic.

#### 2. [APP/src/render-webserver.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/src/render-webserver.js) dan [APP/src/frontend.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/src/frontend.js)

Ini adalah express app yang berfungsi sebagai webserver yang melayani (serve) rendering aplikasi react kita 
menjadi plain text HTML string. Disinilah konsep server side rendering dari sifat isomorphic di-implementasikan. 
Di dua file inilah diagram diatas di-implementasikan. Yaitu diagram yang menggambarkan bagaimana server side 
rendering bekerja.

Jika kita membandingkan 2 source code ini dengan diagram diatas, maka pada `src/frontend.js` line: 17, adalah 
mengimplementasikan proses ke-2 pada diagram. Dan, pada `src/frontend.js` line: 18 s/d 42 mengimplementasikan 
proses ke-3 pada diagram. Dan, pada `src/render-webserver.js` line: 15 adalah membangkitkan / membuat proses 2 
dan 3 tersedia.

### Code Router
---

#### [APP/src/routes.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/src/routes.js)

Kode-kode pada file `routes.js` ini akan berperan sebagai penyedia informasi route yang mana informasi routes 
ini dikonsumsi oleh proses nomer 3 pada diagram diatas. Dan, sekaligus file ini `routes.js` ini juga dikonsumsi
oleh sisi client untuk client side rendering. Jadi, file ini berperan di dua sisi, server side dan client side.

### Code Components
---

#### [APP/src/components/AppRoutes.js](https://github.com/okaprinarjaya/react-isomorphic/blob/master/src/components/AppRoutes.js)

File `src/components/AppRoutes.js` ini HANYA berperan di sisi client saja. Kenapa? Dapat dilihat di file
`src/app-client.js`. Di dalam file `src/app-client.js` inilah `AppRoutes.js` dikonsumsi dan dirender hanya di sisi
client saja. Terlihat di line: 6 baris `ReactDOM.render(<AppRoutes />, document.getElementById('main'));` pada
file `src/app-client.js`.

Dan, selanjutnya semua file yang memuat code component-component dapat dilihat di direktori `src/components/`.
Dimana semua file components dikonsumsi di dua sisi (client side dan server side) kecuali file `AppRoutes.js`.

Baik, saya rasa cukup penjelasan code-code inti dari topik tulisan ini. Saya sarankan kamu untuk ketik ulang semua
code yang saya jelaskan. Agar dapat lebih terasa flow konsep nya. Dan, aplikasi react sederhana yang ada di
github repository `https://github.com/okaprinarjaya/react-isomorphic` sudah jadi dan dapat dijalankan pada
mesin kamu dengan mengikuti tata cara instalasinya. 

## Kenapa disarankan untuk isomorphic?

Ada 3 hal yang sangat mendasar dan penting kenapa disarankan untuk isomorphic, yaitu: 
1. Search Engine Optimization (SEO)
2. Rendering Performance
3. Server Performance

### Search Engine Optimization (SEO)

Pada kasus javascript web apps, sifat dan karakteristik dari javascript web apps adalah tidak meng-output-kan 
elemen-elemen HTML secara statis. Karena elemen-elemen HTML disusun di sisi client (browser) melalui interaksi 
dengan fungsi-fungsi manipulasi DOM (Document Object Model). Dan, DOM hanya ada di browser dan tidak akan pernah 
ada di server. 

Akan tetapi, kita sangat membutuhkan output elemen-elemen HTML yang statis yang sudah disusun di server lalu 
dikirimkan ke client (crawler) sebagai plain text data agar mesin-mesin pencari seperti Google, dan Bing dapat 
meng-crawl dan meng-index content website kita. Proses penyusunan semua elemen-elemen HTML di server dikenal 
dengan istilah Server Side Rendering.

### Rendering Performance

Untuk initial load sebuah halaman, maka kita perlu menjalankan javascript web apps kita di sisi server agar semua 
elemen-elemen HTML disusun di sisi server. Maka suatu halaman menjadi lebih cepat untuk ditampilkan pada phase 
initial load. Masalah umum dari client side rendering pada phase initial load sebuah halaman adalah efek "white 
page". Efek "white page" terjadi karena browser perlu menunggu file javascript selesai terdownload untuk 
melakukan render halaman melalui javascript. Bayangkan jika di web apps kamu ada API service call dan kamu 
melakukan client side rendering untuk initial load sebuah halaman.

### Server Performance

Pada web apps berbasis PHP, Ruby, Python, Java, .NET dan lain-lain yg sifatnya server side, selain memproses data 
dan bussiness logic, server juga diberikan beban untuk memproses tampilan / menyusun elemen-elemen HTML. Setiap 
ada perubahan state tampilan, maka keseluruhan tampilan akan diproses ulang dan keseluruhan data akan diproses 
ulang di server. Padahal kita tidak membutuhkan semua data untuk diproses ulang.

Oleh karena itu, javascript web apps yang isomorphic memberikan solusi untuk masalah ini. Javascript web apps 
yang isomorphic hanya membebani server untuk memproses tampilan hanya sekali saja saat initial load sebuah 
halaman. Saat halaman sudah dirender oleh browser, maka proses-proses selanjutnya akan dieksekusi di sisi 
browser. Setiap ada perubahan state tampilan oleh karena eksekusi sebuah proses, maka sudah tidak ada lagi 
pemrosesan tampilan dan keseluruhan data lagi di server. Hanya data yg diperlukan saja yang diproses dan 
dikirimkan ke browser lalu rendering perubahan state tampilan sepenuhnya ditangani oleh browser.

## Proses-proses apa saja yang bisa dilakukan di sisi server dan client?

Hampir semua proses dapat dilakukan di dua sisi (client & server). Berikut proses-proses umumnya yaitu:

1. Templating
2. Routing
3. Localization
4. Data transformation
5. API Interaction

Tetapi tetap masih ada beberapa proses yang tidak dapat dilakukan di sisi server ataupun di sisi client. Proses 
yang tidak dapat dilakukan di sisi server adalah berinteraksi dengan object-object browser seperti: `window`, dan 
`document`. Proses yang tidak dapat dilakukan di sisi client adalah berinteraksi dengan resources server seperti: 
filesystem, dan session.
