next-redux-saga
=====

[![npm version](https://img.shields.io/npm/v/next-redux-saga.svg)](https://npmjs.org/package/next-redux-saga)
[![npm downloads](https://img.shields.io/npm/dm/next-redux-saga.svg)](https://npmjs.org/package/next-redux-saga)
[![XO code style](https://img.shields.io/badge/code_style-XO-5ed9c7.svg)](https://github.com/sindresorhus/xo)
[![styled with prettier](https://img.shields.io/badge/styled_with-prettier-ff69b4.svg)](https://github.com/prettier/prettier)
[![Build Status](https://travis-ci.com/bmealhouse/next-redux-saga.svg?branch=master)](https://travis-ci.com/bmealhouse/next-redux-saga)
[![All Contributors](https://img.shields.io/badge/all_contributors-4-orange.svg)](#contributors)

> `redux-saga` HOC for [Next.js](https://github.com/zeit/next.js/). controlled `redux-saga` execution for server side rendering.

> **Attention:** Synchronous HOC is no longer supported since version 4.0.0!

## Installation

```sh
yarn add next-redux-saga
```

## Getting Started

Check out the official [Next.js example](https://github.com/zeit/next.js/tree/canary/examples/with-redux-saga) or clone this repository and run the local example.

### Try the local example

1. Clone this repository
1. Install dependencies: `yarn`
1. Start the project: `yarn start`
1. Open [http://localhost:3000](http://localhost:3000)

## Usage

`next-redux-saga` uses the redux store created by [next-redux-wrapper](https://github.com/kirill-konshin/next-redux-wrapper). Please refer to their documentation for more information.

### Configure the Store wrapper

```js
import {applyMiddleware, createStore} from 'redux'
import createSagaMiddleware from 'redux-saga'
import {createWrapper} from 'next-redux-wrapper'

import rootReducer from './root-reducer'
import rootSaga from './root-saga'

const makeStore = context => {
  const sagaMiddleware = createSagaMiddleware()
  const store = createStore(
    rootReducer,
    applyMiddleware(sagaMiddleware),
  )

  store.sagaTask = sagaMiddleware.run(rootSaga)

  return store
}

const wrapper = createWrapper(makeStore)

export default wrapper

```

### Configure Custom `_app.js` Component

```js
import React from 'react'
import App from 'next/app'
import withReduxSaga from 'next-redux-saga'

import wrapper from './store-wrapper'

class ExampleApp extends App {
  static async getInitialProps({Component, ctx}) {
    let pageProps = {}

    if (Component.getInitialProps) {
      pageProps = await Component.getInitialProps(ctx)
    }

    return {pageProps}
  }

  render() {
    const {Component, pageProps} = this.props
    return (
      <Component {...pageProps} />
    )
  }
}

export default wrapper.withRedux(withReduxSaga(ExampleApp))
```

### Connect Page Components

```js
import React, {Component} from 'react'
import {connect} from 'react-redux'

class ExamplePage extends Component {
  static async getInitialProps({store}) {
    store.dispatch({type: 'SOME_ASYNC_ACTION_REQUEST'})
    return {staticData: 'Hello world!'}
  }

  render() {
    return <div>{this.props.staticData}</div>
  }
}

export default connect(state => state)(ExamplePage)
```