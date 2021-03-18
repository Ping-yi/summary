
# JS
### 三次握手
从浏览器地址栏输入url到显示页面的步骤

* 基础版本
  * 浏览器根据请求的URL交给DNS域名解析，找到真实IP，向服务器发起请求；
  * 服务器交给后台处理完成后返回数据，浏览器接收文件（HTML、JS、CSS、图象等）；
  * 浏览器对加载到的资源（HTML、JS、CSS等）进行语法解析，建立相应的内部数据结构（如HTML的DOM）；
  * 载入解析到的资源文件，渲染页面，完成。

* 详细版
  1. 在浏览器地址栏输入URL
  2. 浏览器查看缓存，如果请求资源在缓存中并且新鲜，跳转到转码步骤
     1. 如果资源未缓存，发起新请求
     2. 如果已缓存，检验是否足够新鲜，足够新鲜直接提供给客户端，否则与服务器进行验证。
     3. 检验新鲜通常有两个HTTP头进行控制Expires和Cache-Control：
        1. HTTP1.0提供Expires，值为一个绝对时间表示缓存新鲜日期
        2. HTTP1.1增加了Cache-Control: max-age=,值为以秒为单位的最大新鲜时间
  3. 浏览器解析URL获取协议，主机，端口，path
  4. 浏览器组装一个HTTP（GET）请求报文
  5.  浏览器获取主机ip地址，过程如下：
      1.  浏览器缓存
      2.  本机缓存
      3.  hosts文件
      4.  路由器缓存
      5.  ISP DNS缓存
      6.  DNS递归查询（可能存在负载均衡导致每次IP不一样）
  6.  打开一个socket与目标IP地址，端口建立TCP链接，三次握手如下：
      1.  客户端发送一个TCP的SYN=1，Seq=X的包到服务器端口
      2.  服务器发回SYN=1， ACK=X+1， Seq=Y的响应包
      3.  客户端发送ACK=Y+1， Seq=Z
  7.  TCP链接建立后发送HTTP请求
  8.  服务器接受请求并解析，将请求转发到服务程序，如虚拟主机使用HTTP Host头部判断请求的服务程序
  9.  服务器检查HTTP请求头是否包含缓存验证信息如果验证缓存新鲜，返回304等对应状态码
  10. 处理程序读取完整请求并准备HTTP响应，可能需要查询数据库等操作
  11. 服务器将响应报文通过TCP连接发送回浏览器
  12. 浏览器接收HTTP响应，然后根据情况选择关闭TCP连接或者保留重用，关闭TCP连接的四次握手如下：
      1.  主动方发送Fin=1， Ack=Z， Seq= X报文
      2.  被动方发送ACK=X+1， Seq=Z报文
      3.  被动方发送Fin=1， ACK=X， Seq=Y报文
      4.  主动方发送ACK=Y， Seq=X报文
  13. 浏览器检查响应状态吗：是否为1XX，3XX， 4XX， 5XX，这些情况处理与2XX不同
  14. 如果资源可缓存，进行缓存
  15. 对响应进行解码（例如gzip压缩）
  16. 根据资源类型决定如何处理（假设资源为HTML文档）
  17. 解析HTML文档，构件DOM树，下载资源，构造CSSOM树，执行js脚本，这些操作没有严格的先后顺序，以下分别解释
  18. 构建DOM树：
      1.  Tokenizing：根据HTML规范将字符流解析为标记
      2.  Lexing：词法分析将标记转换为对象并定义属性和规则
      3.  DOM construction：根据HTML标记关系将对象组成DOM树
  19. 解析过程中遇到图片、样式表、js文件，启动下载
  20. 构建CSSOM树：
      1.  Tokenizing：字符流转换为标记流
      2.  Node：根据标记创建节点
      3.  CSSOM：节点创建CSSOM树
  21. 根据DOM树和CSSOM树构建渲染树:
      1.  从DOM树的根节点遍历所有可见节点，不可见节点包括：1）script,meta这样本身不可见的标签。2)被css隐藏的节点，如display: none
      2.  对每一个可见节点，找到恰当的CSSOM规则并应用
      3.  发布可视节点的内容和计算样式
  22. js解析如下：
      1.  浏览器创建Document对象并解析HTML，将解析到的元素和文本节点添加到文档中，此时document.readystate为loading
      2.  HTML解析器遇到没有async和defer的script时，将他们添加到文档中，然后执行行内或外部脚本。这些脚本会同步执行，并且在脚本下载和执行时解析器会暂停。这样就可以用document.write()把文本插入到输入流中。同步脚本经常简单定义函数和注册事件处理程序，他们可以遍历和操作script和他们之前的文档内容
      3.  当解析器遇到设置了async属性的script时，开始下载脚本并继续解析文档。脚本会在它下载完成后尽快执行，但是解析器不会停下来等它下载。异步脚本禁止使用document.write()，它们可以访问自己script和之前的文档元素
      4.  当文档完成解析，document.readState变成interactive
      5.  所有defer脚本会按照在文档出现的顺序执行，延迟脚本能访问完整文档树，禁止使用document.write()
      6.  浏览器在Document对象上触发DOMContentLoaded事件
      7.  此时文档完全解析完成，浏览器可能还在等待如图片等内容加载，等这些内容完成载入并且所有异步脚本完成载入和执行，document.readState变为complete，window触发load事件
  23. 显示页面（HTML解析过程中会逐步显示页面）
  
