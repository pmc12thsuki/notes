Server-Side Rendering 
===
###### tags: `React`
# Why SSR
## 前端網頁的進化
- [後端 MVC / 前端 MVC / SPA / SSR](https://blog.techbridge.cc/2017/09/16/frontend-backend-mvc/)
- SSR 是為了改善 client-side rendering 一些缺點而誕生的
    - 包含 initial loadup 速度太慢
    - SEO 不友善
- SSR 屬於前端的一種優化
## Traditional React-App
- traditional React-App
    - 傳統的 Reacp-App 都是 Browser-side render
    - Browser 打到 server 後
        - 先拿到一份 root 的 html 檔，這份 html 檔通常很空（沒有重要資訊）
        - 因為 html 檔案中有 src 我們的 js file ，也就是 build 後的那一份 JS 檔，裡面有所有的 react render 邏輯，所以 browser 會再 request 一次這個 js file
        - 通常 react 還需要透過 api 拿到 data，所以通常還需要再發送一個 api request 到 API (backend) server
    - 如下圖
    ![](https://i.imgur.com/9QJgTSK.png)

- 效率問題
    - 許多研究顯示，當頁面越快浮現消費者的停留/購買率越高
    - 因此要想辦法解決 react render 太慢的問題
    - 於是使用 SSR
    - **使用 SSR 的目的是讓 client 可以越快看到頁面越好**
## 對於 SSR 的誤解
- 對於 SSR 的誤解
![](https://i.imgur.com/1cNpFNy.png)

- SSR 的確可以解決 SEO 的問題
    - 傳統 SEO 就是用爬蟲來爬頁面的 HTML element
    - 但用傳統 React 時，HTML 頁面幾乎是空的，所有元素都是靠 JS 來動態產生
        - 搜尋引擎有可能在動態頁面產生之前就爬完了，所以只爬到空空的內容，導致 SEO 很慘
        - Google 其實是會爬 JS 的、但其他搜尋引擎不確定。google 爬的比重也可能會改
    - 因此使用 SSR，在第一個頁面由 server-side 提供已經有內容的 HTML，確實可以改善 SEO

# SSR Overview
- **使用 SSR 的目的是讓 client 可以越快看到頁面越好**
- 使用 SSR 後，browser 第一次 request 所得到的 html file **就已經是可以顯示的頁面**
    - 如此可以讓 client 端很快地顯示畫面，**大幅減少第一個頁面呈現的時間**
    - client 端看到畫面後，之後在 client 端運行的仍是傳統的 React App
    - 第一個頁面由 Server side render，之後的操作還是由 Client side render
- 使用 SSR 將傳統 React 改進，讓 initial request 不論是進到哪個頁面都可以快速的 render
    - 因此對於所有的 Component，都要考慮到 client render 跟 server render 的可能性

- SSR (server-side rendering) 中的 server
    - **並不是 API SERVER**
    - **而是 RENDER SERVER**
    - 用途是來 render react component 的
    - 當然也可以將兩個 server 寫在一起，**但是不建議，會造成 scale up 困難、邏輯混亂**
![](https://i.imgur.com/WWIpvT8.png)

## SSR FLow
- browser 初次向 server 端 request 時，會拿到一份已經可以 render 的 HTML file
- 由於這份 HTML 中有 src 我們真正的 react app bundle，因此 browser 會再向 server 端 request html file 中 include 的那個 js bundle 檔案（跟 traditional react app 一樣）
- 接著 React App 要 boot up 時一樣會向 server 端打 api 拿 data
    - 我們會將 initial HTML 中所需要的 data 都先塞進 initial HTML 中，節省時間
- 最後就會像一般的 React App 運作
![](https://i.imgur.com/VmY4rxD.png)

# App Overview
![](https://i.imgur.com/nySoi1A.png)

## 已經架好的 Api server
- 位置在 `https://react-ssr-api.herokuapp.com/`
![](https://i.imgur.com/I8yd4ga.png)

# Server Architecture 
## 使用兩個 backend Server
- 使用兩個 backend Server
    - API Server 提供資料、商業邏輯、Auth 等等
    - Rendering Server 提供頁面的 render
    - ![](https://i.imgur.com/KrVmJYn.png)
- 分開的理由
    - 兩者的功能十分不一樣
        - 將 render 及 Api server 分開，方便以後想要換 render 的方式（angular, vue）等等，就不需要被 react 綁死
        - ![](https://i.imgur.com/pzquTI7.png)
    - 比較容易 scale up
        - 狀況 A： api 很複雜，但 render 很簡單。如果是這樣我們可以輕易透過增加 API server 的數量來 scale up
            - ![](https://i.imgur.com/lZ4mbNE.png)
        - 狀況 B：api 較簡單，但 render 的東西很複雜。由於 **server-side rendering 其實很消耗資源且耗時**，因此常常遇到 SSR 的 render server 需要 scale up 的狀況
            - ![](https://i.imgur.com/hfqr36l.png)

# Project Setup
## 流程
![](https://i.imgur.com/h9bSisy.png)

## Bolierplate
- 資料夾結構
```

- api // api server
|
- server // render server
|--- src
    |--- index.js // route server, include all server logic, will not be visible by client
    |--- helpers // include server-side helpers
        |--- renderer.js // 負責生成 server 端首次要提供給 client 的 HTML，會將 react component 轉成 HTML string，並 include client 的 bundle
    |--- client
        |--- client.js // client side entry point, will be visible by client
        |--- component // 裡面放所有 react component
            |--- Home.js
            |--- ...more
|--- package.json
|--- node modules
```

## Create the Home component

### RenderToString
- Home component 是要當在 route `/` 回復的 html
- Home component 是用 react 的方法寫，怎麼產生成傳統的 html 並回覆呢
    - 使用 ReactDOM 中的 renderToString 
    - 會將 react component 轉換成 html string
![](https://i.imgur.com/kdpVHmj.png)

- 寫完 Home component 後，在 index 中回覆該 component
    - 這個 code 將 react 改用 `require` 而不是 `import` 的方式，之後會改進
    - 其中使用了 `renderToString(<Home />) ` 要將 Home compoenet 轉換成 HTML string
    - **然而，node 並不了解 JSX**，並不知道 <Home /> 是什麼
        - **在一般的 react 中，JSX 也都會先轉換成真正的 js 語法**
        - **因此必須讓 server side 能解析 JSX**
```javascript=
'use strict';

const express = require('express');
const React = require('react');
const { renderToString } = require('react-dom/server');
const Home = require('./client/components/Home').default;

const PORT = 3000;
const app = express();


app.get('/', (req, res) => {
  const content = renderToString(<Home />);
  res.send(content);
});


app.listen(PORT, () => {
  console.log(`listening on port ${PORT}`);
});

```

### JSX on the Server
- 傳統在 client 端，寫好的 react (JSX) 透過下面流程轉換成 JS
    - 經過 webpack 及 babel 轉換成 ES5
    - 並 bundle 成一個 js file 
![](https://i.imgur.com/sy7TA59.png)

- 我們也將此流程用在 server side
    - 將 server side 所有的 code 都透過 webpack 跟 babel 轉換並 bundle 成單一個 js 檔後，再用 node 執行這個 js 檔
![](https://i.imgur.com/iq1TMLy.png)

#### 設定 server 端的 webpack
- 設定 webpack
    - 產生 webpack.server.js file
    - 下面的設定有些已經過時了，新專案需要重新改寫
```javascript=
'use strict';

const path = require('path');

module.exports = {
  // tell webpack that we're building a bundle for nodeJS, rather than for the Browser
  target: 'node',

  // tell webpack the root file (entry point) of our server application
  entry: './src/index.js',

  // tell webpack where to put the output file that is generated
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'build'),
  },

  // tell webpack to run babel on every file it runs through
  // 把 JSX, es2015~2017 等 轉換成 ES5
  // 下面有些設定已經過時了，可以省略
  module: {
    rules: [
      {
        test: /\.js?$/, // 只使用 babel on .js file. 是 regex expression
        loader: 'babel-loader', // 使用 babel
        exclude: /node_modules/, // 不對 node_modules 資料夾中的檔案做 babel
        options: { // 傳進 babel-loader 中的 options
          presets: [ // the rules that will be used by babel to trans our code
            'react', // JSX -> js
            'stage-0', // async code
            ['env', { targets: { browsers: ['last 2 versions'] } }], // 將 popular browser 最近兩個 version 所需要的都做 transform
          ],
        },
      },
    ],
  },

};

```
- 設定 package.json 中的 script
    - 告訴 webpack 我們的 config 檔案
    - `"dev:build:server": "webpack --config webpack.server.js"`

- 接著在 terminal 中下剛剛設定的 script，webpack 就會 build 檔案並產生`bundle/bundle.js`
- 在 terminal 中用 `node build/bundle.js` 來執行，就會起 server 並 server `/` 的 Home component
    - `/` 會回傳使用 Home 所編譯成的 HTML

### 已解決的兩個問題
![](https://i.imgur.com/4f1Kkju.png)

# Server Configuration
## Hot Reload - Rebuilding and Restarting
- 要加上 hot reload 的功能，需要改兩個地方
    - 當檔案有修改時，webpack 要重新 build 出 bundle.js
    - 當有新的 bundle.js 時，express server 要重啟，server 新的 bundle.js

![](https://i.imgur.com/QEa1Dro.png)

- 在 package.json
    - webpack 加上 --watch，讓他持續偵測是否有改動並重新 build
    - 使用 nodemon，並 --watch build 讓他只在 build 資料夾有更新時，重新啟動 exec 裡的指令（`node build/bundle.js`）
    - 目前這樣的設定需要同時起兩個 terminal 才行運作
```json=
"scripts": {
    "dev:build:server": "webpack --config webpack.server.js --watch",
    "dev:server": "nodemon --watch build --exec \"node build/bundle.js\""
  },
```

## Isomorphic Javascript
- 只講 server side rendering 其實有點誤導
    - 不需要 react 也可以做 SSR
    - 例如傳統的 server side templating 也是在 server-side 寫 html（如傳統 MVC）
![](https://i.imgur.com/JubL4oC.png)

- 所以後來有人講 Universal JS 或 Isomorphic JS，都是指我們的 code 在 server 端跟 browser 端都可以運作

- 一個 universal JS 的原則是，我們應該在 client 跟 server 端使用**同樣的coding style**
    - 我們目前還是使用不同的 coding style
    - 因為 node 沒有支援 ES2015 的語法，所以依然使用 `require/module.exports`
    - 但 react 使用 ES2015 語法，照理來說是使用 `import/export`

- 由於我們目前有在 server 端加上 webpack 跟 babel 來將 ES2015 轉換成舊的 JS，所以我們可以將 **node 的語法改成使用 ES2015**

```javascript=
import express from 'express';
import React from 'react';
import { renderToString } from 'react-dom/server';
import Home from './client/components/Home';
```

# Client side problem
## JS 無法運作
- 傳統 react client side
    - rendor DOM 之後，因為有帶 js file ，所以會 set up event handlers
    - ![](https://i.imgur.com/wX72TZM.png)
- 由於現在只把 Component 轉換成 HTML string 之後就回傳
    - 就算原本的 component 中有寫 event handler，但轉換成 HTML 後這些屬於 js 的 code 就會消失
    - 因此轉成 HTML string 時，裡面並沒有帶 js
    - 所以無法跟頁面互動
    - 例如有一個 button 帶有 onClick event，但轉成 HTML 之後就不見了
    - ![](https://i.imgur.com/SDPcD16.png)

## Client side bundle

- 解決方法：再開一個 webpack pipeline 來產生另一個 client bundle
    - 第一個 bundle 是我們之前已經創好的的，包含 server side 的 code ，且會回覆 `/` route 的 HTML 頁面
        - 所有 server side 的 code 都在這個 bundle 中
        - 也包含了一開始要呈現給 client 的那個 Home component
        - 由於 server side 的東西較機密，**我們不能將這個 bundle 送到前端執行**
    - 第二個 bundle 是要用來解決上述問題的
        - 會包含所有 client side 的 js code，也**就是整個傳統的 React App，會整個傳給前端**
        - 這個 bundle 會被 root HTML 所引用 (client 一開始拿到的那個 HTML)，因此 browser 會再回來 request 這個 bundle 檔案
![](https://i.imgur.com/6Zx6mJh.png)


- 設定第二個 bundle 的 webpack file (webpack.client.js)
    - entry point 改成 client/index.js
    - ouput 的位置改成 public
    - target 是 browser，因為是預設所以可以不用寫

```javascript=
'use strict';

const path = require('path');

module.exports = {
  // webpack build bundle for client (browser) on default, so do not need to specify target

  // tell webpack the root file (entry point) of our client bundle
  entry: './src/client/client.js',

  // tell webpack where to put the output file that is generated
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'public'),
  },

  // tell webpack to run babel on every file it runs through
  // 把 JSX, es2015~2017 等 轉換成 ES5
  // 下面有些設定已經過時了，可以省略
  module: {
    rules: [{
      test: /\.js?$/, // 只使用 babel on .js file. 是 regex expression
      loader: 'babel-loader', // 使用 babel
      exclude: /node_modules/, // 不對 node_modules 資料夾中的檔案做 babel
      options: { // 傳進 babel-loader 中的 options
        presets: [ // the rules that will be used by babel to trans our code
          'react', // JSX -> js
          'stage-0', // async code
          ['env', {
            targets: {
              browsers: ['last 2 versions'],
            },
          }], // 將 popular browser 最近兩個 version 所需要的都做 transform
        ],
      },
    }],
  },
};

```

- 接著在 server 中設定，讓一開始所傳的 HTML 檔案中有 src 這個 client 端的 bundle.js
    - 如此一來，當 client 到 `/` 時， server 會直接回傳一份帶有**馬上要顯現給使用者的頁面** 的 HTML，且此 HTML 中有引用 client 端的 js (bundle.js)，讓 client 之後可以像一般的 react app 一樣繼續運作
    - **可以兼顧首次 render 的速度、並且可以維持後續的 react 運作**
```javascript=
app.use(express.static('public')); // a static direcroty that is avaliable for everyone

app.get('/', (req, res) => {
  const content = renderToString(<Home />);

  // 創一個簡單的 html，include 上面的 content 且引用 client 端的 bundle.js (在 public 中)
  const html = `
  <html>
    <head></head>
    <body>
      <div id="root">${content}</div> // 引用要 render 的 content, 加上 id 讓之後的 react app 可以重新 render 一次
      <script src="bundle.js"></script> // 引用 client 端的 script
    </body>
  </html>
  `;
  res.send(html);
});
```

### 兩個 bundle 的差別
- server side 的 bundle 是直接由後端來運行
    - 其中包含 server 的機密邏輯（API key，商業邏輯等等）
    - 可以 include client 端的東西來用
    - 但千萬不能傳給 browser、或被 client 給 include
- client side 的 bundle 是要傳給 browser 來運行
    - 千萬不能 include 到 server 端的檔案，否則機密就傳給 client 了

- 兩邊的 entry point 都會 boot-up 一些 react 的東西
    - 但兩邊的設定是不一樣的
    - 有些 react-redux 是只想在 server 端發生的、有些只想在 client 端發生
![](https://i.imgur.com/mMswjzU.png)

### 設定 client bundle 的 entry point
- browser 拿到 client bundle 後，需要**再重新 render 一次已有的畫面** (Home component)，才能加上 event handler 等等，並成為真正的 react app

- 在 client.js 中 （entry point）
    - 由於要重新渲染已有的畫面 （server 一開始回給 client 的 HTML）
        - 所以使用 `ReactDOM.hydrate` 而不是傳統的 `ReactDOM.render`
```javascript=
// entry point for the client side application

import React from 'react';
import ReactDOM from 'react-dom';
import Home from './components/Home';

// 使用 hydrate 來重新渲染已有的畫面 （server 一開始回給 client 的 HTML）
ReactDOM.hydrate(<Home />, document.querySelector('#root')); // 會把 event handler 再重新 bind 上去

```

## Boot up Flow
- client 首次打 server 端時，server 會直接回覆一個 html template，此 template 可以很快地顯現給 browser
- 由於此 html template 有 include 一份 client 的 bundle，所以 browser 會再向 server 要這份檔案
    - 這份 client 的 bundle 就是所有的 react code (傳統的 react code)
- browser 拿到這份 bundle 後，會再重新 render 同樣的頁面一次
    - 將一開始的 HTML 頁面中綁定上 js (event handler 等等)
    - 並帶上 react router 等功能，成為完整的 react app
![](https://i.imgur.com/23wthMJ.png)
# Refactoring
## Refactor webpack 
### Extract Base webpack
- 現在的兩個 webpack 設定檔有許多重複的地方
    - 將重複的地方拆出來，變成 base, client, server 三個檔案
    - 再使用 webpack-merge 這個套件來將兩個 webpack 設定檔合而為一，產出 client 端的 webpack 設定檔及 server 端的設定檔
    - ![](https://i.imgur.com/NHCLYbv.png)

- 創造 webpack.base （為前後端共同的部分）
```javascript=
module.exports = {
  // tell webpack to run babel on every file it runs through
  // 把 JSX, es2015~2017 等 轉換成 ES5
  // 下面有些設定已經過時了，可以省略
  module: {
    rules: [{
      test: /\.js?$/, // 只使用 babel on .js file. 是 regex expression
      loader: 'babel-loader', // 使用 babel
      exclude: /node_modules/, // 不對 node_modules 資料夾中的檔案做 babel
      options: { // 傳進 babel-loader 中的 options
        presets: [ // the rules that will be used by babel to trans our code
          'react', // JSX -> js
          'stage-0', // async code
          ['env', {
            targets: {
              browsers: ['last 2 versions'],
            },
          }], // 將 popular browser 最近兩個 version 所需要的都做 transform
        ],
      },
    }],
  },
};

```

- 再在 client/server 端把 baseConfig 跟各自的 config merge 在一起
    - `merge(baseConfig, config)`

### 減少 server 端的 bundle 大小
- webpack 預設會將所有 import / require 的套件的整包 code 都一起加入 bundle 中
    - 在**前端的確需要這樣，如此才能在 browser 中跑**
    - 但在後端，那些套件都可以在 runtime 中直接跑 node_modules
    - 因此在後端，我們可以將所有在 node_modules 中的套件都**省略放進 bundle** 中
    - 如此會讓 server-side 的 bundle 大小更小、在 bundle 時更快
- 以上**完全非必須**，因為 server 端的 bundle 不會傳給 client 端，所以 bundle 大小其實不會影響太大。但還是能讓 server build 的速度加快



- 可以使用 `webpack-node-externals` 來達成
    - 在 webpack.server.js 中加上一行就可以
    - `externals: [webpackNodeExternals()]`
    - 讓 webpack 在打包 server bundle 時，不會將引用的套件也一起打包進 bundles
## Single Script Startup
- 我們現在有三個分別的指令需要啟動： build server bundle / build client bundle / run server
- 使用 `npm-run-all` 來幫助我們一次啟動三個指令
    - `dev:*` 會跑所有 dev: 開頭的 script

```json=
"scripts": {
    "dev:build-server": "webpack --config webpack.server.js --watch",
    "dev:build-client": "webpack --config webpack.client.js --watch",
    "dev:server": "nodemon --watch build --exec \"node build/bundle.js\"",
    "dev": "npm-run-all --parallel dev:*"
  },
```

## Renderer Helper
- 先前 server 端的 render 是直接寫死在 `/` route 中，較不彈性
- 現在把 render HTML 的 code 拉出來，放在 `/src/helpers/renderer.js` 中
- renderer.js
```javascript=
import React from 'react';
import { renderToString } from 'react-dom/server';
import Home from '../client/components/Home';


export default () => {
  const content = renderToString(<Home />);

  // 創一個簡單的 html，include 上面的 content 且引用 client 端的 bundle.js (在 public 中)
  return `
  <html>
    <head></head>
    <body>
      <div id="root">${content}</div>
      <script src="bundle.js"></script>
    </body>
  </html>
  `;
};

```

- 如此 route 就變得很簡潔
```javascript=
app.get('/', (req, res) => {
  res.send(renderer()); // render the server-side html
});
```

# Adding Navigation (react router)
- 專案中會有兩個 route handler
    - express route handler：負責一些 api 相關的 route，但完全不負責跟 Render 有關的 route
    - react router：負責所有跟 render 有關的 route
- 所有跟 HTML render 有關的 route，express router 都會交給 React router 來處理
    - 包含 **initial page render on the server**
    - 跟 follow up render on the browser
![](https://i.imgur.com/2n3AwHF.png)

- SSR 的目標是第一個頁面由 Server side render，之後的操作還是由 Client side render
    - 我們目前只寫死 `/` 會由 server-side render
    - 目標是不管使用者的第一個頁面進到哪裡，都要由 Server side render。接下來的頁面則都是由 client side render
    - 並且第一個頁面的 server-side render，也會**交給 React Router 來決定實際的 render 內容**
## 傳統的 React router 運作 (非 SSR)
- 對於所有要求頁面的 request （initial request），express 都一律回覆 index.html 跟 bundle.js
- 接著 client 端就可以由 bundle.js 進行 client side render (single page application)
- client 端的 route 都是交給 **React Router** 來處理
    - React router 會根據 address bar 中的 URL 來決定現在要 render 哪些元件、頁面
![](https://i.imgur.com/6QIZSec.png)

## SSR 實現 React router 的困難
- 上述 React router 在 browser 實現時，時會根據 address bar 中的 URL 來決定要 render 哪些 Component
- 但在 SSR render 時，並沒有 address bar 來給 React router 資訊
- 怎麼做到讓 server 端的 React router 能在 initial request 進來時，知道要將哪一個 component 轉成 HTML 並回覆呢
- 其實 React-Router 有提供兩種 Router
    -  StaticRouter 就是給 server side 用的 router
    -  BrowserRouter 就是傳統在 react app 中使用的 client 端 browser
![](https://i.imgur.com/ZoheGAn.png)

- 我們建立 Route.js 這個 Component 來設定 Route ( 使 URL 對應到的 component 都一樣)
    - 只是 server-side 使用 staticRouter
    - client-side 使用 browserRouter
- 如此不論 browser 一開始 request 的是哪一個頁面
    - 都能夠用 SSR 回應那個初始頁面
    - 後續的 React SPA 也能夠正確的 routing
    - ![](https://i.imgur.com/L187z7Q.png)

### Route.js

```javascript=
import React from 'react';
import { Route } from 'react-router-dom';
import Home from './components/Home';

export default () => (
  <div>
    <Route exact path="/" component={Home} />
    {/* more routes */ }
  </div>
);

```

### client.js (client-side)
- 引入 Route.js 並使用 BrowserRouter
```javascript=
// entry point for the client side application

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import Routes from './Routes';

ReactDOM.hydrate(
  <BrowserRouter>
    <Routes />
  </BrowserRouter>,
  document.querySelector('#root') // 會把 event handler 再重新 bind 上去
);

```

### renderer.js (server-side)
- 引入 Route.js 並使用 StaticRouter
    - client 端的 BrowserRouter 會直接讀 address bar 的 URL 來對應路徑
    - StaticRouter 由於沒有 address bar，所以我們必須告訴他現在的 URL 是什麼，他才能顯示對應的路徑
        - 也就是透過 express 中的 `req.path` 來達成
    - StaticRouter 中的 `context` prop 是必須的，之後會使用到
- 如此無論使用者第一次進入哪一個頁面，都可以享受 SSR 帶來的好處
```javascript=
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router-dom';
import Routes from '../client/Routes';

export default (req) => {
  const content = renderToString(
    <StaticRouter location={req.path} context={{}}>
      <Routes />
    </StaticRouter>
  );

  // 創一個簡單的 html，include 上面的 content 且引用 client 端的 bundle.js (在 public 中)
  return `
  <html>
    <head></head>
    <body>
      <div id="root">${content}</div>
      <script src="bundle.js"></script>
    </body>
  </html>
  `;
};

```

### index.js (server-side)
- 現在不論 browser initial request 什麼 route
    - express 都可以交給 renderer 去 render 出 SSR 的頁面
    - 這個 initial SSR HTML 都會帶有 bundle.js，讓 React APP 後續運作在 client 端
```javascript=
app.get('*', (req, res) => {
  res.send(renderer(req)); // render the server-side html
});
```

### 常見的 error message
- 如果在 browser console 看到類似下列的 error
`Warning: Expected server HTML to contain a matching <div> in <div>.`
- 代表我們在初次 server render 的 HTML 跟使用 ReactDOM.hydrate 所 render 的 component 有所不同
    - 也就是 HTML Mismatch，如下圖
    - ReactDOM.hydrate 期待兩個 render 的 HTML 應該要是一樣的
    - 只是會再綁定上 JS 的東西進去

![](https://i.imgur.com/qko9hnE.png)

- 可以透過瀏覽器 disable JS 來找出 server 初次 render 的 HTML、與跟後續由 JS dynamic render 的差別

# Adding Redux
## reducers and action creator
![](https://i.imgur.com/7WcC372.png)

## Four Big Challenges
- 想要在 SSR 端加入 reudux 會遇到四大困難
    - server 端的 redux 跟 client 端的需要不同設定
        - 所以會有兩個 Store 的設定
        - 就像 Router 一樣
    - 處理 Authentication
        - 我們會打 API server 確認使用者的登入狀態，並根據狀態顯示不同頁面
        - client 端傳統使用 cookie/session/token 來驗證是否已經登入，這些都是夾帶在 browser 中
        - **現在需要在 render-server 端認證使用者是否已經登入**
    - 在 client 端使用 redux 時， action creators 會主動觸發 reducer 去改變 state，接著自動重新 render
        - **而在 Server 端，我們需要控制、等到所有 API 需要的 initial data 都拿到之後，才能產生 SSR 頁面並且送出**
        - 否則 SSR 送出去的 HTML 頁面都會是沒有 data 在裡面的
        - 最困難的部分！
    - redux bootup 時會需要再一次的 hydration
        - 就跟先前 React Bootup 時很像
![](https://i.imgur.com/Bk1MV6p.png)

## 設定兩個 redux store (問題ㄧ)
### action and reducer
- action 跟 reducer 跟原先的設定方式一模一樣
- acitons/index.js
```javascript=
import axios from 'axios';

export const FETCH_USERS = 'fetch_users';
export const fetchUsers = () => async (dispatch) => {
  const res = await axios.get('https://react-ssr-api.herokuapp.com/users');
  dispatch({
    type: FETCH_USERS,
    payload: res,
  });
};

```
- reducers/userReducer.js
```javascript=
import { FETCH_USERS } from '../actions';


export default (state = [], action) => {
  switch (action.type) {
    case FETCH_USERS:
      return action.payload.data;
    default:
      return state;
  }
};

```
- reducers/index
```javascript=
import { combineReducers } from 'redux';
import usersReducer from './usersReducers';

export default combineReducers({
  users: usersReducer,
});

```
### client side
- client 端的 redux setup 跟原本一模一樣
```javascript=
// redux
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import { Provider } from 'react-redux';
import reducers from './reducers';

const store = createStore(reducers, {}, applyMiddleware(thunk));

ReactDOM.hydrate(
// provider component 中的 child component 都能取得 store 中的 prop
  <Provider store={store}> 
    <BrowserRouter>
      <Routes />
    </BrowserRouter>
  </Provider>,
  document.querySelector('#root') // 會把 event handler 再重新 bind 上去
);
```

### server side
- 先做基本設定
    - 邏輯跟 client 端一樣
    - 某些地方還需要加上更多設定才能正確運作
- helpers/createStore
    - 只負責產生 store
```javascript=
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducers from '../client/reducers';
/*
由於 server 端的 redux store 還需要一些 config 才能提供給 provider，
所以將 store 的建造與 config 寫在這個 file 中，等都設定好之後，再在 index.js 中將 store 提供給 provider

這個 file 用於設定 redux store，而 provider 是用在將 redux 跟 react 連結
provider 本身就是一個 react component， provider 的所有 childer 都能直接拿到 store 中的 prop
*/


export default () => {
  const store = createStore(reducers, {}, applyMiddleware(thunk)); // 跟 client 端一樣
  // some config here
  return store;
};

```

- index.js
    - 在 route 中先建立 store
    - 等待初始的 API data 都拿到後，交給 store
    - 才能 render component，轉成 HTML 並回傳給 client
```javascript=
app.get('*', (req, res) => {
  const store = createStore();

  // some logic to initialize and load data into the store

  res.send(renderer(req, store)); // render the server-side html
});

```

- helpers/renderer.js
    - 拿到的 store 已經是包含 initial data 的
    - 所以直接把交給 Proviver 就好
```javascript=
const content = renderToString(
    <Provider store={store}>
      <StaticRouter location={req.path} context={{}}>
        <Routes />
      </StaticRouter>
    </Provider>
  );
```

- 由於上述的 config 還沒設定
    - **所以現在執行時，SSR 送出的 HTML 都不包含 API request 的 data**
### babel polyfill
- 我們在 action creator 中常會使用 async/await 來跟 request API data
- 目前的 babel 設定，要執行 async/await code 的轉換時會產生 error
    - 不論是 server-side 或 client-side 都有用 babel 打包，所以都會遇到這個問題
    - 以前的前端都用 CRA 來建立 react 專案，其中都已經幫我們設定好 webpack 與 babel 了，所以沒有遇到這個問題
    - 以前後端的 node 版本已經支援 async/await，且不需要再透過 babel 打包，所以沒有遇到這個問題

- 解決方法很簡單，在兩個 entry point (index.js 跟 client.js) 最上面都加上 `import 'babel-polyfill';` 就好

## Server side data loading (問題二)
### Client side
- client loading data 的方式
    - 在 componentDidMount 觸發 action creator
    - 等 state 改變後就會重新 render
![](https://i.imgur.com/JZlrYjS.png)

- timeline 如下
![](https://i.imgur.com/44Sidfn.png)

### Server side
- 我們在 renderer.js 中，component 是馬上轉成 HTML 並回覆給 browser 
    - **component 中的 `componentDidMount` 根本沒有被呼叫**
    - 因此當然不會觸發 API call

- 解決方法
    - SSR 中，我們得先知道要 render 的那個 URL **需要**哪些 component
        - **但還先不要 render**
        - 這邊跟現有的 react route 不一樣，現有的 react router 看到符合的 URL 後就會**直接 render 所需的 component**
        - 因此會需要對 react router 做大幅度的修改
    - 呼叫這些 component 的 loadData function，透過這些 function 來打對應的 API 並拿到所有需要的 data
    - 等所有 API 都完成，把 data 給 store，再開始 render 並轉成 HTML 回覆
        - **只需要 render 一次**
    - ![](https://i.imgur.com/ILPxVej.png)
- pros and cons
    - 雖然麻煩，但是這是目前流行的 best practices (包含 Next.js 也是使用這個概念)
    - ![](https://i.imgur.com/uRDgYMT.png)

- 為了要達成上述的目標，需要很多額外的 code，並且讓 react-router 變得很難寫又很醜、犧牲彈性
    - 這是 SSR 的缺點
    - In the case where some of our app’s routes need to load data upon rendering, we’ll need a static way to define our routes
    - instead of the dynamic way of doing it when only the client is involved. 
    - Losing the ability to define dynamic routes is **one reason why server-side rendering is best kept for apps that really need it.**

#### 用 react-router-config
- [react-router-config](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config) 是 react-router 中的一個功能
    - 用來做 SSR
    - 可以滿足上圖中的前兩步驟
        - Figure out what components would have rendered based on URL
        - call a `loadData` method attached to each of thos components
    - 但寫起來非常麻煩，原本的 Route 定義會變很冗長，只能用 JS object 的方式寫，不能用原本的 JSX

- 原本的 Routes.js 要改成如下的形式
    - 原本是 export 一個 react component
    - 現在是 export 一個 array of object
        - 每個 object 代表一個 route ，並設定其對應的 component 及 loadData function
```javascript=
import Home from './components/Home';
import UsersList from './components/UsersList';

export default [
  {
    path: '/',
    component: Home,
    exact: true,
    // more setting
  },
  {
    path: '/users',
    component: UsersList,
    // more setting
  },
];

```

- 原本引用 Routes.js 的地方也要修改，renderer.js 及 client.js 如下
    - 改用 renderRoutes 這個函數，來將 route (array of obj) 變成真正的 component
```javascript=
ReactDOM.hydrate(
  <Provider store={store}>
    <BrowserRouter>
      <div>{renderRoutes(Routes)}</div>
    </BrowserRouter>
  </Provider>,
  document.querySelector('#root') // 會把 event handler 再重新 bind 上去
);

```

- 接著在 index.js 中，使用 matchRoutes 來看看對應的 URL 需要 render 什麼 components
    - matchRoute 的第一個參數是 a list of route config，也就是上面改寫的 Routes
    - 第二個參數是現在正 request 的 url
    - 會回傳 array of component 代表該 url 所需要 render 的 component
    - 如此就可以在 render 之前，針對這些 component 來打 API 拿資料

```javascript=
app.get('*', (req, res) => {
  const store = createStore();

  // some logic to initialize and load data into the store
  // matchRoutes will look at our route configuration and decide what set of components need to be rendered
  // 第一個參數是 list of route config，第二個參數是現在 request 的 route。會回傳 array of component 代表該 url 所需要 render 的 component
  matchRoutes(Routes, req.path);
  res.send(renderer(req, store)); // render the server-side html
});
```

#### LoadData Funtions
- 要完成 loaddata，有三個要改的地方
![](https://i.imgur.com/A4s6Y6c.png)

1. 在每個「有可能會需要 SSR 的 component 中」，若其在 render 時需要某些 API data，則在這個 component 中加上 loadData 這個 function
    - loadData 跟傳統在 componentDidMount 中觸發的 action creator 很像
    - loadData 一樣會呼叫 action creator，但會將其結果 dispatch 給 server side 的 reducer，並變更 server-side 的 store
    - 如在 UsersList ，fetchUser 結束後會回傳 action，並透過 store.dispatch 傳給 server 端的 reducer，藉以更改 server 端的 store 的狀態
```javascript=
function loadData(store) { // server 端的 store，而不是 client 端的
  // manually dispatch
  // will return a Promise of API call
  return store.dispatch(fetchUsers()); 
}

export { loadData }; // named export
```
2. 在 Routes 設定中，將對應的 URL 所要呼叫的 component 的 loadData function 夾帶在 config 中
```javascript=
import Home from './components/Home';
import UsersList, { loadData } from './components/UsersList';

export default [
  {
    path: '/',
    component: Home,
    exact: true,
  },
  {
    loadData: loadData, // 可以簡寫成 loadData 就好
    path: '/users',
    component: UsersList,
  },
];

```
3. 在 index.js 中
    - 由於 matchRoute 會回傳一個「所有該 URL 需要被 render 的 component」的陣列
    - 陣列中每個 obj 就是在 route.js 中設定的 obj
    - 所以可以透過每個 obj 是否帶有 loadData 參數，得知該 component 是否需要先打 API 來獲得資料
    - 如果需要就呼叫該 component 的 loadData 函數（同時把 server 端 的 store 傳進去當參數）
    - 再用 Promise.all 等待所有需要的 API 都呼叫完成
    - 由於 API 呼叫完成後，就會觸發 reduer 去改變 server 端的 store，所以此時 server 端的 store 已經獲得所有該有的資訊
    - **就可以開始 render 了！！**（終於）

```javascript=
app.get('*', async (req, res) => {
  const store = createStore();

  // 針對每個需要被 render 的 component，如果該 component 有 loadData 這個 property，就執行 loadData 的 function call，沒有的話就維持 null
  const promises = matchRoutes(Routes, req.path).map(({ route }) => (route.loadData ? route.loadData(store) : null));
  
  // waiting for loadData(API calls) to complete
  await Promise.all(promises); 

  // then store is full of require data
  res.send(renderer(req, store)); // render the server-side html
});
```
- 整體流程如下
    - 透過呼叫各個 component 的 loadData，來填滿 server 端的 store
![](https://i.imgur.com/mBUv0HM.png)

![](https://i.imgur.com/LQ5ddqV.png)

## Client State (store) Rehydration (問題三)
### Client 的 store
- 目前的頁面，雖然 SSR 已經獲得 API 資料後才 render，但仍可以發現頁面會出現 refresh 的感覺
- 因為我們只填充的 server side 的 store
- 但當 client react app 掌管 (hydrate)之後，client 端的 store 仍是空的，導致頁面會出現短暫空白
- 等 client 端要重打 API、得到資訊後，才會填滿 cliet 端的 store

![](https://i.imgur.com/kdICX0o.png)

### Dumping state to HTML templates
- 要讓 client 端也讀到 server 端 store 的 state
    - 透過 server 端 render 的 HTML 來傳遞資料
![](https://i.imgur.com/q1kPfb7.png)
- 在 render HTML 時，把 server 端 store 中的 state 加進去
    - 直接加一個 script tag，並將 store 轉成 JSON string 加到 window 中
```javascript=
<html>
    <head></head>
    <body>
      <div id="root">${content}</div>
      <script>
        window.INITIAL_STATE = ${JSON.stringify(store.getState())}
      </script>
      <script src="bundle.js"></script>
    </body>
  </html>
```
- client 再透過讀取 windoe 中的 state 來獲得資訊
    - 可以直接透過 `window.INITIAL_STATE` 讀到 data
    - 用這個 state 當作 client 端 store 的初始值
### Security Flaw - XSS attack
- XSS : 跨站腳本攻擊(Cross-Site Scripting, XSS)
- 由於我們直接將 server store 中的 data 直接 stringify 提供給前端
    - 但我們不能確定這些 data 是乾淨的
    - 有可能藏有惡意的輸入
        - 有可能在最一開始存進 ＤＢ 時就沒有檢查到、或打的 API 被駭客入侵了
        - 不應該假設拿到的資料都是正確的！
    - 因此可能發生類似 injection 的 attack，稱為 XSS attack
- 使用 `serialize-javascript` 套件
    - 會將 string 中放在 script tag 裡的可疑元素替掉，轉換成 Unicode 表達法
    - 使 browser 端不會執行這些惡意的 script tag
    - 在 browser render 時依然可以正確的將 unicode 轉換成原本的字母、符號
- By default，React 在 render 時會檢查並避免 XSS attack 
    - 但這邊是由我們直接提供 initial state，所以 React 不會檢查
    - 要由我們自行避免 XSS 發生
# Components Refactoring
## The Page Components
- 我們想要區別哪些 Component 可能包含 loadData function
- 我們想要把 Root level component 跟其他小 component 分開
    - Root level component 就是直接對應到某個 URL 的 component
    - 且只有 Root level component 有可能會包含 loadData function
    - 如 `/` -> Home component
    - 如 `/users` -> UsersList

- 將 Root Component 的名稱都改為 `{componentName}Page`
    - 稱為 Page Component
    - 如 HomePage, UsersPage, AdminPage 等
    - 只有 Page Component 能包含 loadData function
    - Page Component 可以共享、重複使用一些 component
![](https://i.imgur.com/rRoXwLO.png)

## Page exports
- 由於可能多個 page 都會需要 export 其 component 跟 loadData
- 因此將 HomePage 跟 UsersListPage... 等 Page 改成下面的輸出方式

- HomePage
```javascript=
export default { component: Home };
// 沒有用到 loadData function
```

- UsersListPage
```javascript=
export default {
  loadData,
  component: connect(mapStateToProps, { fetchUsers })(UsersList),
};

```

- 如此在設定 Routes.js 時，就可以善用 spread 表達式

就會由原本的 code

```javascript=
import Home from './components/Home';
import UsersList, { loadData } from './components/UsersList';

export default [
  {
    path: '/',
    component: Home,
    exact: true,
  },
  {
    loadData: loadData, // 可以簡寫成 loadData 就好
    path: '/users',
    component: UsersList,
  },
];
```

變得像下方非常簡潔！！！！
```javascript=
import HomePage from './pages/HomePage';
import UsersListPage from './pages/UsersListPage';

export default [
  {
    ...HomePage, // 等於 { component: Home }
    path: '/',
    exact: true,
  },
  {
    ...UsersListPage, // 等於 {component: usersListPage, loadData: loadData}
    path: '/users',
  },
];

```