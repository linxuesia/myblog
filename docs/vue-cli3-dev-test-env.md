## 需求

首先介绍一下本项目的背景，是基于 vue-cli3.1.1 的单页应用，目前测试环境和生产环境都在线上，并且都在同一个域名下，其中生产环境部署在根目录下，测试环境部署在名为 test 的子目录下，根据生产环境和测试环境的不同，某些界面和交互有细微的差别。

根据以上项目的基本情况，分析出需求如下：

* 实现区分线上生产环境和线上测试环境的环境变量，供项目代码中全局使用。
* 实现输入一行命令，执行两条指令，分别打包线上生产环境和线上测试环境的代码。
* 实现打包之后，自动将打包好的文件夹分别按照项目名+时间+环境的格式压缩成 zip 文件





### 配置环境变量和模式

根据 vue-cli3 文档，可以通过设置项目根目录下的 `.env`，`.env.[mode]` 文件来指定环境变量。

创建一个  `.env`  和  `.env.beta`  文件，在 `.env` 文件中，配置如下：

```
VUE_APP_URL = '/'   // 用来设置项目的 baseUrl 和路由的 base 选项 默认是根目录
VUE_APP_MODE = ''   // 设置一个变量 区分线上生产环境和线上测试环境 默认是生产环境
outputDir = 'dist'  // 用来设置打包后生成的文件夹的名字，默认为 dist 文件夹
```



在 `.env.beta` 文件中，配置线上测试环境，如下：

```
NODE_ENV = 'production'   // 因为是打包项目 所以 NODE_ENV 还是 production
VUE_APP_URL = '/test/'   // 线上测试环境，部署在 test 目录下 
VUE_APP_MODE = 'test'   // 这个变量表示当前是线上测试环境
outputDir = 'beta'      // 将线上测试环境打包后生成的代码放在 beta 文件夹下，以示区别。
```



定义好环境变量之后，把项目制定的路径配置到路由和 vue 配置文件中去：

```
// router.js
export default new Router({
    base: process.env.VUE_APP_URL,
	...
})

// vue.config.js
module.exports = {
	baseUrl: process.env.VUE_APP_URL,  // 从 Vue CLI 3.3 起已弃用，改成publicPath
}
```



在项目代码中，可以在任意位置使用 `process.env.VUE_APP_MODE` 的值来判断是线上生产环境还是线上测试环境，用以编写不同的业务代码。例：

```
if(process.env.VUE_APP_MODE == 'test'){
	this.name = '线上测试用户'
}
```

配置好不同模式的环境变量之后，在  `package.json` 里面设置一个 beta 模式：

```
{
	"scripts": {
		...
		"beta": "vue-cli-service build --mode beta"
	}
}
```



这样配置完之后，在命令行运行 `npm run beta` ，会在根目录下生成一个 beta 文件夹，里面就是打包出来的线上测试版本的项目文件。

至此就完成了分环境配置打包，但是这样的麻烦点在于，每次要在命令行输入两次命令，因为我们的项目文件都是发送给后端去部署，所以还需要把文件压缩成 zip，每次都要手动操作，是在有点累。所以，还需要进行一些小小的改造，彻底解放俺们的双手~~



###  合并构建打包命令并且自动生成压缩文件

将两个命令合并成一个很简单，只需要在 `package.json` 里面配置一下，新建一个新的命令用于同时调用两个命令。

```
{
	"scripts": {
		...
		"beta": "vue-cli-service build --mode beta",
		"publish": "vue-cli-service build && vue-cli-service build --mode beta"
	}
}
```

这样配置好之后，只要执行`npm run publish`， 它会自动先执行正式环境构建打包，完成之后再自动执行测试环境的构建打包，hin是方便~~

接着再配置自动压缩，这里就需要使用到一个 webpack 的插件 — `filemanager-webpack-plugin`。

先安装，可以在命令行安装，也可以直接在 vue-cli3 自带的图形管理界面搜索安装，都挺方便。

```
npm install filemanager-webpack-plugin -dev
```

安装完成之后，在 `vue.config.js` 里面配置一下这个插件，它提供了丰富的操作文件的选项 ，这里我只需要使用删除和压缩：

```
let ts = Date.parse(new Date());
module.exports = {
	...,
	chainWebpack: (config) => {
		        if(process.env.VUE_APP_MODE == 'test'){
            config.plugin('compress')
                .use(FileManagerPlugin, [{
                    onEnd: {
                      delete: [   //首先需要删除项目根目录下的dist.zip
                        './*.zip',
                      ],
                      archive: [   //然后我们选择dist文件夹将之打包成dist.zip并放在根目录
                        {source: './dist', destination: `./biProject-${ts}-production.zip`},
                        {source: './beta', destination: `./biProject-${ts}-test.zip`}
                      ]
                    }
                }])
        }
	}
}
```



欧克 ~ 大功告成 ~ ^_^ ~



