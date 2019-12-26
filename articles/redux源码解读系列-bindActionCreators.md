## bindActionCreators源码解读
```javascript
/**
 * 执行bindActionCreator会返回一个匿名函数
 * function() { return dispatch(actionCreator.apply(this, arguments)) }
 * 当外界调用这个函数时，会传入参数，此时arguments就是外界传入的参数的类数组对象
 */
function bindActionCreator(actionCreator, dispatch) {
  return function() {
    return dispatch(actionCreator.apply(this, arguments))
  }
}

export default function bindActionCreators(actionCreators, dispatch) {
  //传入的actionCreators为函数，则返回函数，传入的为对象，则返回对象
  if (typeof actionCreators === 'function') {
    return bindActionCreator(actionCreators, dispatch)
  }

  //如果传入的actionCreators不为object类型，或者为null，则报错 ---- typeof null === 'object'
  if (typeof actionCreators !== 'object' || actionCreators === null) {
    throw new Error(
      `bindActionCreators expected an object or a function, instead received ${
        actionCreators === null ? 'null' : typeof actionCreators
      }. ` +
        `Did you write "import ActionCreators from" instead of "import * as ActionCreators from"?`
    )
  }

  const boundActionCreators = {}
  for (const key in actionCreators) {
    const actionCreator = actionCreators[key]
    //遍历对象，如果值为函数类型，则传入bindActionCreator中，并且生产新的对象
    if (typeof actionCreator === 'function') {
      boundActionCreators[key] = bindActionCreator(actionCreator, dispatch)
    }
  }
  //返回最终生成的对象
  return boundActionCreators
}

```