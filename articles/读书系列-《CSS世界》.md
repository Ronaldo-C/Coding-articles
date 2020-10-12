# 读书系列-《CSS世界》

## 第 1 章 概述

### 什么是“流”

**“流”又叫“文档流”，是 CSS 世界中引导元素排列和定位的一条看不见的“水流”。 **利用“流”的特性实现的“流体布局”，具有自适应性（如：使用`<div>`自上而下，使用`<span>`从左向右）。

## 第 2 章 需提前了解的术语和概念 

## 第 3 章 流、元素与基本尺寸

### 3.1  块级元素

#### 块级元素和内联元素

- 块级元素：一个水平流上只能单独显示一个元素，多个块级元素则换行显示。（如：`<div>`、`<li>`和`<table>` 等）
- 内联元素：典型特征是可以和文字在一行显示。因此，文字是内联元素，图片是内联元素，按钮是内联元素，输入框、下拉框等原生表单控件也是内联元素。（如：`<span>`、`<a>`和`<img>`等）

#### 标记盒子、外在盒子和内在盒子

- 标记盒子：**专门用来放圆点、数字这些项目符号的盒子。**`display:list-item`元素会出现项目符号就是因为生成了标记盒子，IE 浏览器下伪元素不支持 list-item或许就是无法创建这个“标记盒子”导致的。 
- 外在盒子和内在盒子：外在盒子负责元素是可以一行显示，还是只能换行显示；内在盒子（**容器盒子**）负责宽高、内容呈现等。按照 display 的属性值不同，值为 block 的元素的盒子实际由外在的“块级盒子” 和内在的“块级容器盒子”组成，值为 inline-block 的元素则由外在的“内联盒子”和内在的“块级容器盒子”组成，值为 inline 的元素则内外均是“内联盒子”。 

### 3.2  width/height 作用的具体细节

####  `width:auto` 在不同场景下的宽度表现

