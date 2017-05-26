# 前端开发 #
通过我最近的调研web前端开发的学习路线大概分以下步骤
1. 基础知识 html5 + css3 + js 代码规范
2. 框架JQuery + Bootstrap + Ajax + react + Angular
3. 浏览器的适配，兼容性，跨平台 ,移动app, 响应式布局
4. 了解服务端至少一门语言python
5. debug，调试 firebug + chrome


## 首先推荐廖雪峰的一个网站 ##

- [JavaScript](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499832124d97d77b00706461f9daf1a390b75ade1000)
- [Python](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)
- [imooc](http://www.imooc.com/)

## map/reduce ##

``` javascript
function pow(x) {
    return x * x;
}
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
```


<!-- [x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4) -->

``` javascript
function add(x ,y) {
    return x + y;
}
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.reduce(add); // 45
```

## 对象的创建 ##
1. 根据对象创建对象

```javascript
var Student = {
    name: 'Robot',
    height: 1.2,
    run: function() {
        console.log(this.name + ' is running...')
    }
}

var xiaoming = {
    name: '小明'
};

xiaoming.__proto__ = Student // xiaoming的原型只想Student对象
```

2. 通过Object.create(Student) 工厂创建对象

```javascript
function createStudent(name) {
    var s = Object.create(Student)
    s.name = name
    return s
}

// xiaoming.__proto__ === Student; // true
```

3. 构造函数创建对象

```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!')
    }
}

var xiaoming = new Student('小明')

xiaoming.constructor === Student.prototype.constructor; // 小明的构造器 等于 构造的函数原型对象的构造器
Student.prototype.constructor === Student; //构造的函数原型对象的构造器 等于 构造函数

Object.getPrototypeOf(xiaoming) == Student.prototype;
```

对象（比如xiaoming）只有__proto__, 确没有prototype, 
构造函数（比如Student）有prototype, 它只想某个对象，也就是小明的原型
这个对象有个constructor属性指向构造函数（Student）

这个时候我们需要把，对象的方法放到原型对象上，达到公用的目的

```javascript
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!')  
};
```

最后也可以使用一个工厂方法创建对象

## 继承 ##
js中的继承跟传统的java c++ 和oc实现不太一样，它是通过prototype来实现的
1. 原型继承

```javascript
function inherits(Child, Parent) {
    var F = function () {}; 
    F.prototype = Parent.prototype; 
    Child.prototype = new F(); 
    Child.prototype.constructor = Child; 
    // 1、创建一个空函数
    // 2、空函数的原型对象 --> 父类构造函数的原型对象
    // 3、子类构造函数的原型对象 -->通过空构造函数创建的一个对象。 因为 通过这个空构造函数的对象的原型对象 --> 父类构造函数的原型对象，所有继承链就ok了
    // 4、子类原型对象的构造器 --> 子类构造函数
}
```

2. class继承
```javascript
class Student {
    constructor(name) {
        this.name = name
    }

    hello () {

    }
}


class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name):
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade)
    }
}
```


## CSS ##

1. 盒子模型 margin border padding content
<!-- 定位相关的  -->
2. position:static absolute relative fixed
3. float 属性 left right none inherit float属性不在普通文档流中
4. clear 属性 left right both inherit

