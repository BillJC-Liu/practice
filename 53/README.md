# 输出以下代码的执行结果，并解释其原因
此题主要考察堆栈的认识

```javascript
  var a = { n: 1 }
  var b = a;
  a.x = a = { n : 2 }
  console.log( a.x ); // undefind
  console.log( b.x ); // { n: 2 }
```
 执行：
 1.声明变量a，变量b。
 2.变量赋值，a赋值{ n:1 }，由于{ n:1 }是对象，不是原始数据类型，所以存在堆中，a拿到的是{ n:1 }的引用。b=a，b拿到的是a的引用，也就是b也指向{ n:1 }。
 3.a.x=a={n:2}; 虽然赋值表达式是由右至左，但是 . 的优先级比 = 高，所以这里先执行a.x，修改了引用的值，{n:1, x:undefined};接着a.x={ n:2 }；修改了引用的值 { n:1,x:{n:2} }；此时b仍然指于此。a={n:2}，变量a修改了引用，指向{ n:2 }。执行完毕后，a指向{n:2},b指向{ n:1, x:{n:2} }。

 <br/>所以输出结果:  console.log( a.x ); // undefind ; console.log( b.x ); // { n: 2 }
 
