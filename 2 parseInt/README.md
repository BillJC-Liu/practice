# ['1', '2', '3'].map(parseInt) what & why ?

### 先跑一下代码看一看
```javascript
   ['1', '2', '3'].map(parseInt)  // [1, NaN , NaN]
```
* 首先看一下map函数，map函数的第一个参数callback
  .map( function( currentValue[, index [, array]] ){ } )

  callback一共可以接受3个参数，第一个参数是当前要被处理的元素，第二个参数是当前要被处理元素的索引。
* parseInt 是用来解析字符串的，使字符串成为指定基数的整数。<br />
  parseInt( string, radix ) <br />
  接受两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数，且在2~36之间。 <br />
  在此方法中，parseInt并未显式的传入值，实际上是 <br />
  parseInt( currentValue, index ); // 第一参数是当前被处理的元素，第二个是索引值做基数值 <br />
  1.parseInt( "1", 0 ); // 基数为0，string参数不以0x或者0开头，按照10为基数来处理，则返回1 <br />
  2.parseInt( "2", 1 ); // 基数为1，最大值小于2，解析错误，返回NaN <br />
  3.parseInt( "3", 2 ); // 基数为2，最大值小于3，解析错误，返回NaN <br />
  map返回的是一个数组，所以返回 [1, NaN, NaN] <br />
  这里需要注意的是，parseInt("5" , 4); 基数4在[2,36]的区间中，但输出的仍是NaN。
