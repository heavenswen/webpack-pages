#webpack-page

config说明：

ab)
webpack 多页环境搭建 jquery,window引入方法 分离公用css、js,生成对应入口css、js ,压缩css、js， html生成对应页面,支持 vue ，sass，es6编译,地址采用绝对地址，请使用绝对地址访问



npm 操作

<h4>下载依赖</h4>
<pre>npm i</pre>

<h4>测试服务器</h4>
<pre>npm run dev</pre>
//局域网地址 192.168.4.30 --host 192.168.4.30(去掉为127.0.0.1)
//webpack-dev-server --inline --hot --host 192.168.4.30

<h4>发布</h4>
npm run build


文件结构
-static //生成发布目录
-src //源码
--assets //资源库
---img
---js
---css
---fonts
--components //组件模块 
--pages //页面资源
---user //页面库
----index //单页面 生成时对应名称
-----app.html//页面 必须为‘app’ 
-----app.js //单页入口
-----app.vue //入口vue 模版

#参考vue-multiple-pages

**This project is a modern Vue.js starter
which uses Vue 2, Webpack2, and ElementUI**

## Features

1. [Vue2](https://github.com/vuejs/vue)
2. [Webpack2](https://github.com/webpack/webpack)
3. [ElementUI](https://github.com/ElemeFE/element)
4. [Eslint](https://github.com/eslint/eslint)

## Start

 - Clone or download this repository
 - Enter your local directory, and install dependencies:

``` bash
npm install
```

## Dev

``` bash
# serve with hot reload at localhost:8010
npm run dev

```

[http://localhost:8010/user/login.html](http://localhost:8010/user/login.html)

[http://localhost:8010/user/index.html](http://localhost:8010/user/index.html)

## Build

``` bash
# build for production with minification
npm run build
node server.js
```

[http://localhost:2333/user/login.html](http://localhost:2333/user/login.html)

## Folder Structure

```bash
├── src             # main
│    ├── assets     # source
│    │    ├── css   # css
│    │    └── img   # img
│    ├── components # components
│    └── pages      # pages
│         └── user  # user part
│              ├── index  # index.html
│              │    ├── app.js    # entry js
│              │    ├── app.html  # html template
│              │    └── app.vue   # main vue for login
│              └── login  # login.html
│                   ├── app.js    # entry js
│                   ├── app.html  # html template
│                   └── app.vue   # main vue for login
├── dist            # npm run build result
├── node_modules    # dependencies
├── .babelrc        # babel config
├── .eslintrc.js    # eslint config
├── server.js       # port 2333
├── webpack.config.js # webpack config
├── node_modules    # dependencies
└── package.json    # package info
```

## How The `Multiple Page` Works ?(Assumed that you have the basic knowlege of [webpack](https://github.com/webpack/webpack))

1. Firstly, we need to get the `entries` and `chunks`

    * The constant `entries` is an [Object](https://webpack.js.org/configuration/entry-context/#entry) type, it's prop is the `chunk` and it's value is the relative path of the entry js file
    * The constant `chunks` is an Array type for the [CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)

    This step needs a package called: [glob](https://github.com/isaacs/node-glob)
    ```js
    const entries = {}
    const chunks = []
    glob.sync('./src/pages/**/*.js').forEach(path => {
      // Get all the entry js files and forEach them

      const chunk = path.split('./src/pages/')[1].split('.js')[0]
      // E.g, chunk = 'user/index/app' path = './src/pages/user/index/app.js'

      entries[chunk] = path
      // Now we got the entries

      chunks.push(chunk)
      // Then, we collect the chunks for CommonsChunkPlugin
    })
    // ...
    const config = {
      entry: entries, // The constant entries is used here
      plugins: [
        new CommonsChunkPlugin({
          name: 'vendors',
          filename: 'assets/js/vendors.js',
          chunks: chunks, // The constant chunks is used here
          minChunks: chunks.length
        })
        // ...
      ],
    }
    ```

2. Then,combine the `html template file` with the right `chunk`

  The second step,we use the webpack plugin: [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)

  ```js
  // ...
  const config = {
    // ...
  }
  // ...
  glob.sync('./src/pages/**/*.html').forEach(path => {
    // Get all the html template files and forEach them
    // E.g, path = './src/pages/user/index/app.html'

    const filename = path.split('./src/pages/')[1].split('/app.html')[0] + '.html'
    // E.g, the html filename will be 'user/index.html' in the 'dist' folder

    const chunk = path.split('./src/pages/')[1].split('.html')[0]
    // E,g. the chunk will be 'user/login/app'

    const htmlConf = {
      filename: filename,
      template: path,
      inject: 'body',
      favicon: './src/assets/img/logo.png',
      hash: process.env.NODE_ENV === 'production',
      chunks: ['vendors', chunk]
    }
    config.plugins.push(new HtmlWebpackPlugin(htmlConf))
  })
  ```

## Inspired by [element-starter](https://github.com/ElementUI/element-starter)

## License
MIT
