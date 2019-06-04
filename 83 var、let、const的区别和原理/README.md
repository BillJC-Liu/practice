# var、let、const三者的区别和实现原理
###### 1.先来说说var let const 三者的区别
* var 是函数作用域
* var 和 let 用以声明变量，const 用于声明只读的常量
* var声明的变量不存在块级作用域，在全局作用域中有效，let和const声明的变量只在它所声明的作用域中有效
* let和const不存在像var那样的`变量提升`现象，var可以先使用再声明，而let和const只能是先声明后使用
* let声明的变量存在暂时性的死区，即只要块级作用域中存在let，那么它所声明的变量就绑定了这个区域，不再受外部的影响
```javascript
  var tmp = 123;
  if (true) {
    tmp = 'abc'; // ReferenceError  下面的语句用let 将tmp绑定在了当前的作用域中，此时是未声明就使用了，所以报了语法错误。
    let tmp;  
  }
```
* let不允许在相同的作用域中重复声明同一个变量
* const在声明时必须初始化赋值，一旦声明，其声明的值就不能改变（只读），也不允许重复声明。
>所用const声明一个复合类型，其存储的是一个引用地址，不允许改变的是这个地址，而对象本身是可变的

###### 2.var 为什么会变量提升
**变量提升的定义：** 所有变量的声明语句都会在被提升到代码的头部，这就是变量提升。<br/>
**原理：** JS引擎的工作方式是①先解析代码，获取所有被声明的变量 ②然后在运行。   分为预编译和执行两个阶段。
###### 3.let 真的不会有变量提升的情况嘛？
  &nbsp;&nbsp;答案是错误的，let也存在变量提升的情况。具体看下面代码。
```javascript
  console.log(a); // Uncaught ReferenceError: a is not defined
  let a = 1;
```
  &nbsp;&nbsp;这里报了一个错，说a未声明
```javascript
  let a = 1;
  if(true){
    console.log( a ) // Uncaught ReferenceError: a is not defined
    let a = 2
  }
```
  &nbsp;&nbsp;这里在判断中先去打印了a再去用let声明a，若没有变量提升的话，应该会打印出上面全局的a=1。也就是说let定义也会有变量提升的现象，let禁止变量提升的原因是因为let的暂时性死区（temploral dead zone）设计：当前作用域顶部到声明位置中间的部分，都是let变量的死区，禁止访问该变量。结论：**let声明变量存在变量提升的现象，但是由于死区TDZ的原因使这个变量无法在声明前访问该变量。**

  
