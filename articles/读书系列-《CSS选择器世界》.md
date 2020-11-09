# CSS选择器世界

## 第1章 概述

### 选择器、选择符、伪类和伪元素

CSS选择器可以分为4类，即选择器、选择符、伪类和伪元素。

- **选择器**：指的就是平常使用的CSS声明块前面的标签、类名等。例如：

  ```css
  .container { background-color: olive; }
  body { font: menu; }
  ```

- **选择符**：CSS选择器世界中的选择符有5个

  ```css
  /* 后代关系 */
  .container img { object-fit: cover; }
  /* 父子关系 */
  ol > li { margin: .5em 0; }
  /* 相邻兄弟关系 */
  button + button { margin-left: 10px; }
  /* 兄弟关系 */
  button ~ button { margin-left: 10px; }
  /* 列关系 */
  .col || td { background-color: skyblue; }
  ```

- **伪类**：伪类的特征是其前面会有一个冒号（:），通常与浏览器行为和用户行为相关联。

  ```css
  a:hover { color: darkblue; }
  ```

- **伪元素**：伪元素的特征是其前面会有两个冒号（::），常见的有：`::before`，`::after`，`::first-letter`，`::first-line`等。

## 第2章 CSS选择器的优先级

### CSS选择器优先级规则概览

CSS优先级分为0~5这6个等级

- **0级**：通配选择器、选择符和逻辑组合伪类（只有逻辑伪类的优先级是0，其它伪类优先级是2）。

  ```css
  /* 通配选择器 */
  * { color: #000; }
  /* 选择符：+、>、~、空格和|| */
  /* 逻辑组合伪类：:not()、:is()和:where等 */
  :not() {}
  ```

- **1级**：标签选择器

  ```css
  body { color: #333; }
  ```

- **2级**：类选择器、属性选择器和伪类

  ```css
  .foo { color: #666; }
  [foo] { color: #666; }
  :hover { color: #333; }
  ```

- **3级**：ID选择器

  ```css
  #foo { color: #999; }
  ```

- **4级**：`style`属性内联

  ```css
  <span style="color: #ccc;">优先级</span>
  ```

- **5级**：`!important`

  ```css
  .foo { color: #fff !important; }
  ```

### CSS选择器优先级注意事项

- CSS选择器优先级与DOM元素的层级位置没有任何关系，并且遵循‘后来居上’的原则。

  ```css
  body .foo { color: red; }
  html .foo { color: blue; }
  /* .foo颜色为blue */
  ```

- 增加CSS选择器优先级：1.重复选择自身；2.借助必然会存在的属性选择器

  ```css
  .foo.foo {}
  
  .foo[class] {}
  #foo[id] {}
  ```

## 第3章 CSS选择器的命名

- 命名书写

  - 命名建议使用小写，使用英文单词或缩写，对于专有名词，可以使用拼音，不建议使用驼峰命名，例如：

    ```css
    .cs-logo-youku {}
    ```

  - 对于组合命名，可以使用短横线或下划线连接，例如：

    ```css
    .cs-logo-youku {}
    .cs_logo_youku {}
    .cs-logo--youku {}
    .cs-logo__youku {}
    ```

  - 设置统一前缀，强化品牌同时避免样式冲突，例如：

    ```css
    .cs-header {}
    .cs-logo {}
    .cs-logo-a {}
    ```

- 根据选择器使用类型，可以将网站CSS分为三个部分，分别是CSS重置样式，CSS基础样式和CSS交互样式。无论那种样式，都没有任何理由使用ID选择器，可以使用属性选择器替代，它的优先级和类选择器一摸一样。

  ```css
  [id="someId"] {}
  ```

  - CSS样式的重置可以使用标签选择器或者属性选择器等。

    ```css
    body, p { margin: 0; }
    
    [type="radio"],
    [type="checkbox"] {
       position: absolute; clip: rect(0 0 0 0);
    }
    ```

  - CSS基础样式全部使用类选择器，没有层级，没有标签。

    ```css
    .cs-module .img {}    /* 不建议 */
    .cs-module-ul > li {}    /* 不建议 */
    
    .cs-module-img {}
    .cs-module-li {}
    ```

  - 所有选择器嵌套或者级联，所有伪类全部都在CSS交互样式发生变化的时候使用。

    ```css
    .cs-content.active {
       height: auto;
    }
    .active > .cs-content-more {
       display: none;
    }
    ```

## 第4章 精通CSS选择符

