# {1:222, 2:123, 5:888}，请把数据处理为如下结构：[222, 123, null, null, 888, null, null, null, null, null, null, null]。

```javascript
  let obj = { 1:222 , 2:123, 5: 888 };
  obj.length = 12;
  obj = Array.from(obj).slice(1); // [222, 123, undefined, undefined, 888, undefined, undefined, undefined, undefined, undefined, undefined]
  // 接下来就是遍历该数组 将 undefined 置为null
```
 `Array.from(arr, mapfn,thisArg)`方法，用于将两类可以把对象转换为真正的数组：类似数组的对象和可遍历的对象（部署了Iterator接口的，String，ES6新增的Map，Set）。可以传3个参数，其中第一个是数组必传；第二个是一个函数（类似map函数），对数组元素进行操作后再返回数组，可选；第三个是对于this关键字的指向，可选。<br/>
 类似数组的对象：
 必须有length对象，如果没有，转出空的数组。所以任何有length属性的对象，都能通过这个方法转换为数组（此时扩展运算符...无法转换，length属性会决定转化的数组是什么样子。）