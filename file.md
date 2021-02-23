# JS
## 原型及原型链
### 原型概念
每个函数都有一个prototype属性,它指向了一个对象,这个对象里的所有方法和属性都会被构造函数的实例继承, 这个对象就称为原型对象.
### 原型链
每个对象都有一个指向其原型对象的指针,对象从原型中继承方法和属性.每个原型对象又有自己的原型,并从中继承方法和属性,直到某个对象的原型为null为止,这种关系被称为原型链
````
function F1() {}
function F2() {}
function F3() {}

let f1 = new F1();
let f2 = new F2();
let f3 = new F3();

F2.prototype = f1;
F3.prototype = f2;

````
### 为什么需要原型链
1. 用构造函数生成的实例,都有自己的方法和属性

## 继承
1. 构造函数继承
````

````
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

### 复制数组
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
