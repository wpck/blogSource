---
title: javaScript继承
date: 2020-12-08 10:57:53
tags:
cover: ./img/10extend.jpg
---
### 原型链
>js中,每个函数都有prototype属性,指向函数的原型对象; 每个对象(null除外)被创建的时候,都会有__proto__属性,指向其原型对象,并从原型对象继承属性和方法; 每个原型都有一个constructor属性,指向关联的构造函数;      
>当读取实例的属性时,若未找到,会继续查找实例的原型,若未找到,会继续查找原型的原型,直到最顶层(Object)为止;     
```
function Parent() {
    this.p_property = true;
}
let parent = new Parent();
//构造函数,原型对象,实例三者之间的关系
Parent.prototype = parent.__proto__;
parent.__proto__.constructor = Parent;
Object.prototype.__proto__ = null
```
### 原型链继承
原型链继承是改变子类构造函数的原型对象,指向父类的实例对象;
>优点: 父类的方法可以共享;   
>缺点: 1.子类不能像父例传参; 2.父类的引用属性被所有实例共享,子类对引用类型的操作会影响父类;
```
function Parent() {
    this.p_property = true;
}
function getPValue() {
    return this.p_property;
}
function Child() {
    this.c_property = false;
}
function getCValue() {
    return this.c_property;
}

Child.prototype = new Parent()    //原型链继承的关键,实例的原型对象指向父实例
let child = new Child();
console.log(child.getPValue())    //true
let parent = new Parent();
child.p_property = false;
console.log(parent.p_property)    //false 改变子类对父类属性的引用,会影响父类及其他子类实例
```
### 构造函数继承
在子类的构造函数里改变this指向调用父类构造函数,优缺点跟原型继承恰恰相反;
>优点: 子类可以向父类传参; 父类属性不会被所有实例共享;   
>缺点: 只能继承父类实例的方法,不能继承父类原型上的方法; 每个实例都有父类实例方法的副本,影响性能;
```
function Child() {
    Parent.call(this);    //构造函数继承的关键点
}
```
### 组合继承
原型继承和构造函数继承结合起来,使用构造函数继承实现实例属性的继承,使用原型继承实现原型属性和方法的继承;
>优点: 父类方法可以被继承; 子类可以向父类传参; 父类属性不会被共享;
>缺点: 会两次调用父类的构造函数.造成浪费;
```
function Parent(property) {
    this.type = 'person';
    this.property = property;
}
Parent.prototype.getType = function() {
    return this.type;
}
function Child(property, isRed) {
    Parent.call(this, property);    //构造函数继承,实现继承实例属性
    this.isRed = isRed;
}
//原型继承,实现原型属性和方法的继承
Child.prototype = new Parent();
Child.prototype.constructor = Child;
Child.prototype.getProperty = function() {
    return this.property;
}
let child = new Child('child', true);
console.log(child.getType())     //person
console.log(child.getProperty())    //child 
```
### 原型式继承
对象字面量声明的对象的继承,无法使用构造函数相关: 利用一个空对象,直接将某个对象赋给空对象构造函数的原型并返回;
>优点: 父类方法可以复用
>缺点: 父类引用属性被多个子类实例共用,子类实例操作影响父类; 子类不能像父类传参;
```
function object(obj) {    //该方法跟Object.create()是一样的
    function F() {};
    F.prototype = obj;
    return new F();
}
let parent = {
    eye2: 2,
    month: 1,
    name: 'parent'
}
let child1 = object(parent);    //等同于Object.create(parent)
child1.name = 'child1';
let child2 = object(parent);
child2.name = 'child2';
console.log(parent.name);   //child2
```
### 寄生式继承
在原型式继承的基础上给拷贝来的对象增加方法,优缺点同原型式继承
```
function createChild(obj) {
    let child = object(obj);
    child.prototype.getName = function() {
        return this.name;
    }
    return child;
}
let child1 = createChild(parent);
```
### 寄生组合继承
组合继承会有调用多次调用父类构造函数的缺点,寄生组合继承可以解决这个问题,该方法核心在于让子类的prototype指向父类原型的拷贝;
```
function Parent(name) {
    this.name = name;
}
Parent.prototype.getName = function() {
    return this.name;
}
function Child(name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = Object.create(Parent.prototype);    //原型式继承的改造;
Child.prototype.constructor = Child;

let child = new Child('ping', 26);
```
### 实现多继承,继承多个父类
```
function Child() {
    Parent1.call(this);
    Parent2.call(this);
}
Child.prototype = Object.create(Parent1);
Object.assign(Child.prototype, Parent2.prototype);
Object.prototype.constructor = Child;
```
### es6继承
es6引入了Class语法糖,底层仍是基于原型的实现;
```
class Person {
    constructor(name) {
        this.name = name;
    }
    getName = function() {
        return this.name;
    }
}
class Child extends Person {
    constructor(name, age) {
        super(name);
        this.age = age;
    }
}
let child = new Child('ping', 26);
```
--------------
es5的继承与es6 class继承的不同之处:    
>es5的继承通过原型链和构造函数实现,实质上是先创建子类的实例对象this,然后调用父类的构造函数,将父类的方法添加到this上;   
>es6的继承通过class,extend关键字实现,实质上是先创建父类实例对象this,在子类构造函数中修改this,所以首先要调用super方法,否则新增实例会报错;子类没有自己的this对象,而是继承了父类的this对象,super代指父类的this实例对象, 函数声明会被提升,类声明不会提升,必须先声明类,才能访问它;;
