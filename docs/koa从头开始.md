## 配置 koa-route

## 配置静态资源文件

```
npm i koa-static

// app.js
const static = require('koa-static')
app.use(static(__dirname+'public'))
```

## koa使用模板引擎

```
npm install --save art-template
npm install --save koa-art-template

// 引入
const render = require('koa-art-template');

render(app, {
    debug: config.debug,  //启动模板引擎调试模式；如果为true: {cache:false, minimize:false, compileDebug:true}
    root: './app/views',
    extname: '.html',
    escape: false,
    htmlMinifierOptions: {
        collapseWhitespace: true,
        collapseInlineTagWhitespace: true,
        minifyCSS: true,
        minifyJS: true,
        trimCustomFragments: true
    }
});
```



## koa 使用 redis 缓存