- **充分利用可用空间**：比方说，`<div>`、`<p>`这些元素的宽度默认是 100%于父级容器的。**CSS3**中表现为 `width:fill-available`。

  - [实际开发中的应用](https://demo.cssworld.cn/3/2-3.php)

- **收缩与包裹**：典型代表就是浮动，绝对定位，inline-block水平元素或`table`表格，英文称为“shrink-to-fit”，直译为“收缩到合适”，可理解为**“包裹性”**。**CSS3**中表现为 `width:fit-content`。

  - [实际开发中的应用](https://demo.cssworld.cn/3/2-5.php)

- **收缩到最小**：这个容易出现在 table-layout 为 auto 的表格中，当每一列空间都不够的时候，文字能断就断，但中文是随便断的，英文单词不能断。这种行为在规范中被描述为“preferred minimum width”或者“minimum content width”。**CSS3**中表现为 `width:min-content`。

  - [实际开发中的应用](https://demo.cssworld.cn/3/2-6.php)

  ![收缩到最小](../images/CSS世界3-1.png)

- **超出容器限制**：上面1~3情况，除非有明确的`width`相关设置，否则尺寸都不会主动超过父级容器宽度的，但是存在一些特殊情况。例如，内容很长的连续的英文和数字，或者内联元素被设置了`white-space:nowrap`。子元素既保持了 inline-block 元素的收缩特性，又同时让内容宽度大，直接无视父级容器的宽度限制。**CSS3**中表现为 `width:max-content`。

  - [实际开发中的应用](https://demo.cssworld.cn/3/2-7.php)

  如下CSS代码：
  
  ```css
  .father {   
      width: 150px;   
      background-color: #cd0000;   
      white-space: nowrap; 
  } 
  .child {   
      display: inline-block;   
      background-color: #f0f3f9; 
} 
  ```

  结果：
  
  ![超出容器限制](../images/CSS世界3-2.png)

#### 宽度分离原则

所谓“宽度分离原则”，就是 CSS 中的`width`属性不与影响宽度的padding/border（有时候包括margin）属性共存，也就是**不能出现**以下的组合：

```css
.box { width: 100px; border: 1px solid; } 
.box { width: 100px; padding: 20px; } 
```

应该`width`独立占用一层标签，而`padding`、`border`、`margin`利用流动性在内部自适应呈现：

```css
.father {
 width: 180px;
}
.son {
 margin: 0 20px;
 padding: 20px;
 border: 1px solid;
} 
```

#### `box-sizing`的作用细节

- box-sizing被直译为“盒尺寸”，实际上，其更准确的叫法应该是“盒尺寸的作用 细节”，或者说得更通俗一点，叫“width作用的细节”，也就是说，box-sizing属性的作用是改变width的作用细节。

- box-sizing被发明出来最大的初衷应该是解决替换元素宽度自适应问题，平时开发使用“宽度分离原则”更为合理。

  ```css
  input, textarea, img, video, object { /** 替换元素 **/
   box-sizing: border-box;
  } 
  ```

#### 为何`height:100%`无效

- 浏览器渲染的基本原理：先下载文档内容，加载头部的样式资源（如果有的话），然后按照从上而下、自外而内的顺序渲染 DOM 内容。
- 当渲染到父元素的时候，子元素的`width:100%`并没有渲染，宽度就是图片加文字内容的宽度；等渲染到文字这个子元素的时候， 父元素宽度已经固定，此时的 width:100%就是已经固定好的父元素的宽度。宽度不够怎么 办？溢出就好了，overflow属性就是为此而生的。（例子：[width: 100%](https://demo.cssworld.cn/3/2-10.php)）
- 为何宽度支持，高度就不支持`width:100%`呢？
  - 规范中高度的解释：如果包含块的高度没有显式指定（即高度由内容决定），并且该元素不是绝对定位，则计算值为 auto。
  - 规范中宽度的解释：如果包含块的宽度取决于该元素的宽度，那么产生的布局在 CSS 2.1 中是未定义的。

#### 如何让元素支持`height:100%`效果

- **设定显式的高度值**：宽高百分比计算是相对于content box计算的。

  ```css
  html, body {
   height: 100%;
  } 
  ```

- **使用绝对定位**：宽高百分比计算是相对于padding box的。

  ```css
  div {
   height: 100%;
   position: absolute;
  } 
  ```

### 3.3  CSS min-width/max-width 和 min-height/max-height 二三事

- max-width和max-height的初始值是 **none**；min-width和min-height的初始值是 **auto**。

- max-width会覆盖width，并且比 !important 权重还高。

  [权重大于!important](https://demo.cssworld.cn/3/3-1.php)

- min-width会覆盖max-width。

  ```css
  .container {
   	min-width: 1400px; //min-width生效
   	max-width: 1200px;
   } 
  ```

#### 任意高度元素的展开收起动画技术

- [max-height与任意高度元素滑动展开收起效果](https://demo.cssworld.cn/3/3-2.php)

### 3.4 内联元素

#### 什么是内联元素

- “内联元素”的“内联”特指“外在盒子”，inline-block和inline-table都是“内联元素”，因为它们的 “外在盒子”都是内联盒子。
- “内联元素”的典型特征就是可以和文字在一行显示。因此，文字是内联元素，图片是内联元素，按钮是内联元素，输入框、下拉框等原生表单控件也是内联元素。

#### 幽灵空白节点

在**HTML5 文档声明**中，内联元素的所有解析和渲染表现就如同每个行框盒子的前面有一个“空白节点”一样，这 个“空白节点”永远透明，不占据任何宽度，看不见也无法通过脚本获取。在规范中这是个假想盒，名叫“strut”，中文直译为“支柱”，是一个存在于每个“行框盒子”前面，同时**具有该元素的字体和行高属性的 0 宽度的内联盒**。

## 第 4 章 盒尺寸四大家族

### 4.1 深入理解 content

#### 替换元素和非替换元素

- 通过修改某个属性值呈现的内容就可以被替换的元素就称为“替换元素”（`img`、`<object>`、`<video>`、`<iframe>`、`<textarea>`、`<input>`）。
- 替换元素的特性：
  - 内容的外观不受页面上的 CSS 的影响。
  - 有自己的尺寸。如：`<video>`、`<iframe>`的默认尺寸是300像素×150 像素；`<img>`的Chrome 浏览器下默认尺寸是0像素，Firefox 浏览器下显示的是 0×22。
  - 在很多 CSS 属性上有自己的一套表现规则。

- 替换元素的尺寸从内而外分为 3 类：固有尺寸、HTML 尺寸和 CSS 尺寸；计算规则是：**固有尺寸 < HTML 尺寸 < CSS 尺寸**
  - **固有尺寸**：指替换内容原本的尺寸。例如，图片、视频作为一个独立文件存在的时候，都是有着自己的宽度和高度的，这个宽度和高度的大小就是这里的“固有尺寸”。**替换元素的固有尺寸无法改变**
  - **HTML 尺寸**：指替换元素的HTML原生属性。例如，`<img>`的 width 和 height 属性、 `<input>`的 size 属性、`<textarea>`的 cols 和 rows 属性等。
  - **CSS 尺寸**：指可以通过 CSS 的 width 和 height 或者 max-width/min-width 和 max-height/min-height 设置的尺寸，对应盒尺寸中的 content box。

- content 属性生成的对象称为“匿名替换元素”。

#### content的实际开发用处

- [content换行符与打点loading效果实例页面](https://demo.cssworld.cn/4/1-9.php)

- content 计数器和content attr属性值

  ```html
  // css
  .reset {
  	counter-reset: data;
  }
  .counter {
  	padding-left: 20px;
  }
  .counter::before{
  	content: counters(data, '-') '.';
  	counter-increment: data;
  	font-size: 16px;
  }
  
  .attr::before {
  	content: attr(data-title);
  }
  // html
  <h1 class="attr" data-title="这是标题"></h1>
    <div class="reset">
      <div class="counter">这是第1条数据
        <div class="reset">
          <div class="counter">这是第1.1条数据</div>
        </div>
      </div>
      <div class="counter">这是第2条数据</div>
      <div class="counter">这是第3条数据</div>
    </div>
  ```

  ![content 计数器和content attr属性值](../images/CSS世界4-1.png)

### 4.2 温和的 padding 属性

#### padding 的百分比值

padding不可为负值，但是可以为百分比值，**padding 百分比值无论是水平方向还是垂直方向均是相对于宽度计算的。**

```css
.father {
    width: 100px;
}
.son {
    padding: 30% 50%;
    background-color: wheat;
}
```

![padding 的百分比值](../images/CSS世界4-2.png)

#### padding 与图形绘制

```css
//css
.icon1 {
    display: inline-block;
    width: 30px;
    height: 5px;
    padding: 5px 0;
    border-top: 5px solid;
    border-bottom: 5px solid;
    background-clip: content-box;
    background-color: currentColor;
}
.icon2 {
    display: inline-block;
    width: 15px;
    height: 15px;
    padding: 5px;
    border-radius: 50%;
    border: 5px solid;
    background-clip: content-box;
    background-color: currentColor;
}

//html
<i class="icon1"></i>
<i class="icon2"></i>
```

![padding 与图形绘制](../images/CSS世界4-2-2.png)

### 4.3 激进的 margin 属性

#### margin 与元素的内部尺寸

元素设定了width或者保持“包裹性”的时候，padding会改变元素可视尺寸；对于margin则相反，只有元素是“充分利用可用空间”状态的时候，margin才可以改变元素的可视尺寸。

对于普通块状元素，在默认的水平流下，margin只能改变左右方向的内部尺寸，垂直方向则无法改变。如果我们使用writing-mode改变流向为垂直流，则水平方向内部尺寸无法改变，垂直方向可以改变。

```css
//css
.father {
    width: 300px;
    background-color: hotpink;
}
.son {
    margin: 10px;
    background-color: grey;
}

//html
<div class="father">
	<div class="son">aaa</div>
</div>
```

#### margin负值实现等高布局

- 优点：兼容性足够，IE6 浏览器也支持，且支持任意个分栏等高布局。（`padding-bottom:9999px`也可以用 `border-bottom: 9999px solid transparent`代替，不过 IE7 以上浏览器才支持。）
- 缺点：
  - 父级的`overflow:hidden`会阻止子元素定位到容器之外。
  - 当触发锚点定位或者使用 DOM.scrollIntoview()方法的时候，可能就会出现奇怪的定位问题。
- [margin负值实现等高布局](https://demo.cssworld.cn/4/3-2.php)

#### margin 合并

1. margin 合并的特点

   - **块级元素**，但不包括浮动和绝对定位元素，尽管浮动和绝对定位可以让元素块状化。
   - **只发生在垂直方向**，只发生在和当前文档流方向的相垂直的方向上。由于默认文档流是水平流，因此发生 margin 合并的就是垂直方向。

2. margin 合并的3种场景

   - **相邻兄弟元素 margin 合并**
   - **父级和第一个/最后一个子元素**
   - **空块级元素的 margin 合并**

3. 如何阻止 margin 合并

   - 父元素设置为块状格式化上下文元素；
   - 设置border或padding阻隔margin；
   -  用内联元素（如文字）阻隔；
   - 父元素设置 height、min-height 或 max-height。

4. margin 合并的计算规则

   “正正取大值”、“正负值相加”、“负负最负值”

#### 深入理解 CSS 中的`margin:auto`

1. `margin:auto`的填充规则

   - 如果一侧定值，一侧 auto，则 auto 为剩余空间大小。
   - 如果两侧均是 auto，则平分剩余空间。

2. 为什么`margin:auto`无法垂直居中？

   原因在于触发`margin:auto`计算有一个前提条件，就是 width 或 height 为 auto 时， 元素是具有对应方向的自动填充特性的。比方说这里，假如说把.son 元素的`height:100px`去 掉，.son 的高度会自动和父元素等高变成 200px 吗？显然不会！因此无法触发`margin:auto`计算，故而无法垂直居中。

   ```css
   .father {
       height: 200px;
   }
   .son {
       height: 100px;
       margin: auto;
   }
   ```

3. 垂直方向margin实现居中

   - 使用 writing-mode 改变文档流的方向。

     ```css
     .father {
         height: 200px;
         writing-mode: vertical-lr;
     }
     .son {
         height: 100px;
         margin: auto;
     } 
     ```

   - 格式化宽度和格式化高度

     ```css
     .father {
         width: 300px; 
         height:150px;
         position: relative; 
     }
     .son {
         position: absolute;
         top: 0; 
         right: 0; 
         bottom: 0; 
         left: 0; 
         width: 200px; 
         height: 100px; 
         margin: auto;
     }
     ```


### 4.4 功勋卓越的 border 属性

#### border-width 不支持百分比值

- margin 和 padding支持百分比，border-width不支持百分比。
- border-width 还支持关键字：
  - thin：等同于 1px。
  - medium（默认值）：等同于 3px。
  - thick：等同于 4px。

#### border-color 和 color

border-color 默认颜色就是 color 色值，具有类似特性的 CSS 属性还有 outline、box-shadow 和 text-shadow 等。

[border-color 默认颜色实际开发作用](https://demo.cssworld.cn/4/4-1.php)

#### border 与图形构建

[border生成三角形](https://codepen.io/ronaldo-c/pen/GRoOQxK)

#### border实现等高布局

[border实现等高布局](https://demo.cssworld.cn/4/4-4.php)

## 第 5 章 内联元素与流

### 5.1 字母 x—CSS 世界中隐匿的举足轻重的角色

#### 字母 x 与 CSS 世界的基线

- 字母 x 的下边缘（线）就是基线，vertical-align的默认值baseline也是基线。
- CSS 中有一个概念叫作 x-height，指的就是小写字母 x 的高度；`vertical-align:middle`指的是基线往上 1/2 x-height 高度，可以近似理解为字母 x 交叉点那个位置。
- ex 是 CSS 中的一个尺寸单位，是一个相对单位，指的是小写字母 x 的高度，就是指 x-height。
- [借助ex实现垂直居中](https://demo.cssworld.cn/5/1-1.php)

### 5.2 内联元素的基石 line-height

1. 行距 = line-height - font-size；**半行间距  = (line-height - font-size) / 2**

   ![半行间距](../images/CSS世界5-2.png.png)

2. line-height的几种属性值：
   - **数值**，如`line-height:1.5`，其最终的计算结果是和当前font-size相乘后的值。
   - **百分比值**，如`line-height:150%`，其最终的计算结果是和当前font-size相乘后的值。
   - **长度值**，也就是带单位的值，如`line-height:21px`或`line-height:1.5em`，此处em是一个相对于font-size的单位，所以`line-height:1.5em`最终的计算结果也是和当前font-size相乘后的值。

3. line-height属性值继承细节不同：如果使用数值作为 line-height 的属性值， 那么所有的子元素继承的都是这个值；但是，如果使用百分比值或者长度值作为属性值，那么所有的子元素继承的是最终的计算值。
4. **存在误区的说法：要想让单行文字垂直居中，只要设置 line-height 大小和 height 高度一样就可以了。**
   - 误区一：要让单行文字垂直居中，只需要 line-height 这一个属性就可以，与 height 一点儿关系都没有。
   - 误区二：行高控制文字垂直居中，不仅适用于单行，多行也是可以的，不过需要设置 vertical-align 属性。

### 5.3 line-height 的好朋友 vertical-align

#### vertical-align简介

- vertical-align的属性值
  - **线类**，如 baseline（默认值）、top、middle、bottom。
  - **文本类**，如 text-top、text-bottom。
  - **上标下标类**，如 sub、super。
  - **数值百分比类**，如 20px、2em、20%等。（line-height百分比值是相对于 font-size计算的，而vertical-align 属性的百分比值则是相对于line-height的计算的）。

- vertical-align只能应用于**内联元素**以及 **display 值为table-cell**的元素。

#### img标签图片底部间隙问题

- 留白原因

  当前 line-height 计算值是 20px，而 font-size 只有 14px，因此，字母 x 往下一定 有至少 3px 的半行间距（具体大小与字体有关），而图片作为替换元素其基线是自身的下边缘。 根据定义，默认和基线（也就是这里字母 x 的下边缘）对齐，字母 x 往下的行高产生的多余的间隙就嫁祸到图片下面，让人以为是图片产生的间隙，实际上，是“幽灵空白节点”、 line-height 和 vertical-align 属性共同作用的结果。
  
  ```css
  //css
  .father {
      outline: 1px solid #aaa; 
  }
  .father > span {
      display: inline-block;
      width: 100px;
      height: 100px;
      background-color: hotpink;
  }
  
  //html
  <div class="father">
  	x<span></span>
</div>
  ```
  
  ![img标签图片底部间隙问题](../images/CSS世界5-3.png)
  
- 如何清除间隙？

  - 图片块状化。
  - 容器 line-height 足够小。只要半行间距小到字母 x 的下边缘位置或者再往上，自 然就没有了撑开底部间隙高度空间了。比方说，容器设置`line-height:0`。
  - 容器 font-size 足够小。此方法要想生效，需要容器的 line-height 属性值和当 前 font-size 相关，如 line-height:1.5 或者 line-height:150%之类；否则只会让下面的间隙变得更大，因为基线位置因字符 x 变小而往上升了。
  - 图片设置其他 vertical-align 属性值。间隙的产生原因之一就是基线对齐，所以我们设置 vertical-align 的值为 top、middle、 bottom 中的任意一个都是可以的。

## 第 6 章 流的破坏与保护

### 6.1 魔鬼属性 float

### 6.2 float 的天然克星 clear

#### clear属性不能真正意义上的清除浮动

- 官方对 clear 属性的解释：元素盒子的边不能和前面的浮动元素相邻。`clear:left`就是不和左边浮动元素相邻，其它值也同理。
- clear属性只有块级元素才有效。
- `clear:both`清除浮动的缺点：
  - 如果 clear:both 元素前面的元素就是 float 元素，则 margin-top 负值即使设 成-9999px，也不见任何效果。
  - clear:both 后面的元素依旧可能会发生文字环绕的现象。

### 6.3 CSS 世界的结界——BFC 

#### BFC简介

1. BFC的定义：BFC 全称为 block formatting context，中文为“块级格式化上下文”。相对应的还有 IFC， 也就是 inline formatting context，中文为“内联格式化上下”。
2. BFC的特性：如果一个元素具有 BFC，内部子元素再怎么翻江倒海，都不会影响外部的元素。所以，BFC 元素是**不可能发生 margin 重叠的**，因为 margin 重叠是会影响外面的元素的；BFC 元素也可以用来**清除浮动的影响**，因为如果不清除，子元素浮动则父元素高度塌陷，必然会影响后面元素布局和定位，这显然有违 BFC 元素的子元素不会影响外部元素的设定。
3. 如何触发BFC：
   - `<html>`根元素；
   - float 的值不为 none；
   - overflow 的值为 auto、scroll 或 hidden；
   - display 的值为 table-cell、table-caption 和 inline-block 中的任何一个；
   - position 的值不为 relative 和 static。

### 6.4 最佳结界 overflow

### 6.5 float 的兄弟`position:absolute`

#### 无依赖 absolute 绝对定位

- absolute 是非常独立的 CSS 属性值，其样式和行为表现不依赖其他任何 CSS 属性就可以完成；一个绝对定位元素，没有任何 left/top/right/bottom 属性设置，并且其祖先元素全部都是非定位元素，其位置还是在当前位置。
- [“无依赖绝对定位”与下拉列表定位实例页面](http://demo.cssworld.cn/6/5-7.php)
- [“无依赖绝对定位”与表单实例页面](https://demo.cssworld.cn/6/5-6.php)

### 6.6 absolute 与 overflow

### 6.7 absolute 与 clip

### 6.8 absolute 的流体特性

- 当 absolute 遇到 left/top/right/bottom 属性的时候，absolute 元素才真正变成绝对定位元素，原本的相对特性丢失。如果仅设置了一个方向的绝对定位，则另一个方向依然保持了相对特性，例如：

  ```css
  //此时，水平方向绝对定位，垂直方向仍然保持相对特性
  .box {
      position: absolute;
      left: 0;
  } 
  ```

- 当一个绝对定位元素，其对立定位方向属性同时有具体定位数值的时候，流体特性就发生了。例如：

  ```css
  <div class="box"></div>
  .box {
      position: absolute;
      left: 0; right: 0;
  } 
  ```

- 利用绝对定位元素的流体特性和 margin:auto 的自动分配特性实现水平垂直居中：

  ```css
  .element {
      width: 300px; height: 200px;
      position: absolute;
      left: 0; right: 0; top: 0; bottom: 0;
      margin: auto;
  } 
  ```

### 6.9 position:relative 才是大哥

当相对定位元素同时应用对立方向定位值的时候，也就是 top/bottom 和 left/right 同时使用的时候，其表现和绝对定位差异很大。**绝对定位是尺寸拉伸，保持流体特性**，但是**相对定位却只有一个方向的定位属性会起作用**。这与文档流的顺序有关的，默认的文档流是自上而下、从左往右，因此 top/bottom 同时使用的时候，bottom 不起作用；left/right 同时使用的时候，right 不起作用。

```css
.example {
    position: relative;
    top: 10px;
    right: 10px; /* 无效 */
    bottom: 10px; /* 无效 */
    left: 10px;
} 
```

### 6.10 强悍的 position:fixed 固定定位

唯一可以限制固定定位元素的就是<html>根元素。

## 第 7 章 CSS 世界的层叠规则

### 7.1 z-index 只是 CSS 层叠规则中的一叶小舟

z-index 属性只有和定位元素（position 不为 static 的元素）在一起的时候才有作用，可以是正数也可以是负数。

### 7.2 理解 CSS 世界的层叠上下文和层叠水平

#### 什么是层叠上下文

层叠上下文，英文称作 stacking context，是 HTML 中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元素在 z 轴（表示的是用户与显示器之间这条看不见的垂直线）上就“高人一等”。

#### 什么是层叠水平

层叠水平，英文称作 stacking level，决定了同一个层叠上下文中元素在 z 轴上的显示顺序。

#### 什么是层叠顺序

层叠顺序，英文称作 stacking order，表示元素发生层叠时有着特定的垂直显示顺序。

### 7.3 理解元素的层叠顺序

#### 为什么内联元素的层叠顺序要比浮动元素和块状元素都高？

background/border 为装饰属性，浮动和块状元素一般用作布局，而内联元素都是内容，所以内容的层叠顺序相当高，这样当发生层叠时，重要的文字、图片内容才可以优先显示在屏幕上。

**CSS2.1层叠顺序规则**例图：

![元素的层叠顺序](../images/CSS世界7-3.png)

### 7.4 务必牢记的层叠准则

当元素发生层叠的时候，其覆盖关系遵循下面两条准则：

- **谁大谁上**：当具有明显的层叠水平标识的时候，如生效的 z-index 属性值，在同一 个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。
- **后来居上**：当元素的层叠水平一致、层叠顺序相同的时候，在 DOM 流中处于后面的 元素会覆盖前面的元素。

### 7.5 深入了解层叠上下文

#### 层叠上下文的特性

- 层叠上下文的层叠水平要比普通元素高。
- 层叠上下文可以阻断元素的混合模式。
- 层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的“层叠上下文”。
- 每个层叠上下文和兄弟元素独立，也就是说，当进行层叠变化或渲染的时候，只需要 考虑后代元素。
- 每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠 上下文的层叠顺序中。

#### 层叠上下文的创建

- **根层叠上下文**：根层叠上下文指的是页面根元素，可以看成是<html>元素。
- **定位元素与传统层叠上下文**：对于 position 值为 relative/absolute 以及 Firefox/IE 浏览器（不包括 Chrome 浏览器）下含有`position:fixed`声明的定位元素，当其 z-index 值不是 auto 的时候，会创建层叠上下文。
- **CSS3 与新时代的层叠上下文**：
  - 元素为 flex 布局元素（父元素`display:flex|inline-flex`），同时 z-index 值不是 auto。
  - 元素的 opacity 值不是 1。
  - 元素的 transform 值不是 none。
  - 元素 mix-blend-mode 值不是 normal。
  - 元素的 filter 值不是 none。
  - 元素的 isolation 值是 isolate。
  - 元素的 will-change 属性值为上面 2～6 的任意一个（如`will-change:opacity`、`will-chang:transform`等）。
  - 元素的-webkit-overflow-scrolling 设为 touch。

#### 为什么定位元素会层叠在普通元素的上面？

元素一旦成为定位元素，其 z-index 就会自动生效，此时其 z-index 就是默认的 auto，也就是 0级别，根据上面的层叠顺序表，就会覆盖inline或block或float元素。而不支持z-index 的层叠上下文元素天然是`z-index:auto`级别，也就意味着，层叠上下文元素和定位元素是 一个层叠顺序的，于是当它们发生层叠的时候，遵循的是“后来居上”准则。

### 7.6 z-index 负值深入理解

z-index 负值渲染的过程就是一个寻找第一个层叠上下文元素的过程，然后层叠顺序止步于这个层叠上下文元素。

[z-index负值渲染理解](https://demo.cssworld.cn/7/6-1.php)

## 第 8 章 强大的文本处理能力

### 8.1 line-height 的另外一个朋友 font-size

- [vertical-align 使用 ex 单位实现图标垂直居中](https://demo.cssworld.cn/8/1-1.php)

- font-size 的初始值是 medium ，字号大小跟随浏览器的设置，默认计算值是 16px。

### 8.2 字体属性家族的大家长 font-family

### 8.3 字体家族其他成员

### 8.4 font 属性

### 8.5 真正了解@font face 规则

### 8.6 文本的控制

- [letter-spacing与字符动效实例页面](https://demo.cssworld.cn/8/6-4.php)

- [word-break:break-all和word-wrap:break-word的区别实例页面](https://demo.cssworld.cn/8/6-5.php)

- white-space 不同属性值功能示意：

  | 属性     | 换行 | 空格和制表 | 文本环绕 |
  | -------- | ---- | ---------- | :------- |
  | normal   | 合并 | 合并       | 环绕     |
  | nowrap   | 合并 | 合并       | 不环绕   |
  | pre      | 保留 | 保留       | 不环绕   |
  | pre-wrap | 保留 | 保留       | 环绕     |
  | pre-line | 保留 | 合并       | 环绕     |

- [text-transform 字符大小写实例页面](https://demo.cssworld.cn/8/6-9.php)

### 8.7 了解:first-letter/:first-line 伪元素

- [::first-letter电商实际应用举例](https://codepen.io/ronaldo-c/pen/PozwMNV)

## 第 9 章 元素的装饰与美化

### 9.1 CSS 世界的 color 很单调

- currentColor 变量可以使用当前 color 计算值；CSS 中很多属性值默认就是 currentColor 的表现，如 border、text-shadow、 box-shadow 等。

### 9.2 CSS 世界的 background 很单调

## 第 10 章 元素的显示与隐藏

### 10.1 display 与元素的显隐

- `display:none`的元素 background-image 图片会加载吗？

  - Firefox 浏览器：`display:none`的元素的 background-image 图片是不加载的， 包括父元素 `display:none`也是如此；

  - Chrome 和 Safari 浏览器：父元素`display:none`，图片不加载，若本身背景图所在元素隐藏，则图片依旧会去加载；

  - IE 浏览器：无论怎样都会请求图片资源。

- <img>元素设置`display:none`在所有浏览器下依旧都会请求图片资源。

### 10.2 visibility 与元素的显隐

#### `visibility:hidden`与`display:none`的区别

- visibility 具有继承性，父元素设置`visibility:hidden`，子元素会继承`visibility:hidden`，但如果子元素设置了`visibility:visible`，则子元素又会显示出来。
- `visibility:hidden`不会影响计数器的计数，而`display:none `则会影响。
- CSS3 transition 支持的 CSS 属性中有 visibility，但是并没有 display。

## 第 11 章 用户界面样式

### 11.1 和 border 形似的 outline 属性

### 11.2 光标属性 cursor

## 第 12 章 流向的改变

### 12.1 改变水平流向的 direction

- [CSS direction与文字开头溢出打点效果实例页面](https://demo.cssworld.cn/12/1-2.php)

### 12.2 改变 CSS 世界纵横规则的 writing-mode

- [CSS writing-mode与text-indent文字下沉实例页面](https://demo.cssworld.cn/12/2-5.php)

- [CSS writing-mode与兼容的图标旋转效果实例页面](https://demo.cssworld.cn/12/2-6.php)