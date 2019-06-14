### 输出以下代码执行的结果并解释为什么
```javascript
  var obj = {
    '2': 3,
    '3': 4,
    'length': 2,
    'splice': Array.prototype.splice,
    'push': Array.prototype.push
  }
  obj.push(1)
  obj.push(2)
  console.log(obj)  // [ empty x2 , 1, 2, splice: f , push: f]
```
  **解析：** 此题主要考察类数组；push方法；对象转数组的方法。
  1. 对象转数组的方式：Array.from()、splice()、concat()等。
  2. obj对象中具有length属性，push、和splice方法。在调用这两种方法时，会将该对象转为类数组。执行ob.push(1)时，调用的是Array原型上的push，此时将对象转换为类数组，改数组的长度为属性length为2，然而其中的属性，'2' '3'是数组的下标，是数组的第三项和第四项。所以在执行push(1)时，该类数组为 [ , ,] ，两个空。此时push(1) 得到 [ , , 1] , push(2) 得到 [ , , 1, 2]。
  
  