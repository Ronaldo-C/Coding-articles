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

### 焦点伪类`:focus`

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

### `:link`和`:visited`伪类

- 区分`<a>`元素按钮是否禁用

  ```css
  [href] { color: skyblue; }
  a:not([href]) { opacity: .6; } /** a标签被禁用 **/
  ```

- `:visited`

  - `:visited`伪类选择器目前只支持：`color`，`background-color`，`border-color`，`border-bottom-color`，`border-left-color`，`border-right-color`，`border-top-color`，`column-rule-color`和`outline-color`。

  - `:visited`代表浏览器访问过的链接。

  - 没有半透明色，要么纯色，要么全透明。

  - 只能重置属性，不能凭空设置属性。

    ```css
    a { color: blue; }
    a:visited { color: red; background-color: gray; } /** 背景色不生效 **/
    
    a { color: blue; background-color: white; }
    a:visited { color: red; background-color: gray; } /** 背景色生效 **/
    ```

  - 无法使用`JavaScript`的`getComputedStyle()`方法获取获取`:visited`设置和呈现的色值。

### `:any-link`伪类

- 匹配所有设置了`href`属性的链接元素，包括`<a>`、`<link>`、`<area>`这三种元素。

- 匹配所有匹配`:link`伪类或者`:visited`伪类的元素。

  ```html
  <a href="//www.cssworld.cn?r=any-link">没有访问过的链接</a><br>
  <a href>访问过的链接</a><br>
  <a>没有设置href属性的a元素</a>
  
  a:any-link { color: white; background-color: deepskyblue; }
  ```

### `:target`与锚点

- URL锚点可以和页面中的`id`匹配的元素进行锚定，浏览器的默认行为就是触发滚动定位，同时进行`:target`伪类匹配。（`#cs-anchor`就是锚点，术语名称就是哈希(hash的音译)，即javascript中location.hash的返回值）。

  ```javascript
  https://www.cssworld.cn/#cs-anchor
  ```

