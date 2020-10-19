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

