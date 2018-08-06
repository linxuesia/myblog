## 创建多个入口文件

webpack 入口 entry 可以设置成单个文件，也可以设置成多文件入口。多文件入口的入口的话需要用到 node.js 的模块来遍历入口文件夹，然后输出成一个对象。

```
function (globPath) {
  var entries = {},
    basename, tmp, pathname;

  glob.sync(globPath).forEach(function (entry) {
    basename = path.basename(entry, path.extname(entry));
    tmp = entry.split('/').splice(-4);
	
	var pathsrc = tmp[0]+'/'+tmp[1];
	if( tmp[0] == 'src' ){
		pathsrc = tmp[1];
	}
    pathname = pathsrc + '/' + basename;  //正确输出js和html的路径
    entries[pathname] = entry;
    
    
  });
  
  return entries;
  
}
```



## 创建html模板

## 在普通js文件里判断是开发环境还是生产环境

## 