* 详细简版
  - 从浏览器接收url到开启网络请求线程（这一部分可以展开浏览器的机制以及进程与线程之间的关系）
  - 开启网络线程到发出一个完整的HTTP请求（这一部分涉及到dns查询，TCP/IP请求，五层因特网协议栈等知识）
  - 从服务器接收到请求到对应后台接收到请求（这一部分可能涉及到负载均衡，安全拦截以及后台内部的处理等等）
  - 后台和前台的HTTP交互（这一部分包括HTTP头部、响应码、报文结构、cookie等知识，可以提下静态资源的cookie优化，以及编码解码，如gzip压缩等）
  - 单独拎出来的缓存问题，HTTP的缓存（这部分包括http缓存头部，ETag，catch-control等）
  - 浏览器接收到HTTP数据包后的解析流程（解析html-词法分析然后解析成dom树、解析css生成css规则树、合并成render树，然后layout、painting渲染、复合图层的合成、GPU绘制、外链资源的处理、loaded和DOMContentLoaded等）
  - CSS的可视化格式模型（元素的渲染规则，如包含块，控制框，BFC，IFC等概念）
  - JS引擎解析过程（JS的解释阶段，预处理阶段，执行阶段生成执行上下文，VO，作用域链、回收机制等等）
  - 其它（可以拓展不同的知识模块，如跨域，web安全，hybrid模式等等内容）
## 跨域
  浏览器出于安全考虑，有同源策略,是一种约定,协议\域名\端口只要有一个不同就是跨域,请求就会失败
  1. jsonp
     * 利用script标签没有同源策略的限制,通过 script标签指向一个需要访问的地址并提供一个回调函数来接收数据
     * 只能用于get请求
  2. CORS 后端来支持可以指定哪些域名可以访问
  
## 原型及原型链
### 构造函数

#### new 运算符

