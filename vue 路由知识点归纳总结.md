最近做项目才发现，我确实对 vue-router 太不熟悉了，都只了解个简单用法就开始搞了，本来很简单的问题，都搞不清楚。现在重新看一遍文档，重新梳理一下。



## vue 路由的原理
说实话，路由我一直也就光顾着用，没认真思考过这个问题，还是那次人家面试问了这个，我才反应过来是应该好好的了解一下了。  

无刷新跳转页面，是单页应用的一大优势，用户体验好，加载速度快，vue 路由的跳转就是无刷新的，它有两种形式，可以在定义路由的时候通过 `mode` 字段去配置，如果不配置这个字段，那么默认使用的就是 `hash` 模式。
hash 模式，即通过在链接后添加 # + 路由名字，根据匹配这个字段的变化，触发 `hashchange` 事件，动态的渲染出页面。就有点类似像 a 链接用作页面上的锚点一样，不会刷新页面。  

另外一种方式，是 `history` 模式，也就是使用的浏览器的 history API，`pushState` 和 `replaceState`。通过调用 `pushState` 操作浏览器的 `history` 对象，改变当前地址，同时结合`window.onpopstate` 监听浏览器的返回和前进事件，同样可以实现无刷新的跳转页面。`replaceState` 与 `pushStete` 不同的就是，前者是替换一条记录，后者是添加一条记录。  

