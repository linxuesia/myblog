第一步，全局安装，执行 `npm i localtunnel -g` 。

安装完成后，启动你本地的服务，确保本地服务能正常访问，之后执行`lt -s domain -p 8080`， `-s`代表的是 subdomain，用来配置你的个性化域名，也可以不填。 `-p`代表的是本地服务的端口号。

正常运行之后，会看到如下提示，打开链接就可以访问啦。

需要注意的是，本地开发环境的代码都是没有压缩过的，文件大小都很大，访问起来可能比较慢一些。

另外需要注意一点，webpack 起的本地项目，要把 dev-server 的 disableHostCheck 设置为 true 。

