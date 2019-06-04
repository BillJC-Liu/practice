# call和apply的区别和性能对比
* Function.prototype.apply和Function.proptype.call作用是一样的，区别在于传入的参数不同。
* call和apply的第一个参数都是指向函数体内的this指向。
* 第二个参数不同，call的第二个参数个数不定的，是一个参数列表。而apply的第二个参数是一个数组或类数组。
* call的性能比apply要好，平常可以多用call。