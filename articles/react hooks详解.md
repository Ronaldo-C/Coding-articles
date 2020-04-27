# react hooks详解

## 1.useState

1. `useState`和`this.state`的六不同

   - `useState`不会合并属性，每次更新会直接替换旧的`state`，而`this.setState`会帮我们合并属性。

   - `useState`更新时，如果与当前 state 完全相同，则不会进行重新渲染，而`this.setState`每次调用都会重新渲染。（**当`useState`更新对象时，必须要改变引用地址，不然react会认为`state`没有变化，只会替换`state`，但不会重新渲染**）

   - `useState`初始值可以接受一个函数，并且只在初始渲染时被调用，而`this.state`不行。

   - 因为`this.setState`不是立即更新`state`，所以它可以接受两个参数，第一个为要更新的`state`，第二个为更新完之后的回调函数，当然也可以使用`componentDidUpdate`来进行监听。

   - `useState`可以多次调用声明，而`this.state`只可以声明一次。

   - `useState`初始值可以是任何数据类型，而`this.state`只能是对象。

     ```javascript
     function App() {
       //初始值可以是任何类型值，并且可以接收一个函数
       let [ n, setN ] = useState(0)
       let [ m, setM ] = useState(() => {
           return {
               a: 0,
               b: 0
           }
       })
       //setN会立即更新
       const clickFunN = () => {
         setN(++n)
         console.log(n)
       }
     
       //引用地址不变，不会重新渲染，只会替换state
       function clickFunM() {
         // m.a = ++m.a
         // setM(m)
         setM((m) => { //可以接收一个函数，形参为当前state
           return { a: ++m.a }
         })
       }
       return (
         <div>
           <p>n:{n}</p>
           <p>m.a:{m.a}</p>
           <p>m.b:{m.b}</p>
           <p>
             <button onClick={clickFunN}>n</button>
             <button onClick={clickFunM}>m</button>
           </p>
         </div>
       )
     }
     ```

     

2. `useState`和`this.state`的一相同

   - `useState`返回的`setState`和`this.setState`一样可以接受一个函数当入参，但是`setState`只有一个形参，代表当前的`state`，而`this.setState`有两个形参，分表代表当前的`state`和`prop`。

3. 简单实现一个`useState`

   ```javascript
   //自定义实现useState
   let _state = []
   let _index = 0
   
   function myUseState(initalValue) {
     let currentIndex = _index;
     _index += 1;
     _state[currentIndex] = _state[currentIndex] || initalValue;
     const setState = newState => {
       _state[currentIndex] = newState;
       render()
     }
     return [_state[currentIndex], setState];
   }
   
   function render() {
     _index = 0
     ReactDOM.render(<App />, document.getElementById('root'));
   }
   ```

4. 两种情况

   ```javascript
   //第一种 
   //因为闭包原因，n会读取上一次渲染的n值，不会获取最新的n值
   import React from "react";
   import ReactDOM from "react-dom";
   const rootElement = document.getElementById("root");
   
   function App() {
     const [n, setN] = React.useState(0);
     const log = () => setTimeout(() => console.log(`n: ${n}`), 3000);
     return (
       <div className="App">
         <p>{n}</p>
         <p>
           <button onClick={() => setN(n + 1)}>+1</button>
           <button onClick={log}>log</button>
         </p>
       </div>
     );
   }
   
   ReactDOM.render(<App />, rootElement);
   
   //第二种 
   //因为闭包原因，执行两遍setN其实只是执行了最后一个setN，但如果setN入参是函数，使用形参的方式，则两遍setN都会执行。
   import React, {useState} from "react";
   import ReactDOM from "react-dom";
   
   function App() {
     const [n, setN] = useState(0)
     const onClick = ()=>{
       setN(n+1)
       setN(n+1) // 你会发现 n 不能加 2
       // setN(i=>i+1)
       // setN(i=>i+1)
     }
     return (
       <div className="App">
         <h1>n: {n}</h1>
          
         <button onClick={onClick}>+2</button>
       </div>
     );
   }
   
   const rootElement = document.getElementById("root");
   ReactDOM.render(<App />, rootElement);
   ```

   

