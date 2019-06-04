# 异步笔试题
```javascript
  async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
  }

  async function async2() {
      console.log('async2');
  }

  console.log('script start');

  setTimeout(function() {
      console.log('setTimeout');
  }, 0)

  async1();

  new Promise(function(resolve) {
      console.log('promise1');
      resolve();
  }).then(function() {
      console.log('promise2');
  });

  console.log('script end');

  /*
  正确的输出的结果：
    script start
    async1 start
    async2
    promise1
    script end
    async1 end
    promise2
    setTimeout
  */
```
这道题主要考察的是时间循环中函数执行顺序的问题，其中包括`async`，`await`，`setTimeout`，`Promise`函数。下面来说一下本题中涉及的知识点。
### 任务队列
首先我们需要要明白一下几件事情：
* JS分为同步任务和异步任务
* 同步任务都在主线程上执行，形成一个执行栈
* 主线程之外，时间出发线程管理者一个任务队列，只要异步任务有了运行结果，就在我任务队列之中放置一个事件。
* 一旦执行栈中的虽偶有同步任务执行完毕（此时JS引擎空闲），系统就会读取任务队列，将可运行的异步任务添加到可执行栈中，开始执行。
根据规范，事件循环是通过任务队列的机制来进行协调的。一个Event Loop中，可以有一个或者多个任务队列（task queue），一个任务队列便是一系列有序任务（task）的集合；**每个任务都有一个任务源（task source），源自同一个任务源的task必须放到同一个任务队列，从不同源来的则被添加到不同队列。**setTimeout/Promise等API便是任务源，而进入任务队列的是他们制定的具体执行任务。
![任务队列图](/1.png);

### 宏任务
(macro)task(称之为宏任务)，可以理解是每次执行栈执行的代码就是一个宏任务(包括每次从事件队列中获取一个事件回调并放到执行栈中执行)。
浏览器为了能够使得JS内部（macro）task与DOM任务能够有序的执行，**会在一个（macro）task执行结束后，在下一个（macro）task执行开始前，对页面进行重新渲染，流程如下：**
>（macro）task --> 渲染 ---> （macro）task --> ...
（macro）task主要包含：script（整体代码）、setTimeout、setInterval、I/O、UI交互事件、postMessage、MessageChannel、seetImmediate（Node.js环境）

### 微任务
micritask（又称之为微任务），**可以理解是在当前task执行结束后立即执行的任务。**也就是说，在当前task任务后，下一个task之前，在渲染之前。<br/>
所以它的响应速度相比setTimeout（setTimeout是task任务源）会更快，因为无需等待渲染。也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）。<br/>
microtask主要包含：Promise.then 、MutaitionObserver、process.nextTick(Node.js环境)<br/>

### 运行机制
在事件循环中，每进行一次循环操作称为tick，每一次tick的任务处理模型是比较复杂的，但关键步骤如下：
* 执行一个红任务(栈中没有就从事件队列中获取)
* 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
* 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
* 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
* 渲染完毕后，JS线程继续接管，开始下一个宏任务(从事件队列中获取)
流程图如下：
![运行流程](/2.jpg)

### Promise和async中的立即执行
我们知道Peomise中的异步体现在`then`和`catch`中，所以写在Promise中的代码是被当做同步任务立即执行的。而在async/await中，在出现await出现之前，其中的代码也是立即执行的。那么出现await时候发生了什么呢？

### await做了什么
从字面意思上看await就是等待的意思，await等待的是一个表达式，这个表达式的返回值可以是一个promise对象也可以是其他值。<br/>
很多人以为await会一直等待值后的表达是执行完之后才会继续执行后面的代码，**实际上awaot是一个让出线程的标志，await后面的表达式会先执行一遍，将await以下的代码加入到microtask（微任务）中，然后就会跳出整个async函数来执行后面的代码**
因为async await本身就是promise+generator的语法糖，所以await后面的代码是microtask。
```javascript
  asymc function async1(){
    console.log('async1 start');
    await async2()
    console.log('async end');
  }
```
等价于
```javascript
  async function async1(){
    console.log('async1 start');
    Promise.resolve(async2()).then(()=>{
      console.log('async1 end')
    }))
  }
```
## 回到题目
* 1.首先，事件循环从宏任务(macrotask)队列开始，这个时候，宏任务队列中，只有一个script（整体代码）任务；当遇到任务源task source时，则会分发任务到对应的任务队列中去。

* 2.然后看到首先定义两个async函数，接着往下走，遇到console语句，输出 `script start`，输出之后，script任务继续往下执行，遇到setTimeout宏任务源，则会将其任务分发到对应的队列中：此时macrotask队列中就存在两个任务，一个是script(整体代码)正在运行，另一个是 setTimeout宏任务源。

* 3.script任务继续往下走，执行到async1()函数，前面讲过async函数中在await之前都是同步任务，是立即执行的，所有输出`async1 start`，然后执行await 后面的代码，执行async2方法，而await下面的代码加入带microtask队列中

* 4.script任务继续往下执行，遇到Promise实例，由于Promise中的函数是立即执行的，而后续的`.then`则会被分到microtask队里中。所以输出`promise1`，然后执行resole，将`promise2`分配到对应的队列中。此时microtask中有两个任务，`async1`函数中await下面的代码和这里的`promise2`

* 5.script任务继续往下执行个，执行到最后一句，打印`script end`。到此，全局任务（script）到此执行完毕。<br/>
根据上面讲到的，每次执行完一个宏任务后，会去检查是否有microtask，如果有，则执行microtask直到清空微任务队列中的任务。然后开始下一个宏任务的执行。
因此，script任务执行完毕之后，开始查找请求微任务队列。此时，微任务中有两个任务，依次输出`async1 end`、`promise2`

* 第二轮循环依旧从宏任务队列开始。此时宏任务中只有一个`setTimeout`，取出直接执行即可，至此整个流程结束。
所以整个过程输出：
```javascript
  /*
    script start
    async1 start
    async2
    promise1 
    script end 
    async1 end 
    promise2
    setTimeout
  */
```




























