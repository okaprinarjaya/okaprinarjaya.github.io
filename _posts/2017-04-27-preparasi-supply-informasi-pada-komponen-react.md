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

Pada contoh code diatas, informasi-informasi di-supply ke class komponen `PuzzleApp` melalui pembuatan variabel `PuzzleAppContainer`. 
Dan, `PuzzleAppContainer` hanyalah sebuah object yang memuat instance class komponen `PuzzleApp`.

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

## Implementasi pola preparasi supply informasi

Di bagian tulisan ini, mari kita mulai mempraktekkan preparasi supply informasi dengan membuat struktur direktori aplikasi yang sesuai 
yang dapat memanfaatkan function-function umum yang nanti akan kita definisikan. Dan berikut struktur direktori dari aplikasi kita:

```text
APP/
|__ src/
   |__ actions/
   |__ reducers/
   |__ common/
      |__ componentPreparatory.js
      |__ config/
         |__ config.menubar.json
         |__ config.constants.json
         |__ config.etc.json
   |__ components/
      |__ ComponentOne/
         |__ ComponentOne.js
         |__ index.js
      |__ ComponentTwo/
         |__ ComponentTwo.js
         |__ index.js
      |__ ComponentThree/
         |__ ComponentThree.js
         |__ index.js
      |__ ComponentEtc/
         |__ ComponentEtc.js
         |__ index.js
```

Dapat dilihat dari struktur direktori diatas, komponen yang bersifat / berjenis display-component dibuatkan dan ditempatkan di direktori 
baru sesuai nama komponennya (`ComponentOne/`, `ComponentTwo/`, `ComponentThree/`, `ComponentEtc/`).

Pastikan library `redux` dan `react-redux` sudah terinstall di project kamu. Dengan menggunakan struktur direktori diatas, maka berikut 
ini adalah hal-hal yang perlu dibuat:

1. **Function-function umum untuk preparasi supply informasi**
2. **Hal-hal Redux**
3. **Dua display component**
4. **Tampilkan dua component itu**

### Function-function umum untuk preparasi supply informasi

Pada struktur direktori yang telah dirancang diatas, function-function umum untuk preparasi supply informasi ditaruh di 
`APP/src/common/componentPreparatory.js`. Berikut listing code nya: 

#### `APP/src/common/componentPreparatory.js`

```js
import React from 'react'
import { connect } from 'react-redux'
import { commonDispatchMethod } from './actions/actionCreators'

export const supplyConfigMenubar = (configMenubar, TheComponent) => (props) => {
  return (
    <TheComponent {...props} configMenubar={configMenubar} />
  )
}

export const supplyTheNameAttrStateObj = (TheComponent) => {
  return connect(
    (state) => {
      return {
        theName: state.theName
      }
    }
  )(TheComponent)
}

export const supplyCommonActionDispatchMethod = (TheComponent) => {
  return connect(
    (state) => {
      return {}
    },
    (dispatch) => {
      return {
        commonDispatchMethodHandler: (theParam) => dispatch(commonDispatchMethod(theParam))
      }
    }
  )(TheComponent)
}
```

//

