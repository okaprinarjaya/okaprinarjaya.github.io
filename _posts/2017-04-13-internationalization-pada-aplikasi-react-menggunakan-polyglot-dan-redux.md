---
layout: post
title: Internationalization pada aplikasi React menggunakan Polyglot dan Redux
---

## Pengantar

Fitur multi bahasa pada suatu website adalah sangat penting untuk menjangkau pengunjung internasional. Tulisan ini membahas bagaimana
mengimplementasikan fitur multi bahasa menggunakan paket library `node-polyglot` yang di-integrasikan dengan `redux-polyglot` dan 
`redux` yang di-integrasikan dengan `react-redux`. Perlu diperhatikan, kita menggunakan `node-polyglot` yang merupakan library 
penanganan multi bahasa yang dikembangkan oleh Airbnb. Karena ada juga beberapa library penanganan multi bahasa dengan nama yang sama.

Untuk persiapan project aplikasi React yang baru, saya menggunakan cara yang sudah saya tulis di tulisan saya sebelumnya yang bisa kamu
baca di http://okaprinarjaya.github.io/webpack-snorkling/#menyusun-react-development-environment-dengan-webpack-2-dan-babel .

## Implementasi

Install terlebih dahulu paket-paket library yang diperlukan sebagai berikut 

```sh
npm install --save redux react-redux node-polyglot redux-polyglot
```

Setelah paket-paket library yang dibutuhkan sudah berhasil terinstall semuanya, kita lanjutkan dengan membuat hal-hal berikut ini yaitu:

* **Display component**
* **Action types dan action creators, fungsi-fungsi reducers**
* **Container component**
* **Redux store dan polyglot middleware**
* **Melakukan translasi di display component**
* **Handler switch bahasa pada suatu button**

### Display component

Buatlah file dan source code berikut ini di `APP/src/components`. Berikut file-file source code yang langsung dapat kamu copy di
gist github:

* [MenuNavigation.js](https://gist.github.com/okaprinarjaya/018c6116e307e39c9464d8b7e44f17e2.js)
* [LanguageNavigation.js](https://gist.github.com/okaprinarjaya/b5bfd8630e5ab0f957c9e1400e6b17b2)
* [MainWrapper.js](https://gist.github.com/okaprinarjaya/6fc0d3e30c652274a04e59cce785753c)

Lalu lanjutkan dengan membuat 2 file berikut dibawah ini dengan lokasi file yg berbeda-beda. Berikut:

* [app.js](https://gist.github.com/okaprinarjaya/cc7907661c2f76246c30554cba2a4a16) Simpan di APP/src/app.js
* [index.html](https://gist.github.com/okaprinarjaya/0c6024ca151b9c895b202d0c99293b8c) Simpan di APP/public/index.html
* [styles.css](https://gist.github.com/okaprinarjaya/f7b29b97cef37095b2f2ceebbd51b71f) Simpan di APP/public/styles.css

Jika semua source code display component sudah dibuat, maka kamu sudah bisa mencoba menjalankannya untuk melihat bagaimana tampilannya.

### Action types dan action creators, dan reducers

//



