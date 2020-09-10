# CSS、JS、TS

## 1. BFC 
BFC的全称为 Block Formatting Context，也就是块级格式化上下文的意思。用于决定块盒子的布局及浮动相互影响范围的一个区域。Block Formatting Context提供了一个环境，HTML元素在这个环境中按照一定规则进行布局。一个环境中的元素不会影响到其它环境中的布局。
    
### 以下方式都会创建BFC：

- 根元素(html)
- 浮动元素（元素的 float 不是 none）
- 绝对定位元素（元素的 position 为 absolute 或 fixed）
- 行内块元素（元素的 display 为 inline-block）
- 表格单元格（元素的 display为 table-cell，HTML表格单元格默认为该值）
- 表格标题（元素的 display 为 table-caption，HTML表格标题默认为该值）
- 匿名表格单元格元素（元素的 display为 table、table-row、table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot的默认属性）或 inline-table）
- overflow 值不为 visible 的块元素
- display 值为 flow-root 的元素
- contain 值为 layout、content或 paint 的元素
- 弹性元素（display为 flex 或 inline-flex元素的直接子元素）
- 网格元素（display为 grid 或 inline-grid 元素的直接子元素）
多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。

但其中，最常见的就是overflow:hidden、float:left/right、position:absolute。也就是说，每次看到这些属性的时候，就代表了该元素以及创建了一个BFC了。

### BFC布局规则：

1. 内部的box会在垂直方向，一个接一个的放置（可以看作BFC中有一个的常规流）。
2. box垂直方向的距离有margin决定。属于同一个BFC的两个相邻box的margin会发生重叠。
3. 每个元素的左外边距与包含块的左边界相接触，即使浮动元素也是如此。
4. BFC的区域不会与float的元素区域重叠。
5. 计算BFC的高度时，浮动子元素也参与计算。
6. BFC就是页面上一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。

### BFC能解决的问题：

1. 父元素塌陷
2. 外边距重叠
3. 清除浮动

## 2. 绝对定位
设置为绝对定位的元素框从文档流完全删除，并相对于其包含块定位，包含块可能是文档中的另一个元素或者是初始包含块。元素原先在正常文档流中所占的空间会关闭，就好像该元素原来不存在一样。