- **后代选择符空格（ ）**

  ```css
  <div class="lightblue">
     <div class="darkblue">
        <p>1. 颜色是？</p>
     </div>
  </div>
  <div class="darkblue">
     <div class="lightblue">
        <p>2. 颜色是？</p>
     </div>
  </div>
  .lightblue { color: lightblue; }
  .darkblue { color: darkblue; }
  /* 此时，1颜色是darkblue；2颜色是lightblue */
  
  .lightblue p { color: lightblue; }
  .darkblue p { color: darkblue; }
  /* 此时，1和2颜色都是darkblue */
  ```

- **子选择符箭头（`>`）**

  - 子选择符只会匹配第一代子元素，而后代选择符会匹配所有子元素。
  - 能不用子选择符就尽量不用，虽然它的性能优于后代选择符，但与其日后的维护成本比，这实在不值一提。

- **相邻兄弟选择符加号（`+`）**

  - 相邻兄弟选择符只能选择后面一个兄弟。
  - [实现类似:first-child的效果](https://demo.cssworld.cn/selector/4/3-3.php)
  - [相邻兄弟选择符与:focus伪类交互实例页面](https://demo.cssworld.cn/selector/4/3-4.php)

- **随后兄弟选择符弯弯（`~`）**
  
- 相邻兄弟选择符只会匹配它后面的第一个兄弟元素，而随后兄弟选择符会匹配后面的所有兄弟元素。
  
- **列选择符双管道（||）**

## 第5章 元素选择器

### 元素选择器的特点

- 元素选择器主要包括两类，一类是**标签选择器**，一类是**通配符选择器**。

- 元素选择器是唯一不能重复自身的选择器。但可以借助`html`和`body`元素来提高标签优先级，也可以借助`:not()`伪类，括号里面是任意其它不一样的标签名称即可。

  ```css
  .foo.foo {}
  #foo#foo {}
  [foo][foo] {}
  foo*foo {}    /* 无效 */
  
  body foo {} /* 借助html和body元素来提高标签优先级 */
  
  /* 借助:not()伪类 */
  foo:not(not-foo) {}
  foo:not(a) {}
  foo:not(_) {}
  ```

- 级联使用的时候元素选择器必须写在前面。

  ```css
  [type="radio"]input {}    /* 无效 */
  input[type="radio"] {}
  [type="radio"]* {}    /* 无效 */
  *[type="radio"] {}    /* *也可以省略 */
  [type="radio"].input {}
  :hover.foo {}
  ```

## 第6章 属性选择器

### 4种属性值直接匹配选择器

- `[attr]`表示只要包含指定的属性就匹配，无论值的内容是什么。

  ```css
  [disabled] {}
  
  <input disabled> /*匹配*/
  <input disabled=""> /*匹配*/
  <input disabled="disabled"> /*匹配*/
  <input disabled="true"> /*匹配*/
  <input disabled="false"> /*匹配*/
  
  [data-title] {}
  <a href class data-title="提示" role="button">删除</a> /*匹配*/
  ```

- `attr="val"`是属性值完全匹配选择器。

  ```css
  [type="radio"] {}
  [type="checkbox"] {}
  [data-type="1"] {}
  ```

- `attr~='val'`是属性值单词完全匹配选择器，专门用来匹配属性中的单词。

  ```css
  [rel~="noopener"] {}
  [rel~="nofollow"] {}
  
  <a href rel="nofollow noopener">链接</a> /*匹配*/
  
  [rel~=""] {} /* 无任何匹配 */
  
  [attr~="val"]
  
  <div attr="value"></div> /*匹配*/
  <div attr="val-ue"></div> /*匹配*/
  <div attr=" val "></div> /*匹配*/
  <div attr="val    ue"></div> /*匹配*/
  ```

  ```css
  /*适用场景*/
  <div data-align="left top"></div>
  <div data-align="top"></div>
  <div data-align="right top"></div>
  <div data-align="right"></div>
  <div data-align="right bottom"></div>
  <div data-align="bottom"></div>
  <div data-align="left bottom"></div>
  <div data-align="left"></div>
  <div data-align="center"></div>
  
  /*最佳实践*/
  [data-align] { left: 50%; top: 50%; }
  [data-align~="top"] { top: 0; }
  [data-align~="right"] { right: 0; }
  [data-align~="bottom"] { bottom: 0; }
  [data-align~="left"] { left: 0; }
  ```

- `[attr|="val"]`是属性值起始片段完全匹配选择器，表示具有`attr`属性的元素，其值要么正好是`val`，要么以`val`外加短横线`-`开头。

  ```html
  <!-- 匹配 -->
  <div attr="val"></div>
  <!-- 匹配 -->       
  <div attr="val-ue"></div>
  <!-- 匹配 -->       
  <div attr="val-ue bar"></div> 
  <!-- 不匹配 --> 
  <div attr="value"></div>
  <!-- 不匹配 -->
  <div attr="val bar"></div>
  <!-- 不匹配 -->
  <div attr="bar val-ue"></div>
  ```

### 属性值正则匹配选择器

- `[attr^="val"]`表示`attr`属性值以字符`val`开头的元素。

  ```html
  <!-- 匹配 --> 
  <div attr="val"></div> 
  <!-- 不匹配 -->      
  <div attr="text val"></div>
  <!-- 匹配 --> 
  <div attr="value"></div> 
  <!-- 匹配 -->    
  <div attr="val-ue"></div>
  ```

- `[attr$="val"]`表示匹配`attr`属性值以字符`val`结尾的元素。

  ```html
  <!-- 匹配 --> 
  <div attr="val"></div> 
  <!-- 匹配 -->      
  <div attr="text val"></div>
  <!-- 不匹配 --> 
  <div attr="value"></div> 
  <!-- 不匹配 -->    
  <div attr="val-ue"></div>
  ```

- `[attr*="val"]`表示匹配`attr`属性值包含字符`val`的元素。

  ```html
  <!-- 匹配 --> 
  <div attr="val"></div> 
  <!-- 匹配 -->      
  <div attr="text val"></div>
  <!-- 匹配 --> 
  <div attr="value"></div> 
  <!-- 匹配 -->    
  <div attr="val-ue"></div>
  ```

### CSS属性选择器搜索过滤技术

正则匹配运算符是属性选择器新增的运算符，它可以忽略属性值大小写，使用字符`i`或`I`作为运算符值。

[忽略大小写的搜索功能](https://codepen.io/ronaldo-c/pen/RwRMovL)

## 第7章 用户行为伪类

### 手型经过伪类`:hover`

- [体验优化与:hover延时](https://demo.cssworld.cn/selector/7/1-1.php)

- 当借助`:hover`伪类实现下拉列表效果时，不光可以通过父子选择器控制，也可以借助相邻兄弟选择符实现类似效果。[hover显示有间距的相邻图片元素实例页面](https://demo.cssworld.cn/selector/7/1-3.php)
- 纯`:hover`交互在用户无鼠标时，则功能完全失效，可以通过增加`:focus`伪类来优化体验。如果浮层内部带有链接或按钮的元素，可以使用整体焦点伪类`:focus-within`，但目前IE浏览器不支持`:focus-within`。[focus也能显示tips提示实例页面](https://demo.cssworld.cn/selector/7/1-4.php)

### 激活状态伪类`:active`

`:active`伪类与CSS数据上报

```css
.button-1:active::after {
   content: url(./pixel.gif?action=click&id=button1);
   display: none;
}
.button-2:active::after {
   content: url(./pixel.gif?action=click&id=button2);
   display: none;
}
```

### 焦点伪类`:focus

[focus点击显示二维码图片实例页面](https://demo.cssworld.cn/selector/7/3-1.php)

```html
<!-- 此时div是一个类似textarea元素的输入框 -->
<div contenteditable="true"></div>
<div contenteditable="plaintext-only"></div>

<div tabindex="-1">内容</div> <!-- 不可以被Tab键索引，且被点击的时候可以触发:focus样式 -->
<div tabindex="0">内容</div>  <!-- 可以被Tab键索引，且被点击的时候可以触发:focus样式 -->
<div tabindex="1">内容</div>  <!-- 不可以被Tab键索引，且被点击的时候可以触发:focus样式 -->
```

### 整体焦点伪类`:focus-within`

- **`:focus-within`伪类和`:focus`伪类的区别**：`:focus`伪类样式只有在当前元素处于聚焦状态的时候才能匹配，而`:focus-within`伪类样式在当前元素或者是当前元素的任意子元素处于聚焦状态的时候都会匹配。
- [:focus-within高亮输入框前面的描述文字实例页面](https://demo.cssworld.cn/selector/7/4-1.php)
- [:focus-within实现下拉列表实例页面](https://demo.cssworld.cn/selector/7/4-2.php)

### 键盘焦点伪类`:focus-visible`

- `:focus-visible`可以让我们知道元素的聚焦行为到底是鼠标触发的还是还是键盘触发的。
- [:focus-visible与聚焦轮廓控制实例页面](https://demo.cssworld.cn/selector/7/5-1.php)

## 第8章 URL定位伪类