- [:target伪类与显示全部文章内容实例页面](https://demo.cssworld.cn/selector/8/3-1.php)

- [:target伪类实现选项卡切换效果实例页面](https://demo.cssworld.cn/selector/8/3-2.php)

### `:target-within`

- `:target-within`伪类的含义与`:focus-within`伪类类似，只是一个是`:target`伪类的祖先匹配，一个是`:focus`伪类的祖先匹配。`:focus-within`伪类目前已经可以在项目中使用，但`:target-within`伪类却还没有浏览器支持。

- 假设浏览器的URL后面的锚点地址是`#cs-anchor`，则`:target`匹配的是`li#cs-anchor`元素，而`:target-within`不仅可以匹配`li#cs-anchor`元素，还可以匹配父元素`ul`，因为`ul`的后代元素`li#cs-anchor`匹配`:target`伪类。

  ```html
  <ul>
     <li id="cs-first">第1行，id是cs-first</li>
     <li id="cs-anchor">第2行，id是cs-anchor</li>
     <li id="cs-last">第3行，id是cs-last</li>
     <li id="cs-anchor">第4行，id同样是cs-last</li>
  </ul>
  ```

## 第9章 输入伪类

### 可用状态与禁用状态伪类`:enabled`和`:disabled`

### 读写特性伪类`:read-only`和`:read-write`

### 占位符显示伪类`:placeholder-shown`

- `:placeholder-shown`伪类的匹配和`:placeholder`属性密切相关，表示当输入框的`placeholder`内容显示的时候，匹配该输入框。

- [:placeholder-shown与占位符交互实例页面](https://demo.cssworld.cn/selector/9/1-1.php)

- `:placeholder-shown`与空值判断

  ```html
  textarea:placeholder-shown + small::before,
  input:placeholder-shown + small::before {
     content: '尚未输入内容';
     color: red;
     font-size: 87.5%;
  }
  <input placeholder=" "> <small></small>
  <textarea placeholder=" "></textarea> <small></small>
  ```

### 默认选项伪类`:default`

- `:default`伪类选择器只能作用在表单元素上，表示处于默认状态的表单元素。
- [:default伪类与自动添加推荐实例页面](https://demo.cssworld.cn/selector/9/1-2.php)

### 选中选项伪类`:checked`

- `[checked]`能匹配，那么`:checked`伪类的意义是什么呢？（同理问题：`[disabled]`和`:disabled`，`[readonly]`和`:read-only`）
  - `:checked`只能匹配标准表单控件元素，不能匹配其它普通元素，即使这个普通元素设置了`checked`属性，但是`[checked]`属性选择器可以和任意元素匹配。
  - `[checked]`属性变化并非实时的。
  - 伪类可以正确匹配从祖先元素继承过来的状态，但是属性选择器不可以。
- 只要`<label>`元素的`for`属性值和单复选框的`id`一致，点击`label`元素就等同于点击单复选框。
- [:checked伪类与展开收起交互效果实例页面](https://demo.cssworld.cn/selector/9/2-1.php)
- [:checked伪类实现选项卡切换效果实例页面](https://demo.cssworld.cn/selector/9/2-2.php)
- [:checked伪类模拟单复选框实例页面](https://demo.cssworld.cn/selector/9/2-3.php)
- [:checked伪类与开关效果实例页面](https://demo.cssworld.cn/selector/9/2-4.php)
- [:checked伪类与各种选择交互实现实例页面](https://demo.cssworld.cn/selector/9/2-5.php)

### 不确定值伪类`:indeterminate`

- 复选框除了选中和没选中状态外，还有半选状态，半选状态多用在包含全选功能的列表中。没有原生HTML属性可以设置半选状态，半选状态只能通过JavaScript进行设置，这一点和全选不一样（全选有`checked`属性）。

- `:indeterminate`伪类可以匹配复选框、单选框和进度条元素`<progress>`。

  ```javascript
  // 设置checkbox元素为半选状态
  checkbox.indeterminate = true;
  ```

- [:indeterminate伪类与复选框半选状态实例页面](https://demo.cssworld.cn/selector/9/2-6.php)

- [:indeterminate伪类与单选框组的选择提示实例页面](https://demo.cssworld.cn/selector/9/2-7.php)

### 有效性验证伪类`:valid`和`:invalid`

- [:invalid伪类验证与实际应用实例页面](https://demo.cssworld.cn/selector/9/3-1.php)

### 范围验证伪类`:in-range`和`:out-of-range`

`:in-range`和`:out-of-range`伪类与`min`属性和`max`属性密切相关，因此这两个伪类常用来匹配`number`类型的输入框和`range`类型的输入框。

### 可选性伪类`:required`和`:optional`

- `:optional`伪类可以看成是`:required`伪类的对立面，只要表单元素没有设置`:required`属性，都可以匹配`:optional`伪类，甚至`<button>`按钮也可以匹配。
- [:required和:optional伪类标记问卷可选项实例页面](https://demo.cssworld.cn/selector/9/3-2.php)

## 第10章 树结构伪类

### `:root`伪类和`<html>`元素

- `:root`伪类和`<html>`元素有什么区别？
  - `:root`伪类优先级更高。
  - 对于`:root`伪类，IE9以上版本才支持，兼容性逊于`html`标签选择器。
  - `:root`指所有XML格式文档的根元素，XHTML文档只是其中一种。

### `:empty`伪类

- `:empty`伪类基本匹配特性：
  - `:empty`伪类用来匹配空标签元素。
  - `:empty`伪类可以匹配前后闭合的替换元素，如`<button>`元素和`<textarea>`元素。
  - `:empty`伪类还可以匹配非闭合元素，如`<input>`元素、`<img>`元素和`<hr>`元素等。
  - 元素内有注释和空格，`:empty`不能匹配。
  - `:empty`伪类可以匹配`::before`和`::after`伪元素。

  ```css
  <div class="cs-empty"></div>
  .cs-empty:empty {
     width: 120px;
     padding: 20px;
     border: 10px dashed;
  }
  
  <textarea></textarea>
  textarea:empty {
     border: 6px double deepskyblue;
  }
  
  input:empty,
  img:empty,
  hr:empty {
     border: 6px double deepskyblue;
  }
  <input type="text" placeholder="请输入姓名">
  <img src="./1.jpg">
  <hr>
  
  .cs-empty::before {
     content: '我是一段文字';
  }
  .cs-empty:empty {
     border: 10px dotted deepskyblue;
  }
  <!-- 可以匹配:empty伪类 -->
  <div class="cs-empty"></div>
  ```

### 子索引伪类

- `:first-child`伪类和`:last-child`伪类

  `:first-child`伪类可以匹配第一个子元素，`:last-child`伪类可以匹配最后一个子元素；`:first-child`伪类出现早，IE7浏览器就开始支持，而`:last-child`伪类是CSS3时代出现的，IE9浏览器才开始支持，所以优先使用`:first-child`伪类。

- `:only-child`伪类

  - `:only-child`伪类就是匹配没有任何兄弟元素的子元素。
  - [:only-child伪类与loading模块实例页面](https://demo.cssworld.cn/selector/10/3-1.php)

- `:nth-child()`伪类和`:nth-last-child()`伪类
  - `:nth-child()`伪类和`:nth-last-child()`伪类区别在于，`:nth-child()`伪类是从前面开始匹配的，而`:nth-last-child()`是从后面开始匹配的，除此之外，两者没有其它区别。
  - 各种类型参数含义：
    - **`tr:nth-child(odd):`**匹配表格1，3，5行，等同于`tr:nth-child(2n+1)`。（辅助记忆：odd三个字母，匹配奇数位数的元素）
    - **`tr:nth-child(even):`**匹配表格2，4，6行，等同于`tr:nth-child(2n)`。（辅助记忆：even四个字母，匹配奇数位数的元素）
    - **`:nth-child(3):`**匹配第三个元素。
    - **`:nth-child(5n):`**匹配第5，10，15...个元素。
    - **`:nth-child(3n+4):`**匹配第4，7，10...个元素。
    - **`:nth-child(-n+3):`**匹配前3个元素。
    - **`li:nth-child(n):`**匹配所有的`li`元素，和`<li>`标签选择器区别在于就是优先级更高了。
    - **`li:nth-child(1):`**匹配第一个`<li>`元素，和`li:first-child`匹配的作用一样，区别就是后者兼容性更好。
    - **`li:nth-child(n+4):nth-child(-n+10):`**匹配第4~10个元素。
  - [:nth-child与指定区间范围列表高亮实例页面](https://demo.cssworld.cn/selector/10/3-2.php)
  - [first-child/nth-last-child动态列表数量匹配技术实例页面](https://demo.cssworld.cn/selector/10/3-3.php)

### 匹配类型的子索引伪类

匹配类型的子索引伪类类似于子索引伪类，区别在于匹配类型的子索引伪类是在同级别列表中相同标签元素之间进行索引和解析的。

- ``:first-of-type``伪类和``:last-of-type``伪类

  `:first-of-type`伪类匹配当前标签类型元素的第一个；`:last-of-type`伪类匹配最后一个同类型的标签元素。

  ```css
  dl > :first-of-type {
     color: deepskyblue;
     font-style: italic;
  }
  
  <dl>
     <dt>标题1</dt> /** 匹配 **/
     <dd>内容1</dd> /** 匹配 **/
     <dt>标题2</dt>
     <dd>内容2</dd>
  </dl>
  
  
  dl > :last-of-type {
     color: deepskyblue;
     font-style: italic;
  }
  <dl>
     <dt>标题1</dt>
     <dd>内容1</dd>
     <dt>标题2</dt> /** 匹配 **/
     <dd>内容2</dd> /** 匹配 **/
  </dl>
  ```

- `:only-of-type`伪类

  `:only-of-type`表示匹配唯一的标签类型的元素。匹配`:only-child`的元素一定匹配`:only-of-type`伪类，但匹配`:only-of-type`伪类的元素不一定匹配`:only-child`伪类。

  ```css
  dl > :only-of-type {
     color: deepskyblue;
     font-style: italic;
  }
  
  <dl>
     <dt>标题</dt> /** 匹配 **/
     <dd>内容</dd> /** 匹配 **/
  </dl>
  
  <dl>
     <dt>标题1</dt> /** 不匹配 **/
     <dd>内容1</dd> /** 不匹配 **/
     <dt>标题2</dt> /** 不匹配 **/
     <dd>内容2</dd> /** 不匹配 **/
  </dl>
  ```

- `:nth-of-type()`伪类和`:nth-last-of-type()`伪类

  - `:nth-of-type()`伪类匹配指定索引的当前标签类型元素，`:nth-of-type()`伪类是从前面开始匹配的，而`:nth-last-of-type`伪类是从后面开始匹配的。

  - `:nth-child()`伪类和`:nth-of-type()`伪类的异同：

    - 相同：语法是一模一样的。
    - 不同：`:nth-of-type()`伪类匹配范围是所有相同标签的相邻元素，而`:nth-child()`伪类会匹配所有相邻元素，无视标签类型。

    ```css
    /* 第奇数个<p>元素的背景为灰色 */
    p:nth-of-type(2n + 1) {
       background-color: #ddd;
    }
    /* 将第4的倍数个<p>元素加粗同时深天蓝色显示 */
    p:nth-of-type(4n) {
       color: deepskyblue;
       font-weight: bold;
    }
    
    <article>
       <h3>标题1</h3>
       <p>段落内容1</p> /** 匹配 **/
       <p>段落内容2</p>
       <h3>标题2</h3>
       <p>段落内容3</p> /** 匹配 **/
       <p>段落内容4</p> /** 匹配 **/
    </article>
    ```

    ```css
    /* 第奇数个元素，同时是<p>标签 */
    p:nth-child(2n + 1) {
       background-color: #ddd;
    }
    /* 第4的倍数个<p>元素，同时是<p>标签 */
    p:nth-child(4n) {
       color: deepskyblue;
       font-weight: bold;
    }
    
    <article>
       <h3>标题1</h3>
       <p>段落内容1</p> 
       <p>段落内容2</p> /** 匹配 **/
       <h3>标题2</h3>
       <p>段落内容3</p> /** 匹配 **/
       <p>段落内容4</p> 
    </article>
    ```

    