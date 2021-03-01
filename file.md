# JS

## 构造函数

### new运算符

1. 创建一个新的空对象实例。
2. 将此空对象的隐式原型指向其构造函数的显示原型。
3. 执行构造函数（传入相应的参数，如果没有参数就不用传），同时 [this](#this指向) 指向这个新实例。
4. 如果返回值是一个新对象，那么直接返回该对象；如果无返回值或者返回一个非对象值，那么就将步骤（1）创建的对象返回。

## 原型及原型链

### 原型概念

每个函数都有一个prototype属性,它指向了一个对象,这个对象里的所有方法和属性都会被构造函数的实例继承, 这个对象就称为原型对象.

### 原型链

每个对象都有一个__proto__属性,指向其原型对象的指针,对象从原型中继承方法和属性.每个原型对象又有自己的原型,并从中继承方法和属性,直到某个对象的原型为null为止,这种关系被称为原型链

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

* 让父类构造函数使用call在子类构造函数里执行,改变父类的this指向
* 缺点:
  * 父类原型上的方法和属性不能被继承
  * 会创建多次方法
  
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

* 将子类的原型指向父类的实例,借助原型链实现继承,
* 缺点:
  * 子类的多个实例共用父类原型,无法隔离,
  * 子类实例无法向父类传参

3. 组合继承
* 结合了两种模式的优点，传参和复用, 通过call调用父类构造函数,然后将子类的原型指向父类的实例,
* 缺点: 父类构造函数调用两次

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

* 在一个方法内部创建一个临时函数,将传入的对象作为临时构造函数的原型,最后返回实例,实现了浅复制的功能
* 缺点: 引用类型的数据共享,无法隔离

5. 寄生式继承 创建一个封装继承过程的函数,该函数可以在内部增强对象,最后返回对象
* 缺点: 无法做到函数复用而影响效率

6. 寄生组合继承 通过构造函数继承属性, 将子类的原型指向为父类原型的副本, constructor指向子类构造函数
-------------------------
## js基础

### var,let,const

* let
  * 声明的变量只在它所在的代码块中生效 比如for循环 用let声明的i只在for循环内有效
  * 不会变量提升,变量要在声明后使用否则会报错
  * 不允许在相同作用域内多次重复同一个变量
* const
  * 声明创建的值不允许修改,声明的变量只在它所在的代码块中生效
  * 如果修改基本数据类型的值,会报错,修改引用数据类型的话则不会
* var
### this指向
大部分情况下,函数的调用方式决定了this的指向
1. 在全局执行环境中，this指向全局对象,在浏览器中全局对象是Windows
2. 在全局函数中,this指向Windows
3. 当函数在对象中作为方法被调用时,this指向为调用该函数的对象
4. 在构造函数中,[new关键字](#new运算符)
   1. 如果返回值是一个对象,那么this指向其返回的对象
   2. 如果不是或者没有返回,则指向函数所创建的实例
## 功能类
### 数组去重
1. 使用es6的new Set构造函数初始化数组,然后使用array.from
2. 利用for嵌套for循环,如果第一个循环内每一项不等于第二个循环的项,就往新数组中添加
3. 利用for循环内indexOf或者includes,创建一个新数组,检测新数组中是否包含原数组的值,不包含则往新数组中添加
4. filter循环内使用indexOf,当前项的第一个索引值是否等于当前循环索引值,返回新数组

### 深浅拷贝
  1. concat()
  ```
  const a1 = [1, 2];
  const a2 = a1.concat();

  a2[0] = 2;
  a1 // [1, 2]
  ```
  2. es6扩展运算符
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
  2. es6扩展运算符
  ```
  var arr1 = ['a', 'b'];
  var arr2 = ['c'];
  var arr3 = [...arr1, ...arr2]
  ```
### 数组去重
  1. [...new Set(array)]


## 数组方法

1. filter()
   * filter() 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。
   * 注意： filter() 不会对空数组进行检测。
   * 注意： filter() 不会改变原始数组。

  ```
    var words = ["spray", "limit", "elite", "exuberant", "destruction", "present"];
    var longWords = words.filter(function(word){
      return word.length > 6;
    });
    // Filtered array longWords is ["exuberant", "destruction", "present"]
  ```

2. map

# 混合开发
## 更新流程
1. 分版本，有版本号，如 201803211015
2. 将静态文件压缩成 zip包，上传到服务端
3. 客户端每次启动，都去服务端检查版本号
4. 如果服务端版本号大于客户端版本号，就去下载最新的 zip 包
5. 下载完之后解压包，然后将现有文件覆盖
