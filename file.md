# JS

## 构造函数

### new 运算符

1. 创建一个新的空对象实例。
2. 将此空对象的隐式原型指向其构造函数的显示原型。
3. 执行构造函数（传入相应的参数，如果没有参数就不用传），同时 [this](#this指向) 指向这个新实例。
4. 如果返回值是一个新对象，那么直接返回该对象；如果无返回值或者返回一个非对象值，那么就将步骤（1）创建的对象返回。

## 原型及原型链

### 原型概念

每个函数都有一个 prototype 属性,它指向了一个对象,这个对象里的所有方法和属性都会被构造函数的实例继承, 这个对象就称为原型对象.

### 原型链

每个对象都有一个**proto**属性,指向其原型对象的指针,对象从原型中继承方法和属性.每个原型对象又有自己的原型,并从中继承方法和属性,直到某个对象的原型为 null 为止,这种关系被称为原型链

```
  function F1() {}
  function F2() {}
  function F3() {}

  let f1 = new F1();
  let f2 = new F2();
  let f3 = new F3();

  F2.prototype = f1;
  F3.prototype = f2;

```

### 为什么需要原型链

1. 用构造函数生成的实例,都有自己的方法和属性

## 继承

1. 构造函数继承

```
  function Parent1() {
      this.name = 'parent1 的属性';
  }

  function Child1() {
    //【重要】此处用 call 或 apply 都行：改变 this 的指向
      Parent1.call(this);
      this.type = 'child1 的属性';
  }

  console.log(new Child1);
```

- 让父类构造函数使用 call 在子类构造函数里执行,改变父类的 this 指向
- 缺点:
  - 父类原型上的方法和属性不能被继承
  - 会创建多次方法

2. 原型链继承

```
  function Parent() {
      this.name = 'Parent 的属性';
  }

  function Child() {
      this.type = 'Child 的属性';
  }

  Child.prototype = new Parent(); //【重要】

  console.log(new Child());
```

- 将子类的原型指向父类的实例,借助原型链实现继承,
- 缺点:
  - 子类的多个实例共用父类原型,无法隔离,
  - 子类实例无法向父类传参

3. 组合继承

- 结合了两种模式的优点，传参和复用, 通过 call 调用父类构造函数,然后将子类的原型指向父类的实例,
- 缺点: 父类构造函数调用两次

4. 原型式继承

```
    function CreateObj(o){
        function F(){}
        F.prototype = o;
        console.log(o.__proto__ === Object.prototype);
        console.log(F.prototype.constructor === Object); // true
        return new F();
    }

    var person = {
        name: 'xiaopao',
        friend: ['daisy','kelly']
    }

    var person1 = CreateObj(person);
```

- 在一个方法内部创建一个临时函数,将传入的对象作为临时构造函数的原型,最后返回实例,实现了浅复制的功能
- 缺点: 引用类型的数据共享,无法隔离

5. 寄生式继承 创建一个封装继承过程的函数,该函数可以在内部增强对象,最后返回对象

- 缺点: 无法做到函数复用而影响效率

6. 寄生组合继承 通过构造函数继承属性, 将子类的原型指向为父类原型的副本, constructor 指向子类构造函数

---

## js 基础

## 变量提升

- 在 JavaScript 中，函数声明与变量声明经常被 JavaScript 引擎隐式地提升到当前作用域的顶部。
- 声明语句中的赋值部分并不会被提升，只有名称被提升
- 函数声明的优先级高于变量，如果变量名跟函数名相同且未赋值，则函数声明会覆盖变量声明
- 如果函数有多个同名参数，那么最后一个参数（即使没有定义）会覆盖前面的同名函数

### var,let,const

- let
  - 声明的变量只在它所在的代码块中生效 比如 for 循环 用 let 声明的 i 只在 for 循环内有效
  - 不会变量提升,变量要在声明后使用否则会报错
  - 不允许在相同作用域内多次重复同一个变量
- const
  - 声明创建的值不允许修改,声明的变量只在它所在的代码块中生效
  - 如果修改基本数据类型的值,会报错,修改引用数据类型的话则不会
- var
  - [变量提升](#变量提升)

### this 指向

大部分情况下,函数的调用方式决定了 this 的指向

1. 在全局执行环境中，this 指向全局对象,在浏览器中全局对象是 Windows
2. 在全局函数中,this 指向 Windows
3. 当函数在对象中作为方法被调用时,this 指向为调用该函数的对象
4. 在构造函数中,[new 关键字](#new运算符)
   1. 如果返回值是一个对象,那么 this 指向其返回的对象
   2. 如果不是或者没有返回,则指向函数所创建的实例

## 功能类

### 数组去重

1. 使用 es6 的 new Set 构造函数初始化数组,然后使用 array.from
2. 利用 for 嵌套 for 循环,如果第一个循环内每一项不等于第二个循环的项,就往新数组中添加
3. 利用 for 循环内 indexOf 或者 includes,创建一个新数组,检测新数组中是否包含原数组的值,不包含则往新数组中添加
4. filter 循环内使用 indexOf,当前项的第一个索引值是否等于当前循环索引值,返回新数组

### 深浅拷贝

1. concat()

```
const a1 = [1, 2];
const a2 = a1.concat();

a2[0] = 2;
a1 // [1, 2]
```

2. es6 扩展运算符

```
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
```

3. Object.assign
4. Object.create

### 合并数组

1. concat()
   `[1, 2].concat(more)`
2. es6 扩展运算符

```
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = [...arr1, ...arr2]
```

### 数组去重

1. [...new Set(array)]

## 数组方法

1. 遍历类

   **遍历类无特殊说明的都是不对空数组进行检测都不会改变原数组**
* every() 通过制定函数检测数值元素的每个元素是否都符合条件。
  - 只要有一个不满足,则返回 false 并且后面的不再检查
  - 所有都满足返回 true
* some() 检测数组元素中是否有元素符合指定条件。
  - 只要有一个满足,则返回 true 后面的不检查
  - 没有满足的返回 false
* filter() 检测数值元素，并返回符合条件所有元素的数组。 
* forEach() 数组每个元素都执行一次回调函数。
* map() 通过指定函数处理数组的每个元素，并返回处理后的数组。
* reduce() 将数组元素计算为一个值（从左到右）。
* reduceRight() 将数组元素计算为一个值（从右到左）。

2. 操作类
* concat() 连接两个或更多的数组，并返回结果。
* from() 通过给定的对象中创建一个数组。
* includes() 判断一个数组是否包含一个指定的值。
* indexOf() 搜索数组中的元素，并返回它所在的位置。
* isArray() 判断对象是否为数组
  * Object.prototype.toString.call(value) == '[object Array]'
* join() 将传入的分隔符把数组的所有元素放入一个字符串。
* shift() 删除并返回数组的第一个元素。
* pop() 删除数组的最后一个元素并返回删除的元素。
* push() 向数组的末尾添加一个或更多元素，并返回新的长度。
* unshift() 向数组的开头添加一个或更多元素，并返回新的长度。
* slice(start, end) 包含从 start 到 end （不包括该元素）的元素，并返回一个新数组。
* sort() 对数组的元素进行排序。
* splice() 从数组中添加或删除元素。
* toString() 用逗号把数组中元素分隔并转换为字符串，并返回结果。


## dom

### 位置/宽高

1. 返回宽高度
   - offsetWidth/offsetHeight //（包括元素宽度、内边距和边框，不包括外边距）
   - clientWidth/clientHeight //（包括元素宽度、内边距，不包括边框和外边距）
   - style.width/style.height //（包括元素宽/高度，不包括内边距、边框和外边距）
   - scrollWidth/scrollHeight //（包括元素宽/高度、内边距和溢出尺寸，不包括边框和外边距），无溢出的情况，与 clientWidth 相同
2. 返回距离
   - offsetTop/offsetLeft // 元素 border 外到父元素 border 内的距离(都不包含 border)
   - scrollTop/scrollLeft // 元素 border(包括 border)到窗体上边界

# 混合开发

## 更新流程

1. 分版本，有版本号，如 201803211015
2. 将静态文件压缩成 zip 包，上传到服务端
3. 客户端每次启动，都去服务端检查版本号
4. 如果服务端版本号大于客户端版本号，就去下载最新的 zip 包
5. 下载完之后解压包，然后将现有文件覆盖
# css
## 清除浮动

# 特殊
## BFC
1. 概念
   1) BFC(Block formatting context)直译为“块级格式化上下文”。
   2) 可以理解成：创建了 BFC的元素就是一个独立的盒子，里面的子元素不会在布局上影响外面的元素（里面怎么布局都不会影响外部），BFC仍属于文档中的普通流
   3) display属性为block, list-item ,table的元素，会产生BFC。
