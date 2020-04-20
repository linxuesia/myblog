# 什么是微信`JS-SDK`

微信`JS-SDK`是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包。

通过调用微信`JS-SDK`的api，来高效的实现拍照、选图、语音、位置等调用原生手机系统的功能，同时可以直接使用微信分享、扫一扫等微信特有的能力。

# 微信`JS-SDK`的引入和使用

* 直接使用`script`引入文件，[点击下载](http://res.wx.qq.com/open/js/jweixin-1.2.0.js)。引入之后，可以通过全局变量`wx.`来调用api。
* 使用`wechatPlugin`插件，支持`CommonJS`模块化的引入方式，因为在我的项目中使用了`vux`框架，已经添加了`wechatPlugin`插件，所以可以用模块化的方式来引入，引入之后，在`Vue`组件内部可以用`this.$wechat`来获取`wx`对象，如果是在组件外部使用，采用以下的方式来引入：

```
import { WechatPlugin } from 'vux';

Vue.use(WechatPlugin);

Vue.wx.config({

    //...your wx.configs here

})

```

这样引入之后，你就可以很方便的把需要的接口功能封装起来，在其他的组件中调用。

# [微信JSSDK上传多张图片](http://leo108.com/pid-2069.asp)

注意！`wx.uploadImage` 这个接口只能链式调用，不能并行请求！假如你要上传的图片放在一个数组里面，然后用`for`循环的方式，这样只能上传第一张图片，`promise.all`也不行噢，因为它也是并行的。

```
//递归请求微信上传图片接口
            let looper = ()=>{
        
                this.upLoadImg(ids[count]).then(res=>{
                    return this.downLoadImg(res);
                })
                .then(res=>{
                    if(count<lengt){
                        arrs.push(res);
                        count++;
                        looper();
                    }else{
                        resolve(arrs);
                    }
                })
                .catch(err=>{
                    reject(arrs);
                })
        
            }
            looper();
```



# 一些bug

* 在微信的页面里面 cookies不能跨页面调用？？？只有localStorage可以。因为在这个项目里我需要把儿童的id存到本地，但是跳转到新页面之后，cookie就没有了？已经设置了`path:/`还是不行。百度了一下有的人也遇到这个问题，最后选择用·localStorage`，不懂是什么问题。