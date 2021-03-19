---
title: 项目中用到的ES6语法
date: 2020-11-12 20:20:21
tags:
cover: ./img/4es6.jpg
description: es6
---
### 项目中用到的ES6语法
#### 1.数组的一些方法:
Array.from() -- 将伪数组转为真正的数组  
Array.of(v1,v2,v3) -- 将一系列值转为数组;   
Array.find() Array.findIndex() -- 找出数组中第一个符合条件的数组成员;
>参数是一个回调函数,返回符合条件的成员/位置,未找到返回undefined/-1;

includes() -- 该数组是否包含给定的值,返回布尔值;,第二个参数表示搜索的起始位置,如果为负,表示从后面的位置开始搜索;   
keys() values() entries() -- 返回一个遍历器对象,可以使用 for...of...遍历
>keys()返回键名  values()返回键值  entries()返回键值对

#### Set, WeakSet, Map, WeakMap
Set(集合), Map(字典) 主要的应用场景在于 数据重组 和 数据存储;
+ Set   
Set 类似数组的数据结构,成员唯一,无序,没有重复的值, ; Set 内部判断两个值是否相等,使用类似 全等(===) 的方式,区别在于 Set 认为 NaN 等于其自身;   
属性: size -元素数量; 方法: add, delete, has, clear   
遍历方法: keys()/values()/entries() 分别返回包含 键/值/元素 的迭代器对象; forEach()/map()/filter()
+ WeakSet   
WeakSet允许你将一个弱引用对象储存在一个集合中,与Set的区别在于:   
WeakSet只能储存对象引用,不能储存值; 储存的对象都是被弱引用的,垃圾回收不考虑WeakSet是否还存在引用; WeakSet对象是不可遍历的,无法拿到它包含的所有元素;
+ Map    
本质上是键值对的集合,不允许重复的值,具有遍历器接口,且每个成员都是双元素的数组的数据结构; Map 内部判等的方法与 Set 是一致的; Map 的键实际上是跟内存地址绑定的; Object是"字符串-值", Map是"值-值"对应;
属性: size -返回Map中所包含的元素;   
方法: set(key, value); get(key); has(key); delete(key); clear();
遍历方法同Set;
+ WeakMap
本质上是一组键值对的集合,其中键是弱引用对象,而值可以是任意值;每个键所引用的对象都是弱引用,若值对象没有其他引用时,这个对象将会被垃圾回收,key值不可枚举;
```
new Set(['value1', 'value2', 'value3']);
new WeakSet([['value1', 'value2'], ['value3', 'value4']]);
new Map([['key1', 'value1'], ['key2', 'value2'], ['key3;, 'value3']]);
let MapToObj = (map) => {    //Map转object
    let obj = Object.create(null);
    for (let [key, value] in map) {
        obj[key] = value;
    }
    return obj;
}
let ObjToMap = (obj) => {    //object转Map
    let map = new Map();
    for (let key in obj) {
        map.set(key, obj[key]);
    }
    return map;
}

```


#### 2.箭头函数:
缩减代码,改变this指向;
()=>{}  函数体内的this对象指向该函数定义时所在的对象,而不是使用时所在的对象;   
>不能当做构造函数 new;不能使用arguments对象,使用rest参数代替;不能使用yield命令;   
>rest参数形式为: ...变量名 ;获取函数的多余参数;    
>>该参数是一个数组,将多余的参数放入该数组中;
function addNum(...nums){ return nums,reduce( (sum,num)=>{ return sum + num } ) }
arr,reduce( (sum,num)=>{ return sun+num; } )   --求和函数
一个函数最多声明一个rest参数,且必须是最后一个参数

#### rest参数:

