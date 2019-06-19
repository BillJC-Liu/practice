# 实现(5).add(3).minus(2)功能

```javascript
    Number.prototype.add = function(num = 0){
      if(typeof num !== "number" || num.isNaN()) throw new Error("请输入数字")
      return this.valueOf() + num 
    }

    Number.prototype.minus = function(num = 0 ){
      if(typeof num !== "number"  || num.isNaN()) throw new Error("请输入数字")
      return this.valueOf() - num 
    }
```