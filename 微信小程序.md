### 取消弹出授权的框

小程序取消了弹出授权的层，直接用`open-data`组件可以获取到用户的信息，或者使用`button`组件的`open-type="getUserInfo"`属性也可以获取到。

### 小程序跨页面通信

* 将方法和属性全部挂载到 `getApp()`也就是小程序实例上，在各个页面都可以通过`getApp()`来获取。

* 封装一个事件的类，模拟事件的发送和接收。下面是两种方式，好像第一种稍微要简洁一些，但是没有移除事件的方法。

  ```
  var event = {
    on: function(name, fn) {
      var data = broadcast.data
      if (data.hasOwnProperty(name)) {
        data[name].push(fn)
      } else {
        data[name] = [fn]
      }
      return this
    },
    fire: function(name, data, thisArg) {
      var fnList = broadcast.data[name] || []
      for (i = 0, len = fnList.length; i < len; i++) {
        fnList[i].apply(thisArg || null, [data, name])
      }
      return this
    },
    data: {}
  }
  ```

  ```
  export default class Event {
    constructor() {
      //创建一个map对象，用来存储事件和事件的函数方法
      this.listeners = new Map();
    }
  
    /**
     * 监听事件 将事件名、对象实例、函数方法存储在MAP对象
     * @param {name:监听的事件名称；obj:调用页面的实例；callback:事件的函数方法}
     */
    on(name,obj,callback) {
      
      let titles =[obj,callback]; 
      this.listeners.has(name)||this.listeners.set(name,[]);
      this.listeners.get(name).push(titles);
      
    }
  
    /**
     * 触发事件 找出与事件名对应的函数方法，传入参数并调用这个方法
     * @param{name:触发的事件名称；args:需要传入函数方法的参数} 
     */
    emit(name,args) {
  
      let listeners = this.listeners.get(name);
      if(listeners&&listeners.length){
        listeners.forEach(item=>{
          item[1].call(item[0],args);
        });
        return true;
      }
      return false;
      
    }
  
    /**
     * 移除事件 在MAP对象中移除事件名对应的函数方法
     * @param{name:移除的事件名称；obj:移除页面的实例；handler:需要移除的函数方法；}
     */
    off(name,obj,handler) {
      let obser = this.listeners.get(name);
      let _index = obser.findIndex(item=>{return item[0]===obj});
      obser.splice(_index,1);
      this.listeners.set(name,obser);
    }
      
  }
  ```

  

* 将数据存储到本地来读取，好像有点太复杂。

* 页面之间传递参数，优点是简单好用，缺点是只能由上一页传递到下一页，返回上一页没办法触发。

  

  

  ### 摇一摇切换环境代码

  ```
  // 定义初始值
  var lastTime = 0
  var x = 0
  var y = 0
  var z = 0
  var lastX = 0
  var lastY = 0
  var lastZ = 0
  var shakeSpeed = 120 // 摇一摇阈值
  // 定义加速度监听回调
  function shake () {
    var nowTime = new Date().getTime() // 记录当前时间
    // 如果这次摇的时间距离上次摇的时间有一定间隔 才执行
    if (nowTime - lastTime > 100) {
      var diffTime = nowTime - lastTime // 记录时间段
      lastTime = nowTime // 记录本次摇动时间，为下次计算摇动时间做准备
      x = acceleration.x // 获取x轴数值，x轴为垂直于北轴，向东为正
      y = acceleration.y // 获取y轴数值，y轴向正北为正
      z = acceleration.z // 获取z轴数值，z轴垂直于地面，向上为正
      // 计算 公式的意思是 单位时间内运动的路程，即为我们想要的速度
      var speed = Math.abs(x + y + z - lastX - lastY - lastZ) / diffTime * 10000
      var flag = true
      // 如果计算出来的速度超过了阈值，那么就算作用户成功摇一摇
      if (speed > shakeSpeed) {
        // 触发间隔周期内再次检测到摇一摇，直接返回
        if (+(new Date()) - lastTriggerTime < triggerDuration) {
          flag = false
        }
        // 避免多次跳转环境切换页面
        getCurrentPages().forEach((item) => {
          if (item.__route__ == 'pages/console/index') {
            flag = false
          }
        })
        if (flag) {
          //切换环境 也可以替换成别的代码
          wx.navigateTo(...)
          lastTriggerTime = +(new Date())
        }
      }
      lastX = x // 赋值，为下一次计算做准备
      lastY = y // 赋值，为下一次计算做准备
      lastZ = z // 赋值，为下一次计算做准备
    }
  }
  ```

  

  

