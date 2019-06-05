# 下面代码中a在什么情况下回打印1？
>题目如下
```javascript
  var a = ?;
  if(a == 1 && a == 2 && a == 3){
    console.log(1);
  }
```
本题主要考察`==`的隐式类型转换，引用类型在比较运算符的时候，隐式转换会调用本类型的`toString`或`valueOf`方法所以只需改`toString`或者`valueOf`方法即可。

```javascript
  var a = {
    i: 1,
    toString(){
      return a.i++
    }
  }

  // 或者
  var a = {  i: 0 }
  a.valueOf = ()=>{
    return ++a.i    
  }

  if(a == 1 && a==2 && a==3){
    console.log(1);
  }
```


