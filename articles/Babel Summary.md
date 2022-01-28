# Babel学习总结

## Babel的用途

babel 的名字来自巴别塔的典故，是一个 js 转译器，用于 es next、typescript等代码的转换，同时还暴露出了 api 让开发者可以进行特定用途的转换。除此以外，还可以做各种静态分析。

### 转译 esnext、typescript、flow 等到目标环境支持的 js。

### 一些特定用途的代码转换

babel 是一个转译器，暴露了很多 api，用这些 api 可以完成代码到 AST 的 parse，AST 的转换，以及目标代码的生成。

开发者可以用它来来完成一些特定用途的转换，比如函数插桩（函数中自动插入一些代码，例如埋点代码）、自动国际化、default import 转 named import 等。

现在比较流行的小程序转译工具 taro，就是基于 babel 的 api 来实现的。

### 代码的静态分析

对代码进行 parse 之后，能够进行转换，是因为通过 AST 的结构能够理解代码。理解了代码之后，除了进行转换然后生成目标代码之外，也同样可以用于分析代码的信息，进行一些检查。

## Babel的编译流程

### 编译器和转译器

- 编译器 Compiler 是指高级语言（c++、javascript）到低级语言（汇编语言、机器语言）的转换工具。
- 转译器 Transpiler 是指高级语言到高级语言的转换工具。babel 就是一个 Javascript Transpiler。

### Babel的编译流程

- parse：通过 parser （词法分析➡️语法分析）把源码转成抽象语法树（AST）。
- transform：遍历 AST，遍历的过程中处理到不同的 AST 节点会调用注册的相应的 visitor 函数，也就是通过各种 transform 插件对 AST 进行增删改。
- generate：把转换后的 AST 打印成目标代码，并生成 sourcemap。

### Babel的AST

代码中常见的语法在 babel 的 AST 中对应的节点： 标识符 Identifer、各种字面量 xxLiteral、各种语句 xxStatement，各种声明语句 xxDeclaration，各种表达式 xxExpression，以及 Class、Modules、File、Program、Directive、Comment 这些 AST 节点。

AST 节点可能同时有多种类型，确定一种 AST 节点是什么类型主要看它的特点，比如 Statement 的特点是可以单独执行，Expression 的特点是有返回值，所以一些可以单独执行的 Expression 会包一层 ExpressionStatement。

