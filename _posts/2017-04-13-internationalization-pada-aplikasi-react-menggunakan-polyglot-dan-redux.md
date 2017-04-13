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
  }
}

export default languages
```

### Container component

Yang akan di-contain oleh container component yang kita buat adalah display component yang bernama `MainWrapper`. Kenapa kita 
membutuhkan component yang bersifat sebagai container ? 