## 2.useEffect

1. 模拟`class`组件的生命周期

   - `componentDidMount`

     ```javascript
     useEffect(() => {
         console.log('第二个参数为[]')
     }, [])
     ```

   - `componentDidUpdate`

     ```javascript
     useEffect(() => {
         console.log('没有第二个参数')
       }) //每次更行都会执行
     useEffect(() => {
         console.log('第二个参数为[n]')
     }, [n]) //n变化才会执行
     ```

   - `componentWillUnmount`

     ```javascript
     useEffect(() => {
         //组件卸载时将会执行
         return () => {
             console.log('clean 第二个参数为[]')
         }
     }, [])
     ```

2. 与`class`组件的生命周期函数的两点不同

   1. 每次更新都会依次运行`useEffect`，并且会在调用一个新的`useEffect `之前对前一个`useEffect `进行清理。

      ```javascript
      function App() {
        let [n, setN] = useState(0)
        const clickFunN = () => {
          setN(i => ++i)
        }
        useEffect(() => {
          console.log('没有第二个参数')
          return () => {
            console.log('clean 没有第二个参数')
          }
        })
        useEffect(() => {
          console.log('第二个参数为[]')
          return () => {
            console.log('clean 第二个参数为[]')
          }
        }, [])
        useEffect(() => {
          console.log('第二个参数为[n]')
          return () => {
            console.log('clean 第二个参数为[n]')
          }
        }, [n])
      
        return (
          <div>
            <p id="val">value:{n}</p>
            <p>
              <button onClick={clickFunN}>n</button>
            </p>
          </div>
        )
      }
      ```

   2. `useEffect`是在浏览器**绘制之后**执行的，而`componentDidMount`、`componentDidUpdate`是在浏览器**绘制之前**执行的，可以使用`useLayoutEffect`，它的执行时机和`componentDidMount`、`componentDidUpdate`是一样的，并且函数签名和`useEffect`一样，但是尽可能使用`useEffect` 以避免阻塞视觉更新。

      ```javascript
      //使用useEffect可以发现，页面刷新时300会出现一瞬间，而useLayoutEffect不会，因为它在浏览器绘制之前就执行了。
      function App() {
        let [n, setN] = useState(300)
      
        // useEffect(() => {
        //   document.getElementById('val').innerText = 'value:1000'
        // })
      
        useLayoutEffect(() => {
          document.getElementById('val').innerText = 'value:1000'
        })
      
        return (
          <div>
            <p id="val">value:{n}</p>
          </div>
        )
      }
      ```


## 3.useMemo和useCallback

- `useCallback`是`useMemo`的语法糖，`useCallback`直接返回目标函数，而`useMemo`要传一个函数，然后函数再返回目标函数。

- 原理都是返回一个函数，只有当依赖变化才会重新生成函数的引用。

  ```javascript
  function App() {
    let [n, setN] = useState(0)
    let [m, setM] = useState(0)
  
    const setNFun = useCallback(() => {
      setN(++n)
      console.log(m)
    }, [n, m]) //如果依赖中不加m，可以发现打印的m值不会跟随更新
  
    const setMFun = useMemo(() => () => {
      setM(++m)
    }, [m])
  
    return (
      <div>
        <p id="val">父组件n：{n}</p>
        <button onClick={setNFun}>+1n</button>
        <MemoChild propFun={setMFun} m={m}/>
      </div>
    )
  }
  
  
  function Child(props) {
    console.log('子组件渲染了')
    return (
      <div>
        <p>子组件m：{props.m}</p>
        <button onClick={props.propFun}>+1m</button>
      </div>
    )
  }
  const MemoChild = React.memo(Child)
  ```

  