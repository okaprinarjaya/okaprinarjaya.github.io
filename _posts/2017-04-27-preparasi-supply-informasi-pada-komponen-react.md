---
layout: post
title: Preparasi supply informasi pada komponen React
---

## Pengantar

Setiap komponen pada suatu aplikasi berbasis React tentunya ketergantungan dengan berbagai jenis informasi pada saat komponen itu mulai 
diaktifkan (mounted). Informasi-informasi itu dapat berupa informasi wording, informasi styling, informasi bahasa, dan informasi daftar 
menu. Dan bahkan informasi yang berupa function yang nantinya bisa dipanggil dari komponen yang menerima supply informasi.

Biasanya informasi-informasi itu disimpan sebagai konfigurasi yang disimpan dalam sebuah **file konfigurasi** atau **object state** aplikasi React 
kita. Nah, Informasi-informasi itu akan disiapkan (preparasi) dan di-supply ke sebuah component yang membutuhkannya melalui function-
function yang kita definisikan sendiri. Function-function yang melakukan preparasi itu akan menjadi function yang bersifat umum (reusable), 
yang bisa digunakan untuk preparasi supply informasi ke semua komponen, tidak hanya komponen tertentu saja. 

## Dua Cara untuk supply informasi ke komponen

Ada 2 cara untuk men-supply informasi ke sebuah komponen. Dimana kedua-duanya dari cara itu pada akhirnya hanyalah akan menghasilkan 
object **[props](https://facebook.github.io/react/docs/components-and-props.html)**. Berikut adalah 2 cara tersebut yaitu:

1. Passing attribute di pemanggilan komponen sebagai JSX
2. Menggunakan function `connect()` dari library `react-redux`

### Passing attribute di pemanggilan komponen sebagai JSX

Yang dimaksud dari cara passing attribute di pemanggilan komponen sebagai JSX adalah terlihat seperti contoh code dibawah berikut ini:

#### `JustExample.js`

```js
import React, { Component } from 'react'
import AnotherComExample from './AnotherComExample'
import aConfig from './config/aConfig'
import bConfig from './config/bConfig'
import cConfig from './config/cConfig'

class JustExample extends Component {
  
  render() {
    const anObj = {
      objAttrA: "Object attribute A",
      objAttrB: "Object attribute B"
    }
    
    return (
      <div>
        <AnotherComExample attr1={aConfig} attr2={bConfig} attr3={cConfig} attr4="Oka Prinarjaya" attr5="100" />
      </div>
    )
  }

}
```

Attribute-attribute dari JSX `<AnotherComExample />` yaitu: `attr1`, `attr2`, `attr3`, `attr4`, `attr5` akan menjadi object **props** di 
class komponen `AnotherComExample`.

### Menggunakan function `connect()` dari library `react-redux`

Mensupply informasi dengan menggunakan function `connect()` **adalah jika informasi-informasi yang akan disupply adalah bersumber / 
diambil dari object state**. Codenya terlihat seperti code berikut dibawah ini:

#### `PuzzleAppContainer.js`

```js
import { connect } from "react-redux"
import { setOperation } from "../actions/actionCreators"
import PuzzleApp from "../components/PuzzleApp"

const mapStateToProps = (state) => {
    return {
      operation: state.operation,
      channel: state.channel,
      favoriteMusic: state.favoriteMusic
    }
}

const mapDispatchToProps = (dispatch) => {
    return {
        onChangeOperation: (name) => dispatch(setOperation(name))
    }
}

const PuzzleAppContainer = connect(
    mapStateToProps,
    mapDispatchToProps
)(PuzzleApp)

export default PuzzleAppContainer
```

Pada contoh code diatas, informasi-informasi di-supply ke class komponen `PuzzleApp` melalui pembuatan variabel `PuzzleAppContainer` 
dan `PuzzleAppContainer` hanyalah sebuah object yang memuat instance class komponen `PuzzleApp`.

Lalu mana yg akan dipanggil di dalam fungsi `render()` ? Jawabannya, tentunya `PuzzleAppContainer` yang akan dipanggil di dalam 
`render()`. **TAPI** yang dirender pada akhirnya adalah `PuzzleApp` karena `PuzzleAppContainer` yang diciptakan melalui penggunaan 
function `connect()` hanyalah proses supply informasi ke class komponen `PuzzleApp`. Berikut contoh konsumsi informasinya di 
`PuzzleApp.js`

#### `PuzzleApp.js`

```js
import React from 'react'

const PuzzleApp = (props) => {
  return (
    <div>
      <button onClick={e => props.onChangeOperation("substract")}></button>
      <ul>
        <li>{props.operation}</li>
        <li>{props.channel}</li>
        <li>{props.favoriteMusic}</li>
      </ul>
    </div>
  )
}

export default PuzzleApp
```

## Pola preparasi supply informasi

//
