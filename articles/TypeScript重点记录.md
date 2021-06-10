# TypeScript重点记录

## `any`和`unknown`区别

- **any 和 unknown 的最大区别是, unknown 是 top type (任何类型都是它的 subtype) , 而 any 即是 top type, 又是 bottom type (它是任何类型的 subtype ) ,这导致 any 基本上就是放弃了任何类型检查.**

- 所有类型都可以分配给`any`和`unknown`，但`unknown`类型的变量只能赋值给`any`或`unknown`，而`any`类型的变量可以赋值给所有类型，并且如果不使用**类型断言**或**类型收缩**来缩小类型，就无法对`unknown`类型进行任何操作。

- [理解TypeScript 中 any 和 unknown](https://zhuanlan.zhihu.com/p/104296850)

  ```typescript
  let a: any = 88
  let b: string = a
  
  let a: unknown = 88
  let b: string = a //Type 'unknown' is not assignable to type 'string'.
  
  let a: any = '88'
  a.toLocaleLowerCase()
  
  let a: any = '88'
  if(typeof a === 'string') a.toLocaleLowerCase()
  
  let a: unknown = '88'
  a.toLocaleLowerCase() //Object is of type 'unknown'.
  ```

## `never`和`void`区别

- `void`表示没有任何类型，`never` 表示永远不存在的值的类型。

- 返回never的函数必须存在无法达到的终点。

  ```typescript
  function warnUser(): void {
      console.log("This is my warning message");
  }
  
  //抛出异常，所以不会有返回值
  function error(message: string): never {
      throw new Error(message);
  }
  
  function infiniteLoop(): never {
      while (true) {
      }
  }
  
  const foo = 123;
  if (foo !== 123) {
      const bar = foo;    // bar: never
  }
  ```

- `nerver`的应用场景

  ```typescript
  //默认代码不应该进入default分支，如果进入了则产生一个编译错误
  interface Foo {
    type: 'foo'
  }
  
  interface Bar {
    type: 'bar'
  }
  
  type All = Foo | Bar
  
  function handleValue(val: All) {
    switch (val.type) {
      case 'foo':
        // 这里 val 被收窄为 Foo
        break
      case 'bar':
        // val 在这里是 Bar
        break
      default:
        // val 在这里是 never
        const exhaustiveCheck: never = val
        break
    }
  }
  ```

## `Enum`

- `Enum`分为数字枚举、字符串枚举和数字，字符串组合的异构枚举。
- 通过`const`修饰符的枚举会在编译阶段删掉。
- 和对象比较，枚举最大的优势是可以直接当成类型使用。

```typescript
//异构枚举
enum Direction {
  Up = "UP",
  Down = 0,
  Left = "LEFT",
  Right = "RIGHT",
}


"use strict";
var Direction;
(function (Direction) {
    Direction["Up"] = "UP";
    Direction[Direction["Down"] = 0] = "Down";
    Direction["Left"] = "LEFT";
    Direction["Right"] = "RIGHT";
})(Direction || (Direction = {}));
```

[Enums](https://www.typescriptlang.org/docs/handbook/enums.html#const-enums)

## `keyof`

**获取类型的键值**

```typescript
type Point = { x: number; y: number };
type P = keyof Point; // type p = x | y

type Mapish = { [k: string]: boolean };
type M = keyof Mapish; // type M = string | number  JS对象的键总是被强制转化为string，所以obj['0']和obj[0]是一样的
```

## `typeof`

**获取变量的类型**

```typescript
let obj = { x: 66, y: '88' }
type a = typeof obj
// type a = { x: number, y: 'string' }
```

## Indexed Access Types(索引访问类型)

**通过索引访问类型**

```typescript
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"]; //type Age = number
type I1 = Person["age" | "name"]; //type I1 = string | number
type I2 = Person[keyof Person]; //type I2 = string | number | boolean

const person = {
    name: 'Name',
    age: 'Age'
} as const

type valueOf<T> = T[keyof T]

type person = valueOf<typeof person> //type person = Name | Age
```

## `extends`

- 类型继承
- 在范型中使用是类型约束

```typescript
type t1 = {
    a: string
}
interface t2 extends t1 {
    b: number
}
let obj: t2 = {
    a: '33',
    b: 22
}

//类型K必须包含在类型T中
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

## `in`

- **`in` 关键词的作用主要是做类型的映射，遍历已有接口的 key 或者是遍历联合类型。**
- **检查一个对象上是否存在一个属性，类型保护用**

[typescript高级类型](https://segmentfault.com/a/1190000023800536)

## `is`

类型保护，缩小类型，和`typeof`、`instanceof`、`in`关键词作用一致，缩小一个代码保护块中的类型。

```typescript
function isUndefined(val: any): val is undefined{
    return typeof test === "undefined";
}
function example(foo?: string){
    if(isUndefined(foo)){ //通过 val is undefined 告诉了TS在这个保护块中，foo为undefined类型
        console.log("it is a undefined");
    } else {
        console.log(foo.split(','))
    }
}
```

[`is`keyword](https://stackoverflow.com/questions/40081332/what-does-the-is-keyword-do-in-typescript)

## `type`和`interface`的区别

- `interface`是通过继承的方式来扩展的，`type`是通过 & 来扩展的。
- `interface`可以自动合并，而`type`不行。

[differences-between-type-aliases-and-interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)

[理解 Typescript 的 type 和 interface](https://zhuanlan.zhihu.com/p/351213183)

## `React.FC<>`

`React.FC<>`是`react`提供给函数组件的一个范型，它是`React.FunctionComponent<>`的简写，它在定义里就已经规定好了`children`,`defaultProps`等`react`提供的默认属性的类型和函数的返回值。

```typescript
type FC<P = {}> = FunctionComponent<P>;

interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
	propTypes?: WeakValidationMap<P>;
	contextTypes?: ValidationMap<any>;
	defaultProps?: Partial<P>;
	displayName?: string;
}
```

`React.FC`的几个缺点：

- 定义的是一个`function`的类型，而不是参数的类型。

- 总是带有`children`属性，导致一些组件不需要`children`，传了也不会报错。可以使用`PropsWithChildren<>`来自定义是否需要`children`。

- 切断了与`defaultProps`的联系，如果使用了`React.FC<>`，即使定义了组件的`defaultProps`，使用组件时仍需要传入必传参数，否则`typescript`会报错；如果不使用`React.FC<>`并定义了组件的`defaultProps`，那么使用组件时，不必传入必传参数。

  ```typescript
  //定义的是一个函数的类型
  const Greeting:FC<GreetingProps> = function({ name }) {
    return <h1>Hello {name}</h1>
  }
  
  //此时使用Children组件不会报错，如果使用React.FC<>定义类型，那么就会报错
  const Children = (props:{name: string, age: number}) => {
    console.log('children', props)
  
    return (
      <div>{props.name}</div>
    )
  }
  Children.defaultProps = {
    name: '123',
    age:1
  }
  ```

[Why I don't use React.FC](https://fettblog.eu/typescript-react-why-i-dont-use-react-fc/)
