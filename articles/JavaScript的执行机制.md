# JavaScrip的执行机制

## 1.关于javascript

众所周知，javascript是一门**单线程**的语言，但是如果一个任务耗时过长，那么后一个任务也必须等着吗？这显然是不可能的，javascript分为**同步任务**和**异步任务**，js代码自上而下执行时，同步任务则在主线程中执行，遇到异步任务进入Event Table并注册函数，具体看下图：

<img src="../images/Event Loop.png" alt="Event Loop" style="zoom:100%;" />

根据上图我们来整理下Event Loop（事件循环）的步骤：

1. 同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册回调函数。
2. 当指定的事情完成时，Event Table会将这个函数移入Event Queue。
3. 主线程执行栈内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
4. 上述的三步会不断重复，也就是常说的Event Loop(事件循环)。

## 2.Event Queue（事件队列）

Event Queue分为两种，分别是**宏任务队列**和**微任务队列**，Event Loop执行过程中，总是先执行微任务，然后在执行宏任务。

- 宏任务（Task，或者叫MacroTask）
  - 如 包括整体代码script, setTimeout, setInterval, setImmediate, requestAnimationFrame, I/O, UI rendering
- 微任务（MicroTask）
  - 如 process.nextTick, Promises, Object.observe, MutationObserver

<img src="../images/task.png" alt="task" style="zoom:100%;" />

## 3.几个注意点

### setTimeOut

根据以上我们知道`setTimeOut`并不是延迟多少秒立即执行，而是延迟多少秒把它的回调函数注册到Event Queue，一旦主线程执行栈为空，并且前面的任务队列事件执行完（**队列是先进先出，栈是先进后出**），则执行它的回调函数。

```javascript
var t = +new Date()
setTimeout(() => {
  console.log('实际上3秒后执行')
}, 1000)
while(+new Date() - t < 3000) {}
```



## 4.几道面试题

```
// 说出一下代码执行结果，为什么？
console.log('script start'); 
setTimeout(function(){
    console.log('settimeout') 
});
let promise1 = new Promise(function (resolve) {
    console.log('promise1') 
    resolve()
    console.log('promise1 end') 
}).then(function () {
    console.log('promise2')
});
console.log('script end');

答案：
script start
promise1
promise1 end
script end
promise2
settimeout
```