2. 原理
   1) 内部的Box会在垂直方向，一个接一个地放置。
   2) **Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠**
   3) 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
   4) BFC的区域不会与float box重叠。
   5) BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
   6) 计算BFC的高度时，浮动元素也参与计算
3. 如何创建BFC?
   1) 根元素
   2) float属性不为none
   3) position不为static和relative
   4) overflow不为visible
   5) display为inline-block, table-cell, table-caption, flex, inline-flex
4. BFC作用
   1) 防止外边距重叠。
      1) 我们可以在div外面包裹一层容器，并触发该容器生成一个BFC。那么两个div便不属于同一个BFC，就不会发生margin重叠了。
   2) 清除浮动[清除浮动](#清除浮动)

# 框架
## vue
1. mvc
   ```
   Model 数据
   View 视图、界面
   Controller 控制器、逻辑处理
   ```
   `速度速度是反对广泛的`
2. mvvm
   
   Model层代表数据模型，View代表UI组件，ViewModel是View和Model层的桥梁，数据会绑定到viewModel层并自动将数据渲染到页面中，视图变化的时候会通知viewModel层更新数据
   ```
   Model：数据模型
    仅仅关注数据本身,不关心行为
   View：界面
    当ViewModel对Model进行更新的时候，会通过数据绑定更新到View
   ViewModel：作为桥梁负责沟通 View 和 Model
   ```
3. 数据绑定原理
   采用数据劫持结合发布者-订阅者模式的方式，在组件挂载时，通过 Object.defineProperty() 来劫持data中各个属性的setter，getter，在数据被读取时进行依赖收集,在数据变动时发布消息给订阅者，触发相应监听回调。
  
4. vue实现流程
   1. 解析模板成 render 函数
   2. 响应式开始监听
   3. 首次渲染，显示页面，且绑定依赖
   4. data 属性变化
5. 生命周期
   - beforeCreate	组件实例被创建之初，vue 实例的挂载元素$el和数据对象 data 都为 undefined，还未初始化。
   - created	组件实例已经完全创建，属性和方法也可用，但真实dom还没有生成，$el还不可用
   - beforeMount	在挂载开始之前被调用：相关的 render 函数首次被调用,进行模版编译
   - mounted	vue实例挂载后,dom完全渲染到页面中可进行dom操作
   - beforeUpdate	组件数据更新之前调用，发生在虚拟 DOM 打补丁之前
   - update	组件数据更新之后
   - activited	keep-alive专属，组件被激活时调用
   - deadctivated	keep-alive专属，组件被销毁时调用
   - beforeDestory	组件销毁前调用
   - destoryed	组件销毁后调用
6. v-if 和 v-show 区别
   1. 手段：
    - v-if 是动态的向DOM树内添加或者删除DOM元素；
    - v-show 是通过设置DOM元素的display样式属性控制显隐；
   2. 编译过程：
    - v-if 切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；
    - v-show只是简单的基于css切换；
   3. 编译条件：
    - v-if 是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译（编译被缓存？编译被缓存后，然后再切换的时候进行局部卸载);
    - v-show 是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素保留；
   4. 性能消耗：
    - v-if 有更高的切换消耗；
    - v-show 有更高的初始渲染消耗；
   5. 使用场景：
    - v-if 适合运营条件不大可能改变；
    - v-show 适合频繁切换。
