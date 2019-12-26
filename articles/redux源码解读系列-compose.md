## compose源码解读
```javascript
export default function compose(...funcs) {
  /**
   * 如果传入的数组为空，则返回一个函数，查看applyMiddleware源码可知，
   * dispatch = compose(...chain)(store.dispatch)，dispatch = (arg => arg)(store.dispatch)，
   * 这样如果没有传入中间件，dispatch就是redux原生的store.dispatch
   */
  if(funcs.length === 0) {
    return arg => arg
  }

  //如果之传入了一个中间件，则直接返回
  if(funcs.length === 1) {
    return funcs[0]
  }

  /**
   * 理解这段首先要理解reduce方法，可去MDN查看详细语法
   * https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce
   * 假如传入进来的funcs有三个中间件[a, b, c],这三个中间件经过applyMiddleware传入进来，
   * 此时，它们的函数签名应该是(next) => (action) => {...}
   * 以下代码并不可以执行，只是为了方便大家理解
   * 第一次执行：(...args) => ((next) => (action) => {...args}) => (action) => {...} ---> (...args) => a(b(...args)))
   * 第二次执行：(...args) => (((next) => (action) => {...args}) => (action) => {...}) => (action) => {...} ---> (...args) => a(b(c(...args))))
   * 这样做最终返回的是(...args) => a(b(c(...args))))，就像一个套娃一样，每一个中间件的next参数，就是下一个中间件。
   */
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```