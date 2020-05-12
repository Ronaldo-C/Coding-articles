## CSS系列

### 1.居中的几种方法

- 自身设置相对定位实现居中

  ``` css
  .content {
        width: 600px;
        height: 600px;
        border: 1px solid #cccccc;
  }
  .box {
        background-color: skyblue;
        width: 200px;
        height: 200px;
        position: relative;
        top: 50%;
        margin: 0 auto;
        /* margin-top: -100px; */
        transform: translateY(-50%);
  }
  ```

- 子绝父相实现居中

  ```css
  .content {
        width: 600px;
        height: 600px;
        border: 1px solid #cccccc;
        position: relative;
  }
  .box {
        background-color: skyblue;
        width: 200px;
        height: 200px;
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        margin: auto;
        /* top: 50%;
        left: 50%;
        transform: translate(-50%, -50%); */
  }
  ```

- flex布局实现居中

  ```css
  .content {
        width: 600px;
        height: 600px;
        border: 1px solid #cccccc;
        display: flex;
        justify-content: center;
        align-items: center;
  }
  .box {
        background-color: skyblue;
        width: 200px;
        height: 200px;
  }
  ```

### 2.盒模型

- 标准盒模型：width和height指的是**content**

- IE盒模型：width和height指的是**content+border+padding**

- 如何设置两种盒模型

  ```
  /* 设置当前盒子为 标准盒模型（默认） */
  box-sizing: content-box;
  
  /* 设置当前盒子为 IE盒模型 */
  box-sizing: border-box;
  ```

### 3.BFC（块级格式化上下文）

BFC的原理

- BFC **里面的**元素，在垂直方向，**边距会发生重叠**。
- BFC在页面中是独立的容器，外面的元素不会影响里面的元素，反之亦然。
- **BFC区域不与旁边的`float box`区域重叠**。（可以用来清除浮动带来的影响）。
- 计算BFC的高度时，浮动的子元素也参与计算。

如何生成BFC

- overflow: 不为vidible，可以让属性是 hidden、auto。
- 浮动中：float的属性值不为none。意思是，只要设置了浮动，当前元素就创建了BFC。
- 定位中：只要posiiton的值不是 static或者是relative即可，可以是`absolute`或`fixed`，也就生成了一个BFC。
- display为inline-block, table-cell, table-caption, flex, inline-flex

### 4.em和rem

- em是相对于使用em单位的元素的字体大小，因为子元素可以继承父元素的`font-size`，所以父元素可以影响子元素的em大小，一旦子元素设置显式的`font-size`，那么子元素的em大小，就是基于本身的`font-size`

- 根 html 元素将继承浏览器中设置的字体大小，除非显式设置固定值去覆盖，而rem的值是基于html元素的`font-size`

  ```css
  html {
      font-size: 30px;
  }
  .box {
      font-size: 16px;
      padding: 1rem; //30px
  }
  .content {
      font-size: 20px;
      padding: 2em; //40px
  }
  
  <div class="box">
  	<div class="content"></div>
  </div>
  ```

- rem与px的换算方式

  - 媒体查询
  - 通过屏幕宽度或者dpr进行换算

- dpr：设置像素比 = 物理像素 / 逻辑像素(px)   Ps:在某一方向上，x方向或者y方向

## JS系列

### 1.数组方法

#### 1.使用reduce取出arr数据中的所有奇数，并形成新数组

```javascript
const arr = [1,2,3,4,5,6,7,8,9,10];
let oddArr = arr.reduce((a, b) => (b % 2 !== 0 && a.push(b), a), [])
console.log(oddArr) //[1, 3, 5, 7, 9]
```

### 2.字符串方法

#### 1.截取路由，获取query

```javascript
const url = "https://www.yunlizhihui.com?username=luyon&level=3&part=tPart";
let obj = {}
url.split('?')[1].split('&').forEach(item => {
	let arr = item.split('=')
    obj[arr[0]] = arr[1]
})
console.log(obj)
```

### 3.JS高级知识

#### 1.[JavaScript的执行机制](./JavaScript的执行机制.md)

#### 2.[this取值的四种情况](./JS高级总结.md)

#### 3.[构造函数继承和class继承](./JS高级总结.md)

### 4.React知识

#### 1.componentWillMount和componentDidMount的区别，接口在那个位置调用好些，为什么？

1. `componentWillMount`是在组件初始化之前，`render`之前调用，`componentDidMount`是在组件初始化之后，`render`之后调用。

2. **在`componentWillMount`中执行`this.setState`是不会触发二次渲染的**。

3. `componentWillMount`是服务端渲染唯一会调用的生命周期函数。

4. 在`class`组件中，`constructor`函数和`componentWillMount`是一样的作用,所以你在构造函数里初始化了组件的状态就不必在`componentWillMount`做重复的事情了。