[Babel的AST](https://juejin.cn/book/6946117847848321055/section/6946582409664004133)

[查看AST节点](https://astexplorer.net/)

## Babel的api

- `@babel/parser` 对源码进行 parse，可以通过 plugins、sourceType 等来指定 parse 语法
- `@babel/traverse` 通过 visitor 函数对遍历到的 ast 进行处理，分为 enter 和 exit 两个阶段，具体操作 AST 使用 path 的 api，还可以通过 state 来在遍历过程中传递一些数据
- `@babel/types` 用于创建、判断 AST 节点，提供了 xxx、isXxx、assertXxx 的 api
- `@babel/template` 用于批量创建节点
- `@babel/code-frame` 可以创建友好的报错信息
- `@babel/generator` 打印 AST 成目标代码字符串，支持 comments、minified、sourceMaps 等选项。
- `@babel/core` 基于上面的包来完成 babel 的编译流程，可以从源码字符串、源码文件、AST 开始。

```javascript
const sourceCode = `
    console.log(1);

    function func() {
        console.info(2);
    }

    export default class Clazz {
        say() {
            console.debug(3);
        }
        render() {
            return <div>{console.error(4)}</div>
        }
    }
`;

const targetCalleeName = ['log', 'info', 'error', 'debug'].map(item => `console.${item}`);

//babel plugin
function parametersInsertPlugin({template, types}) {
  return {
    visitor: {
      CallExpression: (path, state) => {
        if (path.node.isNew) {
          return;
        }
        const calleeName = path.get('callee').toString();
        if (targetCalleeName.includes(calleeName)) {
          const {line, column} = path.node.loc.start;
          const newNode = template.expression(`console.log("${state.filename || 'unkown filename'}: (${line}, ${column})")`)();
          newNode.isNew = true

          if (path.findParent(parentPath => parentPath.isJSXElement())) {
            path.replaceWith(types.arrayExpression([newNode, path.node]));
            path.skip();
          } else {
            path.insertBefore(newNode);
          }
        }
      }
    }
  }
}

const {code} = transformSync(sourceCode, {
  plugins: [parametersInsertPlugin],
  parserOpts: {
    sourceType: 'unambiguous',
    plugins: ['jsx']
  }
})

console.log(code)
```

[Babel 的 api](https://juejin.cn/book/6946117847848321055/section/6946578914764390434)

## traverse 的 visitor 和 path

[path的属性](https://juejin.cn/book/6946117847848321055/section/6947689001713139720)

[查某个AST的类型别名](https://babeljs.io/docs/en/babel-types)

[某个别名包含哪些AST类型](https://github.com/babel/babel/blob/main/packages/babel-types/src/ast-types/generated/index.ts#L2489-L2535)

## generate 和 sourcemap

generate 是把 AST 打印成字符串，是一个从根节点递归打印的过程，对不同的 AST 节点做不同的处理，在这个过程中把抽象语法树中省略掉的一些分隔符重新加回来。

[每一种AST节点的打印方式](https://github.com/babel/babel/tree/main/packages/babel-generator/src/generators)

在 generate 的时候可以选择是否生成 sourcemap，那么 sourcemap有哪些作用呢？

- 调试代码时定位到源码
- 线上报错定位到源码

[generate 和 sourcemap](https://juejin.cn/book/6946117847848321055/section/6947678220401508392)

## Babel 插件和 preset

[plugin 和 preset](https://juejin.cn/book/6946117847848321055/section/6946582521672892456)

## Babel 插件的单元测试

Babel插件单侧的几种方式：

- 测试转换后的 AST，是否符合预期
- 测试转换后生成的代码，是否符合预期（如果代码比较多，可以存成快照，进行快照对比）
- 转换后的代码执行一下，测试是否符合预期

[Babel 插件的单元测试](https://juejin.cn/book/6946117847848321055/section/6947733921123532814)

## Babel的内置功能

### plugin

babel 插件需要转换的特性包括 es 标准、proposal（未加入到语言标准的特性），还有 react、flow、typescript 等，所以 **babel 的 plugin，就 @babel/plugin-syntax-xxx, @babel/plugin-transform-xxx、@babel/plugin-proposal-xxx 3种**，分别对应处理上面三种特性。

### preset

- 不同版本的语言标准支持： preset-es2015、preset-es2016 等，babel7 后用 preset-env 代替
- 未加入标准的语言特性的支持： 用于 stage0、stage1、stage2 的特性，babel7 后单独引入 proposal plugin
- 用于 react、jsx、flow 的支持：分别封装相应的插件为 preset-react、preset-jsx、preset-flow，直接使用对应 preset 即可

### helper

helper 分为两种：

- 一种是注入到 AST 的运行时用的全局函数
- 一种是操作 AST 的工具函数，比如变量提升这种通用逻辑

**babel helpers 是用于 babel plugin 逻辑复用的一些工具函数，分为用于注入 runtime 代码的 helper 和用于简化 AST 操作 的 helper两种。第一种都在 @babel/helpers 包里，直接 this.addHelper(name) 就可以引入， 而第二种需要手动引入包和调用 api。**

### babel runtime

babel runtime 里面放运行时加载的模块，会被打包工具打包到产物中，下面放着各种需要在 runtime 使用的函数，包括三部分：regenerator、corejs、helper。

[Babel的内置功能](https://juejin.cn/book/6946117847848321055/section/6954550974698651680)

### @babel/preset-env

我们可以通过插件完成了各种代码（es next、proposal、typescript/flow/jsx...）到 es5 的转换，然后把不同的转换插件封装到不同的 preset （preset-env、preset-typescript、preset-react...）里，而且还把插件内部的公共逻辑抽成 helper 来复用，并且提供了 runtime 包用于注入运行时的 api。这样已经能够达到不同语法的代码转 es5 同时对 api 进行 polyfill 的目标了，但是这些只是针对目标语言版本，而通过 @babel/preset-env 我们可以针对目标环境来按需引入插件。

[@babel/preset-env](https://juejin.cn/book/6946117847848321055/section/6947175741821812768)

