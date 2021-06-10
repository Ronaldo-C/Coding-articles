# 深入浅出React相关技术栈

## Redux

### 什么是Redux

`redux`是一个严格的单向数据流，顶层`store`是一个对象树，包裹了全局所有的`state`，并且全局只能有一个`store`，`state`是只读的，只能通过调用`dispatch`函数，并传递`action`参数，来调用`reducer`纯函数进行修改。

### Redux相关API

#### `createStore`

调用的时候也会调用一次`dispatch`函数，生成初始默认的`store`对象树，**切记，全局只可调用一次，不要创建多个`store`**。

- 入参：
  - `reducer`：**必传参数**，根`reducer`，可以使用`combineReducers`来集合所有的`reducer`。
  - `preloadedState`：初始化的`store`对象树，如果不传，则使用`reducer`默认的`state`。
  - `enhancer`：是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。（`redux-devtools-extension`包返回的`composeWithDevTools`就是一个`enhancer`函数，它帮助我们可以在浏览器中使用`Redux DevTools`插件）
- 出参：
  - `dispatch`：分发`action`，返回值是传入的`action`。这是触发`state`变化的惟一途径。（`action`本质上是一个`javascript`对象，必须有一个字符串`type`字段表示要执行的动作，其它字段可以自己决定）
  - `subscribe`：监听函数，返回一个`unsubscribe`取消监听函数，监听函数会在每次`dispatch`函数执行时，更新完`state`后执行。
  - `getState`：返回应用当前的`state`树。
  - `replaceReducer`：替换 store 当前用来计算 state 的 reducer。

```react
const store = createStore(rootReducer, {
  todos: [{
    id: 1,
    text: 'anything',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
})

console.log(store.getState())
/**
{
  todos: [{
    id: 1,
    text: 'anything',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
**/
```

[redux源码解读系列-createStore](./redux源码解读系列-createStore.md)

#### `combineReducers`

入参是一个对象，它的`value`为`reducer`，`key`为对应`reducer`返回的`state`的命名。出参是一个调用入参`reducers`对象内所有`reducer`的`reducer`，并且构造一个与 `reducers` 对象结构相同的 state 对象。

```react
import todos from './todos'
import visibilityFilter from './visibilityFilter'

export default combineReducers({
  todosKey: todos,
  visibilityFilterKey: visibilityFilter
})
/**
{
  todosKey: [{
    id: 1,
    text: 'anything',
    completed: false
  }],
  visibilityFilterKey: 'SHOW_COMPLETED'
}
**/
```

[redux源码解读系列-combineReducers](./redux源码解读系列-combineReducers.md)

#### `bindActionCreators`

把一个`value`为不同 `action creator`的对象，转成拥有同名`key`的对象。同时使用`dispatch` 对每个`action creator`进行包装，以便可以直接调用它们。如果传入的是一个`action creator`函数，则返回的也是函数。（**`action creator`就是一个生成`action`的函数**）

- 入参：
  - `action creators`：一个`action creator`，或者一个`value`是`action creator`的对象。
  - `dispatch`
- 出参：一个与原对象类似的对象，只不过这个对象的`value`都是会直接`dispatch`原`action creator`返回的结果的函数。如果传入一个单独的函数作为 `actionCreators`，那么返回的结果也是一个单独的函数。

```react
const mapDispatchToProps = (dispatch, ownProps) => {
  const setVisibilityFilterFun = bindActionCreators(setVisibilityFilter, dispatch)
  return {
    onClick: () => setVisibilityFilterFun(ownProps.filter)
  }
}

//actions.js
export const actions = {
  setInputText: text => ({
    type: types.SET_INPUT_TEXT,
    text
  }),
  clearInputText: () => ({
    type: types.CLEAR_INPUT_TEXT
  }),
  addHistoryKeyword: keywordId => ({
    type: types.ADD_HISTORY_KEYWORD,
    text: keywordId
  }),
  clearHistoryKeywords: () => ({
    type: types.CLEAR_HISTORY_KEYWORDS
  })
}

//container.js
import { actions as searchActions} from 'actions.js'
import { bindActionCreators } from 'redux'

class Search extends Component {
  render() {
    return (
    	// ...something
    )
  }

  componentWillUnmount() {
    const { clearInputText } = this.props.searchActions
    clearInputText()
  }

  handleChangeInput = text => {
    const { setInputText } = this.props.searchActions
    setInputText(text)
  }

  handleClearInput = () => {
    const { clearInputText } = this.props.searchActions
    clearInputText()
  }

  handleClickItem = (item) => {
    const { setInputText, addHistoryKeyword } = this.props.searchActions
    setInputText(item.keyword)
    addHistoryKeyword(item.id)
  }

  handleClearHistory = () => {
    const { clearHistoryKeywords } = this.props.searchActions;
    clearHistoryKeywords();
  }
}


const mapStateToProps = (state, props) => {
  return {
    //...something
  }
}

const mapDispatchToProps = dispatch => {
  return {
    searchActions: bindActionCreators(searchActions, dispatch)
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(Search);
```

[redux源码解读系列-bindActionCreators](./redux源码解读系列-bindActionCreators.md)

#### `applyMiddleware`

- 使用包含自定义功能的`middleware`来扩展`Redux`，返回的是一个`enhancer`函数。
- `middleware`函数签名是`({ getState, dispatch }) => next => action`。
- `applyMiddleware`中的`middleware`按传入的顺序，从左到右执行。

```react
//store.dispatch表示重新执行中间件，如果不限制执行，会导致栈溢出
const logMiddleware = store => next => action => {
  console.log(store.getState())
  next(action)
  console.log(store.getState())
}

const store = createStore(rootReducer, applyMiddleware(logMiddleware))
```

[redux源码解读系列-applyMiddleware](./redux源码解读系列-applyMiddleware.md)

## `react-redux`

#### `connect`

`connect()`的作用是让`react component`连接`redux store`，它可以使连接的`component`从`store`中获取数据，和发送`action`。

- 入参：
  - `mapStateToProps?: Function`：
    - `function mapStateToProps(state, ownProps?)`。`state`是调用`store.getState()`返回的值；`ownProps`是父级组件传递进来的`props`。
    - `mapStateToProps`会在每次`store state`改变时或`ownProps`改变时调用；组件则会在传递进来的`props`改变时重新渲染。
  - `mapDispatchToProps?: Function | Object`：
    - 如果为空，则组件默认接受`dispatch`作为`props`。
    - 如果是函数，签名是：`function mapDispatchToProps(dispatch, ownProps){}`；可以和`redux`返回的`bindActionCreators`结合使用。
    - 如果是对象，会用`dispatch`包裹传递进来的`action creator`，类似`bindActionCreators`。
  - `mergeProps?: Function`
  - `options?: Object`