5. `componentWillMount`在`react v16`中已经被标记过时了，新代码不推荐使用这个生命周期函数。

   ```javascript
   //在componentWillMount中执行this.setState是不会触发二次渲染的
   let time = 1
   class A extends React.Component {
     constructor() {
       super()
       this.state = {
         a: 0
       }
     }
   
     UNSAFE_componentWillMount() {
       this.setState({
         a: 0
       })
     }
   
     componentDidMount() {
       this.setState({
         a: 0
       })
     }
   
     render() {
       console.log(time++)
       return (
         <div>返回了</div>
       )
     }
   }
   ReactDOM.render(<A />, document.getElementById('root'))
   ```

   

#### 2.函数组件和类组件的区别

1. 函数组件是一个纯函数，它接受一个`props`对象，返回一个`react`元素；类组件需要去继承`React.Component`并且创建`render`函数返回`react`元素。

2. 在`react16.8`添加了`hooks`，所以状态管理和生命周期钩子不再是它们的区别。

3. 两者在调用方式上不同，函数组件`React`直接调用它，获取`react`元素；类组件将`new`一个新的组件实例，然后调用`render`类方法，获取`react`元素，这也是类组件中`this`会改变的原因。

4. 两者获取渲染时值不同，在`React`中`props`是不可变的，但是`this`是可变的，而且是一直可变的，这也是类组件中`this`的目的，所以通过`this.props`获取值永远都是最新的值。我们可以通过**闭包**的方式，将上一状态的值保存下来。

   ```javascript
   //方式一
   class ProfilePage extends React.Component {
     showMessage(user) {
       alert('Followed ' + user);
     }
   
     handleClick() {
       cosnt {user} = this.props
       setTimeout(this.showMessage.bind(this, user), 3000);
     }
   
     render() {
       return <button onClick={this.handleClick.bind(this)}>Follow</button>
     }
   }
   
   //方式二
   class ProfilePage extends React.Component {
     render() {
       // 获取props
       const props = this.props
   
       // 它们不是类方法
       const showMessage = () => {
           alert('Followed ' + props.user);
       }
   
       const handleClick = () => {
           setTimeout(showMessage, 3000)
       }
   
       return <button onClick={handleClick}>Follow</button>
     }
   }
   ```

#### 3.类组件的事件绑定

1. `class`内部默认就是严格模式，所以`this`的全局指向是`undefined`。

2. 绑定的几种方法
   1. 在`constructor`构造函数中使用`bind`绑定`this`
   2. 在调用的时候使用`bind`绑定`this`
   3. 在调用的时候使用箭头函数绑定`this`
   4. 使用实验性的 `public class fields` 语法

3. 几种方式的优缺点
   1. 方式二、三每一次调用的时候都会生成一个新的方法实例，因此对性能有影响，并且当这个函数作为属性值传入低阶组件的时候，这些组件可能会进行额外的重新渲染，因为每一次都是新的方法实例作为的新的属性传递。
   2. 方式一代码量多一点。
   3. 方式四是实验性语法，需要用babel转译。

4. [react的事件处理](https://zh-hans.reactjs.org/docs/handling-events.html)

```javascript
//用下面的例子可以直观的看出几种绑定方式的差异
class A extends React.Component {
  constructor() {
    super();
    this.state = {
      a: 1
    };
    // this.clickFun = this.clickFun.bind(this);
  }
  clickFun = () => {
    this.setState((s,p) => {
      return {
        a: s.a++
      }
    })
  };
  render() {
    return (
      <div className="App">
        <Child clickFun={() => this.clickFun()} />
        <h1>Hello CodeSandbox</h1>
        <h2>Start editing to see some magic happen!</h2>
      </div>
    );
  }
}

class Child extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    return this.props.clickFun !== nextProps.clickFun
  }
  render() {
    console.log("子组件渲染了");
    return <div onClick={this.props.clickFun}>子组件渲染了</div>;
  }
}

ReactDOM.render(<A />, document.getElementById('root'))
```

#### 4.useEffect模拟componentDidMount

- [react hooks详解](./react hooks详解.md)

## HTTP和浏览器

#### 1.localStorage、sessionStorage、cookie、session几种web数据存储方式对比总结

- [localStorage、sessionStorage、cookie、session几种web数据存储方式对比总结](https://juejin.im/post/5dc2415e6fb9a04a5d586590)

#### 2.OPTIONS请求

- 当发起**跨域请求**时，对那些可能对于服务器数据产生副作用的HTTP请求（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器会首先发起一个OPTIONS请求，即**CORS预检请求**，服务器若接受该跨域请求，浏览器才继续发起正式请求。
- [什么时候会发送options请求](https://juejin.im/post/5cb3eedcf265da038f7734c4#heading-0)

#### 3.跨域

- [前端常见跨域解决方案](https://juejin.im/entry/59b8fb276fb9a00a42474a6f)

#### 4.XSS（跨站脚本攻击）和CSRF（跨站请求伪造）

- [浅说 XSS 和 CSRF](https://juejin.im/entry/5b4b56fd5188251b1a7b2ac1)