---
layout: post
title: Internationalization pada aplikasi React menggunakan Polyglot dan Redux
---

## Pengantar

Fitur multi bahasa pada suatu website adalah sangat penting untuk menjangkau pengunjung internasional. Tulisan ini membahas bagaimana
mengimplementasikan fitur multi bahasa menggunakan paket library `node-polyglot` yang di-integrasikan dengan `redux-polyglot` dan 
`redux` yang di-integrasikan dengan `react-redux`. Perlu diperhatikan, kita menggunakan `node-polyglot` yang merupakan library 
penanganan multi bahasa yang dikembangkan oleh Airbnb. Karena ada juga beberapa library penanganan multi bahasa dengan nama yang sama.
<!--more-->

Untuk persiapan project aplikasi React yang baru, saya menggunakan cara yang sudah saya tulis di tulisan saya sebelumnya yang bisa kamu
baca di suatu bagian tulisan dengan judul [Menyusun React Development Environment dengan Webpack 2 dan Babel](http://okaprinarjaya.github.io/webpack-snorkling/#menyusun-react-development-environment-dengan-webpack-2-dan-babel) .

## Implementasi

Install terlebih dahulu paket-paket library yang diperlukan sebagai berikut 

```sh
npm install --save redux react-redux node-polyglot redux-polyglot
```

Setelah paket-paket library yang dibutuhkan sudah berhasil terinstall semuanya, kita lanjutkan dengan membuat hal-hal berikut ini yaitu:

* **Display component**
* **Action types dan action creators, dan reducers**
* **Redux store, polyglot middleware dan mendefinisikan database bahasa**
* **Container component**
* **Melakukan translasi di display component**
* **Handler switch bahasa pada suatu button**

### Display component

Buatlah file dan source code berikut ini di `APP/src/components`. Berikut file-file source code yang langsung dapat kamu copy di
gist github:

* [MenuNavigation.js](https://gist.github.com/okaprinarjaya/018c6116e307e39c9464d8b7e44f17e2)
* [LanguageNavigation.js](https://gist.github.com/okaprinarjaya/b5bfd8630e5ab0f957c9e1400e6b17b2)
* [MainWrapper.js](https://gist.github.com/okaprinarjaya/6fc0d3e30c652274a04e59cce785753c)

Lalu lanjutkan dengan membuat 2 file berikut dibawah ini dengan lokasi file yg berbeda-beda. Berikut:

* [app.js](https://gist.github.com/okaprinarjaya/cc7907661c2f76246c30554cba2a4a16) Simpan di APP/src/app.js
* [index.html](https://gist.github.com/okaprinarjaya/0c6024ca151b9c895b202d0c99293b8c) Simpan di APP/public/index.html
* [styles.css](https://gist.github.com/okaprinarjaya/f7b29b97cef37095b2f2ceebbd51b71f) Simpan di APP/public/styles.css

Jika semua source code display component sudah dibuat, maka kamu sudah bisa mencoba menjalankannya untuk melihat bagaimana tampilannya.

### Action types dan action creators, dan reducers

#### `APP/src/actions/types.js`

```js
export const CHANGE_LOCALE = 'CHANGE_LOCALE'
```

#### `APP/src/actions/creators.js`

```js
import { CHANGE_LOCALE } from './types'

export function changeLocale(locale) {
  return {
    type: CHANGE_LOCALE,
    payload: {locale: locale}
  }
}
```

#### `APP/src/reducers/index.js`

```js
import { combineReducers } from 'redux'
import { polyglotReducer } from 'redux-polyglot'
import { CHANGE_LOCALE } from '../actions/types'

function locale(state = 'en', action) {
  if (action.type === CHANGE_LOCALE) {
    return action.payload.locale
  }

  return state
}

const appReducers = combineReducers({
  locale,
  polyglot: polyglotReducer
})

export default appReducers
```

### Redux store, polyglot middleware dan mendefinisikan database bahasa

Untuk konsep Redux Middleware dapat kamu baca di: http://redux.js.org/docs/advanced/Middleware.html#the-final-approach dan untuk konsep 
store dapat kamu baca di: http://redux.js.org/docs/basics/Store.html .

#### `APP/src/store.js`

```js
import { createStore, applyMiddleware, compose } from 'redux'
import { createPolyglotMiddleware } from 'redux-polyglot'

import { CHANGE_LOCALE } from './actions/types'
import appReducers from './reducers'
import languages from 'languages'

// https://github.com/Tiqa/redux-polyglot#with-middleware
const middlewares = [
  createPolyglotMiddleware(
    [CHANGE_LOCALE],
    action => action.payload.locale,
    locale => new Promise(resolve => {
      resolve(languages[locale])
    })
  )
]

// http://redux.js.org/docs/advanced/Middleware.html#the-final-approach
const appliedMiddleware = applyMiddleware(...middlewares);
const reduxStore = compose(appliedMiddleware, window.devToolsExtension())
const store = reduxStore(createStore)(appReducers, window.__INITIAL_STATE__);

if (module.hot) {
    module.hot.accept('./reducers', () => {
        const nextRootReducer = require('./reducers').default
        store.replaceReducer(nextRootReducer)
    })
}

export default store
```

#### `APP/src/languages.js`

```js
const languages = {
  "en": {
    "weather": "Weather",
    "false-news": "False News",
    "valid-news": "Valid News",
    "gossip": "Gossip",
    "entertainment": "Entertainment",
    "food-n-beverage": "Food and Beverage",
    "products": "Products",
    "music": "Music",
    "store": "Store",
    "settings": "Settings",
    "calendar": "Calendar",
    "games": "Games",
    "electronics": "Electronics",
    "finance": "Finance",
    "banking": "Banking",
    "automotive": "Automotive",
    "menus-computer": "Menus of your computer"
  },
  "fr": {
    "weather": "Météo",
    "false-news": "Fausses nouvelles",
    "valid-news": "Nouvelles valides",
    "gossip": "Potins",
    "entertainment": "Divertissement",
    "food-n-beverage": "Nourriture et boisson",
    "products": "Des produits",
    "music": "La musique",
    "store": "Le magasin",
    "settings": "Paramètres",
    "calendar": "Calendrier",
    "games": "Jeux",
    "electronics": "électronique",
    "finance": "La finance",
    "banking": "Bancaire",
    "automotive": "Automobile",
    "menus-computer": "Menus de votre ordinateur"
  }
}

export default languages
```

### Container component

Yang akan dimuat oleh container component yang kita buat adalah sebuah display component yang bernama `MainWrapper`. Kenapa kita 
membutuhkan component yang bersifat sebagai container? Jawabannya adalah, karena kita akan mem-passing semua functions yang kita 
butuhkan dari polyglot melalui sebuah container component yang akan mempassing semua kebutuhan ke display component. Jadi, Polanya 
adalah, satu container component akan memuat satu atau lebih display component.

Ok, berikut adalah file-file yang perlu kamu buat dan beberapa file yang sudah pernah kamu buat akan diedit / disesuaikan. Yaitu:

#### `APP/src/containers/MainWrapperContainer.js` (File baru)

```js
import { connect } from 'react-redux'
import { createGetP } from 'redux-polyglot'
import { changeLocale } from '../actions/creators'
import MainWrapper from '../components/MainWrapper'

const mapStateToProps = (state) => {
  const { t: translate } = createGetP({allowMissing: true})(state)

  return {
    ...state,
    translate
  }
}

// Ini akan merelasi ke 'Handler switch bahasa pada suatu button'
// function changeLocaleHandler() akan dieksekusi di display component LanguageNavigation
const mapDispatchToProps = (dispatch) => {
  return {
    changeLocaleHandler: (locale) => dispatch(changeLocale(locale))
  }
}

const MainWrapperContainer = connect(
  mapStateToProps,
  mapDispatchToProps
)(MainWrapper)

export default MainWrapperContainer
```

Lalu, seperti penjelasan di awal, kita lanjutkan dengan mempassing beberapa function yg kita butuhkan yaitu `translate()` dan 
`changeLocaleHandler()` ke display component bernama `MainWrapper`. Kita edit file `APP/src/components/MainWrapper.js` menjadi seperti 
berikut ini:

#### `APP/src/components/MainWrapper.js` (File edit)

```js
import React from 'react'
import MenuNavigation from './MenuNavigation'
import LanguageNavigation from './LanguageNavigation'

const MainWrapper = ({ translate, changeLocaleHandler }) => (
  <div>
    <MenuNavigation translate={translate} />
    <LanguageNavigation translate={translate} changeLocaleHandler={changeLocaleHandler} />
  </div>
)

export default MainWrapper
```

Untuk membuat output / render maka kita tidak lagi menggunakan `MainWrapper` untuk dirender. Kita akan merender `MainWrapperContainer`
dengan mengedit file `APP/src/app.js` dengan perubahan-perubahan seperti berikut:

#### `APP/src/app.js` (File edit)

```js
import React from 'react'
import { render } from 'react-dom'
import { Provider } from "react-redux"

import store from './store'
import MainWrapperContainer from './containers/MainWrapperContainer'

class App extends React.Component
{
    render()
    {
        return (
          <Provider store={store}>
            <MainWrapperContainer />
          </Provider>
        )
    }
}

render(
    <App />,
    document.getElementById('react-app')
)
```

### Melakukan translasi di display component

Untuk melakukan translasi kita menggunakan function `translate()` yang dimiliki oleh `node-polyglot`. Harus diingat, bahwa function 
`translate()` dipassing ke `MainWrapper` melalui `MainWrapperContainer`. Berikut file-file yang perlu kita tambahkan perubahan yaitu:

#### `APP/public/index.html` (File edit)

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Awesome React</title>
        <meta charset="utf-8">
        <link rel="stylesheet" href="styles.css" />

        <script type="text/javascript">
        window.__INITIAL_STATE__ = {
          locale: "en",
          polyglot: {
            locale: "en",
            phrases: {
              "weather": "Weather",
              "false-news": "False News",
              "valid-news": "Valid News",
              "gossip": "Gossip",
              "entertainment": "Entertainment",
              "food-n-beverage": "Food and Beverage",
              "products": "Products",
              "music": "Music",
              "store": "Store",
              "settings": "Settings",
              "calendar": "Calendar",
              "games": "Games",
              "electronics": "Electronics",
              "finance": "Finance",
              "banking": "Banking",
              "automotive": "Automotive",
              "menus-computer": "Menus of your computer"
            }
          }
        }
        </script>
    </head>
    <body>
        <div id="react-app"></div>
        <script type="text/javascript" src="/public/bundle-dist.js" charset="utf-8"></script>
    </body>
</html>
```

#### `APP/src/components/MenuNavigation.js`

```js
import React from 'react'

const MenuNavigation = ({ translate }) => (
  <div className="menu-nav-wrap">

    <h1>{translate('menus-computer')}</h1>

    <div className="pagina">

      <div className="linha">
        <div className="tile amarelo">
          <span className="titulo">{translate('weather')}</span><br/>
        </div>

        <div className="tile azul">{translate('false-news')}</div>
        <div className="tile tileLargo vermelho">{translate('valid-news')}</div>
        <div className="tile verde">{translate('gossip')}</div>
        <div className="tile tileLargo amarelo">{translate('entertainment')}</div>
      </div>

      <div className="linha">
        <div className="tile tileLargo amarelo">{translate('food-n-beverage')}</div>
        <div className="tile azul">{translate('products')}</div>
        <div className="tile verde">{translate('music')}</div>
        <div className="tile vermelho">{translate('store')}</div>
        <div className="tile tileLargo verde">{translate('settings')}</div>
      </div>

      <div className="linha">
        <div className="tile amarelo">{translate('calendar')}</div>
        <div className="tile verde">{translate('games')}</div>
        <div className="tile vermelho">{translate('electronics')}</div>
        <div className="tile tileLargo verde">{translate('finance')}</div>
        <div className="tile azul">{translate('banking')}</div>
        <div className="tile verde">{translate('automotive')}</div>
      </div>

    </div>

  </div>

)

export default MenuNavigation
```

### Handler switch bahasa pada suatu button

Pada saat kamu membuat code untuk container component `MainWrapperContainer`, harus kamu perhatikan bahwa kita sudah mendefinisikan
sebuah function yang akan merubah `redux store` kita yang akan mengubah state `locale`. Edit file `APP/src/components/LanguageNavigation.js` menjadi seperti berikut:

#### `APP/src/components/LanguageNavigation.js` (File edit)

```js
import React from 'react'

const LanguageNavigation = ({ translate, changeLocaleHandler }) => (
  <div className="lang-nav-wrap">
    <button type="button" onClick={(x) => changeLocaleHandler('en')}>Change language to ENGLISH</button>
    <button type="button" onClick={(x) => changeLocaleHandler('fr')}>Change language to FRANCE</button>
  </div>
)

export default LanguageNavigation
```

DONE! kamu bisa test hasilnya.