7. $route和$router的区别
   $route是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。
   而$router是“路由实例”对象包括了路由的跳转方法，钩子函数等
8. 组件传值
   1. 父子 
      1. 子=>父 子发送$emit 父组件接收
      2. 父=>子 子定义props中的属性
   2. 兄弟
      可以通过new一个vue实例bus作为媒介,在组件ab中分别引入,a组件$$emit,b组件可以用$$on接收
   3. provide/inject
      组件中使用provide向下分发数据,下面的所有子组件都可以使用inject接收数据
9. computed和watch区别
   1. computed是计算属性,
      1. 当页面中有某些数据依赖其他数据进行变动的时候适合用computed,
      2. 有缓存行只有依赖的响应式数据变化了computed才会重新执行
   2. watch用于观察和监听页面上的vue实例,如果要在数据变化的同时进行异步操作或者是比较大的开销，那么watch为最佳选择
10. 性能优化
   - 不要在模板里面写过多表达式
   - 循环调用子组件时添加key
   - 频繁切换的使用v-show，不频繁切换的使用v-if
   - 尽量少用float，可以用flex
   - 按需加载，可以用require或者import()按需加载需要的组件
   - 路由懒加载
     - resolve
     - import
11. Vue.js特点
    - 简洁：页面由HTML模板+Json数据+Vue实例组成
    - 数据驱动：自动计算属性和追踪依赖的模板表达式
    - 组件化：用可复用、解耦的组件来构造页面
    - 轻量：代码量小，不依赖其他库
    - 快速：精确有效批量DOM更新
    - 模板友好：可通过npm，bower等多种方式安装，很容易融入
