---
layout: post
title: Preparasi supply informasi pada komponen React
---

## Pengantar

Setiap komponen pada suatu aplikasi berbasis React tentunya ketergantungan dengan berbagai jenis informasi pada saat komponen itu mulai 
diaktifkan (mounted). Informasi-informasi itu dapat berupa informasi wording, informasi styling, informasi bahasa, dan informasi daftar 
menu. Dan bahkan informasi yang berupa function yang nantinya bisa dipanggil dari komponen yang menerima supply informasi.
<!--more-->

Biasanya informasi-informasi itu disimpan sebagai konfigurasi yang disimpan dalam sebuah **file konfigurasi** atau **object state** aplikasi React kita. 
Nah, Informasi-informasi itu akan disiapkan (preparasi) dan di-supply ke sebuah component yang membutuhkannya melalui function-
function yang kita definisikan sendiri. Function-function yang melakukan preparasi itu akan menjadi function yang bersifat umum 
(reusable), yang bisa digunakan untuk preparasi supply informasi ke semua komponen, tidak hanya komponen tertentu saja. 

## Dua Cara untuk supply informasi ke komponen

Ada 2 cara untuk men-supply informasi ke sebuah komponen. Dimana kedua-duanya dari cara itu pada akhirnya hanyalah akan menghasilkan 
object **[props](https://facebook.github.io/react/docs/components-and-props.html)**. Berikut adalah 2 cara tersebut yaitu:

1. **Passing attribute di pemanggilan komponen sebagai JSX**
2. **Menggunakan function `connect()` dari library `react-redux`**

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
|__ public/
|__ src/
   |__ actions/
   |__ reducers.js
   |__ store.js
   |__ app.js
   |__ common/
      |__ componentPreparatory.js
      |__ config/
         |__ config.menubar.json
         |__ config.constants.json
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

Pastikan library `redux` dan `react-redux` sudah terinstall di project kamu. Dengan menggunakan struktur direktori diatas, maka 
selanjutnya kita siapkan Function-function umum untuk preparasi supply informasi. Berikut code nya:

### `APP/src/common/componentPreparatory.js`

```js
import React from 'react'
import { connect } from 'react-redux'
import { changeTheName, changeTheEmail } from '../actions/creators'

export const supplyConfigMenubar = (configMenubar, TheComponent) => (props) => {
  return (
    <TheComponent {...props} menubar={configMenubar} />
  )
}

export const supplyConfigConstants = (configConstants, TheComponent) => (props) => {
  return (
    <TheComponent {...props} constants={configConstants} />
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
        changeTheNameHandler: (theParam) => dispatch(changeTheName(theParam)),
        changeTheEmailHandler: (theParam) => dispatch(changeTheEmail(theParam))
      }
    }
  )(TheComponent)
}
```

### Contoh praktek penggunaan function-function preparasi supply informasi

Untuk dapat menampilkan contoh penggunaan function-function preparasi supply informasi, maka kita perlu dibuat sebuah display component
sesuai dengan struktur direktori diatas. Berikut:

#### `APP/src/components/Menubar/Menubar.js`

```js
import React, { Component } from 'react'

class Menubar extends Component {
  changeTheNameHandler() {
    const listOfNames = [
      'Bobby KooL',
      'JrX',
      'Eka Rock',
      'Mike Ness',
      'Jim Root',
      'Mike Thompson',
      'Oka Prinarjaya'
    ]

    this.props.changeTheNameHandler(listOfNames[Math.floor(Math.random() * listOfNames.length)])
  }

  render() {
    const menuItems = this.props.menubar.menuItems.map((item) => (
      <li key={item.id}>{item.title}</li>
    ))

    return (
      <div style={{border: "1px red solid", padding: "10px", marginBottom: "15px"}}>
        <h2>I am Menubar</h2>

        <ul>
          {menuItems}
        </ul>

        <ul>
          <li>{`ENABLE_LALALA = ${this.props.constants.ENABLE_LALALA}`}</li>
          <li>{`ENABLE_BLABLABLA = ${this.props.constants.ENABLE_BLABLABLA}`}</li>
          <li>{`FETCH_DATA_ASYNC = ${this.props.constants.FETCH_DATA_ASYNC}`}</li>
        </ul>

        <p>{`Now ${this.props.theName} is with You...`}</p>
        <button type="button" onClick={() => this.changeTheNameHandler()}>ROLL baby ROLLLL..!</button>
      </div>
    )
  }
}

export default Menubar
```

Dapat dilihat pada source code diatas, kita mengakses semuaaaaaa informasi-informasi yang di-supply melalui object `props`. Lalu 
bagaimana men-supply informasi-informasi itu? Berikut ini caranya:

#### `APP/src/components/Menubar/index.js`

```js
import Menubar from './Menubar'
import {
  supplyConfigMenubar,
  supplyTheNameAttrStateObj,
  supplyCommonActionDispatchMethod,
  supplyConfigConstants
} from '../../common/componentPreparatory'

import configMenubar from '../../common/config/config.menubar'
import configConstants from '../../common/config/config.constants'

export default supplyConfigConstants(
  configConstants,
  supplyCommonActionDispatchMethod(
    supplyTheNameAttrStateObj(
      supplyConfigMenubar(configMenubar, Menubar)
    )
  )
)
```

Dari code `index.js` diatas, apakah kamu bingung membaca dan mengerti potongan code diatas yang seperti berikut ini?

```js
export default supplyConfigConstants(
  configConstants,
  supplyCommonActionDispatchMethod(
    supplyTheNameAttrStateObj(
      supplyConfigMenubar(configMenubar, Menubar)
    )
  )
)
```

Agar code diatas lebih mudah dibaca dan dimengerti, maka kamu bisa menulisnya seperti berikut ini:

```js
let MenubarPrepare = supplyConfigConstants(configConstants, Menubar)
MenubarPrepare = supplyCommonActionDispatchMethod(MenubarPrepare)
MenubarPrepare = supplyTheNameAttrStateObj(MenubarPrepare)
MenubarPrepare = supplyConfigMenubar(configMenubar, MenubarPrepare)

export default MenubarPrepare
```

Dan, bagaimana component `Menubar` dipanggil untuk dijalankan? Component `Menubar` dipanggil dengan cara seperti ini: 
`import Menubar from './components/Menubar'`. Karena ada file `index.js` di dalam direktori `Menubar/` maka perintah `import` mirip 
dengan tingkah laku sebuah webserver dalam membaca direktori document root nya. Web server selalu akan mencari file `index.*` sebagai 
default file yang direquest. Maka perintah `import Menubar from './components/Menubar'` sama saja atau bisa juga ditulis seperti ini: 
`import Menubar from './components/Menubar/index.js'`

Mari kita lihat code berikut dibawah ini untuk mengetahui contoh lengkapnya pemanggilan component `Menubar`

#### `APP/src/app.js`

```js
import React, { Component } from 'react'
import { render } from 'react-dom'
import { Provider } from "react-redux"

import store from './store'
import Menubar from './components/Menubar'
import NeedNameAndConstants from './components/NeedNameAndConstants'

const HoldAllComs = () => (
  <div>
    <Menubar />
    <NeedNameAndConstants />
  </div>
)

class App extends Component {
    render()
    {
        return (
          <Provider store={store}>
            <HoldAllComs />
          </Provider>
        )
    }
}

render(
    <App />,
    document.getElementById('react-app')
)
```

## Download source code

Silahkan download / clone source code lengkap dari contoh aplikasi yang dibuat di tulisan ini di 
[https://github.com/okaprinarjaya/react-comprep](https://github.com/okaprinarjaya/react-comprep)

