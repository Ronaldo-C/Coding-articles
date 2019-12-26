## createStore源码解读
```javascript
export default function createStore(reducer, preloadedState, enhancer) {
  if (
    (typeof preloadedState === 'function' && typeof enhancer === 'function') ||
    (typeof enhancer === 'function' && typeof arguments[3] === 'function')
    //检测参数传递错误，传入多个增强函数
  ) {
    throw new Error(
      'It looks like you are passing several store enhancers to ' +
      'createStore(). This is not supported. Instead, compose them ' +
      'together to a single function.'
    )
  }

  //如果第二个参数是函数，第三个参数为空，则交换值
  if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
  }

  //判断是否有增强器函数，如果有返回调用
  if (typeof enhancer !== 'undefined') {
    if (typeof enhancer !== 'function') {
      throw new Error('Expected the enhancer to be a function.')
    }

    return enhancer(createStore)(reducer, preloadedState)
  }

  //判断reducer是否为函数，不是则报错
  if (typeof reducer !== 'function') {
    throw new Error('Expected the reducer to be a function.')
  }

  let currentReducer = reducer
  let currentState = preloadedState
  let currentListeners = []
  let nextListeners = currentListeners
  /*
   * 如果我们在执行所有的订阅事件时，在执行未完成前，对订阅事件数组（nextListeners）进行增删操作，
   * 都会影响到订阅事件的循环执行，所以在每一次的订阅（subscribe）和（unsubscribe）中都会产生一个
   * 新的订阅事件引用数组，再进行相应的操作，避免对正在执行的订阅事件数组产生影响
   */

  let isDispatching = false

  //切断currentListeners和nextListeners之间的引用关系
  function ensureCanMutateNextListeners() {
    if (currentListeners === nextListeners) {
      nextListeners = currentListeners.slice()
    }
  }

  function getState() {
    //如果dispatch正在执行，则抛出错误
    if (isDispatching) {
      throw new Error(
        'You may not call store.getState() while the reducer is executing. ' +
        'The reducer has already received the state as an argument. ' +
        'Pass it down from the top reducer instead of reading it from the store.'
      )
    }

    return currentState
  }

  function subscribe(listener) {
    //参数必须为函数
    if (typeof listener !== 'function') {
      throw new Error('Expected the listener to be a function.')
    }

    //如果dispatch正在执行，则抛出错误
    if (isDispatching) {
      throw new Error(
        'You may not call store.subscribe() while the reducer is executing. ' +
        'If you would like to be notified after the store has been updated, subscribe from a ' +
        'component and invoke store.getState() in the callback to access the latest state. ' +
        'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
      )
    }

    let isSubscribed = true

    ensureCanMutateNextListeners()
    nextListeners.push(listener)

    //返回一个取消监听的函数，闭包
    return function unsubscribe() {
      //如果这个监听器已经被取消了，则直接返回
      if (!isSubscribed) {
        return
      }

      //如果dispatch正在执行，则抛出错误
      if (isDispatching) {
        throw new Error(
          'You may not unsubscribe from a store listener while the reducer is executing. ' +
          'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
        )
      }

      //监听器已取消
      isSubscribed = false

      ensureCanMutateNextListeners()
      const index = nextListeners.indexOf(listener)
      nextListeners.splice(index, 1)
    }
  }

  function dispatch(action) {
    //isPlainObject判断action是否为一个纯粹的对象,如果不是则报错
    if (!isPlainObject(action)) {
      throw new Error(
        'Actions must be plain objects. ' +
        'Use custom middleware for async actions.'
      )
    }

    //所派发的action必须有一个type属性，否则报错
    if (typeof action.type === 'undefined') {
      throw new Error(
        'Actions may not have an undefined "type" property. ' +
        'Have you misspelled a constant?'
      )
    }

    //如果redux正在派发action,则报错
    if (isDispatching) {
      throw new Error('Reducers may not dispatch actions.')
    }

    try {
      //是否派发action状态置为true
      isDispatching = true
      //派发action，重新赋值currentState
      currentState = currentReducer(currentState, action)
    } finally {
      //派发完毕,状态重新变为false
      isDispatching = false
    }

    //重新赋值currentListeners，并建立引用关系
    const listeners = (currentListeners = nextListeners)
    //执行所有的监听函数
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }

    return action
  }

  //动态替换reducer的方法
  function replaceReducer(nextReducer) {
    if (typeof nextReducer !== 'function') {
      throw new Error('Expected the nextReducer to be a function.')
    }

    currentReducer = nextReducer

    dispatch({ type: ActionTypes.REPLACE })
  }

  function observable() {
    const outerSubscribe = subscribe
    return {
      subscribe(observer) {
        if (typeof observer !== 'object' || observer === null) {
          throw new TypeError('Expected the observer to be an object.')
        }

        function observeState() {
          if (observer.next) {
            observer.next(getState())
          }
        }

        observeState()
        const unsubscribe = outerSubscribe(observeState)
        return { unsubscribe }
      },

      [$$observable]() {
        return this
      }
    }
  }

  //初始生成state tree 结构
  dispatch({ type: ActionTypes.INIT })

  return {
    dispatch,
    subscribe,
    getState,
    replaceReducer,
    [$$observable]: observable
  }
}
```