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