有关于 `pushState` 的用法，详见[MDN 文档](https://developer.mozilla.org/zh-CN/docs/Web/API/History_API)。这个 API  与 `ajax` 合起来构成的 `pjax` 技术，也是用于实现页面无刷新加载的一种方式，常用于 PC 长列表页面的翻页啥的~
history 相对于 hash 模式来说，最大的好处就是没有讨厌的 # 符号，比如同样一个 list 页面，在 `hash` 模式下，url 链接表现为 `http://yoursite.com/#/list`，看着就难受。在 `history` 模式下面，url 链接表现为 `http://yoursite.com/list` ，看着比较清爽～而且～相信做过微信公众号开发的都懂，这个该死的 # 有多烦人～在下面的应用场景里面我再讲下这个问题～

那么问题来了，既然 history 模式样子好看，功能也一样，为啥还是用 hash 模式的人比较多【此处没有真凭实据，我瞎说的】？因为 history 模式，还需要服务端进行配置，否则刷新页面就会产生 404 错误。这里也比较好理解啦，因为我们实际上是使用的 pushState 操作页面的跳转，而不是真的去服务器请求另外一个 list.html 文件，那按照 `http://yoursite.com/#/list` 这个路径，自然找不到啦～

如果是 nginx 的服务器，在 `location /` 里面加上 `try_files $uri $uri/ /index.html;` 即可。这行代码表示：如果匹配不到静态资源的路径，就将重定向到 index 页面，这样就不会出错啦～因为需要找后端小哥哥修改服务器配置文件，如果自己没有完全理解，两边又沟通不清楚的情况下，使用 history 模式的难度无疑就大了一些～不过也不是什么大问题～全看个人需要啦哈哈～



## vue 路由传参的两种方式
页面参数无非就两种，query 和 params，params 是以 `/params` 的形式表现在 url 上，而 query 是以 `?query=query1` 这种形式表现在 url 上，此外，使用 params 参数还需要配置到路由定义上，不然不会展示在 url 上，并且刷新就会消失。

这个比较简单，需要注意的地方就是：如果传 params 参数，不能使用 `path` 字段跳转，否则没效果。而 query 参数则没有这个限制，使用 `name` 和 `path` 字段都可以。
这个虽然简单！但是一定要自己操作一遍才记得住啊。。反正我是早就看到，但是一直记混了～～重新用 demo 写了一遍才记住～而且别人说的也不一定就是对的，还是要自己实验一遍才知道呢。╮(╯▽╰)╭



## vue 路由的跳转
vue 路由的跳转分成两种，一种是声明式，使用`<router-link>`声明跳转，`to`属性定义跳转的参数。另一种是编程式，使用 `router.go()`、`router.push()`、`router.replace()`方法进行跳转，`go`方法就是与浏览器的`history` api 的方法相同，可以进行返回上一页等操作。

`push`方法和`replace`方法的区别在于，前者会把当前页面加入 history 记录里面，可以通过`history.go(-1)`回到这个页面。而`replace`方法则会在 history 记录里面替换掉当前记录，如果你在跳转后的新页面返回上一页，它不会回到跳转前的页面，会回到上上个页面，如果上上个页面没有记录，则不会跳转。



## vue 路由守卫

vue 路由守卫分为三种，一种是全局的路由守卫，通常在实例化路由之后设置，来做一些通用的路由操作，它所有的路由跳转都会执行的操作；一种是单个路由独享的守卫，在单个路由定义的时候进行设置，所有跳转这个路由都会执行；另外一种就是组件内的守卫，只在组件内生效。

全局路由守卫类型：
* `router.beforeEach(to, from, next)`
* `router.afterEach(to, from, next)`

路由独享的守卫：
* `beforeEnter(to, from, next)`

组件独享的守卫:
* `beforeRouteEnter(to, from, next)`
* `beforeRouteUpdate(to, from, next)`  —— 动态参数路径改变时，组件实例被复用的时候调用。
* `beforeRouteLeave(to, from, next)` —— 导航离开组件所在路由时被调用。



## vue 路由的一些应用场景处理


### 什么时候用 push，什么时候用 replace
最开始路由跳转我都是用 `push()` 比较多，或者用 `go()` 做返回，很少用到过 `replace()` ，后来在业务需求下翻文档才发现这个漏掉的 API。简单来说，当你需要从A页面跳转到B页面，再跳转到C页面，然后在C页面返回，能直接返回到A页面。那么在B页面跳转C页面的时候，使用`replace()`方法进行跳转即可。


### 动态改变页面的 title
* 定义路由的时候，在路由的 meta 字段里面添加一个 title 属性，定义好页面的标题名称。
* 在全局的路由守卫`beforeEach()`方法里面添加一个判断，获取路由的 meta 字段，动态的改变页面的 title。
```
// router.js
{
	path: '/index',
	name: 'index',
	meta: {
		title: '首页'
	}
}

// main.js
router.beforeEach(to, from, next){
	if(to.meta.title){
		document.title = to.meta.title
	}
	next()  // 这个方法必须调用 不然路由不会跳转
}
```

### 微信开发，单页应用页面 url 导致的传参或者跳转失败的问题

#### 微信授权跳转
在做微信授权跳转的时候，hash 模式下链接里面带有 # 号可能会导致重定向跳转失败，使用 encodeURIComponent 把页面地址处理之后，再传入。
```
let _url = encodeURIComponent(location.href)
location.href = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${this.appid}&redirect_uri=${_url}&response_type=code&scope=snsapi_base&state=#wechat_redirect`
```

#### 获取 wxconfig 配置
前端获取 wxconfig 比较简单，主要的操作都在后端，前端只需要传一个 url 参数，由后端去获取 config 的参数，回传给前端。前端拿到参数后，调用 `wx.config` 方法。
```
let url = location.href.split('#')[0]
http.get('weixin/config',{
    params:{
        url: encodeURIComponent(url)
    }
})
.then(res=>{
    wx.config({
        beta: true,        // 必须这么写，否则wx.invoke调用形式的jsapi会有问题
        debug: false,                       // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
        appId: res.data.appId,           // 必填，企业微信的corpID
        timestamp: res.data.timestamp,  // 必填，生成签名的时间戳
        nonceStr: res.data.nonceStr,   // 必填，生成签名的随机串
        signature: res.data.signature,// 必填，签名，见 附录-JS-SDK使用权限签名算法
        jsApiList: ['scanQRCode'] // 必填，需要使用的JS接口列表，凡是要调用的接口都需要传进来
    })
    // 检测微信
    wx.error(function(res){
        //  config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
        console.log('错误信息====',res)
    })
})
```


### 单页面应用加百度统计
在单页应用上面，如果直接把百度统计的代码加到 html 不做任何处理的话，是统计不到每个页面的访问量的，所以把添加 js 和 监听跳转页面的代码都写到 main.js 里面去。
```
// 添加百度统计 先判断是生产环境还是开发环境 如果是开发环境 不用添加
if (process.env.NODE_ENV !== 'development') {
    let _hmt = _hmt || [];
    window._hmt = _hmt;  // 必须把_hmt挂载到window下，否则找不到
    (function() {
        var hm = document.createElement("script");
        hm.src = "https://hm.baidu.com/hm.js?4b2adcc1085fb7ebad2110873f4f33b7";
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(hm, s);
    })()
}

router.beforeEach(to, from, next){
	// 添加百度统计代码 先判断是生产环境还是开发环境
    if (process.env.NODE_ENV !== 'development') {
        // 添加页面统计
        if (_hmt) {
            if (to.path) {
                _hmt.push(['_trackPageview', '/#' + to.fullPath]);
            }
        }
    }
}
```


参考文档：
https://segmentfault.com/a/1190000011967786
https://www.jianshu.com/p/febd38110645