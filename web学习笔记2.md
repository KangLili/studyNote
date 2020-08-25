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