### vuex
### vue-router
1. 路由原理
   本质就是监听 URL 的变化，然后匹配路由规则，显示相应的页面，并且无须刷新。
   * hash 模式  `www.test.com/##/`
   * history 模式
2. 路由守卫
   1. 全局守卫
      * router.beforeEach 全局前置守卫 进入路由之前
      * router.beforeResolve 全局解析守卫(2.5.0+) 在beforeRouteEnter调用之后调用
      * router.afterEach 全局后置钩子 进入路由之后
   2. 路由独享守卫
   3. 路由组件内的守卫
      * beforeRouteEnter 进入路由前, 在路由独享守卫后调用 不能 获取组件实例 this，组件实例还没被创建
      * beforeRouteUpdate (2.2) 路由复用同一个组件时, 在当前路由改变，但是该组件被复用时调用 可以访问组件实例 this
      * beforeRouteLeave 离开当前路由时, 导航离开该组件的对应路由时调用，可以访问组件实例 this

## react
1. 声明周期
   1. 挂载卸载过程
       - constructor()
         - constructor()中完成了React数据的初始化，它接受两个参数：props和context，当想在函数内部使用这两个参数时，需使用super()传入这两个参数。
       - componentWillMount() **已废弃**
         - 初始化后还未挂载dom时调用
       - componentDidMount()
         - 页面渲染完成,dom节点生成
         - 适合做网络请求和添加订阅
       - componentWillUnmount ()
         - 会在组件卸载及销毁之前直接调用。
   2. 更新过程
       - componentWillReceiveProps (nextProps) **已废弃**
         - 会在已挂载的组件接收新的props之前调用
       - shouldComponentUpdate(nextProps,nextState)
         - 当props或者state发生变化时,在渲染之前调用,返回true或者false决定是否重新渲染
       - componentWillUpdate (nextProps,nextState) **已废弃**
         - 组件更新之前调用
       - componentDidUpdate(prevProps,prevState)
         - 更新完成后调用
       - render()
   3. React新增的生命周期
       - getDerivedStateFromProps(nextProps, prevState)
         - 调用render之前调用,并且在组件初始挂载和更新之前调用,可以返回一个对象更新state,返回null则不更新任何内容
       - getSnapshotBeforeUpdate(prevProps, prevState)
  2. setState

### redux
   
  
  