1. 创建一个新的空对象实例。
2. 将此空对象的隐式原型指向其构造函数的显示原型。
3. 执行构造函数（传入相应的参数，如果没有参数就不用传），同时 [this](#this-指向) 指向这个新实例。
4. 如果返回值是一个新对象，那么直接返回该对象；如果无返回值或者返回一个非对象值，那么就将步骤（1）创建的对象返回。

### 原型概念

每个函数都有一个 prototype 属性,它指向了一个对象,这个对象里的所有方法和属性都会被构造函数的实例继承, 这个对象就称为原型对象.

### 原型链

每个对象都有一个**proto**属性,指向其原型对象的指针,对象从原型中继承方法和属性.每个原型对象又有自己的原型,并从中继承方法和属性,直到某个对象的原型为 null 为止,这种关系被称为原型链

```JavaScript
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

### 继承

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
        name: 'xiaoping',
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
### 数据类型
1. 基本数据类型
   * 有number,string,boolean,null,undefined五类。
   * 存储在栈内存中
   * 精度问题
      `parseFloat((0.1 + 0.2).toFixed(10))`
      * toFixed 四舍五入返回值是字符串
      * parseFloat 将字符串转化为数字
1. 引用数据类型
   * 无序对象{a:1}，数组[1,2,3]，以及函数
   * **名存在栈内存中，值存在于堆内存中，但是栈内存会提供一个引用的地址指向堆内存中的值，**
#### 深浅拷贝[拷贝](#深浅拷贝)
只针对复杂数据类型
1. 浅拷贝仅仅复制的是对象的引用,没有开辟新的栈.也就是说两个对象指向的是同一个地址.改变其中一个对象的属性,另一个也会跟着改变
2. 深拷贝是重新开辟一个空间,两个对象不受干扰
#### 判断数据类型
1. typeof 对于基本类型，除了 null 都可以显示正确的类型
2. instanceof 判断一个构造函数的原型是否存在另外一个要检测对象的原型链上
3. Object.prototype.toString.call(xx) => [object Type]
### 变量提升

- 在 JavaScript 中，函数声明与变量声明经常被 JavaScript 引擎隐式地提升到当前作用域的顶部。
- 声明语句中的赋值部分并不会被提升，只有名称被提升
- 函数声明的优先级高于变量，如果变量名跟函数名相同且未赋值，则函数声明会覆盖变量声明
- 如果函数有多个同名参数，那么最后一个参数（即使没有定义）会覆盖前面的同名函数

#### var,let,const

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
4. 在构造函数中,[new关键字](#new-运算符)
   1. 如果返回值是一个对象,那么 this 指向其返回的对象
   2. 如果不是或者没有返回,则指向函数所创建的实例
5. 箭头函数没有this,如果箭头函数被非箭头函数包含,this指向的就是最近一层非箭头函数

### 储存
## 作用域
* 全局作用域：
  * 代码在程序的任何地方都能被访问，window 对象的内置属性都拥有全局作用域。
* 函数作用域：
  * 函数内行程的作用域
* 作用域链
  * 在函数内部中访问变量时,会先在当前作用域内查找,如果在当前作用域中没有查到值，就会向上级作用域去查，直到查到全局作用域，这么一个查找过程形成的链条就叫做作用域链。
  * 内部环境可以通过作用域链访问所有外部环境，但外部环境不能访问内部环境的任何变量和函数。
## 闭包
闭包是指能够读取另一个函数作用域中变量的函数,在一个自执行匿名函数中,有个名为name的变量和一个能够访问name变量的函数,这个函数就称为闭包
* 特性
  * 函数内再嵌套函数
  * 内部函数可以引用外层的参数和变量
  * 参数和变量不会被垃圾回收机制回收
* 好处
  * 保护函数内变量安全,避免全局污染
* 坏处
  * 变量不会被销毁,内存消耗过大


## 功能类

### 数组去重

1. 使用 es6 的 new Set 构造函数初始化数组,然后使用 array.from
2. 利用 for 嵌套 for 循环,如果第一个循环内每一项不等于第二个循环的项,就往新数组中添加
3. 利用 for 循环内 indexOf 或者 includes,创建一个新数组,检测新数组中是否包含原数组的值,不包含则往新数组中添加
4. filter 循环内使用 indexOf,当前项的第一个索引值是否等于当前循环索引值,返回新数组

### 深浅拷贝
1. 浅拷贝
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
2. 深拷贝
   1. JSON.parse( JSON.stringify(a) )
      __无法复制函数__

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
## 适配
### rem
  1. 原理
     1. rem是一个相对单位,相对于根元素来设置大小,也就是说我们可以通过获取屏幕宽度动态给html的font-size设置不同的值来达到适配的效果。在工作中通常是750的设计稿，将屏幕宽度等分为10份,然后设置根元素的font-size为其中一份，即1rem等于75px，对于px转rem的计算可以通过sass的函数来实现。
     2. 在工作中有使用flexible来适配，原理是根据型号和屏幕宽度来设置dpr和根元素的font-size,然后通过dpr来设置viewport的缩放比例
  2. 字体不使用rem
     1. 可以通过sass来定义一个宏,如果

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
   采用数据劫持结合发布者-订阅者模式的方式，在组件挂载时，
   1. 实现监听器Observe,通过 Object.defineProperty() 来劫持data中各个属性的setter，getter，在数据被读取时进行依赖收集,在数据变动时发布消息给订阅者，触发相应监听回调。
   2. 实现一个订阅器Dep,用来收集Observe和watcher
   3. 通过订阅者watcher,可以收到属性的变化并执行相应的方法,从而更新视图
   4. 实现一个解析器 Compile，可以解析每个节点的相关指令，对模板数据和订阅器进行初始化。
  
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
6. 父组件和子组件生命周期执行顺序
   1. 加载渲染过程
      1. 父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted
   2. 子组件更新过程
      1. 父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
7. v-if 和 v-show 区别
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
8.  $route和$router的区别
   $route是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。
   而$router是“路由实例”对象包括了路由的跳转方法，钩子函数等
11. 组件传值
   1. 父子 
      1. 子=>父 子发送$emit 父组件接收
      2. 父=>子 子定义props中的属性
   2. 兄弟
      可以通过new一个vue实例bus作为媒介,在组件ab中分别引入,a组件$$emit,b组件可以用$$on接收
   3. provide/inject
      组件中使用provide向下分发数据,下面的所有子组件都可以使用inject接收数据
12. computed和watch区别
   4. computed是计算属性,
      1. 当页面中有某些数据依赖其他数据进行变动的时候适合用computed,
      2. 有缓存行只有依赖的响应式数据变化了computed才会重新执行
   5. watch用于观察和监听页面上的vue实例,如果要在数据变化的同时进行异步操作或者是比较大的开销，那么watch为最佳选择
13. 性能优化
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
      * router.afterEach
   1. 路由组件内的守卫
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
      - setState是异步的，不会立即改变state的值
      - 多次调用setState会状态合并。
      - setState可以接受一个函数,获取到上一次state的值
      - 第二个参数可以是一个回调函数。
        - 是在setState调用完成组件开始渲染时调用,可以获取更新后的state的值
3. 坑点
     1. JSX做表达式判断时候，需要强转为boolean类型,不强转会显示原来的数值
4. 优化方案
     1. shouldComponentUpdate对新旧props做比较,避免不必要的渲染,可用PureReactComponent组件,内部已经封装了新旧props的浅比较逻辑
     2. 对于列表增加唯一key标识
     3. render中减少匿名函数的使用
5. 高阶组件HOC
     1. 是一个函数,接收一个组件返回新的增强组件
     2. 主要功能是代码复用

## vue和react的区别

### redux
1. connect原理
   1. 用Provider在原应用组件上包裹一层，使原来整个应用成为Provider的子组件 接收Redux的store作为props，通过context对象传递给子孙组件上的connect
   2. connect做了些什么。它真正连接 Redux 和 React，它包在我们的容器组件的外一层，它接收上面 Provider 提供的 store 里面的state 和 dispatch，传给一个构造函数，返回一个对象，以属性形式传给我们的容器组件
2. 三大原则
   1. 唯一数据源
   
      整个应用的state都被存储到一个状态树里面，并且这个状态树，只存在于唯一的store中
   2. 保持只读状态
   
      唯一改变state的方法就是触发action，action是一个用于描述以发生时间的普通对象
   3. 数据改变只能通过纯函数来执行,编写reducers

# 公众号
## 权限流程
获取授权信息 根据code获取用户openid
  
 
