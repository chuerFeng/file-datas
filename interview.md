## Webpack

```javascript
npm i -D  // npm install --save-dev  开发环境
npm i -S // npm install --save  生产环境
```



### webpack配置

#### build

`http_env` 为参数，`npm run build2`运行后会被赋值在node全局配置`process.env`中

```javascript
// package.json
scripts: {
    "serve": "vue-cli-service serve",
    "build": "webapck --config ./webpack.config.js",
    "lint": "vue-cli-service lint",
    "build2": "cross-env  http_env=qa node ./build/webpack.prod2.config.js"
}
```

```javascript
// webpack.prod2.config.js
const ENV = process.env.http_env
console.log("2:", ENV);
```



#### module

###### rules



> *css-loader:解析css文件中的@import依赖关系*

> *style-loader：将webpack处理之后的css内容插入到HTML的HEAD标签里*

`style-loader` ` css-loader` 



> autofixer是postcss的功能插件，主要是给css中的一些属性添加-webkit-这种前缀做兼容的
>
> postcss-loader则是webpack的loader组件，主要作用是webpack在读取css模块的时候调用postcss和postcss的插件处理css内容的。所以会有postcss-loader配置options的过程实际上是为postcss配置需要的插件

`autoprefixer`  `postcss-loader` 



`less`  ` less-loader`

`file-loader`

`bable-loader` ` @bable/preset-env ` `@bable/core`  // 将es6,7,8 转换为es5

`@bable/polyfill` 转换 promise, proxy, Set, Maps等新api

`vue-loader` ` vue-template-compiler` ` vue-style-loader`  解析vue

`webpack-dev-server` 热更新

```javascript
module.exports = {
  // ...省略其他配置
  devServer:{
    port:3000,
    hot:true,
    contentBase:'../dist'
  },
  plugins:[
    new Webpack.HotModuleReplacementPlugin()
  ]
}
```





`npm i -S vue`