绝对定位的元素的位置相对于最近的已定位祖先元素，如果元素没有已定位的祖先元素，那么它的位置相对于最初的包含块。
### 注意：
1. 根据用户代理的不同，最初的包含块可能是画布或 HTML 元素。
2. 元素定位后生成一个块级框，而不论原来它在正常流中生成何种类型的框。
3. 未指定left/top/right/bottom的absolute元素，其在所处层级中的定位点就是正常文档流中该元素的定位点。
4. 绝对定位相对于包含块的起始位置是祖先元素的内边距或内容，效果如下
```
<div style="border:4px solid red;position: relative;padding: 20px; margin: 20px;">
    <div style="width: 100px;height: 100px;position: absolute; left: 0;top:0;background: pink;">
    </div>
    <span>测试绝对定位测试绝对定位</span>
</div>
```
![](https://user-gold-cdn.xitu.io/2020/7/5/1731caa4745c21db?w=421&h=144&f=png&s=3676)

## 3. z-index相关
层叠上下文/层叠水平/层叠顺序

（参考:https://www.cnblogs.com/leftJS/p/11063683.html）

### 形成层叠上下文的方法有：
层叠上下文即元素在某个层级上z轴方向的排列关系。
 - 根元素 <html></html>
 - position值为 absolute|relative，且 z-index值不为 auto
 - position 值为 fixed|sticky
 - z-index 值不为 auto 的flex元素，即：父元素 display:flex|inline-flex
 - opacity 属性值小于 1 的元素
 - transform 属性值不为 none的元素
 - mix-blend-mode 属性值不为 normal 的元素
 - filter、 perspective、 clip-path、 mask、 mask-image、 mask-border、 motion-path 值不为none 的元素
 - perspective 值不为 none 的元素
 - isolation 属性被设置为 isolate 的元素
 - will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
 - -webkit-overflow-scrolling 属性被设置 touch的元素

    注意：
        1. 层叠上下文可以包含在其他层叠上下文中，并且一起组建了一个有层级的层叠上下文
        2. 每个层叠上下文完全独立于它的兄弟元素，当处理层叠时只考虑子元素，这里类似于BFC
        3. 每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会在父级叠上下文中按顺序进行层叠
   


   
### 层叠等级 (层叠水平, Stacking Level)   
 
 决定了同一个层叠上下文中元素在z轴上的显示顺序的概念；
    
    注意：
        1. 普通元素的层叠等级优先由其所在的层叠上下文决定
        2. 层叠等级的比较只有在同一个层叠上下文元素中才有意义
        3. 在同一个层叠上下文中，层叠等级描述定义的是该层叠上下文中的元素在Z轴上的上下顺序
        4. flex形成层叠上下文元素的是框里的子元素
        5. 绝对定位和相对定位的元素z-index默认为auto，如果不设置值（不为auto的值）则只是属于层叠顺序中的第六层，并没有形成层叠上下文

### z-index
元素的 z-index 值只在同一个层叠上下文中有意义。如果父级层叠上下文的层叠等级低于另一个层叠上下文的，那么它 z-index 设的再高也没用。所以如果你遇到 z-index 值设了很大，但是不起作用的话，就去看看它的父级层叠上下文是否被其他层叠上下文盖住了。
 
### 层叠顺序

![](https://user-gold-cdn.xitu.io/2020/7/6/17324a3c35f40937?w=768&h=383&f=png&s=37639)


    注意：
        1. 谁大谁上：当具有明显的层叠水平标示的时候，如识别的z-indx值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。通俗讲就是官大的压死官小的。
        2. 后来居上：当元素的层叠水平一致、层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素。
        3. 定位元素会层叠在普通元素的上面？根本原因就在于，元素一旦成为定位元素，其z-index就会自动生效，此时其z-index就是默认的auto，也就是0级别，根据上面的层叠顺序表，就会覆盖inline或block或float元素。
        4. 不支持z-index的层叠上下文元素天然z-index:auto级别，也就意味着，层叠上下文元素和定位元素是一个层叠顺序的，于是当他们发生层叠的时候，遵循的是“后来居上”准则。
        5. z-index是负值不会覆盖包含块的背景色（但是如果有内容，会被包含块的内容覆盖）
        6. z-index的值影响的元素是定位元素以及flex盒子


## 4. 经典布局
圣杯布局和双飞翼布局达到的效果基本相同，都是侧边两栏宽度固定，中间栏宽度自适应（中间先加载渲染）。 
主要的不同之处就是在解决中间部分被挡住的问题时，采取的解决办法不一样，圣杯布局是在父元素上设置了padding-left和padding-right，在给左右两边的内容设置position为relative，通过左移和右移来使得左右两边的内容得以很好的展现，而双飞翼则是在center这个div中再加了一个div来放置内容，在给这个新的div设置margin-left和margin-right 。 

## 5. 行内元素和块元素的区别
- 块元素：不管内容多少，在浏览器中独占一行。可设置宽高，如果不设置宽高那么它的宽度是100%（占满父级元素一整行）。垂直相邻元素垂直外边距重叠，父子相邻元素上外边距重叠。
- 行内元素：内容决定所占空间的多少，内容多少就占多少空间。不能设置宽高（默认宽高是0）。margin垂直方向无效（margin-top,margin-bottom）,如果设置垂直方向用line-height属性。
- 行内块：共享一行，可设置宽高,多个行内元素排列在一起，他们之间会出席空格。可设置margin,padding属性。集合了块和行内的所有优点。

## 6. clientHeight、offsetHeight、scrollHeight、offsetTop、scrollTop
网页可见区域高：document.body.clientHeight
网页正文全文高：document.body.scrollHeight
网页可见区域高（包括边线的高）：document.body.offsetHeight
网页被卷去的高：document.body.scrollTop
屏幕分辨率高：window.screen.height

## 7. 防止同名CSS污染
1. style加scoped属性（vue）
 - scoped 属性是 HTML5 中的新属性，是一个布尔属性，如果使用该属性，则样式仅仅应用到 style 元素的父元素及其子元素。原理是通过PostCSS 给一个组件中所有的 DOM 添加了一个独一无二的动态属性，然后给 CSS 选择器额外添加了一个对应的属性来选择该组件中的 DOM，这种做法使得样式只作用于含有该属性的 DOM ——组件内部 DOM 。。
 - 缺点：
   - 添加了属性选择器，对于CSS选择器的权重加重
   - 使用第三方组件设置样式不生效。解决方案如下：
   1. 使用 >>> 可以穿透 scoped 属性，修改其他第三方组件的样式。
   ```
   <style scoped>
    外层 >>> 第三方组件 {
      样式
    }
  	</style>
   ```
   2. 用两个 style 标签，一个带 scoped 属性，一个不带 scoped 属性，用来修改第三方组件的样式。
   3. 使用 sass 或 less 的样式穿透 /deep/ 
   ```
    <style scoped>
      /deep/ .title {
        color: #888;
      }
    </style>
    ```
2. 通过在最外层加 id 或者 class 的形式进行区分。
3. CSS Module(react)
  - CSS的规则都是全局的，任何一个组件的样式规则，都对整个页面有效。产生局部作用域的唯一方法，就是使用一个独一无二的class的名字，不会与其他选择器重名。这就是 CSS Modules 的做法。

## 8. CSS选择器以及这些选择器的优先级
- !important（10000）
- 内联样式（1000）
- ID选择器（0100）
- 类选择器/属性选择器/伪类选择器（0010）
- 元素选择器/伪元素选择器（0001）
- 关系选择器/通配符选择器（0000）
    
## 9. display:none visibility:hidden opacity:0 区别
- display: none;

	1. DOM 结构：浏览器不会渲染 display 属性为 none 的元素，不占据空间；
	2. 事件监听：无法进行 DOM 事件监听；
	3. 性能：动态改变此属性时会引起重排，性能较差；
	4. 继承：不会被子元素继承，毕竟子类也不会被渲染；
	5. transition：transition 不支持 display。

- visibility: hidden;

	1. DOM 结构：元素被隐藏，但是会被渲染不会消失，占据空间；
	2. 事件监听：无法进行 DOM 事件监听；
	3. 性 能：动态改变此属性时会引起重绘，性能较高；
	4. 继 承：会被子元素继承，子元素可以通过设置 visibility: visible; 来取消隐藏；
	5. transition：visibility 会立即显示，隐藏时会延时

- opacity: 0;

	1. DOM 结构：透明度为 100%，元素隐藏，占据空间；
	2. 事件监听：可以进行 DOM 事件监听；
	3. 性 能：提升为合成层，不会触发重绘，性能较高；
	4. 继 承：会被子元素继承,且，子元素并不能通过 opacity: 1 来取消隐藏；
	5. transition：opacity 可以延时显示和隐藏

## 10. 头尾固定高度中间高度自适应布局
1. flex: 容器设置`display: flex;flex-direction: column;`，中间栏设置`flex: 1` 即 ` flex-grow: 1;flex-shrink: 1;flex-basis: 0;`
2. absolute: 头尾和中间都设置绝对定位，头部top:0;底部bottom:0;中间设置top值为头部高度，bottom值为底部高度，不设高度。 
    
## 11. SCSS
1. 变量$开头
2. 嵌套：嵌套选择器，嵌套中的父选择器用&表示，可嵌套属性
3. 导入：导入变量优先级问题，可以加!default。可以嵌套导入。
4. 注释：/*注释*/:这种注释会被保留到编译后的css文件中，//注释:这种不会
5. 混合器：使用@mixin指令声明一个函数，可传参和设置默认值，使用函数的关键字为@include。有重复的代码片段都可以考虑使用混合器将他们提取出来复用。
6. 继承：使用%定义一个被继承的样式，通过关键字@extend即可完成继承。

## 12. px/em/rem的区别
- px像素（Pixel）: 相对长度单位。像素px是相对于显示器屏幕分辨率而言的。
- em: 是相对长度单位。相对于当前对象内文本的font-size。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。会继承父级元素的font-size
- rem: CSS3新增的一个相对单位（root em，根em），相对的是HTML根元素font-size



# JS
## 1. 节流和防抖

函数节流: 指定时间间隔内只会执行一次任务；

函数防抖: 触发事件后 n 秒后才执行函数，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。防抖函数分为非立即执行版和立即执行版。

### 使用场景
在进行resize、scroll、mousemove，输入框内容校验等操作时，如果事件处理函数调用的频率无限制，会加重浏览器的负担，导致用户体验非常糟糕。此时我们可以采用debounce（防抖）和throttle（节流）的方式来减少调用频率，同时又不影响实际效果。

    函数节流与函数防抖的原理非常简单，巧妙地使用 setTimeout 来存放待执行的函数，这样可以很方便的利用 clearTimeout 在合适的时机来清除待执行的函数。
    
## 2.函数柯里化
Currying 为实现多参函数提供了一个递归降解的实现思路——把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数
### 使用场景
  1. 参数复用
  2. 延迟执行

## 3. ES6/ES7/ES8特性
### ES6
  常用：类(class)、模块化(export/import)、箭头函数、函数参数默认值、解构赋值、模板字符串、对象属性简写、延展操作符、Promise、支持let/const
  - 注意：
   1. 类的数据类型就是函数，类本身就指向构造函数。 class 类内部this指向实例对象，把属性定义在this上即 this.a = 'xxx'  则这个a属性是实例的自有属性；直接定义在类上的属性则是原型对象上的属性。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。类的内部所有定义的方法，都是不可枚举的（non-enumerable）。
   2. export default输出则不用知道模块内有哪些属性和方法，本质上，export default就是输出一个叫做default的变量或方法。
   3. 类和模块的内部，默认就是严格模式。
   4. 设置函数参数默认值，则参数不能在函数体内改变。如果设置了默认值，且有重复的形参，则会报错。
   
### ES7
  - Array.prototype.includs()
  - 指数运算符**

  
### ES8
  - async/await
  - Object.values()
  - Object.entries()
  - String.prototype.padStart/String.prototype.padEnd
  - Object.getOwnPropertyDescriptors()
  - 函数参数列表结尾允许逗号

## 4. 怎么实现异步请求
    原生ajax和vue的axios都是可以发异步请求的
 - 回调函数
 - promise对象
 - generator函数
 - async函数
 
  注意：    

  - async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数。
  - async 函数中可能会有 await 表达式，async 函数执行时，如果遇到 await 就会先暂停执行 ，等到触发的异步操作完成后，恢复 async 函数的执行并返回解析值。
  - await 关键字仅在 async function 中有效。如果在 async function 函数体外使用 await ，你只会得到一个语法错误。
  - promise和async/await的区别
    1. Promise的出现解决了传统callback函数导致的“地域回调”问题，但它的语法导致了它向纵向发展行成了一个回调链，遇到复杂的业务场景，这样的语法显然也是不美观的。Async/Await可以让异步代码看起来就像同步代码那样，大大提高了异步代码的可读性。
    2. async await与Promise一样，是非阻塞的。

## 5. 遍历对象的方法
  - for in

    主要用于遍历对象的可枚举属性，包括自有属性、继承自原型的属性
    
  - Object.keys
    
    此方法返回一个数组，元素均为对象自有可枚举的属性


  - Object.getOwnPropertyNames

    此方法用于返回返回一个数组，元素为对象的自有属性，包括可枚举和不可枚举的属性

## 6. this指向
（参考：https://juejin.im/post/59bfe84351882531b730bac2）

this 永远指向最后调用它的那个对象;

    注意:
        1. 如果使用严格模式的话，全局对象就是 undefined
        2. 非严格模式下，setTimeout 的对象是 window，严格模式下是undefined
        
### 不同情况下的this指向 
1. 构造函数中，this指向实例对象
2. 在全局中直接调用的函数，this指向全局
3. 箭头函数中this指向即外层非箭头函数的this
4. 当做属性调用时，this指向最后调用它的那个对象。
5. 定时器内的this指向全局，严格模式下指向undefined 

### 怎么改变this指向
 1. 使用 ES6 的箭头函数
 
    注意：箭头函数的 this 始终指向函数定义时的 this，而非执行时。箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 window。
 2. 使用 apply、call、bind
 3. new 实例化一个对象
 

## 7. call/apply/bind的区别

相同：apply、call、bind 都是可以改变 this 的指向的，即改变对象的执行上下文。

不同：
    1. apply: 接收的是一个包含多个参数的数组。
    2. call: 接收若干个参数列表
    3. bind: 返回值是一个函数

## 8.new 的过程（即自定义构造函数）
  1. 首先函数接受不定量的参数，第一个参数为构造函数，接下来的参数被构造函数使用
  2. 然后内部创建一个空对象 obj
  3. 将新创建的空对象的隐式原型指向其构造函数的显示原型
  4. 将构造函数的 this 指向这个对象
  5. 如果无返回值或者返回一个非对象值，则将 obj 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。
  

## 9. 原型对象/构造函数/原型链
（具体参考：https://juejin.im/post/5cc99fdfe51d453b440236c3）

1. 当任意一个普通函数用于创建一类对象时，它就被称作构造函数，或构造器。
2. 原型对象即实例对象自己构造函数内的prototype对象。prototype对象用于放某同一类型实例的共享属性和方法，实质上是为了内存着想。constructor属性就是被当做共享属性放在它们的原型对象中。也就是prototype对象保存着构造函数给它的实例们调用的共享属性和方法。向原型对象中添加或修改共有属性或方法：类型名.prototype.新成员=值/function(){...}。另外实例对象可以通过 实例.prototype = new xxx 改变实例对象的prototype指向。
3. constructor属性其实就是一个拿来保存自己构造函数引用的属性
4. 对象.constructor指向创建该对象的构造函数；Function函数，它是JS的内置对象，它的构造函数是它自身，所以内部constructor属性指向自己。Function函数同样是Object这类内置对象的构造函数。在JS里，函数就是Function函数的实例对象。
5. 实例person1.constructor = xxx；改的并不是原型对象上的共享属性 constructor，而是给实例person1加了一个constructor属性。
6. 所有函数的__proto__指向他们的原型对象，即Function函数的prototype对象
7. 实例对象.__proto__ = 创建自己的构造函数内部的prototype（原型对象）；
8. 实例对象.__proto__.constructor = 创建自己的构造函数
9. 实例对象.constructor 等于 实例对象.__proto__.constructor，因为当在一个实例对象上找不到某个属性时，JS就会去它的原型对象上找是否有相关的共享属性或方法
10. 函数内的prototype也不过是个普通的对象，并且默认也都是Object对象的实例。
11. 最后一个prototype对象是Object函数内的prototype对象。Object函数作为JS的内置对象，是所有对象通过原型链追溯到最根的构造函数。
12. Object函数的prototype中的__proto__指向null。如果指向自己的prototype，那当找不到某一属性时沿着原型链寻找的时候就会进入死循环，所以必须指向null，这个null其实就是个跳出条件。
13. __proto__属性在原型链中起着关键作用，它将一个个实例和原型对象关联在一起，但由于所关联的原型对象也有可能是另一个类型的实例对象，所以就形成了串连的形式，也就形成了我们所说的原型链。

![](https://user-gold-cdn.xitu.io/2020/7/8/1732e7851fc9118a?w=936&h=279&f=png&s=20168)

![](https://user-gold-cdn.xitu.io/2020/7/8/1732e788597e0436?w=936&h=297&f=png&s=22658)

![](https://user-gold-cdn.xitu.io/2020/7/8/1732e78b4305d0d0?w=936&h=266&f=png&s=17366)

## 10. JavaScript 数据类型
### 基本类型（存放在栈中）
  基本数据类型是指存放在栈中的简单数据段，数据大小确定，内存空间大小可以分配，它们是直接按值存放的，所以可以直接按值访问。基本数据类型的值是没有办法添加属性和方法的 

  String、Number、Boolean()、Null、Undefined、Symbol(唯一值)、BigInt(大数据值)

### 引用数据类型（存放在堆内存中的对象）
  引用类型数据在栈内存中保存的实际上是对象在堆内存中的引用地址。通过这个引用地址可以快速查找到保存在堆内存中的对象

  对象(Object)：Array、Date、RegExp、Function、Error

### 注意一些特殊值：

 1. NaN 不是有效数字，但是属于Number类型。
    - console.log(typeof NaN)  // =>'number'

### 数据类型转换
#### 把其他数据类型转换为Number类型
1. 特定需要转换为Number的
  - Number([val]) 
    - 只要有非数字字符(不包括‘.’)就返回NaN
    - 注意：null转换为数字为0，undefined转换为数字为NaN，任何数字和NaN做数学运算都为NaN。
  - parseInt([value],[n])
    - 把[value]把value转换为数字（内核机制：需要把value先变为字符串，然后从字符串左侧第一个字符查找，把找到的有效数字字符转换为数字，直到遇到一个非有效数字字符为止；
    - 看做[n]进制的数据，最后转换为十进制
    - [n]不写：默认是10，特殊情况字符串是以0X开头，默认值是16进制
    - [n]范围 2~36之间  不在这个之间的  除了0和10一样,剩下结果都是NaN
  - parseFloat([val])
    - 如果在解析过程中遇到了正负号（+ 或 -）、数字 (0-9)、小数点，或者科学记数法中的指数（e 或 E）以外的字符，则它会忽略该字符以及之后的所有字符，返回当前已经解析到的浮点数。同时参数字符串首位的空白符会被忽略。
    - 如果参数字符串的第一个字符不能被解析成为数字，则 parseFloat 返回 NaN。
2. 隐式转换（浏览器内部默认要先转换为Number在进行计算的）
    - isNaN([val])
    - 数学运算（特殊情况：+在出现字符串的情况下不是数学运算，是字符串拼接，即 ‘+’两边只要出现一个字符串就做拼接）
    - 在==比较的时候，有些值需要转换为数字再进行比较
    

#### 把其它数据类型转换为字符串
1. 能使用的办法
  - toString() --> 注意：转换null和undefined报错
  - String()
  - 注意：空数组[]转换为字符串为空字符串；对象包括空对象{}转换为 “[object Object]”。
2. 隐式转换（一般都是调用其toString）
  - 加号运算的时候，如果某一边出现字符串，则是字符串拼接
  - 把对象转换为数字，需要先toString()转换为字符串，再去转换为数字；对象转换为字符串先valueOf() 再 toString()
  - 基于alert/confirm/prompt/document.write...这些方式输出内容，都是把内容先转换为字符串，然后再输出的

 
#### 把其它数据类型转换为布尔
1. 基于以下方式可以把其它数据类型转换为布尔
  - ! 转换为布尔值后取反
  - !! 转换为布尔类型
  - Boolean([val])
2. 隐式转换
  - 在循环或者条件判断中，条件处理的结果就是布尔类型值
3. 规则
  - 只有 ‘0、NaN、null、undefined、空字符串’ 五个值会变为布尔的FALSE，其余都是TRUE
  


#### 在==比较的过程中，数据转换的规则：
1. 【类型一样的几个特殊点】
  - {}=={}：false  对象比较的是堆内存的地址
  - []==[]：false
  - NaN==NaN：false
2. 【类型不一样的转换规则】
  - null==undefined：true，但是换成===结果是false（因为类型不一致），剩下null/undefined和其它任何数据类型值都不相等
  - 字符串==对象  要把对象转换为字符串
  - 剩下如果==两边数据类型不一致，都是需要转换为数字再进行比较
3. 注意：比较的优先级最低，如果两边需要计算先计算两边的


## 11. 判断数据类型的四种方法
 1. typeof 
    - 检测出来是字符串，字符串中包含对应数据类型。
    - 缺陷：对于原始类型来说，除了 null 都可以显示正确的类型，null是 object; 对于对象来说，除了函数返回function其他都会返回 object，所以说 typeof 并不能准确判断变量到底是什么类型
 2. instanceof
    - 实现原理就是只要右边变量的 prototype 在左边变量的原型链上即可。因此，instanceof 在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 prototype，如果查找失败，则会返回 false，告诉我们左边变量并非是右边变量的实例。
    - 这种方法判断数据类型不一定准确，慎用。比如 数组也可以被判断为Object.
    - instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。
 3. constructor
 
    问题：
    1. null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。
    2. 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object
    
 4. Obejct.prototype.toString.call()
    - toString()是Object的原型方法，调用该方法，默认返回当前对象的[[Class]]。这是一个内部属性，其格式为[object Xxx],其中Xxx就是对象的类型。
    - 对于Object对象，直接调用toString()就能返回[object Object]，而对于其他对象，则需要通过call、apply来调用才能返回正确的类型信息。
 

### 建议：
基本数据类型用typeof,对象用Obejct.propertype.toString.call()
```
function getType(obj){
  let type  = typeof obj;
  if(type != "object"){
    return type;
  }
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1');
}
```

## 12. 赋值、浅拷贝和深拷贝
### 赋值
引用地址的拷贝。修改赋值后的数据，不管是基本数据类型还是引用数据类型，都会影响到原数据。

### 浅拷贝
一层拷贝。是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。

### 深拷贝(deepClone)
无限层级拷贝。是将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象。

对于一个引用类型，如果直接将它赋值给另一个变量，由于这两个引用指向同一个地址，这时改变其中任何一个引用，另一个都会受到影响。当我们想复制一个对象并且切断与这个对象的联系，就要使用深拷贝。对于一个对象来说，由于可能有多层结构，所以我们可以使用递归来解决这个问题

- 实现方式：
  1. JSON.parse(JSON.stringify())
  	 - 缺陷：
	  	  1. 时间对象变成字符串类型；
	  	  2. 正则和Error对象变成空对象；
	  	  3. 函数和undefined会弄丢；
	  	  4. NaN、Infinity和-Infinity会变成null；
	  	  5. JSON.stringify()只能序列化对象的可枚举的自有属性，例如 如果obj中的对象是有构造函数生成的， 则使用JSON.parse(JSON.stringify(obj))深拷贝后，会丢弃对象的constructor；
	  	  6. 如果对象中存在循环引用的情况也无法正确实现深拷贝。
  2. 函数库lodash的_.cloneDeep方法
  3. jQuery提供的$.extend()方法
  
        `$.extend(deepCopy, target, object1, [objectN])//第一个参数为true,就是深拷贝`
  4. 手写：循环加遍历

## 13.回调地狱
    即函数作为参数层层嵌套
### 解决办法：
模块化、promise、Generator、await/async

## 14.跨域
  (参考视频：https://www.bilibili.com/video/BV1SE411r7yk)
### 解决方案：
 - 同源方案：生产上后台前端都部署到同一台服务器；平时开发测试时，前端在本地用xampp(把MySql/php/apache等集合在一起的)构建一个web服务器，修改host文件，把本地服务器域名定位到请求后台数据对应的服务器域名，模拟同源效果。
 - JSONP
    - Web前端事先定义一个用于获取跨域响应数据的回调函数，并通过没有同源策略限制的script标签发起一个请求（将回调函数的名称放到这个请求的query参数里），然后服务端返回这个回调函数的执行，并将需要响应的数据放到回调函数的参数里，前端的script标签请求到这个执行的回调
    - 函数后会立马执行，于是就拿到了执行的响应数据。

    - 原理：
        1. 首先在客户端注册一个callback方法，放到window对象上,然后把callback的名字（callbackFunction）传给服务器。
        2. 服务器先生成 JOSN 数据。
    	3. 将 JOSN 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档（如callbackFunction(JOSN)），返回给客户端。
    	4. 客户端浏览器，将返回的JS标签插入DOM，解析script标签后，会执行callbackFunction(JOSN)。
    	通过这种方式，即可实现跨域获取数据。
    - 注意：
        1. 在 jquery 中，JSOP 被封装在 $.ajax() 方法中，但是他的实现原理与 ajax 完全不同。
    	2. JSONP的兼容性较好，在更加古老的浏览器中都可以运行，不需要XMLHttpRequest或ActiveX的支持。
    	3. JSONP只支持GET请求而不支持POST等其它类型的HTTP请求。
    	4. JSONP在调用失败的时候不会返回各种HTTP状态码（解决方法：添加timeout参数，虽然JSONP请求本身的错误没有被捕获，但是最终会因为超时而执行error回调）。
    	5. 在使用JSONP的时候必须要保证使用的JSONP服务必须是安全可信的。万一提供JSONP的服务存在页面注 入漏洞，它返回的javascript都将被执行，若被注入这是非常危险的。

 - CORS(跨域资源共享)
    - 前端：正常发请求
    - 后端：需要在处理正式请求之前先处理预检OPSIONS请求，看是否允许当前跨域请求。服务器端需要设置响应头的Access-Control-Allow-Methods，Access-Control-Allow-Headers，Access-Control-Allow-Origin，Access-Control-Allow-Credentials等字段，来允许哪个源可以进行跨域传输，哪些方式，是否允许携带cookie，允许哪些请求头。如果允许源写的*，则不允许携带cookie，如果写具体地址，只支持写一个。
    
    
 - 代理
 
    是指利用服务器之间没有同源策略限制，可以在请求的域名下部署一个代理服务器，通过代理服务器去请求别的跨域的服务器。

    - proxy 代理 -- 需要前端配webpack、webpack-dev-server
    - nginx 反向代理 -- 不需要前端做什么
 
## 15. 面向对象OOP
- 对象：同时存储一个事物的多个属性和功能的存储空间
- 面向对象：程序中，都是用对象来描述现实中一个具体事物。包含了属性和方法的对象是类的实例，但是在js是没有类的概念，是直接使用对象来实现编程的。
- 三大特点：封装、继承、多态
- 如何实现继承（参考：https://github.com/ziyi2/js/blob/master/JS%E7%B1%BB%E5%92%8C%E7%BB%A7%E6%89%BF.md；https://juejin.im/post/6844903833492013064）
  1. 原型链继承
     - 实现方式：Son类的prototype引用Father类的实例对象，`Son.prototype = new Father()`, 从而实现Son类的原型对象和实例对象都可以继承Father类原型对象和实例对象的属性和方法。
     - 缺陷：引用类型值的原型属性会被所有实例共享。
	 

  2. 构造函数继承
     - 实现方式：在Son类构造函数中调用Father类构造函数并改变this指向。`Father.call(this)`
     - 缺陷：没法实现方法的共享
     
  3. 原型链和构造函数组合继承
     - 实现方式：前两者加上调整Son原型对象的constructor指向，`Son.prototype.constructor = Son`
     - 缺陷：子类的原型对象中还存在父类实例对象的实例属性

  4. 原型继承
  5. 寄生式继承
  6. 寄生组合式继承
  7. ES6中的class、extend、super
     - class: 用来声明类，也就是js中充当类的自定义函数 
     - extend: 表示用哪个作为父类来继承
     - super: 表示父类的构造器函数，可以向内部传参
            
- 如何创建对象：
  1. 利用构造函数创建对象，var obj = new 函数名()
  2. 使用Object构造函数创建对象，var obj = new Object()
  3. 字面量创建对象，var obj = {}; obj.name = 'xxxx'
  4. 结合构造函数和Object创建对象（工厂模式）

## 16. XML和JSON的区别
(1).数据体积方面。

JSON相对于XML来讲，数据的体积小，传递的速度更快些。

(2).数据交互方面。

JSON与JavaScript的交互更加方便，更容易解析处理，更好的数据交互。

(3).数据描述方面。

JSON对数据的描述性比XML较差。

## 17. 闭包相关
1. javascript 语言层面只原生支持两种作用域类型：全局作用域 和 函数作用域 。全局作用域程序运行就有，函数作用域只有定义函数的时候才有，它们之间是包含的关系。
2. 作用域之间是可以嵌套的，我们把这种嵌套关系称为 作用域链。
3. 可执行代码在作用域中查询变量时，只能查询 本地作用域 及 上层作用域，不能查找内部的函数作用域。JS 引擎搜索变量时，会先询问本地作用域，找到即返回，找不到再去询问上层作用域...层层往上，直到全局作用域。
4. javascript 中使用的是 “词法作用域”，因此函数作用域的范围在函数定义时就已经被确定，和函数在哪执行没有关系。
5. 有权访问另一个函数内部变量的函数，我们称为 闭包。闭包的本质是利用了作用域的机制，来达到外部作用域访问内部作用域的目的。
6. 闭包的使用场景非常广泛，然而过度使用会导致闭包内的变量所占用的内存空间无法释放，带来 内存泄露 的问题。
7. 我们可以借助于 chrome 开发者工具查找代码中导致了内存泄露的代码。
8. 避免内存泄露的几种方法：避免使用全局变量、谨慎地为DOM 绑定事件、避免过度使用闭包。最重要的，还是代码规范。 
9. 作用：
 - 1. 闭包具有保护作用:保护私有变量不受外界的干扰。
 - 2. 闭包具有保存作用:形成不销毁的栈内存，把一些值保存下来，方便后面的
调取使用。
	

## 18.作用域链

作用域是在运行时代码中的某些特定部分中变量，函数和对象的可访问性。换句话说，作用域决定了代码区块中变量和其他资源的可见性。作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。

作用域链的作用是保证执行环境里有权访问的变量和函数是有序的，作用域链的变量只能向上访问，变量访问到window对象即被终止，作用域链向下访问变量是不被允许的。
当代码在一个环境中创建时，会创建变量对象的一个作用域链（scope chain）来保证对执行环境有权访问的变量和函数。作用域第一个对象始终是当前执行代码所在环境的变量对象（VO）。如果是函数执行阶段，那么将其activation objec t（AO）作为作用域链第一个对象，第二个对象是上级函数的执行上下文AO，下一个对象依次类推。

## 19. 创建ajax的过程
 1. 创建XMLHttpRequest对象,也就是创建一个异步调用对象.
 2. 创建一个新的HTTP请求,并指定该HTTP请求的方法、URL及验证信息.
 3. 如果是post请求，需要在状态变化函数与发送请求之前先设定请求报文头
 4. 设置响应HTTP请求状态变化的函数.
 5. 发送HTTP请求.
 6. 获取异步调用返回的数据.
 7. 使用JavaScript和DOM实现局部刷新.
 

## 20. Javascript垃圾回收方法
 标记清除、引用计数

## 21. Object.is 原理
1. Object.is()是ES6新增的用来比较两个值是否严格相等的方法，与===的行为基本一致。
先说===，只需要利用下面的规则来判断两个值是否恒等就行了：

 - 如果两个值都是null，或者都是undefined，那么相等。

 - 如果两个值都引用同一个对象或函数，那么相等，即两个对象的物理地址也必须保持一致；否则不相等。

 - 如果两个值都是同样的Boolean值，那么相等。

 - 如果两个都是字符串，每个位置的字符都一样，那么相等；否则不相等。

 - 值得注意的是，如果两个值中至少一个是NaN，那么不相等（判断一个值是否是NaN，可以用isNaN()或Object.is()来判断）。

 - 如果两个都是数值，并且是同一个值，那么相等； 如果类型不同，就不相等

2. 和===不同的是：0不等于-0。 NaN等于自身
![](https://user-gold-cdn.xitu.io/2020/7/13/17348d0f8b32dbbc?w=206&h=106&f=png&s=3884)

## 22. Event Loop
- 浏览器中的Event Loop
	
	- 执行栈中的代码(同步代码)永远最先执行

	- 宏任务： script （主代码块）、setTimeout 、setInterval 、setImmediate 、I/O 、UI rendering
	  - 注意：宏任务并非全是异步任务，主代码块就是属于宏任务的一种
	在执行完每一个宏任务之后，会去看看微任务队列有没有新添加的任务，如果有，会先将微任务队列中的任务清空，才会继续执行下一个宏任务

	- 微任务：process.nextTick（Nodejs） 、Promise 、Await(下面代码)、Object.observe 、MutationObserver
	
	- 执行优先级上，先执行宏任务macrotask，再执行微任务mincrotask。
	
	- 根据事件循环机制，梳理一下流程：
	    1. 执行一个宏任务（首次执行的主代码块或者任务队列中的回调函数）
	    2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
	    3. 宏任务执行完毕后，立即执行当前微任务队列中的所有任务（依次执行）
	    4. JS引擎线程挂起，GUI线程执行渲染
	    6. GUI线程渲染完毕后挂起，JS引擎线程执行任务队列中的下一个宏任务

- Node中的Event Loop会在每次切换队列的时候 清空微任务队列，也就会会将当前队列都执行完，在进入下一阶段的时候检查一下微任务中有没有任务

## 23. DOM事件流
- 当在页面上某个元素触发特定事件时, 比如点击, 除了被点击的目标元素, 所有祖先元素都会触发该事件, 一直到 window.
- 事件流又称为事件传播，DOM2级事件规定的事件流包括三个阶段：
	1. 事件捕获阶段(capture phase)：事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在事件到达预定目标之前就捕获它
	2. 处于目标阶段(target phase)
	3. 事件冒泡阶段(bubbling phase)：事件开始时由最具体的元素(文档中嵌套层次最深的那个节点)接收，然后逐级向上传播到较为不具体的节点(文档)

- 首先发生的是事件捕获，为截获事件提供了机会。然后是实际的目标接收到事件，最后一个阶段是冒泡阶段，可以在这个阶段对事件做出响应
- addEventListener()方法中的第三个参数设置为true时，即为事件捕获阶段
- target/currentTarget/relateTarget具体指向什么目标。
  - target 是事件触发的真实元素
  - currentTarget 是事件绑定的元素
  - relatedTarget 事件属性返回与事件的目标节点相关的节点。(仅对mouseover 和mouseout有用)
- 事件委托：利用冒泡的原理，把事件加到父级上，触发执行效果。好处：
    1. 减少内存的占用,减少事件注册；
    2. 当新增子DOM 对象时,无需再对其进行事件绑定,对于动态内容部分尤为合适

## 24. 变量提升
- var命令会发生“变量提升”现象，即变量可以在声明之前使用，值为undefined
- 当前上下文代码执行之前，会把var/function声明或定义，带var的只声明，带function声明+定义，如果遇到{}新老浏览器表现不一致(兼容ES3、兼容ES6)
  - IE浏览器 <= IE10: 不管{}，还是一如既往的function声明+定义，而且不会存在块级作用域。
  - 新版本浏览器：{}中的function在全局下声明不再定义。{}中出现的function/let/cosnt会创建一个块级作用域。

![](https://user-gold-cdn.xitu.io/2020/7/16/173569af533c7e8c?w=907&h=515&f=png&s=91553)

  -  注意：除了上面那种情况，另外一种情况也会产生块级作用域：函数有形参赋值了默认值且函数体中有单独声明过某个变量。这样在函数运行的时候，会产生两个上下文：函数执行形成的私有上下文 EC(FUNC)；函数体大括号包起来的是一个块级上下文 EC(BLOCK)
  
![](https://user-gold-cdn.xitu.io/2020/7/16/17356b154ec17044?w=783&h=578&f=png&s=73006)

## 25.自执行匿名函数
- 格式：
    - (function () { /* code */ } ()); 
    - !function () { /* code */ } ();
    - ~function () { /* code */ } ();
    - -function () { /* code */ } ();
    - +function () { /* code */ } ();

## 26. 消抖和节流
- 防抖：
 触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间
- 节流：
 每隔一段时间执行一次

## 27. let/const/var 的区别
  1. var声明的变量会存在变量提升，而let 和 const的变量不会存在变量提升
  2. var声明的变量会挂载到window上，会放在全局，let 和 const声明的变量不会
  3. let和const声明的变量会形成块级作用域，var不会
  4. let和const不能在同一作用域下声明同一变量名，而var可以
  5. const用来声明一个常量，一旦声明其值不可更改。所以const在声明的时候必须要初始化，不能先声明再初始化否则会报错。

## 28. 数组的遍历
  1. for，优化：先将数组长度存起来
  2. for…in…，输出的是数组索引
  3. for…of…（ES6），输出的是每个元素
  4. forEach: 调用数组的每个元素，并将元素传递给回调函数，没有返回值。
  ```
    var arr = [1, 2, 3, 4, 5, 6]
    arr.forEach(function (item, index, array) {
        console.log(item)     // 1 2 3 4 5 6
        console.log(array)    // [1, 2, 3, 4, 5, 6]
    })
  ```
  5. map: 遍历每一个元素，返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值，不会改变原始数组。
  ```
  var arr = [1, 2, 3, 4, 5, 6]
  var newArr = arr.map(function (item, index) {
    return item * item
  })
  console.log(newArr)    // [1, 4, 9, 16, 25, 36]
  ```
  6. filter: 遍历数组，过滤出符合条件的元素并返回一个新数组，不会改变原始数组。
  7. some：遍历数组，只要有一个以上的元素满足条件就返回 true，否则返回 false
  8. every：遍历数组，每一个元素都满足条件 则返回 true，否则返回 false
  9. find（ES6）：遍历数组，返回符合条件的第一个元素，如果没有符合条件的元素则返回 undefined
  10. findIndex（ES6）：遍历数组，返回符合条件的第一个元素的索引，如果没有符合条件的元素则返回 -1
  
### map 和 forEach 的区别
#### 相同点：
  1. 都是循环遍历数组中的每一项
  2. forEach和map方法里每次执行匿名函数都支持3个参数，参数分别是item（当前每一项），index（索引值），arr（原数组）
  3. 匿名函数中的this都是指向window
  4. 只能遍历数组
  5. 都不会改变原数组
#### 区别：
  - map方法：
	1. map方法返回一个新的数组，数组中的元素为原始数组调用函数处理后的值。
	2. map方法不会对空数组进行检测，map方法不会改变原始数组。
	3. 浏览器支持：chrome、Safari1.5+、opera都支持，IE9+
	4. 若arr为空数组，则map方法返回的也是一个空数组。
	
  - forEach
    1. forEach方法用来调用数组的每个元素，将元素传给回调函数
    2. forEach对于空数组是不会调用回调函数的。
    3. 无论arr是不是空数组，forEach返回的都是undefined。这个方法只是将数组中的每一项作为callback的参数执行一次。

## 29. 数组去重
(参考：https://segmentfault.com/a/1190000016418021)
  1. 用ES6的Set，但无法去掉空对象{}
  2. 双层循环，内循环时比较值，值相同则用splice删除后面那个
  3. 利用indexOf去重
    - 新建一个空的结果数组，for 循环原数组， ，如果有相同的值则跳过，不相同则push进数组。 
  4. 利用sort()。利用sort()排序方法，然后根据排序后的结果进行遍历及相邻元素比对。但是NaN、{}没有去重。
  5. 利用includes检测数组是否有某个值，不能去重{}
  6. 利用filter + hasOwnProperty，所有类型都去能去重。
  7. 利用filter + indexOf，筛选条件为在原始数组中的第一个索引===当前索引值，否则返回当前元素。
  8. 利用reduce+includes
  
## 30. new String() 与 String（）
- new String() 生成字符串对象
- String() 生成字符串

## 31. 事件绑定和事件监听的区别
1. addeventlistener 可以绑定多次同一个事件，且都会执行。
2. onclick：在DOM结构如果绑定两个 “onclick” 事件，只会执行第一个；在脚本通过匿名函数的方式绑定的只会执行最后一个事件。

## 32. Promise
- all: 接收的参数是一个数组，返回的是一个Promise，判断是否每一项都处理完了，如果都处理完就走resolve，只要有一个传入的promise没有成功则走reject。

## 33. 设计模式
（https://juejin.im/post/6844903734800023559）
- 工厂模式：就是为了创造对象
- 单例模式：保证一个特定类仅有一个实例。举例：实现一个遮罩层。
- 模块模式
- 代理模式
- 职责链模式
- 策略模式
- 发布订阅模式：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知


# TS
## 1. TS的理解：
1. ts是js的超集，意味着js本身的语法在ts里面也能跑的通。ts一方面是对js加上了很多条条框框的限制，另一方面是拓展了js的一些能力。
2. TS有类型检查。
3. 需要标记类型，进行声明(interface/type)，有清晰的函数参数/接口属性、进行
静态检查、生成api文档、配合现代编辑器有各种提示。

# 综合

# 综合
## 1. React和Vue的区别
(参考：https://juejin.im/post/6844904040564785159)

- 相同点：

   1、react和vue都支持服务端渲染
    
   2、利用虚拟DOM实现快速渲染，组件化开发，通过props传参进行父子组件数据的传递
    
   3、都是数据驱动视图
    
   4、都有支持native的方案（react的react native，vue的weex）
    
   5、都有状态管理（react有redux，vue有vuex）

- 不同点：

  1、监听数据变化的实现原理不同
    - Vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就能达到很好的性能
    - React 默认是通过比较引用的方式进行的，如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的VDOM的重新渲染
    
  2、数据流的不同
    - vue实现了数据的双向绑定react数据流动是单向的

  3、HoC 和 mixins
    - 在 Vue 中我们组合不同功能的方式是通过 mixin，而在React中我们通过 HoC (高阶组件）。
  
  4、组件通信的区别
    - Vue中子组件向父组件传递消息有两种方式：事件和回调函数，而且Vue更倾向于使用事件。但是在 React 中我们都是使用回调函数的，这可能是他们二者最大的区别。
 
  5、模板渲染方式的不同
    - React 是通过JSX渲染模板。React是在组件JS代码中，通过原生JS实现模板中的常见语法，比如插值，条件，循环等，都是通过JS语法实现的
    - Vue是通过一种拓展的HTML语法进行渲染。Vue是在和组件JS代码 分离的单独的模板中，通过指令来实现的，比如条件语句就需要 v-if 来实现
   
  6、Vuex 和 Redux 的区别
    - Redux 使用的是不可变数据，而Vuex的数据是可变的。Redux每次都是用新的state替换旧的state，而Vuex是直接修改。
    - Redux 在检测数据变化的时候，是通过 diff 的方式比较差异的，而Vuex其实和Vue的原理一样，是通过 getter/setter来比较的（如果看Vuex源码会知道，其实他内部直接创建一个Vue实例用来跟踪数据变化）。

## 2. MVC、MVP和MVVM的区别
1. MVC
  - View：负责视图显示，将Model中的数据显示出来。检测用户的键盘、鼠标等行为，传递调用Controller执行应用逻辑,View更新需要重新获取Model的数据
  - Controller：负责View和Model之间协作的应用逻辑或业务逻辑处理，根据用户行为对Model中的数据进行修改。
  - Model：负责保存数据，Model变更后，通过观察者模式通知View更新视图。
2. MVP
  - Passive View：View不再处理同步逻辑，对Presenter提供接口调用。由于不再依赖Model，可以让View从特定的业务场景中抽离，完全可以做到组件化。
  - Presenter（Supervising Controller）： 和经典MVC的Controller相比，
  - 任务更加繁重，不仅要处理应用业务逻辑，还要处理同步逻辑(高层次复杂的UI操作)。
  - Model：Model变更后，通过观察者模式通知Presenter，如果有视图更新，Presenter又可能调用View的接口更新视图。
3. MVVM
  - ViewModel：内部集成了Binder(Data-binding Engine，数据绑定引擎)，在MVP中派发器View或Model的更新都需要通过Presenter手动设置，而Binder则会实现View和Model的双向绑定，从而实现View或Model的自动更新。
  - View：可组件化，例如目前各种流行的UI组件框架，View的变化会通过Binder自动更新相应的Model。
  - Model：Model的变化会被Binder监听(仍然是通过观察者模式)，一旦监听到变化，Binder就会自动实现视图的更新。

4. 区别
   - 三者都是框架模式，它们的设计目标都是为了解决Model和View的耦合问题。
   - MVC模式出现较早主要应用在后端，在前端领域早期也有应用，如Backbone.js。它的优点是职责分离，分层清晰，多视图更新，缺点是数据流混乱，灵活性带来的维护性问题，依赖强烈，View强依赖Model(特定业务场景)，因此View无法组件化设计。
   - MVP模式在是MVC的进化形式，View可组件化设计，Presenter作为中间层负责MV通信，解决了两者耦合问题，但P层过于臃肿会导致维护问题。
   - MVVM模式在前端领域广泛应用，它不仅解决MV耦合问题，还同时解决了维护两者映射关系的大量繁杂代码和DOM操作代码，在提高开发效果，可读性同时还保持了优越的性能表现。 
  

## 3. 怎么将前端项目部署到服务器
1. 购买云服务器
    - 注意：如果想对外宣传的话，需要购买域名
    - 操作方法：
    1. 购买域名（产品菜单->域名注册->购买）
    2. 解析（控制台->域名）
    3. 备案
2. 搭建服务器环境
    - 就是你本地开发那个环境，放在服务器上，不过设置成生产环境。

3. 部署项目
    - 建议用git，在本地安装好git后，找一个git服务器托管，git push，上传进去，再登录服务器用git clone下载下来，启动服务器，大功告成！

## 4. 前端工程化的认识

前端工程化可以分成四个方面来说，分别为模块化、组件化、规范化和自动化。

### 模块化
- 模块：将一个复杂的程序依据一定的规则(规范)封装成几个块(文件), 最后进行统一的打包和加载，这样能够很好的保证高效的多人协作。
- 模块化的好处：更好的分离, 按需加载；更高复用性；高可维护性。
- 模块化规范：
  - CommonJS：module.export/require，模块输出的是值的拷贝，模块是运行时加载
  - AMD：define()/require()，依赖前置、提前执行
  - CMD：define()/seajs.use()，依赖就近，延迟执行
  - ES6 Module：export/import，模块输出的是值的引用，模块是在编译时加载

### 组件化
不同于模块化，模块化是对文件、对代码和资源拆分，而组件化则是对 UI 层面的拆分。

### 规范化
指的是我们在工程开发初期以及开发期间制定的系列规范

### 自动化
从最早先的 grunt、gulp 等，再到目前的 webpack、parcel。这些自动化工具在自动化合并、构建、打包都能为我们节省很多工作。



## 5. 前端性能优化
### 性能优化
代码层面：避免使用css表达式，避免使用高级选择器，通配选择器。

缓存利用：缓存Ajax，使用CDN，使用外部js和css文件以便缓存，添加Expires头，服务端配置Etag，减少DNS查找等

请求数量：合并样式和脚本，使用css图片精灵，初始首屏之外的图片资源按需加载，静态资源延迟加载。

请求带宽：压缩文件，开启GZIP

### 雅虎35条军规（和上面可能有重复，参考：https://www.jianshu.com/p/fa25121c0f5f）
1. 页面内容：减少http请求，减少DNS查询，避免重定向，缓存AJAX请求，延迟加载，预加载，减少DOM数量，划分内容到不同域名，尽量减少iframe的使用，避免404错误。
2. 服务器：使用CDN，添加Expires和Cache-Control响应头，启用Gzip压缩，配置Etag，尽早输出(flush)缓冲，Ajax请求使用GET方法，避免图片src为空
3. Cookie：减少Cookie大小，静态资源使用无Cookie域名
4. CSS：把样式表放在<head>中，不要使用CSS表达式，使用<link>替代@import，不要使用filter
5. JavaSript：把脚本放在页面底部，使用外部JS和CSS，压缩JS和CSS，移除重复脚本，减少DOM操作，使用高效的事件处理
6. 图片：优化图片，优化CSS Sprite，不要在HTML中缩放图片，使用体积小可缓存的favicon.ico
7. 移动端：保证所有组件都小于25k，把组件打包到一个复合文档里。

### 提高用户体验
1. 根据雅虎35条优化建议保证页面的载入速度。
2. 做好的页面结构，页面重构和用户体验；
3. 对每一像素和细节的苛刻。好的用户体验应该从细节开始，并贯穿于每一个细节，能够让用户有所感知
4. 保证页面的浏览器兼容性，做到可用性和可访问性。

## 6. 前端常见兼容性问题
- 样式兼容
    1. 不同浏览器的默认样式存在差异，可以使用 Normalize.css 抹平这些差异。当然，你也可以定制属于自己业务的 reset.css。比如加一个全局的*{margin:0;padding:0;}来统一。
    2. 部分属性需加浏览器前缀
    ![](https://user-gold-cdn.xitu.io/2020/7/12/173411678b5f4d45?w=642&h=386&f=png&s=20955)

- 交互兼容性
    1. 添加绑定事件：
    	![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2809e1e331e7412389aa5a726384d577~tplv-k3u1fbpfcp-zoom-1.image)
    2. 取消事件监听
    	![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5898b060a509437384e8641bef3825ad~tplv-k3u1fbpfcp-zoom-1.image)
    3. 阻止事件冒泡
     	![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a2ee32f76c144f180b6bbc3984756fb~tplv-k3u1fbpfcp-zoom-1.image)
    4. 取消默认事件
        // event.preventDefault();
        // event.returnValue = false; // IE9以下的浏览器
    5. 事件对象：`event = event || window.event;`
    6. new Date()构造函数使用，正确的兼容用法是'2018/07/05'
    7. 求窗口大小的兼容写法clientWidth、scrollWidth、offsetWidth、scrollTop： 
    例：document.documentElement.scrollTop||document.body.scrollTop;

## 7. 移动端适配方案

### 物理像素和逻辑像素
- 物理像素

设备像素 = 物理像素，设备像素就是屏幕中一个个的点,设备分辨率就是横向点的个数 * 纵向点的个数


- 逻辑像素

设备独立像素 = CSS 像素 = 逻辑像素

### 移动端适配方案
1. rem
  - 设置根元素的字体大小，1rem = 根元素字体大小。CSS用媒体查询根据不同宽度设置根元素字体大小，或者用JS  `document.documentElement.style.fontSize = 100 * (clientWidth /750) + 'px';` 其中100是设计稿的html大小，750是假设的设计稿大小。
  - 写入CSS的尺寸 = UI图标注的尺寸/100px*1rem, 100px为该设计稿对应的html的font-size
  - rem与em的区别：em相对父级元素设置的font-size来设置大小 如果父元素没有设置font-size ，则继续向上查找，直至有设置font-size元素

2. viewport
  - 使用了理想视口，固定高度，宽度自适应。
  `<meta name="viewport" content="width=device-width,initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">`
  - 固定布局视口宽度，使用viewport进行缩放。如荔枝FM的网页宽度始终为640px。缩放比例scale为：`var scale = window.screen.width / 640`
  `<meta name="viewport" content="width=640, minimum-scale = 0.5625, maximum-scale = 0.5625, target-densitydpi=device-dpi">`

3. vw/vh：vw表示相对于视口的宽度，vh表示相对于视口高度

## 8. 滚动吸顶实现方式
(参考：https://juejin.im/post/6844903815041269774)
### 实现方式（后三种方案都是在滚动监听事件里判断是否满足吸顶条件即滚动容器的已滚动高度scrollTop 是否大于需要吸顶的元素到已定位的父级元素或者距离页面顶部的距离offsetTop，满足条件就把吸顶元素设置为position: fixed）
1. 使用 position:sticky 实现
2. 使用 JQuery 的 offset().top 实现
3. 使用原生的 offsetTop 实现
4. 使用 obj.getBoundingClientRect().top 实现
  - 这个 API 可以告诉你页面中某个元素相对浏览器视窗上下左右的距离。使用 obj.getBoundingClientRect().top 代替 scrollTop - offsetTop

### 问题
1. 吸顶的那一刻伴随抖动
  - 出现抖动的原因：在吸顶元素 position 变为 fixed 的时候，该元素就脱离了文档流，下一个元素就进行了补位。就是这个补位操作造成了抖动。
  - 解决为这个吸顶元素添加一个等高的父元素，我们监听这个父元素的 getBoundingClientRect().top 值来实现吸顶效果
2. 吸顶效果不能及时响应
  - 描述：当页面往下滚动时，吸顶元素需要等页面滚动停止之后才会出现吸顶效果
当页面往上滚动时，滚动到吸顶元素恢复文档流位置时吸顶元素不恢复原样，而等页面停止滚动之后才会恢复原样
  - 原因：在 ios 系统上不能实时监听 scroll 滚动监听事件，在滚动停止时才触发其相关的事件。
  - 解决方案：IOS 使用 position:sticky，Android 使用滚动监听 getBoundingClientRect().top 的值。

## 9. 图片懒加载
1. 方案1：将资源路径赋值到img标签的data-xx属性中，而非直接赋值在src属性。监听滚动事件，
持续判断图片是否在用户当前窗口的可视范围内，从而动态将data-xx的值(url)赋值到src里去。判断条件是img的el.getBoundingClientRect().top 小于window.innerHeight（或者document.documentElement.clientHeight），则需要展示图片。可以通过节流函数
2. 方案2：通过IntersectionObserver api判断图片是否出现在可视区域内，不需要监听Scroll来判断。在回调函数中用获取到的目标元素的可见比例intersectionRatio大于0或者isIntersecting为true，则目标元素可见。IntersectionObserver是浏览器原生提供的构造函数，接受两个参数：callback是可见性变化时的回调函数，option是配置对象（该参数可选）

## 10.拖拽

### 我的小程序里的拖拽组件
需要拖拽的元素设置为绝对定位，设一个初始位置，在catchtouchmove事件里控制拖拽元素只能在规定范围内移动，控制条件是根据鼠标在窗口中的垂直水平坐标（e.touches[0].clientY和e.touches[0].clientX）是否超过窗口的高度宽度或者小于0，如果超过了则设置top或者left为范围内的最大值，如果小于0则设置为0。

### H5 拖拽功能
#### 1. 先设置需要拖拽的元素draggable属性为true，表示可拖拽。
#### 2. 事件
 - 拖拽元素支持的事件
   - ondrag 应用于拖拽元素，整个拖拽过程都会调用
   - ondragstart 应用于拖拽元素，当拖拽开始时调用
   - ondragleave 应用于拖拽元素，当鼠标离开拖拽元素是调用
   - ondragend 应用于拖拽元素，当拖拽结束时调用
 - 目标元素支持的事件
   - ondragenter 应用于目标元素，当拖拽元素进入时调用
   - ondragover 应用于目标元素，当停留在目标元素上时调用
   - ondrop 应用于目标元素，当在目标元素上松开鼠标时调用(拖拽元素在目标元素上释放时)
   - ondragleave 应用于目标元素，当鼠标离开目标元素时调用
#### 3. dataTransfer对象
- dataTransfer对象允许在其上存储数据，这使得在被拖动元素与目标元素之间传送信息成为可能。
- 属性：dropEffect、effectAllowed、files、types
- 事件：setData、getData、clearData、setDragImage
#### 4. 注意：
调用 preventDefault() 来避免浏览器对数据的默认处理（drop 事件的默认行为是以链接形式打开）

## 11. 移动端点击事件延迟300毫秒
### 原因：
300 毫秒延迟的主要原因是解决双击缩放(double tap to zoom)。当用户一次点击屏幕之后，浏览器并不能立刻判断用户是确实要打开这个链接，还是想要进行双击操作，因此浏览器就等待 300 毫秒，以判断用户是否再次点击了屏幕。

### 解决方案：
1. 添加viewpoint meta标签，禁止用户进行缩放。
`<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />`

2. FastClick

移动端事件触发顺序：在移动端，手指点击一个元素，会经过：touchstart --> touchmove -> touchend -->click。

fastclick.js的原理是：FastClick的实现原理是在检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并把浏览器在300ms之后真正的click事件阻止掉。

vue中使用

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8fa62185a8aa4bbbb3e4ef4f82a5c6c6~tplv-k3u1fbpfcp-zoom-1.image)

## 12. 点击穿透
### 发生原因：
 - 手指按下之后，会先执行touch事件，然后记录点击的坐标，300ms之后(移动端使用鼠标事件会有300毫秒的延迟)，在该坐标上查找元素，如果该元素绑定了鼠标事件，就把事件执行了
### 点透发生的条件：
 - A 和 B不是后代继承关系(如果是后代继承关系的话，就直接是冒泡子类的话题了)
 - A发生touch， A touch后立即消失， B事件绑定click
 - A z-index大于B，即A显示在B浮层之上
### 解决方案
1. `A.addEventListener('touchend', function(e) {e.preventDefault(); });`
2. 用定时器延迟3ms关闭A
3. 把点击事件换成touch事件

## 13. 移动端兼容性问题
### 1. 点击事件延迟300毫秒
### 2. 点透
### 3. 安卓下大面积触摸会导致触发touchmove的问题
　- 解决方案：在touchmove中判断一下touchstart的上一次位置和当前位置是否一样，一样就使move return掉
### 4. 输入框被遮挡
  - 解决方案：
    - 在focus里去做：获取屏幕高度，比较输入框的底部是否超出了屏幕，超出就让外框向上移动超 出的距离。在软件盘弹出之后（在focus中加个延迟时间），用getBoundingClientRect()获取input的距离视窗的位置，如果input底部到视窗的高度比视窗高度大，说明被遮挡，此时需要上移。
    - 在blur里做：把外框回到原来位置
### 5. 滚动卡顿
` -webkit-overflow-scrolling:touch; overflow-scrolling:touch; `
### 6. iphone及ipad下输入框默认内阴影
`-webkit-appearance:none;`

## 14. 阻塞DOM解析和渲染
- CSS 不会阻塞 DOM 的解析，但会阻塞 DOM 渲染。
- JS 阻塞 DOM 解析，但浏览器会"偷看"DOM，预先下载相关资源。
- 浏览器遇到 <script>且没有defer或async属性的 标签时，会触发页面渲染，因而如果前面CSS资源尚未加载完毕时，浏览器会等待它加载完毕在执行脚本。
- <script>最好放底部，<link>最好放头部，如果头部同时有<script>与<link>的情况下，最好将<script>放在<link>上面

## 15. 长列表渲染
（https://www.cnblogs.com/cangqinglang/p/12341362.html）
### 分析
- 消耗时间最多的两个阶段是Recalculate Style和Layout 
  1. Recalculate Style：样式计算，浏览器根据css选择器计算哪些元素应该应用哪些规则，确定每个元素具体的样式。
  2. Layout：布局，知道元素应用哪些规则之后，浏览器开始计算它要占据的空间大小及其在屏幕的位置。
  
### 方案

#### 时间分片
- 适用于列表项DOM结构比较简单
- 使用 requestAnimationFrame

#### 虚拟列表
- 虚拟列表：其实是按需显示的一种实现，即只对可见区域进行渲染，对非可见区域中的数据不渲染或部分渲染的技术，从而达到极高的渲染性能。
- 实现：在首屏加载的时候，只加载可视区域内需要的列表项，当滚动发生时，动态通过计算获得可视区域内的列表项，并将非可视区域内存在的列表项删除。


# Vue、React、webpack、浏览器、node

# Vue.js
## 1. 运行机制
### 初始化流程
 - 创建Vue实例对象
 - init过程会初始化
 - ，初始化事件中心，初始化渲染、执行beforeCreate周期函数、初始化 data、props、computed、watcher、执行created周期函数等。
 - 初始化后，调用$mount方法对Vue实例进行挂载（挂载的核心过程包括模板编译、渲染以及更新三个过程）。
 - 如果没有在Vue实例上定义render方法而是定义了template，那么需要经历编译阶段。需要先将template 字符串编译成 render function，template 字符串编译步骤如下：

    - parse正则解析template字符串形成AST（抽象语法树，是源代码的抽象语法结构的树状表现形式）
    - optimize标记静态节点跳过diff算法（diff算法是逐层进行比对，只有同层级的节点进行比对，因此时间的复杂度只有O(n))
    - generate将AST转化成render function字符串
    
 - 编译成render function 后，调用$mount的mountComponent方法，先执行beforeMount钩子函数，然后核心是实例化一个渲染Watcher，在它的回调函数（初始化的时候执行，以及组件实例中监测到数据发生变化时执行）中调用updateComponent方法（此方法调用render方法生成虚拟Node，最终调用update方法更新DOM）。
 - 调用render方法将render function渲染成虚拟的Node（真正的 DOM 元素是非常庞大的，因为浏览器的标准就把 DOM 设计的非常复杂。如果频繁的去做 DOM 更新，会产生一定的性能问题，而 Virtual DOM 就是用一个原生的 JavaScript 对象去描述一个 DOM 节点，所以它比创建一个 DOM 的代价要小很多，而且修改属性也很轻松，还可以做到跨平台兼容），render方法的第一个参数是createElement(或者说是h函数)，这个在官方文档也有说明。
 - 生成虚拟DOM树后，需要将虚拟DOM树转化成真实的DOM节点，此时需要调用update方法，update方法又会调用pacth方法把虚拟DOM转换成真正的DOM节点。需要注意在图中忽略了新建真实DOM的情况（如果没有旧的虚拟Node，那么可以直接通过createElm创建真实DOM节点），这里重点分析在已有虚拟Node的情况下，会通过sameVnode判断当前需要更新的Node节点是否和旧的Node节点相同（例如我们设置的key属性发生了变化，那么节点显然不同），如果节点不同那么将旧节点采用新节点替换即可，如果相同且存在子节点，需要调用patchVNode方法执行diff算法更新DOM，从而提升DOM操作的性能。

### 响应式流程
 - 在init的时候会利用Object.defineProperty方法（不兼容IE8）监听Vue实例的响应式数据的变化从而实现数据劫持能力（利用了JavaScript对象的访问器属性get和set，在未来的Vue3中会使用ES6的Proxy来优化响应式原理）。在初始化流程中的编译阶段，当render function被渲染的时候，会读取Vue实例中和视图相关的响应式数据，此时会触发getter函数进行依赖收集（将观察者Watcher对象存放到当前闭包的订阅者Dep的subs中），此时的数据劫持功能和观察者模式就实现了一个MVVM模式中的Binder，之后就是正常的渲染和更新流程。
 - 当数据发生变化或者视图导致的数据发生了变化时，会触发数据劫持的setter函数，setter会通知初始化依赖收集中的Dep中的和视图相应的Watcher，告知需要重新渲染视图，Wather就会再次通过update方法来更新视图。

## 2. 修饰符
### 事件修饰符
.stop
.prevent
.capture
.self
.once
.passive
### 按键修饰符
.enter
.tab
.delete (捕获“删除”和“退格”键)
.esc
.space
.up
.down
.left
.right
### 系统修饰键
.ctrl
.alt
.shift
.meta
.exact 修饰符
### 鼠标按钮修饰符
.left
.right
.middle
### v-model修饰符
.lazy 光标离开更新
.trim 过滤首尾的空格
.number 如果你先输入数字，那它就会限制你输入的只能是数字。 如果你先输入字符串，那它就相当于没有加.number
### v-bind修饰符
.sync
.prop
.camel 驼峰显示属性

## 3. watch的实现原理
- watch的分类：

    - deep watch（深层次监听）
    - user watch（用户监听）
    - computed watcher（计算属性）
    - sync watcher（同步监听）

- watch实现过程：

    - watch的初始化在data初始化之后（此时的data已经通过Object.defineProperty的设置成响应式）
    - watch的key会在Watcher里进行值的读取，也就是立马执行get获取value（从而实现data对应的key执行getter实现对于watch的依赖收集），此时如果有immediate属性那么立马执行watch对应的回调函数
    - 当data对应的key发生变化时，触发user watch实现watch回调函数的执行


## 4. computed运行原理
- computed的属性是动态挂载到vm实例上的，和普通的响应式数据在data里声明不同
- 设置computed的getter，如果执行了computed对应的函数，由于函数会读取data属性值，因此又会触发data属性值的getter函数，在这个执行过程中就可以处理computed相对于data的依赖收集关系了
- 首次计算computed的值时，会执行vm.computed属性对应的getter函数（用户指定的computed函数，如果没有设置getter，那么将当前指定的函数赋值computed属性的getter），进行上述的依赖收集
- 如果computed的属性值又依赖了其他computed计算属性值，那么会将当前target暂存到栈中，先进行其他computed计算属性值的依赖收集，等其他计算属性依赖收集完成后，在从栈中pop出来，继续进行当前computed的依赖收集
 
## 5. nextTick
目的就是产生一个回调函数加入task或者microtask中，当前栈执行完以后（可能中间还有别的排在前面的函数）调用该回调函数，起到了异步触发（即下一个tick时触发）的目的。

## 6. vue-router有哪几种导航守卫

Vue-router 导航守卫分别有全局守卫，独享守卫和组件内守卫，一般用的最多的就是router.beboreEach(进入路由之前) 里做权限校验，或者组件内的权限校验用的beforeRouteEnter

- 全局守卫：
    - router.beforeEach 全局前置守卫 进入路由之前
    - router.beforeResolve 全局解析守卫(2.5.0+) 在beforeRouteEnter调用之后调用
    - router.afterEach 全局后置钩子 进入路由之后

- 路由独享守卫：
    - beforeEnter

- 路由组件内守卫:
    - beforeRouteEnter 进入路由前, 在路由独享守卫后调用，不能获取组件实例this，组件实例还没被创建
    - beforeRouteUpdate (2.2) 路由复用同一个组件时, 在当前路由改变，但是该组件被复用时调用
    - beforeRouteLeave 离开当前路由时, 导航离开该组件的对应路由时调用

- 完整的导航解析流程
  1. 导航被触发。
  2. 在失活的组件里调用 beforeRouteLeave 守卫。
  3. 调用全局的 beforeEach 守卫。
  4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
  5. 在路由配置里调用 beforeEnter。
  6. 解析异步路由组件。
  7. 在被激活的组件里调用 beforeRouteEnter。
  8. 调用全局的 beforeResolve 守卫 (2.5+)。
  9. 导航被确认。
  10. 调用全局的 afterEach 钩子。
  11. 触发 DOM 更新。
  12. 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## 7. 权限校验
### 登陆态校验
  - beforeEach（登陆态存储在vuex中）
  
  - 向服务器发送请求
  
    登陆的时候，登陆成功后会在vuex中存储已经登陆 isLogin。为了防止页面刷新过程中vuex存储信息消失，服务器是记录登陆态的。

    - 会话方式  cookie session
    - token方式  JWT生成token  客户端把token存储起来（本地）

### 菜单/按钮/功能的权限【显示隐藏】
  - 登陆成功从服务器获取用户的权限字段，存储在本地（本地存储[不安全，可以加密存储] / vuex[推荐]）
  - 自定义指令控制渲染还是不渲染（不建议大家组件内 v-if）
  - 有的公司，客户端渲染的菜单，都是服务器返回的HTML(服务器压力比较大，但是安全)

### 不管是否有权限都能看，只不过点击会提示或者不让跳转路由，再或者跳转到指定的路由
  - beforeEach
  - 组件内自己判断

### 数据接口权限
 - 服务器处理的（保证绝对的安全性）

## 8. keep-alive(使用参考：https://juejin.im/post/5cdcbae9e51d454759351d84)
keep-alive 是 Vue  内置的一个组件，可以使被包含的组件保留状态，避免重新渲染 ，其有以下特性：
- 一般结合路由和动态组件一起使用，用于缓存组件；
- 提供 include 和 exclude 属性，两者都支持字符串或正则表达式， include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存 ，其中 exclude 的优先级比 include 高；
- 对应两个钩子函数 activated 和 deactivated ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated。

## 9. v-show 与 v-if 有什么区别
- v-show是css切换，v-if是完整的销毁和重新创建，v-if是条件渲染，当false的时候不会渲染
- 使用频繁切换时用v-show，运行时较少改变时用v-if

## 10. v-model 的原理
```
<input v-model='searchText'>
//=>原理
<input :value="searchText" 
    @input="searchText= $event.target.value">
```
如果需要让一个自定义组件支持 v-model=’value’ 指令实现双向数据绑定，即表示父组件调用子组件时用value属性传递了输入框值，以及绑定了一个input事件，可以通过这个事件让子组件给父组件传参。

## 11. computed 和 watch 的区别和运用的场景
### computed 
  - 定义：是计算属性，依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值；
  - 运用场景：当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
  - 区别：
  
  	1.computed支持缓存（只有引用的响应式属性改变时才会重新计算）

	2.computed不支持异步操作

	3.computed中的属性有一个get和set方法，如果属性为函数时，默认调用get方法。

### watch 
   - 定义：更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；
   - 运用场景：当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。
   - 区别：
   
    1.watch不支持缓存，会直接监听数据，当数据改变时，会直接触发对于的操作

	2.watch支持异步操作
	
	3.watch可以接收参数（oldValue，newValue）
	
	4.watch中的属性必须时data中声明或者其他组件传递过来的数据

	5.watch中的属性为对象时，可以使用其handler方法、immediate属性（当为true时，页面刷新时就会调用handler方法）和deep属性（当为true时，能够监听复杂属性类型）

## 12. 对vue生命周期的理解
- created/mounted/updated/destroyed，以及对应的before钩子。分别是创建=>挂载=>更新=>销毁。
- activated & deactivated,  被 keep-alive 缓存的组件激活/停用时调用
- errorCaptured（v2.5 以上版本有的一个钩子，用于处理错误）
- serverPrefetch，前身是ssrPrefetch，用来处理ssr的。允许我们在渲染过程中“等待”异步数据。

## 13. Vue 怎么实现数据双向绑定的：
- 实现一个监听器Observer：
  对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter， 那么就能监听到了数据变化。

- 实现一个解析器 Compile：
  解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。

- 实现一个订阅者 Watcher：
  Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发解析器 Compile 中对应的更新函数。

- 实现一个订阅器 Dep：
  订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和 订阅者 Watcher 进行统一管理。

## 14. 组件间相互通信的方法
1. props / $emit 适用 父子组件通信
2. $refs 与 $parent / $children 适用 父子组件通信。在父组件中的子组件添加一个ref属性，this.$refs.xxx就能获取到对应的子组件实例
3. EventBus （$emit / $on） 适用于 父子、隔代、兄弟组件通信。创建一个eventBus.js文件，在需要传参的组件中引入改文件，传参时eventBus.$emit(eventName, [...args])，接收参数的组件内eventBus.$on(eventName, callback)
4. provide / inject 适用于 隔代组件通信
5. vuex 公共状态管理 适用于SPA单页面应用中的各类情况

## 15. 怎样理解 Vue 的单向数据流？
- 所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。

- Vue 的父组件和子组件生命周期钩子函数执行顺序可以归类为以下 4 部分：
  - 加载渲染过程：父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted
  - 子组件更新过程：父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
  - 父组件更新过程：父 beforeUpdate -> 父 updated
  - 销毁过程：父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

- 每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。子组件想修改时，只能通过 $emit 派发一个自定义事件，父组件接收到后，由父组件修改。有两种常见的试图改变一个 prop 的情形 :
  - 这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。 在这种情况下，最好定义一个本地的 data 属性并将这个 prop 用作其初始值
  - 这个 prop 以一种原始的值传入且需要进行转换。 在这种情况下，最好使用这个 prop 的值来定义一个计算属性

## 16. 路由跳转的方式
1. router-link
2. this.$router.push()。用params传参只能用name，如果用path则用query传参或者手写带有完整参数的path.
3. this.$router.replace()，与上一个不同的是history栈中不会有记录。
4. this.$router.go(n)，向前或者向后跳转n个页面，n可为正整数或负整数。

## 17.vuex（参考：https://www.jianshu.com/p/2e5973fe1223）
- 定义：VueX是适用于在Vue项目开发时使用的状态管理工具.
- 使用场景：单页应用中，组件之间的共享状态和方法。
- 安装：
	1. npm 安装 Vuex  `npm i vuex -s`
	2. 在项目的根目录下新增一个store文件夹，在该文件夹内创建index.js
- 使用：
	1. 初始化store下index.js中的内容，export输出
	2. 在main.js中引入store， 将store挂载到当前项目的Vue实例当中去
	3. 在组件中使用Vuex `this.$store.state.xxx`就能获取到store中定义的状态xxx
- 属性：
	1. state：包含了store中存储的各个状态。
	2. getter: 类似于 Vue 中的计算属性，根据其他 getter 或 state 计算返回值。
	3. mutation:定义的方法动态修改Vuex 的 store 中的状态或数据，只能是同步操作。
	4. action: 由于直接在mutation方法中进行异步操作，将会引起数据失效。所以提供了Actions来专门进行异步操作，最终提交mutation方法。view 层通过 store.dispatch 来分发 action。
	5. modules：项目特别复杂的时候，可以让每一个模块拥有自己的state、mutation、action、getters,使得结构非常清晰，方便管理。

## 18. 路由按需引入
1. `component: () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')`
2. `component: resolve => require(['../components/PromiseDemo'], resolve)`

## 19. vue项目中遇到的问题
- 打包文件太大，使用路由懒加载，对代码进行分割，require/import；
- setInterval路由跳转继续运行并没有及时进行销毁，在beforeDestory中clearInterval；

## 20. vue插件开发

## 21. vue-router 原理
1. 实现一个静态install方法，因为作为插件都必须有这个方法，给Vue.use()去调用；
2. 可以监听路由变化；
3. 解析配置的路由，即解析router的配置项routes，能根据路由匹配到对应组件；
4. 实现两个全局组件router-link和router-view；（最终落地点）


# React
## 1. 高阶组件
- 定义：高阶组件（Higher Order Component，HOC）并不是React提供的某种API，而是使用React的一种模式，用于增强现有组件的功能。一个高阶组件就是一个函数，这个函数接受一个组件作为输入，然后返回一个新的组件作为结果，而且，返回的新组件拥有了输入组件所不具备的功能。这里提到的组件并不是组件实例，而是组件类，也可以是一个无状态组件的函数。
- 作用：
  1. 重用代码。
  2. 修改现有React组件的行为。
- 按实现方式分类：
  1. 代理方式的高阶组件
  2. 继承方式的高阶组件


# webpack
## 1. 谈谈你对webpack的看法
 - 定义：WebPack 是一个模块打包工具，你可以使用WebPack管理你的模块依赖，并编绎输出模块们所需的静态文件。它能够很好地管理、打包Web开发中所用到的HTML、Javascript、CSS以及各种静态文件（图片、字体等），让开发过程更加高效。对于不同类型的资源，webpack有对应的模块加载器。webpack模块打包器会分析模块间的依赖关系，最后 生成了优化且合并后的静态资源。
 - 两大特点：
    - code splitting（可以自动完成）
    - loader 可以处理各种类型的静态文件，并且支持串联操作
- webpack 是以commonJS的形式来书写脚本滴，但对 AMD/CMD 的支持也很全面，方便旧项目进行代码迁移。
- webpack具有requireJs和browserify的功能，但仍有很多自己的新特性：
    1. 对 CommonJS、AMD、ES6的语法做了兼容
    2. 对js、css、图片等资源文件都支持打包
    3. 串联式模块加载器以及插件机制，让其具有更好的灵活性和扩展性，例如提供对CoffeeScript、ES6的支持
    4. 有独立的配置文件webpack.config.js
    5. 可以将代码切割成不同的chunk，实现按需加载，降低了初始化时间
    6. 支持 SourceUrls 和 SourceMaps，易于调试
    7. 具有强大的Plugin接口，大多是内部插件，使用起来比较灵活
    8. webpack 使用异步 IO 并具有多级缓存。这使得 webpack 很快且在增量编译上更加快

## 2. Webpack的loader和plugins的区别
- Webpack打包原理：
    - 识别入口文件
    - 通过逐层识别模块依赖。（Commonjs、amd或者es6的import，webpack都会对其进行分析。来获取代码的依赖）
    - webpack做的就是分析代码。转换代码，编译代码，输出代码
    - 最终形成打包后的代码
- loader：是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中。
    - 处理一个文件可以使用多个loader，loader的执行顺序是和本身的顺序是相反的，即最后一个loader最先执行，第一个loader最后执行。
    - 第一个执行的loader接收源文件内容作为参数，其他loader接收前一个执行的loader的返回值作为参数。最后执行的loader会返回此模块的JavaScript源码
- Plugin：在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
 
- 区别：
    - 对于loader，它就是一个转换器，将A文件进行编译形成B文件，这里操作的是文件，比如将A.scss或A.less转变为B.css，单纯的文件转换过程。
    - plugin是一个扩展器，它丰富了wepack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务。

## 3. webpack提高打包速度
（参考：https://blog.csdn.net/weixin_40811829/article/details/88599201；https://blog.csdn.net/xiaoenwu/article/details/106698039）
### 1. 使用 DLLPlugin和DLLReferencePlugin两个插件 提高打包速度
  - 单独对那些不常更新的第三方模块进行打包，使第三方模块只打包一次，以后的每次构建都只会生成自己的业务代码，可以很好的提高构建效率。
### 2. 优化loader配置
  - 缩小文件匹配范围(include/exclude)，exclude用于排除不处理的目录，通过排除node_modules下的文件 从而缩小了loader加载搜索范围 高概率命中文件；
  - 缓存loader的执行结果(cacheDirectory)。
### 3. resolve优化配置
  - 优化模块查找路径 resolve.modules。为了减少搜索范围，可以直接写明 node_modules 的全路径。
  -  resolve.alias 配置路径别名。配置项通过别名来把原导入路径映射成一个新的导入路径 此优化方法会影响使用Tree-Shaking去除无效代码。
  -  resolve.extensions，当引入模块时不带文件后缀 webpack会根据此配置自动解析确定的文件后缀，后缀列表尽可能小，频率最高的往前放，导出语句尽可能带上后缀。
### 4. module.noParse
  - 对于引入的大型第三方库，不需要将其解析成语法树来解析依赖，提高构建速度。被定义在该配置中的模块中，不能调用import/require/define等引入机制(没采用模块化)。例如jquery.
### 5. HappyPack
  - 让webpack对loader的执行过程，从单一进程形式扩展为多进程模式，也就是将任务分解给多个子进程去并发的执行，子进程处理完后再把结果发送给主进程。从而加速代码构建 与 DLL动态链接库结合来使用更佳。
### 6. ParallelUglifyPlugin
  - 这个插件可以帮助有很多入口点的项目加快构建速度。把对JS文件的串行压缩变为开启多个子进程并行进行uglify。
### 7. terser-webpack-plugin
  - 对js进行压缩的插件
### 8. 模块化引入
```
import {chain, cloneDeep} from 'lodash';
// 可以改写为
import chain from 'lodash/chain';
import cloneDeep from 'lodash/cloneDeep';
```
### 9. 开启Gzip压缩








# 浏览器
## 1. 讲讲304缓存的原理
- 服务器首先产生ETag，服务器可在稍后使用它来判断页面是否已经被修改。本质上，客户端通过将该记号传回服务器要求服务器验证其（客户端）缓存。

- 304是HTTP状态码，服务器用来标识这个文件没修改，不返回内容，浏览器在接收到个状态码后，会使用浏览器已缓存的文件。

- 客户端请求一个页面（A）。 服务器返回页面A，并在给A加上一个ETag。 客户端展现该页面，并将页面连同ETag一起缓存。 客户再次请求页面A，并将上次请求时服务器返回的ETag一起传递给服务器。 服务器检查该ETag，并判断出该页面自上次客户端请求之后还未被修改，直接返回响应304（未修改——Not Modified）和一个空的响应体。


## 2. 请求头的content-type有几种类型
    Content-Type（内容类型），一般是指网页中存在的 Content-Type，用于定义网络文件的类型和网页的编码，决定浏览器将以什么形式、什么编码读取这个文件

application/x-www-form-urlencoded、multipart/form-data、application/json、text/xml
    
## 3. TCP传输的三次握手四次挥手策略
- 为了准确无误地把数据送达目标处，TCP协议采用了三次握手策略。用TCP协议把数据包送出去后，TCP不会对传送 后的情况置之不理，它一定会向对方确认是否成功送达。握手过程中使用了TCP的标志：SYN和ACK。
- 发送端首先发送一个带SYN标志的数据包给对方。接收端收到后，回传一个带有SYN/ACK标志的数据包以示传达确认信息。 最后，发送端再回传一个带ACK标志的数据包，代表“握手”结束。 若在握手过程中某个阶段莫名中断，TCP协议会再次以相同的顺序发送相同的数据包。
- 断开一个TCP连接则需要“四次挥手”：
 1. 第一次挥手：主动关闭方发送一个FIN，用来关闭主动方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方：我已经不 会再给你发数据了(当然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，主动关闭方依然会重发这些数据)，但是，此时主动关闭方还可 以接受数据。
 2. 第二次挥手：被动关闭方收到FIN包后，发送一个ACK给对方，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号）。
 3. 第三次挥手：被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了，不会再给你发数据了。
 4. 第四次挥手：主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手。

## 4. 进程和线程
- 浏览器（多进程）包含了1 个浏览器（Browser）主进程、1 个 GPU 进程、1 个网络（NetWork）进程、多个渲染进程和多个插件进程。其中渲染进程和Web前端密切相关，包含以下线程：
	- GUI渲染线程
	- JS引擎线程
	- 事件触发线程（和EventLoop密切相关）
	- 定时触发器线程
	- 异步HTTP请求线程
- 注意：GUI渲染线程和JS引擎线程是互斥的，为了防止DOM渲染的不一致性，其中一个线程执行时另一个线程会被挂起。
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/93caa132594d427781b8c186e32df1e5~tplv-k3u1fbpfcp-zoom-1.image)


## 5. Cookie、Session、Token、JWT (参考：https://juejin.im/post/5e055d9ef265da33997a42cc)
### Cookie
- HTTP 是无状态的协议，需要通过 cookie 或者 session 去维护一个状态。cookie 存储在客户端，cookie 是不可跨域的。
- cookie的重要属性：name=value，domain，path，maxAge，expires，secure，httpOnly

### Session
- session 是另一种记录服务器和客户端会话状态的机制
- session 是基于 cookie 实现的，session 存储在服务器端，sessionId 会被存储到客户端的cookie 中

### Cookie 和 Session 的区别
- 安全性： Session 比 Cookie 安全，Session 是存储在服务器端的，Cookie 是存储在客户端的。
- 存取值的类型不同：Cookie 只支持存字符串数据，想要设置其他类型的数据，需要将其转换成字符串，Session 可以存任意数据类型。
- 有效期不同： Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭（默认情况下）或者 Session 超时都会失效。
- 存储大小不同： 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie，但是当访问量过多，会占用过多的服务器资源。
 

### Token（令牌）
1. Acesss Token
- 访问资源接口（API）时所需要的资源凭证
- 简单 token 的组成： uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token 的前几位以哈希算法压缩成的一定长度的十六进制字符串）
- 特点：
    - 服务端无状态化、可扩展性好
    - 支持移动端设备
    - 安全
    - 支持跨程序调用
- 每一次请求都需要携带 token，需要把 token 放到 HTTP 的 Header 里
- 基于 token 的用户认证是一种服务端无状态的认证方式，服务端不用存放 token 数据。用解析 token 的计算时间换取 session 的存储空间，从而减轻服务器的压力，减少频繁的查询数据库
- token 完全由应用管理，所以它可以避开同源策略

2. Refresh Token
- refresh token 是专用于刷新 access token 的 token
- Access Token 的有效期比较短，当 Acesss Token 由于过期而失效时，使用 Refresh Token 就可以获取到新的 Token，如果 Refresh Token 也失效了，用户就只能重新登录了。
- Refresh Token 及过期时间是存储在服务器的数据库中，只有在申请新的 Acesss Token 时才会验证，不会对业务接口响应时间造成影响，也不需要向 Session 一样一直保持在内存中以应对大量的请求。

### Token 和 Session 的区别
- Session 是一种记录服务器和客户端会话状态的机制，使服务端有状态化，可以记录会话信息。而 Token 是令牌，访问资源接口（API）时所需要的资源凭证。Token 使服务端无状态化，不会存储会话信息。
- Session 和 Token 并不矛盾，作为身份认证 Token 安全性比 Session 好，因为每一个请求都有签名还能防止监听以及重放攻击，而 Session 就必须依赖链路层来保障通讯安全了。如果你需要实现有状态的会话，仍然可以增加 Session 来在服务器端保存一些状态。
- 如果你的用户数据可能需要和第三方共享，或者允许第三方调用 API 接口，用 Token 。如果永远只是自己的网站，自己的 App，用什么就无所谓了。

### JWT
- JSON Web Token（简称 JWT）是目前最流行的跨域认证解决方案。
- 是一种认证授权机制。
- JWT 认证流程：
  - 用户输入用户名/密码登录，服务端认证成功后，会返回给客户端一个 JWT
  - 客户端将 token 保存到本地（通常使用 localstorage，也可以使用 cookie）
  - 当用户希望访问一个受保护的路由或者资源的时候，需要请求头的 Authorization 字段中使用Bearer 模式添加 JWT，其内容看起来是下面这样

    `Authorization: Bearer <token>`
    
- JWT 的特点：
  - 服务端的保护路由将会检查请求头 Authorization 中的 JWT 信息，如果合法，则允许用户的行为
  - 因为 JWT 是自包含的（内部包含了一些会话信息），因此减少了需要查询数据库的需要
  - 因为 JWT 并不使用 Cookie 的，所以你可以使用任何域名提供你的 API 服务而不需要担心跨域资源共享问题（CORS）
  - 因为用户的状态不再存储在服务端的内存中，所以这是一种无状态的认证机制。

- JWT 的使用方式

  客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。
  
  1. 方式一：当用户希望访问一个受保护的路由或者资源的时候，可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求头信息的 Authorization 字段里，使用 Bearer 模式添加 JWT。
  2. 方式二：跨域的时候，可以把 JWT 放在 POST 请求的数据体里。
  3. 方式三：通过 URL 传输
  

### Token 和 JWT 的区别

- 相同：
  - 都是访问资源的令牌
  - 都可以记录用户的信息
  - 都是使服务端无状态化
  - 都是只有验证成功后，客户端才能访问服务端上受保护的资源
- 不同：
  - Token：服务端验证客户端发送过来的 Token 时，还需要查询redis获取用户信息，然后验证 Token 是否有效。
  - JWT： 将 Token 和 Payload 加密后存储于客户端，服务端只需要使用密钥解密进行校验（校验也是 JWT 自己实现的）即可，不需要查询或者减少查询数据库，因为 JWT 自包含了用户信息和加密的数据。

## 6. HTTP状态码206是干什么的
 206状态码表示客户端通过发送范围请求头Range抓取到了资源的部分数据，一般用来解决大文件下载问题，一般CDN服务器都会支持这种能力。
 
## 7. http2实现了多路复用,http1.x为什么不能多路复用？
HTTP 1.1是基于文本分割解析的协议,也没有序号,如果多路复用会导致顺序错乱,http2则用帧的方式,等于切成一块块,每一块都有对应的序号,所以可以实现多路复用.

## 8. get和post的区别
- GET产生一个TCP数据包；POST产生两个TCP数据包。但并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。
  - 对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
  - 而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。
- GET在浏览器回退时是无害的，而POST会再次提交请求。
- GET产生的URL地址可以被Bookmark，而POST不可以。
- GET请求会被浏览器主动cache，而POST不会，除非手动设置。
- GET请求只能进行url编码，而POST支持多种编码方式。
- GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
- GET请求在URL中传送的参数是有长度限制的，而POST么有。
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
- GET参数通过URL传递，POST放在Request body中。

## 9. 回流和重绘
- 回流：通过构造渲染树，将可见DOM节点和它对应样式结合起来，计算它们在设备视口(viewport)内的位置和大小，这个计算的阶段就是回流。
- 重绘：根据渲染树以及回流得到的几何信息，将渲染树的每个节点转换为屏幕上的实际像素，这个阶段就是重绘节点。
- 何时发生回流：
   - 添加或删除可见的DOM元素
   - 元素的位置发生变化
   - 元素的尺寸发生变化（包括外边距、内边框、边框大小、高度和宽度等）
   - 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代。
   - 页面一开始渲染的时候（这肯定避免不了）
   - 浏览器的窗口尺寸变化（因为回流是根据视口的大小来计算元素的位置和大小的）
- 如何避免触发回流和重绘
   - 避免使用table布局。
   - 尽可能在DOM树的最末端改变class。
   - 避免设置多层内联样式。
   - 将动画效果应用到position属性为absolute或fixed的元素上
   - 避免使用CSS表达式（例如：calc()）
   - CSS3硬件加速（GPU加速）
   - 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性
   - 避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中
   - 也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘
   - 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。

## 10. localstorage sessionstorage和cookie的区别

### 基本概念及使用场景

cookie：它的主要用于保存登陆信息，比如登陆某个网站市场可以看到'记住密码’，这就是通过在cookie中存入一段辨别用户身份的数据来实现的。判断用户是否登录过网站，以便实现下次自动登录或记住密码；保存事件信息等

sessionStorage：会话，是可以将一部分数据在当前会话中保存下来，刷新页面数据依旧存在。但是页面关闭后，sessionStorage中的数据就会被清空。敏感账号一次性登录；单页面用的较多（sessionStorage 可以保证打开页面时 sessionStorage 的数据为空）

localStorage：常用于长期登录（判断用户是否已登录），适合长期保存在本地的数据。

### 区别

1. 存储大小
   - cookie：一般不超过4K（因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识）
   - sessionStorage：5M或者更大
   - localStorage：5M或者更大
2. 数据有效期
   - cookie：一般由服务器生成，可以设置失效时间；若没有设置时间，关闭浏览器cookie失效，若设置了时间，cookie就会存放在硬盘里，过期才失效
   - sessionStorage：仅在当前浏览器窗口关闭之前有效，关闭页面或者浏览器会被清除
   - localStorage：永久有效，窗口或者浏览器关闭也会一直保存，除非手动永久清除，因此用作持久数据
3. 作用域
   - cookie：在所有同源窗口中都是共享的
   - sessionStorage：在同一个浏览器窗口是共享的（不同浏览器、同一个页面也是不共享的）
   - localStorage：在所有同源窗口中都是共享的
4. 通信
   - cokie：十种携带在同源的http请求中，即使不需要，故cookie在浏览器和服务器之间来回传递；如果使用cookie保存过多数据会造成性能问题
   - sessionStorage：仅在客户端（即浏览器）中保存，不参与和服务器的通信；不会自动把数据发送给服务器，仅在本地保存
   - localStorage：仅在客户端（即浏览器）中保存，不参与和服务器的通信；不会自动把数据发送给服务器，仅在本地保存
5. 易用性
   - cookie：需要自己进行封装，原生的cookie接口不够友好
   - sessionStorage：原生接口可以接受，可以封装来对Object和Array有更好的支持
   - localStorage：原生接口可以接受，可以封装来对Object和Array有更好的支持

## 11. 从输入URL到页面呈现发生了什么
1. 查找强缓存
2. DNS解析
3. TCP连接
4. 发送http请求
5. 关闭TCP连接
6. 浏览器渲染



# node.js

## 1.node.js的优缺点
### 优点：
1. 事件驱动，通过闭包很容易实现客户端的生命活期。
2. 不用担心多线程，锁，并行计算的问题
3. V8引擎速度非常快
4. 对于游戏来说，写一遍游戏逻辑代码，前端后端通用
### 缺点：
1. nodejs更新很快，可能会出现版本兼容
2. nodejs还不算成熟，还没有大制作
3. nodejs不像其他的服务器，对于不同的链接，不支持进程和线程操作