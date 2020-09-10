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
（https://www.cnblogs.com/xiaozhumaopao/p/12716266.html）

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