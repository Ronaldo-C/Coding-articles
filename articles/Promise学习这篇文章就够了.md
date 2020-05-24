## 1.什么是Promise

- 主要用于异步计算。
- 可以将异步操作队列化，按照期望的顺序执行，返回符合预期的结果。
- 可以在对象之间传递和操作**Promise**，帮助我们处理队列。

## 2.Promise的语法
```javascript
new Promise(
  //执行器 executor
  function (resolve, reject) {
    //一段耗时很长的异步操作

    resolve(); //数据处理完成

    reject(); //数据处理失败
  }
).then(function A() {
  //成功，下一步...
  }, function B() {
    //失败， 下一步...
  })
```
## 3.Promise详解
1. **Promise**有三个状态
   - *pending*[待定]初始状态
   - *fulfilled*[实现]操作成功
   - *rejected*[被否决]操作失败
2. **Promise**状态发生改变，就会触发.then()里的响应函数处理后续步骤。
3. **Promise**状态一经改变，就不会在变。
4. **Promise**实例一经创建，执行器立即执行。

## 4.then()和catch()
- .then()接受两个函数作为参数，分别代表*fulfilled*和*rejected*。
- .then()返回一个新的**Promise**实例，所以它可以链式调用。
- 当前面**Promise**状态改变时，.then()根据其最终状态，选择特定的状态相应函数执行。
- 状态响应函数可以返回新的**Promise**，或其他值。
- 如果返回新的**Promise**，那么下一级.then()会在新**Promise**状态改变之后执行。
- 如果返回其它任何值，则会立刻执行下一级.then()。
- 如果有*rejected*函数，则*rejected*函数接受错误，而.catch不会接收到错误。
- .catch返回一个新的**Promise**实例，并且默认是*fulfilled*状态。
- 接收状态为*fulfilled*的参数时，如果该参数不是函数，则会在内部被替换为 `(x) => x`，即原样返回 promise 最终结果的函数。[then()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)
```javascript
console.log('start')
new Promise(function(resolve) {
  console.log('step1') //Promise实例一经创建，执行器立即执行。
  setTimeout(() => {
    resolve('Hello')
  }, 1000)
}).then(value => {
  console.log(value + ' World')
  return 'step2' //如果返回其它任何值，则会立刻执行下一级.then()。
}).then(value => { //.then()返回一个新的Promise实例，并且它的默认状态是fulfilled。
  console.log(value) 
  //如果返回新的Promise，那么下一级.then()会在新Promise状态改变之后执行。
  return new Promise(function(resolve, reject) { 
    setTimeout(() => {
      reject('Err1')
    })
  }, 2000)
}).then(value => { 
  console.log(value + ' fulfilled')
}, err => {
  //如果有rejected函数，则rejected函数接受错误，而catch不会接收到错误。
  console.log(err + ' rejected')
  throw new Error('Err2')
}).catch(err => {
  console.log(err + ' catch')
  return 'step end'
}).then(value => {
  //.catch返回一个新的Promise实例，并且默认是fulfilled状态。
  console.log(value)
})

结果：
start
step1
Hello World
step2
Err1 rejected
Error: Err2 catch
step end
```
## 5.all()
- 批量执行**Promise**(*Promise.all([p1, p2, p3])*)。
- 用于将多个**Promise**实例，包装成一个新的**Promise**实例，返回的实例就是普通的**Promise**。
- 接受一个数组作为参数，数组里可以是**Promise**对象，也可以是别的值，只有**Promise**会等待状态改变。
- 当所有子**Promise**都完成，该**Promise**完成，返回值是全部值的数组。
- 有任何一个失败，该**Promise**失败，返回值是第一个失败子**Promise**结果。
- all常和map一起使用。
```javascript
console.log('start')
Promise.all([1, 2, 3]) //传入三个数字
.then( all => {
  console.log('1: ', all)
  return Promise.all([function () {
    console.log('ooxx')
  }, 'ooxx', 'ooxx']) //传入一个函数，两个字符串
})
.then(all => {
  console.log('2: ', all)
  let p1 = new Promise(resolve => {
    setTimeout(() => {
      resolve('I am p1')
    }, 2000)
  })
  let p2 = new Promise(resolve => {
    setTimeout(() => {
      resolve('I am p2')
    }, 1000)
  })
  return Promise.all([p1, p2]) //传入两个Promise实例
}).then(all => {
  console.log('3: ', all)
  let p1 = new Promise(resolve => {
    setTimeout(() => {
      resolve('I am p1')
    }, 2000)
  })
  let p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('I am p2')
    }, 1500)
  })
  let p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('I am p3')
    }, 1000)
  })
  return Promise.all([p1, p2, p3]) //传入三个Promise实例，其中两个置为rejected状态
}).then(all => {
  console.log('all: ', all)
}).catch(err => {
  console.log('catch: ', err)
})

结果：
start
1:  [ 1, 2, 3 ]
2:  [ [Function], 'ooxx', 'ooxx' ]
3:  [ 'I am p1', 'I am p2' ]
catch:  I am p3
```
## 6.Promise.resolve()
- 所有的then()和catch()都是用Promise.resolve()来处理返回值。
- 参数为空，返回一个状态为*fulfilled*的**Promise**实例。
- 参数是一个跟**Promise**无关的值，同上，不过*fulfilled*响应函数会得到这个值。
- 参数是**Promise**实例，则返回该实例，不做任何修改。
- 参数是thenable，立刻执行它的.then()。
```javascript
console.log('start')
//参数为空，返回一个状态为*fulfilled*的**Promise**实例。
Promise.resolve()
.then((value) =>{
  console.log('1: '+ value)
  //参数是一个跟**Promise**无关的值，同上，不过*fulfilled*响应函数会得到这个值。
  return Promise.resolve('step')
})
.then(value => {
  console.log('2: ' + value)
  //参数是**Promise**实例，则返回该实例，不做任何修改。
  return Promise.resolve(new Promise(resolve => {
    setTimeout(() => {
      resolve('step')
    }, 2000)
  }))
})
.then(value => {
  console.log('3: ' + value)
  //参数是thenable，立刻执行它的.then()。
  return Promise.resolve({
    then() {
      console.log('end')
    }
  })
})

结果：
start
1: undefined
2: step
3: step
end
```
## 7.Promise.reject()
- 返回一个*rejected*的Promise实例。
- 功能类似Promise.resolve()，但是Promise.reject()不认thenable。
## 8.Promise.race()
- 类似Promise.all()，区别在于它有任意一个完成就算完成。
```javascript
console.log('start')
let p1 = new Promise(resolve => {
  setTimeout(() => {
    resolve('p1')
  }, 1000)
})

let p2 = new Promise(resolve => {
  setTimeout(() => {
    resolve('p2')
  }, 2000)
})

Promise.race([p1, p2])
.then((value) => {
  console.log(value)
})

结果：
start
p1
```
## 9.代码案例
- 实现队列
```javascript
//使用.forEach()
function queue(things) {
  let promise = Promise.resolve()
  things.forEach(thing => {
    promise = promise.then(() => {
      return new Promise(resolve => {
        doThing(thing, () => {
          resolve()
        })
      })
    })
  })
  return promise
}

//使用.reduce()
function queue(things) {
  return things.reduce((promise, thing) => {
    return promise.then(() => {
      return new Promise(resolve => {
        doThing(thing, () => {
          resolve()
        })
      })
    })
  }, Promise.resolve())
}

queue(['lots', 'of', 'things', ....])
```
- 异步读取文件
```javascript
const fs = require('fs');
const path = require('path');

function readFileMethods (url) {
    var promise = new Promise((resolve, reject) => {
        fs.readFile(path.join(__dirname, './files/' + url), 'utf8', (err, datastr) => {
            if (err) return reject(err);
            resolve(datastr);
        })
    })
    return promise;
}

readFileMethods('1.txt').then(function (datastr1) {
    console.log(datastr1);
    return readFileMethods('2.txt');
}).then(function (datastr2) {
    console.log(datastr2);
    return readFileMethods('3.txt');
}).then((datastr3) => {
    console.log(datastr3);
}).catch((err) => {
    console.log(err);
})
```
## 10.写在最后

作者水平有限，欢迎大家指出问题，一起交流，么么哒～
