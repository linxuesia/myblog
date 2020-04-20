项目里面有个需求，是检测两个数组里面是否有相同的重复项，有则返回 `true`，没有则返回 `false`，并且不需要获取重复项的数据。
如果是需要获取重复项的数据，那肯定需要遍历循环，但是既然只要简单知道是否重复，就没有必要一个个去遍历，直接用 ES6 的新数据类型 Set 即可，因为他有一个特性是每个元素都不重复，因此经常被用来做数组去重。
通过它的这个特性，自然也可以检测重复，将需要检测的两个数组合并成一个 Set，如果长度小于两个数组的长度之和，那么肯定存在重复项。
```
 function isInArry(arr1, arr2){
     let new_arr = new Set([...arr1, ...arr2])
     return new_arr.size < (arr1.length + arr2.length)
 }
```

### 如果需要取两个数组里面重复的部分，可以换另外一个方法：
```
let arr1 = [1,2,3]
let arr3 = [2,3,7]

function filterArry(arr){
     return arr.filter(i => arr.indexOf(i) !== arr.lastIndexOf(i))
}
filterArry(arr1.concat(arr2))
```

今天又有一个新的需求
### 数组随机乱序
```
function shuffleArray(arr){
    let m = arr.length
    while (m>1) {
        let index = Math.floor(Math.random() * m--)
        [arr[m], arr[index]] = [arr[index], arr[m]]
    }
    return arr
}
```



## 数组分组

