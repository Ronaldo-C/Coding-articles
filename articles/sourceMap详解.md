## 什么是source map？

- Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。

## webpackd devtool配置5个关键字说明。

- eval： 使用eval包裹模块代码
- source-map： 产生.map文件
- cheap：不包含列信息，也不包含loader的sourcemap
- module： 包含loader的sourcemap（比如jsx to js ，babel的sourcemap）
- inline：将.map作为DataURI嵌入，不单独生成.map文件。

## eval模式

- 使用eval将webpack中每个模块包裹，然后在模块末尾添加模块来源//# souceURL， 依靠souceURL找到原始代码的位置。包含eval关键字的配置项并不单独产生.map文件，依靠sourceURL来定位原始代码。

```javascript
eval("consoee.log(666)\n\n//# sourceURL=webpack:///./index.js?");
```

## source map模式

- 都会产生一个.map文件，该文件保存有原始代码与运行代码的映射关系， 浏览器可以通过它找到原始代码的位置。（注：包含inline关键字的配置项也会产生.map文件，但是这个map文件是经过base64编码作为DataURI嵌入）。使用.map文件的方式来定位。
- eval-source-map是eval和source-map的组合，可知使用eavl语句包括模块，也产生了.map文件。webpack将.map文件作为DataURI替换eval模式中末尾的//# souceURL。

## source map文件基本格式。

```javascript
{
　　　　version : 3, //Source map的版本，目前为3。
　　　　file: "out.js", //转换后的文件名。
　　　　sourceRoot : "", //转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。
　　　　sources: ["foo.js", "bar.js"], //转换前的文件。该项是一个数组，表示可能存在多个文件合并。
　　　　names: ["src", "maps", "are", "fun"], //转换前的所有变量名和属性名。
　　　　mappings: "AAgBC,SAAQ,CAAEA" //记录位置信息的字符串，分成三层。
　　}
```
## mappings属性

- 第一层是行对应，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。
- 第二层是位置对应，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。
- 第三层是位置转换，以VLQ编码表示，代表该位置对应的转换前的源码位置。

## webpack devtool 常用配置

- development模式：cheap-module-eval-source-map
- production模式：cheap-module-source-map

## 推荐阅读博客

- [Webpack中sourcemap的配置](http://www.cnblogs.com/axl234/p/6500534.html)
- [JavaScript Source Map 详解](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)