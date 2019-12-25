# React高阶组件

## 1. 高阶组件介绍

### 1.高阶函数基本概念

 - 函数可以作为参数被传递
 - 函数可以作为返回值输出

### 2. 高阶组件基本概念

- 高阶组件就是接受一个组件作为参数并返回一个新组件的函数
- 高阶组件是一个函数，并不是组件

### 3. 为什么需要高阶组件

- 多个组件都需要某个相同的功能，使用高阶组件减少重复实现

## 2. 高阶组件实现

### 1. 高阶组件编写

1. 实现一个普通组件
2. 将普通组件使用函数包裹

### 2.使用高阶组件

1. higherOrderComponent(WrappedComponent)
2. @higherOrderComponent(装饰器)

## 3. 高阶组件应用 ##

- 代理方式的高阶组件
  
  - 返回的新组件类直接继承自React.Component类，新组件扮演的角色传入参数组件的一个代理，在新组件的render函数中，将被包裹组件渲染出来，除了高阶组件自己要做的工作，其余功能全都转手给了被包裹的组件。
    - 操作Props
    - 抽取状态
    - 访问ref
    - 包装组件
  
  ```
  export default () => WrappedComponent => class A extends Component {
  	render() {
  		const {...otherProps} = this.props
  		return <WrappedComponent {...otherProps} />
  	}
  }
  ```
  
  
  
- 继承方式的高阶组件
  
  - 采用继承关联作为参数的组件和返回的组件，加入传入的组件参数是WrappedComponent，那么返回的组件就直接继承自WrappedComponent。
    - 操作Props
    - 操作生命周期函数
  
  ```
  export default () => WrappedComponent => class A extends WrappedComponent {
  	render() {
  		const {user, ...otherProps} = this.props
  		this.props = otherProps
  		return super.render()
  	}
  }
  ```
  
  
  
- 高阶组件显示名

  - displayName